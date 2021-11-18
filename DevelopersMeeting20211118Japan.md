---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20211118Japan

https://bugs.ruby-lang.org/issues/18266

## DateTime and location

* 11/18 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 12/09 (Thu) 13:00-17:00 JST @ Online
    * mame: 12/16 Conflicts with WRC.
    * matz: Can't attend this
    * pre: 12/07

## Announce

## About release timeframe

* preview 1 has been released
* preview 2?
    * no concrete plan yet.

## Check security tickets

[secret]


### [[Feature #18262]](https://bugs.ruby-lang.org/issues/18262) `Enumerator::Lazy#partition` (zverok)

* Part of the effort to make `Lazy` more natural: `#partition` to return two lazy enumerators

Preliminary discussion:

* mame: maybe a use case must be clarified
* mame: knu may have an opinion

Discussion:

* knu: I believe this would be a good addition.  The only concern is compatibility, but I think the users of `lazy` would understand this could change.
* mame: This would consume memory more than before?
* akr: Must be the same as before at worst case.
* knu: we shall document that.  The implementation is not that intuitive.
* matz: This proposal returns two Lazy instances, which is a new concept to us.  Is this okay?  Can buffer indefinitely in case of end-less ranges etc.
* knu: Hmm... That can be a problem.
* akr: If it has a reasonable use case, it is valuable to provide it even if it has some weird corner cases

Conclusion:

* knu: I will discuss it in the ticket

### [[Feature #12737]](https://bugs.ruby-lang.org/issues/12737) `Module#defined_refinements` (shugo)

It's good to have for debugging purposes. Users can see refinements defined in a module in irb etc. without reading code.

Preliminary discussion:

* mame: shugo will explain the proposal himself

Discussion:

* shugo: This is what we currently lack.
* shugo: For debugging purpose, maybe used from IRB.
* shugo: Also useful when tesing refinements themselves.
* matz: Feature okay, but name...?  Is a refinement "defined"?
* shugo: What else?
* matz: `refined_refinements`
* shugo: That sounds the refinements' list of used one.
* mame: `used_refinements`
* shugo: That's the next ticket ↓
* nobu: `refining_modules`
* shugo: I'd prefer "something_refinements" naming.
* nobu: Started to think that `defined_refinements` describes the return value the best.

----

* shugo: What about the return value?  Proposed one retuns a hash.
* matz: Is it possible to make it return an array?
* shugo: Yes, but then we need another API to query which refiements refines which class.
* matz: Hmm.

Conclusion:

* Devs are considering names.


### [[Feature #14332]](https://bugs.ruby-lang.org/issues/14332) `Module.used_refinements` to list refinement modules (shugo)

It's good to have for debugging purposes. Users can see all activated refinements in a given scope.

Preliminary discussion:

* ko1: used_modules + defined_refinements => used_refinements ?

```ruby=
class Module
  def self.used_refinements
    used_modules.map{|mod|
      mod.defined_refinements
    }.flatten
  end
end
```

Discussion:

* shugo: used_modules+defined_refinements and used_refinements return the same result except when new refinements are defined in a module after `using`.

```ruby=
module M
  refine Integer do
    ...
  end
end

using M

module M
  refine String do
    ...
  end
end

p Module.used_refinements #=> [#<refinement:Integer@M>]
p Module.used_modules.flat_map { |m| m.defined_refinements.values } #=> [#<refinement:Integer@M>, #<refinement:String@M>]
```

* matz: Can this be obtained in case `defined_refinements` above is intoduced?
* shugo: See above, there are differences.
* matz: Ah okay.
* mame: Are the return values flat array?  Should this be a tree?
* shugo: Implementation-wise the values are already flat.

Conclusion:

* matz: `used_refinements` accepted.
* matz: We need to discuss impedance mismatch between this one versus `deifned_refinements`


### [[Bug #18270]](https://bugs.ruby-lang.org/issues/18270) Refinement#{extend_object,append_features,prepend_features} should be removed (shugo)

* Refinements are different from normal modules, so it's potentially dangerous to use refinements as mixins.
* Why not deprecate them in Ruby 3.1, and remove them in Ruby 3.2.

Preliminary discussion:

* mame: shugo will explain the proposal himself
* mame: #12737 says the following use case, but this proposal will make it not work
```ruby
for klass, refinement in Module.defined_refinements
  klass.prepend(refinement)
end
```
* shugo: These methods raise ArgumentError since #13236 was fixed, so it may not be necessary to remove them.
  append_features is undefined in the class Class, and Module#include raises TypeError before calling append_features if the given argument is a Class.

