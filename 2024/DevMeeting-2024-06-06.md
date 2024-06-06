# DevMeeting-2024-06-06

https://bugs.ruby-lang.org/issues/20435

## DateTime and location

* 2024/06/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/07/11 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets
### [[Bug #20504]](https://bugs.ruby-lang.org/issues/20504) Interpolated string literal in regexp encoding handling (kddnewton)

* I think there is an inconsistency here that we can solve by using the same codepath for creating regexps.
* Could we change this? It should be minimal impact.

Preliminary discussion:

```ruby
# encoding: us-ascii

interp = "\x80"
regexp = /#{interp}/
p regexp.encoding #=> #<Encoding:BINARY (ASCII-8BIT)>

regexp = /#{"\x80"}/ #=> invalid multibyte character: /\x80/
```

Discussion:

* Kevin: One way or another this is inconsistent right now.
* shyouhei: I guress nobody actually cared until now.
* shyouhei: no new code should rely on this gotcha but is there any existing code?
* Kevin: Is it possible for old codes to raise exceptions?
* shyouhei: maybe not.  The syntax error happens for literals, which means the source file becomes unloadable.
* Kevin: agreed.

Conclusion:

* matz: Let's just be consistent.  `/#{"\x80"}/` should be a binary encoded regexp.

### [[Bug #20478]](https://bugs.ruby-lang.org/issues/20478) Circular parameter syntax error rules (kddnewton)

* I think there is an inconsistency here, impacted by the presence of pipes.
* Could we change this to be consistent? Either all reads at depth 0 or all reads at any depth.

Preliminary discussion:

```ruby
# syntax error
def foo(bar = -> { bar }) end       # no lambda parameters
def foo(bar = ->() { bar }) end     # no lambda parameters
def foo(bar = baz { bar }) end      # no block parameters
def foo(bar = baz { _1 + bar }) end # parameters, but no pipes
def foo(bar = baz { it + bar }) end # parameters, but no pipes
```

```ruby
# no syntax error
def foo(bar = ->(baz) { bar }) end   # lambda parameters
def foo(bar = baz { || bar }) end    # no block parameters but empty pipes
def foo(bar = baz { |qux| bar }) end # block parameters
```

```ruby
def foo(bar = bar + 1) end
def foo(bar = bar) end
```

```ruby
bar = bar + 1
#=> undefined method `+' for nil (NoMethodError)

bar = -> { bar.call }
bar.call #=> infinite loop
```

* https://bugs.ruby-lang.org/issues/10314
* mame: this warning was introduced just for compatibliity to very old behavior before 2.1.
* mame: I guess no one was confused by accidentally writing `def foo(x = x)` or something
* mame: I think now we can just remove this error

Discussion:

* mame: I guess nobody would write `def foo(bar=bar)`
* ko1: Actually we intentionally changed `def foo(bar=bar)` behaviour in 2.0 era.  Before, `bar=bar` assigned a return value of method `bar` to a variable `bar`.  cf: [#10314](https://bugs.ruby-lang.org/issues/10314)
* matz: I'd like to lean on syntax erros.
* mame: Should `def foo(bar = ->(baz) { bar })` also be an error?  the `bar` can be visible.
* matz: What should be the value of `bar` ? nil?
* ko1: It would also make sense for them to just evaluate to `nil`.  `def foo(bar=bar)` is already SyntaxError.
* naruse: I don't think `bar = ->{bar.()}` can be banned.
* knu: there would be no way to make a recursive lambda then.  :+1: for allowing this.
* matz: not entirely persuadad but OK.
* akr: why you prefered errors?
* matz: I think earlier error should benefit.  But agree with @knu that we need that kind of lambdas.

Conclusion:

* matz: We don't change ordinal ( non-argument ) local variables, at least.
* matz: Let's make `def foo(bar=bar)` assign `nil` to `bar`, even when there is a method namely `bar`.
* matz: I accept the removal of all the warnings/exceptions

### [[Misc #20488]](https://bugs.ruby-lang.org/issues/20488) Document source file size restrictions (kddnewton)

* Could we restrict source files to 32-bits?
* It would help on memory size for parsers.

Preliminary discussion:

```ruby
eval 'pp caller(0)', binding, 'hoge', (2 ** 31 - 1)
#=> ["hoge:2147483647:in `<main>'", "t.rb:2:in `eval'", "t.rb:2:in `<main>'"]

