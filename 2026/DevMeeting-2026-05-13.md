# DevMeeting-2026-05-13

https://bugs.ruby-lang.org/issues/21956

## DateTime and location

* 2026/05/13 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2026/06/11 (Tue) 15:00-17:00 JST

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21981]](https://bugs.ruby-lang.org/issues/21981) Remove CREF rewriting for methods on cloned classes/modules (jhawthorn)

* Current behaviour is inconsistent with all other constant references being lexically scoped (inheritance, mixins, define_method). Only `Class#{dup,clone}` has weird dynamic behaviour.
* I think it's rarely used, however it's a breaking change.

Discussion:

* mame: What is CREF _rewriting_?
* nobu:  I don't think I added this
* ko1: This is when a class is cloned.

```
class C
  A = 1
  def a; A; end
end

D = C.clone
D.const_set(:A, 2)

p C.new.a  # => 1
p D.new.a  # => 2 (should be 1? matz: 1 is acceptable)

class D
  def b; A; end # This should respect const_set(:A, 2)
end

p D.new.b  #=> 2 (matz: This behavior should be kept)
```

* matz: The proposal is acceptable as long as `D.new.b` returns 2
* ko1: For example, if user want to makes new class which changes only the value of constants, this clone technique can be used.
* matz: It seems dynamic scoping. In general, this kind of technique is bad idea. Constants are basically lexical scoping and it seems good idea to keep it.

Conclusion:

* matz: I will reply. I will accept. If some users are affected, I may reconsider

### [[Bug #22058]](https://bugs.ruby-lang.org/issues/22058) {Method,InstanceMethod}#super_method doesn't work correctly for refined method with refinements for method active in the caller's scope (jeremyevans0)

* I found semantic issues with #super_method for refined methods.
* For the current class, it uses refinements activated at the time the Method/InstanceMethod was created.
* For ancestors, it uses refinements activated in the scope calling #super_method.
* Can we define the expected semantics for #super_method for refined methods?

Discussion:

* shugo: repeatedly calling `#super_method` enters an infinite loop right now.
* shugo: a method's super method can have multiple choices when there are refinements.  This is the root cause.
* shugo: this behaviour changed since 2.7.  If we revert to pre 2.7, the fix is easy.
* matz: you think pre-2.7 behaviour is easier to understand, and fix?
* shugo: I think so.

Conclusion:

* matz: OK, let's revert the behaviout of `super` itself, rather fixing `#super_method`.

### [[Feature #22056]](https://bugs.ruby-lang.org/issues/22056) Add zero-copy String constructor backed by an arbitrary Ruby object (himura467)

* Propose adding `rb_enc_str_new_external` (and variants, names tentative): creates a String referencing existing memory without copying, with the GC keeping an arbitrary `parent` object alive
* To fully satisfy the use case described in #22056, this proposal alone is insufficient. Two additional concerns raised in that ticket also need to be addressed:

Discussion:

* shyouhei: does this fit into a ruby object?
* nobu: it does, if we can reuse an unused flag bit
* matz:  what can be the `parent`?
* nobu: arbitrary, and specifically `IO::Buffer` is in mind.
* matz: myself and @kou talked in person that it whould need non-NUL-terminated string.
* akr: I tried to abuse non-NUL-terminated strings before, but from my experience it was difficult.
* nobu: that's because I fixed issues.  Problems other 3rd-parties where I have not looked at.
* mamee: I also tried it but have the opposite feeling; I think I can abose it super easily when enabled.  careless use of strcat() can be think of.
* akr:  what happens if the "external" string is modified?
* nobu: it's CoW-ed.

Conclusion:

* nobu: At least we need to give up NUL-termination first, in order to add this feature.
* matz: Also ~~I don't think the API is mature enough.~~  OK, I know how it is designed now.
* matz:  The proposal itself is not an immediate NG though.  If situation allows there could be a room.

### [[Feature #22060]](https://bugs.ruby-lang.org/issues/22060) Improve Pathname by migrating internal methods to pathname.c (nobu)

* The performance of related methods becomes 2..4 times faster.
* [GH-16907](https://github.com/ruby/ruby/pull/16907)
* Currently `Pathname("a:.").absolute?` returns true on Windows.

Discussion:

* nobu: I have several speed ups.
* mame: eregon seems okay with it
* matz: me too.
* akr: me too.

Conclusion:

* nobu: no one seems against.

### [Feature #13677]: Add more details to error "Name or service not known (SocketError)"

Proposal PR adding the host into the error message
before: "getaddrinfo: Name or service not known (SocketError)"
after: "getaddrinfo 'invalid.host.com': Name or service not known (SocketError)"

Discussion:

* akr: because this is an error message for getaddrinfo we need also show the service name.

```
getaddrinfo: No such host is known. "unknownhost.example.com":80
```

* conclusion: akr: I will reply

### [Feature #21619]: logger: Context API
discussed in the dev meeting 5 months ago
no feedback from sonots nor tagomoris, asking for feedback again.

* There is no committer involved for logger in this meeting

## remaining agenda from the previous meeting

### [[Feature #21768]](https://bugs.ruby-lang.org/issues/21768) Remove some deprecated C APIs (byroot)

*  We have a lot of functions that have been deprecated for a very long time, could we cull some?
* Nobu had a proposed patch: https://github.com/ruby/ruby/pull/15447
* Ideally we'd do this before the first preview so that we can selectively re-introduce some of them if happens that they are used in the wild.

Conclusion:

* nobu:  I'm handling this.

### [[Feature #21963]](https://bugs.ruby-lang.org/issues/21963) A solution to completely avoid allocated-but-uninitialized objects (eregon)

* How about that solution? `Class#safe_initialization`/`rb_class_safe_initialization()` + `rb_copy_alloc_func_t()`?

Conclusion:

*  no conclusion, discussion continues in the BTS

### [[Misc #22000]](https://bugs.ruby-lang.org/issues/22000) Requesting to be a co-maintainer of ostruct (eregon)

* marcandre is in favor
* Could I get access on GitHub?
* And for pushing the gem?

### [[Misc #21922]](https://bugs.ruby-lang.org/issues/21922) Permissions for committers for ex-default/bundled/unbundled gems repositories (eregon)

* Let's discuss.

Conclusion:

* hsbt: I'm talking it with eregon.

### [[Misc #22001]](https://bugs.ruby-lang.org/issues/22001) Adding TruffleRuby in the CI of all default & bundled gems (eregon)

* Let's discuss.


### [[Bug #20409]](https://bugs.ruby-lang.org/issues/20409) END { break } (kddnewton)

* Can we please statically mark this as a syntax error? It always results in a runtime error.

Discussion:

* matz: yes.  Any problems?
* nobu: `END { next }` could be a separate issue.

Conclusion:

* matz: `END { next }` should also be prohibited.

### [[Feature #21795]](https://bugs.ruby-lang.org/issues/21795) Methods for retrieving ASTs (eregon)

* Would love to make progress on this.
* How about the idea to use start line/column + end line/column (or equivalently, start & end offsets)?
* It would avoid issues with using different Prism versions, be simpler and more portable.

Discussion:

*

Conclusion:

*

### [[Feature #21998]](https://bugs.ruby-lang.org/issues/21998) Add {Method,UnboundMethod,Proc}#source_range (eregon)

* Add {Method,UnboundMethod,Proc}#source_range, returns Ruby::SourceRange with methods:
* `start_line`, `end_line`, `start_column` (or `start_byte_column`), `end_column` (or `end_byte_column`), `inspect`.
* OK?

---

https://bugs.ruby-lang.org/issues/21973 Smile argument

```
def foo(x, :)
```
https://bugs.ruby-lang.org/issues/21972 Add Date.birthday and Date.age to track Ruby's milestones
https://bugs.ruby-lang.org/issues/21976 Add `$SECONDS`, `$RANDOM`, and other bashisms

https://bugs.ruby-lang.org/issues/21640 Core Pathname is missing 3 methods / is partially-defined
https://bugs.ruby-lang.org/issues/21982 Add `Decimal` as a core numeric class
https://bugs.ruby-lang.org/issues/21987 Assume `chdir(2)` isn't called and cache `rb_dir_getwd_ospath()`

* akr: How about introducing `Dir.pwd(cached: true)`, and `Dir.chdir` should invalidate the cache

https://bugs.ruby-lang.org/issues/21994 If there is a local variable `foo`, calls to a method `foo` with a regexp literal as first argument is always a SyntaxError without parentheses
```
# FYI: vscode's highlight
rule /foo      # div X
rule / foo     # div
rule / foo /   # regexp X
rule / foo /x  # regexp X
rule / foo / x # div

range.right-1
range.left -1
```

https://bugs.ruby-lang.org/issues/22007 Inconsistent type checking on rescue
https://bugs.ruby-lang.org/issues/22012 Data class should respond to #dig
https://bugs.ruby-lang.org/issues/22011 Hash tables with swiss table
https://bugs.ruby-lang.org/issues/21943 Add StringScanner#get_int to extract capture group as Integer without intermediate String
