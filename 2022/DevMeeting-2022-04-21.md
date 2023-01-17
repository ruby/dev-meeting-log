---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-04-21

https://bugs.ruby-lang.org/issues/18652

## DateTime and location

* 2022/04/21 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/05/19 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #18654]](https://bugs.ruby-lang.org/issues/18654) Enhancements to prettyprint (kddnewton)

* PrettyPrint currently immediately pushes content onto the output buffer and then inserts newlines where necessary. This makes it impossible to change the output based on how groups are broken. This commit changes the behavior to first build up the entire doc node tree, and the lay it out once it knows all of the content. This allows a lot more flexibility and new nodes like `IfBreak`, `BreakParent`, and `Trim`. This flexibility is necessary to support formatting Ruby itself as in https://github.com/ruby-syntax-tree/syntax_tree.

Discussion:

* akr: I don't want to pull this big-bang commit. Some part are ng to me.
* mame: Some part understandable, like supporting trailing comma.  But I don't know the whole change.
* akr: What happens if the trailing comma itself triggers the line breaking?

Conclusion:

* akr will reply.

### [[Feature #18642]](https://bugs.ruby-lang.org/issues/18642) Named ripper fields (kddnewton)

* This commit adds a new subclass for ripper that functions similarly to SexpBuilderPP but uses classes with named fields instead of arrays. It additionally includes location information for all of the nodes, and implements pretty printing and pattern matching. This makes it much easier to work with Ripper, as you can get handed an AST with full documentation. (This addresses a lot of the complaints raised in [#14844](https://bugs.ruby-lang.org/issues/14844).)

Discussion:

* shyouhei: How about keeping it as a separate gem?
* akr: We need a good reason to merge it.
* shyouhei: The biggest matter is maintenability.
* mame: Agreed. It is very huge. Also, it has some custom nodes to make it easy to handle the AST, but it means it is not a simple wrapper for Ripper. It would not be trivial to maintain it.
* ko1: Agreed
* matz: Not against the idea behind this; but unsure if we should merge.
* ko1: If we encourage use of this gem we finally have to retain AST level backwards compatibility.
* matz: We don't have to guerantee the compatibility of AST even if we merge this.
* shyouhei: it seems difficult to reach conclusion, but it may be good to start it with a gem and see if it gets adopted.

Conclusion:


### [[Feature #18668]](https://bugs.ruby-lang.org/issues/18668) Merge `io-nonblock` gems into core (eregon)

* Let's do it?
* Unlike io-wait these methods seem all stable and well established.

Preliminary discussion:

* mame: io/nonblock was useful in the age of 1.8 because it actually changed the blocking mode for all IO operations
```ruby
root@a27d53b9108e:/all-ruby# ./bin/ruby-1.8.7-p374 -rio/nonblock -e '$stdin.nonblock = true; p $stdin.read'
-e:1:in `read': Resource temporarily unavailable (Errno::EAGAIN)
        from -e:1
```

* mame: However, nowadays, almost all IO operations uses select for waiting, so the blocking mode does not matter
* ko1: the following program wait for the pipe write even if the pipe is "nonblock".

```ruby=
require 'io/nonblock'

r, w = IO.pipe
p r.nonblock? #=> true

Thread.new do
  sleep 1
  w.puts "hello"
end

system("cat", in: r)

p r.nonblock? #=> false
```

* ko1: because it removes `O_NONBLOCK` from 0-2 fds before exec ([https://github.com/ruby/ruby/blob/master/process.c#L1767](https://github.com/ruby/ruby/blob/4a4c1d6920c7d9fbcd2542a3462f19ccd48c02e8/process.c#L1767)).

Discussion:

* akr: I think this api is basically useless.
* mame: It seems there are rooms for this in Fiber scheduler.
* mame: Eric Wong tells us that setting nonblock mode by default is risky.
* akr: I started to think we need this method because we want turn standard IOs blocking again at the end of the process.
* mame: API-wise this seems too "general".  I expected read would raise EAGAIN for nonblocking IO instances (which is not true right now).
* akr: Fiber scheduler should require "io/nonblock" to set O_NONBLOCK. Casual programs would not need it. So it is not necessary to make it built-in.

Conclusion:

* matz: This API (does not change `IO#read` behaviour) is at least confusing to me. I will reply.

FYI:

```ruby
require 'io/nonblock'
require 'socket'

# p STDOUT.nonblock?
p s = Socket.tcp('atdot.net', 80)
p s.nonblock = true
p s.nonblock? #=> `nonblock?': nonblock?() function is unimplemented on this machine (NotImplementedError)

=begin
ruby 3.2.0dev (2022-01-14T04:46:12Z gh-4636 c613d79f9b) [x64-mswin64_140]
#<Socket:fd 4>
true
t.rb:8:in `nonblock?': nonblock?() function is unimplemented on this machine (NotImplementedError)
	from t.rb:8:in `<main>'
=end
```

* nobu: I'll try.

### [[Bug #18628]](https://bugs.ruby-lang.org/issues/18628) Link contributing is broken (jeremyevans0)

* Also affects #18665.  Problem is that ruby uses the --page-dir rdoc option.
* Any documentation generation that does not use the --page-dir rdoc option has broken links.
* I propose to remove the --page-dir rdoc option, and fix any internal documentation that relies on it.
* This will allow externally generated documentation (which is unlikely to use --page-dir) to have working links.

Discussion:

* nobu: ruby-doc.org uses something other than rdoc, meseems.
* nobu: docs.ruby-lang.org already fixed this.
* nobu: Jeremy's pull request fixes a link and creates another dead link.

Conclusion:

* mame: this is a ruby-doc.org's issue.


### [[Bug #18155]](https://bugs.ruby-lang.org/issues/18155) (nil..nil).cover?(x) is true for all x since beginless ranges were introduced (jeremyevans0)

* How should cover? work for ranges unbounded in both directions?
* In 2.7, with the introduction of beginless ranges, it started always returning true, instead of returning false.
* I propose to reject this bug, and keep the current behavior.

Preliminary discussion:

* mame: I vote for "do nothing"

```
ko1@aluminium:~$ gem-codesearch 'nil\s*\.\.\s*nil'
/srv/gems/active_period-7.0.0/README.md:Period.new(nil..nil).to_s
/srv/gems/behold-0.0.0/lib/behold.rb:          nil..nil, 1..10, 'a'..'f',
/srv/gems/chewy_query-0.0.1/spec/chewy_query/builder/nodes/range_spec.rb:    specify{ expect(render{ age == (nil..nil) }).to eq(range: { 'age' => { gt: nil, lt: nil } }) }
/srv/gems/groupdate-6.1.0/CHANGELOG.md:- Added support for `nil..nil` ranges in `range` option
/srv/gems/groupdate-6.1.0/lib/groupdate/series_builder.rb:                nil..nil
/srv/gems/lydown-0.14.0/lib/lydown/cli/diff.rb:      nil..nil
/srv/gems/match_skeleton-1.1.0/test/test_match_skeleton.rb:    assert_equal [nil, nil],    ms1.offset(2)        # (nil..nil)==@offsets[2]
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  #    For example, (nil..nil) is NOT valid (nb., it raised Exception in Ruby 1.8).
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  #    RangeExtd.valid?(nil..nil)     # => false
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  # In Ruby1.8, this causes  ArgumentError: bad value for range (because (nil..nil) is unaccepted).
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  # are now regarded as NOT valid, such as, (1...1) and (nil..nil)
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  #    (nil..nil).valid? # => false
/srv/gems/range_extd-1.1.1/lib/range_extd/range_extd.rb:  #   (nil..nil).empty?  # => nil
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert_raises(re){ RangeExtd(nil..nil) }
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert  ((nil..nil)  != (nil...nil))
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:        RangeExtd.new(nil..nil, true)
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert !RangeExtd.valid?(nil..nil, 9,9)
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert !(nil..nil).valid?
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      refute  (nil..nil).valid?
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert_nil (nil..nil).empty?
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert     (nil..nil).null?
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert !(nil..nil).is_none?
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert !RangeExtd.valid?(nil..nil)     # => false
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert !(nil..nil).valid? # => false
/srv/gems/range_extd-1.1.1/test/test_range_extd.rb:      assert_nil       (nil..nil).empty?  # => nil
/srv/gems/rangeary-1.0.1/lib/rangeary/rangeary.rb:  # unless both begin and end are nil (RangeExtd::NONE, nil..nil, nil...nil),
/srv/gems/rangeary-1.0.1/test/test_rangeary.rb:      _ = nil..nil  rescue return  # nil..nil may raise Exception in some Ruby versions
/srv/gems/rangeary-1.0.1/test/test_rangeary.rb:      assert_nil         Rangeary.comparable_end(nil..nil)
/srv/gems/rangeary-1.0.1/test/test_rangeary.rb:      _ = nil...nil rescue return  # nil..nil may raise Exception in some Ruby versions
/srv/gems/rangeary-1.0.1/test/test_rangeary.rb:      assert_raises(ArgumentError){ Rangeary.new(3..5, nil..nil) }       # => invalid parameter for RangeExtd, hence for Rangeary (nil..nil).
/srv/gems/rangesmaller-1.0.5/README.rdoc:Any (pair of) objects that can consist of Range can be given in initialising, though that does not mean it would always successfully run any methods, like (nil..                                                                                                  nil).each().
/srv/gems/rangesmaller-1.0.5/lib/rangesmaller/rangesmaller.rb:  #   For example, (nil..nil) is accepted, but (Complex(2,3)..Complex(2,3)) is not.
/srv/gems/rangesmaller-1.0.5/lib/rangesmaller/rangesmaller.rb:  #    (nil..nil).valid? # => false
/srv/gems/rangesmaller-1.0.5/lib/rangesmaller/rangesmaller.rb:          false           # Not Comparable; eg., nil..nil
/srv/gems/rangesmaller-1.0.5/lib/rangesmaller/rangesmaller.rb:  #   (nil..nil).empty?       # => nil
/srv/gems/rangesmaller-1.0.5/lib/rangesmaller/rangesmaller.rb:      nil # eg. (nil..nil)
/srv/gems/rangesmaller-1.0.5/test/test_rangesmaller.rb:      sn = Rangesmaller.new(nil..nil, :exclude_begin => true)
/srv/gems/riveter-0.8.3/lib/riveter/attributes.rb:          value ||= nil..nil
/srv/gems/sequel-5.55.0/doc/release_notes/5.22.0.txt:    DB[:table].where(:column=>(nil..nil))
/srv/gems/sequel-5.55.0/doc/release_notes/5.22.0.txt:    DB[:table].insert(:column=>(nil..nil))
/srv/gems/tdiary-5.2.1/vendor/bundle/ruby/3.1.0/gems/sequel-5.53.0/doc/release_notes/5.22.0.txt:    DB[:table].where(:column=>(nil..nil))
/srv/gems/tdiary-5.2.1/vendor/bundle/ruby/3.1.0/gems/sequel-5.53.0/doc/release_notes/5.22.0.txt:    DB[:table].insert(:column=>(nil..nil))
/srv/gems/typeprof-0.21.2/lib/typeprof/builtin.rb:          idx = (nil..nil)
```

Discussion:

* matz: My take is to return false.
* shyouhei: a set which has neither begin nor end is "the Universe", isn't it?
* knu: I remember this is an intentional design.
* akr: Shouldn't we document this behaviour?

Conclusion:

* matz: Let's do nothing. I will reply


### [[Bug #18629]](https://bugs.ruby-lang.org/issues/18629) block args array splatting assigns to higher scope _ var (jeremyevans0)

* I believe this is expected behavior.  Can we confirm that and reject this bug?

Preliminary discussion:

```ruby=
v = 1; [[2]].each{ |(v)| }; p v #=> 1
_ = 1; [[2]].each{ |(_)| }; p _ #=> 2
_a = 1; [[2]].each{|(_a)|}; p _a #=> 2
```

* mame: In my first impression, it is a bug.

Conclusion:

* matz: It is a bug. I want to fix it if possible
* mame: I have a fix


### [[Bug #18622]](https://bugs.ruby-lang.org/issues/18622) `const_get` still looks in `Object`, while lexical constant lookup no longer does (jeremyevans0)

* Can we decide whether `const_get` should operate like a relative lookup or an absolute lookup?
* Currently, it operates as a relative lookup, so `Array.const_get(:String)` is like `class Array; String; end` and works.
* If switched to an absolute lookup, `Array.const_get(:String)` would be like `Array::String`, and would raise an exception.

Preliminary discussion:

```ruby=
class C; end
class D; end

p C::D
#=> uninitialized constant C::D (NameError)

p C.const_get(:D) #=> D

# Currently C.const_get(:D) does:
p(class C; D; end) #=> D
```

Discussion:

```ruby
# knu: Is this okay to prohibit this?
class C; const_get(:D); end

# ko1: How about writing it as follows?
class C; Object.const_get(:D); end
```

```
ko1@aluminium:~$ gem-codesearch '\sconst_get' | wc -l
...
/srv/gems/imgkit-1.6.2/spec/spec_helper.rb:    constants.each { |c| const_get(c).force_encoding("ASCII-8BIT")  }
/srv/gems/inch-0.8.0/lib/inch/language/ruby/provider/yard/object.rb:                const_get(class_name)
/srv/gems/incline-0.3.14/lib/incline/bit_enum.rb:          v = const_get(name)
/srv/gems/incline-0.3.14/lib/incline/bit_enum.rb:          v = const_get(name)
/srv/gems/incline-0.3.14/lib/incline/cli.rb:              klass = const_get c
/srv/gems/incline-0.3.14/lib/incline/constant_enum.rb:              ret[nm.to_s] = const_get(nm)
/srv/gems/indis-core-0.1.4/lib/indis-core/binary_architecture.rb:        e = const_get(c)
/srv/gems/indis-core-0.1.4/lib/indis-core/binary_format.rb:        e = const_get(c)
/srv/gems/indonesian_stemmer-0.2.0/lib/indonesian_stemmer/morphological_utility.rb:          const_get("#{constant_name}".upcase.to_sym)
/srv/gems/infraruby-java-4.0.0/lib/infraruby-java.rb:                           return const_get(name)
/srv/gems/innate-2015.10.28/lib/innate/node.rb:      const_get($1)
/srv/gems/integrity-bob-0.3.1/lib/bob/scm.rb:      const_get(class_name)
/srv/gems/intentmedia-activerecord-jdbc-adapter-1.1.1.1/lib/arjdbc/jdbc/extension.rb:      mod = const_get(name)
/srv/gems/intercom-rails-0.4.2/lib/intercom-rails/config.rb:      to_reset = self.constants.map {|c| const_get c}
/srv/gems/interloper-0.2.3/lib/interloper.rb:        prepend const_get(interloper_const_name)
/srv/gems/interloper-0.2.3/lib/interloper.rb:        const_get(interloper_const_name)
/srv/gems/internet_box_logger-1.0.2/lib/internet_box_logger/parsers.rb:      constants.dup.keep_if {|c| const_get(c.to_s).is_a? Module and c.to_s =~ /\wParser$/ } .map {|m| const_get m}
/srv/gems/intersect_rails_composer-0.0.7/spec/rails_wizard/recipe_spec.rb:            const_get('RailsWizard'  ).
/srv/gems/intersect_rails_composer-0.0.7/spec/rails_wizard/recipe_spec.rb:            const_get('Recipes'      ).
/srv/gems/intersect_rails_composer-0.0.7/spec/rails_wizard/recipe_spec.rb:            const_get('RecipeExample')
...

ko1@aluminium:~$ gem-codesearch '\sconst_get' | wc -l
4673
```

* ko1: I found a program that will be affected by the proposed change
  * https://github.com/googleapis/google-cloud-ruby/blob/367ca0d9c3e7a9a972c367670e56439ff49b8d18/google-cloud-retail-v2/lib/google/cloud/retail/v2/search_service/client.rb

```ruby=
# /srv/gems/google-cloud-retail-v2-0.7.0/lib/google/cloud/retail/v2/search_service/client.rb

module Google
  module Cloud
    module Retail
      module V2
        module SearchService
...
            def self.configure
              @configure ||= begin
                namespace = ["Google", "Cloud", "Retail", "V2"]
                parent_config = while namespace.any?
                                  parent_name = namespace.join "::"
                                  parent_const = const_get parent_name
                                  break parent_const.configure if parent_const.respond_to? :configure
                                  namespace.pop
                                end
```

Conclusion:

* matz: I want to fix it, but I'd care a lot about the compatibilty issue. I will reply


### [[Bug #18649]](https://bugs.ruby-lang.org/issues/18649) `Enumerable#first` breaks out of the incorect block when across threads (jeremyevans0)

* Can we specify how Enumerator#first should work across threads?
* It currently has definitely incorrect behavior, jumping out of block it should not jump out of.
* I have a patch that fixes the definitely incorrect behavior, but still passes a value to the calling thread, which may be undesireable.

Preliminary discussion:

```ruby
def foo
  Thread.new do
    1.times do
      begin
        yield 42
      rescue
      end
    end

    p :should_not_reach_here!
  end.join
end

to_enum(:foo).first #=> :should_not_reach_here!
```

Discussion:

* mame: `first` in above method breaks `1.times`, not `Thread.new`.

Conclusion:

* nobu: My patch should suffice.


### [[Feature #18683]](https://bugs.ruby-lang.org/issues/18683) Allow to create hashes with a specific capacity. (byroot)

- Useful for various parsers and deserializers that know the Hash size in advance, such as Redis protocol, msgpack, etc.
- When creating large hashes of known size, lots of needless reallocations are wasteful.
- Ruby interface: `Hash.new(default = nil, capacity = nil)`
- C-Ext interface: `rb_hash_new_capa(long)`

Preliminary discussion:

*

Discussion:

* knu: I'd prefer positional argument for better compatibility: Hash.new(nil, capa)
* matz: I don't like `Hash.new(nil, capa)` API.
* akr: What this "capacity" means is not clear to me.  Does the hash grow in case the hash value collides?
* akr: I prefer keyword parameters to specify this kind of hinting information, so `Hash#create`.
* ko1: Is this feature worth a method?
* ko1: What about optimizing `Array#to_h` ?  Could be faster than just preallocating regions.
* ko1: It seems `rb_hash_bulk_insert()` is not suitable for external inputs?
* shyouhei: `st_rehash()` handles duplicated key.  Seems safe.

Conclusion:

* akr:  Let's ask if optimized `Array#to_h` works or not.
* ko1: I'll reply.
    * exposing C-API is okay (`rb_hash_new_capa()`).
    * Matz: `Hash.new(capcacity:)` is not acceptable to keep compatibility.
    * Matz: `Hash.new(ifnone, capa = 0)` is not acceptable.
    * akr: How about to use `Array#to_h` by using `rb_hash_bulk_insert()` and it can improve performance.
* akr: Don't forget to document what this "capacity" means.

### [[Feature #18685]](https://bugs.ruby-lang.org/issues/18685) Enumerator.product: Cartesian product of enumerables (knu)

* Can be used to reduce nested blocks and allows for iterating over an indefinite number of enumerable objects.

```ruby
# product = Enumerator.product(1..3, ["A", "B"])
# p product.class #=> Enumerator

Enumerator.product(1..3, ["A", "B"]) do |i, c|
  puts "#{i}-#{c}"
end

=begin output
1-A
1-B
2-A
2-B
3-A
3-B
=end
```

```ruby=
Enumerator.product do |*a|
  p a #=> []
end

Enumerator.product(1..3, []) do
  # should not reach here
end

Enumerator.product(key: 1) # raise an Error (to allow future extension)

Enumerator.product([:A, :B, :C, :D], (1..)) do |a, b|
  p [a, b] #=> [:A, 1], [:A, 2], [:A, 3], [:A, 4], ....

  # or? (we may support this enumeration style with a keyword in future if needed)
  p [a, b]
    #=> [:A, 1],
    #=> [:A, 2], [:B, 1],
    #=> [:A, 3], [:B, 2], [:C, 1],
    #=> [:A, 4], [:B, 3], [:C, 2], [:D, 1], ...
end
```

```ruby=
# Enumerator.product 3.times, 4.times do |x, y|
Enumerator.product((0..2), (0..4)) do |x, y|
   p [x, y]
end
```

```ruby=
[*0..2].product([*0..4]) #=> [[0,0], [0,1], [0,2], [0,3], [0,4], [1,0], ...]
[*0..2].product([*0..4], [*1..2]) #=> [[0,0,1], [0,0,2], [0,1,1], [0,1,2], ...]
```

```ruby=
2.times do |x|
  3.times do |y|
    p [x, y]
  end
end

ntimes(2, 3) do |x, y|
end

for x in 3.times, y in 4.times
  p [x, y] 
end
```

Discussion:

* matz: Go ahead



### [[Feature #18729]](https://bugs.ruby-lang.org/issues/18729) Method#owner and UnboundMethod#owner are incorrect after using Module#public/protected/private (eregon)

```ruby=
# other bug found

class A
  protected def foo # line:2
    :A
  end
end

class B < A
  public :foo
  p B.instance_method(:foo) #=> #<UnboundMethod: B(A)#foo() t.rb:2>
  p B.instance_method(:foo).public? #=> false
  p B.instance_method(:foo).protected? #=> true

  # If we admit that there is a dummy method entry for B#foo,
  p B.instance_method(:foo).owner #=> A (expected B)

  # If we hide the dummy method entry,
  p B.instance_methods(false) #=> [:foo] (expected [])

  p self.new.foo #=> A
end

class C < B
  p C.instance_method(:foo) #=> #<UnboundMethod: C(A)#foo() t.rb:2>
  p C.instance_method(:foo).bind(A.new).call #=> :A
end
```

```ruby
module M
  def foo = nil # t.rb:2
end

class C
  define_method :bar, M.instance_method(:foo)
  p C.instance_method :bar #=> #<UnboundMethod: C#bar(foo)() t.rb:2>
end
```


```ruby=
[8] pry(main)> $ B.new.foo

From: (pry):2:
Owner: A
Visibility: protected
Signature: foo()
Number of lines: 3

protected def foo
  :A
end

[9] pry(main)> B.new.foo
=> :A

[10] pry(main)> B.instance_method(:foo).source_location
=> ["(pry)", 2]
```

Discussion:

matz: it seems a bug.
ko1: How about:
  * owner #=> B
  * visibility #=> public
  * source_location #=> original method definition
  * super_method #=> A's unbound method
mame: source_location should be `public` line.
ko1: now current implementaion doesn't have source_location for ZSUPER method, so `nil`?

```ruby
[11] pry(main)> def foo; end
=> :foo
[12] pry(main)> alias bar foo
=> nil
[14] pry(main)> $ bar

From: (pry):15:
Owner: Object
Visibility: public
Signature: bar()
Number of lines: 1

def foo; end
```

```ruby=
class A
  private def foo # line 2
    :A
  end
end

class B < A
  alias bar foo

  p instance_method(:bar) #=> #<UnboundMethod: B(A)#bar(foo)() t.rb:2>
  p instance_method(:bar).public? #=> false
  p instance_method(:bar).owner #=> B
  p instance_method(:bar).source_location #=> ["t.rb", 2]
  p instance_method(:bar).super_method #=> #<UnboundMethod: A#foo() t.rb:2>

  public :bar

  p instance_method(:bar) #=> #<UnboundMethod: B(A)#bar(foo)() t.rb:2>
  p instance_method(:bar).public? #=> true
  p instance_method(:bar).owner #=> B
  p instance_method(:bar).source_location #=> ["t.rb", 2]
  p self.new.bar #=> :A
  p instance_method(:bar).super_method #=> #<UnboundMethod: A#foo() t.rb:2>
end
```

Conclusion:
```ruby=
class A
  protected def foo = nil
end

class B < A
  public :foo
end
```
matz:
  * owner #=> B
  * visibility #=> public
  * source_location #=> original method definition (no change)
  * super_method #=> A's unbound method
  * B.instance_methods(false) #=> contains :foo (no change)
  * The changed result is same as "alias".

### [pathname] [Implement Pathname#lutime](https://github.com/ruby/pathname/pull/20)

* knu: @akr: Is it OK to merge this?
* akr: Go ahead.

