---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-12-01

https://bugs.ruby-lang.org/issues/19074

## DateTime and location

* ~~2022/11/17 (Thu) 13:00-17:00 JST @ Online~~
* -> 2022/12/01 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/12/15 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* do something soon
* rc1 in a week

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #19078]](https://bugs.ruby-lang.org/issues/19078) Introduce `Fiber#storage` for inherited fiber-scoped variables. (ioquatix)

- Updated based on feedback, is it acceptable?

Preliminary discussion:

* ko1: Is the storage implicitly dup'ed?
* mame: It looks like the patch does so.
    * samuel: it's explicitly duped. The internal storage is not exposed, but the hash assignment and accessor is provided for convenience.
* mame: Is the key a Symbol? or non-Symbol instance can be also used?
    * samuel: we decided symbol key only.
    * mame: +1
    * samuel: `@key = :"#{self.class}-#{self.object_id}"` `Symbol.gensym` -> unique symbol.
    * samuel: Currently `Thread.current["foo"]` is allowed. Is it okay to allow a String for Fiber.
    * `Thread.current[obj]` == ``Thread.current[obj.to_s]``
* mame: Does `fiber.storage=` clear elements? `fib[:foo] = 1; fib.storage = {}; fib[:foo] #=> ?`
    * samuel: yes, `nil`.
    * mame: I see. It looks a bit dangerous for a library to use it
    * samuel: Any library can do such a thing with existing thread locals, etc. Shoganai?
    * mame: I think there is no way to delete thread local variables

```ruby!
def handle_requests
    # One way:
    while request = next_request
        Fiber.current.storage = {request_id: ...}
    
    # Another way:
    while request = next_request
        Fiber[:request_id] = ...

    # Another way:
    while request = next_request
        Fiber.new(storage: {request_id: ...}) do ...
end
```

* ko1: Is the storage implicitly inherited when a thread is created? The patch seems to do so
    * samuel: yes.

Discussion:

```ruby!
# Small example:
def call(env)
  Fiber[:request_id] = SecureRandom.uuid
  # Invoke user code, it can use `Fiber[:request_id]` in child fibers, threads, etc and get a consistent value.
  @app.call(env)

  Enumerator.new do |output|
    output << Fiber[:request_id] # inherited
  end.next # lazy fiber created.
end
```

* ko1:
  * `Fiber.[]` & `Fiber.[]=`
  * `Fiber#storage #=> Hash`, `Fiber#storage= (Hash)`
      * Always duped.
  * `Fiber#storage = { Object.new => 42 }` raises a TypeError (for non-Symbol key)
* mame:
  * `Fiber#storage = Object.new` raises a TypeError too?
  * samuel: I will check, but I believe it should.
    * mame: +1
* ko1: Is `Fiber` the right module for this functionality? Because it is not specific for the Fiber (Thread can affet). But I agree there is no better namespace.
  * matz: I can compromise with the name `Fiber`
* matz: I'm afraid to abuse `Fiber#storage=` because it can clear all other keys.

---
* `Fiber.new(storage: true)` -> inherit (dup)
* `Fiber.new(storage: false)` -> inherit (without dup), for Enumerator like behaviour.
* `Fiber.new(storage: {})` -> initial state, empty storage.
* `Fiber.new(storage: nil)` Created lazily.

```ruby!
Fiber[:count] = 0
Enumerator.new do |output|
  output << Fiber[:count]
  Fiber[:count] += 1
end.to_a, .next # different behaviour if the inherited storage is always duped.
```

```c
static void
next_init(VALUE obj, struct enumerator *e)
{
    VALUE curr = rb_fiber_current();
    e->dst = curr;
    // We inherit the fiber storage by reference, not by copy, by specifying Qfalse here.
    e->fib = rb_fiber_new_storage(next_i, obj, Qfalse);
    e->lookahead = Qundef;
}
```

