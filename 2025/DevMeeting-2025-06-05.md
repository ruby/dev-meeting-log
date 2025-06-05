# DevMeeting-2025-06-05

https://bugs.ruby-lang.org/issues/21379

## DateTime and location

* 2025/06/05 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/07/10 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Misc #21369]](https://bugs.ruby-lang.org/issues/21369) Propose Max Bernstein (@tekknolagi) as a core committer (k0kubun)

* It's detrimental to our productivity that such a core contributor isn't a committer. I want him to merge his PRs himself.

Conclusion:

* matz: approved


### [[Feature #21346]](https://bugs.ruby-lang.org/issues/21346) Introduce `String#ensure_suffix` (matheusrich)

- Is the naming good?
- If approved, should `String#ensure_prefix` be added as well?

Preliminary discussion:

```ruby
"Hell".ensure_suffix("o!")   # => "Hello!"
"Hello!".ensure_suffix("o!") # => "Hello!"

"Hello".ensure_suffix("o!") # => "Helloo!" or "Hello!"
```

Discussion:

* akr: the name is confusing. I wrongly expected `"Hello".ensure_suffix("o!")` to return `"Hello!"`
* matz: I don't see the use case
* ko1: `add_suffix_if_absent`
* matz: `"Hello".ensure_suffix("o!")` should return `"Helloo!"`
* matz: Pend `String#ensure_suffix!`and `String#ensure_prefix`
* mame: `"Hello".ensure_suffix("o!")` should return the receiver? or dup'ed new string instance?

Conclusion:

* matz: replied

### [[Feature #21389]](https://bugs.ruby-lang.org/issues/21389) Simplify `Set#inspect` output (jeremyevans0)

