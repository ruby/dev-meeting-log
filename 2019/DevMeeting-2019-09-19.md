---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-09-19

Please comment on your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of the ticket comments)

*DO NOT* discuss then on this ticket, please.

----
Date: 2019/09/19 (Thu)
Time: 13:00-17:00 (JST)
Place: (Written in the Log)
Sign-up: (Add your name in the Log)
Log: https://docs.google.com/document/d/1oYWVhH6BEgTNy8PxMUd-MS3JUK0kQMw43q5saBp8aZk

# NOTES

- Dev meeting *IS NOT* a decision-making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about the discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place are scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.7 timeframe

## Check security tickets

## Discussion

----

Please comment on your favorite ticket we need to discuss with *the following format*.

```
* [Ticket ref] Ticket title (your name)
  * your comment why you want to put this ticket here if you want to add.
```

Your comment is very important if you are no attendee because we can not ask why you want to discuss it.

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss this feature.
```

We don't guarantee to put tickets in the agenda if the comment violates the format (because it is hard to copy&paste).

**A short summary of a ticket is strongly recommended.  We cannot read all discussion of the ticket in a limited time.**
A proposal is often changed during the discussion, so it is very helpful to summarize the latest/current proposal, post it as a comment in the ticket, and write a link to the comment.

# Log

https://bugs.ruby-lang.org/issues/16152
Venue
9/19 (Thu) 13:00-17:00 @ moneyforward
optional: 9/2 (Mon) 13:00-16:00 @ zoom
Attendees
Add your name (or ask a committer to invite you)
Matz
Mame
Mrkn (remote)
Nobu (remote)
hsbt(remote)
knu
usa (remote)
Martin Dürst
aycabta
osyo
akr
a_matsuda
znz (remote)
Soutaro (invited by mame)
Next Date
10/17 (Thu) 13:00-17:00
pixiv
Announce
About 2.7 timeframe
preview 2
maybe on the end of this month
Check security tickets
Topics
[Bug #12984] rescue *[] should be equivalent to rescue as method_call(*[]) is equivalent to method_call (jeremyevans0)
I think the current behavior is expected. Can matz confirm?
Discussion:
in short: rescue *[] should be the same as rescue?
begin
  raise 'error' #Uncaught exception
rescue *[]
  puts 'caught'
end


matz: it is actually intended.
[Bug #11022] opening an eigenclass does not change the class variable definition context (jeremyevans0)
I think the current behavior is expected. Can matz confirm?
Discussion:
Class variable lookup just ignores singleton classes. Is this intentional?
(mame: I couldn’t follow a very long discussion about what a singleton class should be…)
ko1: If singleton class can have a class variable, the implementation becomes simpler.
mame: We should not change the behavior if there is no need. It just breaks compatibility.
matz: let me consider for a while.
[Bug #8297] extend & inherited class variable issue (jeremyevans0)
I would like to fix Module#class_variables to be consistent with Module#class_variable_get using the patch.
Discussion:
class_variables should return []
module M
@@xyz = 123
end


class C
  extend M
end
p C.singleton_class.class_variables           # [:@@xyz]
p C.singleton_class.class_variable_get :@@xyz # NameError exception


matz: I’m a bit afraid about incompatibility, but try Jeremy’s patch.
[Bug #7844] include/prepend satisfiable module dependencies are not satisfied (jeremyevans0)
I would like to make it so Module#include does not prepend modules before the receiver using the patch.
Discussion:
in short: difficult to understand Module#include result
A.ancestors # => [P, A, Object, Kernel, BasicObject]
S.ancestors # => [S, Q, P, R]
A.include S
A.ancestors #=> [P, R, A, S, Q, Object, Kernel, BasicObject]


# expected:     [P, A, S, Q, R, Object, Kernel, BasicObject]


matz: I expect [P, A, S, Q, P, R, Object, Kernel, BasicObject]
duplication is actually allowed in prepend (in some cases)
also allowed in include (in a pathological case)
module P
end
class C1
end
class C2 < C1
end
C1.prepend P
C2.prepend P
p C2.ancestors #=> [P, C2, P, C1, Object, Kernel, BasicObject]


module P
end
class C1
end
class C2 < C1
end
C2.include P
C1.include P
p C2.ancestors #=> [C2, P, C1, P, Object, Kernel, BasicObject]


module P
end
module Q
  prepend P
end
module R
  prepend P
end
class C
  include Q
  include R
end
p C.ancestors #=> [C, P, R, Q, Object, Kernel, BasicObject]


(I couldn’t follow much discussion about Module#include, prepend, inheritance, module/object system…)
matz: Let me consider for a while. My homework.
[Bug #7522] Non-core “Type()” Kernel methods return new object (jeremyevans0)
I would like to make it so Kernel#{BigDecimal,Complex,Pathname} return argument if given correct type using the patch.
Discussion:
in short: Pathname(p) should return p itself when p is Pathname?
a = []
a.equal? Array(a) #=> true


p = Pathname.new('/tmp')
p.equal? Pathname(p) #=> false


matz: accepted
[Bug #11636] super in instance_eval in a method defined in a module is invoked with a wrong receiver (jeremyevans0)
I would like to raise an exception instead, similar to super in instance_eval in method defined in class using the patch.
Discussion:
# current behavior (can cause SEGV)
class Foo
  def foo
    p self #=> #<Object:0x0000558801e588a8>
  end
end


module M
  def foo
    x = Object.new
    x.instance_eval do
      super
    end
  end
end


class Bar < Foo
  include M
end


Bar.new.foo


# If it is "super"ed not via a Module, it raises an exception
class Foo
  def foo
    p self
  end
end


class Bar < Foo
  def foo
    x = Object.new
    x.instance_eval do
      super #=> self has wrong type to call super in this context: Object (expected Bar)
    end
  end
end


Bar.new.foo


matz: Bug. Need to fix. But I’m a bit afraid if the patch adds a new field. I accept if adding a field is really needed. nobu, could you review Jeremy’s patch?
[Feature #16150] Add a way to request a frozen string from to_s (eregon)
Can I commit https://github.com/ruby/ruby/pull/2437 (make Symbol#to_s frozen)? It seems compatible enough. What about making true/false/nil#to_s return a frozen cached String?
Discussion:
p :foo.to_s.frozen? #=> actual: false, expected: true


s = ""
p s.to_s.equal?(s) #=> true, so it is considered harmful to update a return value of to_s


naruse: I bet that it will bring incompatibility, but I agree.
akr: Me too.
matz: Let’s try.
[Bug #16154] lib/delegate.rb issues keyword argument warnings (mame)
Module#pass_positional_hash for disabling false positive warnings in delegation. This is just for 2.7 migration path. Can we commit this?
The latest proposal is Module#pass_keywords which is for a migration path for Ruby 2.6 to 3.0.
Discussion:
# perfectly (syntactically) compatible delegation between 2.6 to 3.0
pass_keywords def foo(*args, &block)
  bar(*args, &block)
end


akr: I’m not positive. A Hash flag seems better approach.
akr: Is it really needed to allow one code that work on all 2.6 to 3.0?
a_matsuda: It is needed for Rails.
naruse: If keyword argument separation is postponed to 3.2, it might be easy to migrate because 2.6 is EOL.
mame: I personally like pass_keywords because it is explicit and easy to add. A Hash flag is too implicit and difficult to understand and control. Keeping it so long requires people to understand it.
a_matsuda: I’m already trying pass_keywords in my branch of Rails, and it seems to work gracefully, as far as I see.
ko1: The name pass_keywords sounds normal attribute, so we will never remove it even in far future.
matz (or someone): How about ruby2_keywords?
ko1: It is very clear that it is for migration path. Sounds good.
mame: How about trying ruby2_keywords in Ruby 2.7-preview 2 and checking it out?
matz: … Okay.
[Misc #16124] Let the transient heap belong to objspace (methodmissing)
As per PR and outline of pros and cons in the issue, I think it’s stable and performance enough to be coupled a little tighter to objspace instead of just being a global slab / scratch space. Thoughts?
I volunteer to do the work, if it makes sense.
Also VM -> global method cache for example - cleaner for multi-VM, but not sure of the future of MVM
Discussion:
in short: refactoring?
ko1: a bit afraid if it makes it slow because of indirect access.
nobu: need to experiment.
[Misc #16160] Lazy init thread local storage (methodmissing)
Storage for Fiber local storage of execution context for Thread#[] and Thread#[]= APIs are lazy initialized.
I think the same pattern should be applied to real thread local storage as well.
Discussion:
in short: nobu should know
ko1: looks good.
nobu: need to experiment.
[Feature #16131] Remove $SAFE, taint and trust (alanwu)
What do you think about the proposed schedule?
I am asking because this might inform the fix for [Bug #16151]
Discussion:
proposed schedule (by Jeremy Evans):
2.7:
Remove taint tracking/mechanism.
Non-verbose warning on setting/access of $SAFE
taint/trust/untaint/untrust become no-ops, verbose warning when called
3.0:
No warning on setting/access of $SAFE, it switches to normal global variable.
3.2:
taint/trust/untaint/untrust non-verbose warning when called
3.3:
taint/trust/untaint/untrust removed
ko1 (or someone): taint etc. may be removed earlier than 3.3?
matz: Okay, almost accepted.
[Feature #16123] Allow calling a private method with self. (alanwu)
Pinging because of inactivity. I think it makes sense.
Patches that would benefit from reviews:
Discussion:
in short: allow self.private_method
knu: This enables calling a private method with a reserved name (do, etc.) by prefixing self..
mame: Is it OK to allow calling Kernel#require with self.require?
soutaro: I have filed the same proposal and it is already accepted (#11297).
soutaro: The situation I faced was that some coworker tends to write self.foo to represent it is a method call, but we cannot make the method private.
soutaro: But I heard from matz that Ruby’s private is not for visibility, but for restriction of method call style (no receiver is allowed). I was satisfied for the explanation, so I left the ticket.
matz: Okay, I accept it again.
[Bug #16121] Stop making a redundant hash copy in Hash#dup (alanwu)
Discussion:
in short: review it
mame: I have never seen the patch but the idea looks good to me (maybe?)
mame: Oops, it removes some branches. Need to review them carefully.
ko1: Will do.
[Feature #16163] Reduce the output of RubyVM::InstructionSequence#to_binary (alanwu)
Discussion:
in short: ko1 knows
(There is no objection but discussed how to merge)
nobu: The PR includes some intermediate commits like “Refactoring”.
ko1: I have a question about git. In such a situation, how can we merge it? Ask the author to rebase -i? or May I squash?
naruse: For this paticular ticket, ko1 can handle it as you like.
ko1: The PR changes only 2000 lines even by squashing. Not so big. Okay I’ll squash,
[Bug #16119] Optimize Array#flatten and flatten! for already flattened arrays (alanwu)
Discussion:
in short: review it
mame: I have never seen the patch but the idea looks good to me
nobu: Will review.
[Feature #16155] Add an Array#intersection method (alanwu)
Is this an acceptable method name?
Should this be a simple alias for Array#&, or should it accept multiple arrays as arguments, like difference and union do?
Discussion:
in short: Array#intersection
matz: accepted.
[Feature #16168] Add keyword argument separation to C functions using rb_scan_args (jeremyevans0)
I would like to commit the patch so that methods implemented in C handle keyword argument separation similar to methods implemented in Ruby.
Discussion:
in short:
Make rb_scan_args do the same as Ruby methods
Add “k”, “e”, and “n” for rb_scan_args
IO.foreach(..., open_args: array) applies rb_scan_args to the array!
matz: Why is rb_scan_args(":") treated as def foo(opt = {})? If there is no special reason, I’d like to handle it as def foo(**opt)
matz: I’m not positive for adding “k”, “e”, and “n” flags. If they are really needed, how about providing them as separated functions like rb_scan_args_kw?
[Feature #16170] Remove the unmaintained libraries from Ruby 2.7
We should remove them for reducing security and mantainance cost:
cmath
mutex_m
scanf
shell
sync
thwait
tracer
Discussion:
a_matsuda: mutex_m is used by concurrent-ruby
someone: scanf is sometimes used
aycabta: irb uses tracer and e2mmap, but I’m okay for the removal. I’ll rewrite irb if needed
hsbt: okay, we first remove cmath, scanf, shell, sync, and thwait
[Feature #16120] Omitted block argument if block starts with dot-method call (Dan0042)
posts.map{ .author.name } has beautiful readability, no incompatibility, changes a syntax error into an intuitive idiom, already implemented in 6 lines by nobu. 本当に美しいと思います。
Discussion:
matz: I prefer this style to .map(&:to_s). But I understand it is not flexible enough. Difficult to determine.
mame: If this style is accepted, Symbol#to_proc is deprecated? It sounds good.
matz: Sounds like a plan.
someone: Can we write 1.times { .foo; any-statement }?
matz: No
nobu: It is difficult to implement… Currently my patch even allows 1.times { .foo(.bar) }
matz: No
matz: Give me time to consider it.
[Feature #16153] eventually_frozen flag to gradually phase-in frozen strings
This would make previous backward-incompatible proposals (#16150, #15836, #11473) possible with a backward-compatible transition.
Discussion:
in short: pseudo frozen flag that allow mutation with a warning, for providing a migration path to true frozen
durest: eventual: false looks “never frozen”. immediately: true looks better.
mame: Agreed.
mame: This proposal looks feasible, but there are some points. (1) Ruby will become immutable language? (2) Is this worthwhile to use one bit for every object?
mame: I’m a bit against (1) because it kills Ruby’s most important property: dynamics. If many objects are frozen, it may prevent monkey-patching or what not.
naruse (or someone): I’m skeptical if immutability makes Ruby faster. Frozen string literal was for stopping many PRs just to add .freeze for every string literal blindly, not for making Ruby faster. Actually, I have never seen report that frozen string literal made a real-world program (not a micro benchmark) significantly faster.
matz: Let me consider it.
[Feature #16021] floor/ceil/round/truncate should accept a :step argument
Since Time#floor/ceil methods were added, it would be convenient to round to the previous/next minute/hour: Time.now.ceil(step:60). Also add to Numeric, for convenience and consistency.
If this is a good idea I would like to try writing it
Discussion:
step: or by: or to:
12.3456.floor(step: 0.2) #=> 12.2
12.3456.round(step: 0.2) #=> 12.4
12.3456.floor(step: 0.2) #=> 12.4
12.3456.floor(step: 0.2) #=> 12.2


akr: For Time#floor, it cannot handle leap second well.



