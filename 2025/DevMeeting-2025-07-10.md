# DevMeeting-2025-07-10

https://bugs.ruby-lang.org/issues/21399

## DateTime and location

* 2025/07/10 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/08/21 (Tue) 13:00-17:00 JST @ Online
    * 8/7 is too close to rubyconftw

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #17473]](https://bugs.ruby-lang.org/issues/17473) Make Pathname to embedded class of Ruby (eregon)

* Related to that issue and discussed there, I made a PR to define most of Pathname in Ruby: https://github.com/ruby/pathname/pull/53
* It's faster, easier to read and maintain and enables sharing most of the Pathname implementation between Ruby implementations.
* Could @akr review it? I wrote him on Slack but got no reply, maybe a committer knows better how to contact him?
* If @akr is not available, please let me know and I will try to find another reviewer.

Preliminary discussion:

* mame: If I recall, pathname was originally written in Ruby. I cannot remember why akr rewrote it in C. Let's ask to akr

Discussion:

* akr: I wrote it in C because I originally planned to use `openat(2)` so that pathnames could be more robust.
* akr: No progress so far, though.
* matz: I didn't like the idea before.
* akr: Maybe it's a good time to hand over the maintenance.
* hsbt: BTW I want pathname be a part of the builtin classes, rather than a separate gem.
* matz: Not a big fan of that idea either, but not strongly against it.
* nobu: `Pathname#sub` in @eregon's patch has incompatibility:
    ```console:status quo
    $ ruby -rpathname -e 'p Pathname("foo").sub(/o/, "x"); p $~'
    #<Pathname:fxo>
    #<MatchData "o">
    ```
* nobu: if Pathname is embeded to the core, I prefer the C implementation to Ruby impl.
* samuel: for the purpose of JIT isn't it better to write more Ruby in Ruby?
* shyouhei: nobody wants to JIT a path manipulation.

Conclusion:

* matz: persuaded.  OK to embed pathname.
* shyouhei: whether we should implement it in C or Ruby is a separate story.
* mame:  We can start from C, then convert each method into rbinc (if needed).
* akr: To use the following four methods, `require "pathname"` is needed for a while
  * find, mkpath, rmtree, mktmpdir
  * rationale: they require other libraries (find gem, fileutils, tmpdir)
  * in future, they (except mktmpdir) should be rewritten in pure C (or rbinc) without dependency to find and fileutils
    * It would be hard to rewrite tmpdir

### [[Bug #19473]](https://bugs.ruby-lang.org/issues/19473) can't be called from trap context (ThreadError) is too limiting (ioquatix)

- Can we change the behaviour? https://github.com/ruby/ruby/pull/13545
- Or should we consider alternatives, e.g. `Mutex#safe_in_trap_context`?

Preliminary discussion:

```ruby
Signal.thread_mode = true
Signal.trap(:INT) { ... }

# ↓

SignalEvents = Queue.new
Thread.new do
  while ev = SignalEvents.pop
    ...
  end
end
Signal.trap(:INT) { SignalEvents << it }
```

Discussion:

- samuel: Maybe better: `Mutex#recursive=true/false` (express intent)
- samuel: I'm against running in a separate thread (by default).
- samuel: There is no way to tell if you are in trap context (in general), so you can't avoid it.

```ruby
# (1) This mutex is never used, except in trap handler. Then by definition it cannot deadlock. But, we prevent it anyway.
mutex = Mutex.new

r, w = IO.pipe
Signal.trap(:INT) do
  w.write("!") # This uses "mutex_synchronize"
end

def normal_user_code
  # (1) Safe -> But we can't do this because of the limitation of Ruby.
  ... rb_check_interrupts -> invoke above signal handler - is okay

  # (2) Deadlock - can never occur if (1) is true
  mutex.synchronize do
    rb_check_interrupts -> can result in deadlock
    # It's okay to lock the mutex recursively => It's okay to lock the mutex in a trap handler.
    # Mutex#recursive = true/false can express the intent of nested locking in unexpected situations.
  end
end
```

