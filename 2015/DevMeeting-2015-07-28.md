---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-07-28

Date: 2015/07/28 (Tue)
Time: 14:00- 19:00 (JST)
Place: Tokyo, Japan
Remote participation: Slack? Google hangout? Ask ko1.
Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.3 based on tickets.
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
* How should we handle signal
* issue triage on [github](https://github.com/ruby/ruby/pulls)
 * https://github.com/ruby/ruby/pulls/nobu
* [Feature #11253] "flags" keyword for open() (naruse)
* [Feature #11258] add 'x' mode character for O_EXCL (akr)
* [Feature #11398] deprecate constants (nobu)
* clang-format

## From non-attendees

* status of RubyCI and the expectation of its restoration (usa)
 * our storage account is expired from about 2-3 week ago.
 * we will replace following azure backends to s3.
 * https://github.com/akr/chkbuild/blob/master/chkbuild/upload.rb#L75
* about the necessity of r51384 (it takes toooooo looooong time to run test-all) (usa)
* [Feature #8919] Queue as embedded class: please determine whether or not it should be introduced. (glass_saga)
* [Feature #11297] Allow private method of self to be called (a_matsuda)

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendee: akr, hsbt, ko1, matz, naruse, nobu

# deprecated constants

Matz: OK

# open flags

matz: ok

# “x” for open

matz: ok

## [https://bugs.ruby-lang.org/issues/11339](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11339&sa=D&source=editors&ust=1686086447558338&usg=AOvVaw2vgkHD9pqJjDzGHhqvEaG1)

Introducing IDL will

 GOOD: improve performance

 BAD: increase required knowledeg to hack Ruby

Introducing the primitve “C.foo(x, y)” calling C function “foo” with two VALUEs “x and y” seems good primitive only in prelude.rb. Now, it is only for prelude.rb, not for all C-extensions.

akr pointed out that it will introduce incompatibility compare with C implemented function and Ruby with C function because therea are interrupt timing in Ruby. For example,

 class IO

 def IO.open(..., &b)

 fd = C.sysopen(...)

 f = IO.new(fd)

 …

this code can leak \`fd’ if some exception is occur before making IO object (IO object will free fd). In this case, we need to make C functions to make IO objects.

Also, additional TracePoint will be introduced.

# status of RubyCI and the expectation of its restoration (usa)

- our storage account is expired from about 2-3 week ago.
- we will replace following azure backends to s3.
- [https://github.com/akr/chkbuild/blob/master/chkbuild/upload.rb#L75](https://www.google.com/url?q=https://github.com/akr/chkbuild/blob/master/chkbuild/upload.rb%23L75&sa=D&source=editors&ust=1686086447560583&usg=AOvVaw1Y-fiqFqbpP-ZlbBuzn_Uu)

## about the necessity of r51384 (it takes toooooo looooong time to run test-all) (usa)

Resolved.

## \[Feature [#8919](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8919&sa=D&source=editors&ust=1686086447561376&usg=AOvVaw1GpriQ8IBecqjlUXWryVI9)\] Queue as embedded class: please determine whether or not it should be introduced. (glass\_saga)

Matz: seems good.

Nobu: how is SizedQueue? They share same codes.

… no conclusion. Koichi will try.

# Feature #10600: \[PATCH\] Queue#close [https://bugs.ruby-lang.org/issues/10600](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10600&sa=D&source=editors&ust=1686086447562092&usg=AOvVaw3ALYRgd-eCbEZsvau_bO_e)

Matz: seems good.

akr: What happens on pushing/popping closed Queue?

ko1: there are discussions on ticket. I’ll take it.

## \[Feature [#11297](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11297&sa=D&source=editors&ust=1686086447562734&usg=AOvVaw2n78P0SgnFCpyVhlq5wN_v)\] Allow private method of self to be called (a\_matsuda)

Matz: seems good
