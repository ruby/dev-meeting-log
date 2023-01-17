---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-01-19

https://bugs.ruby-lang.org/issues/19240

## DateTime and location

* 2023/01/19 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/02/09 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #13890]](https://bugs.ruby-lang.org/issues/13890) Allow a regexp as an argument to 'String#count' (duerst)

* Recent interest, should be easy to implement

Preliminary discussion:

```ruby!
"abc".count(/.+/) #=> 1? or 3?

# => 1
#   1: 'abc'
# => 3
#   1: 'abc'
#   2: 'bc'
#   3: 'c'

"abc".count(/.+/, overlap: :no) #=> 1
```

Discussion:

* akr: What are some use cases?
* matz: I am not so positive. It looks to be overkill for String#count.  It was added for counting occurrences of characters without using Regexp.

Conclusion:

* matz: I want to know the use case

### [[Feature #19245]](https://bugs.ruby-lang.org/issues/19245) `Array#pack` should have a strict mode (byroot)

- Currently it silently modulo the arguments if they are too big for the type.
- This can can lead to silent data loss.
- I believe `pack` need a "strict mode" that would instead raise an error, e.g. `[4096].pack("C", strict: true) # => 4096 out of char range (RangeError)` (like `Integer.chr`)
- It may make sense to gradually make the strict mode the default in future versions.

Discussion:

```ruby
[4096].pack("C", strict: true) #=> 4096 out of char range (RangeError)
```

* naruse: I don't object the feature, but it would not be default
* matz: I agree. The feature looks good to find a bug, but making it default is too dangerous
* mame: `[128].pack("c", strict: true)` should be raised? (note: "c" is signed char)
* mame: `[-1].pack("C", strict: true)` should be also raised? (note: "C" is unsigned char)
* mame: if `[v].pack(fmt).unpack(fmt).first == v` is expected, the two above should be raised
* akr: it is called as wrap around in C terminology
* nobu: How about another modifier rather than `strict` keyword?
* nobu: Such as `[128].pack("c^")`. It enables users to decide out-of-range policy in one format: `[allowed, not_allowed].pack("cc^")`
* naruse: the option name `strict` looks a bit weird to me
* matz: The name is acceptable, but a bit vague. Is not clear how strict it is. It might be better if the name represents "range check". But if the option checks other things, `strict` might be better
* matz: I don't like the modifier very much
* znz: `["foo"].pack("a2", strict: true)` should be raised?
* matz: Should be raised? Maybe
* mame: I am afraid if it breaks some code
* mame: `["f"].pack("a2", strict: true)` should be raised?
* shyouhei: Filling with NUL is a documented behavior if a given string is shorter

Conclusion:

* mame: I will write the discussion to the ticket

### [[Feature #19236]](https://bugs.ruby-lang.org/issues/19236) Allow to create hashes with a specific capacity from Ruby (byroot)

- This is significantly more efficient when you need to create large hashes of known size.
- `rb_hash_new_capa(long)` was added as a C-API in 3.2
- We're still missing a similar API in Ruby.
- What about `Hash.create(capacity: 4096)` ?

FYI: `ko1@gem-codesearch:~$ gem-codesearch 'Hash\.create\b'`
https://gist.github.com/ko1/294b6b8a35dcfad4792f85c8938f822f

Discussion:

* `Hash.create(capacity: 4096)`
* shyouhei: Is the term "create" used for other classes?
* ko1: Do some gems define `Hash.create`?
* matz: The API `Hash.create` is acceptable (unless any major gems use it)
* a_matsuda: Is `Hash.new(capacity: 4096)` okay if it is prohibited under keyword argument separation?
* mame: people should write `Hash.new({ capacity: 4096 })` if you want to specify ifnone
* knu: If we do this in two phases, the feature will only be available in 3.4 after introducing a warning in 3.3.

