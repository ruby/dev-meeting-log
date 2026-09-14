# DevMeeting-2026-06-11

https://bugs.ruby-lang.org/issues/22088

## DateTime and location

* 2026/06/11 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2026/07/09 (Tue) 13:00-17:00 JST

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #22085]](https://bugs.ruby-lang.org/issues/22085) `String#to_f` and `Kernel#Float` shouldn't issue `Float out of range` warnings (byroot)

* This is a good warning when Ruby is parsing source code.
* But it's not really actionable when parsing user input.

#### Discussion:

* mame: Kernel#Float warns, but returns infinity nonetheless.
* matz: Does it warn always?
* mame: only when verbose.
* matz: why not then?  I understand it can be annoying if default on but
* hsbt: It is impossible for a program to know if the warning is issued or not, so it is not useful as it seems.

#### Conclusion:

* matz:  Let me ask why warnings are disliked here.


### [[Feature #22082]](https://bugs.ruby-lang.org/issues/22082) Introduce Bit Operations into String (hasumikin)

* I'd like you to discuss whether it's appropriate to add such functionality to the String class
* And if so, which methods should be added first

#### Discussion:

* mame: bit operations are proposed again.
* matz: it is not intuitive which API works how.  Do they need all of them?
* akr: Endian?
* mame: proposal have `lsb_first: true` parameters.
* akr: for all methods?  That sounds rather annoying.

#### Conclusion:

* String bit operations in general are not rejected.  But this particular set of APIs araise tons of questions.  We need to discuss on the bug tracker.

### [[Bug #22058]](https://bugs.ruby-lang.org/issues/22058) {Method,InstanceMethod}#super_method doesn't work correctly for refined method with refinements for method active in the caller's scope (shugo)

* Is it ok to keep the 2.7+ behavior of `super`? https://bugs.ruby-lang.org/issues/22058#note-13

#### Discussion:

* shugo: I talked this issue with jeremy.
* shugo: Now we concluded that `super` should take effect at the context of the `super` call site.

Conclusion:

* matz: OK.


### [[Feature #21998]](https://bugs.ruby-lang.org/issues/21998) Add {Method,UnboundMethod,Proc}#source_range (eregon)

* Are these tweaks OK? See https://bugs.ruby-lang.org/issues/21998#note-25
* PR is ready: https://github.com/ruby/ruby/pull/16835

#### Discussion:

* mame: eregon wants to amend his proposal.
* matz: It changed what we had discussed in Hakodate

#### Conclusion:

* matz: I want to confirm his intention.  Let me respond.

### [[Feature #19315]](https://bugs.ruby-lang.org/issues/19315) Lazy substrings in CRuby (himura467)

