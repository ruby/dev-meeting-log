---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting2021026Japan

https://bugs.ruby-lang.org/issues/17200

## DateTime and location

* 10/26 (Mon) 13:00-17:00 JST @ Online

## Next Date

* 11/20 (Fri) 13:00-17:00 JST @ Online

* pre-agenda reading: 11/16 (Mon) 13:00- @ online

## Announce

## Ruby 2.7 timeframe

none

## About 3.0 timeframe

Next: preview 2
* Ractor changes
* Type: TypeProf is bundled

## Check security tickets

[secret]

### [[Feature #17016]](https://bugs.ruby-lang.org/issues/17016) Enumerable#scan_left (parker)

* This is a proposal to add a method to enumerable that accumulates the results of injecting an operation over the enumerable. There is interest in adding this method, but concern about the name. I think that `#accumulate` is a good name.
* Should we add this method?
* Is the name `#accumulate` acceptable?

Preliminary discussion:

* mame: `accumulate` sounds the same as `inject`
* (nobu: `scan_left` resembles `scan_right`)

```ruby=
[1, 2, 3].accumulate(0, &:+) => [0, 1, 3, 6]
```

Discussion:

* Matz: Use cases still not concrete.
* Matz: I don't like `scan_left`

Conclusion:

* Request for feedbacks.


### [[Feature #16851]](https://bugs.ruby-lang.org/issues/16851) Ruby hashing algorithm could be improved using Tabulation Hashing (greggzst)

* Improves hashing algorithm

Preliminary discussion:

* nobu: Is this algorithm reviewed in terms of security?
* mame: The patch does not call #hash... It should be fixed easily, but the performance measurement is not trustable

Discussion:

* 

Conclusion:

* mame: set feedback


### [[Bug #14012]](https://bugs.ruby-lang.org/issues/14012) NameError is raised when use class variables in Refinements (jeremyevans0)

* Refinement methods cannot reference class variables of the classes/modules they refine.
* Is this a bug or expected behavior?
* If a bug, how should class variable lookup work in refinement methods?

Preliminary discussion:

* ko1: how about to pending it? it seems difficult...

Discussion:

(failed to log)

Conclusion:

* matz: keep the behavior as-is


### [[Bug #13768]](https://bugs.ruby-lang.org/issues/13768) SIGCHLD and Thread dead-lock problem (jeremyevans0)

* You can end up with thread deadlock even when a signal handler could result in breaking the deadlock.
* I don't think this is a bug, otherwise we would have to skip deadlock detection for all custom signal handlers.
* Do we want add a feature such as `Thread.ignore_deadlock = true` so that we can support this approach?

Preliminary discussion:

* ko1: `Thread.ignore_deadlock = true` is easy to implement (just skip dead-lock detection)
* nobu: should we skip detection on there are trap handlers?

Discussion:

* mame: we can reroute this particular case by creating another thread that just sleeps forever.
* nobu: that is commented at the OP's example.
* ko1: traps to kill deadlock detector souds too much.
* mame: is deadlock detector useful?
* nalsh: I remember fluentd uses it.
* matz: is this that deadlock detector can false-positive so programmers want to stop that?
* ko1: yes I think so.

Conclusion:

* matz: accept `Thread.ignore_deadlock = true`


### [[Bug #12485]](https://bugs.ruby-lang.org/issues/12485) Kernel.Rational raises TypeError though given denominator returns 1 by to_int (jeremyevans0)

* Do we want Kernel#Rational to fallback to checking for `to_int` if `to_r`  is not defined?
* If so, is the patch acceptable?

Preliminary discussion:

* nobu: seems nice.
* mrkn: I'll handle it.

Conclusion:

* already closed.


### [[Bug #11808]](https://bugs.ruby-lang.org/issues/11808) Different behavior between Enumerable#grep and Array#grep (jeremyevans0)

* Is the current behavior bug or expected?
* @nobu has a patch allowing regexp global variables to be available in blocks passed to Enumerable#grep.
* If current behavior is a bug, do we want to use @nobu's patch?

Preliminary discussion:

* ko1: nobu's patch is ad-hoc; `grep` first writes $~ to a wrong frame, and then read it and to a right frame.
* nobu: I see, will try fixing the patch

Discussion:

* ko1: this should be fixed but the patch is not good. I'll handle it if I have time.

Conclusion:

* ko1 will respond.


### [[Bug #11202]](https://bugs.ruby-lang.org/issues/11202) No warning when a link to an original method body was removed (jeremyevans0)