```c
static VALUE
fiber_initialize(VALUE self, VALUE proc, struct fiber_pool * fiber_pool, unsigned int blocking, VALUE storage)
{
    if (storage == Qundef || storage == Qtrue) {
        // The default, inherit storage (dup) from the current fiber:
        storage = inherit_fiber_storage();
    }
    else if (storage == Qfalse) {
        storage = current_fiber_storage();
    }
    else /* nil, hash, etc. */ {
        fiber_storage_validate(storage);
        storage = rb_obj_dup(storage);
    }
```

```ruby
irb(main):034:0> Thread.current[Object.new] = Object.new
(irb):34:in `[]=': #<Object:0x00007f37a75dec20> is not a symbol (TypeError)
```

```ruby!
# ID rb_check_id(volatile VALUE *namep)

Thread.current[:foo] = "bar"

class Foo
  def to_str
    "foo"
  end
end
=> :to_str

Thread.current[Foo.new]
=> "bar"
```

- How about introducing strict mode? `rb_check_id(..., strict = TRUE / FALSE)` or `rb_check_id_only_symbol(...)`?

```bash
$ grep "rb_check_id" **/*.c | wc -l
55
```

Conclusion:

* Use `rb_check_id` and follow same model as `Thread.current[key]`.
* `Fiber.[Symbol]` & `Fiber.[Symbol]=` (Accepted)
  * raise `ArgError` (TypeError?) unless the key is not Symbol
* `Fiber#storage #=> Hash` (Accepted)
* `Fiber.new() #=> (default) inherited with duping == storage: true` (Accepted)
* `Fiber.new(storage: hash)` (Accepted)
* `Fiber.new(storage: nil)` for the empty storage (Accepted)
* Experimental
  * `Fiber.new(storage: true/false)` (Accepted, Experimental)
  * `Fiber#storage= (Hash)` (Accepted, Experimental)

### [[Bug #19079]](https://bugs.ruby-lang.org/issues/19079) Modules included in a DelegateClass cannot override delegate methods (jonathanhefner)

* Proposed PR: https://github.com/ruby/delegate/pull/14#pullrequestreview-1154572034
* Define delegation methods in an included module instead of directly in the class.
* This allow to call `super`.
* Very minimal backward compatibility concerns.

Preliminary discussion:

```ruby
Base = Class.new do
  def foo
    "base"
  end
end

Helper = Module.new do
  def foo
    "helper"
  end
end

WithHelper = DelegateClass(Base) { include Helper }

WithHelper.new(Base.new).foo #=> actual: "base", expected: "helper"
```

* nobu: why doesn't the OP use Module#prepend?

Discussion:

* mame: This is a delegation, not an inheritance. I think the expectation is wrong and the curent behavior is correct

```ruby
require "delegate"

Foo = Struct.new(:field)
FooDelegator = DelegateClass(Foo) do
  def field #=> warning: method redefined; discarding old field
    super.to_s
  end
end
```

Conclusion:

* matz: Let's ask the rationale of OP's expectation and set it as feedback
* nobu: We need to revert suppressing redefinition warning https://github.com/ruby/delegate/pull/13 https://bugs.ruby-lang.org/issues/19047
  * nobu: `Module#prepend` or `Class.new(Foo)` or `DelegateClass(Class.new(Foo))` would be a more correct solution
  * naruse: Please revert it today

### [[Bug #18899]](https://bugs.ruby-lang.org/issues/18899) Inconsistent argument handling in IO#set_encoding (@eregon) (Eregon)

* Could someone review Jeremy's fix and merge it?
* The logic for IO set_encoding is really complicated (also following the C code for it is hard), I wish we could simplify it.

Preliminary discussion:

```ruby
File.open('/dev/null') do |f|
  f.set_encoding("binary:utf-8")
  p f.external_encoding #=> #<Encoding:ASCII-8BIT>
  p f.internal_encoding #=> nil

  f.set_encoding("binary", "utf-8")
  p f.external_encoding #=> #<Encoding:ASCII-8BIT>
  p f.internal_encoding #=> #<Encoding:UTF-8>
end
```

Conclusion:

* naruse: Commented


### [[Feature #18814]](https://bugs.ruby-lang.org/issues/18814) Add a query method to check Ractor Queue count (tenderlovemaking)

* Proposed PR: https://github.com/ruby/ruby/pull/5973
* Good for monitoring
* Sender can decide if it should be queued or not

Preliminary discussion:

* ko1: In general, sender shouldn't decide sending by busy-ness so I'm afraid that it is abused by users.

Discussion:

* ko1: I don't want to introduce this feature because there is no feature in actor (not Ractor)

Conclusion:

* ko1: I will comment

### [[Feature #19104]](https://bugs.ruby-lang.org/issues/19104) Introduce the cache-based optimization for Regexp matching (mame)

* This optimization to onigmo makes most Regexp matching linear time. In short, it will solve ReDoS.
* There are two notable limitations: (1) there are some unoptimizable Regexp patterns (e.g., back-references. About 10% Regexp objects are not optimizable according to measurement with some real-world libraries), and (2) it comsumes memory proportional to the input string (According to measurement, at worst, about 10 times to the length of the input string)
* We want you to discuss: (1) can we merge this change (into preview3 if possible), and (2) should we provide a way to tell users if a given regexp is optimizable or not (e.g., a warning at unoptimizable regexp creation, a new API like `Regexp#optimizable?`, or a new opt-in Regexp flag `/foo/r` to raise a SyntaxError when it is not optimizable, etc.)

Preliminary discussion:

```ruby!
Regexp.check_safe?(reg_src_str)
Regexp.new(reg_src_str).check_safe?

require "regexp"; Regexp.check_safe?(reg_src_str)

Regexp.new(reg_src_str, raise_if_slow: true)

RubyVM.redos_safe?(/.../)

require "regexp" #?
Regexp.redos_safe?("...", Regexp::IGNORE_CASE)
Regexp.redos_safe?(/.../)

Regexp.linear_match?(/.../)

Regexp.linear_time?("...", Regexp::IGNORE_CASE)
Regexp.linear_time?(/.../)
```

Conclusion:

* mame: It is already merged. 
* matz: I would be good to have `Regexp.linear_time?`


### [[Feature #11689]](https://bugs.ruby-lang.org/issues/11689) Add methods allow us to get visibility from Method and UnboundMethod object. (eregon)

* `{Method,UnboundMethod}#{private?,protected?,public?}` exist in 3.1 but are currently not defined in 3.2
* I think we should add them back, I think there is no reason to remove them anymore, since the behavior is intuitive now. Of course we need to store and return the visibility of the original method found by `.method/.instance_method`, not the zsuper-resolved method (same as for `owner`).
* I think it is unnecessary to wait for someone to open a ticket "private?,protected?,public? methods missing in 3.2".

Preliminary discussion:

*

Discussion:


```ruby
#$ ./miniruby -v
#ruby 3.2.0dev (2022-11-30T04:52:33Z master 9a84971315) [x86_64-linux]

class C0
  private def foo = :C0
end

class C1 < C0
end

class C2 < C1
  public :foo
end
  
p C2.new.foo #=> :C0
p m = C2.new.method(:foo)
#=> #<Method: C2(C0)#foo() ../../src/clean/test.rb:4>

class C1 < C0
  def foo = :C1
end

p C2.new.foo #=> :C1
p C2.new.method(:foo)
#=> #<Method: C2(C1)#foo() ../../src/clean/test.rb:18>

p m.call #=> :C0
```

Refs:
* https://bugs.ruby-lang.org/issues/11689 visibility
* https://bugs.ruby-lang.org/issues/18729 need more consideration by Matz
* https://bugs.ruby-lang.org/issues/18751
* https://bugs.ruby-lang.org/issues/18798 ko1: I will do today

Conclusion:

*


### [[Bug #19012]](https://bugs.ruby-lang.org/issues/19012) BasicSocket#recv* methods return an empty packet instead of nil on closed connections (jeremyevans0)

* It seems like a good idea to be able to differentiate an empty packet versus a closed connection.
* Is it OK to change the behavior to return nil for closed connections?

Discussion:

* akr: I wonder there is no portable way to distinguish between datagram (UDP) or stream (TCP)
* nobu: How about defining TCPSocket#recv* and make them to do so (do not touch BasicSocket#recv* themselves)?
* akr: OP's proposal focuses on UNIXSocket. I think there is datagram mode for UNIXSocket. nobu's proposal is very limited

Conclusion:

* Let's ask jeremy about the portability concern


### [[Bug #19003]](https://bugs.ruby-lang.org/issues/19003) TracePoint behavior inconsistency in 3.2.0-preview2 (jeremyevans0)

* Behavior has changed for this case since Ruby 3.1 to fix other TracePoint bugs.
* Do we consider the new behavior a bug, or an implementation detail?

Preliminary discussion:

* ko1: I understand author's idea but not sure how to implement it. I leave it as an implementation details. One idea to implelement it is pending addition operation after TracePoint all hooks.

Conclusion:

* ko1: will reply


### [[Feature #18996]](https://bugs.ruby-lang.org/issues/18996) Proposal: Introduce new APIs to reline for changing dialog UI colours (st0012)

  * After discussing with @k0kubun, we've decided to use iTerm2's settings as colour options' naming reference.
  * We also think it's better to accept colour configs at both `DialogRenderInfo` and completion level (which builds on top of `DialogRenderInfo` but doesn't expose its APIs). This will give us enough flexibility for future extension.
  * Do we think it's ready for implementation?

Discussion:

* hsbt: `Reline.completion_attrs`? Do we need `_color` in `:foreground_color`?

```ruby
Reline.completion_colors[:foreground_color] = :white
Reline.completion_colors[:background_color] = :black
Reline.completion_colors[:selected_text_color] = :black
Reline.completion_colors[:selection_color] = :white
```

* naruse: it is difficult to make the process because of the lack of Itoyanagi-san
9* naruse: k0kubun and stanlo can add a minor change to irb, but I think this is not minor

(...long discussion about how to maintain irb and reline...)

Conclusion:

* no conclusion right now; it is difficult to meet Ruby 3.2, sorry

### [[Bug #19113]](https://bugs.ruby-lang.org/issues/19113) Inconsistency in retention of compare_by_identity flag in Hash methods (jeremyevans0)

* Is it OK to change `Hash.[]` to never return a compare_by_identity hash?
* Is it OK to change `Hash.ruby2_keywords_hash` to not drop the compare_by_identity flag for empty hash arguments?
* Is it OK to change `Hash#compact` to copy the default value/proc and compare_by_identity flag?

Preliminary discussion:

```ruby!
h = {a: 1}.compare_by_identity
p Hash[h].compare_by_identity?
#=> true

h = {}.compare_by_identity
p Hash[h].compare_by_identity?
#=> false
```

* ko1: I expect keys should be same with original Hash so I think `compare_by_identity?` returns true.

Discussion:

(Sorry, forgot to discuss this topic!)

### [[Feature #19107]](https://bugs.ruby-lang.org/issues/19107) Allow trailing comma in method signature (byroot)

- I would like to make `def foo(a,)`, `def foo(a:,)` and `def foo(&block,)` valid syntax.
- Trailing commas are popular when breaking up a very long statement into multiple lines.
- Method calls, array literals, hash literals, all accept trailing commas, but method definitions don't.
- This would seem more consistent to me.

Discussion:

* matz: I don't like this...
* matz: We often add an actual argument to the last, but I think it is relatively rare to add a formal parameter
* mame: a method definition is less than method calls
* nobu: no reason to allow `&block,` because that has to be the last one.
* shyouhei: we add a newline to formal parameters when optinoal expression is long

```ruby!
def foo(
  a, b, c,
  opt = very.long.expression,
)
end

def foo(x, y, z,)
end

def foo(
  a, b, c,
  mode1: false,
  mode2: false,
  mode3: false,
)
end

def foo(
  a, b, c
  , mode1: false
  , mode2: false
  , mode3: false
)
end
```

* mame: what about allowing comman if only if the comma is at end of a line?
* shugo: I guess matz doesn't get what is good about this proposal.
* matz: no.
* knu: I had a chance when a API changed and its wrapper has to follow.

Conclusion:

* matz: will reply


### [[Feature #18951]](https://bugs.ruby-lang.org/issues/18951) Object#with to set and restore attributes around a block (byroot)

- It's not clear to me what was decided when this was first discussed in a previous meeting.
- I kinda understand that `with` is seen as too generic, if so would `with_attr` be acceptable?
- Any other concerns?

Preliminary discussion:

```ruby!
Bar.with_attr(foo: true) do
  # test things
end

# ==

begin
  backup, Bar.foo = Bar.foo, true
  # test thing
ensure
  Bar.foo = backup
end
```

Discussion:

* matz: in general it is not a good idea in terms of thread safety
    * samuel: I can confirm I have seen real world bugs due to unsafe manipulation of global state on multiple threads like this.
* mame: I often write this pattern with `$VERBOSE`, but I personally cannot remember for accessor `Bar.foo`
* matz: How about starting with gem?
* knu: Rails often provides dedicated methods for this pattern, such as `with_lock`.
* knu: It is barely possible to type-check calls to this method, so this API may not fit in this day and age.
* amatsuda: I often see this pattern in test code including Rails
* yui-knk: Does activesupport support this?

Conclusion:

* matz: Will reply

### [[Feature #19117]](https://bugs.ruby-lang.org/issues/19117) Include the method owner in backtraces, not just the method name (byroot)

- `from /tmp/foo.rb:4:in 'Foo::Bar#inspect'` instead of just `from /tmp/foo.rb:4:in 'inspect'`.
- Would make backtrace much easier to understand just by reading them without jumping to the definition every time.

Discussion:

* matz: Sounds nice, but concern performance.
* ko1: `self` has to be preserved per frame for this proposal, plus extra name lookup needed when actually stringizing the backtrace.
* mame: `#inspect` can be too long depending on its implementation.
* ko1: we should forbid ruby-level method calls to creep in here.
* mame: agreed.
* ko1: Constant name can also be long `A::B::C::D::...`
* naruse: We don't need the nesting.

Conclusion:

* naruse: need to discuss more.  Maybe in 3.3 era.

### [[Feature #19000]](https://bugs.ruby-lang.org/issues/19000) Data: Add "Copy with changes method" [Follow-on to #16122 Data: simple immutable value object] (bdewater)

* There seems to be consensus for the need of the feature, but not on what the method name should be. The proposals are `with` (C#, Java, Sorbet, value_semantics gem) and `dup` (with arguments, similar to Sequel datasets).
* I think it would be valuable to have this decided and shipped with 3.2 to make Data objects ergonomic to work with.

Discussion:

* `Data#with(k:1)`
* `Data#dup(k:1)`
* `Data#dup_with(k:1)`
* `A.new(k:1, **a.to_h)`

```ruby
A = Data.define(:foo, :bar)
a1 = A.new(1, 2)              #=> #<data A foo=1, bar=2>
a2 = A.new(**a1.to_h, bar: 3) #=> #<data A foo=1, bar=3>
```

```ocaml
# origin;;
- : point = {x = 0; y = 0}
# let p = {origin with y = 3};;
val p : point = {x = 0; y = 3}
```

* matz: `dup` sounds wrong to me.  Duplication is not the main purpose here.
* matz: How about `Data#update`
* mame: Sounds functional, nice.
* ko1: But `Hash#update` has a different semantics.
* matz: I guess Data and Hash are different things.
* shugo: It doesn't mutate things unlike Hash does.  Should be safer than vice-verca.
* ko1: How about `Data#merge` like `Hash#merge`? They have similar semantics
* matz: merge is NG.

Conclusion:

* matz: I will propose `Data#update`. `Data#with` is acceptable. `Data#dup` is not acceptable.

---

* matz: Data#frozen? should return true?
* matz: it is theoretically possible to rewrite the fields by calling `#initialize`
* nobu: it also prohibits to add a singleton method. Is it okay?
* matz: I think okay. In principle, Data instance should not have object identity
* mame: In future, is it possilbe to implement dedup for Data?
* matz: Maybe possible?
* matz: I will create a ticket for freezing all Data instance

### [[Feature #19138]](https://bugs.ruby-lang.org/issues/19138) `SyntaxError#path` for syntax_suggest

* Currently syntax_suggest searches the path name from the exception message.
But extracting the info from messages for humans is fragile, I think.
So proposing a new method `SyntaxError#path`, similar to `LoadError#path`.
* Since we have not released with `SyntaxError#detailed_message` yet, there should not be a compatibility issue.
* [Implementation](https://github.com/ruby/ruby/pull/6779)

Conclusion:

* matz: looks good

## other topic

#### https://bugs.ruby-lang.org/issues/19036

* samuel: I support `IO.new(..., path: ...)` / `IO.for_fd(..., path: ...)` since this is how Ruby's IO model is implemented and it makes sense to expose this.

```ruby
p STDERR
#=> #<IO:<STDERR>>

p IO.for_fd(STDERR.to_i)
#<IO:fd 2>

p IO.for_fd(STDERR.to_i, path: '<STDERR>')
#=> #<IO:<STDERR>>
```

`name`?

---

```
def foo(...) # def foo(*, **, &)
  bar(*) # OK
  bar(**) # NG
end
```

* shugo: I will create a ticket

### [[Bug #19132]](https://bugs.ruby-lang.org/issues/19132) `**` with other keyword parameters causes "no anonymous keyword rest parameter" (shugo)

  * RBS expects no name for keyword rest parameters while test_method.rb etc expects `:**`. Can we choose `:**` for consistency?

Conclusion:

* matz: accepted

### [[Feature #19134]](https://bugs.ruby-lang.org/issues/19134) `**` is not allowed in `def foo(...)` (shugo)

  * I believe `**` should be allowed, or both `*` and `&` should be prohibited.

Conclusion:

* matz: LGTM


### [[Bug #19108]](https://bugs.ruby-lang.org/issues/19108) Format routines like pack blindly treat a string as ASCII-encoded (@eregon) (Eregon)

* Is it OK to make `unknown pack directive` an `ArgumentError`?
* If not is it OK to make it a non-verbose warning (i.e., shown with default `$VERBOSE=false`)?

Conclusion:

* matz: ok


### [[Bug #19150]](https://bugs.ruby-lang.org/issues/19150) pack/unpack silently ignores unknown directives (eregon)

* Similar to #19108 just above.
* I believe unknown directive should be ArgumentError, not silent unless VERBOSE is true.

Discussion:

```ruby
[1].pack('>L') #=> "\x01\x00\x00\x00"

$VERBOSE = true
[1].pack(">L") #=> <internal:pack>:144: warning: unknown pack directive '>' in '>L'
```

* matz: looks good, but there is no enough time to test it with preview release
* naruse: how about starting with a warning by default (non-VERBOSE?)
* naruse: making it raise in ruby 3.3 preview 1 looks reasonable

Conclusion:

* matz: try non-verbose warning in Ruby 3.2, and try an exception with Ruby 3.3

---

