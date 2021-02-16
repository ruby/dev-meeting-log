---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200216Japan

https://bugs.ruby-lang.org/issues/17535

## DateTime and location

* 2/16 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 3/17 (Wed) 13:00-17:00 JST @ Online
  * pre-reading: 3/15 (Mon)

## Announce

## About 3.0/3.1 timeframe

* 3.0.1?
  * ko1: I want to tackle with the cache problem on Rails
  * naruse: It is a blocker
  * ko1: https://bugs.ruby-lang.org/issues/17553
  * mame: It looks like an issue of master. Maybe unrelated to 3.0 branch?
  * ko1: Will investigate today or tomorrow

## Check security tickets

[secret]

## Tickets

### [[Feature #14394]](https://bugs.ruby-lang.org/issues/14394) Class.descendants (ko1)

* ko1: need some confirmations

* The performance is not so important

* self should be excluded

* singleton classes should be excluded

* order is random (not specified)

* performance of this method is not important, or important (calls it many times)?

* ko1: What is `Module#descendants`? ActiveSupport provides only `Class#descendants`.

```ruby
module M
end

p M.ancestors #=> M, Kernel

class C0
  prepend M
end

class C1 < C0
end

p C0.ancestors
#=> [M, C0, Object, Kernel, BasicObject]

p C1.ancestors
#=> [C1, M, C0, Object, Kernel, BasicObject]

p M.descendants
#=> [M, C0, C1]
```

```ruby
module M0
end

module M1
end

module M
  include M0
  prepend M1
end

class C0
  prepend M
end

class C1 < C0
end

p C0.ancestors #=> [M1, M, M0, C0, Object, Kernel, BasicObject]
p C1.ancestors #=> [C1, M1, M, M0, C0, Object, Kernel, BasicObject]

p M.ancestors #=> [M1, M, M0]
p M.descendants #=> ??
```

* ko1: no `Module#descendants`, `Class#descendants` should behave same as ActiveSupport?

Conclusion:

* matz: go ahead about `Class#descendants`

### [[Bug #17592]](https://bugs.ruby-lang.org/issues/17592) Allow reading class instance varaibles from non-main Ractor (marcandre)

* We need way to have global config that is mutated very little. `TVar` may be a solution; reading class instance variables would be simpler, backwards compatible.

Preliminary discussion:

* ko1: This ticket proposal: To maintain global configuration (mutable information), class/module instance variables should be readable from other ractors.
 
```ruby
module Foo
  singleton_class.attr_accessor :config
  Foo.config = {example: 42}.freeze
end

# Current
Ractor.new{ p Foo.config } # => IsolationError

# Proposal
Ractor.new do
  p Foo.config #=> {example: 42}
  p Foo.config = {example: 43}.freeze
    #=> IsolationError, beacuse it is read-only from non-main ractors
end
    
Foo.config = {example: 44}.freeze # allowed updating from the main ractor
```

Usage:
https://github.com/ruby/uri/pull/15/files#diff-936b286152b1184cde04f027289d65e633d0f3ee52fdc42cf4eb072c24312e15R84

* ko1: There are two concerns: (1) atomicity concern and (2) performance concern.

(1) Atomicity concern

If two or more ivars (named `@a` and `@b`) should be update atomic, but threre is no way to synchronize them.

```ruby
class C
  @a = @b = 0
  def self.update
    # assertion: @a, @b should be equal
    @a += 1
    @b += 1
  end
  def self.vars
    [@a, @b]
  end
end
```

Main ractor can calls `C.update` and update ivars. A ractor can call `C.vars` and it can returns inconsist values (`@a != @b`).

The danger of this concern is relatively low because this example is very artificial. Maybe most of usecase is initialization at loading time and no other ractors read while mutating. Also there is no coupled variables (like `@a, @b`), there is no problem. For example, there is no problem with only one `@config` ivar which manages all configrations. In other words, two or more configurations `@configA`, `@configB`, ... can have an atomicity issue.

The following code is also artifitial example.