```
ko1@gem-codesearch:~$ gem-codesearch '\bHash\.new\(\w+: '
2018-09-10 /srv/gems/adhearsion-reporter-2.3.1/lib/adhearsion/reporter.rb:        email Hash.new(via: :sendmail), desc: "Used to configure the email notifier, with options accepted by the pony (https://github.com/benprew/pony) gem"
2020-10-20 /srv/gems/bauk-core-0.0.6/lib/bauk/utils/log.rb:        @@loggers_list ||= Hash.new(level: Log4r::WARN,
2022-05-19 /srv/gems/cucumber-8.0.0/lib/cucumber/multiline_argument/data_table.rb:      NULL_CONVERSIONS = Hash.new(strict: false, proc: ->(cell_value) { cell_value }).freeze
2021-05-29 /srv/gems/da_funk-3.34.1/lib/da_funk/helper.rb:      processing = Hash.new(keep: true)
2015-06-24 /srv/gems/fling-0.1.2/spec/fling/box_spec.rb:  let(:example_message)     { Hash.new(foo: "x", bar: "y", baz: "z") }
2022-12-02 /srv/gems/gtk_paradise-0.11.45/lib/gtk_paradise/widgets/gtk2/pdfwalker/treeview.rb:        @@appearance = Hash.new(Weight: :normal, Style: :normal)
2018-06-26 /srv/gems/incline-0.3.14/test/extensions/object_extensions_test.rb:    assert Hash.new(hello: :world).respond_to?(:object_pointer)
2018-06-26 /srv/gems/incline-0.3.14/test/extensions/object_extensions_test.rb:    assert Hash.new(hello: :world).respond_to?(:to_bool)
2014-09-09 /srv/gems/ninja-collection-0.0.2/spec/ninja/hash_spec.rb:    expect(Ninja::Hash.new(hoge: 1).respond_to?(:a)).to eq(false)
2017-10-07 /srv/gems/origami-2.1.0/lib/origami/graphics/instruction.rb:        @insns = Hash.new(operands: [], render: lambda{})
2023-01-12 /srv/gems/origamindee-3.1.0/lib/origami/graphics/instruction.rb:        @insns = Hash.new(operands: [], render: lambda{})
2022-05-12 /srv/gems/osc-machete-2.0.0/lib/osc/machete/job.rb:  #     opts = Hash.new(script: '/path/to/job/dir/go.sh')
2018-10-04 /srv/gems/osu_term-1.0.0/lib/osu_term.rb:      Hash.new(full: 'Spring', abbr: 'Sp')
2017-10-07 /srv/gems/pdfwalker-1.0.0/lib/pdfwalker/treeview.rb:        @@appearance = Hash.new(Weight: :normal, Style: :normal)
2013-11-20 /srv/gems/redis-copy-1.0.0/spec/redis-copy/strategy/classic_spec.rb:      let(:options) { Hash.new(pipeline: true) }
2022-11-22 /srv/gems/rom-sql-3.6.1/lib/rom/sql/relation/reading.rb:        ROW_LOCK_MODES = Hash.new(update: 'FOR UPDATE'.freeze).update(
2017-04-16 /srv/gems/rtasklib-0.2.4/spec/models_spec.rb:    # let(:data) { Hash.new(description: "Wash dishes") }
2023-01-16 /srv/gems/rubypitaya-3.12.2/lib/rubypitaya/app-template/vendor/bundle/ruby/3.1.0/gems/cucumber-8.0.0/lib/cucumber/multiline_argument/data_table.rb:      NULL_CONVERSIONS = Hash.new(strict: false, proc: ->(cell_value) { cell_value }).freeze
2014-01-14 /srv/gems/schema_reader-0.0.5/spec/lib/schema_reader_spec.rb:    let(:attributes) { Hash.new(name: 'Fred', age: 37, email: "fred@example.com") }
2017-10-10 /srv/gems/sufia-7.4.1/spec/controllers/api/zotero_controller_spec.rb:      let(:broken_config) { Hash.new(client_key: 'foo', client_secret: 'bar') }
```

