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
# Next meeting:

- when 7 Sept., 14:00 〜
- At @tagomoris house?

## Doorkeeperhq account upgrade[¶](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20160809Japan%23Doorkeeperhq-account-upgrade&sa=D&source=editors&ust=1686086875971719&usg=AOvVaw21QOfqXD8V3e3sX7tmFtEX)

We need a paypal account to connect to cruby.doorkeeper.jp (shyouhei)

- shyouhei: we need payment account to continue using doorkeeper.
- naruse: do we need to use it?
- matz: meeting place should be sufficicent.  Doorkeeper is a bit over-spec.
- shyouhei: alternative?
- naruse: wiki?
- ko1: isn’t it OK to continue using doorkeeper to support it?
- naruse: I’ll try redmine.

- [http://ruby.connpass.com/event/37719/](https://www.google.com/url?q=http://ruby.connpass.com/event/37719/&sa=D&source=editors&ust=1686086875972724&usg=AOvVaw1wXJm1S3_xfwJxYoAjUdug)

## About 2.4 timeframe

- Read through proposals
- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24&sa=D&source=editors&ust=1686086875973207&usg=AOvVaw0mf9vYFIjJiHK7MSVpTqzJ)
- [http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/76640](https://www.google.com/url?q=http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/76640&sa=D&source=editors&ust=1686086875973478&usg=AOvVaw03uLiJ0_i8wzZvbWPjfUtW)

- [https://bugs.ruby-lang.org/issues/6265](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6265&sa=D&source=editors&ust=1686086875973712&usg=AOvVaw13HTA2lMkyK60JA5u1-y3Z)

- It breaks tests.
- It’s difficult to introduce in 2.4.

- [https://bugs.ruby-lang.org/issues/4840](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4840&sa=D&source=editors&ust=1686086875974065&usg=AOvVaw0i7dEp-PxeLl-_mHdDAA6S)

- nobu tried this one before but not successful for technical reasons.

- [https://bugs.ruby-lang.org/issues/8643](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8643&sa=D&source=editors&ust=1686086875974353&usg=AOvVaw2sOP7RxN0hhS9CHBu2wlSE)

- shouldn’t this be an ERB matter?
- It is not that easy to modify ERb’s way to handle local variable to directly use a hash because ERb needs to use local variable as a ruby-code fragment.

- [https://bugs.ruby-lang.org/issues/6559](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6559&sa=D&source=editors&ust=1686086875974719&usg=AOvVaw1VJOzdjVdrTDWSjhtP2NtE)

- naruse: I’ll nudge the assignee

## Carry-over from previous meeting(s)

- \[Misc [#12283](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12283&sa=D&source=editors&ust=1686086875975207&usg=AOvVaw1MdTuIhIrMkOXKR03vzN7-)\] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

- naruse: no progress

- \[Bug [#12466](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12466&sa=D&source=editors&ust=1686086875975535&usg=AOvVaw2NLAzhNtz2vgF-ueKGmaEq)\] Enumerable should yield multiple values when possible (shyouhei)

- nobu: not easy
- Matz: reject

- \[Bug [#12402](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12402&sa=D&source=editors&ust=1686086875975927&usg=AOvVaw1-bzBgndH12AjjqL4b5fio)\] Inline rescue behavior inconsistent for method calls with arguments and assignment (shyouhei)

- ko1: var1 is surprising.
- nobu: it is interpreted as “(var1 = Date.parse var1) rescue nil”
- mrkn: in which way should we fix this, if we consider this as a bug?
- Matz: Smells like a bug to me.
- nobu: What happens when you combine rescue\_mod with massign?
- assigning to nobu.

- \[Bug [#12489](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12489&sa=D&source=editors&ust=1686086875976728&usg=AOvVaw3nF71TiXwn6PKcwlAajc8T)\] hppa problems on Debian GNU/Linux (shyouhei) who to look at it?

- nobu: it’s hard to track without actual environment.
- naruse: make it feedback with no assignee.
- nobu: the fail log lacks C level backtrace.

- \[Bug [#12497](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12497&sa=D&source=editors&ust=1686086875977178&usg=AOvVaw059AZVga7m4v2NjuJQy-61)\] GMP version of divmod may be slower(shyouhei)

- assign akr

- \[Feature [#12490](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12490&sa=D&source=editors&ust=1686086875977508&usg=AOvVaw1gU55p5MeXMbsTFyk4NjLA)\] Remove warning on shadowing block params(shyouhei)

- naruse: negative.  shadowing itself is a bad idea and should be warned.
- Matz: reject.

- \[Feature [#3511](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/3511&sa=D&source=editors&ust=1686086875977890&usg=AOvVaw1IsnPwm_1df-NjG_9U6Y54)\] rb\_path\_to\_class should call custom const\_defined? methods (shyouhei)

- akr: this is dangerous to implement.
- ko1: is this backward compatible? akr: it seems.
- Matz: It seems it hurts than it gains.

- \[Feature [#12508](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12508&sa=D&source=editors&ust=1686086875978320&usg=AOvVaw3dg85BNBRkDWqNDwq4b6SU)\] Integer#mod\_pow(shyouhei)

- Matz: 2-argument version of pow is seen in other language (e.g. python)
- akr: there is no pow method in Ruby sadly
- nobu: pattern match \*\* and % to unify instructions?
- nobu: why not a separate gem?
- akr: it can even be faster in pure-ruby so gem can be a way
- mrkn: optimized algorithm is described in Wikipedia.
- naruse: what about introducing #pow
- mrkn: Julia has powermod [http://docs.julialang.org/en/latest/stdlib/math/#Base.powermod](https://www.google.com/url?q=http://docs.julialang.org/en/latest/stdlib/math/%23Base.powermod&sa=D&source=editors&ust=1686086875979039&usg=AOvVaw1zX0mxaGlvOcL06yl8MO0U)
- Matz: accept Integer#pow

- \[Feature [#12521](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12521&sa=D&source=editors&ust=1686086875979347&usg=AOvVaw2GPgzCzOdIT2Ec_MJuP2f_)\] Syntax for retrieving argument without removing it from double-splat catch-all(shyouhei)

- Matz: what’s good with it?  compared to taking everything with \*\*arg.

- \[Feature [#11195](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11195&sa=D&source=editors&ust=1686086875979668&usg=AOvVaw0YYlx0oaPiBVxNrFfnwaYW)\] Add "no\_proxy" parameter to Net::HTTP.new (shyouhei)

- shyouhei: would like to assign this to someone.
- akr: what about requesting a patch

- \[Feature [#12142](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12142&sa=D&source=editors&ust=1686086875980086&usg=AOvVaw3a-PsK0Q7WTDn5cLxvpO_4)\] Hash tables with open addressing (shyouhei) merge?

- ko1: evaluation undergoing.

- \[Feature [#12586](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12586&sa=D&source=editors&ust=1686086875980388&usg=AOvVaw05iXi0zCHwLXUmzaZ9hMvQ)\] Hash#sample (shyouhei)

- mrkn: use case?
- naruse: doesn’t hash\[hash.keys.sample\] make sense?
- mrkn: I want to see a real-world use case.  Is there a need of to\_h?

- \[Feature [#12553](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12553&sa=D&source=editors&ust=1686086875980824&usg=AOvVaw38eBC2v0DRPgDtdjQViwLL)\] IO.readlines(filename, chomp: true) (shyouhei)

- shyouhei: should how the “chomp” work?  Does this work with $/ ?
- mrkn: readlines itself takes argument to reflect $/
- naruse: chomp should work the same way like #chomp
- Matz: which methods are affected?
- naruse: IO.readlines, IO.foreach, IO#each\_line
- Matz: accept for those three

- \[Bug [#12548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12548&sa=D&source=editors&ust=1686086875981499&usg=AOvVaw1wxZcpklQMf_LqwD_9AKVg)\] Rounding modes inconsistency between round versus sprintf (shyouhei) maybe round(mode: "even") or something like that?

- shyouhei: should we “fix” this by modifying #round to be banker’s tie-break, or to add mode to it?
- akr: I prefer banker’s round
- nobu: this can be backward incompatible; tests fail for rational and mathn.
- Matz: I have never needed to specify rounding mode
- conclusion: change default rounding mode to even.

- \[Feature [#12596](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12596&sa=D&source=editors&ust=1686086875982103&usg=AOvVaw0y7sVLiwjnmfD5_4R9XUJj)\] Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
- \[Feature [#12573](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12573&sa=D&source=editors&ust=1686086875982373&usg=AOvVaw3RRBmLHXYPRbs-s0g8acww)\] Introduce a straightforward way to discover whether a process is running (nobu)

- Matz : I undertand the mood but it seems not useful

- \[Bug [#9569](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9569&sa=D&source=editors&ust=1686086875982716&usg=AOvVaw3tIiBBVAhHvn0sNYHuNB_i)\] SecureRandom should try /dev/urandom first (shyouhei) I'd like to hear other opinions.
- \[Feature [#7882](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7882&sa=D&source=editors&ust=1686086875983054&usg=AOvVaw2v7kTvElVtvjHPfekemPMZ)\] Allow rescue/else/ensure in do..end

- matz: will respond

## From attendees

- \[Feature [#11818](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11818&sa=D&source=editors&ust=1686086875983690&usg=AOvVaw3ieNqMKUtkqDnRyepGiLgT)\] Hash#compact (mrkn)

- Matz: accept.

- \[Feature [#3511](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/3511&sa=D&source=editors&ust=1686086875984043&usg=AOvVaw3mSs7koRz5Z2YLXS40xs34)\] rb\_path\_to\_class should call custom const\_defined? methods (shyouhei)
- \[Feature [#10098](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10098&sa=D&source=editors&ust=1686086875984285&usg=AOvVaw2lv1kH8qQURFmO8UmRU18l)\] \[PATCH\] Timing-safe string comparison for OpenSSL::HMAC (shyouhei) status?
- \[Feature [#12591](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12591&sa=D&source=editors&ust=1686086875984534&usg=AOvVaw1VlR6lmPkc7Y4VxvhY9Kx4)\] Allow ruby to either catch misspelled "ailas" statements or, possibly more accurately, be more specific in what it reports as an error to the end-user (shyouhei)
- \[Feature [#12594](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12594&sa=D&source=editors&ust=1686086875984792&usg=AOvVaw1E32APH4NppyhWZSS04Nzg)\] The class does not inherit from a module the modules that were included after the inclusion (shyouhei)
- \[Feature [#12596](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12596&sa=D&source=editors&ust=1686086875985030&usg=AOvVaw0DAtX-pVwqeQsrle8rBweT)\] Add Pathname#empty? to be consistent with Dir.empty? and File.empty? (shyouhei)
- \[Feature [#12593](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12593&sa=D&source=editors&ust=1686086875985273&usg=AOvVaw1aQs-YtXgmzvWzLUphXAOd)\] Allow compound assignements to work when destructuring arrays (shyouhei)

- matz wil respond

- \[Feature [#12602](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12602&sa=D&source=editors&ust=1686086875985578&usg=AOvVaw0ak3SwyXFdTl-hVATb-Mis)\] Add NilClass#to\_d (shyouhei)
- \[Feature [#12607](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12607&sa=D&source=editors&ust=1686086875985830&usg=AOvVaw0eOJa9gxFRXMlQiU9AT04h)\] Ruby needs an atomic integer (shyouhei)
- \[Feature [#12608](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12608&sa=D&source=editors&ust=1686086875986062&usg=AOvVaw2uuu-dxx_B_AtN0fMRrQY5)\] Proposal to replace unless in Ruby (shyouhei)

- Matz: reject

- \[Feature [#12615](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12615&sa=D&source=editors&ust=1686086875986363&usg=AOvVaw16Fx7z3HuosTISPCFnTVOS)\] Pathname#rename does not work across filesystem boundaries. (shyouhei) is this by design or...?
- \[Feature [#12624](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12624&sa=D&source=editors&ust=1686086875986602&usg=AOvVaw2sbB7D42Mm5Gx96eXx1zpK)\] !== (other) (shyouhei)

- matz: will respond

- \[Feature [#12625](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12625&sa=D&source=editors&ust=1686086875986918&usg=AOvVaw2Wq43Ls_i3EWNR8752qYTI)\] TypeError.assert, ArgumentError.assert (shyouhei)
- \[Feature [#12626](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12626&sa=D&source=editors&ust=1686086875987170&usg=AOvVaw07MhKoSnkSKRfftz4p0CiT)\] Add ceiling alias for ceil on Numeric objects (shyouhei)

- matz wil respond

- \[Feature [#12623](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12623&sa=D&source=editors&ust=1686086875987502&usg=AOvVaw3Vqi0EZm09WBYBej3JU9l1)\] rescue in blocks without begin/end (shyouhei)
- \[Feature [#12635](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12635&sa=D&source=editors&ust=1686086875987760&usg=AOvVaw080PjAwgAa9TJ25VPo40Ok)\] Shuffling/Reassigning "namespaces" more easily (shyouhei)

- matz wil respond

- \[Feature [#12374](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12374&sa=D&source=editors&ust=1686086875988077&usg=AOvVaw1YTsc1f8oM42bH_fZLRBzW)\] SingletonClass (sawa)

- ko1: this is GoF’s singleton pattern.
- sawa: I want to have a reverse of Object#singleton\_class
- akr: what use case?
- Matz: this is technically possible, but what poupose?  There is singleton\_class?

- \[Feature [#9704](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9704&sa=D&source=editors&ust=1686086875988576&usg=AOvVaw3f3tRNXCeZZf3YZFhzfQXr)\] Refinements as files instead of modules (shyouhei)
- \[Feature [#12637](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12637&sa=D&source=editors&ust=1686086875988829&usg=AOvVaw3sdVPstqgp3H0AtwxtR2Kq)\] Unified and consistent method naming for safe and dangerous methods (shyouhei)
- \[Feature [#8643](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8643&sa=D&source=editors&ust=1686086875989080&usg=AOvVaw1fa2JbX2cFz9PMFZwNblof)\] Add Binding.from\_hash (shyouhei)
- \[Feature [#12650](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12650&sa=D&source=editors&ust=1686086875989357&usg=AOvVaw2necddIf8ljFwejO_OnKVB)\] Use UTF-8 encoding for ENV on Windows (shyouhei)
- \[Feature [#12648](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12648&sa=D&source=editors&ust=1686086875989605&usg=AOvVaw1ZPXVopdjtuSZ7oHCIIu4l)\] Enumerable#sort\_by with descending option (shyouhei)

- akr: sort\_by with multiple arguments is odd.
- shyouhei: I sometimes need secondary sort key
- Matz: I think such complex sort shall be another method.
- akr: I think sort\_by with descending option is okay
- naruse: isn’t reverse\_sort\_by OK?
- Matz: I understand the needs of reverse sort.
- sawa: I can settle with sort\_by.reverse

- \[Feature [#12574](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12574&sa=D&source=editors&ust=1686086875990293&usg=AOvVaw0wbUkCaFG33_j7d6mDmFQ4)\] Remove TRUE, FALSE, and NIL (shyouhei)
- \[Feature [#12655](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12655&sa=D&source=editors&ust=1686086875990545&usg=AOvVaw3YwZHn6FjLkDM4WiZwqkno)\] accessing a method visibility (shyouhei)
- \[Feature [#9612](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9612&sa=D&source=editors&ust=1686086875990794&usg=AOvVaw0rJrTvNN-5c23cmQ_5PHYZ)\] Gemify OpenSSL (hsbt)
- \[Feature [#8539](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8539&sa=D&source=editors&ust=1686086875991042&usg=AOvVaw0yQF4qUCM0U1iUixON4s0V)\] Unbundle ext/tk (hsbt)
- \[Feature [#12512](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12512&sa=D&source=editors&ust=1686086875991296&usg=AOvVaw1Cn7oVdKZodapIsHv4EGbt)\] Import Hash#transform\_values and its destructive version from ActiveSupport

- Matz: accept Enumerable#map\_v
- Matz: not now for map\_k, map\_kv

## From non-attendees

- \[Feature [#12299](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12299&sa=D&source=editors&ust=1686086875992061&usg=AOvVaw14ELf7L45DpD7cSFdgvKRb)\] Add Warning module for customized warning handling (jeremyevans)

- Matz: accept the fundamental concept (library needs more consideration)

- \[Feature [#10594](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10594&sa=D&source=editors&ust=1686086875992422&usg=AOvVaw35GgzqTvml5UbZ-bwVRVXf)\] Comparable#clamp (nerdinand)

- shyouhei: shoudl this be a method of Comparable?
- nobu: can be a method of Range
- akr: what about exclude\_end
- naruse: how about providing two:

- Comparable#clamp(range)
- Comparable#clamp(begin, end)

- use case?
- Matz: accept the 2-ary version.