Discussion:

* shugo: We cut `Refinement` class from `Module`.  But some methods remain.  They are useless.  Let's delete them.
* shugo: Calling them are runtime exceptions right now.
* shugo: `undef`ining these methods change the exceptions raised.
* naruse: Please hesitate backwards incompatibilities...

Conclusion:

* matz: No objection.
* naruse: Accept it next year.


### [[Feature #16252]](https://bugs.ruby-lang.org/issues/16252) `Hash#partition` should return hashes (dan0042)

* (mame: you need to write your comment)

Preliminary discussion:

* mame: Incompatibility. Maybe we need another name if it is introduced

Discussion:

* mame: This is `Enumerable#partition` right now.
* knu: I fully agree `partition` should have been this way if possible.
* shyouhei: Understand the needs but?
* matz: `Hash#select` returns a Hash...
* mame: But `Hash#map` does not.
* knu: Interesting how `Hash#select` returns a hash while `Hash#collect` returns an array.
* mame: Would be better to avoid breaking existing codes.

Conclusion:

* knu:  I understand the results of public code search but `Hash#partition` is likely used in application code, so this change would hurt many businesses.  Provide us a new name.


### [[Misc #18285]](https://bugs.ruby-lang.org/issues/18285) `NoMethodError#message` uses a lot of CPU/is really expensive to call (eregon)

* OK to do https://bugs.ruby-lang.org/issues/18285#note-11 and no longer call custom #inspect for NoMethodError?
* Same for NameError?


Summary of issue:

* Performance: slow when inspecting on a recursively huge object takes time.
* Usability: difficult to read

Preliminary discussion:

* mame: I understand the motivation, but reducing information will be unuseful. I wonder which is better
* mame: eregon's code example

```ruby=
# Already the case, nothing to change for those :)
true.foo  # => undefined method `foo' for true:TrueClass (NoMethodError)
false.foo # => undefined method `foo' for false:FalseClass (NoMethodError)
nil.foo   # => undefined method `foo' for nil:NilClass (NoMethodError)
Object.new.foo # => undefined method `foo' for #<Object:0x0000000002f3afc8> (NoMethodError)
String.foo # => undefined method `foo' for String:Class (NoMethodError)

