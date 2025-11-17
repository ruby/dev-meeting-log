# DevMeeting-2025-11-13

https://bugs.ruby-lang.org/issues/21647

## DateTime and location

* 2025/11/13 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/12/11 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21665]](https://bugs.ruby-lang.org/issues/21665) Revisit `Object#deep_freeze` to support non-Ractor use cases (headius)

- matz: I have two concerns:
  - how do we handle object traversal (what if the object graph is recursive)
- headius: "Just expose what Ractor does now" could be a solution here.
- matz: second one:
    - In case of ractor, classes are sharable without freezing.  Should they also in case of deep_freeze?
- headius: in case of [ice9](https://github.com/dkubb/ice_nine) classes are not frozen, which could be an option ("just a object graph" could be what users want)
- matz: we want to make decision here, whether we we actually freeze everything reachable, or special-case classes etc.
- matz: personally I prefer classes/modules to also be frozen.
- headius:  I don't know where the freeze stops then.  Classes inherit parent classes, which eventyally freezes ::Object, under whose namespace everything resides,...
- matz: What if we freeze the classes themselves but not its parents.
- headius: Could there be a compromise where a object can have distinction between which part of itself is frozen and which part won't, like #inspect can be overridden to hide some info.
- mame: I expect `[String].deep_freeze` to be (almost) equal to `[String].each { it.freeze }`. Does it freeze the `String` class?
- matz: `[String].deep_freeze` freezes String, OTOH `["string"].deep_freeze` does not freeze String(the class of `"string"`).
- ko1: Are you poposing `Object#deep_freeze`?
- headius: Rather `Object.deep_freeze`, Object's singleton method that is non-overridable.
- ko1: OK.
- ko1: In case of ractor `Ractor.make_sharable` stops when it encounters what is already sharable.  Is it okay that deep_freeze also stops when it finds a frozen object?
  ```ruby
  ary = [[]].freeze # ary is frozen, ary[0] is not.
  ```
- headius: Right.  Deep_freeze can be equivalent to make_sharable.
- ko1: Maybe we need to introduce a new protocol to freeze everything, or give up that part.
- headius:  Right.  This is an open problem that we need to look into.

```ruby
class Object
  def deep_freeze
    ivars.each{|e| e.deep_freeze}
    self.freeze
  end
end

class Array
  def deep_freeze
    self.each{|e| e.deep_freeze}
    super
  end
end

class Thread
  def deep_freeze
    raise ...
  end
end

class Module
  def deep_freeze
    # ignore
  end
end
```

- matz: I prefer to introduce method protocol.
- akr: Marshal should be remembered.  The code above could not work for complex object graph (might recurse forever). Also `Marshal` has customization protocol feature, marshal_dump and marshal_load.
- headius: Yes.

Conclusion:
- headius:  thanks, I'll have conversation with others and will bring a proposal to the issue.


### [[Feature #20205]](https://bugs.ruby-lang.org/issues/20205) Enable `frozen_string_literal` by default (eregon)

* Let's decide in which release the chilled string deprecation warning shows up regardless of verbosity level (R1) to make progress on this. How about 4.0?
* Otherwise in 10 years all Ruby files will still have to begin with `frozen_string_literal: true` to not be a performance trap.

Discussion:

* mame: Are you still on it matz?
* matz: I heard this is not a big performance boost...
* naruse: "Frozen string literals do not make things faster enough" is not a recent news though.
* mame: freezing literals only is the crupit
* matz: I think it is natural because I'm a C programmer.
* akr: I also think it is natural because it is lexically one object.
* mame: This issue has to be split into two
  * whether we should default warn
  * whether we should default freeze

Conclusion:

* matz: 4.0 does not introduce additional deprecation warnings.

### [[Bug #21654]](https://bugs.ruby-lang.org/issues/21654) `Set#new` calls extra methods compared to previous versions (tenderlovemaking)

* Set#new is calling "size" on its input, this is causing extra database queries in our app
* The bug was introduced in d4020dd5faf28486123853e7f00c36139fc07793 in order to detect `Set.new(0..)`
* I think we should revert d4020dd5faf28486123853e7f00c36139fc07793 and allow `Set.new(0..)` to hang

Discussion:

Conclusion:

matz: go ahead. let leave it to knu

### [[Feature #20163]](https://bugs.ruby-lang.org/issues/20163) Add `Integer#popcount` (tenderlovemaking)

* Matz asked for real-world use cases, and I provided one
* Bit arrays can be used to represent undirected graphs, and "popcount" allows us to count edges

Discussion:

* shyouhei: What it is really needed is `String#popcount`, isn't it?
  ```ruby
  "\xff".popcount                #=> 8
  "\x01\x01\x01".popcount        #=> 3
  "\x01\x02\x03".popcount(1..2)  #=> 3 (byte index?)
  "\x01\x02\x03".popcount(8..15) #=> 3 (bit index?)
  ```
* akr: Endian is a concern here!
* mame: if we only concern 8 bits each, we can do this using 256-element lookup table.
* matz: `String#popcount` sounds esoteric to me as of today.  There should be other bit-related operations (getbit etc.) first, if there really needs be a popcount.
* ko1: `IO::Buffer` could be a better fit.

Conclusion:

* shyouhei: Let me withdraw my String#popcount proposal.
* matz: I'll continue discussion on the issue.


### [[Bug #20409]](https://bugs.ruby-lang.org/issues/20409) Missing reporting some invalid breaks (kddnewton)

* The main point of the issue is already resolved in both parsers
* `END { break }` is not a syntax error in any parser
* Any reason not to treat `END { break }` the same as `BEGIN { break }` which is invalid?
* Implementation for prism here https://github.com/ruby/prism/pull/3707

Discussion:

* shyouhei: What should I do when I want to bail out of `END`?
* matz: You don't.  There is no such thing like an abnormal enf of `END`.
* ko1: Oh yes there is?  We can do `END {next}`.
* tompng:  But we cannot do `BEGIN {next}`...

```ruby
END {
  break
  p :not_reached
}

BEGIN { next } # keep it as-is (SyntaxError)
END { next }   # keep it as-is (escape from END block)

proc{
  break #=> break from proc-closure (LocalJumpError)
}.call
```

Conclusion:

* matz: let me consider it a while
* matz: `BEGIN {next}`/`END {next}` need different ticket(s).

### [[Bug #21498]](https://bugs.ruby-lang.org/issues/21498) Windows - Ruby Overrides C Library APIs thus breaking them (alanwu)

* TL;DR linking with ruby.dll overrides some libc symbols on Windows
* The ticket proposes to stop all overrides, is that acceptable?
* If not, removing the override for specifically fclose() seems to practically fix the problem for reports cited in the ticket. How about that?
* If we keep the fclose() override, it's hard or impossible for C99 extensions to access the [standardized](https://devblogs.microsoft.com/cppblog/c11-and-c17-standard-support-arriving-in-msvc/) version of the function.

Conclusion:

* nobu: I want to know the reason why the file descriptor is corrupted by the overriding

### [[Feature #21637]](https://bugs.ruby-lang.org/issues/21637) tracepoint: add support for global variables read/write events (viralpraxis)

* There seems to be no straightforward way to trace gvar reads/writes in runtime
* `Kernel.trace_var` only works when gvar name is known in advance
* `global_variables` hash does not provide enough granularity
* that would be beneficial for a runtime analysis Ruby tools I'm working on
* it’s unclear whether "special" gvars (like `$!` or `$LOAD_PATH`) should be traced

Preliminary discussion:

* ko1: Should we allow to trace an assignment to `$!`, `$0`, etc.?
* ko1: If we will have `trace_var` and `TracePoint.new(:gvar_set)`, which should be fired first?
* ko1: use case?

Discussion:

* ko1: I don't say it's strange, but I wonder if it's requierd in practice.
* shyouhei: could be used by a debugger?
* mame: ivar trace is more important, isnt?

Conclusion:

* ko1: continue to discuss

### [[Feature #21678]](https://bugs.ruby-lang.org/issues/21678) `Enumerable#rfind` (kddnewton)

* This method would be useful for finding the last instance of something in a list.
* There is precedence with index/rindex.
* A lot of people are using reverse_each.find, which is not as nice.
* Can I add this method?

Preliminary discussion:

* ko1: It should be introduced as `Array#rfind`, not `Enumerable`, if it is really needed?

Discussion:

* matz: This can hardly be better re-written than `reverse_each.find` .
* nobu: Alternatively: `find_all.last` is very slightly different from rfind, but is very similar, and could be written in a better space efficient manner.
* mame: Actually all the use cases that OP shows is against Array so Array#rfind could just suffice.

```ruby
class Array
  def rfind
    (size - 1).downto(0) do |i|
      return self[i] if yield self[i]
    end
  end
end

class Enumerable
  def rfind(...) = to_a.rfind(...)

  def find_last
    last_found = nil
    each { last_found = it if yield it }
    last_found
  end
end
```

1. Introduce `Array#rfind`
2. Introduce `Array#rfind`and `Enumerable#rfind`
3. Introduce `Enumerable#find_last`(or another name)
4. Do nothing

* mame: matz, which do you like?
* matz: `Array#rfind`is acceptable, but ...

Conclusion:

* matz: not an immediate NG, but `reverse_each.find` could just be ... okay?
* matz: I will reply

---

### [[Feature #21552]](https://bugs.ruby-lang.org/issues/21552) allow `String.strip` and similar to take a parameter similar to `String.delete`

```ruby
"fooab".chomp("ab") #=> "foo"
"fooba".chomp("ab") #=> "fooba"

"fooab".strip("ab") #=> "foo"
"fooba".strip("ab") #=> "foo"

"fooab".strip(/[ab]/) #=> "foo"
"fooba".strip(/[ab]/) #=> "foo"

"foobar".strip(/[[:space:]]/)
"()()( )main( )()()".strip(/\(\s*\)/)
"foobar".strip(/[[:space:]](a)\1(?=foo)/)

while self.match_from_last(re)
  self.sub_from_last!(re, "")
end
```

Conclusion:

* matz: we want to make the motivation clear

### [[Feature #7845]](https://bugs.ruby-lang.org/issues/7845) Strip doesn't handle unicode space characters in ruby 1.9.2 & 1.9.3 (does in 1.9.1)

### [[Feature #21554]](https://bugs.ruby-lang.org/issues/21554) Which `make` should be supported?

### [[Feature #20437]](https://bugs.ruby-lang.org/issues/20437) Could the licensing conditions be made less ambiguous?
### [[Bug #21625]](https://bugs.ruby-lang.org/issues/21625) Allow `IO#wait_readable` together with `IO#ungetc`
### [[Bug #21634]](https://bugs.ruby-lang.org/issues/21634) Combining read(1) with eof? causes dropout of results unexpectedly on Windows
### [[Feature #21619]](https://bugs.ruby-lang.org/issues/21619) logger: Context API
### [[Feature #21617]](https://bugs.ruby-lang.org/issues/21617) Add Internationalized Domain Name (IDN) support to URI

### [[Feature #21520]](https://bugs.ruby-lang.org/issues/21520) Feature Proposal: `Enumerator::Lazy#tee`

```ruby
module Enumerable
  def tee(...)
    each(...)
    self
  end
end

(1..).     tee { puts it     }.each { ... } # not needed?
(1..).lazy.tee { puts it     }.each { ... }
(1..).lazy.map { puts it; it }.each { ... }
(1..).lazy.each { p it; ... } # isn't it good enough?
```

### [[Feature #17316]](https://bugs.ruby-lang.org/issues/17316) On memoization

```ruby
instance_variable_set_unless_defined(:@foo) do
  long_calc
end
instance_variable_set_if_absent(:@foo) do
  long_calc
end
```

akr: now because of object shape, "assign if absent" pattern is not a fast style.

### [[Feature #12282]](https://bugs.ruby-lang.org/issues/12282) `Hash#dig!` for repeated applications of Hash#fetch
### [[eature #21545]](https://bugs.ruby-lang.org/issues/21545) `#try_dig`, a dig that returns early if it cannot dig deeper
### [[Bug #21551]](https://bugs.ruby-lang.org/issues/21551) Ractor isolation error points to the wrong place
### [[Feature #21564]](https://bugs.ruby-lang.org/issues/21564) Extend `permutation`, `repeated_permutation`, `combination` and `repeated_combination` arguments

```ruby
# find right combination of letters
def brute_force
  [*'a'..'z'].repeated_permutation(*3..6).find do |chars|
    correct_combination?(chars)
  end
end

(3..6).flat_map { [*'a'...'z'].repeated_permutation(it).to_a }.find { ... }

def brute_force
  (3..6).each do |n|
    [*'a'..'z'].repeated_permutation(n) do |chars|
      return chars if correct_combination?(chars)
    end
  end
end
```

---

### [[Bug #21622]](https://bugs.ruby-lang.org/issues/21622) Prism wrongly accepts command call to be a key of keyword argument

### [[Bug #21618]](https://bugs.ruby-lang.org/issues/21618) Allow to use the build-in prism version to parse code

### [[Misc #21630]](https://bugs.ruby-lang.org/issues/21630) Suggest @Earlopain for core contributor

### [[Feature #15590]](https://bugs.ruby-lang.org/issues/15590) Add dups to Array to find duplicates

### [[Bug #21446]](http://bugs.ruby-lang.org/issues/21446) StackOverflow when changing visibility in reopened refinement
### [[Feature #21637]](https://bugs.ruby-lang.org/issues/21637) Tracing global variable assignment
### [[Misc #21646]](https://bugs.ruby-lang.org/issues/21646) Propose Luke Gruber as a Ruby committer
### [[Feature #21642]](https://bugs.ruby-lang.org/issues/21642) Introduce `IO::ConnectionResetError` and `IO::BrokenPipeError` as standardized IO-level exceptions
### [[Bug #19017]](https://bugs.ruby-lang.org/issues/19017) Net::HTTP may block when attempting to reuse a persistent connection

---

### [[Feature #15381]](https://bugs.ruby-lang.org/issues/15381) Let double splat call `to_h` implicitly

```ruby
h = {
  **({a: 1} if some_condition),
  **({b: 2) if another_condition),
} # This already works since 3.4
```

```ruby
ValidOptions = Struct.new(:ssl, :host, :port)
opts = ValidOptions.new
opts.port = 999
setup_request(**opts) # should implicitly call opts.to_h or opts.to_hash?
```

```ruby
h = {}
h[:a] = 1 if some_condition
h[:b] = 2 if another_condition
```

* matz: will reject

### [[Feature #21653]](https://bugs.ruby-lang.org/issues/21653) Unify Hash methods and preserving default/default_proc


### [[Feature #21675]](https://bugs.ruby-lang.org/issues/21675) Advent of Pattern Matching

```ruby
e = Exception.new('something bad happened')
e in Exception('something bad happened')   # => true
e in Exception(/good/)                     # => false

e = KeyError.new('something bad happened', key: :foo)
# (1) KeyError should return all constructor arguments?
e.deconstruct #=> ['something bad happened', { key: }] ???
e in Exception('something bad happened')   # => always false???

# (2) KeyError should respect superclass's deconstruct interface?
e.deconstruct #=> ['something bad happened'] ???
e in Exception('something bad happened')   # => works, but we cannot get key

case Gem::Version.new("3.2.1.rc.1")
in [*dotted_components]
end

Gem::Version.new("3.rc.1").segments
#=> [3, "rc", 1]

case request
in method: "POST", path: %r{^/api/}
  handle_api_request(request)
in method: "GET", path: "/"
  handle_root
end
```
