---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-06-13

Date: 2016/06/13 (Mon)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details. If you want to attend remotely, also please ask ko1.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe in this meeting we discuss about Ruby 2.4, based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is a very busy person.  Take this opportunity to ask him. If you can not attend, other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All activities are best-effort (keep in mind that most of us are volunteer developers).
  * The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name (see example below). Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## About 2.4 timeframe

* when to release preview1? (shyouhei)

## Carry-over from previous meeting(s)

* [Misc #12283] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?
* [Bug #12295] Ripper not emitting `on_parse_error` for global variable name syntax errors (shyouhei) is this the right design?
* [Bug #12359] Named captures "conflict" warning is unnecessary and limits uses of named captures (shyouhei)
* [Feature #12281] Allow lexically scoped use of refinements with `using {}` block syntax (shyouhei)
* [Feature #12317] Name space of a module (shyouhei)
* [Feature #11090] Enumerable#each_uniq and #each_uniq_by (shyouhei)
* [Feature #12275] String unescape (shyouhei) lets limit the scope to "invert #dump's effect"
* [Feature #12345] A module's private constants are given with `Module#constant(false)` (shyouhei) intended behaviour?
* [Feature #12361] Proposal: add `Geo::Coord` class to standard library (shyouhei)
* [Feature #12386] Move definition of `ONIG_CASE_MAPPING` compilation switch o... (duerst)
* [Feature #12138] Support `Kernel#load_with_env` (shyouhei)
* [Feature #12333] `String#concat`, `Array#concat`, `String#prepend` to take multiple arguments (shyouhei)
* [Feature #12334] Final/Readonly Support for Fields / Instance Variables (shyouhei)
* [Feature #12350] Introduce Array#find! that raises an error if element not found (shyouhei)
* [Feature #12360] More useful return values from bang methods (shyouhei)
* [Bug #9569] `SecureRandom` should try `/dev/urandom` first (shyouhei) I'd like to hear other opinions.

## From attendees
* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* GSoC achievements report (ko1)
* [Feature #12299] Add Warning module for customized warning handling (hsbt, jeremyevans)
* [Feature #12361] Proposal: add Geo::Coord class to standard library (hsbt)
* missing Unicode Data for tests ([Bug #12433], duerst)
* [Feature #12142] Hash tables with open addressing (duerst)
* [Feature #12447] Integer#digits for extracting digits of place-value notation in any base (mrkn)
* Supported Platforms (https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/SupportedPlatforms) (duerst)
* [Feature #12460] Make Unicode Version directly available in Ruby (duerst)
* Non-ASCII in rdoc comments in C source (see also https://github.com/rdoc/rdoc/issues/409) (duerst)
* [Bug #12443] Test failures in TestDir_M17N on cygwin (duerst)
* Use of 'feedback' in general (see e.g. https://bugs.ruby-lang.org/issues/12443#note-3) (duerst)
* [Bug #12427] the way that extension libraries know if `Integer` is integrated (nobu)
* How to handle security issues (shugo)
  * Pros./Cons. about using GitHub, Slack, HackerOne and etc.
* [Bug #11859] Regexp matching with \p{Upper} and \p{Lower} for EUC-JP doesn’t work. (duerst)
* Possible to 'comment out' some tests for cygwin (e.g. [Bug #12445])? (duerst)
* Other cygwin-related bugs ([Bug #12440], [Bug #12442], [Bug #12443], [Bug #12444]) (duerst)
* [Feature #12484] Optimizing Rational (hsbt, mrkn)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [Feature #12300] Allow `Object#clone` to take `freeze: false` keyword argument to not freeze the clone (jeremyevans)

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendees: Matz (remote), Koichi Sasada, Nobu(yoshi) Nakada, Shyouhei Urabe (host), Hiroshi Shibata, Akira Tanaka, Yui Naruse, Kenta Murata, Satoru Horie (guest), Martin Dürst, sorah (from 15:00), Shugo  Maeda (remote)

## Next meeting: 7/19 (Tue) 14:00-@Pivotal Lab.

## GSoC achievements report (ko1, Horie-san)

Link to [project description](https://summerofcode.withgoogle.com/organizations/5749695703941120/#4576418910437376) ([Automatic-selection mechanism for data structures in MRI](https://summerofcode.withgoogle.com/projects/#4576418910437376)). Koichi is a mentor. Report: Started to implement String using Rope. Showing some performance results of initial experiments.

Discussion of suitable application benchmarks, possible problems with implementation,...

# The meeting for Preview 1.

Release management of 2.4: When release 2.4 preview1?

- Motivation: Test recent big changes

- Integer
- OpenSSL
- String#upcase/downcase/… now works for Unicode

- Motivation: to release before RedDotRubyConf

This release doesn’t prevent further big changes for Ruby 2.4.

(This release is different from an usual preview1 (even more ‘previewy’))

## [[Misc #12283]](https://bugs.ruby-lang.org/issues/12283) Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

Any progress? → Nothing

Naruse-san or Nakada-san will do.

Q: How should I do when I miss the reference and so on.

A: Use empty commit.

# How to handle security issues (shugo)

- Pros./Cons. about using GitHub, Slack, HackerOne and etc.
- Aaron proposed [HackerOne](https://hackerone.com/) which is a specialized Web site to discuss security issues. In particular, it doesn’t include actual contents in email notifications, …, and also has a system for bounties.
- We can’t stop Mail address.
- Current issue

- We need to decrypt security issue and encrypt to report.
- GitHub notifications must be disabled because they contain sensitive information.

- Try this service.
- Should we decide whether reports are eligible for bounty?

## [[Feature #12333]](https://bugs.ruby-lang.org/issues/12333) String#concat, Array#concat, String#prepend to take multiple arguments (shyouhei)

matz: acceptable

nobu: this patch requires some tests, and fixes.

- ar = [1]; ar.concat(ar, ar) #=> [1,1,1]? or [1,1,1,1]?

- mrkn: we can make [1, 1, 1, 1] with a.concat(ar).concat(ar)
- Matz: Maybe programer may get [1, 1, 1]. So use it.

- see https://bugs.ruby-lang.org/projects/ruby/wiki/DeveloperHowto

## \[Feature #12247\] accept multiple arguments at Array#delete

What’s happen?

ary = [:a, :b, :c, :d, :e]

ary.delete(:a, :f, :c) #=> ???

\# (1) [:a, :c]

\# (2) [:a, nil, :c]

- ko1: who requires return value (array)?
- sora_h: With (2), we can use a return value with multiple assignment

- x, y, z = ary.delete(:a, :f, :c)
- Especially for Hash#delete (this is out of scope, but we should consier consistency)

## [[Bug #12295]](https://bugs.ruby-lang.org/issues/12295) Ripper not emitting on_parse_error for global variable name syntax errors (shyouhei) is this the right design?

Assigned to Minero Aoki.

## [[Feature #12484]](https://bugs.ruby-lang.org/issues/12484) Optimizing Rational (hsbt, mrkn)

- matz approved to add commit permission to Tadashi-san.
- mrkn will reivew the proposed patch

## [[Feature #12281]](https://bugs.ruby-lang.org/issues/12281) Allow lexically scoped use of refinements with using {} block syntax (shyouhei)

Assigned to shugo.

## \[Feature #12086\] using: option for instance_eval etc.

Motivation is replace self and using context.

“Can we make it convinient?”

Matz will comment it.

## [[Feature #12447]](https://bugs.ruby-lang.org/issues/12447) Integer#digits for extracting digits of place-value notation in any base (mrkn)

- Q. Endian? #=> Little endian
- Q. how about negative integers? #=> Math::DomainError
- Q. how about 0? #=> [0] or [] #=> [0]

matz: i’m not sure how it is convinience. but ok.

## [[Feature #12299]](https://bugs.ruby-lang.org/issues/12299) Add Warning module for customized warning handling (jeremyevans)

naruse: it sounds useful with Gem.loaded_specs[ gem_name ].full_gem_path.

matz: ok (except naming).

# Supported Platforms (duerst)

h[ttps://bugs.ruby-lang.org/projects/ruby-trunk/wiki/SupportedPlatforms](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/SupportedPlatforms) says it’s for Ruby 1.9; needs some updating.

## [[Feature #12460]](https://bugs.ruby-lang.org/issues/12460) Make Unicode Version directly available in Ruby (duerst)

Approved; name as proposed by Nobu: RbConfig::CONFIG['UNICODE_VERSION']; implementation: Nobu or Martin.

# Non-ASCII in rdoc comments in C source (duerst)

See [https://github.com/rdoc/rdoc/issues/409](https://github.com/rdoc/rdoc/issues/409). Intent is understandable. May work by just using &#(x)... HTML/XML convention; needs to be checked further.

## [[Bug #12427]](https://bugs.ruby-lang.org/issues/12427) the way that extension libraries know if Integer is integrated (nobu)

Nobu prepared a patch to show warning for rb_cFixnum and rb_cBignum, so developers can recognize this change. However, there are risks to compile broken code (assume passed parameters are Fixnum, but passed BIgnums. In this case, it is difficult to check Fixnum asusmed methods because Bignum may be rare to use, in general).

We can recognize this change with removing rb_cFixnum and rb_cBignum, but there are several gems causing compile errors.

→ try to remove rb_cFixnum, rb_cBignum at Ruby 2.4  preview 1 to check people’s responses