```ruby
class Fib
  @a = @b = 1
  # @a and @b are successive parts of the Fibonacci sequence.
  def self.next
    @a, @b = @b, @a + @b
  end

  def values
    # it should return successive parts of the Fib seq.
    # == "Fib seq constraint"
    [@a, @b]
  end

  def self.eventloop
    loop{
      Ractor.receive; Fib.next
    }
  end
end

gen = Ractor.new(Ractor.current){|main| loop{ m << true } }
con = Ractor.new{ 
  p Fib.value #=> return values can violate "Fib seq constraint"
}
```

If a user misused as an above example (using class/module ivars for the mutable state repository and updating them with multiple ractors (via the main-ractor)), it is danger.

For this concern, we have several options.

* (a) there is no problem to introduce this feature because it is almost safe.
* (b) Ractor is designed to avoid such consistency issues even if it can be avoided by careful programming. So this feature should not be introduced.

(b) is my position, but I agree it is very conservative.

This proposal has advantage for compatibility because many existing code can use ivars for sharing the global configurations and there is no need to rewrite them (if they only refer to sharable objects).

For example, `pp` library has one global configuration: `sharing_detection` which is stored in a class instance variable.

https://github.com/ruby/ruby/blob/master/lib/pp.rb#L109

For Ractor, it was rewrote by using Ractor-local configuration, but it was not ideal modification but ad-hoc modification with existing tools.

If this proposal is introduced, we can revert the ad-hoc modification.

(2) Performance concern

To allow accessing ivars from other ractors, every ivar accesses to class/module should be synchronized. Current implementation doesn't need to synchronize this accesses.

For constants and method search, we implemented const cache and method cache mechanisms. Such cache mechanisms are reasonable because they are not frequently rewriting. On the other hands, ivars can be mutated more frequently and not sure the it is reasonable to have a cache mechanism for it.

----

ko1: Alternative proposal is using TVar.

Rewriting configuration example with TVar:

```ruby
module Foo
  Config = Ractor::TVar.new{ {example: 42}.freeze }
end

Ractor.new do
  Ractor.atomically do
    p Foo::Config.value #=> {example: 42}
  end
  ...
  Ractor.atomically do
    Foo::Config.value = {example: 43}.freeze
    # modification is allowed within atomically block
  end
end

Ractor.atomically do
  Foo::Config.value = {example: 44}.freeze
end
```

The advantage of this example is it is clear that manipulating sharable state between Ractors because `Ractor.atomically` method is needed. Another way of saying this is that we can become more aware of creating a shared state. With instance variables, it is hard to figure out which instance variables are shared with multiple Ractors.

Disadvantages:

* Writing `Ractor.atomically` is long to type.
* Incompatible with older versions.

Configuration should not be changed frequently, so the performance should not be a problem.

```ruby
module Foo
  Config = Ractor::TVar.new{ {example: 42}.freeze }
end

Ractor.new do
  p Foo::Config.value #=> {example: 42}
end
    
Ractor.atomically do
  Foo::Config.value = {example: 44}.freeze
end
```

Rewriting other examples with TVar.

```ruby
class C
  A = Ractor::TVar.new 0
  B = Ractor::TVar.new 0

  def self.update
    # assertion: A.value and B.value should be equal
    Ractor.atomically do
      A.value += 1
      B.value += 1
    end
  end
  def self.vars
    Ractor.atomically do
      [A.value, B.value]
    end
  end
end
```

```ruby
class Fib
  A = Ractor::TVar.new 1
  B = Ractor::TVar.new 1

  def self.next
    Ractor.atomically do
      A.value, B.value = B.value, A.value + B.value
    end
  end

  def values
    # it should return successive parts of the Fib seq.
    # == "Fib seq constraint"
    Ractor.atomically do
      [A.value, B.value]
    end
  end

  def self.eventloop
    loop{
      Ractor.receive; Fib.next
    }
  end
end

gen = Ractor.new(Ractor.current){|main| loop{ m << true } }
con = Ractor.new{ 
  p Fib.value #=> TVar allows us to keep constraint
}
Fib.eventloop

# NOTE: Using TVars, there is no reason to maintain states
# by a main-ractor, so gen ractor can access Fib.next directly.

gen = Ractor.new{loop{ Fib.next } }
con = Ractor.new{ 
  p Fib.value #=> TVar allows us to keep constraint
}
```


