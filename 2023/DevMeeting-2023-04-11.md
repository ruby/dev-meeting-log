---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-04-11

https://bugs.ruby-lang.org/issues/19525

## DateTime and location

* 2023/04/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/05/10 (Thu) 14:00-17:00 JST @ Matsumoto!
  * Next next: 2023/06/08 (Thu) 14:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #19551]](https://bugs.ruby-lang.org/issues/19551) Backport commits for CI failures (hsbt)

* Can I merge PR like "Fix CI failure" for stable branches myself from April 2023?

Preliminary discussion:

* We need to ask naruse, usa and nagachika.

Discussion:

* naruse: I can merge them to my branch(es).  Give me the list.
* nagachika: ok
* usa: I prefer the current workflow used PRs.

Conclusion:

* hsbt: I'd also talk to usa and nagachika.


### [[Feature #19561]](https://bugs.ruby-lang.org/issues/19561) `ObjectSpace::WeakMap#delete` and `ObjectSpace::WeakKeyMap#delete` (byroot)

* When used as a weak set, it can be convenient to eagerly delete some keys.

Discussion:

* nobu: I think it is okay
* matz: Yes

Conclusion:

* matz: accepted


### [[Feature #19538]](https://bugs.ruby-lang.org/issues/19538) Performance warnings (byroot and eregon) (byroot)

* Disabled by default, enabled with `-W:performance`.
* Would allow the VM or YJIT to indicate when it has to deoptimize because of some construct.
* For example: transitioning to `SHAPE_TOO_COMPLEX`, mega-morphic call sites, etc.

Preliminary discussion:

```ruby
Warning[:performance] = true
```

* ko1: it looks not to be a Ruby way
* mame: redefining some optimized builtin method may be also warned?

Discussion:

* mame: OP wants to know if a shape is too complex.
* shyouhei: Should this be a warning?
* akr: I want this somehow in a server log.  Not sure how to achieve that without warning.
* naruse: Should this be a real-time warning?  Could be a profiling API like GC.stat
* akr: APMs like datadog could want this info in a machine-readable form, not in a warning string.
* nobu: But for the case of shape, the exact spot (file location) where transision happend is vital.
* matz: I don't want to _force_ instance variables be in a specific order.
* nobu: We could amend `attr_writer` etc., so that they also define shape of the class.
* shyouhei: I'd say hundreds of instance variables in a class really smell bad at the first place.
* mame: We don't need hundreds of them... Minimal case 4 is enough. (mame: TBH I am not sure)

```ruby!
class Foo
  def initialize
    @cache_a = @cache_b = @cache_c = @cache_d = nil
  end

  def get_a
    @cache_a ||= begin .. end
  end
  def get_b
    @cache_b ||= begin .. end
  end
  def get_c
    @cache_c ||= begin .. end
  end
  def get_d
    @cache_d ||= begin .. end
  end
end
# calling get_a, get_b, get_c, and get_d in a random order may lead to OBJ_TOO_COMPLEX_SHAPE_ID
```

* mame: Other than OBJ_TOO_COMPLEX_SHAPE_ID, eregon proposed to warn singleton class generation (for performance reason)

---

* matz: I basically agree with `Warning[:performance] = true` with some caution:
  * matz: the warning is very specific to implementation details, and we may change them
  * matz: The document should make it clear
* shyouhei: is a warning good for the purpose?
* matz: a warning is good enough I think.

---

* ko1: How about warning OBJ_TOO_COMPLEX_SHAPE_ID?
* ko1: I believe in future Rubocop will require users to initialize all instance varaibles in #initialize
* ko1: I don't think it is a Ruby way. matz what do you think?
* matz: I don't like it neither, but that is also Ruby

---

* mame: do one mode `Warning[:performance]` warn many kinds of behaviors related to performance? It is possible to filter by definining `Kernel#warn`, I think
* ko1: does `ruby -w` enable the mode too? I don't think it is needed
* shyouhei: Agreed, it is not needed
* nobu: Currently `ruby -w` enables all the modes

