---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200618Japan

https://bugs.ruby-lang.org/issues/16933

## DateTime and location

* 6/18 (Thu) 13:00-17:00 JST @ online

## Next Date

* 7/20 (Mon) 13:00-17:00 JST @ online

## Announce

## Ruby 2.7 timeframe

* depends kwargs
* autoload bug [[Bug #16680]](https://bugs.ruby-lang.org/issues/16680)
  * need bisect

## About 2.8/3.0 timeframe
* naruse: is there a topic to request testing?
  * Ractor -> not yet
  * kwargs
  * signature -> still have some tasks

## Check security tickets

[secret]

## Tickets

### [[Bug #8446]](https://bugs.ruby-lang.org/issues/8446) sdbm fails to fetch existing key if many elements in it (hsbt)

* sdbm library is not maintain while a long time. Should we remove it from ruby repo skipping the bundled gems?
* https://bugs.ruby-lang.org/issues/8446#note-5

Preliminary discussion:

* mame: looks good.

Discussion:

* 

Conclusion:

* matz: Agreed for removal.

### [[Feature #16963]](https://bugs.ruby-lang.org/issues/16963) Remove English.rb from Ruby 2.8/3.0 (hsbt)

* Should we remove English.rb from ruby repo?

Discussion:

```
mame@gem-codesearch:~$ csearch "require 'English'" | wc -l
866
mame@gem-codesearch:~$ csearch "require \"English\"" | wc -l
273

mame@gem-codesearch:~$ csearch "\\\$PREMATCH" | wc -l
162
mame@gem-codesearch:~$ csearch "\\\$POSTMATCH" | wc -l
299
mame@gem-codesearch:~$ csearch "\\\$MATCH" | wc -l
285
mame@gem-codesearch:~$ csearch "\\\$LAST_MATCH_INFO" | wc -l
276
mame@gem-codesearch:~$ csearch "\\\$ERROR_INFO" | wc -l
534
mame@gem-codesearch:~$ csearch "\\\$CHILD_STATUS" | wc -l
1064
mame@gem-codesearch:~$ csearch "\\\$PID" | wc -l
2587
mame@gem-codesearch:~$ csearch "\\\$PROCESS_ID" | wc -l
187
```

Conclusion:

* hsbt: We found it used. Pending
* https://bugs.ruby-lang.org/issues/16963#note-2

### [[Bug #9573]](https://bugs.ruby-lang.org/issues/9573) descendants of a module don't gain its future ancestors, but descendants of a class, do (jeremyevans0)

* Support for Module#include was implemented 3 months ago
* Bugs prevented support for Module#prepend at the time
* I've since fixed all bugs
* Implementing support for Module#prepend is now straightforward and causes no test failures, is it OK to merge the patch?

Preliminary discussion:

```ruby
module M1
end

module M2
end

module M3
end

class C
end

p C.ancestors
#=> current: [C, Object, Kernel, BasicObject]
#=> fixed:   [C, Object, Kernel, BasicObject]

C.prepend M1

p C.ancestors
#=> current: [M1, C, Object, Kernel, BasicObject]
#=> fixed:   [M1, C, Object, Kernel, BasicObject]

M1.prepend M2

p C.ancestors
#=> current: [M1, C, Object, Kernel, BasicObject]
#=> fixed:   [M2, M1, C, Object, Kernel, BasicObject]

M2.prepend M3

p C.ancestors
#=> current: [M1, C, Object, Kernel, BasicObject]
#=> fixed:   [M3, M2, M1, C, Object, Kernel, BasicObject]
```

Discussion:

```ruby
# after above,
M1.prepend M3
p C.ancestors
#=> X [M3, M2, M3, M1, C, Object, Kernel, BasicObject]
#=> X [M2, M3, M1, C, Object, Kernel, BasicObject]
#=> O [M3, M2, M1, C, Object, Kernel, BasicObject]
```

```ruby
module M4; end
module M5; end
module M6; end
class D; end
D.prepend M4
p D.ancestors
# => [M4, D, Object, Kernel, BasicObject]
D.prepend M5
p D.ancestors
# => [M5, M4, D, Object, Kernel, BasicObject]
M4.prepend M6
p D.ancestors
# => [M5, M6, M4, D, Object, Kernel, BasicObject]
M5.prepend M6
p D.ancestors
# => [M5, M6, M4, D, Object, Kernel, BasicObject]
```

Conclusion:

* matz: Give it a try.


### [[Feature #14267]](https://bugs.ruby-lang.org/issues/14267) Lazy proc allocation introduced in #14045 creates regression (jeremyevans0)

* I added a patch implementing Proc#== and #eql? (previously not implemented, so Object#== and #eql? were used)
* Do the equalivance conditions I describe make sense, or should more be added?
* Is it OK to merge the patch?

Preliminary discussion:

```ruby
# regression.rb
def return_proc(&block)
  block
end

def return_procs(&block)
  block.inspect if ENV['INSPECT_BLOCK']

  proc_1 = return_proc(&block)
  proc_2 = return_proc(&block)

  return proc_1, proc_2
end

proc_1, proc_2 = return_procs { }

puts RUBY_VERSION
puts "Proc equality: #{proc_1 == proc_2}"
#=> false

(f1, f2), (f3, f4) = [:return_procs, :return_other_procs].map do |m|
  send(m) { }
end
p f1 == f3 #=> true?
```

* mame: Comparing Procs is not recommended, so I'm unsure if we need to do anything for this.  If we do anything, it would be good to confirm if the patch really helps the OP
* nobu: The definition of `Proc#hash` must correspond to `Proc#eql?`

```ruby
# The procs either are both created from a method or both not created from a method

p1 = method(:p).to_proc
p2 = method(:p).to_proc
p p1 == p2
```

* nobu: `eql?` is defined, but `#hash` is not 
* nobu: positive.
* mame: negative.
* ko1: positive. No issues I can think. We need to revisit `Proc#hash` implementation.

Discussion:

* usa: positive. `eql?` should return structual equality.

Conclusion:

* matz: if the rspec is fixed by this change, ok
* ko1: I'll ask the problem of rspec is solved by this patch.

### [[Bug #11669]](https://bugs.ruby-lang.org/issues/11669) inconsitent behavior of refining frozen class (jeremyevans0)

* Refining a frozen class where the refinement adds a method is currently broken.
* Refinement methods are internal, so I'm not sure we should raise FrozenError in this case
* Is it OK to merge the patch?

Preliminary discussion:

* ko1: how about to prohibit (raise FrozenError) on `refine` for frozen class?
* naruse: I have no idea, just noticed it

Discussion:

* shyouhei: it's unintentional inconsistency, of course
* ko1: what about prohibiting `refine` at all when the class is frozen.
* matz: I want the opossite.  Frozen classes shall be allowed to be refined.
* usa: overriding a method of frozen class without refinements is prohibited, isn't it?
* nobu: no, it's prohibited.

(people look at Jeremy's patch)

* usa: The patch is simple and LGTM.

Conclusion:

* matz: ok, go ahead


### [[Bug #16504]](https://bugs.ruby-lang.org/issues/16504) `foo(*args, &args.pop)` should pass all elements of args (jeremyevans0)

* Bug is due to an optimization that skips duping of args for the splat.
* I created a patch that fixes the issue in most cases by removing the optimization in those cases.
* The patch does not remove the optimization in common cases, such as `&local_variable` or `&:symbol`
* Truly fixing the issue in all cases requires removing the optimization completely.
* Is it OK to merge the patch?

Preliminary discussion:

```ruby
def foo(*args, &b)
  p args: args, b: b
end

args = [1, 2, -> {}]; foo(   *args, &args.pop)
#=> actual
# {:args => [1, 2],
#  :b    => #<Proc:0x000001e01a2a56b0 t.rb:5 (lambda)>}

#=> expected
# {:args => [1, 2, #<Proc:... t.rb:5 (lambda)>],
#  :b    => #<Proc:... t.rb:5 (lambda)>}

args = [1, 2, -> {}]; foo(0, *args, &args.pop)
#=> actual and expected in 2.7
# {:args=>[0, 1, 2, #<Proc:... t.rb:6 (lambda)>],
#  :b=>#<Proc:... t.rb:6 (lambda)>}
```

* eregon  in ticket: the "fixed" behavior is useful? performance?
* all: positive to fix.
* ko1: it can be compatible issue, but not so many cases.
* mame:
```
$ csearch "\*\w+, &\w+.pop"
/home/ubuntu/gem-codesearch/latest-gem/grape-1.3.0/lib/grape/middleware/stack.rb:            public_send(operation, *args, &args.pop)
/home/ubuntu/gem-codesearch/latest-gem/grape-extra_validators-1.0.0/vendor/bundle/ruby/2.4.0/gems/grape-1.2.5/lib/grape/middleware/stack.rb:            public_send(operation, *args, &args.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l32/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l32/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec = @test.util_spec(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l64/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l64/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec = @test.util_spec(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/osx/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/osx/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec = @test.util_spec(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/win/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/win/lib/ruby/2.2.0/rubygems/test_utilities.rb:        spec = @test.util_spec(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/ruby-compiler-0.1.1/vendor/ruby/lib/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/ruby-compiler-0.1.1/vendor/ruby/lib/rubygems/test_utilities.rb:        spec, gem = @test.util_gem(*arguments, &arguments.pop)
/home/ubuntu/gem-codesearch/latest-gem/ruby-compiler-0.1.1/vendor/ruby/lib/rubygems/test_utilities.rb:        spec = @test.util_spec(*arguments, &arguments.pop)
```

Discussion:

* hsbt: The results of gem-codesearch is only vendoring code by rubygems. It has no effect.
* matz: accepted.

Conclusion:

* matz: accepted.
* mame: will ask jeremy.


### [[Feature #16470]](https://bugs.ruby-lang.org/issues/16470) Issue with nanoseconds in Time#inspect (jeremyevans0)

* Currently, Time#inspect can display fractional seconds as a rational, which few rational people want.
* I think Time#inspect should always display fractional seconds as decimal.
* I have a patch to do this, but it makes Time#inspect the same for Time instances that are not equal.
* Arbitrary precision for fractional seconds, the benefit of storing nanoseconds as a rational, seems pointless to me.
* Do we want to keep things as is, merge the patch, or change Time's implementation so fractional seconds are not stored with greater than nanosecond precision?

Preliminary discussion:

```ruby
t = Time.utc(2007, 11, 1, 15, 25, 0, 123456.789)
t.inspect # => "2007-11-01 15:25:00 8483885939586761/68719476736000000 UTC"
```

* mame: I'm afraid of truncation.
* mame: Which is better?

```
> p Time.utc(2007, 11, 1, 15, 25, 0, 123456.789)
2007-11-01 15:25:00 8483885939586761/68719476736000000 UTC
2007-11-01 15:25:00.123456789000000011213842299184761941432952880859375 UTC

> p Time.utc(2007, 11, 1, 15, 25, 0, 0.0.next_float)
2007-11-01 15:25:00 1/202402253307310618352495346718917307049556649764142118356901358027430339567995346891960383701437124495187077864316811911389808737385793476867013399940738509921517424276566361364466907742093216341239767678472745068562007483424692698618103355649159556340810056512358769552333414615230502532186327508646006263307707741093494784000000 UTC
2007-11-01 15:25:00.000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004940656458412465441765687928682213723650598026143247644255856825006755072702087518652998363616359923797965646954457177309266567103559397963987747960107818781263007131903114045278458171678489821036887186360569987307230500063874091535649843873124733972731696151400317153853980741262385655911710266585566867681870395603106249319452715914924553293054565444011274801297099995419319894090804165633245247571478690147267801593552386115501348035264934720193790268107107491703332226844753335720832431936092382893458368060106011506169809753078342277318329247904982524730776375927247874656084778203734469699533647017972677717585125660551199131504891101451037862738167250955837389733598993664809941164205702637090279 UTC
```

Discussion:

* akr: why is it displayed in rational?
* nobu: it's due to the passed float being rationalized.
* akr: that's the reason why you save it in rational.  My question is why it is not displayed in decimal.
* nobu: showing it in decimal needs more digits.
* akr: yes. I think that's better than num/den representaion.
* nobu: that needs looong string.
* mame: subnormal numbers can take thousands of digits.
* akr: but not infinite.
* mame: Jeremy's proposal is to cut the string at nanoseconds resolution.
* akr: that sounds against the initial purpose of adding fractions to Time#inspect.  It was confusing for different times to have the same inspection
* naruse: I think this is not an issue of `#inspect`.  Time.utc can take nanoseconds (by passing rationals).  Modifying the inspect output does not solve the real problem (that is, passing a float instead of rational).
* akr: (I don't like this idea but) rounding the passed arguments to nanoseconds resolution might be a remedy.

```
Time.utc(2007, 11, 1, 15, 25, 0, 123456789r/1000000000)
=> 2007-11-01 15:25:00 123456789/1000000000000000 UTC
Time.utc(2007, 11, 1, 15, 25, 0, 123456789r/1000)
=> 2007-11-01 15:25:00.123456789 UTC

irb(main):007:0> 1.0/3
=> 0.3333333333333333
irb(main):008:0> (1.0/3).next_float
=> 0.33333333333333337
```

* naruse: the usec argument given in floating point number is not received as expected. It needs to be passed as 123456.789r (in rational)

Conclusion:

* Took more than one hour to discuss, but didn't agree with any conclusion. Pending.


### [[Feature #16378]](https://bugs.ruby-lang.org/issues/16378) Support leading arguments together with ... (eregon)

* Already accepted for master, could someone review the PR from Jeremy?
* Could we backport it to 2.7.2? So then there is a non-`ruby2_keywords` and long-term way to do delegation for more cases.

Preliminary discussion:

* Already committed.
* backport is relied on nagachika-san.

Discussion:

* matz agrees with this.

Conclusion:

* mame: I'll ask nagachika-san.


### [[Feature #16897]](https://bugs.ruby-lang.org/issues/16897) General purpose memoizer in Ruby 3 with Ruby 2 performance (eregon)

* Thoughts about the `***args` / Arguments proposal?
* Ideas to optimize delegation with `*args, **kwargs` better, which it seems most people expect (it's intuitive, same as Python) is the Ruby 3 way to do delegation?
* Some other ideas to optimize memoization, maybe using `...`?

Preliminary discussion:

```ruby
def foo(...args)
  target(name, *...args)

  args = (...).prepend(name)
  args.positionals #=> [a, b, c]
  args.keywords #=> {k: 1}
  args.block #=> proc {...}
  target(*...args)

  args = (...) # Arguments instance
  target(***args)
end
```

```ruby
args = (...).object_id
# 2.7 -> Error
```

```ruby
# obsolete ... and use *** only
def foo(***)
  target(***)
  target(name, ***)
end
def foo(***args)
  target(name, ***args)
end
```

```ruby
new_arguments = arguments(pre, ...)
arguments(k1: v1, ...)
arguments(..., k1: v1)
arguments(*(...), **(...), k1: v1, &(***))
arguments(...){ ... }
arguments(..., post_arg, k1: 1)
```

* mame: It is very unfortunate that `foo(...args)` conflicts with beginless range.  I want to remove beginless range.
* mame: It would be useful for programming, but it is very very tough to design the API of Arguments class.
  * getter
    * `Arguments#positionals`  #=> an Array of positional args
    * `Arguments#keywords`     #=> a Hash of keys and values
    * `Arguments#block`        #=> a Proc of block
    * `Arguments#block_given?` #=> true/false
    * `Arguments#hash`, `Arguments#eql?` (for memoization)
    * `Arguments#to_a` or `to_ary` is a bad idea
    * `Arguments#[]` for shorthand of `Arguments#positionals[n]`
  * setter/modifier (needed?)
    * `Arguments#prepend` and `append`?
    * `Arguments#add_key` and `remove_key`?
    * `Arguments#strip_block` and `add_block`?
  * `Arguments.new`?
  * Destrictive versions of the operations
  * Marshal?
  * `Binding#arguments`? # ko1: it is difficult to support because of performance
  * `super` with an Arguments?

```ruby
# 2.6
def foo(*args)
  cache[args] ||= begin
    slow_calculation
  end
end

# 3.X
def foo(*args, **kwargs)
  all_args = [args, kwargs]
  cache[all_args] ||= begin
    slow_calculation
  end
end

# 3.X with Arguments class
def foo(...)
  cache[(...)] ||= begin
    slow_calculation
  end
end
```

* javascript: https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/arguments

Discussion:

```ruby
def foo(...args)
  target(...args)
end
```

```ruby
def foo(...)
  args = Method::Arguments.new(...)
  args = Method.arguments(...) #=> {positionals: [], keywords: {}, block: proc {}}
  # args = __VA_ARGS__ # was a bad idea

  args.class #=> Arguments

  # Arguments#to_a
  # Arguments#to_h
  # Arguments#to_proc

  bar(*args, **args, &args)
  
  bar(*args.positionals, **args.keywords, &args.block)
end
```

* problem 1: explicit handling both positionals and keywords is tedious!

```ruby
# 2.6
def foo(*args)
  @all_args = args
end
def bar
  final_target(*@all_args)
end

# 3.X # tedious!
def foo(*args, **kwargs)
  @all_args = [args, kwargs]
end
def bar
  final_target(*@all_args[0], **@all_args[1])
end

# 3.X with Arguments class
def foo(...)
  @all_args = (...)
  @all_args = Method.arguments(...) # usa's preference
end
def bar
  final_target(***@all_args)
  final_target(...@all_args) # conflicts with beginless range
end
```

```ruby
def foo(***args)
  bar(***args)
end
def foo(***)
  bar(***)
end
```

```ruby
def foo(old_lead, ...)
  target(new_lead, ...)
end

def foo(old_key: nil, ...)
  target(new_key: 42, ...)
end

def x(..., old_key: nil) # 
end

def foo(&old_blk, ...)
  target(&new_blk, ...)
  target(...) { .... }
end

def error(*args, **kwds, &block, ...)
end

def ok(x, opt=nil, post, k:, **kwds, &blk, ...)
end

def drop_post(*args, _, ...)
  target(*args, ...)
end
def add_post(*args, ...)
  target(*args, 42, ...)
end

def foo(...)
  @all_args = (...)
end
def bar
  final_target(***@all_args)
end

def drop_old_lead(_old_lead, ...)
  (...)
end
def add_new_lead(...)
  (...)
end
def foo(...)
  @all_args = add_new_lead(42, ***drop_old_lead(...))
end
def bar
  final_target(***@all_args)
end
```

* problem 2: performance of handling both positionals and keywords (typical case: memoization)

```ruby
def foo(...)
  args = (...)
  p args #=> Arguments (internal representation is optimizable)
  p args #=> { positionals: array, keywords: hash, block: proc } (internal representation is disclosed; difficult to optimize)
end
```

* primitive features (Notation is tentative)
  * To receive "all" arguments: `def foo(***all_args)`
    * with dropping a leading positional argument: `def foo(_, ***all_args)`
    * with dropping all positional arguments: `def foo(*positionals, ***all_args)`
    * with dropping a keyword argument: `def foo(old_key: nil, ***all_args)`
    * with dropping all keyword arguments: `def foo(**keywords, ***all_args)`
    * with dropping a block argument: `def foo(&_, ***all_args)`
  * To pass "all" arguments: `def foo(*args, **kwargs, &blk, ***all_args)`
    * with adding a lead argument: `target(42, ***all_args)`
    * with adding a keyword argument: `target(key: 42, ***all_args)` (Error when keys are conflicted)
    * with adding a block argument: `target(key: 42, ***all_args)` (Error when both block)

* matz: I hate `...` and `***`, and the idea that dropping/adding arguments

Conclusion:

* matz: Please write explicit handling of both positional arguments and keywords
* matz: I want to focus on the language design first, and to pend performance issue unless it is a big bottleneck in a real-world application
* matz: Will reply


### [[Feature #14722]](https://bugs.ruby-lang.org/issues/14722) python's buffer protocol clone (dsisnero)

* many C-extensions that use large buffer like objects in C - Numo::Narray, NMatrix, red-arrow, but to transfer between them and ruby usually copy the data to go between types
* create a c-api to describe the shape and access of the data to avoid copying data
* maybe use the arrow c data interface https://arrow.apache.org/docs/format/CDataInterface.html

Preliminary discussion:

* No one knows it (in the pre-discussion meeting)

Discussion:

* mrkn: Buffer protocol is an abstraction of memory buffer with meta information about memory layout (an array of integer, an array of struct, etc.)
* matz: What first user will use the feature?
* mrkn: ktou and me
* matz: We are not a user of buffer protocol, so we cannot create a proposal.
* matz: Please create a concrete proposal. We can review and determine it.

Conclusion:

* mrkn: will take time to create a draft proposal

### [[Feature #12901]](https://bugs.ruby-lang.org/issues/12901) Anonymous functions without scope lookup overhead. (dsisnero)

* this allows performance bump
* also can allow to serialize functions - #11630  or (isolated procs)
* can add multi-method variant if adding new syntax 

```
func add(x,y){ x + y}
mfunc add(x: Int, y: Int){ intrinsic.int_add(x,y)
mfunc add(x: String, y: String){ instinsic.string_add(x,y)}
```

Preliminary discussion:

```ruby
require 'benchmark'

class Bogus
  def call
  end
end

object = Bogus.new
pr = ->{}

N = 10_000_000

Benchmark.bm{|x|
  x.report{
    N.times{
      object.call()
    }
  }
  x.report{
    N.times{
      pr.call
    }
  }
}

__END__
ruby 2.8.0dev (2020-02-21T17:17:30Z value_cc a41dc16bd6) [x64-mswin64_140]

       user     system      total        real
   1.094000   0.015000   1.109000 (  1.161828)
   1.484000   0.000000   1.484000 (  1.502644)
```

* ko1: the main performance gap is not a outerscope lookup, but `call` is not optimized yet.

Discussion:

* nobu: it seems like `UnboundMethod` rather than `Proc`.
* ko1: Proc#call is slow not because it has a reference to outer scope, but because it is not optimized well, so the ticket itself does not make sense
* ko1: Capturing outer scope may be slow; nobu's proposal (`def -> (x) ... end`) may work for it, but I'm unsure if it is worth.
* matz: I hate `def -> (x) ... end`
* usa: I'm interested in the feature itself that does not capture environment, but have no concrete use case.
* nobu:

Conclusion:

* matz: please don't care performance.  If you want the feature because it is useful, create another ticket with a concrete use case

### [[Bug #14541]](https://bugs.ruby-lang.org/issues/14541) Class variables have broken semantics, let's fix them (jeremyevans0)

* My previous commit to fix this only raised in verbose mode, since the class variable overtaken warning was only issued in verbose mode.
* Are we OK jumping directly from verbose-mode warning in 2.7 to RuntimeError in 3.0, or do we want to issue a warning even in non-verbose mode in 3.0 and switch to RuntimeError in 3.1?

Preliminary discussion:

* Context:

2.7
```
ruby -e '@@foo = 1' #=> -e:1: warning: class variable access from toplevel
```

master
```
ruby -e '@@foo = 1' #=> -e:1:in `<main>': class variable access from toplevel (RuntimeError)
```

2.7
```
$ ruby -e 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
$ ruby -we 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
-e:1: warning: class variable @@foo of D is overtaken by C
```

master
```
$ ./miniruby -e 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
$ ./miniruby -we 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
-e:1:in `foo': class variable @@foo of D is overtaken by C (RuntimeError)
        from -e:1:in `<main>'
```

* mame: An exception raised only in verbose mode is not good.

* `./miniruby -e` should warn? Or raise an Exception?
* `./miniruby -we` should warn? Or raise an Exception?

3.0 that mame thinks:
```
$ ruby -e 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
-e:1: warning: class variable @@foo of D is overtaken by C
$ ruby -we 'class C; end; class D < C; @@foo = 1; def self.foo; @@foo; end; end; class C; @@foo = 2; end; D.foo'
-e:1: warning: class variable @@foo of D is overtaken by C
```

Discussion:

* In a case where this warning is printed, 

Conclusion:

* matz: I think an immediate error is enough


### [[Feature #16950]](https://bugs.ruby-lang.org/issues/16950) Stop nonsense keyword argument warnings in 2.6 (mame)

* 2.6 produces a warning that no longer makes sense.  Unfortunately, this warning is not only unhelpful but also annoying for supporting 3.0 keyword behavior (according to some Rails developers).  This is a change for 2.6, but how about deleting the warning?
    
Discussion:

```ruby
def foo(x)    # warning: in `foo': the last argument was passed as a single Hash
end
h = { k: 42 }
foo(**h)      # warning: although a splat keyword arguments here
```

* matz: agrees with that.
* usa: I think it is a bug fix

Conclusion:

* usa: accepted.  Will fix.


### [[Feature #16456]](https://bugs.ruby-lang.org/issues/16456) Ruby 2.7 argument delegation (`...`) should be its own kind of parameter in `Method#parameters` (aaronc81)

* Right now, the `parameters` method returns `[[:rest, :*], [:block, :&]]` if a method uses the `...` syntax.
* It'd be nice if the returned array were more clear about the method using `...`.
* Can we change the returned values to something like `[:rest, :"..."], [:block, :"..."]]`, especially now that `...` can be used after other parameters?

Preliminary discussion:

```ruby
def foo(...)
end
method(:foo).parameters
#=> 2.7     : [[:rest, :*], [:block, :&]]
#=> proposal: [[:rest, :"..."], [:block, :"..."]]
```

Discussion:

```ruby
$ ./miniruby -e 'def foo(...); end; p method(:foo).parameters'
[[:rest, :*], [:block, :&]]

$ ruby -e 'def foo(*, &blk); end; p method(:foo).parameters'
[[:rest], [:block, :blk]]
```

Conclusion:

* matz: I have no opinion.  I want to leave it up to mame and nobu.


### [[Misc #16961]](https://bugs.ruby-lang.org/issues/16961) Is overriding a method in a subclass considered as a breaking change or not? (k0kubun)

* Is it acceptable to override `Integer#zero?` which is currently handled by `Numeric#zero?` to optimize it on MJIT?
* How should we make this kind of decision? (the main topic of the ticket)

Discussion:

* matz: I think it's okay
* mame: I'm afraid if so many PRs will come.
* matz: It's okay

Conclusion:

* matz: accepted.


### [[Feature #15822]](https://bugs.ruby-lang.org/issues/15822) Add Hash#except (zverok)

* Last time it was discussed, Matz asked "We didn't see the need for Hash#except yet. Any (real world use-case)? I don't think the name except is the best name for the behavior." In the ticket's new comments, I am providing use cases and name justification. 
* I vaguely remember already adding it to some previous meetings agenda, but I don't see any outcome in the ticket

* use case: deny listing
```ruby
puts "REQUEST: #{request_data.except(:body, :apikey)}
```

* name: `except` is best because ActiveSupport and Facet use it.  Other candidates: `without_keys(*keys)`, `without(*keys)`, `except_keys(*keys)`.

Discussion:

```ruby
# File activesupport/lib/active_support/core_ext/hash/except.rb, line 12
def except(*keys)
  slice(*self.keys - keys)
end
```

Conclusion:

* matz: accepted


### [[Feature #16954]](https://bugs.ruby-lang.org/issues/16954) A new mode `Warning[:deprecated] = :error` for 2.7 (mame)

* matz: I understand that it is not really needed.
* mame: This change is for 2.7, so avoid it if it is not really needed.
* amatsuda: Agreed.
* mame: Okay I'll withdraw the proposal.

### [[Feature #16955]](https://bugs.ruby-lang.org/issues/16955) A new mode `Warning[:keyword_into_rest_arg] = true` for 2.7 (mame)

* matz: I understand that it brings many false positives.
* mame: This tool might be useful if it is used very carefully, but it is difficult to use it well.  And, we found some people care warnings too much.  I'm afraid if such people worry them needlessly.
* matz: Rejected.
* mame: Will withdraw.

### [[Feature #16812]](https://bugs.ruby-lang.org/issues/16812) Allow slicing arrays with ArithmeticSequence (mrkn)
  * I found the inconsistency between the existing behaviors: `[*0..10][-100..100]` vs `[*0..10][..100]`.
  * I believe it is better to fix the behavior of `[*0..10][-100..100]`.

Discussion:


```ruby
[*0..10][11]
# => nil

[*0..10][-11..-1]
# => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

[*0..10][-12..-1]
# => nil

[*0..10][..-1]
# => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

[*0..10][-12, 5]
# => nil
# => [0, 1, 2, 3] is better than nil
# => [nil, 0, 1, 2, 3]

[*0..10][-1, 5]
# => [10]

[*0..10][11, 1]
# => []

[*0..10][12, 1]
# => nil

[*0..10][-7..5]
#=> [4, 5]

[*0..10][(-7..5)%1]
#=> error
#=> [4, 5]
#=> [4, 5, 6, 7, 8, 9, 10]
#=> [4, 5, 6, 7, 8, 9, 10, 0, 1, 2, 3, 4, 5]

[*0..10][-7..%1]
#=> [4, 5, 6, 7, 8, 9, 10]

[*0..10][(..-6)%1]
#=> [0, 1, 2, 3, 4, 5]

[*0..10][(..-6)%2]
#=> [1, 3, 5]
```

```python
>>> list(range(0, 11))[-7:6]
[4, 5]

>>> list(range(0, 11))[-8:9]
[3, 4, 5, 6, 7, 8]

>>> list(range(0, 11))[-8:9:2]
[3, 5, 7]

>>> list(range(0, 11))[-20:20:2]
[0, 2, 4, 6, 8, 10]

>>> list(range(0, 11))[:-6:2]
[0, 2, 4]

>>> list(range(0, 11))[:-6:-2]
[10, 8, 6]

>>> list(range(0, 11))[-6::-2]
[5, 3, 1]
```

`puts "<table>\n"+(-3..3).map{|x|"<tr>"+(0..3).map{|y|"<td><code>[0,1][#{x},#{y}]=#{[0,1][x,y].inspect}</code></td>" #=>`

<table>
<tr><td><code>[0,1][-3,0]=nil</code></td><td><code>[0,1][-3,1]=nil</code></td><td><code>[0,1][-3,2]=nil</code></td><td><code>[0,1][-3,3]=nil</code></td></tr>
<tr><td><code>[0,1][-2,0]=[]</code></td><td><code>[0,1][-2,1]=[0]</code></td><td><code>[0,1][-2,2]=[0, 1]</code></td><td><code>[0,1][-2,3]=[0, 1]</code></td></tr>
<tr><td><code>[0,1][-1,0]=[]</code></td><td><code>[0,1][-1,1]=[1]</code></td><td><code>[0,1][-1,2]=[1]</code></td><td><code>[0,1][-1,3]=[1]</code></td></tr>
<tr><td><code>[0,1][0,0]=[]</code></td><td><code>[0,1][0,1]=[0]</code></td><td><code>[0,1][0,2]=[0, 1]</code></td><td><code>[0,1][0,3]=[0, 1]</code></td></tr>
<tr><td><code>[0,1][1,0]=[]</code></td><td><code>[0,1][1,1]=[1]</code></td><td><code>[0,1][1,2]=[1]</code></td><td><code>[0,1][1,3]=[1]</code></td></tr>
<tr><td><code>[0,1][2,0]=[]</code></td><td><code>[0,1][2,1]=[]</code></td><td><code>[0,1][2,2]=[]</code></td><td><code>[0,1][2,3]=[]</code></td></tr>
<tr><td><code>[0,1][3,0]=nil</code></td><td><code>[0,1][3,1]=nil</code></td><td><code>[0,1][3,2]=nil</code></td><td><code>[0,1][3,3]=nil</code></td>
</table>

`puts "<table>\n"+(-3..3).map{|x|"<tr>"+(-3..3).map{|y|"<td><code>[0,1][#{x}..#{y}]=#{[0,1][x..y].inspect}</code></td>"}.join("")}.join("</tr>\n")+"\n</table>\n" #=>`

<table>
<tr><td><code>[0,1][-3..-3]=nil</code></td><td><code>[0,1][-3..-2]=nil</code></td><td><code>[0,1][-3..-1]=nil</code></td><td><code>[0,1][-3..0]=nil</code></td><td><code>[0,1][-3..1]=nil</code></td><td><code>[0,1][-3..2]=nil</code></td><td><code>[0,1][-3..3]=nil</code></td></tr>
<tr><td><code>[0,1][-2..-3]=[]</code></td><td><code>[0,1][-2..-2]=[0]</code></td><td><code>[0,1][-2..-1]=[0, 1]</code></td><td><code>[0,1][-2..0]=[0]</code></td><td><code>[0,1][-2..1]=[0, 1]</code></td><td><code>[0,1][-2..2]=[0, 1]</code></td><td><code>[0,1][-2..3]=[0, 1]</code></td></tr>
<tr><td><code>[0,1][-1..-3]=[]</code></td><td><code>[0,1][-1..-2]=[]</code></td><td><code>[0,1][-1..-1]=[1]</code></td><td><code>[0,1][-1..0]=[]</code></td><td><code>[0,1][-1..1]=[1]</code></td><td><code>[0,1][-1..2]=[1]</code></td><td><code>[0,1][-1..3]=[1]</code></td></tr>
<tr><td><code>[0,1][0..-3]=[]</code></td><td><code>[0,1][0..-2]=[0]</code></td><td><code>[0,1][0..-1]=[0, 1]</code></td><td><code>[0,1][0..0]=[0]</code></td><td><code>[0,1][0..1]=[0, 1]</code></td><td><code>[0,1][0..2]=[0, 1]</code></td><td><code>[0,1][0..3]=[0, 1]</code></td></tr>
<tr><td><code>[0,1][1..-3]=[]</code></td><td><code>[0,1][1..-2]=[]</code></td><td><code>[0,1][1..-1]=[1]</code></td><td><code>[0,1][1..0]=[]</code></td><td><code>[0,1][1..1]=[1]</code></td><td><code>[0,1][1..2]=[1]</code></td><td><code>[0,1][1..3]=[1]</code></td></tr>
<tr><td><code>[0,1][2..-3]=[]</code></td><td><code>[0,1][2..-2]=[]</code></td><td><code>[0,1][2..-1]=[]</code></td><td><code>[0,1][2..0]=[]</code></td><td><code>[0,1][2..1]=[]</code></td><td><code>[0,1][2..2]=[]</code></td><td><code>[0,1][2..3]=[]</code></td></tr>
<tr><td><code>[0,1][3..-3]=nil</code></td><td><code>[0,1][3..-2]=nil</code></td><td><code>[0,1][3..-1]=nil</code></td><td><code>[0,1][3..0]=nil</code></td><td><code>[0,1][3..1]=nil</code></td><td><code>[0,1][3..2]=nil</code></td><td><code>[0,1][3..3]=nil</code></td>
</table>

* akr: The current behavior is difficult to explain
* naruse: Agreed.
* ko1: I'm afraid of incompatibility.
* mame: Me too.
* matz: Please create a full proposal so that I can accept or reject.  Note that usability is more important than consistency.  Even if the behavior is inconsistent, I'm okay if it is really useful.

Conclusion:

* Pending