Discussion:

* naruse: if the debugging feature is available, it is acceptable, for example, TracePoint events for ivar accesses.
* akr: how about Modules?
* ko1: This proposal is for classes and modules.

Conclusion:

* Matz: accepted.
* ko1: only for classes and modules.

### [[Feature #17291]](https://bugs.ruby-lang.org/issues/17291) Optimize `__send__` call (mrkn)

* [rspec-mocks](https://github.com/rspec/rspec-mocks) depends on redefining `__send__` to detect the form of the method call in a mock object.
* The mock object raises NoMethodError when the method call form is `obj.method` and the called method is not public.
  * This behavior occurs the following step:

Preliminary discussion:

* There is a fundamental problem of delegation and `__send__`
  * In short, the following should succeed (?)

```ruby
require "delegate"

class Foo
  private
  def foo
  end
end

obj = SimpleDelegator.new(Foo.new)
p obj.__send__(:foo) #=> undefined method `foo'
```

* rspec-mocks solved this issue by redefining `__send__`
  * If we prohibit redefinition of `__send__`, this solution will be disabled
* mame: is this optimization so worth?
* ko1: should we not touch this spec and encorage new syntax?

```ruby
obj $$$(name)
```

Discussion:

* mrkn: To support rspec-mocks case more elegantly, we need a new feature to distinguish how a method is called (`obj.foo`? `obj.__send__(:foo)`? or vcall or fcall?)
* mrkn: In regard with rspec-mocks case, it would be enough for method_missing to be able to distinguish the calling style

* mame: For example, `def method_missing2(method_name, calling_style, ...args)`

```ruby
class Foo
  def method_missing2(name, style, ...)
    p style
  end
end

Foo.new.foo            #=> :normal
Foo.new.__send__(:foo) #=> :__send__
...
```

* naruse: it may reduce the performance of existing programs that use method_missing
* naruse: Or `binding.calling_style` or what not

* mame: is this support really needed?
* naruse: Mock is very important for Ruby, so the support is important

* ko1: we plan to introduce a new syntax to `Kernel#send` alternative. If so, we need to care this problem? If the new syntax calls only public methods, there will be no matter
* matz: the new syntax should call only public methods (like Kernel#public_call)
* mame: If so, why is the new syntax needed? We rarely redefine public_call

Conclusion:

* pending (need to re-implement rspec-mock and need some features)


### [[Feature #16989]](https://bugs.ruby-lang.org/issues/16989) Sets: need ♥️ (mame)

* https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20210113Japan.md#feature-16989-sets-need-%EF%B8%8F-mame
* mame: in the previous meeting, matz said a small start by using autoload (like pp and binding.irb)
* knu: neutral
* ko1: is it really needed? `require "set"` looks enough to me

```ruby
s = Set[1, 2] # works without require
```

```shell
ko1@aluminium:~$ gem-codesearch '\bSet\[' | wc -l
8464

$ gem-codesearch '\bSet\.new' | wc -l
28213

ko1@aluminium:~$ gem-codesearch '\.to_set'  | wc -l
6470
```

* usa: `to_set` is not supported by autoload. Does need use the same mechanism (method redefinition) of `pp`?
* knu: It does not effect to `&:to_set`.
* usa: oops...

Conclusion:

* matz: wait for builtin.

### [[Feature #12194]](https://bugs.ruby-lang.org/issues/12194) `File.dirname` optional parameter (nobu)

* `File.dirname(path, 2)` instead of `File.dirname(File.dirname(path))` or `File.expand_path("../..", path)`.

Preliminary discussion:

* mame: should it be a keyword argument?
* mrkn: `depth` or `level`?
* nobu: or another method `File.upper_directory`?

Discussion:

```ruby=
p File.dirname('/foo/bar/') #=> "/foo"
p File.dirname('/foo/bar')  #=> "/foo"
p File.dirname('/foo')  #=> "/"
p File.dirname('/')  #=> "/"

p File.dirname('foo/bar/') #=> "foo"
p File.dirname('foo/bar')  #=> "foo"
p File.dirname('foo') #=> "."
p File.dirname('.')  #=> "."

p File.dirname('../..')  #=> ".."
p File.dirname('../')  #=> "."
p File.dirname('..')  #=> "."
```

* usa: What will happen if the level is 0?
* ko1: if it means applying the default dirname N times, it should return the argument as-is
* knu: how about removing trailing slash if n == 0?
* usa: small start. raise ArgumentError if n <= 0

Conclusion:

* matz: looks good; an optional argument is fine

```ruby=
class File
  def self.dirname name, n = 1
    raise ArgumentError if n <= 0
    n.times{ name = File.orig_dirname(name) }
    name
  end
end
```

### [[Feature #17544]](https://bugs.ruby-lang.org/issues/17544) Support RFC 3339 UTC for unknown offset local time (nobu)

* In RFC 3339, -00:00 is used for the time in UTC is known, but the offset to local time is unknown.
> * -0000, -00:00
>   In RFC 2822, -0000 the date-time contains no information about the
>   local time zone.
>   In RFC 3339, -00:00 is used for the time in UTC is known,
>   but the offset to local time is unknown.
>   They are not appropriate for specific time zone such as
>   Europe/London because time zone neutral,
>   So -00:00 and -0000 are treated as UTC.

Discussion:

```ruby
def get_t2000
  if no_leap_seconds?
    # Sat Jan 01 00:00:00 UTC 2000
    Time.at(946684800).gmtime
  else
    Time.utc(2000, 1, 1)
  end
end

assert_equal("+0000", t2000.strftime("%z"))
assert_equal("-0000", t2000.strftime("%-z"))
assert_equal("-00:00", t2000.strftime("%-:z"))
assert_equal("-00:00:00", t2000.strftime("%-::z"))

Time.new(2000, 1, 1, in: "+00:00").strftime("%-z") #=> "+0000"
Time.new(2000, 1, 1, in: "-00:00").strftime("%-z") #=> "-0000"
```

* akr: No problem for Time object in UTC mode, I think.  It should not change local time mode and fixed-offset mode.

Conclusion:

* nobu: will investigate further, and will do unless I see a big problem
* matz: ok


### [[Feature #17548]](https://bugs.ruby-lang.org/issues/17548) Need simple way to include symlink directories in `Dir.glob` (nobu)

* cost to avoid infinite recursion

Preliminary discussion:

* znz: zsh has `***`

```
% ls **/
baz

% ls ***/
zsh: too many levels of symbolic links: baz
bar/:
baz

bar/baz/:
bar

bar/baz/bar/:
baz

bar/baz/bar/baz/:
bar

bar/baz/bar/baz/bar/:
baz
```

Conclusion:

* matz: okay for `***`
* nobu: will commit


### [[Feature #17611]](https://bugs.ruby-lang.org/issues/17611) Expose `rb_execarg` interfaces and `rb_grantpt` (nobu)

* Needed by pty.
* `rb_grantpt` may be questionable.
* https://github.com/nobu/pty
  * https://github.com/nobu/pty/blob/master/ext/pty/internal/process.h

Discussion:

* akr: objection, because ...(difficult for mame to log)
* akr: spawn should support controling terminal, which will solve the API issue
* akr: How about ctty keyword for spawn?

Conclusion:

* nobu: will reconsider


### [[Feature #15752]](https://bugs.ruby-lang.org/issues/15752) A dedicated module for experimental features (eregon)

* From the discussion about `RubyVM` in [Feature #17500], I think it became clear that `RubyVM` is not a good place to put experimental features.
* So, how about adding an `Experimental` or `ExperimentalFeatures` module, and add new experimental APIs there, *instead* of in `RubyVM`?

Conclusion:

* naruse: pend one year


### [[Bug #17591]](https://bugs.ruby-lang.org/issues/17591) Test frameworks and REPLs do not show deprecation warnings by default (eregon)

* I think ruby-core needs to have a clear message on this, and create PRs or issues for the main test frameworks/REPLs to show examples.

Preliminary discussion:

* nobu: test-unit enables it now.

Discussion:

* aycabta: IRB is often used by beginners for learning purposes, so I disagree.
* ???: need work for minitest and rspec
* akr: I want to see the feelings of test-unit's change

Conclusion:

* pending

### [[Feature #17593]](https://bugs.ruby-lang.org/issues/17593) `load_iseq_eval` should override the ISeq path (byroot)

- When loading a ISeq returned by `load_iseq(fname)` `fname` should be used as top stack location.
- Right now the location of the source file (compiled with `compile_file`) is used.
- Because of it moving a source file invalidate its iseq cache.
- This makes ISeq caching impossible on some platforms like Heroku.
- `__FILE__` would need to be changed to be a method like `__dir__`

Conclusion:

* matz: leave it to ko1
* ko1: I'll introduce it if it can be implemented.

### [[Feature #17610]](https://bugs.ruby-lang.org/issues/17610) [PATCH] Reduce `RubyVM::InstructionSequence.load_from_binary` allocations (byroot)

- Reduce allocations by 65%.

Conclusion:

* ko1: It is an implementation issue. I will do


### [[Feature #17608]](https://bugs.ruby-lang.org/issues/17608) Compact and sum in one step (sawa)

  * Let `Array#sum` ignore `nil` values, or introduce a method that does that.
  * Many actual use cases of `Array#sum` are followed by `compact`.
  * If we need to take care of `nil` values manually or  pass parameters to adjust what we want to do, then we could use `inject` instead, and there is less motivation for the `Array#sum` method in the first place.
  * The reason for adding this in the agenda is because it has been closed before being discussed.

Preliminary discussion:

* nobu: if `Numeric.sum` were provided, it would be fine to filter the arguments.
* mrkn: Pandas has `skipna` optional argument in `pandas.DataFrame.sum` instance method.  But this skips not only `Null` but also `NaN` when `skipna=True` is specified.

Discussion:

https://github.com/search?l=Ruby&q=compact.sum&type=Code

https://github.com/search?l=Ruby&q=compact.max&type=Code

https://api.rubyonrails.org/classes/ActiveRecord/Calculations.html

```ruby
ary = [
  {a: 1},
  {a: 2, b: 1},
  {b: 1},
]

ary.pluck(:a)                #=> [1, 2, nil]
ary.pluck(:a, compact: true) #=> [1, 2]
ary.sum {|r| r[:a] || 0 }
```

* naruse: I guess the receiver is `...pluck` in many cases. If so, they should call `sum` directly

Conclusion:

* matz: rejected

### [[Bug #17594]](https://bugs.ruby-lang.org/issues/17594) Sort order of UTF-16LE is based on binary representation instead of codepoints (dan0042)

* 'a' < 'ā' is true for UTF-16BE and false for UTF-16LE; imho it would be better to have the same result.
* casecmp compares codepoints when ascii, bytes otherwise

Discussion:

* (difficult for mame to log...)

Conclusion:

* naruse: this is by design. will close

### [[Feature #16978]](https://bugs.ruby-lang.org/issues/16978) Ruby should not use realpath for `__FILE__`

* related: bugs.ruby-lang.org/issues/10222

```ruby
# /root/test.rb
p __FILE__
require_relative "test/foo"

# /root/test/foo.rb
p __FILE__

# /root/sandbox/test.rb -> /root/test.rb
# /root/sandbox/test/   -> /root/test/
```

```
$ ruby -e '
  $: << "/root/sandbox"
  puts "test"
  require "test"
  puts
  puts "test/foo"
  require "test/foo"
'
test
"/root/sandbox/test.rb"
"/root/test/foo.rb"

test/foo
"/root/sandbox/test/foo.rb"
```

* akr: `require "test/foo"` should load `/root/test/foo.rb` instead of a symlink `/root/sandbox/test/foo.rb`

### Timeout

* [Feature #17363: Timeouts](https://bugs.ruby-lang.org/issues/17363) 
* [Feature #17470: Introduce non-blocking `Timeout.timeout`](https://bugs.ruby-lang.org/issues/17470)

naruse: There're two layers: low-level select/nonblock read/write layer and high-level layer like net/http.
