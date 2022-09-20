---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210112Japan

https://bugs.ruby-lang.org/issues/17480

## DateTime and location

* 1/13 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2/16 (Tue) 13:00-17:00 JST @ Online
  * pre-reading: 2/15 (Mon)

## Announce

## About 3.0/3.1 timeframe

* 3.0.1: in this month?
  * SEGV backport https://bugs.ruby-lang.org/issues/17518

## Check security tickets

[secret]

### [[Feature #17472]](https://bugs.ruby-lang.org/issues/17472) `HashWithIndifferentAccess` like `Hash` extension (mame)

* It is a good idea for Ruby 3.1 to provide small but immediate improvements like this. The constant name should be discussed.

Preliminary discussion:

* ko1: need to measure the performance

Discussion:

* shyouhei: is this for speed?
* naruse: this discussion originates from Symbol#to_s, which allocates lots of memory. Why Rails uses Symbol#to_s is for HWIA, so I thought providing HIWA by core was an option.
* ko1: now that Symbol#name was introduced, the situation could be changed.

Conclusion:

* Measure if this is still a bottleneck.


### [[Feature #17473]](https://bugs.ruby-lang.org/issues/17473) Make `Pathname` to embedded class of Ruby (mame)

* We want to make Rubygems independent with any gems. Rubygems heavily depends on `Pathname`. So how about making it built-in?

Preliminary discussion:

* akr: not against but I have no strong motivation. Handling filesystem in OOP style seemed failed

Discussion:

```shell
[master]$ git grep Pathname | grep bundler | wc -l
145
```

* ko1: what is the intention of this request?
    * "pathname is one of most useful utility class of Ruby. I'm happy to use Pathname without require it." in ticket.
* mame: bundler wants to avoid requiring external libraries.
* akr: but including it in-core seems oposite from decoupling this library.
* matz: I honestly... don't bother.
* ko1: if we can get Pathname object with `__FILE__` and so on.
* naruse: introduce `__PATHNAME__`?

```ruby
__PATHNAME__
__path_dir__

File.join(File.dirname(__FILE__), 'foo.rb')
#=>
__PATHNAME__ + 'foo.rb'
```

Conclusion:

* matz: Pending. I'm open to persuation but I don't see so strong advantages right now


### [[Feature #16989]](https://bugs.ruby-lang.org/issues/16989) Sets: need ♥️ (mame)

* `SortedSet` has been removed from set.rb, so there is no longer a dependency problem.

Discussion:

* matz: Looks good to introduce the feature (tentatively, without a literal)
* akr: I think we can implement Set like behavior in Hash
* akr: We don't have to learn a new concept if it is implemented in Hash
* literal discussion:
    * Python set literal: `{1, 2, 3}`
    * Conflict with JS like Hash literal: `{a, b, c} #=> {a: a, b: b, c: c}`
* ko1: I don't understand the value of embedding `Set` without `Set` literal syntax.

Conclusion:

* matz: Let's try with small start by using autoload. Need to contact knu


### [[Feature #17479]](https://bugs.ruby-lang.org/issues/17479) Enable to get "--backtrace-limit" value by "`$-B`" (mame)

* Some libraries need to know what --backtrace-limit is specified. How about providing a command-line option `-B` as an alias, and a special variable `$-B` too?

Preliminary discussion:

* ko1: looks good
* mame: should be read-only?
* nobu: yes

Discussion:

* matz: Okay for the feature, but `$-B` is not acceptable
* mame: How about `Exception.backtrace_limit`?
* znz: irb implements its own `--backtrace-limit`.
    * irb 
      ```
      --back-trace-limit n
                    Display backtrace top n and tail n. The default
                    value is 16.
      ```
    * ruby
      ```
      --backtrace-limit=num
                  limit the maximum length of backtrace
      ```
* nobu: `Thread::Backtrace.limit`?

Conclusion:

* matz: `Thread::Backtrace.limit` accepted.
* ko1: Setter would not be added unless requested.


### [[Feature #17327]](https://bugs.ruby-lang.org/issues/17327) The `Queue` constructor should take an initial set of items (chrisseaton)

* This simple feature captures a common pattern. There's a PR with an implementation based on initial feedback and specs https://github.com/ruby/ruby/pull/3768.

Preliminary discussion:

* mame: `Queue.new(kw: 1)` should be rejected (for future extension with keywords)
* ko1: people want `Queue.new(ary)` instead of `Queue.new(*ary)`?
* nobu: the PR accepts both `Queue.new(ary)` and `Queue.new(*ary)`
* mame: `Queue.new(ary)` looks good to me

