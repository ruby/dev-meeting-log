---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-03-09

https://bugs.ruby-lang.org/issues/19429

## DateTime and location

* 2023/03/09 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/04/13 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe


## Check security tickets

[secret]

## Ordinary tickets

(We have many topics today. To handle more topics as possible, the agenda is sorted in order of easiness to discuss)

### [[Feature #19472]](https://bugs.ruby-lang.org/issues/19472) `Ractor::Selector` to wait multiple ractors (ko1)

* ko1: let me take a time to discuss about new Ractor synchronization API

Preliminary discussion:

* ko1: I have already merged this pull request but can you give me any feedback anyways?
* matz: Can you describe its benefit a more?
* ko1: This is performamnt than `Ractor.select(*[h,u,g,e, ...])`
* akr: Does this change order of `Ractor.select`?
* ko1: No, it's still O(n).
* akr: That can be improved.
* ko1: Yes, I agree.
* shyouhei: does `wait` time out?
* ko1: Not yet because `Ractor.select` does not.  But I think we should add that feature.
* nobu: What happens if a ractor is registered to multiple selector?
* ko1: You can, and at most one of the selector gets notified.

Discussion:

* samuel: +1 to timeout.
* matz: Is this `select`, naming wise?
* ko1: golang has channel select
* matz: But golang's seems vastly different than this.
* ko1: This poposed one is semantically the same as golang's.
* matz: I say it _seems_.
* samuel: Ractor seems to mix "Channel" and "Thread" concept (not a bad thing, but it's different from go I suppose).

```go=
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c -> x:
			x, y = y, x+y
        case quit -> _:
			fmt.Println("quit")
			return
		}
	}
}
```

```ruby=
case Ractor.select(*rs)
in ^c, v
  ...
in ^quit, v
  ...
end
```

* samuel: Is using `Ractor::CloseError` a good idea for what appears to be non-exceptional case? (for one, exceptions can be quite slow).

Conclusion:

* matz: Let me think about it for a while.
* mame: I guess ko1 should talk with matz about this topic.

### [[Feature #10343]](https://bugs.ruby-lang.org/issues/10343) Postfix notations for `when` and `else` inside `case` statement (sawa)

  * Allow `foo when condition` within case-statements.
  * Has not received feedback for eight years.

Preliminary discussion:

```ruby
case foo
  "a" when some_very_long_proc
  "bbb" when short_regex
  "cc" when some_long_regex
  "dddd" else
end
```

Conclusion:

* matz: no, rejected.


### [[Bug #19392]](https://bugs.ruby-lang.org/issues/19392) Endless method vs and/or (jeremyevans0)

* Is this a bug or expected behavior?

Preliminary discussion:

```ruby!
def test = puts("foo") and puts("bar")

# ↑ is parsed as ↓

(def test = puts("foo")) and puts("bar")

def test = puts("foo") unless cond?
```

* nobu: I don't hate it, but I think it is super difficult to fix.
* matz: No idea how to implement it.

Conclusion:

* matz: Please use parentheses. We may reconsider if someone write a patch.

### [[Bug #19362]](https://bugs.ruby-lang.org/issues/19362) `#dup` on `Proc` doesn't call `initialize_dup` (zverok)

* The fact that `initialize_dup` is not called by `Proc#dup` is inconsistent, and there are no docs confirming it is deliberate. @nobu's PR shows it is possible to fix.

Preliminary discussion:

```ruby!
class MyString < String
  def initialize_dup(...)
    p(self.class, ...)
    super
  end
end

class MyProc < Proc
  def initialize_dup(...)
    p(self.class, ...)
    super
  end
end

MyString.new('test').dup   # prints MyString, "test"
MyProc.new { 'test' }.dup  # expect: prints MyProc, actual: nothing printed
```

Discussion:

* akr: I don't understand why we need to duplicate a proc at the first place.
* akr: Does `Proc#dup` copy local variables?
* matz: No it doesn't.
* nobu: Prohibit?
* matz: That sounds harsh to me.

Conclusion:

* matz: Let me consider

### [[Bug #19416]](https://bugs.ruby-lang.org/issues/19416) Inconsistent behaviour for `Struct.new` without any member_names (jeremyevans0)

* The fact that `Struct.new("A")` works seems like a bug and not intentional.
* Do we want to fix the bug, or keep it to retain backwards compatibility?
* If we want to fix it, should we deprecate or remove the support in 3.3?

Preliminary discussion:

```ruby!
Struct.new        #=> wrong number of arguments (given 0, expected 1+)

Struct.new('Foo')
Struct::Foo.new   #=> #<struct Struct::Foo>
```

```ruby
class Foo; end
Foo = Struct.new('Foo')
```

```ruby!
Cons = Struct.new(:car, :cdr)
Nil = Struct.new()
```

```ruby
# type foo = A of int | B | C

A = Struct.new(:int)
B = Struct.new()
C = Struct.new()
```

Discussion:

* mame: Is this a bug or a feature?
* mame: Proposed patch is to prohibit this.
* mame: However Data class allows this.
* ko1: Jeremy found an actual use case of it.
* ko1: Allowing this doesn't introduce any incompatibilty.
* matz: This just delays an error
* akr: The error in practice would raise immediately when an object is created, so no so much "delay" are there.

Conclusion:

* matz: persuaded by the `Nil` example above.  This should be allowed.


### [[Feature #19197]](https://bugs.ruby-lang.org/issues/19197) Add `Exception#root_cause` (AMomchilov)

* It returns the last exception in linked-list of causality chain, that is, the original exception (whose own cause is `nil`).

Preliminary discussion:

```ruby!
class Exception
  def root_cause
    s = self
    while s.cause
      s = s.cause
    end
    s
  end
end
```

Discussion:

* ko1: Not sure if the example :arrow_up: is what is proposed.
* shyouhei: I would need the whole chain of the causes, not only the root one.
* nobu: `#each_cause`?

Conclusion:

* matz: I am not against, but want the OP to explain the use case.
* matz: the name `causes` is not acceptable.
* mame: Will ask the OP.

### [[Feature #19094]](https://bugs.ruby-lang.org/issues/19094) `sleep(nil)` vs `sleep()` and replicating the default implementation. (ioquatix)

- Do we accept `sleep(nil)` is equivalent to `sleep()`?

```ruby=
# Easier for the custom wrapper:
def my_sleep(timeout = nil)
    Kernel::sleep(timeout)
end
```

Preliminary discussion:

* mame: there are so many cases that C-defined methods does not behavior as if it is written casually in Ruby. Should we change all of them?
* eregon: Mutex#sleep accepts an explicit nil argument and it's documented. +1, this inconsistency is rather weird (and rather messy to implement in TruffleRuby).

```ruby=
m = Mutex.new
m.lock
m.sleep(nil) # Currently okay.
```

Discussion:

* matz: other methods shall be considered case-by-case.

Conclusion:

* matz: `sleep(nil)` accepted.
* samuel: Thanks :)

