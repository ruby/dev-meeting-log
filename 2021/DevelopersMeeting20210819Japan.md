---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210819Japan

https://bugs.ruby-lang.org/issues/18039

## DateTime and location

* 8/19 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 9/16 (Thu) 13:00-17:00 JST @ Online
  * pre: 9/14
  * need confirmation

## Announce

## About release timeframe

* 3.1 preview1?
  * RubyKaigi Takeout 2021?

## Check security tickets

[secret]

## Tickets

### [[Misc #18059]](https://bugs.ruby-lang.org/issues/18059) Which `FL_USER`x are open to extension libraries? (nobu)

Discussion:

* nobu: I think `FL_USERx` should be used only for `T_DATA`
* akr: Agreed
* mame: Eregon seems to agree with it
* mame: why is `ROBJECT_TRANSIENT_FLAG` `FL_USER13`, not `FL_USER1` or `FL_USER19`?
* ko1: Because `RSTRING`'s `FL_USER13` was not occupied

Conclusion:

* mame: `T_DATA`'s `FL_USER1` to `19` are reserved for extension libraries, okay?
* ko1, nobu: Okay

Off-topic:

* ko1: `ROBJECT_TRANSIENT_FLAG` means that the object has an instance variable table
* ko1: The object in question has no instance variable, but it keeps transient heap for ivars. This is because if an object of class C allocate ivar fields, after that an instance of C is created, the ivar fields are also allocated.

### [[Feature #18051]](https://bugs.ruby-lang.org/issues/18051) Move symbols exported under internal (nobu)

* Some symbols exported in headers under `internal` are declared and used in some extension libraries.

Discussion:

* mame: I guess no one is against

Conclusion:

* nobu: I will do

### [[Bug #13392]](https://bugs.ruby-lang.org/issues/13392) TracePoint return event location is incorrect for methods defined with define_method (alanwu)

* There are two PRs available, one more complex but opens the possibility for future optimizations. Which do you prefer?

Preliminary discussion:

* ko1: will read.

Discussion:

* ko1: This is an implementation matter, I will handle

### [[Bug #18060]](https://bugs.ruby-lang.org/issues/18060) Fix infinite loop when b_return TracePoint throws (alanwu)

* Does the fix make sense?

Preliminary discussion:

* ko1: I will read.

Discussion:

* ko1: This is an implementation matter, I will handle

### [[Bug #18031]](https://bugs.ruby-lang.org/issues/18031) Nested TracePoint#enable with define_method target crashes (alanwu)

* Does my fix look good? It assumes that multiple TracePoints targeting the same bmethod should work similar to other targets.

Preliminary discussion:

* ko1: will read.

Discussion:

* ko1: This is an implementation matter, I will handle

### [[Feature #18045]](https://bugs.ruby-lang.org/issues/18045) Variable Width Allocation Phase II (duerst)

* Variable Width Allocation is a major change to Ruby internals and therefore should be discussed seriously.

Discussion:

* Degenerate case is same as current behaviour - buckets of size X only.
* Improved memory locality.
    * Inline allocation of String, Array, etc.
* Less memory allocations.
* Longer term, do we aim for 32-byte RObject size?
* Potential reallocation overheads.
    * String size changing/resizing - causes reallocation?
    * Resize may cause malloc and then wasted bytes (original size bucket is unused).
        * Can we measure this overhead?
* Optional feature (long term)?
* Can we show a practical example of VWA usage and performance improvement?
    * The class_ext example is easy to understand, but are there others?

### [[Feature #10917]](https://bugs.ruby-lang.org/issues/10917) Add GC.stat[:total_time] (byroot)

* Time spent in GC is a very important metric for application performance monitoring.
* Right now only `GC::Profiler` can be used, but it is ill suited when there are multiple callers because it is stateful. It also need to be gated by a Mutex.
* A single monotonically increasing time metric would be much more usable.
* If the `getrusage_time` overhead is dimmed to big, it could be a an opt-in metrics through a flag on `::GC`.
* I don't ask to merge that particular patch, but wether the general idea is acceptable.
* @eregon suggested `GC.stat[:time]` in milliseconds, as that's what JRuby and TruffleRuby already expose.

Preliminary discussion:

