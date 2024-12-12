# DevMeeting-2024-12-12

https://bugs.ruby-lang.org/issues/20879

## DateTime and location

* 2024/12/12 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2025/01/09 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #20930]](https://bugs.ruby-lang.org/issues/20930) Different semantics for nested `it` and `_1` (eregon)

* I allowed multiple uses of `it` in different levels of nested blocks, e.g. `files.each { YAML.parse_file(it).each { p it } }`, which is a `SyntaxError` with `_1`. Can we confirm that it's okay to introduce the behavior?

Discussion:

* k0kubun: _1 is error when nested.  But `it` has been around before _1.  Was unable to prohibit this behaviour so allowed.
* k0kubun: Eregon points out the inconsistency but I honestly want current behaviour.  Thoughts?
* mame: I don't understand why `_1` is prohibited to coexist.
* nobu: because it's too difficult.

Conclusion:

* matz: keep the current behaviour.

### [[Feature #20878]](https://bugs.ruby-lang.org/issues/20878) A new C API to create a String by adopting a pointer (byroot)

* Would be useful for many extensions.
* Allow to efficiently work with a raw, malloced buffer, and then turn it into a Ruby string without copying it.
* The pointer is adopted as soon as the function is called, if the function fails the pointer is freed immediately.
* Proposed implementation: https://github.com/ruby/ruby/pull/12143
* Current name is `rb_enc_str_adopt`, but `rb_enc_str_move` could be make sense too.

Discussion:

* matz: I'm not in favour of the name.
* shyouhei: I am, though.  "Adopt" sounds the most right terminology.
* nobu: I don't think it's necessary.
* shyouhei: by which alternative?
* nobu: create a string first and extract its pointer.
* shyouhei: don't extract a string pointer.
* nobu: aaand it's dangerous to adopt a bare pointer.
* shyouhei: while I agree with that byroot says _he_ makes sure it's safe.
* mame: I wonder if it's safe to assume xfree can deallocate asprintf return value, once mmtk lands.
* akr: I once thought it would be nice if we could adopt realpath(3) return value.

Conclusion:

* no conclusion, but let us dump our insights.


### [[Feature #20861]](https://bugs.ruby-lang.org/issues/20861) Add an environment variable so we can change the thread quantum (tenderlovemaking)

* It maintains the default quantum but allows applications to experiment with different quantums
* Lower quantums can help improve performance for applications with mixed IO / CPU workloads
* The best quantum depends on the application load, so having a configuration option would be nice
* We discussed at RubyConf and the feature seems fine but the name maybe not good enough. What is an appropriate name?

Discussion:

* ko1: per-thread quantum could not be achieved on some platforms (notably linux).  there are technical difficulties.  
* matz: naming candidcates?
* ko1: environment variables are legit here.
* ko1:
  ```
  RUBY_THREAD_DEFAULT_QUANTUM_MS
  RUBY_THREAD_SWITCH_INTERVAL
  RUBY_THREAD_TIMESLICE
  RUBY_THREAD_TIME_QUANTUM
  ```
* matz: is "quantum" a normal terminology in multithreaded studies?
* ko1: not a strange naming I guess.  Others are okay too.
* nobu: is it in milliseconds?
* ko1: yes.  Makes no sense for it to be in both nanoseconds, and/or seconds.  Msec is natural.
* akr: does it take effect when a ruby script changes this environment variable?
* ko1: Not right now.  Do we need to?
* nobu: doable though.
* akr: ENV["TZ"] does that.  I don't say we should also here, though.

Conclusion:

* matz: `RUBY_THREAD_TIMESLICE` accepted.

### [[Feature #20884]](https://bugs.ruby-lang.org/issues/20884) reserve "Ruby" toplevel module for Ruby language (hsbt)

* It's better to reserve this at Ruby 3.4, not 3.5.

Discussion:

* ko1:  What happens to current `RUBY_VERSION` etc.?
* nobu: Won't be deleted today.
* hsbt: Today? I guess forever.
* matz: +1 for domain squatting, concerning this leads lots of bikeshed pull requests...
* mame: I have `class Ruby` already so kind of sad to me if `module Ruby` gets predefined, but I am okay for the change

