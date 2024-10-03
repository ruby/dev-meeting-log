# DevMeeting-2024-10-03

https://bugs.ruby-lang.org/issues/20717

## DateTime and location

* 2024/10/03 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/11/07 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Misc #20746]](https://bugs.ruby-lang.org/issues/20746) Request to migrate the `json` gem from `flori/json` repository to `ruby/json` (byroot)

* Current maintainers don't have full administrative rights over the repo, which is problematic.
* We've been trying to contact flori to try to resolve this situation with a transfer for over two years now, without success.

Discussion:

* https://github.com/flori/json/pull/595
* https://github.com/flori/json/pull/597
* https://github.com/flori/json/pull/598

Conclusion:

* hsbt: I will merge byroot's PRs

### [[Misc #20728]](https://bugs.ruby-lang.org/issues/20728) Add eileencodes as a core committer (tenderlovemaking)

* She's made many useful contributions
* She's currently helping to maintain Prism / Prism Compiler and it would be helpful if she can merge her own commits

Conclusion:

* matz: will reply

### [[Feature #20750]](https://bugs.ruby-lang.org/issues/20750) Expose ruby_thread_has_gvl_p in ruby/thread.h (eregon)

* I think rb_thread_call_with_gvl() should be allowed if the GVL is already acquired, it seems a much nicer way to solve the use cases the OP pointed out (for a C function which can be called either with or without GVL).
* @ko1 What do you think, how do you want to solve that issue?

Discussion:

* ko1: it's okay to introduce `ruby_thread_has_gvl_p()` . But I'm a bit negative to allow `rb_thread_call_with_gvl()` on acquiring GVL. Maybe becasuse I don't want to abuse this functioin. For example, use `with_gvl` for any fnctions.  I think this kind of difference (obtaining GVL or not) should be self-conscious.
* matz: This reason is not reasonable for me. How about to document about it?

Conclusion:

* matz: will reply

### [[Feature #10459]](https://bugs.ruby-lang.org/issues/10459) [PATCH] rfc3339 method for Time (hsbt)

* https://github.com/rails/rails/blob/b88d9af34fbc1c84ce2769ba02584eab2c28ac6e/activesupport/lib/active_support/time_with_zone.rb#L152
* It seems reasonable.
* We introduce that with C ext same as `xmlschema`?

Conclusion:

* akr: rfc3339 is slightly different from iso8601. I don't want to introduce this alias

### [[Feature #17295]](https://bugs.ruby-lang.org/issues/17295) Feature: Create a directory and file with Pathname#touch (hsbt)

* I found same use case for that today. How about this?
  * https://github.com/BerkeleyLibrary/util/blob/main/spec/berkeley_library/util/files_spec.rb#L16
* https://github.com/ruby/ruby/pull/3706

Discussion:

* shyouhei: this was rejected before https://bugs.ruby-lang.org/issues/7361

Conclusion:

* akr: negative


### [[Feature #17297]](https://bugs.ruby-lang.org/issues/17297) Feature: Introduce Pathname.mktmpdir (hsbt)

* How about this?
* https://github.com/ruby/ruby/pull/3709

Conclusion:

* akr: okay
* hsbt: I will merge

### [[Feature #17294]](https://bugs.ruby-lang.org/issues/17294) Feature: Allow method chaining with Pathname#mkpath Pathname#rmtree (hsbt)

* How about this?
* https://github.com/ruby/ruby/pull/3705

Discussion:

* akr: This allows method chaining, though it does not fit with my taste
* akr: But now I undrstand the feeling and culture of method chaining, so should I accept this?
* nobu: `(path = Pathname.new("/tmp/new")).mkpath`?
* akr: It is too cryptic
* knu: I understand the feeling of those who want to write this.  I often have to write `LOG_DIR = Pathname("…/log"); PID_DIR = Pathname("…/pids"); OUTPUT_DIR = Pathname("…/out")` followed by mkpath for each.
* mame: I tend to write this

