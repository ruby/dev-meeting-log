---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-08-09

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

- Date: 2018/08/09 (Thu)
- Time: 14:00-18:00 (JST)
- Place: pixiv Inc. (Tokyo, Japan)
- Sign-up: https://ruby.connpass.com/event/95321/
- Past meetings: <https://bugs.ruby-lang.org/projects/ruby/wiki#Developer-Meetings>

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.6 timeframe

## Carry-over from previous meeting(s)

- [Bug #14887] Array#delete_if does not use #delete (shyouhei)
    - Is it by design, or a bug?
- [Feature #13050] Readline: expose rl_completion_quote_character variable (nobu)
- [Feature #14850] Add official API for setting timezone on Time (nobu)
- [Feature #14869] Proposal to add Hash#=== (nobu)
- [Feature #14916] Proposal to add Array#=== (aycabta)
- [Feature #14877] Calculate age in Date class (nobu)
- [Bug #14908] Enumerator::Lazy creates unnecessary Array objects. (nobu)

## From Attendees

(will be edited later)
(if you have a write access permission, please list directly)

- [Feature #5446] at_fork callback API (eregon) Can we get approval for introducing this? It is solving a real-world issue, in a more reliable way than the various hacks in most Ruby servers.
- [Feature #11258] add 'x' mode character for O_EXCL (kazu)
- [Misc #14907] io.c: do not close inherited FDs by default (akr)
- [Feature #14973] Proposal of percent literal to expand Hash (aycabta)

## From non-attendees

- [Feature #14717] thread: allow disabling preempt (normalperson)
- [Feature #11076] Enumerable method count_by (baweaver)
    As mentioned in the discussion, Nobu was kind enough to update this to work with current master for Ruby:     https://github.com/ruby/ruby/compare/trunk...nobu:feature/11076-Enumerable%23count_by

    As the code is already written I believe it would be an ideal target for 2.6 as a quick win on an often requested feature.
* [Feature #14954] Add :wait option to RubyVM::MJIT.pause (k0kubun)
  * Since it's not released even in preview2 yet, is it okay to change the behavior?
  * Is there any objection for having the option or the option name?
- (baweaver)
  * [Feature #14869] Proposal to add Hash#=== (nobu)
  * [Feature #14916] Proposal to add Array#=== (aycabta)
  * [Feature #14912] Introduce pattern matching syntax

    I believe that these three items are related, and personally I could do a lot of very interesting things with `Array#===` and `Hash#===` that would get us exceptionally close to features discussed in  [Feature #14709]

    This to say I would second the inclusion of the first two items as they relate to the third and fourth. It would give Ruby an exceptional amount of power.

- [Feature #14759] [PATCH] set M_ARENA_MAX for glibc malloc (sam.saffron)
    I would also very much like to see it backported 2.4 and 2.5. It is of enormous value to the ecosystem
- [Feature #14736] Thread selector for flexible cooperative fiber based concurrency (ioquatix)
- [Feature #14739] Improve fiber yield/resume performance  (ioquatix)
* [Bug #14968] io.c: make all pipes nonblocking by default (normalperson)
*  [Misc #14937] timer-thread elimination depends on [Bug #14968] (normalperson)

- [Feature #14944] Support optional inherit argument for Module#method_defined?  (jeremyevans0)

(will be edited later)
(if you have a write access, please list directly)

# Log

## Next dev-meeting

- 9/13

- @ Cookpad

## About 2.6 timeframe

- Naruse: nothing to do right now.
- Mame: feature freeze?
- Naruse: expected in Oct. maybe.

## Carry-over from previous meeting(s)

- [[Bug #14887]](https://bugs.ruby-lang.org/issues/14887) Array#delete\_if does not use #delete (shyouhei)

- Is it by design, or a bug?
- Matz: delete is memory inefficient.
- Mame: if you can override #delete, why not also #delete\_if.

- [[Feature #13050]](https://bugs.ruby-lang.org/issues/13050) Readline: expose rl\_completion\_quote\_character variable (nobu)

- Go!!!

## From Attendees

- [[Feature #5446]](https://bugs.ruby-lang.org/issues/5446) at\_fork callback API (eregon) Can we get approval for introducing this? It is solving a real-world issue, in a more reliable way than the various hacks in most Ruby servers.

- Mrkn: I wanted to use this in pycall to call PyOS\_AfterFork\_Child, and so on from C-layer, but I noticed that I can use pthread\_atfork directly.
- Naruse: Ruby’s fork is unsafe operation. Application programmers should understand the fact and carefully handle related operations (for example freeing resources).
- Akr: I guess at\_fork hook would be problematic with multi-thread and/or multi-guild.  But making it Ruby’s standard feature impress this feature usable universally including multi-thread/guild. So, I’m negative for this proposal.
- usa: Furthermore, I consider that fork must be destroyed.

- [[Feature #11258]](https://bugs.ruby-lang.org/issues/11258) add 'x' mode character for O\_EXCL (kazu)

- Matz accepted already.

- [[Misc #14907]](https://bugs.ruby-lang.org/issues/14907) io.c: do not close inherited FDs by default (akr)

- Nobody is against changing defaults

- [[Feature #14850]](https://bugs.ruby-lang.org/issues/14850) Add official API for setting timezone on Time (nobu)

- Akr: localtime/timelocal analogous variant methods should suffice

- Timezone object shall define localtime/timelocal/gmtime/timegm

- Matz: is there any reason we need this?
- Matz: What is actual API?

- [[Feature #14869]](https://bugs.ruby-lang.org/issues/14869) Proposal to add Hash#=== (nobu)

- After \[Feature #14912\]

- [[Feature #14877]](https://bugs.ruby-lang.org/issues/14877) Calculate age in Date class (nobu)

- Matz: I don’t like the idea to implicitly use today.

- [[Feature #14973]](https://bugs.ruby-lang.org/issues/14973) Proposal of percent literal to expand Hash (aycabta)

- Matz: percent literal...

- [[Feature #13618]](https://bugs.ruby-lang.org/issues/13618) \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (ko1)

- Naming.

- X IoThread.new{}
- X GreenThread.new{}
- X Thread::Green.new{} #124 “most likely candidates”(vote: hsbt)
- X Thread.green{} #124 “most likely candidates”
- X Thread.create(scheduler: …, args: ...) #124 “most likely candidates”
- Thread::Coop.new{}
- Thread::Cooperative.new{} # (usa)
- Thread::Nonpreemptive.new{} # (usa; these 2 names are very long, then they are good ^^)
- NonpreemptiveThread.new {} # (mrkn)
- X NPThread.new {} # (mrkn)
- X Thread::Light (ko1)

- Matz: “Thread.green” and “Thread.create” are bad because we should distinguish auto fiber and Thread.
- Matz: “Thread::Green” is bad because it means implementation.

## From non-attendees

- [[Feature #14717]](https://bugs.ruby-lang.org/issues/14717) thread: allow disabling preempt (normalperson)

- Closed already

- [[Feature #11076]](https://bugs.ruby-lang.org/issues/11076) Enumerable method count\_by (baweaver)
    As mentioned in the discussion, Nobu was kind enough to update this to work with current master for Ruby: [https://github.com/ruby/ruby/compare/trunk...nobu:feature/11076-Enumerable%23count\_by](https://github.com/ruby/ruby/compare/trunk...nobu:feature/11076-Enumerable%23count_by)
    As the code is already written I believe it would be an ideal target for 2.6 as a quick win on an often requested feature.

- Matz: I thought #count\_by would be something different.
- Mame: There is #count. #count\_by is very different from that.
- K-tsj: Also, #count takes a block.
- Matz: I’m not against the feature itself.  Also, do we need a block-less counterpart?
- Naruse: It looks similar to [Presto’s histogram function](https://prestodb.io/docs/current/functions/aggregate.html#histogram)

- [[Feature #14954]](https://bugs.ruby-lang.org/issues/14954) Add :wait option to RubyVM::MJIT.pause (k0kubun)

- Since it's not released even in preview2 yet, is it okay to change the behavior?
- Is there any objection for having the option or the option name?

- Not. At. All.
- Usa: Don’t ask for permission, beg for forgiveness.
- Naruse: RubyVM::MJIT is your namespace.

- (baweaver)

- [[Feature #14869]](https://bugs.ruby-lang.org/issues/14869) Proposal to add Hash#=== (nobu)
- [[Feature #14916]](https://bugs.ruby-lang.org/issues/14916) Proposal to add Array#=== (aycabta)
- [[Feature #14912]](https://bugs.ruby-lang.org/issues/14912) Introduce pattern matching syntax
    I believe that these three items are related, and personally I could do a lot of very interesting things with Array#=== and Hash#=== that would get us exceptionally close to features discussed in [[Feature #14709]](https://bugs.ruby-lang.org/issues/14709)
    This to say I would second the inclusion of the first two items as they relate to the third and fourth. It would give Ruby an exceptional amount of power.
- Matz: we should discuss these after pattern match is introduced.

- [[Feature #14759]](https://bugs.ruby-lang.org/issues/14759) \[PATCH\] set M\_ARENA\_MAX for glibc malloc (sam.saffron)
    I would also very much like to see it backported 2.4 and 2.5. It is of enormous value to the ecosystem

- Usa: The decision should be made by Linux port maintainer
- Naruse: up to kosaki.
- Mame: Once kosaki said “If we merge this, we should decide when we remove this”.

- [[Feature #14944]](https://bugs.ruby-lang.org/issues/14944) Support optional inherit argument for Module#method\_defined? (jeremyevans0)

- Usa: I want this too.
- Mame: why?
- Usa: maybe when I want to develop.
- Matz: not that negative.  Rather I think it’s nice to have.

- \[Feature #14975\] String#append without changing receiver's encoding (ioquatix)

- Naruse: I think it’s OK to have this kind of operation(s), but not for #<<

---

Carry over:

- [[Bug #14908]](https://bugs.ruby-lang.org/issues/14908) Enumerator::Lazy creates unnecessary Array objects. (nobu)
- [[Feature #14736]](https://bugs.ruby-lang.org/issues/14736) Thread selector for flexible cooperative fiber based concurrency (ioquatix)
- [[Feature #14739]](https://bugs.ruby-lang.org/issues/14739) Improve fiber yield/resume performance (ioquatix)

- Ko1: will discuss them in person

- [[Bug #14968]](https://bugs.ruby-lang.org/issues/14968) io.c: make all pipes nonblocking by default (normalperson)
- [[Misc #14937]](https://bugs.ruby-lang.org/issues/14937) timer-thread elimination depends on [Bug #14968](https://bugs.ruby-lang.org/issues/normalperson)
