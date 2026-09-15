# DevMeeting-2026-09-10

https://bugs.ruby-lang.org/issues/22249

## DateTime and location

* 2026/09/10 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2026/10/07 (Wed) 13:00-17:00 JST

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #22232]](https://bugs.ruby-lang.org/issues/22232) Deprecate `RHASH_TBL` and associated APIs (byroot)

* I would like to deprecate `RHASH_TBL`, `RHASH_TBL_RAW` and `rb_hash_bulk_insert_into_st_table`.
* It's a dangerous API because it allow bypassing write barriers
* It restrict evolution of `RHash`, as we must always be able to convert a Hash into a public `st_table`.
* gem-codesearch only revealed very old and low download gems.
* All codesearch results have an equivalent `rb_hash_*` API, proving `RHASH_TBL` is useless.

#### Discussion:

* mame: needs?
* shyouhei: there could be many optimisation opportunities if we could get rid of RHASH_TBL.
* shyouhei: one technical difficulty is it's a macro.  It's hard for us to deprecate a C macro.
* ko1: Let's make it an inline function.  We can deprecate then.

#### Conclusion:

* nobu:  Let's try making it an inline function in Preview 1.


### [[Feature #22236]](https://bugs.ruby-lang.org/issues/22236) New API for adjustable JIT warmup (byroot)

* JIT warmup can be a challenge in production.
* After a deploy JIT warmup can cause high latency, so it may need to be tuned to avoid timeouts, queuing, etc.
* Current tuning parameters like `--yjit-call-threshold` are hard to reason about, imprecise and need frequent update as the application changes.
* I'd like YJIT, and potentially ZJIT to have an adjustable `max_compile_time_ns`.
* When `RubyVM::YJIT.runtime_stats[:compile_time_ns]` goes over `max_compile_time_ns` stop compiling.
* When `max_compile_time_ns` is assigned to a value higher than current `compile_time_ns` re-enable compilation.
* It only need to be best effort.
* See ticket for example usage.

#### Preliminary discussion:

* mame: I don't think there is any committer in the meeting who can handle this

#### Discussion:

(as mame noted attendees are not familiar with this)

#### Conclusion:

leave it to k0kubun


### [[Feature #13677]](https://bugs.ruby-lang.org/issues/13677) Add hostname to "Name or service not known (SocketError)" (chucke)

* PR open in github: https://github.com/ruby/ruby/pull/16918
* Changed message format as per suggestion in https://bugs.ruby-lang.org/issues/13677#note-12

#### Preliminary discussion:

* mame: I can understand how it was fixed

```
Before: getaddrinfo: Name or service not known (Socket::ResolutionError)
After: getaddrinfo: Name or service not known "unknown.domain" (Socket::ResolutionError)
```

#### Discussion:

* akr: is the host name `inspect`-ed?
* nobu: seems not.
* akr: considering it could include arbitrary binary patterns it would be safer to use inspect instead.

#### Conclusion:

* akr: feature wise it seems OK, just fix the inspect issue.

### [[Feature #21619]](https://bugs.ruby-lang.org/issues/21619) logger Context API (chucke)

* Putting it back here for lack of feedback last time around.
* PR in github: https://github.com/ruby/logger/pull/132 . Mostly ready, main contentious point being whether the context store should be configurable or not.

#### Discussion:

*

#### Conclusion:

*


### [[Feature #22056]](https://bugs.ruby-lang.org/issues/22056) Zero-Copy String Constructor Backed by Arbitrary Ruby Object (himura467)

