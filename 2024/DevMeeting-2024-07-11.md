# DevMeeting-2024-07-11

https://bugs.ruby-lang.org/issues/20574

## DateTime and location

* 2024/07/11 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/08/01 (Thu) 13:00-17:00 JST @ Online
  * matz: Sorry, I have an appointment  on 8/8.

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets
### [[Bug #20433]](https://bugs.ruby-lang.org/issues/20433) Hash.inspect for some hash returns syntax invalid representation (tompng)

* Change Hash#inspect style to `{key: value}` or not

Preliminary discussion:

```ruby
p({ :key => 42 })  #=> {key: 42}     # if the key is a symbol
p({ :== => 42 })   #=> {"==": 42}    # if the key is a symbol and quotes are needed
p({ "str" => 42 }) #=> {"str" => 42} # otherwise (note that `=>` is surrounded by spaces)
```

Need to update these tests. The required change was within the expected range.

```
bootstraptest
test/coverage
test/error_highlight
test/rdoc
test/reline
test/ruby
test/rubygems
spec/bundler
spec/ruby
```

Two bundled gem tests failed.

```
minitest (4 failure)
debug (2 failure)
```

Discussion:

[previous disucssion](https://ruby.esa.io/posts/101#[Bug%20#20433]%20Hash.inspect%20for%20some%20hash%20returns%20syntax%20invalid%20representation%20(jeremyevans0))
* mame: This is an update for previous discussions.
* mame: basically the failed tests include `inspect`'s output for their expectations.
* hbst: It would also fail on rails, given minitest does.
* mame: minitest having such expectations is kind of inevitable.  They are testing framework.
* matz: To me it feels the aftermath is too big.
* mame: I'd like to use this for a while and rethink later.

Conclusion:

* matz: Let me have time for this.  Will write a reply.


### [[Feature #20576]](https://bugs.ruby-lang.org/issues/20576) Add MatchData#bytebegin and MatchData#byteend (shugo)

* Matz doesn't like the names bytebegin and byteend.
   * Other names: byte_begin/byte_end, begin_in_bytes/end_in_bytes
   * If byte_begin/byte_end are chosen, should we add aliases for existing methods such as byteslice?
   * Since these methods are for advanced users, it may be more important to prioritize consistency with existing methods over readability.

Discussion:

* shugo: I proposed `byte_end` but I don't honestly like it.  I want `byteend`.
* naruse: I can live with `byteslice` / `byte_end` mismatch though.

Conclusion:

* matz: Hmm.

### [[Feature #20594]](https://bugs.ruby-lang.org/issues/20594) A new String method to append bytes while preserving encoding (byroot)

* When working with binary (messagepack, protobuf) or mixed encoding protocols (Redis, Memcached, etc), it's common to assemble string with different encodings.
* Typical example is to have a `BINARY` buffer and assemble some `UTF-8` strings in it.
* In such cases `Encoding::ASCII_8BIT` is very inconvenient as it will do encoding negociation.
* I propose a `String#byteconcat` method that appends string content while preserving the left hand side encoding.
* The logical signature would be to behave like `String#concat` (take varg args, accept String or Integer, return `self`)
* YJIT team is concerned about the performance implication of varargs, and would prefer a single argument.

Preliminary discussion:

```ruby
"foo\xff".b << "あ"  #=> BINARY (ASCII-8BIT) and UTF-8 (Encoding::CompatibilityError)
"foo\xff".b + "あ"   #=> BINARY (ASCII-8BIT) and UTF-8 (Encoding::CompatibilityError)
"foo\xff".b << "あ".b # OK
"foo\xff".byteconcat("あ") # proposed
```

```ruby
p s = 'abc'.encode('US-ASCII').bytesplice(1, 1, 0xf0.chr) #=> "a\xF0c"
p s.encoding #=> #<Encoding:ASCII-8BIT>

p s = 'あいう'.encode('sjis').bytesplice(2, 2, 0xf0.chr + 0xf1.chr)
#=> `bytesplice': incompatible character encodings: Windows-31J and ASCII-8BIT (Encoding::CompatibilityError)
```

```ruby
str.byteconcat(str2) == str.bytesplice(str.length, 0, str2)
# except encoding mismatch behavior
```

* mame: `force_concat`?
* nobu: can we use `IO::Buffer`?

Discussion:

* shyouhei: undestand what is needed.
* mame: The proposed functionality cannot be achieved right now.
* shugo: Is this 'byte'?  Because the proposal has nothing to do with byte-index.
* mame: Other byte-something APIs like bytesplice are different from this one.
* mame: honestly I want `binary << unicode` to work.
* naruse: against that idea, that would make mojibake.
* shyouhei:  why not StringIO?
* shugo: How about `binaryconcat`?
* naruse: it should raise unless the receiver is ASCII-8BIT
* ko1: `binconcat` or `binary_concat`?
* matz: `binary_concat`?

Conclusion:

* matz: feature accepted, naming not sure.  `force_concat` sounds better than `byteconcat`.  But `force_concat` is also NG because what is forced here is not obvious from the name.

### [[Bug #20627]](https://bugs.ruby-lang.org/issues/20627) `require` on Ractor should run on the main Ractor (ko1)

* is it acceptable approach?

Discussion:

* ko1: require on Ractor is prohibited now.  This is harsh.  For instance autoload kills everything.
* ko1: I propose main Ractor to interrupt require in other Ractors, and load things there.
* ko1: Simple so far, but here comes the rubygems.  We need to handle their version of `require`. ... Or they need to undestand my version of `require`, either way.
* hsbt: Make it so that one code can work on both ruby 3.3 / 3.4.
* hsbt: At least rubygems, zeitwerk, and bootsnap each individually tweaks require.  Which one should use this new method?
* nobu: Maybe everyone.

Conclusion:

* matz: no problem for proposed approach and `Ractor.main?` and I'd prefer `Ractor.__require`
* ko1: I'll try auto-prepending technique.

### [[Bug #20626]](https://bugs.ruby-lang.org/issues/20626) `defined?(@ivar)` should return nil when `@iv` can raise on Ractor (ko1)

* it is more compatible with existing code.

Discussion:

* ko1: FileUtils has this feature.  The FileUtils module (which is sharable) has an instance variable, and issues `defined?`.
* akr: `@ivar = something unless defined?(@ivar)` would break if it returns nil.

Conclusion:

* ko1: `defined?(@ivar)` should either be `"instance-variable"` (when defined) or `nil` (otherwise).
* nobu: Not sure how to fix FileUtils though.



### [[Bug #20593]](https://bugs.ruby-lang.org/issues/20593) `Kernel#format` emits a `too many arguments for format string` warning when called with a single hash and no key is used (byroot)

* `format` emits warnings when too many positional arguments were passed, but doesn't do so if too many hash keys were passed.
* This makes sense to me because extra named argument are generally not indicative of a bug.
* However if no key is consumed at all, it does emit a warning: `format("test", unused: 2) #  warning: too many arguments for format string`
* I think it's a bug and no warning should be emitted.

Preliminary discussion:

```ruby
$VERBOSE = true
format("%s", 1, 2)               # warning: too many arguments for format string
format("%{a}s", a: 1, unused: 2) # no warning
format("test", unused: 2)        # warning: too many arguments for format string
```

* advantages of warn all for keyword arguments:
    * easy to detect typo
* advanrages of warn nothing for keyword argments:
    * easy to show the part of Hash
    * no compatibility issue

Discussion:

* byroot: I found this one doesn't make quite sense.  For positional arguments, mismatch might be a bug, but in case of named ones it seems to me of no use.
* matz: they should be constsent at least.
* byroot: the reason I think it's reasonable not to warn is that the format string might come from some configuration file.
* matz: OK, that sounds legit.

Conclusion:

* matz: accepted.  Make it not to issue warning then.


### [[Feature #19979]](https://bugs.ruby-lang.org/issues/19979) Allow methods to declare that they don't accept a block via `&nil` (ufuk)

* Based on the discussion in DevMeeting at RubyKaigi 2024, it seems this feature is still considered valuable.
* I've rebased @nobu 's [original work](https://github.com/ruby/ruby/pull/10020) and built upon it to create a PR: https://github.com/ruby/ruby/pull/11065
* The PR adds support for the new syntax for both parsers and both compilers (`parse.y` and `prism`)
* Do we still want to go ahead with this feature?

Discussion:

* matz: Syntax is ... under moratrium.
* matz:  Understand its merit though.

Conclusion:

* matz: Postponed for now, sorry!

### [[Bug #20606]](https://bugs.ruby-lang.org/issues/20606) `Thread#thread_variable_get` and `Thread#thread_variable?` don't raise `TypeError` exception for incorrect argument value when thread-local variable storage is not initialised yet (andrykonchin)

* These methods raise `is not a symbol nor a string (TypeError)` when called with a `key` that is not a String or a Symbol **only** when there are some thread local variables already assigned for a thread. And don't raise any exception (and return `nil` or `false` correspondingly) if there is no any variable assigned yet.
* Is it expected behaviour? If not - is it OK to fix this issue (and validate the argument value before checking whether a storage is initialised) in the next Ruby release (3.4)? Or a deprecation warning should be added instead in 3.4 to have the issue fixed later?

Preliminary discussion:

* closed

### [[Bug #20505]](https://bugs.ruby-lang.org/issues/20505) Reassigning the block argument in method body keeps old block when calling super with implicit arguments (jeremyevans0)

* This makes behavior when reassigning block argument inconsistent with reassigning positional/keyword arguments.
* Do we want to make any changes here?

Preliminary discussion:

```ruby
class C
  def foo
    yield
  end
end

class D < C
  def foo(&blk)
    blk = proc { p :proc_in_D }
    super
  end
end

D.new.foo { p :proc_in_toplevel }
#=> current:  :proc_in_toplevel
#   expected: :proc_in_D
```

```ruby
class C
  def foo(...)
    p(...)     #=> :c
  end
end

class D < C
  def foo(*a, &blk)
    a = [:c]
    blk = proc { p :proc_in_D }
    super
    #=> super(*a)
  end
end

def foo((x, ), *)
  super
  super([x, ], *) # => ...??
end
def foo(_, _, _)
  super
  super(_, _, _) # => ...??
end

D.new.foo { p :proc_in_toplevel }
```

Discussion:

* ko1: I intentionally changed this (for non block parameters) in 1.9.
* matz: mruby does the opposite.
* matz: I would prohibit such assignments if I design a new language right now, but Ruby is not a language of today's me.
* shyouhei: What was the orign of ruby's `super`?
* matz: I designed the feature looking at Lisp's `(call-next-method)`. It can also omit arguments.

Conclusion:

* matz: I admit this is inconsistent.  But it's not worth changing the status quo, especially when this change would make a very obscure bug.

### [[Bug #19266]](https://bugs.ruby-lang.org/issues/19266) URI::Generic should use URI::RFC3986_PARSER instead of URI::DEFAULT_PARSER (jeremyevans0)

* This allows URI::Generic.build to accept hostnames with underscores.
* This will also fix #19756.
* RFC 3986 is almost 20 years old now.
* There is an open pull request to uri to implement this change that passes all uri tests.
* Can the pull request be merged?

Preliminary discussion:

* ko1: I'm not sure the spec change between RFC2396 (current `DEFAULT_PARSER`) and RFC 3986, but if there is no rejected URI on newer one, it seems easy to change.
* ko1: I feel strange that `DEFAULT_PARSER` is not a default parser. I think `DEFAULT_PARSER` should be replaced with the true deafult parser if we changed.

Discussion:

* shyouhei: RFC3986 is not the newest specification, either.
* naruse: We would like to jump to whatwg's URL if possible...
* shyouhei: ...but have not been possible so far.
* akr: Apart from the state-of-art URI spec, it doesn't sound bad _for us_ to move to RFC 3986.
* ko1: maybe that can affect other libraries, like net/http.

Conclusion:

* naruse: OK, let's try changing default parser to 3986.  Check to see if this is too drastic or not.

### [[Bug #9115]](https://bugs.ruby-lang.org/issues/9115) Logger traps all exceptions; breaks Timeout (jeremyevans0)

* I know logger is moving out of stdlib, but it would be nice to fix this before then.
* There is an almost 5-year old backwards-compatible pull request that supports not swallowing given exception classes.
* An alternative, less backwards-compatible approach would be to only rescue IOErrors.
* No response from the logger maintainer in the last 4 years.
* Can we make a decision on how to handle this?

Discussion:

* shyouhei: recap there are several proposals to mitigate it somehow.  None of them ultimately addresses the problem though.  Which one to choose is up to the maintener (@sonots), but he is inactive recently.
* mame: I have just texted to him.
* shyouhei: Basically I'm OK with @jeremyevans' patch.

Conclusion:

* waiting for @sonots

### [[Feature #20610]](https://bugs.ruby-lang.org/issues/20610) Float::INFINITY as IO.select timeout argument (akr)

* Float::INFINITY is useful for timeout computation.

Discussion:

* akr: We already have `nil`.  Why not accept infinity.
* akr:  There are other APIs that accept timeouts.  Not sure what to do with them.
* akr: Also I have not been successful to find other languages that accept Innifity as select timeout.

Conclusion:

* matz: select accepted.
* matz: other methods should be considered separatedly.
* akr: Should there be C API for it?
* matz: That's also open for discussions.

### [[Feature #20612]](https://bugs.ruby-lang.org/issues/20612) Introduce new Epsilon (no-op) GC (eightbitraptor)

- Idea taken from [this Java implementation](https://openjdk.org/jeps/318)
  - Used for testing and experimentation only, with the recently merged modular GC work
- We'd like to introduce a `gc` directory, to better facilitate working with modular GC's

Preliminary discussion:

* ko1: "Epsilon" GC is well known one?

Discussion:

* nobu: is this enabled by default?
* naruse: Don't know who this one is better than `GC.disable`.
* byroot: This is useful when CIing our pluggable GC.
* naruse: Maybe we need this one later, but not right now.  There is only one GC.  We might not need to test the framework today... broken parts are just not used.  Testing them does not improve any quality.

Conclusion:

* ko1: This proposal needs "motivation" section.


### [[Feature #20443]](https://bugs.ruby-lang.org/issues/20443) Allow Major GC's to be disabled (eightbitraptor)

- As discussed, I've introduced a configuration mechanism to GC implementations
- I'd like a final decision on the name of the method.
- I prefer `config` for the reasons outlined [here](https://bugs.ruby-lang.org/issues/20443#note-24)

Discussion:

* ko1: OK with the name?

Conclusion:

* matz: GC.config accepted; needs brush-up for corner cases like what happens when no argumets are passd, though.
    * byroot: no arguments returns the current config. Helpful to test if a key is valid etc. Works the same as `GC.stat`
    * ko1: what the return value with parameters?
    * byroot: Like `GC.stat`. `GC.config(:full_mark) => true/false`.

### [[Feature #20624]](https://bugs.ruby-lang.org/issues/20624) Enhance `RubyVM::AbstractSyntaxTree::Node#locations` method and `RubyVM::AbstractSyntaxTree::Location` class (yui-knk)

Discussion:

* mame: Why it's not a Plain-Old-Ruby-Object but a new class?
* yui-knk:  It would be easier for the user side to write codes.
* mame: How should this be stored on memory?
* yui-knk: Just inside of a node.  These days nodes are various in sizes.
* akr: Motivation?
* yui-knk: I need to make my parser compatible with prism.

Conclusion:

* matz: Will reply

## misc

https://bugs.ruby-lang.org/issues/20614 Integer#size returns incorrect values on 64-bit Windows

```
# search <integer_type>.size
$ MN=size && ~/gem-codesearch/gem-codesearch $MN | awk '{print $2}' | sed s/:.*$// | ruby integer_size_finder.rb
["/srv/gems/Graphiclious-0.1.5/lib/graphiclious/gui.rb:89", "                                 10.size,"]
["/srv/gems/Graphiclious-0.1.5/lib/graphiclious/gui.rb:89", "                                 10.size,"]
["/srv/gems/Graphiclious-0.1.5/lib/graphiclious/gui.rb:89", "                                 10.size,"]
["/srv/gems/Graphiclious-0.1.5/lib/graphiclious/gui.rb:89", "                                 10.size,"]
["/srv/gems/Moby-0.5.1/Examples/Dijkstra/data.rb:1", "def infinity; (2**(0.size * 8 -2) -1) end"]
["/srv/gems/RSRuby-0.4.0/test/tc_to_ruby.rb:29", "    assert_equal($r.NA, (-1)*(2**((1.size*8)-1)))"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/atomic/mutex_atomic_fixnum.rb:11",
 "    MIN_VALUE = -(2**(0.size * 8 - 2))"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/atomic/mutex_atomic_fixnum.rb:12",
 "    MAX_VALUE = (2**(0.size * 8 - 2) - 1)"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/atomic/mutex_atomic_fixnum.rb:11",
 "    MIN_VALUE = -(2**(0.size * 8 - 2))"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/atomic/mutex_atomic_fixnum.rb:12",
 "    MAX_VALUE = (2**(0.size * 8 - 2) - 1)"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/thread_safe/util/xor_shift_random.rb:32",
 "        if 0.size == 4"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.2/lib/concurrent/thread_safe/util.rb:9",
 "      FIXNUM_BIT_SIZE = (0.size * 8) - 2"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/map.rb:208",
 "    DEFAULT_OBJ_ID_STR_WIDTH = 0.size == 4 ? 7 : 14 # we want to look \"native\", 7 for 32-bit, 14 for 64-bit"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/map.rb:208",
 "    DEFAULT_OBJ_ID_STR_WIDTH = 0.size == 4 ? 7 : 14 # we want to look \"native\", 7 for 32-bit, 14 for 64-bit"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/map.rb:208",
 "    DEFAULT_OBJ_ID_STR_WIDTH = 0.size == 4 ? 7 : 14 # we want to look \"native\", 7 for 32-bit, 14 for 64-bit"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/map.rb:208",
 "    DEFAULT_OBJ_ID_STR_WIDTH = 0.size == 4 ? 7 : 14 # we want to look \"native\", 7 for 32-bit, 14 for 64-bit"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/thread_safe/util/xor_shift_random.rb:32",
 "        if 0.size == 4"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/thread_safe/util.rb:10",
 "      FIXNUM_BIT_SIZE = (0.size * 8) - 2"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/utility/native_integer.rb:6",
 "      MIN_VALUE = -(2**(0.size * 8 - 2))"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/utility/native_integer.rb:7",
 "      MAX_VALUE = (2**(0.size * 8 - 2) - 1)"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/utility/native_integer.rb:6",
 "      MIN_VALUE = -(2**(0.size * 8 - 2))"]
["/srv/gems/abaci-0.3.0/vendor/bundle/gems/concurrent-ruby-1.0.4/lib/concurrent/utility/native_integer.rb:7",
 "      MAX_VALUE = (2**(0.size * 8 - 2) - 1)"]
 ```
