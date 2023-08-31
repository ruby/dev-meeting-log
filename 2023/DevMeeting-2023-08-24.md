# DevMeeting-2023-08-24

https://bugs.ruby-lang.org/issues/19766

## DateTime and location

* 2023/08/24 (Thu) 13:00-17:00 JST @ Online
* Additonal date: 2023/08/29 (Tue) 10:00-12:00 JST @ Online at office hour

## Next Date

* Next: 2023/09/14 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #19784]](https://bugs.ruby-lang.org/issues/19784) Inconsistency between `String#start_with?` and `String#delete_prefix` for strings with broken encodings. (byroot)

- `"\xFF\xFE".start_with?("\xFF") # => true`
- `"\xFF\xFE".delete_prefix("\xFF") # => "\xFF\xFE"`
- If Ruby considers `\xFF` is indeed the prefix, it's unclear why it refuses to delete that prefix.
- Should `String#delete_prefix` stop validating the encoding, or should `String#start_with?` start validating it?

Discussion:

```ruby
"あ".start_with?("\xe3") #=> true -> should be false
"あ".delete_prefix("\xe3") #=> should be "あ"
"\xFFあ".delete_prefix("\xFF") #=> should be "あ"
"\xFFあ".delete_prefix("\xFF\xe3") #=> should be "\xFFあ"
"\xFFあ".delete_prefix("\xFF\xe3\x81\x82") #=> should be ""
```

Conclusion:

* akr: Both method assumes invalid byte sequence as a string whose encoding is extended encoding which assumes an invalid byte as an character. Further optimizations are allowe while the behavior is not changed
- `"\xFF\xFE".start_with?("\xFF") # => true -> as is` not changed
- `"\xFF\xFE".delete_prefix("\xFF") # => "\xFF\xFE" -> "\xFE"` changed
- `"あ".start_with?("\xE3") # => true -> false` changed
- `"あ".delete_prefix("\xE3") # => "あ" -> as is` not changed


### [[Feature #19783]](https://bugs.ruby-lang.org/issues/19783) Weak References in the GC (peterzhu2118)

* Native support for weak references in the GC. Overwrites dead objects with `Qundef`.
* Implemented for CME of CC, `ObjectSpace::WeakMap` and `ObjectSpace::WeakKeyMap`. Makes the implementation of `ObjectSpace::WeakMap` and `ObjectSpace::WeakKeyMap` simpler.
* No performance impact in yjit-bench. Microbenchmarks show that `ObjectSpace::WeakMap#[]=` is 3x faster.

Preliminary discussion:

* ko1: CME issue was solved so one of motivation is not available.
* ko1: I'll reply.

Discussion:

* peter: native support for weak references needed.  CME already has this feature. Make it available to anyone benefits WeakMap etc.
* peter: MMTK also want this feature (some Mark&Sweep assumption cannot be made there).
* peter: current implementation employs double pointer when marking and when it ends the array of double pointer is scanned and replaced with Qundef.
* ko1: I'm wondering how much impact is there right now, performance wise. 
* ko1: If there is no weak references at all there are no performance penalty and that's okay.  But I'm not sure how it becomes when there are tons of affected objects.
* ko1: Current one could be the fastest way for mark & sweep but for MMRK...?
* ko1: My proposal is to accept the weak reference API itself, and revisit later for performance improvements, maybe in 3.4.

Conclusion:

* peter: agreed.


### [[Feature #19785]](https://bugs.ruby-lang.org/issues/19785) Deprecate `RUBY_GC_HEAP_INIT_SLOTS` (peterzhu2118)

* This environment variable only made sense when we only have one heap.
* It's replaced by the `RUBY_GC_HEAP_INIT_SIZE_%d_SLOTS` environment variables.
* Should we deprecate it? Or can we just remove it? It's an implementation internal environment variable, so maybe we can just remove it?

Preliminary discussion:

* ko1: I didn't know `RUBY_GC_HEAP_INIT_SIZE_%d_SLOTS` environment variables, but how to know the `%d` parts? Is it feasible to set init size for each size by Ruby developers?
* ko1: How about to introduce new `RUBY_GC_HEAP_INIT_PAGES=page_num` to prepare pages in the pool?

Discussion:

* peter: `%d` is 40, 80, 160, 320, 640.
* peter: because we now have several slots the one env to control everything simply doesn't make sense.
* peter: the number can be obtained by `GC.stat_heap`.
* ko1: Is `RUBY_GC_HEAP_INIT_SLOTS` already a no-op?
* peter: `RUBY_GC_HEAP_INIT_SLOTS` still works now, but actually behaves differently than it used to in 2.x.
* mame: How do we tune the values of each slot size?
* peter: When you call `GC.stat_heap` before/after a request you can know how many splots are used per request per size.

