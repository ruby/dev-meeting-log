---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2020-03-16

# The next dev meeting

**Date: 2020/03/16 13:00-17:00**

- Dev meeting *IS NOT* a decision-making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about the discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place are scheduled according to when/where we can reserve Matz's time.
- *DO NOT* discuss then on this ticket, please.

# Call for agenda items

If you have a ticket that you want matz and committers to discuss, please post it into this ticket in the following format:

```
* [Ticket ref] Ticket title (your name)
  * Comment (A summary of the ticket, why you put this ticket here, what point should be discussed, etc.)
```

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss this feature.
```

- Comment deadline: 2020/03/09 (one week before the meeting)
- The format is strict.  We'll use [this script to automatically create an markdown-style agenda](https://gist.github.com/mame/b0390509ce1491b43610b9ebb665eb86).  We may ignore a comment that does not follow the format.
- Your comment is mandatory.  We cannot read all discussion of the ticket in a limited time.

# Log

https://bugs.ruby-lang.org/issues/16661
Venue
3/16 (Mon) 13:00-17:00 @ online
Attendees
Add your name (or ask a committer to invite you)
matz (remote)
mame
duerst (remote)
Next Date
4/10 (Fri) 13:00-17:00 @ online
Announce
Ruby 2.7 timeframe
Release 2.7.1 in March.
About 2.8/3.0 timeframe
No topic
To move to 3.0, anything new is wanted
Check security tickets
[secret]
[Bug #16175] Object#clone(freeze: true) is inconsistent with Object#clone(freeze: false) (jeremyevans0)
Do we want Object#clone(freeze: true) to return a frozen clone if receiver is unfrozen?
Discussion:
h = {}.freeze
h.clone.frozen?                 # => true
h.clone(freeze: false).frozen?  # => false


h = {}
h.frozen?                       # => false
h.clone.frozen?                 # => false
h.clone(freeze: true).frozen?   # => false -- I expected true here!


freeze: false was introduced to get a cloned object with no freeze state copied.
Currently, it is just a problem of consistency; there is no real problem shown.
matz: So there is three way to clone:
clone(freeze: true) always freeze the result
clone(freeze: false) always does not freeze the result
clone() keeps the state of the original object
akr: If this is accepted, clone(freeze: nil) should keep the frozen state
matz: Don’t see an immediate reason to introduce freeze: nil
Conclusion:
clone(freeze: true) is accepted
[Feature #15357] Proc#parameters returns incomplete type information (jeremyevans0)
Can we add a :lambda keyword argument to Proc#parameters using the patch, returning results as if the proc was a lambda proc?
Discussion:
pr = proc{|a,b=2| [a,b] }
pr.parameters  # => [[:opt, :a], [:opt, :b]]
pr.call(:a)    # => [:a, 2]  # :a is interpret as the first parameter


pr = proc{|a=1,b| [a,b] }
pr.parameters  # => [[:opt, :a], [:opt, :b]]
pr.call(:a)    # => [1, :a]  # :a is interpret as the second parameter


Currently, we cannot distinguish them. By adding lambda: true keyword, we can distinguish them
pr = proc{|a,b=2| [a,b] }
pr.parameters(lambda: true)  # => [[:req, :a], [:opt, :b]]


pr = proc{|a=1,b| [a,b] }
pr.parameters(lambda: true)  # => [[:opt, :a], [:req, :b]]


Conclusion:
matz: let me consider for a while.
[Bug #16677] Negative integer powered (**) to a float number results in a complex (alanwu)
Currently -2 ** 2 is parsed as -(2 ** 2) but -2.itself ** 2 is parsed as ((-2).itself) ** 2 which seems inconsistent to me
Since (-2 ** 2) == (-(2 ** 2)), I expected (-2.itself ** 2) == (-(2.itself ** 2)) but that is not the case
The parsing rules around whether - is part of a literal or a use of the unary minus operator seems weird
Curious what others think about this
Discussion:
Already closed by matz.
Conclusion:
matz: no change to keep the compatibility
[Bug #16689] [BUG] try to mark T_NONE object (byroot)
Cause Ruby to crash when the heap gets past a certain size.
Looks like a bug in GC
This is a total blocker for us to upgrade to 2.7.x
Discussion:
alanwu’s patch: https://github.com/ruby/ruby/pull/2964
Conclusion:
ko1: merged.
[Bug #16682] Ruby 2.7.0p0 crash on exit if there is an active RUBY_INTERNAL_EVENT_GC_EXIT tracepoint (byroot)
Can more easily be worked around.
alanwu has a patch for it https://github.com/ruby/ruby/pull/2959
Discussion:
Already closed per OP’s request.
Conclusion:
ko1: SEGV was fixed by #16689, but there is another issue that all TracePoints are not closed before exit
ko1: will review the patch with nobu
[Bug #16497] StringIO#internal_encoding is broken (more severely in 2.7) (zverok)
This bug cause a lot of backward compatibility issues for people upgrading to 2.7
I might have a patch for it: https://github.com/ruby/ruby/pull/2960
Discussion:
Already merged by naruse.
Conclusion:
nobu: there is another issue remained. I will fix it
naruse: please reopen the ticket and set backport. I’ll backport.
[Bug #16466] *args -> *args delegation should be warned when the last hash has a ruby2_keywords flag (eregon)
Since it seems #16463 will be rejected, it’s needed to fix this to not make migration to Ruby 3 worse.
Discussion:
def baz(**kw)
end


def bar(*args)
  baz(*args)
end


ruby2_keywords def foo(*args)
  bar(*args)
end


foo(k: 1)


bar should be marked as ruby2_keywords, but no warning is printed curently
Conclusion:
matz: Very tough decision, but I want to keep the compatibility as-is.




[Feature #12654] On Windows use UTF-8 as filesystem encoding (shyouhei)
shyouhei: What’s the status?
usa: Will do at Ruby 3.0. Aim: June
[Bug #12368] default encoding of Integer#chr (mame)
naruse: see no actual use case, lets close
[Bug #12392] configure --with-sitedir=no --with-sitearchdir=no --with-vendordir=no --with-vendorarchdir=no が機能しない (mame)
nobu: will do.
[Bug #12416] struct rb_id_table lacks mark function (mame)
shyouhei: shiozuke
[Bug #12436] newline argument of File.open seems not respected on Windows (mame)
akr: on Windows, text mode ignores newline: :lf
usa: no one will use this mode (text mode is used only on windows), so let’s raise an exception?
nobu: will do
[Bug #12485] Kernel.Rational raises TypeError though given denominator returns 1 by to_int (mame)
[Bug #12540] test failures when SHARABLE_MIDDLE_SUBSTRING=1 (mame)
ko1: let’s remove SHARABLE_MIDDLE_SUBSTRING
[Bug #12547] Remove ONIG_UNICODE_VERSION_… in enc/unicode/case-folding.rb, casefold.h (mame)
naruse: looks good
nobu: will do
[Bug #12548] Rounding modes inconsistency between round versus sprintf (mame)
naruse: don’t want to fork BSD sprintf implementation
[Bug #12551] Exception accessing file with long path on windows (mame)
usa: need to research Python
[Bug #12599] For CLang, increase inline-threshold to get 7%-10% speedup of optcarrot (mame)
naruse: reject?
[Bug #12666] Fatal error: glibc detected an invalid stdio handle (mame)
shyouhei: will close
[Bug #12671] Hash#to_proc result is not a lambda, but enforces arity (mame)
matz: lets make it lambda
[Bug #12689] Thread isolation of $~ and $_ (mame)
ko1: will consider
[Bug #12706] Hash#each yields inconsistent number of args
(mame)
{ a: 1 }.each do |ary|
  ary == [:a, 1]
end

{a:1}.each {|a,b| p [a,b] }

{a:1}.each(&->(a,b){p [a,b]})    #=> [:a, 1]  (should raise an exception, but incompatible)
{a:1}.each(&->(a,b=42){p [a,b]}) #=> [[:a, 1], 42]

def foo; yield :a, 1; end
foo(&->(a,b){p [a,b]})   #=> [:a, 1]
foo(&->(a,b=1){p [a,b]}) #=> [:a, 1]

def foo; yield [:a, 1]; end
foo(&->(a,b){p [a,b]})   #=> error
foo(&->(a,b=1){p [a,b]}) #=> [[:a, 1], 1]

matz: I want to give it a try (incompatibility)
[Bug #12780] BigDecimal#round returns different types depending on argument (mame)
matz: I like it to return an Integer.  Will add a comment.

