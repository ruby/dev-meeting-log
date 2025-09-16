# DevMeeting-2025-09-11

https://bugs.ruby-lang.org/issues/21549

## DateTime and location

* 2025/09/11 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/10/23 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18878]](https://bugs.ruby-lang.org/issues/18878) parse.y: Foo::Bar {} is inconsistently rejected (yui-knk)

* Do we also support `Foo::Bar do end` (and `Foo::Bar do end + 1`)?

Preliminary discussion:

```ruby
# current master
Foo::Bar {}     # Prism:OK, parse.y:OK
Foo::Bar {} + 1 # Prism:OK, parse.y:NG (yui-knk: should we allow this?)
Foo::Bar do end # Prism:NG, parse.y:NG (yui-knk: should we allow this too?)
```

Conclusion:

* matz: I want to allow them: `Foo::Bar {} + 1`, `Foo::Bar do end`, and `Foo::Bar do end + 1`


### [[Feature #17398]](https://bugs.ruby-lang.org/issues/17398) SyntaxError in endless method (yui-knk)

* Patch for `private def hello = puts "Hello"` (make command as an argument for method call if there is only argument) is created.
* Want to know what matz thinks about it.

Conclusion:

* yui-knk: I have already a reply from Matz in the ticket. No need to discuss this today.

### [[Bug #21097](https://bugs.ruby-lang.org/issues/21097)] x = a rescue b in c and def f = a rescue b in c parsed differently between parse.y and prism
* I am working on this ticket and have some questions about grammar where I'd like to ask for Matz's opinions (https://bugs.ruby-lang.org/issues/21097#note-10).

Discussion:

* yui-knk: (long time to explain https://bugs.ruby-lang.org/issues/21097#note-10)

Conclusion:

* matz: I will answer kaneko-san's questions
* naruse: I will respond that prism should be compatible with Ruby 3.3 syntax (unless the spec change was clearly approved by matz)


### [[Bug #21168]](https://bugs.ruby-lang.org/issues/21168) Prism doesn't require argument parentheses (in some cases) when a block is present but parse.y does (earlopain)

* This was previously discussed in a dev meeting https://bugs.ruby-lang.org/issues/21134
* matz said he prefers `parse.y` behaviour
* I found different code examples in the same spirit that are accepted by `parse.y` (and `prism`)
* Is such code supported or should it also be consistently rejected?

Discussion:

```ruby
foo bar baz
foo bar(baz)
foo(bar baz)
foo(
  bar baz do
  end
)

expect(foo).to eq bar baz
expect(foo).to(
  eq bar bazzzzzzzz
)
```

```
$ ruby --dump=p --parser=parse.y -e 'foo(
  bar baz {}
)'
###########################################################
## Do NOT use this node dump for any purpose other than  ##
## debug and research.  Compatibility is not guaranteed. ##
###########################################################

# @ NODE_SCOPE (id: 8, line: 1, location: (1,0)-(3,1))
# +- nd_tbl: (empty)
# +- nd_args:
# |   (null node)
# +- nd_body:
#     @ NODE_FCALL (id: 0, line: 1, location: (1,0)-(3,1))*
#     +- nd_mid: :foo
#     +- nd_args:
#         @ NODE_LIST (id: 7, line: 2, location: (2,2)-(2,12))
#         +- as.nd_alen: 1
#         +- nd_head:
#         |   @ NODE_FCALL (id: 1, line: 2, location: (2,2)-(2,12))
#         |   +- nd_mid: :bar
#         |   +- nd_args:
#         |       @ NODE_LIST (id: 6, line: 2, location: (2,6)-(2,12))
#         |       +- as.nd_alen: 1
#         |       +- nd_head:
#         |       |   @ NODE_ITER (id: 5, line: 2, location: (2,6)-(2,12))
#         |       |   +- nd_iter:
#         |       |   |   @ NODE_FCALL (id: 2, line: 2, location: (2,6)-(2,9))
#         |       |   |   +- nd_mid: :baz
#         |       |   |   +- nd_args:
#         |       |   |       (null node)
#         |       |   +- nd_body:
#         |       |       @ NODE_SCOPE (id: 4, line: 2, location: (2,10)-(2,12))
#         |       |       +- nd_tbl: (empty)
#         |       |       +- nd_args:
#         |       |       |   (null node)
#         |       |       +- nd_body:
#         |       |           @ NODE_BEGIN (id: 3, line: 2, location: (2,11)-(2,11))
#         |       |           +- nd_body:
#         |       |               (null node)
#         |       +- nd_next:
#         |           (null node)
#         +- nd_next:
#             (null node)
```

```
# parse.y should accept this (if possible)
foo(
  bar baz do
  end
)
```

* matz: only one command is allowed in call_args
 
```
# Currently Prism allows this, but it should be prohibited
foo(
    bar baz do
    end, 1, 2, 3
)
```

```
# Accepted by both <= matz: it should be accepted
foo(
  bar baz {
  }
)
# Accepted by both <= matz: it should be accepted
(
  bar baz do
  end
)
```

```
# yui-knk: are these allowed too?
# matz: allow them
ary[bar baz do end]
ary[bar baz do end] = 1
ary[bar baz do end] ||= 1

# yui-knk: these are not allowed, right?
# matz: do not allow them
def m(x = bar baz do end); end
def m(x = bar baz); end
```
Conclusion:

* matz: I will reply

### [[Feature #21555]](https://bugs.ruby-lang.org/issues/21555) Add support for predicate attribute reader names (eregon)

* As noticed by @Dan0042 there are now 7 tickets asking the same feature.
* Would matz be OK to add it? 9 years ago matz said no in https://bugs.ruby-lang.org/issues/12046#note-3 but the many requests clearly prove interest in this feature.
* The workaround `def foo? = @foo` is not ideal because this is still "just a getter" and those are typically done with `attr_reader`, and it's also slower (`attr_reader` has special optimizations and can be handled without a method call, even without a JIT).
* The other workaround `attr_reader :foo; alias_method foo? foo; remove_method :foo` preserves the performance optimization but is awkward and long.
* If it's accepted, any volunteer to implement it?

Conclusion:

* matz: Please write `def foo? = @foo`.

---

```ruby
ary.sort_by { [it.field1, it.field2] }
ary.sort {|a, b| (a.field1 <=> b.field1).nonzero? || (a.field2 <=> b.field2) }
[a.field1, a.field2] <=> [b.field1, b.field2]
```

---

https://bugs.ruby-lang.org/issues/21039
https://bugs.ruby-lang.org/issues/21557

* (1) new sharable proc rule: don't allow sharable proc if the outer variable is reassigned.
    * ```ruby
      a = 1
      Ractor.sharable_proc{ a } #=> OK

      b = 1
      Ractor.sharable_proc{ b } #=> Error because `a` will be assigned twice
      b = 2
      
      # it is safe, but it will also raises an exception
      def foo c
        c = 2 if cond
        Ractor.sharable_proc{ c } #=> Error because `a` will be overwritten.
      end
      
      d = 1
      pr = proc{ d = 2 }
      Ractor.sharable_proc{ d } #=> Error because `a` will be assigned twice
      pr.call
      ```
* (2) `Ractor.new` should also use this rule.
    * ```ruby
      a = 42
      Ractor.new{ p a } # proposal
      Ractor.new(a){|a| p a} # current
      ```
* (3) `define_method` should use this method as a default, and if it raises an exception, it use non-Ractor sharable method.
    * ```ruby
       a = 42
       define_method(:foo){ a }
       #=> ractor sharable method foo, implicitly
        
       b = 43
       define_method(:bar){ b }
       #=> ractor *un*sharable method bar, implicitly
       b = 44
      ```
  ```ruby
  a = 0
  define_method(:foo){ a }
  binding.local_variable_set(:a, 1) # incompatible
     ```

* (4) `Ractor.sharable_lambda` is not needed.
   * ```ruby
     # to make shareable lambda, it is enough.
     Ractor.sharable_proc(&lambda_proc)  
        ```

```ruby
def example1
  a = 1
  Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
  a = 2 #=> Exception <- misunderstanding
end

def example2
  a = 1
  a = 2
  Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
end

def example3
  a = 1
  1.times{ a = 2 }
  Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
end

def example4
  while true
    a = 0
    Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
  end
  
  x = 0
  1.times{
    x = 1
    Ractor.shareable_proc{ x } #=> Exception because "x" is reassigned
  }

  i = 1
  while true
    a = i
    Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
    i += 1
  end
  
  for i in [1, 2]
    a = i
    Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
  end

  begin
    a = 1
    Ractor.shareable_proc{ a } #=> Exception because "a" is reassigned
  rescue
    retry
  end
end

def example5
  a = 1
  Ractor.shareable_proc { a #=> 1 (not 2) } 
  #=> NOT Exception because it is too difficult to detect

  binding.local_variable_set(:a, 2)
end

def good name, param
  define_method :foo do
    param
  end
end

def define_method name, &pr
  begin
    pr = Ractor.shareable_proc(&pr)
  rescue IsolationError
  end
  old_define_method name, &pr
end

[-"foo", -"bar"].each do |name|
  define_method(name) do
    name
  end
end

def define_counter
  cnt = 0
  define_method(:get){ cnt }
  define_method(:inc){ cnt += 1}
  #=> both unshareable
end

name = define_method(:foo) do
  name #=> nil? or :foo?
end

####
# a: uninitialize
a = 1
# a: initialized
a = 2
# a: reassigned

###
# locals: [:a, :b, :c] => { a: shareable, b: not-shareable, c: not-shareable }

a = 1
while true
  b = 1
end
c = 1
c = 2
```

Conclusion:
(1) matz: okay as best effort
(2) matz: okay let's try
(3) matz: okay let's try
(4) matz: now let's remain.

---

prism issues

https://bugs.ruby-lang.org/issues/20925 Allow boolean operators at beginning of line to continue previous line
https://bugs.ruby-lang.org/issues/21528 SyntaxError#message may have broken encoding with multibyte source under Prism
https://bugs.ruby-lang.org/issues/21540 prism allows foo && return bar when parse.y doesn't

---

https://bugs.ruby-lang.org/issues/21520 Feature Proposal: Enumerator::Lazy#tee
https://bugs.ruby-lang.org/issues/17316 On memoization

```ruby
instance_variable_set_unless_defined(:@foo) do
  long_calc
end
instance_variable_set_if_absent(:@foo) do
  long_calc
end
```

akr: now because of object shape, "assign if absent" pattern is not a fast style.

(There was no time to discuss the following)

https://bugs.ruby-lang.org/issues/12282 `Hash#dig!` for repeated applications of Hash#fetch
https://bugs.ruby-lang.org/issues/21545 `#try_dig`, a dig that returns early if it cannot dig deeper
https://bugs.ruby-lang.org/issues/21551 Ractor isolation error points to the wrong place
https://bugs.ruby-lang.org/issues/21552 allow `String.strip` and similar to take a parameter similar to `String.delete`
https://bugs.ruby-lang.org/issues/21554 Which `make` should be supported?
https://bugs.ruby-lang.org/issues/20163 Introduce `#bit_count` method on Integer
https://bugs.ruby-lang.org/issues/20437 Could the licensing conditions be made less ambiguous?
https://bugs.ruby-lang.org/issues/21564 Extend `permutation`, `repeated_permutation`, `combination` and `repeated_combination` arguments
