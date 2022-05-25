---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-05-19

https://bugs.ruby-lang.org/issues/18747

## DateTime and location

* 2022/05/19 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/06/16 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* No concrete date. Maybe July?

## AWS Cost check

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18729]](https://bugs.ruby-lang.org/issues/18729) `Method#owner` and `UnboundMethod#owner` are incorrect after using `Module#public`/`protected`/`private`

The conclusion of the preivous meeting:

```ruby
class A
  private def foo
  end
end

class B < A
  public :foo
end

m = B.instance_method(:foo)

p m.owner   #=> B    (changed)   A     (in Ruby 3.1)
p m.public? #=> true (changed)   false (in Ruby 3.1)
```

* mame: There were two approaches to implement the behavior above:
  * Approach 1. Allow to create a Method object that wraps ZSUPER method entry
      * Assume implicit method creation like that:
        ```ruby
        public :foo #== public def foo(...) = super(...)
        ```
  * Approach 2. Record owner and visibility information to the Method object
      * Capture owner and visibility at the time that Method#method is called
      * Jeremy seemed to create a patch in this approach
      * The visibility information has been already included in a Method object since January (#18435)
  * Approach 3. Capture not only the original method entry but also ZSUPER method entry
      * Can track of the changes of visibility after captured

* An incompatibility of Approach 1

```ruby
class C
  def foo
    :C
  end
end

class D < C
  private :foo
  $m = D.instance_method(:foo)
end

class C
  undef foo
end

p $m
p $m.bind(D.new).call #=> Current: :C  /  If changed: calling undef method
```

```ruby
class C
  def foo; :C; end
end

class D < C
end

class E < D
  private :foo
end

m = E.instance_method(:foo)

class D
  def foo; :D; end
end

p m.bind(E.new).call
#=> :C on Ruby 3.1
#=> :D on Approach 1
```

* An incompatibility of Approach 2

```ruby=
class C
  def foo; end
  m = instance_method(:foo)
  p m.public? #=> true
  private :foo
  p m.public? #=> false in Ruby 3.1, true in master
end
```

---

* ko1: How about equality? There were three approaches for equality
  * Approach A. compare by only method entry itself
  * Approach B. compare by the original method entry (by lookup of super method of ZSUPER)
  * Approach C. compare by method entry and visibility (valid only with Approach 2)
  * Approach D. compare by ZSUPER method entry (valid only with Approach 3)

```ruby
class C
  def foo
  end
end

class D < C
  private :foo
end

obj = D.new
p C.instance_method(:foo).bind(obj) == D.instance_method(:foo).bind(obj)
# Ruby 3.1: true
# master:   false (because a Method object keeps visibility information and it is respected on equality)

p C.instance_method(:foo) == D.instance_method(:foo)
# Ruby 3.1: false (because a UnboundMethod object keeps the class that instance_method is called against)
# master:   false
```

* An actual case (from rspec, according to eregon)

```ruby
class C
  class << self
    alias_method :n, :new
    private :new
  end
end

um1 = Class.instance_method(:new)
m2 = C.method(:new)
p um1.bind(C) == m2 #=> false on master, true on Ruby 3.1
```

* ko1: Yet another idea for equality: compare by the original methods by looking up parents for ZSUPER

FYI:
```
$ gem-codesearch 'method\(:new\)'  | grep ==
/srv/gems/abaci-0.3.0/vendor/bundle/gems/rspec-mocks-3.5.0/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/fucknumber-0.1.3/vendor/bundle/gems/rspec-mocks-3.5.0/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.4.1/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/rspec-mocks-3.5.0/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/jekyll-antex-0.7.0/lib/jekyll/antex/dealiaser.rb:          options['guards'].values.reject { |value| value == false }.map(&Guard.method(:new))
/srv/gems/jekyll-antex-0.7.0/lib/jekyll/antex/dealiaser.rb:          options['aliases'].values.reject { |value| value == false }.map(&Alias.method(:new))
/srv/gems/logstash-filter-zabbix-0.1.2/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.5.0/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.5.0/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/mastermind_adeybee-0.1.4/vendor/bundle/ruby/2.2.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/restforce-5.2.4/lib/restforce/file_part.rb:require 'restforce/patches/parts' unless Parts::Part.method(:new).arity.abs == 4
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/simplenet-client-0.2.0/vendor/bundle/ruby/1.9.1/gems/rspec-mocks-3.4.1/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/simplenet-client-0.2.0/vendor/bundle/ruby/2.0.0/gems/rspec-mocks-3.4.1/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/sublimetheme-1.0.1/path/gems/rspec-mocks-3.3.2/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/swift-pyrite-0.1.1/vendor/bundle/ruby/2.0.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/method_reference.rb:        klass.method(:new).owner == ::Class
/srv/gems/ungarbled-0.0.11/lib/ungarbled/middleware.rb:        if Browser.method(:new).parameters.flatten.count == 1
/srv/gems/untangle-0.0.1/spec/unit/injector_spec.rb:      subject.inject(greeter.method(:new)).name.should == 'Max'
/srv/gems/with_lock_version-0.1.1/lib/with_lock_version/active_record_ext.rb:        if ActiveRecord::StaleObjectError.method(:new).arity == 0
```

```ruby
class C
  def foo
  end

  $m1 = C.instance_method(:foo)
end

class D < C
  private :foo
end

p $m1.bind(D.new).public? #=> true
```

### [[Bug #18751]](https://bugs.ruby-lang.org/issues/18751) Regression on master for Method#== when comparing public with private method (eregon)

* How do we fix? Restore 3.0-3.1 behavior for #==, or introduce new method to compare if 2 Method/UnboundMethod have the same definition (regardless of visibility)?

Preliminary discussion:

```ruby
class C
  def foo
  end
end

class D < C
  private :foo
end

obj = D.new

m1 = obj.method(:foo)
m2 = C.instance_method(:foo).bind(obj)
p m1 == m2 #=> true, BUT false on master

p m1.public? #=> false on master, true on Ruby 3.1.
p m2.public? #=>                  true on master and Ruby 3.1.
```

* mame: Since January (#18435), a method object has a copy of visibility information
  ```ruby
  class C
    def foo; end
    m = instance_method(:foo)
    p m.public? #=> true
    private :foo
    p m.public? #=> false in Ruby 3.1, true in master
    p instance_method(:foo) == m #=> true in Ruby 3.1, false in master
  end
  ```
* mame: A method object created by `obj.method(:foo)` is private, and `C.instance_method(:foo).bind(obj)` is public. This is why they are different
* mame: How about reverting a copy of visibility information?
  * According to #18435, it is introduced to hide the inconsistency of ZSUPER
  * #18729 will solve the issue itself more fundamentally

* knu: I don't think the visibility is a significant factor to differentiate method objects with, so I'd like to see the behavior restored.
* knu: I think that way because a method object can still be bound and called regardless of the visibility.
* ko1: `UnboundMethod#==` returns `false` if owner is different.

```ruby
class C
  def foo
  end
end

class D < C
  private :foo
  public :foo
end

class E < C
end

p D.instance_method(:foo) == C.instance_method(:foo) #=> false
p E.instance_method(:foo) == C.instance_method(:foo) #=> false
p D.instance_method(:foo) == E.instance_method(:foo) #=> false
```

```ruby
class C
  def foo
  end
  alias bar foo
end

class D < C
  alias baz foo
end

c = C.new
p c.method(:foo) == c.method(:bar) #=> true
p C.instance_method(:foo) == C.instance_method(:bar) #=> true

d = D.new
p d.method(:foo) == d.method(:bar) #=> true

p C.instance_method(:foo) == D.instance_method(:bar) #=> false
p D.instance_method(:foo) == D.instance_method(:bar) #=> true
p C.instance_method(:bar) == D.instance_method(:baz) #=> false
p D.instance_method(:bar) == D.instance_method(:baz) #=> true

p C.instance_method(:foo).bind(d) == D.instance_method(:bar).bind(d) #=> true
p C.instance_method(:foo).bind(d) == D.instance_method(:baz).bind(d) #=> true
p C.instance_method(:bar).bind(d) == D.instance_method(:baz).bind(d) #=> true
```

```ruby
class C
  def foo = nil
  $m1 = C.instance_method(:foo)
  private :foo
  $m2 = C.instance_method(:foo)
  public :foo
  $m3 = C.instance_method(:foo)
end

p $m1 == $m2  #=> false on master, true on Ruby 3.1
p $m1 == $m3  #=> true  on master and Ruby 3.1
p $m1.public? #=> true  on master, false on Ruby 3.1
p $m2.public? #=> false on master, false on Ruby 3.1
```

```ruby
class Parent
  def foo; end
end

class Child < Parent
  protected :foo
end

p Child.instance_method(:foo).owner #=> current: Parent, should be: Child
# https://bugs.ruby-lang.org/issues/18729#note-6
```

```ruby
class C
  class << self
    alias_method :n, :new
    private :new
  end
end

um1 = Class.instance_method(:new)
m2 = C.method(:new)
p um1.bind(C) == m2 #=> false on master, true on Ruby 3.1
```

* options:
    * true:
        * keep compatibility
        * easy to compare to original behavior
    * false: it is different method body (ZSUPER)

```
$ gem-codesearch 'private :new' | wc
    731    2933   69289
```

Discussion:

(two hours; mame failed to log)

* akr: Visibility might not be a property of a method object, but it is of a class/module it is defined in.
* matz: Agreed. Now I want to remove Method#public? and so on

```ruby
class C
  def foo
  end
    
  private def bar
  end
end

class D < C
  private :foo
  public :bar
end

p D.instance_methods(false).include?(:bar) #=> true???
p D.instance_method(:bar).owner   #=> C
p D.instance_method(:bar).public? #=> false???

# instance_methods returns public and protected methods.
D.instance_methods(false) == D.public_instance_methods(false) + D.protected_instance_method(false)

D.public_instance_method?(:foo)   #=> false
D.public_instance_methods         #=> :foo should NOT be included
D.public_instance_methods(false)  #=> :foo should NOT be included
D.private_instance_methods        #=> :foo should be included???
D.private_instance_methods(false) #=> :foo should be included???
D.instance_methods(false)         #=> :foo should NOT be included

# 3.1
p D.public_instance_methods.include?(:foo)         #=> false
p D.public_instance_methods(false).include?(:foo)  #=> false
p D.private_instance_methods.include?(:foo)        #=> true
p D.private_instance_methods(false).include?(:foo) #=> true
p D.instance_methods(false).include?(:foo)         #=> false
```

```ruby
class C
  private def foo
  end
end

class D < C
  public :foo
end

p D.instance_methods(false).include?(:foo) #=> true

```

Conclusion:

* matz: I want to revert everything including `Method#public?` (#11689)
* matz: I want to cancel #18729
* matz: For the motivative inconsistency of #18729 and #18435, `Class#instance_methods(false)` should ignore `ZSUPER`
* matz: But I want time to consider


### [[Feature #18595]](https://bugs.ruby-lang.org/issues/18595) Alias `String#-@` as `String#dedup` (byroot)

- Unary operator have some precedence oddities, forcing to use parantheses.
- `dedup` is quite an explicit name.

Preliminary discussion:

* mame: How about `String#intern_string` ?
* knu: `dedup` corresponds to `dup`

Discussion:

```ruby=
irb(main):004:0> puts RubyVM::InstructionSequence.compile('"".-@.size').disasm
== disasm: #<ISeq:<compiled>@<compiled>:1 (1,0)-(1,10)> (catch: FALSE)
0000 opt_str_uminus                         "", <calldata!mid:-@, argc:0, ARGS_SIMPLE>(   1)[Li]
0003 opt_size                               <calldata!mid:size, argc:0, ARGS_SIMPLE>[CcCr]
0005 leave
=> nil
```

Conclusion:

* matz: The name "dedup" is acceptable, but I suspect whether an useful shorthand is really needed
* matz: will reply

### [[Feature #18339]](https://bugs.ruby-lang.org/issues/18339) GVL instrumentation API (byroot)

- The patch is ready and went through several reviews: https://github.com/ruby/ruby/pull/5500
- The overhead is close to zero if no callback is registered.
- May I merge it?

Preliminary discussion:

* ko1: `RUBY_INTERNAL_EVENT_THREAD_*` should be renamed to `RUBY_THREAD_INTERNAL_EVENT_*` or `RUBY_INTERNAL_THREAD_EVENT_*` or something?
* ko1: The new kind of event types is different from existing ones
* mame: two additional void pointers should be passed for user data and future extension.

```C
typedef void (*rb_thread_callback)(uint32_t event, void *event_data, void *user_callback_data);
```

Conclusion:

* ko1: I'll handle this


### [[Bug #18730]](https://bugs.ruby-lang.org/issues/18730) Double `return` event handling with different tracepoints (alanwu)

* In one situation, enabling a TracePoint inside another runs the second hook within the same event https://bugs.ruby-lang.org/issues/18730#note-6
* Is it reasonable to not call the newly added hook inside the same event for consistency?

Preliminary discussion:

* ko1: it should not be called, but not sure it is feasible.

Conclusion:

* ko1: I'll handle this


### [[Feature #14602]](https://bugs.ruby-lang.org/issues/14602) Version of `dig` that raises error if a key is not present (byroot)

- Proposed names: in the issue `deep_fetch`, `dig!`, `dig(..., exception: true)`
- My preference would go to `deep_fetch`, but maybe we can find better?
- Sometimes `dig` is used because it's shorter and more convenient than `.fetch(:a).fetch(:b)`, but an early error would have been preferable.

Preliminary discussion:

* ko1: (1) call new method (deep_fetch) recursively? or (2) call `fetch` recursively?

```ruby=
module DeepFecher
  # 1
  def deep_fetch *keys
    key, *rest = *keys
    v = self.fetch(key)
    if !rest.empty?
      v.deep_fetch(*rest)
    else
      return v
    end
  end

  # 2
  def deep_fetch(*keys) = keys.inject(self) {|o, k| o.fetch(k)}

  # 2.1
  def deep_fetch(*keys) = keys.inject(self, :fetch)
end

class Hash
  include DeepFetcher
end
    
class Array
  include DeepFetcher
end

...
```

* nobu: deep_fetch is needed? `keys.inject(self, :fetch)`
* ko1: it seems tough to read.

```ruby=
h = { a: { b: { c: 42 } } }

h[:a][:b][:c]
[:a, :b, :c].inject(h, :fetch)
h.deep_fetch(:a, :b, :c)
```

* ko1: if user defines user-defined `dig`, passing `exception:` keyword will break compatibility.

Discussion:

* matz: why is it needed?
* ko1: It is shorthand for `obj.fetch(:a).fetch(:b).fetch(:c)`.
* naruse: We discussed before and we should have more realworld examples.
* mame: JSON parse can be one exmaple.
* matz: What are name candidates?
* ko1: `deep_fetch`, `dig!`, `dig(..., exception: true)`
* shyouhei: `dig!` is not Ruby-style
* nobu: `[:a, :b, :c].inject(json, :fetch)`
* naruse: the error message is important
* mame: the OP proposes as `hash.dig!(:name, :middle) # raises KeyError (key not found: :middle)`
* naruse: I imagine the path information is important, but need real-world example to discuss it
* mame: error_highlight will help to find the failure

```ruby=
json[:a][:b][:c]
            ^^^^

# mame: error_highlight it not perfect in the following case
"#{ json[:a][:b][:c] }"

# mame: we need to write as follows
"#{ json[:a][:b].fetch(:c) }"

"#{ json[:a][:b][:c].must! }" # different meaning
"#{ json.deep_fetch(:a, :b, :c) }"

json.fetch(:a, :b, :c) {|key| raise KeyError, key}
```

Conclusion:

* matz: will reply


### [[Feature #18774]](https://bugs.ruby-lang.org/issues/18774) Add Queue#pop(timeout:) (eregon)

* OK?
* Anyone wants to implement it for CRuby?

Preliminary discussion:

* ko1: `Queue#pop(timeout: sec)`
* ko1: Summary of options when timeout:
    * return `nil`
    * raise an exception
        * `Queue::TimeoutError < StandardError`
        * `Thread::TimeoutError < StandardError`
* ko1: FYI:
    * `Queue#pop(true)` raises an exception `queue empty (ThreadError) < StandardError`
    * `Queue#pop` on closed Queue return `nil`.

Discussion:

* shyouhei: It is possible to specify return value.
* mame: `Queue#pop(timeout: sec, timeout_value: :TimeOut) #=> :TimeOut when time is exceeded`
* naruse: We can't distinguish timeout or closing by `nil`
* shyouhei: We can check `Queue#closed?`
* naruse: So we need to call `#closed?` every time.
* ???: What about `SizedQueue#push(timeout:)`?
* mame: Currently it returns self
* ko1: So is it okay to return nil when timeout?

Conclusion:

* matz: `Queue#pop(timeout: sec)` is accepted and `nil` at timeout.
* matz: Create another ticket for `SizedQueue#push` if someone wants it.

### [[Feature #18742]](https://bugs.ruby-lang.org/issues/18742) Introduce a way to tell if a method invokes the `super` keyword or not (Dan0042)

* It's currently possible to do this with RubyVM::InstructionSequence, but non-portable and a bit slow.
* I think it would be useful and cheap to keep this metadata in [Unbound]Method.

Preliminary discussion:

* mame: TBH, in my first impression, it is too much just for "no clobber def"
* nobu: It should be `call_super?` at least.

Discussion:

```ruby=
class X
  def a
  end; p instance_method(:a).calls_super? #=> false

  def b
    super
  end; p instance_method(:b).calls_super? #=> true

  def c
    super if false
  end; p instance_method(:c).calls_super? #=> true

  def d
    eval 'super'
  end; p instance_method(:d).calls_super? #=> false (I doubt there's a reasonable way for this to return true)
end
```

* motivation: https://bugs.ruby-lang.org/issues/18618 no clobber def

```ruby=
class C
  def foo
  end
  def bar
  end
end

class D < C
  no_clobber
  def foo # should emit a warning or error
  end
  def bar # should be accepted because it is obviously intentional
    super
  end
end
```

* matz: call_super? according to our usual naming convention.
* matz: I don't believe "no clobber def" is as practical as you think. There are many cases where we override a method without super (by copy&paste the implementation)
* matz: I'm not in favor of providing and using this piece of information in meta-programming, but I would rather see it in static analysis.
* nobu: It is possible to use ripper
* akr: One idea is to force users to override explicitly.

```ruby=
class D < C
  def foo # error if C defines foo
  end
  override def bar # accept even if C defines bar
  end
  clobber_check # report D#foo
end
```

Conclusion:

* matz: rejected

### [[Bug #18407]](https://bugs.ruby-lang.org/issues/18407) Behavior difference between integer and string flags to File creation (jeremyevans0)

* I don't think it's worth breaking backwards compatibility to allow File::BINARY to work on Unix.
* Do we want to reject this, or make it so that File::BINARY has the same effect as the 'b' option?

Discussion:

```ruby
Encoding.default_internal = Encoding::UTF_8

f = File.new("/tmp/test.bin", "wb") # "wb" == "wb:BINARY"
f.write "\xC8".force_encoding(Encoding::BINARY) # pass

f = File.new("/tmp/test.bin", File::CREAT | File::WRONLY | File::TRUNC | File::BINARY) #
f.write "\xC8".force_encoding(Encoding::BINARY) # "\\x8B" from ASCII-8BIT to UTF-8 (Encoding::UndefinedConversionError)
```

* nobu: Mode "wb" is NOT the same as `File::CREAT | File::WRONLY | File::TRUNC | File::BINARY`
* nobu: `File::BINARY` does not change Ruby's encoding
* nobu: you need to specify encoding explicitly

```ruby
f = File.new("/tmp/test.bin", File::CREAT | File::WRONLY | File::TRUNC | File::BINARY, encoding: "BINARY")
f.write "\xC8".force_encoding(Encoding::BINARY) # pass
```

Conclusion:

* nobu: replied

### [[Bug #18740]](https://bugs.ruby-lang.org/issues/18740) Use of rightward assignment changes line number needed for line-targeted TracePoint (jeremyevans0)

* Using rightward assignment can change the line numbers needed for TracePoint.
* Is this a bug, or do we consider it an implementation detail?

Discussion:

```ruby=
def test
  "foo"             # Line 2
    .upcase
    .downcase => x  # Line 4
end

TracePoint.new(:line){|tp| p tp }.enable(target: method(:test)) { test }
# expected: #<TracePoint:line -:2 in `test'>
# actual:   #<TracePoint:line -:4 in `test'>
```

Conclusion:

* matz: it is preferable to fix, but the current behavior is acceptable. Leave it to @ko1

### [[Bug #18727]](https://bugs.ruby-lang.org/issues/18727) Make failed on x86_64-cygwin (LoadError) (jeremyevans0)

* Considering all modern Windows systems can support WSL (Windows Subsystem for Linux), do we want to continue to support Cygwin?
* We have 17 open bugs related to Cygwin support.
* Cygwin platform currently has no maintainer.
* We could probably remove a significant amount of code by dropping Cygwin support.
* I recommend that we drop Cygwin support unless someone wants to step up as maintainer and start fixing Cygwin bugs.

Discussion:

* naruse: Let's assign cygwin-related tickets to dummy group, set them as feedback, and ignore them
* nobu: For the particular ticket, the patch looks good. I'll merge it

Conclusion:

* naruse: created a group for cygwin
* nobu: I'll handle them when I like to do so

### [[Bug #18294]](https://bugs.ruby-lang.org/issues/18294) error when parsing regexp comment (jeremyevans0)

* I was able to develop a fix for this, but I'm not sure whether the fix is acceptable.
* The problem is actually wider than just extended regexps, it can affect any regexp if the `(?#` comment syntax is used.

Discussion:

* nobu: The patch need some trivial improvements, but I think we should merge it
* matz: positive

Conclusion:

* nobu: Will review and add a comment to the PR

### [[Bug #18658]](https://bugs.ruby-lang.org/issues/18658) Need openssl 3 support for Ubuntu 22.04 (Ruby 2.7.x and 3.0.x)

Discussion:

* mame: rhe says "This (openssl gem that supports OpenSSL 3.0) includes dropping support for OpenSSL 1.0.1"
* naruse: It is impossible to backport if so
* akr: Users should upgrade ruby to 3.1 first, and then Ubuntu 22.04
