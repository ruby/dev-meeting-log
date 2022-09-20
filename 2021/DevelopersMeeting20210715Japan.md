---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210715Japan

https://bugs.ruby-lang.org/issues/17997

## DateTime and location

* 7/15 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 8/19 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Tickets

### [[Feature #17994]](https://bugs.ruby-lang.org/issues/17994) Clarify `IO.read` behavior and add `File.read` method (mame)

* Jeremy pointed that `Socket.read` is callable but does not make sense. Should we deprecate the use of `IO.read` against a receiver different than `IO` and `File`?

Preliminary discussion:

* mame: Should we prohibit `Socket.read`, `UNIXServer.read`, etc.?
* nobu: What's happening when did a user define their own custom subclass of `IO`?
* mame: `IO.read` may raise an exception when its receiver is neither `IO` or `File`.
* nobu: `readlines`, `write`, `binread`, `binwrite` too?
* mame: Looks good
* mame: Ah, should we define `File.write`, `File.binread`, etc.? Many methods...

Discussion:

* mame: everyone agreed with addition of the pipe behavior to the doc of IO.read

Discussion about the addition of the doc `File.read`

* aycabta: It is possible to add rdoc even if File.read is not defined
* akr: Objection against adding File.read. It is hard to understand and maintain.
* nobu: Agreed with akr
* mame: Okay, so remove the document of File.read

```shell
$ gem-codesearch 'IO\.read\b' | wc -l
  13939
```

Discussion about `Socket.read`

* mame: How about `Socket.read`?
* akr: It is a feature of class methods. No one writes it, so it should be kept as-is.


Conclusion:

* akr: I'll reply


### [[Feature #17938]](https://bugs.ruby-lang.org/issues/17938) Keyword alternative for boolean positional arguments (MatheusRich)

* I think we should address this in the whole std lib (incrementally). Matz seemed positive about it.
* What should we do if both positional and keyword args are given?
* Should we deprecate/warn the positional version? (even in a minor release?)

Preliminary discussion:

```ruby
object.respond_to?(:symbol, include_all: false)
object.methods(regular: true)
```

* mame: both should be accepted
* mame: But the old form should not be deprecated in near future

Discussion:

* usa: I cannot imagine that the old style is deprecated
* nobu: it should raise an exception if both are supplied
* nobu: The name of keyword matters
* mame: To discuss keyword names, is each ticket needed for each method?
* usa: I think so

Discussion about keywords:

* mame: How about `include_all` for `respond_to?`
  * `object.respond_to?(:symbol, include_all: false)`
* matz: It is not clear. Need other good name candidates
* mame: How about `regular`, `only_public`, or `include_all` for `methods` method?
* usa: What is `regular`!?
* mame: It is written in rdoc
* matz: All of them are not clear to me

Conclusion:

* matz: will reply


### [[Bug #15856]](https://bugs.ruby-lang.org/issues/15856) Performance of redundant `Kernel.require` is slow when many gems are activated (jeremyevans0)

* Repeated require of native library without extension is very slow.
* Repeated require of pure ruby library without extension is fast.
* Making `search_required` use the same logic for both native and pure ruby libraries makes repeated require of native library without extension fast.
* In addition to dramatically increasing the performance, this also makes the behavior consistent for native and pure ruby libraries.
* This breaks backwards compatibility, in that `require 'foo.so'; require 'foo'` will not load `foo.rb` if it exists.
* Is such breakage acceptable for the dramatically increased performance and increased consistency?

Preliminary discussion:

* mame: in my first impression, the incompatibility is not acceptable. The behavior reported looks weird, but I have no idea about the rationale and whether we can fix the issue.
* nobu: the behavior ineed looks weird and unintentional. I will investigate

Conclusion:

* nobu: This is a bug. I'll fix.


### [[Feature #17724]](https://bugs.ruby-lang.org/issues/17724) Make the pin operator support instance/class/global variables (jeremyevans0)

* Is it OK to allow the pattern matching pin operator to directly support instance/class/global variables?

