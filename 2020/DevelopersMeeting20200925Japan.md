---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting2020925Japan

https://bugs.ruby-lang.org/issues/17138

## DateTime and location

* 9/25 (Fri) 13:00-17:00 JST @ Online

## Next Date

* 10/26 (Mon) 13:00-17:00 JST @ Online

## Announce

## Ruby 2.7 timeframe

## About 3.0 timeframe

https://hackmd.io/8JtMdAayTMaXew4dQDPB3w
* Preview 1 blockers
* [x] freeze Range
* [x] make define_method error with Ractor

## Check security tickets

[secret]

### [[Feature #16994]](https://bugs.ruby-lang.org/issues/16994) Shorthand syntax for static frozen sets of string/symbols (e.g. `%ws{hello world}`) (marcandre)

* Rest of my "Set program", especially: insure interoperability with `Array` (e.g so `array & set` works and is efficient)

Preliminary discussion:

* ko1: `freeze`? Ask Matz about this idea.
* knu: Proposal is reasonable, but not sure the syntax is acceptable. Also I have no time to implment built-in `Set` in C.

Discussion:

Conclusion:

* matz: will reply and set it as feedback


### [[Bug #17144]](https://bugs.ruby-lang.org/issues/17144) Tempfile.open { ... } does not unlink the file (eregon)

* OK to keep the change and make Tempfile.open(&block) finally intuitive? Some usages might need updating but it seems very few.
* `SomeClass.open { ... }` should always release all resources, `Tempfile.open { ... }` was the only exception.
* Relying on GC to unlink seems very brittle (even more so on other Ruby implementations).

Preliminary discussion:

* mame: I understand the issue but I'm not sure if it deserves the incompatibility, I have no opinion

Discussion:

* 

Conclusion:

* matz: will reply


### [[Feature #17143]](https://bugs.ruby-lang.org/issues/17143) Improve support for warning categories (jeremyevans0)

* Warning.warn :category keyword support was approved last developer meeting.
* I would like to add Kernel#warn :category keyword for Ruby-level warnings, and ruby_category_warn{,ing} for C-level warnings
* I have prepared a pull request that adds them, and also adds categories for all core warnings.  Is it OK?

Preliminary discussion:

https://github.com/ruby/ruby/pull/3508/files#diff-a4624c2e4a017e857303edf9201323c3R72

```ruby
Kernel.warn("Chunky bacon!", category: 'foo')
```

* mame: this proposes two things:
  * (1) Extends `Kernel#warn` to accept category keyword
  * (2) Introduces any category to all existing warnings (not only "experimental" and "deprecated", but also "ignored", "info", "compile", etc. etc.)
    * `Kernel#warn` checks nothing about category (it even allows non-symbol object)
* ko1: this does not look "category" but "tags": some warnings may belong to "compile" as well as to "deprecated"

Discussion:

* mame: `Kernel#warn` with category keyword looks reasonable
* nobu, shyouhei: agreed
* naruse: I'm unsure the use case for adding any category to all existing warnings 
* mame: I think `Kernel#warn` should allow only existing categories: "experimenal" or "deprecated".
* naruse, nobu: (discussing the implementation detail: should avoid calling rb_intern many times?)
* naruse: I understand it is difficult to decide so soon what categories we should have

Conclusion:

* matz: will reply


### [[Bug #16518]](https://bugs.ruby-lang.org/issues/16518) Should we rationalize Rational's numerator automatically? (jeremyevans0)

* I think we should make Kernel#Rational always return a Rational instance, or raise an exception if it cannot.

Preliminary discussion:

```ruby
irb(main):001:0> Rational(BigDecimal(1), 60)
=> 0.16666666666666667e-1
irb(main):002:0> Rational(1.1, 60)
=> (825659931684591/45035996273704960)
```

Discussion:

* mrkn: It is a bug.

```
>> Rational(BigDecimal(1))
=> (1/1)
```

Conclusion:

* matz: mrkn, please handle it.


### [[Bug #15712]](https://bugs.ruby-lang.org/issues/15712) DateTime#=== should be defined and compare date and time instead of just the date (jeremyevans0)

* DateTime#=== is currently the same as Date#===, but this seems more accidental than deliberate.
* Do we want to define DateTime#=== and have it be similar to DateTime#==?

Preliminary discussion:

```ruby
DateTime.new(2001, 2, 3) === DateTime.new(2001, 2, 3, 12)
=> true
```

Discussion:

* naruse: I think it should be WONTFIX because DateTime is obsoleted. Use Time.
* akr: Afraid about compatibility.

Conclusion:

* matz: agreed with WONTFIX.  Will reply


### [[Bug #15661]](https://bugs.ruby-lang.org/issues/15661) Disallow concurrent Dir.chdir with block (jeremyevans0)

