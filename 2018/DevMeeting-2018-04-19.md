---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-04-19

# DevelopersMeeting20180419Japan

Date: 2018/04/19 (Thu)
Time: 14:00-18:00 (JST)
Place: Speee, Inc.
Sign-up: https://ruby.connpass.com/event/82991/
Attendees:
Regrets: (add your name here if you often attend, but can't attend this time)
Language: mostly Japanese (sorry for non native Japanese speakers)

Please add your favorite ticket numbers you want to ask to discuss.  If you have no right to edit this page, add a comment to the ticket that you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is a very busy person.  Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
  * We will write a log about discussion to a file or to each ticket in English.
  * All activities are best-effort (keep in mind that most of us are volunteer developers).
  * The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name (see example below). Explain details on the ticket. If you cannot attend the meeting, we appreciate a short summary because we can understand it more easily (long discussion is difficult to read, especially in a non-native language). Your motivation is also welcome.

## About 2.6 timeframe

* [[ruby-master:ReleaseEngineering26]]

## From attendees

* [Bug #14345] http_proxy setting should respect both parent domain and subdomain (hsbt)
* [Feature #14559] `ENV.slice` (hsbt)
* [Feature #14643] Remove problematic separator `'\0'` of `Dir.glob` and `Dir.[]` (usa)
* [Feature #14111] `ArgumentError`が発生した時メソッドのプロトタイプをメッセージに含む (nobu)
* [Bug #14688] `Net::HTTPResponse#value` raises "`Net::HTTPServerException`" in 4xx response (usa)
* [Feature #14694] `TracePoint#parameters` (mame)
* [Feature #14624] `#{nil}` allocates a fresh empty string each time (nobu)
* [Feature #6670] str.chars.last should be possible (mame)
* [Feature #12912] An endless range `(1..)` (mame)
* [Feature #14352] Array#pack("M") Quoted-printable (matz)
* [Feature #14594] Rethink yield_self's name (matz)
* [Feature #14697] Introducing Range#% as an alias to Range#step (mrkn)
* [Feature #14696] add optname SO_ORIGINAL_DST (akr)

## From non-attendees

* [Feature #14683] IRB with Ripper (aycabta)

## Carry-over from previous meeting(s)

# Log

## Date: 2018/04/19 (Thu)

## Time: 14:00- 18:00 (JST)

## Place: Speee Inc.

## Sign-up:

## log edit:

## log: TBD

wiki: [https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20180419Japan](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20180419Japan)

## Agenda

### Next Developper Meetings

2018/05/17 (Thu) @ Cookpad

### About 2.6 timeframe

- Naruse : would like to release PR2 next month.
- Mame: what’s new?
- Naruse: Exception#cause, JIT update, etc.  Maybe also IRB with ripper?
- Hsbt: also rubygems 3, perhaps.

### From attendees

\[Bug [#14345](https://bugs.ruby-lang.org/issues/14345)\] http\_proxy setting should respect both parent domain and subdomain (hsbt)

\[Feature [#14559](https://bugs.ruby-lang.org/issues/14559)\] ENV.slice (hsbt)

- Matz: OK.

\[Feature [#14643](https://bugs.ruby-lang.org/issues/14643)\] Remove problematic separator '\\0' of Dir.glob and Dir.\[\] (usa)

- Usa: we can pass arrays to the pattern.  No need to use \\0.
- Matz: makes sense.

\[Feature [#14111](https://bugs.ruby-lang.org/issues/14111)\] ArgumentErrorが発生した時メソッドのプロトタイプをメッセージに含む (nobu)

- nobu : seems OK to me.
- Mame: is the “prototype” the actual string in the source code, or reconstructed?
- nobu : reconstructed.
- Matz: so it doesn’t show C source code.
- Matz: OK so far.
- Matz: do we call this info “prototype”?
- Naruse: declaration?
- Mrkn: signature?
- Akr: I prefer “arguments”
- Nobu: would like to review the patch again.
- Ko1: what to show for singleton methods? Might be difficult.

\[Bug [#14688](https://bugs.ruby-lang.org/issues/14688)\] Net::HTTPResponse#value raises "Net::HTTPServerException" in 4xx response (usa)

\[Feature [#14683](https://bugs.ruby-lang.org/issues/14683)\] IRB with Ripper (aycabta)

- Ask keiju to look at it.
- Akr: do the OP want to maintain this?  Or make it a gem to delete from the repo?

\[Feature [#14694](https://bugs.ruby-lang.org/issues/14694)\] TracePoint#parameters (mame)

- Matz: I see no problem?
- Ko1: TracePoint#parameters sounds like it expects the parameter values, not the names.

\[Feature [#14624](https://bugs.ruby-lang.org/issues/14624)\] #{nil} allocates a fresh empty string each time (nobu)

- Matz: sounds like an implementation detail.
- Ko1: it’s too large a patch to implement this as a new VM instruction.
- Naruse: when I tested opt\_to\_s with @k0kubun the speedup was marginal/not that much.

\[Feature [#6670](https://bugs.ruby-lang.org/issues/6670)\] str.chars.last should be possible (mame)

- Mame: I’d like to give up deprecation of this block-giving behaviour.
- Matz: OK.

\[Feature [#12912](https://bugs.ruby-lang.org/issues/12912)\] An endless range (1..) (mame)

- Mame: I’d like to consult matz.
- Matz: I honestly feel there should be a fluent way to do 0..Float::INFINITY.
- Ko1: does this feature conflict with the step concept?
- Mrkn: seems not.
- Akr: when this is done, needs of step should reduce very much.
- Matz: accepted.

\[Feature [#14352](https://bugs.ruby-lang.org/issues/14352)\] Array#pack("M") Quoted-printable (matz)

- Naruse: I’ll take care.  Let me make this a doc issue.

\[Feature #14594\] Rethink yield\_self's name (matz)

- Matz: I can live with #then.

\[Feature [#14697](https://bugs.ruby-lang.org/issues/14697)\] Introducing Range#% as an alias to Range#step (mrkn)

- Matz: I have no objection.
- Mrkn: Numo’s Range#% is here [https://github.com/ruby-numo/numo-narray/blob/master/ext/numo/narray/step.c#L464](https://github.com/ruby-numo/numo-narray/blob/master/ext/numo/narray/step.c#L464)
