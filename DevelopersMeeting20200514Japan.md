---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200514Japan

https://bugs.ruby-lang.org/issues/16775

## Next Date

* 6/18 (Thu) 13:00-17:00 @ online

## Announce

* Try a google calendar to shapre the schedule of dev-meeting
  * https://calendar.google.com/calendar?cid=bHZkcTlsdjhsYmJyYWVlZHRtY3Y5YTB1N2tAZ3JvdXAuY2FsZW5kYXIuZ29vZ2xlLmNvbQ
* Try to use GitHub repository to publish the dev-meeting log instead of Google drive
  * https://github.com/ruby/dev-meeting-log

## Ruby 2.7 timeframe

* Reconsider keyword argument separation
  * To listen pain points of migration
  * Discuss on https://discuss.rubyonrails.org/ with Matz
  * after some remediation is implemented 2.7.x will be released
  * https://discuss.rubyonrails.org/t/new-2-7-3-0-keyword-argument-pain-point/74980
  * matz: created: https://discuss.rubyonrails.org/t/new-2-7-3-0-keyword-argument-pain-point/74980

## About 2.8/3.0 timeframe

## Check security tickets

[secret]

### [[Misc #16778]](https://bugs.ruby-lang.org/issues/16778) Should we stop vendoring default gems code? (greggzst)

* it makes maintenance easier and removes the need for synchronization
* removes confusion for external contributors
* Ruby uses git so git submodules could be used
* it's possible to setup CI to run against Ruby master

Preliminary discussion:

* We only focus the problem about "removes confusion for external contributors". We will ask "Is there the idea to resolve this?".

Discussion:

* naruse: first of all the problem is the directory structure is different

Conclusion:

* hsbt: move it to feedback


### [[Feature #16812]](https://bugs.ruby-lang.org/issues/16812) Allow slicing arrays with ArithmeticSequence (zverok)

* `ary[(5..20) % 2]`—each second element between 5 and 20
* `ary[(0..) % 3]`—each third element
* `ary[10.step(by: -1)]`—elements 10, 9, 8, 7 ....
* Python has and uses it (in form of `beg:end:step`), and `ArithmeticSequence` and `Range#%` were introduced for this

Preliminary discussion:

* Should we have `#[]=` too?
* How is negative index handled?

```ruby
ary = [0, 1, 2, 3, 4]
ary[(1..3) % 2] = "x"
p ary #=> ?

nums = (0..20).to_a

# step: 1
p nums[-5..5]   #=> []
p nums[-5..-3]  #=> [16, 17, 18]
p nums[-30..-3] #=> nil
p nums[(15..)] #=> [15, 16, 17, 18, 19, 20]

p nums[(15..)%2] #=> [15, 17, 19] ??
```

Discussion:

* matz: I'm unsure about use case
* akr: It should the same (or similar) behavior not only as Python but also as numo-naray.
* mrkn: I'll investigate the behavior of numo-narray.

Conclusion:

* matz: Will ask use case


### [[Feature #16822]](https://bugs.ruby-lang.org/issues/16822) Array slicing: nils and edge cases (zverok)

* Never return `nil` from `ary[start...end]` even if out of arrays' bounds

Discussion:

```ruby
ary = [1, 2, 3]

a[4..] # => nil 
a[-10..-9] # => nil 
```

Conclusion:

* matz: Keep compatibility. Will reject.


### [[Feature #16815]](https://bugs.ruby-lang.org/issues/16815) Introduce `Fiber#backtrace`. (ioquatix)

- Add `Fiber#backtrace` which gets any fiber backtrace. Very useful for debugging fiber state.

Preliminary discussion:

* ko1: looks good

Discussion:

* ko1: looks good
* nobu: looks good

Conclusion:

* matz: accepted


### [[Feature #16786]](https://bugs.ruby-lang.org/issues/16786) Light weight scheduler for improved concurrency. (ioquatix)

- Please check it and confirm if we can merge it, or outline what changes are required.

Preliminary discussion:

* mame's personal opinion (not important)
  * The design of Schedular API looks ad-hoc slightly 
  * `Fiber do` API must be selected by matz
  * What's happen if we resume a non-blocking fiber that cannot run immediately


Discussion:

```ruby
class Scheduler
  def initialize
    @fibers = []
  end

  def wait_readable(io)
    Fiber.yield
  end

  def fiber(&blk)
    f = Fiber.new(blocking: false, &blk)
    @fibers << f
    f
  end

  def run(&blk)
    main_fiber = Fiber.new(&blk)
    loop do
      main_fiber.resume
      @fibers.sample.resume
    end
  end
end

Thread.scheduler = Scheduler.new

Thread.scheduler.run do
  f1 = Fiber.new(blocking: false) do
    $io1.read
  end.resume
  # Fiber do .. end = Thread.scheduler.fiber do .. end

  f2 = Fiber.new(blocking: false) do
    $io2.read
  end
end
```

* akr: How about process wait?
* ko1: there is another ticket about Mutex#lock

* usa: What happen when reassigning `Thread.scheduler`?
* matz: idea: After reassingment, new Fibers uses reassigned scheduler and old Fibers uses old one
* ko1: But Thread only knows current (reassigned) scheduler
* usa: Nonblock Fibers should know their scheduler, I think
* ko1: It's one reasonable idea
* usa: So, ↓ such interface is better, isn't it?
```ruby
aScheduler = SomeScheduler.new
# ...
Fiber.new(scheduler: aScheduler) do
  # ...
end
```

* mame: But, there are some troublesome cases if not using thread scope scheduler 
```ruby
Thread.current.scheduler = SomeScheduler.new
# ...
Fiber.new(scheduler: true) do
  # ...
end
```


```ruby
# lib1
def lib
  Fiber.new blocking: true do
    yield
  end.resume
end

class Scheduler
  def wait_readable(io)
    Fiber.yield
  end
end

# Thread.current.schedule
Fiber.new(scheduler: true) do
  lib do
    $io.read
    # Currently, this call blocks.
    # But it is very unintuitive for the author of this code.
    # Even, it may lead to vulnerability such as DoS attack.
    # So, I (mame) believe it should call Scheduler#wait_readable,
    # but Fiber.yield in wait_readable escapes from the Fiber that lib created,
    # which will violate lib's intention.
    # So, I'm afraid if people will blindly avoid any use of blocking fibers.
  end
end
```

Conclusion:

* mame: Will write a comment about my concern
* matz: try it in master.  Tentatively accepted.


### [[Feature #16746]](https://bugs.ruby-lang.org/issues/16746) Endless method definition (eregon)

* Is it intended to allow multi-line definitions? Seems redundant and likely to make code less readable (`end` for class/module/do/if/while/... still exist).

Preliminary discussion:

* mame: I think side-effect is not supposed in endless method definition, but many people will use it?  If so, the syntax should be removed?
  * Or, at least, `def foo(n) = @x = n` must be prohibited, I think
  * `def foo(n) = (@x = n)` is okay?
  * matz: OK; but I think `def foo(n) @n = n end` is better
  * How about `def foo(n) = n => @x` (=== `(def foo(n) = n) => @x`)?


```ruby

class C
  def foo=(n) = @x = n

  def foo(n) = @x = n
  # => def foo(n) = (@x = n)
  
  def foo(n) = n => @x
  # => (def foo(n) = n) => @x

  # current semantics
  @x = def foo(n)
         n
       end

  # expected?
  def foo(n)
    @x = n
  end
  
  
  def calc(n) = f(n).
                g().
                h() => @y
end

def foo() = 1 => @x
p @x #=> :foo ((def foo() = 1) => @x)

private def foo(n) = 1 => @x
# private((def foo(n) = 1) => @x)

private((def foo(n); 1; end) => @x)

def inc(n) = n + 1
def const = 42
def getter = @foo
```


* `def foo() = expr`: why is `()` mandatory?
  * Because `def foo=bar` is ambiguous: `def foo=(bar) end` or `def foo() = bar` ?

* discussion white board...
  * `def foo=1`
  * `def foo`
  * `def foo=bar` → `def foo=(bar)...` or `def foo() = bar` ?
  * `def foo = bar`
  * `def foo=()=bar`
  * `def foo=bar`
  * `def foo= bar`
  * `def foo=bar; end`
