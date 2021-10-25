---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20211021Japan

https://bugs.ruby-lang.org/issues/18174

## DateTime and location

* 10/21 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 11/18 (Thu) 13:00-17:00 JST @ Online
  * pre: 11/16 (Tue)

## Announce

## About release timeframe

* preview 1 will be released in the next week (Monday~)
* naruse: Let me know if you want me to wait for something.
  * Otherwise, I'll release it at arbitrary time
* samuel: any plan for 3.0.3?
  * mame: I have, but the date is not fixed
  * samuel: thanks :)

## Check security tickets

[secret]

## Tickets

### [[Feature #18176]](https://bugs.ruby-lang.org/issues/18176) Make Coverage suspendable (mame)

* I'd like to add `Coverage.suspend` and `Coverage.resume`. Any opinion?

Preliminary discussion:


Discussion:

* mame: Had no use case of it before but encountered one.
* nobu: Specifically?
* mame: I want to know coverage of a specific rails endpoint.  That way I can split what is used into a module.
* samuel: how to handle threads? If you only care about one block of code, won't you get every other thread executing at the same time? i.e. should it be process, thread local or fiber local state?
* mame: Wondering that too :)
* shyouhei: Do coverages of multiple threads mix when in multithreaded?
* mame: Yes.
* samuel: It should be used in one-process-per-request model.
* samuel: APM systems can take advantage of this feature.  They might want to sample a specific endpoint.
* mame: Is it possible to implement per-thread coverage?
* ko1: mmm... 
* aaron: I think it would slow down all threads, even if they don’t care about coverage?
* mame: I guess such thing can slow down other threads that are not measured.
* samuel: You can implement coverage using `TracePoint` but it is slow: <https://github.com/ioquatix/covered>.
    * It can filter coverage by file/pattern/etc.
    * It can measure coverage of HTML templates (ERB, Slim, etc).
    * Maybe provide some inspiration?
    * mame: Yeah, it is inspiring
* usa: I agree with the feature. The concern about multi-threads is valid, but let's consider it in future.

``` ruby
Coverage.for(thread: Thread.current / fiber: Fiber.current) do
  # ...
end => coverage_data

# Example Rack middleware?
class CoverageMiddleware
  def call(env)
    if measure_coverage?(env)
      Coverage.for do
        @app.call(env) # fiber switch will suspend coverage?
      end
    else
      @app.call(env)
    end
  end
end
```

Conclusion:

* mame: I'll merge it. I'll add a comment about multi-thread issue. Later I'll consider with ko1 whether the per-thread coverage is feasible

### [[Bug #18170]](https://bugs.ruby-lang.org/issues/18170) `Exception#inspect` should not include newlines (mame)

* I've created a PR for `StandardError.new("foo\nbar") #=> #<StandardError: "foo\nbar">`. I had to modify some tests. Is it acceptable?

Preliminary discussion:

```ruby
p StandardError.new("foo\nbar")
#=>
# #<StandardError: foo
# bar>
```

```
#<StandardError: "foo\nbar"> # samuel's preference
#<StandardError: foo\nbar>   # eregon's preference
```

`#<StandardError: "foo\nbar">`
Pros: simple and consistent
Cons: incompatible even when it has no newline character
  * samuel: what is it incompatible with?
  * mame: There are some tests that checks the result of Exception#inspect

`#<StandardError: foo\nbar>`
Pros: compatible in many cases
Cons: ad-hoc

Discussion:

* samuel: exceptions may not be formatted correctly by other tools, e.g.:

``` code
<StandardError: "foo\nbar"> # the syntax highlighting still "works".
<StandardError: foo\nbar>
```

* samuel: consistent with other Ruby formatting, e.g.:

``` ruby
FakeError = Struct.new(:message)
p FakeError.new("Hello\nWorld")
#<struct FakeError message="Hello\nWorld">


# This is strange output.
p FakeError.new(StandardError.new("Hello\nWorld"))
#<struct FakeError message=#<StandardError: Hello
World>>
```

* akr: Escape them only when necessary.

```ruby
p StandardError.new("foo bar") #=> #<StandardError: foo bar>
p StandardError.new("foo\nbar") #=> #<StandardError: "foo\nbar">
p StandardError.new("foo\"bar") #=> #<StandardError: "foo\"bar">
p StandardError.new("foo\\bar") #=> #<StandardError: "foo\\bar">
```

* matz: `Symbol#inspect` also both does and doesn't quote depending its contents.
* mame: How do we implement?
* samuel: no one can reliably parse the current output anyway: `StandardError.new("Hello>\n#<StandardError: World")`

``` ruby
StandardError.new("Hello>\n#<StandardError: World")
=> 
#<StandardError: Hello>
#<StandardError: World>
```

* akr: it should use inspect only when (1) it has newlines or (2) it starts with a double quote character?
* mame, usa: very ad-hoc
* akr: Okay, so how about using inspect only when it has newlines?
* akr: `StandardError.new("foo\nbar").inspect` and `StandardError.new('"foo\nbar"').inspect` are not distinguishable, but I think it is practically okay
* samuel: what problem are we trying to solve with this complexity?