* I think Set#inspect should produce output similar to Array#inspect, not Object#inspect.
* Is using `Set[1, 2, 3]` for `Set#inspect` acceptable?
* Regardless of whether we change inspect output, do we want to include subclass name in inspect output ([Bug #21377])?

Preliminary discussion:

```ruby
p Set[1, 2, 3] #=> current: #<Set: {1, 2, 3}>
p Set[1, 2, 3] #=> proposed: Set[1, 2, 3]

Class.new(Set)[] #=> Set[] or #<Class:0x00000c21c78699e0>[]

class S < Set
end

# current
p S[1, 2, 3] #=> #<S: {1, 2, 3}>
```

```ruby
class A < Array
  def initialize
    self.replace [1, 2, 3]
  end
end

p A.new #=> [1, 2, 3]
```

Conclusion:

* matz: I am ok for either, but I accept `p Set[1] #=> Set[1]`
* matz: For a subclass, it should be displayed: `MySet[1]`


### [[Feature #21390]](https://bugs.ruby-lang.org/issues/21390) Deprecate passing arguments to `Set#to_set` and `Enumerable#to_set` (jeremyevans0)

* Array#to_a, Enumerable#to_a, Hash#to_h, Enumerable#to_h do not operate this way.
* This currently allows passing non-Set subclasses, with undesirable results.
* Can we deprecated in Ruby 3.5 and remove in Ruby 3.6?

Preliminary discussion:

```ruby
MySet = Class.new(Set)
set.to_set(MySet, *args, &blk) #=> MySet.new(set, *args, &blk)
```

```ruby
set.to_set(SortedSet)
SortedSet.new(set)
# pp Set.new(1..5) #=> #<Set: {1, 2, 3, 4, 5}>
```

https://gist.github.com/ko1/43fe5fda997a4e122914a423b92cbf78

Conclusion:

* Wait for knu


### [[Bug #21375]](https://bugs.ruby-lang.org/issues/21375) `Set[]` does not call `#initialize` (jeremyevans0)

* It was a deliberate design decision in core Set to call C functions directly and not use method dispatch.
* Hash.[] and Array.[] do not call Hash#initialize or Array#initialize, so Set behavior is consitent with that.
* There are many other places where core Set methods do not call other Set methods.
* For example, Set#initialize calls Set#add and Set#merge in stdlib Set, but not in core Set.
* Unless we plan to restore all internal method calls (undesirable for performance and inconsistent with other core classes), I don't think we should make this change.
* Can we close this?

Preliminary discussion:

```ruby
class MySet < Set
  def initialize(enum = nil)
    compare_by_identity
    super
  end
end

MySet.new.compare_by_identity?
# => true
MySet[].compare_by_identity?
# => false
```

FYI: gem-codesearch 'class .+ < Set\b'
https://gist.github.com/ko1/d4aa1f891c894cd192073a4ef927b250

Discussion:

* shyouhei: I agree with Jeremy. `Set.[]` would not call `#initialize` as `Array.[]` does not.

FYI: https://bugs.ruby-lang.org/issues/21396

Conclusion:

* Wait for knu


### [[Bug #21376]](https://bugs.ruby-lang.org/issues/21376) Inconsistent equality between `Set`s with different `compare_by_identity`, different classes (jeremyevans0)

* I belive this is a bug in stdlib Set that I fixed in core Set.
* Can we close this?

Preliminary discussion:

```ruby
o = Object.new
Set.new([o]).compare_by_identity == Set.new([o]) #=> true? false?
# old set impl. returned false
# core set impl. returns false
# Hash#== return false (respecting the flag of compare_by_identity)

class MySet < Set
end
Set.new([o]) == MySet.new([o]) #=> true? false?
# old set impl. returned true
# core set impl. returns false
# Hash#== return true (ignoring the class)

o = Object.new
Set.new([o]).compare_by_identity == MySet.new([o])
# old set impl. returned true #=> false
# core set impl. returns false

class H < Hash; end
p Hash[1,2,3,4] == Hash[1,2,3,4].compare_by_identity #=> false
p Hash[1,2,3,4] == H[1,2,3,4].compare_by_identity #=> false
```

* mame: I agree with Jeremy. This was a bug in the old Ruby Set.

Conclusion:

* Wait for knu

### [[Feature #21347]](https://bugs.ruby-lang.org/issues/21347) Add `open_timeout` as an overall timeout option for `Socket.tcp` (shioimm)

- I propose introducing an option to `Socket.tcp` that raises a timeout exception after a specified period has elapsed since the method started.
- If approved, is it ok to implement this to `TCPSocket.new` as well?

Discussion:

* shioi: If both `resolv_timeout` and `open_timeout` are specified, what should it do?
* akr: It should respect both
* mame: If both are specified, how about raising an exception?
* akr: It is acceptable

Conclusion:

* matz, akr: go ahead


### [[Misc #21385]](https://bugs.ruby-lang.org/issues/21385) Namespace: Suggesting a rename (eregon)

* "Namespace" seems to confuse many people (that feature is not about namespacing, modules do that, but about multiple execution contexts with their own `$LOAD_PATH`, `$LOADED_FEATURES`, copy of core classes, etc).
* Can we think of another name for that feature?
* How about `Context` or `Ruby::Context`?


Discussion:

* matz: I am okay to change the name, but there are no good other candidate
* matz: I like the name "Context" and "Zone" themselves, but I don't think they represents the feature in question
* matz: "Realm", "Package", "Capsule" seems no suitable neither

Conclusion:

*

### [[Feature #21335]](https://bugs.ruby-lang.org/issues/21335) Namespaces should be present in the backtrace

* Namespaces should be present in the backtrace

Preliminary discussion:

* Possible (or imaginable) options are:
    * A: Show fully-qualified namespace classpath in the backtrace: 
    * B: Show namespace id after `in`:

```
# A
foo.rb:9:in '#<Namespace:0x00007ff4d61f9f20>::Foo.test'
foo.rb:7:in '#<Namespace:0x00007ff4d61f93e0>::Foo.test'

# B
foo.rb:9:in namespace(1) 'Foo.test'
foo.rb:7:in namespace(X) 'Foo.test'
```

Discussion:

*

Conclusion:

* matz: I would like to pend this issue. I think Namespace should be a low-level API, and, I plan to provide a high-level API in the future (Package? or something). Showing a namespace in backtrace may obstucle the future high-level API

### [[Bug #21316]](https://bugs.ruby-lang.org/issues/21316) Namespaces leak with permanent names

```ruby
# main.rb
NS = Namespace.new
NS.require "./sub"
p NS::C.name #=> "C"

# sub.rb
class C; end
p C #=> expected: C
    #=> actual: NS::C
```


* Classes/modules defined in optional namespaces (by `Namespace.new`) are named as `NS1::Name`
    * This breaks users' expectation: `Name = Class.new; Name.name == 'Name'` in namespaces
    * Classes/modules should return names/classpath regarding namespace
    * (in the namespace which defined the class): `#name` returns `Name`
    * (in other namespaces): `#name` returns `NS1::Name`

Conclusion:

* matz: I want to pend this too. The best behavior will be decided in future when considering a high-level API. Tentatively, `Class#name` should not include a namespace regardless of which namespace.

### [[Feature #21365]](https://bugs.ruby-lang.org/issues/21365) Add `Namespace#eval`

* I would like a way to eval code on to a Namespace object.
    * Could we add an eval method that doesn't take a binding object?
    * Writing a new file every time I want to test Namespaces is too cumbersome.
* (tagomoris) It sounds fair and very convenient

Discussion:

* ko1: Does this accept only a String? Not a block?
* tagomoris: I think so

Conclusion:

* matz: If it is not difficult for tagomoris to implement, I accept

### [[Bug #21362]](https://bugs.ruby-lang.org/issues/21362) Namespace: Inline method caches poisoned with builtins

```ruby
class Integer
  def succ = self + 2
end

module Test
  def self.run = 10.times.to_a # [0,2,4,6,8] or [0,1,2,3,4,5,6,7,8,9]
end
```

* How should namespace affect on the (root-namespaced) methods which call other methods?
    * Methods in the root namespace doesn't want to be overridden by user definitions
    * Users expect that `#times` calls `#succ` and we can override it!

Conclusion:

* matz: This is local re-binding, which is prohibited by refinements.

### [[Feature #21262]](https://bugs.ruby-lang.org/issues/21262) Proposal: `Ractor::Port`

* `Ractor::Port` was introduced, `Ractor#value` (similar to `Thread#value`) was introduced, and `Ractor#take` was deleted.
* `Ractor#take` is almost similar, so I left `Ractor#take` with warning https://github.com/ruby/ruby/pull/13512 until the end of Aug, by eregon's request.
* Is it okay?

```ruby
r = Ractor.new{42}
r.value #=> 42 # wait and return the value of Ractor's block
r.value #=> 42
Ractor.new(r){|r| r.value} #=> Exception (only the first Ractor which calls `value` can call `value` again)
```

Conclusion:

* matz: go ahead. I agree to delete `Ractor#take`.

----

https://bugs.ruby-lang.org/issues/21219 `Object#inspect` accept a list of instance variables to display

* matz suggested `instance_variables_to_inspect`
* pp can respect not only `pretty_print_instance_variables` but also the new method

https://bugs.ruby-lang.org/issues/21279 Bare "rescue" should not rescue NameError

* matz: I will close

https://bugs.ruby-lang.org/issues/4539 Array#zip_with
https://bugs.ruby-lang.org/issues/21386 Introduce `Enumerable#join_map`

```ruby
ary1.zip(ary2).map {|a, b| a + b }
ary1.zip_map(ary2) {|a, b| a + b }
```

* matz: I will reject them

https://bugs.ruby-lang.org/issues/21206 Segmentation fault on ISeq#to_binary

https://bugs.ruby-lang.org/issues/14916 Proposal to add Array#===

https://bugs.ruby-lang.org/issues/21325 make ruby more middle-aged man friendly

https://bugs.ruby-lang.org/issues/21358 Advanced filtering support for #dig

https://bugs.ruby-lang.org/issues/21360 Inconsistent Support for `Exception#cause` in `Fiber#raise` and `Thread#raise`

* matz: Looks good

https://bugs.ruby-lang.org/issues/21359 Introduce `Exception#cause=` for Post-Initialization Assignment

* shyouhei: I don't get why `raise ..., cause:` is insufficient
* matz: negative

https://bugs.ruby-lang.org/issues/21361 Set execution file and line

https://bugs.ruby-lang.org/issues/21374 FrozenError message is inconsistent when a singleton method is defined on a frozen object

* matz: `can't modify frozen object` → `can't modify frozen Array` 

https://bugs.ruby-lang.org/issues/21337 Using `not` on the RHS of a logical operator becomes valid syntax with Prism

* matz: I prefer parse.y's behavior
* matz: `p(not 1)` should be rejected too

```
$ ruby -ve 'p(not 1)'
ruby 3.5.0dev (2025-06-02T23:12:09Z master 6f4eaa100f) +PRISM [x86_64-linux]
false
$ ruby --parser=parse.y -ve 'p(not 1)'
ruby 3.5.0dev (2025-06-02T23:12:09Z master 6f4eaa100f) [x86_64-linux]
-e:1: syntax error, unexpected integer literal, expecting '('
p(not 1)
ruby: compile error (SyntaxError)
```

```ruby
1 && p 1
1 && not true && true
```

* matz: Prism must be fixed and the fix should be backported

https://bugs.ruby-lang.org/issues/21378 variable pinning does not look for method arguments

https://bugs.ruby-lang.org/issues/21381 Different error messages when mixing `it` and `_1` in block for Prism and parse.y

https://bugs.ruby-lang.org/issues/21382 Syntax for arguments in || is more strict than arguments in ()

```
p lambda { | x, y = x + 1 | x + y }.call(1)
```

* matz: will reject

https://bugs.ruby-lang.org/issues/21384 const_added is triggered twice when using autoload

https://bugs.ruby-lang.org/issues/21391 Inconsistent trailing slash behavior of File.join and Pathname#join with empty strings

```
$ ls ruby
ruby
$ ls ruby/
ls: cannot access 'ruby/': Not a directory
$ ls ""
ls: cannot access '': No such file or directory
```

### namespace issues

https://bugs.ruby-lang.org/issues/21318

```ruby
# main.rb
p Module.nesting #=> []
Namespace.new.require "./sub"

# sub.rb
p Module.nesting #=> expected: [], actual: [#<Namespace:24,user,optional>]
```

* matz: It should return `[]`

https://bugs.ruby-lang.org/issues/21320

```ruby
# lib.rb
X = :top
class C
  X = :super
end
class D < C
  p X
end

# main.rb
require "./lib" #=> :super
Namespace.new.requrie "./sub"

# sub.rb
require "./lib" #=> :top
```

* matz: Both should show :super

https://bugs.ruby-lang.org/issues/21339

* `load_iseq` is defined in the main namespace
* `Kernel#require` searches `load_iseq` hook in the root namespace, so it fails to find the definitoion

https://bugs.ruby-lang.org/issues/21343

```ruby
# main.rb
String.singleton_class.singleton_class::Foo = 123

ns = Namespace.new
ns.require("./sub.rb")

p String.singleton_class.singleton_class::Bar #=> expected: error, actual: 456

# sub.rb
p String.singleton_class.singleton_class::Foo #=> expected: error, actual: 123
String.singleton_class.singleton_class::Bar = 456
```


https://bugs.ruby-lang.org/issues/21364
https://bugs.ruby-lang.org/issues/21363

```ruby
# main.rb
Bar = 456

ns = Namespace.new
ns.load "./ns.rb"

ns::TEST.call #=> expected: [123, sub namespace]
              #   actual: [456, main namespace]

# ns.rb
Bar = 123

TEST = -> { p [Object::Bar, Namespace.current] }
```