* A C API creating a String that references memory owned by another Ruby object instead of copying it.
* It was blocked on the `RSTRING_PTR()` NUL-termination invariant (https://bugs.ruby-lang.org/issues/22056#note-19); https://bugs.ruby-lang.org/issues/22056#note-20 proposes that only new opt-in APIs produce Strings that are not NUL-terminated.
* Would like to ask whether this direction is acceptable, and whether the C API can proceed on it.

#### Preliminary discussion:

* mame: I don't understand how a new opt-in API solves the concern. The concern is that there IS a non-NUL-terminated String in Ruby object space.

#### Discussion:

* mame: I suspect the main purpose is IO::Buffer.
* ko1: or making a string from another string.
* mame: non-NUL-terminated strings would be hard to accept.  This new API seems not work as intended.
* matz: The intention of accepting "arbitraty" ruby object as its source is not clear to me.
* matz: The reason why this should be a String is also not clear to me.  Why should  an IO::Buffer's slice be string, other than IO::Buffer itself?
* ko1: API wise, what happens when the source IO::Buffer shrunk after the shared String is made?
* nobu: `IO::Buffer#slice` can be made invalid then.
* ko1: that's possible only because IO::Buffer is expected.  This request is for "arbitrary" ruby object.  Achieving this is difficult then.

#### Conclusion:

* ko1: We lean towards forbidding non-NUL-terminated string instances.

### [[Feature #22279]](https://bugs.ruby-lang.org/issues/22279) Region (Length / Range) Arguments for String Bit Operations (hasumikin)
  * Follow-up to [Feature #22118] (String bit operations, accepted): adds `(offset, length)` and Range overloads to `bit_set` / `bit_clear` / `bit_flip` / `bit_count`.
  * Semantics follow #22118: mutations raise `IndexError` on any overrun without modifying bits, `bit_count` clamps and returns `0` for an empty intersection, negative offset is `IndexError`, negative length is `ArgumentError`.
  * Points I'd like feedback on: (1) `bit_count(offset)` with a lone offset raises `ArgumentError` rather than meaning "to the end"; (2) an empty region for mutations still requires `offset <= bit_size`, mirroring `"abc"[4, 0]` being `nil`.

#### Discussion:

* mame: `lsb_first: true` is already accepted.
* shyouhei: is the `offset` affected bt the endiannness? Should lsb_first: false mean offset should be counted MSB first?
* akr: that's theoretically possible but in reality nobody practically needs such feature.
* nobu: why is negative index prohibited?
* shyouhei: maybe also not needed.
* nobu: I can think of "clear bits _except_ several trailing bits".

#### Conclusion:

* matz: accepted

### [[Bug #22276]](https://bugs.ruby-lang.org/issues/22276) alias in a module falls back to `Object` even in classes not inheriting from `Object` (shugo)

* `alias` in a module binds Object's method at alias time, so it is broken in classes not inheriting from Object; this assumes Object is the root class, which is not true since 1.9.
* I propose to check only the method existence with Object and resolve the alias at call time, like ZSUPER methods. PR: https://github.com/ruby/ruby/pull/18553
* Is this change acceptable?


#### Discussion:

* shugo:  Could be intentional, but I find it interesting.
* shugo: Would be nice if we forbid this behaviour, but that could break existing programs
* ko1: Do you know any such breakage?
* shugo: not in practice.
* akr: How a programmer mitigates if we are going to break this?
* shugo: Try this:
    ```ruby
    module M
      def foo(...) = puts(...)
    end
    ```
```ruby
class C
  prepend(Module.new do
    def foo = super
  end)
end
class C
  alias bar foo
  def foo
    bar
  end
end
```
* shugo: just deleting this feature is the best.
* matz: Yes, but could break things...
* shugo: Issuing a warning for such alias is an option.
* mame: this is practically a problem when people alias `alias rrr require` in a module.
* ko1:
    * object_id
       * https://github.com/crapooze/welo/blob/master/lib/welo/core/resource.rb#L360C16-L360C25
   * binding
       * https://github.com/pyrmont/taipo/blob/master/lib/taipo/check.rb#L32
    * raise
        * erratum-4.1.0/lib/erratum.rb (github 404)
    
#### Conclusion:

* matz: let me consider

### [[Bug #22273]](https://bugs.ruby-lang.org/issues/22273) Aliasing doesn't interact well with `Module#prepend` (jeremyevans0)

* Currently, you can an alias of a method in a prepended module.
* This allows `super` to call into a descendant instead of an ancestor, from the perspective of the method calling `super`.
* I think this should be rejected. `alias`/`alias_method` should only consider ancestor methods, not descendant methods, so they should not consider methods in prepended modules.

#### Conclusion

* matz: let me consider

### [[Misc #22272]](https://bugs.ruby-lang.org/issues/22272) Propose Kevin Menard (@nirvdrum) as a core committer (tekknolagi)

* I am proposing we make Kevin Menard, a long-time and frequent contributor, a committer.
* This will also help ZJIT: it would be nice if he could merge PRs.

#### Discussion:

* matz: Sounds legit!
* matz: Welcome to the core committer.

#### Conclusion:

* hsbt: Let me contact.

### [[Bug #22291]](https://bugs.ruby-lang.org/issues/22291) Instance variables should be forbidden on Ractor-shareable objects (jhawthorn)

* Currently reading an ivar from a Ractor-shareable, but **not frozen**, object is only allowed on the main Ractor. But freezing these objects then incorrectly allows other Ractors to read the (possibly Ractor-unsafe) ivars.
* I propose we forbid setting instance variables on shareable Ractor objects.
* Currently, Ractor, ENV, Shareable proc will forbid setting instance variables (no change to Class/Module which have special rules)
* Is this acceptable?

#### Discussion:

* jhawthorn: e.g. Ractors themselves and `ENV` are not frozen, yet sharable.
* jhawthorn:  why not just forbid instance variables?
* shyouhei: what specifically does "forbid" mean?
* jhawthorn: just these objects are not able to set instance variables, ever.
* ko1: not only setting but we should also reading them.
* jhawthorn: isn't it it safe to return nil always?  Reading an instance variables are rarely expected to raise.

```ruby
a = []
a.instance_variable_set(:@foo, 1)
Ractor.make_shareable(a)

a.instance_variable_get(:@foo)    # it is allowed
a.instance_variable_set(:@bar, 2) # it is prohibited
```

#### Conclusion:

* matz: I see no problem. 
* ko1: go ahead.

---- 

### [[Bug #22216:](https://bugs.ruby-lang.org/issues/22216) Special variables (ex. Regexp backref and IO lastline) are thread-unsafe in some cases, incompatible with Ractor

* ko1: matz agrees with headius' request for making svars fiber-local
* matz: yes
* ko1: issues found for letting them fiber-local, esp. when enumerator is involved.
* matz: Agreed, and that's not a desired behaviour.  I don't want to break current enum behaviours.
* mame: that's a contradiction.
* matz: Yes...
* ko1: Fibers are blocking or non-blocking.
* jhawthorn: They are non-blocking at birth, but later you can flip it using `Fiber.blocking`
* ko1: That's not a fluent API...

```ruby
def report
  access_log = [
    %{127.0.0.1 - - [04/Aug/2026:10:00:00 -0700] "GET /index.html HTTP/1.1" 200 1043},
    %{10.2.3.4 - - [04/Aug/2026:10:00:01 -0700] "POST /login HTTP/1.1" 302 0},
  ]

  e = access_log.lazy.select { |line|
    p Fiber.current.blocking? #=> false
    line =~ /"(\w+) (\S+) HTTP/
  }
  loop do
    e.next
    puts "#{$1} #{$2}"
  end
end

report
```

* matz: this is deeper than I thought.
* ko1: maybe can we at least make them thread local?
* jhawthorn: can be, but confusing because they are already fiber local sometimes.
* ko1: this is the only remaining issue that prevents Ractor principle. I want to fix it, don't know how though.
* jhawthorn:  We could probably fix it only for Ractor.
* matz: I need more time to consider.  Please add more info to the issue.

### [[Feature #22226](https://bugs.ruby-lang.org/issues/22226)] Ractor: class/module ownership -- restrict modification to the Ractor that created it (ko1)

* I updated the restriction table.

#### Discussion:

* ko1: to make the story short: let's make operations possible by main Ractors today, to operations possible by the "owner" Ractor.
* ko1: non-owners would raise IsolationError.

#### Conclusion:

* matz: give it a try.

### [[Feature #22300]](https://bugs.ruby-lang.org/issues/22300) `Ractor.check_isolation`: report Ractor isolation violations as warnings instead of raising (ufuk)
  * The proposal gives the ecosystem two things it does not have today for Ractor-safety:
    1. a burn-down list, to make a codebase Ractor-safe;
    2. a ratchet, to keep it Ractor-safe.
  * The attached patch has been used to find and burn-down Ractor-safety violations in the Rails codebase, as well as some internal Shopify codebases.
  * Can we ship a version of this in Ruby 4.1?

#### Discussion

* ko1: motivation behind this is that they want to make things ractor-compatible.  In order to do so they want to know the list of problematic operations, not tackling exceptions one-by-one.
* nobu: Is this debug only?
* ko1: I guess so.
* nobu: Should this also work in production environments?
* ko1: RUBY_RACTOR_EXCLUSIVE=1 flag makes things safer for production environments, by sacrificing runtime performance (kills concurrent execution).

Re: naming

* matz: I don't like `Ractor.check_isolation` naming to remain forever in our API for this purpose.
* ko1: I once introduced `GC.verify_internal_consistency` which seems verrrry internal.
* shyouhei: maybe under `RubyVM` ?
* nobu: or provide a speical extension library that has this method. Like `require "dangerous/operations"`

#### Conclusion

* matz: "check" does not describe what is OK and what is NG.
* matz: no objection for the feature itself, though.

### [[Feature #22266]](https://bugs.ruby-lang.org/issues/22266) Stop warning for duplicate keywords in splatted literal hashes (jeremyevans0)
* Ruby does not generally warn for duplicate keywords in splatted hashes.
* However, Ruby will warn if the splatted hash is a literal hash.
* I think Ruby should be consistent and only warn for duplicate keywords in the same hash, and not for splatted hashes, even if the splatted hash is a literal hash.

#### Discussion

* shyouhei: should `{h:1, h:1}` still be warned?
* nobu: yes
* shyouhei: I don't get why we should ignore `{h:1, **{h:1}}` then.
* matz: nobody practically write such code though.
* akr: maybe `{h:1, **{h:1}}` is a machine-generated code.
* shyouhei: `{h:1, **{<%= hhh.each_pair{|k, v| "#{k}: #{v}"}.join(",") >}}`

#### Conclusion

* matz: Let me comment.

### [[Feature #22134]](https://bugs.ruby-lang.org/issues/22134) Faster `rb_scan_args()` for keyword args (optimization) (nobu)
  * Luke proposes `:^` to avoid keyword hash duplication and `rb_get_kwargs_const` for non-destructive lookup.
  * I suggest `RB_SCAN_ARGS_BORROW_KEYWORDS` (`"^"`) as both a format fragment and a feature detection macro.
  * I also suggest `rb_lookup_kwargs` / `rb_extract_kwargs`, retaining `rb_get_kwargs` as a compatibility alias; my prototype showed 1.13–1.35x speedups in successful keyword-parsing microbenchmarks.

#### Discussions

* shyouhei: seems fine to me.
* nobu: I don't like the name though.  Counter proposal added to the issue.
* shyouhei: it should be warned in its document that the borrow and rb_sacn_args_const must come in pair.  otherwise the calling convention breaks.
* nobu: extension libraries should check the existance of RB_SCAN_ARGS_BORROW_KEYWORDS macro before using it.

#### Conclusion

* matz: Let me comment.
 
---

https://bugs.ruby-lang.org/issues/22238 `String#tr` to take a `Hash` for multi-character replacements

---

https://bugs.ruby-lang.org/issues/22209 `IO#set_encoding` is ignoring the :newline keyword argument when given Encoding positional argument
https://bugs.ruby-lang.org/issues/21308 Replacing the `Float#to_s` (dtoa.c) implementation with a modern algorithm
https://bugs.ruby-lang.org/issues/22244 `ruby -UU` should set both internal and external, `ruby -E` should print encoding list
https://bugs.ruby-lang.org/issues/22245 Reduce `Array#&` cost when self is much shorter than the argument
https://bugs.ruby-lang.org/issues/22255 Add `timeout:` to `Ractor::Port#receive`, `Ractor.receive` and `Ractor.select`
https://bugs.ruby-lang.org/issues/22297 `Module#method_defined?` should have an `include_private` argument

`method_defined?(symbol, inherit: true, only_public: true)`
`method_defined?(symbol, inherit: true, visibility: :public | :private | :protected | :all)`

https://bugs.ruby-lang.org/issues/22296 `Ruby::Box`: method stubs on core classes are invisible to internal builtins