* Concurrently, we have a non-verbose warning.  Do we want to switch to raising an exception?

Preliminary discussion:

* ko1: agree
* mame: not sure for comaptibility

Discussion:

* 

Conclusion:

* matz: agreed.


### [[Feature #17134]](https://bugs.ruby-lang.org/issues/17134) Add resolv_timeout to TCPSocket (glass)

* It introduces `resolv_timeout` similar to `Socket.tcp`. Can I merge it?
* After merging it, I will work on `connect_timeout` as well.

Discussion:

* akr: looks good. Please own it if it brings trouble
* naruse: please deal with net/http too
* glass: yes it is my final target

Conclusion:

* glass: will do


### [[Feature #15628]](https://bugs.ruby-lang.org/issues/15628) init_inetsock_internal should fallback to IPv4 if IPv6 is unreachable (sonalkr132)

* Should we implement IPv6 to IPv4 fallback or "Happy Eyeballs" (RFC8305) in core?

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* glass: give it a try


### [[Feature #14394]](https://bugs.ruby-lang.org/issues/14394) Class.descendants (fatkodima)

* Introduces `Module#descendants` method as a native way to track `Class`/`Module` descendants, instead of inefficient hack like crawling `ObjectSpace` or tracking descendants via `inherited` hook. MRI already tracks subclasses, so this won't introduce new overheads.

Preliminary discussion:

* ko1: +1.
    * How about `Module#descendants`? 
    * prepend?
    * order? 
* mame: should it include self? active_support/core_ext/subclasses does exclude self
* mame: should it include singleton class?

```ruby
module M; end
class C; prepend M; end
p M.descendants #=> [M, C]

class D; include M; end
p M.descendants #=> [M, D, C]
```

```ruby
module M; end
class C; include M; end
p C.descendants #=> [M, C]

class D; include M; end
p M.descendants #=> [M, D, C]

p C.descendants #=> [C]
```

```ruby
module M; end
class << Object.new; include M; end
p M.descendants #=> [M]

module M; end
```

```ruby
module M; end
class A; end
class B < A; include M; end
class C < A; include M; end
class D < C; end

p A.descendants #=> [A, C, D, B]
p M.descendants #=> [M, C, D, B]
```

* usage:
  * https://github.com/rails/rails/blob/fb852668dff2786a4cfb30ad923830da9eed2476/activerecord/lib/active_record/railtie.rb#L169
  * https://github.com/rails/rails/blob/b2eb1d1c55a59fee1e6c4cba7030d8ceb524267c/railties/lib/rails/engine/railties.rb#L11
  * https://github.com/mizzy/specinfra/blob/a6866b8ef7514c9db2c42006c37abc68d214c719/lib/specinfra/command_factory.rb#L77-L82

Discussion:

* 

Conclusion:

* matz: accepted.


### [[Feature #17056]](https://bugs.ruby-lang.org/issues/17056) Array#index: Allow specifying the position to start search as in String#index (fatkodima)

* If we know from which offset to start searching, this will potentially speed up the index findings.

Preliminary discussion:

* mame: (Weak opinion) `Array#find_index` looks a special version of `Enumerable#find_index`. If only `Array#find_index` allows "start", the interface differs from Enumerable version. Is it okay?

Discussion:

* ko1: how about keeping `Array#find_index` as is, and allowing only `Array#index` to accept "start"?
* mame: they are aliases, so it makes them different methods
* ko1: How about remove `Array#find_index` (so `Enumerable#find_index` is used)
* matz: looks good
* nobu: How about `Array#rindex`?
* matz: it should be also dealt with
* mame: marcandre mentions start/stop for bsearch and bsearch_index
* matz: if needed, they should be discussed in another ticket

Conclusion:

* matz: will reply


### [[Bug #17030]](https://bugs.ruby-lang.org/issues/17030) Enumerable#grep{_v} should be optimized for Regexp (fatkodima)

* From this issue, it was decided to add a new `/f` ("fast", the name is debatable) option to regexps. 
* When provided, the methods which previously allocated global `MatchData` objects, won't do that anymore. This will reduce memory usage overall and greatly sped things up. The benchmark results are provided in the ticket. 

Preliminary discussion:

* mame: not decided yet :-)
* mame: this proposes `/regexp/f` which just check if it matches or not, not allocate MatchData Object

Discussion:

* matz: Reject `/regexp/f`. I don't want to add a new attribute to Regexp
* akr: Make `Enumerable#grep` special handling for regexp so that MatchData is generated only for the last match.

Misc:

ko1: strange result: 