### [[Bug #19455]](https://bugs.ruby-lang.org/issues/19455) Ruby 3.2: wrong `Regexp` encoding with non-ASCII comments (jeremyevans0)

* Is using US-ASCII encoding for such regexps a bug? Since the regexp itself does not use non-ASCII, it seems reasonable to use ASCII encoding for the regexp.
* For such regexps, even if the regexp itself is US-ASCII, should the source still be UTF-8, since the source contains non-ASCII characters?
* Are there issues if the regexp encoding does not match the source encoding, because the non-ASCII characters are in comments?

Preliminary discussion:

```ruby!
# Ruby 3.1
/(?#ü)/.encoding  #=> #<Encoding:UTF-8>
/(?#ü)/.source    #=> "(?#ü)"

# Ruby 3.2
/(?#ü)/.encoding  #=> #<Encoding:US-ASCII>
/(?#ü)/.source    #=> "(?#\xC3\xBC)" (invalid encoding)
```

Discussion:

* matz: If not difficult I want the 3.1 behaviour.
* akr: We should preserve original encoding.
* shyouhei: is this because security related (Trojan source code)?
* mame: no. It was due to the change for https://bugs.ruby-lang.org/issues/18294

Conclusion:

* matz: Please try fixing it.
* nobu: Will revisit the current implementation.

### [[Bug #19417]](https://bugs.ruby-lang.org/issues/19417) Regexp `\p{Word}` and `[[:word:]]` do not match Unicode Other_Number character (jeremyevans0)