```
path = Pathname.new("/tmp/new")
path.mkpath
```

* knu: I can't think of a use case of rmtree returning self.  `path.rmtree.mkpath(mode: 700)` may make sense.
* akr: well, maybe it is ok too?

Conclusion:

* akr: go ahead for both mkpath and rmtree
* hsbt: I will proceed this

### [[Feature #17296]](https://bugs.ruby-lang.org/issues/17296) Feature: Pathname#chmod use FileUtils.chmod instead of File (hsbt)

* It's reasonable and useful.
* Does anyone have concern?
* https://github.com/ruby/ruby/pull/3708

Discussion:

* akr: understandable
* akr: is it ok to `return 1`?
* nobu: `lchmod` should be also handled
* nobu: For symlink, `File.chmod` changes the link target file. But `FileUtils.chmod` changes the symlink itself (only when lchmod is available). https://github.com/ruby/fileutils/blob/master/lib/fileutils.rb#L2209
* akr: it is too incompatible, unacceptable

Conclusion:

* akr: the incompatibility is unacceptable
* akr: If this is really wanted, we should improve File.chmod itself

### [Implement Pathname#chdir](https://github.com/ruby/pathname/pull/33)

* Does anyone know the reason why removing chdir?

Discussion:

* hsbt: it was obsoleted at Ruby 1.8 and removed at Ruby 1.9. Why?
* akr: I cannot remember. I guess I disliked the process-global state
* knu: If a pathname were a relative path, it would no longer point to the directory after calling chdir.  A method call making the receiver invalid may sound strange.
* mame: Now what do you think?
* akr: Hmm... I don't use chdir so much. When do users want to use chdir?
* akr: I think it is not needed unless there is an explained use case

### [[GH-2974]](https://github.com/ruby/ruby/pull/2974) Pathname#mountpoint? to support bind mount

* akr: We need to check if the platform is Linux, and the spec of mountinfo.
* znz: mountinfo includes `/tmp/foo\040bar` when the mount path includes a space
* akr: We need to handle the escaping
* znz: Here is the document: https://docs.kernel.org/filesystems/proc.html#proc-pid-mountinfo-information-about-mounts

###  [[GH-2024]](https://github.com/ruby/ruby/pull/2024) each_address should resolve for AAAA first

* Does anyone have any concern?
* akr: Looks good

### [[Feature #18127]](https://bugs.ruby-lang.org/issues/18127) Ractor-local version of Singleton (hsbt)

* What's the blocker for this?

Conclusion:

* hsbt: it is already merged
* matz: confirmed the spec, Okay

### [[Feature #6012]](https://bugs.ruby-lang.org/issues/6012) Proc#source_location also return the column (eregon)

* How about `{Proc,Method,UnboundMethod}.source_location #=> [path, start_line, start_column, start_offset, end_line, end_column, end_offset]` ?
* How about `{Proc,Method,UnboundMethod}.code_location #=> object with path, start_line, start_column, start_offset, end_line, end_column, end_offset and code` ?
* It would avoid having to use RubyVM to find this information.
* If some do not like these APIs, what do they suggest instead?

Discussion:

* matz: adding column information is okay
* matz: I am not sure if offset information is needed
* matz: I am not sure about the incompatibility of changing the array size

Conclusion:

* matz: `{Proc,Method,UnboundMethod}.source_location #=> [path, start_line, start_column, end_line, end_column]` looks good. If offset is really needed, please persuade me.

## misc

### https://bugs.ruby-lang.org/issues/15554 warn/error passing a block to a method which never use a block

* akr: command-line argument is sometimes unuseful. `Warning[...]` is better
* mame: If we want to pass the command-line argument to `rake test`, we need to write `Rake::TestTask#options=` accessor

```ruby
require "rake/testtask"

Rake::TestTask.new(:test) do |t|
  t.libs << "test"
  t.libs << "lib"
  t.options = "--warn-unused-block"
  t.test_files = FileList["test/**/*_test.rb"]
end
```