```ruby
array = Array.new(10_000){'a'*1000}
require 'benchmark'

N = 1000
REGEXP = /a/

Benchmark.bm{|x|
  x.report{
    N.times{
      array.select { |e| e.match?(REGEXP) }
    }
  }
  x.report{
    N.times{
      array.grep(REGEXP)
    }
  }
}

=begin
array = Array.new(10_000){'a'}
       user     system      total        real
   1.439655   0.019911   1.459566 (  1.459582)
   2.958808   0.009992   2.968800 (  2.968850)

array = Array.new(10_000){'a'*1000}
       user     system      total        real
   1.666297   0.020017   1.686314 (  1.686351)
   2.270222   0.009995   2.280217 (  2.280278)
=end
```

Conclusion:

* Matz: not sure `//f` can be accepted, but we can imprive `grep` more before `//f` proposal.

### [[Feature #15573]](https://bugs.ruby-lang.org/issues/15573) Permit zero step in Numeric#step and Range#step (fatkodima)

* This is for consistency with other behavior. The ticket description describes the problem. Matz responded there that, and I implemented a patch and chose to raise an error on zero step. 

Preliminary discussion:

```ruby
(lower_threshold..upper_threshold).step(avg_object_size).take(5)
 #=> [lower_threshold] * 5 is expected???
```

Discussion:

* mrkn: I will make all the following cases errors.

```
irb(main):001:0> 1.step(10, by: 0) { break }
=> nil
irb(main):002:0> 1.step(10, 0) { break }
Traceback (most recent call last):
        5: from /home/mrkn/.rbenv/versions/2.7/bin/irb:23:in `<main>'
        4: from /home/mrkn/.rbenv/versions/2.7/bin/irb:23:in `load'
        3: from /home/mrkn/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/irb-1.2.4/exe/irb:11:in `<top (required)>'
        2: from (irb):2
        1: from (irb):2:in `step'
ArgumentError (step can't be 0)
irb(main):003:0> 1.step(10, 0).each { break }
=> nil
irb(main):004:0> 1.step(10, by: 0).each { break }
=> nil
irb(main):005:0> 1.step(10, by: 0)
=> (1.step(10, by: 0))
irb(main):006:0> 1.step(10, 0)
=> (1.step(10, 0))
```

Conclusion:

* matz: already left up to mrkn
* mrkn: make all raise an exception


### [[Feature #13683]](https://bugs.ruby-lang.org/issues/13683) Add strict Enumerable#single (fatkodima)

* This method will ensure the collection contains only one element and return it. This feature is useful and there are many +1 on that.
* The name for this method (`single`, `one`, `only`, `sole`) is under discussion
* Should this method accept a default (like `Hash#fetch`) when collection is empty, and how: default arg vs block? 

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* matz: let me consider


### [[Feature #14722]](https://bugs.ruby-lang.org/issues/14722) python's buffer protocol clone (mrkn)

* I will fix some bugs until the next meeting.
* Can I merge this?

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* mrkn: I'll merge this when the CI passes.


### [[Feature #16812]](https://bugs.ruby-lang.org/issues/16812) Allow slicing arrays with ArithmeticSequence (mrkn)

* I found the wrong test case, so the implementation was also in wrong state.  The proposed patch has been fixed.
* There are opposing opinions in the following case:

Discussion:

* https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20200826Japan.md

```ruby
[0,1,2,3,4,5][0..10] #=> [0, 1, 2, 3, 4, 5]
[0,1,2,3,4,5][6..10] #=> []
[0,1,2,3,4,5][7..10] #=> nil

[0,1,2,3,4,5][(3..0) % -2] #=> [3, 1]
[0,1,2,3,4,5][(4..0) % -2] #=> [4, 2, 0]
[0,1,2,3,4,5][(5..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(6..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(7..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(8..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(9..0) % -2] #=> [5, 3, 1]
```

or

```ruby
[0,1,2,3,4,5][(3..0) % -2] #=> [3, 1]
[0,1,2,3,4,5][(4..0) % -2] #=> [4, 2, 0]
[0,1,2,3,4,5][(5..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(6..0) % -2] #=> [4, 2, 0]
[0,1,2,3,4,5][(7..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(8..0) % -2] #=> [4, 2, 0]
[0,1,2,3,4,5][(9..0) % -2] #=> [5, 3, 1]
```

or

```ruby
[0,1,2,3,4,5][(3..0) % -2] #=> [3, 1]
[0,1,2,3,4,5][(4..0) % -2] #=> [4, 2, 0]
[0,1,2,3,4,5][(5..0) % -2] #=> [5, 3, 1]
[0,1,2,3,4,5][(6..0) % -2] #=> nil???
[0,1,2,3,4,5][(7..0) % -2] #=> nil
[0,1,2,3,4,5][(8..0) % -2] #=> nil
[0,1,2,3,4,5][(9..0) % -2] #=> nil
```

