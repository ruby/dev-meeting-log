---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-10-20

https://bugs.ruby-lang.org/issues/19040

## DateTime and location

* 2022/10/20 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/11/17 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #12084]](https://bugs.ruby-lang.org/issues/12084) Add `Class#attached_object` (ufuk)

* The proposed method returns the object that the receiver is the singleton class for, and raises `TypeError` if the receiver is not a singleton class.
* There is one particular use-case for this method when doing introspection, with no good workaround available.
* [PR is ready](https://github.com/ruby/ruby/pull/6450) with tests and documentation and issue already has some :+1:.
* Should we have this method for Ruby 3.2?

Preliminary discussion:

```ruby!
obj = Object.new
obj.singleton_class.attached_object.equal?(obj) #=> true

class C; end
C.attached_object #=> a TypeError
```

Conclusion:

* matz: accepted.
* shyouhei: name OK?
* matz: yes.


### [[Bug #19033]](https://bugs.ruby-lang.org/issues/19033) One-liner pattern match as Boolean arg syntax error (jeremyevans0)

* `m(a in b)` is syntax error, similar to how `m(a if b)` is a syntax error
* `m a in b` is a syntax error, but `m a if b` is not (parsed as `m(a) if b`)
* `m((a in b))` and `m((a if b))` are both valid syntax
* Should we attempt to support `m(a in b)`, `m(a if b)`, and/or `m a in b`?

Preliminary discussion:

* mame: `m(a, b, c in ary)` would be ambiguous so I don't think there is a good solution. I propose to keep it as is

Discussion:

* nobu: I don't think it is possible to avoid parser conflicts
* mame: I understand the needs but wonder if it's even possible.
* mame: Would be OK if there could be a sane pull request.
* shyouhei: What would happen if this is implemented?
* mame: `m` would take true/false accordin to the match.

Conclusion:

* matz: once reject. If there is a reasonable patch we may reconsider the proposal


### [[Bug #18998]](https://bugs.ruby-lang.org/issues/18998) `Kernel#Integer` does not convert `SimpleDelegator` object expectly (jeremyevans0)

* I think this behavior is expected and not a bug.
* Should core methods attempt to deal specially with objects using stdlib delegate library?

Preliminary discussion:

* mame: I don't think we should handle delegate specially
* mame: +1 for "not a bug" and keep it as is
* nobu: `Integer(SimpleDelegator.new('0x10'), -1)` returns 16.

Discussion:

```ruby!
require 'delegate'

p Integer('0x10')                      #=> 16
p Integer(SimpleDelegator.new('0x10')) #=> 0

# because Kernel#Integer check if it is T_STRING, but SimpleDelegator instance is not a T_STRING object
```

* knu: I guess there are lots of other similar cases like this.
* knu: This could be rerouted if we called `#is_a?` instead of `FIXNUM_P` etc.  But it would be slow.
* nobu: I wonder if `Kernel#Integer` should try `#to_str`
* matz: I remember I introduced `#to_str` for this very needs.

Conclusion:

* nobu: Let's try `#to_str`


### [[Bug #18983]](https://bugs.ruby-lang.org/issues/18983) `Range#size` for beginless `Range` is not nil. (jeremyevans0)

* `(..object).size` returns Infinity and `(object..).size` returns nil for non-numeric objects
* Should we change `(..object).size` to return nil instead of Infinity?

Preliminary discussion:

The current document

> Returns the count of elements in self if both begin and end values are numeric; otherwise, returns nil

```ruby!
(0..).size       #=> current: Infinity                 matz: Infinity
(..0).size       #=> current: Infinity                 matz: Infinity
(..?a).size      #=> current: Infinity, expected: nil? matz: nil (changed)
(?a..).size      #=> current: nil                      matz: nil
(nil..nil).size  #=> current: Infinity                 matz: nil? (changed?)

(?a..?b).size    #=> current: nil                      matz: nil
```

Discussion:

* mame: I have no strong opinion.
* mame: Current document doesn't tell anything about ranges without ends.
* matz: (described his expectation, written above)
* matz: `(..?a).size` should return nil. I wonder if `(nil..nil).size` also return nil
* mame: How about trying both changes? Let's reconsider it if we face any big problem

Conclusion:

* matz: Let's try changing the two. Will reply.


### [[Bug #18797]](https://bugs.ruby-lang.org/issues/18797) Third argument to `Regexp.new` is a bit broken (jeremyevans0)

* This was discussed in the August 2022 developer meeting, but there was confusion about whether the third argument is ignored or used.
* @matz thought the third argument was ignored, but it is used and results in unexpected behavior.
* Should I update the documentation, or should the third argument be deprecated and removed?

Preliminary discussion:

* mame: let's confirm @matz's intent of "And the third argument is ignored (IIRC)."

Discussion:

* shyouhei: That argument took more litters like `"e"` or `"u"` and they are ignored now.  But `"n"` still is a tihng.

Conclusion:

* matz: Will reply


### [[Bug #19055]](https://bugs.ruby-lang.org/issues/19055) `Regexp.new(regexp, timeout: nil)` is intrupted by `Regexp.timeout` (mame)

* How should we design the timeout keyword to express "no timeout"?
* `Regexp.new(str, timeout: nil)`
* `Regexp.new(str, timeout: 0)`
* `Regexp.new(str, timeout: Float::INFINITY)`
* `Regexp.new(str, timeout: :no_timeout)`

Preliminary discussion:

```ruby!
# current behavior

Regexp.timeout = 1

re = Regexp.new(str, timeout: nil) # re = Regexp.new(str)

re =~ str # raises a Regexp::Timeout in one second
```


* what to decide
  * What API is preferable to force to set "no timeout per instance (cancel the global timeout)"
  * What should happen by `Regexp.new(str, timeout: 1e300)`
  * What should happen by `Regexp.new(str, timeout: 0)`

* mame: no-timeout API
  * `Regexp.new(str, timeout: false)`
  * `Regexp.new(str, timeout: 0)`
  * `Regexp.new(str, timeout: Float::INFINITY)`
  * `Regexp.new(str, timeout: :no_timeout)`

* mame: `Regexp.new(str, timeout: 1e300)` now ignores `timeout: 1e300`
  * should it raise an exception? or degenerate to infinite?
  * ko1: 2^64 nanoseconds are 500 years, so no one should notice the behavior :-)