Discussion:

* nobu: why not extend Queue#push, so that users can write `Queue.new.push(q, w, e, r, t, y, ...)`
* shyouhei: I don't think we should implement the same thing for SizedQueue, because that can block indefinitely.

Conclusion:

* nobody is against the feature itself.
* matz:
    * Queue.new accepted.  Take Enumerable.
    * Extending Queue#push shall be a separate issue.
* nobu: I don't like the implementation so I would rework.


### [[Feature #16806]](https://bugs.ruby-lang.org/issues/16806) `Struct#initialize` accepts keyword arguments too by default (k0kubun)

* Is there any update on kwargs since [the last discussion](https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20200514Japan.md#feature-16806-structinitialize-accepts-keyword-arguments-too-by-default-k0kubun)? Can we introduce a warning for it in 3.1?

Preliminary discussion:

```ruby
# ruby 3.1.0dev (2021-01-07T14:44:25Z :detached: 55e52c19e7)
S = Struct.new(:a, :b)
p S.new(a: 1, b: 2)
#=> #<struct S a={:a=>1, :b=>2}, b=nil>

# in 3.1: #<struct S a={:a=>1, :b=>2}, b=nil> with a warning
# in 3.2: #<struct S a=1, b=2>
```

Discussion:

*

Conclusion:

* matz: accepted.


### [[Feature #17490]](https://bugs.ruby-lang.org/issues/17490) Rename `RubyVM::MJIT` to `RubyVM::JIT` (k0kubun)

* Any objections, or suggestions to the release plan?

Preliminary discussion:

* ko1: if we introduce multi-stage JIT, the API can be changed to control each JIT configuration.

Discussion:

* (Very long discussion)

Conclusion:

* matz: Replied.


### [[Misc #17499]](https://bugs.ruby-lang.org/issues/17499) Documentation backporting (zverok)

* Address systematic lack of docs at docs.ruby-lang.org

Preliminary discussion:

* znz: A backport ticket should be created for each change that they want
* ko1: it should be how much cost should be paid for docs.ruby-lang.org.

Discussion:

* nobu: It is not clear to me which document is for master only and which one is ready for backport. 
* mame: need to hear opinions from the branch maintainers

Conclusion:

* no conclusion because there are no branch maintainer in this meeting


### [[Bug #16497]](https://bugs.ruby-lang.org/issues/16497) StringIO#internal_encoding is broken (more severely in 2.7) (zverok)

* Still is, as of 3.0.0

Preliminary discussion:

* nobu: will fix in ruby/stringio first.


### [[Feature #17407]](https://bugs.ruby-lang.org/issues/17407) `Fiber.current` and `require 'fiber'` (zverok)

* The requirement seem to not be rethought for a long time, can it be lifted?

Preliminary discussion:

* nobu: https://github.com/nobu/ruby/tree/fiber-builtin
* ko1: I think these methods should not be recommended because they are difficult APIs to use (this is why `require` is needed), but I'm okay now.

Discussion:

* ko1: people need not know the current fiber as long as they don't use `Fiber.transfer`.
* nobu: I once wanted to know the current fiber when implementing timeout.  If the current fiber is not the main one, exceptions shall be raised. I wanted to check that.

Conclusion:

* ko1: will respond.


### [[Feature #17330]](https://bugs.ruby-lang.org/issues/17330) `Object#non` (zverok)

* Experimental proposal about syntax like `calculation.non(&:zero?) || something`

Preliminary discussion:

* akr: I prefer `.zero?.!` to `non(&:zero?)`. `.zero?.not` is more preferable
* ko1: `.zero?.!` returns true/false, so `.zero?.!&.foo` and `.zero?.not&.foo` do not work
* mame: `.non(&:empty?)` looks like it returns bool

Discussion:

* mame: the proposed method chain is cryptic.
* matz: Hmm... Not in favor.

Conclusion:

* matz: will respond.


### [[Feature #16122]](https://bugs.ruby-lang.org/issues/16122) `Struct::Value`: simple immutable value object (zverok)

* The discussion hanged a year ago with Matz saying "We need to discuss further". Maybe now after 3.0 it is the time?

Preliminary discussion:

* mame: I wonder how (or whether) we can progress this. The proposal looks too messy

Discussion:

* matz: okay to have e.g. tuples, but I'm not sure if that has to be implemented this way.
* ?: why hash_accessors option is needed?

Conclusion:

* matz: will reply


### [[Feature #17500]](https://bugs.ruby-lang.org/issues/17500) Move RubyVM::* to ExperimentalFeatures (eregon)

* I think this is the best solution, for everyone. Could matz and ruby-core agree to this?

Preliminary discussion:

* mame: already rejected

Conclusion:

* matz: ExperimentalFeatures is not acceptable


### [[Feature #17496]](https://bugs.ruby-lang.org/issues/17496) Add constant `Math::TAU` (jzakiya)

* Reconsider adding to Ruby core, as more languages have. Increased adoption|use since last consideration. It has become "time proven", https://bugs.ruby-lang.org/issues/4897#note-41

Conclusion:

* pending


### [[Feature #12607]](https://bugs.ruby-lang.org/issues/12607) Ruby needs an atomic integer (@shyouhei) (nobu)

Preliminary discussion:

* nobu: recently I experienced some cases that this was needed
* ko1: I prefer to introduce STM. For example, https://github.com/ruby/uri/pull/15/files#diff-936b286152b1184cde04f027289d65e633d0f3ee52fdc42cf4eb072c24312e15R86 is weird pattern to change the global configuration.
* mame: Keeping consistency of only one integer independently, is a so frequent pattern?
* ko1: For example, it is useful for performance counter. But practically, it is not so often that consistency is independent to other data. For example, in the implementation of Ruby, there are very few `ATOMIC_INC` used.

Discussion:

* ko1: I have a better idea than this. Trust me

Conclusion:

* please discuss about STM applicability in the BTS.


### [[Feature #17099]](https://bugs.ruby-lang.org/issues/17099) Remove boolean argument and warning from `Module#attr` (S_H_)

* It has been warned since 1.9.1.

Conclusion:

* naruse: Don't remove in 3.1. Maybe remove in 3.2 (I don't have opinion in 3.2)


