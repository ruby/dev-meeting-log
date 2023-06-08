---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-02-22

# DevelopersMeeting20170222Japan

Date: 2017/02/22 (Wed)
Time: 14:00- 19:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/51744/
Attendees: Yukihiro Matsumoto (Matz), Nobuyoshi Nakada, Shyouhei Urabe, Kenta Murata, Akira Matsuda, Akira Tanaka, Yui Naruse, Hiroshi Shibata, Martin Dürst
Language: mostly Japanese (sorry for non native Japanese speakers)

Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is a very busy person.  Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
  * We will write a log about discussion to a file or to each ticket in English.
  * All activities are best-effort (keep in mind that most of us are volunteer developers).
  * The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name (see example below). Explain details on the ticket. If you cannot attend the meeting, we appreciate a short summary because we can understand it more easily (long discussion is difficult to read, especially in a non-native language). Your motivation is also welcome.

## About 2.5 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25

## Carry-over from previous meeting(s)

* [Feature #12962] Feature Proposal: Extend 'protected' to support module friendship (shyouhei)
  - see also:
  - [Feature #9992] Access Modifiers (Internal Interfaces)
* [Feature #12968] Allow default value via block for Integer(), Float() and Rational() (shyouhei)
* [Feature #12995] Conditional expression taking a receiver outside the condition (shyouhei)
* [Feature #12996] Optimize Range#=== (shyouhei)
* [Feature #10912] Add method(s) to IPAddr for determining whether an address is link local (shyouhei)
* [Bug #13005] Inline rescue is inconsistent when rescuing NoMethodError (Dürst)
* [Feature #13026] Public singleton methods (shyouhei)
* [Bug #13024] Confusing error message matching a non-ASCII string with ASCII-regex (shyouhei)
* [Feature #13009] Implement fetch for Thread.current (shyouhei)
* [Feature #13045] Passing a Hash with String keys as keyword arguments (shyouhei)
* [Feature #9846] Regexp#to_regexp (shyouhei)
* [Feature #13047] Use String literal instead of `String#+` for multiline pretty-printing of multiline strings (shyouhei)
* [Feature #8661] Add option to print backstrace in reverse order(stack frames first & error last) (shyouhei)
* [Feature #13077] [PATCH] introduce String#fstring method (shyouhei)
* [Feature #13083] {String|Symbol}#match{?} with nil returns falsy as Regexp#match{?} (shyouhei)
* [Feature #12508] Integer#mod_pow (shyouhei)
* [Misc #13072] Current state of date standard library (shyouhei)
    - related:
    * [Bug #12981] Date.parse raises an Argument error under a specific condition (shyouhei) assign who?
    * [Bug #13101]  Date#rfc2822 and Time#rfc2822 don't return the same format
* [Feature #13097] Deprecate Socket.gethostbyaddr and Socket.gethostbyname (shyouhei)
* [Bug #13105] `String#to_f` and `String#to_r` don't stop successive underscores (nobu)
* [Feature #13109] `using` in refinements is required to be physically placed before the refined method call (shyouhei)
* [Bug #12705] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei) status?
* [Feature #13095] [PATCH] io.c (rb_f_syscall): remove deprecation notice (shyouhei)
* [Feature #13122] Special syntax for Hash#default_proc (shyouhei)

## From attendees

* [Bug #13225] [DOC] expand docs for Date shifting (hsbt)
* [Feature #13156] In-tree copy of ruby/spec (duerst)
* [Feature #12650] Use UTF-8 encoding for ENV on Windows (duerst)
* [Feature #13133] TracePoint: Add event type for constant access (hsbt)
* [Feature #13197] Gemify fileutils (hsbt)
 * https://bugs.ruby-lang.org/issues/13197#note-4
* [Feature #6284] Add composition for procs (shyouhei)
* [Feature #13137] Hash Shorthand (shyouhei)
* [Feature #13166] Feature Request: Byte Arrays for Ruby 3 (shyouhei)
* [Feature #9116] String#rsplit missing (shyouhei)
* [Feature #4532] [PATCH] add IO#pread and IO#pwrite methods (shyouhei)
* [Bug #13146] Float::NANs in Hashes are confusing (more than usual).
* [Bug #13134] Rational() inconsistency (nobu)
* [Feature #13240, Feature #13241] Change Unicode property implementation / Method(s) to access Unicode properties (duerst)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #13110] Byte-based operations for String (shugo)

# Log

Date: 2017/02/22 (Wed)
Time: 14:00- 19:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/47745/
log edit: https://docs.google.com/document/d/1fZPbRMn2zjVAflvLz1IFWs3Kdijnm2Um4uNLammqURE/edit
log: TBD
Next meeting
3/13
at MoneyForward HQ

About 2.5 timeframe
https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25
合宿
project going on
no fixed dates yet.
hsbt: I will negotiate with ruby association.
Current status of Ruby 2.4
Mentainer not fixed yet.
naruse: no progress.
hsbt: I’ll nudge shugo.
matz: 2.1 security maintenance will last until March

Carry-over from previous meeting(s)
[Feature #9992] Access Modifiers (Internal Interfaces)
matz: the internal visibility is visible from where?
nobu: not obvious?
shyouhei: there seems to be a looong discussion.
matz: seems the OP defines a namespace is a toplevel.
matz: not feeling nice to me.
[Feature #12968] Allow default value via block for Integer(), Float() and Rational()
akr: Integer(string, exception: false) || 0
naruse: that is #12732.
matz: seems a bit long?
akira: there already are exception: false usages.
matz: OK to have either one of them.
shyouhei: I’ll comment on #12968 referring #12732.
performance?
[Feature #12995] Conditional expression taking a receiver outside the condition
matz: this is impossible.
[Feature #12996] Optimize Range#===
akr: is this compatible?
mrkn: seems like that.
nobu: breaks old brhaviour if you redefine Range#include?
akr: I think that’s acceptable, given you want to subclass a Range.
matz: OK then.
[Feature #10912] Add method(s) to IPAddr for determining whether an address is link local
assign knu.
[Bug #13005] Inline rescue is inconsistent when rescuing NoMethodError
Martin: I thought rescue keyword was less priotiry.
matz: thinking of x = y rescue z, exception is much more likely to happen inside of y, not in x.  so rescue should save y.
[Feature #13026] Public singleton methods
matz: not immediately against it but I want to know the use-case.
[Bug #13024] Confusing error message matching a non-ASCII string with ASCII-regex
akr: I wrote the message.
matz: is it a matter of error message?
Martin: delete the “to” word.
matz: how about “binary regexp match against...”
[Feature #13009] Implement fetch for Thread.current
ko1: no objection
matz: ditto,
ko1: what about ENV?
nobu: ENV also has this one.
[Feature #13045] Passing a Hash with String keys as keyword arguments
matz: this request is NG but I understand the needs of conversion.  Make it a new feature request.
[Feature #9846] Regexp#to_regexp
ko1: is there other method that has #to_regexp?
mrkn: Object has.
akr: what.
ko1: no one prepares to_regexp?
akr: conversion nowadays are done by try_convert, no?
matz: try_convert was introduced to mean interfaces to C library.
nobu: the proposed patch is NG
ko1: there will be no to_r?
[Feature #13047] Use String literal instead of String#+ for multiline pretty-printing of multiline strings
akr: is that more readable?
[Feature #8661] Add option to print backstrace in reverse order(stack frames first & error last)
akr: why not just do this?
matz: I like the idea but… breaks something?
shyouhei: if you want to try this, do so today.
ko1: either make it customizable, or change the VM.
naruse: I don’t like the idea.  SEGV output (which is in reverse-order) is hard to read.
naruse: also when you log the output it is natural to list in the current order.
continue discussion.
[Feature #13077] [PATCH] introduce String#fstring method
ko1: I don’t want the name fstring.
matz: -@ seems OK.
[Feature #13083] {String|Symbol}#match{?} with nil returns falsy as Regexp#match{?}
matz: it seems #match should raise instead.
[Feature #12508] Integer#mod_pow
matz: OK. go ahead.
[Misc #13072] Current state of date standard library
naruse: This ticket is great.  It summarizes the current situation quite accurately.
akr: we don’t want to merge Date with Time.
shyouhei: independent from the future of Date, we are going to separate Date._parse. Right?
[Feature #13097] Deprecate Socket.gethostbyaddr and Socket.gethostbyname
akr: I think we should implement gethostbyaddr on top of getaddrinfo.
From attendees
[Bug #13225] [DOC] expand docs for Date shifting
hsbt: I want him to be a committer.
matz: OK.
nobu: I have seen no problem on his past patches.
[Feature #13156] In-tree copy of ruby/spec
duerst: this way you can commit ruby and spec at once.
shyouhei: neutral.
duerst: +. it is easier than separate repo
ko1: somewhat -. github has more user base.
naruse: several reasons to object:
2.3 left unchanged.
mspec lacks some feature. CF [ruby-core:79088]
I don’t think we are going to maintain both test-all and test-rubyspec.  We are either going to switch to rubyspec, or to remain test-all.  And I don’t think we are going to abondon test-all.
nobu: somewhat negative.
akr: I’d like to propose more use of test-all.  test-all tends to have tests for new features.  I think it’s easier to use it than to change core people.
[Feature #13133] TracePoint: Add event type for constant access
hsbt: I want ko1 to look at it.
ko1: this will introduce other trace hooks.
ko1: trace overhead is OK when enabled but is a problem when not traced at all.  Current implementation has overhead when on such situation.
akr: regeneration of binary is needed to reroute that I think.
[Feature #13197] Gemify fileutils
https://bugs.ruby-lang.org/issues/13197#note-4
hsbt: there already are gems named “fileutils” or “dbm”...
rubygems.org blocked these namespaces from future additions, but they are there already.
pathname depends on fileutils.
[Feature #13240, Feature #13241] Change Unicode property implementation / Method(s) to access Unicode properties (duerst)
Martin: currently we can know if a string contains, say, Hiragana, but there is no way to say what script the given string is.
akr: onigumo issue should be reported to their repo.
Martin: that was not what we did for unicode-aware cases
akr: if this feature is merged to onigumo then it’s OK.  But if they reject, we should not introduce our own modified version of onigumo.
naruse: I want to use this feature
shyouhei: but who others?
akr: do we plan to have the whole Unicode property DB?  If so, should that be a part of onigumo?
naruse: including the entire set of Unicode DB in the ruby.tar.gz sounds too big.