* mame: I'm unsure how accurate to accumlate short durations
* ko1: I'm afraid of the performance. Incremental marking and lazy sweeping run gradually (maybe thousands in one second?) so measuring the clock could be problematic
* ko1: Proposed patch reused GC::Profier's mechanism and it doesn't catch up recent changes. I'll make another patch and measure the performance.
* ko1: preliminary measurement: https://github.com/ruby/ruby/pull/4757

```
[RUBY_DEBUG_COUNTER]    gc_enter_start                           1,778
[RUBY_DEBUG_COUNTER]    gc_enter_mark_continue                       6
[RUBY_DEBUG_COUNTER]    gc_enter_sweep_continue                 49,547
[RUBY_DEBUG_COUNTER]    gc_enter_rest                                1
[RUBY_DEBUG_COUNTER]    gc_enter_finalizer                           1

master:
       user     system      total        real
   3.332477   0.000425   3.332902 (  3.332915)

with time:
       user     system      total        real
   3.436177   0.000084   3.436261 (  3.436298)
{:count=>1778,
 :time=>1286,
...
```

```ruby=
pooled = Array.new(100_000){ [] }
N = 5_000_000
h1 = h2 = nil
require 'benchmark'
Benchmark.bm{|x|
  x.report{
    h1 = GC.count
    N.times{
      a = [[[[[[[[[[[[[[[[[[[[[[[[[]]]]]]]]]]]]]]]]]]]]]]]]]
    }
    h2 = GC.count
  }
}
pp GC.stat
```

It's shows worst case.

```ruby
pooled = Array.new(100_000){ [] }
N = 50_000_000
h1 = h2 = nil
require 'benchmark'
Benchmark.bm{|x|
  x.report{
    h1 = GC.count
    N.times{
      a = "str".upcase
    }
    h2 = GC.count
  }
}
pp GC.stat

master:
       user     system      total        real
   5.026109   0.003133   5.029242 (  5.029301)

with time:
       user     system      total        real
   4.995175   0.000336   4.995511 (  4.995567)
{:count=>1423,
 :time=>1229,
 ...
```

If it does some tasks (`String#upcase`), there is no penalty (faster!!).

Discussion:

* mame: How long one GC process takes? -> About 22 microseconds
* matz: How about introducing it as off by default?
* ko1: `GC.measure_time = true/false # (default: false)`

Conclusion:

* matz: accpet the proposal itself. I leave it to ko1 how much overhead it is and whether it should be enabled by default or not

### [[Bug #18036]](https://bugs.ruby-lang.org/issues/18036) Pthread fibers become invalid on fork - different from normal fibers. (jeremyevans0)

* `fork` terminates other threads.
* When using pthread coroutine, all fibers are threads, therefore `fork` ignores all other fibers (in child process).
* Are we OK with this runtime difference with pthread coroutine? (I am.)
* Do we want to terminate other fibers when forking always, or just when using the pthread coroutine?
* Do we want to raise an exception if calling `fork` from a non-main fiber?

Preliminary discussion:

> * Do we want to terminate other fibers when forking always, or just when using the pthread coroutine?

* ko1: I can't accept terminate fibers on all platform.

* Currently on fork, all threads are terminated except calling thread.
    * This means also all fibers on those threads are dead.

Discussion:

The following program SEGFAULT:

``` ruby
#!/home/samuel/.rubies/ruby-head/bin/ruby

require 'fiber'

fiber = Fiber.new do
  while true
    puts "Hello World"
    fork
    Fiber.yield
  end
end

fiber.resume
```

* Samuel: We should consider whether we break existing code by preventing fork from within Fiber. If it doesn't cause any problem, we can consider implementing it across all implementations.
    * We can prevent fork within non-main fiber on platforms where it may be unreliable (i.e. pthread coroutine).
* mame: it makes sense. ↑Conclusion

Conclusion:

* matz: I will reply

### [[Feature #16889]](https://bugs.ruby-lang.org/issues/16889) TracePoint.enable { ... } also activates the TracePoint for other threads, even outside the block (jeremyevans0)

* I think this behavior is currently expected. At least the tests expect it, and the behavior seems reasonable.
* Ruby could benefit from a nicer way to pass a block and only allow tracing for the block.
* I implemented this nicer way by supporting a `target: :block` option.
* Is the current behavior of `TracePoint.enable` with a block expected?
* Is it OK to add support for a `target: :block` option using the patch?

