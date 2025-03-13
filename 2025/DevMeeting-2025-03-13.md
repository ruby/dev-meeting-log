# DevMeeting-2025-03-13

https://bugs.ruby-lang.org/issues/21134

## DateTime and location

* 2025/03/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2025/04/15 (Tue) 16:00-19:00 JST @ Matsuyama

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #21089]](https://bugs.ruby-lang.org/issues/21089) Missing methods on enumerators created from Enumerator::product and Enumerator::Chain (jeremyevans0)

* Is this behavior expected?
* It seems odd that it doesn't work by default, but it does work if you call `to_enum`.
* Maybe we should have `to_enum` called implicitly so that it works, unless there are performance or other reasons not to?

Preliminary discussion:

```ruby
g = Enumerator.product([1, 2, 3], [4, 5, 6])
g.next #=> NoMethodError
```

Discussion:

* knu: It would be nice if the superclass method works also for subclasses.
* mame:  The deletion seems intentional because of its performance.
* matz: This would greatly reduce the performance when introduced, which is not something welcomed.

Conclusion:

* matz: We need a method which is not that slow.  Until someone tackles this problem, we want to resort to calling `to_enum` explicitly.
* knu: Let me take a look at it.

### [[Bug #21026]](https://bugs.ruby-lang.org/issues/21026) `def __FILE__.a; end` should be a syntax error (jeremyevans0)

* I'm not sure it is worth making this a syntax error.
* Do we want to make this change?

Preliminary discussion:

```ruby
def __FILE__.a # parsed
end

def (__FILE__).a
     ^~~~~~~~ cannot define singleton method for literals
end

def nil.a = nil; # OK
def __dir__.a = nil; # OK
```

Discussion:

* mame: `__FILE__` generates strings each time it is evaluated, which effectively deletes the method.
* akr:  Isn't it nice if we could also define methods to literals?
* matz: For instance two bignums can have different identity, but there is no way to say if a numeric literal is a bignum or not.
* shyouhei:  I don't see any benefit allowing this because no one wants to define methods to `__FILE__`
* akr: it is either we should allow both, or prohibit both.

Conclusion:

* matz: Let me think about it for a while.
* nobu: the error message seems strange though.


### [[Bug #21016]](https://bugs.ruby-lang.org/issues/21016) What should massign with `shareable_constant_value: experimental_everything` freeze? (jeremyevans0)

* What is the desired behavior in this case?

Preliminary discussion:

* ko1: How about constantly inserting `makeshareable` insn immediately before `setconstant`?
* nobu: It brings overhead
* ko1: don't care

Discussion:

* shyouhei: what is happening?
* ko1: we simply did not implement this feature for multiple assignments.
* shyouhei: nobody practically want to combine constant assignment with massign I guess
* ko1:  maybe.  but we can support this.

```ruby
A, b = "foo", "bar"
A, B = "foo", "bar"
```

Conclusion:

* ko1: let me implemt it.

### [[Bug #20968]](https://bugs.ruby-lang.org/issues/20968) `Array#fetch_values` unexpected method name in stack trace (jeremyevans0)

* I think this is expected behavior and not a bug.
* If we consider this a bug, how do we plan to address it as we move methods from C to Ruby?

Preliminary discussion:

- (this discussuion was from last dev meeting)

Discussion:

* matz: I still want to remove the useless information
* mame: How to fix. The chunk of `<internal:` backtraces should be merged to one backtrace entry and its location (filename:lineno) should be the one of the below
* mame: Not only an exception raised, but also Kernel#caller, caller_locations, etc. should be fixed
* ko1: TracePoint should not trap the events within the internal. It is practically problematic for debugger: for example, when stepping into `ary.map {}`, I expect to jump to the first line of the block, but currently, it jumpes to the first line of `Array#map`, which is not very useful

Conclusion:

* matz: My feeling is not changed yet
* mame: Let's try to implement the skipping algorithm. I will give it a try after RubyKaigi (if no one tries)

### [[Bug #21166]](https://bugs.ruby-lang.org/issues/21166) Fiber Scheduler is unable to be interrupted by `IO#close`. (ioquatix)

The fiber scheduler provides hooks for `io_read`, `io_write` and `io_wait` which are used by `IO#read`, `IO#write`, `IO#wait`, `IO#wait_readable` and `IO#wait_writable`, but those hooks are not interrupted when `IO#close` is invoked. That is because `rb_notify_fd_close` is not scheduler aware, and the fiber scheduler is unable to register itself into the "waiting file descriptor" list. We propose to fix this issue by:

1. Introducing `VALUE rb_thread_io_interruptible_operation(VALUE self, VALUE(*function)(VALUE), VALUE argument)` to `internal/thread.h` which allows us to execute a callback that may be interrupted. Internally, this registers the current execution context into the existing `waiting_fds` list before executing the callback, and removes it afterwards.
2. Update all the relevant fiber scheduler hooks to use `rb_thread_io_interruptible_operation`, e.g. `io_wait`, `io_read`, `io_write` and so on.
3. Introduce `VALUE rb_fiber_scheduler_fiber_interrupt(VALUE scheduler, VALUE fiber, VALUE exception)` which can be used to interrupt a fiber, e.g. with an `IOError` exception. The signature of the hook in Ruby is `Fiber::Scheduler#fiber_interrupt(fiber, exception)`.
4. Modify `rb_notify_fd_close` to correctly interrupt fibers using the new `rb_fiber_scheduler_fiber_interrupt` function.

Preliminary discussion:

* New public interface: `Fiber::Scheduler#fiber_interrupt(fiber, exception)` - is it acceptable?
    * All other implementation hidden.
* I would like to backport this to Ruby 3.4, 3.3 and 3.2 if possible.

* See <https://github.com/ruby/ruby/pull/12839> for the proposed implementation.

Discussion:

*

Conclusion:

*

----

### https://bugs.ruby-lang.org/issues/21141 `Time#utc?` does not work with a timezone object

* akr: it is by design. It returns true only when it is, say, "UTC mode", not necessarily when it is UTC.
* nobu: I will try to improve the document

### https://bugs.ruby-lang.org/issues/16993 Sets: from hash keys using `Hash#key_set`

* matz: I don't see the use case. I will ask it
* akr: I wonder if the proposed patch does not handle `compare_by_identity` well

### https://bugs.ruby-lang.org/issues/21143 Speficy order of execution `const_added` vs `inherited`

```ruby
class C
  def self.inherited(subclass)
    p subclass.name
  end
end

class D < C
end
```
* matz: I want to change the order: `inherited` should be called before `const_added`
* nobu: When `inherited` is called, the new subclass is not named yet. Is it okay?
* matz: Let's try.

### https://bugs.ruby-lang.org/issues/21155 File scoped namespace declarations as in C#
```
module Foo
  X = 1
  class Bar
    X
  end
end

class Foo::Bar
  X # cannot find Foo::X
end
```

```ruby
namespace module Foo

module = Foo
class = Bar < Baz

in module Foo
in class Bar < Baz

for module Foo
for class Bar < Baz

class Bar # defines Foo::Bar
  X # access Foo::X
end
```

```go
function (*f X).foo(s string) {
}
```

```ruby
module Foo
  X = 1
  class Bar < ZZZ
    class Baz < Qux
      include Quux
      X # This tries to find Baz::Z, Qux::X, Quux::X, Bar::X, Foo::X, ::X (not ZZZ::X)
      X # In this case, Foo::X is found
    end
  end
end

class Foo::Bar::Baz
  X # cannot access to Foo:X
end
```

```
class namespace Foo::Bar::Baz < ZZZ
# module Foo; class Bar; class Baz < ZZZ

# We want not to indent the whole code

# We want to access Foo::X simply by "X"
X

Y # Bar::Y

# end;end;end # implicitly
```

* mame: I like this proposal. The recent languages usually places function definition with no indentation
* matz: I don't like it. This is inherent to the object-oriented programming. I hope the indent problem will be fixed by the namespace proposal (currently being implemented by tagomoris)
* matz: I will close this

### https://bugs.ruby-lang.org/issues/21151 `IO` and `StringIO` raise `FrozenError` even for read-only methods

* ko1: There was a similar discussion about Thread::Queue. That was prohibited to freeze. I don't say the same should go to IO, though.
* mame: Is there a reasonable use case where we want to freeze IO?
* no one came up such a use case
* matz: I have no strong opinion, but I don't see any use case to freeze IO.
* nobu: Let's prohibit freezing IO
* knu: if user-defined deep_freeze is blindly freezing all instance variables including IOs, prohibiting IO#freeze will break it.
* ko1: Personally I want to prohibit such a program
* matz: I will write a reply. I want to ask its use case/reason first.