* I don't think this is a bug.  Do we want to add a warning in this case?

Preliminary discussion:

* nobu: I think it is not a bug.
* mame: there is a technique to stop redef warning

```ruby
class C0
  def foo; end
  alias foo foo
  def foo; end # no warning
end
```

Discussion:

```ruby
class C0
  def foo; end
end

class C < C0
  alias bar foo    # C0#foo is pointed from C0 and C#bar.
  def foo; end
  def bar; end     # C0#foo is not pointed from anybody.
end

class D
  def bar; end
  def bar; end
  # warning: method redefined; discarding old bar
end

class E
  def foo; end
  alias bar foo
  def foo; end
  def bar; end
end
```

* matz: I think it was intentional.
* ko1: I can live both with/without it.

Conclusion:

* matz: keep it as-is.


### [[Feature #17143]](https://bugs.ruby-lang.org/issues/17143) Improve support for warning categories (jeremyevans0)

* Based on feedback at last month's developer meeting, I'm now proposing adding only two new categories: `:uninitialized_ivar` and `:redefine`.
* I have a pull request that adds support for those two categories and adds the `:deprecated` category to additional warnings, it is OK?

Preliminary discussion:

* ko1: process global?

Discussion:

* naruse: I think redefine shall not be ignored because I sometimes find my mistake by the warning.
* ko1: please comment so.
* akr: https://docs.python.org/3/library/warnings.html It seems that Python's standard categories are not the granularity similar to this proposal.
* akr: What the situation that we need to control "redefine" warnings and "uninitialized_ivar" warnings?


Conclusion:

* matz: The proposed categories look to me too fine-grained


### [[Feature #17187]](https://bugs.ruby-lang.org/issues/17187) Add connect_timeout to TCPSocket (glass)

* Could I merge it?

Preliminary discussion:

* ko1: any problem?
* mame: ask akr to review the patch

Discussion:

* akr: no strong opinion.
* akr: one thing: it should be documented.
* ko1: https://ruby-doc.org/stdlib-2.5.3/libdoc/socket/rdoc/Socket.html#method-c-tcp
* mame: no :resolv_timeout?

Conclusion:

* matz: accepted


### [[Feature #16746]](https://bugs.ruby-lang.org/issues/16746) Endless method definition (aycabta)
### [[Feature #15921]](https://bugs.ruby-lang.org/issues/15921) R-assign (rightward-assignment) operator (aycabta)

These two are related.


* The following was discussed at [DevelopersMeeting20200514Japan](https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20200514Japan.md)

> Conclusion:
>
> matz: Let me have some time to consider this issue
> ```
>   def foo() = or def foo() :
>   def foo(n) = n => @x
>
>   (def foo(n) = n) => @x
>   or...
>   def foo(n) = (n => @x)
> ```
> `def foo=bar` may break compatibility (`def foo= bar; bar; end`); So, no-arugment mark () is mandatory. But I may reconsider

> Conclusion:
> 
> matz: The motivation is still valid even after pipeline operator is gave up. It is still useful for a long method chain.
> matz: I think an experiment with commit is needed and works. I will explain.

----

Preliminary discussion:

* nobu: `def foo=` is impossible to implement because it conflicts with `def foo=arg; @foo = arg; end`. If a space is mandatory between `foo` and `=`, it is possible
* ko1: Currently, `1 => a[0]` is allowed, but the semantics will change if it becomes a one-line pattern matching
* ko1: Currently, `1 => x` returns `1`, but if it becomes a one-line pattern matching, we may want its return value (especially when it is used in a condition)

```ruby
begin
  h => {a: {b:}}
  p a, b
rescue NoMatchingPatternError
end
```

Discussion:

* matz: I prefer `def foo() = bar` now.
* matz: re: allowing `def foo() = bar => baz`: the RHS assignment shall be a part of method body.
* matz: It would be acceptable to `def foo = bar`

```ruby
def foo=expr      # keep as a traditional method definiton: def foo=(expr); ... (expect "end")
def foo = expr    # currently syntax error, but change it: allow as an one-line method definition if there is space between method name and "="
def foo() = expr  # keep as an one-line method definition: def foo; expr; end
def foo=() = expr # keep as syntax error

def foo() = expr => var # change: def foo(); expr => var; end

# Currently, a setter-like method name is prohibited because it may be too cryptic
def foo=(n)=n=>@var # too cryptic
def foo= =expr      # allowed? but it has no arguments, so there is no use case in practical

private def foo() = expr => var
#=> private (def foo() = (expr => var))
```

