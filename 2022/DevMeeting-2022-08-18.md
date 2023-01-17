---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-08-18

https://bugs.ruby-lang.org/issues/18954

## DateTime and location

* 2022/08/18 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/09/22 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## AWS Cost check

https://us-east-1.console.aws.amazon.com/cost-management/home?region=ap-northeast-1#/custom?groupBy=UsageType&forecastTimeRangeOption=None&hasBlended=false&hasAmortized=false&excludeDiscounts=true&excludeTaggedResources=false&excludeCategorizedResources=false&chartStyle=Stack&timeRangeOption=Last12Months&granularity=Monthly&reportName=For%20dev-meeting&reportType=CostUsage&isTemplate=false&usageAs=usageQuantity&filter=%5B%7B%22dimension%22:%22RecordType%22,%22values%22:%5B%22SavingsPlanNegation%22,%22Credit%22%5D,%22include%22:false,%22children%22:null%7D%5D&subscriptionIds=%5B%5D&reportId=198dedc9-fa9b-4746-9dfc-0818c16b7528&excludeForecast=false&startDate=2021-07-01&endDate=2022-06-30

* Thank you @naruse-san!

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) `Range#step` semantics for non-`Numeric` ranges (zverok)

* Proposal to reimplement `(a..b).step(c)` via `+`, or provide another method with this behavior
* More clarifications provided & answers to the questions after last dev.meeting

Preliminary discussion:

```ruby=
# desirable
(timestamp1...timestamp2).step(3.hours) { ... }
(date1..date2).step(1.day) { ... }

# not desirable
([]..).step([1]).take(3)        #=> [[], [1], [1, 1]]
(Set[1]..).step(Set[2]).take(3) #=> [Set[1], Set[1, 2], Set[1,2]]
```

* option 1: give up all (current)
* option 2: accept "not desirable" behaviors
* option 3: introduce a new step-like method as "+" semantics
  * `(a..b).over(d)` == `while a <= b; yield(a); a += d; end`
  * `Range#over` keeps "not desirable" behavior
* option 4: delegate to `range.begin.step` for non-supported types
  * `(a..b).step(d)` == `a.step(b, d)`
  * `(a...b).step(d)` == `a.step(b, d, exclude_end: true)`
  * `(a..b).step(d)` == `a.upto(b, false, by: d)`
  * `(a...b).step(d)` == `a.upto(b, true, by: d)`
* option 5: delegate to `range.begin.increment(d)` for non-supported types
  * `(a..b).step(d)` == `while a <= b; yield(a); a = a.increment(d); end`
  * `(a...b).step(d)` == `while a < b; yield(a); a = a.increment(d); end`

Discussion:

* shyouhei: I concern string

```ruby=
# String
("a".."z").step(2)   #=> "a", "c", "e", "g", ...
("a".."z").step("a") #=> "a", "aa", "aaa", "aaaa", ...

# Numeric
(1..10).step(0.1){p _1}
(0..).step(1i).take(3)  #=> [(0+0i), (0+1i), (0+2i)]
(0i..10i) #=> bad value for range (ArgumentError)
```

* matz: I still want to avoid undesired behaviors. So I don't accept Option 2
* matz: at least I reject Option 3. I don't want to introduce a new method that behaves almost the same as `Range#step`

Conclusion:

* matz: I don't like all current options. Leave it to me


### [[Bug #18580]](https://bugs.ruby-lang.org/issues/18580) `Range#include?` inconsistency for beginless String ranges (zverok)

* "This was discussed during the February 2022 developer meeting, and @matz said he needs more time to consider it."

Preliminary discussion:

```ruby
('a'..'z').include?('ww') #=> false

(..'z').include?('ww')    #=> actual: true, expected: false? or an Exception?
p 'ww' < 'z' #=> true

("a"..).include?('ww')    #=> true
```

Discussion:

* matz: Should we raise an exception on this case? It is safer than return weired results.

```ruby
(..'z').include?('a')
#=> Matz's idea: raises an exception

("a"..).include?('ww')  #=> current: true, should raise an exception too?
("a"..).include?('ww2') #=> current: true, should raise an exception too?

("a".."zzzzzzzzzz").include?("ww2") #=> false, but takes long; it calls each
  
('a'..).include? 'z'
```

akr: should we raise on end-less case?
nobu: In this case, we should use `Range#cover?`.
matz: I want to remove `succ` approach completely from `Range#include?`, but it is a huge incompatiblity

Conclusion:

* matz: Try to prohibit beginless or endless ranges to be used by `include?`

### [[Feature #17330]](https://bugs.ruby-lang.org/issues/17330) `Object#non`  (zverok)

* Proposal to introduce generic "nullify if unacceptable" API (zverok)
* more explanations and examples provided

Preliminary discussion:

* mame: `params[:name]&.non(&:empty?)` is extremely unreadable to me

