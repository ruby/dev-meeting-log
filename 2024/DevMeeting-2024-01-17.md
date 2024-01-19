# DevMeeting-2024-01-17

https://bugs.ruby-lang.org/issues/20075

## DateTime and location

* 2024/01/17 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2024/02/14 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #20182]](https://bugs.ruby-lang.org/issues/20182) Rewrite `Array#each` in Ruby (k0kubun)

* Can we discuss whether `Array#each` is supposed to operate on the receiver atomically or not?

Discussion:

* samuel: general mutation during `each` seems to be a feature (separately from race condition).

```ruby
items = 10.times.to_a
items.each do
  puts items.pop
end

# output:
9
8
7
6
5
=> [0, 1, 2, 3, 4]
```

#### Race condition
* matz: JRuby has have this race since the beginning doesn't it?
* K0kubun: @eregon says TruffleRuby avoids such race conditions.
* mame: @k0kubun has a workaround though.
* matz: sounds okay if user has appropriate mutex around multithreaded codes...
* shyouhei: But "inserting appropriate mutex around mutithreaded codes" is impossible for human beings; we learned in our decades of experiences.
* mame: `Array#each` to yield `nil` sounds very problematic.
* mame: also that should trigger panic pull-request storm for every existing gems.

#### Use of `Integer#succ`
* matz: Okay for using this particular method.
* mame: Redefinition of `Integer#succ` practically kills everything else.
* ko1: This could affect debuggers.  debuggers can now step into this method.

Conclusion:

* Okay for rewriting `Array#each`.
* Race conditions are not welcomed.  If avoidable using adequate complexity, let us do so.
* If a user redefines `Integer#succ`, they are on their own.

### [[Feature #20102]](https://bugs.ruby-lang.org/issues/20102) Introduce `Fiber#resuming?` (ioquatix)

* Can we introduce?

Preliminary discussion:

* mame: By using `Fiber#resuming?`, what behavior is desired if it is impossible to `Fiber#raise`?
* samuel: We can know to raise later, which is how it works here: https://github.com/socketry/async/blob/a2d9af42dd1dedd364a18781723a32f5994262ea/lib/async/task.rb#L228-L234 - by using `Fiber#resuming?` we can avoid this unusual exception handling.

```ruby
# Expected usage:
if fiber.resuming?
  scheduler.raise_later(fiber, ...) # During the next iteration of the event loop.
else
  fiber.raise(...)
end

# Current hack:
begin
  fiber.raise(...)
rescue FiberError
  scheduler.raise_later(fiber, ...)
end
```

Discussion:

* ioquatix: I want to avoid raising exceptions from inside of a resuming mutex.
* matz: what happens at scheduler.raise_later in the examble above?
* ioquatix: it essentially rewinds that fiber, but at the point it can.
* nobu: what's the problem of the status quo?
* ioquatix: it uses exceptions as contol, which is ugly.
* ko1: fiber scheduler should know which fiber is resuming or not.
* ioquatix: fair point, but a user code can or cannot be resuming and we cannot control.
* ko1: I don't think it's possible to schedule arbitrary fibers.
* ioquatix: but it works™️.
* matz: I'm actually for adding a method.  But the method name does not sound well.  `resuming` might not be our primary concern.
* ioquatix: I don't have any strong opinion on the name.
* ko1: maybe `resumable?`
* ioquatix: or `raisable?`
* ioquatix: `resumable?` -> true if the fiber is not `resuming?`
    * `Fiber#kill`, `Fiber#raise` will decide to use `resume` depending on the situation.

```ruby
fiber2 = nil

fiber1 = Fiber.new do
  fiber2.resume
end

fiber2 = Fiber.new do
  while true
    puts fiber1.resuming? # true
    Fiber.yield
  end
end

fiber1.resume
```

Conclusion:

* `resuming?` does not describe what we want, but the feature itself is okay.
* -> continue discussion.


### [[Feature #19057]](https://bugs.ruby-lang.org/issues/19057) Hide implementation of `rb_io_t`. (ioquatix)

* Eric has agreed to release updated gems. Can we continue to move forward?

Preliminary discussion:

* samuel: Initial PR: https://github.com/ruby/ruby/pull/9568 - expected additional clean up of encoding and buffer structs.

Discussion:

* ko1: this is reverted because it breaks unicorn.
* ioquatix: @normalperson approved this.
* ko1: basically there is nothing that blocks merging this one, it seems to me.
* ioquatix: (slow path) We can try to do the PR above, without any further changes for 6 months, and see if there are any problems. That way, it's easy to revert.
* ko1: do we plan to hide the file descriptor from C extensions?
* ioquatix: that's a different and difficult problem.
* ko1: I remember there is rb_io_fd().
* ioquatix: The situation is much complicated.
* ioquatix: besides, there are tons of other fields than fd (mode, encoding, buffer, ...) which are really annoying when being public.

```ruby
io = File.open(...)
io.timeout = 0.1
io.encodings
io.sync
io.read -> blah

# C extension
rb_io_descriptor(io) -> io
read(io) ... etc 
# Buffering doesn't work, Encoding doesn't work....

`read(rb_io_descriptor(io), ...)` -> `rb_read(io, ...)` # better
rb_read(io, ...) # makes the most sense
```

Conclusion:

* Let's give it a try and see if unicorn releases a new version...
* We should revert again if Eric don't release new version of unicorn, etc at Ruby 3.4.

### [[Bug #19857]](https://bugs.ruby-lang.org/issues/19857) Eval coverage is reset after each `eval`. (ioquatix)

* Can we merge proposed fix? https://github.com/ruby/ruby/pull/9571

Preliminary discussion:

* samuel: `eval` and similar methods will clear existing coverage., e.g.

```ruby
require 'coverage'
Coverage.start(lines: true, eval: true)

def render(name, age)
  # Pretend to be simple ERB template (or something similar):
  template = eval(<<~RUBY, binding, "choice.rb", 1)
  proc do |name, age|
    if age < 10
      "Hi #{name}!"
    else
      "Dear #{name},"
    end
  end
  RUBY

  return template.call(name, age)
end

# Pretend to run two tests:
render("John", 5)
render("Jane", 15)

# Do you expect 100% coverage?
puts Coverage.result
```

Discussion:

* ioquatix: Basically we have a template. And run it. Then the coverage is below 100%.
* mame: This particular example doesn't seem 100% covered to me.
* ioquatix: why?
* mame: For instance local variable names could be different in each eval.
* samuel: We have real production test suites where every line is executed, but the coverage is not captured.

* mame: coverage library does not support some use cases I admit, but I don't want to make it more complex.  It was designed to take coverage of source _files_, not source _codes_.
* samuel: from the developer's point of view, I think they would expect this to be 100% coverage, the actual lines of code in the source file given.
* mame: one thing is that the "actual lines of code in the sorce file" is kind of difficult to infer.
* ioquatix: that has to be inferrable using eval's 3rd and 4th argument.
* matz: that's not guaranteed here.  To get the trustworthiness of the coverage we need to make sure this point.
* matz: I understand the issue but the un-trustworthiness coverage may be worse than the current.
* matz: maybe we need a better solution.
* ko1: how other languages (python etc.) handle this situation could help designing our one.

Conclusion:

*


### [[Feature #19742]](https://bugs.ruby-lang.org/issues/19742) Introduce `Module#anonymous?` (ioquatix)

* Can we accept definition "Anonymous module is one that does not have a permanent name"?
* Can we introduce this interface?

Preliminary discussion:

* ko1: the word "anonymous" is first time to introduce?
* samuel: no, e.g. 

- `must_not_be_anonymous` https://github.com/ruby/ruby/blob/8642a573e6d1091fefbb552bb714ebcf8da66289/marshal.c#L252
- "Creates a new, anonymous class." https://github.com/ruby/ruby/blob/8642a573e6d1091fefbb552bb714ebcf8da66289/include/ruby/internal/intern/class.h#L32
- "Module#refine creates an anonymous module..." https://github.com/ruby/ruby/blob/8642a573e6d1091fefbb552bb714ebcf8da66289/doc/syntax/refinements.rdoc?plain=1#L31
- "A normal class, neither singleton nor anonymous" https://github.com/ruby/ruby/blob/8642a573e6d1091fefbb552bb714ebcf8da66289/lib/rdoc/normal_class.rb#L3
- And more... https://github.com/search?q=repo%3Aruby%2Fruby%20anonymous&type=code