* mame: `Regexp.new(str, timeout: 0)` now ignores `timeout: 0`
  * it is considered "no timeout per instance" but "respect the global timeout"
  * samuel says it should not be a special case: it may raise an exception immediately
  * ko1: Queue.pop(timeout: 0) means nonblock, not an exception

Existing `io.c` timeout logic:

```clang!
if (timeout == Qnil || timeout == Qundef) {
    timeout = fptr->timeout;
}

if (timeout != Qnil) {
    tv_storage = rb_time_interval(timeout);
    tv = &tv_storage;
}

int ready = rb_thread_wait_for_single_fd(fptr->fd, RB_NUM2INT(events), tv);
```

Discussion:

* mame: `timeout: nil` honours Regexp's global timeout configuration, and that sounds legit.
    * samuel: totally agreed
* mame: eregon wonders if there should be a way to ignore global timeouts per instance, because that should introduce a security concern.

* samuel: can we please be consistent with other timeout behavior/arguments.
    * If we introduced timeout: false, the above C code should change too:
    `if (timeout != Qnil) {` -> `if (RTEST(timeout)) {`

* matz: I think there is no need to allow "no timeout per Regexp instance"
    * samuel: Assuming we had a global `IO.timeout`, I think there are valid use cases for `io.timeout = false` (no timeout, don't use the default if it was specified. But we don't have that at the moment.
    * shyouhei: For IO, it would make sense to wait indefiintely.  But for regexp there can be no needs for that.  Situations can be different.
    * samuel: agree, but maybe confusing for user (inconsistent behaviour from the same parameter name).
    * shyouhei: Yes. Makes sense.

* mame: I'm okay not to allow "no timeout per instance"
* mame: then, what will happen in the following code:

```ruby!
Regexp.new(str, timeout: 1e300)
#=> idea 1: no timeout (with a warning?)
#=> idea 2: max value (2^64 ns) with a warning
#=> idea 3: error

# matz: no timeout (or 500 years is also acceptable), no warning

Regexp.new(str, timeout: Float::INFINITY)
# matz: the same as 1e300

Regexp.new(str, timeout: 0)
#=> idea 1: no timeout (current meaning)
#=> idea 2: raise immediately at the match (never match)
#=> idea 3: raise if backtrack count exceeds internal limit
#=> idea 4: raise immediately at Regexp.new

# akr: I prefer idea 2, and idea 3 is acceptable
# knu: It is not unreasonable to represent 0 as no timeout (unicorn, nginx, etc.)
# akr: Now I prefer idea 4
# matz: Okay for idea 4
# mame: Idea 4 is reasonable, I think

Regexp.new(str, timeout: 1e-10) #== 0 nanoseconds

Regexp.new(str, timeout: -1)  #== 0 (raise an exception)
Thread.new { sleep }.join(-1) #== 0 (no error)
```

- What is the unit of the timeout argument?
  - nobu: Seconds.  Can take Float / Rational to reprsent fractions though.
* duerst: it would be easiest to follow Timeout.rb's API.
* mame: `Kernel#sleep` raises an exception for huge timeout, but `Thread#join` waits infinitely
    * samuel: can we please make the behaviour consistent.

```ruby=
sleep 2 ** 64         #=> `sleep': bignum too big to convert into `long long' (RangeError)
sleep Float::INFINITY #=> `sleep': Inf out of Time range (RangeError)

Thread.new{sleep}.join(1e300)           #=> wait infinity (not 1e300 seconds)
Thread.new{sleep}.join(Float::INFINITY) #=> wait infinity

q = Queue.new
p q.pop(timeout: 1e300) #=> nil (immediately timeout)
p q.pop(timeout: Float::INFINITY) #=> nil (immediately timeout)


require 'timeout'

Timeout.timeout(1e300) do
  sleep
end
#=> `sleep': 1000000000000000052504760255204420248704468581108159154915854115511802457988908195786371375080447864043704443832883878176942523235360430575644792184786706982848387200926575803737830233794788090059368953234970799945081119038967640880074652742780142494579258788820056842838115669472196386865459400540160.000000 out of Time range (RangeError)
```

* shyouhei: Can we agree that at lease these APIs should be consistent?
* duerst: Everything timeout-related seems implemented inside of time.c
* mame: There are other cases where `sleep` (with argument) is used instead of timeout.
* matz: I would prefer `Thread#join`'s one, if we take one as a general behaviour.
* ko1: Maybe we can postpone `Kernel.sleep` etc.  Urgent ones are Queue, Regexp etc that take additional timeout keyword

* samuel: I am happy to help with this, as I deal with this a lot in IO, fiber scheduler, etc. I can help with PR/consistency changes/improvements.

Conclusion:

* Let's say there would be no needs to kill __regexp__ timeout per instance.
* In case timeout is 1e300 that means timeout is 500 yrs or some far future.
* In case timeout is 0,
  * It means non-blocking for IOs.
  * It makes no sense for Regexp. Would just raise exceptions ("idea 4" above).
* Negative timeouts should not be allowed.
```
> $stdin.wait_readable(-1)
(irb):1:in `wait_readable': time interval must not be negative (ArgumentError)
```

### [[Feature #19056]](https://bugs.ruby-lang.org/issues/19056) Introduce `Fiber.annotation` for attaching messages to fibers. (ioquatix)

- Do we accept the proposed interface?

Preliminary discussion:

* mame: `Fiber#name` can be modified like what we do on setproctitle.

Discussion:

* ioquatix: This is for debugging purpose.
* ioquatix: annotation is for what the Fiber is doing right now.  Can be something diverse from its name.
* ioquatix: There can be performance isses if used wrong.

* mame: How about explicitly using an instance variable of a Fiber instance?
```ruby!
f = Fiber.new { }
f.instance_variable_set(:@annotate, "doing something")
f.instance_variable_get(:@annotate)
```
* ioquatix: Instance variable is exactly how it is implemented.
* ioquatix: I need public APIs to encourage libraries to use this way.
* ko1: It sounds async's annotation but not fiber's annotation.  This proposal introduces some new ... rules?  I'm not sure how it affects ruby the language.
* ioquatix: People saying to me that debugging Fibers are hard, and this is a part of my answer.  I believe this is highly valuable.

```c
rsock_connect() {
    ...
    rb_fiber_annotate("Connecting...");
}
```

* ko1: what's wrong with just showing current file and line?
* ioquatix:  That doesn't include necessary information like where the IO is connecting to, ...
* matz: Same concern applies to other objects, esp Threads.
* ioquatix: I don't want to apply it to Objects in general; that doesn't make sense.
* matz: In the pull request there are 3 methods: `Fiber.annotateion` and `Fiber.annotation=` are understandable.  But do we really need `Fiber.annotate` ?
* ioquatix: That's for convenience.
* matz: convenient?
* ioquatix: Yes.
* matz: Can you tell us a bit more about the convenience?
* ioquatix: Sure.
* matz: Can we set some standard for some debugger? I mean we don't have to have any mechanism to force this but,
* ioquatix: It would be nice to have a documentation! Let's make it clear how to use it wisely.



* ko1:
```ruby
class Fiber
    attr :annotation
    
    # current
    def self.annotate(value)
        current.annotation = value
    end
end

module Kernel
    def annotate!(value)
        Fiber.current.anntoation = value
    end
    
    # Optional enable/disable
    def annotate!
        if $DEBUG
            Fiber.current.annotation = yield
        end
    end
    
    # Usage
    annotate!{"Connecting to #{host}"}
    annotate!{"Resolving #{host}"}
    annotate!{"Executing query #{query}"}
    
    def annotate_stack!(message)
        current_message = Fiber.current.annotation
        Fiber.current.annotation = message
        yield
    ensure
        Fiber.current.annotation = current_annotation
    end
end
```

mame:
```ruby
def fetch(host)
  annotate "resolving the hostname #{ host.inspect } by DNS"
  raise
  annotate "connecting to the host: #{ ip }"
  # ...
end

def foo
  annotate "fetching example.net"
  # fetch("example.net")

  annotate "fetching example.com"
  fetch("example.com")
end

foo

# t.rb:3:in `fetch' (resolving the hostname "example.com" by DNS): unhandled exception
#         from t.rb:12:in `foo' (doing the part 2)
#         from t.rb:16:in `<main>'
```

Conclusion:

* matz: Basically agree with the proposal.  You still need to convince me how convenient it is though.


### [[Feature #19062]](https://bugs.ruby-lang.org/issues/19062) Introduce `Fiber#locals` for shared inheritable state. (ioquatix)

- Related to [Feature #19058] Introduce `Fiber.inheritable` attributes/variables for dealing with shared state.
- Can we introduce it?

Preliminary discussion:

```ruby
Fiber.current.locals[:x] = 10

Fiber.new do
  pp Fiber.current.locals[:x] # => 10
end
```

* ko1: Proposed `locals` is not local on a fiber (Fibers in family tree shares the storage) so at least "locals" is not good name. This proposal may request a method to share an object between fibers.

```ruby!
# This is not "local" at all

Fiber.current.locals = {}

Fiber.new do
  Fiber.current.locals[:a] = 1
end.resume

p Fiber.current.locals #=> {:a=>1}

# it can be worked around

Fiber.current.locals = {items: []}

Fiber.new(locals: Fiber.current.locals.dup) do
  Fiber.current.locals[:items] = << 42
end.resume

p Fiber.current.locals[:items] #=> [42]
```

```ruby
Thread.current[:x] # fiber local
Thread.thread_local_set(:x) # thread local

users = Users.find_each
e = Enumerator.new do
  users.each do |user|
    y << user
  end
end

e.next

# => with the proposal

f1 = Fiber.new{

Fiber.current.locals[:x] = ActiveRecord::Base.connection

users = Users.find_each
e = Enumerator.new do
  users.each do |user|
    y << user
  end
end

}

f2 = Fiber.new{
  
Fiber[:x] = ActiveRecord::Base.connection

users = Users.find_each
e = Enumerator.new do
  users.each do |user|
    y << user
  end
end
}

e.next

ExtentLocal<...> x;

with(Class.x = ...) do
    Class.x # => ...
    # dynamic scope - variables bound per stack frame.
end

# dynamic scope - variables bound per fiber.
Fiber.current.locals[:x] = 1

# ENV -> copied to subprocesses.

Fiber.new{
  Fiber.current.locals[:x] = nil # ko1's expect

  Fiber.current.locals[:x] #=> 1
  Fiber.current.locals[:x] = 2
}.resume

p Fiber.current.locals[:x] # 1

# Discovery is bad. Searching for Fiber locals ???
Fiber.current.inherited_table[:x]

Fiber.current.extent_variables[:x]

```

- samuel: After discussing the issue at length, I'm okay with dup for every fiber, but the cost is about 20-30% slower fiber creation. So the behaviour can be avoided.

Discussion:

* ko1: Do you want to share a hash table across Fibers?
* ioquatix: Locals shall be inherited, not shared.
* ko1: "local" sounds strange to me.
* ioquatix: Do you have an alternative?
* ko1: inheritation-based data table maybe?  No good opinion.
* ioquatix: All other implementatios share the "local" terminology.
* ko1: Thread local variables, for instance, won't be shared accross threads.
* ko1: What's wrong with Thread.thread_local_get/set?
* ioquatix: They don't interface with fibers at all, see example above.
* matz: What about "storage"?
* ko1: I understand the proposal now and the needs and the usage seems valid but locals... just doesn't sound right to me.

Conclusion:

* ioquatix: Let's ask others for naming.


### [[Feature #19059]](https://bugs.ruby-lang.org/issues/19059) Introduce top level `module TimeoutError` for aggregating various timeout error classes. (ioquatix)

- @matz can you give your opinion and/or close the issue?

Discussion:

```ruby
module TimeoutError
end

IO::TimeoutError.include(TimeoutError)
Regexp::TimeoutError.include(TimeoutError)

# Maybe?
Timeout::Error.include(TimeoutError)

# Net::OpenTimeout, a subclass of Timeout::Error, is raised if a connection cannot be created within the open_timeout.
```

* matz: Most of the time SomethingError is a class, not a module.
* ioquatix: Yep.  No good solution for that.
* ioquatix: The problem I'm facing is there is no way to catch those timeouts at once.
* matz: Agree with the convenience.
* ioquatix: IO-related errors have the same kind of problems.

```ruby
module Error::Timeout
# module StandardError::Timeout
# Maybe confusing error for classes < StandardError
end
    
module Error::IO
end

rescue Error::Timeout
rescue Error::IO

hosts.each do |host|
    response = request(host)
rescue Error::Timeout, Error::IO
    # Ignore and retry
rescue JSON::ParseError
    # Total failure, give up.
    raise
end
```

* matz: It would be good to have an aggregate module that cathers all timeouts.
* matz: But we need a better name.
* mame: Do we really want to catch only timeout-related error? In practice?
* ioquatix: most other exceptions are non-recoverable.  But timeouts tend to be retryable (same with IO).
* mame: to recover the calculation, maybe the user need to distingush between Regexp::TimeoutError and IO::TimeoutError
* samuel: `Regexp::TimeoutError` could occur because CPU usage spike, it's not an absolute failure of the regexp to match and retrying the operation in theory could work.
* naruse: the use case is not clear to me neither

Conclusion:

* Focus on IO error categorisation and close this one.

### [[Feature #19063]](https://bugs.ruby-lang.org/issues/19063) `Hash.new` with non-value objects should be less confusing. (ioquatix)

- Discuss proposals.

Preliminary discussion:

* mame: as Austin pointed, @shugo uses this idiom intentionally and correctly:

https://github.com/shugo/textbringer/blob/65dff909295f14f466f7b27e824ea36fe1f9ddd7/bin/merge_mazegaki_dic#L5

```ruby!
# Proposals 2 and 3: break shugo's legitimate code

MAZEGAKI_DIC = Hash.new([])

ARGF.each_line do |line|
  # ...
  MAZEGAKI_DIC[key] |= values
end

# Proposal 3 also breaks jeremy's code

class A; end
class B < A; end
class C < A; end
class_map = Hash.new(A)
class_map[:b] = B
class_map[:c] = C

# znz: in future, we can make it more explicit
class_map = Hash.new(A, intentionally_allow_unfrozen_default: true)
```

```ruby=
# Proposal 1 breaks the compatibility a little
h = Hash.new([]) # = Hash.new{|h, k| h[k] = [].dup}
h[:a] # only read access
p h  #=> {}       before the change of Proposal 1
     #=> {:a=>[]} after the change of Proposal 1

h[:b] << "b"
p h #=> {:a=>[], :b => ["b"]}
```

Discussion:

* matz: I see it's difficult to "fix" it without breaking something else...
* mame: I understand the motivation, but I think it is very difficult to change
* mame: `ary = Array.new(10, []); ary[0].equal?(ary[1])`
    * samuel: `Array.new(10, [], dup: true)`???
    * mame: I am neutral to the keyword, but newbies will not be helped because they just don't know the behavior
        * samuel: after introducing the keyword, the non-keyword variant can warn.
        * mame: it is very destructive!!
        * samuel: bugs are very destructive!! =)

```ruby!
# How about:

Hash.new([], assign: true/false)
# or
Hash.new([], dup: true/false) # or copy/clone/???
# or
Hash.new{[]} # Similar to Array.new(10){[]} semantics.
```

shyouhei:
```ruby
count = 0
h = Hash.new {|h, k| h[k.downcase] = count += 1 }
p h[:a] #=> 1
p h[:A] #=> 2
```

Conclusion:

* matz: I don't change anything, I will reply
* matz: I will reject #19069 too


### [[Feature #19061]](https://bugs.ruby-lang.org/issues/19061) Proposal: make a concept of "consuming enumerator" explicit (zverok)

* Introduce an `Enumerator#consuming?` method that will allow telling one of the other (and make core enumerators like `#each_line` properly report they are consuming).
* Introduce `consuming: true` parameter for `Enumerator.new` so it would be easy for user's code to specify the flag
* Introduce `Enumerator#consuming` method to produce a consuming enumerator from a non-consuming one

Preliminary discussion:

```ruby!
[1, 2, 3].each.consuming?   #=> false
$stdin.each_line.consuming? #=> true

Enumerator.new {}.consuming?                  #=> false
Enumerator.new(consuming: true) {}.consuming? #=> true
```

```ruby!
e = Enumerator.new do |g|
  g << 1 << 2 << 3
end
p e.consuming? #=> false
p e.next  #=> 1
p e.to_a  #=> [1, 2, 3] # relaunches the proc
p e.next  #=> 2

p e.rewind
p e.next  #=> 1
p e.next  #=> 2


ary = [1, 2, 3]
e = Enumerator.new(consuming: true) do |g|
  g << ary.shift until ary.empty?
end
p e.consuming? #=> true
p e.next  #=> 1
p e.to_a  #=> [2, 3]
p e.next  #=> interation reached an end
```

* ko1: Is it reasonable for `$stdin.each_line` to return a `ConsumingEnumerator (< Enuemrator)` or something?

```ruby
e = File.foreach("file")
p e.consuming? #=> false

p e.next #=> "first line"
p e.next #=> "second line"
p e.rewind
p e.next #=> "first line"
p e.next #=> "second line"

e = $stdin.each_line
p e.consuming? #=> true

p e.next #=> "first line"
p e.next #=> "second line"
p e.rewind #=> Illegal seek @ rb_io_rewind - <STDIN> (Errno::ESPIPE)
```

ko1: if `Enumerable#consuming` is a key, how about to introduce only `Enumerable#rest`?

```ruby=
e = (1..4).to_enum
e.next #=> 1
e.next #=> 2
e.to_a #=> [1, 2, 3, 4]

e = (1..4).to_enum
e.next #=> 1
e.next #=> 2
rest_e = e.rest #=> Enumerator for the rest elements
rest_e.to_a #=> [3, 4]
rest_e.next #=> 3
rest_e.rewind
rest_e.next #=> 3 is expected (maybe very difficult to implement)
```

```ruby!=
class Enumerator
  def consuming
    source = self
    Enumerator.new { |y| loop { y << source.next } }
  end
end

e1 = (1..5).to_enum
e2 = e1.consuming
e2.next #=> 1
e1.next #=> 2 (is this intentional?)
e2.next #=> 3 (is this intentional?)

e1.rewind

e2.next #=> 1 (rewound, is this intentional?)
```

Discussion:

* knu: I see that an Enumerator can be consuming or not depending on the nature of the underlying data source, and I agree sometimes you want to provide a consuming Enumerator even if the data source to use is on memory (like an array) and the stream is thus reproducible.
* knu: So I support the idea of introducing a feature for creating a consuming enumerator easily.
* knu: But I'm skeptical about the usefulness of the `consuming?` flag as a property at the cost of propagating and computing the flag values every time a new Enumerator is created. (We are already doing the same thing with size())
* matz: I thought Enumerator was basically consuming.
* mame: How about this case?

```ruby
e = [1, 2, 3].each
p e.next #=> 1
p e.to_a #=> current: [1, 2, 3], expected: [2, 3]
```

Conclusion:

* matz: I will ask what is really needed

### [[Bug #19067]](https://bugs.ruby-lang.org/issues/19067) `Module` methods not usable at toplevel under wrapped script (shioyama)

- Similar to #18960
- Based on my understanding of the `wrap` module, this should work, but `top_self` currently extends the `wrap` module.
- Up to 3.2, `wrap` was an anonymous module (or no wrap). But now it can be passed in as an argument.
- This creates some confusion around what methods are callable. I think it should be clarified what is expected.
- Maybe different handling of boolean/module `wrap`?

Discussion:

* matz: The file `foo.rb` cannot be require'ed nor load'ed without target module specification.

Conclusion:

* matz: I will reject


### [[Feature #19068]](https://bugs.ruby-lang.org/issues/19068) Upgrades required Bison version for development (yui-knk)

* Change set is only improvement of yydebug option output, however this upgrades required Bison version. Then want to confirm it's acceptable.

Discussion:

* matz: What is the new minimum version?
* yui-knk: I want bison 2.3.b+ and later at least, but if possible I want bison 3 and later
* matz: okay, please specify the minimum version requirement
* yui-knk: 3.0.0

Conclusion:

* matz: accepted


### [[Feature #19070]](https://bugs.ruby-lang.org/issues/19070) Enhance `keep_tokens` option for `RubyVM::AbstractSyntaxTree` parsing methods

Discussion:

* ko1: Do we need `keep_tokens` option? How about enabling it by default

Conclusion:

* matz: looks good, accepted

### [[Feature #19066]](https://bugs.ruby-lang.org/issues/19066) Enable Scorecard Github Action (duerst)

- I don't know the details, but at first sight, it seems worth doing

Conclusion:

* matz: hsbt-san, could you please give it a try?

### [[Feature #18639]](https://bugs.ruby-lang.org/issues/18639) Update Unicode data to Unicode Version 15.0.0

* [[Bug #19007]](https://bugs.ruby-lang.org/issues/19007) Unicode tables differences from Unicode.org 14.0 data