```ruby
p StandardError.new("foo bar") #=> #<StandardError: foo bar>
p StandardError.new("foo\nbar")   #=> #<StandardError: "foo\nbar">
p StandardError.new('"foo\nbar"') #=> #<StandardError: "foo\nbar"> (not distinguishtable)
p StandardError.new("foo\"bar") #=> #<StandardError: foo"bar>
p StandardError.new("foo\\bar") #=> #<StandardError: foo\bar>
p StandardError.new("Hello>\n#<StandardError: World") #=> #<StandardError: "Hello>\n#<StandardError: World">
StandardError.new("\"\\n\"").inspect == StandardError.new("\n").inspect #=> #<StandardError: "\n"> (indistinguishable)
```

Logger output:

```
> Logger.new($stderr).info StandardError.new("Hello>\n#<StandardError: World")
I, [2021-10-21T18:00:09.901112 #26013]  INFO -- : Hello>
#<StandardError: World (StandardError)
```

* nobu: How about adding "quoted" indicator?
```
StandardError.new("foo\nbar") #=> #<StandardError:: "foo\nbar">
```

* akr: How about remove the space when inspect is used?

```
StandardError.new("foo\nbar")   #=> #<StandardError:"foo\nbar">
StandardError.new('"foo\nbar"') #=> #<StandardError: "foo\nbar">
```

Conclusion:

* matz: Escape only when the message includes a newline.
* samuel: great :)
* matz: I leave it to mame whether "remove a space" hack is introduced or not
* mame: I'll give it a try to implement

### [[Feature #18035]](https://bugs.ruby-lang.org/issues/18035) Introduce general model/semantic for immutable by default. (ioquatix)

- Can we agree on interface and implementation?
- `Process::Status` is example use-case. Should be many more, (e.g. `Range`, ...?).

Advantages (from Eregon):

- No need to worry about .allocate-d but not #initialize-d objects => not need to check in every method if the object is #initialize-d
- Internal state/fields can be truly final/const.
- Simpler and faster 1-step allocation since there is no dynamic call to #initialize (instead of .new calls alloc_func and #initialize)
- Known immutable by construction, no need for extra checks, no need to iterate instance variables since no instance variables
- Potentially lower footprint due to no instance variables
- Can be shared between Ractors freely and with no cost
- Can be shared between different Ruby execution contexts in the same process and even in persisted JIT'd code
- Easier to reason about both for implementers and users since there is no state
- Can be freely cached as it will never change

General idea/interface:

``` ruby
module Immutable
  def new(...)
    Ractor.make_shareable(super.freeze)
  end
end       

class MyClass
  extend Immutable
end  
```

We already have an idea for immutability in Ruby.

``` ruby
5.dup # 5

# Dup of immutable objects returns the same object:
:foo.equal?(:foo.dup)

# Clone of immutable objects returns the same object:
:foo.equal?(:foo.clone)

# Clone of immutable object cannot remove immutable status:
:foo.clone(freeze: false)
# <internal:kernel>:48:in `clone': can't unfreeze Symbol (ArgumentError)
```

Existing model is implmented in `object.c`, see:
- [`special_object_p`](https://github.com/ruby/ruby/blob/master/object.c#L314-L332). It is used to check whether an object is "immutable" or not.
- [`rb_obj_clone2`](https://github.com/ruby/ruby/blob/master/object.c#L349-L356) uses `special_object_p` to use mutable or immutable clone implementation. Same with dup.

What we need to decide:

1. Do we want to introduce a model for immutability? yes/no
2. Are we happy with proposed interface? yes/no/suggestions/changes
3. Do we need to create a new flag or can we reuse `Ractor.make_shareable` / `RB_FL_SHAREABLE`.
    - Reuse existing flags: `RB_FL_FREEZE` is applied recursively by `rb_ractor_make_shareable`. so one very natural option is `RB_FL_FREEZE & RB_FL_SHARABLE` implies immutable object.
        - Advantage: compatible with Ractor, but may require ko1 to consider behaviour of `rb_ractor_shareable_p` behaviour.
    - Introduce new flag: `RB_FL_IMMUTABLE`. This would naturally imply `RB_FL_FREEZE` and `RB_FL_SHAREABLE` too.
        - Advantage: minimum changes to existing semantics by introducing indepdent semantics.
        - Disadvantage: less compatible with Ractor/existing flags/frozen state, more confusing internal state/flags.
4. Are we happy with current semantics for immutability and `Object#dup` & `Object#clone`? yes/no/suggestions
    - Consider that unfreezing the top level object would often create a broken object since internal state would still be frozen.

Preliminary discussion:

* mame: I can't understand what is proposed
* ko1: what is the proposal? There are many discussion and it's not clear the proposal for me.

Discussion:

* samuel: The proposal is to "unify" the situation about ruby's immutabiliy.
* samuel: `freeze` and `make_sharaeble` have different semantics.  Why not add a semantics of immutability.  That can benefit JIT and other optinisation opportinities.
    * Proposal part 1 is interface (`Immutable` module).
    * Part 2 is its actual implementation (`FL_IMMUTABLE` flag).
