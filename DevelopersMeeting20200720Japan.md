---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200720Japan

https://bugs.ruby-lang.org/issues/17019

## DateTime and location

* 7/20 (Mon) 13:00-17:00 JST @ online

## Next Date

* 8/26 (Wed) 13:00-17:00 

## Announce

## Ruby 2.7 timeframe


## About 2.8/3.0 timeframe

## Check security tickets

[secret]

## Tickets

### [[Feature #17000]](https://bugs.ruby-lang.org/issues/17000) 2.7.2 turns off deprecation warnings by default (mame)

* It is already decided that we fix, but it has been discussed how to fix. (Nagachika-san will not attend the meeting, but I'd like to hear opinions from committers.)

Discussion:

* *Current 2.7*: `Warning[:deprecated] = true` by default and show keyword parameter warnings
* mame original: `Warning[:deprecated] = false` by default (`IO#lines` warnings also disappeared)
* nagachika: add a new category for dedicated for keyword warnings: `Warning[:keyword_deprecated] = false`
    * `Warning[:deprecated] = true` (default) doesn't show any warnings for keyword parameters
* jeremy: print the warnings only when `$VERBOSE = true` (Not only keyword-related warnings but also other VERBOSE warnings are enabled)
* mame second: `RUBY3_KEYWORD_WARNING=1`
* akr: Make `IO#lines` and other all non-keyword-arguent deprecated warnings out of `Warning[:deprecated]` suppression

----

If derepcation warning is disabled on default:

* well-maintainance
    * [OK] test-framework can detect deprecation warnings
* passive-maintainance
    * a few user can report deprecation warnings with explicit `-W:deprecated`.
        * really? (ko1)
* no-maintainance
    * nobody maintains it so it should die.
    * we can detect the death of this app just after deprecated.

----

matz: now I want to make all the deprecation warnings off by default

Conclusion:

* matz: changed my mind, will reopen the old ticket to disable all deprecation warnings by default
  * https://bugs.ruby-lang.org/issues/16345
* nagachika: if 3.0 changes the deprecation policy, 2.7.2 will import it
  * if 3.0 does not change the policy, I'll accept akr's suggestion (remove IO#lines and other non-keyword warings from `Warning[:deprecation]` suppression)



### [[Feature #17018]](https://bugs.ruby-lang.org/issues/17018) Show cfunc frames in rb_profile_frames() (mame)

* My colleagues want this feature because it will make it easier to investigate an application bottleneck.

Discussion:

* shyouhei: I'm curious why cframes were removed
* ko1: lineno and filenames are ambiguous, but I'm okay for the suggestion

Conclusion:

* matz: accepted
* mame: will do


### [[Feature #16923]](https://bugs.ruby-lang.org/issues/16923) Switch reserved for numbered parameter warning to SyntaxError (jeremyevans0)

* Is it OK to commit the patch?

Preliminary discussion:

```ruby
_1 = 42 #=> warning: `_1' is reserved for numbered parameter; consider another name
```

Conclusion:

* matz: go ahead


### [[Bug #9790]](https://bugs.ruby-lang.org/issues/9790) Zlib::GzipReader only decompressed the first of concatenated files (jeremyevans0)

* As I mentioned in the ticket, transparently operating like zcat would be very invasive and would break existing code (non gzip-data after gzip data).
* I implemented Zlib::GzipReader.zcat, is it OK to add that (I would still prefer adding Zlib::GzipReader.each_file)?

Conclusion:

* matz: accept `zcat`.


### [[Feature #16470]](https://bugs.ruby-lang.org/issues/16470) Issue with nanoseconds in Time#inspect (jeremyevans0)

* I have added a pull request which uses Float#rationalize instead of Float#to_r for float conversions.
* Float#rationalize is generally slower than Float#to_r, but in this use case it ends up being faster.
* Float#rationalize and Float#to_r have the same accuracy, as floats are inexact.
* Is it OK to commit the patch?

Discussion:

* akr: I don't think that Float#rationalize is a good idea
* akr: The reporter misunderstood the situation, so we need to tell them. The ticket status is now "feedback", so why don't we wait for the response?

Conclusion:

* matz: okay


### [[Feature #17004]](https://bugs.ruby-lang.org/issues/17004) Provide a way for methods to omit their return value (shyouhei)

* Is it a nice trick that we want to have, or a bad tool that is too easy to be abused?

Preliminary discussion:

* https://cpprefjp.github.io/lang/cpp17/nodiscard.html
* https://doc.rust-lang.org/reference/attributes/diagnostics.html#the-must_use-attribute

Discussion:

* mame: akr suggested "pure" attribution for methods

```ruby
pure def foo
  "foo"
end

foo #=> this call is ignored because it is pure and the result is not used
bar
```

* matz: I'm not so for the proposal. I don't want Ruby users to care it.
* ko1: it seems a good idea to try it as C internal API
* mame: How about builtin.rb?
* matz: Looks good

Conclusion:

* matz: I don't like this public API.


### [[Feature #17016]](https://bugs.ruby-lang.org/issues/17016) Enumerable#scan_left (parker)

* There is a [pull request](https://github.com/ruby/ruby/pull/3078) to implement `#scan_left`, i.e. the [scan](https://en.wikipedia.org/wiki/Prefix_sum#Scan_higher_order_function) operation in functional programming.
* The scan operation is especially valuable with lazy enumerables because `#inject` cannot be lazy.
* Should a `#scan_left` method be added to `Enumerable`?

Preliminary discussion:

```ruby
(1..10000).lazy.inject([0]) {|a,e|a << a.last+e}.take(3) #=> [1, 3, 6]
(1..10000).lazy.inject([0]) {|a,e|a << a.last+e}.last

p (1..10).lazy.enum_for(:inject, 0).map {|a, b| a + b }.force #=> [1, 3, 6, 10, 15, 21, 28, 36, 45, 55]
p (1..).lazy.enum_for(:inject, 0).map {|a, b| a + b }.force

p (1..).lazy.enum_for(:inject, 0).map {|a, b| a + b }.take(10)
#=> #<Enumerator::Lazy: #<Enumerator::Lazy: #<Enumerator::Lazy: #<Enumerator::Lazy: 1..>:inject(0)>:map>:take(10)>

p (1..).lazy.enum_for(:inject, 0).map {|a, b| a + b }.take(10).force
#=> [1, 3, 6, 10, 15, 21, 28, 36, 45, 55]
```

* https://www.boost.org/doc/libs/1_64_0/libs/hana/doc/html/group__group-Sequence.html#gaec484fb349500149d90717f6e68f7bcd
* http://www.cplusplus.com/reference/numeric/partial_sum/, https://cpprefjp.github.io/reference/numeric/partial_sum.html

Conclusion:

* matz: Need more work and better name. will write a comment


### [[Feature #14722]](https://bugs.ruby-lang.org/issues/14722) python's buffer protocol clone (mrkn)

* I made an implementation proposal.

* Note: the implementation is not complete
* Who must prevent the object from GC?
* Can we get_buffer from the same object multiple times? (Need counter?)
* The buffer must not be realloc'ed
* Currently, available_p is per class, but it must be per object

* format in source: https://github.com/ruby/ruby/pull/3261/files#diff-11794140df6df4b033804f16b0ba5269R44

* name candidates
    * memlayout
    * bufproto

* matz: looks good, but let me show the implementation candidate that actually works before commit

### [[Feature #16812]](https://bugs.ruby-lang.org/issues/16812) Allow slicing arrays with ArithmeticSequence (mrkn)

* I made a patch.

```
p [*1..10][(2..-1)%2] #=> [3, 5, 7, 9]
```

* matz: basically looks good
* We have no time to discuss its details, so the final decition was postponed.

### [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) Expose rb_fstring and its family to C extensions (tenderlove)

* Can we expose the fstring family of functions?  It seems the answer is "yes" but we need a better name (is this the current status?)

Preliminary discussion:

* `rb_str_pool` prefix
    * `rb_str_pool(VALUE)`
    * `rb_str_pool_buff(const char *ptr, long len)`
    * `rb_str_pool_cstr(const char *ptr)`
    * `rb_enc_str_pool_buff(const char *ptr, long len, rb_encoding *enc)`
    * `rb_enc_str_pool_cstr(const char *ptr, rb_encoding *enc)`

* FYI: `rb_str_new` series
    * `rb_str_new(const char *ptr, long len)`
    * `rb_str_new_cstr(const char *ptr)`
    * `rb_enc_str_new(const char *ptr, long len, rb_encoding *enc)`
    * `rb_enc_str_new_cstr(const char *ptr, rb_encoding *enc)`
    * `VALUE rb_str_new_static(const char *ptr, long len)`


* Eregon's proposal: https://bugs.ruby-lang.org/issues/13381#note-7
    * `rb_str_uminus(VALUE)` // or just `rb_funcall(str, rb_intern("-@"))`
    * `rb_str_pool(const char *ptr, long len)`
    * `rb_str_pool_cstr(const char *ptr)`
    * `rb_enc_str_pool(const char *ptr, long len, rb_encoding *enc)`
    * `rb_enc_str_pool_cstr(const char *ptr, rb_encoding *enc)`

Discussion:

* ko1: `uminus` does not represent the feature, so `rb_str_pool_value(VALUE)` ?
* matz: I use `buf` instead of `buff`.
* shyouhei: I don't like `buf` because there already are `rb_str_buf_new()` etc.
* shyouhei: this is what Rails calls `find_or_create` operation.

Conclusion:

* not concluded.

## [[Feature #16989]](https://bugs.ruby-lang.org/issues/16989) Sets need ♥️, aka the "Set Program" (marcandre)

* Bring Set into core
* Insure interoperability with Array (e.g so array & set works and is efficient)
* Shorthand syntax for static frozen sets of string/symbols (e.g. %ws{hello world})

Python:
```python
{ 1, 2, 3 }    #=> set
{ 1: 1, 2: 2 } #=> dict
{}

# Python3
type({}) #=> <class 'dict'>
```

JavaScript:
```javascript
{ a, b, c } == { "a": a, "b": b, "c": c }
console.log({ a, b, c })
```

```ruby
# joking? $ = "S"et and/or "S"truct
$[1, 2, 3]
$(1, 2, 3)
${ a, b, c } == ${ a: a, b: b, c: c }
```

```ruby
p{a:1} # Syntax error
p{a}   # passed as block


p a:a
p({a})
p({a:})

p { foo_bar_baz_qux_quux:... }
p { foo_bar_baz_qux_quux:" } # "
p { foo_bar_baz_qux_quux:〃}
p { foo_bar_baz_qux_quux:. }
```


* akr: How about making Hash Set-like?  For example, Hash#inspect doesn't show values if all values are true.

```ruby
s = {1}        # s = { 1 => NoValue }
s << 2         # s[2] = NoValue
p s.inspect    #=> { 1, 2 }
s += other_set # s = s.merge(other_set)
p s.inspect    #=> { 1, 2 }
```

```ruby
class Hash
  class Set < Hash
    def each
      super do |k, _|
        yield k
      end
    end
  end

  NoValue = Object.new.freeze

  def <<(other)
    self[other] = NoValue
  end
end

h = {a: 1}
h << :b
pp h #=> {:a=>1, :b}

h = {:a, :b, :c} #=> {a: NoValue, b: ...}
h.each{|k, v| 
  p k #=> :a, ...
  p v #=> NoValue
}
h.each{|*a|
  p a #=> (1) [:a]
  p a #=> (2) [:a, NoValue]
}
h[:a] #=> ??
```

### conclusion

* matz: _Positive_ to introduce Set into core.  But we need to first introduce a set literal.  `{ x, y, z }` is good, but JavaScript uses it as another meanings, so it would bring confusion.  I have no idea.
* knu: As a set library maintainer, I'll first communicate, and try to solve the easy issues that can be sorted out in the library side.


## Addtional topics

### [[Feature #16986]](https://bugs.ruby-lang.org/issues/16986) Anonymous Struct literal (ko1)

* equality

```ruby
s1 = ${a: 1, b: 2}
s2 = ${a: 1, b: 2}
p s1 == s2 #=> true
s3 = ${b: 2, a: 1}
p s1 == s3 #=> false # maybe true is desired
```

* matz: Syntax is better. Don't like `${ ... }`. no good idea now. I proposed `{|a: 1, b: 2|}`.

* mame: This feature may make it difficult to add a new Object#methods.

```ruby
S = Struct.new(:a, :b, :size)
p S.new(1, 2, :size).size
```

* matz: sub-class of `BasicObject`?
    * `Record`, `::BasicObject::Record < BasicObject` # NG
    * `NamedTuple`

```ruby
class BasicObject::Record < BasicObject
end

class Record
  p self #=> BasicObject::Record
end
```

### Can I merge [Ractor branch](https://github.com/ko1/ruby/tree/ractor_parallel)? (ko1)

* matz: Go ahead. It is must-have for 3.0.


### I will propose decimal https://github.com/mrkn/ruby/tree/decimal (mrkn)

(not discussed; just a random idea)

* Decimal: A subclass of Rational

* The internal representation is different from Rational
  * decimal floating point (like BigDecimal)
  * digits+len+exp (`digits * 10**exp`)

* Spec/Behavior
  * Decimal + Decimal should be Decimal
  * Decimal / 3 may return Rational (if it is not divisible)
  * inspect shows it in decimal style
  * Rational#inspect #=> 1/2
  * Decimal#inspect  #=> 0.5 (or 0.5r or 0.5d)
  *                  #=> 0.50, 0.500, 0.5000, .... depending upon precision?
  * Float#inspect    #=> 0.5
  * to_s ?
  * "0.5r" should also be Decimal
  * intended use case: ActiveRecord
    ```ruby
    x = Rational("1.3")
    p x #=> (13/10)
    "%.3f" % x #=> "1.300"
    ```
    since ruby 2.2
    ```ruby
    printf "%.30f\n", 1/3r #=> 0.333333333333333333333333333333
    printf "%.30f\n", 1/3.0 #=> 0.333333333333333314829616256247
    ```

* akr: faster than Rational because no reduction is needed?

```ruby
irb(main):001:0> BigDecimal("1", 10000).precs
=> [9, 10008]
irb(main):002:0> (BigDecimal("1", 10000) / BigDecimal("1", 10000)).precs
=> [9, 36]
irb(main):003:0> (BigDecimal("1", 10000) * BigDecimal("1", 10000)).precs
=> [9, 27]
irb(main):004:0> (BigDecimal("1", 10000) + BigDecimal("1", 10000)).precs
=> [9, 18]
irb(main):005:0> (BigDecimal("1", 10000) - BigDecimal("1", 10000)).precs
=> [9, 18]
```

### The new maintainer of net-smtp (hsbt)

https://github.com/tmtms said "I'm interesting in maintaining ruby/net-smtp". I feels it is welcomed. Is there the objection about this?

https://github.com/ruby/net-smtp

Note: We can add the commit grant only GitHub and rubygems. The code sync remains in hsbt's work.