* Is this a documentation bug or a code bug?
* If a documentation bug, how specific should the documentation be about what \p{Word} and `[[:word:]]` match?

Preliminary discussion:

```ruby!
"\u00B2" #=> "²" (Other_Number)

/\p{Word}/ # matches with Letter, Mark, Number, Connector_Punctuation according to the document

/\p{Word}/u.match?("\u00B2") #=> expected: true, actual: false
```

Discussion:

* mame: I don't know if it should match or should not.

Conclusion:

* naruse: Let me see


### [[Bug #19230]](https://bugs.ruby-lang.org/issues/19230) The openssl backend of securerandom is no longer needed (mame)

* The openssl backend of securerandom has not worked for several years, but we have received no bug reports. I think no one uses it. Let's remove the backend.

Discussion:

* shyouhei: one possibility is to give up openssl.
* shyouhei: other way is to change so that openssl is properly seeded.

Conclusion:

* shyouhei: Let me take a look.

### [[Bug #18743]](https://bugs.ruby-lang.org/issues/18743) `Enumerator#next` / `peek` re-use each others stacktraces (ko1)

* It should be a bug and there is a proposed patch.

Preliminary discussion:

```ruby=
# enum.rb             # 1
                      # 2
enum = [].each        # 3
enum.peek rescue nil  # 4
begin
  enum.next           # 6
rescue Exception => e
  p e.backtrace #=> ["t.rb:4:in `peek'", "t.rb:4:in `<main>'"]
end
```

```ruby=
f = Fiber.new do
  raise
end

f.resume #=> test.rb:2:in `block in <main>': unhandled exception
```

```ruby!
 1: def foo
 2:   raise
 3: end
 4:
 5: def bar(f)
 6:  f.resume
 7: end
 8:
 9: f = Fiber.new do
10:   foo
11: end
12:
13: bar(f)

# actual:
#
# test.rb:2:in `foo': unhandled exception
#         from test.rb:10:in `block in <main>'
#
# expected?:
#
# test.rb:2:in `foo': unhandled exception
#         from test.rb:10:in `block in <main>'
#         from test.rb:6:in `resume'
#         from test.rb:6:in `bar'
#         from test.rb:13:in `<main>'
```

```ruby
def foo(g)                # 1
  raise StopIteration     # 2
end                       # 3
e = Enumerator.new do |g| # 4
  foo(g)                  # 5
end                       # 6
e.peek rescue nil         # 7
e.next                    # 8

# matz's expectation (ideal):
# tt.rb:2:in `foo': StopIteration (StopIteration)
#        from tt.rb:5:in `block in <main>'
#        from tt.rb:8:in `<main>'

# matz's expectation (in case of cause):
# tt.rb:8:in `next': StopIteration (StopIteration)
#        from tt.rb:8:in `<main>'
# casued by:
# tt.rb:2:in `foo': StopIteration (StopIteration)
#        from tt.rb:5:in `block in <main>'
#        from tt.rb:in `each'
#        from tt.rb:in `each'

# current:
# tt.rb:2:in `foo': StopIteration (StopIteration)
#        from tt.rb:5:in `block in <main>'
#        from tt.rb:in `each'
#        from tt.rb:in `each'

# proposed: (not confirmed)
# tt.rb:8:in `<main>': StopIteration (StopIteration)

../../src/clean/test.rb:2:in `foo': StopIteration (StopIteration)
        from ../../src/clean/test.rb:5:in `block in <main>'
        from ../../src/clean/test.rb:in `each'
        from ../../src/clean/test.rb:in `each'

```

Discussion:

* mame: what does this proposed patch do?
* ko1: seems it injects an exception?
* shyouhei: what if the in-fiber exception to become the `#cause` of it?
* mame: I thought it would be nice to mix the inter- and intra- fiber backtraces.
* ko1: I don't like that.
* matz: I would say the staus quo is buggy.
* mame: okay, what to do then?

(people write codes above...)

Conclusion:

* matz: We should fix it.
* mame: Let's take the `cause` way.

### [[Feature #19435]](https://bugs.ruby-lang.org/issues/19435) Expose counts for each GC reason in `GC.stat` (byroot)