* I created a prototype implementation: https://github.com/ruby/ruby/pull/17045
* This is also a prerequisite for [Feature #22056].
* Proposed approach:
  * Keep `RSTRING_PTR()` behavior unchanged. This is essential for preserving compatibility with existing C extensions.
  * Introduce a new API (tentatively named `RSTRING_RAW_PTR()`) that returns a raw pointer without guaranteeing null termination and without triggering unsharing.
    * Zero-copy requires caller migration, but I consider compatibility the higher priority.
* Would like you to discuss:
  * Whether the no-GVL handling in `RSTRING_PTR()` is warranted (see https://bugs.ruby-lang.org/issues/19315#note-18)
  * How `RSTRING_PTR()` should handle null termination: permanently unshare, or allocate a read-only copy instead (see https://bugs.ruby-lang.org/issues/19315#note-23), with a possible restriction to frozen/`STR_TMPLOCK` strings only (see https://bugs.ruby-lang.org/issues/19315#note-27)
  * Whether `RSTRING_RAW_PTR()` is an appropriate name for the new API

#### Discussion:

* shyouhei: so this proposal extends sharable strings that already exist today,  and allow middle part be shared.
* mame: this broke before, but he claims he has a workaround.
* mame: it seems RSTRING_PTR allocates a memory region now.
* shyouhei: Sounds much like rb_str_modify()
* nobu: This change breaks lots of extension libraries
* shyouhei: and they should.  RSTRING_PTR has to be abandoned.
* mame: in theory I could agree with you but in practice it has to be a nightmere.
* akr: I think people rarely use raw pointers without GVL.
* ko1: I do some times...  It is too easy to call fopen(RSTRING_PTR(path), ...)
* nobu: I can give up SHARABLE_MIDDLE_SUBSTRING, for record.
* akr: I'd rather propose warning when RSTRING_PTR is issued without GVL.
* nobu: I also wonder if this change is worth.  Does it actually reduce memory footprint?  I know java thinks otherwise.

#### Conclusion:

* shyouhei: It seems some incompatibility (be they slow, SEGV, anything) is inevitable.  We need to find better trade-off point.
* ko1: At least we need quantitive measurements.

### [[Feature #22093]](https://bugs.ruby-lang.org/issues/22093) Introduce `Process::ID` for process IDs returned by `Process.spawn` and `fork` (nobu)

* Add integer-like class `Process::ID` which wraps a process ID.
* Open questions
  * Should `Process::ID` include `Comparable`, or only define `<=>`?
  * Should Process::ID#wait accept the same integer flags as existing wait APIs, provide keyword arguments, or support both?
  * Should `Process::ID#detach` simply be equivalent to `Process.detach(self)`?
  * Are there other process-related APIs that should return or preserve `Process::ID`?

#### Discussion:

* shyouhei: I want it start as a gem.
* nobu: why gem?
* shyouhei: rather I ask you why it should be in core?
* nobu: I want to change the Process.spawn return value.
* shyouhei: I guess you can start with Process::ID.spawn.
* mame: Why was Process a module rather than a Class?
* matz: I hesitated small objects back when I designed it.
* matz: Also I vaguely question whether process controlling operations shall be done in object oriented manner or not.
* akr: #inspect returns a meaningful message rather than a decimal number is a neat feature though.

#### Conclusion:

* matz: I would add my feeling.

### [[Feature #22067]](https://bugs.ruby-lang.org/issues/22067) New `RUBY_TYPED_THREAD_SAFE_FREE` bit to declare thread safe `dfree` functions (jhawthorn, luke-gru) (jhawthorn)

* Proposes an opt-in TypedData flag declaring a `dfree()` function thread-safe
* Allows GC implementations to free TypedData in parallel / concurrently with Ruby code or with Ractor-local GC
* Implies `RUBY_TYPED_FREE_IMMEDIATELY` (more strict for extension authors, more flexible for GC)
* Alternate name: `RUBY_TYPED_FREE_THREAD_SAFE`. Shares prefix with `RUBY_TYPED_FREE_IMMEDIATELY`, but reads more awkwardly.
* OK to add flag?

#### Discussion:

* ko1: This is a special subkind of RUBY_TYPED_FREE_IMMEDIATELY  that can run at literally _any_ moment.
* ko1: they want to use it for parallel sweep, I also want it for Ractor local GC.
* shyouhei: Does it mean the dfree has to be async signal safe?
* ko1: No, it just has to be thread safe.

#### Conclusion:

* matz: leave it up to ko1

### [[Feature #18915]](https://bugs.ruby-lang.org/issues/18915) New error class: NotImplementedYetError or scope change for NotImplementedError (koic)

* `NotImplementedError` has frequently been used for a purpose different from its intended role, namely to indicate that a method is expected to be implemented by a subclass. A new exception class with a name such as `SubclassResponsibility` (or `AbstractMethodError`) has been proposed for this use case.
* Since both the naming and inheritance hierarchy were still under discussion, I raised this topic at Matsue RubyKaigi 12 on June 6, 2026.
* `SubclassResponsibility` was considered a candidate for the class name.
* At Matsue RubyKaigi 12, I confirmed support for having the new exception class inherit from `ScriptError` (not `RuntimeError`).
* I have posted a summary of these discussions in an issue comment. Are there any remaining concerns that could block resolution?

Discussion:

* shyouhei: they basically request two things
  1.  NotImplementedError is a bad name, and
  2.  NotImplementedError being a subclass of ScriptError is a bad idea.
* matz: people tend to intentionally raise NotImplementedError by hand, to express that the method in question has to be implemeted in a subclass.
* matz: Also these days AIs learn such codes and tend to misuse it.
* matz: naming wise SubclassResponsibility or AbstractMethodError are both okay to me.
* ko1: Do we want to add `module Enumerable; def each=raise...` ?
* mame:  That breaks existing codes.
* nobu: anything other than not yet error is okay to me.

Conclusion:

* matz: I will decide

### [[Feature #22081]](https://bugs.ruby-lang.org/issues/22081) Core type definition migration from ruby/rbs to ruby/ruby (soutaro)

* Move RBS type definitions of core library from ruby/rbs to ruby/ruby
* Better developer experience for updating RBS files for core library including running tests

#### Discussion:

* soutaro:  I want ruby/ruby to ship type definitions.
* shyouhei: benefits?
* soutaro: I want core devs to write types, and that should become easy by this movement.
* hsbt: Do we _have to_ write types?
* soutaro: you can skip by rbs_skip_tests.
* nobu: How should types of default gems, that are in rbs/rbs now, be handled?
* soutaro: I guess I'll move them to gem_rbs_collection, or to individual repositories.
* hsbt: I guess a part of the problem here is that rbs runtime depends on rbs type signatures, which include yet-to-be-released core features, and that becomes a blocker.
* shyouhei: I guess it's acceptable to ship e.g. string.rbs with ruby, because it's rarely changed.  But bundling default gem's type signatures?  Can be too much.
* hsbt: what is the goal here?  Bundling can or cannot be a solution.
* soutaro: my goal is to let others write types.
* nobu:  then it might not be the direct answer.

#### Conclusion

* There are some problems:
   * Where are rbs files for default gems in?
   * What to do about the (duplicated) rdoc in rbs files?
   * (How about writing rbs-inline into rbinc?)

### [[Feature #22094]](https://bugs.ruby-lang.org/issues/22094) Speed up Array#join with a byte-copy fast path (yaroslavmarkin)

* Speed up ASCII or UTF-8 Array#join's of strings with fast copying

* nobu: the patch includes unneeded changes
* mame: the patch introduces complexity
* mame: what is needed is not a discussion but a review. Anyone who volunteers to review the patch?

---

### https://bugs.ruby-lang.org/issues/13677 Add more details to error "Name or service not known (SocketError)"
  * in the previous meeting, akr said he would reply

### https://bugs.ruby-lang.org/issues/21973 Smile argument

### https://bugs.ruby-lang.org/issues/21972 Add Date.birthday and Date.age to track Ruby's milestones

### https://bugs.ruby-lang.org/issues/21994 If there is a local variable `foo`, calls to a method `foo` with a regexp literal as first argument is always a SyntaxError without parentheses
  * matz: I would like to confirm this at the next dev meeting before changing it.
  * nobu: all operators including + and -?

```
$ ruby -We 'x +2'
-e:1: warning: ambiguous first argument; put parentheses or a space even after `+` operator

$ ruby -We 'x = 1; x +2'
-e:1: warning: '+' after local variable or literal is interpreted as binary operator even though it seems like unary operator

$ ruby -We 'x %w()'
-e:1:in '<main>': undefined method 'x' for main (NoMethodError)

$ ruby -We 'x = 1; x %w()'
-e:1: warning: '%' after local variable or literal is interpreted as binary operator even though it seems like string literal

$ ruby -We 'x = 1; x *2'
-e:1: warning: '*' after local variable or literal is interpreted as binary operator even though it seems like argument prefix

$ ruby -We 'x = 1; x &2'
-e:1: warning: '&' after local variable or literal is interpreted as binary operator even though it seems like argument prefix
```

* matz: It is ok to remove warning about '/'. Let me consider other operators

---

### https://bugs.ruby-lang.org/issues/22080 `Integer(obj, exception: false)` raises when `to_str` doesn't return String

```ruby
obj = Object.new
def obj.to_int = "1"
"abcde"[obj]
#=> in 'String#[]': can't convert Object into Integer (Object#to_int gives String) (TypeError)
```

### https://bugs.ruby-lang.org/issues/21720 Add a native Binary Heap / Priority Queue to Ruby's Standard Library (heapify, heappush, heappop)

* matz: I think gem is good enough for this

### https://bugs.ruby-lang.org/issues/17944 Remove Socket.gethostbyaddr and Socket.gethostbyname

* akr: ok
* matz: ok. I will reply