# Suggested change:
"foobar".foo
# old => undefined method `foo' for "foobar":String (NoMethodError)
# new => undefined method `foo' for #<String:0x0000000002ffaaa8> (NoMethodError)
(the String could be very long, same for other object with long or expensive #inspect)

Object.new.instance_exec { @a = 42; self.foo }
# old => undefined method `foo' for #<Object:0x00000000025e6030 @a=42> (NoMethodError)
# new => undefined method `foo' for #<Object:0x00000000025e6030> (NoMethodError)
(printing ivars could lead to very long and slow output due to recursive #inspect)
```

* ko1: eregon's `<Object:0x...>`. Name it as `any_to_s` (not define as a Ruby method)
* ko1: how about to introduce new inspect method `limited_inspect(length)` which has rule:
    * non-recursive
    * limited to the given `length`
    * `Kernel#limited_inspect` is implemented by `any_to_s`


Options:

* Do nothing
* Cache `#message` results (by ticket author)
* Do not use inspect (by ticket author)
* Use only `#any_to_s` (by eregon)
* Use only `#class`
* Introduce `#limited_inspect`

Discussion:

* mame: This is because the receiver is very big and we are trying to inspect it.
* mame: The problem is (1) slow, and (2) those very big outputs are impossible to read.
* shyouhei: Whether an inspected data is impossble to read or not is not known until we actually inspect one.
* mame: The OP also claims `#inspect` being too slow.
* mame: OP also proposes to cache the result.
* shyouhei: Cache doesn't work here.
* akr: I proposed [#6733](https://bugs.ruby-lang.org/issues/6733) before to introduce `#inspect_to`.
* mame: It reminds me of Rust's display.
* shyouhei: Doesn't `#class` have its own performance pitfall when the class is anonymous?
* nobu: I fixed that (class' name issue) before.
* akr: It seems `#inspect_to` is overkill for the issue to me.  It is NoMethodError.  Knowing the receiver's class shall suffice.

```ruby=
{}.foo
#=> old: NoMethodError (undefined method `foo' for {}:Hash)
#=> new: NoMethodError (undefined method `foo' for #<Hash:0xXXXXXXXX>)

class Foo; foo; end
#=> old: NameError (undefined local variable or method `foo' for Foo:Class)
#=> new: NameError (undefined local variable or method `foo' for #<Class:0xXXXXXXXX>)
```

* akr: A class object should be handled specially, but maybe any_to_s is good enough for other objects
* matz: I don't want to introduce it into Ruby 3.1
* matz: What about delaying the construction of message until it is ultimately needed?
* shyouhei:  The OP is datadog, which serializes errors and send them to their servers.  They always need the message.

Conclusion:

* matz: Give up for Ruby 3.1, let's try any_to_s approach after 3.1 is released towards 3.2


### [[Feature #18273]](https://bugs.ruby-lang.org/issues/18273) `Class#subclasses` (byroot)

- Something we forgot to discuss as part of [Feature #14394](https://bugs.ruby-lang.org/issues/14394).
- There are cases where you only want direct subclasses, not the whole tree.
- Can either be `Class#subclasses` like in Active Record.
- Or could be a boolean parameter on `descendants`.

Preliminary discussion:

* mame: The implementation of subclass list is turned out to be difficult to tract because this is a weakref list to child classes.
* ko1: I don't want to manage the data structure unless these features (Class#subclasses and #descendants) are really needed (I'm not confident to maintain this data structure)
* mame: Is "subclasses" a good name? Consider `class C; end; class D < C; end; class E < D; end`, E is a subclass of C.
* ko1: "direct_subclasses"?

Discussion:

* mame: Why there is `#descendants` and not a method that returns direct children?
* nobu: Why not extend `#descendants` so that it takes recursion limit?
* mame: "subclass" can also mean grandchildren.
* matz: Yes there are both tradition of "subclass" meaning.
* mame: @eregon says Rails already has `#subclasses`.
* matz: I think this is okay

Candidates:

```ruby
(nothing)
klass.subclasses
klass.direct_subclasses
klass.descendants(immediate = false)
klass.descendants(immediate: false)
klass.descendants(direct: false)
klass.descendants(lv: Integer)
```

Conclusion:

* matz: `Class#subclasses` accepted.


### [[Feature #18287]](https://bugs.ruby-lang.org/issues/18287) Support `nil` value for `sort` in `Dir.glob` (eregon)

* Let's be consistent for core methods and treat `nil` as `false`, or raise if not boolean.

Preliminary discussion:

```ruby
Dir.glob("brace/a{.js,*}", sort: true)  #=> sorted
Dir.glob("brace/a{.js,*}", sort: false) #=> not sorted
Dir.glob("brace/a{.js,*}", sort: nil)
#=> current: sorted,
# proposal: not sorted
# proposal2: raise on non boolean value
```

Discussion:

* mame: `Dir.glob`'s `sort` option is default true.  Should explicitly passing nil be true? false? error?
* matz: The convention that nil is default is not popular, so it would be good to see it as true or raise an exception
* nobu: This option is new.
* mame: It's since 3.0.

Conclusion:

* nobu: I'd like to raise an exception.
* matz: Go ahead.

### [[Feature #6210]](https://bugs.ruby-lang.org/issues/6210) load should provide a way to specify the top-level module (jeremyevans0)

* This is fairly easy to implement in a backwards compatible manner, and seems useful.
* Are we OK adding this feature?  If so, is the pull request acceptable?

Preliminary discussion:

```ruby=
# foo.rb
def foo = 42

# main.rb
module M
end
load "foo.rb", M
class C
  include M
end
C.foo #=> 42

# definition
def load path, wrap = false
  if wrap
    if wrap.type == T_MODULE
      target_module = wrap
    else
      target_module = Module.new
    end
  else
    target_module = main_module
  end
  ...
end
```

* ko1: I'm afraid this feature can be abused.

Discussion:

* nobu: this doesn't work for e.g. extensions.
* mame: implementation-wise it is impossible to inject source codes into classes.
* nobu: I wrote `module_eval File.read ...` before.  This can save that.
* matz: ...okay.
* nobu: Why hasn't there been this one for this long time?
* matz: I don't remember any longer.

Conclusion:

* matz: replied.


### [[Feature #11256]](https://bugs.ruby-lang.org/issues/11256) anonymous block forwarding (jeremyevans0)

* This was accepted by @matz four years ago, but never committed.
* I've created a new working version based on patches from @nobu and @mame.
* There should be no backwards compatibility issues, as the proposed syntax is currently invalid.
* Is it OK to add this feature?  If so, is the pull request acceptable?

Preliminary discussion:

```ruby=
def foo(a, b) = bar(&)    # Type (A)
def foo(a, b, &) = bar(&) # Type (B)
# def foo(&blk) = bar(&blk)

def foo(a, b, &) = bar(a, b, &) ==
def foo(...) = bar(...)
```

* ko1: `...` is introduced. Is it needed more? I agree some times we need to pass only block (no other parameters). But not sure how many.

Discussion:

* shyouhei: @matz already accepted. @nobu has a patch. Why bother? Just merge it.
* mame: ko1 says we have `...`, which covers this use case.
* mame: It has been already accepted four years ago. Matz are you still okay?

Conclusion:

* matz: Go ahead.
* matz: "Type (A)" above shall be prohibited.

---

```ruby=
class C
    def foo(&blk); ...; end
end

class D
    def foo; end
    def foo(&); end # you must write it like this
end

def call_foo(c_or_d)
    c_or_d.foo {}
end

call_foo(C.new)
call_foo(D.new) #=> false positive
```

* matz: Let's warn it after Ruby 3.2
* nobu: by default?
* matz: maybe default is preferable
* matz: warn it once per line of code

### [[Feature #11689]](https://bugs.ruby-lang.org/issues/11689) Add methods allow us to get visibility from `Method` and `UnboundMethod` object. (jeremyevans0)

* As requested by @matz, I put together a patch for #public?, #private?, and #protected?.
* Do we want to use this approach, or do we want to reconsider the #visibility method that returns a symbol?
* If we want to use this approach, is the pull request acceptable?

Preliminary discussion:

```ruby
Foo.method(:bar).public? #=> true or false
```

Discussion:

* mame: matz hesitated use of "visibility" terminology.
* matz: Yes, and the proposed ones are okay.

Conclusion:

* matz: go ahead


### [[Feature #12495]](https://bugs.ruby-lang.org/issues/12495) Make "private" return the arguments again, for chaining (jeremyevans0)

* This seems useful, and the backwards compatible issues are quite small.
* Are we OK adding this feature?  If so, is the pull request acceptable?

```ruby=
my_decorator private def foo
  ...
end
```

Preliminary discussion:

* ko1: if Matz want to put `private` etc on the beginning of line, it shouldn't be changed.

Discussion:

```
% nocorrect sudo docker run -i --rm rubylang/all-ruby env ALL_RUBY_SINCE=1.9.0 LC_ALL=C.UTF-8 ./all-ruby -e 'class Foo; p private def doo; end end'
ruby-1.9.0-0        -e:1:in `private': nil is not a symbol (TypeError)
                        from -e:1:in `<class:Foo>'
                        from -e:1:in `<main>'
                exit 1
...
ruby-2.0.0-p648     -e:1:in `private': nil is not a symbol (TypeError)
                        from -e:1:in `<class:Foo>'
                        from -e:1:in `<main>'
                exit 1
ruby-2.1.0-preview1 Foo
...
ruby-3.0.2          Foo
```

```
% nocorrect sudo docker run -i --rm rubylang/all-ruby env LC_ALL=C.UTF-8 ./all-ruby -e 'class Foo; def foo; end; p(private :foo); end'
ruby-0.49             -e:1: syntax error
                      -e:1: syntax error
                  exit 2
ruby-0.50             -e:1: syntax error
                      -e:1: syntax error
                  exit 2
ruby-0.51             -e:1: syntax error
                  exit 1
...
ruby-0.95             -e:1: syntax error
                  exit 1
ruby-0.99.4-961224    nil
ruby-1.0-961225       nil
ruby-1.0-971002       Foo
...
ruby-1.6.8            Foo
ruby-1.8.0            -e:1: warning: parenthesize argument(s) for future version
                      Foo
...
ruby-1.8.6-p420       -e:1: warning: parenthesize argument(s) for future version
                      Foo
ruby-1.8.7-preview1   Foo
...
ruby-3.0.2            Foo
```

Conclusion:

* matz: accepted
* matz: private with no arguments should return nil (as Jeremy's patch)


### [[Feature #16663]](https://bugs.ruby-lang.org/issues/16663) Add block or filtered forms of `Kernel#caller` to allow early bail-out (jeremyevans0)

* Currently it is not possible to generate a partial backtrace without knowing how many frames you need up front.
* This feature allows the production of partial backtraces without that knowledge.
* For example, you can use it to return the only the first frame that meets some criteria.
* There shouldn't be any backwards compatibility issues, as `caller`/`caller_locations` does not currently use a block.
* Are we OK adding this feature?  If so, is the pull request acceptable?

Preliminary discussion:

* mame: no objection.

Discussion:

* mame: proposed is an extension to existing `#caller` to take a block.
* shyouhei: can be under a different name like `#each_caller`.
* matz: I feel something.
* mame: API?
* matz: (nod) Don't know how to read a "caller with a block".
* mame: I don't understand the "caller" terminology at the first place.
* matz: The name "caller" came from Perl.
* matz: Understand the needs of it though.

Conclusion:

* matz: Please propose another good name than "caller".


### [[Feature #18336]](https://bugs.ruby-lang.org/issues/18336) How to deal with Trojan Source vulnerability (duerst)

- This includes [A] Bidi characters [B] non-spacing characters (see [Feature #18337]), and [C] mixed-script identifiers.

Discussion:

* duerst: I mailed this to security@ but was told that this is already open.
* duerst: I want to hear your opinions about it.
* duerst: I propose to prohibit the "[B]" ones for (at least) variable names.
* mame: What other languages do?
* matz: It's hard to compare, because Ruby is not Unicode-centric.
* matz: maybe tools can help?
* naruse: It's theoretically possible to filter some unicode properties but wonder if that solves everything.
* duerst: There is a concept called defence-in-depth.  Multiple places to protect a vuln is not a bad idea.
* nobu: Because there are string interpolarations it is impossible to protect strig literals to have LRO/RLO.
* naruse: Restricting the source code encoding to US-ASCII is the safest.
* nobu: We have supported UTF8 long before 2.0.
* naruse: This vuln is about people reviewing their source code.  It goes ultimately to rendering texts.
* mame: homograph attacks has been known for years.
* duerst: Domain names are restricted.  No similar thing is done in ruby.
* znz: What about restricting characters to Unicode letter class?
* duerst: That doesn't save us from homograph attacks.
* akr: Do unicode classes change over time?
* duerst: They don't decrease.  There are additions though.
* naruse: We have to prohibit use of unicode-unassigned characters then.
* shyouhei: What problem?
* naruse: Speed
* duerst: 99.9% source codes are in ASCII.

```ruby=
# coding: UTF-8; lang: ja

[BIDI control letter] <- not accepted
```

* mame: Github already treats this, VS code also handles this now.
* akr: I think that's reasonable.  Tools to review pull requests are the right places to prevent this problem.
* duerst: Going that way doesn't save those people using NOTEPAD.EXE
* knu: Maybe `git diff` should show bidi characters as such because not a few people review code with git on terminal (nobu is one of them).  Perhaps pagers like less, or even terminal emulators should have an option to do that too?
* knu: What should ruby do to prevent things like that?  Maybe by getting `ruby -c` to warn?  The interpreter should just do whatever written to do in run time.
* duerst: It is a bad idea for Ruby to check this run time.  There are situations when we want to handle those unicode characters.  Ruby should, if any, check this at compile time.
* naruse: Ultimately if we want to prevent attacks of this class, we need a new kind of unicode normalization that normalizes "similar" characters be the same, like the one defined in the IDN, but more complete.
* mame: After many languages agree with any concensus about this issue, let's consider it to merge

Conclusion:

* matz: let's wait for concensus of other languages


### [[Bug #18296]](https://bugs.ruby-lang.org/issues/18296) Custom exception formatting should override `Exception#full_message`. (ioquatix)

Introduce general interface:

```
class Exception
  def print(io, **options)
    io.write(self.full_message(highlight: io.tty?, **options))
  end
end
```

Discussion:

* mame: AFAIK did_you_mean and error_highlight heavily monkey-patch everything, which is suboptimal.  The OP wants to gather them into `#full_message`.
* mame: This is almost backwards compatible but datadog can be broken.
* matz: @mame's summary doesn't sound like a bad idea.

Conclusion:

* naruse: let's consider it next year.

### [[Bug #18243]](https://bugs.ruby-lang.org/issues/18243) `Ractor.make_shareable` does not freeze the receiver of a Proc but allows accessing ivars of it (eregon)

* How about this approach to fix it https://bugs.ruby-lang.org/issues/18243#note-9?
* I think passing a Proc with a non-shareable `self` to a Ractor is highly confusing semantically and in practice, and it's prone to bugs.

Issue:

`Ractor.make_shareable(Proc.new{self})` doesn't make sharable the `self` of `Proc`. It's violate Ractor's assumptions.

Preliminary discussion:

```ruby=
def make_sharable(pr)
  unless Ractor.shareable?(pr.self)
    pr.self = nil # internal
  end
  make_shrable_proc(pr)
end 
```

Another idea: add `proc_self` keyword
```ruby=
def make_sharable obj, proc_self: nil
end
```

```ruby=
class C
  define_method :foo, Ractor.make_sharable(-> { 
      ... 
  })
end

class C
  { :foo => 1, :bar => 2, :baz => 3 }.each do |name, val|
    Ractor.define_method :foo do
      val
    end
  end
end
```

Related: https://bugs.ruby-lang.org/issues/18275

Motivation:

* Pass to `define_method` which can sharable with Ractors
* Pass the Proc to another Ractor to send a logic

Options:

* Raise exception if `self` is unsharaeble
* Introduce `Proc#bind(shareable_object (ex: nil))`
* Replace Proc's self to nil implicitly
* Add `proc_self:` keyword and replace it
* Mark as a sharable proc that can be called only by `#bind_call` (or `instance_eval, ...`) (not `#call`)

Proc types:

* ISeq (`pr = -> {...}`)
* symbol#to_proc (`:sym.to_proc`)
* bmethod (used internally)
* compound (`pr >> pr`)

Discussion:

* mame: I think I heard from @ko1 that he plans to raise exception for unfrozen `self`

Conclusion:

* matz: We need @ko1 to discuss this one.

### [[Feature #18276]](https://bugs.ruby-lang.org/issues/18276) `Proc#bind_call(obj)` same as `obj.instance_exec(..., &proc_obj)`

* ko1: isn't it useful?

---

* ko1: I didn't understand the spec of `instance_exec` but I didn't consider the replacement of `def` to singleton class. Just replacing `self` with a given argument. So the title (and the description) was wrong.

Discussion:

* matz: who needs it?
* mame: This is the counter proposal of freezing `Proc#self`.  Instead of making sure it is frozen, Allow ractors to call proc with explict ractor-local self.
* matz: UnboundMethod's `#bind_call` is a `#bind` then `#call`, do we also want `#bind` for Procs?
* mame: @eregon supports that.
* matz: But we don't want to polymorph between Proc and UnboundMethod?
* mame: no.

Conclusion:

* matz: I want to talk with @ko1.
* matz: Can procs be made sharable at the first place...?

### [[Feature #10917]](https://bugs.ruby-lang.org/issues/10917) Add `GC.stat[:total_time]` when GC profiling enabled

* ko1: https://bugs.ruby-lang.org/issues/10917#note-13 is acceptable?

GC measurement feature

* `GC.measure_total_time = true/false` enables/disables total time measurement (default: true)
* `GC.measure_total_time` returns current flag.
* `GC.total_time` returns measured total time in nano seconds.
* `GC.stat(:time)` (and Hash) returns measured total time in milli seconds.

Conclusion:

* matz: leave it to @ko1

### [[Feature #18339]](https://bugs.ruby-lang.org/issues/18339) GVL instrumentation API (byroot)

* I'd like 3 new internal tracepoints: RUBY_INTERNAL_EVENT_GVL_ACQUIRE_ENTER, RUBY_INTERNAL_EVENT_GVL_ACQUIRE_EXIT, RUBY_INTERNAL_EVENT_GVL_RELEASE
* It would be also very useful if the number of waiting thread was part of the hook arguments.
* It would allow to instrument the GVL which is a key metric for threaded environments, and to tune concurrency in applications.
* Is the feature acceptable?
* Any performance concerns when no hook is registered?

Discussion:

* mame: People want to know how many threads are waiting for GVLs, to optimise their thread pool.
* mame: The optimization can be done by additional C level hooks to measure GVL acquisition.
* shyouhei: Are they C level only?
* mame: Yes, and they are the first ones in this category that run without GVL.
* mame: It could be dangerous to manipulate the list of hooks without GVL.