* samuel: 4 things we have to decide (see above).
* matz: `MyClass` is immutable.  Does this mean the class is recursively frozen? By `make_sharable`?
* samuel: Correct.  -- Or by simiar implementation.
* samuel: Do you feel ruby, as a language, need immutablity?
* matz: I'm still wondering.  It has benefits.  OTOH We see some immutable strigs in some other languaes that are less usable.  In ruby objects are basically mutable.  Not sure how much useful is it to have immutable ones.
* samuel: People tend to confuse `freeze` because it is shallow.  There are many constnats (esp. in Rails) that need not be mutable.
* samuel: I see there are advantages of immtables not as internal optimisation but user-level concepts
* matz: Do you have a half-real world example to show that?
* samuel: Sure!
* matz: I was thinking of new syntax that generate frozen-hash literals etc.  I can understand they are useful.  But not sure about this user-defined immutable classes.  Show me some example.

----

* mame: My understanding of this proposal is:
  * immutable/deep_frozen/ractor-shareable is a good concept that has many advantages.
  * Ruby should recommend this style as the language.
  * So let's introduce module `Immutable`" to encourage people to follow the concept
* Is my understanding correct?
* knu: Should Immutable#freeze freeze all instance variables by default?
* samuel: unfortunately we cannot guarantee it:

``` ruby
class MyObject
  extend Immutable
  
  def freeze
    # oops
  end
end
```

Conclusion:

* matz: Basically neurtal right now.  Neither for nor against the idea yet.  To make decision, show me more info (example etc).
    *   matz: Additional complexity of immutability could be disadvantage.
* samuel: Okay we will give examples of them on issue tracker.


### [[Feature #18083]](https://bugs.ruby-lang.org/issues/18083) Capture error in ensure block. (ioquatix)

- Can we deprecate `$!` (& `$@`)? It's dangerous and every use case is better with explicit capture. Also may have some performance advantages.

Preliminary discussion:

* mame: I have read only the comment above. TBH deprecating `$!` is very unlikely to be considered
* ko1: can we change the semantices of `$!` to begin/rescue/ensure local? does it solve the issue?
* samuel: I'm not sure that it does unless you mean lexical scope.

issue example:

```ruby=
def foo
  p $!
end

def bar
  begin
    # no raise
    THE_CODE
  ensure
    p $!
    if $!
      # bar programmer may expect
      # THE_CODE raises an exception,
      # but not raised
    end
  end
end

begin
  raise
ensure
  foo #=> RuntimeError
  bar #=> RuntimeError
end
```

```ruby=
# a case showing that method-local $! is not enough
begin
  THE_CODE_1 # raises Foo
ensure
  begin
    THE_CODE_2 # raises nothing
  ensure
    p $! # Foo, but nil is expected
  end
end
```

```ruby=
# Correct bar method using $!

def bar
  prev_exception = $!
  begin
    # no raise
    THE_CODE
  ensure
    unless prev_exception.equal($!)
      # ...
    end
  end
end

# OR without using $! at all:

def bar
  begin
    # no raise
    THE_CODE
  rescue => error
    raise
  ensure
    if error
      # ...
    end
  end
end
```

Discussion:

* samuel: `$!` is thread local, travels across method boundary. 
* samuel: does it also travel across fiber boundary?
* matz: maybe `$!` should be local?
* ko1: nested `ensure` can berek that.
* samuel: yes, that's correct. Any usage of $! is (always|potentially?) a problem!
* matz: Should we discourage use of `$!` in an ensure clause?
* matz: It is basically a bad idea to use `$!` in an ensure clause. An ensure clause should do a task that is not depending on whether an exception is raised or not
    * nobu: If not the exception is StandardError, how do we do?
    * usa: write `rescue Exception => ex`
    * usa: "do `rescue`" means that we should re-raise the exception in rescue block in such case...
* samuel: there is also some potential performance advantage - no need to construct backtrace in `$@`.
* eregon: "And it would avoid escaping the Exception instance to the thread, which mean it can be escaped analyzed. Also, if the rescue doesn't use `=>` or `$!`, there is no need to generate a backtrace which is a huge performance gain!" (if `$!` and `$@` is lexically scoped).
* knu: `at_exit` and `END` need to reference `$!`, and that is one reason we cannot immediately deprecate the use of `$!`.  Probably we can pass the exception object to their block.
* samuel: there should be a better way to achieve this, e.g.:

``` ruby
at_exit do |error|

end
```

knu: That's what I thought.

Conclusion:

* matz: Understood the problem. But it's kinda hard to fix this by breaking things.
* matz: Adding warning towards the error-prone `$!` usages is okay, not sure how difficult is it though.
* samuel: the only safe usage is at the top level of the ensure block, but if `$!` is set before entering `begin` then it's always unsafe.


### [[Feature #18020]](https://bugs.ruby-lang.org/issues/18020) Introduce `IO::Buffer` for fiber scheduler. (ioquatix)

- For efficient file IO we need an internal buffer with well defined semantics suitable for IO.

Preliminary discussion:

* mame: I cannot understand what is finally needed. Doesn't String with ASCII-8BIT work?
    * samuel: String is very hard to use correctly for high performance IO, too many edge cases relating to encoding, memory allocation, etc. Using a String for a zero copy IO buffer in the fiber scheduler is practically impossible.
* ko1: I can't understand how to use it with IO? Only for scheduler?
    * samuel: Initial implementation is primarily for scheduler, between IO's internal buffers and scheduler interface. We cannot use String here because we cannot create String from fixed C `(void *, size_t)` buffer which is exposed to Ruby interface (fake string).

Discussion:

* The motivation:
    * String is unuseful (for some reasons) for Fiber scheduler's io hooks for a zero copy IO buffer implementation
* The proposal: Introduce IO::Buffer, String-like object that has no encoding, etc.
* samual: It's almost doable using string but that must expose fake strings into ruby level.
* ko1: Tell me how to use it?
* samuel: sure:

```c=

rb_write_internal(rb_io_t *fptr, const void *buf, size_t count)
{
  ...
  VALUE result = rb_fiber_scheduler_io_write_memory(scheduler, fptr->self, buf, count, count);
}

VALUE
rb_fiber_scheduler_io_write_memory(VALUE scheduler, VALUE io, const void *base, size_t size, size_t length)
{
    VALUE buffer = rb_io_buffer_new((void*)base, size, RB_IO_BUFFER_LOCKED|RB_IO_BUFFER_IMMUTABLE);

    VALUE result = rb_fiber_scheduler_io_write(scheduler, io, buffer, length);

    rb_io_buffer_free(buffer);

    return result;
}
```

```ruby
   # Write from the given buffer into the specified IO.
   # @parameter io [IO] The io to write to.
   # @parameter buffer [IO::Buffer] The buffer to write from.
   # @parameter length [Integer] The minimum amount to write.
    def io_write(fiber, io, buffer, length)
        # In the best case, buffer points directly at the source string or buffer.
        offset = 0

        while length > 0
            # From offset until the end:
            chunk = buffer.to_str(offset, length)
            case result = io.write_nonblock(chunk, exception: false)
            when :wait_readable
                self.io_wait(fiber, io, READABLE)
            when :wait_writable
                self.io_wait(fiber, io, WRITABLE)
            else
                offset += result
                length -= result
            end
        end

        return offset
    end
```

Here is zero-copy implementation: https://github.com/socketry/event/blob/master/ext/event/selector/uring.c#L409-L457

* samuel: Basically it is impossoble to guarantee that a string object does not CoW.  We wan to make sure that a buffer is zero-copy but a string can be shared later, hurts zero-copy property or breaking entirely.
* samuel: Ruby String interface is horrible for general IO buffering.

----
* mame: if IO::Buffer object is leaked (for example by ObjectSpace.each_object), it may bring SEGV?
* samuel: every access to IO::Buffer is validated, so it can never cause problems. Once the buffer goes out of scope, the buffer becomes invalid. Even if you still have a refernce to it, it won't give you access to anything (raises an exception).
* mame: understood, thank you!
* samuel: you are most welcome and it's a good question.

----
* mame: it resembles Fiddle::Pointer https://ruby-doc.org/stdlib-2.5.3/libdoc/fiddle/rdoc/Fiddle/Pointer.html
* samuel: `IO::Buffer` is much more specific to high performance zero-copy IO. Also, we can't use anything which isn't part of the core Ruby interface.
* ko1: I think it looks possible to implement it with String, but maybe need some extra APIs

----
* akr: what if the write was partial?
* samuel: that is already considered:
```c=
VALUE Event_Selector_URing_io_write(VALUE self, VALUE fiber, VALUE io, VALUE buffer, VALUE _length) {
	struct Event_Selector_URing *data = NULL;
	TypedData_Get_Struct(self, struct Event_Selector_URing, &Event_Selector_URing_Type, data);
	
	int descriptor = Event_Selector_io_descriptor(io);
	
	const void *base;
	size_t size;
	rb_io_buffer_get_immutable(buffer, &base, &size);
	
	size_t offset = 0;
	size_t length = NUM2SIZET(_length);
	
	if (length > size) {
		rb_raise(rb_eRuntimeError, "Length exceeds size of buffer!");
	}
	
	while (length > 0) {
		int result = io_write(data, fiber, descriptor, (char*)base+offset, length);
		
		if (result >= 0) {
			offset += result;
			if ((size_t)result >= length) break;
			length -= result;
		} else if (-result == EAGAIN || -result == EWOULDBLOCK) {
			Event_Selector_URing_io_wait(self, fiber, io, RB_INT2NUM(EVENT_WRITABLE));
		} else {
			rb_syserr_fail(-result, strerror(-result));
		}
	}
	
	return SIZET2NUM(offset);
}
```

* samuel: Yes for each operation we allocate one `IO::Buffer` but we could pool these or reuse them. However, Ruby IO does many more allocations per read/write so it's really not a big deal in my tests.

* samuel: After the method is done, the buffer can be invalidated to prevent invalid usage. Every operation is checked: `rb_io_buffer_get_immutable(buffer, &base, &size)` performs validation that the underlying buffer is still valid and raises an exception if not.

* samuel: performance is good - measured in benchmarks.

* samuel: conceptual simplicity e.g. `NSString` vs `NSData`.

* samuel: invalidation is done here: <https://github.com/ruby/ruby/pull/4621/files#diff-29a83910395dea702ac0c02b4cd9976ba252ff50f133d7cd8109beffbd2eab1dR259> it's basically setting the internal pointer to `NULL`.

Conclusion:

* matz: positive.
* akr: I understand it works.  Not as light-weight as pointer operation but that's inevitable.
* matz: I'm basically positive but wait for the discussion between akr and samuel. Let me know when it is concluded

