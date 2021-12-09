---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20211209Japan

https://bugs.ruby-lang.org/issues/18346

## DateTime and location

* 2021/12/09 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/01/13 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* preview 2?
    * no concrete plan yet.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #18364]](https://bugs.ruby-lang.org/issues/18364) Add `GC.stat_pool` for Variable Width Allocation (peterzhu2118)

- This feature will allow accessing stats for memory pools through Ruby. This will be especially useful for the Variable Width Allocation feature.
- Should we extend `GC.stat` to return stats for memory pools or create a new method such as `GC.stat_pool`?
- Is `GC.stat_pool` a good name?

Discussion:

* ko1: `GC.stat` returns a flat hash (Symbol to Numeric map) now.  This proposal changes it so that the method returns a nested hash.

```ruby
# current
pp GC.stat
#=> {:count=>22,
#    :heap_allocated_pages=>146,
#    :heap_sorted_length=>146,
#    ...
#   }

pp GC.stat_pool(0) # statistics of heap pool whose unit size is 0
#=> {:heap_allocated_pages=>146,
#    :heap_sorted_length=>146,
#    ...
#   }

# alternative API
pp GC.stat
#=> {:count=>21,
#    :heap_allocated_pages=>99,
#    ...
#    :size_pools=>
#     [
#      {:slot_size=>40,
#       :heap_allocatable_pages=>4,
#       :heap_eden_pages=>40,
#       :heap_eden_slots=>1234,
#       :heap_tomb_pages=>0,
#       :heap_tomb_slots=>0},
#      {:slot_size=>80,
#       :heap_allocatable_pages=>80,
#       :heap_eden_pages=>14,
#       :heap_eden_slots=>123,
#       :heap_tomb_pages=>0,
#       :heap_tomb_slots=>0}
#      ...
#     ],
#     ...
#   }
```

* matz: Is stat pool specific to MRI?
* ko1: Yes.
* matz: Is there any portability concern then?
* ko1: This method already returns drastically different things among versions.  There should be no problem.
* ko1: Also jruby already extends this method so that it returns more than just a number.
* matz: If so I guess we can follow jruby?
* ko1: That breaks our CAPI (which expects `size_t`s as values).
* matz: Hmm.
* mame: I wonder what "pool" means in this `:stat_pool` key.
* matz: There is only one "heap" in a ruby object space; it is split into two "generation"s; and each generation are split into several "page"s.
* aaron: Peter made multiple pools which are sized.
* matz: Maybe we should just have better terminologies.
* aaron: size groups maybe?
* matz: Or "arena".
* aaron: `size_heap` makes sense to me because the data sturcture is the same.


