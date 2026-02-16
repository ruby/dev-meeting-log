---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-03-13

Date: 2017/03/13 (Wed)
Time: 14:00- 19:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/51783/
Attendees: Yukihiro Matsumoto (matz), Koichi Sasada, Nobuyoshi Nakada, Yui Naruse, Shyouhei Urabe, Akira Tanaka, Hiroshi Shibata, Masaya Tarui, Martin Dürst (duerst)
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

* [Feature #13110] Byte-based operations for String (shugo)
* [Bug #13105] `String#to_f` and `String#to_r` don't stop successive underscores (nobu)
* [Feature #13109] `using` in refinements is required to be physically placed before the refined method call (shyouhei)
* [Bug #12705] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei) status?
* [Feature #13095] [PATCH] io.c (rb_f_syscall): remove deprecation notice (shyouhei)
* [Feature #13122] Special syntax for Hash#default_proc (shyouhei)
* [Feature #12650] Use UTF-8 encoding for ENV on Windows (duerst)
* [Feature #6284] Add composition for procs (shyouhei)
* [Feature #13137] Hash Shorthand (shyouhei)
* [Feature #13166] Feature Request: Byte Arrays for Ruby 3 (shyouhei)
* [Feature #9116] String#rsplit missing (shyouhei)
* [Feature #4532] [PATCH] add IO#pread and IO#pwrite methods (shyouhei)
* [Bug #13146] Float::NANs in Hashes are confusing (more than usual). (mrkn)
* [Bug #13134] `Rational()` inconsistency (nobu)

## From attendees

* [Bug #13257] Symbol#respond_to?(:singleton_class) should be false (naruse)
* [Misc #13163] Uncaught exceptions may not be reported when Thread#report_on_exception=true and Thread#abort_on_exception=true (naruse)
* [Bug #13181] Unexpected line in rescue backtrace (shyouhei) what's this?
* this week's "assign who?" section (shyouhei)
    * [Bug #13271] Clarifications on refinement spec
    * [Bug #13269] test/readline/test_readline.rb and mingw
    * [Bug #13298] mingw SEGV TestEnumerable#test_callcc
    * [Bug #13280] net/ftp: Putbinaryfile (on Windows) requires blocksize equal to file size
    * [Bug #13276] Dir.glob returns empty array when OS has no more file handles (expected exception)
    * [Bug #13284] IA64 ruby 2.4 miniruby segfault
    * [Bug #13286] Segfault when using rb_debug_inspector_open / rb_debug_inspector_frame_binding_get with Fiddle, but not when directly from C extension
    * [Bug #13305] Occasional segfaults after defining methods while running coverage
    * [Bug #13307] Changing scheme from http to https for the URI does not change the port number
* [Bug #13282] opt_str_freeze does not always dedupe (shyouhei) seems OK to me
* [Feature #13295] [PATCH] compile.c: apply opt_str_freeze to String#-@ (uminus) (shyohei) seems OK to me as well
* [Feature #13179] Deep Hash Update Method (shyouhei)
* [Feature #8263] Support discovering yield state of individual Fibers (shyouhei)
* [Feature #8576] Add optimized method type for constant value methods (shyouhei)
* [Bug #13188] Reinitialize Ruby VM. (shyouhei) New C API ...?
* [Feature #2740] Extend const_missing to pass in the nesting (shyouhei)
* [Feature #13211] Hash#delete taking a splat (shyouhei)
* [Bug #11567] Segmentation fault CFUNC :gets (shyouhei)
* [Feature #13172] Method that yields object to block and returns result (shyouhei) new name candicate
* [Feature #13224] Add FrozenError as a subclass of RuntimeError (shyouhei)
* [Misc #13230] Better Do ... while structure (shyouhei)
* [Feature #13245] [PATCH] reject inter-thread TLS modification (shyouhei)
* [Feature #13252] C API for creating strings without copying (shyouhei)
* [Bug #13249] Access modifiers don't have an effect inside class methods in Ruby >= 2.3 (shyouhei) design issue or bug?
* [Misc #13283] Disable `&' interpreted as argument prefix warning when passing symbol to Enumerable#map (duerst)
* [Feature #13290] A method to use a hash like in a case construction (duerst)
* [Feature #13265] TracePoint for basic operation redefinition (shyouhei)
* [Bug #12834] `prepend` getting prepended even if it already exists in the ancestors chain (shyouhei)
* [Bug #7976] TracePoint call is at call point, not call site (shyouhei)
* [Bug #13257] Symbol#singleton_class should be undef (shyouhei)
* [Feature #12901] Anonymous functions without scope lookup overhead (shyouhei) status?
* [Feature #13166] Feature Request: Byte Arrays for Ruby 3 (shyouhei)
* [Feature #12573] Introduce a straightforward way to discover whether a process is running (shyouhei)
* [Feature #13263] Add companion integer nth-root method to recent Integer#isqrt (shyouhei)
* [Feature #12698] Method to delete a substring by regex match (shyouhei)
* [Feature #13290] A method to use a hash like in a case construction (shyouhei)
* [Feature #9453] Return symbols of defined methods for `attr` and friends (shyouhei) look at the last comment
* [Feature #13302] Provide a (force) --enable-openssl switch for ruby ./configure (or similar) (shyouhei)
* [Feature #13303] String#any? as !String#empty? (shyouhei)
* [Feature #13077] [PATCH] introduce String#fstring method (ko1) some discussions on twitter they don't like `-'str'` notation.

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [Bug #13223] What should the behavior of `File.join` be when `File::SEPARATOR` is set? Can I apply this patch? (tenderlove)

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

# Log

## Date: 2017/03/13 (Mon)

## Time: 14:00- 19:00 (JST)

## Place: Money Forward inc. Headquarter

## Sign-up: [https://ruby.connpass.com/event/47745/](https://www.google.com/url?q=https://ruby.connpass.com/event/47745/&sa=D&source=editors&ust=1686087099212572&usg=AOvVaw2H69ZGcgNYVH87pcL_SNae)

## log edit: [https://docs.google.com/document/d/1fZPbRMn2zjVAflvLz1IFWs3Kdijnm2Um4uNLammqURE/edit](https://www.google.com/url?q=https://docs.google.com/document/d/1fZPbRMn2zjVAflvLz1IFWs3Kdijnm2Um4uNLammqURE/edit&sa=D&source=editors&ust=1686087099212956&usg=AOvVaw252KatT_TiaZG1EV8OMs74)

## log: TBD

## Next meeting

- 4/17

- at CookPad HQ, yiebisu

## About 2.5 timeframe

## [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087099213820&usg=AOvVaw3vacAuKsyIn9qLjxob6FaO)

- 合宿

- hsbt: I asked shugo-san and he got budget for this.
- next action: we have to send him a propsal.

- Jul - Spet?
- Kanto or not?

- hsbt: I’ll handle.

## Current status of Ruby 2.4

- 2.4.1?

- naruse: working on it.

- 安定版保守事業

- transition progress report

- Apr. 1st thtough May 31th

- nobody here is on contract.
- ko1: should there be a ceremony for transition?

## Carry-over from previous meeting(s)

- \[Feature [#13110](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13110&sa=D&source=editors&ust=1686087099215238&usg=AOvVaw0xN5gKnOxOoMJuPSR-6tHp)\] Byte-based operations for String (shugo)

- matz: OK for byteoffset.
- akr: destructive modification using bytesplice may cause string rescan because it can break validity of the string.
- duerst: is byteindex needed? is it enough to add a method for MatchData?
- naruse: that is byteoffset here.
- akr: byteindex may have problem for inter-codepoint index.

- \[Bug [#13105](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13105&sa=D&source=editors&ust=1686087099215908&usg=AOvVaw0O3GiFFJWC56gM1ogTVdTU)\] String#to\_f and String#to\_r don't stop successive underscores (nobu)

- nobu: is this intentional?
- ko1: ignore all underscores?
- shyouhei: all? at the beginning?
- ko1: \_ generates zero for to\_i.
- akr: to\_i once allowed multiple underscores but prohibits now.
- tal: ruby’s parser does not allow multiple undersocres as literals.
- matz: these days other languages follow us like java, python.

- python: PEP-515, no multiple undersocres
- java: no pre-post undersocres, but multiple inter-underscores allowed.

- matz: OK, lets make it as we do as literals.

- \[Feature [#13109](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13109&sa=D&source=editors&ust=1686087099216861&usg=AOvVaw0erPIxct6YD7y0abLysVr_)\] using in refinements is required to be physically placed before the refined method call (shyouhei)

- matz: I don’t think it’s a bug. It’s intentional.  But we may allow this as an extension.

- \[Bug [#12705](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12705&sa=D&source=editors&ust=1686087099217332&usg=AOvVaw1qMyEnb2Nd3sHYLjJBe1IP)\] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei) status?

- ko1: we should fix the interpreter
- ko1: we introduced ad-hoc change to lambda creation but that turned out to be a mistake, and observed here.
- ko1: to be fixed in 2.5.

- \[Feature [#13095](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13095&sa=D&source=editors&ust=1686087099217859&usg=AOvVaw066oj3PgFXySVobuckLeqT)\] \[PATCH\] io.c (rb\_f\_syscall): remove deprecation notice (shyouhei)

- shyouhei: do we want to remove system calls?
- akr: yes.
- ko1: it is about foreign-function interface
- akr: syscall could be easier than libffi because syscall has only one entry point.
- matz: I think it’s safer to avoid using this feature so kind of reject in mood but …
- akr: current situation is too difficult to use properly, too easy to get used.
- akr: my suggestion is to move it to ext/syscall.

- \[Feature [#13122](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13122&sa=D&source=editors&ust=1686087099218630&usg=AOvVaw1SRRvSfqN5fhvIzq0wOpNs)\] Special syntax for Hash#default\_proc (shyouhei)

- tal: not only default proc, but also there are default values.
- matz: reject

- \[Feature [#12650](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12650&sa=D&source=editors&ust=1686087099219093&usg=AOvVaw3JpLnOerj1nUKGqvRnIddj)\] Use UTF-8 encoding for ENV on Windows (duerst)

- nobu: ENV is about inter-process, we cannot change alone.
- shyouhei: seems they need something like “UTF-8 mode”
- nobu: that should be “-U”
- shyouhei: does that option change the encoding of ENV?
- naruse: it might be possible to use default internal.
- akr: is ENV in windows handled by kernel?
- nobu: yes
- akr: then what encoding?
- nobu: so-called wide character.
- duerst: when a PC is used by only one person ENV tends to remain single encoding but when people share a PC, ENV is shared across users. (cp437?)
- nobu: system-wide.

- \[Feature [#6284](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6284&sa=D&source=editors&ust=1686087099220048&usg=AOvVaw3Gp66fHVpk497QngalvDcR)\] Add composition for procs (shyouhei)

- ko1: is it a matter of speed?
- shyouhei: it seems composition is faster now
- nobu: notation seems also be the problem.
- duerst: notation is a point. (f ∘ g)(x) can be either f(g(x)) or g(f(x)).
- matz: I have no strong opinion on it.
- matz: I’ll respond.

- \[Feature [#13137](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13137&sa=D&source=editors&ust=1686087099220749&usg=AOvVaw2tyFZZiP8em3CshXFSwAhR)\] Hash Shorthand (shyouhei)

- matz: reject

- \[Feature [#13166](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13166&sa=D&source=editors&ust=1686087099221148&usg=AOvVaw3p_MXSXFgsq6Tez2JmQmdh)\] Feature Request: Byte Arrays for Ruby 3 (shyouhei)

- akr: I’m for this but I don’t think I can persuade matz.
- matz: exactly.
- akr: so I proposed setbit/getbit, in order for the OP to create a dedicated class on top of it.

- \[Feature [#9116](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9116&sa=D&source=editors&ust=1686087099221706&usg=AOvVaw3aDTz3pGyxRcgoODBkm5S5)\] String#rsplit missing (shyouhei)

- akr: what’s this?
- shyouhei: they say python’s
- naruse: use case?
- tal: rsplit(...,2) can be generally written using regexp. ohter cases when we cant do this using regexp?

- \[Feature [#4532](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4532&sa=D&source=editors&ust=1686087099222269&usg=AOvVaw22AvqSOLdUchLTL9e7lFeZ)\] \[PATCH\] add IO#pread and IO#pwrite methods (shyouhei)

- akr: portability concern? This is POSIX but added recently.
- shyouhei: POSIX issue 5
- akr: at least 2001 version has this.
- matz: OK.

- \[Bug [#13146](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13146&sa=D&source=editors&ust=1686087099222840&usg=AOvVaw3WNO4EjCXfize9UXRWvo6g)\] Float::NANs in Hashes are confusing (more than usual). (mrkn)

- nobu: prohibit inserting NaNs into hashs?
- naruse: that’s nonsense.

- \[Bug [#13134](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13134&sa=D&source=editors&ust=1686087099223330&usg=AOvVaw2yJhRMDxlA9rZvFUhUd4T2)\] Rational() inconsistency (nobu)

- matz: OK.

## From attendees

- \[Bug #13304\] public function rb\_thread\_fd\_close is removed at r57422 (naruse)

- naruse: there are usages for this function.
- akr: why do you want to remove this?
- nobu: this is not a function to close a fd (bad naming). rb\_io\_close shall be used instead.
- ko1: then why not just let it an alias of rb\_io\_close?
- nobu: shuld we deprecate?
- naruse: no. you can’t deprecate without transition path.
- conclusion: delete DEPRECATE

- \[Feature #12926\] -l flag for line end processing should use chomp! instead of chop! (naruse)

- naruse: should we backport this?
- matz: I don’t think so.

- \[Bug [#13181](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13181&sa=D&source=editors&ust=1686087099224731&usg=AOvVaw3okPfUXFVXkMR5SaSRY92E)\] Unexpected line in rescue backtrace (shyouhei) what's this?
- this week's "assign whom?" section (shyouhei)

- \[Bug [#13271](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13271&sa=D&source=editors&ust=1686087099225128&usg=AOvVaw1Q_aL-8NGjVp5naOGCDFXV)\] Clarifications on refinement spec
- \[Bug [#13269](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13269&sa=D&source=editors&ust=1686087099225464&usg=AOvVaw30DhA6egfHkXns4BDqf6qh)\] test/readline/test\_readline.rb and mingw
- \[Bug [#13298](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13298&sa=D&source=editors&ust=1686087099225792&usg=AOvVaw3dS_w_JXpYWF2BSbdwwB5A)\] mingw SEGV TestEnumerable#test\_callcc
- \[Bug [#13280](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13280&sa=D&source=editors&ust=1686087099226145&usg=AOvVaw0P-SRVBHUjjIH72HqyMfB3)\] net/ftp: Putbinaryfile (on Windows) requires blocksize equal to file size
- \[Bug [#13276](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13276&sa=D&source=editors&ust=1686087099226481&usg=AOvVaw3zLCJauTJZiuP4wc1QLgsi)\] Dir.glob returns empty array when OS has no more file handles (expected exception)

- nobu: this one predates 1.0. should we backport?
- matz: I don’t think so.

- \[Bug [#13284](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13284&sa=D&source=editors&ust=1686087099226976&usg=AOvVaw2_Lcw6Gi_iZL6aqA2Zzlhx)\] IA64 ruby 2.4 miniruby segfault
- \[Bug [#13286](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13286&sa=D&source=editors&ust=1686087099227307&usg=AOvVaw3yn5GrUj7YbWxIQ-14lwzY)\] Segfault when using rb\_debug\_inspector\_open / rb\_debug\_inspector\_frame\_binding\_get with Fiddle, but not when directly from C extension
- \[Bug [#13305](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13305&sa=D&source=editors&ust=1686087099227770&usg=AOvVaw3HvtlaXIObiYNKqFKvsqaR)\] Occasional segfaults after defining methods while running coverage
- \[Bug [#13307](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13307&sa=D&source=editors&ust=1686087099228139&usg=AOvVaw1PSKobRb61Cfs3xHkyy8dH)\] Changing scheme from http to https for the URI does not change the port number

- \[Bug [#13282](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13282&sa=D&source=editors&ust=1686087099228488&usg=AOvVaw2jT5DReNaQ_n1_l91tKSdk)\] opt\_str\_freeze does not always dedupe (shyouhei)

- shyouhei: this is about power\_assert
- akr: what’s wrong with it?
- shyouhei: seems tracepoint?
- ko1: don’t know the exact reason.

- \[Feature [#13295](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13295&sa=D&source=editors&ust=1686087099229087&usg=AOvVaw0hVUyyVTliR_h4mRAkD2K8)\] \[PATCH\] compile.c: apply opt\_str\_freeze to String#-@ (uminus) (shyohei) seems OK to me as well

- ko1: this is buggy.  Can’t redefine \-@ in this patch.

- \[Feature [#13179](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13179&sa=D&source=editors&ust=1686087099229616&usg=AOvVaw2fbLLS3REYDZqKKfKagYeM)\] Deep Hash Update Method (shyouhei)

- nobu: #bury has already been rejected, no update since then.

- \[Misc [#13283](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13283&sa=D&source=editors&ust=1686087099230046&usg=AOvVaw02OXNlusTuXTa80ArSAdy9)\] Disable \`&' interpreted as argument prefix warning when passing symbol to Enumerable#map (duerst)

- duerst: the warning is older than Symbol#to\_proc
- shyouhei: why not suppress the warning for symbol literals, not for variables
- nobu: lexer / parser gets complicated that case.
- nobu: is this a warning shown everywhere? -> only when \-w
- matz: mruby won’t warn this case?
- nobu: it does.

- \[Feature [#13290](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13290&sa=D&source=editors&ust=1686087099230847&usg=AOvVaw31bT8lwmHBx028wsbHRvoj)\] A method to use a hash like in a case construction (duerst)

- matz: I don’t understand the usage.  Let me ask the OP.

- \[Feature [#13077](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13077&sa=D&source=editors&ust=1686087099231274&usg=AOvVaw3OZ8EPm61NmRL-PRfnrsKP)\] \[PATCH\] introduce String#fstring method (ko1) some discussions on twitter they don't like \-'str' notation.

- shyouhei: we can add other stylish names later. \-@ is “for the time being”.
- matz: if you have opinions, have a separate issue.

## From non-attendees

- \[Bug [#13223](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13223&sa=D&source=editors&ust=1686087099232038&usg=AOvVaw1rr2yUj5QHpJ9FbbtUdbmo)\] What should the behavior of File.join be when File::SEPARATOR is set? Can I apply this patch? (tenderlove)

- ko1: this is a bug.
- nobu: this is either we fix the document to allow other separators or not.
- akr: I’d like to refrain from adding new feature.
- nobu: File.split does not honor the constant.
- shyouhei: It seems jruby honors this constant.
- matz: OK, lets not use File::SEPARATOR, and explicitly state in document that we might ignore reassignments to that constant.