### [[Feature #17369]](https://bugs.ruby-lang.org/issues/17369) Introduce non-blocking `Process.wait`, `Kernel.system` and related methods. (ioquatix)

- Currently, `$?` is thread-local, but it should be fiber local.
- Should we try to fix `$?` or deprecate it?
- Introduce new interface? `system(...) {|status| ...}`?

Preliminary discussion:

* mame: Making `$?` to fiber-local looks reasonble though I am very afraid about compatibility
    * samuel: it's fair point.

Discussion:

* ko1: The pull request is merged already.  Proposal is not directly about that.
* shyouhei: I guess libraries shall not rely on `$?` but use `spawn` instead to have explicitly assign a status object to a local variable.
* mame: I think `$?` is still used in libraries, but I guess the incompatibility is acceptable

Conclusion:

* matz: Looks a good idea. Will reply.

### [[Bug #17429]](https://bugs.ruby-lang.org/issues/17429) Prohibit include/prepend in refinement modules (shugo)

* Is the name `Refinement#import` OK?

Preliminary discussion:

* nobu: As I recall, fiddle defines `Module#import`
* znz: JRuby provides `import`
* ko1: It should be careful to introduce `import` if Matz want to use this naming.

Discussion:

* mame: Did you talk in person?
* matz: Not yet.
* matz: I want to reserve "import" terminology for future package system.
* nobu: my comment about fiddle was fake, please ignore

(waiting for shugo to appear...)

* shugo: Decision made?
* matz: not yet.
* mame: There are other use cases of "import" in the wild.
* shugo: This proposal is limited to Refinements.
* matz: ... but different things have the identical name is a bad idea in general.
* ko1: people might want this feature out of refinements, too.
* matz: `mix`?
* mame: `mix` might have a nuance that it could affect both sides.
* ko1: `import_methods` is another descriptive idea.
* matz: `copy_methods`
* knu: copy with only one argument might sound weird.
* shugo: `copy_methods_from`
* tenderlove: +1 for `import_methods`
* matz: okay

Conclusion:

* matz: `import_methods` accepted. 


### [[Feature #18239]](https://bugs.ruby-lang.org/issues/18239) Variable Width Allocation: Strings (duerst)

- Discuss deployment schedule and binary incompatibility issues.

Preliminary discussion:

* ko1: Now I can not see the clear benefit (and no-disadvantage).

Discussion:

* tenderlove: I guess Peter wants to merge this becasue he want to merge the size class concept.
* matz: Basically for this idea but can you show us the number?
* tenderlove: Yeah Peter put benchmark results in the ticket above.
* matz: Am looking at it.
* ko1: This is to make development faster?
* tenderlove: and to reduce complexity.
* ko1: Maybe current implementation doesnt show "clear" improvements.  I cannot say just "yes" yet.  I'm sure this approach will theoretically improve things in future.  Not yet proven.
* samuel: What is the tradeoff of it?
* ko1: We need to maintain two different data structures and switch between them.
* tenderlove: and we need to do the same thing every time we introduce this to a class.
* tenderlove: I really want feedbacks from the community.  We've put it in our production and see no problems so far.
* samuel: My concern is the performance/complexity trade off. At worst we should make Ruby faster with a proportional increase in complexity, and at best it should be simpler.
* shyouhei: I guess nobody in the community is even aware of it.  Let's have this merged and have a feedback?
* mame: Should this be enabled by default or not?
* ko1: Is the target 3.1, right?
* tenderlove: yes
* ko1: I'm not against merging it and enabling it on 3.2. If we want to encourage it, I'm not totally against introducing it now (3.1).
* samuel: I also agree maybe aim for 3.2?

Conclusion:

* matz: merge it, with default off for 3.1; flip the default in 3.2.


### [[Feature #18242]](https://bugs.ruby-lang.org/issues/18242) Allow multiple assignment in logical expression (nobu)

* `1 < 2 and a, b = 2, 1` as `(1 < 2) and (a, b = 2, 1)` and so on.

Preliminary discussion:

* nobu: turned out it is possible.
* ko1: this proposal makes "SyntaxError" to "Works".

from ticket:
```ruby
a, b = 2, 1     if 1 < 2     # Works
a, b = [2, 1]   if 1 < 2     # Works
(a, b) = 2, 1   if 1 < 2     # Works
(a, b) = [2, 1] if 1 < 2     # Works
(a, b = [2, 1]) if 1 < 2     # Works
a, b = 2, 1     unless 2 < 1 # Works
a, b = [2, 1]   unless 2 < 1 # Works
(a, b) = 2, 1   unless 2 < 1 # Works
(a, b) = [2, 1] unless 2 < 1 # Works
(a, b = [2, 1]) unless 2 < 1 # Works
1 < 2   and a, b = 2, 1      # SyntaxError
1 < 2   and a, b = [2, 1]    # SyntaxError
1 < 2   and (a, b) = 2, 1    # SyntaxError
1 < 2   and (a, b) = [2, 1]  # SyntaxError
(1 < 2) and a, b = 2, 1      # SyntaxError
(1 < 2) and a, b = [2, 1]    # SyntaxError
(1 < 2) and (a, b) = 2, 1    # SyntaxError
(1 < 2) and (a, b) = [2, 1]  # SyntaxError
1 < 2   and (a, b = 2, 1)    # Works
1 < 2   and (a, b = [2, 1])  # Works
2 < 1   or a, b = 2, 1       # SyntaxError
2 < 1   or a, b = [2, 1]     # SyntaxError
2 < 1   or (a, b) = 2, 1     # SyntaxError
2 < 1   or (a, b) = [2, 1]   # SyntaxError
(2 < 1) or a, b = 2, 1       # SyntaxError
(2 < 1) or a, b = [2, 1]     # SyntaxError
(2 < 1) or (a, b) = 2, 1     # SyntaxError
(2 < 1) or (a, b) = [2, 1]   # SyntaxError
2 < 1   or (a, b = 2, 1)     # Works
2 < 1   or (a, b = [2, 1])   # Works
```

