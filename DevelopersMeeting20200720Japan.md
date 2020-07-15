---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200720Japan

https://bugs.ruby-lang.org/issues/17019

## DateTime and location

* 7/20 (Mon) 13:00-17:00 JST @ online

## Next Date


## Announce

## Ruby 2.7 timeframe


## About 2.8/3.0 timeframe

## Check security tickets

[secret]

## Tickets

### [[Feature #17000]](https://bugs.ruby-lang.org/issues/17000) 2.7.2 turns off deprecation warnings by default (mame)

* It is already decided that we fix, but it has been discussed how to fix. (Nagachika-san will not attend the meeting, but I'd like to hear opinions from committers.)

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #17018]](https://bugs.ruby-lang.org/issues/17018) Show cfunc frames in rb_profile_frames() (mame)

* My colleagues want this feature because it will make it easier to investigate an application bottleneck.

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #16923]](https://bugs.ruby-lang.org/issues/16923) Switch reserved for numbered parameter warning to SyntaxError (jeremyevans0)

* Is it OK to commit the patch?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Bug #9790]](https://bugs.ruby-lang.org/issues/9790) Zlib::GzipReader only decompressed the first of concatenated files (jeremyevans0)

* As I mentioned in the ticket, transparently operating like zcat would be very invasive and would break existing code (non gzip-data after gzip data).
* I implemented Zlib::GzipReader.zcat, is it OK to add that (I would still prefer adding Zlib::GzipReader.each_file)?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #16470]](https://bugs.ruby-lang.org/issues/16470) Issue with nanoseconds in Time#inspect (jeremyevans0)

* I have added a pull request which uses Float#rationalize instead of Float#to_r for float conversions.
* Float#rationalize is generally slower than Float#to_r, but in this use case it ends up being faster.
* Float#rationalize and Float#to_r have the same accuracy, as floats are inexact.
* Is it OK to commit the patch?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #17004]](https://bugs.ruby-lang.org/issues/17004) Provide a way for methods to omit their return value (shyouhei)

* Is it a nice trick that we want to have, or a bad tool that is too easy to be abused?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #17016]](https://bugs.ruby-lang.org/issues/17016) Enumerable#scan_left (parker)

* There is a [pull request](https://github.com/ruby/ruby/pull/3078) to implement `#scan_left`, i.e. the [scan](https://en.wikipedia.org/wiki/Prefix_sum#Scan_higher_order_function) operation in functional programming.
* The scan operation is especially valuable with lazy enumerables because `#inject` cannot be lazy.
* Should a `#scan_left` method be added to `Enumerable`?

Preliminary discussion:

*

Discussion:

*

Conclusion:

*


### [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) Expose rb_fstring and its family to C extensions (tenderlove)

* Can we expose the fstring family of functions?  It seems the answer is "yes" but we need a better name (is this the current status?)

Preliminary discussion:

*

Discussion:

*

Conclusion:

*

