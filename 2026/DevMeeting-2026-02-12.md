# DevMeeting-2026-02-12

https://bugs.ruby-lang.org/issues/21839

## DateTime and location

* 2026/02/12 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2026/03/17 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #19107]](https://bugs.ruby-lang.org/issues/19107) Allow trailing comma in method signature (k0kubun)

* We provided use cases in response to Matz's question, but no progress for 3 years. Can we clarify why we're not adding this?

Discussion:

* matz: I was persuaded.
* mame: `def foo(&block,)` should be prohibited, right?
* matz: Do not allow trailing comma after `def foo(&block,)` and `def foo(...,)`
* nobu: carrige return is requred? `def foo(a,)` seems strange.
* matz: no problem to allow it.

Conclusion:

* matz: accepted. I replied

### [[Feature #21800]](https://bugs.ruby-lang.org/issues/21800) New API to efficiently scan directories efficiently (byroot)

* It's common for development tools to need to recursively scan the file system, but it's slower than it should be in Ruby because the API impose N+1 `stat` syscalls.
* This would benefit many popular gems such as Zeitwerk, rubocop, etc.
* Previous discussion had concern about changing the behavior of `Dir.each_child` and other existing methods.
* I propose:
* `Dir.scan(path) { |entry_name, entry_type| }`
* `Dir.scan(path) # => [[entry_name, entry_type], ...]`
* The type is just a symbol, similar to `File::Stat#ftype`
* In case of `DT_UNKNOWN`, Ruby issue a lstat to obtain the real type (important for portability).

Preliminary discussion:

```
File.stat("/etc/passwd").ftype #=> "file"
```

* mame: I am not sure if it is consisitent between `File::Stat#ftype` and readdir's `struct dirent.d_type`

Discussion:

* matz: The name `Dir.scan` looks good
* akr: it will yield `"."` and `".."`, or not?  Excluding them looks useful.
* matz: ok.
* akr: automatically converting `DT_UNKNOWN` to `File::Stat#ftype`'s result looks good
* akr: but it may bring extra overhead, so we need to provide a way to disable the automatic conversion: `Dir.scan("dir", implicit_lstat: false) { ... }` or `Dir.scan("dir", lstat: false) { ... }` or something
* mame: What object should be yielded? Currently `File::Stat#ftype` returns a new string.
* akr: I want to use symbols: `:file`, `:directory`, `:blockSpecial`, `:link`, etc.
* akr: Or use method name convension: `:symlink` (not `:link`) from `File::Stat#symlink?`,  `:pipe` from `File::Stat#pipe?`, `:blockdev` from `File::Stat#blockdev?`, etc.
* mame: Or create a new name set? `:file` `:directory` `:chardev` `:blockdev` `:fifo` `:symlink` `:socket` `:unknown`
* matz: `blockSpecial`, ... is from tcl.
* matz: I want not to introduce a new name convension, though I admit `:symlink` and `:chardev` are definitely better than `:link` and `:characterSpecial`...

Conclusion:

* matz: `Dir.scan` is approved (including the name)
* matz: automatic conversion by lstat is also approved
* matz: disabling automatic conversion is postponed until real-world use case is shown
* matz: it should yield a symbol version of `File::Stat#ftype` like `:blockSpecial`
* matz: `"."` and `".."` should be excluded

### [[Feature #21788]](https://bugs.ruby-lang.org/issues/21788) Promote Thread::Monitor to a core class (byroot)

* Previous discussion ended on the discussion of whether a recursive Mutex would be enough
  * Yes it would, but that's what Monitor is used for in a ton of code, I don't think it's worth causing churn here.
* Monitor is about as useful as Mutex and yet one is a core class and the other is a "stdlib" extension.
* Bringing it into core allow to make is about as fast as mutex, while it is currently ~20% slower.
* I don't personally think `MonitorMixin` should be made core though. It can remain in `monitor.rb`.

Discussion:

* ko1: Is it ok to define `Thread::Monitor` instead of `::Monitor`?
* ko1: most of Ruby script requires `monitor.rb`, so no name conflicts.

Conclusion:

* matz: accept.
* matz: keep the name `monitor.rb`. Do not move to `lib/monitor_mixin.rb`

### [[Feature #21863]](https://bugs.ruby-lang.org/issues/21863) Add `RBASIC_FLAGS()` and `RBASIC_SET_FLAGS()` (eregon)