eval 'pp caller(0)', binding, 'hoge', (2 ** 31)
#=> win64: in `eval': bignum too big to convert into `long' (RangeError)
#=> linux: in 'Kernel#eval': integer 2147483648 too big to convert to 'int' (RangeError)
```

* mame: currently, negative lineno is allowed
```ruby
eval("raise", nil, "foo", -(2**31))
#=> foo:-2147483648:in '<top (required)>': unhandled exception

eval("raise", nil, "foo", -(2**31)-1)
#=> integer -2147483649 too small to convert to 'int' (RangeError)
```
* mame: there is a dirty hack to `eval("# some magic comment\n# and something\n" + src, nil, file, -1)`
* nobu: I have used the hack for some case

Discussion:

* ko1: This is about cruby's documentation, not ruby in general?
* Kevin: yes but  all rubies would eventually use it.
* mame: In fact one could pass negative numbers to eval.  The documentation should at least document about it.
* shyouhei: I don't remember where do we save this info.  They were packed into NODE's flags before but not any longer?
* Kevin: It's not stored anyware actually.

Conclusion:

* matz: I agree with making the limits
  * maximum line: signed 32 bits
  * maximum column: unsigned 32 bits
  * maximum file byte size: unsigned 32 bits

### [[Bug #20433]](https://bugs.ruby-lang.org/issues/20433) Hash.inspect for some hash returns syntax invalid representation (jeremyevans0)

* Some symbols when used as hash keys results in `Hash#inspect` returning invalid syntax.
* Most compatible fix is only using spaces around `=>` when necessary.
* Most consistent fix would be always using spaces around `=>`.
* Alternative fix would be to have `Symbol#inspect` quote the symbols, even though quotes aren't required outside of usage as a hash key.
* Which approach should be used?

Preliminary discussion:

```ruby
{ :a! => 1 } # {:a!=>1}
{ :a? => 1 } # {:a?=>1}
{ :== => 1 } # {:===>1}
{ :< => 1 }  # {:<=>1}
{ :+ => 1 }  # {:+=>1} # parseable!
{ :- => 1 }  # {:-=>1} # parseable!
{ :* => 1 }  # {:*=>1} # not parseable!
{ :/ => 1 }  # {:/=>1} # not parseable!
{ :/ => 1 }  # {:%=>1} # not parseable!
{ :< => 1 }  # {:<=>1}   # not parseable!
{ :<= => 1 } # {:<==>1}  # parseable!
{ :> => 1 }  # {:>=>1}   # not parseable!
{ :>= => 1 } # {:>==>1}  # parseable!
{ :<=> => 1} # {:<=>=>1} # parseable!

# Sol. 0
{ :== => 1 } # {:===>1} # no change

# Sol. 1
{ :== => 1 } # {:== =>1}
{ :a! => 1 } # {:a! =>1}
{ :key => 1 } # {:key=>1}

# Sol. 2
{ :== => 1 } # {:== => 1}
{ :a! => 1 } # {:a! => 1}
{ :key => 1 } # {:key => 1}

# Sol. 3
{ :== => 1 }  # {:"=="=>1}
{ :a! => 1 }  # {:"a!"=>1}
{ :key => 1 } # {:key=>1}

# Sol. 4
{ :== => 1, :key => 2 } # {"==": 1, key: 2}
{ :== => 1, :key => 2, "str" => 3 } # {"==": 1, key: 2, "str" => 3}
```

Discussion:

* shyouhei: It's very cryptic right now, at least.
* mame: Jeremy proposes to add spaces (always / when needed).
* matz: "when needed" is not as easy as it sounds.
* matz: I don't bother if it evaluates back.  But user experience does matter.
* knu: Solution #3 is a natural extension to the status quo.  `{:'foo bar' => 1}.inspect`  already quotes.
* matz: I'd prefer Solution #2.
* mame: would break tests though.
* shyouhei: DO NOT PARSE `#inspect` OUTPUT. period.
* mame: I'd push Solution #4 if we are allowed to break tests.

