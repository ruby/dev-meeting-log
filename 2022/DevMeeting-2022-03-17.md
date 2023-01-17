---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-03-17

https://bugs.ruby-lang.org/issues/18591

## DateTime and location

* 2022/03/17 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/04/21 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* 3.2.0 preview 1: in progress

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #18598]](https://bugs.ruby-lang.org/issues/18598) Add String#bytesplice (shugo)

  * I withdrew the proposal of String#bytesplice in #13110 because it may cause problems if the specified offset does not land on character boundary. But how about to raise IndexError in such cases?

```ruby
# encoding: utf-8

s = "あいうえおかきくけこ"
s.bytesplice(9, 6, "xx")
p s #=> "あいうxxかきくけこ"
s.bytesplice(2, 3, "x") #=> offset 2 does not land on character boundary (IndexError)
s.bytesplice(3, 4, "x") #=> offset 7 does not land on character boundary (IndexError)
```

Preliminary discussion:

*

Discussion:

* naruse: I agree to merge it.

Conclusion:

* matz: go ahead.


### [[Feature #18576]](https://bugs.ruby-lang.org/issues/18576) Rename `ASCII-8BIT` encoding to `BINARY` (byroot)

- The patch is ready.
- I'd like to make the change for 3.2.0-preview1, so that we can get early feedback and if it's negative revert before final release.
- Matz last statement is ambiguous, does it means OK for 3.2.0-preview1?
- There's an open question on wether C API should alias `*ascii8bit*` functions too.

Preliminary discussion:

(https://github.com/ruby/ruby/pull/5571/files#diff-51920e95310ebfbc1ae31709f3b95f89afffbf4f1a6e38e8b2b406e2fb6197eaR57)
>    * Encoding::ASCII_8BIT was renamed to Encoding::BINARY.

https://github.com/ruby/ruby/pull/5571/files#diff-0b652aaedd3286ea21ba6f6db9f4ec65360d348647c9a2fe4ca457bbe95a9721R44
> rb_encdb_declare("BINARY");

Discussion:

* ko1: I showed affected gems before meeting, but there may be also applications (not in gems)
* matz: application users may not try pre-relese version so it is hard to find out the compatibility issues.
* akr: This change doesn't introduce another advantage such as performance improvement, so it can only introduce incompatibility hurts.

Conclusion:

* matz: I'll write I'm negative on it.


### [[Feature #18589]](https://bugs.ruby-lang.org/issues/18589) Finer-grained constant invalidation (kddeisz)

* We want to trade a very small memory increase for much better constant cache invalidation.
* This will allow relying on more stable constant caches for better code specialization in JITs.
* This can also help speed up initial boot times since caches are currently invalidated each time a class is created.
* Because of APIs like Object#extend, ActiveRecord::Relation#extending, and autoload, constant caches get busted far more often than they should in production applications, and has a measurable impact on request speed.

Preliminary discussion:

* ko1: will reply

```ruby=
module M0
  CONST0 = 0
end
module M1
  include M0
  CONST1 = 1
end
class C
  include M1
end

p C::CONST0 #=> 0
p C::CONST1 #=> 1
```

Conclusion:

* matz: accepted. It doesn't change the behavior so we can consider later if it has an issue.


### [[Feature #18615]](https://bugs.ruby-lang.org/issues/18615) Use -Werror=implicit-function-declaration by default for building C extensions (eregon)

* OK to do it? It seems an obvious gain and will avoid many confusing errors which happen too late and instead make them clear and early.

Preliminary discussion:

* nobu: Currently Ruby uses `RTLD_LAZY` for `dlopen`, so Ruby can require an extension library even if some symbol is undefined. It aborts when the undefined symbol is actually used. If this `-Werror` is enabled, a user will not be able to "gem install".
* nobu: Personally, I'd like to enable the option too
* mame: I like to enable it by default too, but also I want some workaround to disable it by external environment variable or what not

Discussion:

* shyouhei: I don't against about this proposal, but I'm afraid the compatibility.
* akr: try it on 3.2? It is 2022.
* nobu: now we replace `s/Werror/W/` for extensions for `-Werror=implicit-function-declaration` to show only warning.
* mame: can we ask shopify people to try?

Conclusion:

* matz: go aheed.
* mame: if we have one or more issues, the reports are very welcome.


### [[Feature #18566]](https://bugs.ruby-lang.org/issues/18566) Merge `io-wait` gem into core `IO` class. (byroot)

- It's very small and any non-trivial IO code will require it.
- Merge io-wait into io.c for Ruby 3.2
- Remove io-wait as a dependency of all gems maintained by ruby-core (e.g. net-protocol).
- Publish a new io-wait version that is simply an empty gem.
- Add a `lib/io/wait.rb` stub, with eventually a verbose deprecation warning.
- It was suggested to do the same with `io-nonblock`.

Discussion:

* nobu: The reason why I created it as an extention library is because it was just possible
* shyouhei: Is there any reason to merge it to the core?
* mame: I think there is no special reason. It once caused a dependency problem: net-protocol depended on io-wait gem, but older rubies cannot handle it because they have not io-wait "gem" even though it has io-wait extension library.
* mame: Now net-protocol does NOT depend on io-wait gem, so there is no problem even if we keep the current status (keep io-wait as a default gem)
* ko1: Is there a good reason to keep it as a separate gem/ext?
* mame: IMO there is no reason.
* nobu: I regret to have introduced some methods like `nread` a little.
* akr: Do we merge all the methods of io-wait gem?
* ko1: It would be good if the number of methods being merged is smaller.

```
/srv/gems/ruby_volt-0.0.5/lib/ruby_volt/connection.rb:        socket.read(socket.nread) if socket_open? && socket.nread > 0 # Flush data waiting to be read
/srv/gems/shopify-cli-2.14.0/vendor/deps/webrick/lib/webrick/server.rb:                            sp.read_nonblock([sp.nread, 8].max, buf, exception: false)
/srv/gems/smart_proxy_dynflow-0.7.0/lib/smart_proxy_dynflow/runner/command.rb:          if @command_out.nread.positive?
/srv/gems/smart_proxy_dynflow-0.7.0/lib/smart_proxy_dynflow/runner/command.rb:            lines = @command_out.read_nonblock(@command_out.nread)
/srv/gems/yahns-1.18.0/test/test_wbuf.rb:    Timeout.timeout(10) { b.read(b.nread) until b.nread == 0 }
```

* ko1: We need to consider each method, instead of the whole methods
* matz:
  * ok:
    * `wait_readable/writable`
  * unknown:
    * `wait`
    * `ready?` == `wait_readable(0)`
      * naming issue: `ready_to_read?`?
    * `wait_priority` 
      * What is this??? No one knows it
    * `nread`
      * what use case is it for?
      * naming issue
* akr: io/nonblock should not be merged because it is not needed for most of applications.

Conclusion:

* matz: let's discuss in different tickets to merge methods respectively (listed above as unkonwn).

### [[Bug #18620]](https://bugs.ruby-lang.org/issues/18620) Not possible to partially curry lambda or proc's `call` method (jeremyevans0)

* Should we make changes to allow this to work?  It complicates the internals, and the benefits seem minimal.
* If we do decide to make this change, I've prepared a patch, but the implementation is suboptimal.

Preliminary discussion:

```ruby
lambda { |a, b| a + b }.method(:call).curry[1][2]
#=> expected: 3
#=> actual: wrong number of arguments (given: 1, expected 2)
```

* mame: I vote for no change. `Proc#curry` should work as is
* nobu: Agree.

Discussion:

* usa: I think it is by design

```ruby=
lambda { |a, b| a + b }.curry[1][2]
#=> 3

lambda { |a, b| a + b }.method(:call).curry[1, 2]
#=> 3

lambda { |a, b| a + b }.method(:call).curry(2)[1][2]
#=> 3
```

Conclusion:

* matz: No change.
* matz: it would be good to add a dedicated documentation to Proc#curry, such as "it does not work if the arity is -1"


### [[Feature #15357]](https://bugs.ruby-lang.org/issues/15357) `Proc#parameters` returns incomplete type information (jeremyevans0)

* Last discussed at the March 2020 developer meeting, but no decision was made.
* I still think it makes sense to consider a lambda keyword argument for Proc#parameters.

Preliminary discussion:

```ruby
pr = proc{|a,b=2| [a,b] }
pr.call(:a)    # => [:a, 2]  # :a is interpret as the first parameter

pr.parameters                # => [[:opt, :a], [:opt, :b]]
pr.parameters(lambda: true)  # => [[:req, :a], [:opt, :b]]
```

```ruby
pr = proc{|a=1,b| [a,b] }
pr.call(:a)    # => [1, :a]  # :a is interpret as the second parameter

pr.parameters                # => [[:opt, :a], [:opt, :b]]
pr.parameters(lambda: true)  # => [[:opt, :a], [:req, :b]]
```

Discussion:

* mame: What is the use case?
  * https://github.com/larskanis/eventbox/blob/3bcbc30096c6003e96d41c6496c781dfc90ac36a/lib/eventbox/argument_wrapper.rb#L10-L17
* matz: Umm, okay?
* ko1: Do we need the same keyword for `Method#parameters` too?
* matz: Only `Proc#parameters`
* matz: The name "lambda" is... okay

Conclusion:

* matz: accepted. Will reply


### [[Feature #18563]](https://bugs.ruby-lang.org/issues/18563) Add "graphemes" and "each_grapheme" aliases (znz)

* Some languages already use graphemes.

Preliminary discussion:

* nobu: I feel rather "grapheme" should be omitted.
* nobu: Unicode Consortium doesn't have a good brand-new word?
* ko1: `each_grapheme` makes it seem to me that it breaks down into graphemes from one graheme cluster.
* nobu: How about `graster`?
 
https://hexdocs.pm/elixir/1.12/String.html
> Furthermore, this module also presents the concept of grapheme cluster (from now on referenced as graphemes).

https://www.php.net/manual/en/ref.intl.grapheme.php
> Grapheme Functions (grapheme_extract, ...)

https://jp.mathworks.com/help/textanalytics/ref/splitgraphemes.html
> `newStr = splitGraphemes(str)` splits the string str into graphemes. A grapheme (also known as grapheme cluster) is the Unicode term for human-perceived characters.

Discussion:

* naruse: If it is really a common word, we can use it.
* naruse: However, we should check only languages: PHP, Elixir, and Matlab.
* naruse: We should wait for other languages, don't we?
* matz: I want to wait too
* mame: We call a HashTable as "Hash" but some people critize the wording
* naruse: A string is conceptually important in Ruby, so we should use strict wording
* matz: I don't want the dishonor of wrong wording unless it is really common

Conclusion:

* matz: For now, I reject it. Will reply.

### [[Feature #12655]](https://bugs.ruby-lang.org/issues/12655) Accessing the method visibility (jeremyevans0)

* This issue discusses many things that have been discussed previously in other issues.
* I want to focus on one additional method: `Module#undefined_instance_methods`
* We don't currently offer a way to see which methods are undefined in a module.
* Is this worth adding?  The use case I know of is it would allow significantly simpler implementation of the looksee gem.

Preliminary discussion:

```ruby
class C0
  def foo = p :C0
end

class C1 < C0
  undef foo
end

C1.new.bar #=> undefined method `bar' for #<C1:0x0000018f8acd15c0> (NoMethodError)
C1.new.foo #=> undefined method `foo' for #<C1:0x000002c703aa5678> (NoMethodError)
  
C1.undefined_instance_methods #=> [:foo]
```

With proposed `Module#undefined_instance_methods`, we can get `[:foo]` with `C1.undefined_instance_methods`

* ko1: I'm positive. Does it check with superclasses?

Discussion:

```
$ gem-codesearch 'undef_method\b' | grep '\.rb:' | wc
   8674   39277 1150347
```

```ruby
class C0
  def foo = 1
  def bar = 1
end

class C1 < C0
  undef foo
  undef bar
end

class C2 < C1
  def bar = 2
end

C2.undefined_instance_methods        #=> ?? []
    
#=> not supported
C2.undefined_instance_methods(true)  #=> [:foo]
C2.undefined_instance_methods(false) #=> []
```

* matz: No need to check superclass? So the ability to check super classes are not needed.

Conclusion:

* matz: accepted. The `inherited_too` optional parameter is not needed.

### [[Bug #11063]](https://bugs.ruby-lang.org/issues/11063) Special singleton class should return true for singleton_class? test (jeremyevans0)

* I don't think it is worth changing the existing behavior.
* Can this be rejected?

Preliminary discussion:

```ruby=
nil.singleton_class #=> NilClass
nil.singleton_class.singleton_class? #=> false

true.singleton_class.singleton_class? #=> false
false.singleton_class.singleton_class? #=> false
```

Conclusion:

* matz: I think it is not worth to change. rejected


### [[Bug #18632]](https://bugs.ruby-lang.org/issues/18632) Struct.new wrongly treats a positional Hash as keyword arguments (eregon)

* Some C functions incorrectly treat a positional Hash as keyword arguments, e.g. Struct.new and Struct#initialize.
* This is inconsistent with the separation of positional and keyword arguments in Ruby 3
* Jeremy proposed to audit and fix them

Preliminary discussion:

```ruby
h = { keyword_init: true }
Struct.new(:a, h)
# actual:   it works
# expected: {:keyword_init=>true} is not a symbol (TypeError)
```

* nobu: I think we should fix this particular case

```ruby
h = { cause: RuntimeError.new("bar") }
raise 'foo', h
#=> Now it works with the following error
#   foo (RuntimeError)
#   bar (RuntimeError)
#
#=> after fixed, it is handled as "raise: (Exception, String)":
#   exception class/object expected (TypeError)
```

* mame: I'm basically positive for the change. If we find a big incompatibility, we can reconsider

Discussion:

* nobu: as long as I investigated, only the two methods (Struct.new and Kernel#raise) have the problem

Conclusion:

* matz: Please fix them. I want to give it a try. No deprecation seems to be needed to me


### [[Bug #18625]](https://bugs.ruby-lang.org/issues/18625) ruby2_keywords does not unmark the hash if the receiving method has a *rest parameter (eregon)

* This seems a clear inconsistency, and it hurts for 3 reasons:
* Can cause issues when migrating to other forms of delegation
* Makes the ruby2_keywords semantics confusing/inconsistent/more complex
* May force other Ruby implementations to replicate this bug if not fixed
* I believe we should fix it now. Fixing it later would cause issues when migrating to `(*args, **kwargs)` or `(...)`.

Preliminary discussion:

```ruby
# A hash that has "ruby2_keywords" flag
marked_hash = Hash.ruby2_keywords_hash({ a: 1 })
args = [marked_hash]

def single(arg)
  arg
end

def splat(*args)
  args.last
end

after_usage = single(*args)
p Hash.ruby2_keywords_hash?(after_usage) #=> false

after_usage = splat(*args)
p Hash.ruby2_keywords_hash?(after_usage) #=> true
```

```ruby
def target(a:)
  p a
end

# after the patch, ruby2_keywords modifier is requried to bar
def bar(*args)
  target(*args)
end

ruby2_keywords def foo(*args)
  bar(*args)
end

p foo(a: 42) #=> 42 in Ruby 3.1
             #=> ArgumentError after the patch
```

Conclusion:

* matz: I think it is good to fix
* mame: It may break (a lot of?) existing programs, but hopefully easy to fix. I'll discuss


### [[Bug #18633]](https://bugs.ruby-lang.org/issues/18633) `proc { |a, **kw| a }` autosplats and treats empty kwargs specially (eregon)

* Intended or bug? Should we fix it?

Preliminary discussion:

```ruby=
proc {|a| a }.call([1, 2])         #=> [1, 2]
proc {|a, **kw| a }.call([1, 2])   #=> 1  # should be [1, 2]
proc {|a, kw: 42| a }.call([1, 2]) #=> 1  # should be [1, 2]

proc {|a,| a }.call([1, 2])        #=> 1
```

```ruby=
proc {|a, **kw| a }.call([1, 2])       #=> 1  # should be [1, 2]
proc {|a, **kw| a }.call([1, 2], **{}) #=> [1, 2]
```

* FYI: Jeremy has fixed this for #16166

```ruby=
proc {|*a, **kw| a }.call([1, 2])  #=> old: [1, 2], new: [[1, 2]]
```

Conclusion:

* matz: I think it should be fixed