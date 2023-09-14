# DevMeeting-2023-09-14

https://bugs.ruby-lang.org/issues/19858

## DateTime and location

* 2023/09/14 (Thu) 13:00-17:00 JST @ Online

## Next Date

* Next: 2023/10/12 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* naruse: I want to have a preview release.  Hold on, am packaging right now.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #19075]](https://bugs.ruby-lang.org/issues/19075) Binary searching for the last element (kyanagi)

* My latest pull request implements `bsearch(target: :last)` and `bsearch_index(target: :last)`.
* https://bugs.ruby-lang.org/issues/19075#note-6
* argument name?: `target: :last`, `find: :last`, `reverse: true`, ...
* separate method or not?: `bsearch_last`, `reverse_bsearch`, `rbsearch`, ...

Preliminary discussion:

```ruby=
ary = [1, 2, 3, 3, 3, 4]
ary.bsearch_index{|x|x>=3} #=> 2
ary.bsearch_index{|x|x>3}  #=> 5
ary.bsearch_index(last: true) {|x| x<3 } #=> 1

[1,2,3,4,5].bsearch{|x|x/2<=>1} #=> 3
[1,2,3,4,5].bsearch(last: true){|x|x/2<=>1} #=> 4
```

```ruby=
ary = ["A", "B", "C", "D", "E", "F"]
ary.bsearch_index {|x| x >= "A" } #=> 0
ary.bsearch_index {|x| x >= "D" } #=> 3
ary.bsearch_index {|x| x >= "G" } #=> nil

# proposed
ary.bsearch_last_index {|x| x < "D" } #=> 2

# If we implment the propsed one using current methods...
i = ary.bsearch_index {|x| !(x >= "D") }
if i == 0
  i = nil
elsif i
  i = i - 1
else
  i = ary.size - 1
end

ary.bsearch_index(target: :left) {|x| x >= "D" } #=> 2
ary.bsearch_index(search_last_false: true) {|x| x >= "D" } #=> 2

ary = ["A", "B", "C", "C", "C", "D", "E"]
ary.bsearch_index {|x| x <=> "C" }
```

Discussion:

* akr: what does it mean by "last"?
* mame: I was wondering that too.
* mame: OP wants the "last index that satisfies the block" instead of the first one.
* akr: what should be returned when there is no such thing that satisfies the block then?  Not obvious to me.
* mame: the proposed functionality is hard to implement by hand.
* akr: that's because bsearch itself is complicated by design.

----

* mame: it's hard to name the functionality.
* akr: what about `bsearch_last_found?`
* mame: the thing is not always "last found" I guess.

Conclusion:

* matz: It looks difficult to design a good API for this use case. I want to reject it until a good API is proposed. I will reply

### [[Bug #19868]](https://bugs.ruby-lang.org/issues/19868) `Process::Status` methods for compatibility with `Fixnum` (nobu)

