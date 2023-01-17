---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-01-13

https://bugs.ruby-lang.org/issues/18399

## DateTime and location

* 2022/01/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/02/17 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* 3.1.1?
  * The end of January? Too soon?

* 3.2.0 preview 1?
  * After WASI branch (see below) is merged.

## Check security tickets

[secret]

## A look-back on the 3.1.0 release

https://hackmd.io/qUbiOiyRQXibCfLRbzS9Og

### rubygems test issues

Not yet, hsbt will inform rubygems team.

### We want to un-bundle libffi

* usa: Found that vcpkg has libffi
* nobu: ext/libffi/extlibs can be eliminated.
* naruse: What about libyaml?
* usa: It's also in vcpkg.  But I guess we have different reasons to bundle it.
* aaron: Trying to remember the reason for bundled libyaml.

### Re: RUBY_DEVEL

#### Is it okay to make `RUBY_ON_BUG` default on? (Not doing so now for security considerations)

* ko1: This environment variable is used to spawn arbitrary external process when rb_bug() hapens.
* ko1: CI being different from production is a problem.
* naruse: Would be better if we can reduce effects of RUBY_DEVEL.
* naruse: There are other environment variables like RUBYOPT.  Security problem, if any, should not be increased by this.
* ko1: https://bugs.ruby-lang.org/issues/18483

### rdoc is slow because of its makrdown parser.

* mame: What we can fix in rdoc are fixed already.
* naruse: Evan Phenix is actively maintaining kpeg.gem
* aycabta: Okay I'll try hard with .kpg

### Do we still need CHANGELOG?

* matz: _I_ do.
* naruse: Would be better to put the generated one to S3.

### Do we still need patch levels?

* Seems not.
* 3.2 will not have patch levels.
* https://bugs.ruby-lang.org/issues/18513

## Ordinary tickets

### [[Feature #18481]](https://bugs.ruby-lang.org/issues/18481) Porting YJIT to Rust (request for feedback)

* Devs are basically positive.
* matz: I'm negative to use rust other than YJIT because I don't know Rust well now. I'm afraid if it depends other libraries. But using Rust for YJIT is acceptable.

### [[Feature #18462]](https://bugs.ruby-lang.org/issues/18462) Proposal to merge WASI based WebAssembly support (mame)

* Let's support wasm. This will allow MRI to work on browsers and edge computing platforms.

Discussion:

* katei-san (who is the author of WASM/WASI port) explained the implementation approach