- Add 8 new keys in `GC.stat`.
- We used that patch in production to fix GC issues we had, we believe these counters to be high value.
- The only way to get this information today is to compile with debug counters, which isn't very suitable.

Discussion:

* ko1: what is added here is:
  * the number of counts of major GC per each reason why a GC ran.
* ko1: my concern is the key name?
* shyouhei: keys are okay to me.
* matz: stat gets bloated is kind of worrying.
* ko1: this could be done using an external gem.  I wrote one before. https://github.com/ko1/gc_tracer

Conclusion:

* ko1: Will ask about extensions.

### [[Feature #19437]](https://bugs.ruby-lang.org/issues/19437) Add marking and sweeping time to `GC.stat` (peterzhu2118)

- This feature records GC time in marking and sweeping phases and exposed them via a `marking_time` and `sweeping_time` key in `GC.stat`.
- This feature will allow us to know the proportion of time spent in each phase of GC to help us better tune the GC.
- This means that `GC.stat(:time) == GC.stat(:marking_time) + GC.stat(:sweeping_time)`.

Preliminary discussion:

* mame: which do `marking_time` or `sweeping_time` include "compacting_time"?

Discussion:

* matz: is it okay for us to stick to mark and sweep alogrithm forever?
* shoyuhei: let's just abandon GC.stat then.
* naruse: nobody seriously uses this information except for monitoring.
* mame: yes, and it makes no sense when the internal changes to retain stats. 

Conclusion:

* ko1: accepted.

### [[Bug #19436]](https://bugs.ruby-lang.org/issues/19436) Call Cache for singleton methods can lead to "memory leaks" (byroot)

- When caching a singleton method call, the receiver becomes immortal until that call cache is updated.
- On large apps with some infrequently called codepaths this can lead to very large objects graphs not being collected.
- The reference chain is `IMEMO(callcache) -> IMEMO(ment) -> ICLASS -> CLASS(singleton) -> OBJECT`
- I explored 3 solutions
  - Only keep a weak reference to the CME
  - Don't cache singleton method calls (unless it's a Class or Module singleton)
  - Make Class#attached_object a weak reference
- I'd like to get opinions on which solution would be preferable.


Preliminary discussion:

```ruby!
# option 3 is not acceptable
obj = Object.new
def obj.foo
end

sc = obj.singleton_class
sc.attached_object #=> obj
```

Discussion:

* akr: how frequent does it happen?
* shyouhei: Would be severe if the code base is large.
* ko1: CME to keep weak refs is NG to me.
* matz: shoud we mark a call cache?
* ko1: yes, otherwise it gets GCed, reused later, then a cache can point to a different thing.
* ko1: Maybe we can just revert to old way (using class serial rather than the class itself).
* ko1: Or maybe we should invalidate stale cache entries every time a Class is dead.

Conclusion:

* matz: this shall be fixed.  How to fix it is up to ko1.

### [[Feature #19406]](https://bugs.ruby-lang.org/issues/19406) Allow declarative reference definition for `rb_typed_data_struct` (eightbitraptor)

* This is essentially a simpler interface for handling marking/compaction for `rb_typed_data_struct` that hold references to other Ruby objects than the existing `dmark`/`dcompact` callback interface.
* It's useful in non-complex cases where the object just holds simple reference. Is this enough for it to have value?

Preliminary discussion:

```clike!
struct foo {
    VALUE e1;
    VALUE e2;
};

static const size_t foo_refs[] = {
    RUBY_REF_EDGE(foo, e1),
    RUBY_REF_EDGE(foo, e2),
    RUBY_REF_END
};

static const rb_data_type_t foo_data_type = {
    "foo",
    {
        NULL,        // no need to define foo_mark manually
        foo_free,
        foo_memsize,
        NULL,        // no need to define foo_compact manually
    },
    0, (void *)foo_refs, RUBY_TYPED_FREE_IMMEDIATELY | RUBY_TYPED_DECL_MARKING
};
```

Discussion:

* shyouhei: So the OP doesn't want to write their own mark function?
* ko1: Both mark and compact.  The two has to be linked, and doing so is not straight-forward.
* samuel: This seems great to me, I would definitely use it.
* matz: I'm for this idea.  The macro interface can be made better though.
* matz: mmtk is a different story though.
* nobu: I have concerns in memory layouts.