* `ruby -W:deprecation`
* `ruby -W:performance`

---

* mame: How about warning creation of singleton classes?
* matz: not acceptable. it is unavoidable, I think

---

* mame: How about warning redefining some optimized builtin methods such as Integer#+ or Array#[]?
* matz: acceptable if anyone wants it

Conclusion:

* matz: Accept `Warning[:performance] = true`
* matz: Accept warning of OBJ_TOO_COMPLEX_SHAPE_ID
* matz: `ruby -w` should not enable this mode
* matz: `ruby -W:performance` should enable the mode

### [[Feature #19528]](https://bugs.ruby-lang.org/issues/19528) `JSON.load` enabling `create_additions: true` by default is surprising and lead to security vulnerabilities (byroot)

* `JSON.load` is the natural method to reach to as "load/dump" is the standard Ruby interface (`Marshal.load`, `YAML.load`, etc).
* Various linters have to ben `load` and recommend to use `parse` instead.
* I think json 2.x should warn when this feature is implicitly enabled, and json 3.x should disable it by default.
* This has led to multiple CVE in the past.

Preliminary discussion:

* nobu: it should be discussed with JSON maintainers, not us

Discussion:

* knu: It is understandable when YAML.load has been made safe by default and JSON.parse is much more well-known and preferred.
* akr: maybe safe by default is preferable here
* hsbt: this may obstable upgrade to Ruby 3.3, naruse-san what do you think?
* naruse: it is acceptable, but how about a migration path?
  * `JSON.load` without `create_additions` keywords should be warned first
  * If object deserializaion is needed, a user should explicitly specify it by adding `create_additions: true`
  * Otherwise, a user should use `JSON.parse` instead of `JSON.load`
  * After some period, we can change the default to `create_additions: false`
* matsuda: I dont like the name `create_addition`, how about another good name?

Conclusion:

* (conclusion is up to JSON author(s))


### [[Feature #19560]](https://bugs.ruby-lang.org/issues/19560) `IO#close_on_fork=` and `IO#close_on_fork?` (byroot)

* `O_CLOFORK` was added to the POSIX spec in 2020, and some system (not Linux) supports it.
* Ruby could emulate it.
* Such feature would make it much easier to write fork-safe network clients.

Discussion:

* akr: If it is defined in POSIX, this proposal would be acceptable.
* nobu: What happens if a descriptor is shared using IO#for_fd?
* naruse: It would have many edge cases, but we can discuss them later.
* akr: Start with a warpper of OS feature, without an emulation layer.
* ko1: Linux does not provide the flag, so it is not available in Linux.
* naruse: If it is needed in Linux, we need a clear use case.
* nobu: To prevent thread race condition, the flag should be specified at open, not fcntl after it is opened
* nobu: How about with starting with only constants File::Constants::CLOFORK (for O_CLOFORK) and Fcntl::FD_CLOFORK in fcntl.so
* akr: it looks reasonable
* mame: is it okay that this feature is not avaiable in Linux?
* naruse: I think it is acceptable
* mame: Why don't we introduce an emulation layer?
* akr: at least, IO#close_on_fork= will not solve the race condition issue of threads

----
* ko1: what happens when a bufferd IO gets forked?
* akr: does nothing?
* ko1: that means buffered contents would be written twice?
* akr: Yes. Use exit! to avoid.
* shyouhei: forked process can have a closed IO, which is not yet flushed, which gets GCed in each child.
* akr: That's a problem I agree.
* nobu: ...(looking at the source code) No, no treatment for that case.
* akr: It is a known UNIX pitfall.  Just use exit!.
* ko1: surprised this is revealed after 30 yrs.
* mame: no one use fork with opened IOs leaved. This might show no need of CLOFORK

```ruby!
# t.rb
open("/tmp/x", 'w'){|f|
  f.write("a")
  fork{
  }
}

# $ ruby t.rb && cat /tmp/x
# aa <-- wow!!
```

