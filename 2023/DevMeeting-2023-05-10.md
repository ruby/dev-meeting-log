---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-05-10

https://bugs.ruby-lang.org/issues/19599

## DateTime and location

* 2023/05/10 (Thu) 14:00-17:00 JST @ Matsumoto!

## Next Date

* Next next: 2023/06/08 (Thu) 14:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### Parser improvements

#### Yuichiro Kaneko

* I want to introduce my parser project including https://github.com/yui-knk/lrama and https://rubykaigi.org/2023/presentations/spikeolaf.html#may11.

#### Kevin Newton / Jemma Issroff

* https://github.com/Shopify/yarp

* Ruby 3.3 ships both

### [[Feature #19610]](https://bugs.ruby-lang.org/issues/19610) `GC.delay_promotion` (peterzhu2118)

* This feature changes the GC to not immediately promote young objects referred from old objects.
* We tested on production traffic on Shopify's [Storefront Renderer](https://shopify.engineering/how-shopify-reduced-storefront-response-times-rewrite).
* We saw significant improvement in GC time (0.81x on average, 0.88x for p99, 0.45x for p99.9).

Preliminary discussion:

* byroot: I think it should just be the new behavior, with no switch.
* 

Discussion:

*

Conclusion:

*


### [[Bug #19616]](https://bugs.ruby-lang.org/issues/19616) Remove ext/readline from Ruby 3.3 (hsbt)

* Any opinion about this?

Preliminary discussion:

* hsbt: I have already merged the PR. Reline works great. Any comments are welcome.

Discussion:

*

Conclusion:

*


### [[Misc #19608]](https://bugs.ruby-lang.org/issues/19608) Being a co-maintainer of the ruby/openssl for the OpenSSL FIPS mode (hsbt)

* I'm +1 to this proposal. Does anyone have objection?

Preliminary discussion:

*

Discussion:

*

Conclusion:

* Go


### [[Feature #19572]](https://bugs.ruby-lang.org/issues/19572) Add a new `TracePoint` event for rescued exceptions (st0012)

  * I want to add a new `rescue` event type to `TracePoint`.
  * When the event is triggered, `TracePoint#rescued_exception` can be used to access the exception.
  * It will be helpful for building debugging tools and can enhance `ruby/debug`'s tracer too.

Preliminary discussion:

* ko1: will add a comment

Discussion:

*

Conclusion:

*


### [[Feature #18885]](https://bugs.ruby-lang.org/issues/18885) End of boot advisory API (byroot)

* I'm planning to merge the first optimizations for 3.3
* Are we still good with the `Process.warmup` name? Or would we rather expose something on `GC`?

Preliminary discussion:

* ko1: Should the API accept keywords?
* nobu: I guess it should accept any keywords for future extension (and optinally warn (not raise) against unknown keywords)
* mame: Who and how to use this API? An application end user? or the authors of frameworks (such as, Rails.warmup method or something calls Process.warmup internally?)

Discussion:

*

Conclusion:

*


### [[Feature #19236]](https://bugs.ruby-lang.org/issues/19236) Allow to create hashes with a specific capacity from Ruby (byroot)

* We exposed this capability in the C API in 3.2, but never agreed on a similar API on the Ruby side.
* The logical API would be `Hash.new(capacity: 1000)` but there are some backward compatibility concerns.
* `Hash.create(capacity: 1000)` was proposed as well.

Preliminary discussion:

* mame: In my opinion, `Hash.new(kw: x)` should be first replaced with `Hash.new({ kw: x })`
* mame: And then, we can introduce a new `capacity` keyword for `Hash.new`
* mame: So Ruby 3.3 should show a warning against `Hash.new(kw: x)` to urge users to rewrite it with braces, and Ruby 3.4 will have `Hash.new(capacity: 1000)`
* ko1: I don't like a new protocol of `Hash.create`. Then we may want it for all containers like `Array.create`

Discussion:

* matz: I strongly object capacity accessor
* matz: my preference is keyword argument
* mame: Should we use Hash.new or a new method?
* matz: agreed
* mame: In 3.3 it throws error all keyword argments to Hash.new. Then Ruby 3.4 allows that Hash.new will accept capacity keyword argument.

Conclusion:

* In 3.3 it ~~throws error~~ emit a deprecation warning all keyword argments to Hash.new. Then Ruby 3.4 allows that Hash.new will accept capacity keyword argument.



### [[Feature #19435]](https://bugs.ruby-lang.org/issues/19435) Expose counts for each GC reason in `GC.stat` (byroot)

* Seems there was some concern about bloating `GC.stat`
* This data would be very useful for instrumenting GC performances cheaply.

Preliminary discussion:

* ko1: will add a comment

Discussion:

*

Conclusion:

*


### [[Misc #18248]](https://bugs.ruby-lang.org/issues/18248) Add Feature Triaging Guide (jeremyevans0)

* Last discussed during October 2021 developer meeting, no decision made at the time.
* I think it would be useful to triage features, so that features in the issue tracker are things we would actually want.

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Bug #595]](https://bugs.ruby-lang.org/issues/595) `Fiber` ignores ensure clause (jeremyevans0)

* Last discussed back in 2018.
* It appears agreed that doing this automatically (when the fiber/thread is GCed) is a bad idea.
* Do we want to add `Fiber#kill` to allow for explicit fiber kills (including going through each ensure block)?
* How should we handle fiber yield/transfer inside ensure blocks in that case?

Preliminary discussion:

* ko1: I like `Fiber#kill`. It does not solve the fundamental problem itself, but it will mitigate the problem. I am okay to close this ticket by adding it.

Discussion:

*

Conclusion:

*

### [[Bug #19597]](https://bugs.ruby-lang.org/issues/19597) `Process.argv0` returns the same mutable String (nobu)

Preliminary discussion:

* nobu: I want the methond to return a frozen string. I have written a PR.


Discussion:

*

Conclusion:

*

### About Ruby governance (ufuk)

I would like to talk a little bit about Ruby Governance and what improvements we can make to how it is communicated to the public.

### [[Feature #19633]](https://bugs.ruby-lang.org/issues/19633) Allow passing block to `Kernel#autoload` as alternative to second `filename` argument (shioyama)

* Something like this would remove the need for Zeitwerk to monkeypatch `Kernel#require`
* Connects autoloads directly to any callbacks on them, rather than having the connection be implicit via `require`. I think this is an improvement.
* I am implementing autoloads on anonymous modules via a similar monkeypatch; this would make that monkeypatch unnecessary and simplify other logic via the block's closure.

### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) `Range#step` semantics change for non-`Numeric` ranges (zverok)

* Implementation is provided, but no movement is seen in months. Can we agree (or provide the adjustments) on the [final spec](https://bugs.ruby-lang.org/issues/18368#note-17) and the implementation?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*
