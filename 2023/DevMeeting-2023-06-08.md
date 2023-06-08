---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-06-08

https://bugs.ruby-lang.org/issues/19599

## DateTime and location

* 2023/06/08 (Thu) 13:00-17:00 JST @ Online

## Next Date

* Next: 2023/07/13 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) `Range#step` semantics change for non-`Numeric` ranges (zverok)

* Implementation is provided, but no movement is seen in months. Can we agree (or provide the adjustments) on the [final spec](https://bugs.ruby-lang.org/issues/18368#note-17) and the implementation?

Discussion:

```ruby!
# current
(1..-1).step(1) { p _1 }            #=> no output
(1..-1).step(-1) { p _1 }           #=> step can't be negative
(1..-1).step(2.minutes) { p _1 }    #=> no output
(1..-1).step(-1.seconds) { p _1 }   #=> step can't be negative
(1..-1).step(1i) { p _1 }           #=> comparison of Complex with 0 failed

# after zverok's patch
(1..-1).step(1) { p _1 }            #=> no output
(1..-1).step(-1) { p _1 }           #=> 1 0 -1
(1..-1).step(2.minutes) { p _1 }    #=> no output
(1..-1).step(-1.seconds) { p _1 }   #=> no output <- NG
(1..-1).step(1i) { p _1 }           #=> no output

p (1..-1).step(-1.seconds) #=> #<Enumerator: 1..-1:step(-1 seconds)>

(1.seconds..-1.seconds).step(-1.seconds) { p _1 } #=> no output
```

```ruby
class Range
  def my_step(n)
    a, b = self.begin, self.end
    case n <=> 0
    when -1
      while a >= b # TODO: we need to care "exclude end"
        yield a
        a += n
      end
    when 0
      raise "step can't be 0"
    when 1
      while a <= b # TODO: we need to care "exclude end"
        yield a
        a += n
      end
    end
  end
end

(1..-1).step(-1) { p _1 } #=> 1, 0, -1
(1..2).step(-1) { p _1 }  #=> current: can't be negative, new: no output <- matz: the change accepted
```

* knu: I think making `(1..-1).step(-1.seconds) { p _1 }` not raising exception is a step backward if it silently ends not yielding anything.  The step value should be compared with zero using the `<=>` operator to see if it is consistent with the range direction (ascending/descending) and start a loop if it is.
* knu: I'm not absolutely sure if it's OK to introduce comparison with zero because it could close the door to allowing step over a non-Numeric range.

Conclusion:

* matz: I would like to confirm if the OP's expectation is the same as the avobe code of my_step
* matz: The proposed changes are all accepted, but
* matz: At least, `(1..-1).step(-1.seconds) { p _1 }` should display `1, 0 seconds, -1 seconds`
* matz: I think `Integer#coerce` should call `#to_int` if any. We need a separated ticket to discuss this change. I want to ask the OP to create that

### [[Bug #19681]](https://bugs.ruby-lang.org/issues/19681) The final classpath of partially named modules is sometimes inconsistent once permanently named (byroot)

- Please see the code snippet in the ticket description.
- The current implementation iterate over `RCLASS_CONST_TBL` and assign the first name it find.
- But that table is unordered, so if the module was aliased, the result can be surprising.

Preliminary discussion:

```ruby
m = Module.new

class m::C; end
p m::C.name # => "#<Module:0x000000010789fbe0>::C"

m::D = m::C
p m::D.name # => "#<Module:0x000000010789fbe0>::C"

M = m
p M::C.name # => "M::D" # actual (depending on the order of unordered hash)
p M::C.name # => "M::C" # expected
```

Discussion:

* samuel: Use ordered hash? Resolve in the order they were defined?

Conclusion:

* matz: I choose the option 2 (undefined behavior): https://bugs.ruby-lang.org/issues/19681#note-26
* samuel: Even if you choose option 2, can we implement option 1?
* matz: If it is worth doing that, it is possible, but I don't see the use case yet. I will ask it in the ticket

### [[Bug #11704]](https://bugs.ruby-lang.org/issues/11704) Refinements only get "used" once in loop (jeremyevans0)

* Is it expected that if a refinement is activated for a block, it remains activated if the block is re-entered?
* This is how CRuby works, but may be confusing to users expecting more dynamic behavior.
* This would seem difficult to change in CRuby, though JRuby (and maybe TruffleRuby) already operates the more dynamic way.

Preliminary discussion:

```ruby!
module C
  refine Array do
    def foo
      p :C
    end
  end
end

module D
  refine Array do
    def foo
      p :D
    end
  end
end

2.times do
  module X
    using C
    [].foo
  end
  module Y
    using D
    [].foo
  end
end
#=> :C, :D, :C, :D

2.times do
  tap do
    using C
    [].foo
  end
  tap do
    using D
    [].foo
  end
end
#=> expected: :C, :D, :C, :D
#=> actual:   :C, :D, :D, :D

[].foo #=> :D
```

Discussion:

* It is expected behaviour that using in a block affects the outer file or module scope. (shugo)

Conclusion:

* matz: rejected


### [[Bug #18622]](https://bugs.ruby-lang.org/issues/18622) `const_get` still looks in `Object`, while lexical constant lookup no longer does (jeremyevans0)

* Should we change `Module#const_get` to raise `NameError` instead of looking into `Object`, for modules and subclasses of `Object`?
* If so, should we deprecate the behavior in Ruby 3.3 and change the behavior in Ruby 3.4?

Preliminary discussion:

```ruby
module A
  Foo = :foo
end

module B
end

p B.const_get("A::Foo") # => :foo

p B::A::Foo # => const_get.rb:9:in `<main>': uninitialized constant B::A (NameError)

module B
  p A::Foo #=> :foo
end
```

Discussion:

* mame: I think no use case is explained
* samuel: Isn't "`const_get` still looks in `Object`, while lexical constant lookup no longer does" the use case, i.e. making the `MyConstant` and `self.const_get(:MyConstant)` behave the same?
* mame: no one write `self.const_get(:MyConstant)` casually. And we confirmed some code fragments that depends on the current behavior. We need more explicit explanation if we change it
* samuel: Makes sense. I actually use `const_get` quite frequently and didn't realise the semantics were different.

Conclusion:

* matz: I will reply


### [[Bug #15428]](https://bugs.ruby-lang.org/issues/15428) Refactor `Proc#>>` and `#<<` (jeremyevans0)

* Last discussed at August 2021 developer meeting, no decision made.
* Calling `to_proc` if the passed object does not respond to `call` should be backwards compatible.
* Do we want to change the behavior of `Proc#>>` and `Proc#<<` to encourage their usage with symbols?
* Alternatively, should `Proc#>>` and `Proc#<<` raise error if called with argument not supporting `#call` (to avoid `NoMethodError` later when you call the resulting `Proc`)?

Discussion:

```ruby!
JSON.method(:parse) >> :symbolize_keys
#=> current: callable object is expected (TypeError)
#=> expected: same as: -> x { JSON.parse(x).symbolize_keys }
```

* mame: Why not write the proc plainly? `-> x { JSON.parse(x).symbolize_keys }` is much simpler
* samuel: I agree, I don't think we should encourage this style.

Conclusion:

* matz: My current opinion is "reject", but I will read the ticket later.


### [[Bug #19702]](https://bugs.ruby-lang.org/issues/19702) Promote racc as bundled gems (hsbt)

* Does anyone have objection this?

Discussion:

* no one was against the proposal

Conclusion:

* matz: go ahead


### [[Feature #19057]](https://bugs.ruby-lang.org/issues/19057) Hide implementation of `rb_io_t`. (ioquatix)

- So far the consensus is to hide the entire implementation (`struct rb_io` members) and fix dependencies.

Preliminary discussion:

* It is blocking us improving `IO#close` and will simplify other changes to `IO` implementation.
* Exposing the implementation details makes it tricky for TruffleRuby / JRuby.

* Is deprecation acceptable?
    * PR: https://github.com/ruby/ruby/pull/7916
* Can we remove completely before 3.3 is released?
* We already fixed `io-console`, `io-wait`, `pty`, `stringio`, etc.

Discussion:

* naruse: Please list up the affected gems. Unless you prove the impact is small, we need a migration period (can't remove in 3.3)

* Gem code search results: https://gist.github.com/ioquatix/c0422bde98def1f9878c096d0035a840

Conclusion:

* it seems okay to merge the deprecations.

### [[Bug #19717]](https://bugs.ruby-lang.org/issues/19717) `ConditionVariable#signal` is not fair when the wakeup is consistently spurious. (ioquatix)

- Can we discuss whether we should prevent spurious wakeup?
- No PR yet.. can we discuss best approach?

Preliminary discussion:

* samuel: Reproduction https://github.com/ioquatix/ruby-condition-variable-timeout

* samuel: alternative (joke) implementation of `wakeup_one` which also solves the problem:

```clang=
// thread_sync.c

static void
wakeup_one_random(struct ccan_list_head *head)
{
    struct sync_waiter *cur = 0, *next;
    size_t size = 0;

    ccan_list_for_each_safe(head, cur, next, node) {
        size++;
    }

    if (size == 0) return;

    size_t index = rand() % size;

    ccan_list_for_each_safe(head, cur, next, node) {
        if (index-- == 0) {
            ccan_list_del_init(&cur->node);

            if (cur->th->status != THREAD_KILLED) {
                if (cur->th->scheduler != Qnil && cur->fiber) {
                    rb_fiber_scheduler_unblock(cur->th->scheduler, cur->self, rb_fiberptr_self(cur->fiber));
                }
                else {
                    rb_threadptr_interrupt(cur->th);
                    cur->th->status = THREAD_RUNNABLE;
                }
            }

            return;
        }
    }
}

// ...

static VALUE
rb_condvar_signal(VALUE self)
{
    struct rb_condvar *cv = condvar_ptr(self);
    wakeup_one_random(&cv->waitq);
    return self;
}
```

```ruby
workers = 3.times do
    Thread.new do
        # (0) All workers try to get the resource. Worker 0 proceeds and 1, 2 are waiting.
        # (2) (worker-1) is woken up
        POOL.with_resource do # (3) Try to lock but can't.
            Logger.debug('Sleep with resource #1')
            sleep(0.001) # simulates a DB call
        end # (1) Worker 0 `ConditionVariable#signal`

        POOL.with_resource do # (1) Immediately re-acquire the mutex here.
            Logger.debug('Sleep with resource #2')
            sleep(0.001) # simulates a DB call
        end
    end
    
    sleep(0.001)
end
```

Discussion:

*

Conclusion:

* samuel: I'm going to confirm whether this issue is to do with Ruby's implementation of `wakeup_one` or whether the same issue occurs with pthread (C implementation).

### [[Bug #18818]](https://bugs.ruby-lang.org/issues/18818) Thread `waitq` does not retain referenced objects, can lead to use after free.

- Should we mark waitq and similar structures?
- PR: https://github.com/ruby/ruby/pull/5979

Preliminary discussion:

*

Discussion:

*

Conclusion:

* ko1: My preference is for the clean up code, because marking of `waitq` can be expensive.
* We hope that this issue can't affect dead threads.
* We know it can affect dead fibers, so they should be changed to clean up if possible.

### [[Feature #19520]](https://bugs.ruby-lang.org/issues/19520) Support for `Module.new(name)` and `Class.new(superclass, name)`.

- Any thoughts on moving forward with this proposal?
- PR: https://github.com/ruby/ruby/pull/7376

Preliminary discussion:

* What about marking it as experimental?

* Examples:
    * Rails: https://github.com/rails/rails/blob/55c3066da325703ff7a9524dbdc479b860db3970/activerecord/lib/active_record/relation/delegation.rb#L36-L39
    * Rails: https://github.com/rails/rails/blob/55c3066da325703ff7a9524dbdc479b860db3970/activemodel/lib/active_model/attribute_methods.rb#L535
    * Rails: https://github.com/rails/rails/blob/55c3066da325703ff7a9524dbdc479b860db3970/activerecord/lib/active_record/associations.rb#L2059https://github.com/rails/rails/blob/55c3066da325703ff7a9524dbdc479b860db3970/activerecord/lib/active_record/associations.rb#L2059
    * RSpec: https://github.com/rspec/rspec-core/blob/d722da4a175f0347e4be1ba16c0eb763de48f07c/lib/rspec/core/example_group.rb#L842-L848
    * Lots of other examples if you go looking for them.

* We can always make it a no-op later if it turns out to be a bad idea?

Discussion:

* My recommended proposal:

```ruby
Class.new(superclass, name)
Module.new(name)
```

* Alternative: https://bugs.ruby-lang.org/issues/19521

```ruby
c = Class.new do
  self.name = "fake name"
end

c = Class.new
c.name = "fake name"
```

* names could be randomly changed?
* Callbacks like `inherited` - between creating the class, invoking callbacks, and setting the name, strange temporary names can be created.

```ruby
c = Class.new
c.set_anonymous_name "Array"
```

Conclusion:

* matz: will reply to the issues, "19520" rejected, "19521" with improved naming.

### [[Feature #19712]](https://bugs.ruby-lang.org/issues/19712) `IO#reopen` removes singleton class (nobu)

* As well `#reopen` removes the singleton class, even when the logical class is the same. This can be surprising at times.

* An example:
  ```ruby=
  io = File.open(__FILE__)
  io.define_singleton_method(:foo) {}
  io.reopen(File.open(__FILE__))
  io.foo # NoMethodError
  ```

* While it's hard to trivially solve this with keeping the intended functionality, could it be considered to make edge cases more visible?

* Examples:
  - `IO#reopen` issues a warning message when the receiver's singleton class has been updated (but keep removing it as of current state)
  - `IO#reopen `is limited on instances with a default singleton class, and issues an error when called on an instance with an updated singleton class
  - make `IO#reopen` carry over the singleton class only if the recipient and argument's class are the same, and yield an error otherwise (different classes)

Preliminary discussion:

* samuel: This is the line of code responsible for copying the class (but not singleton class): https://github.com/ruby/ruby/blob/533368ccbd9b0abd1633f1d7ea4dc737d75f38e6/io.c#L8348
    * It's not commonly used: https://github.com/search?q=repo%3Aruby%2Fruby%20RBASIC_SET_CLASS&type=code
    * Should `reopen` should change the class?
* akr: How about adding a keyword argument to disable changing the class?  Such as: `io1.reopen(io2, retain_class:true)`

Discussion:

```ruby
f1 = File.open("a")
p f1.class #=> File
f2 = TCPSocket.open("localhost", 8080)
p f2.class #=> TCPSocket
f1.reopen(f2)
p f1.class #=> TCPSocket
```

- Assigning to `$stdout` could be better if that's the desired behaviour:

```ruby!
$stdout = "foo"
# $stdout must have write method, String given (TypeError)

class MyFakeOut
  def write(s)
    STDOUT.write(s)
  end
end
=> :write
irb(main):014:0> $stdout = MyFakeOut.new
# Accepted but doesn't work in irb.
```

- samuel: Changing the interface should never be possible IMHO.

```ruby
class BorkedIO < IO
  IO.instance_methods(false).each do |method|
    self.undef_method(method)
  end
end

$stdout.reopen(BorkedIO.new(1))
$stdout.write(...) # NoMethodError
# Does it make sense?
```

- samuel: It also doesn't work for singleton methods defined on the argument to `reopen`:

```ruby
f = File.open("foo.txt", "w+")
# #<File:foo.txt>
r, w = IO.pipe
# [#<IO:fd 10>, #<IO:fd 11>]
def f.bar
  self.write("bar")
end

w.reopen(f)
w.bar
# undefined method `bar' for #<File:foo.txt> (NoMethodError)
```

Conclusion:

* matz: prefer documentation update to describe reopen changes the class and can drop singleton.

### [[Feature #19718]](https://bugs.ruby-lang.org/issues/19718) Extend `-0` option (nobu)

* Perl's `-0` option is extended to accept a hexadecimal Unicode codepoint.
* `-0=separator`
* `-0:CODEPOINT,...`

Discussion:

* no one sees its use case

Conclusion:

* matz: rejected

### [[Feature #19719]](https://bugs.ruby-lang.org/issues/19719) Universal Parser

Discussion:

* https://github.com/ruby/ruby/compare/master...yui-knk:ruby:universal-parser-2

Conclusion:

* matz: accept the conditional compilation by UNIVERSAL_PARSER
* matz: also accept the direction described in https://bugs.ruby-lang.org/issues/19719

