---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-01-18

Date: 2016/01/18 (Mon)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.4 based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is very busy person, so we can ask him directly on this opportunity. If you can not attend there, then other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All of activities are best-effort (please remind that most of us are volunteer developers).
  * The date, time and place is when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name. Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## From Attendees

* example: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [Feature #11949] Allow @/$ prefix in Regexp's named captures
* [Feature #11987] daemons can't show the backtrace of rb_bug
* planning about maintenance branches (usa)

## From non-attendees


(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendees: unak, mrkn, naruse, ayumin, ko1, sorah, zack, martin

# Windows CI

- Section

- Azure

- Low security risks (comapre with maintaining physical machines)
- Periodical payment is not easy
- Offering by Microsoft is best

- Phisical machines

- Easy to buy

- Schedule

- Negotiate with MS (wait for up to 6 months)
- If no positive response, seek other sponsors

- Negotiation

- Ayumin
- Takahashi-san
- Shibata-san
- Usa-san

## [[Feature #11949]](https://bugs.ruby-lang.org/issues/11949) Allow @/$ prefix in Regexp's named captures (naruse)

/(?<[@timestamp](https://github.com/timestamp)\>[^ ]\*): / =~

\>> /(?<@a>.)/ =~ 'x'

SyntaxError: (irb):9: invalid char in group name <@a>: /(?<@a>.)/

\>> /(?<a@b>.)/ =~ 'x'

\=> 0

valid: > /(?<a@-あfoo>a)/.match("a")

\=> #<MatchData "a" a@-あfoo:"a">

1.times{

 /(?<a@$b>.)/ =~ 'x'

 p [$~.names, binding.local_variables] #=> [["a@$b"], []]

}

1.times{

 /(?<ab>.)/ =~ 'x'

 p [$~.names, binding.local_variables] #=> [["a@$b"], [:ab]]

}

- use case: fluentd scans logs with regexp and convert MatchData into Hash, then send it receivers.
- Meta characters are allowed in middle of names, but meta characters at the beggining of names are not allowed
- Maybe it is because $foo is confusing inserting to global bariables, and so on.

- $foo
- @foo
- #foo
- \>foo (NG)
- \\foo

- Should not set values other than local variabls

Conclusion: acceptable confusion or not. Matz is positive to accept this feature

## [[Feature #11987]](https://bugs.ruby-lang.org/issues/11987) daemons can't show the backtrace of rb_bug

Process.daemon closes STDERR (or redirect to /dev/null).

There’s no easy and foundamental way.

Conclusion: Use wrapper process to intercept stderr.

# Planning about maintenance branches (usa)

- Maintainance policy

- two versions with one security version
- Current living versions

- 2.3 (maintained 2015/12-??)
- 2.2 (maintained 2014/12-??)
- 2.1 (maintained 2013/12-??)
- 2.0.0 (security, 2013/02-2016/02)

- Need to determin EOL.
- Duplicated dates are useful.

- Summary

- 2.3 (chikanaga-san: maintained 2015/12-??)
- 2.2 (usa: maintained 2014/12-2017/03, security phase 2017/04-2018/03)
- 2.1 (usa: maintained 2013/12-2016/03, security phase 2016/04-2017/03)
- naruse: want to maintain trunk, not stable branch
- ask chikanaga-san to confirm his preference: -> he want to maintain younger branch.
- \-> will slide maintainers

# 2.3 Retrospective

- [https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20151109Japan](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20151109Japan)
- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23)

- Release dates:

- Preview 1:        2015/11/11
- Preview 2:        2015/12/13
- Release:        2015/12/25

- Points

- Bad: We should release preview1 earlier; We did it late

- Because there’s no large feature worth to release preview1
- Try: release preview1 at next RubyKaigi2016 (2016/09)

- Good: Highly compatible with Ruby 2.2.
- Good features:

- Indented here document
- Hash#dig
- &.

- Try: We should follow the announced schedule more strictly

# 2.4 Release Engineering

- 8 Jun: Call for feature proposal
- 8 Sep (RubyKaigi): Preview 1
- 10 Nov (RubyConf): Preview 2
- XX Dec: Release Candidate, feature freezed
- 25 Dec: Final Release
- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24)
- Note:

- Declare that preview releases are experimental and subject to change.
- Additional announcement (e.g. [www.ruby-lang.org](http://www.ruby-lang.org))?
- Reminders at before a release?

[https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/CallForFeatureProposalTemplate](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/CallForFeatureProposalTemplate)

## Announcement channel

We have only mailing lists and [www.ruby-lang.org](http://www.ruby-lang.org) to annouce news. However, it is difficult to catch up latest news by such channels. Any other ideas?

- official twitter account (www.ruby-lang.org headline)

- [https://twitter.com/rubylangorg](https://twitter.com/rubylangorg)
- something RSS bot

# Next release

- 2.4 or 3.0?
- 3.0 should contain:

- concurrency
- type check
- performance

naruse’s note [https://gist.github.com/nurse/4324519](https://gist.github.com/nurse/4324519)

## Remove Fixnum and Bignum

Matz: Try it on 2.4

## 1 / 2 is Rational

Matz: Difficult to change later.

## 0.1 is Rational

naruse: use 0.1r

mrkn; 0.1f is required.

## timezone name on Windows

naruse: It would be good if we can use IANA time zone name on Windows

## rubypath

Path to executing ruby executable

Name issue.

idea: Etc.rubybin

naruse: On OS X, people make a bundle

nobu: To load libraries from relative path, --enable-load-relative

## Threads dump on SEGV

ko1: i agree.

# Unicode Data File Update

## Files don’t get downloaded on a new install (or version update)

Various levels of downloading "urgency" (triggered by 'make up' or some subtarget):

Current: Don't download at all

Ideal: Always check for updates (with If-Modified-Since); don't actually download data if nothing new

Currently, there's a flag (ALWAYS_UPDATE_UNICODE = yes at common.mk: 1002) that needs to be manually changed to do 2) but has a comment suggesting 1). If that flag isn't activated, what happens is 5), but we need at least 4), and a reload when we change the version.

Conclusion: Change to update whenever ‘make up’ is activated

## List each Unicode Data file only once

Currently, each file is listed twice (UNICODE_FILES at common.mk:1004 and ./.unicode-$(UNICODE_VERSION).time at common.mk:1021); it would be great if this could be reduced to list each file only once.

Conclusion: Nobu to try to find a solution to avoid duplication, but not make it too complicated, please!

# Use of Unicode in Source Files

C source shouldn’t include non ASCII, because there may be some old compilers that bark on it (e.g. working in Shift_JIS and thus barking on UTF-8 input).

lib/unicode_normalize/tables.rb: is auto generated code; raw Unicode character is acceptable.

raw Unicode characters are also acceptable in test files

# Next

2015/02/16 (Tue) 14:00-