Discussion:

* mame: It complicates the parser, which bothers me.

Conclusion:

* matz: Why not?
* matz: Wait, looking at the patch...
* matz: I now feel this edge case is not worth the complexity.
* matz: Let me have time to consider.


### [[Misc #18248]](https://bugs.ruby-lang.org/issues/18248) Add Feature Triaging Guide (jeremyevans0)

* Is it OK to add a feature triaging guide and start using it triage feature requests?

Preliminary discussion:

*

Discussion:

* mame: This is basically what we already do.  Just made things explicit.
* shyouhei: I see this is gentle.
* mame: There is no auto-close.  Just "devs can close stale issues when..." document.

Conclusion:

* matz: let me read the draft later.


### [[Bug #18066]](https://bugs.ruby-lang.org/issues/18066) Load did_you_mean/error_highlight even with --disable-gems (jeremyevans0)

* Should --disable-gems also disable did_you_mean and error_highlight?
* Most developers appear in favor, though @mame is against (but not strongly opposed).

Preliminary discussion:

https://github.com/ruby/ruby/pull/4729

> The option --disable-gems is just for debugging (as ruby --help says). When we use --disable-gems, we want to measure the performance of the bare core interpreter.

* ko1: Now `did_you_mean` and `error_highlight` is not related to rubygems (because default gems) so I think it is natural to be independent.

Discussion:

* mame: I'm against it.  `--disable-gems` is used for debugging the ruby interpreter with minimal-overhead.  This proposal goes against it.
* nobu: This is technically possible, but I see no merits.
* mame: I don't want users to use `--disable-gems`.  This should be restricted for debugging purpose.

Conclusion:

* matz: Keep it as it is now.
* mame: Note also that `--disable-gems -rdid_you_mean` is possible.


### [[Bug #18187]](https://bugs.ruby-lang.org/issues/18187) `Float#clamp()` returns `ArgumentError` (comparison of Float with 1 failed) (jeremyevans0)

* How should `Float#clamp` handle case where receiver is NaN?
* Currently it raises `ArgumentError`, but that is due to implementation details, not by design.
* Is it OK to change it to return the receiver (NaN) in this case?

Preliminary discussion:

```ruby=
# current
Float::NAN.clamp(0, 100) #=> comparison of Float with 0 failed (ArgumentError)

# expected
Float::NAN.clamp(0, 100) #=> Float::NAN
```

* mrkn: I think it's OK to return NaN for all the cases of Float::NAN.clamp. (copied from a ticket)

Discussion:

* mrkn: It's not NaN but comparison of it to 0?
* nobu: It seems it's NaN that is problematic.
* usa: This introduces other discussions whether NaN should behave differently than the current one.
* nobu: Returning NaN here does not solve the ultimate problem; it just deays the error.  Early failure should be encouraged instead.
* usa: The reason why Ruby has NAN is mainly for interoperability with other programs and data

Conclusion:

* matz: Keep it as it is now


### [[Bug #12436]](https://bugs.ruby-lang.org/issues/12436) newline argument of `File.open` seems not respected on Windows (jeremyevans0)

* The option is currently ignored due to an oversight when the code was originally added.
* Is it OK to introduce `lf_newline` transcoder and `newline: :lf` option to fix this?
* Also, is `newline: :crlf` supposed to convert `\r\n` to `\n` during read only on Windows?

Preliminary discussion:

* nobu: will take a look

Discussion:

* usa: text mode in windows is handled by the MSVCRT.  Because of speed.  One way is to detect `newline: :lf` etc., and fallback to our own code.
* mame: If the newline is explicitly stated we shall honour that.
* matz: Does text mode in windows only change newlines?
* usa: yes.
* matz: What is the problem doing it ourself?
* usa: It's super-slow.  Order of magnitude slower than the status quo.
* nobu: We want to avoid MSVCRT if possible but the reality is...
* ko1: Nobody practically uses `newline: :lf`.

Conclusion:

* matz: accepted, nobu please review the patch


### [[Feature #10917]](https://bugs.ruby-lang.org/issues/10917) Add `GC.stat[:total_time]` when GC profiling enabled (byroot)

- @ko1 did submit a pull request which seem fine except maybe for 32bits support.
- @ko1 did also measure the overhead and it appeared small.
- Could this feature be merged for Ruby 3.1?

Preliminary discussion:

* ko1: clock_gettime is not portable, so need to fix it for other environments
* ko1: Is the unit second? Millisecond? Nanosecond?
* ko1: should we introduce on/off method to avoid performance penalty? or not?

Discussion:

* ko1: Performance penalty is ~3%.
* (no one has any opinion about the performance penalty)
* ko1:
  * `GC.stat[:total_time]` #=> Integer (in ms) or Float (in sec.) or something?
  * `GC.stat[:total_time_ms]`
  * `GC.stat[:total_time_milliseconds]`
  * `GC.stat[:total_time_us]`
  * `GC.stat[:total_time_uanoseconds]`
  * `GC.stat[:total_time_ns]`
  * `GC.stat[:total_time_nanoseconds]`
  * `GC.stat[:time]` #=> Integer (in ms)  (JRuby and TruffleRuby use this)
* usa: at least, should have `GC.stat[:time]` in ms for compatibility with JRuby and TruffleRuby.  To implement another units is another issue.
* ko1: I'll throw a dice to decide we need enable flag or don't need.

Conclusion:

* matz: ok

### [[Feature #17760]](https://bugs.ruby-lang.org/issues/17760) Where we should install a header file when `gem install --user`? (byroot)

- `digest` have been gemified. However its `extconf.rb` have `$INSTALLFILES = { "digest.h" => "$(HDRDIR)" }` which fail for most people because ruby's `include` directory is owned by root.
- What should rubygems do here? Have a secondary user owned `include` directory?
- If I have several versions of `digest` installed? How gems linking against it would know which version to use?
- If there's no clear solution, with 3.1.0 approaching, should we revert that `digest` gemification so that 3.1.0 is actually usable?

Preliminary discussion:

* ko1: `ruby/digest.h` seems used.
* ko1: How about to move digest.h to ruby's include library to avoid this issue?

```
$ gem-codesearch 'ruby/digest\.h'
/srv/gems/crcs-0.1.0/ext/digest/crc32/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-blake3-0.37.0.1/ext/digest/blake3/blake3_ruby.c:#include <ruby/digest.h>
/srv/gems/digest-blake3-0.37.0.1/ext/digest/blake3/extconf.rb:unless have_header("ruby.h") && have_header("ruby/digest.h")
/srv/gems/digest-blake3-0.37.0.1/ext/digest/blake3/extconf.rb:  raise "Can't find ruby.h & ruby/digest.h, try installing the ruby-dev headers"
/srv/gems/digest-kangarootwelve-0.3.2/ext/digest/kangarootwelve/ext.c:#include <ruby/digest.h>
/srv/gems/digest-keccak-0.0.5/ext/digest/extconf.rb:have_header! 'ruby/digest.h'
/srv/gems/digest-keccak-0.0.5/ext/digest/keccak.c:#include "ruby/digest.h"
/srv/gems/digest-sha3-1.1.0/ext/digest/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-sha3-1.1.0/ext/digest/sha3.c:#include "ruby/digest.h"
/srv/gems/digest-sha3-patched-1.1.1/ext/digest/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-sha3-patched-1.1.1/ext/digest/sha3.c:#include "ruby/digest.h"
/srv/gems/digest-sha3-patched-ruby-3-1.1.1/ext/digest/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-sha3-patched-ruby-3-1.1.1/ext/digest/sha3.c:#include "ruby/digest.h"
/srv/gems/digest-tiger-1.0.3/ext/digest/tiger/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-tiger-1.0.3/ext/digest/tiger/tiger_init.c:#include "ruby/digest.h"
/srv/gems/digest-whirlpool-1.0.3/ext/digest/whirlpool/extconf.rb:have_header('ruby/digest.h')
/srv/gems/digest-whirlpool-1.0.3/ext/digest/whirlpool/whirlpool.c:#include "ruby/digest.h"
/srv/gems/digest-xxhash-0.2.1/ext/digest/xxhash/ext.c:#include <ruby/digest.h>
/srv/gems/digest_extensions-0.1.0/ext/digest_extensions/digest_extensions.c:#include "ruby/digest.h"
/srv/gems/enzoic-1.1.3/ext/digest/whirlpool/extconf.rb:have_header('ruby/digest.h')
/srv/gems/enzoic-1.1.3/ext/digest/whirlpool/whirlpool.c:#include "ruby/digest.h"
/srv/gems/keccak-1.2.2/ext/digest/extconf.rb:have_header! 'ruby/digest.h'
/srv/gems/keccak-1.2.2/ext/digest/sha3.c:#include "ruby/digest.h"
/srv/gems/monero_wallet_gen-0.1.0/vendor/bundle/ruby/2.3.0/gems/digest-sha3-1.1.0/ext/digest/extconf.rb:have_header('ruby/digest.h')
/srv/gems/monero_wallet_gen-0.1.0/vendor/bundle/ruby/2.3.0/gems/digest-sha3-1.1.0/ext/digest/sha3.c:#include "ruby/digest.h"
/srv/gems/passwordping-1.0.3/ext/digest/whirlpool/extconf.rb:have_header('ruby/digest.h')
/srv/gems/passwordping-1.0.3/ext/digest/whirlpool/whirlpool.c:#include "ruby/digest.h"
/srv/gems/passwordping_legacy-1.0.0/ext/digest/whirlpool/extconf.rb:have_header('ruby/digest.h')
/srv/gems/passwordping_legacy-1.0.0/ext/digest/whirlpool/whirlpool.c:#include "ruby/digest.h"
/srv/gems/rbt-0.10.175/lib/rbt/yaml/cookbooks/ruby.yml: - ruby/digest.h
/srv/gems/rbt-0.10.175/lib/rbt/yaml/expanded_cookbooks/ruby.yml:- ruby/digest.h
```