* Looking at [54274b8c65a0981f1c69055a1513ba3c614dd675](https://bugs.ruby-lang.org/projects/ruby-master/repository/git/revisions/54274b8c65a0981f1c69055a1513ba3c614dd675), noticed that `pst_rshift` contains shift by a negative integer, an unspecified behavior in C99.
* I think an exception would be ok for such argument, also we can suggest alternative methods to encourage the transition.
* https://github.com/ruby/ruby/pull/8392

Discussion:

* deletion targets?
  * ~~`Process::Status#to_int`~~
  * `Process::Status#>>`
  * `Process::Status#&`
* nobu: We wrote them in 1.8 era.
* shyouhei: the pull request is full of magic numbers...
* akr: is it okay to delete them just because they are old?
* nobu: they are not only old but also system-dependent.  Very hard to use properly.

Conclusion:

* matz: Let's give it a try

### [[Bug #17146]](https://bugs.ruby-lang.org/issues/17146) Queue operations are allowed after it is frozen (jeremyevans0)

* Last discussed at October 2020 developer meeting, no decision made.
* @headius and I discussed this at RubyKaigi and agreed it would be best to raise FrozenError for push/pop for frozen queues.
* Can we either decide this is a bug and fix it, or decide it is not and close it?

Preliminary discussion:

* shyouhei: the [previous discussion](https://github.com/ruby/dev-meeting-log/blob/master/2020/DevelopersMeeting20201026Japan.md#bug-17146-queue-operations-are-allowed-after-it-is-frozen-ko1)

Discussion:

* ko1: not strange to raise FrozenError, but there are other cases when frozen things can be changed.
* matz: for instance?
* ko1: `binding.freeze.eval("...")` can change the local variable inside of it.
* matz: can be an option to prohibit freezing a queue at all.
* ko1: or prohibit operatins for frozen queue.  In that case what happens if dequeueing a frozen-empty-queue is not obvious.
* mame: the thread shall wait forever?
* matz: that (non-obvoius-ness) is the reason why I'm for prohibiting freezing.
* matz: queue being destructive is the nature of that class in general.
* matz: I guess there are other methods that do not check frozen-ness in middle of that method.
* ko1: like `Array#map!`?

```ruby=
ary = [1, 2, 3, 4, 5]

t = Thread.new do
  ary.map! {|x| sleep 1; x.to_s }
end

sleep 2
ary.freeze

p t.value # after 3 seconds
#=> can't modify frozen Array: ["1", "2", 3, 4, 5] (FrozenError)
```

* mame: Queue being unable to get frozen sounds the easiest way to me.
* shyouhei: NoMethodError?
* mame: ENV.freeze raises TypeError

Conclusion:

* matz: I think Queue#freeze does not make sense. I will reply

### [[Bug #17354]](https://bugs.ruby-lang.org/issues/17354) Module#const_source_location is misleading for constants awaiting autoload (jeremyevans0)

* Last discussed at March 2021 developer meeting, no decision made.
* I agree the method result is misleading, but as @mame states in note 10, current design is the most flexible.
* Can we either decide this is a bug and fix it, or decide it is not and close it?

Preliminary discussion:

* shyouhei: [previous discussion](https://github.com/ruby/dev-meeting-log/blob/master/2021/DevelopersMeeting20210317Japan.md#bug-17354-moduleconst_source_location-is-misleading-for-constants-awaiting-autoload-jeremyevans0)

Discussion:

* options:
    * 1. as is
    * 2. nil
    * 3. load the file and return the location
    * 4. an empty array
* matz: I don't like 2 and 4 either. 1 and 3 work for me. I think 1 is good enough because it is easy to load it actually be accessing the constant

Conclusion:

* nobody objects for option #1.
* matz: I will reply

### [[Bug #7964]](https://bugs.ruby-lang.org/issues/7964) Writing an ASCII-8BIT String to a StringIO created from a UTF-8 String (jeremyevans0)

* Do we want StringIO to handle encoding?
* Making it handle encoding makes it more like IO, fixing some existing compatibility issues but introducing new compatibility issues.

Preliminary discussion:

Discussion:

* nobu: StringIO does not understand encoding options right now.
* nobu: I have been dreaming of implementing this area...
* mame: What should happen when a binary string is written for (theoretical) Unicode StringIO object?
* usa: They want UndefinedConversionError, same as IO.
* nobu: that's sort of a hutsle.

Conclusion:

* nobu: let me do this!


### [[Bug #9115]](https://bugs.ruby-lang.org/issues/9115) Logger traps all exceptions; breaks Timeout (jeremyevans0)

* Can we fix this by supporting specific exception classes to reraise instead of swallow?
* If not, is there an alternative approach that would be acceptable to fix this?

Discussion:

* shoyouhei: Logger wants to continue writing at all costs vs Timeout wants to throw at all costs.
* ko1: this shall be done by `handle_interrupts`.
* mame: that will stop timeout to be raised.
* ko1: logger do not consider asynchronous exception, it seems to me.

Conclusion:

* there is no "fix" to this issue.
* shyouhei: handle_interrupts is a better-than-nothing mitigation.
* nobody is against inserting a handle_interrupts guard

---

## Thread#native_thread_id is incorrectly cached across fork on Linux

https://bugs.ruby-lang.org/issues/19873

* ko1: seems nice.
* naruse: will take a look.

## Unicode line and paragraph separator are not stripped

https://bugs.ruby-lang.org/issues/19867

by design? -> Yes

## Update license phrases to SPDX BSD-2-Clause

https://bugs.ruby-lang.org/issues/19860

* usa: why the change?
* hsbt: "BSD-2-Clause" by SPDX is different from current our BSDL. Should we update it?
* Matz: There is no opinion.
* naruse: SPDX-listed licenses could be machine-detected
* naruse: but Ruby's license is not machine-detectable at the beginning (we are combining lots of differently licensed source codes)
* hsbt: ok

## Start & Finish, Begin & End

https://bugs.ruby-lang.org/issues/19859

* matz: `start_with?` is an influence from java.
* mame: Python and JS also uses startsWith/endsWith
* rejected

## Get thread creation time

https://bugs.ruby-lang.org/issues/19850

* usa: understand the use case.
* usa: but the thread could be reused using thread pools
* ko1: maybe API to reset the time?
* usa: would better be done by users.
* naruse: modify the thread's name accordingly.
* mame: or assign an instance variable.

## Requiring file with autoload results in confusing error if file doesn't exist

https://bugs.ruby-lang.org/issues/19849

* usa: we can show autolaod line in LoadError message.

## Need a method to check if two ranges overlap

https://bugs.ruby-lang.org/issues/19839

* matz: accepted

```ruby
p (1..2).overlap?(2...2)  #=> true -> false?
p (2..2).overlap?(2...2)  #=> true -> false?
p (2...2).overlap?(2...2) #=> true -> false?
p (1..2).overlap?(2..-1)  #=> true -> false?
p (1..2).overlap?(3..-1)  #=> false
p (4..6).overlap?((2..8) % 3) #=> TypeError (okay?)
p (3..4).overlap?((2..8) % 3) #=> TypeError (okay?)
```

## Method#destructive?, UnboundMethod#destructive?

https://bugs.ruby-lang.org/issues/19832

* matz: I will reject

## Allow `Array#transpose` to take an optional size argument

https://bugs.ruby-lang.org/issues/19830

```ruby=
# basic expectation
ary.transpose.transpose == ary

# but is not true
[[], []].transpose.transpose #=> []

[[], []].transpose.transpose[0] #=> expected: [], actual: nil

# because the first transpose returns an empty array
[[], []].transpose #=> []
# (2, 0)               (0, 2) -> (0, 0) dimension

# explicitly specify the dimension
[[], []].transpose.transpose(2) #=> [[], []]
```

* matz: I cannot determine if this is needed
* knu: `.fill(0, 2) { [] }`

## Add Enumerable#uniq_map, Enumerable::Lazy#uniq_map, Array#uniq_map and Array#uniq_map!

https://bugs.ruby-lang.org/issues/19787

## Remove tailcall_optimization support

https://bugs.ruby-lang.org/issues/19780

## Introduce defp keyword for defining overloadable, pattern matched methods

https://bugs.ruby-lang.org/issues/19764

## URI::HTTP.build does not accept a host of `_gateway`, but `URI.parse` will.

https://bugs.ruby-lang.org/issues/19756

```ruby
URI::HTTP.build(host: "gateway")    #=> #<URI::HTTP http://gateway>
URI::Generic.build(host: "gateway") #=> #<URI::Generic //gateway>
```

```
$ irb -ruri
irb(main):001> URI::DEFAULT_PARSER = URI::RFC3986_PARSER
(irb):1: warning: already initialized constant URI::DEFAULT_PARSER
/home/mame/.rbenv/versions/3.3.0-dev/lib/ruby/3.3.0+0/uri/common.rb:24: warning: previous definition of DEFAULT_PARSER was here
=> #<URI::RFC3986_Parser:0x00007f04fdbc3488>
irb(main):002> URI::HTTP.build(host: "_gateway")
=> #<URI::HTTP http://_gateway>
```

* If there are real people who are in a trouble of this, we may think about it later

## Add support for UUID version 7

https://bugs.ruby-lang.org/issues/19735

UUIDv7 contains of not just a random value, but also a timestamp. Is it okay to provide it in random/formatter? It will not be idempotent

## Warning for non-linear Regexps

https://bugs.ruby-lang.org/issues/19720

To consider both compatibility and security...

定義: 「リニアでない正規表現」を「make_now_justさんので動かない正規表現」正規表現とする
* back reference
  * check /(["'])[a-z]*\1/ on https://makenowjust-labs.github.io/recheck/playground/
* sub expression call

* 普通に作った正規表現
  * -wなし: 警告でない
  * -W:may-nonlinear-regexpあり: 警告でる
* looseモードの正規表現 `/(x)\1/d`
  * -wなし: 警告でない
  * -W:may-nonlinear-regexpあり: 警告でない

* Matz: 正規表現の素晴らしさをspoilするような機能は入れたくない
  * -W:may-nonlinear-regexpはギリギリ許容