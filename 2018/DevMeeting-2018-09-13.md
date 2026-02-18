---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-09-13

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2018/09/13 (Thu)
Time: 14:00-18:00 (JST)
Place: Cookpad Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/97843/
Past meetings: https://bugs.ruby-lang.org/projects/ruby/wiki#Developer-Meetings

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

## From Attendees

(will be edited later)
(if you have a write access permission, please list directly)

* [Misc #14956] Remove staled branches in svn repository (hsbt)
* [Feature #6823] Where/how should ruby-mode issues be reported? (kazu)

## From non-attendees

(will be edited later)
(if you have a write access, please list directly)

# Comment format

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

I don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

## Next dev-meeting

- 10/10

- @ pixiv

## About 2.6 timeframe

- Nothing.

## Carry-over from previous meeting(s)

## From Attendees

- \[Feature [#15022](https://bugs.ruby-lang.org/issues/15022)\] Oneshot coverage

- I'd like to introduce a new kind of coverage to record whether each line is executed or not. It provides less but still useful information compared to the traditional line coverage, and the measurement is quite faster.
- shyouhei: clear is not atomic, isn’t it?
- mame: oh, yes.  maybe I should add clear: true option to peek\_result.
- all: let’s go

- \[Feature [#8661](https://bugs.ruby-lang.org/issues/8661)\] Add option to print backtrace in reverse order(stack frames first & error last)

- The current backtrace order is really annoying, and I'm not still used; is there any chance to revert?
- ???: How about using pager?

- \[Feature [#14609](https://bugs.ruby-lang.org/issues/14609)\] \`Kernel#p\` without args shows the receiver (ko1)

- I feel this feature is very useful and some people say :+1: so let discuss about this feature.
- everyone except mame/ko1: not good
- nobu: ruby -ep gets broken
- nobu: how about Kernel#P ?
- knu: There’s a gem: [https://github.com/esminc/tapp](https://github.com/esminc/tapp)

- \[Misc [#14956](https://bugs.ruby-lang.org/issues/14956)\] Remove stale branches in svn repository (hsbt)

- go ahead.  not remove, but mv to tag

- \[Feature [#6823](https://bugs.ruby-lang.org/issues/6823)\] Where/how should ruby-mode issues be reported? (kazu)

- Remove ruby-mode.el from trunk, use emacs’s instead.
- Other \*.el’s upstream moves to github.

- \[Feature [#14183](https://bugs.ruby-lang.org/issues/14183)\] "Real" keyword argument

- I'd like to hear matz's current opinion and other committers' ones.

- \[Feature [#14850](https://bugs.ruby-lang.org/issues/14850)\] Add official API for setting timezone on Time (nobu)

- nobu: a patch posted.
- akr: it’s not good to use Time object as a container.  But, we can define the official interface as a Struct which has :year, :month, … members.  then, Time objects suit the interface accidentally :)

## From non-attendees

- \[Feature [#14975](https://bugs.ruby-lang.org/issues/14975)\] String#append without changing receiver's encoding (ioquatix)

- Can we review this PR? We need to decide functionality (i.e. not changing receivers encoding, rejecting encoding if it's not same, unless receiver is binary).
- Longer term, we should consider if << and concat should be modified. Changing receiver's encoding seems like a mistake.

- \[Feature [#14739](https://bugs.ruby-lang.org/issues/14739)\] Improve fiber yield/resume performance (ioquatix)

- Can I get commit access and can we agree to merge this change?

- \[Feature [#14888](https://bugs.ruby-lang.org/issues/14888)\] Add trace point for eval (and related functions) (ioquatix)

- Can we decide if this is acceptable so I can make PR?

- \[Feature [#14097](https://bugs.ruby-lang.org/issues/14097)\] Add union and difference methods to Array ([ana06 (Ana Maria Martinez Gomez)](https://bugs.ruby-lang.org/users/13437))

- Addition of two new methods in aim of readability, ease of use, efficiency and consistency. There are already PR for both methods.
- [matz (Yukihiro Matsumoto)](https://bugs.ruby-lang.org/users/13) told me to add this here ;) After my talk in EuRuKo and the feedback from the standing voting I hope it is clearer and easy to decide if this is wanted

- \[Bug [#14908](https://bugs.ruby-lang.org/issues/14908)\] Enumerator::Lazy creates unnecessary Array objects

- Proposed solution is to change arity of Enumerator::Yielder#<< to 1 from -1 and use it internally for lazy enum instead of Enumerator::Yielder#yield. Generally, method << is used with only one parameter changing arity to 1 also makes it consistent with other classes (String, Array)
- Since, proposed solution involves spec changes (Enumerator::Yielder#<<'s arity) a discussion is requested before the provided patch can proceed.

- \[Feature [#13167](https://bugs.ruby-lang.org/issues/13167)\] Dir.glob is 25x slower since Ruby 2.2 (ahorek)

- improves performance of a glob pattern with braces, especially on windows but also on other platforms
- the idea was already approved by [matz (Yukihiro Matsumoto)](https://bugs.ruby-lang.org/users/13) [#13873](https://bugs.ruby-lang.org/issues/13873) but later revered (incompatibility of the order)
- attached patch don't have the same problem

- \[Feature [#13618](https://bugs.ruby-lang.org/issues/13618)\] auto-fiber name, is "Thread::Coro" OK? How should Mutex work between coroutines? Queue/SizedQueue works, now (not sure about signal handler).

\[Feature [#5825](https://bugs.ruby-lang.org/issues/5825)\] Sweet instance var assignment in the object initializer