* knu: `def foo=(x) = set_foo(x)` would be handy and I see no reason to prohibit defining a setter method.

* matz: Well, how about a colon...

```ruby
def foo: expr
def foo : expr
def foo(): expr
def foo=(): expr
def foo(): expr => var
def foo(name: var): bar
def foo(k: :k): :k::method_calll
```

* matz: it strongly resembles keyword argument. abandon the idea
* knu: if we use colon I think mandating parens shall make them more readable.

Conclusion:

* matz: allow the following two items

```ruby
def foo = expr    # change: allow if there is space between method name and "="; it shall be the same as "def foo; expr; end"
def foo() = expr => var # change: it shall be "def foo(); expr => var; end"
```

### [[Feature #17260]](https://bugs.ruby-lang.org/issues/17260) Promote pattern matching to official feature (ktsj)

 * I'd like to remove single line pattern matching and experimental warnings.

Preliminary discussion:

* ko1: I have compatibility concern if use `=>` as one-line pattern match syntax.

```ruby
# current master
ret = begin 
  1 => x
end #=> 1

# current master
ret = begin
  1 in x # Syntax Error
end

# current master
a = []
ret = begin
  0 => lv   #=> lv = 0
  1 => a[0] #=> a[0] = 1
  2 => C    #=> C = 2
  3 => @i   #=> @i = 3
  4 => @@cv #=> @@cv = 4
  5 => $gv  #=> $gv = 5
  6 => o.m  #=> o.m = 6
  7 => a, b #=> a, b = 7
end
```
```ruby
case [0, 1, [2]]
in [0, @a, []]
in @a if @a == 0

```

Discussion:

* Matz: reject R-assignment and accept one-line pattern matching.

```ruby
# current master
a = []
ret = begin
  0 => lv   #=> lv = 0
  2 => C    #=> case 2; in C; end #=> NoMatchError or NameError
  7 => [a, b] # syntax ok, but NoMatchError
  
  # Syntax error
  1 => a[0] #=> a[0] = 1
  3 => @i   #=> @i = 3
  4 => @@cv #=> @@cv = 4
  5 => $gv  #=> $gv = 5
  6 => o.m  #=> o.m = 6
  7 => a, b
end
```

Conclusion:

* Delete the entire RHS assignment feature, and use `=>` as one-liner pattern matching.
* return value NoMatchError (not true/false nor assinign value)
* `=>` and find patterns remain experimental.
* `=>` is a void expression.


### [[Feature #17259]](https://bugs.ruby-lang.org/issues/17259) `Kernel#warn` should ignore <internal: entries (eregon)

* Currently, `warn('message', uplevel: n)` can show `<internal:kernel>:90: warning: message` instead of `calling_file.rb:42: warning: message`.
* https://github.com/ruby/ruby/pull/3647 fixes it, OK to merge?
* Also, RubyGems redefines Kernel#warn for skipping RubyGems' require. That redefinition caused various incompatibilities. If we ignore `<internal:`, no need to redefine `Kernel#warn` (I'll make a PR to RubyGems).

Preliminary discussion:

* ko1: looks good
* nobu: feels fine as the first step.

Discussion:

* nobody was against it.

Conclusion:

* matz: accepted


### [[Feature #17171]](https://bugs.ruby-lang.org/issues/17171) Why is the visibility of constants not affected by `private`? (marcandre)

* Privacy is important for gems in particular. `private_constant` is verbose to use.
* The constants affected would have special flag "legacy public"
* Ruby 3.0: warn (verbose only) on first access to "legacy public"
* Ruby 3.1: warn (always) on first access to "legacy public"
* Ruby 3.2: "legacy public" becomes private

Preliminary discussion:

* znz: the meanings of the other visibilities (e.g. `protected`) are not obvious. so no argument `private_constant` feels better.

Discussion:

* shyouhei: How about a magic comment?  If `# private_constant: true` is written, `private; Foo = 1` will define a private constant
    * shyouhei: I guess people want it on/off per-gem.
* matz: why is a singleton method involved? We should ignore them in this discussion

* ko1: is `D` also private constant?

```ruby
class C
  private
  
  class D # is D private constant?
  end
end
```

* znz: How about `protected`?

```ruby
class C
  protected
  D = 1 # is public?
end
```

* akr: How about starting with `private_constant` with no argument?

Conclusion:

* matz: Will reply
  * Changing `private` looks too aggressive
  * `private_constant` with no argument is another idea, I'd like to discuss it in another ticket if anyone really wants it


