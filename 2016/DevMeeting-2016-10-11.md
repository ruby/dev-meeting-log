---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-10-11

Date: 2016/10/11 (Tue)
Time: 14:00- 18:00 (JST)
Place: SFDC Japan, Tokyo
connpass: http://ruby.connpass.com/event/39930/

Attendees/参加者: duerst (Martin Dürst), shyouhei, hsbt, mrkn
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

## About 2.4 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24

## (From RubyKaigi admin) for Keynote speakers

* Please send us keynote speaker's Shinkansen receipt (shyouhei)

## Carry-over from previous meeting(s)

* [Misc #12283] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?
* [Feature #12695] File.expand_path should resolve ~/ using /etc/passwd when HOME is not set (shyouhei) conclusion?
* [Feature #12733] Bundle bundler to ruby core (shyouhei)
* [Feature #6647] Exceptions raised in threads should be logged (shyouhei) Look at [\[ruby-core:77259\\]](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/77259)
* [Feature #12744] Add str.reverse_each_char and str.reverse_chars (shyouhei) conclusion?
* [Bug #11195] Add "no_proxy" parameter to Net::HTTP.new (shyouhei) Roman Bigler volunteers.
* [Feature #12700] regexg heredoc support (shyouhei) the OP _might_ perhaps have real use case.
* [Bug #12594] The class does not inherit from a module the modules that were included after the inclusion (shyouhei)

## From attendees

* [Bug #12649] DateTime method calls hang (shyouhei) seems like a real bug?
* [Bug #12652] For Dir.home encode passed user (shyouhei) seems like a real bug?
* [Bug #12509] Using qsort_s in mingw-w64 causes failures (shyouhei) who to handle this?
* [Bug #8996] pthread_mutex_lock EINVAL (shyouhei) seems like a real bug?
* [Bug #12776] Flaky test case: TestThread#test_thread_name (shyouhei)  seems like a real bug, or....?
* [Feature #12656] Expand short paths with File.expand_path (shyouhei)
* [Bug #7877] E::Lazy#with_index should be lazy (shyouhei)
* [Feature #12664] Multiline pretty-printing of multiline strings (shyouhei)
* [Feature #4897] Define Math::TAU and BigMath.TAU. The "true" circle constant, Tau=2*Pi. See http://tauday.com/ (shyouhei)
* [Bug #12674] io/wait: not handling the case when the socket is closed before doing wait_readable/writable with timeout (shyouhei) might be a design issue?
* [Bug #12678] No way to set a timeout for TLS handshake when using Net::SMTP (shyouhei) should who handle this?
* [Bug #6783] Infinite loop in inspect, not overriding inspect, to_s, and no known circular references. Stepping into inspect in debugger locks it up with 100% CPU. (shyouhei)
* [Bug #10222] require_relative and require should be compatible with each other (shyouhei)
* [Bug #12705] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei)
* [Bug #4537] Incorrectly creating private method via attr_accessor (shyouhei) cf. [Bug #9005]
* [Feature #12721] public_module_function (shyouhei)
* [Feature #12732] An option to pass to `Integer`, `Float`, to return `nil` instead of raise an exception (shyouhei)
* [Feature #12745] String#(g)sub(!) should pass a MatchData to the block, not a String (shyouhei)
* [Feature #12747] Add TracePoint#callee_id (shyouhei)
* [Feature #12755] optimize instruction sequence (shyouhei)
* [Feature #12753] Useful operator to check bit-flag is true or false (shyouhei)
* [Bug #12754] Want to use prepared buffer with `Array#pack` (shyouhei)
* [Feature #12752] Unpacking a value from a binary requires additional '.first' (shyouhei)
* [Feature #12746] class Array: alias .prepend to .unshift ? (shyouhei)
* [Feature #11815] Proposal for method `Array#difference` (shyouhei)
* [Feature #12770] Hash#left_merge (shyouhei)
* [Feature #12760] Optional block argument for `itself` (shyouhei)
* [Feature #12775] Random subset of array (shyouhei)
* [Feature #2172] Enumerable#chunk with no block (shyouhei)
* [Bug #10290] segfault when calling a lambda recursively after rescuing SystemStackError (shyouhei)
* [Bug #12688] Thread unsafety in autoload (shyouhei)
* [Bug #12780] BigDecimal#round returns different types depending on argument (shyouhei)
* [Feature #12786] String#casecmp? (shyouhei)
* [Feature #12612] Switch Range#=== to use cover? instead of include? (shyouhei)
* [Bug #9244] unexpected behaviour of 'require' when $LOAD_PATH gets changed (shyouhei)
* [Feature #12790] Better inspect for stdlib classes (shyouhei)
* [Feature #12719] `Struct#merge` for partial updates (shyouhei)
* [Feature #12715] Allow ruby hackers to omit having to specify class or module mandatory, if they know exactly what they want to do (shyouhei)
* [Feature #12698] Method to delete a substring by regex match (shyouhei)
* [Feature #12697] Why shouldn't Module meta programming methods be public? (shyouhei)
* [Feature #8960] Add Exception#backtrace_locations (shyouhei) another problem is issued or...?
* [Feature #12664] Multiline pretty-printing of multiline strings (shyouhei)
* [Feature #12142] Hash tables with open addressing (duerst) (we should make progress on this one because it would be a nice feature for Ruby 2.4)
* [Feature #11286] [PATCH] Add case equality arity to Enumerable's sequence predicates. (shyouhei)
* [Bug #12828] Check whether macro RUBY is okay for protecting ruby-specific onigumo extensions (duerst)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #12760] Optional block argument for `itself` (Victor Shepelev)

# Log

Date: 2016/10/11 (Tue)

Time: 14:00- 18:00 (JST)

Place: SFDC Japan, Tokyo

Attendees/参加者: ko1, naruse, akr, amatsuda, sorah, nakada, duerst (Martin Dürst), shyouhei, hsbt, mrkn

Language: mostly Japanese (sorry for non native Japanese speakers)

## Agenda

- next meeting?

- Nov. 3-4 RWC
- Nov. 5-6 (camp?)@ Shimane
- Nov. 10-12 RubyConf
- conclusion: next meeing will be some point at later half of November.

- dev-camp?

- hotel, transport, and arrangement are OK (by ruby-assn)
- seems enough people are willing to attend.

- st report (ko1)

- ko1 benchmarked 3 (Vladimir’s, FunnyFalcon’s, current one) implementations

- against kaminari-pagenated rails scaffold
- using ab

- he found most time are consumed in looking up of small tables (size <= 64).
- for such small tables, it seems everything are on cache, algorithm difference was negilible.
- lets merge (any one of them) at the camp.

## About 2.4 timeframe

- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24&sa=D&source=editors&ust=1686086930351083&usg=AOvVaw2L-Zg8RwYhuncyL23DuTct)
- no showstopper
- what about the Rational renovation?

- please nudge \_tad\_

## (From RubyKaigi admin) for Keynote speakers

- Please send us keynote speaker's Shinkansen receipt (shyouhei)

- naruse: no. I don’t have one.

## Carry-over from previous meeting(s)

- \[Misc [#12283](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12283&sa=D&source=editors&ust=1686086930352237&usg=AOvVaw3VIQxUWMRm3isocLvJ__te)\] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

- naruse: no move.
- shyouhei: I write git-style commit log.
- mrkn: me too.
- Martin: the problem is not commit log, but to auto-generate ChanegLog.
- naruse: I’ll do this on camp.

- \[Feature [#12695](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12695&sa=D&source=editors&ust=1686086930352946&usg=AOvVaw2P3EnZbXbqOoZZfJ1MjPXZ)\] File.expand\_path should resolve ~/ using /etc/passwd when HOME is not set (shyouhei) conclusion?

- waiting for matz.

- \[Feature [#12733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12733&sa=D&source=editors&ust=1686086930353321&usg=AOvVaw3vxYlQgUvtYgRIpg-bdhcb)\] Bundle bundler to ruby core (shyouhei)

- hsbt: no progress.
- hsbt: rubygems master bundles bundler by default.  It sheds some light into its darkness.
- nobu: it will be merged when rubygems side is OK.
- hsbt: carefully watching rubygems’ progress.

- \[Feature [#6647](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6647&sa=D&source=editors&ust=1686086930353822&usg=AOvVaw2Yzrz03mx8N-9BeUdhqk1s)\] Exceptions raised in threads should be logged (shyouhei) Look at [\[ruby-core:77259\]](https://www.google.com/url?q=http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/77259&sa=D&source=editors&ust=1686086930354071&usg=AOvVaw2-hrivnRy7J0n0ktxeJVUh)

- akr: the proposal can be okay, but we currently do not implemnt thread GC mode.

- \[Feature [#12744](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12744&sa=D&source=editors&ust=1686086930354673&usg=AOvVaw2XHZ5ssMRX1s5sXBf7Cud8)\] Add str.reverse\_each\_char and str.reverse\_chars (shyouhei) conclusion?

- naruse: I agree this makes things faster a bit, but will it really be needed?
- Martin: array allocation is avoided in the patch, but each\_char generates lots of strings anyways.

- \[Bug [#11195](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11195&sa=D&source=editors&ust=1686086930355169&usg=AOvVaw1OsPxSKKdvSr_piG0Qe-wp)\] Add "no\_proxy" parameter to Net::HTTP.new (shyouhei) Roman Bigler volunteers.

- akr: +1 to refacor find\_proxy, then use it from Net::HTTP.
- let’s request a patch, then mentor by naruse.

- \[Bug [#12594](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12594&sa=D&source=editors&ust=1686086930355678&usg=AOvVaw2vDOgVMbC_t02n_f24K-ai)\] The class does not inherit from a module the modules that were included after the inclusion (shyouhei)

- nobu: I’ll fix this someday. hopefully before 2.4?

## From attendees

- \[Bug [#12649](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12649&sa=D&source=editors&ust=1686086930356298&usg=AOvVaw0pycTqt0JiJY7Z2fj5l1cF)\] DateTime method calls hang (shyouhei) seems like a real bug?

- assign nobu.

- \[Bug [#12652](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12652&sa=D&source=editors&ust=1686086930356715&usg=AOvVaw2ZNFuoE2anVbUhNJtOLjvR)\] For Dir.home encode passed user (shyouhei) seems like a real bug?

- assign naruse

- \[Bug [#12509](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12509&sa=D&source=editors&ust=1686086930357136&usg=AOvVaw25U_9Ki1xRrmNpL6ng90_X)\] Using qsort\_s in mingw-w64 causes failures (shyouhei) who to handle this?

- assign nobu

- \[Bug [#8996](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8996&sa=D&source=editors&ust=1686086930357542&usg=AOvVaw1Sf1UW-IpZRMwQVG-_eDBy)\] pthread\_mutex\_lock EINVAL (shyouhei) seems like a real bug?

- assign ko1

- \[Bug [#12776](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12776&sa=D&source=editors&ust=1686086930357965&usg=AOvVaw3y-eYIl9E2pTlHIOM0GqMC)\] Flaky test case: TestThread#test\_thread\_name (shyouhei) seems like a real bug, or....?

- closed, lets backport.

- \[Feature [#12656](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12656&sa=D&source=editors&ust=1686086930358397&usg=AOvVaw27uoe3UpcaCNyogoL7iIi9)\] Expand short paths with File.expand\_path (shyouhei)

- assign cruby-windows

- \[Bug [#7877](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7877&sa=D&source=editors&ust=1686086930358870&usg=AOvVaw2COoSvCj74XtVYyqweFzw5)\] E::Lazy#with\_index should be lazy (shyouhei)

- assign nobu

- \[Feature [#12664](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12664&sa=D&source=editors&ust=1686086930359310&usg=AOvVaw3p_t0b_nyZElrXIZxEmR92)\] Multiline pretty-printing of multiline strings (shyouhei)

- nudge akr

- \[Feature [#4897](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4897&sa=D&source=editors&ust=1686086930359721&usg=AOvVaw3GyHTWB_6hriztr6-4CL82)\] Define Math::TAU and BigMath.TAU. The "true" circle constant, Tau=2\*Pi. See [http://tauday.com/](https://www.google.com/url?q=http://tauday.com/&sa=D&source=editors&ust=1686086930359946&usg=AOvVaw3OsNcYMEhUzWK_PXDv76hl) (shyouhei)

- mrkn: I suggest Math::TWOPI
- akr: C has PI\_2, but this means π/2
- wating for matz (or time?)

- \[Bug [#12674](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12674&sa=D&source=editors&ust=1686086930360527&usg=AOvVaw1hqJIxnP2drHmu-0egJeEq)\] io/wait: not handling the case when the socket is closed before doing wait\_readable/writable with timeout (shyouhei) might be a design issue?

- assign nobu

- \[Bug [#12678](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12678&sa=D&source=editors&ust=1686086930360934&usg=AOvVaw2x4TvYSIUKDFx-jrv9Le7v)\] No way to set a timeout for TLS handshake when using Net::SMTP (shyouhei) should who handle this?

- assign shugo

- \[Bug [#6783](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6783&sa=D&source=editors&ust=1686086930361297&usg=AOvVaw2YSeTaukIXaSyXxB7ftddQ)\] Infinite loop in inspect, not overriding inspect, to\_s, and no known circular references. Stepping into inspect in debugger locks it up with 100% CPU. (shyouhei)

- akr: I have a conception of ritcher inspection framework that passes-around contexts, but have not implemented yet.

- \[Bug [#10222](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10222&sa=D&source=editors&ust=1686086930361713&usg=AOvVaw2JXdIk6kGmDD6h_6YCwaae)\] require\_relative and require should be compatible with each other (shyouhei)

- akr: require\_relative expands real path, while require doesn’t.
- naruse: if we expand realpath on require, we need disk access every time we call require (even when the library is already loaded).
- naruse: what about adding both real path and expanded path to $LOADED\_FEATURES at once for require?
- shyouhei: I think for this shown use case b.rb shall not be called twice.

- \[Bug [#12705](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12705&sa=D&source=editors&ust=1686086930362193&usg=AOvVaw356hZ9qUN_ZfSmkDZkoOzY)\] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei)

- akr: I think it’s a bug.  They should be consistent.
- akr: someone broke here at 2.2.
- ko1: maybe me.

- \[Bug [#4537](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4537&sa=D&source=editors&ust=1686086930362603&usg=AOvVaw3YgfR6n5vfOGD7EhwMaEFE)\] Incorrectly creating private method via attr\_accessor (shyouhei) cf. \[Bug [#9005](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9005&sa=D&source=editors&ust=1686086930362767&usg=AOvVaw0QL7nL3TIXcjdruCp8qCKO)\]

- shyouhei: is this fixed already?
- nobu: seems not.
- shyouhei: updated ruby -v field.

- \[Feature [#12721](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12721&sa=D&source=editors&ust=1686086930363149&usg=AOvVaw1AG6yED67DWd7rhoGwffga)\] public\_module\_function (shyouhei)

- waiting for matz.

- \[Feature [#12732](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12732&sa=D&source=editors&ust=1686086930363437&usg=AOvVaw2rPdExP6u7vze40SW7IBsl)\] An option to pass to Integer, Float, to return nil instead of raise an exception (shyouhei)

- akr: it seems there are needs of other conversion methods than to\_i or Integer
- nobu: they just want to detect validity, not exceptions.
- Martin: what about Integer?()
- Martin: but Upcase?() is a new concept.
- waiting for matz.

- \[Feature [#12745](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12745&sa=D&source=editors&ust=1686086930364089&usg=AOvVaw1tzeHHmpZSPRqTGKwFrijG)\] String#(g)sub(!) should pass a MatchData to the block, not a String (shyouhei)

- akr: naming matters.
- shyouhei: is that we need a new method name?  What about changing gsub behaviour?
- akr: it breaks compatibility.
- akira: what about passing extra second argument to the block, which happen to be a copy of $~
- akr: that is possible.
- nobu: what about defineing MatchData#to\_str ?
- shyouhei: it might be possible to extend the passed string to define method that returns $~.
- akr: adding a new method is the most clean way if we can find good method name.

- \[Feature [#12747](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12747&sa=D&source=editors&ust=1686086930364862&usg=AOvVaw143Gbv7OihK3VYxZ5ZfsCF)\] Add TracePoint#callee\_id (shyouhei)

- assign ko1.

- \[Feature [#12755](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12755&sa=D&source=editors&ust=1686086930365190&usg=AOvVaw0ZYMrFPkd4QllFbXdRXLXY)\] optimize instruction sequence (shyouhei)

- waiting matz.

- \[Bug [#12780](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12780&sa=D&source=editors&ust=1686086930365477&usg=AOvVaw2Lw0F5nFcBk5q7MIgHnkHN)\] BigDecimal#round returns different types depending on argument (shyouhei)

- assign mrkn

- \[Feature [#12790](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12790&sa=D&source=editors&ust=1686086930365763&usg=AOvVaw0a_Awcs8vXJ0lnLj4uh9e5)\] Better inspect for stdlib classes (shyouhei)

- akr: I can’t read the output.
- mrkn: yes.  I’ll fix this.  But it’s not that simple.

- \[Feature #12005\] define rb\_cFixnum and rb\_Bignum again? (akr)

- dependency hell

- situation: we “tried” to delete them.  Main difficulty is “gem ‘json’, ‘~> 1.8.0’”
- hsbt: I pushed this change (remove JSON’s version) to Rails 4

- C-level constants: rb\_cFixnum and rb\_cBignum

- akr: resurrection of these constants does \_not\_ solve the issue.

- warnigs for Ruby-level constants: Fixnum and Bignum

- nobu: lets see RC1’s response.

- \[Bug [#12828](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12828&sa=D&source=editors&ust=1686086930366756&usg=AOvVaw1c5m8QmSFb81a83FEQB4AO)\] Check whether macro RUBY is okay for protecting ruby-specific onigumo extensions (duerst)

- nobu: RUBY is already defined at defines.h
- naruse: doesn’t anyone want to use vanilla onigumo someday?
- akr: is that possible?  Not that easy than it sounds.

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

- \[Feature [#12760](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12760&sa=D&source=editors&ust=1686086930367484&usg=AOvVaw2YVQ5SLKojV8JdEyR088Zm)\] Optional block argument for itself (Victor Shepelev)

- “itself” doesn’t sound right
- nobu: if itself takes block it should behave like tap.