```
ko1@ruby-sp1:~$ ~/gem-codesearch/gem-codesearch '^Ruby ='
2016-06-22 /srv/gems/goodguide-gibbon-0.14.4/vendor/gibbon/gibbon.browser.dev.js:Ruby = (function(_super) {
2016-06-22 /srv/gems/goodguide-gibbon-0.14.4/vendor/gibbon/gibbon.browser.js:Ruby = (function(_super) {
2011-02-15 /srv/gems/mbailey-chef-0.9.12.2/lib/chef/knife/bootstrap/client-install.vbs:Ruby = Dst & "\bin\ruby.exe"
2011-05-06 /srv/gems/naked-chef-0.9.15.3/lib/chef/knife/bootstrap/client-install.vbs:Ruby = Dst & "\bin\ruby.exe"
2009-07-04 /srv/gems/ripper2ruby-0.0.2/test/fixtures/stuff.rb:Ruby = 'Just another Ruby hacker,' and print Object.const_get(:Ruby)
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/tool/eval.rb:Ruby = ENV['RUBY'] || RbConfig.ruby
2016-05-20 /srv/gems/rubylexer-0.8.0/test/data/jarh.rb:Ruby = 'Just another Ruby hacker,' and print Object.const_get(:Ruby)
2009-07-04 /srv/gems/svenfuchs-ripper2ruby-0.0.2/test/fixtures/stuff.rb:Ruby = 'Just another Ruby hacker,' and print Object.const_get(:Ruby)
```

```
$ ~/gem-codesearch/gem-codesearch '^class Ruby\b'
2013-04-18 /srv/gems/_vm-0.0.0/_ruby:class Ruby < Manager
2022-05-29 /srv/gems/arachni-1.6.1.3/components/fingerprinters/languages/ruby.rb:class Ruby < Platform::Fingerprinter
2016-04-29 /srv/gems/core_extended-0.0.10/lib/core_extended/ruby.rb:class Ruby
2023-10-28 /srv/gems/cvefixer-0.6.8/lib/taskgroups/ruby.rb:class Ruby
2018-05-17 /srv/gems/dev_osx_updater-0.0.6/lib/ruby.rb:class Ruby
2011-05-20 /srv/gems/gdb.rb-0.1.7/scripts/ruby-gdb.py:class Ruby (gdb.Command):
2013-11-25 /srv/gems/gitlab-pygments.rb-0.5.4/vendor/pygments-main/tests/examplefiles/example.rb:class Ruby < Scanner
2008-09-17 /srv/gems/grammar-0.8.1/lib/grammar/ruby/code.rb:class Ruby
2008-09-17 /srv/gems/grammar-0.8.1/lib/grammar/ruby.rb:class Ruby
2008-09-17 /srv/gems/grammar-0.8.1/test/test_ruby.rb:class Ruby < ::Test::Unit::TestCase
2017-04-28 /srv/gems/inwxupdate-0.4.0/lib/detectors/ruby.rb:class Ruby
2011-09-10 /srv/gems/linqr-0.1.0/lib/linqr.rb:class Ruby::Node
2011-09-10 /srv/gems/linqr-0.1.0/lib/source_name_evaluator.rb:class Ruby::Arg
2011-09-10 /srv/gems/linqr-0.1.0/lib/source_name_evaluator.rb:class Ruby::Call
2011-09-10 /srv/gems/linqr-0.1.0/lib/source_name_evaluator.rb:class Ruby::Variable
2011-09-10 /srv/gems/linqr-0.1.0/lib/source_name_evaluator.rb:class Ruby::Const
2014-03-14 /srv/gems/mortar-pygments.rb-0.5.7/vendor/pygments-main/tests/examplefiles/example.rb:class Ruby < Scanner
2013-12-04 /srv/gems/pygments.rb-jruby-0.5.4.2/vendor/pygments-main/tests/examplefiles/example.rb:class Ruby < Scanner
2016-01-27 /srv/gems/test_mysore-0.0.0/lib/test_mysore.rb:class Ruby
2012-09-23 /srv/gems/xiki-0.6.5/lib/xiki/ruby.rb:class Ruby
```