Discussion:

* mame: Currently `gem install` writes to system directly (`/usr/local/include` this time), which breaks for non-root users.
* usa: Why is it our problem than rubygems?
* mame: `ext/digest` is the first one who actually does such evil thing.
* knu: This is not necessarily RubyGems' fault since `digest`'s `Makefile` installs `digest.h` to the system header directory (`HDRDIR`) via `$INSTALL_FILES`, which is a feature of `mkmf.rb`.  However, `HDRDIR` can be altered by specifying `make HDRDIR=/destination install`, so the gem command probably should consider doing that when performing a `--user` install.
* usa: Ignoring installation failure could be an option for now.
* mrkn: There is also a problem when a new gem updates a header file.
* mame: Do 3rd-party gems need to have header files?
* nobu: `mkmf` already hass a way to reroute header installation destionation to some specific path.  Gem just doesn't use it.
* usa: whichever, this is not CRuby's problem but RubyGems' and/or digest gem's problem.

Conclusion:

* Ruby's `sudo make install` already installs `<ruby/digest.h>` because `digest` is a-priori.
* For 3.1, `digest` shall ignore installation failure.
* Ultimately this shall be handled by rubygems.
* knu: I'll comment on the issue.


### [[Feature #18256]](https://bugs.ruby-lang.org/issues/18256) Change the canonical name of Thread::Mutex, Thread::Queue, Thread::SizedQueue and Thread::ConditionVariable to just Mutex, Queue, SizedQueue and ConditionVariable (eregon)

* OK?

Preliminary discussion:

*

Discussion:

* shyouhei: There was ::Mutex at the beginning and we eventually moved it to ::Thread::Mutex.
* mame: The transition was maybe because we had then in `thread.so` extension.
* nobu: I'm against it.  The classes are for inter-thread things.
* matz: But Mutex for instance can be used from Fiber.
* akr: Mutex and CV are okay but what about ::Queue?  Queue is the name of data structures.

Conclusion:

* matz: will reply

----
- We called it a day.  The rest of the agenda will be discussed in the Office Hour held on next Monday.
----

### [[Feature #17795]](https://bugs.ruby-lang.org/issues/17795) Around `Process.fork` callbacks API (dan0042)

* A few naming suggestions have been made; does Matz like any of them?

Preliminary discussion:

* `Process.forkpid`
* `Process._fork`
* `Process.fork!`
* `Process.wrap_fork`
* `Process.around_fork`
* `RubyVM.fork`
* `Process::Wrap.fork`

https://github.com/rails/rails/blob/83217025a171593547d1268651b446d3533e2019/activesupport/lib/active_support/fork_tracker.rb#L44

```c=
static VALUE
rb_f_fork(VALUE obj)
{
    rb_pid_t pid;

    switch (pid = rb_fork_ruby(NULL)) {
      case 0:
	if (rb_block_given_p()) {
	    int status;
	    rb_protect(rb_yield, Qundef, &status);
	    ruby_stop(status);
	}
	return Qnil;

      case -1:
      rb_sys_fail("fork(2)");
      // => rb_bug("unreachable");

      default:
	return PIDT2NUM(pid);
    }
}
```


```ruby=
r, w = IO.pipe
Thread.new{
  Process.daemon
  w.puts Thread.list.inspect
}.join

p r.read
```

Discussion:

* matz: 
  * `Process.fork_core`: how about it?
  * `Process._fork`: any similar?
  * `Process.fork!`: similar to `exit!`?
  * rejected
      * `Process.forkpid`: dosen't make sense
      * `Process.wrap_fork`, `Process::Wrap.fork`: it is not "wrap"per.
      * `Process.around_fork`: ditto.
      * `RubyVM.fork`: `RubyVM` is not correct place.
      * `Process.__fork__`: `__send__` should not be overwridden

Conclusion:

matz: `Process._fork` is the best on the list.

### [[Feature #17837]](https://bugs.ruby-lang.org/issues/17837) Add support for Regexp timeouts (dan0042)

* Adding this to ruby 3.1 with a safe (high) value for `Regexp.backtrack_limit` would be an improvement on the current lack of limit, and would allow to get measurements from real-world applications for a better default value.

Discussion:

* mame: Measuring a time at every interrupt check makes execution slow. Limiting backtrack count is difficult to use (better default value is difficult to decide).
* knu, ko1: How about mix the two ideas? So start the time limit after some (say, 10_000) backtracks occurred?
* mame: Sounds a great idea.

Conclusion:

* mame: I'll experiment the new idea

### [[Feature #18254]](https://bugs.ruby-lang.org/issues/18254) Add an `offset` parameter to `String#unpack` and `String#unpack1` (byroot)

- Binary protocol parsers currently have to slice their buffer repeatedly, this new argument would save a lot of string copying.

Discussion:

* ko1: is the read length fixed? Some format may make it variable
* nobu: ASCIZ. The length changes depending on the position of NUL
* nobu: should the API return how many bytes are read?
* mame: we can calculate the read length by checking the length of ASCIZ string read
* mame: a programmer can (and should) keep track of offsets that they are reading now

Conclusion:

* matz: accepted