Preliminary discussion:

* ko1: I'll ask Jeremy about it.

Discussion:

* mame: I agree with eregon, I like to make TracePoint#enable with a block thread-local. I hope this incompatibility is acceptable
* mame: But I'm unsure if TracePoint#enable without a block should be also changed. For the consistency, we want to change it too, but it may break Zeitwerk

Conclusion:

* naruse: Let's change it at Ruby 3.2

### [[Bug #15404]](https://bugs.ruby-lang.org/issues/15404) Endless range has inconsistent chaining behaviour (jeremyevans0)

* Do we want to make ranges of ranges a parse error (e.g. `(1..2)..(1..3)`)?
* I think we shouldn't.  It's possible to define `Range#<=>` and `Range#succ` such that a range of ranges could be useful.

Preliminary discussion:

* mame: I agree with jeremy

Discussion:

```ruby
1.. ..1 # 1..(..1)
```

Conclusion:

* matz: It is okay as-is. Rejected.

### [[Bug #15428]](https://bugs.ruby-lang.org/issues/15428) Refactor Proc#>> and #<< (jeremyevans0)

* Do we want `Proc#<<` and `Proc#>>` to call `to_proc` on the argument given?
* Doing so would allow usage of symbols and other things that implement `to_proc` but not `call`.
* Doing so always would break usage with arguments that respond to `call` but do not respond to `to_proc`.
* Doing so only if the argument responds to `to_proc` and not `call` may result in inconsistent behavior.
* I see no problem with the current situation, but I'm not opposed to calling `to_proc` if the object does not respond to `call`.

Preliminary discussion:

* mame: I vote for no change

Discussion:

```ruby=
(a >> b).call(arg) #=> b.call(a.call(arg))
(a << b).call(arg) #=> a.call(b.call(arg))

# already working
p (File.method(:basename) >> :upcase.to_proc).call("/foo/bar")
#=> "BAR"

# proposed
p (File.method(:basename) >> :upcase).call("/foo/bar")
#=> "BAR"

# Should we allow this too??
p (:upcase << File.method(:basename)).call("/foo/bar")

# SUPER CLEAR
-> { File.basename(_1).upcase }.call("/foo/bar")

(JSON.method(:parse) >> :symbolize_keys.to_proc).call('{"foo": "bar"}')
(JSON.method(:parse) >> :symbolize_keys).call('{"foo": "bar"}')
-> { JSON.parse(_1).symbolize_keys }.call("/foo/bar")
```

Conclusion:

* matz: I will consider

### [[Bug #14744]](https://bugs.ruby-lang.org/issues/14744) Refinements modules have a superclass (jeremyevans0)

* Should we hide the superclass of a refinement module from reflection APIs such as `ancestors`?

Discussion:

* matz: Looks good to hide the internal.

Conclusion:

* matz: Will reply

### [[Feature #16182]](https://bugs.ruby-lang.org/issues/16182) Should `expr in a, b, c` be allowed or not? (ktsj)

* How about allowing brackets/braces to be omitted in one-line pattern matching?

Preliminary discussion:

```ruby=
ary = [1, 2, 3]
ary => [a, b, c] # already allowed (exception on not-matched)
ary => a, b, c # proposal: let's allow this (same meaning as above line)
ary in a, b, c # ditto

hash => {a:, b:} # already allowed
hash => a:, b: # proposal: let's allow this (same meaning as above line)
hash in a:, b: # ditto

foo(ary => a, b, c) # it is clear that we cannot use => as a method argument
foo(ary in a, b, c) # ambiguous, but treat it like =>
foo((ary in a, b, c)) # OK
```

```ruby=
# related syntax https://bugs.ruby-lang.org/issues/18080

p do
end => a
p a #=> nil

p 1 do
end => a
p a #=> 
# syntax error, unexpected =>, expecting end-of-input
# end => a
#    ^~

p(1) do
end => a
p a #=> 1

p 1 => a

refine Array do
  ...
end => a

refine(Array) {} => a
```

* matz: I want to fix this if possible

Conclusion:

* matz: Agreed with omission of parentheses

Off-topic:

* ktsj: a new class is introduced. Okay?
* matz: a bit longer name, but okay
```ruby
NoMatchingPatternKeyError.new(message = nil, matchee: nil, key: nil)
```

### [[Feature #18007]](https://bugs.ruby-lang.org/issues/18007) C extension "allocation function" check (tenderlovemaking)

* Should we add a config option and put in 3.1, or just wait for 3.2?

Preliminary discussion:

* mame: How about raising an exception when attempting to allocate a T_DATA object with the default allocator?
* nobu: Unfortunately it is impossible because `Class#allocate` creates T_OBJECT

Discussion:

* naruse: the configure option is acceptable if it is off by default.
* matz: I accept this. It should be merged in future

Conclusion:

* nobu: I have a good idea. I will ~~create a~~ comment the ticket
  * If `rb_data_object_wrap` is called on a `T_DATA` object with the default allocator, it rewrites the dummy allocator that raises an exception
  * If `Class#allocate` is called on the object, the dummy allocator will be called and then raise an exception
  * mame: It has no false positive, looks very good

### [[Feature #18083]](https://bugs.ruby-lang.org/issues/18083) Capture error in ensure block. (ioquatix)

We want to make it easy for people to write robust Ruby programs, even if there is failure. As discussed in <https://bugs.ruby-lang.org/issues/15567> there are some tricky edge cases when using `$!` because it's non-local state. As an example:

``` ruby
def do_work
  begin # $! may be set on entry to `begin`.
    # ... do work ...
  ensure
    if $!
      puts "work failed"
    else
      puts "work success"
    end
  end
end
 
begin
  raise "Boom"
ensure
  # $! is set.
  do_work
end
```

There are real examples of this kind of code: https://bugs.ruby-lang.org/issues/15567#note-26

There is another kind of error that can occur:

``` ruby
begin
  raise "Boom"
rescue => error
  # Ignore?
ensure
  pp error # <RuntimeError: Boom>
  pp $! # nil
end
```

As a general model, something like the following would be incredibly useful, and help guide people in the right direction:

``` ruby
begin
 ...
ensure => error
  pp "error occurred" if error
end
```

Currently you can almost achieve this:

``` ruby
begin
  ...
rescue Exception => error # RuboCop will complain.
  raise
ensure
  pp "error occurred" if error
end
```

The limitation of this approach is it only works if you don't need any other rescue clause. Otherwise, it may not work as expected or require extra care (e.g. care with `rescue ... => error` naming. Also, Rubocop will complain about it, which I think is generally correct since we shouldn't encourage users to `rescue Exception`.

Because `$!` can be buggy and encourage incorrect code, I also believe we should consider deprecating it. But in order to do so, we need to provide users with a functional alternative, e.g. `ensure => error`.

Another option is to clear `$!` when entering `begin` block (and maybe restore it on exit?), but this may break existing code.

#### More Examples

```
Gem: bundler has 1480241823 downloads.
/lib/bundler/vendor/net-http-persistent/lib/net/http/persistent.rb

  ensure
    return sleep_time unless $!

Gem: rubygems-update has 817517436 downloads.
/lib/rubygems/core_ext/kernel_require.rb

  ensure
    if RUBYGEMS_ACTIVATION_MONITOR.respond_to?(:mon_owned?)
      if monitor_owned != (ow = RUBYGEMS_ACTIVATION_MONITOR.mon_owned?)
        STDERR.puts [$$, Thread.current, $!, $!.backtrace].inspect if $!

Gem: launchy has 196850833 downloads.
/lib/launchy.rb

    ensure
      if $! and block_given? then
        yield $!

Gem: spring has 177806450 downloads.
/lib/spring/application.rb

          ensure
            if $!
              lib = File.expand_path("..", __FILE__)
              $!.backtrace.reject! { |line| line.start_with?(lib) }

Gem: net-http-persistent has 108254000 downloads.
/lib/net/http/persistent.rb

  ensure
    return sleep_time unless $!

Gem: open4 has 86007490 downloads.
/lib/open4.rb

            ensure
              killall rescue nil if $!

Gem: net-ssh-gateway has 74452292 downloads.
/lib/net/ssh/gateway.rb

    ensure
      close(local_port) if block || $!

Gem: vault has 48165849 downloads.
/lib/vault/persistent.rb

  ensure
    return sleep_time unless $!

Gem: webrick has 46884726 downloads.
/lib/webrick/httpauth/htgroup.rb

        ensure
          tmp.close
          if $!

Gem: stripe-ruby-mock has 11533268 downloads.
/lib/stripe_mock/api/server.rb

        ensure
          raise e if $! != e

Gem: logstash-input-udp has 8223308 downloads.
/lib/logstash/inputs/udp.rb

  ensure
    if @udp
      @udp.close_read rescue ignore_close_and_log($!)
      @udp.close_write rescue ignore_close_and_log($!)

Gem: shrine has 6332950 downloads.
/lib/shrine/uploaded_file.rb

      ensure
        tempfile.close! if ($! || block_given?) && tempfile

```