### [[Feature #17485]](https://bugs.ruby-lang.org/issues/17485) Keyword argument for timezone in Time.new (@nobu) (nobu)

* Let `Time.new` accept a timezone as a keyword argument, as well as `Time.at` and `Time.now`.

Discussion:

* naruse: I'm for the feature
* usa: `in:` ??
* ko1: yes.  Other methods e.g. `Time.at` takes that too.
* What happens by `Time.new(2021, 1, 1, 0, 0, 0, "+09:00", in: "+00:00")`?
    * usa: it should raise ArgumentError

Conclusion:

* matz: OK.


### [[Bug #17504]](https://bugs.ruby-lang.org/issues/17504) Allow UTC offset without colons per ISO-8601 (@nobu) (nobu)

* Although ISO-8601 allows UTC offset to be `+HHMM` as well as `+HH:MM`, `Time.new`, `Time#getlocal` etc reject that format as invalid.

Discussion:

```ruby
Time.at(0, in: "+00:00")
#=> 1970-01-01 00:00:00 +0000 # current 2.0..3.0

Time.at(0, in: "+0000")
# current: ArgumentError ("+HH:MM", "-HH:MM", "UTC" or "A".."I","K".."Z" expected for utc_offset)
# should be accepted
```

* usa: looks good to accept it

Conclusion:

* matz: no strong opinion. go ahead


### [[Bug #17509]](https://bugs.ruby-lang.org/issues/17509) Custom `respond_to?` methods in modules break `defined?(super)` (@benediktdeicke) (nobu)

* Custom `respond_to?` can't know the point to continue seaching the method entry.

Preliminary discussion:

* mame: I vote for revert

```ruby
class A
  def respond_to? *args
    super
  end
end

class B < A
  def a_method
    p defined?(super)
  end
end

B.new.a_method
#=>
# Ruby ~2.7  #=> nil
# Ruby 3.0~  #=> "super"

class C
  def a_method
    p defined?(super)
  end
end

C.new.a_method
#=> nil
```

Discussion:

* nobu: I'd like to revert it for 3.1. Consider a retry for 3.2, later

Conclusion:

* matz: revert for now


### [[Bug #17523]](https://bugs.ruby-lang.org/issues/17523) Inconsistent `Warning[]` values in scripts loaded by `-r` option (@nobu) (nobu)

* While `-w` option affects `$VERBOSE` for both the main and required scripts, but affects `Warning[]` values only in the main script.

Preliminary discussion:

* mame: looks good

Discussion:

* usa: this is a bug.


### [[Bug #17429]](https://bugs.ruby-lang.org/issues/17429) Prohibit include/prepend in refinement modules (jeremyevans0)

* I've added a pull request for this, is it OK to merge?
* Do we want to implement a feature to copy methods from a module into a refinement, such as the `:import` option?

Discussion:

* matz: OK but when? Would break 3.0.1 if we do this now.
* akira: This is not that rare than you think.
* naruse: I don't want to introduce incompatibility in 3.1
* https://github.com/tomykaira/rspec-parameterized/blob/master/lib/rspec/parameterized/table_syntax.rb

Conclusion:

* need to continue to discuss


### [[Bug #17423]](https://bugs.ruby-lang.org/issues/17423) `Prepend` should prepend a module before the class (jeremyevans0)

* Should `Module#prepend` add iclasses to the start of the ancestor chain if the receiver already includes the module?
* Should `Module#prepend` add iclasses to the start of the ancestor chain if the receiver has already prepended the module?
* Should `Module#prepend` add iclasses to the start of the ancestor chain if the ancestor chain already starts with the iclasses for the prepended module?
* If the answer to any question is yes, can we decide on details for how prepend should work?

Preliminary discussion:

```ruby
module M1; end
module M2; end
class C; include M2; end

M2.prepend M1
C.prepend M1
p M2.ancestors #=> [M1, M2]
p C.ancestors
  #=> [M1, C, M2, Object, Kernel, BasicObject] in 2.7
  #=> [C, M1, M2, Object, Kernel, BasicObject] in 3.0
```

* Should be `[M1, C, M2, ...]` (same in 2.7) or `[M1, C, M1, M2, ...]`

```ruby
module M; end
module A; end
A.prepend M
class B; include A; end

B.prepend M
p A.ancestors #=> [M, A]
p B.ancestors #=> [B, M, A, Object, Kernel, BasicObject] in both 2.7 and 3.0
```

* Should `Module#prepend` add iclasses to the start of the ancestor chain if the receiver already includes the module?

```ruby
class C
  include M
  prepend M
end

C.ancestors #=> ?? [M,C,M,...]
# ~master: [C, M, Object, Kernel, BasicObject]
```

* matz: it should be `[M,C,M,...]`

```ruby
class C
  include M
end

class D < C
  prepend M
end

D.ancestors #=> ?? [M, D, C, M, ...]
#=> ~master: [M, D, C, M, Object, Kernel, BasicObject]
```

* matz: it should be `[M, D, C, M, ...]`


* Should `Module#prepend` add iclasses to the start of the ancestor chain if the receiver has already prepended the module?
* Should `Module#prepend` add iclasses to the start of the ancestor chain if the ancestor chain already starts with the iclasses for the prepended module?

```ruby
class C
  prepend M
  prepend M
end

C.ancestors #=> [M, M, ...] ?
#=> ~master: [M, C, Object, Kernel, BasicObject]
```

* matz: either ok. One `M` looks better? Raising an exception at the second `prepend` is also acceptable.

```ruby
module M1; end
module M2; end
class C
  prepend M1
  prepend M2
  prepend M1
  prepend M2
end

p C.ancestors 
#=> ~master: [M2, M1, C, Object, Kernel, BasicObject]
```

* matz: `[M2,M1,M2,M1,C,...]` is acceptable, but personally I like `[M2,M1,C,...]`

```ruby
module M; end

class C
end

class D < C
  prepend M
end

class C
  prepend M
end

p D.ancestors
#=> ~master: [M, D, M, C, Object, Kernel, BasicObject]
```

* matz: should be `[M, D, M, C, ...]`

```ruby
module M; end
class C
  prepend M
end

class D < C
  prepend M
end

p C.ancestors # => [M, C, ...]
p D.ancestors # => ideally should be [M, D, C, ...], but realistical implementation is [M, D, M, C, ...]
```

Discussion:

* akr: It seems that single module can occur multiple times in an ancestors but it is just due to implementation (ancestors chain is shared by multiple classes). What the important specification of `C.prepend M` is that M occur before C in C.ancestors. When M occur multiple times in an ancestors, only the first occurence would be meaningful in most cases.  We recommend Ruby programs don't depend on the multiple occurences.


### [[Bug #17517]](https://bugs.ruby-lang.org/issues/17517) File.expand_path returns us-ascii when both arguments are ascii compat (znz)

* require_relative use expand_path, and it returns us-ascii

Preliminary discussion:

* nobu: let's fix it.


## Previous meeting

https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20201210Japan.md

### [[Bug #17280]](https://bugs.ruby-lang.org/issues/17280) Dir.glob with FNM_DOTMATCH matches ".." and "." and results in duplicated entries (jeremyevans0)

> * matz: try to remove "." and ".." on 3.1 (early timing to check compatibility issues)

### [[Bug #17162]](https://bugs.ruby-lang.org/issues/17162) Dir['**/*'] : stack smashing detected when listing big amount of directories (jeremyevans0)

> mame: let's postpone it to 3.1