(mame's understanding)

* advantage
  * CRuby works on edge computing environment (such as Fastly)
  * CRuby works on browsers
    * Emscripten port is also useful, but katei-san's WASI port is more "neat", e.g., Fiber works

* current status
  * There are some technical difficulties, but almost all is solved
  * Almost all Ruby features works as far as reasonably possible
    * Main unsupported feature is Thread

* discussion
  * Is it able to maintain?
  * matz: we can give katei-san a commit bit
  * katei: Okay

Conclusion:

* matz: Okay.
* ko1: We need a port maintainer.
* matz: WDYT @katei?
* katei: Will do.

### [[Misc #18285]](https://bugs.ruby-lang.org/issues/18285) NoMethodError#message uses a lot of CPU/is really expensive to call (mame)

* mame: This was postponed at Ruby 3.1. Do we go forward?

```ruby=
42.time { }

#=> current:   undefined method `time' for 42:Integer (NoMethodError)
#=> proposal1: undefined method `time' for #<Integer:0x55> (NoMethodError)
#=> proposal2: undefined method `time' for Integer (NoMethodError)
#=> proposal3: undefined instance method `time' for Integer (NoMethodError)
#=> proposal1: undefined method `time' for object Integer (NoMethodError)

class Foo
  privatee
end

#=> current:    undefined local variable or method `privatee' for Foo:Class (NameError)
#=> proposal1:  undefined local variable or method `privatee' for #<Class:0xXXXXXXXXXXXXXXXX> (NameError)
#=> proposal1': undefined local variable or method `privatee' for Foo (NameError)
#=> proposal2:  undefined local variable or method `privatee' for Foo (NameError)
#=> proposal3:  undefined local variable or singleton method `privatee' for Foo (NameError)
#=> proposal4:  undefined local variable or method 'privatee' for class Foo (NameError)

Foo.new.foo
#=> current:    undefined method `foo' for #<Foo:0x00000279f8fd1320> (NoMethodError)
#=> proposal1:  undefined method `foo' for #<Foo:0x00000279f8fd1320> (NoMethodError)
#=> proposal1': undefined method `foo' for #<Foo:0x00000279f8fd1320> (NoMethodError)
#=> proposal2:  undefined method `foo' for Foo (NoMethodError)
#=> proposal3:  undefined instance method `foo' for Foo (NoMethodError)
#=> proposal4:  undefined method `foo' for object Foo (NoMethodError)

def (o=Object.new).foo
end

o.bar
#=> proposal4:  undefined method `bar' for extended object Object (NoMethodError)

Foo.bar
#=> current:    undefined method `bar' for Foo:Class (NoMethodError)
#=> proposal1:  undefined method `bar' for #<Class:0xXXXXXXXXXXXXXXXX> (NoMethodError)
#=> proposal1': undefined method `bar' for Foo (NoMethodError)
#=> proposal2:  undefined method `bar' for Class (NoMethodError)
#=> proposal3:  undefined singleton method `bar' for Foo (NoMethodError)
#=> proposal4:  undefined method `bar' for class Foo (NoMethodError)
```

* mame: proposal1' handles Module specially. At the previous meeting, many committers liked proposal1' (as far as I recall)
* usa: I like proposal3 or proposal1'
* akr: if the object has singleton method, should show some special message
* akr: I like proposal4
* usa: hmm, proposal4 seems good (but `object Foo` is a little odd, `an object of Foo` or `an instance of Foo` is more confortable)

Conclusion:

* continue to discuss in the ticket

### [[Feature #17881]](https://bugs.ruby-lang.org/issues/17881) Add a Module#const_added callback (mame)

* mame: This was postponed at Ruby 3.1. Do we go forward?

Conclusion:

* matz: go ahead


### [[Feature #18367]](https://bugs.ruby-lang.org/issues/18367) Stop the interpreter from escaping error messages (mame)

* At the previous dev-meeting, we decided to survey the current status of how modern terminal emulators handle untrusted escape sequences.
* I surveyed rxvt, Eterm, xterm, VTE (gnome-terminal), Windows Terminal, and iterm2, and all of them have addressed known vulnerabilities.
* Also, according to some comments in source code, terminal authors are completely aware that this kind of vulnerabilities should be addressed on terminal emulator side.
* I believe there is already a concensus that this kind of security issues should be handled by terminal emulators, not Ruby.
* So, let's remove the escape!

Conclusion:

* mame: I'd like to commit the proposed migration
  * When an error message does not include a control character, no escaping is applied.
  * When an error message does include a control character, "Warning: this error message is currently escaped because it includes a control character(s), but this will not be escaped in Ruby 3.X" is printed, and the escaping is applied.
* matz: go ahead

### [[Feature #18438]](https://bugs.ruby-lang.org/issues/18438) Add `Exception#additional_message` to show additional error information (mame)

* I'd like to apply error_highlight more exceptions than NameError, but it will break some existing tests that check the return value of `Exception#message`.
* This feature allows did_you_mean and error_highlight to add their suggestions without modifying the return value of `Exception#message`.
* To be honest, I'm unsure if this is a right way. I'd like to hear opinions from committers.
* I'll explain some details in the meeting (prefix, needed cooperation, the API style between `additional_message` and `description`, etc.)

Preliminary discussion:

```ruby=
class Exception
  def additional_message(highlight: false) = ""
end

class MyError < StandardError
  def message = "my error!"
  def additional_message(highlight: false) = "#{ super }\nThis is\nan additional\nmessage" # the interpreter adds `| ` to /^/
end

raise MyError
```

```
$ ./miniruby test.rb
test.rb:6:in `<main>': my error! (MyError)
| This is
| an additional
| message
```

* byroot and some people like `Exception#description` approach

```ruby=
class Exception
  def description(highlight: false, **) = escape(message)
end

class MyError < StandardError
  def message = "my error!"
  def description(highlight: false, **) = "#{ super }\n| This is\n| an additional\n| message"
end

raise MyError
MyError.new.full_message(error_highlight: false)

class SyntaxError < Exception
  def description(**)
    "foo"
  end
end
```

```
$ ./miniruby test.rb
test.rb:6:in `<main>': my error! (MyError)
| This is
| an additional
| message
```

Discussion:

* matz: `additional_message` is not good name.
* matz: I like the API style of `description`.
* ko1, mame: Do you like `description`?
* matz: Hmm.
* ko1, usa: Does `highlight: false` mean to kill error_highlight?
* mame: No. `highlight:` keyword argument expects to omit escape sequences, but error_highlight does not use escape sequences.
* matz: I'm unsure if `description` is between `message` and `full_message`

```ruby=
class Exception
    # default implementation of error message methods
    def message = @message
    def description = message
    def full_message = <<~EOM
        #{description}

        #{backtrace....}
    EOM
end
```

* aycabta: `pretty_message` like `git log`?
* mame: `power_message`?

Conclusion:

* matz: please wait.  the API is OK, but the name `description` is not enough good.

---

Discussed at 2022-01-28

* mame: eregon suggested "detailed_message"
* matz: Accepted.

```ruby
class Exception
    # default implementation of error message methods
    def message = @message

    def detailed_message(highlight: false, **) = <<~EOM
        #{message}
        #{did_you_mean/error_highlight suggestion if any}
    EOM

    def full_message(highlight: Exception.to_tty?, **) = <<~EOM
        #{detailed_message(highlight: highlight, **)}

        #{backtrace....}
    EOM
end

# interpreter will show detailed_message and backtrace
# (alternatively, interpreter can show full_message, but it is a different topic)
```

* ko1: What's the default of highlight keyword?

```
$ ruby -e 'p StandardError.new.full_message'
"-e:1:in `full_message': \e[1mStandardError (\e[1;4mStandardError\e[m\e[1m)\e[m\n"

$ ruby -e 'p StandardError.new.full_message' 2> /dev/null
"-e:1:in `full_message': StandardError (StandardError)\n"
```

```ruby
class MyError < StandardError
  # full_message-style
  def detailed_message(highlight: Exception.to_tty?, **)
    "#{ message }\n\nadditional message"
  end

  # akr's suggestion
  def detailed_message(highlight: false, **)
    "#{ message }\n\nadditional message"
  end
end
```

* ko1: The current rdoc of `Exception#full_message` looks wrong. Should be fixed

```
 * If _highlight_ is +true+ the default error handler will send the
 * messages to a tty.
```


### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) `Range#step` semantics for non-`Numeric` ranges (zverok)

* Is there a reason why `(timestamp1...timestamp2).step(3.hours)` is not what `#step` supports?
* If there is, can there be a new method introduced with this semantic?

Preliminary discussion:

```ruby=
require "time"

# Should each of these be allowed?
(Time.parse('2021-12-01')..Time.parse('2021-12-24')).step(n) { ... }
(Time.parse('2021-12-01')..Time.parse('2021-12-24')).step { ... }
(Time.parse('2021-12-01')..Time.parse('2021-12-24')).each { ... }
(Date.parse('2021-12-01')..Date.parse('2021-12-24')).step(n) { ... }
```

1. Define `Time#succ` and call it repeatedly (slow)
2. Call `begin_object#+` with a given step repeatedly (only when `Range#step` accepts a step argument and the receiver type is not special)
3. Let `Range#step` handle `Time` specially (but `Date` is not handled well)
4. Call `begin_object#upto` (nobu)

Discussion:

* mame: as case 4, how the given step effects?
* shyouhei: Currently Range#step has only special cases (Integer, Float, String). This proposal adds a general case. But the case is incompatible with already-existing special cases.
* knu: Delegation should be the OO way, but upto generally doesn't have an exclusive option, right?
* usa: String#upto has an exclusive flag!

```ruby=
class String; def succ = self; end

("a".."z").step(3).take(3)  #=> ["a", "d", "g"]
("a".."zz").step(3).take(3) #=> ["a", "a", "a"]

# knu's expectation
("a"..).step("a").take(3) #=> ["a", "aa", "aaa"]
```

* matz: we should not modify the behavior when the receiver is a String
* mame: The following behavior will be allowed. Is it okay?

```ruby
([]..).step([1]).take(3) #=> [[], [1], [1, 1]]
```

* knu: If the above behavior is allowed, the special handling of String cases may be confusing

```ruby
# knu's expectation (again)
(''..).step('*').take(3) => '', '*', '**'
```

* usa: the step argument should be restricted to numeric like objects, I think.

* knu: `Range#sum` simply call `+`.

```ruby
p ('a'..'z').sum('')
#=> "abcdefghijklmnopqrstuvwxyz"
```

```ruby
p (Time.now .. (Time.now+10)).sum(Time.now)
#=> t.rb:1:in `each': can't iterate from Time (TypeError)
```

* akr: How about Set?

```ruby=
(Set[1]..Set[3]).step(Set[2]) #=> infinite loop?
# Set[1], Set[1,2], Set[1,2], ...
```

* matz: I'd like to allow numeric-type '+', but to deny concatination-type '+'

Conclusion:

* matz: we should not modify the behavior when the receiver is a String
* matz: I'm okay to allow `(timestamp1...timestamp2).step(3.hours)`
* matz: but I'd like to prohibit `([]..).step([1]).take(3)`
* matz: I don't come up with a good spec to meet the above conditions
  * matz: Should we specially handle only Time's range?
  * mame: But it is very natural to want the same behavior for Date's range


### [[Bug #15928]](https://bugs.ruby-lang.org/issues/15928) Constant declaration does not conform to JIS 3017:2013 (jeremyevans0)

* This was discussed at the May 2021 developer meeting, and it was decided to wait until after 3.1.
* This makes constant assignment consistent with attribute assignment, so evaluation occurs left-to-right.
* Is this OK to commit now?

Preliminary discussion:

* ko1: looks good

Discussion:

```ruby
expr::C = rhs
```

* Expectation (and JIS spec)
  * expr
    * raise TypeError if expr is not a module
  * rhs
  * assign

* Current
  * rhs
  * expr
    * raise TypeError if expr is not a module
  * assign

```ruby=
expr.c = rhs
# current (and knu's expectation) 1. expr, 2. rhs, 3. call c=
expr::c = rhs
```

Conclusion:

* matz: I think it is time for a change


### [[Bug #17545]](https://bugs.ruby-lang.org/issues/17545) Calling dup on a subclass of Proc returns a Proc and not the subclass (jeremyevans0)

* Is this OK to commit this now?

Preliminary discussion:

* ko1: the same as the prvious topic

Conclusion:

* matz: go ahead


### [[Bug #16908]](https://bugs.ruby-lang.org/issues/16908) Strange behaviour of Hash#shift when used with `default_proc`. (jeremyevans0)

* This was discussed in the March 2021 developer meeting, and it was decided to wait until after 3.1.
* I've added a pull request to make `Hash#shift` return nil if the hash is empty.
* Is this OK to commit this now?

Preliminary discussion:

* mame: matz has already approved it

Discussion:

```ruby
hash = Hash.new{|h,k| h[k] = 0}

hash.shift # => 0
hash.shift # => [nil, 0]
```

Conclusion:

* matz: go ahead


### [[Bug #16663]](https://bugs.ruby-lang.org/issues/16663) Add block or filtered forms of Kernel#caller to allow early bail-out (jeremyevans0)

* @headius prefers iteration over search, and recommends `Thread.each_backtrace` as the method name.
* @headius also has an idea for a block form that returns an enumerable, but I think that would greatly increase complexity.
* I don't think `each_backtrace` is a good name for a method that yields individual caller locations.
* I would like to go forward with `Thread.each_caller_location` or `Kernel.each_caller_location` (singleton method only), yielding `Thread::Backtrace::Location` objects.  Is that acceptable?

Preliminary discussion:

* mame: +1 for `Thread.each_caller_location`
* ko1: +1 too.

Discussion:

```ruby=
def foo
  Thread.each_caller_location do |frame|
    break frame
  end
end
```

* matz: `Thread.each_caller_location` is an acceptable name
* knu: What does the method return? nil?
* knu: nil looks good enough. We can later change the return value if needed

```ruby
def foo
  Thread.each_caller_location #=> block not given (LocalJumpError)
  Thread.each_caller_location.drop_while {|frame| frame is an internal path }
end

e = foo
e.each { ... }
```

* mame: Jeremy proposes not to support the generator usage
* nobu: What will happen if we do `Thread.to_enum(:each_caller_location)`?
* usa: it should work as is
* knu: if we cannot use it as a generator, the name `each_*` might not be good

```ruby
def foo
  e = Thread.to_enum(:each_caller_location)
  e.next
  e
end

e = foo
e.next # Is it safe?
```

* mame: I think returning anything is okay, but should not cause SEGV
* mame: each_caller_location is called under a new Fiber, so maybe it returns an empty array or something

Conclusion:

* matz: accepted. The generator usage should be clearly prohibited in the document


time up; we will continue 28th Jan.

---

### [[Bug #11064]](https://bugs.ruby-lang.org/issues/11064) #singleton_methods for objects with special singleton_class returns an empty array (jeremyevans0)

* It is a bit inconsistent that `singleton_method` for `nil`/`true`/`false` returns a Method, but `singleton_methods` does not include the method.
* Is this a bug, or expected behavior?

Preliminary discussion:

```ruby=
def nil.bla = 42
p nil.singleton_methods #=> []

def (o = Object.new).bla = nil
p o.singleton_methods #=> [:bla]
```

* nobu: ignore or how about this?

```ruby=
class NilClass
  def singleton_methods
    NilClass.instance_methods(false)
  end
end
```

* mame:

```ruby
p NilClass.singleton_class? #=> false
```

* ko1: solve with?

```ruby
NilClass = nil.singleton_class
```

* mame: I recommend to ignore related issues. No change.

Discussion:

*

Conclusion:

*

### [[Feature #18408]](https://bugs.ruby-lang.org/issues/18408) Allow pattern match to set instance variables (Dan0042)

* Currently, `expr => @var` is not supported.
* It doesn't seem like there's a specific reason for that limitation; `rescue => @var` works fine.
* I think ivars should be supported, it would be one less gotcha to learn.
* The same might apply to `@@var` and `$var` ?

Preliminary discussion:

* nobu: `expr => foo.writer` or `expr => ary[0]`?
* mame: If ivar is allowed as a pattern, the following will be valid
```ruby
case 42
in @forty_two
  p @forty_two #=> 42
end

case 42
in ary[0] # looks invalid
end

case 42
in Constant # conflict
end
```

* ko1: If ivar (and other lhs) is allowed only in the target of `=>` pattern, the following will be valid
  * But `expr => @a` is invalid

```ruby=
case obj
in [4, 2] => @a
end
    
1 => _ => @a
# is equivalent to
case 1
in _ => @a
end
```

* ko1: Another idea. ivar (and other lhs) is allowed only in the target of a right assignment (not a pattern) (but I don't like it)

```ruby=
# specially allowed
ary => @a

# also specially allowed??
ary => @a, @b, @c
```

Discussion:

* matz: Umm
* akr: What can not be allowed in the right side of right assignment?

```ruby=
# Constant: Currently this means `case expr; in Constant; else raise; end`
expr => Constant

# writer method: Is it possible to implement the parser?
expr => obj.foo
expr => ary[0]
```

* matz: I would forgive you if I could. However, the disadvantages that come with it seem to be too great for the benefits. Let's give up.

Conclusion:

* matz: Will reply


### [[Feature #18136]](https://bugs.ruby-lang.org/issues/18136) `Enumerable#take_while_after` (zverok)

* Many real-life use-cases provided
* Two alternative names provided: `take_while_after` and `drop_after`
* Is there any way this can be moved forward?

Preliminary discussion:

```ruby=
a = [1, 2, 3, 4, 5]
p a.take_while {|i| i < 3 }         # => [1, 2]
p a.take_while_after {|i| i < 3 }   # => [1, 2, 3]

(1..10).each_slice(3).take_while { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
(1..10).each_slice(3).take_while_after { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]

(1..10).each_slice(3).take_until { _1.size != 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]

(1..10).each_slice(3).drop_after { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]

# mame: drop resembles Enumerable#drop which discards the first arguments

(1..10).each_slice(3).take_upto { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]

# ko1: I feel `take_upto` is equal to `take_while`

(1..10).each_slice(3).take_while(include_first_false_element: true) { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]
```

```ruby
# nobu
def upto i, n
  return if i > n
  begin
    yield i
    i = i.succ
  end until i == n
end

# ko1
def upto i, n
  while i <= n
    yield i
    i = i.succ
  end
end
```

* mame: Different people imagine different behaviors from the name `take_upto {}`
  * See the following examples

```ruby=
# ko1's expectation
(1..10).each_slice(3).take_upto { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# nobu (and znz)'s expectation
(1..10).each_slice(3).take_upto { _1.size != 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]

# mame's expectation (note that the condition is flipped)
(1..10).each_slice(3).take_upto { _1.size == 3 }
# => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]
```

Discussion:

* matz: I thought take_upto was good, but it indeed seems confusing.
* matz: The name "drop_after" is acceptable to represent the behavior, but I'm not convinced the need.
* ko1: How about adding a keyword argument to take_while?
* matz: I'm not positive because the while statement does not have such option.

Conclusion:

* matz: Will reply

### [[Feature #16122]](https://bugs.ruby-lang.org/issues/16122) `Struct::Value`: simple immutable value object (zverok)

* the concept was somewhat favorably discussed a long time ago, but since then no reiterations were made.
* I provided the extended description and rationales and answered the questions that were raised.
* Is it possible to move forward with the idea?

Preliminary discussion:

```ruby
Struct.new(
  :foo,
  keyword_init: nil,   # will no longer be needed in near future
  writers: true,       # define `foo=` or not
  enumerable: true,    # include Enumerable or not
  hash_accessor: true, # defines values and [] (and []= when writer is true)
)
```

Discussion:

* knu: I'm negative to add keywords to Struct.new. I wonder if it is a good idea to extend Struct ad-hocly
* knu: I expect `Struct` class should have same features like Enumerable, `#[]` accessors.
* knu: I don't want to see "various" Structs.  We should instead offer a simple data type without bells and whistles and let users add whatever they want.
* matz: I somewhat agree with knu. Having "various" Structs looks not good
* matz: I'm positive to ad the original proposal as the simplest version, but I am unsure if the name `Struct::Value` is good
* ko1: There is one more addition to the proposal; allowing zero-member struct. `Struct[]`
* nobu: That should be a separate issue.

* mame: What is the proposal exactly?
  * Do not include: `writer=`, `each` and Enumerable, `to_a`, `each_pair`, `values`, `[]`, `[]=`, `dig`, `members`, `values_at`, `select`, `filter`, `size`
  * Should include: `reader`, `deconstruct_keys`, `deconstruct`, `==`, `eql?`, `hash`
  * ko1: I want `members` and `size` for debugger
  * knu: You can `obj.class.members` and `obj.class.members.size`.
  * knu: I kind of feel you'd want `to_h` in order to export a struct value. => On second thought, each struct class should define to_h on its own only if necessary.
  * nobu: How about adding `get` instead of `[]`? And `fetch`? I'm afraid if no feching method is okay
  * knu: You have to use `send(:foo)` to fetch the value, and that's fine because you generally don't need to do that when you know what the members are.
  * matz: I don't see why `fetch` is needed when there's no aref.

Conclusion:

* matz: Positive to the proposed version of Struct (below). Need to continue to discuss the name.
  * Do not include: `writer=`, `each` and Enumerable, `to_a`, `each_pair`, `values`, `[]`, `[]=`, `dig`, `members`, `values_at`, `select`, `filter`, `size`, `to_h`
  * Should include: `reader`, `deconstruct_keys`, `deconstruct`, `==`, `eql?`, `hash`

### [[Bug #18294]](https://bugs.ruby-lang.org/issues/18294) error when parsing regexp comment (Martin Dürst) (duerst)

- We should decide whether this is spec, or to make parsing easier, or a bug.

Preliminary discussion:

* mame: https://bugs.ruby-lang.org/issues/18449 is this related? -> not related directly

```ruby=
_re = /
  foo  # \M-ca
/x

# Ruby 3.0 or ealier
t.rb:3: too short escaped multibyte character: /
  foo  # \M-ca
/x

# Ruby 3.1
t.rb:3: too short escaped multibyte character: /
  foo  # \xE3a
/x
```

* nobu: will later investigate what's happening

Discussion:

* nobu: This exception is raised in Ruby side, not onigmo. Onigmo does no such check.
* nobu: If we want to fix this, Ruby need to parse the regexp and check if the invalid character is in a comment. Or we need to modify the source code of onigmo.
* akr: I hope it is fixed, but it looks too bothersome to fix.
* knu: This is definitely a bug, but I'm not sure if it's worth all the trouble to fix it.

Conclusion:

* matz: Will reply

### [[Feature #18370]](https://bugs.ruby-lang.org/issues/18370) Call Exception#full_message to print exceptions reaching the top-level (mame)

* nobu: The current default error handler prints error message incrementally. But full_message creates the whole string of error message. So if the backtrace is huge (and not recusive), this proposal makes the error printing slow
* akr: I don't like it
* mame: If the max length of backtrace is about 10,000, and each line is about 100 bytes, so the message is about ~1MB
* matz: Nowadays it does not matter, I think
* ko1: I think it is okay to give it a try. We can revert if it brings a practical problem
* mame: I (or nobu) will create a PR. If there is no problem, give it a try

Conclusion:

* matz: Will reply

## [[Feature #18490]](https://bugs.ruby-lang.org/issues/18490) `MakeMakefile.pkg_config` should accept multiple options

* matz: Will accept