```
ko1@aluminium:~$ gem-codesearch 'rb_gc_stat\('
/srv/gems/airbnb-ruby-prof-0.0.1/ext/ruby_prof/rp_measure_allocations.c:size_t rb_gc_stat(VALUE key);
/srv/gems/airbnb-ruby-prof-0.0.1/ext/ruby_prof/rp_measure_allocations.c:    return rb_gc_stat(total_alloc_symbol);
/srv/gems/gc_tracer-1.5.1/ext/gc_tracer/gc_logging.c:   out_sizet(logging->out, rb_gc_stat(sym));
/srv/gems/gc_tracer-1.5.1/ext/gc_tracer/gc_logging.c:    rb_gc_stat(ID2SYM(rb_intern("count")));
/srv/gems/gctools-0.2.4/ext/gcprof/gcprof.c:  rb_gc_stat(obj);
/srv/gems/gctools-0.2.4/ext/gcprof/gcprof.c:  rb_gc_stat(_gcprof.start);
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:      _oobgc.start.total_allocated_objects = rb_gc_stat(sym_total_allocated_objects);
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:      _oobgc.start.heap_tomb_pages = rb_gc_stat(sym_heap_tomb_pages);
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:        rb_gc_stat(sym_heap_swept_slots) +
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:        (rb_gc_stat(sym_heap_tomb_pages) * _oobgc.heap_obj_limit) -
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:        rb_gc_stat(sym_heap_final_slots);
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:  size_t curr = rb_gc_stat(sym_total_allocated_objects);
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:    if ((rb_gc_stat(sym_old_objects) >= rb_gc_stat(sym_old_objects_limit)*0.97 ||
/srv/gems/gctools-0.2.4/ext/oobgc/oobgc.c:        rb_gc_stat(sym_remembered_wb_unprotected_objects) >= rb_gc_stat(sym_remembered_wb_unprotected_objects_limit)*0.97) &&
/srv/gems/heapfrag-0.1.0/ext/heapfrag/heapfrag.c:  size_t total_pages = rb_gc_stat(sym_heap_allocated_pages);
/srv/gems/heapfrag-0.1.0/ext/heapfrag/heapfrag.h:size_t rb_gc_stat(VALUE hash_or_sym);
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/extensions/x86_64-linux/2.3.0-static/ruby-prof-0.15.9/gem_make.out:checking for rb_gc_stat()... yes
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/extensions/x86_64-linux/2.3.0-static/ruby-prof-0.15.9/mkmf.log:have_func: checking for rb_gc_stat()... -------------------- yes
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/ruby-prof-0.15.9/ext/ruby_prof/rp_measure_allocations.c:size_t rb_gc_stat(VALUE key);
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/ruby-prof-0.15.9/ext/ruby_prof/rp_measure_allocations.c:    return rb_gc_stat(total_alloc_symbol);
/srv/gems/journalist-0.0.3/ext/journalist/garbage_collection.c:  rb_gc_stat(cookie.gc);
/srv/gems/journalist-0.0.3/ext/journalist/garbage_collection.c:  rb_gc_stat(cookie.gc);
/srv/gems/rblineprof-0.3.7/ext/rblineprof.c:    .allocated_objects = rb_gc_stat(sym_total_allocated_objects)
/srv/gems/rhodes-7.4.1/platform/shared/ruby/gc.c:rb_gc_stat(VALUE key)
/srv/gems/rhodes-7.4.1/platform/shared/ruby/include/ruby/intern.h:size_t rb_gc_stat(VALUE);
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/doc/NEWS-2.1.0:* rb_gc_stat() added. This allows access to specific GC.stat() values from C
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/gc.c:rb_gc_stat(VALUE key)
/srv/gems/ruby-prof-1.4.3/ext/ruby_prof/rp_measure_allocations.c:    return (double)rb_gc_stat(total_allocated_objects_key);
/srv/gems/tunemygc-1.0.71/ext/tunemygc/extconf.rb:        f.puts "    record->#{k} = rb_gc_stat(sym_gc_stat[#{i}]);"
```

Conclusion:

* matz: Let's return nested data structure for Ruby.
* matz: We can discuss about the key for this new value set.
* ko1: Maybe we can also refine our CAPI.

----
* ko1: What should be returned when VWA is off?
* aaron: It doesn't change the output.
* ko1: Make sure 3.1 without VWA outputs the same thing as 3.0.



### [[Feature #18349]](https://bugs.ruby-lang.org/issues/18349) Let --jit enable YJIT on supported platforms (k0kubun)

* Any concerns? Can we do this from Ruby 3.1?

Discussion:

* shyouhei: Is `--jit` still experimental?
* matz: I don't remember I declared it is mature.
* matz: I have no reason to stop this proposal, given this is requested by @k0kubun.
* n0kada: What happens when there is no YJIT for the platform?
* naruse: I think it's better to kill JIT then.
* matz: Users can be explicit by passing `--mjit`/`--yjit` if they really need specific one.
* naruse: Understood.
* hsbt: `--yjit` will be kept?
* matz: yes

Conclusion:

* matz: Leave it to @k0kubun.
* naruse: okay for Ruby 3.1


### [[Feature #18351]](https://bugs.ruby-lang.org/issues/18351) Support anonymous rest and keyword rest argument forwarding (jeremyevans0)

* I think this is a natural addition after the addition of anonymous block forwarding in #11256.
* Is it OK to support this syntax? If so, is the patch acceptable?

Preliminary discussion:

```ruby=
def foo(*)
  bar(*)
end

def baz(**)
  quux(**)
end
```

* mame: I am not sure if this syntactic complexity is worth or not
* mame: I am keen to remove anonymous block forwarding :-)
* ko1: Is this so useful than `...` forwarding?

Discussion:

* matz: (Reading the pull request...)
* matz: I think it's possible.
* ko1: For 3.1?
* matz: That's the point.  It's already December and it's too late for 3.1.
* knu: Maybe you want to write `ary = [*]` but it seems it is not covered in this implementation. (cf. Lua's `...`)
* matz: That seems confusing.
* nobu: You could use Array.new for that.
* knu: But you can pass `*` to `[]`/`[]=` because they are method calls, right?

```ruby=
def foo(*)
  [*]
end
p foo(1, 2, 3)
```

```ruby=
def foo(*)
  p(*, *)
end

foo(1, 2) #=> 1, 2, 1, 2

def bar(*, **)
  foo(*, **)
  foo(*, *, **)
end
```

```ruby=
def foo *
  bar
end
#=>
# 3.0 and earlier
# and after this proposal
def foo(*bar)
end
```

```ruby=
def foo(*)
  bar *
  baz
end
#=>
def foo(*)
  bar * baz
end
```

Conclusion:

* matz: Interesting.  Accepted for 3.2.  Wait for a while.


### [[Feature #16663]](https://bugs.ruby-lang.org/issues/16663) Add block or filtered forms of `Kernel#caller` to allow early bail-out (jeremyevans0)

* Two possible method names are `find_caller` and `each_caller`.
* `find_caller` optimizes for the case where the first matching frame will be returned.
* `each_caller` is more general, always iterating unless there is a non-local exit (break/return).
* Are either of these names acceptable?  If so, which method name should be used?

Discussion:

* mame: New name candidates are propsed.
* ko1: Can we add new method into `Kernel` for this purpose? (I'm not sure it's so important). I prefer to extend `caller` if possible.

```ruby=
# Kernel methods
find_caller do |frame|
  frame.path =~ /.../
end #=> frame or nil

each_caller do |frame|
  break frame if frame.path =~ /.../
end #=> frame or ???

each_caller do |frame|
  frame.path =~ /.../
end #=> void

callers.each do |frame|
  # ...
end
```

```ruby=
# Thread class methods
Thread::Backtrace.each_caller do |frame|
end

Thread.each_caller do |frame|
end

Thread.callers.each do |frame|
end
```

```ruby=
# Thread instance methods
Thread.current.each_caller do |frame|
end
other_thread.each_caller do |frame| # may read frames from the other thread
end
```

* knu: `each_caller` is confusing to me becasue what is returned from the method is not that obvious.  What you need may be `callers` that returns an enumerable object.
* nobu: Maybe we can add this to `Thread::Backtrace` instead of `Kernel`.
* matz: `each`_something is not a good name because `Enumerable#each`'s block is not a filter. `find`_something sounds like it stops after first truthy block evaluation.
* akr: What the OP wants is actually the `each` semantics, though.
* ko1: how about keyword parameter like:
```ruby=
caller(filter: -> {|path| /pat/ =~ path})
```

* matz: I don't think it's a wise idea for a method to return different types depending on its keyword arguments.

Conclusion:

* matz: No conclusion now.


### [[Feature #12084]](https://bugs.ruby-lang.org/issues/12084) `Class#instance` (jeremyevans0)

* This should be simple to implement, since a singleton class keeps a reference to the instance.
* Are we OK adding this feature? If so, is the method name acceptable, or do we want `singleton_instance`?
* If called on a non-singleton class, should `TypeError` be raised?
* For `TrueClass`, `FalseClass`, and `NilClass`, should it return `true`, `false`, and `nil`?

Discussion:

```ruby=
Array.singleton_class.instance # => Array
"foo".singleton_class.instance # => "foo"
```

* ko1: Forget about edge cases first.
    * Do we want this?
    * Name?
* ko1: This is easy to do in C but fairly difficult to do in Ruby currently. One need to scan the entire `ObjectSpace`.
* mame: There seems no practical use-case...

Conclusion:

* matz: I don't think it is needed.


### [[Bug #14891]](https://bugs.ruby-lang.org/issues/14891) `Pathname#join` has different behaviour to `File.join` (jeremyevans0)

* I agree with @znz that this behavior is expected.
* Can this be closed?

Preliminary discussion:

* nobu: Go ahead

Discussion:

* mame: What do you mean by "go ahead", nobu?

Conclusion:

* nobu: Already closed
* akr: Maybe we can improve documents.


### [[Bug #14434]](https://bugs.ruby-lang.org/issues/14434) `IO#reopen` fails after `EPIPE` (jeremyevans0)

* I'm not sure whether this behavior is a bug.
* The behavior has existed since Ruby 1.9 (no failure in 1.8.7).
* Can we decide whether this behavior is expected or a bug?

Discussion:

* akr: So the IO is failing to flush.
* mame: The patch tries to ignore errors.
* nobu: ... But the patch breaks our CI.

Conclusion:

* nobu: I think it is a bug but maybe we cannot fix it soon, by Ruby 3.1


### [[Feature #18370]](https://bugs.ruby-lang.org/issues/18370) Call `Exception#full_message` to print exceptions reaching the top-level (eregon)

* Improve consistency in how exceptions are shown, and makes it easier to evolve exception formatting.
* OK to do?

Preliminary discussion:

* mame: I am not sure if a consistency is a good enough reason to change this, but I'm okay if matz is okay

Discussion:

* mame: This request is unrelated to `did_you_mean`/`error_highlight` according to eregon.  The OP wants consistency.
* matz: We need a clear benefit for this.
* akr: I think this is a bug of full_message, it should escape the message too (if the escaping behavior is really needed)

Conclusion:

* matz: Show us clear benefits of this, so that I can :+1: to this idea.


### [[Feature #18367]](https://bugs.ruby-lang.org/issues/18367) Stop the interpreter from escaping error messages (mame)

* mame: Strongly related to the previous topic
* mame: TBH I'm a bit afraid if `raise "\0"` can show NUL character to the terminal

Discussion:

* akr: Do we ignore the reason we introduced the escapes?
* mame: Other languages consider this as application matters.
* shyouhei: That means they insist for instance `docker(1)` should escape its containers' outputs?
* mame: I guess the "application" here means their scripts.
* naruse: That is harsh.  That forces everyone to properly escape their `puts` arguments, which is not a practical requirement.
* akr: Yes.. This request is moving the accountability demarcation point.

```bash
$ ruby -e '"\\".no_method'
-e:1:in `<main>': undefined method `no_method' for "\\\\":String (NoMethodError)

"\\".no_method
   ^^^^^^^^^^
```

* naruse: Why do we have to escape backslashes at the first place?
* mame: If the escape is removed, `raise "\\x00"` and `raise "\x00"` would be undistinguishable, nobu said

```ruby
raise "\\x00" #=> test.rb:1:in `<main>': \x00
raise "\x00"  #=> test.rb:1:in `<main>': \x00
              #                          ^^^^ highlighted by ANSI escape sequence

raise "\e[31mRed\e[0m"
```

* akr: Are recent terminals vulnerable? If not, the escaping behavior can be removed
  * xterm WindowsTerminal iterm2 libvte mlterm attri
* matz: My gut feeling is we might not need to escape.

Conclusion:

* No change for 3.1, either way.
* Research recent terminal emulators in 3.2.

#### Slightly-related but off topic

* `SyntaxError` contains many info often.

```=
$ ruby -e 'eval("def foo(nil = 1)")'
Traceback (most recent call last):
        1: from -e:1:in `<main>'
-e:1:in `eval': (eval):1: syntax error, unexpected `nil', expecting ')' (SyntaxError)
def foo(nil = 1)
        ^~~
(eval):1: Can't assign to nil
def foo(nil = 1)
        ^~~
(eval):1: syntax error, unexpected ')', expecting end-of-input
def foo(nil = 1)
               ^

$ ruby -e 'begin; eval("def foo(nil = 1)"); rescue SyntaxError; p $!.message; end'
"(eval):1: syntax error, unexpected `nil', expecting ')'\ndef foo(nil = 1)\n        ^~~\n(eval):1: Can't assign to nil\ndef foo(nil = 1)\n        ^~~\n(eval):1: syntax error, unexpected ')', expecting end-of-input\ndef foo(nil = 1)\n               ^\n"
```

* `SyntaxError#message`                             #=> "syntax error"
* `SyntaxError#additional_message(highlight: true)` #=> "(eval):1:..."

```=
-e:1:in `eval': syntax error (SyntaxError)     # #message part
| (eval):1:...                                 # #additional_message part
```

### [[Feature #17881]](https://bugs.ruby-lang.org/issues/17881) Add a `Module#const_added` callback (byroot)

- Would allow the Zeitwerk autoloader to no longer use tracepoint.
- The main problem with Zeitwerk using tracepoint is that since tracepoint callbacks are not "reentrant" Zeitwerk is partially broken when using a debugger, e.g. https://github.com/ruby/debug/issues/408
- This cause lots of confusion for users.
- Could alternatively be `Module#module_defined`, to only be called for `Module/Class`

Preliminary discussion:

* ko1: `const_added` can be overwritten by some gems. Is it really okay for Zeitwerk usecase? I will ask if the proposed API is really useful for Zeitwerk use case

Discussion:

* ko1: If we had this hook Zeitwerk can get rid of trace points.
* mame: Why we want to avoid trace point is debugger also uses it.
* ko1: ... And JIT breaks for trace pont.
* ko1: Basically trace point shall be used for traces.  Zeitwerk's usage is kind of abuse.
* akr: This can be used to autoload `::A::B::C`, in case `::A::B` is not loaded yet.
* ko1: If Zeitwerk want to use it every one of modules must interface well with its `const_defined` hook.
* akr: What has been done for ther hooks?
* ko1: Maybe nobody cared?
* nobu: I can try extending `autoload` to support nested ones, but not in time frame of 3.1.

Conclusion:

* ko1: Let's postpone it for 3.2
* matz: Agreed. I accept the feature.

### [[Feature #15912]](https://bugs.ruby-lang.org/issues/15912) Allow some reentrancy during TracePoint event (byroot)

* Alternative to `Module#const_added` for fixing the Zeitwerk and debuggers combination.
* @ko1 proposed `TracePoint#reopen(&block)` so that debuggers can yield to the user with that.
* Potentially with a list of reopened events: `tp.reopen(:class) { "do stuff" }`

Preliminary discussion:

* ko1: `TracePoint.allow_reentrance do ... end`?

Discussion:

* ko1: Trace points do not nest.  But debuggers can evaluate in middle of Zeitwerk load, which doesn't work.
* ko1: Also breaks byebug.
* ko1: The proposal explicitly allows a region of trace point call backs to fire other trace points.
* ko1: Basically this is a dangerous operation (can e.g. loops indefinitely).  This one is very explicit.
* matz: For instance signal handlers can register itself for reentrancy.
* akr: Doubt.  Current POSIX manipulates signal masks for that purpose.
* ko1: Unlike signal mask (which is per signal no), trace points blocks/allows everything at once.

Conclusion:

* matz: One of `TracePoint.allow_reentran{t,ce,cy}` accepted.


### [[Feature #12737]](https://bugs.ruby-lang.org/issues/12737) `Module#defined_refinements` (shugo)

* Is the name Module#refinements acceptable?
    * It's consistent with `Module#constants`.
    * Other candidates: `configured_refinements`, `contained_refinements`, `defined_refinements`
* Is it OK to return an Array instead of Hash?
    * The target class of a refinement can be obtained by another new method `Refinement#refined_class`.
* I'd like to add Module#refinements and `Refinement#refined_class` after the release of Ruby 3.1.

Discussion:

* mame: The OP comes to a new name.

```ruby
module M
  refine String do
    $M_String = self
  end

  refine Integer do
    $M_Integer = self
  end
end

p M.refinements #=> [$M_String, $M_Integer]
p M.refinements[0].refined_class #=> String
p M.refinements[1].refined_class #=> Integer
```

* matz: Got it.

Conclusion:

* matz: `Refinement#refined_class` accepted.
* matz: `Module#refinements` accepted.
* shyouhei: Wait for 3.2.


### [[Feature #14332]](https://bugs.ruby-lang.org/issues/14332) `Module.used_refinements` to list refinement modules (shugo)

* eregon suggested to add it soon, but It may be too late to add it in Ruby 3.1.

Discussion:

* mame: What was the previous status of this issue?

Conclusion:

* matz: Wait for 3.2.


### [[Feature #16038]](https://bugs.ruby-lang.org/issues/16038) Provide a public `WeakMap` that compares by equality rather than by identity (eregon)

* This is necessary to have access to a weak-keys map on TruffleRuby/JRuby. There are many use cases:, notably [this PR in Rails](https://github.com/rails/rails/pull/43723) and all the reasons WeakHashMap is used in Java code.

Preliminary discussion:

* mame: I'm unsure what is proposed. Moving `ObjectSpace::WeakMap` to `::WeakMap` and adding `WeakMap#compare_by_equality`? Or introducing `ObjectSpace::WeakKeysMap`?
* mame: I think I got it
  * JVM cannot implement `WeakMap`
  * So if it is used in Rails, JRuby/TruffuleRuby cannot support it well
  * eregon wants WeakKeysMap or WeakValuesMap because:
    * it is enough for Rails use case
    * it is possible to implement on JVM
* mame: I feel it is a different story from the original issue

```ruby=
# use case: object deduplication
class Foo
  # @@cache = ObjectSpace::WeakKeysMap.new
  @@cache = ObjectSpace::WeakValuesMap.new
  @@cache.compare_by_equality

  def self.new(*args)
    @@cache[args] ||= super
  end
end

foo1 = Foo.new(1, 2, 3)
GC.start
foo2 = Foo.new(1, 2, 3)

# compare_by_equality is needed
p foo1.equal?(foo2) # satisfied by WeakValuesMap
                    # not necessarily satisfied by WeakKeysMap
```

* mame: I don't know this use case is the same as Rails use case
* mame: What's happen if we do `hash[obj] = obj` when `hash` is Java's WeakHashMap? Is `obj` collected?

Discussion:

* mame: Original proposal was to use WeakMap as object deduplication.  In order to do so one have to use compare_by_identity now.  The OP wanted to use equality method.
* mame: @matz understood the motivation but was not sure if WeakMap was the right solution.
* mame: Besides JVM languages have difficulties implementing their own WeakMap.
* matz: Yes because a weak map in general has to interact with underlying GC; which is what they cannot.
* mame: According to @eregon there is no weak-key weak-value map for JVM; there are only weak-key _or_ weak-value map.
* akr: It sounds what is needed is a weak set.  A weak key map should suffice.
* mame: That works only when the registered keys themselves can be queried.
* ko1: It seems Java's WeakHashMap lacks that feature.

Conclusion:

* matz: Let me summarize and have a feedback.


### [[Feature #18033]](https://bugs.ruby-lang.org/issues/18033) `Time.new` or `Time.at` with string (nobu)

* `Time.new("2021-12-09")` or `Time.at("2021-12-09")`.
* Other new methods: ~~`Time.try_convert`~~ or `Time.iso`.

Preliminary discussion:

```ruby=
[master]$ ruby -ve 'p Time.new("2021-12-09")'
ruby 3.0.2p107 (2021-07-07 revision 0db68f0233) [x86_64-linux]
2021-01-01 00:00:00 +0900

[master]$ ruby -ve 'p Time.new("2021-12-09")'
ruby 3.1.0dev (2021-11-26T02:39:59Z master 7f7c3a0a75) [x86_64-linux]
<internal:timev>:310:in `initialize': invalid value for Integer(): "2021-12-09" (ArgumentError)

[master]$ ruby -ve 'p Time.new("2021-12-09")'
ruby 3.X.0 (nobu's patch)
2021-12-09 00:00:00 +0900
```

```bash
ko1@aluminium:~$ gem-codesearch '\bTime\.new\(\"'
/srv/gems/cosmos-4.5.1/lib/cosmos/tools/cmd_tlm_server/limits_groups_background_task.rb:    FUTURE_TIME = Time.new("3000")
/srv/gems/cosmos-4.5.1/lib/cosmos/tools/cmd_tlm_server/limits_groups_background_task.rb:    PAST_TIME = Time.new("1900")
/srv/gems/feldtruby-0.4.12/lib/feldtruby/optimize/stdout_logger.rb:                     @last_report_time = Hash.new(Time.new("1970-01-01")) # Maps event strings to the last time they were reported on, used by anote.
/srv/gems/harvestime-0.0.3/lib/harvestime/invoice.rb:      Time.new("").to_date
/srv/gems/harvestime-0.0.3/spec/harvestime/invoice_spec.rb:        expectation = Time.new("").to_date
/srv/gems/logstash-input-multi-rds-0.0.1/lib/logstash/inputs/multirds.rb:          return Time.new("1999-01-01")
/srv/gems/logstash-input-rds-0.17.0/lib/logstash/inputs/rds.rb:          return Time.new("1999-01-01")
/srv/gems/marvell-0.1.1/spec/spec_helper.rb:    Time.new("2014-02-09 20:24:26 -0600")
/srv/gems/puppet-7.12.1/spec/unit/network/formats_spec.rb:        tm = Time.new("2016-01-27T19:30:00")
/srv/gems/roadforest-0.7/spec/rdfa-handler.rb:          @graph << [EX.a, EX.b, RDF::Literal::Time.new("12:34:56")]
```

```bash
ko1@aluminium:~$ gem-codesearch "\bTime\.new\(\'"
/srv/gems/digi_moji-0.0.3/lib/digi_moji/cli.rb:      t = Time.new('00:00:00') + time_in_sec(sec, unit)
/srv/gems/distribution-api-client-0.1.0/spec/card_url_spec.rb:    t = Time.new('2015-10-30T21:00:00+001')
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time1 = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time2 = Time.new('2014-01-02T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-0.1.0/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time1 = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time2 = Time.new('2014-01-02T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/fluent-plugin-mixpanel-enchanced-0.0.13/test/plugin/test_out_mixpanel.rb:    time = Time.new('2014-01-01T01:23:45+00:00').to_i
/srv/gems/frodata-0.9.3/spec/frodata/properties/time_spec.rb:  let(:subject) { FrOData::Properties::Time.new('Timely', '21:00:00-08:00') }
/srv/gems/frodo-0.12.8/spec/frodo/properties/time_spec.rb:  let(:subject) { Frodo::Properties::Time.new('Timely', '21:00:00-08:00') }
/srv/gems/grooveshark-0.2.14/spec/grooveshark/user_spec.rb:    expect(user.feed(Time.new('20141122')))
/srv/gems/logstash-output-s3-leprechaun-fork-1.0.15/spec/outputs/s3_spec.rb:      allow(Time).to receive(:now) { Time.new('2015-10-09-09:00') }
/srv/gems/looky-lu-0.0.5/spec/generators/state_generator_spec.rb:    time = Time.new('2013-05-10 19:58:31 +0000')
/srv/gems/odata-0.6.18/spec/odata/properties/time_spec.rb:  let(:subject) { OData::Properties::Time.new('Timely', '21:00:00-08:00') }
/srv/gems/odata4-0.9.1/spec/odata4/properties/time_spec.rb:  let(:subject) { OData4::Properties::Time.new('Timely', '21:00:00-08:00') }
/srv/gems/picky-4.31.3/spec/lib/loggers/verbose_spec.rb:        Time.stub :now => Time.new('zeros')
/srv/gems/picky-4.31.3/spec/lib/loggers/verbose_spec.rb:        Time.stub :now => Time.new('zeros')
/srv/gems/puppet-7.12.1/spec/unit/util/storage_spec.rb:          { :checked => Time.new('2018-08-08 15:28:25.546999000 -07:00') }
/srv/gems/ruby-cldr-0.5.0/README.textile:       format   = Cldr::Format::Time.new('HH:mm:ss z', calendar)
/srv/gems/ruby-cldr-0.5.0/test/format/datetime_test.rb:    time   = Cldr::Format::Time.new('HH:mm', @calendar)
/srv/gems/ruby-nmap-0.10.0/spec/scanner_spec.rb:    let(:start_time) { Time.new('Sat Jul 20 23:55:27 2013') }
/srv/gems/rubycom-0.4.3/test/rubycom/arg_parse_test.rb:    time = Time.new('2013-05-08 00:00:00 -0500')
/srv/gems/saper-0.5.3/spec/items/time_spec.rb:      expect { Saper::Items::Time.new('') }.to raise_error Saper::InvalidItem
/srv/gems/scraperd-0.0.3/test/activity_test.rb:              :pubDate => Time.new('2015-03-14 19:45:50 +0100'),
/srv/gems/scraperd-0.0.3/test/activity_test.rb:    assert_equal activity.added_at, Time.new('2015-03-14 19:45:50 +0100')
/srv/gems/scrapperd-0.0.1/test/activity_test.rb:              :pubDate => Time.new('2015-03-14 19:45:50 +0100'),
/srv/gems/scrapperd-0.0.1/test/activity_test.rb:    assert_equal activity.added_at, Time.new('2015-03-14 19:45:50 +0100')
/srv/gems/wavefront-client-3.6.2/spec/wavefront/cli/batch_write_spec.rb:    expect(k.valid_timestamp?(Time.new('1999-12-31').to_i)).to be false
/srv/gems/zentool-0.8.0/lib/zentool/zendesk_ticket.rb:    @start_time = Time.new('2016-01-01').to_i
```

* Or `Kernel#Time`, `Time#of`?

Discussion:

* nobu: @eregon wants `iso8601` because `Time.new` already takes a string.
* nobu: In fact takig non-integer string is not accepted already. 

```bash
zsh % nocorrect sudo docker run -i --rm rubylang/all-ruby env ALL_RUBY_SINCE=1.9.0 LC_ALL=C.UTF-8 ./all-ruby -e 'p Time.new("2021-12-09")'
ruby-1.9.0-0        -e:1:in `initialize': wrong number of arguments(1 for 0) (ArgumentError)
                        from -e:1:in `new'
                        from -e:1:in `<main>'
                exit 1
...
ruby-1.9.1-p431     -e:1:in `initialize': wrong number of arguments(1 for 0) (ArgumentError)
                        from -e:1:in `new'
                        from -e:1:in `<main>'
                exit 1
ruby-1.9.2-preview1 2021-01-01 00:00:00 +0000
...
ruby-3.0.2          2021-01-01 00:00:00 +0000
ruby-3.1.0-preview1 <internal:timev>:306:in `initialize': invalid value for Integer(): "2021-12-09" (ArgumentError)
                        from -e:1:in `new'
                        from -e:1:in `<main>'
                exit 1
```

* shyouhei: That sounds NG to me.
* ko1: Looking at the code search it seems there are use cases `Time.new` to take a String.  They are mostly tests.
* mame: Should we
  * Revert the behaviour to 3.0?
  * Keep the current 3.1 implementation?
  * Merge nobu's patch?
* akr: I have some code concerns about nobu's patch.  Too many optional time components.
* akr: It seems the current behavior is a bug fix to me.
* knu: I agree.  The month/mday and the time part should not be optional.  Only sub-sec part and maybe timezone part should be optional.  I also agree that the change fro m to_i to Integer() should be considered a bug fix.

Conclusion:

* matz: Not this time.

### [[Feature #18190]](https://bugs.ruby-lang.org/issues/18190) Split `Random::Formatter` from securerandom (nobu)

Preliminary discussion:

* Already [ruby/securerandom#7](https://github.com/ruby/securerandom/pull/7) has been merged.

Conclusion:

* matz: Accepted


### [[Feature #18183]](https://bugs.ruby-lang.org/issues/18183) make `SecureRandom.choose` public (nobu)

* How about `Random::Formatter#random_string` if `choose` is not a good name

```ruby
SecureRandom.choose([*"1".."3"], 5) #=> 12312 or 23131 or something
SecureRandom.choose([*"A".."C"], 5) #=> ABCBA or BCACA or something

SecureRandom.random_string([*"1".."3"], 5) #=> 12312 or 23131 or something
SecureRandom.random_string([*"A".."C"], 5) #=> ABCBA or BCACA or something
```

```ruby=
ALPHANUMERIC = [*'A'..'Z', *'a'..'z', *'0'..'9']
def alphanumeric(n=nil)
  n = 16 if n.nil?
  choose(ALPHANUMERIC, n)
end
```

Conclusion:

* matz: I don't like the name, `random_string`


### [[Feature #18181]](https://bugs.ruby-lang.org/issues/18181#change-95160) Introduce Enumerable#min_with_value, max_with_value, and minmax_with_value (ko1)

```ruby=
%w(abcde fg hijk).lazy.map {|str| [str, str.size] }.min_by {|str, size| size }
#=> ["fg", 2]
```

Conclusion:

* matz: A better name is required


### [[Feature #18397]](https://bugs.ruby-lang.org/issues/18397) Remove documentation that Qfalse == 0 in `extension.rdoc`, instead encourage use of RTEST

Conclusion:

* matz: Accepted as a documentation change
