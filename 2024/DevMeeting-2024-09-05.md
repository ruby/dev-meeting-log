# DevMeeting-2024-09-05

https://bugs.ruby-lang.org/issues/20660

## DateTime and location

* 2024/09/05 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/10/03 (Thu) 13:00-17:00 JST @ Online
    * matz: 10/10 is NG to me

## Announce

### About release timeframe

## Check security tickets

[secret]

## Misc

### r-l.o email
:@shyouhei: I still cannot send/receive using  my ruby-lang.org email address.  Can you help?

## Ordinary tickets
### [[Feature #20627]](https://bugs.ruby-lang.org/issues/20627) `require` on Ractor should run on the main Ractor

#### Discussion
- ko1: The idea is already approved, what remains is the naming.
- ko1: `Ractor.require` :arrow_right:  `Ractor._require` done.
- ko1: Also I need `Ractor._activated` which runs at most once per process lifetime.
- nobu: I'm not quite sure if the proposed implementation works.

#### Conclusion

- matz: accepted, as far as the bug mentioned by @nobu is fixed.

### [[Feature #15554]](https://bugs.ruby-lang.org/issues/15554) warn/error passing a block to a method which never use a block (Re: strict mode)

#### Discussion
- ko1: I tried this and saw lots (thousands) of false positives.
- ko1: we need strict / non-strict modes for this warning.
- ko1: how we do that?
- nobu: NG for environment variables only.
- shyouhei: library authors might want to suppress this warning for their own gems.
- akr: that would not happen when relax mode is the default.
- matz: maybe in a future we want code annotations... but not today.
- mame: How about issue warning only outside of gems?
- nobu: One can filter out warnings inside of gems using `Warning.warn`.

#### Conclusion
- no conclusion yet, continue discussion.

### [[Feature #20594]](https://bugs.ruby-lang.org/issues/20594) A new String method to append bytes while preserving encoding (byroot)

