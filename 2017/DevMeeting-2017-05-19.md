---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-05-19

Date: 2017/05/19 (Fri)
Time: 14:00- 19:00 (JST)
Place: Speee Inc., Kurosaki Bldg. 4F, 4-1-4 Roppongi, Minato-ku Tokyo, Japan
Sign-up: https://ruby.connpass.com/event/55487/
Attendees: Matz (Yukihiro Matsumoto), muraken (Kenta Murata), Nobuyoshi Nakada, ko1 (Koichi Sasada), Akira Matsuda, Yui Naruse, Hiroshi Shibata, akr (Akira Tanaka), Shota Fukumori, Shyouhei Urabe, Martin Dürst
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

- Previous bugs that were not assigned
    - [Bug #13196] Improve keyword argument errors when non-keyword arguments given
    - [Bug #13320] rescue blocks get an entry in backtrace locations
    - [Bug #13324] IRB Segmentation Fault from eval infinite loop
    - [Bug #13330] Array.include? is slow for symbols
    - [Bug #13336] Default Parameters don't work
    - [Bug #13337] Eval and Later Defined Local Variables
    - [Bug #13390] MinGW build test-all SEGV, issue in test framework or error recovery?
    - [Bug #13350] File.read :newline option not respected on Linux
    - [Bug #13351] net/http: Net::HTTP.start sets wrong default arg value
    - watson's
        - [Bug #13341] Improve performance of implicit type conversion
        - [Bug #13342] Improve yielding block performance
        - [Bug #13354] Improve Time#<=> performance
        - [Bug #13357] Improve Time#+ & Time#- performance
        - [Bug #13365] Improve performance of rb_equal() with special constants
        - [Bug #13368] Improve performance of Array#sum with float elements
        - [Bug #13374] Fix one of performance regressions in method calling
    - [Bug #13373] FileUtils methods for copy, move and remove directories is not providing a decent error trace for letting know if it was success or fail
    - [Bug #13101] Date#rfc2822 and Time#rfc2822 don't return the same format
    - [Bug #13312] String#casecmp raises TypeError instead of returning nil
    - [Bug #12684] Delegator#eql? missing
    - [Feature #13389] [PATCH] POP3 support timeout for TLS handshake
    - [Bug #13404] Hash#any? yields arguments to lambdas with proc semantics
    - [Bug #13413] --with-static-linked-ext doesn't install extension files on `make install`
    - [Bug #13429] Net::SMTP has no read timeout when connexion over TLS
- [Feature #13309] Locale paramter for Integer(), Float(), Rational() (shyouhei)
- [Feature #12733] Bundle bundler to ruby core (shyouhei) updates?
- [Feature #11302] Dir.entries and Dir.foreach without `[".", ".."]` (shyouhei)
- [Feature #13332] Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13333] block to yield (shyouhei)
- [Feature #13378] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381] [PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Bug #13315] Single "%" at the end of `printf` format string appears in the result (shyouhei)
- [Feature #13385] [PATCH] Make Resolv::DNS::Name validation similar to host and dig commands (shyouhei)
- [Feature #13383] [PATCH] Module#source_location (shyouhei)
- [Feature #12063] KeyError#receiver and KeyError#name (shyouhei) status?
- [Bug #13397] #object_id should not be signed (shyouhei)
- [Feature #13396] Net::HTTP has no write timeout (shyouhei)
- [Bug #13407] We have recv_nonblock but not send_nonblock... can we add it? (shyouhei)
- [Feature #13395] Add a method to check for not nil (shyouhei)

## From attendees

- [Feature #13483] TracePoint#enable with block for thread-local trace (ko1)
- [Bug #13249] Access modifiers don't have an effect inside class methods in Ruby >= 2.3 (ko1, ask the approach)
- watson continues to request pulling his ideas
    - [Bug #13436] Improve performance of Array#<=> with Fixnum/Float/String elements
    - [Bug #13437] Improve performance of Enumerable#{sort_by,min_by,max_by,minmax_by}
    - [Bug #13443] Improve performance of Range#{min,max}
    - [Bug #13482] Improve performance of "set instance variable"
    - [Bug #13447] Improve performance of rb_eql()
    - [Bug #13503] Improve performance of some Time & Rational methods
    - [Bug #13506] Improve performance of `Complex#{+,-,*,/,**,abs2}`
    - [Bug #13519] Improve performance of some Time methods
    - [Bug #13507] Improve performance of some Complex methods where call Numeric#real? internally
    - [Bug #13553] Improve performance in where push the element into non shared Array object
- assign who? corner (shyouhei)
    - [Bug #13432] set_trace_funcにproc->is_from_method = TRUEのオブジェクトを渡し、SystemStackErrorを発生させるとRubyVMが停止する
    - [Bug #13446] refinements with prepend for module has strange behavior
    - [Bug #13492] Integer#prime? and Prime.each might produce false positives
    - [Bug #13498] Weakref, Weakmap and define_finalizer don't work on frozen objects
    - [Bug #13513] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages
    - [Bug #13501] Process.kill behaviour for negative pid is not documented and may be wrong
    - [Bug #13515] Pathname#join doesn't add separator on UNC paths
    - [Bug #13521] [PATCH] Add fallback for DNS resolver registry key on Wine
    - [Bug #13537] ruby crash in rb_gc_mark
    - [Bug #13545] Ruby built by clang 4.0.0 parses Float literal wrongly
    - [Bug #13548] miniruby SEGV while building with non-default CFLAGS (caused by __builtin_setjmp)
    - [Bug #13555] Disable TestTrace#test_trace_stackoverflow
    - [Bug #13524] miniruby: [BUG] Segmentation fault at 0x0055e487e00230 ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-li
    - [Bug #13557] there's no way to pass backtrace locations as a massaged backtrace
    - [Bug #10290] segfault when calling a lambda recursively after rescuing SystemStackError
        - Fix confirmed?
    - [Feature #10674] Net::HTTP retries idempotent requests once after a timeout, but its not configurable
    - [Bug #13542] MinGW trunk Builds - Summary of Issues
        - [Bug #13549] MinGW / Windows encoding - Two issues
        - [Bug #13556] MinGW readline Alt / Meta keys
        - [Bug #13569] Windows - TestRubyOptions#test_search - append to paths instead of replacing
    - [Bug #13564] Exception message management
- [Feature #13383] [PATCH] Module#source_location (shyouhei)
- [Feature #13434] better method definition in C API (shyouhei)
- [Feature #13512] System Threads (shyouhei)
- [Feature #13518] Indented multiline comments (shyouhei)
- [Feature #13516] Improve the text of the circular require warning (shyouhei)
- [Feature #13532] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)
- [Bug #13535] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?
- [Feature #13552] [PATCH 0/2] reimplement ConditionVariable, Queue, SizedQueue using ccan/list (shyouhei)
- [Feature #13568] File#path for O_TMPFILE fds are unmeaning (sorah)
- [Feature #13563] Implement Hash#choice method. (shyouhei)
- [Feature #13562] Use a sized enumerator with #yield_self (shyouhei) needs?
- [Feature #13056] base option to `Dir.glob` (nobu)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [Feature #8661] Backstrace in reverse order. The OP asked for an option but this is currently always the case for the toplevel exception handler. Is this OK for compatibility? It is surprising for the least (See comment 6). Can we have matz's opinion? (eregon)

# Log

Date: 2017/05/19 (Mon)

Time: 14:00- 19:00 (JST)

Place: Speee Inc. Headquarter

Sign-up: https://ruby.connpass.com/event/55487/

log edit: [https://docs.google.com/document/d/1XolSYFFvBOj\_EwtLeYrg8QMadKZpUDE3b2EHcXoklYA/edit](https://www.google.com/url?q=https://docs.google.com/document/d/1XolSYFFvBOj_EwtLeYrg8QMadKZpUDE3b2EHcXoklYA/edit&sa=D&source=editors&ust=1686087156478680&usg=AOvVaw3OMGEHJuo3NAojETLp_5NU)

log: TBD

## Next meeting

- 6/16 14:00 ~ 18:30

- at Speee, inc. Headquarter

## About 2.5 timeframe

## [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087156480146&usg=AOvVaw0VF_ATzWdeZPXffCleFlIC)

- Previrew 1 in June?

- Do we release preview 1 soon? (2.2: Sep, 2.3: Nov, 2.4: June)
- We don’t have such big change to release preview1 early.

- anything big that is worth releasing a preview
- 合宿

## Watson-san

- shyouhei: I’d recommend him to be a committer.
- ko1: he needs review before commit.
- matz: that’s what everyone do for the first time.  Confirm.

## Carry-over from previous meeting(s)

- Previous bugs that were not assigned

- \[Bug [#13196](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13196&sa=D&source=editors&ust=1686087156482105&usg=AOvVaw3Yo7tKZQmQV723le4d_SCL)\] Improve keyword argument errors when non-keyword arguments given

- ko1: isn’t it too long?
- shyouhei: that depends.
- matz: it’s a good idea to make errors descriptive.

- \[Bug [#13320](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13320&sa=D&source=editors&ust=1686087156482880&usg=AOvVaw2JDGt2gZgKOZmhQ2j62qOv)\] rescue blocks get an entry in backtrace locations

- shyouhei: can you look at it? > nobu
- ko1: there are other corner cases in backtraces where \_unexpected\_-ish backtrace situation.
- mrkn: does it cause any problems? is it worth deleting?
- ko1: I tried this and the “bar” line seems indicating the method bar, not raise.

- \[Bug [#13324](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13324&sa=D&source=editors&ust=1686087156483626&usg=AOvVaw1bE2X8qLYGazqxy-jZQJIH)\] IRB Segmentation Fault from eval infinite loop

- akr: this is a infinite loop.
- akr: isn’t it possible to extend guard page?
- ko1: it is technically impossible to rewind stack overflow.

- \[Bug [#13330](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13330&sa=D&source=editors&ust=1686087156484237&usg=AOvVaw0qfAi2jIPCsi6VAEWaMsVo)\] Array.include? is slow for symbols

- assign ko1

- \[Bug [#13336](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13336&sa=D&source=editors&ust=1686087156484703&usg=AOvVaw2uZATeXdtJ0oUbe3lVregb)\] Default Parameters don't work

- ko1: would like to hear from mame.

- \[Bug [#13337](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13337&sa=D&source=editors&ust=1686087156485229&usg=AOvVaw3tjKJAUjAs3XsieghWipll)\] Eval and Later Defined Local Variables

- akr: this is by design.
- shyouhei: doc issue?

- \[Bug [#13390](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13390&sa=D&source=editors&ust=1686087156485769&usg=AOvVaw0bkxXWseBCShXuoKIdXzUx)\] MinGW build test-all SEGV, issue in test framework or error recovery?

- nobu: I’m working on it
- naruse: OK, assign to you.

- \[Bug [#13350](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13350&sa=D&source=editors&ust=1686087156486332&usg=AOvVaw0OL0VFd6FpdF9sar8yqwSo)\] File.read :newline option not respected on Linux

- shyouhei: this is a bug.
- nobu: Windows situation is done by the runtime.

- \[Bug [#13351](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13351&sa=D&source=editors&ust=1686087156486908&usg=AOvVaw06cbnnkbCI1kAY9k3IgD5T)\] net/http: Net::HTTP.start sets wrong default arg value

- akr: is it possible to simulate old behavior, when we change the default?
- shyouhei: yes.

- \[Bug [#13373](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13373&sa=D&source=editors&ust=1686087156487498&usg=AOvVaw1hkV0QH-vkftklmSoBDN_a)\] FileUtils methods for copy, move and remove directories is not providing a decent error trace for letting know if it was success or fail

- matz: reproducible code?

- \[Bug [#13101](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13101&sa=D&source=editors&ust=1686087156487951&usg=AOvVaw3J49qqgtIk9YVXvn1Tk_Ua)\] Date#rfc2822 and Time#rfc2822 don't return the same format

- akr: technically they are both true.
- nobu: the patch also includes -0000 change
- shyouhei: that’s a problem.

- \[Bug [#13312](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13312&sa=D&source=editors&ust=1686087156488678&usg=AOvVaw0IP2Tibp_sVfj4hlOHJ_2n)\] String#casecmp raises TypeError instead of returning nil

- nobu: I think it’s OK to fix.
- hsbt: assign stomer

- \[Bug [#12684](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12684&sa=D&source=editors&ust=1686087156489198&usg=AOvVaw0-gaLT34QgLJnczTM9AuZ9)\] Delegator#eql? missing

- shyouhei: it ends with “nobu: seems fine”.
- nobu: will look at it again.

- \[Feature [#13389](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13389&sa=D&source=editors&ust=1686087156489703&usg=AOvVaw3UAc2VLGvjLJGeVATXtx96)\] \[PATCH\] POP3 support timeout for TLS handshake

- assign who?
- seems no one is active.
- nobu: why not just merge it?

- \[Bug [#13404](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13404&sa=D&source=editors&ust=1686087156490332&usg=AOvVaw0nDnfQmQzf4iuobhMoSASw)\] Hash#any? yields arguments to lambdas with proc semantics

- nobu: #13391 is blocking this issue.

- \[Bug [#13413](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13413&sa=D&source=editors&ust=1686087156490850&usg=AOvVaw1jtNFgFhXOE5WRqMBrDW5U)\] --with-static-linked-ext doesn't install extension files on make install

- assign nobu

- \[Bug [#13429](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13429&sa=D&source=editors&ust=1686087156491355&usg=AOvVaw3dRqRqIMWEKA7b6JjM4_ct)\] Net::SMTP has no read timeout when connexion over TLS

- assign shugo

- \[Feature [#13309](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13309&sa=D&source=editors&ust=1686087156491786&usg=AOvVaw1agut7TpHNRQ3wcMNxID6A)\] Locale paramter for Integer(), Float(), Rational() (shyouhei)

- nobu: This should start as a gem.

- \[Feature [#12733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12733&sa=D&source=editors&ust=1686087156492263&usg=AOvVaw0XQpOQUyMziOg6FlAsILlE)\] Bundle bundler to ruby core (shyouhei) updates?
- \[Feature [#11302](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11302&sa=D&source=editors&ust=1686087156492604&usg=AOvVaw0nDkpvFwx_POGpajeQCQHw)\] Dir.entries and Dir.foreach without [".", ".."](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/shyouhei&sa=D&source=editors&ust=1686087156492895&usg=AOvVaw3OBe57vNKsMf_ATnZ0N4OI)

- matz: OK.

- \[Feature [#13332](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13332&sa=D&source=editors&ust=1686087156493326&usg=AOvVaw1_kzKnDDMkBrcOKrbpMnbe)\] Forwardable#def\_instance\_delegator nil (shyouhei)
- \[Feature [#13333](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13333&sa=D&source=editors&ust=1686087156493785&usg=AOvVaw2hHHe_xqEmSzddHE_tTvzy)\] block to yield (shyouhei)

- nobu: because we can.
- ko1: I’m afraid there can be places where this breaks something in-core.

- \[Feature [#13378](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13378&sa=D&source=editors&ust=1686087156494351&usg=AOvVaw0WKGt3JtNvayGUYVAZRdYQ)\] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- \[Feature [#13381](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13381&sa=D&source=editors&ust=1686087156494730&usg=AOvVaw0BMZUadcCXBfbJCCiUq0LV)\] \[PATCH\] Expose rb\_fstring and its family to C extensions (shyouhei)
- \[Bug [#13315](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13315&sa=D&source=editors&ust=1686087156495152&usg=AOvVaw1ecD5ezwSkySAflvN4VgDC)\] Single "%" at the end of printf format string appears in the result (shyouhei)
- \[Feature [#13385](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13385&sa=D&source=editors&ust=1686087156495600&usg=AOvVaw1HGYyO6BJ_jHeJcaXPto3W)\] \[PATCH\] Make Resolv::DNS::Name validation similar to host and dig commands (shyouhei)
- \[Feature [#13383](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13383&sa=D&source=editors&ust=1686087156495967&usg=AOvVaw0q7PPXoJ1A8asn8ROwOeBq)\] \[PATCH\] Module#source\_location (shyouhei)
- \[Feature [#12063](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12063&sa=D&source=editors&ust=1686087156496344&usg=AOvVaw2CtN3dqBFbnsEvtCYMIJmX)\] KeyError#receiver and KeyError#name (shyouhei) status?
- \[Bug [#13397](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13397&sa=D&source=editors&ust=1686087156496693&usg=AOvVaw0LrtXRQ_88mu7aXuTziKVQ)\] #object\_id should not be signed (shyouhei)
- \[Feature [#13396](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13396&sa=D&source=editors&ust=1686087156497048&usg=AOvVaw09p-UD2axNXzbK4vNbHNfA)\] Net::HTTP has no write timeout (shyouhei)
- \[Bug [#13407](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13407&sa=D&source=editors&ust=1686087156497394&usg=AOvVaw2FaC6UP2MitQeAfP9d5b-X)\] We have recv\_nonblock but not send\_nonblock... can we add it? (shyouhei)
- \[Feature [#13395](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13395&sa=D&source=editors&ust=1686087156497736&usg=AOvVaw3tmSS73OsQBuqp1ui4vBFd)\] Add a method to check for not nil (shyouhei)

## From attendees

- \[Feature [#13483](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13483&sa=D&source=editors&ust=1686087156498294&usg=AOvVaw3wehzK0inW8VtKzeeR1Exd)\] TracePoint#enable with block for thread-local trace (ko1)

- ko1: I want to make trace thread local
- shyouhei: is there a way to emulate old behaviour?
- ko1: ThracePoint.enable will do.

- \[Bug [#13249](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13249&sa=D&source=editors&ust=1686087156499012&usg=AOvVaw2YoPSETbfcRi7LzKtJs3HA)\] Access modifiers don't have an effect inside class methods in Ruby >= 2.3 (ko1, ask the approach)

- shyouhei: we want to prohibit private with zero arity inside of a singleton method.

- watson continues to request pulling his ideas

- \[Bug [#13436](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13436&sa=D&source=editors&ust=1686087156499600&usg=AOvVaw2pgVRdoyZhRe4KIb0NYmnY)\] Improve performance of Array#<=> with Fixnum/Float/String elements
- \[Bug [#13437](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13437&sa=D&source=editors&ust=1686087156499976&usg=AOvVaw22P1wkLUWAT6OSDoKzhWm5)\] Improve performance of Enumerable#{sort\_by,min\_by,max\_by,minmax\_by}
- \[Bug [#13443](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13443&sa=D&source=editors&ust=1686087156500366&usg=AOvVaw3uzkASks7qI6UNC3KVVrLE)\] Improve performance of Range#{min,max}
- \[Bug [#13482](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13482&sa=D&source=editors&ust=1686087156500796&usg=AOvVaw2Kv3qcBxDYPIZOug5MEtxb)\] Improve performance of "set instance variable"
- \[Bug [#13447](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13447&sa=D&source=editors&ust=1686087156501151&usg=AOvVaw2Iyb8Y8dIeiQ-itEZGWemd)\] Improve performance of rb\_eql()
- \[Bug [#13503](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13503&sa=D&source=editors&ust=1686087156501553&usg=AOvVaw3ofWEuQ01nKoXEyigOoFPp)\] Improve performance of some Time & Rational methods
- \[Bug [#13506](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13506&sa=D&source=editors&ust=1686087156501938&usg=AOvVaw1TYWnB_KvI4UzYtFnbVkxj)\] Improve performance of Complex#{+,-,,/,\*,abs2}
- \[Bug [#13519](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13519&sa=D&source=editors&ust=1686087156502369&usg=AOvVaw3nil-_WaPKS2pd8ST0B9y8)\] Improve performance of some Time methods
- \[Bug [#13507](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13507&sa=D&source=editors&ust=1686087156502724&usg=AOvVaw3jbehw0HVf5ThYtJXf4a5O)\] Improve performance of some Complex methods where call Numeric#real? internally
- \[Bug [#13553](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13553&sa=D&source=editors&ust=1686087156503081&usg=AOvVaw3vpDy6mnSwX51rdUEQ1nQ0)\] Improve performance in where push the element into non shared Array object

- assign who? corner (shyouhei)

- \[Bug [#13432](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13432&sa=D&source=editors&ust=1686087156503577&usg=AOvVaw2erUAboxhD4hfImQDZZZTy)\] set\_trace\_funcにproc->is\_from\_method = TRUEのオブジェクトを渡し、SystemStackErrorを発生させるとRubyVMが停止する
- \[Bug [#13446](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13446&sa=D&source=editors&ust=1686087156503966&usg=AOvVaw20tzVK2SBDSsFv9bASZKDQ)\] refinements with prepend for module has strange behavior
- \[Bug [#13492](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13492&sa=D&source=editors&ust=1686087156504341&usg=AOvVaw3fyt3wqL8OX2EXbWS9INzy)\] Integer#prime? and Prime.each might produce false positives

- akr: the patch seems ok.

- \[Bug [#13498](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13498&sa=D&source=editors&ust=1686087156504793&usg=AOvVaw1i3ZJhZ1vXBaiq-NlQ--Wl)\] Weakref, Weakmap and define\_finalizer don't work on frozen objects
- \[Bug [#13513](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13513&sa=D&source=editors&ust=1686087156505148&usg=AOvVaw1IPiit_S6X1pM1SEVhfRoG)\] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages
- \[Bug [#13501](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13501&sa=D&source=editors&ust=1686087156505512&usg=AOvVaw3LWyvmdb0eq4S-yZi6gZ_F)\] Process.kill behaviour for negative pid is not documented and may be wrong
- \[Bug [#13515](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13515&sa=D&source=editors&ust=1686087156505881&usg=AOvVaw37_gLDUbCciX62u8Fpd3jI)\] Pathname#join doesn't add separator on UNC paths
- \[Bug [#13521](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13521&sa=D&source=editors&ust=1686087156506254&usg=AOvVaw2XwNDf0uUiRU0WK_C7Q9z9)\] \[PATCH\] Add fallback for DNS resolver registry key on Wine
- \[Bug [#13537](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13537&sa=D&source=editors&ust=1686087156506590&usg=AOvVaw2SqObIX5GRNMQnFaky39J5)\] ruby crash in rb\_gc\_mark
- \[Bug [#13545](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13545&sa=D&source=editors&ust=1686087156506931&usg=AOvVaw3hbe0du98A3AVWlpXjz9HV)\] Ruby built by clang 4.0.0 parses Float literal wrongly

- naruse: OK but I think this is a day-to-day fix.

- \[Bug [#13548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13548&sa=D&source=editors&ust=1686087156507356&usg=AOvVaw0DLPlt4d0_wSCwvLIcEqsk)\] miniruby SEGV while building with non-default CFLAGS (caused by \_\_builtin\_setjmp)
- \[Bug [#13555](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13555&sa=D&source=editors&ust=1686087156507751&usg=AOvVaw30MBcwqRKLgiDPCQJEARRs)\] Disable TestTrace#test\_trace\_stackoverflow
- \[Bug [#13524](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13524&sa=D&source=editors&ust=1686087156508106&usg=AOvVaw2m-4FemFV2XV5SNz0eeMAw)\] miniruby: \[BUG\] Segmentation fault at 0x0055e487e00230 ruby 2.4.1p111 (2017-03-22 revision 58053) \[x86\_64-li
- \[Bug [#13557](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13557&sa=D&source=editors&ust=1686087156508446&usg=AOvVaw3CgU_8VCATlR60oVBDokEf)\] there's no way to pass backtrace locations as a massaged backtrace
- \[Bug [#10290](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10290&sa=D&source=editors&ust=1686087156508796&usg=AOvVaw2DnEr7eU7hak0qhLHezgxP)\] segfault when calling a lambda recursively after rescuing SystemStackError

- Fix confirmed?

- \[Feature [#10674](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10674&sa=D&source=editors&ust=1686087156509213&usg=AOvVaw0oS1ixlUMQPG-GHsjeFkHi)\] Net::HTTP retries idempotent requests once after a timeout, but its not configurable
- \[Bug [#13542](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13542&sa=D&source=editors&ust=1686087156509541&usg=AOvVaw1hIzDfQ9KoGXLPX026cCTr)\] MinGW trunk Builds - Summary of Issues

- \[Bug [#13549](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13549&sa=D&source=editors&ust=1686087156509873&usg=AOvVaw0-NsVNCuUVQI52uD_VCqb0)\] MinGW / Windows encoding - Two issues
- \[Bug [#13556](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13556&sa=D&source=editors&ust=1686087156510264&usg=AOvVaw2eE_t456FLMudE8YFPmPMt)\] MinGW readline Alt / Meta keys
- \[Bug [#13569](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13569&sa=D&source=editors&ust=1686087156510610&usg=AOvVaw0k9asRw25p5AF9hEsm4hch)\] Windows - TestRubyOptions#test\_search - append to paths instead of replacing

- \[Bug [#13564](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13564&sa=D&source=editors&ust=1686087156510952&usg=AOvVaw29Jj333vNISP3NG4k2gSah)\] Exception message management

- \[Feature [#13383](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13383&sa=D&source=editors&ust=1686087156511287&usg=AOvVaw2vKkWlR9_NLrplwUbUQEaD)\] \[PATCH\] Module#source\_location (shyouhei)
- \[Feature [#13434](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13434&sa=D&source=editors&ust=1686087156511619&usg=AOvVaw1wObOeC1lGVpJH6j-zTvN_)\] better method definition in C API (shyouhei)
- \[Feature [#13512](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13512&sa=D&source=editors&ust=1686087156511968&usg=AOvVaw2vgfqObmc2IFbSO_PbirvZ)\] System Threads (shyouhei)
- \[Feature [#13518](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13518&sa=D&source=editors&ust=1686087156512303&usg=AOvVaw2e65VBJ0pKjyUbjrcTzIeu)\] Indented multiline comments (shyouhei)
- \[Feature [#13516](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13516&sa=D&source=editors&ust=1686087156512643&usg=AOvVaw1CID3YzNqvKyfIoDR7yGjA)\] Improve the text of the circular require warning (shyouhei)
- \[Feature [#13532](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13532&sa=D&source=editors&ust=1686087156513009&usg=AOvVaw3S9F32ECstLUxDT0J9dY9Y)\] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)

- shyouhei: is this by design or…?
- akr: it’s ok to add.  Patch please.

- \[Bug [#13535](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13535&sa=D&source=editors&ust=1686087156513552&usg=AOvVaw1VAoFAD1tXqwi_Z3bpSKIp)\] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?
- \[Feature [#13552](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13552&sa=D&source=editors&ust=1686087156513886&usg=AOvVaw2iR4NUqMaTR-QgzffLxrZT)\] \[PATCH 0/2\] reimplement ConditionVariable, Queue, SizedQueue using ccan/list (shyouhei)
- \[Feature [#13568](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13568&sa=D&source=editors&ust=1686087156514270&usg=AOvVaw0bYuJ5ofufaGP_pwZLixGu)\] File#path for O\_TMPFILE fds are unmeaning (sorah)

- sorah: It makes no sense for O\_TMPFILE to have a path
- nobu: but should it be nil?  There will be no way to obtain underlying filesystem then.
- sorah: it might make sense to have a separate method that returns the “argument string passed to File.new”.
- akr: that sounds too big.
- akr: File#path is used as inspectation on logs. therefore it should return informational string
- naruse: \`ls -al ll /proc/25378/fd/9\` returns “lrwx------ 1 naruse 64 May 19 17:13 9 -> /tmp/#14379824 (deleted)”

- \[Feature [#13563](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13563&sa=D&source=editors&ust=1686087156515276&usg=AOvVaw1a-rMwtAA5NF6tofVqz2J7)\] Implement Hash#choice method. (shyouhei)
- \[Feature [#13562](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13562&sa=D&source=editors&ust=1686087156515676&usg=AOvVaw2ORAGE_cmGHx9LZmECGkMZ)\] Use a sized enumerator with #yield\_self (shyouhei) needs?

- \[Feature [#13056](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13056&sa=D&source=editors&ust=1686087156516020&usg=AOvVaw0m4XDCXguOzsod5ughtFeQ)\] base option to Dir.glob (nobu)

- akr: how about it?
- matz: OK.
- shyouhei: what is expected for the base: argument?
- nobu: a path string, or an instance of Dir if openat(2) is available.

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

- \[Feature [#8661](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8661&sa=D&source=editors&ust=1686087156517195&usg=AOvVaw1BMLB5RCDdwsmcYCWe3In1)\] Backstrace in reverse order. The OP asked for an option but this is currently always the case for the toplevel exception handler. Is this OK for compatibility? It is surprising for the least (See comment 6). Can we have matz's opinion? (eregon)

- shyouhei: test-unit scrumbles backtrace.
- hsbt: I want to hear other application developer’s voice
- mrkn: what about show frame # ?
- naruse: would like to observe PR1’s response.
