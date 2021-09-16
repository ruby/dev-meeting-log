---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210916Japan

https://bugs.ruby-lang.org/issues/18122

## DateTime and location

* 9/16 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 10/21 (Thu) 13:00-17:00 JST @ Online
  * pre: 10/19 (Tue)

## Announce

## About release timeframe

## Check security tickets

[secret]

## Tickets

### [[Feature #18136]](https://bugs.ruby-lang.org/issues/18136) `take_while_after` (or `take_upto`) (zverok)

* I have proposed it once a long time ago, but those examples were convoluted; now I came with clearer ones and explanations of the usefulness.
* I am ready to work on the PR, if accepted.

Preliminary discussion:

```ruby=
[1, 2, 3, 4, 5].take_while {|v| v != 3 }
#=> [1, 2]

[1, 2, 3, 4, 5].take_while_after {|v| v != 3 }
#=> [1, 2, 3]
```

Discussion:

* knu: I understand there are use cases for this, but there are times you know you call it done only after processing an element, in which case a generic `begin…end while` structure or `Enumerator.new { … break if … }` comes in anyway.
* nobu: vote for `take_upto`
  ```ruby=
  [1, 2, 3, 4, 5].take_upto {|v| v == 3 }
  #=> [1, 2, 3]
  ```
  ```ruby=
  class X
    include Enumerable
    def each
      (1..5).each {|v| p v; yield v}
    end
  end
  p X.new.slice_when {|v| v==3}.first
  # 1
  # 2
  # 3
  # 4
  #=> [1, 2, 3]
  ```
* mame: take_while returns elements that satisfies a given predicate, while take_upto returns elements that does not satisfy a given predicate (plus only one element that satisfies it). It is a bit confusing to me. I'm not against it though
* naruse: The listed use cases are not so convincing

Conclusion:

* timeout; it took too much time
* mame: I wrote the discussion


### [[Feature #18143]](https://bugs.ruby-lang.org/issues/18143) Add a new method to change `GC.stress` only in the given block such as `GC.with_stress(flag) {...}` (kou)

* This feature is generally useful for debugging GC related problems and writing tests for them.
* We don't need to implement the same feature multiple times if `GC` provides the feature by default.

Preliminary discussion:

* ko1: If the problem is about envutil, how about letting "test-unit" have this feature?
* mame: Looks a good idea > test-unit
* mame: I'm a bit negative against this because the block style is confusing to be a thread-local API.

Discussion:

* matz: in general, the proposal looks not a good idea
* matz: making the feature built-in to core will make people think that it is so robust
* mame: I agree

Conclusion:

* matz: Rejected

### [[Feature #18148]](https://bugs.ruby-lang.org/issues/18148) `Marshal.load` `freeze` option (byroot)

* Useful to deserialize static data that is meant to stay in memory.
* Useful for in-memory caches.
* `JSON`,`Psych` and `MessagePack` all added a similar option in the last couple years.

Preliminary discussion:

```ruby=
dump = Marshal.dump([1, 2, 3])
ary = Marshal.load(dump, freeze: true)
p ary.frozen? #=> true
```

* ko1: How about `Marshal.load` with a proc?

Discussion:

* mame: The proposal looks reasonable to me
* mame: The benefit of this request is that strings inside of such Marshalled objects get deeuped by this.
* knu: Is frozenness dumped with `Marshal.dump`? (Seems not)

```ruby=
obj = Object.new.freeze
p s = Marshal.dump(obj)
p Marshal.load(s).frozen? #=> false
```

* shyouhei: Frozen strings gets deduped automatically?
* nobu: No.  Deduplication complicates Marshal.  Objects are managed based on object ID.

```ruby=
# The string deduplication may introduce confusion.
# But it is not a new issue. Actually, frozen-string-literal has the same issue
s1, s2 = Marshal.load(Marshal.dump(["foo", "foo"]))
s1 << "bar"
p s2 #=> "foobar" when frozen-string-literal: true
p s2 #=> "foo"    when frozen-string-literal: false
```

```ruby=
# off-topic: "compare by identity" flag is not saved.
h = {}
h.compare_by_identity
h['foo'.dup] = 1
h['foo'.dup] = 2
pp h
#=> {"foo"=>1, "foo"=>2}

pp h = Marshal.load(Marshal.dump(h))
#=> {"foo"=>2}
p h.compare_by_identity?
#+> false
```

Conclusion:

* matz: OK to introduce `freeze: true`.
* matz: OK for dedup, too


### [[Bug #18141]](https://bugs.ruby-lang.org/issues/18141) Marshal load with proc yield strings before they are fully initialized (byroot)