Three proposed options:
(1) Run trap handlers in a separate threads (samuel: I think it's bad idea).
(2) Relax the restriction (easy option but maybe Mutex#lock is used in a dangerous way).
(3) `Mutex#recursive = true/false`
    * ko1: It is same as `Monitor`.  Also it has different behaviour with ordinal mutex.
    * ```
      m = Mutex.new
      m.synchronize{
        @name = 'foo'
        # <- trap handler should not interrupt it.
        @age = 10
      }
      ```
(4) `Mutex#safe_in_trap_handler = true/false` to remove limitation on some Mutex.
- POSIX says `pthread_mutex_lock` is not async-signal-safe. => Mutex.lock is not safe => except that we don't have actual signal handlers in Ruby.

- `IO#write` -> Using `mutex_synchronize` internally (????) -> pragmatic decision.
- At present, we couldn't implement IO in Pure Ruby because of this.

- We agree that we shouldn't use Mutex in trap handler.
- But the question is: Is it actually unsafe and are there valid use cases?

```ruby
mutex = Mutex.new
mutex.safe_in_trap_handler = true

Signal.trap(:INT) do # safe: true
  mutex.synchronize do
    # Safe?
  end
end
```

- Number (4) is okay but it's a bit ugly/specific to trap context implementation of Ruby.
- Number (3) seems more "standard" way - express that it can occur in a nested fashion.

```ruby
Signal.trap(:INT) do
  # Completely isolated, this code can never deadlock.
  mutex = Mutex.new
  condition = ConditionVariable.new
  state = nil
  Thread.new do # Thread pool
    mutex.synchronize do
      state = something
      condition.signal
    end
  end
  # This code was actually in a work pool implementation.
  mutex.synchronize do # Currently, this is not possible
    condition.wait(mutex)
  end
end
```

- samuel: I agree it's not the greatest code, but why is it not possible?
- samuel: Similar problem appears to exist in `Timeout.timeout` (eregon reported it).
- tompng: If I understand correctly, `Timeout.timeout`'s mutex cannot use `safe_in_trap_handler = true`. So Eregon's issue cannot be solved by Number (4)
- samuel: Since `safe_in_trap_handler = true` prevents the `ThreadError`, I don't know why it wouldn't work, I guess we'd have to ask him if it was sufficient.

- samuel: Are we sure `IO#write` can't deadlock in a trap handler?
```c
inline static void
io_allocate_write_buffer(rb_io_t *fptr, int sync)
{
    // ...
    if (NIL_P(fptr->write_lock)) {
        fptr->write_lock = rb_mutex_new();
        rb_mutex_allow_trap(fptr->write_lock, 1); // <-- Allow usage in trap context.
    }
}

static long
io_binwritev(struct iovec *iov, int iovcnt, rb_io_t *fptr)
{
    // Don't write anything if current thread has a pending interrupt:
    rb_thread_check_ints();

    // ...

    if (!NIL_P(fptr->write_lock)) {
        return rb_mutex_synchronize(fptr->write_lock, io_binwritev_internal, (VALUE)&arg);
    }
    else {
        return io_binwritev_internal((VALUE)&arg);
    }
}

static inline int
io_flush_buffer(rb_io_t *fptr)
{
    if (!NIL_P(fptr->write_lock) && rb_mutex_owned_p(fptr->write_lock)) { // <-- Prevent recursive locking
        return (int)io_flush_buffer_async((VALUE)fptr);
    }
    else {
        return (int)rb_mutex_synchronize(fptr->write_lock, io_flush_buffer_async, (VALUE)fptr);
    }
}
```

- samuel: JRuby and TruffleRuby do not have this limitation (preventing `Mutex#synchronize` in trap handler). By preventing `Mutex#synchronize` in trap context, we protect users from some hypothetical problems, but we prevent legitmate programs from working too. I think we can accept that `trap(...) {...}` is already advanced feature, so I think it's reasonable to expect users to use it correctly.
- samuel: Another idea - turn it into a warning with a specific category?
- samuel: It's also true that it's extremely risky to blockin a finalizer (just as it is in a trap handler) for general programs.

```ruby
mutex = Mutex.new

Thread.new do
  mutex.synchronize{sleep}
end

Signal.trap(:INT) do
  Thread.new do
    mutex.synchronize{sleep}
  end.join
end

# mutex.recursive = true may solve this problem?
while true
  sleep
end
```

- samuel: Why don't we prevent any operation that can deadlock in signal handlers?
- mame: Thread#join should be prohibited in a trap handler
  - samuel: If we are trying to prevent ALL deadlocks, then yes. What about if `IO#write` blocks?
  - samuel: Basically, it seems like impossible problem to solve.
- akr: I cannot imagine any user will use Thread#join in a trap handler. If there is, s/he want to die
  - samuel: It was just proposed in this meeting :)
- akr: A user may wrongly call Mutex#lock in a trap handler, but I cannot imagine a user unintentionally call Thread#join. So I don't think we need to prohibit Thread#join

```
# I have seen logging libraries that have done this (bad
def log_interrupt(message)
  # Internal implementation:
  Thread.new do
    Net::HTTP.post("http://log-server/ingest", {message: message})_
  end
end

Signal.trap(:INT) do
  log_interupt("It was interrupted")
end
```

- samuel: So what about introducing a new category of warnings, and using that for all operations that might deadlock? (also applies to GC finalizer/any trap context).
  - akr: It is too extreme. I don't see the needs
  - samuel: But isn't `ThreadError` more extreme?
  - akr: It would be enough to prohibit only Mutex#lock, currently I think.
  - samuel: Okay, but it seems extremely inconsistent to me I guess.
  - mame: I have no strong opinion but I somewhat agree with samuel. It is indeed inconsistent
  - akr: The consistency is not a goal of Ruby
  - samuel: Basically I also don't have a strong opinion, I ended up writing a native extension to work around the issue. It's a no-gvl work pool for GVL offload, and sometimes we release the GVL in trap handler context, and the work can get scheduled into the work pool. But I wrote the initial implementation in pure Ruby, but it would fail very occasionally if the code was invoked in a GC finalizer or trap context. So actually, this protection had the opposite effect, it worked 99% of the time, and would occasionally fail if the code ended up being executed in a trap context.

- samuel: I basically agree with Eregon.

Conclusion:

* matz: I have no strong opinion. but basically, I want to protect users and keep it as is (as possible) because it is safe default
* matz : a GC finalizer invocation should be postponed until a trap handler ends
* ko1: Timeout.timeout issue (eregon has) would be more complex, so we need a separate discussion

### [[Bug #21360]](https://bugs.ruby-lang.org/issues/21360) Inconsistent Support for `Exception#cause` in `Fiber#raise` and `Thread#raise` (ioquatix)

- Can we fix the consistency issues?

Preliminary discussion:

```
$ ruby -e 'raise "foo", cause: RuntimeError.new'
-e:1:in '<main>': foo (RuntimeError)
-e: RuntimeError (RuntimeError)

$ ruby -e 'Fiber.current.raise "foo", cause: RuntimeError.new'
-e:1:in 'Fiber#raise': exception class/object expected (TypeError)
        from -e:1:in '<main>'

$ ruby -e 'Thread.current.raise "foo", cause: RuntimeError.new'
-e:1:in 'Thread#raise': exception class/object expected (TypeError)
	from -e:1:in '<main>'
```

Discussion:

* samuel: Is it okay to make `Fiber#raise` and `Thread#raise` consistent (as much as possible) with `Kernel#raise`? It's additive so it shouldn't break anything.

Conclusion:

* matz: I agree.

### [[Feature #21140]](https://bugs.ruby-lang.org/issues/21140) Add a method for getting addresses of certain functions for 3rd party JITs (tenderlovemaking)

* RJIT has been extracted to a gem, but it can't work without getting the address of certain functions
* Not all JIT related functions are exported, so `dlsym` can't get the address
* Proposed API is like this: `RubyVM::Internals.address_of(:rb_vm_ci_argc)`, where `rb_vm_ci_argc` is the function we want the address for
  * If the function exists, it returns the address
  * If not, it returns `nil`
* This API should _not_ make guarantees about stability or portability
* I have no strong feelings about the method or constant name, but `RubyVM::Internals` tries to communicate this is an _internal_ and _unstable_ API

Preliminary discussion:

* ko1: I'm afraid if the exposed functions are specification, we can't change the internal functions. We need to explain it doesn't have any guarantee for compatibility.
* ko1: `RubyVM::Internals` seems convenient but not clear.
* mame: `RubyVM` itself is already internal, so `RubyVM.address_of`? Too general?
* ko1: `RubyVM.internal_function_address_of`?  `RubyVM::Lowlevel`?

https://github.com/ruby/ruby/compare/master...tenderlove:ruby:rjit-addr?expand=1

Discussion:

*

Conclusion:

*


### [[Feature #21442]](https://bugs.ruby-lang.org/issues/21442) Make tsort to bundled gems (hsbt)

* Any objection for this?

Conclusion:

* akr: as a maintainer of tsort, I approve this


### [[Feature #21039]](https://bugs.ruby-lang.org/issues/21039) Ractor.make_shareable breaks block semantics (seeing updated captured variables) of existing blocks (Eregon)

* How about `Ractor.make_shareable { ... }` which only allows literal block?
* That avoids the well known problem of a block body being reinterpreted in different ways (like the same block as proc & lambda, or in this issue like a regular block and a block-with-snapshot-of-the-environment).
* See https://bugs.ruby-lang.org/issues/21039#note-11 for details

Discussion:

* mame: The idea seems reasonable. The name `make_shareable` seems problem.

```
# ideas
f = Ractor.shareable_proc do |x, y|
end
f[1, 2, 3] # OK

f = Ractor.shareable_lambda do |x, y|
  self #=> nil
end
f[1, 2, 3] # should raise an ArgumentError

f = Ractor.shareable_lambda(self: nil) do |x, y|
  self #=> nil
end

f = nil.ractor_proc do |x, y|
  self #=> nil
end

class C
  define_method(:add, lambda {|x, y| x + y })
  define_method(:add, Ractor.shareable_lambda(self: 42) {|x, y|
    p self #=> #<C:...>, not 42
    x + y
  })
  define_method(:add, ractor: true) {|x, y| x + y }

  s = "hello"
  define_method(:add) { s << "str" }
  
  define_method(:add) {|x, y| x + y }
end

Ractor.lambda do
end

Ractor.proc do
end

Proc.isolate do
end

Proc.shareable do
end
```

* name
    * `Ractor.shareable_proc { }` # matz: accepted
    * `Ractor.shareable_lambda { }` # matz: accepted
    * `Ractor.proc { }`
    * `Ractor.lambda { }`
    * `Proc.isolate { }`
    * `Ractor.arrow` from `->`
 * specify self
     * `Ractor.shareable_proc(self: nil) do ... end # self keyword, deafult: nil` # matz: accepted
     * `Ractor.shareable_proc(nil)       do ... end # optional, deafult: nil`

* knu: doc/ractor.md contains `Proc#isolate` => should be removed.

Conclusion:

* matz: I agree to reject proc objects for `Ractor.make_shareable()`
* matz: Accept
    * `Ractor.shareable_proc { }` # matz: accepted
    * `Ractor.shareable_lambda { }` # matz: accepted
* matz: I think `Module#define_method` should have better notation for Ractors

### [[Feature #21459]](https://bugs.ruby-lang.org/issues/21459) Add Set C-API (jeremyevans0)

* I would like to add a minimal C-API for Set.
* We can add more functions later, but these are the ones I think would be necessary for extensions using core Set.
* Is the PR OK?

Discussion:

* akr: Is there any existing C API that has a prefix `rb_set_`?
* mame: There are `rb_set_errinfo`, `rb_set_end_proc`, `rb_set_class_path`, and `rb_set_class_path_string`
* knu: I can imagine the needs for them.

Conclusion:

* matz: accepted

---

Confirmation: frozen-string-literal by default for Ruby 4.0?
https://bugs.ruby-lang.org/issues/20205

* matz: At least, the version we will release this year should not change the default. Keep it off by default. I will reply.

---

matz: please close them
https://bugs.ruby-lang.org/issues/4539 Array#zip_with
https://bugs.ruby-lang.org/issues/21386 Enumerable#join_map

---

https://bugs.ruby-lang.org/issues/21455 Add a block argument to Array#join

* matz: I will reject

https://bugs.ruby-lang.org/issues/21402 ruby2_keywords affects methods/procs with post arguments

* matz: nobu, could you review the patch? If you are ok, it's ok

```ruby
def a(*c, **kw) [c, kw] end
def b(*a, b)
  p b #=> {bar: 1}
  a(*a, b)
end
ruby2_keywords(:b)

p b({foo: 1}, bar: 1) #=> [[{foo: 1}, {bar: 1}], {}]
# warning: Skipping set of ruby2_keywords flag for b (method accepts keywords or post arguments or method does not accept argument splat)
```

https://bugs.ruby-lang.org/issues/21435 Kernel#then_try as a conditional #then

* matz: No need. I will reject

https://bugs.ruby-lang.org/issues/21452 ARGS_SPLAT bytecode regression between 3.3 and 3.4

* matz: The current status is acceptable. Not a regression

https://bugs.ruby-lang.org/issues/21454 "undefined method 'break' for an instance of Binding"

* akr: no-op behavior could be useful. When we want to run code on and not on a debugger, we may want to ignore `binding.break`
* mame: But if it is no-op by default, people may unintentionally commit `binding.break`to the production code. If a gem that my app uses contains the break, and if I use debug gem for my app, it will break, which is never expected
* ko1: I will reject

https://bugs.ruby-lang.org/issues/21456 IO.close does not work in a rescue IO::TimeoutError block.

```ruby
execArg = 'echo testwrite; sleep 10'
tofuProcess = IO.popen(execArg, 'r', pgroup: true) ### Create a process group
puts 'executed tofu process.'
tofuProcess.timeout=1
begin
        tofuOut = tofuProcess.read
rescue IO::TimeoutError
        puts 'rescue occured'
        Process.kill(:TERM, -tofuProcess.pid) ### send a signal manually
        tofuProcess.close
        puts 'process closed'
end
```
https://bugs.ruby-lang.org/issues/21385 Namespace: Suggesting a rename

* matz: I will reply

https://bugs.ruby-lang.org/issues/21503 \p{Word} does not match on \p{Join_Control} while docs say it does

* naruse: I will take a look

https://bugs.ruby-lang.org/issues/21501 Include native filenames in backtraces as sources for native methods

* matz: I will reply