* Sounds OK to add? (I can make a PR, it's trivial)
* If not, what alternative? Deprecate RBasic->flags?

Preliminary discussion:

```
2007-05-30 /srv/gems/nodewrap-0.5.0/ext/nodewrap.c:  RBASIC(module)->flags = NUM2INT(flags);
```

Discussion:

* nobu: why are `RB_FL_SET`, `RB_FL_TEST`, `RB_FL_UNSET` not enough? They can be used for bit flags in `flags` field. Other data in `flags` (such as object type) must not be tweaked.

Conclusion:

* check why the proposed macros are really needed


### [[Bug #21855]](https://bugs.ruby-lang.org/issues/21855) Bundle `win32-registry` or implement it without `fiddle` (hsbt)

* Is it okay to extract that at Ruby 4.1 without warning cycle?

Discussion:

1. Extract `win32-registry` at Ruby 4.2 and warn it at Ruby 4.1.
2. Extract `win32-registry` at Ruby 4.1 without warning <= chosen by naruse
3. Extract `win32-registry` at Ruby 4.1 and backport that warning to Ruby 4.0.x.

Conclusion:

* hsbt: I will ask usa-san to Plan 2


### [[Feature #21835]](https://bugs.ruby-lang.org/issues/21835) Unbundle bunled gems like net-ftp (hsbt)

* How about this?

Preliminary discussion:

* mame: I want prime :-)

Discussion:

*

Conclusion:

* hsbt: I will remove net-ftp and net-pop


### [[Misc #21630]](https://bugs.ruby-lang.org/issues/21630) Suggest @Earlopain for core contributor (earlopain)

* Could this be reconsidered? Recently I've made changes/wanted to make changes that require small updates in ruby/ruby
* * https://github.com/ruby/ruby/pull/15914
* * https://github.com/ruby/ruby/pull/15999
* * https://github.com/ruby/ruby/pull/16052
* * https://github.com/ruby/ruby/pull/16085
* I have to wait on others to fix CI/help me make the changes I want to do in prism
* Sometimes prism CI doesn't tell me that a change will break ruby CI (we run `test-all` only)
* I would especially like to unbreak CI when I accidentally break it
* It would also allow me to collaborate with parse.y maintainers in the same PR like https://github.com/ruby/ruby/pull/15498 (I think)

Discussion:

* naruse: I understand earlopain's trouble, but kddnewton (as a mentor) should help them
* matz: Who can be a mentor? I am positive for the committer proposal itself but I also understand the mentoring concern

Conclusion:

* discussed but no conclusion

### [[Feature #21871]](https://bugs.ruby-lang.org/issues/21871) Add `Module#undef_const` (jeremyevans0)

* I'd like to add this method, which offers for constants the same behavior that `undef_method` offers for methods.
* This allows hiding specific constants inside a namespace, to allow for encouraging safer coding patterns in some cases.
* My expected use case for this is to assist in avoiding IDOR (insecure direct object reference) vulnerabilities.
* I've implemented support for this in a pull request.
* Is the feature itself OK?
* If so, is the implementation in the pull request OK?

Preliminary discussion:

```ruby
CONST = 1

class C
  undef_const :CONST
  begin
    p CONST
  rescue => e
    p e #=> #<NameError: uninitialized constant C::CONST>
  end

  p ::CONST #=> 1
end
```

https://ruby.github.io/play-ruby/?run=21814774663&code=CONST+%3D+1%0A%0Aclass+C%0A++undef_const+%3ACONST%0A++begin%0A++++p+CONST%0A++rescue+%3D%3E+e%0A++++p+e+%23%3D%3E+%23%3CNameError%3A+uninitialized+constant+C%3A%3ACONST%3E%0A++end%0A%0A++p+%3A%3ACONST+%23%3D%3E+1%0Aend%0A%0A

Discussion:

* matz: I feel a gap between the use case and the proposal...

Conclusion:

* matz: I will reply


### [[Misc #21872]](https://bugs.ruby-lang.org/issues/21872) `-S` with directory separator (nobu)

* Its origin Perl’s `-S` doesn't search the script with directory separator from `$PATH`.
* `sh` seems similar to Perl.
* Is this behavior intentional?

Conclusion:

* nobu: I directly talked with matz and already merged it, discussion is no longer needed

