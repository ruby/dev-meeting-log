---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-06-16

https://bugs.ruby-lang.org/issues/18803

## DateTime and location

* 2022/06/16 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/07/21 (Thu) 13:00-17:00 JST @ Online

## Announce

* shyouhei: RubyKaigi CFP is now open! We want YOU be there!

## About release timeframe

* no concrete plan yet.

## AWS Cost check

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18806]](https://bugs.ruby-lang.org/issues/18806) protected methods defined by refinements can't be called (shugo)

* I prefer "1. Treat defined methods as though they were defined on the refined class. "
* The pull request looks fine: https://github.com/ruby/ruby/pull/5966

Preliminary discussion:

```ruby=
class A
end

module MyRefine
  refine(A) {
    private def private_foo = :refined
    def private_foo_in_refinement = private_foo

    protected def protected_foo = :refined
    def protected_foo_in_refinement = protected_foo
  }
end

class A
  using MyRefine

  def call_private = private_foo
  def call_private_through_refinement = private_foo_in_refinement

  def call_protected = protected_foo
  def call_protected_through_refinement = protected_foo_in_refinement
  def is_defined = defined?(protected_foo)
end

A.new.call_protected
# => actial: NoMethodError: protected method `protected_foo' called for #<A:0x00007f23f35e9390>
# => expected option1 and 2: :refined

A.new.call_protected_through_refinement
# => NoMethodError: protected method `protected_foo' called for #<A:0x00007f23f35e9390>
# => expected option1 and 2: :refined
      
A.new.is_defined
# "method"
```

Options:
1. Treat defined methods as though they were defined on the refined class. Both examples above will now work, and it is possible to call the refined method on objects other than self.
2. Always allow "fcalls" to protected methods. Making them basically an alias for private when used in a refinement.
3. Forbid using protected inside a refinement (and Refinement#import_methods), raising an error.

* ko1: who use `protected`?

Discussion:

* shugo: Option 1 is natural for me. I'm not sure `protected` in refinement is used.
* jhawthorn: I prefer option 1, but option 2 also fine. I don't think anyone will use this code, but we need a defined behaviour for implementation.
* shugo: The function name in PR is not clear, so I'll comment on it.

Conclusion:

* Matz: I accept option 1.


### [[Bug #18813]](https://bugs.ruby-lang.org/issues/18813) Module#autoload isn't strict about the autoloaded constant (fxn)

* `module M; autoload :OpenSSL, "openssl"; end` works but is inconsistent.
* `M.constants(false) # => [:OpenSSL]`
* `M::OpenSSL # => ::OpenSSL`
* `M.constants(false) # => []`
* Should we break it? deprecate it? do nothing?
* There are some important gems relying on this currently, including `bundler`.

Preliminary discussion:

```ruby=
# bar.rb
Bar = :bar

# foo.rb
class Foo
    autoload :Bar, 'bar'
    p Bar #=> ::Bar => :bar 
    # ::Foo::Bar is not defined!!
end
```

```ruby
module MyGem
  autoload :OpenSSL, 'openssl'
end
```

```
/srv/gems/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l32/lib/ruby/2.2.0/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/l64/lib/ruby/2.2.0/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/osx/lib/ruby/2.2.0/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/rb2exe-0.3.1/bin/traveling-ruby-2.2.2/win/lib/ruby/2.2.0/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/rhodes-7.4.1/lib/extensions/net-http/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/lib/net/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/rubysl-net-http-2.0.4/lib/rubysl/net/http/http.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/tauplatform-1.0.3/lib/extensions/net-http/net/http.rb:  autoload :OpenSSL, 'openssl'

/srv/gems/scrypt-ruby-2.0.2/lib/scrypt-ruby.rb:  autoload :OpenSSL, 'openssl'
/srv/gems/simpleblockingwebsocketclient-0.43/lib/ws.rb:  autoload :OpenSSL, 'openssl'
```

```
$ gem-codesearch '\s+autoload.+[\''\"]date[\''\"]'
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_autoload.rb:    autoload :Date, "date"
```

```
ko1@aluminium:~$ gem-codesearch '\s+autoload.+[\''\"]open-uri[\''\"]'
/srv/gems/argon-1.3.1/vendor/bundle/ruby/2.7.0/gems/thor-1.0.1/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/bundler-2.3.15/lib/bundler/vendor/thor/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/colorcode_convert_rgb-0.1.2/vendor/bundle/ruby/2.5.0/gems/thor-1.0.1/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/date_n_time_picker_activeadmin-0.1.2/vendor/bundle/ruby/2.6.0/gems/thor-1.1.0/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/rubygems-update-3.3.15/bundler/lib/bundler/vendor/thor/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/symbolic_enum-1.1.5/vendor/bundle/ruby/2.7.0/gems/thor-1.0.1/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/tdiary-5.2.2/vendor/bundle/ruby/3.1.0/gems/thor-1.2.1/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
/srv/gems/thor-1.2.1/lib/thor/runner.rb:  autoload :OpenURI, "open-uri"
```

https://github.com/rubygems/rubygems/blob/a9e305432955f56b847881187a7e01a97b6901e3/lib/rubygems/version.rb#L155-L156
```
class Gem::Version
  autoload :Requirement, File.expand_path('requirement', __dir__)
  #=> defines Gem::Requirement
...
```

Discussion:

* nobu: we changed constant lookup, autoload is left behind.
* mame: I don't think constant lookup is related here.
* mame: it seems several gems use this in-module autoload feature.
* shyouhei: How about starting with a deprecation warning?
* shyouhei: I wonder if this change is really needed or not
* mame: I wonder if this is an actual issue in any real-world application or library, or just consistency problem
* shyouhei: should we ask it?
* matz: Currently I have no strong motivation to change the behavior

```ruby=
# Matz only allows
autoload :Foo, 'foo'

module Foo
    module Bar
        autoload :Baz, 'foo/bar/baz' # defines ::Baz
        p Baz #=> ::Baz
    end
end

# foo/bar/baz.rb
class Baz
    
end

# foo/bar/baz.rb
module Foo
    module Bar
        class Baz
        end    
    end
end
```

Conclusion:

* matz: Currently I don't want to break anything. Unless there is any actual issue, I won't change it
* matz: A warning is acceptable, but currently I have no plan to change it in future, so it should not be a deprecation warning (`-w` is required for this warning).

Misc:

```ruby=
module Foo
  module Bar
    autoload :Baz, './baz' # defines ::Baz
    p Baz #=> ::Baz
    p defined?(::Foo::Bar::Baz) #=> nil
  end
end

# baz.rb
p defined?(::Foo::Bar::Baz) #=> nil
class Baz
end
```

### [[Feature #18773]](https://bugs.ruby-lang.org/issues/18773) Pass an optional range object to deconstruct (kddeisz)

* It can be very expensive to compute the array for matching against deconstruct
* By passing a range object we can quickly dismiss matches that won't work
* This can be done in a backward-compatible way by checking the arity of deconstruct

Preliminary discussion:

* mame: leave it to ktsj

Discussion:

* mame: The proposal is not clear, but I understood as I commented.
* ko1: can we break compatibility of `#deconstruct`? Proposal checks arity, but if we can change...
* akr: with keyword arguments?
* ktsj: It can be just now. But I don't prefer to change.
* ktsj: I also worry about error message building like that:

```ruby
[0] => []
# (irb):1:in `<main>': [0]: [0] length mismatch (given 1, expected 0) (NoMatchingPatternError)
```

Conclusion:

* continue to discussion.


### [[Feature #18788]](https://bugs.ruby-lang.org/issues/18788) Support passing Regexp options as String to Regexp.new (jeremyevans0)

* Do we want to add support for `Regexp.new(code, options)`, where options is a string (e.g. `'im'`).
* @nobu pointed out it is already possible to get identical behavior using `Regexp.new("(?#{options}:#{code})")`.

Preliminary discussion:

*

```ruby
# proposal

Regexp.new('foo', 'i')   # => /foo/i
Regexp.new('foo', 'imx') # => /foo/imx
```

```ruby
# compatibility

Regexp.new('foo', true)                            #=> /foo/i
Regexp.new('foo', false)                           #=> /foo/
Regexp.new('foo', 0)                               #=> /foo/
Regexp.new('foo', 1)                               #=> /foo/i
Regexp.new('foo', 2)                               #=> /foo/x
Regexp.new('foo', 3)                               #=> /foo/ix
Regexp.new('foo', 4)                               #=> /foo/m
Regexp.new('foo', 'keep case')                     #=> /foo/i
Regexp.new('foo', 'true')                          #=> /foo/i
Regexp.new('foo', 'false')                         #=> /foo/i
Regexp.new('foo', Complex(4, 8))                   #=> /foo/i
Regexp.new('foo', :x)                              #=> /foo/i
```

Discussion:

* ko1: it resembles `Kernel#open`'s mode: `open("...", "rb")`
* matz: hmm
* akr: Why is it needed?
* matz: acceptable. not bad to have
* Specification:
    * Integer: no change
    * true/false/nil: no change
        * true: ignore case
        * false: default
        * nil: default
    * String: error when unknown letter
    * Other class: no change (a warning with `-w`)

Conclusion:

* matz: accepted with above specification.

### [[Feature #18749]](https://bugs.ruby-lang.org/issues/18749) Strangeness of endless inclusive ranges (jeremyevans0)

* Currently, we support both endless inclusive ranges and endless exclusive ranges.
* Endless exclusive ranges do not contain endless inclusive ranges.
* Do we want to convert endless inclusive ranges to endless exclusive ranges?
* Do we want to keep supporting both, but consider them equal?

Preliminary discussion:

* `[a, ∞)` is correct.
* `[a, ∞]` is wrong.

```
(0..).include?(Float::INFINITY)  #=> true
(0...).include?(Float::INFINITY) #=> true (should be false?)
```

```ruby
("a"..)  # NG
("a"...) # OK

ary[1...] # reluctant to write
```

```ruby
("a"..nil).cover?("a"...nil) #=> true
("a"...nil).cover?("a"..nil) #=> false

(nil..nil).cover?(nil...nil) #=> true
(nil...nil).cover?(nil..nil) #=> false
```



Conclusion:

* matz: accept mame's reject

Misc:

* ko1: `..`, `...` is from perl?
* matz: Ruby original. Inclusive `..` is there and we introduced `...` after.

```ruby
(1..)  #=> (1...)
(1...) #=> (1...)

(1..<5)  # exclusive in swift?
(1..^5)  # exclusive in Perl
(1^..^5) # Perl
```

### [[Feature #18461]](https://bugs.ruby-lang.org/issues/18461) closures are capturing unused variables (jeremyevans0)

* Should procs capture local variables they do not use syntactically inside the proc?
* Currently, they do, which I believe is expected and desired.
* Removing this ability would remove the ability to using eval inside the proc to get access to the local variable.
* Changing the behavior could allow for earlier garbage collection, and potentially improve performance.

Preliminary discussion:

* ko1: erb uses the behavior in a real-world use case with binding.

```ruby=
require 'erb'                                      #=> true

def foo
  a = 1                                            #=> 1
  ERB.new(<<~END).result(binding)                  #=> "1\n"
  <%= a %>
  END
end

foo
```

* mame: It kills `Kernel#eval` too

```ruby
a = 1
eval("a") #=> NameError?
```

```ruby
def foo op
  case op
  when :+
    -> a, b { a + b }
  when :-
    -> a, b { a - b }
  end
end

pr = foo(:+)
pr.call(1, 2) #=> 3
```

```ruby
a = lambda{| x, y ; a| p a}
a.call(1, 2) #=> nil

# nobu's proposal: uncaptured Proc
a = proc {|x, y; *| p a}
a.call(1, 2) #=> undefined local variable or method `a'

outer = nil
a = proc{|x, y; * ; outer| p outer}

1.times do |n; *|
end

Ractor.new do |; *|
end

-> x, y; *, self do
    @a 
end
```

* mame: It is a trade-off between performance gain and incompatibility. I think we cannot evaluate the trade-off without actual prototype of the optimization.

Discussion:

* matz: it is implementation dependent.

Conclusion:

* matz: will reject

### [[Feature #18279]](https://bugs.ruby-lang.org/issues/18279) `ENV.merge!` support multiple arguments as `Hash.merge!` (jeremyevans0)

* Can we add this ability?
* @nobu has already prepared a patch.

Preliminary discussion:

```
{}.merge!({1=>1}, {2=>2}, {3=>3})  #=> {1=>1, 2=>2, 3=>3}

ENV.merge!({"foo" => "foo"}, {"bar" => "bar"}) #=> wrong number of arguments (given 2, expected 1) (ArgumentError)
```

Discussion:

Conclusion:

* matz: accepted.

### [[Feature #18159]](https://bugs.ruby-lang.org/issues/18159) Integrate functionality of dead_end gem into Ruby (hsbt)

* How about this?
* IMO: We try to add `dead_end` as the default gems before releasing 3.2.0-preview2

Preliminary discussion:

* ko1: The name "DeadEnd" is okay? `--disable-dead_end`, a constant `DeadEnd`?

```
ruby --help

...
Features:
  gems            rubygems (only for debugging, default: enabled)
  error_highlight error_highlight (default: enabled)
  did_you_mean    did_you_mean (default: enabled)
  dead_end        dead_end (default: enabled)
  rubyopt         RUBYOPT environment variable (default: enabled)
  frozen-string-literal
                  freeze all string literals (default: disabled)
  mjit            C compiler-based JIT compiler (default: disabled)
  yjit            in-process JIT compiler (default: disabled)
```

https://github.com/zombocom/dead_end#what-syntax-errors-does-it-handle

* mame: Should schneem get a commit bit?

Discussion:

* mame: `dead_end` command is allowed to install?

```
$ dead_end broken.rb
...
```

* matz: how about to make separate gem?
* ko1: The name `dead_end` is okay? I think it is not clear.
* martin: I agree with it. The clear name is important for newbies.
* matz: I don't care. I can understand when I see 3 times. Newbie people don't use `--disbale-dead_end`.
* mame: I have heard `--disable-error_highlight --disable-did_you_mean` is too long. Now it will be `--disable-error_highlight --disable-did_you_mean --disable-dead_end`
* matz: `--disable-error-decoration` to disable `did_you_mean`, `error_highlight` and `dead_end`?
* mame: How about a constant "DeadEnd"?
* ko1: How about "SyntaxError::DeadEnd" or something?

Conclusion:

*

### [[Bug #18729]](https://bugs.ruby-lang.org/issues/18729) `Method#owner` and `UnboundMethod#owner` are incorrect after using `Module#public`/`protected`/`private` (eregon)

* Please see https://bugs.ruby-lang.org/issues/18435#note-12
* Removing "ZSUPER methods" solves everything: simpler semantics, easier to understand for everyone and Method/UnboundMethod objects behave like people expect, no special edge case for "ZSUPER methods". Also solves #18729 in a very simple way. And it is already what JRuby & TruffleRuby do.
* "ZSUPER methods" seems accidental complexity from long ago, I believe it is time to remove it.

Discussion:

```ruby
class A
  private def foo
  end
end

class B < A
end

class C < B
  public :foo
end

class B
  def foo
    p :intercepted
    super
  end
end

C.new.foo
#=> MRI: :intercepted
#=> jruby 9.2.13.0 (2.5.7): nil
```

Conclusion:

* matz: The old (current) behavior (ZSUPER) is by design. I will reply

### [[Bug #18826]](https://bugs.ruby-lang.org/issues/18826) `Symbol#to_proc` inconsistent, sometimes calls private methods (eregon)

* Let's fix so it never calls private methods at least
* What about protected methods? I think it would be best for `Symbol#to_proc` procs to only call public methods, never protected/private, otherwise we have deep inconsistencies and complexity.

Preliminary discussion:

```ruby
class Integer
  private def foo
    42
  end
end

p 1.tap(&:foo)           #=> 1
# Matz; it should be an error like `collect`
p (1..4).collect(&:foo)  #=> private method `foo' called for 1:Integer
```

```ruby=
module M
  refine(Integer) do
    private def foo
      42
    end
  end
end

using M

# Matz: the folllowing two are both wrong
p 1.tap(&:foo)          #=> 1
p (1..4).collect(&:foo) #=> [42, 42, 42, 42]
```

```ruby
class Foo
  protected def foo
    42
  end
end

p Foo.new.tap(&:foo)     #=> error?
p Foo.new.tap { _1.foo } #=> error

# Ideal behavior
class Foo
  def dummy
    p Foo.new.tap(&:foo)     #=> 42 (OK?)
    p Foo.new.tap { _1.foo } #=> 42 (OK)
  end
end

# But it requires keeping self at calling Symbol#to_proc
# It is too tough and will not pay
# So let's prohibit both private and public methods
```

```ruby=
class Foo
class Foo
  protected def foo = 42                           #=> 42
  private def bar = 43                             #=> 43
end

Foo.new.method(:foo).call                          #=> 42
Foo.new.method(:bar).call                          #=> 43
```

Conclusion:

* matz: I replied

## Other random topics

### [[Feature #18611]](https://bugs.ruby-lang.org/issues/18611) `[@a, @b, @c].hash`

* its optimization (opt_newarray_hash instruction): https://bugs.ruby-lang.org/issues/18825

* matz: I think it is a way to go


### [[Feature #16495]](https://bugs.ruby-lang.org/issues/16495) markdown-friendly error message

inline ``undefined local variable or method `foo' for main:Object`` inline

https://daringfireball.net/projects/markdown/syntax#code
``There is a literal backtick (`) here.``

### [[Feature #18183]](https://bugs.ruby-lang.org/issues/18183) `Random::Formatter#choose`

* `Random::Formatter#alphanumeric(n = 16, alphabet: [*'A'..'Z', *'a'..'z', *'0'..'9'])`
* `Random::Formatter#alphabet(n = 16, alphabet: [*'A'..'Z', *'a'..'z'])`
* `Random::Formatter#from_set(set, n = 16)` (or `Random::Formatter#from_set(n = 16, set: …)`)
* `Random::Formatter#alphanumeric(n = 16, set: [*'A'..'Z', *'a'..'z', *'0'..'9'])`
* akr: a keyword argument looks good

```ruby=
Random.alphanumeric(10, alphabet: %w(A B C D E))
Random.alphanumeric(10, set: %w(A B C D E))
Random.alphanumeric(10, chars: %w(A B C D E))
```

* matz: I don't hate "alphabet" and "set", but "chars" looks the best natural to me

### [[Feature #18831]](https://bugs.ruby-lang.org/issues/18831) Block argument to `yield` (nobu)

* ``|&block|`` has been introduced since 1.8.7.
* https://github.com/nobu/ruby/tree/blockarg-yield
* ~~test-bundler-parallel is failing.~~

```ruby=
def foo
  # Proposed
  yield 1, 2, 3 do |x|
    42 + x
  end
end

foo do |a, b, c, &blk|
  blk.call(1) #=> 43
end

def foo(&b)
    b.call(1,2,3) do |x|
    end
end


c

$ ruby -rripper -e 'pp Ripper.sexp("def foo; yield() {}; end")'
nil

def foo
  return 1, 2, 3 do
  end
end
```


* matz: I don't want to enhance yield any more

### Jeremy's database

http://tagged-ruby-bugs.jeremyevans.net/#!feedback-patch

* https://bugs.ruby-lang.org/issues/8445
  * ask @akr
  * naruse: go ahead

* https://bugs.ruby-lang.org/issues/8973
  * nobu: added a comment

* https://bugs.ruby-lang.org/issues/9115
  * Hmm

* https://bugs.ruby-lang.org/issues/9208
  * nobu: I think it should be fixed in conemu side
  * mame: I wonder if

* Content-range with no "bytes"
  * https://bugs.ruby-lang.org/issues/11450
  * https://bugs.ruby-lang.org/issues/12055
  * naruse: will look later

* https://bugs.ruby-lang.org/issues/11526
  * ?

* https://bugs.ruby-lang.org/issues/12436
  * mame: nobu please review it

* https://bugs.ruby-lang.org/issues/13513
  * akr: I should review...

* https://bugs.ruby-lang.org/issues/13864
  * (m_seki does not attend the meeting)

* https://bugs.ruby-lang.org/issues/14582
  * ko1: the documentation patch looks good. But the maintainer is tenderlove

* https://bugs.ruby-lang.org/issues/14607
  * ko1: the patch looks good. I ask @mame to check it
  * mame: will do

* Eric Wong's patches
  * https://bugs.ruby-lang.org/issues/15263
  * https://bugs.ruby-lang.org/issues/15310
  * https://bugs.ruby-lang.org/issues/15315
  * https://bugs.ruby-lang.org/issues/15386

* https://bugs.ruby-lang.org/issues/16288
  * nobu: I think it is already fixed
  * mame: will confirm

* https://bugs.ruby-lang.org/issues/16836
  * Hmm

* https://bugs.ruby-lang.org/issues/17120
  * nobu: `\K` is not a lookbehind, but match reset
  * mame: will close it.
  * nobu: doc/regexp.rdoc has a wrong description about `\K`, maybe need to update