* UTF-8 strings are yielded as `ASCII-8BIT` because their encoding isn't set yet.
* If the `proc` freeze the string, `Marshal.load` raises a `FrozenError`

Preliminary discussion:

* nobu: it is a bug and should be fixed.

Conclusion:

* nobu: already merged


### [[Bug #15027]](https://bugs.ruby-lang.org/issues/15027) When `Struct#each` method is overriden `Struct#select` and `Struct#to_a` use wrong collections (jeremyevans0)

* Do we want methods like `Struct#select` to internally call `#each` (at least if #each is overridden)? (I'm against making such a change)

Preliminary discussion:

* ko1: same issue on Array's subclass. But it's common to make a `Struct` subclass, and is not common to make a sub-class of `Array`.

```ruby=
class A < Array
  def each(&block)
    [:baz, :qux].each(&block)
  end
end

p A.new(3).to_a #=> [nil, nil, nil]
```

* mame: I agree with Jeremy to close it.

Conclusion:

* matz: agreed with closing it


### [[Bug #11182]](https://bugs.ruby-lang.org/issues/11182) Refinement with alias causes strange behavior (jeremyevans0)

* I think the current behavior of alias is expected and is not a bug.  Can this be closed?

Preliminary discussion:

* ko1: I'll ask shugo if I may close this

Conclusion:

* shugo: already closed

### [[Bug #17429]](https://bugs.ruby-lang.org/issues/17429) Prohibit include/prepend in refinement modules (jeremyevans0)

* @shugo has a patch to deprecate Refinement prepend/include, and add `Refinement#import`.
* Is it OK to merge @shugo's patch?

Preliminary discussion:

* ko1: leave it to shugo
* shugo: Is the name `import` OK?
* shugo: Should `Refinement#import` import C methods without refinement activation?
    * In the current implementation, it raises an ArgumentError when the given module has C methods.
* shugo: Should `Refinement#import` import ancestors' methods of the given module?
    * In the current implementation, only directely defined methods are imported.
    * Because `Refinement#import` (method copy) doesn't work with super.

Discussion:

* 

```ruby=
module R
  refine C do
    import Enumearable

    def each
      ...
    end
  end
end

using R
C.new.map{...}
```

* ko1: Do people want to introduce `Module#import` without refinements?

Conclusion:

* matz: The concept is accepted, but I want to consider the name "import"

### [[Feature #14579]](https://bugs.ruby-lang.org/issues/14579) Hash value omission (shugo)

* Should method calls are allowed?

Preliminary discussion:

* matz: Yes, they should. I was convinced by the points knu made.
* shugo: I beleave method calls should be allowed because it's convenient and `x:` is a syntax sugar of `x: x`　except that keywords are allowed as local variable or method names (e.g., `{if:}` is not a syntax error, and `{self:}` doesn't access the receiver). 

Discussion:

* shugo: `T = 1; {T:} #=> { :T => 1 }`. Is this okay?
* matz: Okay.
* shugo: `{self:} #=> { :self => <<binding.local_variable_get(:self) or send(:self)>> }`. Is this okay?
* matz: Okay.

```ruby
def true = false
h = {true:}
p h #=> {true: false}
```

* nobu: `{__FILE__:}` raises a `NameError`, is it okay?

```ruby=
def __FILE__ = true
h = {__FILE__:}
p h
#=> {:__FILE__=>true}
```

Conclusion:

* matz: The current behavior of ruby/ruby master looks good

### [[Bug #17568]](https://bugs.ruby-lang.org/issues/17568) `Thread.handle_interrupt` is per-Thread but should probably be per-Fiber (jeremyevans0)

* Do we want to make `Thread.handle_interrupt` a per-Fiber setting?
* Seems confusing to me to do so, considering it's a method defined on Thread.

Preliminary discussion:

* ko1: For enumerator, there is no way to mask interrupt for all iterations. But maybe there are only few cases it becomes an issue. For `Fiber` as userlevel thread, per-fiber is reasonable.
* mame: When creating a new `Fiber` during `Thread.handle_interrupt`, should the configuration be inherited?
* ko1: For naming, introducing `Fiber.handle_interrupt` and make `Thread.handle_interrupt` as an alias of `Fiber`'s. It's same situation with TLS (`Thread#[]` is fiber-local).

Discussion:

* nobu: is it even possible?

```ruby
Fiber.new do
  Thread.handle_interrupt(RuntimeError => :never) do
    Fiber.yield
  end
end.resume

# RuntimeError is masked forever
```

