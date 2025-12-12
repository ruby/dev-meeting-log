# DevMeeting-2025-12-11

https://bugs.ruby-lang.org/issues/21689

## DateTime and location

* 2025/12/11 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2026/01/14 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21619]](https://bugs.ruby-lang.org/issues/21619) logger context API (chucke)

* Blog post detailing motivation [here](https://honeyryderchuck.gitlab.io/2025/11/12/context-missing-api-in-logger.html)
  * logger doesn't have a context API to enrich payload for structured logging
  * other languages have some of the kind (the post details examples from java, go, python)
* PR with proposal [here](https://github.com/ruby/logger/pull/132)
  * some public API feedback to address.
  * sonots (gem maintainer) didn't reply since I opened the proposal.

#### Preliminary discussion:

```ruby
logger = Logger.new(STDOUT)
logger.with_context("user_id" => 1) do
  logger.info("foo")
    #=> I, [2025-11-28T17:57:50.947152 #3705893] [user_id=1] INFO -- : foo
    # if using a hypothetical json formatter
    #=> { "message" => "foo", "user_id" => 1, ....
  end
end
logger.info("foo", context: { "user_id" => 1 }) #=> same thing, but single call only
```

#### Discussion:

* tagomoris: should I take over logger gem?
* mame: I believe no one will be against it

#### Conclusion:

* tagomoris: I will contact on sonots (maybe in person).


### [[Feature #21704]](https://bugs.ruby-lang.org/issues/21704) Expose `rb_process_status_new` to C extensions. (ioquatix)

- Required for improved io_uring `process_wait` hook (it returns full status).
- https://man7.org/linux/man-pages/man3/io_uring_prep_waitid.3.html
- Is it acceptable? https://github.com/ruby/ruby/pull/15213

#### Discussion:

* mame: was ko1 against?
* ko1: Not patricular against to this one but I guess it can lead to other requests?  That if any can be handled as separate tickets so I'm okay.
* n0kada: I don't see practial info that people want to add here though.
* akr: wait,
    ```C
    VALUE rb_process_status_new(rb_pid_t pid, int status, int error);
    ```
    Why do we need `error` here?  Process::Status doesn't have that bit.
* mame: nobu made the API.
* n0kada: me remembers nothing.
* mame: It seems the function was a refactoring effort of old `rb_last_status_set()`
* akr: I guess `rb_last_status_set()` doesn't fit to Sam's needs because the function is thread-local.

#### Conclusion:

* mame: Rather than exposing this existing function, which isn't well thought-out, it would be better to have a more fluent API.
* akr: I will reply


### [[Feature #21701]](https://bugs.ruby-lang.org/issues/21701) Enumerator.produce accepts an optional `size` keyword argument (knu)

- Enumerator#to_set now rejects infinite sequences, and the current situation of Enumerator::Producer that its size is fixed to infinity creates an inconsistency that prevents finite produce enumerators from being converted to sets.

#### Preliminary discussion:

```ruby
# Infinite enumerator
enum = Enumerator.produce(1, size: Float::INFINITY, &:succ)
enum.size  # => Float::INFINITY

# Finite enumerator with known/computable size
abs_dir = File.expand_path("./baz") # => "/foo/bar/baz"
traverser = Enumerator.produce(abs_dir, size: -> { abs_dir.count("/") + 1 }) {
  raise StopIteration if it == "/"
  File.dirname(it)
}
traverser.size  # => 4

abs_dir = "/"
traverser = Enumerator.produce(abs_dir, size: -> { abs_dir.count("/") + 1 }) {
  raise StopIteration if it == "/"
  File.dirname(it)
}
p traverser.size #=> 2
p traverser.to_a #=> ["/"]
```

#### Discussion:

* knu: `produce` can in fact be used to create an infinite enumerator, but after using it for a while I found that it is not always the case.
* mame: I don't like it when the provided size and actual number of iteration differs.
* akr: produce's size being infinity now seems like a wrong idea to me

(everybody nods)

* akr: does "size being infinite" mean the enumerator _does_ not stop, or _can_ stop?
* knu: size can be different from actual number of iteration.  For instance, `SELECT COUNT(*)` returns a fixed number, then later `SELECT *` can accumulate different number of rows because of INSERT / DELETE in between the twp operations.
* akr:  Then I guess it was not a wise idea for #to_set to check that info...

#### Conclusion:

* matz: Go ahead

### [[Feature #21552]](https://bugs.ruby-lang.org/issues/21552) allow String.strip and similar to take a parameter similar to String.delete (shugo)

- pull request: https://github.com/ruby/ruby/pull/15400
- Is it acceptable to take character selectors like String#delete?
  - [Python's str.strip](https://docs.python.org/3/library/stdtypes.html#str.strip) seems not to support ranges etc.

#### Discussion:

* shugo: I find it handy.
* shugo: Also python has this feature.
* matz: I'm not super against it.  This breaks nothing.
* mame: Not a big fan though.
* ko1: It can be misused with `String#delete_suffix` and so on.
* mame: `"aabbccddee".count("a-c") #=> 6`

#### Conclusion:

* matz: Go ahead.
* naruse: OK to ship it w/4.0.0.

### [[Bug #21049]](https://bugs.ruby-lang.org/issues/21049) Reconsider handling of the numbered parameters and "it" parameter in `Binding#local_variables` (mame)

* An API like `Binding#numbered_parameter_get` was actually requested. I'd like to discuss how to proceed.

#### Discussion:

* mame: local_variable_get already does not return it.
* mame: a way to get it has been postponed, untl requested from someone.
* mame: now RubyMine's authors want to get that bit from their debugger.
* matz: `#it_get` sounds esoteric to me...

```ruby
Binding#local_variables #=> [:a, :b, :c]

Binding#implicit_parameters #=> [:_1, :_2, :_3, ...]
Binding#implicit_parameters #=> [:it]
Binding#implicit_parameter_get(:_1) #=> obj
Binding#implicit_parameter_get(:_9) #=> implicit parameter '_1' is not defined for #<Binding:...> (NameError)
Binding#implicit_parameter_get(:it) #=> implicit parameter 'it' is not defined for #<Binding:...> (NameError)
Binding#implicit_parameter_defined?(:_1) #=> true or false
Binding#implicit_parameter_defined?(:it) #=> true or false

Binding#implicit_parameter_set: do not introduce this

foo do
  _2
  binding.implicit_parameters #=> [:_1, :_2]
  binding.eval("_2") #=> undefined local variable or method '_2'
end
```

#### Conclusion:

* matz: Feature accepted, naming need brush-ups.


### [[Feature #19979]](https://bugs.ruby-lang.org/issues/19979) Reconsider adding `&nil` to method declarations, to signal it won't accept a block (pabloh)

* Adding this feature should be possible now that the syntax moratorium is over

#### Discussion:

* matz: Super honestly speaking it was a design mistake, that a method takes a block implicitly.
* akr:  what abot allowing implicit blocks only when `yield` is called?
* matz: that "only when" is not obvious, for instance consider `def foo(...) = super(...)`
* ko1: that's warned though.
* matz: given that `def foo(**nil) = /.../` is already allowed, `def foo(&nil) = /.../` can be a compromise to me.
* knu: It would be meddlesome when rubocop starts warning every possible method definitions I have already wrote.

#### Conclusion:

* matz: This would be a 4.1 killer feature. Accepted.


### [[Feature #8948]](https://bugs.ruby-lang.org/issues/8948) Frozen regex (eregon)

* This is to make all Regexps frozen and not just literals.
* I made a PR, it passes CI: https://github.com/ruby/ruby/pull/14547
* Is it OK to merge it for Ruby 4.0?

#### Discussion:

* shyouhei: I remember it was not merged before because of test breakage.
* ko1: eregon also fixed those tests.
* mame: Looking at the test changes, it can be problematic when previously `Marshal.dump`-ed (using 3.x) regular expression cannot be `Marshal.load`-ed in 4.x.

* akr: Perhaps it is the first time we try freezing objects with "actual" identities, that is something different from Integer etc?

```
2008-04-12 /srv/gems/Kanocc-0.1.0/lib/kanocc/token.rb:  class Token < Regexp
2009-05-16 /srv/gems/animehunter-mongo-0.9/lib/mongo/types/regexp_of_holding.rb:      class RegexpOfHolding < Regexp
2016-07-21 /srv/gems/apipie-params-0.0.5/lib/apipie/params/descriptor.rb:      class Number < Regexp
2008-12-22 /srv/gems/bahuvrihi-tap-0.12.0/lib/tap/test/regexp_escape.rb:    class RegexpEscape < Regexp
2025-02-04 /srv/gems/blouson-4.0.0/lib/blouson/tolerant_regexp.rb:  class TolerantRegexp < Regexp
2013-05-04 /srv/gems/chomsky-0.1.1/lib/chomsky/parsers/blank.rb:    class Blank < Regexp
2018-12-07 /srv/gems/eleetscript-0.0.30/lib/lang/runtime/base_classes.rb:  class ESRegex < Regexp
2012-04-01 /srv/gems/hiwai-0.0.1/lib/hiwai/masked.rb:  class MaskedRegexp < Regexp
2022-04-27 /srv/gems/jekyll-bible_references-0.1.0/lib/jekyll/bible_references/scripture_pattern.rb:    class ScripturePattern < Regexp
2010-03-01 /srv/gems/kbaum-mongo-0.19/lib/mongo/types/regexp_of_holding.rb:  class RegexpOfHolding < Regexp
2010-08-31 /srv/gems/lgierth-rack-mount-0.6.13/lib/rack/mount/regexp_with_named_groups.rb:    class RegexpWithNamedGroups < Regexp
2010-01-28 /srv/gems/mongo-find_replace-0.18.3/lib/mongo/types/regexp_of_holding.rb:  class RegexpOfHolding < Regexp
2009-05-16 /srv/gems/mongodb-mongo-0.14.1/lib/mongo/types/regexp_of_holding.rb:  class RegexpOfHolding < Regexp
2024-10-12 /srv/gems/oktest-1.5.0/lib/oktest.rb:    class PartialRegexp < Regexp
2023-11-23 /srv/gems/opal-1.8.2/spec/opal/core/marshal/dump_spec.rb:class MarshalUserRegexp < Regexp
2009-05-16 /srv/gems/pahagon-mongo-abd-0.14.1/lib/mongo/types/regexp_of_holding.rb:  class RegexpOfHolding < Regexp
2012-07-14 /srv/gems/persistence-0.0.4/spec/persistence/object/flat/regexp_spec.rb:    class ::Persistence::Object::Flat::RegexpMock < Regexp
2015-03-05 /srv/gems/quandl_client-2.13.0/lib/quandl/pattern.rb:  class Pattern < Regexp
2011-08-30 /srv/gems/rack-mount-0.8.3/lib/rack/mount/regexp_with_named_groups.rb:    class RegexpWithNamedGroups < Regexp
2023-03-06 /srv/gems/resyma-0.1.2/lib/resyma/core/automaton/regexp.rb:    class RegexpConcat < Regexp
2023-03-06 /srv/gems/resyma-0.1.2/lib/resyma/core/automaton/regexp.rb:    class RegexpSelect < Regexp
2023-03-06 /srv/gems/resyma-0.1.2/lib/resyma/core/automaton/regexp.rb:    class RegexpRepeat < Regexp
2023-03-06 /srv/gems/resyma-0.1.2/lib/resyma/core/automaton/regexp.rb:    class RegexpSomething < Regexp
2023-03-06 /srv/gems/resyma-0.1.2/lib/resyma/core/automaton/regexp.rb:    class RegexpNothing < Regexp
2024-02-15 /srv/gems/rhodes-7.6.0/spec/framework_spec/app/spec/core/marshal/fixtures/marshal_data.rb:class UserRegexp < Regexp
2024-02-15 /srv/gems/rhodes-7.6.0/spec/framework_spec/app/spec/core/regexp/shared/new.rb:    class RegexpSpecsSubclass < Regexp
2017-09-09 /srv/gems/riel-1.2.1/lib/riel/regexp.rb:class NegatedRegexp < Regexp
2011-11-01 /srv/gems/shell_test-0.5.0/lib/shell_test/regexp_escape.rb:  class RegexpEscape < Regexp
2024-08-22 /srv/gems/sitediff-1.2.11/lib/sitediff/sanitize/regexp.rb:      class WithSelector < Regexp
2025-06-02 /srv/gems/strong_routes-2.0.1/lib/strong_routes/matcher.rb:  class Matcher < Regexp
2010-06-07 /srv/gems/tack-0.0.1/lib/tack/test_pattern.rb:  class TestPattern < Regexp
2011-01-09 /srv/gems/tap-test-0.7.0/lib/tap/test/shell_test/regexp_escape.rb:      class RegexpEscape < Regexp
2016-05-23 /srv/gems/tauplatform-1.0.3/spec/framework_spec/app/spec/core/marshal/fixtures/marshal_data.rb:class UserRegexp < Regexp
2016-05-23 /srv/gems/tauplatform-1.0.3/spec/framework_spec/app/spec/core/regexp/shared/new.rb:    class RegexpSpecsSubclass < Regexp
2010-05-21 /srv/gems/usher-0.8.3/lib/usher/route/static.rb:      class Greedy < Regexp
2013-07-26 /srv/gems/verbal-0.1.2/lib/verbal.rb:class Verbal < Regexp
2014-06-04 /srv/gems/verbal_expressions-0.1.5/lib/verbal_expressions.rb:class VerEx < Regexp
2024-11-11 /srv/gems/wallaby-core-0.3.2/lib/routes/wallaby/lazy_regexp.rb:  class LazyRegexp < Regexp
2018-06-17 /srv/gems/web-page-parser-1.2.2/lib/web-page-parser.rb:    class ORegexp < Regexp
```

```ruby
module Mongo
  ...
  class RegexpOfHolding < Regexp

    attr_accessor :extra_options_str

    # +str+ and +options+ are the same as Regexp. +extra_options_str+
    # contains all the other flags that were in Mongo but we do not use or
    # understand.
    def initialize(str, options, extra_options_str)
      super(str, options)
      @extra_options_str = extra_options_str
    end
  end
end
```

```ruby
# frozen_string_literal: true

module Wallaby
  # This is designed to delegate all the methods to {#lazy_regexp}
  # So that it doesn't need to load all the models for {Map.mode_map}
  # when the engine is mounted in `config/routes.rb`
  #
  # @see Map.resources_regexp
  # @see Map.id_regexp
  class LazyRegexp < Regexp
    delegate(*Regexp.instance_methods(false), to: :lazy_regexp)

    def initialize(source, **options)
      @lazy_source = source
      super(source.to_s, **options)
    end

    protected

    def lazy_regexp
      Map.try(@lazy_source)
    end
  end
end
```
#### Conclusion:

* yui-knk: the Marshal glitch is a show-stopper to me.
* akr: when a user class inherits Regexp, freezing it can be NG.

### [[Feature #21766]](https://bugs.ruby-lang.org/issues/21766) Pathname + FileUtils making sweet music together (eregon)

* These methods are useful and in fact are expected by some to already exist on Pathname.
* How should we add them given they depend on FileUtils?

#### Discussion:

* akr:  Feature wise pathname already have renaming etc.  What is wanted here is just naming?  That doesn't amuse me.
* hsbt:  WDYT about `require "fileutils"` adds methods to Pathname?
* akr: not in favour.

#### Conclusion:

* akr: I don't share your love for FileUtils.

### [[Misc #21769]](https://bugs.ruby-lang.org/issues/21769) Use "vX.Y.Z" instead of "vX_Y_Z" as tag names on ruby.git (k0kubun)

* I want Ruby 4.0.0 to use `v4.0.0` as its git tag name instead of `v4_0_0`. If we release rc1, can we call it `v4.0.0-rc1` too?
* Do we also want to drop `v` and make it `4.0.0` like @jeremyevans0 proposed?

#### Preliminary discussion:

* v4_0_0_preview2
* v4.1.1
* v4.1.0
* v4.0.2
* v4.0.1
* v4.0.0 <- selected
* v4.0.0-preview3

or

* v4_0_0_preview2
* v3_5_10
* 4.1.1
* 4.1.0
* 4.0.2
* 4.0.1
* 4.0.0

#### Discussion:

* shyouhei: This is a CVS tradition.
* n0kada: We just have had no needs to change previous tag scheme until now.
* ko1: the motivation of transition?
* k0kubun: I want to align version number and tag name.
* n0kada: then v4.0.0 can be something you don't want.
* mame: tag `4.0.0` would appear at the very end of the list of tags, esp. in github web UI.  This is NG to me.

#### Conclusion:

* matz: `v4.0.0` accepted.
* hsbt: previous `v4_0_0_preview2` should be renamed to: `v4.0.0-preview2`.

### [[Misc #21770]](https://bugs.ruby-lang.org/issues/21770) Stop bumping RUBY_PATCHLEVEL in release versions (k0kubun)

* I want to update `tool/merger.rb` to skip updating `RUBY_PATCHLEVEL` for Ruby 4.0.0+. Is that okay?

#### Discussion:

* shyouhei: (initial author of `tool/merger.rb`) I'm fine with that.  Others?
* hsbt: do we want to eliminate `-1`?
* k0kubun: No, as @eregon says someone wants to detect if a version is under development or not.

#### Conclusion:

* shyouhei: OK, let's step forward.

### [[Misc #21768]](https://bugs.ruby-lang.org/issues/21768) Remove deprecated functions

* Functions to be removed:
  - `rb_clear_constant_cache` deprecated for 3 years
  - postponed job APIs deprecated for 2 years
  - old APIs to allocate a data object deprecated for 5 years
  - `rb_complex_polar` deprecated for 7 years
  - `rb_clone_setup` and `rb_dup_setup` deprecated for 4 years
  - `rb_gc_force_recycle` deprecated as "removed soon"
  - taintedness/trustedness enums/macros deprecated for 4 years
  - `RUBY_FL_DUPPED` deprecated for 4 years
  - `rb_iterate` deprecated since 1.9
  - `struct RData` deprecated by `struct RTypedData`

#### Discussion:

* shyouhei: I once tried killing `rb_iterate` in 3.0 era, but it turned out that there still were actual usage of the function.

#### Conclusion:

* mame: Let's try for 4.1

### reminder

* https://bugs.ruby-lang.org/issues/20409 Missing reporting some invalid breaks
* https://github.com/ruby/ruby/pull/15189 Array#rfind, ok to merge? > matz
* https://bugs.ruby-lang.org/issues/21520 Feature Proposal: Enumerator::Lazy#tee

```ruby
module Enumerable
  def tee(...)
    each(...)
    self
  end
end

(1..).lazy.map { |x| puts x; x }.select { it.even? }.first(3)
(1..).lazy.select { p it; it.even? }.first(3)

[1, 2, 3].lazy.tee { p it }.map { it.to_s }
[1, 2, 3]     .tee { p it }.map { it.to_s }
[1, 2, 3]                  .map { p it; it.to_s }

[1, 2, 3].tee { p it }.map { p [it] } #=> ["1", "2", "3"]
# 1
# [1]
# 2
# [2]
# 3
# [3]

[1, 2, 3].map { it.to_s }.each { p [it] }
[1, 2, 3].lazy.map { it.to_s }.each { p [it] }
```

* matz: let me consider more

### misc.

* https://github.com/ruby/ruby/pull/15424
* `-d`
* `$DEBUG` usage: https://gist.github.com/ko1/a15e9b1ec84e0bbe36503c0112662134

### parser fixes

Looks like the fixes are ready for both parse.y and prism. When will we merge them? After 4.0.0 release?

* https://bugs.ruby-lang.org/issues/18878 parse.y: `Foo::Bar {}` is inconsistently rejected
* https://bugs.ruby-lang.org/issues/21168 Prism doesn't require argument parentheses (in some cases) when a block is present but parse.y does
* https://bugs.ruby-lang.org/issues/21669 Thoroughly implement void value expression check
  * matz: Let's give it a try for Ruby 4.1

Which behavior is preferable?

* https://bugs.ruby-lang.org/issues/21712 Prism and parse.y inconsistency in command call with block and `.()`
  * `a b do end.()` prism: OK, parse.y: SyntaxError->OK
  * `a b do end.call()` prism: OK, parse.y: OK
  * matz: It should parse

### misc.

* https://bugs.ruby-lang.org/issues/20959 Add a way to get codepage of console. `Encoding.find("console")`
* https://bugs.ruby-lang.org/issues/21684 Does `IO#pos` clear the buffer?
* https://bugs.ruby-lang.org/issues/21686 In combination with `IO#ungetbyte`, the write position may become unpredictable.
* https://bugs.ruby-lang.org/issues/21690 Inconsistent `rb_popcount64()` definition
* https://bugs.ruby-lang.org/issues/21693 Allow calling any callable object as a method
* https://bugs.ruby-lang.org/issues/21374 `FrozenError` message is inconsistent when a singleton method is defined on a frozen object
* https://bugs.ruby-lang.org/issues/21706 Add SIMD optimizations for string comparison operations
* https://bugs.ruby-lang.org/issues/21715 Miscompilation on x86-64-v2 due to undefined behavior in search_nonascii in string.c
* https://bugs.ruby-lang.org/issues/21709 `Regexp` interpolation is inconsistent with `String` interpolation
  * `/#{ '\p{Hiragana}'.encode("US-ASCII") }\u1234/ #=> encoding mismatch in dynamic regexp : US-ASCII and UTF-8`
  * `/#{ 'p{Hiragana}'.encode("US-ASCII") }\u1234/` #=> no error
* https://bugs.ruby-lang.org/issues/21723 `binding.irb` raises a LoadError under `bundle exec`
* https://bugs.ruby-lang.org/issues/21721 Allow `Queue` and `SizedQueue` to be used as LIFO queues
* https://bugs.ruby-lang.org/issues/21005 Update the source location method to include line start/stop and column start/stop details

```
irb(main):001> def foo = nil; method(:foo).source_location
=> ["(irb)", 1, 0, 1, 13]

irb(main):002> proc {}.source_location
=> ["(irb)", 2, 5, 2, 7]

irb(main):003> binding.source_location
=> ["(irb)", 3]

irb(main):004> b = binding
=> #<Binding:0x000073d738ed70a0>

irb(main):005> b.source_location
=> ["(irb)", 4]
```


* https://bugs.ruby-lang.org/issues/21767 Consider procs which `self` is Ractor-shareable as Ractor shareable
* https://bugs.ruby-lang.org/issues/21720 Add a native Binary Heap / Priority Queue to Ruby's Standard Library (heapify, heappush, heappop)

### box

https://bugs.ruby-lang.org/issues/21760 `Ruby::Box`: a couple of require-related problems

### autoload

* https://bugs.ruby-lang.org/issues/21719 Thread deadlock with explicit require of a base clase in Linux Ruby 3.4

```ruby
# start.rb
#
autoload :Target, "./target"
Thread.new { Target }
require "./target"

# target.rb
#
class Target
end
```