```ruby=
# With non
return Faraday.get(url).non { |r| r.code >= 400 }
# Note how little changes necessary to say "successful response's body"
return Faraday.get(url).non { |r| r.code >= 400 }&.body

# with `then`
return Faraday.get(url).then{ |r| r.code >= 400 ? nil : r}&.body

# plain-old ruby
r = Faraday.get(url)
return r.code >= 400 ? nil : r.body


# itself_if
return Faraday.get(url).itself_if{ |r| r.code < 400 }&.body

# itself_unless
return Faraday.get(url).itself_unless{ |r| r.code >= 400 }&.body
```

* mame: I misread the "non" code as `r = Faraday.get(url); return r.code >= 400 ? r.body : nil`. Surprisingly unreadable

Discussion:

* ko1: I feel `non` returns boolean.
  * matz: I don't care on this point if well-defined.
* matz: `unless` with complex condition is difficult to read and `non` also encorage this kind of difficulties.
  * matz: So I can't accept `itself_unless` too.
* mame: `nil.non(&:nil?) #=> nil`
* naruse: `itself_if` makes sense to return the receiver.
* shugo: ujihisa-san really want `itself_if`? I think he just suggested it because ko1 called for the name. The OP wants the negation version, so even if only `itself_if` is accepted, the OP will not be satisfied. If `non` or `itself_unless` is not accepted, this ticket should be rejected
* knu: This does not look intuitive and readable even for rubyists.  I'm afraid introducing this kind of tricky feature would end up confusing non-native ruby programmers.

Conclusion:

* matz: I reject `non`, but I don't reject the proposal itself. Want a good name candidate


### [[Feature #16122]](https://bugs.ruby-lang.org/issues/16122) `Struct::Value`: simple immutable value object (zverok)