* nobu: for instance AST should not be there.
* mame: in which version? -> 3.4
* shyouhei: should we freeze the constant so that user land can never overwrite?
* akr: maybe tests etc. want to mock these constants.  I don't think it's worth.

Conclusion:

* matz: global toplevel `Ruby` accepted, while what should be inside of it need very careful considerations for each one of them.
* matz: Let's warn first.
    * 3.4: issue deprecation warning for toplevel constant `::Ruby` (which ever class this is)
    * 3.5: define the module.


### [[Misc #20913]](https://bugs.ruby-lang.org/issues/20913) Proposal: Adding Jeremy Evans and Burdette Lamar to `www.ruby-lang.org`’s English Editorial Team (st0012)

* They have expressed enthusiasm for improving the website during recent discussions at RubyConf in Chicago.
* Adding them would enhance our reviewing capacity for the increased contributions to the site's content.

Discussion:

* matz: Are both of them okay with this?
* shyouhei: Seems @BurdetteLamar have not contributed to ruby-lang.org so far?
* matz: I met him at Ruby Conf.  Also he has contributed to ruby's main repo.
* hsbt: person aside, I want to know what RubyCentral talked with them.  Do they plan to do anything on the site?

Conclusion:

* Propsal itself is kind of welcome, but it would be better if the surrounding context is a bit detailed.

### [[Feature #20935]](https://bugs.ruby-lang.org/issues/20935) API for Globally Enabling/Disabling Happy Eyeballs Version 2 in the Socket Class (shioimm)

- I would like to discuss the appropriateness of the API for enabling/disabling HEv2 in the `Socket` class.

Discussion:

* shioimm: This feature itself would be necessary I guess.  I would like to know if this API design is okay?
* ko1:  Tell us the use case of this option?
* shioimm:  There are cases when infrastructure is already known to have exact one DNS server.  HEv2 would degrade performance for those cases and users might want to disable this feature.
* matz: naming-wise, is `fast_fallback` acceptable here?
* mame: I guess golang uses that terminology.
* shyouhei:  Is this method argument only?  or environment variable?
* mame: or both.  would be nice to have them at once.
* ko1: should we introduce an environment variable?
* matz: I prefer envval.
* ko1: `RUBY_TCP_FAST_FALLBACK=0` for disable?
* mame: `RUBY_SOCKET_TCP_FAST_FALLBACK=0` to make clear that it is for Socket library?
* matz: default (0) should enable happy eyeballs, so `RUBY_TCP_NO_FAST_FALLBACK=1` disables happy eyeballs.
* naruse: `RUBY_TCP_FAST_FALLBACK_MODE=tradtional|happy_eyeballs_v2` 
* https://github.com/ruby/ruby/blob/6a1aaf3679ed6d467172debc10de4df1c484a21b/man/ruby.1#L703

Conclusion:

* matz: methods are okay.
* matz: envval: `RUBY_TCP_NO_FAST_FALLBACK=1` to disable happy eyeballs.

### [[Bug #20940](https://bugs.ruby-lang.org/issues/20940)] Colored syntax error from prism

* just confirmation.
* off-topic: `RUBY_DESCRIPTION` contains `+PRISM`?

Discussion:

* matz: it is okay if we can disable coloring.
* naruse: gcc/clang and other softwares support coloring.
* akr: how about to support `NO_COLOR`?

```
$ ruby -e "foo(" |& cat
-e: -e:1: syntax error found (SyntaxError)
> 1 | foo(
    |     ^ unexpected end-of-input; expected a `)` to close the arguments
  2 |
```

* matz: `NO_COLOR=1` to disable coloring.

Conclusion:

* matz: `NO_COLOR=1` to disable coloring. `1` can be replaced with any non-empty string.
  * `NO_COLOR=0` should also disable color

