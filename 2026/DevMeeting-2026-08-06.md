# DevMeeting-2026-08-06

https://bugs.ruby-lang.org/issues/22221

## DateTime and location

* 9/17 is EuRuKo
* 2026/09/10 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2026/09/10 (Tue) 13:00-17:00 JST

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #22213]](https://bugs.ruby-lang.org/issues/22213) Allow no-argument and chained calls of Proc#refined (shugo)

* A no-argument call returns the receiver itself, and a chained call returns a Proc with additional refinements: `prc.refined(*ms).refined(*ns)` is equivalent to `prc.refined(*ms, *ns)`
* The copy of the block is deferred until the first call, so a Proc that is never called is never copied, and a chained call is memoized like a single call with all the modules
* I'd like matz to approve this behavior

#### Discussion:

* shugo: this feature was merged, but the situation when no arguments are passed are not defined yet.
* matz: does `refined.refined.redined....` create many intermediate copies?
* shugo: understand the concern, I don't want that too.
* shugo: I'd like to delay `proc.refined.refined.refined.call()` to actually refine the proc at `call` timing.
* shugo: this can change the behaviour when `p=proc.refined ...; module.refine; p.call`, though
* matz: that's acceptable.

#### Conclusion:

* matz: OK, accepted.


### [[Feature #22205]](https://bugs.ruby-lang.org/issues/22205) Deprecate ruby2_keywords (shugo)

* I'd like to deprecate and eventually remove ruby2_keywords; feedback on the ticket is positive, and the proposed schedule is in the description
* Should Hash.ruby2_keywords_hash? and Hash.ruby2_keywords_hash be deprecated one phase later than the marking methods (they are needed as long as flagged hashes exist), or on the same schedule?

#### Discussion:

* shugo: I noticed this feature is an ISeq flag, which prevents ISeqs to be sharable among ractors.
* shugo: rather than maintaining the feature I guess it's a good timing to delete it.
* matz: sounds nice
* shugo: I want to discuss the deprecation/deletion timeline. (describes the timeline shown in the ticket)
* shyouhei: it's nice Hash#ruby2_keywords_hash returns a no-op copy.
* mame: jeremy has his library that still support 1.9, so he needs some time until removal.

#### Conclusion:

* matz: Accepted.


### [[Feature #9779]](https://bugs.ruby-lang.org/issues/9779) Add Module#descendants (shugo)

* Module#descendants returns the classes and modules that inherit or include the receiver
* Is it OK to add it to Module, not only Class?
* Is it OK that the return value does not include self?

#### Preliminary discussion:

* ko1: the internal relations from a module to included classess are managed, for the following specification:

```ruby
module M
end

class C
  include M
end

module M
  def m = :M
end

p C.new.m
```

#### Discussion:

* shugo: There was a discussion about `Class#decendants`, which once was merged, but reverted eventually.
* ko1: we have Class#subclasses though.
* shugo: ActiveSupport has Class#decendants.
* mame: Do you think it's more natural for modules to have this method than class, shugo?
* shugo: Yes.  My use case is "pointcut" in aspect-oriented programming.
* ko1: Do we still need this fature after having Class#subclasses ?  Is there something that cannot be done by the method?
* shugo: Class#subclasses could not be something what a customer wanted to begin with?
* mame: Rails uses it though.
* matz: I'm not super aganist it, may not use it myself though.
* shugo: (me neither actually)
* matz: But it's okay
* shugo: Another POV is that this is harder to implement in jruby than cruby.
* eregon:  They now have links to ancestors/decendants. we don't have to worry about that. (necessary with recent Module#include semantics impacting all classes/modules including a given a module M when including a new module in M). JRuby has `RubyModule#includingHierarchies` which is that, used for both method cache invalidation  & transitive `include`/`prepend` propagation.
* nobu: I read the ActiveSupport implementation.  It could be too much for Module#descendants to retuen _everything_ that's reachable?  I guess direct subclasses can be enough for some situations.
* nobu: Also I'm not sure if we also need Module#descendant_classes that only selects classes, etc.
* jhawthorn:  What happens when there are multiple Boxes?  Because a modules can be inclided in different classes for different boxes.

```ruby
module M
end
box = Ruby::Box.new
box::M = M
box.eval(<<-END)
  class String
    include M
  end
  class MyClass
    include M
  end
END
M.descendants #=> [String, (box::)MyClass]? or []?
              # matz: [] or [(box::)MyClass]
# but String < M == false in current Box
box::MyClass.included_modules #=> [M]

class Parent
end
box = Ruby::Box.new
box::C = Parent
box.eval(<<-END)
  class Child < C
  end
END
p Parent.subclasses  #=> current: [box::Child]
                     # matz: no strong opinion [box::Child] or []
```

