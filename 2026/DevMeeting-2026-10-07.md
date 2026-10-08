# DevMeeting-2026-10-07

https://bugs.ruby-lang.org/issues/22394

## DateTime and location

* 2026/10/07 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2026/11/12 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

* hsbt: k0kubun wants a tagged 4.1.0-preview1 so that users can test against it. It needs naruse, who was absent today.
  * mame asked to naruse later, and naruse ack'ed to it.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #22395]](https://bugs.ruby-lang.org/issues/22395) `RubyVM::InstructionSequence.load_from_binary` to optionally accept `file` and `path` arguments like `compile` (byroot)

* ISeq caching has a long live problem that ISeqs contain the source file path and realpath, making Iseq "non-portable". See [Feature #17593].
* To solve this, I think we need to be able to provide the new path and realpath when loading an ISeq from binary. Which means extending some APIs.
* Proposed: `load_from_binary(binary [, file[, path]])`, similar to the existing `compile(source[, file[, path[, line[, options]]]])`.
* Also C API: `VALUE rb_iseq_load_from_binary(const char *ptr, size_t len, VALUE file, VALUE path)`. The later argument can be set to `nil`.

#### Discussion:

* ko1: this affects `__FILE__` and `require_relative`
* ko1: I don't like separating `file` and `path` but they are split already.
* mame: is path a path to infer require_relative?
* ko1: yes, file is to be shown in the backtrace.

#### Conclusion:

* matz: OK.
* ko1: I will respond.

### [[Feature #22309]](https://bugs.ruby-lang.org/issues/22309) Allow super in a module method to work if method was called by refinement method super (jeremyevans0)

* From Ruby 2.4-4.0, `super` has been allowed, but used the wrong method lookup, so it generally resulted in a `NoMethodError`.
* I changed it to always raise a `NoMethodError` instead of doing method lookup a few months ago (#22071).
* However, I think it's better to actually remove the limitation and allow `super` to work correctly.
* This also fixes `defined?(super)` and `{Method,UnboundMethod}#super_method` in these methods.
* Can we remove this limitation? @shugo has already reviewed the pull request.

#### Preliminary discussion:

```ruby
module M
  def m = [:M, *super]
end

class C
  prepend M
  def m = :C
end

module R
  refine M do
    def m = [:R, *super]
  end
end
using R

C.new.m #=> current: super: no superclass method 'm' for an instance of C
        #=> expected: [:R, :M, :C]
```

```ruby
class C0
  def m = :C0
end

module M
  def m = [:M, *super]
end

module R
  refine M do
    def m = [:R, *super]
  end
end
using R

class C < C0
  include M
  def m = [:C, *super]
end

p C.new.m
__END__
#=>
t.rb:6:in 'M#m': super: no superclass method 'm' for an instance of C (NoMethodError)
	from t.rb:11:in 'm'
	from t.rb:18:in 'C#m'
	from t.rb:21:in '<main>'

[SCRIPT] t.rb
[DIFF] ruby 2.3.8p459 (2018-10-18) [x64-mswin64_140] (0.2 sec)
======================================================================
t.rb:10:in `refine': wrong argument type Module (expected Class) (TypeError)
	from t.rb:10:in `<module:R>'
	from t.rb:9:in `<main>'
======================================================================
[DIFF] ruby 2.4.10p364 (2020-03-31 revision 67874) [x64-mswin64_140] (0.2 sec)
======================================================================
t.rb:6:in `m': super: no superclass method `m' for #<C:0x0000022fec283b38> (NoMethodError)
	from t.rb:11:in `m'
	from t.rb:18:in `m'
	from t.rb:21:in `<main>'
```

#### Discussion:

* matz: I am satisfied for the proposed behavior.
* mame: is it intended that `super` in a refinement calls the method of the refined class?
* matz: yes. Refinements were designed that way.
* shugo: it already works for classes. Only modules were left out, because the refinement could not see that the module was included.
* shugo: the ICLASS chain is cached with the refinement ICLASS as the key. That ICLASS is created at each `using`, so the cache grows without bound. It should be keyed by the refinement itself, or use a weak-key cache.
* shugo: minor points: the cache lives in the classpath slot of the ICLASS, and filling it takes `RB_VM_LOCKING`, which blocks other Ractors. The latter is acceptable to me.
* nobu: is there an upper limit, or a point where the cache is cleared?
* shugo: neither, for now. It is bounded by the number of refinements in a normal application.

#### Conclusion:

* matz: Accept the proposal if shugo's concerns are resolved
* shugo: I will discuss with Jeremy in the ticket or PR
* matz: I will reply

### [[[Bug #22276]]](https://bugs.ruby-lang.org/issues/22276 ): alias in a module falls back to Object even in classes not inheriting from Object (shugo)

https://github.com/ruby/ruby/pull/18740

#### Discussion:

* shugo: matz agreed last time to remove this behavior and left the schedule to me. A warning in verbose mode is already in.
* shugo: a helper moves the warning to the next stage automatically when the version is bumped. I will close the ticket once the behavior is removed.

#### Conclusion:

* matz: OK with the schedule.

### [[Feature #22399]](https://bugs.ruby-lang.org/issues/22399) Iterator Methods for String Bit Operations (hasumikin)

* Proposed new methods `each_bit`, `bits`, `each_bit_offset` and `bit_offsets` for String
* They are the iterator forms of the single-bit read methods merged in #22118
* Please discuss whether the method names and the API conventions are suitable for Ruby

#### Discussion:

* matz: I am ok for `String#bits` but am not sure if `String#each_bit` is really needed
* matz: what are `each_bit_offset` and `bit_offsets` for?
* mame: e.g. listing the lit pixels of a bitmap as coordinates.
* akr: no existing method enumerates every matching offset like this.
* nobu: with a block, `bits` already works the same as `each_bit`.
* mame: if the goal is to save memory, the block form looks more natural than returning an Array.

#### Conclusion:

* matz: I will ask whether each_* is really needed
* matz: methods (such as `String#bits`) that return an Array requires memory, but the original purpose of these bit operation methods was to reduce memory consumption. Is it ok?

### [[Feature #22405]](https://bugs.ruby-lang.org/issues/22405) Run-Length Methods for String Bit Operations (hasumikin)

* Proposed new methods `bit_run_length`, `each_bit_run` and `bit_runs`
* Likewise, please consider the method names and the API conventions

#### Discussion:

* mame: `bit_runs` produces run lengths, so it is the encoder. Decoding the runs back is the harder part.
* nobu: `bit_set` does not extend the String, so decoding needs an explicit resize.
* akr: starting from a zero-cleared String, decoding needs only `bit_set`.
* matz: the use cases on the ticket, partial refresh of e-paper and run-length data from sensors, need more thought on how convincing they are.

#### Conclusion:

* same for above ticket

### [[Feature #22400]](https://bugs.ruby-lang.org/issues/22400) Use rb_long_t for lengths so that String and Array can exceed 2GiB on mswin (hsbt)

* The first step (PR #18862) replaces `long` with `rb_long_t`, which stays `typedef long`, and changes nothing. The second step makes it 64 bits on mswin and breaks a few extensions there. I would like to know whether we go this way, whether `rb_long_t` is a good name, and whether both steps can land in 4.1.

#### Discussion:

```c
long len = RSTRING_LEN(str); // now returns rb_long_t (= long long in mswin)
```

* matz: `long` was chosen when a 64-bit `long long` was not an option. Having a separate type now is reasonable.
* akr: what about `unsigned long`?
* hsbt: `rb_ulong_t` is already in the PR, along with a macro that tells extensions the size of `rb_long_t`.
* akr: changing Fixnum later would mean converting the `long` in the Fixnum code as well.
* mame, ko1: an extension that stores the new return value into `long` silently truncates.
* nobu: it surfaces only on mswin and only beyond 2 GiB.
* hsbt: MSVC warning C4244 can catch it.
* hsbt: fewer than a hundred C extensions are widely used, so fixing them is feasible.
* matz: the type name must not contain a bit width, because the width depends on the platform. On mswin it should be defined with an explicit 64-bit type rather than `long long`, which a future Windows could change again.
* matz: an `int`-like name may fit better than `long`. mruby uses `mrb_int`. No strong opinion.
* hsbt: a build option for trying it on MinGW is fine, but mswin should switch by default.

#### Conclusion:

* matz: OK for both the first step and the second one
* matz: I have no strong opinion about the C API type name. Up to hsbt san
* matz: mswin can switch to 64 bits by default.
* matz: I will reply

### [[Feature #22392]](https://bugs.ruby-lang.org/issues/22392) Allow refinements to define && and || (shugo)

* I propose to allow `&&` and `||` to be defined in refinements, so that DSLs can write `where { name == "x" && age > 20 }` instead of `where { (name == "x") & (age > 20) }`.
* The right-hand side is evaluated before the hook is called, so a hook never short-circuits; code outside `using` or `Proc#refined` keeps the current behavior, and defining them outside refinements raises NameError.
* `&&=` and `||=`, conditions such as `if a && b`, and statements that discard the value keep the current behavior.
* PoC: https://github.com/ruby/ruby/pull/19133
* Objections on the ticket: the cost for non-users (alanwu) and that `&`/`|` with parentheses is readable enough (matheusrich). With conditions excluded, code that doesn't use the hooks pays +2..3 instructions per `&&`/`||` whose value is used and about +0.4% of iseq memory. Is the feature worth that?

#### Discussion:

```ruby
module QueryDSL
  refine Condition do
    def &&(other) = And.new(self, other)
    def ||(other) = Or.new(self, other)
  end
end

class BasicObject
  def && = self ? yield : self
  def || = self ? self : yield
end
1 && 2      #=> 2
nil && 2    #=> nil
false && 2  #=> false
1 || 2      #=> 1
nil || 2    #=> 2
false || 2  #=> 2
```

* matz: what about short-circuit evaluation where they are redefined?
* shugo: the hooks never short-circuit. C++ allows overloading them and its guidelines discourage it. Other languages resolve it at compile time, e.g. Scala with by-name parameters.
* mame: an alternative is to make `&&` and `||` ordinary methods of `BasicObject` that take the right-hand side as a block, which keeps short-circuiting (code above).
* matz: I see no theoretical problem with that.
* matz: this is a big change and I cannot decide easily. But I have felt that conditions like `where { name == "x" && age > 20 }` are hard to write today, and I sympathize with the motivation.
* shugo: the current proposal is half-baked, because it applies only in refinements and only where the value is used. If we do it, `&&` and `||` should be redefinable in every case, like `!`.
* ko1: then every `&&` and `||` pays a check like the one for `!`, and type analysis can no longer narrow by them.
* matz: losing the narrowing is fine.

#### Conclusion:

* matz: I will reply that I understand the motivation.
* shugo: After that, I will withdraw the proposal for now

### [[Feature #22402]](https://bugs.ruby-lang.org/issues/22402) Drop support for Darwin on PowerPC (hsbt)

* PowerPC Macs stopped at Mac OS X 10.5 in 2007 and no CI covers them, but `coroutine/ppc`, `coroutine/ppc64` and the PowerPC branches under `__APPLE__` remain and still get occasional patches. I would like to drop Darwin on PowerPC only, not PowerPC in general (ppc64le and AIX stay), and raise the minimum Mac OS X version to 10.6 along with it (https://github.com/ruby/ruby/pull/19183). Can we drop it?

#### Discussion:

* hsbt: Mac OS X 10.5, the last release for PowerPC, came out 19 years ago and its support ended 15 years ago. Hobbyists still report build failures about twice a year, and some of them get fixed upstream.
* hsbt: this drops Darwin on PowerPC only. ppc64le on Linux has CI and stays.
* nobu: this also drops Mac OS X 10.5?
* hsbt: yes.

#### Conclusion:

* no one is against the removal
* hsbt: I will comment on the ticket and go ahead.

### [[Feature #22376]](https://bugs.ruby-lang.org/issues/22376) Undefine the allocator for `Class` (jhawthorn)

* Makes `Class.allocate` raise. Same as ex. `Proc.allocate`
* `Class` can't be subclassed, and I didn't find any legitimate uses in `gem-codesearch`. I don't think there will be compatibility issues.
* Okay to merge? https://github.com/ruby/ruby/pull/18964

#### Discussion:

* akr: Marshal dumps a class by its path and never allocates one, so it does not need `Class.allocate`.
* nobu: several classes already undefine the allocator, e.g. `Process::Status`.

#### Conclusion:

* matz: go ahead, I will reply

---

Matz "let me comment" issues

https://bugs.ruby-lang.org/issues/22134
https://bugs.ruby-lang.org/issues/22300

---

https://bugs.ruby-lang.org/issues/22312 Add parent directory operations and recursive file removal

* matz: looks good
* akr: I have some local improvement patches for the feature
* akr: a safe recursive removal has to handle four things: races through symlinks or directory renames, fd exhaustion if an fd is kept for every level, memory exhaustion if every directory is read in full, and entries that cannot be removed.
* akr: GNU rm reads a directory up to a limit and keeps the fd only when it could not read everything. That level is enough for us.
* nobu: the new options are all keyword arguments named after the GNU coreutils long options, e.g. `parents:` as in `rmdir -p`. The existing optional argument becomes the default of its keyword.
* matz: the API is OK. The implementation is up to akr and nobu.

https://bugs.ruby-lang.org/issues/22328 Should `IO#wait_priority` be deprecated and then removed?

* mame: public code barely uses it. matz's comment on the original ticket seems to have been missed when the PR was merged.
* matz: it should be deprecated and then removed
* matz: I will reply.

https://bugs.ruby-lang.org/issues/22274 Make `IO::Buffer` no longer experimental.

* hsbt and nobu: we will add some concerns about removing experimental flag
* mame: removing the warning is fine if it is annoying, but the documentation should keep calling it experimental for now.
* hsbt: reports about `IO::Buffer` are handled as regular bugs because it is experimental.
* nobu: features are still being added after the ticket was filed.
* matz: it may be better to wait a little longer. I will reply after hsbt and nobu comment.

(After the meeting, matz replied on the ticket: keep it experimental in 4.1 and look again for 4.2. No objection to removing only the warning.)

https://bugs.ruby-lang.org/issues/22379 Remove prism mismatch warning from `#syntax_tree`

* matz: Let me consider wth #22305 (Ruby::Box: statically linked prism loads in only one box)
  * matz: Ruby::Prism or Ruby::Parser
    * It should be defined as autoload
    * It should not be defined under `--parser=parse.y`
    * prism gem should be a bundled gem?
  * mame: my original proposal was Ruby::Node
  * matz: Let me consider

```
module Prism  #=> module Ruby::Parser
``` 

* mame: the warning should stay. Mismatched node definitions are a real problem, and hiding the warning does not fix it.
* matz: I agree with that point.
* matz: my comment on #22305 preferring option 1 did not take `syntax_tree` into account.
* mame: option 1 alone leaves `syntax_tree` without a stable way to get the node definitions of the interpreter's prism. They need their own namespace in core. Then `syntax_tree` no longer requires `prism`, and the warning can go.
* matz: the namespace must be separate, although having both `Prism` and `Ruby::Prism` feels a bit odd.
* hsbt: then `require "prism"` just loads the gem, which can become a bundled gem, and the core namespace always matches the interpreter. That is simple for users, but sharing the code between the two is the hard part.
* ko1: keeping two copies of the source would be bad.
* ko1: what happens with `--parser=parse.y`?
* matz: the namespace is not visible at all, and neither is `syntax_tree`.
* mame: I will try a PR.
* matz: I will comment on the `syntax_tree` side, not on #22305.

https://bugs.ruby-lang.org/issues/22404 Make `Data` (and/or `Struct`, etc.) true "first-class citizens" and thank you!

```ruby
class D[x, y] # D = Data.define(:x, :y); class D
end

class D
  add_field :x, :y
  D.new(x: 1, y: 2)
  add_field :z, :w
end
D.new(x: 1, y: 2)
```

* matz: this does not make `Data` first-class. It changes the `class` statement.
* matz: I will reject

https://bugs.ruby-lang.org/issues/11817 `map.parallel`

* mame: an 11-year-old ticket, bumped recently by someone else. matz asked for an example back then.
* hsbt: concurrent-ruby and the parallel gem cover this today, and so do Ractor, Thread and Fiber.
* matz: I will reply.

---

Ruby::Box

* Ruby::Box session: 2026-11-06 (Fri) 14:00-18:00 JST, online, with matz and tagomoris.

https://bugs.ruby-lang.org/issues/22329 Specify --disable-gem per Ruby Box
https://bugs.ruby-lang.org/issues/22331 `Ruby::Box`: a box's top self lacks the top-level definition methods
https://bugs.ruby-lang.org/issues/22332 `Ruby::Box`: prelude is evaluated only in the master box, so `pp` and `binding.irb` load into it

#22329 Specify --disable-gems per Ruby Box

* tagomoris: the use case is a clean box that loads only a minimal set of libraries, e.g. for a DSL. Someone from Red Hat asked for it at EuRuKo.
* mame: `--disable-gems` is for development and debugging, not for normal use. Making it per box needs a concrete reason.
* tagomoris: then it can stay pending until a use case comes.

Local rebinding

* matz: local rebinding means that a redefinition in a box also applies to methods called indirectly. If a box redefines `inspect`, `p` in that box calls it.
* tagomoris: Box was partly designed to prevent that. Builtin methods written in Ruby can already opt in, per method, to look methods up from the caller's user box. `Warning.warn` could use it.
* matz: some people want local rebinding and others do not.

```ruby
# common
class C
  def bar = p "common::C"
end

def warn msg
  Warning.send_under_caller_box(:warn, msg)
end
def do_dangerous_method_emitting_warning
  warn "foo"
end

# box1
def foo(x) = x.bar

def foo2(x) = x.send_under_caller_box(:bar)
def foo(x) = foo2(x)

def foo2(x) = x.send_under_box(:bar, Ruby::Box.prev_frame)
def foo(x) = foo2(x)

# box2
def Warning.warn(msg) = ...
do_dangerous_method_emitting_warning()

class C
  def bar = p "box2::C"
end
box1::foo(C.new) #=> "box2::C"
```

Class identity across boxes

* tagomoris: an idea from talking with matz: when the feature name given to `require` and the resulting class path are the same, treat the classes in different boxes as one class object with separate definitions, as `String` is today. `is_a?` then keeps working across boxes, which mattered a lot when porting test-unit. It could also be a basis for packages.
* mame: using the feature path for this feels dangerous. Why not the class path alone?
* matz: with the class path alone, different versions of a gem may get mixed.

#22305 prism in boxes

* hsbt: prism cannot be required inside a box, and many tools require it.
* mame: option 1 alone breaks `syntax_tree`. The prism developers oppose option 2. A separate namespace for the interpreter's prism solves both.
* tagomoris: I will comment on #22305.

CI

* hsbt: I want CI to run `make check` with `RUBY_BOX=1` by 4.1.
* tagomoris: no objection.

---

### [Connect through Happy Eyeballs Version 2 on Windows](https://github.com/ruby/ruby/pull/18791) (hsbt)

* hsbt: POSIX behavior is unchanged. Three things differ on Windows.
* hsbt: the socket is returned in blocking mode, because `IO#close` cannot interrupt a read on a nonblocking socket on Windows.
* akr: interrupting a read by close differs among Unix systems too. The test may rely on behavior that is not guaranteed.
* hsbt: Winsock has no `EAI_ADDRFAMILY` and returns `EAI_NONAME`, so the error is kept and the connection goes on with the other family.
* akr: that is fine as long as the family that succeeds is used.
* hsbt: Winsock reports a failed nonblocking connect only in `exceptfds`.
* akr: that is unavoidable and documented by Microsoft. The Ruby-level implementation already works on Windows, so compare the behavior with it.
* hsbt: I will not merge it yet.