```
$ ruby -v
ruby 3.4.0dev (2024-12-10T23:16:24Z master c71f7faaa9) +PRISM [x86_64-linux]
$ ruby -v --parser=parse.y
ruby 3.4.0dev (2024-12-10T23:16:24Z master c71f7faaa9) [x86_64-linux]
```

* matz: remove `+PRISM` towards 3.5

### [[Feature #20875](https://bugs.ruby-lang.org/issues/20875)]: Atomic initialization for Ractor local storage

Discussion:

* ko1: `compute_if_absent` terminology is introduced.
* matz: `compute` is not known in Ruby world. `Ractor.store_if_absent(key){ init_block }` is aceptable. I prefer than `init`/`once`.
* ko1`Ractor.local_storage_store_if_absent(key){ init_block }` (local_storage) is not needed?
* matz: too long.
* mame: atomicity (thread-safety) is not represented with this name. Is it okay?
* matz: no problem.

Conclusion:

* matz: Accept `Ractor.store_if_absent(key){ init_block }`

---

https://bugs.ruby-lang.org/issues/20858 please reject > matz

https://bugs.ruby-lang.org/issues/20817 Ruby 3.4.0dev emits `warning: possibly useless use of + in void context` while Ruby 3.3.5 does not

* akr: we should accept such a minor behavior change, regardless of prism
* mame: I will close

https://bugs.ruby-lang.org/issues/20757 Make rb_tracearg_(parameters|eval_script|instruction_sequence) public C-API

---

https://bugs.ruby-lang.org/issues/20882 Provide `Boolean(...)`
```
# ENV["SOME_FEATURE"] is unset
Boolean(ENV["SOME_FEATURE"]) # => false

# ENV["SOME_FEATURE"] is unset, but allow a default?
Boolean(ENV["SOME_FEATURE"], true) # => true

# explicitly disable
ENV["SOME_FEATURE"] = "0"
Boolean(ENV["SOME_FEATURE"], true) # => false

# explicitly enable
ENV["SOME_FEATURE"] = "1"
Boolean(ENV["SOME_FEATURE"]) # => true
```
* matz: rejected

https://bugs.ruby-lang.org/issues/20885 `String#gsub?`
```
str.gsub?(...) == str.dup.gsub!(...)
```
```
# akr's proposal
str = "foo".freeze
str2 = str.gsub(/bar/, "BAR")
str.equal?(str2) #=> current: false, akr-proposed: true

# akr's proposal 2
str = "foo".freeze
# A new method "gs" returns self if no match
str2 = str.gs(/bar/, "BAR") # or sg
str2.frozen? #=> true
str.equal?(str2)
```
https://blade.ruby-lang.org/ruby-dev/33553

* matz: I will reject

https://bugs.ruby-lang.org/issues/20899 Reconsider adding `Array#find_map`
```
(1..9).find_map {|i| i * 2 if i.even? } #=> 4

ret = (1..9).find {|i| i.even? } #=> 2 or nil
ret = ret * 2 if ret

ret = (1..9).find {|i| break i * 2 if i.even? }

ret = (1..9).find {|i| i.even? }&.then { it * 2 }

(1..9).find{it.even?}&.then{nil}
(1..9).find_then{nil if it.even?}

ret = (1..9).find do |i|
  v = f(i)
  cond?(v)
end&.then { f(ret) }

ret = (1..9).lazy.map {|i| f(i) }.find {|v| v }
ret = (1..9).find_map {|i| f(i) }
ret = (1..9).lazy_map_find {|i| f(i) }

ret = nil
(1..9).find {|i| ret = f(i) }

ret = nil
(1..9).each {|i| break if ret = f(i) }

ret = (1..9).map_find { f(it) }
ret = (1..9).map{ f(it) }.find{ it }

(1..9).find {|v| v.price >= 200 }&.price
```

```
list = ['some 123', 'list 234', 'of 345', 'strings 456']
list.find_map {|s| s[/\Aof (\d+)\z/, 1] } # => "345"
list.find_map {|s| $1.to_i if s =~ /\Aof (\d+)\z/ } # => 345
list.find {|s| break $1 if s =~ /\Aof (\d+)\z/ } # => "345"
```
```
# to not explicitly use word "map" if it feels confusing
find_mapped
find_transform
find_transformed

# to connect with `Object#then` method
find_then
find_and_then