Discussion:

* mame: context: because we now have `Module#set_temporary_name`, it is impossible for a user code to know if a module is anonymous or not, by looking at its name.
* ioquatix: main usage for this method is serialization.  If the class is anonymous there would be no way to deserialize.
* ioquatix: one of the confusing situaiton is `remove_const` which removes the name itself, but module itself thinks otherwise.
* matz: Hmm...?
* ko1: maybe reverting set_temporary_name could remedy...?
* ioquatix: no, the problem still exists.

```ruby
m = Module.new
m.set_temporary_name("foo")
m::N = Module.new
=> foo::N

class M
end

m = M.new
Object.send(:remove_const, :M)

m
=> #<M:0x000000011dfd9080>

m.class.name
=> "M" # Doesn't exist.
m.class.anonymous? # true

def serialize(m)
  if m.class.anonymous?
    raise TypeError
  end
  
  # ...
end
```

Current implementation in Ruby:

```c
static VALUE
must_not_be_anonymous(const char *type, VALUE path)
{
    char *n = RSTRING_PTR(path);

    if (!rb_enc_asciicompat(rb_enc_get(path))) {
        /* cannot occur? */
        rb_raise(rb_eTypeError, "can't dump non-ascii %s name % "PRIsVALUE,
                 type, path);
    }
    if (n[0] == '#') {
        rb_raise(rb_eTypeError, "can't dump anonymous %s % "PRIsVALUE,
                 type, path);
    }
    return path;
}
```

Conclusion:

* matz: `anonymous?` is not wordy enough here.  We need to clear the concept of being named. Besides, serialization issue could be something not directly a matter of naming.


### [[Feature #18035]](https://bugs.ruby-lang.org/issues/18035) Introduce general model/semantic for immutability. (ioquatix)

* Do we accept proposed syntax.

```ruby
# Method to make something immutable:
Immutable(x) -> x

# Module to make class immutable:
class Foo
  extend Immutable
end

Object#immutable? -> true/false
```

* Do we accept proposed list of immutable classes? https://gist.github.com/eregon/bce555fa9e9133ed27fbfc1deb181573

Preliminary discussion:

* samuel: previous discussion https://github.com/ruby/dev-meeting-log/blob/9f106e4e484f6a512666d346f7854dcd05d59391/2021/DevelopersMeeting20211021Japan.md?plain=1#L202
* ko1: where is "syntax" and "list"?

* samuel: Usage:
    * Safer code: sharing between ractors/threads/fibers.
    * Clear interface: don't modify this thing.
    * Potential optimisations (constant propagation, etc).

Discussion:

* (skipped this time due to time limit)

Conclusion:

*


### [[Feature #16495]](https://bugs.ruby-lang.org/issues/16495) Inconsistent quotes in error messages (ioquatix)

* Gathered feedback from major players in the industry. Is it sufficient to move forward?

Preliminary discussion:

```
$ mruby -e 'p foo'
trace (most recent call last):
-e:1: undefined method 'foo' (NoMethodError)

# double quote:
-e:1: undefined method "foo" (NoMethodError)
```
```
# Python
>>> foo
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'foo' is not defined


```