Conclusion:

* ~~mame: Let's take solution #3.  Quote symbols that match `/\W\z/`.~~
* matz: Evaluate how solution #4 breaks existing tests.  Can someone create a patch?

### [[Bug #20043]](https://bugs.ruby-lang.org/issues/20043) `defined?` checks for method existence but only sometimes (jeremyevans0)

* I have a pull request to fix this (needs an update for prism).
* However, @ko1 mentioned we may want to deprecate `defined?` when used with expressions.
* Do we want to deprecate such usage of `defined?`?

Preliminary discussion:

```
$ ./miniruby -e'p defined?(a)'
nil
$ ./miniruby -e'p defined?([a])'
nil
$ ./miniruby -e'p defined?([*a])'
"expression"

$ ./miniruby -e'p defined?(itself)'
"method"
$ ./miniruby -e'p defined?(itself(a))'
nil
$ ./miniruby -e'p defined?(itself(*a))'
"method"

$ ./miniruby -e'p defined?([[[[a]]]])'
nil
$ ./miniruby -e'p defined?({ a => a })'
"expression"
```

```ruby
defined?(foo.bar.baz.qux) # foo.bar.baz is evaluated normally, and check if qux is defined on the object

defined?(foo)
defined?($foo)
defined?($1)
defined?(@@foo)
defined?(@foo)
defined?(Foo)
defined?(yield)
defined?(super)
defined?(foo.bar)
```

Discussion:

* matz: all of them should return nil
* akr: what's `defined` supposed to do?
* nobu: "evaluates right before the last method call", implementation wise.
* mame: would make sense to restrict what can be passed to it.
* matz: I don't think it's worth breaking existing codes.

~2011
https://gist.github.com/ko1/b31517a5037d55bbe50e7f12d79b9fc1

Conclusion:

* matz: I agree that `defined?([*a])`, `defined?(itself(*a))`, and `defined?({ a => a })` should all return nil.
* matz: I am not positive for removal of the currently-allowed expressions
* ko1: I will investigate the use cases of `defined?` in gem codesearch

### [[Bug #19749]](https://bugs.ruby-lang.org/issues/19749) Confirm correct behaviour when attaching private method with `#define_method` (jeremyevans0)

* I think the behavior of not changing method visibility is definitely a bug.
* Last reviewed at the August 2023 developer meeting, still waiting on a decision from @matz.

Preliminary discussion:

- [2023-07-13 discussion](https://github.com/ruby/dev-meeting-log/blob/master/2023/DevMeeting-2023-07-13.md#bug-19749-confirm-correct-behaviour-when-attaching-private-method-with-define_method--jeremyevans0)
- [2023-08-24 discussion](https://github.com/ruby/dev-meeting-log/blob/master/2023/DevMeeting-2023-08-24.md#bug-19749-confirm-correct-behaviour-when-attaching-private-method-with-define_method-jeremyevans0)

```ruby
class C
  def foo; end

  private

  define_method(:foo, instance_method(:foo))
  # current: the method foo is kept public
  # expected: the method foo is changed private
end
```

Conclusion:

* matz: It is good to fix the issue. Accept the change

### [[Bug #18622]](https://bugs.ruby-lang.org/issues/18622) const_get still looks in Object, while lexical constant lookup no longer does (jeremyevans0)

* It would be nice to have consistent behavior.
* Last reviewed at the June 2023 developer meeting, still waiting on a decision from @matz.
  * https://github.com/ruby/dev-meeting-log/blob/master/2023/DevMeeting-2023-06-08.md#bug-18622-const_get-still-looks-in-object-while-lexical-constant-lookup-no-longer-does-jeremyevans0
      * `matz: I will reply`

Preliminary discussion:


```ruby
module ConstantSpecsTwo
  Foo = :cs_two_foo
end

module ConstantSpecs
end

p ConstantSpecs.const_get("ConstantSpecsTwo::Foo") # => :cs_two_foo
p ConstantSpecs::ConstantSpecsTwo::Foo # => const_get.rb:9:in `<main>': uninitialized constant ConstantSpecs::ConstantSpecsTwo (NameError)
```

Discussion:

* shyouhei: `const_get` cannot be changed.  We see there are use cases depend on this behaviour in the wild.

Conclusion:

* matz: I'm just writing a reply right now... replied.

### [[Bug #20513]](https://bugs.ruby-lang.org/issues/20513) the feature of kwargs in index assignment has been removed without due consideration of utility, compatibility, consistency and logic (bughit)

- it doesn't make sense to remove a feature because there are fixable edge-case bugs in its implementation
- it doesn't make sense from a higher level language design perspective to allow kwargs in index but not in index assignment
- instead of removal, index assignment should get proper/real kwargs and the multiple assignment problem should be fixed

Preliminary discussion:

* https://bugs.ruby-lang.org/issues/20218

```ruby
class Foo
  def []=(idx, opt={}, val)
  end
end

foo = Foo.new
foo[42, kw: "foo"] = :val
```

```ruby
ary[kw:1] = 1
# keyword arg given in index assignment (SyntaxError)
```

https://gist.github.com/ko1/ed58eaef0cbcb552ce430ef9be3a7221

```
["gem_date/2009/11/23/amp-pure-0.5.0/lib/amp/server/repo_user_management.rb",
 (183,10)-(183,56),
 "REPOS[:url => repository] = {:private => true}"]
["gem_date/2009/03/12/colincasey-sequel-2.10.4/spec/sequel_core/dataset_spec.rb",
 (1665,4)-(1665,27),
 "@d[:a => 1] = {:x => 3}"]
["gem_date/2009/03/23/epugh-sequel-0.0.0/spec/core/dataset_spec.rb",
 (1668,4)-(1668,27),
 "@d[:a => 1] = {:x => 3}"]
{:total=>3}
2010
["gem_date/2010/04/22/viking-sequel-3.10.0/spec/core/dataset_spec.rb",
 (2071,4)-(2071,27),
 "@d[:a => 1] = {:x => 3}"]
["gem_date/2010/04/22/viking-sequel-3.10.0/spec/integration/dataset_test.rb",
 (539,4)-(539,26),
 "@ds[:a=>20] = {:b=>30}"]
["gem_date/2010/03/13/amp-0.5.3/lib/amp/server/repo_user_management.rb",
 (183,10)-(183,56),
 "REPOS[:url => repository] = {:private => true}"]
["gem_date/2010/11/29/rocket-server-0.0.4/lib/rocket/server/session.rb",
 (34,8)-(34,60),
 "subscriptions[channel => connection.signature] = sid"]
["gem_date/2010/11/02/bike-0.2.3/lib/_storage/sequel.rb",
 (158,8)-(158,47),
 "@dataset[:full_name => full_name] = val"]
["gem_date/2010/11/02/bike-0.2.3/lib/_storage/sequel.rb",
 (168,6)-(170,7),
 "@dataset[:full_name => v[:full_name]] = v.merge("]
{:total=>6}
2011
{}
2012
["gem_date/2012/09/25/ntable-0.1.6/test/tc_basic_values.rb",
 (108,8)-(108,51),
 "table_[:column => :blue, :row => 5] = \"bar\""]
["gem_date/2012/09/25/ntable-0.1.6/test/tc_basic_values.rb",
 (118,8)-(118,54),
 "table_[1 => 2, 0 => ::NTable.index(5)] = \"bar\""]
["gem_date/2012/09/25/ntable-0.1.6/test/tc_basic_values.rb",
 (140,8)-(140,43),
 "t1_[:row => 0, :column => :red] = 1"]
["gem_date/2012/09/25/ntable-0.1.6/test/tc_basic_values.rb",
 (142,8)-(142,43),
 "t2_[:row => 0, :column => :red] = 1"]
["gem_date/2012/06/14/rarity-0.1.1/lib/rarity/tracker.rb",
 (34,8)-(34,185),
 "@db[:images][:path=>cleanpath] = {:png_o_level => options[:png_o_level], :after_bytes=>results[:after], :time_taken=>(results[:time]+ @db[:images][:path=>cleanpath].time_taken)}"]
["gem_date/2012/12/27/fluent-plugin-couchbase-0.0.2/lib/fluent/plugin/out_couchbase.rb",
 (52,8)-(52,67),
 "connection[record.delete('key'), :ttl => self.ttl] = record"]
{:total=>6}
2013
2013
["gem_date/2013/04/22/hikkmemo-1.0.0/lib/hikkmemo/worker.rb",
 (33,16)-(33,65),
 "@db[:threads][:id => tid] = { :last_post => pid }"]
["gem_date/2013/10/20/multi_git-0.0.1/lib/multi_git.rb",
 (13,2)-(13,45),
 "BACKENDS[   :git, priority: 0] = GitBackend"]
["gem_date/2013/10/20/multi_git-0.0.1/lib/multi_git.rb",
 (14,2)-(14,48),
 "BACKENDS[:rugged, priority: 1] = RuggedBackend"]
["gem_date/2013/10/20/multi_git-0.0.1/lib/multi_git.rb",
 (15,2)-(15,46),
 "BACKENDS[  :jgit, priority: 2] = JGitBackend"]
["gem_date/2013/09/02/NetApp.rb-0.1.2/lib/netapp.rb",
 (168,16)-(172,17),
 "plexes[name: plx.child_get_string(\"name\")] = { "]
["gem_date/2013/09/02/NetApp.rb-0.1.2/lib/netapp.rb",
 (277,16)-(281,17),
 "plexes[name: plx.child_get_string(\"name\")] = { "]
["gem_date/2013/09/02/NetApp.rb-0.1.2/lib/netapp.rb",
 (471,12)-(473,13),
 "result[qtree: key.child_get_string(\"qtree\")] = {"]
["gem_date/2013/09/02/NetApp.rb-0.1.2/lib/netapp.rb",
 (484,12)-(491,13),
 "result[id: key.child_get_string(\"id\")] = {"]
["gem_date/2013/09/02/NetApp.rb-0.1.2/lib/netapp.rb",
 (549,12)-(555,13),
 "result[qtree: key.child_get_string(\"qtree\")] = {"]
["gem_date/2013/09/04/gentooboontoo-gphys-1.3.1.1/lib/numru/gphys/gphys.rb",
 (1508,3)-(1508,19),
 "gg['y'=>1] = 999"]
{:total=>10}
2014
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (19,6)-(19,39),
 "x[:x => 1 , :y => 1, :a => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (23,6)-(23,29),
 "x[:x => 1, :y => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (31,6)-(31,38),
 "x[:x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (32,6)-(32,38),
 "x[:x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (33,6)-(33,38),
 "x[:x => 1, :y => 1, :z => 3] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (35,6)-(35,38),
 "x[:x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (43,4)-(43,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (47,4)-(47,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (55,4)-(55,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (62,4)-(62,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (63,4)-(63,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (66,4)-(66,54),
 "y[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (67,4)-(67,54),
 "y[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (74,4)-(74,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (75,4)-(75,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (78,4)-(78,18),
 "y[:z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (79,4)-(79,18),
 "y[:z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (87,4)-(87,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (88,4)-(88,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (89,4)-(89,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 3] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (91,4)-(91,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (92,4)-(92,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (93,4)-(93,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 3] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (106,4)-(106,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (107,4)-(107,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 2"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (108,4)-(108,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 3] = 3"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (110,4)-(110,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 1] = 4"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (111,4)-(111,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 2] = 5"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (112,4)-(112,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 3] = 6"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (123,4)-(123,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 1] = 4"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (124,4)-(124,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 1, :z => 2] = 5"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (125,4)-(125,54),
 "x[:i => 1, :j => 1, :x => 1, :y => 2, :z => 3] = 6"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (135,4)-(135,27),
 "x[:x => 2, :y => 2] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (145,4)-(145,36),
 "x[:x => 1, :y => 1, :z => 1] = 1"]
["gem_date/2014/02/13/multimatrix-0.6/test/test_multimatrix.rb",
 (146,4)-(146,36),
 "x[:x => 2, :y => 1, :z => 1] = 1"]
{:total=>35}
2015
["gem_date/2015/05/15/olfactory-0.2.1/spec/olfactory/dictionary_spec.rb",
 (10,6)-(10,84),
 "Olfactory.dictionaries[:streets][:borough => 1, :street => \"BROADWAY\"] = 10001"]
2016
{}
2017
{}
2018
{}
2019
["gem_date/2019/03/19/gphys-1.5.6/lib/numru/gphys/gphys.rb",
 (1604,3)-(1604,19),
 "gg['y'=>1] = 999"]
{:total=>1}
2020
{}
2021
{}
2022
["gem_date/2022/05/19/simplygenius-atmos-0.14.0/lib/simplygenius/atmos/cli.rb",
 (193,12)-(193,51),
 "Atmos.config.[]=(k, v, additive: false)"]
{:total=>1}
2023
{}
2024
{}
```

Discussion:

* mame: So we prohibited keyword arguments inside of arrays.  The OP complains that they did use that feature.
* matz: How did that behave?
* mame: It was as if a hash was passed to the method.
* ko1: I searched for older gems and it seems there _were_ very little but actual usages.
* ko1: Should we ignore them or not?
* matz: Hmmmmm
* mame: Honestly I don't want people to use this though.

Conclusion:

* matz: Let me communicate in #20218

### [[Feature #19998]](https://bugs.ruby-lang.org/issues/19998) Emit deprecation warnings when the old (non-Typed) Data_XXX API is used (byroot)

- Marked as deprecated in the documentation as of Ruby 2.3.0 (2015)
- Yet I found gems using it without realizing `TypedData` is preferred.
- Proposed patch emit a deprecation warning when the gem is compiled: https://github.com/ruby/ruby/pull/10872

Discussion:

* mame: It seems nobody is against issuing deprecation warnings.
* shyouhei: Nobody hardly reads the compile-time output though. `gem install` doesn't show them.
* byroot: The intended audience is gem authors, they see warnings when they run `bundle exec rake compile`

Conclusion:

* matz: go ahead

----

* hsbt: I'm concerning that 3.4 has much more warnigns than before.

### [[Bug #20518]](https://bugs.ruby-lang.org/issues/20518) Escaped-newline in %W (akr)

* I think it is a bug but fixing it causes (hopefully small) incompatibility.

Preliminary discussion:

```ruby
'x\
foo\bar'   #=> current: "x\\\nfoo\\bar"

"x\
foo\bar"   #=> current: "xfoo\bar"

%w[x\
foo\bar]   #=> current: ["x\nfoo\\bar"]
%w[a\ b c] #=> current: ["a b", "c"]

%W[x\
foo\bar] #=> current: ["x\nfoo\bar"]
         #=> expected?: ["xfoo\bar"]
%W[x#{"\
"}foo\bar]

%I[x\
foo\bar] #=> [:"x\nfoo\bar"]

%q[x\
foo\bar] #=> "x\\\nfoo\\bar"
```

Discussion:

* akr: I thought it was a bug at first sight.  Not that sure right now though.
* akr: newline in `%w` could either be a word separator, line separator, or a verbatim newline.  Which one should this be?
* matz: I know what is going on.
* mame: I can live with the current situation.
* akr: I now think we don't have to risk breaking backwords compatibility for this one.

Conclusion:

* akr: Am trying to improve documents.


### [[Misc #20407]](https://bugs.ruby-lang.org/issues/20407) Question about applying encoding modifier to an interpolated Regexp (andrykonchin)

* The documentation states that a Regexp with only `US-ASCII` characters has `US-ASCII` encoding, otherwise a regular expression is assumed to use the source encoding. This can be overridden with encoding modifiers.
* It isn't clear enough how a Regexp encoding (that Regexp#encoding method returns) is calculated in case an encoding modifier (e.g. `u`, `e`, etc) is specified for a Regexp with interpolation (e.g. `/a #{ "b".encode("windows-1251") } c/e`).
* The encoding modifier is applied in some cases and isn't in other ones what seems confusing at first glance.
* It seems result depends on a source encoding, characters of the Regexp literal fragments and encoding of Regexp interpolated fragments as well.

Discussion:

* nobu: `/.../e` is merely a hint.  The contents of the regular expression should be honoured.
* mame: It's very hard for me to consider `//e` as a hint.  It doesn't make sense for instance `//i` to be ignored.
* akr: We would better remove `//e`.
* mame: If we are going to remove should we try hard describing what is going on here right now?

Conclusion:

* nobu: Consistent or not the current situation is that "if a regular expression, after interpolated, solely consist of US-ASCII characters, then it honours the encoding suffix.  Otherwise the contents wins".
* akr: Deprecation of `//e` shall be a separate topic.


### [[Bug #20416]](https://bugs.ruby-lang.org/issues/20416) IO#read doesn't preserve buffer encoding if `maxlen = nil` (andrykonchin)

* `IO#read` and similar methods when called with buffer argument preserve its encoding.
* But `IO#read` doesn't do so in case the `maxlen` argument is `nil`.

Discussion:

* nobu: I have commented to the ticket.

Conclusion:

* matz: agreed with nobu. Closing


### [[Bug #20319]](https://bugs.ruby-lang.org/issues/20319) Singleton class is being frozen lazily in some cases (andrykonchin)

* When an object becomes frozen (with `#freeze` method call) only its own singleton class becomes frozen immediately.
* Classes in the singleton classes chain become frozen only when they are returned to user with `#singleton_class` method call.
* The object's singleton class' singleton class (and so on) doesn't become frozen even if it was already instantiated
* This lazy freezing can be observed by a user when he gets a singleton class of the object's singleton class before freezing the object. After freezing the object the instantiated singleton class is still not frozen.
* There might be several options (1) don't change anything, 2) freeze all the instantiated singleton classes in a chain immediately or 3) don't freeze singleton classes in a chain at all and stop freezing even an object's singleton class) and there is a PR with fix (https://github.com/ruby/ruby/pull/10245) but it's unclear what option is the best one.

Discussion:

* mame: Should a singleton class' singleton class shall also be frozen when the object gets frozen?
* shyouhei: An object's singleton class also gets frozen when the object gets frozen is a twist.
* mame: But there seems no way other than that to prevent adding a singleton method to a frozen object.
* matz: Is it difficult to recursively freeze everything?
* nobu: Not really.  Jeremy already has a patch to do so.
* (reviewing Jeremy's patch)
* mame: What is "shared singleton class"? The patch follows the attached_object chain to make sure it matches the original object. Why is this process needed?
* shyouhei: I think there was a bug in the past where the singleton class was shared, but I don't know if that had anything to do with it.
* mame: According to Jeremy's comment, it appears that a naive fix broke `TestModule#test_frozen_singleton_class` failed and he found this hack
* mame: Looks like no one can understand this patch immediately. It would be difficult to review the patch in this meeting.

Conclusion:

* matz: We cannot determine anything without deeper understanding

### [[Feature #6648]](https://bugs.ruby-lang.org/issues/6648) Provide a standard API for retrieving all command-line flags passed to Ruby (eregon)

* @nobu are you OK with `RbConfig.ruby_args`? If not can you suggest a better place?

Discussion:

* mame: What is needed is currently unable to obtain from `::ARGV`.
* matz: I'm OK with the feature.
* nobu: I'm against adding it to `RbConfig`.
* mame: @eregon is against adding it to `RubyVM`
* nobu: Maybe `Process`?

----

* mame: is it possible to execute the current ruby in Windows?
* nobu: No.
* shyouhei: There is no way to exec in Windows?
* nobu: Yes there is but there is no way to reconstruct C level argv from what we currently have.
* shyouhei: It's NG then.  What is impossible is impossible.
* mame: I don't say it is impossible, but I strongly feel a bad smell for this API

Conclusion:

* mame: I will reply

### [[Feature #20443]](https://bugs.ruby-lang.org/issues/20443) Allow Major GC's to be disabled

* @ko1 (Koichi Sasada) is against the name disable_major
* I would like advice from committers about what an appropriate name for this method should be.

Discussion:

* matz: I think I would like to have this feature.
* ko1: `GC.disable_major` or `GC.disable_major_gc`?
* ko1: `GC.needs_major?` or `GC.needs_major_gc?` or `GC.need_major_gc?`
* ko1: "major GC" is a GC terminology. I prefer "disable_major_gc".
* matz: what we currently do with what we call the "major GC" highly depends on our current GC implementation.  Having this method could make other pluggable algorithms harder.
* byroot: the plan is for plugable GC to declare what options they support. If they don't support one method like this, it behave as `NotImplemented`.
    * matz: OK then.
* shyouhei: What happens when there are more than 3 generations is not clear to me.

* `Gc.major` does a major GC: https://ocaml.org/manual/5.2/api/Gc.html

* byroot: `GC.start` calls it `full_mark`, so it’s also an option I guess
* nobu: `GC.disable_full_mark` or `GC.disable(full_mark: true)` or `GC.disable(full_mark: false)`
  * ko1: true? false? keyword argument is too confusing
  * byroot: keyword arguments are hard to feature test. e.g. `GC.respond_to?(:disable_full_mark)` is easy to use in gems etc.
    * mame: makes sense
* matz: I prefer `GC.disable_major` and `GC.need_major?`(no `s` of `needs`)

* byroot/ko1: How about more generalized setting mechanism?

```ruby
GC.configure(
  full_mark: false,
  mmtk_something: 42,
  mmtk_other_thing: 4422,
)

# for the current GC impl
p GC.get_config #=> { enabled: true, full_mark: false, stress: false, auto_compact: true, ... }

# for the mmtk-based GC
p GC.get_config #=> { mmtk_something: 42, mmtk_other_thing: 4422, ...}
```

* byroot: I like this because it helps plugable GC, but what would be the behavior on unknown keys? Ignored or error?
  * nobu: error?
  * byroot: if error, then we need a way to know what keys are accepted, otherwise it makes supporting multipe Ruby versions hard.
* byroot: Also I think `GC.config` and `GC.config = {}` would feel a bit more natural?
* byroot: Feature testing can be done with `GC.config.key?(:full_mark)`

* mame: it is difficult to decide the details of the setting mechanism today. We should decide which approach (individual method like `GC.disable_major` or grand setting API) is preferred
* matz: I prefer the generalized one
* nobu: me too
* ko1: me too
* byroot: how to we handle `GC.need_major?` with the generalized solution? `GC.config[:need_major]`? Or do we keep `GC.need_major?` as a method.
  * mame: good point
* ko1: `GC.latest_gc_info` can contain information
  * byroot: good idea. 
  * `GC.latest_gc_info[:need_major_by]`
* ko1: we can extend `GC.latest_gc_info(:need_major_by)` like `GC.stat(key)`
  * nobu: it is  already supported.
  * ko1: it can lack the information so we may need to introduce new key.

Conclusion:

* matz: `GC.configure`-like API should be explored

### [[Bug #20314]](https://bugs.ruby-lang.org/issues/20314) Simultaneous Timeout expires may raise an exception after the block (mame)

* I need a wisdom from committers on how to fix nested timeouts.

Preliminary discussion:

```ruby
def task1
  Timeout.timeout(0.5, B) do
    nil while true
  rescue B
    log("timeout")
  end
rescue B
  log("timeout/outer")
ensure
  final1
  final2
end

def task2
  Timeout.timeout(0.1, A) do
    task1
    task3
  end
rescue Timeout::Error
end
```

```ruby
Timeout.timeout(0.1, A) do # A
  Timeout.timeout(0.5, B) do # B
    # A のタイムアウトを一時停止して、B を 0.1 秒待つ（A の待ち時間）
    # stop_outer_timeout
    # timeout = outer < innner ? outer : innner
    sleep 1 # 0.1 秒後に B が投げられる
  ensure
    # ここに到達した時点で A を再開する restart_outer_timeout
    # が、0.1 秒たっているので、A を raise する
  end
ensure
end
```


```ruby
Timeout.timeout(0.5, A) do # A
  Timeout.timeout(0.1, B) do # B
    # A を一時停止
    sleep 1 # 0.1 秒後に B が投げられる
  rescue B
    sleep 1 # これを Aで止めたいけど、A は一時停止しているので止められない
  ensure
    # ここに到達した時点で A を再開する restart_outer_timeout
    # が、0.1 秒たっているので、A を raise する
  end
ensure
end
```

Conclusion:

* no conclusion. timeout API is difficult
