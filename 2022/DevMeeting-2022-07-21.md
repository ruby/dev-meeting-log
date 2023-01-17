---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-07-21

https://bugs.ruby-lang.org/issues/18836

## DateTime and location

* 2022/07/21 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/08/18 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* 3.2.0 preview 2 in summer

## AWS Cost check

https://us-east-1.console.aws.amazon.com/cost-management/home?region=ap-northeast-1#/custom?groupBy=UsageType&forecastTimeRangeOption=None&hasBlended=false&hasAmortized=false&excludeDiscounts=true&excludeTaggedResources=false&excludeCategorizedResources=false&chartStyle=Stack&timeRangeOption=Last12Months&granularity=Monthly&reportName=For%20dev-meeting&reportType=CostUsage&isTemplate=false&usageAs=usageQuantity&filter=%5B%7B%22dimension%22:%22RecordType%22,%22values%22:%5B%22SavingsPlanNegation%22,%22Credit%22%5D,%22include%22:false,%22children%22:null%7D%5D&subscriptionIds=%5B%5D&reportId=198dedc9-fa9b-4746-9dfc-0818c16b7528&excludeForecast=false&startDate=2021-07-01&endDate=2022-06-30

Data transfer が増えている

https://us-east-1.console.aws.amazon.com/cost-management/home?region=ap-northeast-1#/custom?groupBy=UsageType&forecastTimeRangeOption=None&hasBlended=false&hasAmortized=false&excludeDiscounts=true&excludeTaggedResources=false&excludeCategorizedResources=false&excludeForecast=true&chartStyle=Stack&timeRangeOption=CurrentMonth&granularity=Daily&reportName=Data%20Transfer&reportType=CostUsage&isTemplate=false&usageAs=usageQuantity&filter=%5B%7B%22dimension%22:%22UsageTypeGroup%22,%22values%22:%5B%22EC2:%20Data%20Transfer%20-%20CloudFront%20(Out)%22,%22EC2:%20Data%20Transfer%20-%20Inter%20AZ%22,%22EC2:%20Data%20Transfer%20-%20Internet%20(In)%22,%22EC2:%20Data%20Transfer%20-%20Internet%20(Out)%22,%22EC2:%20Data%20Transfer%20-%20Region%20to%20Region%20(In)%22,%22EC2:%20Data%20Transfer%20-%20Region%20to%20Region%20(Out)%22,%22EC2:%20EC2%20Data%20Transfer%20-%20CloudFront%20(In)%22,%22S3:%20Data%20Transfer%20-%20Internet%20(In)%22,%22S3:%20Data%20Transfer%20-%20Internet%20(Out)%22,%22S3:%20Data%20Transfer%20-%20Region%20to%20Region%20(In)%22,%22S3:%20Data%20Transfer%20-%20Region%20to%20Region%20(Out)%22%5D,%22include%22:true,%22children%22:null%7D%5D&subscriptionIds=%5B%5D&reportId=ba42dd79-8c05-4ccb-aae0-677f341c10b2&startDate=2022-07-01&endDate=2022-07-31

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18832]](https://bugs.ruby-lang.org/issues/18832) Suspicious superclass mismatch (#18832) (eregon)

* OK to fix it? (i.e., don't look into included/prepended modules in Object, no special rule if the closest nesting is Object/top-level)
* Any compatibility concerns? Anything we can do to address that, maybe warn first if the special rule is used, then remove the special rule?

Preliminary discussion:

```ruby=
module M
  class C
  end
end

class C2
  include M
  class C < String # OK; defined C2::C
  end
end

#class Object
#  include M
#  class C < String # superclass mismatch for class C (TypeError)
#  end
#end

include M
# class ::M::C < String # superclass mismatch for class C (TypeError)
class C < String # superclass mismatch for class C (TypeError)
end
```

```ruby=
module M
  class C
  end
end

class C2
  include M
  class C
    def test1
    end
  end
end

include M
class C
  def test2
  end
end

p M::C.instance_methods(false) #=> [:test2]
```

Discussion:

* samuel: It seems confusing.
* mame: Is this intentional?
* matz: I don't think so. It is indeed inconsistent, but I don't know it is worth fixing
* mame: nobu says it's been like this since 1.1.x or so.
* ko1: If we fix it, which behavior (reopening the class or defining a new class) is preferable?
    * samuel: Isn't defining a new class safer?
    * shyouhei says the same
    * mame: agreed
* matz: No idea why it behaves as it is...
* samuel: reopening class should follow lexical scope or...? If your intention is to modify `M::C` why not reopen it `class M::C ...`.
* mame: agreed to Samuel.
* ko1: does `prepend` works the same way?
* mame: There is no prepend for global toplevel.
* samuel: Maybe very risky, e.g. https://github.com/rails/rails/blob/main/activerecord/lib/active_record/base.rb#L282-L332 - user code could incorrectly break library code.

```ruby=
module P
  class C < String
  end
end

class Object
  include P
end

class C < Array #=> t.rb:12:in `<main>': superclass mismatch for class C (TypeError)
end
```

* samuel: It's consistent with inheritance too:

```ruby=
class A
    class B < String
    end
end

class C < A
    class B < Array
    end
end

# irb(main):010:0> A::B
# => A::B
# irb(main):011:0> C::B
# => C::B
```

* mame: matz, what do you want to do?
* matz: Hmm... So try the fix? If it breaks something let's revert it.

Conclusion:

* matz: Try changing the toplevel's behaviour towards that of nested ones.


### [[Feature #18809]](https://bugs.ruby-lang.org/issues/18809) Add `Numeric#ceildiv` (nobu)

* ceiling or rounding up division
* maybe also `Numeric#floordiv`?
* `Numeric#div(divisor, round: :up)`?
* Julia provides `cld` and `fld` for the ceiling and flooring division, respectively.
* https://github.com/ruby/ruby/pull/5965

Preliminary discussion:

* ko1: Should it be `Integer#ceildiv`? Is there any reason to make it belong to Numeric?

Discussion:

```ruby
a.ceildiv(b)  #=> (a + b - 1) / b or a.quo(b).ceil

# knu
a./(b, round: :up) # joke
a.quo(b).ceil

# OP's alternative
a.div(b, round: up)

# akr: How about Integer#divmod ?
10.div(3, round: :up)    #=> 4
10.divmod(3, round: :up) #=> [4, -2]
```

* mame: I can think of a use case in calculating pages of paginated queries.
* samuel: Time for a new operator ^/ and _/ ??? haha. `cld` and `fld` are too short.
* shyouhei: Julialang is such a language of terseness.
* naruse: Is there any precision issues?
* mame: Seems not for this particular case.
* matz: `#ceildiv` doesn't sound that bad.
* nobu: We don't have to bother divmod if we go for a separate method.
* nobu: Do we have `#floordiv` as an alias to `Integer#div`?
* ko1: Do other languages than Julia have this?
    * Matlab has [ceildiv](https://www.mathworks.com/help/fixedpoint/ref/ceildiv.html)
    * Python: https://stackoverflow.com/questions/14822184/is-there-a-ceiling-equivalent-of-operator-in-python
        * Integer division operator? `//`
* ???: What's happening if self and/or argument is negative?

```ruby
 3.div( 2) #=>  1  == floor(3/2)
 3.div(-2) #=> -2  == floor(-3/2)
-3.div( 2) #=> -2  == floor(-3/2)
-3.div(-2) #=>  1  == floor(3/2)
```

```ruby
# naive implementation: ceildiv = (a + b - 1) / b
 3.ceildiv( 2) #=>  2  == ceil(3/2)
 3.ceildiv(-2) #=>  0  != ceil(-(3/2))
-3.ceildiv( 2) #=> -1  == ceil(-(3/2))
-3.ceildiv(-2) #=>  3  != ceil(3/2)

# The implementation of the PR: ceildiv = -(a / -b)
 3.ceildiv( 2) #=>  2  == ceil(3/2)
 3.ceildiv(-2) #=> -1  == ceil(-(3/2))
-3.ceildiv( 2) #=> -1  == ceil(-(3/2))
-3.ceildiv(-2) #=>  2  == ceil(3/2)
```

- Check with Matlab???

* matz: Let's introduce only `#ceildiv`. Consider `#floordiv` if anyone actually wants it
* mame: Do we introduce only `Integer#ceildiv`? or `Numeric#ceildiv`?
* matz: Do we want `Floor#ceildiv`?

```ruby
3.0.ceildiv(4.0)
3.0.div(4.0).ceil
```

Conclusion:

* matz: Start with only `Integer#ceildiv`

### [[Feature #18655]](https://bugs.ruby-lang.org/issues/18655) Copy `IO#wait_readable`, `IO#wait_writable`, `IO#wait_priority` and `IO#wait` into core. (ioquatix)

- Pull Request: https://github.com/ruby/ruby/pull/6036

Preliminary discussion:

```
https://linuxjm.osdn.jp/html/LDP_man-pages/man7/socket.7.html

POLLPRI
There is some exceptional condition on the file descriptor. Possibilities include:
* There is out-of-band data on a TCP socket (see tcp(7)).
* A pseudoterminal master in packet mode has seen a state change on the slave (see ioctl_tty(2)).
* A cgroup.events file has been modified (see cgroups(7)).
```

https://man7.org/linux/man-pages/man7/epoll.7.html
https://man7.org/linux/man-pages/man2/poll.2.html

```
POLLPRI
High-priority data may be read without blocking.
```

https://pubs.opengroup.org/onlinepubs/009696799/functions/poll.html

Discussion:

* Merged into CRuby using preprocessor flag to prevent `io-wait` gem from defining those methods.
* It seems like there is some concern around `wait_priority` method.
    * Is there a good reason NOT to include `wait_priority`?
    * Follows same name conventions of system interface.
    * Can also be accessed via `io.wait(IO::PRIORITY, ...)`.
* matz: Let me see...
* matz: previous conclusion was "if nobu and akr are both okay, go".
* nobu: No problem with me.
* nobu: Not sure about wait_priority though.
* ko1: wait_priority waits for a `send(..., MSG_OOB)`. Can be useful when for instance interfacing ^C.
* samuel: `select` doesn't support it. Only `poll`, `io_uring`, etc.
* akr: I guess `select` supports OOB: https://pubs.opengroup.org/onlinepubs/009696799/functions/select.html
* akr: OOB could have portability issues?
    * samuel: Unfortunately all network IO has portability issues :( e.g. non-blocking on windows etc... I wouldn't assume `MSG_OOB` works well on Windows?
* samuel: `MSG_OOB` is part of TCP standard IIRC.
    * Windows: https://docs.microsoft.com/en-us/windows/win32/winsock/protocol-independent-out-of-band-data-2
    * Linux: https://www.gnu.org/software/libc/manual/html_node/Out_002dof_002dBand-Data.html (BSD/MacOS similar).

```ruby=
require 'socket'
require 'io/wait'

fork{
  sleep 1
  p :start
  s = Socket.tcp('localhost', 12345)
  4096.times{
    s.send("hello", 0)
  }
  s.send('X', Socket::MSG_OOB)
  sleep
}

# server
Socket.tcp_server_loop('localhost', 12345){|s, client|
  p [s, client]

  # monitoring thread
  Thread.new{
    s.wait_priority
    p recv_pri: s.recv(4096, Socket::MSG_OOB)
    # exit!
  }

  i = 0
  loop{
    p wait_readable: s.wait_readable
    # p read: s.read
    p recv: s.recv(4096).split(//).tally
    i += 1
    exit if i > 10
  }
}
```

* ko1: One possible option is to let `IO#wait` handle OOB.
    * samuel: It's fine - maybe `wait_priority` is too obscure? But... maybe it's inconsistent with `wait_readable` and `wait_writable`?
* usa: It's a bit hard to understand how `wait_priority` works, from its name.
    * samuel: The name is directly copied from flag name, `POLLPRI` -> `PRIORTY`.
* samuel: It's not just for `MSG_OOB`. There are some other obscure use cases.
* samuel: It's acceptable to defer to `IO#wait(IO::PRIORITY)`. This is the interface for the fiber scheduler which just handles an integer mask for events.
* usa: maybe the prefferred name is like `wait_prior_msg`.
* shyouhei: Should we drop `wait_priority` for now?
* ko1: how about `IO::PRIORITY`?

```
ko1@aluminium:~$ gem-codesearch 'IO::PRIORITY'
/srv/gems/async-2.0.3/lib/async/wrapper.rb:                     @io.to_io.wait(::IO::READABLE|::IO::WRITABLE|::IO::PRIORITY, timeout) or raise TimeoutError
/srv/gems/evt-0.4.0/lib/evt/backends/bundled.rb:  #   `IO::WRITABLE` and `IO::PRIORITY`.
/srv/gems/evt-0.4.0/lib/evt/backends/bundled.rb:    # TODO: IO::PRIORITY
/srv/gems/io-event-machty-1.0.1/lib/io/event/debug/selector.rb:                         if (events & IO::PRIORITY) > 0
/srv/gems/io-event-machty-1.0.1/lib/io/event/selector/select.rb:                                if (events & IO::READABLE) > 0 or (events & IO::PRIORITY) > 0
/srv/gems/io-wait-0.2.3/ext/io/wait/wait.c: * +IO::PRIORITY+.
/srv/gems/packwerk-2.2.0/sorbet/rbi/gems/activesupport@7.0.0.alpha-d612542336d9a61381311c95a27d801bb4094779.rbi:IO::PRIORITY = T.let(T.unsafe(nil), Integer)
/srv/gems/pg-1.4.1/lib/pg/connection.rb:                                                socket_io.wait(IO::READABLE | IO::PRIORITY, timeout)
/srv/gems/pg-1.4.1/lib/pg/connection.rb:                                                socket_io.wait(IO::WRITABLE | IO::PRIORITY, timeout)
```

* matz: Not against this constant.
* mame: Why are you okay with IO::PRIORITY but not okay with wait_priority?
* matz: Because if it is removed, there would be no way to wait the event.
* matz: Also, ppoll accepts PRIORITY flags. We want to follow the system API design
* ko1: ppoll accepts POLLPRI, but not does PRIORITY. There is no "PRIORITY" in the system API itself
    * samuel: We don't expose "poll" or "epoll" or "kqueue" but expect some internal mapping. So `IO::PRIORITY` -> `POLLPRI` for poll/epoll/io_uring but maybe not others e.g. `select` backend, `IO::PRIORITY` maybe `select(..., error_fds)`?
    * mame: Traditionally Ruby does not create its own API for system but just follow UNIX's design. If some platform does not provide UNIX's API, Ruby tries to emulate it as far as possible
* matz: Hmm... Okay, I accept `IO::PRIORITY` constant. It looks too late to change. I should have reviewed the API design thoroughly at the time of introduction
  * ko1: IO#wait_priority and IO::PRIORITY have been introduced since Ruby 3.0
  * samuel: in the `io/wait` gem. So if we don't want to move it to core it's still possible to a certain extent.
  * mame: matz says it breaks comaptibility
* ko1: wait_priority is not used in any gem yet, so we may change it
* nobu: shall we remove it from io/wait gem?

```
POLLPRI: High-priority data may be read without blocking.
https://pubs.opengroup.org/onlinepubs/009696799/functions/poll.html
```

* ko1: Do we want to kill the io-wait gem?
* nobu: We don't. there are remaining methods there.

Conclusion:

* matz: `#wait_readable` and `#wait_writable` accepted.
* matz: drop `#wait_priority`, _for now_, at least.
* matz: `#wait` and `IO::PRIORITY` are accepted (given it already has use cases).
  * `io.wait(IO::READABLE)` == `io.wait_readable`
  * `io.wait(IO::READABLE | IO::WRITABLE)` similar to `IO.select([io], [io])`

### [[Feature #18810]](https://bugs.ruby-lang.org/issues/18810) Make `Kernel#p` interruptable. (ioquatix)

- Pull Request: https://github.com/ruby/ruby/pull/5967

```ruby=
  def test_handle_interrupt_and_p
    assert_in_out_err([], <<-INPUT, %w(:ok :ok), [])
      th_waiting = false
      t = Thread.new {
        Thread.current.report_on_exception = false
        Thread.handle_interrupt(RuntimeError => :on_blocking) {
          th_waiting = true
          nil while th_waiting
          # p shouldn't provide interruptible point
          p :ok
          p :ok
        }
      }
      Thread.pass until th_waiting
      t.raise RuntimeError
      th_waiting = false
      t.join rescue nil
    INPUT
  end
```

Discussion:

* samuel: Is there any reason why this should be different from every other IO operation?
* mame: Because it is for debugging. It would be practically impossible to use it in handle_interrupt region
    * samuel: What use case is broken if we make it interruptable?
    * mame: We want to insert `p` anywhere for debugging. We don't expect any other side effect for adding it. I'm afraid if context switching on `p` is an unexpected side effect
* ko1: Do you (samuel) have any problem if `p` is not interruptible?
  * ...
* samuel: How about adding a document to `Kernel#p` that it is just for debugging?
  * matz: I agree

```
/*
 *  call-seq:
 *    p(object)   -> obj
 *    p(*objects) -> array of objects
 *    p           -> nil
 *
 *  For each object +obj+, executes:
 *
 *    $stdout.write(obj.inspect, "\n")
 *
 *  With one object given, returns the object;
 *  with multiple objects given, returns an array containing the objects;
 *  with no object given, returns +nil+.
 *
 *  Examples:
 *
 *    r = Range.new(0, 4)
 *    p r                 # => 0..4
 *    p [r, r, r]         # => [0..4, 0..4, 0..4]
 *    p                   # => nil
 *
 *  Output:
 *
 *     0..4
 *     [0..4, 0..4, 0..4]
 *
 */
static VALUE
rb_f_p(int argc, VALUE *argv, VALUE self)
```

- samuel: It might be a problem for internal fiber scheduler if `p` happens on non-blocking stdout but I don't have any opinion.

Conclusion:

* matz: Let's add a document to Kernel#p
    * "Uninterruptible" & "Debugging Only"

### [[Feature #18630]](https://bugs.ruby-lang.org/issues/18630) Introduce general `IO#timeout` and `IO#timeout=` for blocking operations. (ioquatix)

- Pull Request: https://github.com/ruby/ruby/pull/5653

Discussion:

* We can handle all blocking IO operations in general.
    * Some OS limitations (maybe Windows is worse).
* Only cost when timeout exists.
* What exception class should we use? Is it an error?
    * `IO::TimeoutError < RuntimeError`
    * `IO::TimeoutError < ::TimeoutError < ???`
    * `Timeout < Exception` (see `Interrupt`).

- Q1: Do we accept `IO#timeout` and `IO#timeout=` for general timeout for any blocking operation
- Q2: What kind of exception do we want to use?
    - Errno::ETIMEDOUT is the current PR.

* ko1: I don't think _everything_ can be interruptible.  For instance `IO#close` can block, and can never be interrupted.
    * samuel: This isn't actually true, `IO#close` CAN have a timeout with `io_uring`.
    * samuel: OK' I'll list operations that interfaces with this API.
    * `IO.select` & `IO#timeout` are completely independent.
* akr: `write` with timeout would be difficult with blocking I/O (O_NONBLOCK is clear).  writability notified by select/poll don't guarantee writable more than one byte.  So `write` system call with two or more bytes may block indefinitely.  I think non-blocking I/O (O_NONBLOCK is set) works well.  Maybe IO#timeout= should be limited for non-blocking I/O?  Documentation for limitation about `write` system call with blocking I/O may be enough?

```
/*
 *  call-seq:
 *    timeout = duration -> duration
 *    timeout = nil -> nil
 *
 *  Set the internal timeout to the specified duration or nil. The timeout
 *  applies to all blocking operations, but the implementation may be limited
 *  depending on teh underlying system call.
 *
 *  This affects the following methods (but is not limited to): #gets, #puts,
 *  #read, #write, #wait_readable and #wait_writable. This also affects
 *  blocking socket operations like Socket#accept and Socket#connect.
 *
 */
```

* matz: Not everything that blocks are IO-related.
    * samuel: network / IO blocking is more of a security issue, e.g. slow HTTP clients deliberately tying up server resources/connections/threads.
* akr: There are methods that involves multiple IO operations, like `IO#gets` (can issue multiple system calls).  This timeout can be handy for those situations.
    * samuel: The timeout is per system call only. It's a last line of defence against slowloris attacks or other similar network attacks. Could consider `IO.default_timeout=` and/or `Socket.default_timeout=`.
    * samuel: DNS resolution can/should have an independent timeout, and this can be implemented using the fiber scheduler hooks `address_resolve`.

* matz: OK I think I understand the motivation.
* matz: re: exception class?
* nobu: Shouldn't it raise `SystemCallError`?
* akr: It's not the system call that is failing.
    * samuel: agreed.
* ko1: `Timeout::Error` can be suboptimal.  This exception is asynchronous.
    * samuel: also isn't it from a gem/not part of core?
* mame: Similar discussion was there for Regexp.
    * https://bugs.ruby-lang.org/issues/17837#change-97056
    * samuel: agree. Can we find some common pattern to make it easier for users/developers? `rescue ::Timeout`/`rescue ::TimeoutError` -> any timeout error from any operation.

* samuel: `Async::TimeoutError` has worked well in practice: https://github.com/socketry/async/blob/f37f8e4f816f748b2a2a509614dce54a3e3b71e1/lib/async/task.rb#L46-L52

* 2 main options:

```ruby=
class Timeout < Exception; end
class IO::Timeout < ::Timeout; end
```

```ruby=
class TimeoutError < StandardError; end
class IO::TimeoutError < ::TimeoutError; end
class Regexp::TimeoutError < ::TimeoutError; end
class Resolv::TimeoutError < ::TimeoutError; end
```

Example:

```ruby=
# class TimeoutError < StandardError; end
# class IO::TimeoutError < TimeoutError; end
# class IO::TimeoutError < StandardError; end
# class Timeout < Exception; end

STDIN.timeout = 1
begin
    STDIN.read
rescue Errno::ETIMEDOUT # What do we want here?
rescue ::TimeoutError
rescue ::IO::TimeoutError
rescue ::Timeout
    # ...
end
```

It can be difficult for users to know what exceptions to use and it's getting more complex as we introduce more: https://github.com/socketry/async-http/blob/ed3fbca3b65b8669964c70566aa6edc3a72a7aab/lib/async/http/protocol/http2/connection.rb#L103-L110

```ruby=
begin
    while !self.closed?
        self.consume_window
        self.read_frame
    end
rescue SocketError, IOError, EOFError, Errno::ECONNRESET, Errno::EPIPE, Async::Wrapper::Cancelled # horror
end

module IO::DisconnectedError
end

class GeneralIOError
end
class SockerError < GeneralIOError; end
class IOError < GeneralIOError; end

module GeneralIOError
end
class SocketError
  include GeneralIOError
end
class IOError
  include GeneralIOError
end
class Errno::ECONNRESET
  include GeneralIOError
end

# Do we prefer
IO::GeneralError
IOGeneralError
GeneralIOError

SocketError ???
Socket::Error ???
```

* akr: I can think of a difficult situation where a _blocking_ `IO#write` blocks.  `select` can help, but doesn't ultimately prevent a `write(2)` system call from blocking indefinitely.
    * samuel: it's true there is no recovery in this case, but my consideration is "last line of defence" in network IO.
    * `io_uring` can help prevent `write` from blocking forever because you can attach an explicit timeout to the `write(2)` system call (partly implemented in `io-event` gem).
* akr: Practically, timeout is needed for network IOs.  They are normally nonblocking.  It should not be a serious problem.
    * samuel: 100% agree timeout is needed for network IOs. Without timeout, it's very risky.

```
ko1@aluminium:~$ gem-codesearch '^class TimeoutError\b'
/srv/gems/01xinan-metasploit-framework-6.0.17/lib/rex/exceptions.rb:class TimeoutError < Interrupt
/srv/gems/busser-robot-0.1.3/vendor/robot/src/robot/errors.py:class TimeoutError(RobotError):
/srv/gems/canvas-jobs-0.11.0/lib/delayed/worker.rb:class TimeoutError < RuntimeError; end
/srv/gems/concurrent-0.2.2/lib/concurrent/actors/mailbox.rb:class TimeoutError < RuntimeError
/srv/gems/concurrent-selectable-0.1.1/lib/concurrent/selectable/common.rb:class TimeoutError < RuntimeError
/srv/gems/dohruby-0.3.8/lib/doh/util/doh_socket.rb:class TimeoutError < RuntimeError
/srv/gems/dohruby-0.3.8/lib/doh/util/file_edit.rb:class TimeoutError < RuntimeError
/srv/gems/dragonfly_puppeteer-0.1.0/node_modules/gh-got/node_modules/p-timeout/index.js:class TimeoutError extends Error {
/srv/gems/dragonfly_puppeteer-0.1.0/node_modules/p-timeout/index.js:class TimeoutError extends Error {
/srv/gems/dstruct-0.0.1/lib/rex/exceptions.rb:class TimeoutError < Interrupt
/srv/gems/exact_target_sdk-1.0.1/lib/exact_target_sdk/errors.rb:class TimeoutError < Error
/srv/gems/exercism-analysis-0.1.1/vendor/python/logilab/common/proc.py:class TimeoutError(ResourceError):
/srv/gems/librex-0.0.999/lib/rex/exceptions.rb:class TimeoutError < Interrupt
/srv/gems/msgpack-rpc-0.7.0/lib/msgpack/rpc/exception.rb:class TimeoutError < RPCError
/srv/gems/necktie-1.1.0/vendor/session/test/session.rb:class TimeoutError < StandardError; end
/srv/gems/pylintr-0.1.3/bin/pylint_2_7/lib/python2.7/site-packages/pip/_vendor/requests/packages/urllib3/exceptions.py:class TimeoutError(HTTPError):
/srv/gems/redial-ruby-agi-2.0.1/lib/ruby-agi/error.rb:class TimeoutError < Exception
/srv/gems/redpack-1.0.6/rblib/redpack/exception.rb:class TimeoutError < Error
/srv/gems/rex-2.0.13/lib/rex/exceptions.rb:class TimeoutError < Interrupt
/srv/gems/rex-core-0.1.28/lib/rex/exceptions.rb:class TimeoutError < Interrupt
/srv/gems/rigid-0.2.1/vendor/requests/packages/urllib3/exceptions.py:class TimeoutError(HTTPError):
/srv/gems/ruby-agi-2.0.0/lib/ruby-agi/error.rb:class TimeoutError < Exception
/srv/gems/session-3.2.0/test/session.rb:class TimeoutError < StandardError; end
/srv/gems/ssl_scan-0.0.6/lib/ssl_scan/exceptions.rb:class TimeoutError < Interrupt
```

* ko1: what the position of this method?
    * samuel: What is the cost of support on platforms where it is unsupported. Can we reduce this? What platforms are impacted?
    * samuel: Windows looks to eventually support the same `io_uring` concept: https://windows-internals.com/i-o-rings-when-one-i-o-operation-is-not-enough/ which will make non-blocking I/O (including network I/O) much simpler.

Conclusion:

* samuel: I'll experiment a synchronous timeout error approach
* matz: I look forward to seeing the result

### [[Bug #18780]](https://bugs.ruby-lang.org/issues/18780) Surprising `self` for C API `rb_eval_string()` (alanwu)

* Script for `rb_eval_string()` runs with locals from the inner most Ruby context, but `self` is always top level `self` (`main`). This unique execution environment is surprising; it's not equivalent to any environment one could get using pure Ruby.
* Test script available at [[ruby-core:108919]](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/108919). You need to flip the `if` in the C code to see it.
* `extension.rdoc` says it's [supposed to capture local variables](https://github.com/ruby/ruby/commit/9562813d338b5f2870408e95747d71502590c14f#diff-756b839706deff86d7d27012df24a9df5105960cf6a23d9fa96f3fe3a53acd7cR305) while the header says it runs in an ["isolated binding"](https://github.com/ruby/ruby/blob/fdd1102550d375be4302ac8d2e081797de00a205/include/ruby/internal/eval.h#L43)
* It looks like `self` has stayed as the top level `self` [since 1.3](https://github.com/ruby/ruby/blob/ruby_1_3/eval.c#L1119)
* Should we change `self` for scripts passed to `rb_eval_string()`?

Preliminary discussion:


```clike=
diff --git a/range.c b/range.c
index e45e405f5a..63cca95b82 100644
--- a/range.c
+++ b/range.c
@@ -2326,6 +2326,12 @@ range_count(int argc, VALUE *argv, VALUE range)
  *
  */

+static VALUE
+range_my_eval(VALUE self)
+{
+    return rb_eval_string("[self, __method__, __callee__, __FILE__, a]");
+}
+
 void
 Init_Range(void)
 {
@@ -2368,4 +2374,5 @@ Init_Range(void)
     rb_define_method(rb_cRange, "include?", range_include, 1);
     rb_define_method(rb_cRange, "cover?", range_cover, 1);
     rb_define_method(rb_cRange, "count", range_count, -1);
+    rb_define_method(rb_cRange, "my_eval", range_my_eval, 0);
 }
```

```ruby=
class C
  def foo
    a = 1
    p (1..2).my_eval
    #=> [main, :foo, :foo, "eval", 1]
  end
end

C.new.foo
```

nobu: `rb_obj_instance_eval` can be used this usecase.

```diff=
diff --git a/range.c b/range.c
index e45e405f5a..ba5532d56d 100644
--- a/range.c
+++ b/range.c
@@ -2326,6 +2326,16 @@ range_count(int argc, VALUE *argv, VALUE range)
  *
  */

+static VALUE
+range_my_eval(VALUE self)
+{
+    // return rb_eval_string("[self, __method__, __callee__, __FILE__, a]");
+    VALUE argv[1] = {
+        rb_str_new2("[self, __method__, __callee__, __FILE__, a]"),
+    };
+    return rb_obj_instance_eval(1, argv, self);
+}
+
 void
 Init_Range(void)
 {
@@ -2368,4 +2378,5 @@ Init_Range(void)
     rb_define_method(rb_cRange, "include?", range_include, 1);
     rb_define_method(rb_cRange, "cover?", range_cover, 1);
     rb_define_method(rb_cRange, "count", range_count, -1);
+    rb_define_method(rb_cRange, "my_eval", range_my_eval, 0);
 }
```

```ruby=
def foo
  a = 1
  p (1..2).my_eval
  #=> [main, :foo, :foo, "eval", 1]
  p (3..4).my_eval{}
  #=> my_eval': wrong number of arguments (given 1, expected 0) (ArgumentError)
end

foo
```

* ko1: If `my_eval` receives a block which is not related to the eval, it doesn't work.
* ko1: So, as AlanWu wrote, `rb_eval_string()` is similar to `toplevel_self.instance_eval("...")`, not `TOPLEVEL_BINDING.eval("...")`
  * Options
    1. (same as doc/extension says) change `self` to the current (caller's) self.
    2. (same as header says) use an completely isolated binding (prohibit reading from the current locals) `TOPLEVEL_BINDING.eval(...)`
    3. remain current API for compatibility (and use `rb_funcall(rb_binding_new(), :eval, ...)`

Discussion:

* matz: I'm keen to Option 2

```ruby
def foo
  TOPLEVEL_BINDING.eval("a = 1")
end
def bar
  TOPLEVEL_BINDING.eval("p a")
end
foo
bar #=> 1
```

```clike
rb_eval_string("a = 1");
rb_eval_string("p a"); //=> matz: should be raised
```

shugo: I use rb_eval_string in mod_ruby. I expect any value to be GC'ed even if it is assigned to a local variable
matz: Okay, so I choose Option 1 and if `rb_eval_string` is called under no ruby frame, it would eval the string with a new empty binding (not TOPLEVEL_BINDING)

Conclusion:

* `rb_eval_string` evals the string under the current binding (i.e., self should be changed)
* If there is no ruby frame, `rb_eval_string` creates an empty binding and use it to eval the string (i.e., local variables are not kepy between multiple `rb_eval_string` calls)


### [[Bug #18882]](https://bugs.ruby-lang.org/issues/18882) On 64-mingw-ucrt, `File.read()` sometimes doesn't read entire file (alanwu)

* Plain one argument call to `File.read()` can stop reading in the middle of file due to CTRL+Z being [interpreted as EOF](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/fopen-wfopen?view=msvc-170) in Windows text mode
* I think in the one argument form the intention is to read every byte in the file
* Reading everything seems like a better default nowadays. But maybe too hard to change it
* Should we special case the "read entire file" case? Maybe undesirable because that also disables CRLF translation
* Could always just update the docs about this pitfall and make the user handle it
* Because of Ruby's popularity on non Windows systems and FS APIs in other languages not exposing text mode by default, the current behavior will probably continue to surprise

Preliminary discussion:

* nobu: this is not the issue affects only Ruby, but will surprise all Windows users.
* nobu: the handling of ctrl+z (0x1a), CRLF transition, etc. are handled by msvcrt for the performance reason. It is difficutl for Ruby to configure the behavior. Please contact on Windows
* mame: it would be good to add a doc

FYI: https://ja.wikipedia.org/wiki/End_Of_File

Discussion:

* usa: This is per spec. Period.
* usa: I'm not against flipping the default mode to be binary, though.
* shyouhei: I guess the OP is not talking about default mode in general, but File.read method.
* usa: Then use binread instead.
* shyouhei: Agreed, and broader story should be a separate ticket.

Conclusion:

* usa: Let's add "File.read reads a file under text mode" to the document of File.read (and File.write)


### [[Misc #18888]](https://bugs.ruby-lang.org/issues/18888) Migrate ruby-lang.org mail services to Google Domains and Google Workspace (shugo)

  * We are planning to migrate the mail services to Google Domains and Google Workspace (Google Groups).
  * The maximum numbr of Google Domains email aliases is 100, but currently ruby-lang.org has 153 email aliases, so we have to reduce them.
       * The number of active committers listed in email.yml is 58.

Preliminary discussion:

```
$ git log| grep @ruby-lang.org | sort | uniq
...
Author: Aaron Patterson <tenderlove@ruby-lang.org>
Author: Alan Wu <alanwu@ruby-lang.org>
Author: Eric Wong <normal@ruby-lang.org>
Author: Hiroshi SHIBATA <hsbt@ruby-lang.org>
Author: NAKAMURA Usaku <usa@ruby-lang.org>
Author: Nobuyoshi Nakada <nobu@ruby-lang.org>
Author: SHIBATA Hiroshi <hsbt@ruby-lang.org>
Author: Shugo Maeda <shugo@ruby-lang.org>
Author: U.Nakamura <usa@ruby-lang.org>
Author: Urabe, Shyouhei <shyouhei@ruby-lang.org>
Author: Yuichiro Kaneko <yui-knk@ruby-lang.org>
Author: Yusuke Endoh <mame@ruby-lang.org>
Author: git <svn-admin@ruby-lang.org>
Author: nagachika <nagachika@ruby-lang.org>
Author: 卜部昌平 <shyouhei@ruby-lang.org>
```

Discussion:

* https://github.com/ruby/ruby-commit-hook/blob/master/config/email.yml
* shyouhei: If we have 100+ new committers in future what will happen?
* shugo: We will not be able to provide a mail alias to new committers in such case

* hsbt: Related topic, blade is down. Currently there is no achive for the mailing lists.


### [[Misc #18891]](https://bugs.ruby-lang.org/issues/18891) Expand tabs in C code (k0kubun)

* Does anybody have objections to fixing this problem? Any suggestions on how to approach this?

Conclusion:

* nobu: I gave up
* mame: Thank you!
* matz: Okay go ahead


### [[Bug #18768]](https://bugs.ruby-lang.org/issues/18768) Inconsistent behavior of `IO`, `StringIO` and `String` `each_line` methods when return paragraph and `chomp: true` passed (jeremyevans0)

* When using paragraph separator and `chomp`, should String#each_line be changed to chomp paragraphs instead of lines?
* Assuming we should chomp paragraphs in this case, is the pull request acceptable?

Preliminary discussion:
```ruby=
File.write("file", "a\n\nb\n\nc\n")
                      open("file")  .each_line("", chomp: true).to_a #=> What should be returned?
            File.read(open("file")) .each_line("", chomp: true).to_a #=> What should be returned?
StringIO.new(File.read(open("file"))).each_line("", chomp: true).to_a #=> What should be returned?

# current:
#       IO#each_line: ["a", "b", "c\n"]
#   String#each_line: ["a\n", "b\n", "c\n"]
# StringIO#each_line: ["a\n", "b\n", "c"]   (at the report time)
#                     ["a\n", "b\n", "c\n"] (current)
```

* mame: All of them should return the same result. Okay?

* `.each_line("")` #=> `["a\n\n", "b\n\n", "c\n"]`

* mame: What does `.each_line("", chomp: true)` mean?
  1. `.each_line("") {|s| s = s.chomp;     ... }` #=> `["a", "b", "c"]`
  2. `.each_line("") {|s| s = s.chomp(""); ... }` #=> `["a", "b", "c"]`
  3. (Jeremy's interpretation) Remove the separators:
    * The separator in `"a\n\n"` is the last `"\n\n"`
    * The separator in `"b\n\n"` is the last `"\n\n"`
    * There is no separator in `"c\n"`
    * `.each_line("", chomp: true)` #=> `["a", "b", "c\n"]`

Conclusion:

* matz: I agree with Jeremy's interpretation


### [[Bug #18770]](https://bugs.ruby-lang.org/issues/18770) Inconsistent behavior of IO/StringIO's each methods when called with nil as a separator, limit and `chomp: true` (jeremyevans0)

* When using `nil` separator and `chomp` keyword, should IO chomp a final line separator?
* I don't think it should, as `chomp` should only remove separators, and `nil` separator means no separator.
* Assuming we should not chomp in this case, is the pull request acceptable?

Preliminary discussion:

```ruby=
File.write("file", "abc\n\ndef\n\n")
p              File.open("file") .each_line(nil, 2, chomp: true).to_a #=> What should be returned?
p              File.read("file") .each_line(nil, 2, chomp: true).to_a #=> What should be returned?
p StringIO.new(File.read("file")).each_line(nil, 2, chomp: true).to_a #=> What should be returned?

# current:
#       IO#each_line: ["ab", "c\n", "\nd", "ef", "\n\n"]
#   String#each_line: wrong number of arguments (given 2, expected 0..1)
# StringIO#each_line: ["ab", "c\n", "\nd", "ef", "\n"]   (at the report time)
#                     ["ab", "c\n", "\nd", "ef", "\n\n"] (current)

File.write("file", "abc\n\ndef\n")
p              File.open("file") .each_line(nil, chomp: true).to_a #=> What should be returned?
p              File.read("file") .each_line(nil, chomp: true).to_a #=> What should be returned?
p StringIO.new(File.read("file")).each_line(nil, chomp: true).to_a #=> What should be returned?

# current:
#       IO#each_line: ["abc\n\ndef"]   (SHOULD BE FIXED BY JEREMY'S PATCH) => ["abc\n\ndef\n"]
#   String#each_line: ["abc\n\ndef\n"]
# StringIO#each_line: ["abc\n\ndef"]   (at the report time)
#                     ["abc\n\ndef\n"] (current)
```

* Jeremy's interpretation: `chomp: true` should be ignored when the separator is `nil`
  * because there is no separator
  * `IO#each_line` should return `["abc\n\ndef\n"]`

Conclusion:

* matz: I agree with Jeremy


### ~~[[Bug #18905]](https://bugs.ruby-lang.org/issues/18905) :"@=".inspect is non-evaluatable (jeremyevans0)~~

* I don't think this is a bug, since Ruby does not attempt to ensure inspect returns valid Ruby code (e.g. `Object#inspect`).
* Do we want to change it so that `Symbol#inspect` always returns valid ruby code (something you can pass to `eval`)?

Preliminary discussion:

* mame: I agree that it is not a bug, but personally okay to change it.

```ruby
p :'a=b'.inspect #=> ":\"a=b\""
p :'@='.inspect #=> ":@="
```

Discussion:

* nobu: not a bug, but changed it as an improvement

Conclusion:

* nobu: Refined outputs.


### [[Bug #18837]](https://bugs.ruby-lang.org/issues/18837) Not possible to evaluate expression with numbered parameters in it (jeremyevans0)

* I agree with the poster that this isn't a bug, the issue should probably be switched to feature.
* Do we want to change things so that bindings can deal with numbered parameters?

Preliminary discussion:

```ruby
# What the OP wants:
1.times {
  p binding.local_variable_get('_1') #=> expected: 0, acutal: NameError
}

1.times {
  p _1 # Works if there is this line
  p binding.local_variable_get('_1') #=> 0
}
```

* ko1: it is impossible because the argument is not kept (unless `_1` is actually used in the block)
* znz: The meaning of `_1` changes depending on that `_2` is used or not.

```ruby
# current behavior
1.times{
  p _1 #=> 0
  p binding.local_variable_get('_1') #=> 0
  p binding.eval('_1') #=> undefined local variable or method `_1' for main:Object (NameError)
}

1.times{
  p _1 #=> 0
  p binding.local_variable_get('_1') #=> 0
}

1.times{
  p binding.local_variable_get('_1') #=> local variable `_1' is not defined for #<Binding:0x000001ac628d9428> (NameError)
}
```

Discussion:

```ruby
def foo(*)
end

TracePoint.new(:call) {|tp| tp.args }.enable
```

Conclusion:

* matz: it is difficult to support. Give up


### [[Bug #18784]](https://bugs.ruby-lang.org/issues/18784) `FileUtils.rm_f` and `FileUtils.rm_rf` should not mask exceptions (jeremyevans0)

* Should these methods only swallow exceptions if passed files that don't exist?

Preliminary discussion:

```shell
$ mkdir foo
$ touch foo/bar
$ chmod 555 foo

$ ruby -rfileutils -e 'FileUtils.rm_rf("foo")' # expected: an exception, actual: say nothing

$ ls foo
bar

$ rm -rf foo
rm: cannot remove 'foo/bar': Permission denied

$ ruby -w -rfileutils -e 'FileUtils.rm_rf("foo", verbose: true)'
rm -rf foo
```

* ko1: I asked the original author, Aoki-san

https://twitter.com/mineroaoki/status/1549560931364978688
> rm_rfはStandardErrorは無視するという仕様なので仕様だねそれは

https://twitter.com/mineroaoki/status/1549561740098097154
> rmコマンドに合わせるべしっていうのでも別にいいけど……そうすると何を通して何をエラーにするのかPOSIXに当たって全部列挙するはめになるんじゃないかなあ。別案としては、エラーにはしないけど警告を吐くとかはありかも？（警告は出してるような気もしてきた）

Discussion:

* ko1: The original author, Aoki-san, says it is intentional. `-f` means "say nothing" even if any error occurred
* usa: Completely agreed with Aoki-san
* matz: I feel the same
* mame: I expected the directory to be removed after `rm_rf`. If it fails to remove I want to know it
* knu: Agreed that rm_f/rm_rf should raise an error if it failed to remove the file/directory.

```
# man rm on linux
       -f, --force
              ignore nonexistent files and arguments, never prompt
```

```
https://docs.ruby-lang.org/ja/latest/class/FileUtils.html
:force 
真を指定すると作業中すべての StandardError を無視します。

https://ruby-doc.org/stdlib-2.7.0/libdoc/fileutils/rdoc/FileUtils.html
:force
forced operation (rewrite files if exist, remove directories if not empty, etc.);
```

* akr: How about introducing exception: true

```
FileUtils.rm_rf("foo", exception: true) #=> raise an exception
```

* mame, knu, naruse: it should be fixed
* matz, usa: keep it
* knu: The detailed behavior of `rm -rf` should be studied
* knu: `rm_rf` enumerates files of the directory, and then deletes each of them. If a file is removed by another process after the enumeration, does the method report `ENOENT` or ignore it? We need to study https://pubs.opengroup.org/onlinepubs/009604599/utilities/rm.html or the implementation of `rm -rf`
* znz: If there are multiple files that cannot be deleted, what's happen?

```
# GNU/Linux rm tries to remove all removable files.
$ rm -rf /tmp/foo
rm: cannot remove '/tmp/foo/bar': Permission denied
rm: cannot remove '/tmp/foo/d50': Permission denied
rm: cannot remove '/tmp/foo/d30': Permission denied
```

Conclusion:

* matz: This is not a bug. I'm open for change request, but we need more detailed spec, rationale, and compatibility consideration.


---TIMEUP---

The next meeting is scheduled on Aug 2nd

### [[Feature #18885]](https://bugs.ruby-lang.org/issues/18885) Long lived fork advisory API (potential Copy on Write optimizations) (byroot)

- We have some ideas how to improve CoW performance, but we'd need a Ruby side API to notify the Virtual Machine that we're done loading code.
- Something like `RubyVM.prepare`
- Would mostly be useful for forking setups, but could be useful for other long lived programs.
- Currently gems like `nakayoshi_fork` decorate the `Process.fork` method to know that, but it's not always a good assumption.

Discussion:

* akr: How efficient is this? Can anyone provide some experiment data?
* ko1: I don't have. I created nakayoshi_fork just for fun
* akr: How is related to `Process._fork` API? The information of `long_lived` must be passed to the API?
* ko1: It breaks the compatibility. It should invoke four GCs before `_fork` hooks
* mame: What default is good? Are there many use cases for "short-lived" fork?
* ko1: Sometimes a parent process doesn't need repeated compaction before `fork` if the parent doesn't work (only spawn child processes), so `RubyVM.prepare_for_long_lived_fork` can be preferable on this case.
* mame: check memory consumption just after last `fork`?

Conclusion:

* mame: I will give feedbacks.

### [[Feature #18559]](https://bugs.ruby-lang.org/issues/18559) Allocation tracing: Objects created during compilation are attributed to `Kernel.require`  (byroot)

- When tracing allocations, all internal objects created during compilation are attributed to `Kernel.require` or `ISeq.load_from_binary`
- This make it impossible to track down where most of the memory usage of an app comes from.
- Ideally you'd want to insert a frame before compilation, except you need an ISeq to create frame, so there is a chicken and egg problem.
- Instead I think we should handle this special case ad hoc with an override: https://github.com/ruby/ruby/pull/6057

Preliminary discussion:

* ko1: I can accept to change the path (to the required file) but I feel strange to change the lines because it doesn't executed. So `required_path.rb:0` is acceptable for me. Introducing pseudo-frame solves only path (no-path information).
* mame: How to use this information? I don't understand what is so big problem if iseqs are attributed to bootsnap

For the https://github.com/ruby/ruby/pull/6057/files
* parse.y is not considered. (parse.y allocates some object such as literal arrays, which will be attributede to `Kernel.require`)
* For lines, there are some issues:
    * `iseq_compile_each0` is recursive structure but the current doesn't care returned line.
    * `ibf_load_iseq_each` only marked first line.
    * Tracking this kind of information correctly needs huge effort, but I don't think it is valuable comapre with the effort (ko1).

Discussion:


```ruby
# foo.rb
# frozen_string_literal: true
class Foo
  def plop
    "fizz"
      # current: test.rb:3
      # proposal: foo.rb:4 (matz's preference)
      # ko1's preference: foo.rb:0
  end
end
```

```ruby
# bar.rb
class Foo
  def plop
    "fizz" # This is dedup'ed (even though it is in a different file)
  end
end
```

```ruby
# test.rb
require "objspace/trace"
require_relative "foo"
require_relative "bar"
p Foo.new.plop #=> test.rb:3 (current)
p Bar.new.plop #=> test.rb:3 (not test.rb:4)
```

* ko1: I think `foo.rb:0` because I think `foo.rb:4` isn't run.
* matz: I prefer proposal (`foo.rb:4`) because it seems useful.
* mame: With frozen-string-literal, the location of frozen-strings will be `test.rb` or `foo.rb:0`. It is not useful.
* akr: if frozen-string-literal are written in another file, it returns that location. It is known limitation.
* ko1: Also it seems hard to track the correct line number especially for node/iseq/load_iseq. It can be overhead (maybe it is enough small, though). I want to see the performance penalty (I don't think it is not problem)
* nobu: For example, regexp is generated at parse.y.

```ruby
# a.rb
$reg = /foo/

# t.rb
require 'objspace/trace' # t.rb:1

require_relative 'a'     # t.rb:3
p $reg
#=>
objspace/trace is enabled
/foo/ @ t.rb:3
```

Conclusion:

* matz: I like `foo.rb:4`
* ko1: Because the precise implementation for it is hard, I like `foo.rb:0`. I will ask the original poster if the lineno is really important


### [[Feature #18822]](https://bugs.ruby-lang.org/issues/18822) Ruby lack a proper method to percent-encode strings for URIs (RFC 3986) (byroot)

- `URI.escape` was RFC 3986 compliant but was remove in 3.0
- `ERB::Util.url_encode` still exist, but it's very slow compared to `CGI.escape`
- All other methods use `+` for spaces, which isn't RFC 3986 compliant.
- I propose:
  - `CGI.url_encode(" ") # => "%20"`
  - Or `CGI.encode_url`.
  - Alias `CGI.escape` as `CGI.encode_www_form_component`
  - Clarify the documentation of `CGI.escape`.

Preliminary discussion:

* mame: What the OP wants is a fast method to escape a space to `"%20"`
* mame: I am not unsure if it is only one difference

Discussion:

```ruby=
CGI.escape(" ") #=> "+"
CGI.escape("/") #=> "%2F"
CGI.escape("=") #=> "%3D"
```

* akr: (mame: I failed to log because I cannot understand. URI encoding is difficult)
* List of escaped characters by escaping methods in http://www.a-k-r.org/d/d2004_07.html
* (Discussed what character should be encoded or not)
  * `~`
  * `!`
  * `(` and `)`
  * mame: How about following CGI.escape except a space?
    * ???: No

---

* naruse: The proposed behavior (a space is encoded to `%20` instead of `+`, and other characters are handled the same as `CGI.escape`) is reasonable
* akr: `CGI.escapeURI`
* nobu: There are both `CGI.escapeHTML` and `CGI.escape_html` (as an alias)
* naruse the name `escapeURI` is similar to `encodeURI` of JavaScript, but they are different behavior for "/". It's confusing
* akr: Since encodeURIComonent seems based on RFC2xxx, it doesn't encode "!", "(" and ")". But now we have RFC3986 and it says they are not unreserved. We should encode them.
    * https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent
    * > To be more stringent in adhering to RFC 3986 (which reserves !, ', (, ), and *), even though these characters have no formalized URI delimiting uses, the following can be safely used:
* akr: I think the new method should escape everything except unreserved characters in RFC 3986.
* naruse: new URL spec is https://url.spec.whatwg.org/
* naruse: escapeURIComponent seems reasonable. It's slightly different from JavaScript both the name and the mapping and follows the naming convention of cgi library.

Conclusion:

* `CGI.escapeURIComponent(str)` behaves like `CGI.escape`, but `%20` instead of `+` for a space
* `CGI.unescapeURIComponent(str)` is a reverse operation
* Two aliases like `CGI.escape_uri_component` are provided
* No alias `CGI.encode_www_form_component` is needed, but document improvement is welcome

### [[Bug #18911]](https://bugs.ruby-lang.org/issues/18911) `Process._fork` hook point is not called when `Process.daemon` is used (mame)

* akr, why should `Process._fork` have ignored `Process.daemon`?

Discussion:

* akr: For a use case where data connection is terminated, we don't want to call `Process._fork`
* akr: For a use case where we respawn a monitoring thread after fork, we want to call `Process._fork`

Conclusion:

* mame: Let's update the document. I will reply.

### [[Feature #18925]](https://bugs.ruby-lang.org/issues/18925) Add `FileUtils.ln_sr` to create symbolic links relative to link location (nobu)

* Similar to GNU coreutils `ln -sr`.

Discussion:

```
$ ln -s  ../foo/bar baz/ # creates baz/bar as symlink to "../foo/bar"
$ ln -sr    foo/bar baz/ # the same
```

* akr: If `baz/` is a symlink what will happen?
* nobu: It first resolves `baz/` to a real path, and creates a relative path to `foo/bar`

Conclusion:

* nobu: I'll ask aoki-san who is the maintainer of fileutils

### [[Feature #18949]](https://bugs.ruby-lang.org/issues/18949) Deprecate and remove replicate and dummy encodings (eregon)

* What do you think? Could we simplify and speedup Ruby in this area?
* It would lead to speedups for many String methods and simplify semantics.

Conclusion:

* naruse: rejected
* matz: agreed with naruse

### [[Feature #18930]](https://bugs.ruby-lang.org/issues/18930) Officially deprecate class variables (eregon)

* Could at least we discourage them in official docs?
* Could we deprecate them? They often hurt more than help and it is not hard to replace them with instance variables.

```ruby
class SomeThingNeverInherited
  class << self
    def some_config=(value)
      @@some_config = value
    end
  end

  def do_thing
    if @@some_config
      do_one_thing
    else
      do_another_thing
    end
  end
end

SomeThingNeverInherited.some_config = true
SomeThingNeverInherited.new.do_thing #=> do_one_thing
SomeThingNeverInherited.some_config = false
SomeThingNeverInherited.new.do_thing #=> do_another_thing
```

Conclusion:

* matz: I don't deprecate class variables

### [[Feature #17767]](https://bugs.ruby-lang.org/issues/17767) `Cloned ENV` inconsistently returns `ENV` or `self` (nobu)

* nobu: May I prohibit `ENV.clone` since Ruby 3.2?
* matz: Go ahead

```
/srv/gems/aruba-win-fix-0.14.2/spec/aruba/jruby_spec.rb:    @fake_env = ENV.clone
/srv/gems/designshell-0.0.8/bin/designshelld:   :env=>ENV.clone,
/srv/gems/fulmar-shell-1.8.3/lib/fulmar/shell.rb:      env = ENV.clone
/srv/gems/hybrid_platforms_conductor-33.9.5/lib/hybrid_platforms_conductor/core_extensions/bundler/without_bundled_env.rb:      env = ENV.clone.to_hash
/srv/gems/kontena-cli-1.5.4/lib/kontena/cli/stacks/upgrade_command.rb:      prev_env = ENV.clone
/srv/gems/krates-1.7.11/lib/kontena/cli/stacks/upgrade_command.rb:      prev_env = ENV.clone
/srv/gems/panoptimon-0.4.5/spec/passthru_spec.rb:    (env = ENV.clone)['RUBYOPT']='-Ilib'; ENV.stub('[]') {|x| env[x]}
/srv/gems/pipefitter-0.2.0/bin/pipefitter:  Pipefitter::Cli.run(ARGV.clone, :environment => ENV.clone)
/srv/gems/redmine-installer-2.3.2/lib/redmine-installer/redmine.rb:        backup = ENV.clone.to_hash
/srv/gems/rspec-redo-0.1.2/lib/rspec-redo/runner.rb:      env = ENV.clone.tap { |e| e.store 'RSPEC_REDO', '1' }
/srv/gems/rubocop-1.32.0/lib/rubocop/cop/lint/deprecated_class_methods.rb:      #   ENV.clone
```