# to explicitly connect to filter_map
filter_map_first

filter_map(first: true)
```
* matz: I don't understand the need very much, but the name `find_map` is acceptable. I will reply
* nobu: Should we try it in Ruby 3.5, not 3.4?

https://bugs.ruby-lang.org/issues/13820 Add a nil coalescing operator
```
??: ATS, C#, JavaScript, PHP, PowerShell, Swift
No operator: Python, Ruby, Rust, SQL
?:: CFML, Kotlin, Objective-C
!: Freemarker, Haskell
:-: Bourne-like shells
|?: F#
//: Perl
%||%: R
If: VB.NET
```
```
def cached_foo(...)
  @cache ||= foo(...)

  @cache |||= foo(...)

  if @cache == nil
    @cache = foo(...)
  else
    @cache
  end
end
```

https://bugs.ruby-lang.org/issues/20925 Allow boolean operators at beginning of line to continue previous line
```
if cond1
  && cond2
  && cond3
  statement1
  statement2
end

if a = b && cond #=> a = (b && cond)
end

if a = b
  && cond
end

if request.secret_key_base.present? &&
  request.encrypted_signed_cookie_salt.present? &&
  request.encrypted_cookie_salt.present?

  request.encrypted_cookie
end
```

```ruby
if cond1 & # concat & and &
 & cond2
  BODY
end
```

https://bugs.ruby-lang.org/issues/20922 Should not we omit parentheses in assert calls?
```
p -1    # user may expect "p - 1"
p -1, 2 # user cannot expect "p - 1, 2", no misleading

assert_equal -1, n         # ambiguous first argument; put parentheses or a space even after `-` operator
assert_equal {}, something # syntax error

assert_equal (-1), minus_one
assert_match (/foo/), str
assert_match $ /foo/, str

/foo/ |> assert_match _, str

re = /foo/
assert_match re, str

assert_match \
  /foo/, str

assert_match  /foo/, str
```

---

https://bugs.ruby-lang.org/issues/17566 Tune thread QoS / efficiency on macOS
https://bugs.ruby-lang.org/issues/20917 `redo`/`next` in nested `begin` block causes wrong order of execution

https://bugs.ruby-lang.org/issues/20938 Percent String literal delimiter impacts string contents with parse.y
https://bugs.ruby-lang.org/issues/11177 DATAでEOF文字以降が読めない
https://bugs.ruby-lang.org/issues/19191 Implicit console input transcoding is more desirable

https://bugs.ruby-lang.org/issues/20889 `IO#ungetc` and `IO#ungetbyte` should not cause `IO#pos` to report an inaccurate position
https://bugs.ruby-lang.org/issues/20919 `IO#seek` and `IO#pos=` do not clear the character buffer in some cases while transcoding
~https://bugs.ruby-lang.org/issues/20924 `IO#readline` ignores the limit argument when the encoding is UTF-32LE and the limit would split a character~
https://bugs.ruby-lang.org/issues/20943 Constant defined in `Data.define` block

```
module (expr)::Foo
  # cref??
end

module M
  B = 1
end
M.class_eval do
  p B # error
  A = 1
end
p A
```

```ruby
module M1
  module Foo
    X = 1
  end
end
module M2
  module Foo
    X = 2
  end
end
[M1, M2].each do
  module it::Foo
    p [:A, X]
    if rand < 0.5
      p [:B, X]
    else
      p [:C, X]
    end
  end
end

# [:A, 1]
# [:C, 1]
# [:A, 1]
# [:B, 2]
```

```ruby
module M1
  module Foo
    X = 1
  end
end
module M2
  module Foo
    X = 2
  end
end
[M1, M2].each do |m|
  module m::Foo
    def self.f
      p X
    end
  end
end

M1::Foo.f #=> 1
M2::Foo.f #=> 1
```
