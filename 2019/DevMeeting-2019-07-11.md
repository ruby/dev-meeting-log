---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-07-11

Please comment on your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of the ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/07/11 (Thu)
Time: 13:00-17:00 (JST)
Place and Sign-up: https://ruby.connpass.com/event/135823/
log: https://docs.google.com/document/d/1K61SGIwp8_rNsPyhmayUcERu71vt_etDjXdhqrLmBVY/edit#

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

We don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

`if cond1 ... cond2`DevelopersMeeting20190711Japan
https://bugs.ruby-lang.org/issues/15930
Next Date
8/20 (Tue) 13:00-17:00 @ pixiv
Ann
7/14, 15 Ruby Development camp @ https://marumo.net/gasshuku-plan001/
About 2.7 timeframe
Preview 2 Summer at earliest?
Check security tickets
talked about some matters of concernce
Topics
[Feature #14912] Introduce pattern matching syntax (pitr.ch)
Could the pattern matching be made available as a first-class citizen to be used as a filter when searching in data structures and to be able to implement Actor receive method
Consider contractions log_messages.find in [:error, message] { puts message }
Non-symbol matching of Hashes would be very desirable
Elaborated in https://bugs.ruby-lang.org/issues/14912#note-22
matz: I don’t want all proposals (1)…(3)
[Feature #15797] Use realpath(3) instead of custom realpath implementation if available (jeremyevans0)
Is this OK to commit? It may cause regressions on less common Unix not tested by Travis (e.g. FreeBSD, NetBSD, AIX, Solaris), though we could just fallback to current implementation in that case.
Do we want to add workarounds for Mac OS <=10.5 (10.5 was released in October 2007)?
akr: looks good.
[Feature #15903] Move RubyVM.resolve_feature_path to Kernel.resolve_feature_path (eregon)
Could you decide between Kernel.resolve_feature_path and $LOAD_PATH.resolve_feature_path?
matz: I pick up $LOAD_PATH.resolve_feature_path. We need to improve the documentation issue in future.
[Feature #15897] it as a default block parameter and [Misc #15723] Reconsider numbered parameters (eregon)
Could committers continue the discussion from last meeting? Are most people OK with just one implicit argument vs many? I think that could be the first thing to settle.
I think a comment from matz sharing his thoughts on either ticket would be helpful.
matz: We have two options. Many people says one whole parameter is enough, but a few (who likes math?) says they want multiple parameters. I can’t decide it yet…
matz: If we pick up the first option, I think it is the best. But some RSpec people concerns it. So my second favorite is _.
usa: _ is often used as “not used” arguments.
knu: Existing ignored parameters can be changed to _key. Giving a name is generally a good practice even if it’s not used.
matz: Clojure uses %1. Currently %1 may be used in Ruby?
matz: I’ll comment my current opinion to that ticket.
martin: I vote for multiple2.
single
it
_
%0?
@
multiple
@1, @2
%1, %2
:1, :2
[Feature #5400] Remove flip-flops in 2.0 (nobu)
Strong objections. Way too hard to rewrite them.
matz: Okay revert the decision.
[Feature #15950] Allow negative length in Array#[], Array#[]=, Array#slice, Array#slice!, String#[], String#[]=, String#slice, String#slice! (sawa)
knu, usa: It brings burden for brain. Especially when the use case is unclear and it’d be rare you’d see it.
matz: Umm…
akr: "abcdefgh"[1, -3] #=> "ab" and "abcdefgh"[0, -3] #=> "fgh" looks confusing
a = list(range(0,10))
# 7番目から始点までの要素を-2個ごとに取り出す
a[7::-2] # [7, 5, 3, 1]
# 終点から5番目までの要素を-2個ごとに取り出す
a[:4:-2] # [9, 7, 5]
# 8番目から3番目までの要素を-2個ごとに取り出す
a[8:2:-2] # [8, 6, 4]
# -2番目から-7番目までの要素を-3個ごとに取り出す
a[-2:-8:-3] # [8, 5]


https://qiita.com/okkn/items/54e81346d8f35733ab5e
[Feature #15958] Time#inspect with frac (naruse)
akr: It looks good.
mame: I’m a bit afraid about the compatiblity but it would be okay because it is a #inspect method.
matz: Try it.
"2019-07-11 14:38:54.123456789 +0900"
"2019-07-11 14:38:54 (+1/3) UTC" (not so concrete idea)
[Feature #14385]: Deprecate back-tick for Ruby 3. (znz)
related enforce %x(style invocation) because backticks `` are deprecated
matz: I cancelled this breaking decision (at least in Ruby 3).
[Feature #11808] Different behavior between Enumerable#grep and Array#grep (nobu)
It needs a new C-API.
(long discussion under assumption that there is “Array#grep”…)
mame: There is no definition of “Array#grep”.
ko1: I got it. This is a spec of the VM. VM attempts to find the first Ruby frame and write $1 in the frame. In this case, Test#each is the frame, so it works as follows:
matz: looks a bug. ko1, could you please fix it if possible in future?
(long discussion about the implementation detail…)
ko1: I’ll give it a try.
class Test
  include Enumerable
  def each
    return enum_for(:each) unless block_given?
    yield "Hello"
    p $1 #=> "H"
    yield "World"
    p $1 #=> "W"
  end
end


enum = Test.new
enum.grep(/(.)/) {}


[Feature #15966] Introducing experimental features behind a flag, disabled by default (eregon)
Could you read the description and reply what you think? Do you think it’s a good idea? I think it would be a much better way to introduce experimental features.
matz: I’m not positive for the flag, I’ll write a comment.
matz: How about holding an online (English?) meeting? Text-based one is preferable.
matz: How about updating the “description” of each ticket? It would be helpful for us to grasp the current/latest proposal. (A casual version of PEP?)
[Feature #15940] Coerce symbols internal fstrings in UTF8 rather than ASCII to better share memory with string literals (byroot)
Saves some resident memory
Makes symbols & constant names defined in UTF-8 files UTF-8 encoded, which is much less surprising.
Little to no backward compatibility concerns.
(Failed to write a log… Very subtle and difficult discussion for me) (mame)
[Feature #15939] Dump symbols reference to their fstr in ObjectSpace.dump() (byroot)
It’s important for the heap dump consistency. Otherwise when you build the reference graph you end up with some dangling objects.
[Feature #15973] Make Kernel#lambda always return lambda (nobu)
ko1: How about this semantics?
matz: It might be good if it is possible… maybe? ko1, could you try to implement it?
b = proc {|x| x }
lambda(&b) #=>
lambda {|x| b[x] }


b = proc {|x, y, k:1| x }
lambda(&b) #=>
lambda {|x, y, k:1| b[x, y, k:k] }


[Feature #15631] Let round_capa for ID table not allocate excess capacity for power of 2 ints >= 4 (methodmissing)
PR and further due diligence comments in https://github.com/ruby/ruby/pull/2278
ko1: fannyfalcon should review this.
[Feature #15987] Let boolean option (such as exception in Kernel#Complex, Kernel#Float, Kernel#Integer, Kernel#Rational) be falsy vs. truthy (eregon)
Does this issue need matz approval or can it be fixed directly? If it does, does matz agree we should address this and use RTEST()?
naruse: In general a change which is for Core libraries and visible from Ruby code requires Matz’s approval.
[Bug #10463] :~@ and :!@ are not parsed correctly (jeremyevans0)
Can we deprecate the automatic conversion of ~@ to ~ and !@ to ! in method names and symbols?
matz: + is binary and +@ is unary. But ! and !@ are both unary. So the current behavior is reasonable.
[Feature #15865] <expr> in <pattern> expression (mame)
We have some opinions: scoping, matching strictness, the keyword in, and the word order. But all are not specific to the one-line matching. I think it is acceptable if case/in matching is acceptable. May I experimentally commit it as well as case/in?
matz: go ahead (experimentally).




