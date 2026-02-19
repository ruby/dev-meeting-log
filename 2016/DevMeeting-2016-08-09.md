---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-08-09

Date: 2016/08/09 (Tue)
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

## Doorkeeperhq account upgrade

We need a paypal account to connect to cruby.doorkeeper.jp (shyouhei)

## About 2.4 timeframe

* Read through proposals (shyouhei)

## Carry-over from previous meeting(s)

* [Misc #12283] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?
* [Bug #12466] Enumerable should yield multiple values when possible (shyouhei)
* [Bug #12402] Inline rescue behavior inconsistent for method calls with arguments and assignment (shyouhei)
* [Bug #12489] hppa problems on Debian GNU/Linux (shyouhei) who to look at it?
* [Bug #12497] GMP version of divmod may be slower(shyouhei)
* [Feature #12490] Remove warning on shadowing block params(shyouhei)
* [Feature #3511] rb_path_to_class should call custom const_defined? methods (shyouhei)
* [Feature #12508] Integer#mod_pow(shyouhei)
* [Feature #12521] Syntax for retrieving argument without removing it from double-splat catch-all(shyouhei)
* [Feature #11195] Add "no_proxy" parameter to Net::HTTP.new (shyouhei)
* [Feature #12142] Hash tables with open addressing (shyouhei) merge?
* [Feature #12586] Hash#sample (shyouhei)
* [Feature #12553] IO.readlines(filename, chomp: true) (shyouhei)
* [Bug #12548] Rounding modes inconsistency between round versus sprintf (shyouhei) maybe `round(mode: "even")` or something like that?
* [Feature #12596] Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
* [Feature #12573] Introduce a straightforward way to discover whether a process is running (nobu)
* [Bug #9569] `SecureRandom` should try `/dev/urandom` first (shyouhei) I'd like to hear other opinions.

## From attendees

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #11818] `Hash#compact` (mrkn)
* [Feature #3511] rb_path_to_class should call custom const_defined? methods (shyouhei)
* [Feature #10098] [PATCH] Timing-safe string comparison for OpenSSL::HMAC (shyouhei) status?
* [Feature #12591] Allow ruby to either catch misspelled "ailas" statements or, possibly more accurately, be more specific in what it reports as an error to the end-user (shyouhei)
* [Feature #12594] The class does not inherit from a module the modules that were included after the inclusion (shyouhei)
* [Feature #12596] Add `Pathname#empty?` to be consistent with `Dir.empty?` and `File.empty?` (shyouhei)
* [Feature #12593] Allow compound assignments to work when destructuring arrays (shyouhei)
* [Feature #12602] Add `NilClass#to_d` (shyouhei)
* [Feature #12607] Ruby needs an atomic integer (shyouhei)
* [Feature #12608] Proposal to replace unless in Ruby (shyouhei)
* [Feature #12615] `Pathname#rename` does not work across filesystem boundaries. (shyouhei) is this by design or...?
* [Feature #12624] !== (other) (shyouhei)
* [Feature #12625] `TypeError.assert`, `ArgumentError.assert` (shyouhei)
* [Feature #12626] Add `ceiling` alias for `ceil` on `Numeric` objects (shyouhei)
* [Feature #12623] `rescue` in blocks without `begin`/`end` (shyouhei)
* [Feature #12635] Shuffling/Reassigning "namespaces" more easily (shyouhei)
* [Feature #12374] SingletonClass (shyouhei)
* [Feature #9704] Refinements as files instead of modules (shyouhei)
* [Feature #12637] Unified and consistent method naming for safe and dangerous methods (shyouhei)
* [Feature #8643] Add `Binding.from_hash` (shyouhei)
* [Feature #12650] Use UTF-8 encoding for `ENV` on Windows (shyouhei)
* [Feature #12648] `Enumerable#sort_by` with descending option (shyouhei)
* [Feature #12574] Remove `TRUE`, `FALSE`, and `NIL` (shyouhei)
* [Feature #12655] accessing a method visibility (shyouhei)
* [Feature #9612] Gemify OpenSSL (hsbt)
* [Feature #8539] Unbundle ext/tk (hsbt)
* [Feature #12512] Import `Hash#transform_values` and its destructive version from ActiveSupport

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #12299] Add Warning module for customized warning handling (jeremyevans)
* [Feature #10594] `Comparable#clamp` (nerdinand)

# Log
## Next meeting:

- when 7 Sept., 14:00 〜
- At @tagomoris house?

## Doorkeeperhq account upgrade[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20160809Japan#Doorkeeperhq-account-upgrade)

We need a paypal account to connect to cruby.doorkeeper.jp (shyouhei)

- shyouhei: we need payment account to continue using doorkeeper.
- naruse: do we need to use it?
- matz: meeting place should be sufficicent.  Doorkeeper is a bit over-spec.
- shyouhei: alternative?
- naruse: wiki?
- ko1: isn’t it OK to continue using doorkeeper to support it?
- naruse: I’ll try redmine.

- [http://ruby.connpass.com/event/37719/](http://ruby.connpass.com/event/37719/)

## About 2.4 timeframe

- Read through proposals
- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24)
- [http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/76640](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/76640)

- [https://bugs.ruby-lang.org/issues/6265](https://bugs.ruby-lang.org/issues/6265)

- It breaks tests.
- It’s difficult to introduce in 2.4.

- [https://bugs.ruby-lang.org/issues/4840](https://bugs.ruby-lang.org/issues/4840)

- nobu tried this one before but not successful for technical reasons.

- [https://bugs.ruby-lang.org/issues/8643](https://bugs.ruby-lang.org/issues/8643)

- shouldn’t this be an ERB matter?
- It is not that easy to modify ERb’s way to handle local variable to directly use a hash because ERb needs to use local variable as a ruby-code fragment.

- [https://bugs.ruby-lang.org/issues/6559](https://bugs.ruby-lang.org/issues/6559)

- naruse: I’ll nudge the assignee

## Carry-over from previous meeting(s)

- [[Misc #12283]](https://bugs.ruby-lang.org/issues/12283) Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

- naruse: no progress

- [[Bug #12466]](https://bugs.ruby-lang.org/issues/12466) Enumerable should yield multiple values when possible (shyouhei)

- nobu: not easy
- Matz: reject

- [[Bug #12402]](https://bugs.ruby-lang.org/issues/12402) Inline rescue behavior inconsistent for method calls with arguments and assignment (shyouhei)

- ko1: var1 is surprising.
- nobu: it is interpreted as “(var1 = Date.parse var1) rescue nil”
- mrkn: in which way should we fix this, if we consider this as a bug?
- Matz: Smells like a bug to me.
- nobu: What happens when you combine rescue\_mod with massign?
- assigning to nobu.

- [[Bug #12489]](https://bugs.ruby-lang.org/issues/12489) hppa problems on Debian GNU/Linux (shyouhei) who to look at it?

- nobu: it’s hard to track without actual environment.
- naruse: make it feedback with no assignee.
- nobu: the fail log lacks C level backtrace.

- [[Bug #12497]](https://bugs.ruby-lang.org/issues/12497) GMP version of divmod may be slower(shyouhei)

- assign akr

- [[Feature #12490]](https://bugs.ruby-lang.org/issues/12490) Remove warning on shadowing block params(shyouhei)

- naruse: negative.  shadowing itself is a bad idea and should be warned.
- Matz: reject.

- [[Feature #3511]](https://bugs.ruby-lang.org/issues/3511) rb\_path\_to\_class should call custom const\_defined? methods (shyouhei)

- akr: this is dangerous to implement.
- ko1: is this backward compatible? akr: it seems.
- Matz: It seems it hurts than it gains.

- [[Feature #12508]](https://bugs.ruby-lang.org/issues/12508) Integer#mod\_pow(shyouhei)

- Matz: 2-argument version of pow is seen in other language (e.g. python)
- akr: there is no pow method in Ruby sadly
- nobu: pattern match \*\* and % to unify instructions?
- nobu: why not a separate gem?
- akr: it can even be faster in pure-ruby so gem can be a way
- mrkn: optimized algorithm is described in Wikipedia.
- naruse: what about introducing #pow
- mrkn: Julia has powermod [http://docs.julialang.org/en/latest/stdlib/math/#Base.powermod](http://docs.julialang.org/en/latest/stdlib/math/#Base.powermod)
- Matz: accept Integer#pow

- [[Feature #12521]](https://bugs.ruby-lang.org/issues/12521) Syntax for retrieving argument without removing it from double-splat catch-all(shyouhei)

- Matz: what’s good with it?  compared to taking everything with \*\*arg.

- [[Feature #11195]](https://bugs.ruby-lang.org/issues/11195) Add "no\_proxy" parameter to Net::HTTP.new (shyouhei)

- shyouhei: would like to assign this to someone.
- akr: what about requesting a patch

- [[Feature #12142]](https://bugs.ruby-lang.org/issues/12142) Hash tables with open addressing (shyouhei) merge?

- ko1: evaluation undergoing.

- [[Feature #12586]](https://bugs.ruby-lang.org/issues/12586) Hash#sample (shyouhei)

- mrkn: use case?
- naruse: doesn’t hash\[hash.keys.sample\] make sense?
- mrkn: I want to see a real-world use case.  Is there a need of to\_h?

- [[Feature #12553]](https://bugs.ruby-lang.org/issues/12553) IO.readlines(filename, chomp: true) (shyouhei)

- shyouhei: should how the “chomp” work?  Does this work with $/ ?
- mrkn: readlines itself takes argument to reflect $/
- naruse: chomp should work the same way like #chomp
- Matz: which methods are affected?
- naruse: IO.readlines, IO.foreach, IO#each\_line
- Matz: accept for those three

- [[Bug #12548]](https://bugs.ruby-lang.org/issues/12548) Rounding modes inconsistency between round versus sprintf (shyouhei) maybe round(mode: "even") or something like that?

- shyouhei: should we “fix” this by modifying #round to be banker’s tie-break, or to add mode to it?
- akr: I prefer banker’s round
- nobu: this can be backward incompatible; tests fail for rational and mathn.
- Matz: I have never needed to specify rounding mode
- conclusion: change default rounding mode to even.

- [[Feature #12596]](https://bugs.ruby-lang.org/issues/12596) Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
- [[Feature #12573]](https://bugs.ruby-lang.org/issues/12573) Introduce a straightforward way to discover whether a process is running (nobu)

- Matz : I undertand the mood but it seems not useful

- [[Bug #9569]](https://bugs.ruby-lang.org/issues/9569) SecureRandom should try /dev/urandom first (shyouhei) I'd like to hear other opinions.
- [[Feature #7882]](https://bugs.ruby-lang.org/issues/7882) Allow rescue/else/ensure in do..end

- matz: will respond

## From attendees

- [[Feature #11818]](https://bugs.ruby-lang.org/issues/11818) Hash#compact (mrkn)

- Matz: accept.

- [[Feature #3511]](https://bugs.ruby-lang.org/issues/3511) rb\_path\_to\_class should call custom const\_defined? methods (shyouhei)
- [[Feature #10098]](https://bugs.ruby-lang.org/issues/10098) \[PATCH\] Timing-safe string comparison for OpenSSL::HMAC (shyouhei) status?
- [[Feature #12591]](https://bugs.ruby-lang.org/issues/12591) Allow ruby to either catch misspelled "ailas" statements or, possibly more accurately, be more specific in what it reports as an error to the end-user (shyouhei)
- [[Feature #12594]](https://bugs.ruby-lang.org/issues/12594) The class does not inherit from a module the modules that were included after the inclusion (shyouhei)
- [[Feature #12596]](https://bugs.ruby-lang.org/issues/12596) Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
- [[Feature #12593]](https://bugs.ruby-lang.org/issues/12593) Allow compound assignements to work when destructuring arrays (shyouhei)

- matz wil respond

- [[Feature #12602]](https://bugs.ruby-lang.org/issues/12602) Add NilClass#to\_d (shyouhei)
- [[Feature #12607]](https://bugs.ruby-lang.org/issues/12607) Ruby needs an atomic integer (shyouhei)
- [[Feature #12608]](https://bugs.ruby-lang.org/issues/12608) Proposal to replace unless in Ruby (shyouhei)

- Matz: reject

- [[Feature #12615]](https://bugs.ruby-lang.org/issues/12615) Pathname#rename does not work across filesystem boundaries. (shyouhei) is this by design or...?
- [[Feature #12624]](https://bugs.ruby-lang.org/issues/12624) !== (other) (shyouhei)

- matz: will respond

- [[Feature #12625]](https://bugs.ruby-lang.org/issues/12625) TypeError.assert, ArgumentError.assert (shyouhei)
- [[Feature #12626]](https://bugs.ruby-lang.org/issues/12626) Add ceiling alias for ceil on Numeric objects (shyouhei)

- matz wil respond

- [[Feature #12623]](https://bugs.ruby-lang.org/issues/12623) rescue in blocks without begin/end (shyouhei)
- [[Feature #12635]](https://bugs.ruby-lang.org/issues/12635) Shuffling/Reassigning "namespaces" more easily (shyouhei)

- matz wil respond

- [[Feature #12374]](https://bugs.ruby-lang.org/issues/12374) SingletonClass (sawa)

- ko1: this is GoF’s singleton pattern.
- sawa: I want to have a reverse of Object#singleton\_class
- akr: what use case?
- Matz: this is technically possible, but what poupose?  There is singleton\_class?

- [[Feature #9704]](https://bugs.ruby-lang.org/issues/9704) Refinements as files instead of modules (shyouhei)
- [[Feature #12637]](https://bugs.ruby-lang.org/issues/12637) Unified and consistent method naming for safe and dangerous methods (shyouhei)
- [[Feature #8643]](https://bugs.ruby-lang.org/issues/8643) Add Binding.from\_hash (shyouhei)
- [[Feature #12650]](https://bugs.ruby-lang.org/issues/12650) Use UTF-8 encoding for ENV on Windows (shyouhei)
- [[Feature #12648]](https://bugs.ruby-lang.org/issues/12648) Enumerable#sort\_by with descending option (shyouhei)

- akr: sort\_by with multiple arguments is odd.
- shyouhei: I sometimes need secondary sort key
- Matz: I think such complex sort shall be another method.
- akr: I think sort\_by with descending option is okay
- naruse: isn’t reverse\_sort\_by OK?
- Matz: I understand the needs of reverse sort.
- sawa: I can settle with sort\_by.reverse

- [[Feature #12574]](https://bugs.ruby-lang.org/issues/12574) Remove TRUE, FALSE, and NIL (shyouhei)
- [[Feature #12655]](https://bugs.ruby-lang.org/issues/12655) accessing a method visibility (shyouhei)
- [[Feature #9612]](https://bugs.ruby-lang.org/issues/9612) Gemify OpenSSL (hsbt)
- [[Feature #8539]](https://bugs.ruby-lang.org/issues/8539) Unbundle ext/tk (hsbt)
- [[Feature #12512]](https://bugs.ruby-lang.org/issues/12512) Import Hash#transform\_values and its destructive version from ActiveSupport

- Matz: accept Enumerable#map\_v
- Matz: not now for map\_k, map\_kv

## From non-attendees

- [[Feature #12299]](https://bugs.ruby-lang.org/issues/12299) Add Warning module for customized warning handling (jeremyevans)

- Matz: accept the fundamental concept (library needs more consideration)

- [[Feature #10594]](https://bugs.ruby-lang.org/issues/10594) Comparable#clamp (nerdinand)

- shyouhei: shoudl this be a method of Comparable?
- nobu: can be a method of Range
- akr: what about exclude\_end
- naruse: how about providing two:

- Comparable#clamp(range)
- Comparable#clamp(begin, end)

- use case?
- Matz: accept the 2-ary version.