```ruby
def foo= x
  @x = x
end

def f():body; end
def f(x):body; end
def f: nil
def foo bar: baz   # NG: confusion with kwargs def foo(bar: baz)
def f(x): @x = x
def double(x): x*2
def square(x): x*x
def foo:a
def foo=(x) => @x = x
def foo=(x) => x => @x
def foo=>bar
def foo => bar
def foo x => bar
def foo k1 => v1 => bar # NG, conflict with kwargs
def foo(k1 => v1) => bar
def foo x=y=>z # NG
def foo <= x
def foo := body
def foo x := body


def foo() = body if cond

# confusing
def foo
  body if cond
end

# current semantics
def foo
  body
end if cond

def foo() = (body if cond) # for confusing case

# how to handle `rescue`?
def foo() = body rescue foo

```

* Problem: `def foo=bar` is ambiguous, `def foo=(bar)...` or `def foo() = bar` ?
* What we can do (with compatibility kept):
  * Make `()` mandatory; (matz: I chose this, but I might reconsider)
  * Make a space mandatory


```ruby
def fib(n) = begin
  if n < 2
    n
  else
    fib(n-1) + fib(n-2)
  end
end

def fib(n) = begin
               if n < 2
                 n
               else
                 fib(n-1) + fib(n-2)
               end
             end
```

- https://github.com/crystal-lang/crystal/issues/9080

Discussion:

* matz: I don't recommend multiline code, but don't limt the language itself.  rubocop may have their own style.

Conclusion:

* matz: Let me have some time to consider this issue
  * `def foo() =` or `def foo() :`
  * `def foo(n) = n => @x`
  * `def foo=bar` may break compatibility (`def foo= bar; bar; end`); So, no-arugment mark `()` is mandatory.  But I may reconsider


### [[Feature #16837]](https://bugs.ruby-lang.org/issues/16837) Can we make Ruby 3.0 as fast as Ruby 2.7 with the new assertions? (k0kubun)

* Do we have a policy on what kind of assertions should be always enabled and what should be CI-only, especially when it has impact on performance?

Discussion:

* shyouhei: How about `optflags=-DNDEBUG` by default?
* ko1: If we want to enable assertions?
* shyouhei: You can set `optflags=-O3`.

* (failed to log much discussion..)

* ko1: We cannot make users use the assertion-enabled version; anyway, users will use assertion-disabled one
* ko1: It is not acceptable to release assertion-enabled version by default
* shyouhei: makes sense.
* ko1: We may provide assertion-enabled ruby for CIs (GitHub Actions and Docker images).  Users may choose the assertion-enabled one.
* We agreed

Conclusion:

* `./configure` should disable assertions
* `./configure --enable-debug` should enable assertions
  * ko1 and shyouhei will consider how to implement it


### [[Misc #16747]](https://bugs.ruby-lang.org/issues/16747) Repository reorganization request (shyouhei)

* hsbt (Hiroshi SHIBATA) says: I remember nobu (Nobuyoshi Nakada) has the working branch about this.
* I would like to know its current situation.

Discussion:

* WIP: https://github.com/nobu/ruby/tree/feature/src-dir 

* `*.c` `*.h` `*.rb` -> `TOPDIR/src/` (decided)
* `include` -> `TOPDIR/src/include` or `TOPDIR/include`

Conclusion:

* Decided: `*.c` `*.h` `*.rb` -> `TOPDIR/src/`
* Each other directory should be discussed


### [[Misc #16803]](https://bugs.ruby-lang.org/issues/16803) Discussion: those internal macros reside in public API headers (ko1)

* confirm the discussion.

Discussion:

* all: leave all to nobu

Conclusion:

* If we want to change file names, we should propose it and nobu will decide


### [[Feature #9758]](https://bugs.ruby-lang.org/issues/9758) Allow setting SSLContext#extra_chain_cert in Net::HTTP (stan3)

* useful to allow https with cert chain, small patch, few :+1s

Preliminary discussion:

* Ask naruse.

Conclusion:

* Merged




timeout: We'll hold an extra meeting to discuss remaining topics: 5/26 (Tue) 13:00-

======================================================




### [[Feature #16806]](https://bugs.ruby-lang.org/issues/16806) Struct#initialize accepts keyword arguments too by default (k0kubun)

* This obviates `keyword_init: true`. Is the described incompatibility and release plan (3.0: warn, 3.1: introduce) acceptable? 

Preliminary discussion:

* mame: it may depends on the plan of keyword separation

Discussion:

* nobu: kwargs is still under discussion

Conclusion:

* 


### [[Feature #15771]](https://bugs.ruby-lang.org/issues/15771) Add `String#split` option to set `split_type string` with a single space separator (sawa)

* Proposal by 284km. Allow splitting literally by `" "` when the argument is  `" "`.

Preliminary discussion:

* znz: how to optimize `split(/ /)` treat as `split(' ', literal: true)` in proposal?

Discussion:

* nobu: have wanted to deprecate that behavior for years, and made non-nil `$/` warned.

Conclusion:

* 


### [[Feature #16832]](https://bugs.ruby-lang.org/issues/16832) Use `#name` rather than `#inspect` to build "uninitialized constant" error messages (byroot)

* As `#name` on `Module` and `Class` is more likely to return a proper constant name than `#inspect`

Preliminary discussion:

```ruby
class C
  def self.inspect
    "inspect"
  end
end
C.const_get(:Foo)
# t.rb:8:in `const_get': uninitialized constant inspect::Foo (NameError)
# proposed:
#   uninitialized constant C::Foo
```

Discussion:

* nobu: nameless class doesn't have name but acceptable

Conclusion:

* 


### [[Feature #8661]](https://bugs.ruby-lang.org/issues/8661) Add option to print backtrace in reverse order (stack frames first and error last) (mame)

* matz, could you check the spec of `--suppress-backtrace`?  And should we change the name to `--backtrace-limit` or something?

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* matz: change the name.  The behavior is good.  Go ahead


### [[Feature #16827]](https://bugs.ruby-lang.org/issues/16827) C API for writing custom random number generator that can be used as Random objects (mrkn)

* I need such a feature to implement custom RNGs in an extension library

Discussion:

* It allows to define `Random::ChaCha` and `Random::XXX` as an external library (or gem)
* We can define such a class in Ruby (by defining an object that responds to `#rand`), but it is slow

Conclusion:

* matz: Looks like it does not rqeuire my approval, but I approve.  `Random::ChaCha` should not be added.


### [[Feature #16254]](https://bugs.ruby-lang.org/issues/16254) MRI internal: Define built-in classes in Ruby with `__intrinsic__` syntax (eregon)

* Thoughts on using `Primitive.name(a, b)` instead of `__builtin_name(a, b)`?
* I think this is an important opportunity to share more code between implementations.

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* 


### [[Feature #15921]](https://bugs.ruby-lang.org/issues/15921) R-assign (rightward-assignment) operator (eregon)