```ruby
irb(main):001> GC.stat_heap.transform_values {|h| [h[:slot_size], h[:heap_eden_pages]] }
=> {0=>[40, 30], 1=>[80, 12], 2=>[160, 18], 3=>[320, 4], 4=>[640, 3]}
# RUBY_GC_HEAP_INIT_SIZE_40_SLOTS=30
# RUBY_GC_HEAP_INIT_SIZE_80_SLOTS=12
# RUBY_GC_HEAP_INIT_SIZE_160_SLOTS=18
# RUBY_GC_HEAP_INIT_SIZE_320_SLOTS=4
# RUBY_GC_HEAP_INIT_SIZE_640_SLOTS=3
```

```
$ RUBY_GC_HEAP_INIT_SIZE_40_SLOTS=1000000000000000000000000000 ruby -v empty.rb
ruby 3.3.0dev (2023-08-18T22:27:59Z master 3dff315ed3) [x86_64-linux]
RUBY_GC_HEAP_INIT_SIZE_40_SLOTS=9223372036854775807 (default value: 10000)
ruby: failed to allocate memory (NoMemoryError)
```

* ko1: how do we deprecate an environment variable? warning?
* peter: OK with issuing warning.
* akr: I don't need this.  Maybe a server might set both old and new variables at once for transition.
* peter: understand that.
* akr: -W:deprecated can be acceptable though.
* mame: Most of people don't use `-w` in production so I don't think  it is useful.
* mame: The most important is to mention this in NEWS.

----

* ko1: if 40 bytes dominates the object, we can set `RUBY_GC_HEAP_INIT_SIZE_40_SLOTS` with `RUBY_GC_HEAP_INIT_SLOTS`.
* peter: less than half so it seems not good idea.

Conclusion:

* matz: accept to deprecate.
* matz: warn on `-W:deprecated`.


### [[Misc #19772]](https://bugs.ruby-lang.org/issues/19772) API Naming for YARP compiler (jemmai)

- How should we expose the public API for YARP's compile method?
- Potential options: `YARP.compile`, `RubyVM::InstructionSequence.compile(yarp: true)`, `RubyVM::InstructionSequence.compile_yarp`, any of these with a name other than YARP (please suggest)

Discussion:

#### Naming

* peter: how do people feel about "prism"?
* matz: "prism" is a good name for a gem! ... except when it comes to a built-in class, it could be more descriptive about functionality liek SomethingParser etc.  Too bad there already is RubyParser gem...
  * RubyParser: https://github.com/seattlerb/ruby_parser/blob/master/lib/ruby_parser.rb
* k0kubun: there is ruby_parser but no ruby-parser. (Ruby::Parser)
* matz: there is another debate as to whether we should introduce `Ruby::` name space into core. Could break existing codes?
* ko1: https://gist.github.com/ko1/7eb7aa7d81d6922cd5c8c0ee563eddea
* matz: Ruby::Parser and ruby-parser.gem?
* tenderlove: ruby_parser and ruby-parser are confusing
* matz: they are rivals in any way
* jhawthorn: rubygems.org will not allow ruby-parser https://github.com/rubygems/rubygems.org/blob/master/app/models/gem_typo.rb#L31

---

* ko1: I have already replied about `RubyVM::InstructionSequence.compile(yarp: true)` or something

#### Library

* current implementation:
  * EvalParser (C API)
    * Embedded in interpreter
  * builtin Ruby::Parser (Ruby API)
    * This is enabled with `require "ruby/parser.rb"` and call EvalParser.
    * Q. Is this provided? or default gem / bundled gem
  * Ruby::Parser as gem
    * This provides "ruby/parser.so" that contains C and Ruby API.
        * If we load it, it's different parser with EvalParser.
    * for specified version
    * Should we rename this like `yarp` with different name of ruby-parser?
  * module Ruby
    * Ruby::Parser
      * Ruby::Parser33
      * Ruby::Parser::V33
* discussion point
  * Whether Eval Parser is provided as Ruby API or not?
  * What implmentation is used as Ruby::Parser?
    * EvalParser
      * If so, a gem won't overwrite Ruby::Parser
    * A parser installed from gem for the current version
  * How is a version specified parser provided?