* Continuation from previous meeting
* Alan proposed the method to be named `append_as_bytes`, to be variadic and accept both String and Integer (so like the original proposal).
* For integer the behavior would be the same as `setbyte`, so mask `0xff`.
* Early [implementation prototype show a ~13% gain on our benchmark](https://github.com/Shopify/protoboeuf/pull/116#issuecomment-2309750217).

#### Preliminary discussion:

```ruby
# buf <<
#   255 << title.bytesize << title <<
#   255 << body.bytesize << body

buf = ""
buf.append_as_bytes(255, title.bytesize, title, 255, body.bytesize, body)
```

* `buf.append_as_bytes(256)` should write `"\x00"`, not raise an exception
  * as like `buf.~~setbyte~~(256)`

#### Discussion:

* matz: nobody is against the API?
* ko1: maybe "as" is the new terminology.
* matz: sounds better than byteappend to me.

#### Conclusion:

* matz: accepted

### [[Feature #20684]](https://bugs.ruby-lang.org/issues/20684) Add optimized instructions for frozen literal Hash and Array (byroot)

* Allow to avoid having to define `EMPTY_HASH = {}.freeze` or `EMPTY_ARRAY.freeze` constant in hotspots (see code samples in the issue).
* Also give some minor gains when defining frozen constants.
* Not a big optimization, but a very small patch and help usability.

#### Discussion:

* matz: what prevents this from being approved?
* mame: Optimisation of `String#freeze` eventually introduced frozen-string-literal.  This can also?
* akr: I don't think magic comments work for empty arrays etc.
* ko1: This proposal changes the identity of frozen empty hash.
*  nobu: It seems the proposed patch has a problem (might freeze non-empty arrays).
    * `[1].freeze` is also optimized (but not dedup'ed?)

#### Conclusion:

* matz: I see no problem. `[1].freeze` is cruby's implementation detail that should not be a stable specification of the language.

### [[Bug #20710]](https://bugs.ruby-lang.org/issues/20710) Reducing Hash allocation introduces large performance degradation (probably related to VWA)

#### discussion
- mame: my investigation is that because of vwa this reduces the frequency of full gc.
- ko1: my theory is vwa might not handle situations when only one heap is used very well.
- shyouhei: I'm not entirely sure what is going on here.  We need better observability.

#### conclusion
- matz: this is a bug that should be fixed.

### [[Bug #20675]](https://bugs.ruby-lang.org/issues/20675) Parse error with required kwargs and omitted parens (jeremyevans0)

* Is consistency with method parameters worth breaking compatibility in this case?

#### Discussion:

* mame: what is your expectation?
* matz: would be simpler to make them continuous, but backward compatibility is a concern.

#### Conclusion:

* matz: I replied

### [[Feature #20309]](https://bugs.ruby-lang.org/issues/20309) Bundled gems for Ruby 3.5 (hsbt)

* Discuss about `benchmark`,`irb` and `reline`

#### Discussion:

* shyouhei: what happens for `binding.irb`?
* hsbt: we need to take care.  But I guess it's possible to keep the current behaviour.  Let me try.

```ruby
# t.rb
gem 'debug'
require 'debug'

# Gemfile is empty

#=>
$ bundle exec ruby t.rb
/home/ko1/ruby/install/trunk/lib/ruby/3.4.0+0/bundler/rubygems_integration.rb:253:in 'block (2 levels) in Kernel#replace_gem': debug is not part of the bundle. Add it to your Gemfile. (Gem::LoadError)
        from t.rb:1:in '<main>'
```

ko1: add a method which load newest gem on "bundle exec" context in bundler or rubygems?

----

Other libraries:

* mame: default to standard libs for some libs?
* hsbt: Singleton or tempfile? Discuss next year.
* hsbt: erb has value to be default gem which can be updated.

<details>

```
ext/date/date.gemspec
ext/digest/digest.gemspec
ext/etc/etc.gemspec
ext/fcntl/fcntl.gemspec
ext/fiddle/fiddle.gemspec
ext/json/json.gemspec
ext/openssl/openssl.gemspec
ext/pathname/pathname.gemspec
ext/psych/psych.gemspec
ext/stringio/stringio.gemspec
ext/strscan/strscan.gemspec
ext/win32ole/win32ole.gemspec
ext/zlib/zlib.gemspec
lib/English.gemspec
lib/bundler/bundler.gemspec
lib/cgi/cgi.gemspec
lib/delegate.gemspec
lib/did_you_mean/did_you_mean.gemspec
lib/erb.gemspec
lib/error_highlight/error_highlight.gemspec
lib/fileutils.gemspec
lib/find.gemspec
lib/forwardable/forwardable.gemspec
lib/ipaddr.gemspec
lib/logger/logger.gemspec
lib/net/net-protocol.gemspec
lib/open-uri.gemspec
lib/open3/open3.gemspec
lib/optparse/optparse.gemspec
lib/ostruct.gemspec
lib/pp.gemspec
lib/prettyprint.gemspec
lib/prism/prism.gemspec
lib/pstore.gemspec
lib/rdoc/rdoc.gemspec
lib/readline.gemspec
lib/resolv.gemspec
lib/ruby2_keywords.gemspec
lib/securerandom.gemspec
lib/set/set.gemspec
lib/shellwords.gemspec
lib/singleton.gemspec
lib/syntax_suggest/syntax_suggest.gemspec
lib/tempfile.gemspec
lib/time.gemspec
lib/timeout.gemspec
lib/tmpdir.gemspec
lib/tsort.gemspec
lib/un.gemspec
lib/uri/uri.gemspec
lib/weakref.gemspec
lib/yaml/yaml.gemspec
```

</details>

Conclusion:

*

---

### [[Bug #19744]](https://bugs.ruby-lang.org/issues/19744) Namespace on read (mame)

#### current situation

* tagomoris: currently I have one remaining segv.  Other than that `test-all` is passing.
* mame: is `make test-bundled-gems` green?
* hsbt: `make check` is recommended for your daily life.
* tagomoris: I hope I'm going to issue a pull request this month.

#### timeline

* matz: It's September.  When do we merge this?
* shyouhei: someday until 12/24?
* mame: I NEED @yahonda's rails test long before holidays.
* shyouhei: Maybe we want to push the merge button as soon as possible.
* mame: Do we merge this when something breaks?  Maybe yjit etc.?
* matz: Okay for namespace not interacts with yjit, as far as this is experimental.

#### refinements

- tagomoris: There are three situations.
    - builtin: Where classes like e.g. String reside.  These classes shall be visible from everywhere (otherwise you cannot create a string from inside a namespace which is surreal.)
    - main: Where classes like e.g. Gem reside.  This is the namespace that is implicitly created when the process boots up.
    - other userland namespaces that are created on-demand.
- tagomoris: in my current implementation, main namespace's Object class includes a module that interacts with gem_original_requrire.  However builtin namespace cannot use this technique (no gem).  So we use refinements there.
- mame: bootsnap defines require in main.  Then require inside of builtin namespace should use it...?
- hsbt: in case of zeitwerk, zeitwerk's require uses bootsnap's require, which uses ruby's original require.
- mame: the problem is that when a program has `require "foo"`, what happens is not obvious from the interpreter's POV.
- tagomoris: I don't think `require`s in builtin namespaces need bootsnap.

#### detection of namespace
- ko1: how do your requrie detect the destination namespace?
- tagomoris: I thought I can scan CREF.
- ko1: maybe you can scan the stack?
- ko1: or maintain a thread local variable.
- mame: when that happens?
- ko1: inside of a require.
- mame: the "require" here is not obvious because it can be redefined.

---

### [[Bug #20693]](https://bugs.ruby-lang.org/issues/20693) Dir.tmpdir should perform a real access check before warning about writability (kjtsanaktsidis)

* A minor paper-cut, but I ran into this while running Ruby's tests in an unprivileged Docker container
* `File::Stat#writable?` is really just guessing; to really know if a directory is writable, you need to ask the OS (through a call to `File.writable?`)
* There's a broader question about whether we should deprecate `File::Stat#writable?` and `File::Stat#readable?` altogether, since they can't possibly be correct in 100% of cases. But I'm happy just to fix this one case in `Dir.tmpdir` for now.

#### Discussion:

* mame: in short KJ wants to use File.writable? instead of File::Stat
* nobu: not against it.
* nobu: The world-writable check below could also be affected by ACLs.
* mame: true, but we cannot say if a world-writable directory is in fact protected by ACLs or not.

#### Conclusion:

* akr: I want a comment here why this change is necessary.  Otherwise LGTM.

### [[Feature #20707]](https://bugs.ruby-lang.org/issues/20707) Move `Time#xmlschema` into core (byroot)

#### Discussion:
- akr: RSS has another implementation of ISO8601: w3cdtf method.
- mame: Time#xmlschema is not everything that ISO8601 can represent.
- akr: Yes. ISO8601 permits many variants.
- nobu: Time.new now accepts this string so generation could also be in core.
- matz: I don't really like the "xmlschema" naming though.

#### Conclusion:

* matz: Accepted
* matz: `Time#iso8601` is also accepted to move it to the core

---

### [[Feature #20673]](https://bugs.ruby-lang.org/issues/20673) Enable native SOCKS support by default (znz)

#### Discussion:

- matz:  This pull request builds, but mandates a socks runtime library.
- akr: -1 for additional runtime dependency.

### [[Bug #20708]](https://bugs.ruby-lang.org/issues/20708) EINTR while opening fifo isn't retried

```
$ rm -f /tmp/badger && mkfifo /tmp/badger && ./ruby --disable-gems -we '
trap(:USR1) {}
Thread.new { sleep 1; Process.kill(:USR1, $$) }
IO.read("/tmp/badger")
'
-e:4:in 'IO.read': Interrupted system call @ rb_sysopen - /tmp/badger (Errno::EINTR)
        from -e:4:in '<main>'
```

#### Conclusion:

- nobu fixed already.

### [[Feature #20705]](https://bugs.ruby-lang.org/issues/20705) Should "0.E-9" be a valid float value?

#### Discussion:
- mame: `0.E-9` is `(0.).E(-9)`.
- nobu: The problem is `Float("0.E-9")`

```ruby:Current behavior
Float("0.E-9") #=> invalid value for Float(): "0.E-9"
Float("1.")    #=> invalid value for Float(): "1."
"0.E-9".to_f   #=> 0.0
"1.E-9".to_f   #=> 1.0 (expected?: 1.0e-09)
"1.".to_f      #=> 1.0
Float("0x1p+0") #=> 1.0
"0x1p+0".to_f   #=> 0.0
```

```ruby:matz
Float("1.E-9") #=> 1.0e-09
Float("1.")    #=> 1.0
```

### [[Feature #20703]](https://bugs.ruby-lang.org/issues/20703) Alias `StringIO#string` to `StringIO#to_s`/`to_str`

```
require "stringio"
sio = StringIO.new('my string')

# current behavior
p sio    #=> #<StringIO:0x00007ff905a62e70>
puts sio #=> #<StringIO:0x00007ff905a62e70>
```

* matz: to_str must not be changed
* matz: it is possible to improve StringIO#inspect as showing its size, but it is a different issue
* nobu: I will reject

### [[Feature #20702]](https://bugs.ruby-lang.org/issues/20702) Add `Array#fetch_values`

```
irb(main):001> ENV.methods - {}.methods
=> []
irb(main):002> {}.methods - ENV.methods
=>
[:compare_by_identity?,
 :compare_by_identity,
 :deconstruct_keys,
 :<=,
 :>=,
 :dig,
 :<,
 :>,
 :to_proc,
 :flatten,
 :compact!,
 :default=,
 :default_proc,
 :default_proc=,
 :default,
 :transform_keys,
 :transform_keys!,
 :transform_values,
 :transform_values!,
 :fetch_values,
 :merge]
```

```ruby
h = { a: 1, b: 2, c: 3 }
h.fetch_values(:b, :c) #=> [2, 3]
h.fetch_values(:b, :d) #=> IndexError
h.fetch_values(:b, :d){ 42 } # => [2, 42]

ENV.fetch_values # no method

a = [0, 1, 2]
a.fetch_values(0, 2) # => [0, 3]
a.fetch_values(0, 4) # => IndexError
a.fetch_values(0, 4){ 42 } # => [0, 42]
```

* matz: `Array#fetch_values` is accepted
* matz: `ENV.fetch_values` is also accepted

### [[Bug #20690]](https://bugs.ruby-lang.org/issues/20690) URI.encode_www_form_component method escapes tilde when it's not supposed to

* knu: The method does not strictly implement a specific RFC based behavior.  It is provided as is and changing its behavior would likely break existing use cases.
* naruse: WHATWG URL has the latest definition, which excludes the tilde from the characters that should be percent-encoded.

https://github.com/ruby/uri/pull/117

---

https://github.com/ruby/ruby/pull/10864 to @akr 