* Could matz and other committers clarify the motivation to introduce this? There is no pipeline operator currently so it seems of limited usage.
* Changing syntax often divides the community (e.g., https://twitter.com/devoncestes/status/1256222228431228933), should we be more careful when introducing new syntax? For instance, on syntax issues matz considers to merge, he could state his intention, tweet about the proposal, let it be for at least a month and then decide (merge or not). A blog post by ruby core would be another way to trigger feedback before merging. Often it feels like a decision "out of the blue" with no clear motivation. Discussion after merging feels suboptimal because people realize they have very little chance to change anything and so just love or hate it. Being in master doesn't help much because most people won't try it, yet they can share their opinion based on code snippets using the new syntax.

Preliminary discussion:

* hsbt: The example provided by Prof. Martin is good.
* mame: matz has a priviledge to experiment a new syntax, and IMO it works perfectly
  * mame: (personally, I'm slightly negative against this syntax, though)
* ko1: How about limiting the R-assign target to only a local variable?  I was surprized at `expr => CONST`

```ruby
x = 1; y = 2
p(x => y) # {1 => 2}
p(y = x)  # 2 (x = 2)
```

```ruby
# combination with endless-definitions
def foo(x)= @x = x
def foo(x)= x => @x

  # existing example from ko1's code
  def initialize screen
    @screen = screen
  end
  # replace with?
  def initialize(screen) = @screen = screen
  def initialize(screen) = screen => @screen

  def initialize(screen) = (@screen = screen)
  
def inc(n) = n + 1

def foo() = 1 => @x
p @x #=> :foo ((def foo() = 1) => @x)

private def foo(n) = 1 => @x
# private((def foo(n) = 1) => @x)

until eof?(getc => c) # oh it's kwarg
  # ...
end
```

- http://www.a-k-r.org/d/2017-08.html
- https://twitter.com/yukihiro_matz/status/903387184807292929

Discussion:

* 

Conclusion:

* 


### [[Feature #16828]](https://bugs.ruby-lang.org/issues/16828) Introduce find patterns (ktsj)

* I implemented it as matz wanted it.  Please discuss it on the dev-meeting.

Preliminary discussion:

* 

Discussion:

* 

Conclusion:

* 

### [[Feature #16847]](https://bugs.ruby-lang.org/issues/16847) Cache instruction sequences by default (byroot)

* This make code loading about 30% faster.
* All it's needed is to agree on where and how to store the cache.

Preliminary discussion:

* How about to change rubygems mechanism at first?(hsbt)

Discussion:

* 

Conclusion:

* 


### [[Feature #16848]](https://bugs.ruby-lang.org/issues/16848) Allow callables in $LOAD_PATH (byroot)

* They would receive the relative path as first argument, and be expected to return an absolute path or nil
* This would make implementing various lookup caching strategies in Rubygems/Bundler much easier and more accurate.
* Such caching can yield massive boot time improvements.

Preliminary discussion:

```ruby
gem_path_resolver = -> feature do
  case feature
  when "bar" then "/gems/bar/bar.rb"
  when "baz" then "/gems/baz/baz.rb"
  else
    nil
  end
end

$LOAD_PATH = [
  "my_app/",  # there is my_app/foo.rb
  gem_path_resolver,
  "/stdlib/", # there is /stdlib/qux.rb
]

require "foo" # load my_app/foo.rb
require "bar" # load /gems/bar/bar.rb
require "baz" # load /gems/baz/baz.rb
require "qux" # load /stdlib/qux.rb
```

```ruby
# /home/gem-codesearch/gem-codesearch/latest-gem/FlickrCollage-0.1.1/Rakefile
  $LOAD_PATH.each do |path|
    next unless File.exist?(File.join(path, "rspec"))
    puts "Found rspec in #{path}"
    if File.exist?(File.join(path, "rspec", "core"))
      puts "Found core"
      if File.exist?(File.join(path, "rspec", "core", "rake_task"))
        puts "Found rake_task"
        found = path
      else
        puts "!! no rake_task"
      end
    else
      puts "!!! no core"
    end
  end

# /home/gem-codesearch/gem-codesearch/latest-gem/Graphiclious-0.1.5/lib/graphiclious/environment.rb
    $LOAD_PATH.each do
      |d|
      if /graphiclious.lib$/ =~ d
        return d[0..-5].gsub(/\\/, '/');
      end
    end

# /home/gem-codesearch/gem-codesearch/latest-gem/Pistos-ruby-which-0.5.3/lib/ruby-which.rb
class Which
  # Example usage:
  #   puts Which.which( 'rails' )
  # Or from your shell:
  #   rwhich rails
  # Returns the full library path if the given library is found in the $LOAD_PATH.
  # Under rare circumstances, an Array may be returned in the case of multiple
  # matches under one path.
  def self.which( lib )
    begin
      require lib
    rescue LoadError
      return nil
    end

    $LOAD_PATH.each do |path|
      extension = nil
      if File.extname( lib ).empty?
        extension = '.{rb,so,o,dll,class}'
      end
      found = Dir[ "#{path}/#{lib}#{extension}" ]
      if found.size == 1
        return found[ 0 ]
      elsif found.any?
        return found
      end
    end

    nil
  end
end

# /home/gem-codesearch/gem-codesearch/latest-gem/abaci-0.3.0/vendor/bundle/gems/erubis-2.7.0/contrib/inline-require
      $LOAD_PATH.each do |path|
         if libname_rb
            libpath = path + "/" + libname_rb
            return libpath if test(?f, libpath)
         end
         if libname_so
            libpath = path + "/" + libname_so
            return libpath if test(?f, libpath)
         end
      end

# /home/gem-codesearch/gem-codesearch/latest-gem/abaci-0.3.0/vendor/bundle/gems/rake-11.2.2/lib/rake/testtask.rb
    def find_file(fn) # :nodoc:
      $LOAD_PATH.each do |path|
        file_path = File.join(path, "#{fn}.rb")
        return file_path if File.exist? file_path
      end
      nil
    end
```
Discussion:

* 

Conclusion:

* 