* samuel: Should `RUBY_REF_EDGE` be `RUBY_TYPED` specific? e.g. `RUBY_TYPED_REF_EDGE`?

Conclusion:

* matz: Idea accepted.  Some rough edges need sophisticated.



### [[Feature #19440]](https://bugs.ruby-lang.org/issues/19440) Deprecate `ThreadGroup` (eregon)

* Thoughts?
* If disagree, how do we put the Timeout thread in the default `ThreadGroup`? It seems impossible with current API.

Preliminary discussion:

mame's summary:

* The latest Timeout does not create a timer thread for each `Timeout.timeout` call, but creates it onl at its first call and reuses the timer thread for subsequent `timeout` calls
* And then, the timer thread is added to `ThreadGroup::DEFAULT` [Bug #19020]
* This prevents a user who does `ThreadGroup::DEFAULT.enclose` from calling `Timeout.timeout`
* How can we fix the issue?
  1. Revert the reuse of the timer thread
  2. Revert `ThreadGroup::DEFAULT.add(timer thread)`
  3. Add the timer thread to its dedicated `ThreadGroup` for timeout (nobu)
  4. Make `ThreadGroup#enclose` do nothing (towards deprecation)

Discussion:

* mame: timeout thread is created on-demand, but in which thread group?
* mame: Nobody knows how to use thrad groups but people uses it anyways, according to the code search.
* matz: Too dangerous to delete it then.
* naruse: I remember feature requests are constantly posted once in a several years.
* ko1: but none of them gets real attractions.

Conclusion:

* matz: I know how eregon feels, but deprecation is NG at this point.
* matz: Option 3 accepted.

### [[Bug #19473]](https://bugs.ruby-lang.org/issues/19473) can't be called from trap context (`ThreadError`) is too limiting (eregon)

* This seems a needless limitation on CRuby causing real problems. Let's remove this incorrect exception/limitation?

Discussion:

* ko1: why mutex is prohibited in a trap context is that a trap can happen inside of a mutex lock, and locking it again in a trap is a deadlock.
* ko1: mutex in a trap context is dangerous by design.
* samuel: IIUC, Ruby's interrupt handling does not happen in the actual trap context?
* shyouhei: I don't think it's a wise idea to timeout in a trap at the first place.  But I also agree there are outstanding codes that already do so.
* akr: If people want to do a complex handling people can do `trap() { Thread.new {...} }`

Conclusion:

* ko1: Let me write a reply.

### [[Feature #19450]](https://bugs.ruby-lang.org/issues/19450) Is there an official way to set a class name without setting a constant? (ioquatix)

- Do we accept `Class.new(superclass, name)` and `Module.new(name)` syntax?

Without being able to specify the class/module name, nested classes have ugly names and can be hard for users relate back to source code. e.g.

```ruby=
m = Module.new
m.class_eval("class Foo; end")

m::Foo
=> #<Module:0x00007f7c6a6dddf8>::Foo

m::Foo.new
=> #<#<Module:0x00007f7c6a6dddf8>::Foo:0x00007f7c6a58d840>
```

With my proposal, it would become something like this:

```ruby=
m = Module.new("m")
m.class_eval("class Foo; end")

m::Foo
=> m::Foo

m::Foo.new
=> #<m::Foo:0x00007f2f00e34028>
```

User can provide meaningful name, e.g. controller path, code path, url, etc. The current `Class.name`/`Module.name` does not work for the above use case.

Preliminary discussion:

* mame: No use case is described in the proposal

* samuel: Use case described in the issue:
    - https://github.com/ioquatix/bake/blob/a571f0c47cc202a4b46a836b87b7383d84f74fa0/lib/bake/scope.rb#L30 - "Scope<path/to/file.rb>"
    - https://github.com/ioquatix/bake/blob/a571f0c47cc202a4b46a836b87b7383d84f74fa0/lib/bake/base.rb#L32 - "<Path/To/File>" probably makes sense.
    - https://github.com/socketry/utopia/blob/7f24d503f46000076fbdeeb6e28e293c9544b37b/lib/utopia/controller.rb#LL65C29-L65C29 - probably "Controller<relative/uri/path>"
    - https://github.com/ioquatix/sus/blob/65e408bad260d168701bad79b2cb905ef65435e8/lib/sus/base.rb#L43 - probably "Base"
    - https://github.com/ioquatix/sus/blob/65e408bad260d168701bad79b2cb905ef65435e8/lib/sus/it.rb#L11 - probably "It<path/to/file.rb:line>"

* samuel: Use case in the PR, as part of the Ruby test suite: https://github.com/ruby/ruby/pull/7376/files#diff-84e45206777a1891b7ce021458bfc2fa99cc6cf5f74f9370b883f2ad15eb25a1

* samuel: Example of how RSpec does it: https://github.com/rspec/rspec-core/blob/d722da4a175f0347e4be1ba16c0eb763de48f07c/lib/rspec/core/example_group.rb#L842-L848 which includes specific code to deal with "collisions":

```ruby=
    def self.disambiguate(name, const_scope)
      return name unless const_defined_on?(const_scope, name)

      # Add a trailing number if needed to disambiguate from an existing
      # constant.
      name << "_2"
      name.next! while const_defined_on?(const_scope, name)
      name
    end
```

RSpec could adopt the proposed API and avoid this problem.

* samuel: Search of Google reveals that this is a "frequently" asked question: https://www.google.com/search?q=ruby+anonymous+class+name&oq=ruby+anonymous+class+name

Discussion:

* shyouhei: Seems people actually want to name their classes/modules, but not want that being a constant.
* matz: `Class.new(parent, name)` is NG to me.  It's too easy to be abused.  Ppeople start using it everywhere and break our assumption that class names are constants.
* matz: `def self.name = "something"` feels much better.
* samuel: method is too flexible:

```ruby
class Foo
  def self.name
    Time.now.to_s
  end
    
  class Bar
  end
    
  Bar.name # ????
end
```

```ruby
cls = Class.new do
  class Bar # Too late
  end
  p Bar.name #=> "Bar"
end

cls.name = "c"

Foo = Class.new
class Foo
  class Bar
  end
  p Bar.name #=> "Foo::Bar"
end
```

```ruby!
# ko1: what happens here?
Bar = Class.new(name: "Foo")
p Bar.name
# Current implementation
# => Bar
```

* -> temporary name, permanent name

```ruby!
cls = Class.new
cls.name = "fake class"
cls.class_eval(File.read(path), TOPLEVEL_BINDING, path, 1)

cls = Class.new do
  set_name "fake class"
  self.name = "fake class"
end
```

* ko1: Class names could be related to const_get.

```ruby!
class C
end
Object.const_get(c.name)

```
* samuel: Yes, and it's already broken.

* ko1: You want recognise which anonymous class comes from which.  How about use `__FILE__:__LINE__`, like Proc does?

```ruby
p Proc.new{}
#=> #<Proc:0x0000020560e31950 t.rb:1>
```

* samuel: For instace RSpec cannot be saved by that idea.
* samuel: I think the proposal to add the default file:line to anonymous class/module is a good one but it doesn't solve the other cases.

```ruby
describe "It does a thing" do    # module ItDoesAThing
    it "does the thing" do       #   def does_the_thing
    end
end
```

```
m.label = "Foo loaded from bar.rb"
=> #<(Foo lodaded from bar.rb)::Foo:0x00007f7c6a58d840>
```

Conclusion:

* matz: I understand the request. Let me think of it for a while.

### [[Feature #19452]](https://bugs.ruby-lang.org/issues/19452) `Thread::Backtrace::Location` should have column information if possible. (ioquatix)

- Can we introduce `Thread::Backtrace::Location#column` or similar interface?

Preliminary discussion:

* mame: When an error occurred in foo, you would get the above position

```ruby!
# first_column
receiver.some.call.and.then.foo(argument1, argument2, argument34)
^

# last_column
receiver.some.call.and.then.foo(argument1, argument2, argument34)
                                                                 ^

# ErrorHighlight.spot
receiver.some.call.and.then.foo(argument1, argument2, argument34)
                           ^...^
```

* samuel: I'm not sure why we can't make `first_column` and `last_column` more precise - but if that's the best we can do to start with, it's okay by me. I'd suggest we document it as "best effort" and "may improve in the future", i.e. we may improve the first and last column in the future.

```ruby
def foo
  self.bar
 #^ ["t.rb:14:in `foo'", 14, 2]
end

def bar
  caller_locations.each{|loc|
    p [loc, loc.lineno, loc.first_column]
  }
end

 foo
#^["t.rb:23:in `<main>'", 23, 1]
```

* samuel: it's useful for IDE integration, to present the user where the error occurred. I think the exact "location" can be implementation defined or "unspecified but best effort".

Discussion:

* ko1: in order to do this we need to retain column information in our instruction sequence.
* samuel: how hard is it to implement it?
* ko1: memory consumption is an obvious concern but it can be trivial now a day.
* ko1: in most cases this information is not necessary.  I don't think it's worth.
* ko1: also "what is the column" is not always obvious.  Receiver can be located far from the method name, or arguments, or...
* mame: `ErrorHighlight.spot` is a public API that you can use. Actually the latest rails is already using it
* samuel: Does it work on JRuby etc?
* mame: No, because it depends on `RubyVM::AST`
* samuel: My idea is to make standard interface.
* ko1: Maybe YARP could be a remedy.
* mame: I understand the pain.  No action can be think of right now though.
* samuel: Is YARP going to work on TruffleRuby and JRuby and are we going to expose enough interface to extract AST/column information?

```ruby!
class Thread::Backtrace::Location
    def node
        # Implementation specific YARP node?
    end
end

begin
rescue => error
    error.backtrace_locations.each do |location|
        location.node # ... implementation specific
        Parser.lookup(location.node)
    end
end      
```

Conclusion:

* samuel: Conclusion is, we need to wait for YARP?

### [[Feature #19451]](https://bugs.ruby-lang.org/issues/19451) Extract path and line number from `SyntaxError`? (ioquatix)

- Can we introduce `SyntaxError#column` or similar interface?

Text editor / IDE integration is difficult and presenting errors is difficult without column information. It would be good to expose more information, even if it was just "best effort", e.g. the column offset from the parser where the error occurred.

Preliminary discussion:

* samuel: It should be similar to `Thread::Backtrace::Location#first_column`/`#last_column` for consistency.

* samuel: We should consider whether `syntax_error.backtrace_locations.first` is the location of the syntax error - this is easier for IDEs to consume without knowing specifically about "SyntaxError".

```ruby=
CODE = "foo("

begin
    eval(CODE, TOPLEVEL_BINDING, __file__, 1)
rescue => error
    # Should the first location point at "CODE =" or "eval"?
    error.backtrace_locations.each do |location|
        ide.mark(location.line, location.column)
    end
end
```

Discussion:

* mame: Because the syntax is NG here it's impossible by nature to get an AST.

```ruby
class SyntaxError
    def line
    end
    
    def column
    end

    def error_locations #=> [[lineno, column], [lineno, column], ...]
    end

    # Probably doesn't make sense because we have no AST when SyntaxError occurs.
    def node
        # Implementation specific YARP node?
    end
end
```

* nobu: I think it is possible to implement
* matz: I am for the idea
* mame: What name?

```ruby
error.locations
error.error_locations #=> [[lineno, column], [lineno, column], ...]
error.diagnostics #=> [SyntaxError::Diagnostic, ]
error.spots
error.oopsies
```

* matz: I like error_locations
* nobu: I dislike it because it will include not only the location but also error messages

```ruby
error.xxx #=> [[lineno, column, error message], [lineno, column, error message], ...]
```

```ruby
eval(
    foo
    bar
    baz
)

d = error.diagnostics.first
d.first_lineno
d.first_column
d.last_lineno
d.last_column
d.message
# Just my imagination - can we experiemnt? Maybe it's good or not?
d.relates_to # SyntaxError::Diagnostic | nil
```

```
class Bar # indentation suggests it started here
    def foo


    end
# missing end here
```

```ruby!
# This is a hack to allow us to get the line number of a syntax error.
unless SyntaxError.method_defined?(:lineno)
	class SyntaxError
		def lineno
			if message =~ /:(\d+):/
				$1.to_i
			end
		end
	end
end
```

Conclusion:

* matz: 

## misc

Please reply to count the headcount! https://bugs.ruby-lang.org/issues/19431 DevMeeting at RubyKaigi 2023