### [[Feature #17176]](https://bugs.ruby-lang.org/issues/17176) `GC.auto_compact` / `GC.auto_compact=(flag)` (tenderlove)

* Currently compaction is a manual process, eventually I want to make it automatic
* This feature lets users "opt in" to automatic compaction so we can test extensions
* Reference updating logic remains the same so "heap integrity" should be the same

Discussion:

* (no one has any opinion :-)

Conclusion:

* accepted as long as the default remains false


### [[Feature #17265]](https://bugs.ruby-lang.org/issues/17265) Have common ancestor `Bool` for `TrueClass` and `FalseClass` (marcandre)

* Useful for `RBS`
* Should have very limited incompatibility

Discussion:

* mame: soutaro (the author of RBS) said RBS does not need Bool

Conclusion:

* matz: If RBS does not need it, we have no new motivation


### [[Feature #17267]](https://bugs.ruby-lang.org/issues/17267) Remove `Win32API` at Ruby 3.0 (hsbt)

* Does anyone oppose it? 

Discussion:

* nobody is against it.

Conclusion:

* (failed to log)


### [[Feature #17277]](https://bugs.ruby-lang.org/issues/17277) Make `Enumerator#with_index` yield row and col indices for `Matrix` (greggzst)

* Access Matrix indices in enumerable methods

Preliminary discussion:

```ruby=
Matrix[[1, 2], [3, 4]].each_with_index {|elem, i, j| }
Matrix[[1, 2], [3, 4]].each.with_index {|elem, i| }
#                          ^ <- watch this difference
```

Discussion:

* any attendee has no opinion

Conclusion:

* leave it to marcandre, the current maintainer of Matrix


### [[Bug #17146]](https://bugs.ruby-lang.org/issues/17146) Queue operations are allowed after it is frozen (ko1)

* It is an interesting question what "freeze" mean.

Preliminary discussion:

