---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-07-19

Date: 2016/07/19 (Tue)
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

* Call for Feature Proposals? (shyouhei)

## Carry-over from previous meeting(s)

* [Misc #12283] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?
* [Feature #12299] Add Warning module for customized warning handling (jeremyevans)
* [Feature #12300] Allow `Object#clone` to take `freeze: false` keyword argument to not freeze the clone (jeremyevans)
* [Feature #11090] Enumerable#each_uniq and #each_uniq_by (shyouhei)
* [Feature #12275] String unescape (shyouhei) lets limit the scope to "invert #dump's effect"
* [Feature #12345] A module's private constants are given with `Module#constant(false)` (shyouhei) intended behaviour?
* [Feature #12386] Move definition of `ONIG_CASE_MAPPING` compilation switch o... (duerst)
* [Feature #12138] Support `Kernel#load_with_env` (shyouhei)
* [Feature #12334] Final/Readonly Support for Fields / Instance Variables (shyouhei)
* [Feature #12350] Introduce Array#find! that raises an error if element not found (shyouhei)
* [Feature #12360] More useful return values from bang methods (shyouhei)
* [Bug #9569] `SecureRandom` should try `/dev/urandom` first (shyouhei) I'd like to hear other opinions.

## From attendees
* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Misc #12529] LEGAL file covering all the license information within Ruby (shyouhei)
* [Feature #8526] gemify tk (hsbt)
* [Feature #3187] Allow dynamic Fiber stack size (shyouhei)
* [Feature #12543] explicit tail call syntax: foo() then return (shyouhei)
* [Feature #11813] Extend safe navigation operator for [] and []= with syntax sugar (shyouhei)
* [Feature #12589] VM performance improvement proposal (shyouhei)
* [Feature #12481] Add Array#view to allow opt-in copy-on-write sharing of Array contents (shyouhei)
* [Feature #12057] Allow methods with yield to be called without a block (shyouhei)
* [Feature #12297] Ruby stdlib date can parse non-existent date with year 0 (shyouhei)
    * Wasn't this intentional? cf https://en.wikipedia.org/wiki/0_(year)
* [Feature #12461] Hash & keys to make subset. (shyouhei)
* [Feature #12515] Create "Boolean" superclass of TrueClass / FalseClass (shyouhei)
* [Feature #12512] Import Hash#transform_values and its destructive version from ActiveSupport(shyouhei)
* [Feature #10208] Passing block to Enumerable#to_h (shyouhei)
* [Feature #11741] Migrate Ruby to Git from Subversion (shyouhei) Situation?
* [Feature #12463] ruby lacks plus-plus (shyouhei)
* [Bug #12466] Enumerable should yield multiple values when possible (shyouhei)
* [Bug #12467] Open mode 'a' does not work for Process.spawn on Windows (shyouhei)
* [Feature #12455] Add a way for class String to determine whether it has only numbers / digits or not (shyouhei)
* [Bug #12402] Inline rescue behavior inconsistent for method calls with arguments and assignment (shyouhei)
* [Bug #12489] hppa problems on Debian GNU/Linux (shyouhei) who to look at it?
* [Bug #12497] GMP version of divmod may be slower(shyouhei)
* [Feature #12490] Remove warning on shadowing block params(shyouhei)
* [Feature #3511] rb_path_to_class should call custom const_defined? methods (shyouhei)
* [Feature #12508] Integer#mod_pow(shyouhei)
* [Feature #12515] Create "Boolean" superclass of TrueClass / FalseClass (shyouhei)
* [Feature #12521] Syntax for retrieving argument without removing it from double-splat catch-all(shyouhei)
* [Feature #11195] Add "no_proxy" parameter to Net::HTTP.new (shyouhei)
* [Bug #12592] Tree conflict produced by r55701 (duerst)
* [Bug #12577] Is '$' punctuation or not? Inconsistency between us-ascii and UTF-8 (duerst)
* [Feature #12546] Remove UnicodeNormalize::UNICODE_VERSION (duerst)
* [Bug #12547] Remove ONIG_UNICODE_VERSION_... in enc/unicode/case-folding.rb, casefold.h (duerst)
* [Bug #12597] Produce test failure if some tests cannot be executed due to lacking data (duerst)
* [Feature #12386] Move definition of ONIG_CASE_MAPPING compilation switch outside onigumo files (duerst)
* [Feature #12578] Instance Variables Assigned In parameters ( ala Crystal? ) (duerst)
* [Feature #12419] Improve String#dump for Unicode output (from "\u{130}" to "\u0130") (duerst)
* [Feature #12142] Hash tables with open addressing (shyouhei) merge?
* [Feature #12586] Hash#sample (shyouhei)
* [Feature #12553] IO.readlines(filename, chomp: true) (shyouhei)
* [Bug #12548] Rounding modes inconsistency between round versus sprintf (shyouhei) maybe `round(mode: "even")` or something like that?
* [Feature #12574] Remove TRUE, FALSE, and NIL (shyouhei)
* [Feature #12596] Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
* [Feature #12455] Add a way for class String to determine whether it has only numbers / digits or not (shyouhei)
* Rope String progress report

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

(Additional explanation is welcome because we can't ask about it immediately)

* [Feature #12086] using: option for instance_eval etc. (shugo)
  * Can I commit it?
* Non-support package distribution for debian/centos (sorah)
  * https://sorah.jp/packaging/debian/
  * Should we do it?(hsbt)

# Log

attendees: shyouhei, hsbt, matz, yuki24, nobu, mrkn, akr, ko1

## Next meeting:

- 8/9 (Tue)
- At MoneyForward, Inc.

## Non-support package distribution for debian/centos (sorah)

- sorah:

- It would be useful if binary packages for latest/preview ruby versions were available for some distros. But it would take extra cost to release if we supported them officially.
- For example, I’m maintaining a Debian package for ruby for my company, and recently moved and open sourced [https://github.com/sorah-rbpkg](https://github.com/sorah-rbpkg). I’m currently hosting a debian repository in S3 and upload a deb file to GitHub. I guess other committers are maintaining binary packages for latest releases in-house.
- Distributing a large file could have bandwidth cost. For Ruby project, we have a Fastly (CDN) account with the OSS plan, so we can use CDN for free.
- So some committers would be happy if they can use our Fastly account. To do so, I think a distributed package should be distributed by the Ruby core team, not an individual committer.
- But as I wrote earlier, it would take extra cost to release it as supported. I don’t think it’s good to require extra “sync” for all binary package maintainers when releasing new package. [Like tier-N (N>0) platform,](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/SupportedPlatforms) can we release binary packages w/o full support?
- I’m not clear what “support” means, but if we distribute binary packages, we will see some bug reports on a package on bugs.r-l.o. We should decide how we would respond to them.

- it’s a good idea to have such a package.

- akr: usage of CDN shall be restricted to the ruby itself, no other utilities.

- hsbt: but is it official?
- mrkn: call it “experimental”
- matz: make it clear that a member of core committers is maintaining this for their own.

## About 2.4 timeframe

- Call for Feature Proposals? (shyouhei)

- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24)

- Lets do it. (shyouhei)

- deadline 2016/08/09

- ToDo

- [[Feature #8526]](https://bugs.ruby-lang.org/issues/8526) gemify tk (hsbt)

## Carry-over from previous meeting(s)

- [[Misc #12283]](https://bugs.ruby-lang.org/issues/12283) Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

- naruse: no progress.
- Martin: PLEASE DO!

- [[Feature #12299]](https://bugs.ruby-lang.org/issues/12299) Add Warning module for customized warning handling (jeremyevans)

- no progress this month.
- \---- update: read again.
- matz: I read the new patch. the implementation seems fine (not sure about the constant name, but the clear separation between core and lib is good).  Is Daniel OK wih this?  I don’t have strong negative feeling to it.

- [[Feature #12300]](https://bugs.ruby-lang.org/issues/12300) Allow Object#clone to take freeze: false keyword argument to not freeze the clone (jeremyevans)

- shyouhei: no objection.
- nobu: me too.
- akr: this one is preferrable over [#12092](https://bugs.ruby-lang.org/issues/12092)
- matz: sounds reasonable.

- [[Feature #11090]](https://bugs.ruby-lang.org/issues/11090) Enumerable#each_uniq and #each_uniq_by (shyouhei)

- akr: the definition of uniq is not clear here.
- Martin: where is the difference between each_uniq and each.uniq ?
- mrkn: Enumerable has no #uniq now.  Isn’t this a problem?
- matz: Enumerable#uniq sounds reasonable, also Lasy#uniq.

- [[Feature #12275]](https://bugs.ruby-lang.org/issues/12275) String unescape (shyouhei) lets limit the scope to "invert #dump's effect"

- akr: thing like this is a good thing to have.
- Martin: is there a in-core routine to do this already?
- naruse: no.
- akr: name? why not undump ?

- [[Feature #12345]](https://bugs.ruby-lang.org/issues/12345) A module's private constants are given with Module#constant(false) (shyouhei) intended behaviour?

- shyouhei: is this a bug?
- matz: yes.

- [[Feature #12138]](https://bugs.ruby-lang.org/issues/12138) Support Kernel#load_with_env (shyouhei)

- shyouhei: I understand the needs of such feature, but not this name.
- ko1: maybe the OP wants C’s #include<...> like token pasting?
- matz: this name is NG, the passed context info is not sure, and use case?

- [[Feature #12334]](https://bugs.ruby-lang.org/issues/12334) Final/Readonly Support for Fields / Instance Variables (shyouhei)

- shyouhei: in fact it enables some sort of optimizations
- ko1: we should focus to the feature as a language, not implementation.

- [[Feature #12350]](https://bugs.ruby-lang.org/issues/12350) Introduce Array#find! that raises an error if element not found (shyouhei)

- naruse: why not fetch ?
- akr: find and fetch are different in a way they are called.
- matz: open to such feature but find! naming is wrong.

- [[Feature #12360]](https://bugs.ruby-lang.org/issues/12360) More useful return values from bang methods (shyouhei)

- matz: returning nil for bang methods are intentional; wanted to forbid method chain for bang methods.
- akr: is it more “useful”? Enough to break backwards compat?

## From attendees

- [[Misc #12529]](https://bugs.ruby-lang.org/issues/12529) LEGAL file covering all the license information within Ruby (shyouhei)

- Martin: Unicode-related files needs updates.  For other files, the LEGAL file should be updated.

- naruse: GB2312 is used by EUC-CN

- hsbt: I’ll take care.

- [[Feature #8526]](https://bugs.ruby-lang.org/issues/8526) gemify tk (hsbt)

- shyouhei: is there any contra?
- akr: what about「let us tentatively gemify; please revert if there is any problem」

- [[Feature #3187]](https://bugs.ruby-lang.org/issues/3187) Allow dynamic Fiber stack size (shyouhei)

- ko1: it seems they implemented Thread.new(stack_size: nnn)
- matz: but it’s Fiber.
- ko1: yes but Fiber and Thread are API-compatible now.  Should we break it?
- matz: I prefer Rubinius way (Fiber.new to eat the kwarg).

- [[Feature #12543]](https://bugs.ruby-lang.org/issues/12543) explicit tail call syntax: foo() then return (shyouhei)

- naruse: why not tail call by deault?
- akr: ruby -O0 to disable tail call?
- ko1: this request is different.  the OP wants to ensure tail call.
- akr: there are cases when a C function is unable to tail-call.  On such situation how should the ensure works?
- ko1: In current implementation a fallback trampoline is prepared aside.
- matz: I can accept tail call as transparent optimization but should we encourage such thing?
- Martin: is there any language where one have to explicitly state “this is a tail call”?

- [[Feature #11813]](https://bugs.ruby-lang.org/issues/11813) Extend safe navigation operator for [] and []= with syntax sugar (shyouhei)

- rejected

- [[Feature #12589]](https://bugs.ruby-lang.org/issues/12589) VM performance improvement proposal (shyouhei)

- ko1: Shouldn’t we call him?
- matz: RA might be able to grant.

- [[Feature #12481]](https://bugs.ruby-lang.org/issues/12481) Add Array#view to allow opt-in copy-on-write sharing of Array contents (shyouhei)

- matz: why Array.new(0) then view?  Why not ary1.view…
- nurse: possible use case might be Array#each_cons(3) and such.
- akr: one can implement this on top of #replace
- matz: what use case?
- nobu: should this be mutatable?
- mrkn: seems not because of the subject’s containing “CoW”.

- [[Feature #12057]](https://bugs.ruby-lang.org/issues/12057) Allow methods with yield to be called without a block (shyouhei)

- akr: we already have Enumerator.
- akr: maybe this is automation of enum_for(__method__) unless block_given?

- [[Feature #12297]](https://bugs.ruby-lang.org/issues/12297) Ruby stdlib date can parse non-existent date with year 0 (shyouhei)

- Wasn't this intentional? cf [https://en.wikipedia.org/wiki/0_(year)](https://en.wikipedia.org/wiki/0_(year))
- [http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-dev/10241](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-dev/10241)

- [[Feature #12461]](https://bugs.ruby-lang.org/issues/12461) Hash & keys to make subset. (shyouhei)

- https://bugs.ruby-lang.org/issues/8499

- [[Feature #12515]](https://bugs.ruby-lang.org/issues/12515) Create "Boolean" superclass of TrueClass / FalseClass (shyouhei)

- hmm…
- akr: what’s useful with this?

- [[Feature #12512]](https://bugs.ruby-lang.org/issues/12512) Import Hash#transform_values and its destructive version from ActiveSupport (shyouhei, mrkn)

- shyouhei: no one is against the feature itself but name.
- akr: should this yield what? → it seems values only.
- mrkn: I’d like to have both key and value
- akr: it seems the usage being hash.transform_values(&:to_s) or such, on such assumption value-only usage seems reasonable.
- matz: the naming doesn’t charm me.
- akr: what about #map_v?

- [[Feature #10208]](https://bugs.ruby-lang.org/issues/10208) Passing block to Enumerable#to_h (shyouhei)

- matz: I don’t like the name.
- shyouhei: knu proposes #to_h again.
- nobu: there is no other to_\* method that takes a block.
- ko1: what about collect_\*?
- matz: collect implies Array because it collects something.

- [[Feature #11741]](https://bugs.ruby-lang.org/issues/11741) Migrate Ruby to Git from Subversion (shyouhei) Situation?

- not updated.

- [[Feature #12463]](https://bugs.ruby-lang.org/issues/12463) ruby lacks plus-plus (shyouhei)

- shyouhei: explained.
- ko1 is going to comment

- [[Feature #12455]](https://bugs.ruby-lang.org/issues/12455) Add a way for class String to determine whether it has only numbers / digits or not (shyouhei)

- nobu: use case?
- zzak: there is no constructor method that takes arguments like this.

- [[Bug #12577]](https://bugs.ruby-lang.org/issues/12577) Is '$' punctuation or not? Inconsistency between us-ascii and UTF-8 (duerst)

- naruse: this is the difference between POSIX’s ispunct(3) and Unicode’s definition.
- Matz: we can do nothing to it.  This is a matter of Unicode consortium versus POSIX group.
- cf: [http://pubs.opengroup.org/onlinepubs/9699919799/functions/ispunct.html](http://pubs.opengroup.org/onlinepubs/9699919799/functions/ispunct.html)

- [[Feature #12546]](https://bugs.ruby-lang.org/issues/12546) Remove UnicodeNormalize::UNICODE_VERSION (duerst)

- Matz: OK.

- [[Bug #12547]](https://bugs.ruby-lang.org/issues/12547) Remove ONIG_UNICODE_VERSION_... in enc/unicode/case-folding.rb, casefold.h (duerst)

- Martin: why is it?
- nobu: I’d like to check unicode version of each generated file.
- akr: I don’t understand.
- Martin: why the C constant everywhere?
- nobu: we should check in-core Unicode version in enc/unicode.o and lib/unicode_normalize propagated from common.mk (or command line) each other.

- [[Bug #12597]](https://bugs.ruby-lang.org/issues/12597) Produce test failure if some tests cannot be executed due to lacking data (duerst)

- nobu: this should be skip instead, not failure.
- akr: skip is silently discarded so not helpful.
- nobu: false. skip says something when it has message argument.

- [[Feature #12386]](https://bugs.ruby-lang.org/issues/12386) Move definition of ONIG_CASE_MAPPING compilation switch outside onigumo files (duerst)

- Martin: I’d like to ask nobu where is a proper place to define this macro.
- naruse: you can simply delete this #ifdef.  This part is ruby-specitic.

- [[Feature #12578]](https://bugs.ruby-lang.org/issues/12578) Instance Variables Assigned In parameters ( ala Crystal? ) (duerst)

- nobu: instance variables only? what about global variables?
- mrkn: block variable also?
- matz: I’m negative.

- [[Feature #12419]](https://bugs.ruby-lang.org/issues/12419) Improve String#dump for Unicode output (from "\\u{130}" to "\\u0130") (duerst)

- Matz: accepted

## From non-attendees

- [[Feature #12086]](https://bugs.ruby-lang.org/issues/12086) using: option for instance_eval etc. (shugo)

- Can I commit it?
- matz: Charles Nutter must be consulted before committing this.

## Rope progress (ko1)

- merged to String
- [https://github.com/spinute/ruby/commits/implement_ropestring](https://github.com/spinute/ruby/commits/implement_ropestring)
- better than String#+, comparable to String#concat