Conclusion:

* matz: `File::Constants::CLOFORK` accepted.
* mame: `IO#close_on_fork=` doesn't fix race conditions. NG.
* naruse: Emulation layer needs a use-case.

### [[Feature #19571]](https://bugs.ruby-lang.org/issues/19571) Add `REMEMBERED_WB_UNPROTECTED_OBJECTS_LIMIT_RATIO` to the GC (peterzhu2118)

  - We tested on production traffic on Shopify's [Storefront Renderer](https://shopify.engineering/how-shopify-reduced-storefront-response-times-rewrite).
  - We saw a significant improvement in GC time (0.67x on average and 0.49x for p99) and response time (0.96x on average and 0.86x for p99).

(mame: failed to log... ko1 will reply to the ticket)

### [[Bug #4040]](https://bugs.ruby-lang.org/issues/4040) `SystemStackError` with `Hash[*a]` for Large _a_ (jeremyevans0)

* @ko1 fixed this issue for iseq methods accepting splats in Ruby 2.2.
* We fixed this issue for cfunc methods in January.
* I found this issue applies to all other method calls.
* I developed a fix for all cases I could identity (yjit will need updates for it).
* The fix results in a minor performance decrease in microbenchmarks.
* I have attempted to offset this performance decrease with major optimizations to bmethod, send, symproc, and method_missing calls, using knowledge gained from fixing this bug.
* Do we want to fix this issue, and if so, how much of a performance loss are we willing to accept?
* I propose we should accept this fix if it doesn't result in more than 0.2% performance decrease in production benchmarks such as railsbench.

Discussion:

(mame: failed to log. This is just a breif summary)

* ko1: The change adds a branch on the fast path. I would like to review it seriously, but it would require a time.
* shyouhei: indeed the changes are significant. Difficult to review in a short time.
* ko1: I'll look at it after RubyKaigi, is it ok?
* matz: ok

### [[Bug #19533]](https://bugs.ruby-lang.org/issues/19533) Behavior of `===`/`include?` on a beginless/endless range (`nil..nil`) changed in ruby 3.2 (jeremyevans0)

* Do we want to make beginless+endless `Range#include?` return true for all objects?
* A decision was made in August 2022 to have `include?` raise exception for beginless/endless ranges.
* Looking back, it's possible that it was desired for the beginless^endless case and not the beginless+endless case.

Preliminary discussion:

https://bugs.ruby-lang.org/issues/18580
> matz: So I decided to make include? to raise exception for beginless/endless non-numeric ranges.

```ruby!
(nil..nil) === 1
#=> Ruby 3.1: true
#=> Ruby 3.2: cannot determine inclusion in beginless/endless ranges
```

* should we restore the behavior back to 3.1?

Discussion:

* matz: Hmm... It is possible for `(nil..nil) === any_object` to return true blindly
* knu: I used this feature before and found it handy.
* akr: Range#include? basically uses

```ruby
# decided in #18580
(1..nil).include?(2) #=> raise
(nil..1).include?(2) #=> raise

# changed (unintentionally?) 3.1 -> 3.2 -> 3.3
(nil..nil).include?(2) #=> true -> raise -> raise (keep)
(nil..nil) === 2 #=> true -> raise -> true

(1..nil) === 2 #=> true -> true -> true (keep)
(nil..3) === 2 #=> true -> true -> true (keep)



(nil..nil).cover?(Object.new) #=> true

irb(main):002:0> o=Object.new
=> #<Object:0x0000000103884dd0>
irb(main):003:0> (o..o)
=> #<Object:0x0000000103884dd0>..#<Object:0x0000000103884dd0>
irb(main):004:0> (o..o).cover?(o)
=> true
```

* akr: this could be very incompatible, but Range#=== should be an alias to cover? instead of include? ... Oh, Range#=== already behaves like `cover?`.
* naruse: Since `===` is used by case-when, the current include semantics is odd to such common semantics.