* ko1: can be done by having dedicated masks per fiber.
* nobu: What happens when a masked fiber switches to somewhere else? Exception raises in the switched one?
* ko1: Hmm...  Then we need also having pending signal queues per fiber.  That doesn't sound well (think of abnormal fiber termination)
* mame: Isn't that the expected behaviour...? "The fiber that does't mask gets exceptions" sounds very natural.
* akr: I think whether fibers shall have dedicated queues depends on its usage?
* mame: I think it is difficult to decide the detailed design without a practical use case.

Conclusion:

* ko1: I have no opinion...
* mame: If so, how about keeping it as-is? Until a practical use case appears.

### [[Bug #17048]](https://bugs.ruby-lang.org/issues/17048) Calling `initialize_copy` on live modules leads to crashes (jeremyevans0)

* It's still possible to hang the interpreter by calling `Module#initialize_copy` directly.
* Do we want to make changes in this area, or is OK to accept the current behavior and close this issue?

Preliminary discussion:

* mame: leave it to nobu
* nobu: I couldn't repro

Conclusion:

* nobu: I will take conversation with Jeremy

### [[Feature #18127]](https://bugs.ruby-lang.org/issues/18127) Ractor-local version of `Singleton` (ko1)

* Is it acceptable?
* ko1: the content of `Singleton#instance` tends to be mutable

Discussion:

* matz: How should we use that?
* ko1: This is not a drop-in replacement.  I am proposing new `RactorLocalSingleton`.
* knu: What happens when such program run without a Ractor?
* ko1: That should work.

Conclusion:

* matz: Accepted.

### [[Feature #18159]](https://bugs.ruby-lang.org/issues/18159) Integrate functionality of dead_end gem into Ruby (duerst)

- It would be very helpful for beginners

Preliminary discussion:

* mame: It does not work... Ah it works when `t.rb` is loaded as a library

```ruby=
$ cat t.rb
class Dog
  def bark
    puts "bark"

  def woof
    puts "woof"
  end
end

$ dead_end t.rb

DeadEnd: Missing `end` detected

This code has a missing `end`. Ensure that all
syntax keywords (`def`, `do`, etc.) have a matching `end`.

file: /home/mame/work/ruby/t.rb
simplified:

      1  class Dog
    ❯ 2    def bark
    ❯ 5    def woof
    ❯ 7    end
      8  end
```

* knu: It looks premature, as schneems himself said. It would be good to consider bundling after it becomes mature
* ko1: I like to keep it external, not builtin. At least, a bundled gem would be enough
* knu: I want this to be detected and solved while in editing the code rather than run-time.  It can be plugged in to code formatters or lints.  Maybe `ruby -c` can be extended to load plugins.

Discussion:

* mame: Basically I am for the integration.
* nobu: It redefines "Kernel#require", which is troublesome
* knu: The algorithm is very heuristic, so it would be good to wait that it becomes mature.
* ko1: Is it really needed to make it built-in? I think bundling it is enough
* duerst: Built-in is very important, especially for newbies.
* nobu: It sets five-second timeout, so the algortihm may took so long
* ko1: We can improve the implementation details, but I'm afraid if the heuristic algorithm says very weird output, which is rather confusing to newbies. It may spot completely unrelated lines, so we need evaluation, but I'm not sure how to evaluate it (it seems very difficult to evaluate). Continue to use it?
* mame: The redefinition of "Kernel#require" is not preferable, but I think there is no other good way for dead_end. We need to prepare some internal APIs to allow an extension of syntax error message. This is indeed an implementation detail, but it is not easy to organize

```ruby=
class SyntaxError
  attr_reader :file_path

  alias original_message message

  def message
    "#{ original_message }\nDead end! ~~~" + DeadEnd.message(File.read(file_path))
  end
end
```

- knu: It needs `SyntaxError` to include source location information.  And maybe the source code itself for `ruby -e` and `eval`.

Conclusion:

* knu: We need to prepare APIs for dead_end to remove "require" redefinition hack. "at_exit" or something are nor preferable.
* Not much time for Ruby 3.1, but e.g. include it in master in January to have time to try it out and include it in Ruby 3.2.

## shyouhei (free-format topic)

* Ruby 2.6.8's fileutils is out of sync of its gem https://github.com/ruby/fileutils/issues/59 (shyouhei (Shyouhei Urabe))
  * headius (Charles Nutter) found this glitch. It seems to be due to https://bugs.ruby-lang.org/issues/16979.
  * Given the diff is a bug fix, should the gem also be fixed?
  * https://bugs.ruby-lang.org/issues/18169