### [[Feature #21785]](https://bugs.ruby-lang.org/issues/21785) Add LEB128 pack / unpack (tenderlovemaking)

* Are we OK to merge now?  I think yes, but want to check

Conclusion:

* matz: go ahead

### [[Feature #21796]](https://bugs.ruby-lang.org/issues/21796) Add pack directive to return offset `^` (tenderlovemaking)

* This would be useful with LEB128, as well as other directives that @nobu mentions in the ticket

Discussion:

```ruby
bytes = "\x01\x02\x03"
offset = 0
leb128_value1, offset = bytes.unpack("R^", offset: offset) #=> 1
leb128_value2, offset = bytes.unpack("R^", offset: offset) #=> 2
leb128_value3, offset = bytes.unpack("R^", offset: offset) #=> 3

bytes = "\x01\x02\x03"
offset = 0
leb128_v1, leb128_v2, leb128_v3, offset = bytes.unpack("RRR^", offset: offset) #=> 1, 2, 3, (offset) 3
```

Conclusion:

* matz: go ahead

### [[Feature #15330]](https://bugs.ruby-lang.org/issues/15330) Introduce `autoload_relative` (ioquatix)

```ruby
autoload_relative :MyConstant, "my_constant.rb"

# Similar to:
autoload :MyConstant, File.expand_path("my_constant.rb", __dir__)
```

Preliminary discussion:

* More useful than `autoload` itself as it would typically be used to load relative files within a single project.
  * For example, all of these would be more correct if using `autoload_relative`: https://github.com/rack/rack/blob/550de048886a20c79ff33300556b10adcbd7426e/lib/rack.rb#L17-L63
  * Rails folks specfically said: "Rails plans to implement its reload feature that today is based on const_missing using autoload, so we would prefer if that feature would not be removed, and instead improved."
  * Xavier (fxn, who created zeitwerk) also requested it: https://bugs.ruby-lang.org/issues/18841
* This is a desirable feature requested by many respectable projects. Can we introduce it?
* PR: https://github.com/ruby/ruby/pull/16148

Conclusion:

* matz: I now accept. Go ahead

---

matz reminder