* ko1: options
  * (1) keep it as-is
  * (2) raise `FrozenError` if it attempts to modify
  * (3) raise `TypeError` if it attempts to `freeze` (or no method error)
  * (3') ignore `freeze` completely (as no-op)
* ko1: there are other examples
    * Binding
    * Proc
    * Thread, Fiber
    * Mutex, CV
* znz: `ENV.freeze` raises `TypeError`
* mame: I want a practical use case to determine what behavior is the best. Unless there is a practical use case, we should not decide it. So I vote for (1) currently.

Discussion:

* (failed to log)

Conclusion:

* matz: no time to discuss anymore. pending


### [[Feature #17256]](https://bugs.ruby-lang.org/issues/17256) Freeze all `Regexp` objects (ko1)

* I feel it is too heavy to freeze all regexp...

Preliminary discussion:

* ko1 found that there are cases when Regexp#=~ is mocked.

Discussion:

* (failed to log)

Conclusion:

* matz: try it in the next year


### [[Feature #17269]](https://bugs.ruby-lang.org/issues/17269) Frozen `Process::Status` (ko1)

* low priority

Preliminary discussion:

* ko1: if we need to return fake result, the singleton method can be used.

Discussion:

* (failed to log)

Conclusion:

* matz: try it in 3.0


### [[Feature #17145]](https://bugs.ruby-lang.org/issues/17145) Ractor-aware `Object#deep_freeze` (ko1)

* Can we define the meaning of class/module?

Discussion:

* ko1: `Ractor.make_shareable` is already introduced
* matz: If the name is `Object#deep_freeze`, it should not be related to Ractor
* shyouhei: this proposed API has intermediate state.
* ko1: yes yes and the OP don't care about such cases.
* shyouhei: why it has to be destructive?
* ko1: name candidates
  * Object#deep_freeze (matz doesn't like it)
  * Object#deep_freeze(skip_sharable: true) (I don't know how Matz feel. And it is difficult to define Class/Module/... on skip_sharable: false)
  * Ractor.make_shareable(obj) (clear for me, but it is a bit long)
  * Ractor.shareable!(obj) (shorter. is it clear?)
  * Object#shareable! (is it acceptable?)
  * ... other ideas?

Conclusion:

* matz: frozen and sharable must be two different stories.  Reject.

### [[Feature #17270]](https://bugs.ruby-lang.org/issues/17270) `ObjectSpace.each_object` should be restricted on multi-Ractors (ko1)

* already introduced, but need confirmation

Discussion:

* already introduced

Conclusion:

* matz: ok


### [[Bug #17268]](https://bugs.ruby-lang.org/issues/17268) special global variables which can be accessed from ractors (ko1)

* already introduced, but need confirmation

Discussion:

* (failed to log)

Conclusion:

* matz: Confirmed.  Making arbitary global variables visible form ractors is beyond this proposal.


### [[Feature #17261]](https://bugs.ruby-lang.org/issues/17261) Software transactional memory (STM) for Threads and Ractors (ko1)

* Can I merge it with current naming and semantics?

Discussion:

* shyouhei: I'd like to hear option from concurrent-ruby
* usa?: How about starting with a gem?
* matz: I'm unsure how useful it is

Conclusion:

* ko1: try it with a gem


### [[Feature #17274]](https://bugs.ruby-lang.org/issues/17274) `Ractor.make_shareable(obj)` (ko1)

* already introduced for convenience, but we can change.

Preliminary discussion:

* naming idea
    * `Ractor.make_shareable(obj)`
    * `Ractor.make_shareable!(obj)`
    * `Ractor.let_shareable(obj)`
    * `Ractor.shareable!(obj)`
    * `Object#sharable!` -> `[1, 2].shareable!`
    * `Ractor.freeze!(obj)`
    * joke
        * `Ractor.s!(obj)`
    * operator
        * `Ractor-obj`
        * `Ractor>obj`
        * `Ractor<obj`
        * `Ractor.(obj)`
        * `Ractor[obj]`
        * `Ractor << obj`



Conclusion:

* skip the discussion


### [[Feature #17273]](https://bugs.ruby-lang.org/issues/17273) `shareable_constant_value` pragma (ko1)

* is it acceptable?

Discussion:

```ruby
# shareable_constant_value: true

A = [1, [2, [3, 4]]] # A = Ractor.make_shareable([1, [2, [3, 4]]])
H = {a: "a"}         # H = Ractor.make_shareable({ a: "a" })

B = [OtherLib::ArrayConst] # breaks OtherLib

Ractor.new do
  p A
  p H
end.take
```

* shyouhei: If `B = [OtherLib::ArrayConst]` freezes `OtherLib::ArrayConst`, it breaks `OtherLib`. A bug report may go to `OtherLib`, and its author is not responsible to the bug.
* mame: It is not due to this pragma. It is the inherent problem of `Ractor.make_shareable` (or `deep_freeze`)
* shyouhei: Agreed, but the pragma looks too easy to shoot the foot.
* shyouhei: `Ractor.make_shareable` may have to "deep-copy" the argument and then deep-freeze it.

Conclusion:

* ko1: I will discuss on another day with matz and shyouhei

### [[Feature#17278] On-demand sharing of constants for Ractor](https://bugs.ruby-lang.org/issues/17278)

Discussion:

```ruby
B = ["foo"]
A = [B, "bar"]

B[0] = "FOO"
Ractor.new do
  A # Ractor.make_shareable(A)
end
```

* ko1: it is possible to implement, but IMO, the mechanism is too complex

Conclusion:

* matz: agreed that it is too complex.

### [[Feature #17284] Shareable Proc](https://bugs.ruby-lang.org/issues/17284)

```ruby
a = [1, [2, 3]]
pr = Proc.new{
  p a.frozen? #=> true
}.shareable!

a[0] = 0 #=> frozen error
```

```ruby=
# literal only

# OK
a = 'foo'
pr = Proc.shareable! do 
  p a
end

b = :b
a = 'foo'.freeze
pr = Proc.shareable do # raise an error if "a" is not sharable
  p a
  binding.local_variable_get(:b) #=> error
  eval('p b') #> compile error
end
a = 'bar'
pr.call #=> frozen 'foo'

# NG
def foo(&b)
  Proc.shareable!(&b) # <= NG
end
```

Conclusion:

* (timeout)

----

Ractor API

```ruby
r = Ractor.new do
  
end

# current
r.send obj, move: false # copy/default
r.send obj, move: true

r.send obj, type: :copy
r.send obj, type: :move
r.send obj, type: :share

r.copy obj
r.move obj
r.share obj

r.copy_send obj
r.move_send obj
r.share_send obj

r.copying_send obj
r.moving_send obj
r.sharing_send obj

Ractor.yield obj, type: ...
Ractor.coping_yield obj
Ractor.moving_yield obj
Ractor.sharing_yield obj
```

```ruby
Fiber.current[:x] = 1
Thread.current[:x] = 1 # Fiber local
Ractor.current[:x] = 1 # Ractor local?



RL = Ractor::LocalVariable.new(default = nil)
RL.value = 1
Ractor.new do
  RL.value #=> nil
  #=> Ractor.local_table[RL.value_id]
end


```