```
$ git grep "\`.+'"
spec/ruby/core/exception/backtrace_spec.rb:46:      line.should =~ /^.+:\d+:in `[^`]+'$/
```

code-search results: https://gist.github.com/ko1/adc9ffafd7c7ec15f6d37f0b23dec3cf

* samuel: not sure all those results are relevant.

Discussion:

* matz: This is for historical reasons only.  Besides possible compatibility issues, I have no strong objection.
* samuel: the main reason to do something is because it's **super** annoying to copy into markdown. The change optimises for developer experience.
  * shyouhei: (not against but JFYI) `` you can `double' backquote like this in markdown, including in GitHub.``
* mame: I tried this and lots of tests failed.  But they are just tests.  No production code I can find...?

* samuel: double quote is the standard for "quoting something". Single quote is usually for apostrophe, e.g. "it's".
* I feel single quotes are better because error messages are tends to quoted with double quotes.  Thus, single quotes to quote names are appropriate: "-e:1:in '<main>': undefined local variable or method 'foo' for main:Object (NameError)".

Conclusion:

* matz: I will reply "give it a try"

### [[Feature #13383]](https://bugs.ruby-lang.org/issues/13383) `Module#source_location` (matheusrich)

- Matz [asked](https://github.com/ruby/dev-meeting-log/commit/9f106e4e484f6a512666d346f7854dcd05d59391#diff-c64c00cad3a8a676641bd811a5d27600e162deedc5f3bfe398a43f3c78a88673R46) for specific use cases, so here are some:
  - Debugging/Code Discovery: Similar to `Method#source_location`, it would be

Conclusion:

* matz: I'll reject it.

### [[Feature #20066]](https://bugs.ruby-lang.org/issues/20066) Reduce Implicit `Array`/`Hash` Allocations For Method Calls Involving Splats (jeremyevans0)

* This is a collection of 5 optimizations, some of which are independent.
* The 4th and 5th allow for a performance optimization not currently possible, when using anonymous splats or `...` argument forwarding instead of named splat forwarding.
  * I expect these optimizations to have the most impact, after code is updated to switch to anonymous splats or `...` forwarding.
* Currently, the patch slows down yjit-bench with YJIT, because YJIT does not implement the new instructions, or contain the same optimizations.
  * I expect yjit-bench with YJIT will speed up if YJIT implements the same optimizations.
* Is it OK to merge the pull request as-is, or to merge a subset of the optimization patches?

Conclusion:

* ko1: I will response.

### [[Bug #5179]](https://bugs.ruby-lang.org/issues/5179) `Complex#rationalize` and `to_r` with approximate zeros (jeremyevans0)

* Discussed last at the January 2020 developer meeting (as part of #5321).
* Considering we have Float#to_r, can we change `Complex(1, 0.0).to_r` to return `Rational(1,1)`, as `Complex(1, 0.0.to_r).to_r` does?

Conclusion:

* matz: agreed to fix.

### [[Bug #19918]](https://bugs.ruby-lang.org/issues/19918) Should `a[&b]=c` be syntax valid? (jeremyevans0)

* This is currently valid syntax, and there are tests for it.
* Should we change this to invalid syntax?
* If we keep this syntax:
  * It currently evaluates `c` before `b`.
  * Should it be fixed to evaluate `b` before `c`?

Preliminary discussion:

```ruby
class C
  def []=(a, &b)
    p [a, b] #=> [1, #<Proc:0x000001fab94dd9a0 t.rb:8>]
  end
end
C.new[&proc{p 1}] = 1
```
```shell
$ ruby -c -e 'a[&b], c[&b] = d'
Syntax OK
```

Discussion:

* matz: Mmmmmmmmmmm
* matz: `a[&b]` is intentional. but for `a[&b] = c`…
* matz: I don't want to leave this syntax because nobody uses it.
* matz: I'm positive to remove it if it is easy.

```ruby
# Strange behavior
class C
  def []=(a, &b)
    p([a, b])
  end
end

a = C.new
b = C.new
pr = Proc.new{}
a[&pr], b[&pr] = nil, nil
# t.rb:10:in `<main>': undefined method `[]=' for nil (NoMethodError)
#
# a[&pr], b[&pr] = nil, nil
#  ^^^^^^^^^^^^^^^
```

Conclusion:

* matz: I'm positive to remove it if it is easy.

### [[Bug #19542]](https://bugs.ruby-lang.org/issues/19542) Operations on zero-sized `IO::Buffer` are raising (jeremyevans0)

* Is this behavior expected or a bug?

Preliminary discussion:

* samuel: It can be considered a inconsistency and I fixed it (made the behaviour consistent).

Conclusion:

* samuel: already addressed.


### [[Feature #20093]](https://bugs.ruby-lang.org/issues/20093) Syntax or keyword to reopen existing classes/modules, never to define new classes/modules (tagomoris)

- To make monkey-patching code more explicitly

Preliminary discussion:

* tagomoris: I want to know (make sure) if a `class String` statement is reopening an existing class or creating a new one.
* tagomoris: When implementing module namespaces, I want to know whether for instance `class String` is reopening the process-global String class (like ActiveSupport) or just want to have a new one.

Discussion:

* matz: I understand the needs of it.  But not the syntax.
* ko1: Does this really fix the namespace situation?
* tagomoris: `class String` in a namespace would create a new one, while `class extension String` would look for outer-namespace String class.

```ruby
# in namespace A
# A::String is defined
class String
  def yay; end
end

# ::String is defined
class ::String
end

class extension String
  def yay; end
end

## require 'json'
class JSON
end

## require 'json-my-ext'
class JSON
end
# end A

# simple-activesupport
String#yay
JSON#yay

require "simple/activesupport"

::String
# in namespace A
A::JSON #by require "json"
require "simple/activesupport"
# Not define: ::String#yay
# Want to define: A::String#yay
# Define: A::JSON#yay

# "class extension String" defines
A::String # as an alias to ::String

# in namespace A, (A::)String#yay should be able to be called
"foo".yay #=> ok

# out of namespace A, String#yay should not be able to be called
"foo".yay #=> NoMethodError


# in namespace A
class String # defines A::String as a brand new class
end
remove_const(:String)

String = define_shadow_class(:String) # defines A::String as a shadow class to toplevel's ::String
class String # reopens the shadow A::String
end

# monkeypatch.rb
if namespace_defined?(:String)
else # I know ::String is exist
  define_shadow_class(:String) # defines A::String as a shadow class to ::String
end

define_shadow_class(:String) if defined?(namespace)
reopen
class String # reopen
  def yay
end
```

Conclusion:

* We understand tagomoris want to support that namespace need to support monkey patch code like ActiveSupport inside a namepace
    * there are 4 situations:
       * Both outside and inside has the class
       * Only outside has the class
       * Only inside has the class
       * 
* continue to discuss with a namespace specification

### [[Feature #18576]](https://bugs.ruby-lang.org/issues/18576) Rename `ASCII-8BIT` encoding to `BINARY` (eregon)

* Can we experiment for 3.4 by making ASCII-8BIT the alias? If we have pushback based on actual code then let's go more conservative, but otherwise I think we should do the clean/consistent fix here.

Preliminary discussion:

`ko1@gem-codesearch:~$ gem-codesearch "==\s*['\"]ASCII-8BIT" | sort`: https://gist.github.com/ko1/dbc7f785782a7c63fb27389a423b5ccb

Discussion:

* (Everybody get surprised to see the list ko1 provided.)
* hsbt: wouldn't it be nicer to use @byroot's man-hour for something productive other than fixing the gems listed in the gist above?

Conclusion:

### [[Feature #20108]](https://bugs.ruby-lang.org/issues/20108) Introduction of Happy Eyeballs Version 2 (RFC8305) in Socket.tcp (shioimm)

- Can this be merged? Or is there something more to consider?

Discussion:

* shioimm: Created a pull request.  What should we do next?
* mame: @akr did you read the request?
* akr: Am reading now...
* shioimm: The implementation works, but there are performance penalties
* shyouhei: seems half the speed...
* shioimm: I looked at the cause of slowdown but it seems inevitable.
* akr: Thread creation is slow, could be the reason for it.
* akr: Did you try reducing threads
* shioimm: not yet.
* akr: I guess thread creation could be reduced a bit more.  When an address is resolved thread termination can be notified via IO
* mame: Not 100% sure if the slowdown is really due to thread creation
* shioimm: thread creation is slow.  there could be other things.

(... reading profiler outputs ...)

* akr: it's hard to tell that the thread creation dominates the slowdown.
* mame: HEv2 does add complexity so zero slowdown is not our goal.  The problem is how much is it allowed to be slow.

----
* ko1: how is it tested?
* shioimm: unit tested with mocked DNS responses.
* mame: do the tests cover 100% lines?
* shioimm: no... there are too many branches.
* naruse: not a bad idea to know which part is covered.
* mame: possible? `assert_separately` does not work with coverage library it seems.

Conclusion:

* shioimm: I'll try using pipe to notify end-of-getaddrinfo for TCPSocket.new
* shioimm: Will add a comment about which situation is tested and which is not.