* Python's document explains the behavior: https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range

```
>>> [0, 1, 2, 3, 4, 5][6:0:-2]

i=6, j=0, k=-2
↓
i=5, j=0, k=-2

x = i,i+k,i+2k,... = 5,3,1,...

[5, 3, 1]
```

* The first commit in cpython when the slice support was introduced: https://github.com/python/cpython/commit/5efaf7eac8c1dbbf82a96dc5d9b87fddd5d17b58
* The specification of NumPy's indexing: https://numpy.org/doc/stable/reference/arrays.indexing.html

Conclusion:

* mrkn: raise an exception for out-of-bounds tentatively, and investigate the rationale of Python


### [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) Expose rb_fstring and its family to C extensions (byroot)

* Still an extremely useful feature long awaited, it would be very disappointing if it didn't make it to 3.0.
* Both `json` and `msgpack-ruby` agreed to a feature that calls `String#-@` from C extensions. Meaning `rb_fstring_cstr` would allow them to saves lots of allocations.

Preliminary discussion:

* ko1: Now I'm positive on `interned_str` (we can recognize `String#intern` and the term "interned_str").

Discussion:

* 

Conclusion:

* ko1: will do


### [[Bug #17178]](https://bugs.ruby-lang.org/issues/17178) Procs with kw:def/**kw lose elements when called with a single Array (eregon)

* Does it sound buggy to you too? Do you think we can experiment and try changing it for Ruby 3? Current behavior seems useless, inconsistent and counter-intuitive.

Preliminary discussion:

```ruby
proc {|a          | p a }.call(1,2) #=> 1
proc {|a, kw: :def| p a }.call(1,2) #=> 1

proc {|a          | p a }.call([1,2]) #=> [1, 2]
proc {|a,         | p a }.call([1,2]) #=> 1
proc {|a, kw: :def| p a }.call([1,2]) #=> 1 # eregon's expectation: [1, 2]

# similar case
proc{|a,|       p a}.call([1, 2]) #=> 1
proc{|a,    &b| p a}.call([1, 2]) #=> [1, 2]
proc{|a, *, &b| p a}.call([1, 2]) #=> 1

# inconsistency
proc {|a, kw: :def| p a }.call([1, 2]) #=> 1
proc {|a, &b      | p a }.call([1, 2]) #=> [1, 2]
```

```
# ./all-ruby -e 'proc {|a, kw: :def| p a }.call([1,2])'
ruby-2.0.0-rc1        [1, 2]
...
ruby-2.0.0-p195       [1, 2]
ruby-2.0.0-p247       1
...
ruby-2.7.1            1
```

Discussion:

* 

Conclusion:

* matz: At the present time, I want to change nothing, but give me time to consider it


### [[Bug #17159]](https://bugs.ruby-lang.org/issues/17159) extend `define_method` for Ractor

* Any ideas?
* mix a feature with `Proc#isolate`?

Discussion:

```ruby
10.times do |i|
  define_method("foo#{ i }",
    &-> x do
      -> { x }
    end.call(i).isolate
  )
end
```

```ruby
i = 10
Proc.new{ i }.isolate(capture: true)
Proc.new{ i }.isolate 
Proc.isolate{ i }
lambda{ i }.isolate

i = 10
Ractor.new &{
  sleep 0.1
  p i
}.isolate(capture: true)
i = 20


define_method(name){...}
define_method(name, Proc.new{...}.isolate(capture: true))

i = 10
Proc.new{ i }.isolate(capture: {i: 100}).call #=> 100
Proc.new{ f }.isolate(capture: {f: 100}).call #=> 100

j = 100
Proc.new{ [i, j] }.isolate(capture: [:i]).call
```
    
Conclusion:

* ko1: I will reconsider and organize a proposal


### [[Feature #15504]](https://bugs.ruby-lang.org/issues/15504) Freeze all Range objects


* Can I freeze Range literal like `(1..2)`?

Discussion:

```ruby
def test
  (1..2)
end

p test().object_id == test().object_id #=> true

def test a
  (1..a)
end

p test(3).object_id == test(3).object_id #=> false
```

Conclusion:

* matz: let's try to freeze all Range and ask users.

## 3.0.0-preview1 showstopper

* All Ranges frozen
* A method defined by `define_method` must not be callable from Ractor

## other topics

* disable warnings by default (making 2.7.2 stuck)

```ruby
$ ./miniruby -e '$stderr.lines'
-e:1: warning: IO#lines is deprecated; use #each_line instead
```

* pattern matching
  * remove `in` operator
  * introduce `expr => { a:, b: }`
      * syntax?
      * return value?
* `Proc#using`
* move src/
* rbs was bundled
* typeprof will be bundled