* eregon: maybe `subclasses` & `descendants` should only return those that are in the current Box? 
* eregon: What if `String` includes `Enumerable` in some Box but not in some other Box though? (for `Enumerable.descendants`)

#### Conclusion:

* matz: Let me dump what I think about the box behaviour to the bug tracker.
* matz: The feature itself is on a way to be accepted.

### [[Feature #22132]](https://bugs.ruby-lang.org/issues/22132) Scala-like for comprehensions (shugo)

* At the previous dev meeting, matz said he was interested and wanted to take time to consider it
* Does matz have any opinions or questions at this point?

#### Discussion:

* shugo: WDYT matz?
* matz: still thinking about it...

#### Conclusion:

* matz: let me consider.


### [[Feature #22212]](https://bugs.ruby-lang.org/issues/22212) Add Thread::Backtrace::Location#source_range (eregon)

* Proposes a portable API returning a `Ruby::SourceRange` for an exception's `backtrace_locations` or for `caller_locations`.
* This gives tools stable start/end line/column coordinates without version-specific `node_id` values ([examples of `node_id` instability](https://bugs.ruby-lang.org/issues/21795#note-32)).
* Examples which could use this API instead of currently the CRuby-specific `RubyVM::AST.node_id_for_backtrace_location`, and therefore work reliably on TruffleRuby/JRuby: `Prism.find(Thread::Backtrace::Location)`, [`Rails` exception printing](https://github.com/rails/rails/blob/edf8f12a9f54a476986443070f3b07f300d0955b/actionview/lib/action_view/template.rb#L253), `error_highlight`, `power_assert`.
* It does not require the Prism gem or create `Prism::Node` objects, separating source-code location from parsing that code into an AST.
* The [implementation](https://github.com/ruby/ruby/pull/18043) reparses on demand to avoid persistent memory overhead, uses source-hash validation based on @mame 's work, and supports `RubyVM.keep_script_lines = true` to avoid re-reading files from disk.
* Extensive specs ensure Prism and parse.y return identical user-facing coordinates, including for blocks.
* I am looking for matz's approval for the new method.

#### Preliminary discussion:

* ko1: Backtrace::Location#source_range cannot work in some cases (modify/remove source file). Is it okay?

#### Discussion:

* ko1: for instance line number is always returned even if its source code is modified.  Is it acceptable for source ranges to return nil?  otherwise we have to retail much more than now.
* matz: I guess that's acceptable.
* eregon: ^ this is detected by the source hash, so this raises a RuntimeError like "source has been modified/deleted/not available"
* mame: I have no plan to use this feature in error_hightlight.

#### Conclusion:

* matz: I think we can have both `Thread::Backtrace::Location#syntax_tree` and this `Thread::Backtrace::Location#source_range`.
* matz: I will write a reply to the ticket

### [[Bug #22197]](https://bugs.ruby-lang.org/issues/22197) Backtraces show methods which do not exist (eregon)

* As the title says, backtraces sometimes list methods which do not exist, because of mixing definition name with run-time owner (`Method#owner`).
* Since we print file, line and method name from the original definition (`def`), I think we should show the original module too.
* Let's fix it? PR: https://github.com/ruby/ruby/pull/17963

#### Preliminary discussion:

* ko1: The PR adds a new field to method struct. I want to avoid it if possible

#### Discussion:

* shyouhei: I think it's nice.
* matz: alias is rare, but keeping super method as alias for later use is a common use case.
* ko1: fixing it is welcome. but this particular patch consumes more memory.
* mame: memory consumption is not a big deal to me.
* eregon: the first commit has no extra field, but logic is very complicated and might not be correct in all cases. It's also slower due to extra checks. The performance of this matters for profilers.

#### Conclusion:

* matz: I consider this a bug.
* matz: Merge it.  If this is complained we might have memory optimisations later.


### [[Feature #22118]](https://bugs.ruby-lang.org/issues/22118) Introduce Basic Bit Operations into String (hasumikin)

- https://bugs.ruby-lang.org/issues/22118#note-6
  - Is it OK to have a *temporary* asymmetry where `String#bit_count` doesn't take a bit-order keyword?
  - Is it OK to raise an `ArgumentError` for a bit offset size that cannot be represented internally?

#### Preliminary discussion:

* `str.bit_count(lsb_first: true) #=> unknown keyword` ok?
* `str.bit_get(2<<100) #=> ArgumentError` ok?

#### Discussion:

* mame: can you respond to this ticket @akr ?
* mame: the OP wants to small start, then have another feature request immediately.
* matz: re: `bit_get` vs bigint, is this behaviour consistent with the case when that argument is just out of bounds?
* mame: No (IndexError raised instead for OOB situations), but ArgumentError is acceptable to me.

#### Conclusion:

* matz: Accepted.


### [[Feature #22222]](https://bugs.ruby-lang.org/issues/22222) Expose a C API equivalent of `RubyVM::InstructionSequence.load_from_binary` (byroot)

* It would allow Bootsnap to directly load iseq from C without copying bytes into a string.
* `rb_str_new_static` exists but is impractical and dangerous.

#### Preliminary discussion:

* ko1: Approved.

#### Discussion:

* mame: any further discussions?

#### Conclusion:

* no objection

### [[Feature #22226]](https://bugs.ruby-lang.org/issues/22226) Ractor: class/module ownership -- restrict modification to the Ractor that created it (ko1)

* This proposal reduce the exceptions. In most case, there are no problem.

#### Preliminary discussion:

* mame: The spec look a bit complicated to me. How about prohibiting creating a new class in sub-Ractors completely?
* ko1: I think it is too strict

#### Discussion:

* ko1: You cannot assign an instance variable for a class inside of a Ractor today, because when I designed Ractor it was too complicated to do.
* ko1: Now I think it's okay to allow that operation, as long as no ractor boundary is violated.
* matz:  Where do the info get stored?
* ko1: a class remembers its hometown.
* ko1: I guess nobody seriously defines a method inside of a Ractor.  However there can be situations when anonymous classes are made on-the-fly.  I think the proposed specification is natural for that.
* matz: `Ractor.new { class Foo; end }` defines a global toplevel Foo.  Is that allowed?
* ko1: Yes, other Ractors can look at Foo, but the Ractor that created Foo is the only one that can modify Foo.
* matz: can this also work for @@ class variables as well?
* ko1: there are inevitable race conditions:

```ruby
class C0; end

Ractor.new{
  class C1 < C0; end
  C1.@@cv = 1
}
Ractor.new{
  class C2 < C1
    C2.@@cv = 2 # 実は C1.@@cv に書き込むのでエラー
  end
}
```

* eregon: the proposal description says `Ractor.new { class TopCls; end }.value` raises an exception, but it seems that's not the desired behavior?

#### Conclusion:

* matz: accepted, with supporting the class variables.

### [[Feature #22227]](https://bugs.ruby-lang.org/issues/22227) Per-Ractor GC: collect each Ractor's heap locally, stop the world only when needed (ko1)

* There are several behavior changes, so I want to clear it is acceptable or not.

#### Preliminary discussion:

* ko1: I did it.

#### Discussion:

* ko1: claude code did it actually.
* shyouhei: puma benchmark seems slower a bit.  maybe overhead?
* ko1: maybe.  marking could be slower only a fraction of time.
* ko1: memory consumption is smaller than forking process though.
* shyouhei: Not sure if the ~7% slowdown for non-ractor scenario is acceptable for Rails applications.

#### Conclusion:

* matz: looks good, merge it and optimize it.

### [[Bug #18947]](https://bugs.ruby-lang.org/issues/18947) Unexpected `Errno::ENAMETOOLONG` on Windows (hsbt)

* This fix resolved the real-world issue. But my fix used undocumented feature. Is it okay?

#### Discussion:

* hsbt: current situation: in case of windows file paths longer than 260 characters are not supported today.
* hsbt: however golang supports longer paths, how?
* hsbt: it seems there are hidden Windows kernel system call that enables this feature.
* hsbt: is it okay for us to do this as well?
* akr: is this windows 10 only?
* hsbt: it seems the API is windows 10 1703.  The proposed implementation checks if the current windows version works or not.
* hsbt: There could be discussions whether libruby.dll could also support this.  If we do so, just linking that DLL could change process-global behaviour. 

#### Conclusion:

* nobody is against it.


### [[Bug #19378]](https://bugs.ruby-lang.org/issues/19378) Windows: Use less syscalls for faster require of big gems (hsbt)

* Is it okay to merge? Is there any objection?

#### Discussion:

* hsbt: this is also support from Windows 11 23H2 
* shyouhei: I guess there is no reason to avoid this?

#### Conclusion:

* nobody is against it.


### [[Bug #22216]](https://bugs.ruby-lang.org/issues/22216) Special variables (ex. Regexp backref and IO lastline) are thread-unsafe in some cases, incompatible with Ractor (jhawthorn)

* Are we okay making this either thread-local or fiber-local? It's already Fiber-local at the top-level (only inside of the `Thread.new {...}` or `Fiber.new {...}` block)
* Do we want this Fiber-local (my preference, but more potential compatibility issues), or Thread-local
* Prototype implementation https://github.com/ruby/ruby/pull/18200

#### Discussion:

* ko1: `$~`, `$_` etc., are not currently thread local etc.

```ruby

def mk
  /a/ =~ 'a' # $~ = MatchData(a)
  proc{
    p $~
    /b/ =~ 'b' # $~ = MatchData(b)
  }
end

$pr = mk
Fiber.new{
  $pr.call #=> MatchData(a)
}.resume
```

* shyouhei: This reminds me of https://bugs.ruby-lang.org/issues/17507 / https://bugs.ruby-lang.org/issues/20652 .
* ko1: Is it okay for those variables be fiber local?
* ko1: Enumerable#lazy creates fiber behind the scene, which could lead to surprising behavior.
* akr: I think that's acceptable.
* eregon: +1 for Fiber-local, TruffleRuby already behaves this way for many years, no compatibility issue ever reported about this (IIRC)
* matz: I want to make it clear which variables are considered here and which aren't.
* ko1: `$~` and `$_`.

#### Conclusion:

* matz: okay, accepted.

### [[Feature #22215]](https://bugs.ruby-lang.org/issues/22215) Introduce narrow internal interfaces for bundled extensions (nobu)
  * Add narrow internal interfaces for the bundled extensions
    * `internal/coverage.h`
    * `internal/objspace.h`
  * Remove the transitive inclusion of `vm_core.h` from `internal/gc.h`

#### Conclusion:

* shyouhei: this is an implementation detail that you can freely modfy at will.

### [[Feature #22068]](https://bugs.ruby-lang.org/issues/22068) Adding post-quantum cryptography (PQC) support across Ruby standard libraries (jaruga)

* I explain the feature with my motivations, and answer questions from other developers.
* I want to share how to proceed for possible target libraries to modify: ruby/rubygems, bundler, ruby/net-http, ruby/open-uri, ruby/spec, ruby/drb, ruby/rbs.

#### Discussion:

* jaruga: I'm working on this topic.  I want to share what I do today.
* jaruga: My motivation here is that we want to prevent HNDL , attacks, which are possible today.
* jaruga: Harvest now decrypt later attack for key exchange.
* jaruga: ML-KEM, ML-DSA, SLH-DSA
* jaruga: ruby-openssl already has this feature. other builtin/bundled libraries need also be updated, like adding tests for PQC.
* duerst: I think it's too optimistic for quantum computers to be available for consumers in a few years. But quantum computers could be available for big companies and other organizations quite soon.
* duerst: IETF standardize these PQC algorithms and there are discussions, like ML-KEM could be suboptimal.  PQC is not the sole solution to future cryptographic catastrophy.  I guess we should combine use of other methods with them. (Later clarification: I was talking about non-hybrid ML-KEM (only using a PQC algorithm) and hybrid-mode ML-KEM (a PQC algorithm combined with a 'classical' algorithm for a single encryption,...)).
* jaurga: Merkle Tree Certificate (MTC) - https://blog.google/security/cultivating-a-robust-and-efficient-quantum-safe-https/, https://datatracker.ietf.org/doc/draft-ietf-plants-merkle-tree-certs/ (draft). The future of ML-DSA is public CA or MTC-based CA.
* jaruga: Schedule:  https://media.defense.gov/2025/May/30/2003728741/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS.PDF
  page 6 - Other requirements for NSS, CNSA 2.0 Timeline  by 2025 or 2030. My schedule is until the end of 2026.
* jaruga: Requirements: https://media.defense.gov/2022/Sep/07/2003071836/-1/-1/0/CSI_CNSA_2.0_FAQ_.PDF
  page 19 - ML-KEM-1024, ML-DSA-87 may be required
* jaruga: Ask Sutou Kouhei about ruby/drb review.

#### Conclusion:

* matz: go ahead

5 more tickets from https://bugs.ruby-lang.org/issues/22221#note-12

### [[Bug #22200]](https://bugs.ruby-lang.org/issues/22200) ObjectSpace._id2ref can return a different object than the id's owner on Ruby 4.0 (stale id2ref_tbl entry for objects with generic fields) (eregon)

* `_id2ref` is broken on Ruby 4.0, it returns internal objects, corrupt objects, and doing anything on them might segfault.
* We have seen many crashes caused by this in the `datadog` gem. They all happen on Ruby 4.0, no issue on Ruby 3.x & 2.x.
* `_id2ref` has been removed in Ruby 4.1, but nevertheless I believe we should fix this (or remove it in 4.0 but that's quite incompatible).
* Maybe we could use the older implementation for `_id2ref`, as in Ruby 3.4 and earlier? That seems safer.
* There is possibly a more direct fix if that's preferred.
* UPDATE: talking to @byroot he said reverting isn't really an option. Too big of a change for a backport, and would impact Ractor perf.
* UPDATE: I will try to find a fix.

#### Discussion:

eregon: I think nothing to discuss in the meeting actually, we just need to find the right fix.
eregon: PR at https://github.com/ruby/ruby/pull/18209 I will check the review.

### [[Feature #21826]](https://bugs.ruby-lang.org/issues/21826) Deprecating RubyVM::AbstractSyntaxTree (eregon)

* How about https://github.com/ruby/ruby/pull/18042 ? (documentation-only change)

#### Discussion:

* matz: okay.


### [[Misc #22230]](https://bugs.ruby-lang.org/issues/22230) Should Ruby specs define byte oriented read behavior when the character buffer is not empty? (eregon)

* Is the `byte oriented read for character buffered IO (IOError)` behavior intentional, or an implementation detail of CRuby?
* This edge case means a lot of complexity and the addition of a "character buffer" just for this (in addition to the byte buffer). It's unclear to me if that should be spec'd, I'm currently leaning towards no.
* Do we envision this changing in the future for CRuby? Maybe by doing the reverse conversion on `ungetc` and if that fails raise an error? It would be a nice simplification IMO, and only change behavior in very weird (possibly invalid) edge cases.
* `StringIO` doesn't have this behavior.

#### Discussion:

* akr: we can discuss changes related to `ungetc` on specific tickets (note by eregon)
* eregon: OK let's do that

### [[Feature #22231]](https://bugs.ruby-lang.org/issues/22231) Add `IO::Buffer#index` (himura467)

* `IO::Buffer` has no search primitive, so finding a delimiter means copying the region out with `#get_string` or looping over `#get_value(:U8)` in Ruby. Proposes `buffer.index(object, offset = 0, length = size - offset)`, where `object` is an `Integer` byte, a `String`, or another `IO::Buffer`
* Should an out-of-range `offset`/`length` raise `ArgumentError` (as the most of `IO::Buffer` range methods do) or return `nil` (as `String#index` does)?
* Should an `Integer` outside `0..255` raise, or be masked as `#clear` and `String#setbyte` do?

#### Conclusion:

* matz: I am okay if Samuel is okay

### [[Feature #22186]](https://bugs.ruby-lang.org/issues/22186) Increase the embeddable size limit for substrings created by `str_subseq()` (himura467)

* `str_subseq()` copies a sharable substring only while it fits in the tiny slot. The proposal copies up to a 128 byte slot, keeping the current cutoff when the source is frozen or already shared.
* The cutoff is heuristic. Is it acceptable?
* PR: https://github.com/ruby/ruby/pull/17723

#### Discussion:

---

### https://bugs.ruby-lang.org/issues/17056 `Array#index`: Allow specifying the position to start search as in `String#index`
### https://bugs.ruby-lang.org/issues/22105 Cannot initialize a `WeakRef` in a `Ractor`
### https://bugs.ruby-lang.org/issues/21795 Methods for retrieving ASTs
### https://bugs.ruby-lang.org/issues/22229 Allow `GCI.escapeHTML` to take a custom escape table

* matz: I don't want to make `CGI.escapeHTML` to support this feature because it says "CGI"

```
'Hello </script>'.gsub(">" => '\u003e', "<" => '\u003c', "&" => '\u0026')
'Hello </script>'.gsub_hash(">" => '\u003e', "<" => '\u003c', "&" => '\u0026')
'Hello </script>'.tr("><&", ">" => '\u003e', "<" => '\u003c', "&" => '\u0026')
'Hello </script>'.tr(">" => '\u003e', "<" => '\u003c', "&" => '\u0026') # matz: OK
'Hello </script>'.tr("abc" => 'ABC') # should raise an exception
'Hello </script>'.tr("abc" => 'ABC', "ab" => "XY")
'Hello </script>'.tr("><&", ['\u003e', '\u003c', '\u0026'])
"fée".tr("é" => "€") # it should work

"Hello".tr("l" => "ABC", "o" => "XYZ") #=> "HeABCABCXYZ"

"Hello".gsub(/./m) { hash[$&] || $& } # generic case
```

* matz: I will counter-propose String#tr-with-hash style.
* eregon:  `tr`  is very much about build-a-escape-table-at-runtime (and maybe try inline caching it in some cases) so it seems a good fit for this