```
ko1@gem-codesearch:~$ gem-codesearch '\bHash\.new \w+: '
2019-02-09 /srv/gems/revere_mobile-0.1.1/spec/revere_mobile/client/campaign_spec.rb:        let(:params) { Hash.new name: 'Test Campaign' }
2014-05-11 /srv/gems/undo-1.0.0/lib/undo/integration/shared_undo_integration_examples.rb:  let(:object) { Hash.new hello: :world }
```

* mame: Some people may misunderstand `Hash.new(foo: 1)` as `{ foo: 1 }`? I will investigate
* knu: If we prohibit them, the authors can fix the bugs :-)
* matz: Prohibiting keywords and then introducting :capacity is ... also acceptable? (Not so willing)

Conclusion:

* mame: I will write the discussion to the ticket

### [[Feature #19179]](https://bugs.ruby-lang.org/issues/19179) Support parsing SCM_CRED(ENTIALS) messages from ancillary messages (kjtsanaktsidis)

* Various UNIX-y OS's support getting the (e)uid, (e)gid, and pid from unix sockets - some with a socket option, some with an ancillary message
* Ruby's socket module already has code to parse these, but it's only used for `Socket::Options#inspect` and `Socket::AncillaryData#inspect`
* I propose enhancing `Socket::Options` and `Socket::AncillaryData` to conveniently support this functionality where it's available

Preliminary discussion:

* samuel: It seems reasonable to me. We have plenty of time to refine the interface during 3.3 development cycle.

Discussion:

* mame: I would like to leave it to @akr
* samuel: should we ask for their feedback on the ticket directly?

Conclusion:

* akr: I will discuss it on the ticket

### [[Feature #19144]](https://bugs.ruby-lang.org/issues/19144) Ruby should set AI_V4MAPPED | AI_ADDRCONFIG getaddrinfo flags by default (kjtsanaktsidis)