* [[Feature #6012]](https://bugs.ruby-lang.org/issues/6012) Proc#source_location also return the column
  * mame: we need revert for now?
* [[Feature #21795]](https://bugs.ruby-lang.org/issues/21795) Methods for retrieving ASTs
  * matz said "I'll write my comments on issue, but not concluded yet."

---

[[Bug #21844]](https://bugs.ruby-lang.org/issues/21844) Inconsistent ArgumentError message for Data::define.new

```ruby
C = Data.define(:a, :b)
o = C.new("a" => 1, "b" => 2) #=> #<data C a=1, b=2>

o = C.new(0 => 1); o          #=> missing keywords: :a, :b
o = C.new(0 => 1, -2 => 2); o #=> #<data C a=2, b=nil>
```

* matz: looks a bug. Let's prohibit Strings and Integers
* ko1: How about Struct.new?
* mame: It allows String keys and Integer keys.
* nobu: should we prohibit String keys?
* Matz: should be prohibited. I'll reply.

---

[[Feature #21768]](https://bugs.ruby-lang.org/issues/21768) Remove deprecated functions

* https://github.com/ruby/ruby/pull/15447
* https://github.com/nobu/ruby/tree/deprecate-rdata

[[Feature #19979]](https://bugs.ruby-lang.org/issues/19979) Allow methods to declare that they don't accept a block via `&nil`
[[Feature #21773]](https://bugs.ruby-lang.org/issues/21773) Support for setting encoding when a block is passed to `Net::HTTPResponse.read_body`
* Please send a PR

[[Feature #21781]](https://bugs.ruby-lang.org/issues/21781) Add `fetch_values` method on `ENV`
* matz: go ahead

[[Bug #21808]](https://bugs.ruby-lang.org/issues/21808) Inconsistency in support of additional newlines with boolean logical operators on new line

```ruby
1

.times {} # syntax error found (SyntaxError)
```

* matz: It is by design

https://bugs.ruby-lang.org/issues/21797 Make `Etc.nprocessors` cgroup-aware on Linux
https://bugs.ruby-lang.org/issues/21813 Add `[:forward, :...]` symbol tuple to indicate forwarding arguments when calling `Method#parameters`

```ruby
def foo(*, **, &)
  p method(__method__).parameters
  #=> [[:rest, :*], [:keyrest, :**], [:block, :&]]

  binding.eval "print(#{(method(__method__).parameters.dig(0,1))})" 
  #== print(*)
end

def bar(...)
  p method(__method__).parameters
  #=> actual: [[:rest, :*], [:keyrest, :**], [:block, :&]]
  #=> expected: [[:forwarding, :...]]
  
  # use case
  binding.eval "print(#{(method(__method__).parameters.dig(0,1))})" 
  #== print(*) <= does not work
end

bar
```
* matz: I will reject

https://bugs.ruby-lang.org/issues/21822 Expose Return Value in the ensure Block
similar ticket: https://bugs.ruby-lang.org/issues/18083

```ruby
begin
  ...
ensure => ret, err
  ...
end
```

* akr: `ret == nil` can distinguish between normal return with nil and non-local exit
* akr: it can be written as follows.

```ruby
begin
  ret = nil
  ret = begin
    ...
  end
ensure
  LOGGER.debug "return value: #{ret}"
end
```
* matz: I will reject

https://bugs.ruby-lang.org/issues/21825 Status of the universal parser implementing the Prism API
https://bugs.ruby-lang.org/issues/21767 Consider procs which `self` is Ractor-shareable as Ractor shareable

matz: no objection.

---

https://bugs.ruby-lang.org/issues/21833 Switch default hash from SipHash13 to XXH3?
https://bugs.ruby-lang.org/issues/21851 Performance difference between / operator and fdiv

```ruby
3 / 0x20_0000_0000_0001.to_f #=> 3.3306690738754696e-16
3.fdiv 0x20_0000_0000_0001   #=> 3.330669073875469e-16

0x20_0000_0000_0001.to_f == 0x20_0000_0000_0000.to_f #=> true
```

https://bugs.ruby-lang.org/issues/21858 `Kernel#Hash` considers `to_h` too

```ruby
# current
Hash([1, 2]) #=> can't convert Array into Hash (TypeError)

# after the change
Hash([1, 2]) #=> wrong element type Integer at 0 (expected array) (TypeError)

S = Struct.new(:a, :b)
obj = S.new(1, 2)

# current
Hash(obj) #=> can't convert S into Hash

# after the change
Hash(obj) #=> {a: 1, b: 2}
```

* matz: I will reply

https://bugs.ruby-lang.org/issues/21272 Class.new doesn't trigger :class TracePoint

```
$ ruby -e 'TracePoint.new { p it }.enable { 1 + 2 }'
#<TracePoint:b_call -e:1>
#<TracePoint:line -e:1>
#<TracePoint:c_call '+' -e:1>
#<TracePoint:c_return '+' -e:1>
#<TracePoint:b_return -e:1>
```

https://bugs.ruby-lang.org/issues/21870 Regexp: Warnings when using slightly overlapping \p{...} classes
```ruby
$VERBOSE = true
/[\p{Word}||\p{S}]/ #=> warning: character class has duplicated range: /[\p{Word}||\p{S}]/

p /[a||b]/ =~ "|" #=> 0
#=> warning: character class has duplicated range: /[a||b]/

p /[A-z_]/
p /[\p{Word}A-Z]/ # tompng: I want to notice that this regexp has something wrong
p /[A-Z\p{Word}]/
p /[#asdavasada#]/

Warning[:regexp_lint] = true
```

```
regparse.c:    onig_syntax_warn(env, "character class has '%s' without escape", c);
regparse.c:    onig_syntax_warn(env, "regular expression has '%s' without escape", c);
regparse.c:    onig_syntax_warn(env, "character class has duplicated range: %04x-%04x", from, to);
regparse.c:    onig_syntax_warn(env, "character class has duplicated range");
regparse.c:  onig_syntax_warn(env, "Unknown escape \\%c is ignored", c);
regparse.c:        onig_syntax_warn(env, "invalid Unicode Property \\%c", c);
regparse.c:          onig_syntax_warn(env, "invalid back reference");
regparse.c:          onig_syntax_warn(env, "invalid subexp call");
regparse.c:        onig_syntax_warn(env, "invalid Unicode Property \\%c", c);
regparse.c:            onig_syntax_warn(env, "regular expression has redundant nested repeat operator '%s'",
regparse.c:            onig_syntax_warn(env, "nested repeat operator '%s' and '%s' was replaced with '%s' in regular expression",
```
