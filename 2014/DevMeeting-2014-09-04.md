---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-09-04

# DevelopersMeeting20140904Japan

Date: 2014/09/04
Time: 19:00 -
Place:
Attendees: sign up required: http://cruby.doorkeeper.jp/events/13742

## Agenda (please add proposer's your name)

- [Feature #10200] Symbol API (static count, dynamic count, all_symbols and so on) (ko1)
- [Feature #9880] Dir#fileno (akr)
- [Feature #10199] Drop to support Symbian(hsbt)
- [Feature #10201] Dynamically changing GC tuning parameters (ko1)
- [Feature #8923] Frozen nil/true/false

- Ruby 2.2 release plan: requirement

 * incremental gc
 * latest rubygems, rake, rdoc
 * vfork

- release plan(candidate)

 * preview1: 9/13-18
 * preview2: 11/1-8
 * rc1: 12/1W
 * release: 12/25

- Next meeting

 * 10/29(preview2 pre meeting)

- meeting process
- Check http://rubykaigi.org/2014/ama entries

# Log

- attendee: ko1, sora\_h, akr, naruse, ayumin, a\_matsuda
- on-line: matz, nobu

## [[Feature #10199]](https://bugs.ruby-lang.org/issues/10199) Drop to support Symbian (hsbt)

Matz: agreed.

Nobu: how about BeOS?

akr: no need to think about it now.

hsbt: I will remove.

## [[Feature #10200]](https://bugs.ruby-lang.org/issues/10200) Symbol API (static count, dynamic count, all\_symbols and so on) (ko1)

Symbol.all\_symbols:

matsuda: Symbol.all\_symbols is used by pry to make completion.

matz: leave Symbol.all\_symbols as current behavior

Symbol.find():

akr: Symbol.find() can be removed because it is 2.2 feature.

https://bugs.ruby-lang.org/issues/7854#change-45471

matz: remove it and ask people

Couting immortal symbols is not solved. ko1 will make prototype of such methods.

## [[Feature #9880]](https://bugs.ruby-lang.org/issues/9880) Dir#fileno (akr)

matz: portability?

akr: POSIX support it.

akr: For windows and so on, not implemented error can be acceptable.

matz: ok. write document for compatibility.

## [[Feature #10201]](https://bugs.ruby-lang.org/issues/10201) Dynamically changing GC tuning parameters (ko1)

ko1 will try.

## vfork()

akr: Now trunk uses vfork() to optimize making process. However, now it can be vulnerability because of vfork().

## Ruby 2.2 release plan

Preview 1 (9/13 freeze)

Need to apply:

\- Incremental GC

\- RubyGems / Rake / RDoc

\- vfork

Preview 2 (11/?)

RC1 (12/?)

RC2 (if needed)

Release 2.2.0 (12/25)

[https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering22](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering22)

## Next meeting 10/29
