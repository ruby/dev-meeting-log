---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-06-21

Date: 2018/06/21 (Thu)
Time: 14:00-18:00 (JST)
Place: Cookpad Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/88715/

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

From this time, we use a ticket to make dev-meeting agenda page instead of a wiki page <https://bugs.ruby-lang.org/projects/ruby/wiki#Developer-Meetings>.

# NOTE

Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
We will write a log about discussion to a file or to each ticket in English.
All activities are best-effort (keep in mind that most of us are volunteer developers).
The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.6 timeframe

## From Attendees

(will be edited later)
(if you have a write access permission, please list directly)

* [Feature #13881] Use getcontext/setcontext on OS X
* [Bug #14847] `clone` can generate strange objects
  * we found several strange behavior so let's clear the specification
* [Feature #13625] BigDecimal short form / shorthand
  * mrkn: BigDecimal is not a core class, so we cannot add such a syntax sugar.
* [Bug #14858] Introduce 2nd GC heap named Transient heap (ko1)
  * introduce new memory management technique.
* [Bug #14699] Subtle behaviors with endless range (mame)
  * Need to be discussed with mrkn
* [Bug #14845] Endless Range with nil (mame)
  * Should explicit nil for endless range (like (1..nil)) be prohibited or not?
* [Bug #14824] Endless Range Support in irb (aycabta)
* [Feature #14808] Last token of endless range should have EXPR_END (aycabta)

## From non-attendees


(will be edited later)
(if you have a write access, please list directly)

* [Feature #14781] Enumerator#generate (zverok)
  * more reasonable version of Object#enumerate proposed for the previous meeting.
* [Feature #14830] RubyVM::MJIT.pause / RubyVM::MJIT.resume (k0kubun)
  * Is it okay to add such methods? If so, is there any comment for the behavior described in the ticket?
* [Feature #14709] Proper pattern matching (zverok)
* [Feature #14799] Startless range (zverok)
* [Feature #14784] One-sided Comparable#clamp (with endless/startless ranges) (zverok)
* [Feature #14859] Timeout in VM (normalperson)
  *   Still needs some work, mainly wondering if the idea of moving this part of stdlib into core VM is acceptable or not.  No semantic changes except speed improvement.