### https://bugs.ruby-lang.org/issues/20620 singleton_method undefined for module using "extend self"

* matz: looks good, I will reply "go ahead"

### https://bugs.ruby-lang.org/issues/20564 Switch default parser to Prism

* matz: If any showstopper appears, we may revert. Currently, AFAIK, there is no showstopper. I will reply

### https://bugs.ruby-lang.org/issues/20738 Removing a specific entry from a hash literal

```
func(
  foo: 1,
  bar: 2 if cond?,
  baz: 3,
)
```

* matz: The following looks good enough. I don't see the need of a new syntax

```ruby
h = {}
h[:foo] = 1
h[:bar] = 2 if bar?
h
```

```ruby
kw = {}
kw[:opt] = 1 if cond?
foo(**kw)

foo(
  opt: 1 if cond?
)

foo(
  ?opt: (cond? ? 1 : nil)
)
```

* matz: I am not positive.

### https://bugs.ruby-lang.org/issues/20745 `IO::Buffer#copy` triggers UB when src/dest buffers overlap

* matz: I hope we can avoid undefined behavior unless there is a very special reason.

### https://bugs.ruby-lang.org/issues/20769 Add `Hash#transform_value`

```ruby
mutated_hash = hash.transform_value(:image) { |url| download(url) }
mutated_hash = { **hash, image: download(hash[:image]) }
```

```ruby
h = { :a => 1, :b => 2, :c => 3 }
h.transform_value(:b) {|n| n * 100 } #=> { a: 1, b: 200, c: 3 }
```

* matz: I don't see the need.

```ruby
h = { :a => 1, :b => 2, :c => 3 }
h2 = h.dup
h2[:b] = h2[:b] * 100
```

* matz: I will reject

### https://bugs.ruby-lang.org/issues/20770 A *new* pipe operator proposal

```ruby
add(value, 3) |> 3.times { square(it) }
add(value, 3) |> self.square(it)
add(value, 3) |> mul(it, it)
add(value, 3) |> foo(&it)
add(value, 3) |> it.foo
add(value, 3) |> it * it
add(value, 3) |> "#{ it }"
add(value, 3) |> [it]
add(value, 3) |> x if it == 0
add(value, 3) |> foo(it) { bar }
```

* matz: Instead of introducing `then`-specific syntax, I am rather interested in an one-line block syntax without a closing brace.

```ruby
10.times |> foo(it)
10.times do =|x| foo(x)
10.times do = foo(x)
10.times do |x| = 10.times do |y| = foo(x, y)
10.times do |x|> 10.times do |y|> foo(x, y)

obj.map { foo(it) }.select { cond(it) }
```

```ruby
add(value, 3).then |> square(it).then |> half(it)
```

```ruby
add(value, 3) |> square(it) |> half(it)
add(value, 3).then { square(it) }.then { half(it) }

add(value, 3) => x
square(x) => x
half(x) => x

x = add(value, 3)
x = square(x)
x = half(x)
```

```ruby
add(value, 3) => square(it) => half(it) # No
```
### https://bugs.ruby-lang.org/issues/20778 ruby/repl_type_completor as a bundled gem

* matz: Positive. I will reply

### https://bugs.ruby-lang.org/issues/17326 Add Kernel#must! to the standard library

### annotation?

Matz proposed annotation for Ruby 4 in EuRuKo. Let's do brain storming 

* type annotation
* DI（Java）
* routing info for app web server (like Python's flask)
* mark a test function (like Rust)
* mark a module that is only activated on a specific platform/configuration (like Rust)
* documentation
* memoization
* synchronized
* https://github.com/rails/thor/wiki
* info for optimization (pure, noreturn, etc.)
* `rubocop:disable`

```ruby
def foo
  @_foo ||= begin
    ...
  end
end
```

```
@doc(<<END)
  これはこういうメソッドです
END
def foo ... end
```