Discussion:
* nobu: `$!` is thread-local.
* ko1: Your proposal is to make shorthand for the following code?
  * mame: No

```ruby=
begin
 ...
ensure
  error = $!
  ...
end
```

* knu: different example
```ruby=
begin
  Integer("foo")
rescue
  begin
    Integer("1")
  ensure => error
    # You can't tell `$!` came from this begin block without being rescued.
    p $!  #=> #<ArgumentError: invalid value for Integer(): "foo">
    p error #=> nil
  end
end
```

* ko1: FYI

```ruby=
def foo
  p $! #=> #<RuntimeError: FOO>
  raise SyntaxError
rescue SyntaxError
end

begin
  raise 'FOO'
ensure
  foo; p $! #=> #<RuntimeError: FOO>
  exit
end
```

* mame: It is surprising to me that `$!` is not a method-local but a thread-local variable

```ruby=
at_exit do # |exception| ???
  if $! # exception
    puts "Exiting because of error"
  end
end
```

```ruby=
begin
  raise "Boom"
ensure
  begin
  ensure => error # should be nil - yes correct. This is the difference between ko1's workaround and samuel's proposal
    p $! #=> #<RuntimeError: Boom>
    error = $!
    puts "failed" if $!
  end
end
```


You can almost achieve the ideal situation with this:

``` ruby
begin
  ...
rescue Exception => error # RuboCop will complain.
  raise
ensure
  pp "error occurred" if error
end
```

* matz: I am negative. 

### [[Bug #18077]](https://bugs.ruby-lang.org/issues/18077) `Marshal.dump(closed_io)` raises `IOError` instead of `TypeError` (nobu)

* `Marshal.dump` is expected to raise a `TypeError` for unmarshallable objects. But closed streams raise an `IOError`
* mame: eregon seems reasonable. How about returning the stored encoding even after the io is closed
* matz: Sounds good

Conclusion:

* mame: replied

### [[Feature #12075]](https://bugs.ruby-lang.org/issues/12075) some container#nonempty? (knu)

* knu: What about `#size?`?
  * There is already `File.stat(file).size?` / `FileTest.size?(file)`.
  * It has a positive nuance compared to `nonempty?`.
  * It is a replacement for `array.size > 0`.
  * It can be part of the Enumerator protocol nicely along with `#size`. (Works like `array.size.nonzero?`)

```ruby=
ary = []
ary.size? #=> false

ary = [1]
ary.size? #=> true
```

Motivation example:

```ruby=
if !ary.empty? # We don't want to use a negation as a condition for a normal case!
  # normal case
else
  raise # exceptional case
end

if ary.nonempty? # looks still double negation!
  # normal case
else
  raise # exceptional case
end

if ary.size?
  # normal case
else
  raise # exceptional case
end
```

Potential counter-proposal:

* `present?`: Rails uses it for different meaning (Object#present?)
    * samuel: I saw many Rails application where every `if` statement has `#present?` or something similar, it's very ugly.
    * knu: Agreed.  It was born before we introduce the `&.` operator.  We could encourage the new style `&.size?` when this were added.
* `has_size?`
    * matz: It's against our naming rule that `is_` or `has_` should be removed.
* `any?`: `[false, nil].any? #=> false` / `[false, nil].size? #=> true`
* `some?`: Too similar to `any?`
* `filled?` / `occupied?`: An array can contain multiple elements. Only one element is enough to be described as filled?
    * Array has fill().

Difference from FileTest.size?

* `FileTest.size?` returns nil or an Integer
* `Array#size?` should return true or false (or nil if unknown)
* No seen use case for "nil or an Integer"

Discussion:

* matz: I don't like the name `nonempty?`.
* samuel: Why not `unless ary.empty?`?
  * mame: I don't want to see `unless ... else ... end`!
  * samuel: I'm okay with that, but I understand your point.
* More and more people seem to have been convinced by the name `size?` after discussion on Slack and Discord. 😎
* Let us sleep on it.

```
pp Symbol.all_symbols.map{|e| e.to_s.sub(/\?$/, '')}.tally.sort_by{_2}

 ["has_rdoc", 2],
 ["default_gem", 2],
 ["prerelease", 2],
 ["valid", 2],
 ["runtime", 2],
 ["correct", 2],
 ["activated", 2],
 ["blocking", 2],
 ["stop", 2],
 ["autoload", 2],
 ["utc", 2],
 ["fnmatch", 2],
 ["absolute_path", 2],
 ["setgid", 2],
 ["setuid", 2],
 ["symlink", 2],
 ["executable", 2],
 ["directory", 2],
 ["file", 2],
 ["autoclose", 2],
 ["eof", 2],
 ["pipe", 2],
 ["exclude_end", 2],
 ["binmode", 2],
 ["incomplete_input", 2],
 ["ruby2_keywords_hash", 2],
 ["compare_by_identity", 2],
 ["real", 2],
 ["value", 2],
 ["key", 2],
 ["match", 2],
 ["casecmp", 2],
 ["none", 2],
 ["all", 2],
 ["compatible", 2],
 ["include", 2],
 ["singleton_class", 2],
 ["empty", 2],
 ["lambda", 2],
 ["size", 2],
 ["nil", 2],
 ["", 2],
 ["$", 2],
 ["_deprecated_has_rdoc", 2]]
```

```ruby
(0.1..0.2).size #=> 1
("1".."3").size #=> nil
```

https://github.com/wested/webget_ruby_ramp/blob/091cefc57745a27679bfe94b9c758841516e042e/lib/webget_ruby_ramp/array.rb#L53-L57
```ruby
  # @return [Boolean] true if size > 0
  #
  # @example
  #   [1,2,3].size? => true
  #   [].size? => false

  def size?
    return size>0
  end
```

## Additional topics

* TracePoint
    * constant access: [Feature #18047: TracePoint: Add event type for constant access](https://bugs.ruby-lang.org/issues/18047)
        * `:constant_access` event
        * `TracePoint#constant_value`
    * `method_added`
        * Same timing as `Module#method_added`
    * `instance_variable_write`
        * Before assignement
    * `class_variable_write`
* `ISeq.keep_script_lines = true/false`
    * After this option, compiled ISeqs have a pointer to the script lines.
    * `RubyVM::AST.parse(src, save_script_lines: true)`
    * Change the keyword to `keep_*`
* Can we delete a warning "extra states are no longer copied"?

```
/home/chkbuild/chkbuild/tmp/build/20210817T063004Z/ruby/test/ruby/test_hash.rb:2052: warning: extra states are no longer copied: {1=>2, 3=>4, 5=>6, "str"=>1, "str"=>2}
/home/chkbuild/chkbuild/tmp/build/20210817T063004Z/ruby/test/ruby/test_hash.rb:2052: warning: extra states are no longer copied: {1=>2, 3=>4, 5=>6, "str"=>1, "str"=>2}
/home/chkbuild/chkbuild/tmp/build/20210817T063004Z/ruby/test/ruby/test_hash.rb:2052: warning: extra states are no longer copied: {}
/home/chkbuild/chkbuild/tmp/build/20210817T063004Z/ruby/test/ruby/test_hash.rb:2052: warning: extra states are no longer copied: {}
```
https://ruby-trunk-changes.hatenablog.com/entry/20131216/ruby_trunk_changes_44225_44250

```ruby
class SubHash < Hash
end

SubHash.new.dup.class #=> SubHash in Ruby 1.9
SubHash.new.dup.class #=> Hash    in Ruby 2.0 or later
```