* **The ticket is in weird state. It has "Status: Feedback", but generic state "CLOSED", but I don't see any notice of HOW it was closed?..** Is it RedMine glitch?.. I don't see an option to reopen it.
* The [latest comment](https://bugs.ruby-lang.org/issues/16122#note-28) from Matz seems to lean towards accepting proposal, the only culprit is a name: "Struct::Value seems acceptable to him, but he wanted to seek a better name. Devs suggested Tuple, NamedTuple, and Record, but none of them seemed to fit for him."
* Can we somehow move forward with this?

Preliminary discussion:

* Proposed name candidates:
  * `Struct::Value`
  * `ImmutableStruct` (provided by immutable_struct gem)
  * `FrozenStruct` (matz: the main purpose is not "frozen", so no)
  * `Unit` (matz: concerns conflict)
  * `Item` (matz: concerns conflict)
  * `Box` (matz: concerns conflict)
  * `SimpleStruct` (matz: the main purpose is not simplicity but immutability, so no)
  * `PlainStruct` (matz: not suitable name)
  * `BasicStruct` (matz: it is not inheritance)
  * `ValueStruct` (provided by value_struct gem?)
  * `ValueClass` (provided by values gem?)
  * `Value` (provided by values gem)
  * `Data` (proposed by nobu)

```ruby=
FooBar = ::Data.new(:foo, :bar)
foobar = FooBar.new("foo", "BAR")
```

```
$ gem-codesearch "\\bclass Data\\b" | wc -l
989
$ gem-codesearch "\\bmodule Data\\b" | wc -l
2968
$ gem-codesearch \\bImmutableStruct\\b | wc -c
37939
ko1@aluminium:~$ gem-codesearch '\bValueStruct' | grep class | wc -l
7
ko1@aluminium:~$ gem-codesearch '\bImmutableStrudct' | grep class | wc
      0       0       0
ko1@aluminium:~$ gem-codesearch '\bFrozenStruct' | grep class | wc
      0       0       0
$ gem-codesearch '\bBasicStruct' | grep class | wc -l
22
ko1@aluminium:~$ gem-codesearch '\bValueClass' | grep class | wc -l
79
ko1@aluminium:~$ gem-codesearch '\bPlainStruct' | grep class | wc -l
0
```

* ko1: I think `#dig` are preferable to handle JSON-like data
    * mame: It's weird without `#[]`
* ko1: I want to freeze the instances by default
* mame: I want to keep the field values in instance variable

```ruby!
FooBar = Struct::Value.new(:foo, :bar)

class FooBar
  def foo_plus_bar
    foo + bar
    foo() + bar()
    self.foo + self.bar
    @foo + @bar
    # foo + bar is a bit difficult to read
    # self.foo + self.bar is not ambiguous but too verbose
  end
end
```

```
ko1@aluminium:~$ head -n 3 /srv/gems/kmlbo-0.0.3/test/data.rb
class Data
  PATH = [ [-94.42434787909789,39.13594499803094,0], [-94.42434713169446,39.1355875721549,0], [-94.424343522331,39.13486866498771,0],
  [-94.42433930314023,39.13414897981443,0], [-94.42433298666054,39.13307029557503,0], [-94.42432772546127,39.13217218417481,0],
```

Discussion:

* everyone: mame's proposal is rejected
  * shugo: access via accessors is a good habit
  * k0kubun: when the field name has a typo, like `@fooo`, it doesn't raise an exception but returns nil implicitly
  * shyouhei: `@fooo = 1` will create a wrong instance variable instead of raising an error
  * knu: It may limit the optimization
* ko1: dig is not avaialble. is it okay?
  * matz: discuss after when it is needed.
* shugo: how about `ValueStruct`? 

```
ko1@aluminium:~$ gem-codesearch 'class ValueStruct\b'
/srv/gems/functional-ruby-1.3.0/lib/functional/value_struct.rb:  class ValueStruct < Synchronization::Object
/srv/gems/value_struct-0.8.1/lib/value_struct/version.rb:class ValueStruct < Struct
/srv/gems/value_struct-0.8.1/lib/value_struct.rb:class ValueStruct < Struct
```

* matz: I'll facilitate the discussion with two candidates: `Data` or `Struct::Value`

---

```
ko1@aluminium:~$ gem-codesearch ' Data.new\b' | wc -l
613
```

```
ko1@aluminium:~$ gem-codesearch rb_cData | wc -l
395

# rb_cData was removed from Ruby 3.0
```

```ruby
# eregons's proposal
MyData = Data.for(:foo, :bar)
# matz: Not bad

# mame's two cents
MyData = Data[:foo, :bar]
# matz: I don't like this

# k0kubun's: Kernel#Data
MyData = Data(:foo, :bar)
MyData = Data.class(:foo, :bar)
# matz: I expect this to be conversion method like `String()`

mydata = MyData.new("FOO", "BAR")

# method serieas
Data.new(:foo, :bar)
Data.create(:foo, :bar)
Data.build(:foo, :bar)
#=> it seems create new instance

Data.class(:foo, :bar)
# it conflicts with Object#class

Data.by(:foo, :bar)
Data.for(:foo, :bar)
Data.def(:foo, :bar) # matz: I like this
Data.define(:foo, :bar)
Data.new_class(:foo, :bar)
Data.new_subclass(:foo, :bar)

Data.new(foo: "FOO", bar: "BAR") #=> Data.def(:foo, :bar, eq_class: false).new("FOO", "BAR")
Data.new(foo: "FOO", bar: "BAR") == Data.new(foo: "FOO", bar: "BAR") #=> false

Data.new(...) #=> raise "Use Data.def(...)"

# matz: should we create
Struct.def(:foo, :bar)
Struct.def("NAME", :foo, :bar) # raise
```

* mame: new class inherits from `Data` class?

```ruby!
class MyBase
  def initialize
    @a = nil
    @b = nil
  end

  def sum
    foo + bar
  end
end

MyData = Data.new_class(:foo, :bar, parent_class: MyBase)
mydata = MyData.new("FOO", "BAR")

# only check mydata.foo and mydata.bar
mydata == mydata.dup #=> true

mydata.sum #=> "FOOBAR"

MyData.ancestors #=> [MyData, MyBase, ...]

D2 = Data.new_class(:a, :b)
p D2.ancestors #=> [D2, Data, ...]
```

Matz: Yes, all created new classes are subclasses of the Data class.

```ruby
class AnyType < BaseType
  # ko1
  include Data.new_module(:foo, :bar)
  # nobu/matz
  define_property :foo, :bar
end
```

```ruby!
MyData = Data.new(:foo, :bar)
mydata = MyData[:foo, :bar]

D = Struct.new(:foo, :bar)

class D
  def initialize(*args, **kwargs)
    p args   #=> []
    p kwargs #=> {:foo=>1}
  end
end

D[foo: 1]
```

```ruby!
D = Data.def(:foo, :bar, :baz, default: {foo: 1, bar: 2})

D = Data.def(foo: Data::Required, bar: 1)
D.new(foo: 42) #=> D[foo: 42, bar: 1]
D[42]          #=> D[foo: 42, bar: 1]
D[42, 43]      #=> D[foo: 42, bar: 43]
```


* knu: Having to pass a hash with splat could be a pitfall.
* knu: Should passing a fewer number of values as positional parameters cause an ArgumentError? (To prevent human error)
* knu: How about `D[]` taking only positional parameters (for creating a tuple) and `D.new()` taking only key-value pairs?

* matz: I pick `Data.def`. I will reply
  * `D = Data.def(:foo, :bar)` defines a new subclass `D` of Data.

* Instance creation
  * `D.new(1, 2)`
    * is equal to `D[1, 2]`
  * `D.new(1)` raises an `ArgumentError` 
  * `D.new(foo: 1, bar: 2)`
    * is equal to `D[foo: 1, bar: 2]`
  * `D.new(foo: 1)`
    * is equal to `D[foo: 1, bar: nil]`
  * `h = {foo: 1, bar: 2}; D.new(h) #=> foo: {foo: 1, bar: 2}, bar: nil`
    * do `D.new(**h)` instead
* `Data.new` raises an exception to guide to use `Data.def`.
* Define `Struct.def` without class name specifier and `keyword_init:` keyword.

default value discussion:

```ruby
Data.def(:foo, :bar, baz: 42)
Data.def(:foo, :bar, default: { foo: 42 })
Data.def(foo: 42, bar: Data::Required)

D = Data.def(:foo, :bar)
class D
  def initialize(*args, **kwargs)
    if args.empty?
      super(kwargs.fetch(:foo, 42), kwargs.fetch(:bar))
    else
      raise if args.size != 2
      super(*args)
    end
  end

  def initialize(**kwargs)
    super(foo: kwargs.fetch(:foo, 42), bar: kwargs.fetch(:bar))
  end
  D[1, 2] #=> D[foo: 1, bar: 2]

  def initialize(*args,
                 foo: args.empty? ? 42 : args.shift,
                 bar: args.empty? ? raise : args.shift)
    raise unless args.empty?
    super(foo:, bar:)
  end
    
  def initialize(foo, bar, foo: 42, bar:)
    super(foo:, bar:)
  end
  D[1, 2, foo: 3, bar: 4]
end
```
```ruby
# ko1's concern: the object is shared? it can be common pitfall.
Data.def(:foo, :bar, default: { bar: [] })
Data.def(:foo, :bar, default: { bar: [].freeze })
# naruse: it should deep-copy when a data is created

Data.def(:foo, :bar, default: { bar: -> { [] } })
```

timeup. default value needs more discussion

Conclusion:

* matz: I picked `Data.def`. I will reply
  * `D = Data.def(:foo, :bar)` defines a new subclass `D` of `Data`.
  * `D.new(...)` == `D[...]`
  * `D[1, 2]` # OK
  * `D[1]` raises "wrong number of arguments"
  * `D[1, 2, 3]` raises "wrong number of arguments"
  * `D[foo: 1, bar: 2]` #=> `D[1, 2]`
  * `D[foo: 1]` #=> raises "wrong number of arguments" (may be allowed in future if default values are introduced)
  * `D[foo: 1, bar: 2, baz: 3]` raises "wrong number of arguments"

### [[Feature #18934]](https://bugs.ruby-lang.org/issues/18934) Introduce method results memoization API in the core (zverok)

* New ticket, proposing and justifying simple `memoized def method_without_args`

Preliminary discussion:

```ruby!
class Test
  def foo
    puts "call!"
    5
  end

  memoized :foo

  def bar(x)
  end

  memoized :bar #=> an Exception?
end

o = Test.new
o.foo # prints "call!", returns 5
o.foo # returns 5 immediately
```

* ko1: This is Thread-unsafe? But the motivation is for the pure functions without arguments which return a same value so no problem.
* znz: What happens if it calls itself recursively?
* znz: If the object is frozen?
* ko1: I feel the word "memoize" is for argumented functions so I don't think the `memoized` is good name.
* znz: Does `Marshal.load(Marshal.dump(obj))` delete the cache?

Discussion:

* matz: I want to leave it to a libraries
  * mame: Author said library approach adds additional ivars like `@_memo_wise` and it can exposed at `JSON.dump`.
  * mame: `@_memo_wise` can be removed by using WeakMap
* matz: The word "memoize" is mainly for a method with arguments

Conclusion:

* matz: reject.


### [[Feature #18944]](https://bugs.ruby-lang.org/issues/18944) Add `SizedQueue#push(timeout:)` (byroot)

- Matz said this should be required separately.
- When implementing `Queue#pop(timeout:)` it was very evident this was needed.
- Spec is return `self` on success, `nil` on timeout, like `pop`.
- Useful to periodically do heartbeat, drop messages, etc when waiting to push on a queue.

Discussion:

* No one is against

Conclusion:

* matz: accepted.

### [[Feature #18885]](https://bugs.ruby-lang.org/issues/18885) End of boot advisory API (byroot)

- e.g. something like `RubyVM.prepare`, or `RubyVM.ready`.
- Would allow applications to notify the VM that they're done loading and about to process user input.
- Could be used by the VM to enable some optimizations, such as precomputing constant caches, etc.
- Particularly useful for forking server, but not only.

Preliminary discussion:

* ko1: I think the `prepare` is unclear so more descriptive name should be better.
* mame: Should we support more flexible API to control the behavior?

Discussion:

* matz: RubyVM is only for CRuby, so it is not so suitable
* akr: What and how efficient will this proposal bring?
* mame: according to puma, nakayoshi_fork reduced 10% memory
* akr: Puma can use nakayoshi_fork, so why it need to be built-in?
* mame: If built-in, it can do the same effect more efficiently than 3-times GC
---
* matz: How about a singleton method of "main" object? like `main#include`
* matz: Ah, it is not a good idea because we may want to call the method out of the toplevel
* matz: `Process.???` `GC.???`
* ko1: I have no idea
* nobu: I am okay for `RubyVM.???`
* eregon: `RubyVM` sounds OK to me if only for fork COW optimizations, but not so much if more general API

Conclusion:

* matz: Want a good name


### [[Bug #18955]](https://bugs.ruby-lang.org/issues/18955) `Kernel#sprintf` - `%c` ignores a non-ASCII character's encoding (jeremyevans0)

* Do we want the %c format specifier to transcode a string argument to the format string's encoding before taking the first character?

Preliminary discussion:

* nobu: Ruby does not transcode any string implicitly

```ruby=
format = "%c".encode("Windows-1251")
s = "Й".encode(Encoding::KOI8_U)
r = format % s

p r                  #=> "\xEA"

p r.encoding         #=> #<Encoding:Windows-1251>

p r.bytes == s.bytes #=> actual: true, expected: false

p r.bytes == s.encode("Windows-1251").bytes
                    #=> actual: false, expected: true
```

* nobu: The above behavior is by design. If a string is passed to `%c`, it converts the string to an Integer (by `ord`) and the integer should be encoded as the encoding of the format string.
* nobu: The following behavior is not intented

```ruby!
format = "%c".encode("UTF-8")
s = "Й".encode(Encoding::KOI8_U)
r = format % s
#=> actual: invalid byte sequence in UTF-8 (ArgumentError)
#=> expected (nobu): "ê" == format % s.ord
#=> expected (naruse): format % s should return "Й".encode(Encoding::KOI8_U)
```

* nobu: This is the current design, but I'm not negative to change it

```ruby
# current behavior/spec
# sprintf("%c", s)
enc = format.encoding
buff = String.new('', encoding: enc)
cp = s.force_encoding(enc)[0].ord    # clearly problem.
buff << cp.chr(enc)
```

Discussion:

* eregon: codepoints have a different meaning based on the encoding, so current behavior (use same codepoint value, ignore the encoding) might be surprising
* eregon: docs: `c   | Argument is the numeric code for a single character or a single character string itself.`.
* eregon: Current impl: `"%c" % string` implemented-as `"%c" % string.ord`
* naruse: it is by design, and I want to keep compatibility unless there is a real-world use case where we want to change the behavior
* eregon: it is kind of broken if used this way, except for Unicode encodings and 7-bit where codepoints have the same meaning
* matz: If `format` and `s` have incompatible encodings, I want `format % s` to raise an exception
* naruse: If `format` is US-ASCII and `s` is UTF-8, `"%c".encode("ASCII") % "あ"`

```ruby
"%c" % "あ"[0] #=> "あ"
"%c".encode("ASCII") % "あ" #=> %c requires a character (ArgumentError)
"%c".encode("ASCII") % "Й".encode(Encoding::KOI8_U) #=> invalid byte sequence in US-ASCII (ArgumentError)
```

* naruse: It looks like we need to study the current implementation first
* naruse: This spec is for compatibility of 1.8 that a character was represtened as an Integer

```ruby
"%c" % str[0] # str[0] returned an Integer in 1.8 era
```

* akr: `str[0]` in almost all old code returns 0..127 if they want to pass it to `"%c"` format
* akr: Usually, only `?\M-?` returned 8-bit character in 1.8 era


```ruby
format = "%s".encode("UTF-8")
s = "Й".encode(Encoding::KOI8_U)
r = format % s
p r #=> "\xEA"
p r.encoding #=> #<Encoding:KOI8-U>
```

* akr: `"%c"` should behave like `"%s"` (but only for first one codepoint)
* naruse: it may make sense

```ruby=
format = "%c".encode("Windows-1251")
s = "Й".encode(Encoding::KOI8_U)

# This is (almost) the same as "%"
r = format % s
p [r, r.encoding] #=> ["\xEA", #<Encoding:KOI8-U>]

# r is the same as s representation, the same encoding
```

Conclusion:

* akr: `"%c"` should behave like `"%s"` (but only for first one codepoint)
* matz: agreed with akr's proposal

### [[Bug #18956]](https://bugs.ruby-lang.org/issues/18956) `Kernel#sprintf` - `%c` handles negative `Integer` argument in a confusing way (mame)

Preliminary discussion:

```ruby=
# What should happen?
"%c" % -1000                 #=> invalid character (ArgumentError)
"%c".encode('US-ASCII') % -1 #=> 4294967295 out of char range (RangeError)

"%c" % -1 #=> "\xFF"
"%c" % -2 #=> "\xFE"
"%c" % -3 #=> invalid character (ArgumentError)
```

Discussion:

* naruse: it would be good to raise an exception
* nobu: it makes sense

Conclusion:

* matz: go ahead

### [[Bug #18958]](https://bugs.ruby-lang.org/issues/18958) `Kernel#sprintf` doesn't apply format sequence in some encodings (jeremyevans0)

* Should we raise an ArgumentError when the format string is in an incompatible encoding?
* The encodings listed in the issue are not the same as those that are not `ascii_compatible?`.
* If we do want to raise an exception for this, do we want a new encoding flag for this, or a hardcoded list of incompatible encodings?

Preliminary discussion:

```ruby
format = "%s".encode("UTF-16LE")
format % "foo" #=> "%s"

format.encoding.ascii_compatible? #=> false
```

Discussion:

* matz: Agreed with raising an exception
* naruse: Agreed
* nobu: Agreed

Conclusion:

* matz: Go ahead


### [[Bug #18797]](https://bugs.ruby-lang.org/issues/18797) Third argument to `Regexp.new` is a bit broken (jeremyevans0)

* Do we want to deprecate passing a third argument to Regexp.new?

Preliminary discussion:

```ruby=
/あ/n                      #=> SyntaxError
Regexp.new("あ", nil, "n") #=> actual: /あ/, expected: some error

# nobu: /あ/n is the same as the following
Regexp.new("あ", Regexp::NOENCODING) #=> RegexpError

# Need to explicitly convert it to ASCII-8BIT
Regexp.new("あ".b, Regexp::NOENCODING) #=> /\xE3\x81\x82/n

# expected option 1: allow the following
r = Regexp.new("あ", Regexp::NOENCODING)
p r.encoding #=> ASCII-8BIT

# expected option 2: raise on the following
r = Regexp.new("あ", nil, 'n') #=> RegexpError
```

nobu: I like option 1 and document that the third option is already non-recommended (but not deprecate yet).

```
ko1@aluminium:~$ gem-codesearch 'Regexp.new' | grep "'n'" | wc -l
352
ko1@aluminium:~$ gem-codesearch 'Regexp.new' | grep '"n"' | wc -l
9
```

```
$ gem-codesearch 'Regexp.new' | grep "'n'" | head
/srv/gems/01xinan-metasploit-framework-6.0.17/lib/rex/proto/dcerpc/handle.rb:    re = Regexp.new("(#{uuid_re}):(#{rev_re})\@(#{proto_re}):(.*?)\\[(.*)\\]$", true, 'n')
/srv/gems/01xinan-metasploit-framework-6.0.17/modules/auxiliary/scanner/sap/sap_mgmt_con_getprocessparameter.rb:        name_match = Regexp.new(datastore['MATCH'], [Regexp::EXTENDED, 'n'])
/srv/gems/01xinan-metasploit-framework-6.0.17/modules/exploits/windows/browser/ms10_042_helpctr_xss_cmd_exec.rb:    return str.unpack('H*')[0].gsub(Regexp.new(".{#{2}}", nil, 'n')) { |s| s.hex.to_s + "," }.chop
/srv/gems/Capcode-1.0.0/lib/capcode/ext/rack/urlmap.rb:            match = Regexp.new("^#{Regexp.quote(location).gsub('/', '/+')}(.*)", nil, 'n')
/srv/gems/DefV-soap4r-1.5.8.2/lib/xsd/charset.rb:  EUCRegexp = Regexp.new("\\A#{character_euc}*\\z", nil, 'n')
/srv/gems/DefV-soap4r-1.5.8.2/lib/xsd/charset.rb:  SJISRegexp = Regexp.new("\\A#{character_sjis}*\\z", nil, 'n')
/srv/gems/DefV-soap4r-1.5.8.2/lib/xsd/charset.rb:  UTF8Regexp = Regexp.new("\\A#{character_utf8}*\\z", nil, 'n')
/srv/gems/abaci-0.3.0/vendor/bundle/gems/rack-2.0.1/lib/rack/urlmap.rb:        match = Regexp.new("^#{Regexp.quote(location).gsub('/', '/+')}(.*)", nil, 'n')
/srv/gems/akamai_bookmarklet-0.1.2/vendor/gems/ruby/1.8/gems/rack-1.1.0/lib/rack/urlmap.rb:        match = Regexp.new("^#{Regexp.quote(location).gsub('/', '/+')}(.*)", nil, 'n')
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/rack-1.5.5/lib/rack/urlmap.rb:        match = Regexp.new("^#{Regexp.quote(location).gsub('/', '/+')}(.*)", nil, 'n')
```

```
https://github.com/rack/rack/blob/main/lib/rack/urlmap.rb#L40
/srv/gems/rack-2.2.4/lib/rack/urlmap.rb:        match = Regexp.new("^#{Regexp.quote(location).gsub('/', '/+')}(.*)", nil, 'n')
```

* mame: I vote for nothing to do. Just ignore inconsistency. Unless there is a problem but inconsistency

Discussion:

* matz: add document "the third parameter is obsolete" and do nothing else? 
* naruse: this feature is used by old code and they will not be updated. So I vote to remain the feature for compatibility.

Conclusion:

* matz: will reply


### [[Bug #18767]](https://bugs.ruby-lang.org/issues/18767) `IO.foreach` hangs up when passes `limit=0` (jeremyevans0)

* Is the current infinite loop in this case expected, or should it raise an ArgumentError as IO.readlines does?

Preliminary discussion:

```ruby=
IO.foreach('file.txt', 0) { |s| p s }
#=> actual: "", "", "", ...
#=> expected: invalid limit: 0 for foreach (ArgumentError)

open("file.txt").each_line(0) {} #=> invalid limit: 0 for each_line (ArgumentError)
open("file.txt").each_line(0).take(3) #=> not ["", "", ""] but raises an exception
```

Conclusion:

* matz: Raise an ArgumentError as suggested

### [[Feature #18408]](https://bugs.ruby-lang.org/issues/18408) Allow pattern match to set non-local variables (palkan)

 * That would make, in particular, `42 => @a` possible
   * This addition seems natural after we added non-local vars support for pinning
   * It also "restores" the original rightward assignment functionality
 * Here is a PR attached: https://github.com/ruby/ruby/pull/5426

Preliminary discussion:

```ruby=
@a = 42

# Now pinning for an instance var is allowed
42 in ^@a #=> true

# So, now `42 => @a` is not ambiguous 
42 => @a
```

```ruby
@a = nil

case [1, 2, 3]
in [@a, _, 4]
else
  # execute here!
  p @a #=> 1
end

case [1, 2]
in [a, ^(a+1)]
  # first + 1 == second
else
end

# ivar is allowed in right assignment
1 => @a
[1, 2, 3] => [@a, @b, @c]

# prohibit this
case 1
in @a
end
```

* ko1: ktsj concerns the intermediate state of pattern matching is observable from another thread

```ruby=
# if @ivar is allowed, other requests can be proposed:
42 => ary[0][0]
42 => obj.field
```

Discussion:

* mame: ktsj says side-effect of unmatched pattern is undefined behavior.

Conclusion:

* matz: I'd like to reject this once.


### [[Feature #18821]](https://bugs.ruby-lang.org/issues/18821) Expose Pattern Matching interfaces in core classes (palkan)

* Here is a PR adding `MatchData#{deconstruct,deconstruct_keys}` waiting for feedback: https://github.com/ruby/ruby/pull/6216

Preliminary discussion:

* ktsj: I will review

```ruby
/(?<hours>\d{2}):(?<minutes>\d{2}):(?<seconds>\d{2})/.match("18:37:22")
p $~.captures       #=> ["18", "37", "22"]
p $~.named_captures #=> {"hours"=>"18", "minutes"=>"37", "seconds"=>"22"}

$~ => h, m, s
$~ => minutes:, seconds:

$~.to_a     #=> ["18:37:22", "18", "37", "22"]
$~.captures #=> ["18", "37", "22"]

$~ => _, h, m, s
```

Discussion:

* matz: Looks good
* ktsj: deconstruct_keys looks good, but deconstruct may be arguable (to_a semantics, or capture semantics)
* matz: capture semantics is preferable

Conclusion:

* matz: will accept


### [[Feature #18159]](https://bugs.ruby-lang.org/issues/18159) Integrate functionality of syntax_suggest gem into Ruby

* Is it ok for syntax_suggest?

Conclusion:

* matz: looks good

### [[Feature #18949]](https://bugs.ruby-lang.org/issues/18949) Deprecate and remove replicate and dummy encodings (eregon)

* I will try to attend the dev meeting. Would it be possible to discuss this towards the end of the meeting, 16 JST (= 9am CEST) my preference, or if better 15 JST (= 8am CEST)?
* There are multiple things to decide, see https://bugs.ruby-lang.org/issues/18949#To-Decide
* 1. (`Encoding#replicate` and `rb_define_dummy_encoding()`) is most important (for complexity & performance) and given the extremely low usage it seems safe to deprecate and then remove
* 2. Handling the dummy UTF-16 and UTF-32 encodings, seems used very rarely but harder to assess. It causes significant performance overhead. Discussion e.g. in https://bugs.ruby-lang.org/issues/18949#note-23
* 3. Multiple devs want to keep (other) existing dummy encodings based on usages, so let's keep them for now. Maybe we could make them non-dummy or only available for transcoding in the future.

Discussion:

* We want to keep the possibility to create new encodings, e.g. for EBCDIC,...
* For dummy encodings, Encoding is a label. We don't want to complicate the implementation by having a separate kind of label.
* UTF-16 and UTF-32 have very subtle definitions (first character as BOM and/or ZWNBSP), to some extent, it is unavoidable that the user may need to use specific code.
    * see https://www.rfc-editor.org/rfc/rfc2781.html#section-3.3
* String processing should be very fast for UTF-8, speed for rarely used encodings isn't that improtant.
* Even eliminating a single check on the fast path may lead to speedups.
* Changing the encoding table needs a lock to work with Ractor,...

Conclusion:

* We will try to change the encoding lookup for stings so that the fast path gets faster, and less checks are involved, in a way that should reduce or eliminate locking.
    * https://github.com/ruby/ruby/blob/b0b9f7201acab05c2a3ad92c3043a1f01df3e17f/encoding.c#L423
* For 'UTF-16' and 'UTF-32', we agreed that there is no point in getting the endianness dynamically every time the string is accessed. We will try to change the implementation so that the endianness is evaluated only once (string creation time) and that information can then be reused. This will evaluate some checks in 'hot' code.
    * https://github.com/ruby/ruby/blob/b0b9f7201acab05c2a3ad92c3043a1f01df3e17f/string.c#L364
* matz agreed to deprecate and remove `Encoding#replicate`
* Other committers are unsure if we can remove `rb_define_dummy_encoding()`, but if eregon makes PRs to replace usages in existing gems it may be OK

2022-08-25
* UTF-16/32
    * akr: why such dynamic endian detection is needed?
    * naruse: Implemented for https://bugs.ruby-lang.org/issues/8940, but it doesn't have actual use case
    * naruse: the first 2/4 byte of a UTF-16/32 string is not sure whether it is a BOM or ZWNBSP. When people want to handle the string, they first need to decide it is BOM or ZWNBSP
    * akr: if so, they need to convert them first. What is the benefit of the feature? Maybe we can just remove the dynamic detection if we explain how to convert UTF/32 string into other encoding.
    * naruse: For example `str.encode("UTF-8", "UTF16")`, it assumes the first 2 bytes is a BOM, detect its endian, and convert it into UTF-8
    * Conclusion: Just remove the dynamic UTF-16/32 endian detection. People should convert the string first.
* rb_enc_from_index's lock
    * naruse: I don't know a use case which creates infinite dummy encodings though I know about ten dummies.
    * ko1: Some tests generates but they are tests
    * ko1: if we can use fixed sized array, it improves the performance
    * naruse: At this time we have 103 encodings. 256 must be sufficient (if there's a use case, they should repot to us)
    * Conclusion: Change the encoding table into fixed array whose size is 256

### [[Bug #18729]](https://bugs.ruby-lang.org/issues/18729) `Method#owner` and `UnboundMethod#owner` are incorrect after using `Module#public`/`protected`/`private` (eregon)

* Also [Bug #18435] and [Bug #18751], they are all the same issue basically.
* Let's attempt to stop hiding ZSUPER methods, this was never tried and I think would cause little incompatibilities. OK to try this?
* The semantic model after that change will finally make sense for users and Ruby implementers, instance_methods reflects the method table, owner means in which module's method table the method is.
* Also this will mean ZSUPER methods are just an internal implementation detail on CRuby, other Ruby impls are free to use something else like shallow-copy (like alias_method does) but instance_methods/owner/etc will be the same for the user.
* The hiding is actually "exposing" ZSUPER methods to Ruby users because other Ruby implementations don't use ZSUPER methods and don't hide the visibility-created methods.


Preliminary discussion:

```ruby!
class C
  private def foo = :C
end
    
class D < C
end

class E < D
  public :foo
  #=>
  # maybe eregon expect it?
  # this idea doesn't need zsuper

  # alias foo foo # make E#foo method entry
  public :foo
  define_method(instance_method(:foo)) # = alias behavior, proposed new behavior for public/protected/private
  # does this mean public is like alias_method, and copies method entry? (i.e., no ZSUPER method)
end

class D
  def foo = :D
end

E.new.foo
#=> current: :D
#=> eregon's proposal: :C

C.instance_method(:foo)
# => C, which returns :C
    
E.instance_method(:foo).owner
# => E or C/D?
```

eregon: this seems much better/simpler to me (both for implementation and semantics and implications (e.g., `Method#owner`, `instance_methods`)), already what JRuby & TruffleRuby do

Discussion:

```ruby
p E.instance_method(:foo).owner
#=> eregon's idea: #=> E
```

Conclusion:

* should not be an issue for compatibility (expectation: no one redefines after public or relies on that, would probably be unintentional if used that way)
* matz: change owner: OK, and remove ZSUPER method seems OK (the current illusion is incomplete)
* matz: my mental model is `public` only declares visibility not copy, but since it is the same in practice it seems fine
* eregon: make PR to remove ZSUPER method, use alias-like semantics (because ZSUPER method semantics leak and mental model is much simpler this way)

---

ko1: I still like the current behavir (I don't think `public` captures the method):

```ruby
class C
  def foo; :C1; end
end

class D < C
  public :foo
end

p D.new.foo

class C
  def foo; :C1; end
end

p D.new.foo
#=> current: :C2
#=> proposed: :C1 <- surprising for me.
```

### Many project (M:N) progress (ko1)

### [[Bug #18973]](https://bugs.ruby-lang.org/issues/18973) Kernel#sprintf: %c allows codepoints above 127 for 7-bits ASCII encoding

```ruby!
sprintf("%c".encode("US-ASCII"), 128) #=> "\x80"

sprintf("%c".encode("US-ASCII"), 128).valid_encoding? #=> false
```

```ruby!
s = "".force_encoding("US-ASCII")
s << 128
p s #=> "\x80"
s.encoding #=> #<Encoding:ASCII-8BIT>

s = "".force_encoding("US-ASCII")
s << 256 #=> 256 out of char range (RangeError)
```

### others (not discussed)

* opt_newarray_send
  * https://bugs.ruby-lang.org/issues/18897
* qnighy issues
  * ~~https://bugs.ruby-lang.org/issues/18877~~
  * https://bugs.ruby-lang.org/issues/18878
  * ~~https://bugs.ruby-lang.org/issues/18884~~
  * ~~https://bugs.ruby-lang.org/issues/18890~~
  * https://bugs.ruby-lang.org/issues/18883
* Ractor issues
  * https://bugs.ruby-lang.org/issues/17679
  * https://bugs.ruby-lang.org/issues/18814
  * https://bugs.ruby-lang.org/issues/18816
* rb_profile_frames
  * https://bugs.ruby-lang.org/issues/18907
* username/password of http_proxy envvar on Windows
  * https://bugs.ruby-lang.org/issues/18908
* ARGV.readlines and inplace_mode
  * ~~https://bugs.ruby-lang.org/issues/18909~~
  * ~~https://bugs.ruby-lang.org/issues/18920~~
* NotImplementedYetError
  * https://bugs.ruby-lang.org/issues/18915