* hsbt: I forgot to track stable & old stable branch on the default gems.
    * It's good to backport from ruby_2_6 to github.com/ruby/* and release the new version like x.y.z.a
    * or You can merge the latest version (not bundled version) of the default gems.
    * or You can merge the bundled version of the default gems with Ruby 2.6.

Conclusion

* We should bump up x.y.z.a versioning policy and backport the default gems.
* The default gems maintainers include hsbt should(?)/will release them with ruby/* repos.
* They are best effort.

## hsbt 1 (free-format topic)

* How about these proposals? Does anyone have an objections for them?
  * [[Feature #17297]](https://bugs.ruby-lang.org/issues/17297): Feature: Introduce `Pathname.mktmpdir`
  * [[Feature #17296]](https://bugs.ruby-lang.org/issues/17296): Feature: `Pathname#chmod` use `FileUtils.chmod` instead of `File`
  * [[Feature #17294]](https://bugs.ruby-lang.org/issues/17294): Feature: Allow method chaining with `Pathname#mkpath` `Pathname#rmtree`
  * [[Feature #17295]](https://bugs.ruby-lang.org/issues/17295): Feature: Create a directory and file with `Pathname#touch`
* knu: `FileUtils#touch` accepts some keyword arguments. If `Pathname#touch` is introduced, it should delegate the keyword arguments
* nobu: Should `Pathname#touch` create a base directory like `mkpath`? Should the keyword `mtime` be respected to the directory?
* mame: I have assumed that `Pathname` is so stable, but still there are many proposals. So it may be premature to make it builtin
* knu: We need to decide the criteria for method inclusion. There are so many method candidates that we want in `Pathname`, but introducing them blindly will bring a huge maintenance problem (and possible security issues)
* akr: Pathname already has methods that call FileUtils methods, and the new proposal mktmpdir would require the tmpdir library.  Do we want to build them in as well?
* knu: If we built in Pathname, builtin parts should only include aready builtin features, such as path name manipulation, FileTest/FileStat/File/Dir's builtin methods.

## hsbt 2 (free-format topic)

* Organization level funding configuration for Ruby account of GitHub
  * https://efcl.info/2021/09/04/github-meta-repository/
  * to Matz: How about this?
  * https://bugs.ruby-lang.org/projects/ruby/wiki/Donation
* Matz: Agreed, let's RA receive the donation via GitHub sponsors. I leave administrative minutiae to hsbt-san and shugo-san

## mame/ko1 (free-format topic)

* May we introduce `RubyVM::ISeq.keep_script_lines=` as an internal API?
* It makes all ISeqs keep the original source code.
* Unlike `__SCRIPT_LINES`, the string of the source code will be GC'ed when the corresponding ISeq is GC'ed
* It would be useful for debug gem, and error_highlight gem with IRB

```ruby=
iseq = RubyVM::InstructionSequence.compile("p 1\np 2\ndef foo = nil")
p iseq.script_lines #=> nil

iseq.eval
p RubyVM::InstructionSequence.of(method(:foo)).script_lines #=> nil

RubyVM::InstructionSequence.keep_script_lines = true
iseq = RubyVM::InstructionSequence.compile("p 10\np 20\ndef bar = nil")
p iseq.script_lines
#=> ["p 10\n", "p 20\n", "def bar = nil"]

iseq.eval
p RubyVM::InstructionSequence.of(method(:bar)).script_lines #=> ["p 10\n", "p 20\n", "def bar = nil"]
```
```ruby
RubyVM.keep_script_lines = true
RubyVM::AbstractSyntaxTree.keep_script_lines = true
RubyVM::InstructionSequence.keep_script_lines = true
```

* matz: Whichever is OK by me, as long as you can explain why.

```ruby
eval(<<END)
p 100
def baz
end
END

p RubyVM::AbstractSyntaxTree.of(exc.backtrace_locations.first).script_lines
p RubyVM::AbstractSyntaxTree.of(exc.backtrace_locations.first, keep_script_lines: true).script_lines

p RubyVM::AbstractSyntaxTree.of(method(:foo)).script_lines

p RubyVM::AbstractSyntaxTree.parse("foo", keep_script_lines: true).script_lines
```

## other topics

* https://bugs.ruby-lang.org/issues/14479

```ruby=
def foo
  tp.invoke_call # ~2.4, jeremy's patch
  begin
    tp.invoke_call # 2.5~
    puts "hi"
  rescue => e
    puts "In rescue"
  end
end

TracePoint.trace :call do |tp|
  raise "kaboom" if tp.method_id == :foo
end

foo
```

* https://bugs.ruby-lang.org/issues/18170
* https://bugs.ruby-lang.org/issues/18171
* https://bugs.ruby-lang.org/issues/18172
