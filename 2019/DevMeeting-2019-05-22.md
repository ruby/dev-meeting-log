---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-05-22

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/05/22 (Wed)
Time: 13:00-17:00 (JST)
Place: Speee Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/129507/

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.7 timeframe

## Check security tickets

## Carry-over from previous meeting(s)

## From Attendees

## From non-attendees

----

Please comment your favorite ticket we need to discuss with *the following format*.

```
* [Ticket ref] Ticket title (your name)
  * your comment why you want to put this ticket here if you want to add.
```

Your comment is very important if you are no attendee because we can not ask why you want to discuss about it.

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss about this feature.
```

We don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

[https://bugs.ruby-lang.org/issues/15782](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15782&sa=D&source=editors&ust=1686088829068902&usg=AOvVaw2lvoSocyt9eVFzKGi-1P5J)

## Next Date

6/13 (Thu) 14:00-17:30 @ Speee

## Ann

- 7/14, 15 Ruby Development camp @ [https://marumo.net/gasshuku-plan001/](https://www.google.com/url?q=https://marumo.net/gasshuku-plan001/&sa=D&source=editors&ust=1686088829069631&usg=AOvVaw1vQovSxi2MlSlc1sH5dyJ5)

## About 2.7 timeframe

- Preview 1 soon? (in this week)

- Waiting for Reline

- Preview 2

- no plan

## Check security tickets

Nothing.

## Topics

- [\[Misc #14632\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14632&sa=D&source=editors&ust=1686088829070677&usg=AOvVaw2MOmyhtFrdaZtYi9we0R-i) [git.ruby-lang.org](https://www.google.com/url?q=http://git.ruby-lang.org/&sa=D&source=editors&ust=1686088829070849&usg=AOvVaw3CMIaDTPqLLlZ0yDtY8mPp) (k0kubun)

- RUBY\_REVISION should be full-length hash value

- [\[Bug #15745\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15745&sa=D&source=editors&ust=1686088829071202&usg=AOvVaw2KZfJ56SugMvxuOObPmGuv) There is no symmetry in the beginless range and the endless range using Range#inspect (koic)(1..).inspect      #=> "1.."
- (..5).inspect      #=> "..5"
- (nil..nil).inspect #=> "nil..nil"

- [\[Feature #14915\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14915&sa=D&source=editors&ust=1686088829071759&usg=AOvVaw1IAFGrBYtrB492ZyTh6t00) Deprecate String#crypt (jeremyevans0)

- There are a few committers against the removal. They will reply into the ticket.
- Jeremy Evans committer? -> “Go ahead” by matz ([https://bugs.ruby-lang.org/issues/15853#change-78060](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15853%23change-78060&sa=D&source=editors&ust=1686088829072214&usg=AOvVaw3SLlgoL9nFTQBT14EAtoDc), [https://bugs.ruby-lang.org/issues/15839#change-78087](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15839%23change-78087&sa=D&source=editors&ust=1686088829072421&usg=AOvVaw1Ck6TK557TLQCVUaFmTVik))

- [\[Feature #15765\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15765&sa=D&source=editors&ust=1686088829072708&usg=AOvVaw3-AOZNlScJjLpo4-0oJEmX) \[PATCH\] Module#name without global constant search (alanwu)

- Matz: leaf to nobu.
- Nobu: Will check and merge.

- [\[Feature #15772\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15772&sa=D&source=editors&ust=1686088829073142&usg=AOvVaw1iy8TSQH65ZmGbJvgI2_G5) Proposal: Add Time#ceil (osyo)

- Shyouhei: Use case?
- Matz: Okay!

- [\[Feature #13645\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13645&sa=D&source=editors&ust=1686088829073566&usg=AOvVaw3GIBKneMrbgGB5S0dVo0Yz) Syntactic sugar for indexing when using the safe navigation operator (osyo)

- Matz: the use case looks artificial. Reject once.

- [\[Misc #15802\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15802&sa=D&source=editors&ust=1686088829073884&usg=AOvVaw1WO3Szw3TZQE-Ode5mu9Sv) \[PATCH\] Reduce the minimum string buffer size from 127 to 63 bytes (methodmissing)

- ko1 will check.

- [\[Feature #14736\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14736&sa=D&source=editors&ust=1686088829074211&usg=AOvVaw1LY62uznmL7v4NN5-RM4tf) Thread selector for flexible cooperative fiber based concurrency (ioquatix)

- (ko1, nobu, and matz talks)
- Matz: I’m worry about the performance, it would be difficult to accept the patch as is. I’m not positive to add a hook to IO because it will bring a big overhead when buffered. I prefer auto-fiber to using traditional IOs with a hook.
- Nobu: I’m negative to passing a FD. I agree with eregon.
- Matz: Will reject.
- Ko1: I’m unsure what design is best, but I think eregon wrote [a good comment](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14736%23change-77879&sa=D&source=editors&ust=1686088829074789&usg=AOvVaw3ebK71VLlVj-kdjSNIgOpI). It would be good that the proposal is much simpler than autofiber.

- [\[Feature #15323\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15323&sa=D&source=editors&ust=1686088829075042&usg=AOvVaw12jzyQ-4JxlKhN5Mt8wWlx) \[PATCH\] Proposal: Add Enumerable#filter\_map (greggzst)

- Matz will reply.

- [\[Misc #15843\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15843&sa=D&source=editors&ust=1686088829075413&usg=AOvVaw3wFHl7RYSEDjFNDLCUpu_8) Make “trunk” a symbolic-ref of “master” on [git.ruby-lang.org](https://www.google.com/url?q=http://git.ruby-lang.org/&sa=D&source=editors&ust=1686088829075617&usg=AOvVaw0thzU36T3m9jqdz2Lhwb2M) (k0kubun)

- Go ahead?
- Nobu: Why “master” is primary and “trunk” is secondary.
- Akr: “Out of scope (for a short term)” is a trap! “accept” may mean accept for long term?

- [\[Feature #15778\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15778&sa=D&source=editors&ust=1686088829076136&usg=AOvVaw37J8gLZH-TwPTSoIZHZ7d_) Expose an API to pry-open the stack frames in Ruby (eregon)

- Ko1: I hate Binding.of\_caller.
- Shyouhei: How about requiring require "something" to communicate “should only be used for debugging”?

- [\[Bug #7300\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7300&sa=D&source=editors&ust=1686088829076697&usg=AOvVaw0-4My0xywohL8QXLUUbhKj) Hash#\[\] の挙動が 1.9.3 と異なっている (hsbt)

- Mame: I’d like to merge it.

- [\[Feature #14844\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14844&sa=D&source=editors&ust=1686088829077078&usg=AOvVaw2tPsGcvMik_gOXnCBYKoN1) Future of RubyVM::AST?

- What can we do about this? The current situation is confusing for everyone, the API sounds “blessed” by being in core but it’s actually experimental and unstable. Can we document it as clearly as possible when this API should be used? Could we improve the API so it’s less fragile to parser changes and could be reasonably implemented on other Ruby implementations?
- “Add a document to warn the result of this API may (easily) change in future”

- [\[Feature #15863\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15863&sa=D&source=editors&ust=1686088829077592&usg=AOvVaw12CadwHth6JWaCZOJ8Hb25) Add Hash#slice! and ENV.slice!(bogdanvlviv)
- [\[Feature #15831\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15831&sa=D&source=editors&ust=1686088829077983&usg=AOvVaw2eVfg0ts-1ouKsFURw3k4d) Add Array#extract, Hash#extract, and ENV.extract (bogdanvlviv)

- Matz: negative. (Sorry I missed to log)

- [\[Feature #15864\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15864&sa=D&source=editors&ust=1686088829078493&usg=AOvVaw3xS7e62FVsmR3zW7jBZDgc) Proposal: Add methods to determine if it is an infinite range
    (osyo)

- Matz: negative.

- [\[Feature #15865\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15865&sa=D&source=editors&ust=1686088829078804&usg=AOvVaw3p_Fs5ZfWJtuYB6E7HiNvu) <expr> in <pattern> expression (mame)

- Matz: positive.

- [\[Feature #15281\]](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15281&sa=D&source=editors&ust=1686088829079237&usg=AOvVaw1bKEfmLlIRc8wm5D6vZopG) Speed up Set#intersect with size check. (tenderlove)

- Knu will check.