Preliminary discussion:

* mame: let's ask matz

Discussion:

* matz: looks good

Conclusion:

* matz: will reply

off-topic: remove "experimental" warning of =>?

* matz: looks good

### [[Bug #17757]](https://bugs.ruby-lang.org/issues/17757) `Hash#slice` does not keep `compare_by_identity` on the results (jeremyevans0)

* Some hash methods do not consistently return hashes with compare by identity flag if receiver has compare by identity flag.
* Some of these are definitely bugs, as they treat the empty receiver differently than non empty receiver.
* However, do we want change the behavior for `slice` and/or `transform_keys` to return compare by identity hash if receiver has compare by identity flag?

Preliminary discussion:

* https://github.com/ruby/ruby/pull/4616
  * except
  * merge
  * reject
  * select
  * slice
  * transform_keys
  * transform_values

Discussion:

* no problem to keep the flag
  * transform_values
* Consideration
  * slice
  * except
  * reject
  * select
* Question
  * merge
  * transform_keys https://bugs.ruby-lang.org/issues/17757#note-3
* usa: I suspect that reject/select creates a new Hash object, so I'm unsure whether the flag should be kept or not
* ko1: I think keeping the flag is okay except "merge". I'm not sure what's happen on merge `compare_by_identity` and not.
* knu: `merge` (and other methods) should respect the `compare_by_identity` flag of the receiver.

```shell
$ gem-codesearch compare_by_identity | wc -l
681
```

Conclusion:

* Matz: will reply


### [[Bug #17737]](https://bugs.ruby-lang.org/issues/17737) `Array#permutation` does not immediately check the arity when no block is given (jeremyevans0)

* If we think this is a bug, we'll have to move all argument checking before enumerator creation in all of the methods that return enumerator.
* I don't think it is a bug.
* If this isn't a bug, can this be closed?

Preliminary discussion:

* mame: I don't care

Discussion:

* nobu: I vote for keeping the current behavior

Conclusion:

* matz: agreed, keep the current behavior


### [[Bug #17719]](https://bugs.ruby-lang.org/issues/17719) Irregular evaluation order in hash literals (jeremyevans0)

* Should we fix the evaluation order for duplicate hash keys using @nobu's patch (my preference)?
* Or should we change duplicate hash keys from a warning to an error?

Preliminary discussion:

* mame: I leave it to nobu

Conclusion:

* nobu: no one is against the fix, so I'll fix


### [[Bug #18011]](https://bugs.ruby-lang.org/issues/18011) `Method#parameters` is incorrect for forwarded arguments (eregon)

* How about @jeremyevans0's suggestion to add [:keyrest, :**] for ruby2_keywords methods and for `(...)`? It sounds good to me.

Preliminary discussion:

```ruby=
def foo(ord, kw:) = [ord, {kw: kw}]

def proxy(...) = foo(...)

p method(:proxy).parameters
#=> current:    [[:rest, :*], [:block, :&]]
#=> suggestion: [[:rest, :*], [:keyrest, :**], [:block, :&]]

ruby2_keywords def proxy2(*a, &b) = foo(*a, &b)

p method(:proxy2).parameters
#=> current:    [[:rest, :a], [:block, :b]]
#=> suggestion: [[:rest, :a], [:keyrest, :**], [:block, :b]]
```

* mame: looks reasonable

Conclusion:

* matz: looks good, will reply


### [[Feature #15357]](https://bugs.ruby-lang.org/issues/15357) `Proc#parameters` returns incomplete type information (larskanis)

