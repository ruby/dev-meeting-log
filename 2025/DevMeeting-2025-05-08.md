# DevMeeting-2025-05-08

https://bugs.ruby-lang.org/issues/21134

## DateTime and location

* 2025/05/08 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2025/06/05 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe
- naruse: Will have a sneak peak release when both ZJIT and namespace are merged.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21311]](https://bugs.ruby-lang.org/issues/21311) Namespace on read (tagomoris)

* I'm finishing the namespace branch to be ready for merge (disabled by default) https://github.com/tagomoris/ruby/tree/namespace-on-read-classext
  * Feature #21311
  * https://github.com/ruby/ruby/pull/13226
* Current status:
  * Default (Namespace disabled, YJIT/ZJIT disabled): `make check` and `make exam` passed on my macOS (not checked on CI)
  * Namespace enabled: `make check` passed but `make exam` failed (the known cause and I'll update the implementation soon)
* I hope it'll be merged without further more rebases :P

#### Discussion

* mame: Are there any strong opponents?
* tagomoris: Not that strong ones I can remember, but some are weakly concerning.

#### Current status

- tagomoris: No drastic design changes recently.
- tagomoris: I have been fixing bugs and glitchy corner cases.
- tagomoris: There _could_ be remaining discussions but they can be defeated separately.
- tagomoris: The pull request passes most of the tests.  You can basically try it now.
- nobu: which environment do you use?
- tagomoris: normal ARM-mac
- nobu: I think you can test i686 linux relatively easily if you have a x64 machine.
- mame: The topic branch is getting large, which is making rebase difficult.
- ko1: there are no performance measurements yet, which can be a potential show-stopper.
- tagomoris: what can I do for that?
- ko1: you can look at what `make yjit-bench`  does

#### Conclusion

* mame: next step:
    1. matz responds "go ahead" to the ticket.
    1. @tagomoris has to be a core member in order to tackle remaining CI failures.
    1. mmtk test breaks ATM, so it should be temporary disabled.  We have to turn it back once the test works.
    1. once matured, matz names it ruby4.
 * ko1: is it going to be an experimental feature?
 * tagomoris: Of course I thought so.

### [[Feature #20405]](https://bugs.ruby-lang.org/issues/20405) inline comments (make_now_just)

<!--

```ruby
#: (Integer, Float) -> String
def foo(x (=: Integer =), y (=: Float =)) #: String
end

def foo(
  x, #: Integer
  y, #: Float
) #: String
  foo(untyped (=: Integer =))
  
  x = untyped #: Integer
  foo(x)
end

foo() (=: nonnil =) .bar().baz()

empty_integer_array = [(=: Integer =)]
empty_integer_array = [] #: Array[Integer]

foo(10_000 /* KB */, 16 /* MB */)
foo(10_000 (| KB |), 16 (| MB |))
foo(10_000 (= KB =), 16 (= MB =))
foo 10_000, # KB
    16      # MB

if cond1 (=> foo) && cond2 (=> bar)


```

-->

Using type checkers makes it difficult to write Ruby fluently. To make type checkers work effectively in Ruby, we need to add numerous annotations, and these annotations are done via comments. However, Ruby only has line comments. This restricts our ability to annotate specific parts of an expression or pinpoint locations within a line; we cannot, for instance, comment directly on an individual argument *within* a method's parameter list on the same line or a portion of a complex expression.

A prime example of this is type casting. To cast a specific argument of a method, we often have to resort to workarounds like inserting a line break in the middle of the argument list or adding variables solely for the type checker. In other words, we are forced to write clumsy Ruby code.

```ruby
# Before:
some_method(
  foo,
  [], #: Array[Integer]
  bar
)
# or
tmp_var_for_type_check = [] #: Array[Integer]
some_method(foo, tmp_var_for_type_check, bar)

# After:
some_method(foo, [] {= as Array[Integer] =}, bar)
# or
some_method(foo, [] {= of Integer =}, bar)

some_method(foo, [] {= @rbs: as Array[Integer] =}, bar)
some_method(foo, [] {=: Array[Integer] =}, bar)

some_method(foo, []@{ rbs: Array[Integer] }, bar)
some_method(foo, [] :( rbs: Array[Integer] ), bar)
some_method(foo, [] $( rbs: Array[Integer] ), bar)
some_method(foo, [] %rbs( Array[Integer] ), bar)

some_method(<<EOS)
EOS
{= ... =} # <- Is this for the heredoc, or for the method?

@{
  call-seq: foo(int)
  [[[<<<(((
} private (= <- allowed? =) def foo(x)
end

why not =begin
```

Personally, I find this unenjoyable and feel it detracts from the "Rubyishness" of the language. Therefore, I believe that introducing inline comments is necessary to improve this situation.

Introducing inline comments also brings the advantage of enabling previously impossible annotations. These capabilities would further enrich Ruby's expressiveness.

```ruby
# Passing generic type parameters at the appropriate location
some_generics_method{= [Int, String] =}(foo, bar)
some_generics_method@{[Int, String]}(foo, bar)

once = @{ File.read("foo") }

x = foo #: Integer
y = bar #: String
some_generics_method(x, y) #: Elem

# Locally disabling code coverage
foo = cond ? 1 : {= :nocov: =} 2

foo = if cond
  1
else # :nocov:
  2
end

# Commenting within percent literals
%w(
  foo bar #{= comment =}
  baz     #{= comment =}
)
```

```ruby
# annotation / attributes?

a = int @[ Integer ] + flo @[ Float ]
a = int {= Integer =} + flo {= Float }
```

#### Discussions
- makenowjust: We have terrible developer experience when annotating codes right now.
- makenowjust: proposing `{= … =}` but okay to change the syntax.
- matz: not very attracting to me though...
- matz: understand what is wanted.
- makenowjust: It would be convenient if syntax hilightings work inside
- mame: Is this rbs specific or not?
- makenowjust: that's up to tool authors but..
- mame: `{= @rbs: ... =}` is too annoying?
- makenowjust: works for me though?
- hsbt: in stead of introducing inline comments I would prefer simply rbs to be a part of ruby syntax.
- akr: python has similar situation (supports syntax)
- makenowjust: It could be too difficult for parser and AST tooling ecosystems to keep up with rbs changes.
- mame: `{=:nocov:=}` could confuse steep though.
- akr: Ruby tends to provide "sane default"s, than to provide extensible toolsets.  Maybe "this can be used many ways" does not fit.
- matz:  I rather want a inline comment though.
- shyouhei: this proposal is about inline comments, but it seems what is actually needed can be inline type annotations?
- makenowjust: not strictly type related but I agree everything propsed here are annotations.

#### Conclusion

- matz: understand the needs, but not the proposed syntax.

### [[Feature #21267]](https://bugs.ruby-lang.org/issues/21267) `respond_to` check in `Class#allocate` is inconsistent (jhawthorn)

* `Class#allocate` does a slow `respond_to` check to try to prevent uninitialized values via `method(:allocate).bind_call` on `MatchData`, `Refinement`, `Module`, `Complex`, `Rational`
* The `respond_to` check is easy to trick and bypass. Even if it was more strict, it does not seem to provide additional safety as there are [other ways to construct uninitialized objects](https://bugs.ruby-lang.org/issues/21267#note-1).
* Is it OK to remove?

#### Discussion

* jhawthorn: alllocate method actually checks if that method is defined using respond_to.
* jhawthorn: I guess this is because several classes undefine allocate methods.
* jhawthorn: however the check is useless.  there are ways to rereoute the restriction.
* nobu: I think it's okay to remove. Maybe this check is no longer needed.
* ko1: john's patch included an extra flag which might conflict with tagomoris' work, but with conclusion (just removing the check), it shouldn't confclit with Namespace patch anymore.
* jhawthorn:  I already updated my patch which is now okay.

#### Conclusion

* nobu: The patch seems LGTM. Go ahead.

### [[Feature #15408]](https://bugs.ruby-lang.org/issues/15408) Actually deprecate `ObjectSpace._id2ref` (byroot)

* Matz agreed to deprecate `_id2ref` for Ruby 2.7 but that was never implemented.
* [I have a PR open to finally do it](https://github.com/ruby/ruby/pull/13157), but should the deprecation mention the removal timeline, is there one?
* The main user is `drb` but there is a PR open to solve it: https://github.com/ruby/drb/pull/31

#### Discussion

* mame: There is no deprecation warning yet.
* nobu: I was forgetting that.
* jhawthorn: Difficult to make id2ref work with Ractors, so it would be good to deprecate (eventually remove)
* matz: _id2ref never meant to be a well-mannered API (so the name).
* ko1: used widely though. https://gist.github.com/ko1/25d1cee6527cbc56c84b6eac85f7d4c2

#### Conclusion

* matz: please print a deprecation warning

### [[Feature #21274]](https://bugs.ruby-lang.org/issues/21274) Show performance warnings for easily avoidable unnecessary implicit splat allocations (jeremyevans0)

* This warning only shows method calls that are allocating unnecessarily, where allocation can be avoided by using a local variable.
* This warning found unnecessary allocations in the standard library, rubygems, Rails, and Sequel.
* We are currently only using performance warnings for objects with too many shapes.
* Is this OK to commit?

#### Discussion

* mame: in order to ensure left-to-right evaluation order there are inevitable inefficiencies when writing something like `m(*a, b:)`
* ko1: We recently improved performance here but only when some circumstances allow.  This request shows when that optimisation does not happen.
* shyouhei: this optimisation breaks only when the method touches the binding of caller, which sounds esoteric.
* tompng: It feels rather a rubocop's duty to show such thing
* ko1: issuing a "warning" here seems too harsh. 
* akr: this warning makes programms verbose.

##### Conclusion

* matz: I don't want this as a warning.  I would rather prefer a fluent code.

### [[Feature #21287]](https://bugs.ruby-lang.org/issues/21287) Remove SortedSet autoload and set/sorted_set (jeremyevans0)

* Set is now a core class, and the SortedSet autoload that was in set.rb is now in core.
* This autoload only works if the sorted_set gem is installed.
* I don't think we should have this autoload, can we remove it?
* If so, do we want to deprecate the autoload before removing it?

#### Discussion

* mame: right now SortedSet is autoloaded. Isn't it too much?
* akr: Is SortedSet a tree? ... seems to be.
* hsbt: `sorted_set` gem is already removed from the core.  people has to manually `gem install`.
* shyouhei: seems the current situation is unfortunate.
* mame: it seems not working already?
```
# Ruby 3.4
$ ruby -rset/sorted_set -e 'p SortedSet'
SortedSet

$ ruby -e 'p SortedSet'
-e:1:in '<main>': uninitialized constant SortedSet (NameError)

$ ruby -e 'Set; p SortedSet'
SortedSet

# Ruby 3.5.dev
$ ./local/bin/ruby -e 'p SortedSet'
SortedSet
```

* mame: seems my ruby is too old.
* akr: nobody seriously used this, it seems.

#### Conclusion

* matz: go ahead


### [[Bug #21302]](https://bugs.ruby-lang.org/issues/21302) Remove or Fix Set#to_h (jeremyevans0)

* The method was not present in stdlib Set, and not previously approved for addition.
* However, psych started using it.
* The method is not backwards compatible with the previous Set#to_h (implemented by Enumerable#to_h).
* I recommend we remove it and fix psych, and have a pull request for that.
* If we want to keep it, I have a pull request to fix it, and add tests and documentation.
* Do we want to keep Set#to_h and have a return a true-valued hash if not given a block?

#### Discussion

* nobu: it was my mistake to add that method.

#### Conclusion

* nobu: go ahead


### [[Bug #21280]](https://bugs.ruby-lang.org/issues/21280) StringIO#set_encoding warns when backed by chilled string literal (jeremyevans0)

* This warning is bogus, as most code using this does not need changes when string literals are frozen.
* Issuing bogus warnings prompts for unnecessary code changes and should be avoided.
* StringIO#set_encoding already does not set the encoding if the string is frozen, since [Bug #11827]
* I recommend we stop setting the underlying encoding for non-frozen strings to avoid the warning.
* This could be done for only chilled strings, or for all strings if behavior should be consistent.
* Should we make this change, and if so, for chilled strings or for both chilled and unfrozen strings?

#### Preliminary discussion

```ruby
require 'stringio'
StringIO.new(-"").set_encoding('binary')
#=> no warning
# (read-only)

StringIO.new("").set_encoding('binary')
#=> warning: literal string will be frozen in the futurez (run with --debug-frozen-string-literal for more information)
# (read/write)

StringIO.new("", mode: "r").set_encoding('binary')

StringIO.new("some data").set_encoding("binary").getc
```

#### Discussion

* ko1: chilled strings causes warnings for StringIO because the backend is shared.
* ko1: OTOH frozen strings don't, because StringIO instances created from frozed strings are made read-only.
* akr: The warning is actually pointing out the right problem then? because chilled strings will eventually gets frozen in a future time.
* ko1: when the StringIO is written that is true, however this code is just setting its encoding.
* shyouhei: this is a non-issue I think because passing a string literal to a StringIO.new should just die once after the literal gets frozen.  Such code if any won't work anyways.
* nobu: I propose changing StringIO so that chilled strings instantiate readd-only StringIO insatnces.
* mame: If a warning is false positive, it should not be printed, even if it is true positive in many cases
* matz: I prefer that
* shyouhei: the proposed pull request is not just killing warnings though.

#### Conclusion

* matz: I prefer fewer false-positives than fewer false-negatives.

### [[Feature #21307]](https://bugs.ruby-lang.org/issues/21307) A way to strictly validate time input (mame)

* I want to confirm that the current tolerant behavior of Time.new is intentional, and if so, want a strict variant of Time.new.

#### Discussion

* akr: this is intentional, per POSIX (`mktime(3)`). https://pubs.opengroup.org/onlinepubs/9799919799/functions/mktime.html
* akr: this is because for us mere mortals, it is too difficult to know if a point in time exists or not, especially when there are summer time.
* akr: I guess `(2025, 1, 1, 24, 0, 0, "+00:00")` was allowed later for some reason I don't remember.

```
$ ./all-ruby -e 'p Time.new(2025,1, 1, 24, 0, 0)'
...
ruby-1.9.1-p431       -e:1:in `initialize': wrong number of arguments(6 for 0) (ArgumentError)
                        from -e:1:in `new'
                        from -e:1:in `<main>'
                  exit 1
ruby-1.9.2-preview1   2025-01-02 00:00:00 +0000
...
```

#### Conclusion

* akr: the conclusion is: yes it is.
* akr: I'm not against adding a new method though.
* matz: `Time.new(2025, 2, 29, 0, 0, 0, "+00:00", strict: true)`

### [[Feature #21258]](https://bugs.ruby-lang.org/issues/21258) Retire CGI library from Ruby 3.5
* Can I remove CGI library without CGI::Util ?
* What's the preferred migration process?
    1. We keep only cgi/escape (C impl)feature in Ruby. 
    2. We keep `cgi/util` with `cgi/escape`. The current CGI library is removed and depend cgi-util gem.
    3. We migrate cgi/escape to other class/module. The current CGI library and cgi/escape are removed.
        * `CGI.escape` -> `String.cgi_escape`
        * `CGI.escapeURIComponent` -> `String.cgi_escape_uri_component`
        * `String#dump(String::CGI_...)`
        * ...
    4. We provide cgi-util gem for migration with deprecated warning at Ruby 3.5. In next year, we will remove cgi-util gem. 
* escaping methods from `cgi/escape.so`
    * `CGI.escape`
    * `CGI.unescape`
    * `CGI.escapeHTML`
    * `CGI.unescapeHTML`
    * `CGI.escapeURIComponent`
    * `CGI.unescapeURIComponent`
    * `CGI.escape_uri_component`
    * `CGI.unescape_uri_component`

```
$ ~/gem-codesearch/gem-codesearch 'CGI\.escape' | wc -l
30960
$ ~/gem-codesearch/gem-codesearch 'CGI\.escapeEle' | wc -l
37
```

```
# Current
require "cgi"
CGI.escape("...")

# akr minimum migration
require "cgi/escape" # only changed
CGI.escape("...")

# want to remove the name "CGI" completely
require "uri"
URI.xxx_escape_xxx("...")

## or warning
require "cgi/escape"
CGI.escape("...") #=> warn for using "Use URI.xxx_escape..."

## let's builtin it
"...".percent_escape
String.percent_escape("...")
```

* akr: I prefer to introduce `cgi/escape` and ask users to use it.

```
# $ ruby -rcgi/escape -e 'CGI.escape("foo")'
```

* When retire 'cgi'?
    * naruse: next version is 4.0 so remove cgi immediately?
    * hsbt: `require 'cgi'` (or `require 'cgi/util'`)  => require `cgi/escape` and warn to use `cgi/escape`. If you need the whole CGI gem, install `cgi` gem
        * warn message: `Use "cgi/escape". If you want to use full features of CGI, use "cgi" gem.`

#### Conclusion

* Confirm `require 'cgi/escape'` introduces `CGI.escape...`
    * backport this feature to Ruby 3.4, 3.3
* Introduce dummy library 'cgi' (and 'cgi/util') which require `cgi/escape` and show warning.
* wil remove dummy libraries on future versions (discuss later when)

---

https://bugs.ruby-lang.org/issues/21142 Enumerator::Lazy#take(0) has something wrong

```
class Numbers; def each; 100.times { yield _1 }; end; include Enumerable; end
Numbers.new.lazy.take(0).each_with_index.map { _1 }.to_a #=> actual: [0, 1, ..., 99], expected: []
```

https://bugs.ruby-lang.org/issues/21168

```
# Prism accepts, parse.y rejects. Which is correct?
foo(
  bar baz do
  end
)

# FYI: both reject
[
  foo bar do
  end,
]
```

https://bugs.ruby-lang.org/issues/21205 File.stat should use statx(2)? @akr

```
ST_FIELD(s, mask) //=> s.st_mask or s.stx_mask

#if statx_available
#  define ST_MASK stx_mask
#else
#  define ST_MASK st_mask
#end
s.ST_MASK
```

https://bugs.ruby-lang.org/issues/21219 inspect_instance_variables 

matz: `#instance_variables_to_inspect`?
mame: will reply

https://bugs.ruby-lang.org/issues/21263 eval-after-require hook
https://bugs.ruby-lang.org/issues/21279 Bare "rescue" should not rescue NameError
https://bugs.ruby-lang.org/issues/21284 Array#pad
https://bugs.ruby-lang.org/issues/21300 Array#size=
https://bugs.ruby-lang.org/issues/21313 it in rescue

https://bugs.ruby-lang.org/issues/21171 @ko1 
https://bugs.ruby-lang.org/issues/21206 @mame
https://bugs.ruby-lang.org/issues/21201 @ko1

https://bugs.ruby-lang.org/issues/21194

----

https://bugs.ruby-lang.org/issues/21154

```ruby
# my_gem.rb
module MyGem
  autoload :M, "my_gem/m" # (2)
end

# my_gem/m.rb
require "tool_gem"

module MyGem
  class M < String
    autoload :X, "my_gem/m/x" # (5): circularly require "my_gem/m/x.rb"

    CONST = ToolGem.new
    CONST_X = X.new

    def foo
      X.new #=> NameError
    end
    X.new
    1.times { X.new }
    if rand < 0.5
      X.new
    end

    def initialize
      X.new
    end
    M.new
  end
end

# my_gem/m/x.rb
module MyGem
  class M # (4): fires the autoload of (2)
    class X
    end
  end
end

# main.rb
require "my_gem" # (1)
require "my_gem/m/x" # (3)
```

https://ruby.github.io/play-ruby/?code=%23+%24VERBOSE+%3D+true%0A%0Arequire+%27.%2Fmy%2Fm%2Fx%27%0A%0A%23---+my.rb%0A%0Amodule+My%0Aend%0A%0A%23---+my%2Fm.rb%0A%0Amodule+My%0A++module+M%0A++++require+%27.%2Fmy%2Fm%2Fx%27%0A++++X.new%0A++end%0Aend%0A%0A%23---+my%2Fm%2Fx.rb%0A%0Arequire+%27.%2Fmy%27%0Arequire+%27.%2Fmy%2Fm%27%0A%0Amodule+My%0A++module+M%0A++++class+X%0A++++end%0A++end%0Aend%0A

```ruby
# $VERBOSE = true

require './my/m/x'

#--- my.rb

module My
end

#--- my/m.rb

module My
  module M
    require './my/m/x'
    # X.new
  end
end

#--- my/m/x.rb

require './my'
require './my/m'

module My
  module M
    class X
    end
  end
end
```

```ruby
# a.rb
class A; B; end

# b.rb
class B; A; end

# main.rb
autoload :A, "./a"
autoload :B, "./b"
A
```

WHAT: https://ruby.github.io/play-ruby/?code=%24VERBOSE+%3D+true%0A%0Aautoload+%3AA%2C+%22.%2Fa%22%0Aautoload+%3AB%2C+%22.%2Fb%22%0Ap+A%0A%0A%23---+a.rb%0Ap+%3Aa%0Ap+B%0Aclass+A%3B+end%0A%0A%23---+b.rb%0Ap+%3Ab%0Aclass+B%3B+p+1%3B+p+A%3B+p+2%3B+end%0A