### https://bugs.ruby-lang.org/issues/20953 `Array#fetch_values` vs `#values_at` protocols

* mame: nobu please review it

### https://bugs.ruby-lang.org/issues/21160 Local return from proc

```ruby
def foo
  yield
end

def bar
  foo {
    return
  }
end
```

```ruby
def foo
  yield
rescue LocalJumpError
  1
end

x = foo { break 2 }
p x
```

```ruby
proc do
  return next 10
end.call #=> 10 (actual: unexpected void value expression)
```
* matz: I will reject

### https://bugs.ruby-lang.org/issues/21152 Enumerator's `#size` returned by `Range#reverse_each` raises an exception for endless Range

* akr: I think the new behavior (raising an exception) is correct because `reverse_each` for an endless range does not work (even start).  Infinity means infinite loop.
* matz: Agreed

### https://bugs.ruby-lang.org/issues/21042 Add and expose `Thread#memory_allocations` memory allocation counters

```ruby
Thread.current.memory_allocations
#=> {
#   total_allocated_objects: 1,
#   total_malloc_bytes: 111,
#   total_mallocs: 11,
# }

GC.stat_per_current_thread
GC.stat_for_thread(Thread.current)
```

* ko1: It brings a little overhead. Also, I am afraid if people want more performance indexes, which may bring bigger overhead. Also, people may want per-fiber indexes next. No end.
* matz: Is this implementation-defined?
* mame: Is `Thread.current.memory_allocations` a suitable API? Should it be under `GC`?
* matz: I am a bit positive. ko1, could you please reply to the ticket?
* ko1: ok

### https://bugs.ruby-lang.org/issues/21168 Prism doesn't require argument parentheses (in some cases) when a block is present but parse.y does
```
foo(
  x y, z do
  end
)

# currently Prism parses the above as
foo(
  x(y, z) do end
)

# possible another interpretation
foo(
  x(y),
  z do end
)
```

```
# parse.y accepts this
foo(x y, z)   # as foo(x(y, z))
foo(x do end)
# matz: I allowed the above two calls (only when one argument for "foo"), though it was a wrong decision. However, I think we cannot fix this from now

# parse.y rejects this
foo(w, x y, z)
```

Conclusion:

matz: I'll reply it. I like parse.y behavior. I don't want to allow the code any more

----

off-topic: the following is SyntaxError

```ruby
foo(
  1
  ,
  2
)
foo(1,2,)
foo(,1,2)

a = [
  , 2
  , 3
]

```

### https://bugs.ruby-lang.org/issues/21139 Prism and parse.y parses `it = it` differently
```ruby
42.tap do
  # prism
  it<local> = it<special>
  
  # parse.y
  it<local> = it<local>
end

def x = 1
x = x  # x = x() ?

def x(); end
def foo(x = x) # expected x=x(), but the principle was prioritized
end
```
* mame: I think that the principle that an identifier is interpreted as a local variable access after its assignment is strong. Should we break the principle for this theoretical case of "it"?
* matz: I changed my mind

----
off-topic:
* ko1: 861 assignments to `it` in all rubygems.
https://gist.github.com/ko1/d2c5719a5576487045676f051736d786

### https://bugs.ruby-lang.org/issues/19908 Update to Unicode 15.1

* naruse: I will review the PR

### https://bugs.ruby-lang.org/issues/21029 Prism behavior for `defined? (;x)` differs

* mame: Should `defined? (x;)` be "expression"?
* matz: Yes

### https://bugs.ruby-lang.org/issues/21154 Document or change `Module#autoload?`

```ruby
# main.rb
require 'foo'
M::Foo

# foo.rb
require 'm'
module M::Foo
end

# m.rb
module M
  autoload :Foo, 'foo'

  p constants            #=> [:Foo]
  p const_defined?(:Foo) #=> false
  p autoload?(:Foo)      #=> nil
end
```

```
$ ruby -e 'module M; autoload(:Foo, "xxx"); p constants; p const_defined?(:Foo); p autoload?(:Foo); end'
lv l[:Foo]
true
"xxx"
```

* mame: may I add a document that autoload cannot be used for a file that is currently being loaded
* no one against the addition
* akr: Can we make circular autoload raise an exception?
* akr: It brings an overhead, which might not be acceptable

---

### https://bugs.ruby-lang.org/issues/19841 Marshal.dump stack overflow with recursive Time