* Either change `proc{}.parameters` to return the same information as `lambda{}.parameters`
* or add keyword `parameters(lambda: true)` to enable this in a backward compatible way (@jeremyevans0's patch)
* or keep everything as is?

Preliminary discussion:

```ruby=
p(proc {|a, b=1| }.parameters)
# current:    [[:opt, :a], [:opt, :b]]
# suggestion: [[:req, :a], [:opt, :b]]

# Or Jeremy's suggestion
p(proc {|a, b=1| }.parameters(lambda: false)) #=> [[:opt, :a], [:opt, :b]]
p(proc {|a, b=1| }.parameters(lambda: true))  #=> [[:req, :a], [:opt, :b]]
```

* Rationale: want to distiguish between `|a, b=2|` and `|a=1, b|`

```ruby=
pr = proc{|a,b=2| [a,b] }
pr.parameters  # => [[:opt, :a], [:opt, :b]]
pr.call(:a)    # => [:a, 2]  # :a is interpret as the first parameter

pr = proc{|a=1,b| [a,b] }
pr.parameters  # => [[:opt, :a], [:opt, :b]]
pr.call(:a)    # => [1, :a]  # :a is interpret as the second parameter
```

* (mame: I hate black magic!)

Discussion:

* matz: I don't like the change (even `lambda: true`)

Conclusion:

* matz: let me consider for a while


### [[Misc #18025]](https://bugs.ruby-lang.org/issues/18025) `rb_iterate` is obsolete since 1.9 but is not marked as deprecated for C compilers (eregon)

* OK? Could someone review https://github.com/ruby/ruby/pull/4629?

Preliminary discussion:

* The rationale https://bugs.ruby-lang.org/issues/1501

Conclusion:

* ko1: let's try. go ahead


### [[Feature #17795]](https://bugs.ruby-lang.org/issues/17795) Around `Process.fork` callbacks API (dan0042)

* After a few meetings without a decision, can we at least reach a compromise with `Process._fork_` ?
* For certain uses cases that would improve the statu quo from "impossible to write fork-safe code" to "unobvious but possible"

Discussion:

* akr: I think that `Process._fork_` is a possible option
  * `Process.fork`, `Kernel#fork`, and `IO.popen("-")` should call `Process._fork_`
  * (`Process.daemon` should not)

About the implementation

* mame: I have no idea about the implementation
* akr: rb_fork_ruby in process.c can be wrapped as `Process._fork_`. The function does not accept a block, which is reasonable
* mame: `rb_thread_atfork` must be called after rb_fork_ruby (only when pid == 0 (child))

How to delegate a class method

```ruby=
module M
  def foo
    super
    p :M
  end
end
class C
  def C.foo
    p :C
  end
end
class << C
  prepend M
end
C.foo
#=>
:C
:M
```

```ruby=
# znz proposed special module for override
module Process::Fork
  def self.fork
  end
end

module Process
  def self.fork
    Process::Fork.fork
  end
end

module M
  def fork
    p :M
    super
  end
end

class << Process::Fork
  prepend M
end

Process.fork
```

```ruby=
class Process
  module Fork # prepend to me
    def fork
      # native implementation
    end
  end

  class << self
    include Fork
  end
end

```

About the API

* akr: Is there any precedent of a method that is supposed to be overwritten?
* mame: Warning.warn?
* ko1: It should use `prepend` and `super` (or other method chain technique) to allow multiple libraries. We can't force to use it, but documenttion solves it?

About the name

* mame: `__fork__`?
* matz: `sysfork`?
* akr: It is not a bare wrapper of `fork(2)`, so `sysfork` is not suitable
* matz: the purpose of `__send__` is "do not override", but `__fork__` is supposed to be overridden
* knu: `Process::Fork#call`?

Conclusion:

* matz: Want a good name
* mame: The method is not supposed to be used directly
* akr: A longer name may be possible


### [[Feature #17837]](https://bugs.ruby-lang.org/issues/17837) Add support for Regexp timeouts (Sam Saffron) (duerst)

- I support looking more closely at Nobu's patch. I have done some experiments which show worst-case slowdowns of ~5%.

Discussion:

* naruse:
  * https://isucon.net/archives/48697611.html
  * https://qiita.com/yuki549/items/b2c5f5c398782950a171
* knu: we may need a regexp-opt implementation in Ruby (https://www.emacswiki.org/emacs/RegexpOpt) to reduce backtracking when you match lots of keywords

Conclusion:

* nobu: will perform experiment


### [[Bug #13671]](https://bugs.ruby-lang.org/issues/13671) Regexp with lookbehind and case-insensitivity raises RegexpError only on strings with certain characters (eregon)

* Can we update to latest Onigmo to fix this? Who knows how to do that/who to assign?

Conclusion:

* martin: I will give it a try in the next week


### [[Feature #15211]](https://bugs.ruby-lang.org/issues/15211) `Integer.try_convert` (nobu)

> I found `Integer` doesn't have `try_convert` method, which converts the argument by `to_int` method without explicit conversions like `Integer()`.
> This is useful to treat an `Integer`-like argument like as methods written in C.

Discussion:

* (confirm the specification...)
* no one has any strong opinion about the proposal

Conclusion:

* matz: go ahead

### [[Feature #17798]](https://bugs.ruby-lang.org/issues/17798) exception in finalizer (znz)

* matz: let's try

### [[Bug #14743]](https://bugs.ruby-lang.org/issues/14743) Some links broken in README (znz)
1. Current README.md's link is `[win32/README.win32](win32/README.win32)` and it works on github, but rdoc generates broken link.
2. Using rdoc-ref suggested in Convert links to local files in markdown, it works rdoc generated html, but it does not work on github.
3. Plain filename becomes link on rdoc generated html, but it does not become link on github.

* Rename to `win32/README.rdoc` will work?
* No conclusion.

### [[Feature #18033]](https://bugs.ruby-lang.org/issues/18033) `Time.new` to parse a string (nobu)
Make `Time.new` parse `Time#inspect` and ISO-8601 like strings.

* `Time.iso8601` and `Time.parse` need an extension library, date.
* `Time.iso8601` can't parse `Time#inspect` string.
* `Time.parse` often results in unintentional/surprising results.
* `Time.new` also about 1.9 times faster than `Time.iso8601`.

* naruse
  * https://bugs.ruby-lang.org/issues/16005
  * https://datatracker.ietf.org/doc/html/rfc3339 date-time
  * Since ISO 8601 has Basic and Extended format, this spec supports RubyFormat (Time#to_s) and RFC3339 for sure.

* knu: Please do not be strict about an optional colon in +HH:MM.
* akr: We need to support "UTC" as zone offset to support the result of Time#to_s.

* knu: I want [Time#change](https://api.rubyonrails.org/classes/Time.html#method-i-change)
* naruse: I want [TimeOnly](https://www.infoq.com/jp/news/2021/05/Net6-Date-Time/)

### [[Feature #18007]](https://bugs.ruby-lang.org/issues/18007) Help developers of C extensions meet requirements in "doc/extension.rdoc" (nobu)

https://github.com/ruby/ruby/pull/4604

> ## Problem being solved
>
> This option is intended to help developers of C extensions to check if their code meets the requirements explained in "doc/extension.rdoc". Specifically, I want to ensure that `T_DATA` object classes undefine or redefine the `allocate` method.
>
> There is currently no easy way for an author of a C extension to easily see where they have made the mistake of letting the default `allocate` class method remain.
>
> ## Description of the solution
>
> Compiled with this option, Ruby will warn when a `T_DATA` object is created whose class has not undefined or redefined the `allocate` method.
>
> A new function is defined, `rb_data_object_check`. That function is called from `rb_data_object_wrap()` and
> `rb_data_typed_object_wrap()` (which implement the `Data_Wrap_Struct` family of macros).
>
> The warning, when emitted, looks like this:
>
> ```
> warning: T_DATA class Nokogiri::XML::Document should undefine or redefine the allocate method, please see doc/extension.rdoc
> ```

* naruse: objection for 3.1. Will reply

### [[Feature #18008]](https://bugs.ruby-lang.org/issues/18008) `keyword_init?` method for `Struct` (nobu)

> I'd like to know whether my struct was initialized with `keyword_init: true` or not.
> This information is useful when writing a deserializer (attached an example below).

* matz: Looks good