* Idea set
  built-in EvalParser (== YARP) is always available in interpreter
  1. [current] `YARP` (Ruby API, EvalParser wrapper) and `YARP` by default yarp.gem. Load newer version of parser 
  2. `Ruby::Parser` (Ruby API, EvalParser wrapper) and `Ruby::Parser` by default ruby-parser.gem. Load newer version of parser impl. if available (`require 'ruby/parser'`)
  3. [Matz thought] `Ruby::Parser` (Ruby API, EvalParser wrapper), gem shouldn't touch it. yarp.gem introduce other constant (e.g. `::YARP`)
    3.1 `require 'ruby/parse'` adds `Ruby::Parser`
    3.2 Ruby::Parser is bulitin-class and .rb will be loaded by `autoload` trick
    * How bundle yarp.gem or not bundle are further discussion. default or bundled gems.
  4. No `Ruby::Parser`, but `::YARP` / `::Prism` by requiring yarp/prism.gem
    * `YARP::Current.parse == YARP::Ruby33.parse` on Ruby 3.3
    * `YARP::Ruby33.parse`
    * `YARP::Ruby34.parse`

* matz: the name `Ruby::Parser` is occupied by the interpreter itself. Any gem should not redefine the name space

Conclusion:

* matz: `Ruby::Parser` persuaded.
* mame: Discussions about Ruby 3.3
    * Kernel#eval will use parse.y by default. Using an option, it will use the built-in YARP.
    * Built-in YARP can use used from Ruby in `Ruby::Parser`.
        * `Ruby::Parser` is not loaded by default.
        * `require 'ruby/parser'` will define `Ruby::Parser`.
        * `lib/ruby/parser.rb` will be part of the standard library (it will not be part of default/bundled gems).
    * `Ruby::Parser` is not defined by the gem (= gem cannot replace `Ruby::Parser`).
        * yarp/prism will use a different namespace than `Ruby::Parser`.
        * The Ruby namespace is owned and predefined by the Ruby interpreter.
    * We haven't decided whether to bundle the yarp/prism gem.
        * The name of the gem can be either yarp or prism.
        * yarp's implementation for multi-version support is up to yarp maintainers.

----

about `RubyVM::InstructionSequence.compile_yarp`:

* ko1: if the motivation is only for testing, any method name is acceptable (I prefer `RubyVM::InstructionSequence.compile_yarp`) and will remove it after merging.

### [[Feature #19790]](https://bugs.ruby-lang.org/issues/19790) Optionally write Ruby crash reports into a file rather than STDERR (byroot)

* `STDERR` is often hard to exploit in production, as it's often shared by multiple process causing interwined logs.
* Being able to write bug report in predictable location allow for easier automated reporting of crashes in production.
* The JVM has a similar feature with `-XX:ErrorFile=`
* I suggest `RUBY_BUGREPORT_PATH` as an environment variable to control where the bug reports are written.

Discussion:

* ko1: If the env var is specified, ruby prints only to the file? or both the file and STDERR?
* nobu: This feature enabled with:
  * `--bugreport-path=<FILE>`
  * `RUBY_BUGREPORT_PATH=<FILE>`
* mame: `BUGREPORT`? `RUBY_CRASH_REPORT_PATH`?
  * `--crash-report=<FILE>`
  * `RUBY_CRASH_REPORT_PATH`
* mame: what's happens on unwritable to the path?
* nobu: print to STDERR when error.
* ko1: pipe is acceptable?
  * `RUBY_CRASH_REPORT_PATH="| /bin/cat"`
* akr: how about to use a different environment variable?
  * `RUBY_CRASH_REPORT_PIPE_COMMAND="/bin/cat"`
* ko1: we already have similar feature to spawn a process on `rb_bug()`: `RUBY_ON_BUG='gdb -p'` so no additional vulnerables.

Conclusion:

* matz: accepted
  * enabled with:
    * `RUBY_CRASH_REPORT=<path>`
    * `--crash-report=<path>`
  * patternss (%p, ...) are accepted
  * pipe is accepted.
  * No STDERR output on printing to file

### [[Misc #19777]](https://bugs.ruby-lang.org/issues/19777) Make `Kernel#lambda` raise when called without a literal block (alanwu)

* There are two code paths for detecting whether a literal block was passed, best to have just one
* The way `f_lambda_warn()` does it allows `block_code` to be left uninitialized on calls, which is helpful for YJIT
* Three years feels long enough for users to respond to the deprecation warning

Preliminary discussion:

```ruby
Warning[:deprecated] = true
def foo(&b) lambda(&b) end
foo {}
#=> test.rb:2: warning: lambda without a literal block is deprecated; use the proc without lambda instead
```

Conclusion:

* no one is against
* matz: go ahead


### [[Bug #19779]](https://bugs.ruby-lang.org/issues/19779) `eval "return"` at top level raises `LocalJumpError` (jeremyevans0)

* Do we want to allow `eval "return"` at top level with the same sematics as `return`?

Preliminary discussion:

* ko1: we may implement it but who needs it?

```ruby
def foo
  eval 'return :ok'
end

p foo #=> :ok

1.times do
  eval "break" #=> Can't escape from eval with break (SyntaxError)
end

while true
  eval 'break'
  #=> (eval): (eval):1: Can't escape from eval with break (SyntaxError)
end
```

Conclusion:

* matz: nice to have but low priority

### [[Bug #19749]](https://bugs.ruby-lang.org/issues/19749) Confirm correct behaviour when attaching private method with `#define_method` (jeremyevans0)

* This was discussed last month, but discussion focused on the general semantics and not the specific bug I want to fix.
* Please see comments #13 and #15 for examples of the bug I want to fix.  Can we fix that specific bug?

Preliminary discussion:

```ruby
class C
  def foo; end

  private

  define_method(:bar, instance_method(:foo))
  # the method bar is private

  define_method(:foo, instance_method(:foo))
  # current: the method foo is kept public
  # expected: the method foo is changed private
end

class Foo
  def foo; end
  private
  def foo; end
end
Foo.new.foo #=>  private method `foo' called for an instance of Foo (NoMethodError)
```

Conclusion:

* matz: I will reply.


### [[Bug #19829]](https://bugs.ruby-lang.org/issues/19829) `Enumerator.product` called with keyword arguments raises exception with not precise message (jeremyevans0)

* Can we add rb_no_keywords_accepted to the C-API, so that C functions can implement the equivalent of **nil?

Preliminary discussion:

```ruby=
Enumerator.product([], a: 1)
# actual:   unknown keyword: :a (ArgumentError)
# expected: no keywords accepted (ArgumentError)

def foo = nil
foo k:1
#=> wrong number of arguments (given 1, expected 0) (ArgumentError)

def foo(**nil)
end
foo k:1
#=> no keywords accepted (ArgumentError)
```

* mame: IMPO the current behavior is good enough
* nobu: Is it more useful to show given keyword like `unknown keyword: :a`? (remove `no keywords accepted` message?) 

Conclusion:

* matz: Keep it as is. I will reply.


### [[Misc #19740]](https://bugs.ruby-lang.org/issues/19740) Block taking methods can't differentiate between a non-local return and a throw (jeremyevans0)

* The underlying issue is no longer a problem after a fix to timeout to always use raise instead of throw.
* However, do we want to add a feature for ensure blocks to know if they were called via throw?
* I'm against such a feature, as I don't think we want libraries treating throw differently than other non-local return.
Preliminary discussion:

```ruby=
def foo
  User.transaction do
    return # non-local exit
  end # Want to commit
  # skipped
end

catch(:something) do
  User.transaction do
    throw :something
  end # Want to rollback
  # skipped
end

# but currently we cannot differentiate the two exits
```

Conclusion:

* matz: I want to see no change. I will reply

---

time up; we will continue to discuss the following on 2023/08/29 (Tue)

---

### [[Feature #19842]](https://bugs.ruby-lang.org/issues/19842) Introduce M:N threads (ko1)

* Do you have any questions about it?

Discussion:

* matz: okay for environment variables
  * `RUBY_MN_THREADS=0/1`
  * `RUBY_MAX_CPU=n`
    * Ruby has `Proc` class so matz doesn't like `PROC(s)` 
* locking API
  * C-API
    * `void rb_thread_lock_native_thread(void)`
  * Ruby API
    * with block
      ```ruby
      Thread.lock_native_thread do
        ...
      end
      ```
    * without block
      ```ruby
      Thread.lock_native_thread
      ...
      Thread.unlock_native_thread
      ```
  * now providing only C-API (`rb_thread_lock_native_thread(void)`)

Conclusion:

* matz: go ahead with enough quality implementation
  * `RUBY_MN_THREADS=1` enables M:N threads on the main ractor (deafult: 0). Non-main ractors enables M:N by default.
  * `RUBY_MAX_CPU=n` to specify upper limit
  * Provide only `void rb_thread_lock_native_thread(void)` and add other APIs if needed (by request)