* Many older and widely-deployed glibc versions have a [bug](https://bugs.launchpad.net/ubuntu/+source/glibc/+bug/1961697) which can cause a 5-second delay in DNS resolutions when IPv4/IPv6 are both enabled
* When IPv6 is not in use, the AI_ADDRINFO flag to getaddrinfo works around this bug by not making AAAA requests if the host does not have a useable IPv6 address.
* This bug often shows up in Ruby programs in particular, because Ruby accidentally overwrites one of the overlapping levels of glibc defaults, and causes AI_ADDRCONFIG to be unset where it would normally be set by default.

Preliminary discussion:

* samuel: It seems reasonable to me. We can test it during 3.3 development cycle and revert if it causes any issues.

Discussion:

* mame: I would like to leave it to @akr
* mame: I think no one expect @akr (and @samuel) can discuss this issue

Conclusion:

* akr: I will discuss it on the ticket

### [[Bug #19260]](https://bugs.ruby-lang.org/issues/19260) ruby/spec is failed with Ruby 3.3 (hsbt)

* How handles these issues?

Preliminary discussion:

* Some tests are moved and https://bugs.ruby-lang.org/issues/13671 is remaining.

Discussion:

* mame: almost all issues are already resolved
* mame: https://bugs.ruby-lang.org/issues/13671 remains. This is a bug ( or spec?) of oniguruma/onigmo, so if it is fixed on the libraries, we may backport it
* mame: `Integer#<< (with n << m)` issue is https://bugs.ruby-lang.org/issues/18518, which will be discussed later

Conclusion:

* hsbt: the ticket is already closed

### [[Feature #18285]](https://bugs.ruby-lang.org/issues/18285) NoMethodError#message uses a lot of CPU/is really expensive to call (mame)

* I have created a PR to change the message of NoMethodError based on @akr 's proposal. https://github.com/ruby/ruby/pull/6950
* Why don't you give it a try on master?

Preliminary discussion:

```ruby
42.time    #=> undefined method `time' for an instance of Integer (NoMethodError)

class Foo
  privatee #=> undefined local variable or method 'privatee' for class Foo (NoMethodError)
end

def (o=Object.new).foo
end
o.bar      #=> undefined method `bar' for #<Object: 0x0000(by any_to_s)> (NoMethodError)
```

Discussion:

* matz: Looks good
* knu: "an instance of Integer" sound better than "object Integer"
* knu: "class Foo" is okay
* a_matsuda: Keep the old message format for extended object?
* matz: How about using `any_to_s` for the case? `#<Object: 0x0000(by any_to_s)>`

Conclusion:

* matz: Give it a try


### [[Misc #16671]](https://bugs.ruby-lang.org/issues/16671) BASERUBY version policy (hsbt)

* How about my proposal?

Preliminary discussion:

```
bionic (18.04LTS): 2.5.1, standard EOL date is 2023/04
focal (20.04LTS): 2.7, standard EOL date is 2025/04
jammy (22.04LTS): 3.0, standard EOL date is 2027/04
```

Discussion:

* nobu: I want to use Ruby 2.5 or later
* k0kubun: I want to use Ruby 3.0 or later for endless method def
* mame: Is the BASERUBY version explicitly checked?
* k0kubun: Currently it is checked. If the required version is changed, the check should also be updated
* knu: /usr/bin/ruby on macOS will forever stay at 2.6.  We'll have to ditch it sooner or later.
* mame: I sometimes use docker focal image to debug ruby. But it is acceptable maybe...

Conclusion:

* Agreed with 3.0
* nobu: k0kubun will do

### [[Feature #19314]](https://bugs.ruby-lang.org/issues/19314) String#bytesplice should support partial copy (shugo)

  * Not only I but also kazuho-san wants this feature: https://twitter.com/kazuho/status/1611279616098070532
  * Can the length be omitted in the destination?

Preliminary discussion:

* Gapped buffer: http://takty.stxst.com/res/gapbuf/index.html (written in Japanese)

Discussion:

```ruby
s = "foobar"
s.bytesplice(2, 2, "ABCDE", 1, 3)
p s #=> "foBCDar"

# same as:
s.bytesplice(2, 2, "ABCDE".byteslice(1, 3))

s = "foobar"
s.bytesplice(2..3, "ABCDE", 1..4)
p s #=> "foBCDar"

s.bytesplice(2..3, "ABCDE", 1, 3) # NG
s.bytesplice(2, 2, "ABCDE", 1..4) # NG

s = "あいうえお"
s.bytesplice(1, 1, "ABCDE", 1..3) # IndexError
s.bytesplice(0, 0, "あいう", 1..3) # IndexError
```

Conclusion:

* matz: Accept.
* matz: `String#bytesplice` should consistently return self
* naruse: Change it in 3.2.1

### [[Bug #18518]](https://bugs.ruby-lang.org/issues/18518) NoMemoryError + [FATAL] failed to allocate memory for twice 1 << large (eregon)

* I would like a decision on this ticket
* I propose to raise `RangeError (shift width too big)` if `shift width` is >= 2**31 (32-bit signed integer) for all platforms, like it already behaves on 32-bit platforms.
* Current behavior is useless on 64-bit platforms, it's slow and then eventually NoMemoryError.
* This will restore consistency for this between Ruby, JRuby and TruffleRuby.

Discussion:

* nobu: I fixed it so that `1 << large` consistently raises RangeError, never `ArgumentError` due to integer overflow.

```
$ ./miniruby -e '1 << 2**60'
-e: failed to allocate memory (NoMemoryError)
```

```
# evaluation on ko1's WSL2 env (ruby 3.2.0dev (2022-04-19T05:01:08Z master fada4d24f9) [x86_64-linux])

# proposed limitation
$ time ruby -e 'p (1 << (2**31)).size'
268,435,457

real    0m0.564s
user    0m0.136s
sys     0m0.251s

$ time ruby -e 'p (1 << (2**32)).size'
536,870,913

real    0m0.572s
user    0m0.113s
sys     0m0.347s

$ time ruby -e 'p (1 << (2**36)).size'
8,589,934,593

real    0m10.910s
user    0m0.840s
sys     0m9.931s

$ time ruby -e 'p (1 << (2**37)).size'
Killed

real    1m5.538s
user    0m1.709s
sys     1m1.210s
```

* mame: The criterion looks ad-hoc to me. It is possible to calculate a bignum bigger than `(1 << 2**31)`, for example, by `Integer#*`. Should we prohibit it too?
* naruse: I don't think so
* mame: Is there any reason to prohibit only `Integer#<<`?
* matz: I like early failure. Is it possible to prohibit too huge bignums in general?
* matz: Also we need to prohibit huge array too?
* mame: I have `File.read` for 2GB+ files
* naruse: me too
* a_matsuda: me too
* akr: It is maybe reasonable to prohibit only huge bignums because many huge instances are usually created by calculation
* naruse: Bignum is used for bit-vector table

Conclusion:

* time up

### [[Bug #19289]](https://bugs.ruby-lang.org/issues/19289) RbConfig::CONFIG["STRIP"] should keep `ruby_abi_version` and `ruby_abi_version` should always be part of Ruby (peterzhu2118)

  - In dev meeting #18557 it was decided that we would keep `rb_abi_version` on both development and release (so there would be no difference in behavior between development and release).
  - In commit [cd1a0b3](https://github.com/ruby/ruby/commit/cd1a0b3caaa5446e9258c192cf483b6dfe8d7819) it was changed so that `rb_abi_version` would not be defined in release.
  - This has caused issues in gems such as gRPC and rb-sys (which compiles Rust based gems) because they expect `rb_abi_version` to be present.
  - This will continue to cause problems in the future due to this difference in behavior.

Preliminary discussion:

* https://github.com/ruby/ruby/blob/14fe7a081a8260bbbfc7929a14ec4187faaa3c25/include/ruby/internal/abi.h#L29-L30
```c=29
/* Windows does not support weak symbols so ruby_abi_version will not exist
 * in the shared library. */
```

* https://github.com/ruby/dev-meeting-log/blob/master/DevMeeting-2022-02-17.md#feature-18249-the-abi-version-of-dev-builds-of-cruby-does-not-correspond-to-the-abi-peterzhu2118

Discussion:

* nobu: I am okay to keep `ruby_abi_version` even in the release
* nobu: `ruby_abi_version` should return a fixed value (such as 0?) in all the release versions
* mame: Currently, the ruby master's abi version is "3.3.0+0". The fixed value should be different from 0? For example, `ruby_abi_version` should return 99999 or -1 or something
* nobu: the ABI version is "unsigned"
* peterzhu: 0 is good; The current ruby master should start with 1
* mame: Okay

* samuel: I don't know if it's relevant, but Windows 9 doesn't exist because of a poor design regarding "version" fields:
    * > Microsoft dev here, the internal rumours are that early testing revealed just how many third party products that had code of the form
```
if(version.StartsWith("Windows 9"))
{
    /* 95 and 98 */
} else {
    /* other release */
}
```
and that this was the pragmatic solution to avoid that. We should try to make such a field practical to the user so it can't be misinterpreted.

Conclusion:

* nobu: Keep `ruby_abi_version` as 0 in the release versions
* peterzhu: The ABI version of Ruby master should start with 1

### [[Bug #18658]](https://bugs.ruby-lang.org/issues/18658) Need openssl 3 support for Ubuntu 22.04 (Ruby 2.7.x and 3.0.x) (hsbt)

* Should we merge openssl-3.0.x into `ruby_3_0` before moving security only maintenance phase?

Conclusion:

* hsbt: discussed with @usa


### [[Feature #19347]](https://bugs.ruby-lang.org/issues/19347) Add Dir.fchdir (jeremyevans0)

* This method is useful when passing directory file descriptors through UNIX sockets or to child processes to avoid TOCTOU vulnerabilities.
* We already have Dir#fileno, but nothing that can use the returned directory file descriptor.
* Is it OK to add this method?
* If so, is the implementation in the pull request acceptable?

Discussion:

* akr: POSIX has [fchdir](https://pubs.opengroup.org/onlinepubs/009604499/functions/fchdir.html) 
* [Time-of-check to time-of-use - Wikipedia](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use)
* akr: What does `Dir.fchdir` accept?
* ko1: An integer, which is a file descriptor
* akr: How about Dir object?
* 3 ideas
  * `Dir.fchdir(int)`
    * Proposed
    * Consistent to the POSIX API
    * Can be extended for `Dir.fchdir(dir)` and `Dir.fchdir(io)`
  * `Dir.chdir(int)`
    * Do not need to add new method
    * `Dir.chdir` partially implemented on Windows because fchdir is not supported by Windows. `respond_to?` is not usable to test the availability.
    * Can be extended for `Dir.fchdir(dir)` and `Dir.fchdir(io)`
  * `dir = Dir.for_fd(int); dir.chdir`
    * More object oriented?
    * 2 more methods (`Dir.for_fd(int)` and `Dir#chdir`) are needed
    * `Dir.for_fd(io.fileno).chdir` needs two objects (IO and Dir) references one FD.  It would have a problem with GC. `closedir` frees DIR structure and closes FD. Thus, we cannot implement `autoclose: false` for `Dir.for_fd`.
    * Dir.for_fd(io.fileno, autoclose: false)
        * use dup(io.fileno) and close that, leave the original one alone?

```ruby=
class Dir
  def self.for_fd(descriptor, autoclose: true)
    unless autoclose
      # Caller has indiciated they don't want to close the original fd.
      descriptor = dup(descriptor)
    end

    new(descriptor)
  end
end
```

Conclusion:

* 


### [[Bug #19237]](https://bugs.ruby-lang.org/issues/19237) Hash default_proc is not thread-safe to lazy-initialize value for a given key (jeremyevans0)

* Similar to `hash[key] ||= value` in multiple concurrent threads.
* One workaround is using a Mutex and `mutex.synchronize{hash[key] = value unless hash.key?(key)}` inside the default proc.
* Another workaround is to avoid using a default proc to set values in a hash accessed by multiple concurrent threads.
* Is this a bug? If not, should we update the documentation of `Hash.new` and/or `Hash#default_proc=` regarding use with concurrent threads?

Conclusion:

* matz: The performance penalty is not acceptable.  Addition to the documentation looks good enough.
* ko1: Using a mutex may cause a deadlock depending on what the proc does, so it's not a working solution.


### [[Bug #19286]](https://bugs.ruby-lang.org/issues/19286) What should kwargs' arity be? (jeremyevans0)

* Do we want to change how arity is calculated for methods/procs accepting/requiring keywords?
* I don't think it is worth breaking backwards compatibility here (#parameters can be used for keyword argument details).

Discussion:

* matz: the current behavior is fair
* a_matsuda: `Method#keyword_arity`? But `Method#parameters` is good engouh

### [[Bug #19293]](https://bugs.ruby-lang.org/issues/19293) The new Time.new(String) API is nice... but we still need a stricter version of this (jeremyevans0)

* Do we want to add a keyword argument to Time.new for stricter parsing, or another Time method that is stricter?

Discussion:

```ruby
Time.new("2023")
# Ruby 3.2
#=> 2023-01-01 00:00:00 +0900

# Proposed
#=> Error

Time.new("2023-01-01 00:00:00") # OK
Time.new("2023-01-01 00:00")    # NG in 3.2.0
Time.new("2023-01-01 00")       # NG in 3.2.0
Time.new("2023-01-01")          # OK in 3.2.0, NG in 3.2.1
Time.new("2023-01")             # OK in 3.2.0, NG in 3.2.1
Time.new("2023")                # OK in 3.1.0, OK in 3.2.1
Time.new("2023", "12")          # OK in 3.1.0, OK in 3.2.1
Time.new("2023", "12", "31")    # OK in 3.1.0, OK in 3.2.1

Time.new("   2023-01-01 00:00:00  \n\n ") # OK in 3.2.0, ?? in 3.2.1

Time.new("2023-12-31 00:00:00") # 2023-01-01 00:00:00 in Ruby 3.0
Time.new("2023", nil)           # 2023-01-01 00:00:00 

```

* naruse: `Time.new("2023")` is 1.9.2 feature. We can't deprecate on 3.2.1. Need to continue the discussion for Ruby 3.3.
* a_matsuda: I hope deprecate this notation. Warning it from Ruby 3.3.0?
* ko1: how about `Time.new("2023-01-01")`?
* naruse: NG from 3.2.1.
* mame: how about `Time.new("   2023-01-01 00:00:00  \n\n ")`?
* naruse: NG from 3.2.1.
* a_matsuda: why should we prohibit them?
* naruse: Should be strict them at first and if we need to relax the notation, please make a new issue. We can discuss again.
* knu: For parsing "YYYY-mm-dd", we could add `Date.new("2023-01-01")` in future.

Conclusion:

* naruse: NG from Ruby 3.2.1 for the following case:
  * `Time.new("2023-01-01")`
  * `Time.new("   2023-01-01 00:00:00  \n\n ")`
* naruse: Need to revisit about `Time.new("2023")` for Ruby 3.3 (allow it on Ruby 3.2.x).

### [[Bug #19333]](https://bugs.ruby-lang.org/issues/19333) Setting (Fiber Local|Thread Local|Fiber Storage) to nil should delete value in order to avoid memory leaks.

```ruby=
# How to clear this memory usage?
Thread.current[:key] = value
Thread.current.thread_variable_set(:key, value)
Fiber[:key] = value
```

Is it acceptable? The behaviour of setting nil deletes the association and frees all associated memory.

```ruby=
# How to clear this memory usage?
Thread.current.key?(:key) => true/false
Thread.current[:key] = nil

Thread.current.thread_variable?(:key) #=> true/false
Thread.current.thread_variable_set(:key, nil)

Fiber.current.storage => {key: value or nil}
Fiber[:key] = nil
```

Preliminary discussion:

* It's mainly a problem for thread pools where threads can accumulate an unbounded number of thread locals without having any way to clear them.
    * Maybe we shouldn't be encouraging a design where pooling Thread instances at the Ruby level is a desirable approach, `Thread.new` should be as fast or faster than maintaining a Ruby-implemented thread pool.

Some challenges:

* `thread_variable?(x)` was broken until recently: https://bugs.ruby-lang.org/issues/16906

* The user might use nil as a "valid value" and with this change it will be more expensive to remove the fiber local than to just assign, also more expensive if later that fiber local is set again to non-nil (delete+insert vs set+set).

* This is problematic when implementing fiber locals with Shapes, which TruffleRuby currently does, because it causes a lot more Shape polymorphism if fiber locals can be removed, so makes fiber locals slower.

Discussion:

* samuel: If people don't want this functionality they can explicitly use `class Thread` and `class Fiber` variables/attributes:

```ruby=
class Thread
    attr :my_thread_local
end

class Fiber
    attr :my_fiber_local
end
```

* ko1: It is possible to use `Thread.current[:key] = nil` if only exisgint of `key` is important. Can we ignore this situation?
* matz: Yes. They should rewrite their program using `= true` or something.

Conclusion:

* matz: I will reply
    * samuel: Please consider whether implementation specific behaviour is acceptable or whether we should standardise it. In either case, I'll add extra documentation.
