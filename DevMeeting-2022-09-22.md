---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-09-22

https://bugs.ruby-lang.org/issues/18977

## DateTime and location

* 2022/09/22 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/10/20 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18960]](https://bugs.ruby-lang.org/issues/18960) Module#using raises RuntimeError when called at toplevel from wrapped script (shioyama)

* @jeremyevans0 mentioned this, but I think this is a bug based on common reading of the wrap argument to load.
* See also context for this in my next bullet point.

Preliminary discussion:

* shugo: Positive to fix, but the proposed PR causes [BUG]

Discussion:

* shioyama: Loading into an anonymous module amends the way how toplevel `using` works, thus an error.
* nobu: The script calls `main.using` inside of a module.
* mame: Should be `Module#using` instead.
* ko1: Should be?  The context's `self` is `main`.
* nobu: A copy of `main`, to be precise.
* ko1: 

```ruby!
# target.rb
using M # calls main.using #=> Rurrent: RuntimeError, expected: works as Module#using

"foo".foo #=> expected: hello
```

```ruby!
module M
  refine(String) do
    def foo
      "hello"
    end
  end
end

load "target.rb", true
```

Conclusion:

* matz: Please fix it (how to fix it is up to @shioyama).


### [[Feature #10320]](https://bugs.ruby-lang.org/issues/10320) require into module (shioyama)

* Proof of concept implementing imports like CommonJS etc using some patches to extend top_wrapper: https://bugs.ruby-lang.org/issues/10320#note-13
* Key point is that I want to support _existing_ code, including gems, with no changes.
* POC gem: https://github.com/shioyama/im, requires patched Ruby
* This is just for discussion of feasibility/approach.

Preliminary discussion:

```ruby!
def import(lib)
  mod = Module.new
  mod::NilClass = ::NilClass
  mod::Integer = ::Integer # ?
  load lib, mod
  mod
end
```

ko1: Now `load` doesn't have a feature to load extensions.

```ruby
load '/home/ko1/.rbenv/versions/3.1.2/lib/ruby/3.1.0/x86_64-linux/etc.so'

__END__
/home/ko1/src/rb/t.rb:1:in `load': /home/ko1/.rbenv/versions/3.1.2/lib/ruby/3.1.0/x86_64-linux/etc.so:1: Invalid char `\\x7F' in expression (SyntaxError)
/home/ko1/.rbenv/versions/3.1.2/lib/ruby/3.1.0/x86_64-linux/etc.so:1: Invalid char `\\x02' in expression
/home/ko1/.rbenv/versions/3.1.2/lib/ruby/3.1.0/x86_64-linux/etc.so:1: Invalid char `\\x01' in expression
/home/ko1/.rbenv/versions/3.1.2/lib/ruby/3.1.0/x86_64-linux/etc.so:1: Invalid char `\\x01' in expression
        from /home/ko1/src/rb/t.rb:1:in `<main>'
```

* ko1: How is `$LOADED_FEATURES` handled?
* mame: maybe `im` library handles it.
* ko1: is it safe with autoload? doesn't it safe on concurrent programs?

```ruby
class Foo
  def ==(other)
    self.class == other.class && ...
  end
  
  def <=>(other)
    ...
  end
end

mod1::Foo.new != mod2::Foo.new

module mod1
  class NilClass # Does not look for ::NilClass but defines mod1::NilClass directly
    def to_s
      # ...
    end
  end

  # Im's hack: mod1::NilClass = :NilClass
  class NilClass # (re)opens mod1::NilClass (which is ::NilClass)
    def to_s
      # ...
    end
  end
end

module mod1
  def foo
  end

  class Foo
  end

  class Bar
    ::Foo #=> calls const_missing
  end

  # def Class.const_missing
  #   mod = identify_caller_context #=> mod1
  #   mod::Foo
  # end
end
```

```ruby
# main.rb
load __dir__ + '/l.rb', true

# l.rb
p NilClass.ancestors #=> [NilClass, Object, Kernel, BasicObject]

class NilClass
  p self.ancestors
  #=> [#<Module:0x0000014ecde64d40>::NilClass, Object, Kernel, BasicObject]
end

p NilClass.ancestors
#=> [#<Module:0x0000014ecde64d40>::NilClass, Object, Kernel, BasicObject]
```

Discussion:

* shioyama: What I ultimately want to achieve is to load an entire gem (like `ActiveRecord`) into a separate module.
* shioyama: In order to do so we need to allow calling `require` from a library loaded from `Kernel.load`. That pollutes the global toplevel right now, which ruins everything.
* mame: AFAIK shopify wants to split its Rails application into several parts (≒ namespaces).  This is a part of that project.
* shioyama: This is still my hobby project but would like to apply to the shopify monolith if applicable.
* mame: The proposed patch is to let e.g. `rb_define_const` consider where the script is loaded.
* shioyama: there are other problems, like methods defined from inside of a method gets escaped from the wrapper module.  But they can be worked around by the script themselves I think.

```ruby!
mod = Module.new
load "foo.rb", mod
# mod::Foo is defined
```

* matz: I want some sort of namespace-ed loading feature. I'm for the request in general.
* matz: However here is my 2 points:
  * I guess modules do a lot of jobs already.
  * load and require are designed to work at global toplevel.  Amending this would need a lot of work. How should `$LOADED_FEATURES` work?  What about autoloads?
* matz: I guess you need to hutsle to tackle everything.  Is this worth the hutsle?
* matz: Not against it right now.  However there can be unseen problems.
* ko1: Can we load Rails 5 and 6 in same process?
* shioyama: yes, but we can't share core class like Hash with ActiveSupport between Rails 5 and 6.
* ko1: How about C extension?
* nobu, shyouhei, matz: (technical discussion as to how DLLs work...)

Conclusion:

* naruse: It would be nice to have a separate ticket for what you want at Shopify, rather than reusing old ones.
* matz: OK to change ruby (rather than abusing TracePoint etc.).  I (or we) need to consider detailed specification though.


### [[Feature #18784]](https://bugs.ruby-lang.org/issues/18784) `FileUtils.rm_f` and `FileUtils.rm_rf` should not mask exceptions (mame)

* I changed FileUtils.rm_f/rm_rf so that it raises a SystemCallError but Errno::ENOENT.
* But it seemed to affect mkmf on Windows. mkmf uses rm_f to delete conftest.exe, but sometime it fails. I don't know why it happens.
* I concern that this change will make users just ignore exceptions of FileUtils.rm_rf by wrapping `begin .. rescue SystemCallError; end`.
* This is not what I wanted. Should we revert the change of FileUtils?

Discussion:

* akr: mjit shall be fixed.
* akr: not sure for windows.
* nobu: Windows' Makefile waits for executable files to be writable.
* akr: Should FileUtils handle the situation?
* nobu: Not sure, FileUtils is not the only place to delete files.
* mame: I guess everyone starts to wrap rm_rf with rescue block.  There is no clear solution.
* akr: there are two use cases for rm_rf: one is to force to remove a file, the other is to remove a file if possible: uninstalling must remove the directory.  removing a temporally directory is torrelable if the directory cannot removed.
* akr: the default should be more compatible one (= if possible)
* hsbt: Maybe this is a motivation example to this issue https://github.com/rubygems/rubygems/issues/4888
Some gems contains directories with unappropriate permissions.  "gem install" creates directory with unappropriate permissions.  "gem uninstall" fails to uninstall because the permissions.
* akr: `FileUtils.rm_rf(chmod: true, auto_retry: true)`
* shyouhei: another method?
* mame: `super_rm_rf`

Conclusion:

* akr: Let's just revert it. Reconsider a warning
* matz: OK


### [[Feature #18982]](https://bugs.ruby-lang.org/issues/18982) Add an `exception: false` argument for Queue#push, Queue#pop, SizedQueue#push and SizedQueue#pop (byroot)

- The existing exceptions (ThreadError) are a bit awkward to deal with.
- Queue is often used deep into the stack where exceptions in common cases are fairly costly.

Preliminary discussion:

* ko1: no problem.
* samuel: Can we please introduce a standard for this otherwise every method with `exception: true/false` will behave slightly differently. https://bugs.ruby-lang.org/issues/18774#note-9

```ruby
q = Queue.new
q.pop(false, exception: false) #=> nil
```

Discussion:

* ko1: Currently `q.pop(timeout: 0)` just returns nil.  I guess it just suffice.
* knu: what happens if the queue is closed?
* matz: pop could be done like that, but I guess `push` etc. need more considerations. 

Conclusion:

* ko1 will ask `Queue#pop(timeout:0)` is enough or not.
* No conclusion. Continue discussing.


### [[Feature #18951]](https://bugs.ruby-lang.org/issues/18951) `Object#with` to set and restore attributes around a block (byroot)

- Works with any public accessor, e.g. `GC.with(stress: true) { do_something }`
- This is an extremely common pattern, especially in test suites, but is often implemented with subtle bugs.

Preliminary discussion:

```ruby!
def test_something_when_enabled
  SomeLibrary.with(enabled: true) do
    # test things
  end
end

# equal to:

def test_something_when_enabled
  enabled_was, SomeLibrary.enabled = SomeLibrary.enabled, true
  # test things
ensure
  SomeLibrary.enabled = enabled_was
end
```

* ko1: `attr_with` ?

```ruby
obj.attr_with foo: 1 do
  p obj.foo #=> 1
end
```

* mame: I have written `verbose_bak, $VERBOSE = $VEROBSE, nil` so many times
* samuel: I like this idea, but some how do we handle interleaving `with` on the same object? e.g. the example given `GC.with` is modifying global state. It might cause odd behaviour if two threads try to do the same thing.

Discussion:

* matz: I know the needs.  But how much useful is this?  I guess missing `$VERBOSE` use case is huge.
* matz: Consideing the request to add this method to `Kernel` I doubt the generalty.

Conclusion:

* matz: `#with` is NG as its name.
* matz: I'd ask for actual use cases.


### [[Feature #18776]](https://bugs.ruby-lang.org/issues/18776) Object shapes as new caching system (tenderlovemaking)

* YARV interpreter benchmarks are similar speed or faster
* Memory increase is low
* Opens door for more optimizations

Discussion:

* aaron: We've done a lot of works and I think it's now ready to be merged for 3.2. 
* ko1: Is it thread-safe?  I can't find enough guards.
* aaron: Modifying the tree is properly locked.  Not sure about inline caches.
* ko1: Oh I see where the do so.  The lock is only acquired in case of misshits so there would be no performance overhead for normal cases.
* mame: Is this fast enough?
* aaron: I think it shows more values when combined with JITed codes.
* shyouhei: It seems at least not slower.
* aaron: Our main goal is to reduce machine code generated by the JIT compiler but the simplicity of the source code is also a benefit.
* naruse: I need a bit more info about "more optimizations" because I need it for Ruby 3.2 release note.
* aaron: We may also apply this to class instance variables, for instance.
* matz: Actually we expect more speedup, but we can help.
* aaron: We may do whatever we do but I don't want it be anything more unstable.
* ko1: It doesn't change any CAPI?
* aaron: No.

Conclusion:

* matz: Okay.
* ko1: If possible can you provide the picture of new object layout so that we can understand the data structure better?


### [[Feature #19013]](https://bugs.ruby-lang.org/issues/19013) Error Tolerant Parser (yui-knk)

* matz: Looks great. Try it and gather knowlegdge about what error tolerance features are needed
* matz: This does not discourage kevin's effort to revamp the parser


### [[Feature #18996]](https://bugs.ruby-lang.org/issues/18996) Proposal: Introduce new APIs to reline for changing dialog UI colours (st0012)

  - `irb`'s autocompletion background can make texts hard to read for some users.
  - Because the colours are hardcoded in `reline`, users aren't able to change them. So many users need to disable the feature altogether.
  - The APIs proposed in the ticket actually have been implemented by [@pocari](https://github.com/pocari) and me.
  - Since the maintainer @aycabta is not available now, I hope we can decide if the APIs still need any improvement or they are ready for release.

Preliminary discussion:

* mame: A top-level getter/setter method like `Reline.dialog_default_bg_color` is good? Whenever we want to add color configs, should we increase the number of methods? Instead, I prefer one color config hash

```ruby
Reline.color_config[:dialog_default_bg_color] = :black

Reline.color_config.merge!({
  dialog_default_bg_color: :white,
  dialog_default_fg_color: :black,

  # "dialog_background_color" ???
  # why "default"? (is there some non-default?)
  # why "bg" "fg"? (can we avoid abbreviations?)
})

# samuel:
Reline.set_dialog_style(foreground_color, background_color, *attributes)
# Generally supported colors and styles: https://github.com/socketry/console/blob/main/lib/console/terminal/xterm.rb#L31-L53

Reline.set_color_config(:dialog_default_bg_color, :black)

# nobu
Reline.dialog_default_attribute = { fg: :black, bg: :white, italic: true, bold: true }

Reline.dialog_default_color = [:black, :white] # [fg, bg]
```

* hsbt: bg_color, fg_color and highlight bg_color, fg_color are good naming convention?
* Terminal.app
    * background
    * text
    * bold text
    * selection
* iTerm2.app
    * Background_Color
    * Bold_Color
    * Cursor_Color
    * Cursor_Text_Color
    * Foreground_Color
    * Selected_Text_Color
    * Selection_Color

Conclusion:

* naruse: Since this API will be called in bulk mannger with the configuraiton, we should use basically the following API, but the keyword `dialog_default_bg_color` must be more reconsidered

```ruby!
Reline.color_config.merge!({
  dialog_default_bg_color: :white,
  dialog_default_fg_color: :black,
  dialog_highlight_bg_color: :white,
  dialog_highlight_fg_color: :black,
})
```

* mame: I want to have a naming convention for the key name (ANSI escape sequence?)
  * https://en.wikipedia.org/wiki/ANSI_escape_code
* hsbt: Terminal.app uses the term "selection" instead of "highlight", for example

### [[Feature #19010]](https://bugs.ruby-lang.org/issues/19010) Follow up of #18996: Support changing irb's autocompletion background (st0012)

  - If the above or similar `reline` APIs are introduced, I want to support `dark`/`bright` themes in `irb` (screenshots are included in the ticket)
  - The default will be `dark` theme. I don't want to add more themes for now. So it can be toggled to `bright` theme with a boolean config.
  - To customize more, `irb` can take and pass per-color configurations to `reline` too.

Discussion:

* naruse: I like "THEME", but "DIALOG_THEME" is not what I want. "IRB_THEME"?
* akr: I expect ":dark" for dark-background terminal

Conclusion:

* let's consider after Reline API


### [[Feature #19008]](https://bugs.ruby-lang.org/issues/19008) Introduce coverage support for `eval`. (ioquatix)

- Working implementation.
- Can introduce some challenges for existing code - same challenges as debug gem - accurate line information for `eval` must be given.
- Useful for computing template coverage.
- Some template gems (https://github.com/rtomayko/tilt) work around it by writing the entire template to a temporary file - just to get coverage information! Let's fix this.

Preliminary discussion:

* ko1: branch coverage with eval may break?
  * samuel: Can you explain the issue?
* mame: `Coverage.start(eval: true)`
  * samuel: I'm fine with it. However, we don't do it for debug gem, why should we do it for coverage gem?
* hsbt: If this proposal is accepted, coverage will decrease
  * samuel: It may do, or it may increase, it depends if it's tested or not.

Discussion:

* samuel: It is not normally a production feature, so it probably can't impact "sensitive" environments.
* mame: problems can be:
  * nonexist files: not big problem
  * existing files: it will cause wrong converage
      * samuel: Is this really problem?

* yui-knk:

```ruby!
$ cat tt.rb
# comment line

eval(<<END, nil, "tt.rb")
x = 1
END

$ ./local/bin/ruby t.rb
{
  "tt.rb"=>[1, nil, 1, nil, nil],
}
```

```ruby!
def my_attr
    eval("...", caller...) # this should execute coverage for line "my_attr"
end

class Foo
    my_attr :x
end
```

* naruse: The filename conflict problem is not a real problem. Go ahead!
* samuel: awesome, thanks!
* matz: I have no strong opinion. How about trying it?
* mame: Agreed.

Conclusion:

* matz: go ahead.

### [[Feature #18630]](https://bugs.ruby-lang.org/issues/18630) Introduce general `IO#timeout` and `IO#timeout=` for blocking operations. (ioquatix)

- We can now support generally blocking and internally non-blocking operations (i.e. fiber scheduler).
- Safer network I/O.

Preliminary discussion:

* ko1: this API doesn't guarantee all of IO operations such as close/open for some kind of files so it should be noted (for example, it works on socket read/write and so on).
  * samuel: This was already documented but I've updated it to be clearer.
* ko1: If writing io an IO is interpreted, the data may be corrupted. It should be noted too.
  * samuel: Added this to the documentation.
  * samuel: Is there an alternative to torn writes?
* mame: `Timeout::Error` is an asynchronous exception, while `TimeoutError` is a synchronous exception, which is a bit confusing
  * samuel: Let's clean up `Timeout` module then? That module is a poor design and a poor interface. I suggest we introduce something like `Kernel#with_timeout(duration, exception = ::TimeoutError, message = "Timeout expired", &block)`. Alternatively, let's consider `::TimeoutError.with_timeout(...)`. Probably separate issue/PR.

```ruby
# /srv/gems/msgpack-rpc-0.7.0/lib/msgpack/rpc/exception.rb
# It's not top level, the code is just not indented.

##
# Top Level Errors
#
class TimeoutError < RPCError
        CODE = ".TimeoutError"
        def initialize(msg)
                super(self.class::CODE, msg)
        end
end

# /srv/gems/concurrent-0.2.2/lib/concurrent/actors/mailbox.rb
# last released in 2007 with < 20,000 downloads. Doesn't even install on Ruby 3.1.

class TimeoutError < RuntimeError
end
```

```
irb(main):001:0> require 'msgpack/rpc'
=> true
irb(main):002:0> TimeoutError
(irb):2:in `<main>': uninitialized constant TimeoutError (NameError)
        from /home/samuel/.rubies/ruby-3.1.1/lib/ruby/gems/3.1.0/gems/irb-1.4.1/exe/irb:11:in `<top (required)>'
        from /home/samuel/.rubies/ruby-3.1.1/bin/irb:25:in `load'         
        from /home/samuel/.rubies/ruby-3.1.1/bin/irb:25:in `<main>'       
irb(main):003:0> MessagePack::RPC::TimeoutError
=> MessagePack::RPC::TimeoutError
irb(main):004:0> 
```

Discussion:

- original proposal:

```ruby!
class TimeoutError < StandardError
end

class IO::TimeoutError < TimeoutError
end

class Regexp::TimeoutError < TimeoutError ????
```

- latest proposal:

```ruby!
module TimeoutError
end

class IO::TimeoutError < IOError
  include TimeoutError
end

class Errno::ETIMEDOUT
  include TimeoutError
end

class Regexp::TimeoutError < RegexpError
  include TimeoutError
end

# ??? some level of compatibility?
Timeout::Error.include(TimeoutError)

if defined?(TimeoutError)
  Async::TimeoutError.include(TimeoutError)
end
```

- usage:

```ruby!
begin
  io.read
rescue TimeoutError => error
  # ...
end

raise TimeoutError # doesn't work.
```

* matz: I prefer the proposed error class hieralchy but `TimeoutError` seems error class name.
* akr: `SynchronousTimeout`? `~TimeoutError` is very confusing with `Timeout::Error`.
    - samuel: `Timeout::Error` renamed? `Timeout::InterruptionError` ???
    - akr: I don't think it is possible to rename it because it is too popular.
* samuel: Do developer care about this level of detail?
* nobu: `::Timedout` module?
* naruse: `::TimedoutException` module?
  * ko1: it seems like class name?
* nobu: `::Timeout` module?
  * matz: too tricky.

Conclusion:

* matz: I don't have good name so postpone the conclusion.

### [[Feature #11689]](https://bugs.ruby-lang.org/issues/11689) Add methods allow us to get visibility from Method and UnboundMethod object. (eregon)

* I think  we should re-add (undo revert) {Method,UnboundMethod}#{public?,private?,protected?} on master, see https://bugs.ruby-lang.org/issues/11689#note-27
* Both #18729 and #18751 are fixed, and now Method == method entry, and visibility is an attribute of method entry.
* Ruby 3.1 already has those method and the fix for #18729 and #18751 was [backported to 3.1](https://bugs.ruby-lang.org/issues/18435#note-25). So if we keep them removed for 3.2 we cause incompatibility and there is no practical issue with those methods anymore.

Preliminary discussion:

* samuel: Sounds great to me. Is there any reason not to do it?

Conclusion:

* matz: will reply


### [[Feature #18798]](https://bugs.ruby-lang.org/issues/18798) `UnboundMethod#==` with inherited classes (eregon)

* OK to change `UnboundMethod#==` to check if same method definition (and ignore from which class `instance_method` was used on)? If yes, what about `UnboundMethod#eql?`?
* If not, OK to add `{Method,UnboundMethod}#same_definition?(other)`?

Discussion:

```ruby=
class C
  def foo = :C
  $mc = instance_method(:foo)
end

class D < C
  $md = instance_method(:foo)
end

class E < C
end

p $md                  #=> #<UnboundMethod: D(C)#foo() t.rb:2>
p $md.bind(E.new)      #=> #<Method: E(C)#foo() t.rb:2>
p $md.bind(E.new).call #=> :C
```

* akr: we should remove `D` information from UnboundMetod insntance. `#inspect` doesn't need to show `D` on this case.

Conclusion:

* matz: just ignore receiver class information (`D` in this case) and `#==`, `#eql?`, `#hash` and `#inspect (#to_s)` will be changed.


### [[Bug #18919]](https://bugs.ruby-lang.org/issues/18919) Ractor: can't share #Method objects (jeremyevans0)

* Is this a bug? Ractor cannot share most objects, but some procs are sharable.
* If not a bug, should it be switched to feature request?

Discussion:

* matz: I prefer to be shareable if possible

Conclusion:

* ko1: will reply. I will allow only when self of the Method is shareable.

### [[Bug #18878]](https://bugs.ruby-lang.org/issues/18878) parse.y: Foo::Bar {} is inconsistently rejected (jeremyevans0)

* This is currently invalid syntax, should we try to support it?
* If so, do we consider this a bug or a feature request?
* I tried to support it a couple weeks ago, but all my attempts ended in reduce/reduce conflicts.

Preliminary discussion:

* nobu: I think it is impossible to implement

Conclusion:

* matz: I prefer to allow it if possible
* nobu: I will try


### [[Bug #18978]](https://bugs.ruby-lang.org/issues/18978) Unexpected behaviour in Time.utc and Time.local when 8 arguments are passed in (peterzhu2118)

  - Passing 8 arguments causes both the 7th (microseconds) and 8th arguments to be ignored.
  - The behaviour is undocumented, so it is inconsistent between various Ruby implementations.
  - Proposed fix raises an ArgumentError when 8 arguments are passed in (this is the behaviour when 9 arguments are passed in).
  - Proposed PR: https://github.com/ruby/ruby/pull/6281

Preliminary discussion:

```ruby!
Time.utc(2000, 1, 1, 2, 3, 4, 100)    #=> 2000-01-01 02:03:04.0001 UTC
Time.utc(2000, 1, 1, 2, 3, 4, 100, 1) #=> 2000-01-01 02:03:04 UTC
```

* mame: If we remove the 8th argument, we also remove the folloing code https://github.com/ruby/ruby/blob/3f387e60ef87d61d5503132f02b96bba8caa32bd/time.c#L2947-L2955
* mame: This behavior was introduced for parsedate library, which is super old. Should we keep the compatibility?

Discussion:

* akr: is there any reason to break the compatibility?

Conclusion:

* matz: I will ask the question and set it as feedback


### [[Feature #18885]](https://bugs.ruby-lang.org/issues/18885) End of boot advisory API (byroot)

- The general concept was accepted, but it need a proper name.
- Suggestions:
 - `Kernel.booted` / `Kernel.application_booted` / `Kernel.code_loaded` / `Kernel.startup_done`
 - `ObjectSpace.loaded` / `ObjectSpace.optimize`
- The suggestions try to describe an "event" or point in time, more than which optimizations are being performed.
- This is because the actual optimizations might change over time or be different on other implementations.
- I'd like to implement the constant cache pre-warming soon, so having an accepted name for it would be very helpful.

Preliminary discussion:

* samuel: In daemons, this is sometimes refered to as a "process readiness protocol". The transition into the "ready" state is a specific terminology used. See <https://www.freedesktop.org/software/systemd/man/sd_notify.html> for an example. So, I like `Process.ready!` or something similar. It's also the same word for starting a (human running) race: "Ready, Steady, Go!". 

Discussion:

* matz: `*booted` resembles a hook like `Module#included`, `inherited`, etc.
* matz: The same goes to `Kernel.code_loaded` and `Kernel.startup_done` (past perfect tense) and `ObjectSpace.loaded`
* matz: I am unconfortable with `ObjectSpace.optimize`
    * Hook by user? `ObjectSpace.optimized`

```ruby
module MyOptimization
  def optimize(???)
    # load templates, cache files, load assets, parse data, etc.
  end
end

ObjectSpace.include(MyOptimization)
ObjectSpace.optimize
```

Conclusion:

* matz: will reply

### [[Feature #18411]](https://bugs.ruby-lang.org/issues/18411) Introduce `Fiber.blocking` for disabling the scheduler (efficiently). (ioquatix)

- Useful for writing pure Ruby schedulers.

Preliminary discussion:

* mame: Why can write_nonblock invoke fiber schedular?
  * samuel: Because with io_uring, write itself can be asynchronous, even if the result is non-blocking.

Discussion:

```
io_uring
    -> blocking read (io.nonblock = false)
    -> non-blocking read (io.nonblock = true)
        -> Still schedule in the io_uring using the asynchronous system call.
            -> io_uring_prep_read(fd,...)
            -> io_uring_submit(...)
            -> ... wait ...
            -> io_uring_wait -> cqe -> io -> data | EAGAIN
        -> We want to prevent that.
```

- This line from `read_nonblock` can invoke fiber scheduler hook: <https://github.com/ruby/ruby/blob/master/io.c#L3404>.
    - The `write_nonblock` doesn't go via this path at the moment: <https://github.com/ruby/ruby/blob/master/io.c#L3444>. However, we should consider changing it.
- Scheduler hook: <https://github.com/ruby/ruby/blob/master/scheduler.c#L234-L242>
- Implementation of io_uring `io_read`: <https://github.com/socketry/io-event/blob/25940127d4f297383e0c8d36025b06eebb7bdd8a/ext/io/event/selector/uring.c#L396-L409>
    - `io_write` is similar.

* akr: write_nonblock may even block (such as NFS). I think io_uring can handle such a case well. i.e. write_nonblock may return :wait_readable when NFS server dies. So I understand a motivation that scheduler want to trap write system call in write_nonblock.

* ko1: 

```ruby
# a little slow :)
Fiber.new(blocking: true) {...}.resume

# proposed:
Fiber.blocking{ io.read_nonblock }

# scheduler method
def address_resolve(host)
  Resolv.getaddresses(host) # We want this to be asynchronous. 
end

# "bad" implementation - example of desirable asynchronous behaviour:
def read(io, size)
  # funny implementation
  while true
    result = Fiber.blocking{io.read_nonblock} # prevent infinite recursion
    if result == :wait_nonblock
      io.wait_readable
    else
      return result
    end
  end
end
```

- `Fiber.blocking{}`
- `Fiber.exclusive{}` (maybe not good because it's not exclusive).

- We already have `Fiber.blocking?` (whether the current Fiber / execution context is blocking or not).
- We already have `Fiber.current.blocking?` (reflection of `Fiber.new(blocking: ...)`

``` ruby
Fiber.blocking? # maybe falsey
Fiber.blocking do
  Fiber.blocking? # truthy
end
```

* akr: I think the method name should contain the word "schedular" like `Fiber.schedular_blocking {}`
* akr: How about `Fiber.disable_schedular {}` ?

`fiber.blocking? → true or false`

Returns true if fiber is blocking and false otherwise. Fiber is non-blocking if it was created via passing blocking: false to Fiber.new, or via Fiber.schedule.

Note that, even if the method returns false, the fiber behaves differently only if Fiber.scheduler is set in the current thread.

See the “Non-blocking fibers” section in class docs for details.

`Fiber.blocking? → false or 1`

Returns false if the current fiber is non-blocking. Fiber is non-blocking if it was created via passing blocking: false to Fiber.new, or via Fiber.schedule.

If the current Fiber is blocking, the method returns 1. Future developments may allow for situations where larger integers could be returned.

Note that, even if the method returns false, Fiber behaves differently only if Fiber.scheduler is set in the current thread.

See the “Non-blocking fibers” section in class docs for details.

* ko1: do we have `-ing{}` style naming (adjective word)?
  * ko1: it is deprecated but `Thread.exclusive{ ... }` is one example.

Conclusion:

* matz: accepted. I will reply


### [[Feature #18455]](https://bugs.ruby-lang.org/issues/18455) `IO#close` has poor performance and difficult to understand semantics. (ioquatix)

- We need to fix implementation of `IO#close` which is O(number of blocking operations).
- We need to hide implementation of `struct rb_io_t` because the internal implementation details are leaking into public interface (e.g. `ccan_list`).

Preliminary discussion:

```C
        // Ensure file is open - raise error if not
        GetOpenFile(file, fptr);
#if defined(_WIN32)
        add_format_prefix(info, fptr->pathv);
        SetImageInfoFile(info, NULL);
#else
        SetImageInfoFile(info, rb_io_stdio_file(fptr));
#endif
```

Discussion:

* mame: IO#close takes O(N) where N is the number of waiting threads.
* samuel: For N file descriptors, with M waiting on some data, IO#close can take O(M).

* There is a pull request to fix this.
* It requires changes to `struct rb_io_t`.
* So, it's breaking change in public interface.
* Also, it needs `ccan_list` which is implementation detail.
* So we should hide `struct rb_io_t` before making changes.

- ko1: another idea
    1. preapare array (size >= maximum FD)
    2. waiting for fd number F1, write array[F1]
    3. if F1 is closed, then check array[F1]
    
    - Memory usage problem? 100M fd -> 800MB

- Can we remove the wait list entirely?
    - T1: close(fd); T2: read(fd) -> EBADF
    - Can we take advantage of this?

```c
int fd = fptr->fd; // safe if fd != -1
// case 1:
// release gvl
// some other thread calls IO.close
// fd is reused
read(fd, ...) // -> okay but "wrong" fd (race condition)
    
// case 2:
// release gvl
read(fd, ...) // -> EBADF
```

mame:

```ruby!
# https://bugs.ruby-lang.org/issues/19039
r1, w1 = IO.pipe
r2, w2 = IO.pipe
r3, w3 = IO.pipe
a = (0..10).map do
  Thread.new do
    select([r1, r2, r3])
    p :ok
  end
end
sleep 1
p r1.close
sleep 1
w2 << "foo" #=> `select': closed stream (IOError) in the threads
a.each {|th| th.join }
```

- If the main concern is compatibility, we can have a public struct which is a subset of the whole:

``` c
// io.h
struct rb_io_public {
  [[deprecated]] int fd; // will print warnings.
};

#define rb_io_t rb_io_public

// io.c
struct rb_io {
  rb_io_public public_part;
  buffers...
  encodings...
  lists..
  etc...
};
```

* limitation for rb_io_t <-> IO object approach
  * 1: io1 = IO.for_fd(10); io2 = IO.for_fd(10)
  * 2: can't check fd not connected with IO object
  * Are these the same file descriptors?
  * Can we avoid using wait list? (EBADF, memory barrier, etc)
  * samuel: I'm okay with this limitation. Why do we assume io1 and io2 are the same? Why should closing io1 cause io2 to become closed? (seems like spooky action at a distance).
  * matz: ok, no problem.

It's already a problem for native code:

```c
int fd = rb_io_descriptor(io);
close(fd); // does not wait for other threads.
rb_io_close(io); // waits for other threads which are calling `io.read` or `io.write`.
```

- samuel: proposed approach:
    1. Hide `struct rb_io_t` and introduce macros.
    2. Fix performance issues of `io_close`.
    3. (later) `io_close` fiber scheduler hook for non-blocking `IO#close`.

- samuel: it's tricky to make improvements with people writing `read(fptr->fd, ...` style.

- current old PR: https://github.com/ruby/ruby/pull/5384

Conclusion:

* samuel: split PR into (1) hide `struct rb_io_t` and (2) fix `IO#close` performance.
    * Explain new fields & structures, public macros. Summarise new interfaces for discussion.

### [[Bug #18886]](https://bugs.ruby-lang.org/issues/18886) Struct aref and aset don't trigger any tracepoints. (ioquatix)

- Do we care enough to fix this issue or is the performance cost too high?

Preliminary discussion:

* samuel: I don't care much about the specifics, but I wonder if we want to consider the problem:
  * "tracepoint is slow, even if disabled, so we avoid adding it, even to places where it's useful."
  * can we make trace point zero cost?
  * What about hotpatching traced functions? Or disable tracepoint in "production build"?

Discussion:

* samuel: I am happy to close the issue with no action.
* samuel: Alternatively we should improve tracepoint implementation, e.g. hotpatching functions at runtime: <https://nullprogram.com/blog/2016/03/31/>

Conclusion:

* ko1: I will close this issue in future

### [[Feature #19022]](https://bugs.ruby-lang.org/issues/19022) Use __builtin_ppc_get_timebase on POWER with clang (pkubaj)

* A simple change that allows run performance improvement

Discussion:

* akr: There is `GCC_VERSION_SINCE(4,8,0)`. Do all versions of clang support the function?

### [[Feature #19023]](https://bugs.ruby-lang.org/issues/19023) Enable riscv64 coroutines on riscv64-freebsd, arm32 on arm*-freebsd and ppc on powerpc-freebsd (pkubaj)

* Passes make test

Discussion:

* nobu: It changes coroutine/ppc/Context.S. I am afraid if this change affects other platforms than freebsd
* akr: I want a referece to rationale that explains the change (such as an document of the assembly)
    * samuel: I believe it's AT&T (`%register`) vs Intel syntax (`register`). The format depends on the assembler, so this change might break on some platforms... but I don't know. Does anyone have PPC????
    * mame: ppc with ubuntu is provided by IBM but there is no ppc + freebsd
    * samuel: Well, even if it breaks, does anyone care? We can just fix it later?
    * naruse: I believe freebsd's assembler can read both AT&T and Intel syntax. I guess this PR does include unnecessary changes
* samuel: I asked the original author of this code to give their feedback: <https://github.com/ruby/ruby/pull/5852#issuecomment-1269442714>

### [[Bug #19016]](https://bugs.ruby-lang.org/issues/19016) syntax_suggest is not working with Ruby 3.2.0-preview2

Discussion:

* nobu: How about keeping the message string by calling detailed_message *before* thread termination, and print it after thread termination?
* mame: I am not against it, but the patch will be a bit complicated

Conclusion:

* matz: call detailed_message before thread termination
* matz: I leave it to @nobu and @mame to print the message before or after thread termination

## Random topic: LINQ in Ruby

```ruby!
passed_students = students.select {|s| s.score > 200 }.map {|s| s.name }

passed_students = students.where {|s| s.score > 200 || s.score2 > 200 }
passed_students = students.where("score > 200 || score2 > 200")
Student.select(Arel.star).where(
  Student.arel_table[:score].gt(200).or(Student.arel_table[:score2].gt(200))
)
Student.select(Arel.star).where(
  Student[:score].gt(200).or(Student[:score2].gt(200))
)
  (Student[:score] > 200) | (Student[:score2] > 200))

passed_students = from s in students where PostGIS.intersection(s.poly1, s.poly2).area > 200 select s.name
passed_students = students.where(-> s { s.score > 200 }, "s.score > 200").select(-> s { s.name })

passed_students = [s.name : s in students, s.score > 200]

select name from students where score > 200
```

[Zyacc Homepage](http://www.cs.binghamton.edu/~zdu/zyacc/) 


## Implement cache optimization for regexp matching

https://fservant.github.io/papers/DavisServantLee-SelectiveMemo-IEEE-SP21.pdf
https://github.com/ruby/ruby/pull/6486

```
$ time ruby -e '/^(a|a)*$/ =~ "a" * 25 + "b"'

real    0m2.860s
user    0m2.836s
sys     0m0.020s

$ time ./miniruby -e '/^(a|a)*$/ =~ "a" * 25 + "b"'

real    0m0.034s
user    0m0.024s
sys     0m0.008s
```

* naruse: Let's introduce `Regexp.timeout=` as an experimental feature of Ruby 3.2.
* naruse: If this caching optimization works well enough in future, we can remove `Regexp.timeout=`

[bc4c862a763749a831dfe4e874229aae681ae0fd](https://github.com/ruby/ruby/commit/bc4c862a763749a831dfe4e874229aae681ae0fd)