Conclusion:

* matz: accepted to restore the behavior of `(nil..nil) === any_object #=> true`


### [[Bug #19564]](https://bugs.ruby-lang.org/issues/19564) `Range.cover?` fails for Range wrapped in `SimpleDelegator` (jeremyevans0)

* Do we want to handle delegate objects in `Range#cover?`?
* If so, do we want to add `Range#to_range` and have `Range#cover?` check for that method and call it if it exists?

Preliminary discussion:

```ruby!
class TimeRangeDelegator < SimpleDelegator
end

r_long = (Time.now..Time.now+2)
r_short = (Time.now..Time.now+1)

r_long = TimeRangeDelegator.new(r_long)
r_short = TimeRangeDelegator.new(r_short)

r_long.cover?(r_short)
#=> expected: true, actual: false
```

Discussion:

* matz: I don't want `Range#to_range`

Conclusion:

* matz: will reply


### [[Feature #19573]](https://bugs.ruby-lang.org/issues/19573) Add `Class#singleton_inherited` (jeremyevans0)

* This would be similar to `Class#inherited`, but called for singleton class creation instead of normal subclass creation.
* This could be used to warn or raise on singleton class creation, or modify the object to change behavior.

Preliminary discussion:

> This could be used to warn or raise on singleton class creation,

* mame: why does jeremy want to warn it? (for performance)

> or modify the instance to change behavior, such as allow optimizations when a singleton class does not exist, but allow fallbacks if it does exist.

* mame: what kind of optimizations does jeremy want for example?

Discussion:

* matz: As Ruby object model, every object has its singleton class originally. This proposal breaks the model, so I don't want it
* mame: Do you have counterproposal against jeremy's problem (performance degeneration due to singleton class)
* matz and ko1: (discussing the possibility of optimization)

```ruby!
def receive_request(req)
  req.extend(Something)
  req.foo # method cache miss
  req.bar # method cache miss
  req.baz # method cache miss
end
```

Conclusion:

* matz: reject this proposal itself
* matz: I want ko1 to consider the future optimization to avoid the method cache miss due to singleton class :-P

### [[Feature #19590]](https://bugs.ruby-lang.org/issues/19590) Include the invalid argument in error messages from `Process.clock_gettime` and `Process.clock_getres` (nobu)

Discussion:

* akr: go ahead

Conclusion:

*

## [[Bug #19246]](https://bugs.ruby-lang.org/issues/19246) Rebuilding the loaded feature index much slower in Ruby 3.1 (matz/jeremyevans0)

* mame: nobu, could you review Jeremy's PR?
* nobu: At a glance, it looks good

Conclusion:

* nobu: Will leave a github review.

## [[Feature #19591]](https://bugs.ruby-lang.org/issues/19591) Add `symbolize_keys` to `MatchData#named_captures` (palkan)

Discussion:

* mame: JSON has `symbolize_names`, not `symbolize_keys`
  * `JSON.parse(json_str, symbolize_names: true)`
* akr: Feature seems fine, not sure the proposed name though.
* mame: `MatchData` itself already interfaces with pattern matches, no?

```ruby!
m = /(?<a>.)(?<a>.)/.match("01")
case m
in { a: }
  p a  #=> "1"
end
```

* mame: Use case is not clear to me. I am not against the proposal, though
* matz: I think it is a good habit to use symbols as a hash keys
* matz: Where did `symbolize_keys` come from?
* naruse: maybe Rails ActiveSupport. But the API is `{ "str" => 42 }.symbolize_keys #=> { :str => 42 }`
* naruse: JSON and YAML provide keyword `symbolize_names: true`
* matz: Then `symbolize_names` should be picked

Conclusion:

* matz: accept as `MatchData#named_captures(symbolize_names: true | false)`

## [[Feature #19588]](https://bugs.ruby-lang.org/issues/19588) Allow Comparable#clamp(min, max) to accept nil as a specification

* matz: accepted