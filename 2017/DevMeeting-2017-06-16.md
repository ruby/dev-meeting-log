---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-06-16

Date: 2017/06/16 (Fri)
Time: 14:00- 18:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/57968/
Attendees: duerst (Martin Dürst)
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

- [Feature #12733] Bundle bundler to ruby core (shyouhei) updates?
- [Feature #13332] Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13333] block to yield (shyouhei)
- [Feature #13378] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381] [PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Feature #13385] [PATCH] Make Resolv::DNS::Name validation similar to host and dig commands (shyouhei)
- [Feature #13383] [PATCH] Module#source_location (shyouhei)
- [Feature #12063] KeyError#receiver and KeyError#name (shyouhei) status?
- [Bug #13397] #object_id should not be signed (shyouhei)
- [Feature #13396] Net::HTTP has no write timeout (shyouhei)
- [Bug #13407] We have recv_nonblock but not send_nonblock... can we add it? (shyouhei)
- [Feature #13395] Add a method to check for not nil (shyouhei)
- [Feature #13383] [PATCH] Module#source_location (shyouhei)
- [Feature #13434] better method definition in C API (shyouhei)
- [Feature #13512] System Threads (shyouhei)
- [Feature #13518] Indented multiline comments (shyouhei)
- [Feature #13516] Improve the text of the circular require warning (shyouhei)
- [Feature #13532] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)
- [Bug #13535] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?
- [Feature #13568] File#path for O_TMPFILE fds are unmeaning (sorah)
- [Feature #13563] Implement Hash#choice method. (shyouhei)
- Previous bugs that were not assigned (shyouhei)
    - [Bug #13196] Improve keyword argument errors when non-keyword arguments given
    - [Bug #13320] rescue blocks get an entry in backtrace locations
    - [Bug #13336] Default Parameters don't work
    - [Bug #13337] Eval and Later Defined Local Variables
    - [Bug #13350] File.read :newline option not respected on Linux
    - [Bug #13373] FileUtils methods for copy, move and remove directories is not providing a decent error trace for letting know if it was success or fail
    - [Bug #13101] Date#rfc2822 and Time#rfc2822 don't return the same format
    - [Bug #12684] Delegator#eql? missing
    - [Feature #13389] [PATCH] POP3 support timeout for TLS handshake
    - [Bug #13404] Hash#any? yields arguments to lambdas with proc semantics
    - [Bug #13413] --with-static-linked-ext doesn't install extension files on `make install`
    - [Bug #13429] Net::SMTP has no read timeout when connexion over TLS
    - [Bug #13432] set_trace_funcにproc->is_from_method = TRUEのオブジェクトを渡し、SystemStackErrorを発生させるとRubyVMが停止する
    - [Bug #13446] refinements with prepend for module has strange behavior
    - [Bug #13498] Weakref, Weakmap and define_finalizer don't work on frozen objects
    - [Bug #13513] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages
    - [Bug #13501] Process.kill behaviour for negative pid is not documented and may be wrong
    - [Bug #13515] Pathname#join doesn't add separator on UNC paths
    - [Bug #13521] [PATCH] Add fallback for DNS resolver registry key on Wine
    - [Bug #13537] ruby crash in rb_gc_mark
    - [Bug #13548] miniruby SEGV while building with non-default CFLAGS (caused by __builtin_setjmp)
    - [Bug #13555] Disable TestTrace#test_trace_stackoverflow
    - [Bug #13557] there's no way to pass backtrace locations as a massaged backtrace
    - [Bug #10290] segfault when calling a lambda recursively after rescuing SystemStackError
        - Fix confirmed?
    - [Feature #10674] Net::HTTP retries idempotent requests once after a timeout, but its not configurable
    - [Bug #13542] MinGW trunk Builds - Summary of Issues
        - [Bug #13549] MinGW / Windows encoding - Two issues
        - [Bug #13556] MinGW readline Alt / Meta keys
        - [Bug #13569] Windows - TestRubyOptions#test_search - append to paths instead of replacing
    - [Bug #13564] Exception message management

## From attendees

- [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (ko1)
- [Bug #13576] File#to_path shall be deleted (shyouhei)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [Feature #13618] [PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (normalperson, ko1)
* [Feature #12694] Want a String method to remove heading substr.
  * (sonots) I implemented with a name `String#remove_prefix`. If the name is ok, I want to merge it. Matz, could you decide? There were other candidates such as lchomp, trim_prefix. I dropped trim_prefix because trim is used to remove a list of characters rather than a substring in some languages, so I felt trim_prefix would raise confusion. I dropped lchomp because I just had never heard.

# Log

# Date: 2017/06/16 (Fri)
Time: 14:00- 18:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: [https://ruby.connpass.com/event/57968/](https://www.google.com/url?q=https://ruby.connpass.com/event/57968/&sa=D&source=editors&ust=1686087184887067&usg=AOvVaw2OZBMdpPJxVI_yu1jp1ICr)
log edit: [https://docs.google.com/document/d/1z19pKt8jlpiEUR3RnWWBCfs3OR\_hbiAZMwpQ6ZTllP0/edit#](https://www.google.com/url?q=https://docs.google.com/document/d/1z19pKt8jlpiEUR3RnWWBCfs3OR_hbiAZMwpQ6ZTllP0/edit%23&sa=D&source=editors&ust=1686087184887495&usg=AOvVaw3VWTshutgCtNIdMFjYy2g1)
Attendees: duerst (Martin Dürst)
Language: mostly Japanese (sorry for non native Japanese speakers)

# Agenda

- next

- 7/14, 14:00~
- at Cookpad HQ, Yebisu

- next after

- 8/31
- at Cookpad HQ, Yebisu

# About 2.5 timeframe

# [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087184888539&usg=AOvVaw043T4verO1u-pRUKG6Mc_E)


## Carry-over from previous meeting(s)

## \[Feature [#12733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12733&sa=D&source=editors&ust=1686087184889024&usg=AOvVaw0gHwd6vDBbqp7qSRzuJBV-)\] Bundle bundler to ruby core (shyouhei) updates?


- hsbt: rubygems/rubygems 2.7 requires bundler.  After it is released, when ruby-core merges that version, we have to take care.

- import bundler for test then?
- if so why only for test?

- ko1: is there any blocker?
- hsbt: rspec.

## \[Feature [#13332](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13332&sa=D&source=editors&ust=1686087184889733&usg=AOvVaw1CcdqiB6YdbxIhbWJbFX_1)\] Forwardable#def\_instance\_delegator nil (shyouhei)


- assign nobu

## \[Feature [#13333](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13333&sa=D&source=editors&ust=1686087184890295&usg=AOvVaw0GqC30q3mUgemm9FBfOuIb)\] block to yield (shyouhei)


- ko1: we need more use cases.
- martin: nobu: add them to the ticket.

## \[Feature [#13378](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13378&sa=D&source=editors&ust=1686087184890861&usg=AOvVaw01R3vn2nSGuEbMj6mswm8d)\] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)


- nobu: I don’t want to handle fds this way because we can’t close properly on exceptions. I think we should wrap them using IOs.

## \[Feature [#13381](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13381&sa=D&source=editors&ust=1686087184891306&usg=AOvVaw2zjJLQ9Mw0cLcrODpULDzV)\] \[PATCH\] Expose rb\_fstring and its family to C extensions (shyouhei)


- ko1: how about it? I don’t like “f”string naming.
- nobu: other languages call it “intern”
- ko1: true but we already have rb\_intern().
- ko1: nobody has idea. so that nobu and ko1 will decide it. nobody against exposing this functionality.

## \[Feature [#13385](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13385&sa=D&source=editors&ust=1686087184891916&usg=AOvVaw2RVOIyAr1VhU4Gt8NHdOS7)\] \[PATCH\] Make Resolv::DNS::Name validation similar to host and dig commands (shyouhei)


- akr: I can’t say if this is valid until I fully understand the RFC.
- akira: what about using domain\_name gem?
- assign akr.

## \[Feature [#13383](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13383&sa=D&source=editors&ust=1686087184892450&usg=AOvVaw1EEHWUhq4dhMc6OymCu0aP)\] \[PATCH\] Module#source\_location (shyouhei)


- akira: needs?
- shyouhei: I think source\_locations makes more sense than source\_location.
- akira: I started to confuse.  Is it useful?  We can already get Method#source\_location.

## \[Feature [#12063](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12063&sa=D&source=editors&ust=1686087184893030&usg=AOvVaw2Fo_FzZrLeD7RWEGzoofyN)\] KeyError#receiver and KeyError#name (shyouhei) status?


- matz: I take KeyError#key.

## \[Bug [#13397](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13397&sa=D&source=editors&ust=1686087184893580&usg=AOvVaw3zo4Xb1qFIH5rUZyPSr234)\] #object\_id should not be signed (shyouhei)


- akr: isn’t it possible to somehow calculate so that we can avoid negative object\_id?  Because pointers are 8 byte aligned, while positive fixnums are of 262 space.
- nobu: it’s true we can avoid negative numbers on some environment, but not always.
- reject this specific issue and request for new issue that address the inspect helper.

## \[Feature [#13396](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13396&sa=D&source=editors&ust=1686087184894240&usg=AOvVaw1vefgo5p62kDz6Zt2V-Onx)\] Net::HTTP has no write timeout (shyouhei)

## \[Bug [#13407](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13407&sa=D&source=editors&ust=1686087184894587&usg=AOvVaw010kIpPf30MdMEq_ZGIxol)\] We have recv\_nonblock but not send\_nonblock... can we add it? (shyouhei)

## \[Feature [#13395](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13395&sa=D&source=editors&ust=1686087184894947&usg=AOvVaw1dSl1pcnJg6bUAWWDKWZME)\] Add a method to check for not nil (shyouhei)

## \[Feature [#13434](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13434&sa=D&source=editors&ust=1686087184895275&usg=AOvVaw12wiXOGVB4bD4Dgc6Zvrrn)\] better method definition in C API (shyouhei)


- nobu: I think parsing string is not a good idea.

## \[Feature [#13512](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13512&sa=D&source=editors&ust=1686087184895695&usg=AOvVaw1ZVaRuFFe3tgN5RfwBkN5a)\] System Threads (shyouhei)

## \[Feature [#13518](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13518&sa=D&source=editors&ust=1686087184896015&usg=AOvVaw1sXeOWCScQIvhZiiCrMi8k)\] Indented multiline comments (shyouhei)

## \[Feature [#13516](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13516&sa=D&source=editors&ust=1686087184896391&usg=AOvVaw0F00iM2ALPgPaq05Ic1Mys)\] Improve the text of the circular require warning (shyouhei)

## \[Feature [#13532](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13532&sa=D&source=editors&ust=1686087184896734&usg=AOvVaw0kcDakKJAaX2XxL-cWPSb-)\] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)

## \[Bug [#13535](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13535&sa=D&source=editors&ust=1686087184897069&usg=AOvVaw2uatNbvc6g9yzT1sHwl1Vx)\] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?

## \[Feature [#13568](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13568&sa=D&source=editors&ust=1686087184897401&usg=AOvVaw0lOVxztWlY59eqrQVGmHHz)\] File#path for O\_TMPFILE fds are unmeaning (sorah)

## \[Feature [#13563](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13563&sa=D&source=editors&ust=1686087184897740&usg=AOvVaw1J_tfM5W2xRrzg0lc3Uxoa)\] Implement Hash#choice method. (shyouhei)

- Previous bugs that were not assigned (shyouhei)


## \[Bug [#13196](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13196&sa=D&source=editors&ust=1686087184898244&usg=AOvVaw2s9kAK_W1dClDf6-K_fI5S)\] Improve keyword argument errors when non-keyword arguments given

## \[Bug [#13320](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13320&sa=D&source=editors&ust=1686087184898590&usg=AOvVaw0aF6yt42Xx2uNx1-w2qjwI)\] rescue blocks get an entry in backtrace locations

## \[Bug [#13336](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13336&sa=D&source=editors&ust=1686087184898914&usg=AOvVaw2xD22Y6Etgd6P1c6GQNaR3)\] Default Parameters don't work

## \[Bug [#13337](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13337&sa=D&source=editors&ust=1686087184899255&usg=AOvVaw2ujMtb45viBHo_OAOKh5pU)\] Eval and Later Defined Local Variables

## \[Bug [#13350](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13350&sa=D&source=editors&ust=1686087184899627&usg=AOvVaw0Y37tTZsLOSHz6PvnlXlYT)\] File.read :newline option not respected on Linux

## \[Bug [#13373](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13373&sa=D&source=editors&ust=1686087184899976&usg=AOvVaw0iPn3ks7iHAVt-YCOzHRr5)\] FileUtils methods for copy, move and remove directories is not providing a decent error trace for letting know if it was success or fail

## \[Bug [#13101](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13101&sa=D&source=editors&ust=1686087184900315&usg=AOvVaw0kLgayEfaLiOOFxoiHOKLQ)\] Date#rfc2822 and Time#rfc2822 don't return the same format

## \[Bug [#12684](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12684&sa=D&source=editors&ust=1686087184900653&usg=AOvVaw2qHpzrmz4YU_aecfWQDLKQ)\] Delegator#eql? missing

## \[Feature [#13389](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13389&sa=D&source=editors&ust=1686087184900993&usg=AOvVaw0dSpBqNXGYJqsQaEz9JdUx)\] \[PATCH\] POP3 support timeout for TLS handshake

## \[Bug [#13404](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13404&sa=D&source=editors&ust=1686087184901373&usg=AOvVaw1jNhbQ54aLoDrwfGpR0nMU)\] Hash#any? yields arguments to lambdas with proc semantics

## \[Bug [#13413](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13413&sa=D&source=editors&ust=1686087184901778&usg=AOvVaw0MUJmkOc2xjZzFAarrHnzv)\] --with-static-linked-ext doesn't install extension files on make install

## \[Bug [#13429](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13429&sa=D&source=editors&ust=1686087184902166&usg=AOvVaw2vUhYc__TmQwFvAFvik40b)\] Net::SMTP has no read timeout when connexion over TLS

## \[Bug [#13432](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13432&sa=D&source=editors&ust=1686087184902515&usg=AOvVaw0VhpLLckIFymF7S_SOKHGa)\] set\_trace\_funcにproc->is\_from\_method = TRUEのオブジェクトを渡し、SystemStackErrorを発生させるとRubyVMが停止する

## \[Bug [#13446](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13446&sa=D&source=editors&ust=1686087184902866&usg=AOvVaw2dccQS0f2yC7sz0PBrfNuW)\] refinements with prepend for module has strange behavior

## \[Bug [#13498](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13498&sa=D&source=editors&ust=1686087184903292&usg=AOvVaw1ofZUaZcw3yXfctIvzI_f5)\] Weakref, Weakmap and define\_finalizer don't work on frozen objects

## \[Bug [#13513](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13513&sa=D&source=editors&ust=1686087184903638&usg=AOvVaw3AnkJPxJEaO4T3Fhaey05W)\] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages

## \[Bug [#13501](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13501&sa=D&source=editors&ust=1686087184903981&usg=AOvVaw0dGD3hZmpaJAHSLzIgyqOq)\] Process.kill behaviour for negative pid is not documented and may be wrong

## \[Bug [#13515](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13515&sa=D&source=editors&ust=1686087184904325&usg=AOvVaw0639yqnZnSpsBlbTs0kC-X)\] Pathname#join doesn't add separator on UNC paths

## \[Bug [#13521](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13521&sa=D&source=editors&ust=1686087184904666&usg=AOvVaw2DI9jgBeqgpGn2F2Iz9ZnX)\] \[PATCH\] Add fallback for DNS resolver registry key on Wine

## \[Bug [#13537](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13537&sa=D&source=editors&ust=1686087184904990&usg=AOvVaw216houodGjR-03fVO552gj)\] ruby crash in rb\_gc\_mark

## \[Bug [#13548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13548&sa=D&source=editors&ust=1686087184905334&usg=AOvVaw2e7oo7kRI3i4HURpxAlppb)\] miniruby SEGV while building with non-default CFLAGS (caused by \_\_builtin\_setjmp)

## \[Bug [#13555](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13555&sa=D&source=editors&ust=1686087184905672&usg=AOvVaw3os2StQA6bZbMG9f_yTbwM)\] Disable TestTrace#test\_trace\_stackoverflow

## \[Bug [#13557](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13557&sa=D&source=editors&ust=1686087184906024&usg=AOvVaw07BpTjse2I4tatsCgyXDOT)\] there's no way to pass backtrace locations as a massaged backtrace

## \[Bug [#10290](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10290&sa=D&source=editors&ust=1686087184906356&usg=AOvVaw2wp1u4a8-HWNT9tZKdtpIJ)\] segfault when calling a lambda recursively after rescuing SystemStackError


- Fix confirmed?

- nobu: the variant of setjmp seems to be the root cause.  The proposed workaround works, but may impact performance.  We have to measure before changing the default.

## \[Feature [#10674](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10674&sa=D&source=editors&ust=1686087184906916&usg=AOvVaw3SUSEjy1EaXpxSgy3xSxyo)\] Net::HTTP retries idempotent requests once after a timeout, but its not configurable

## \[Bug [#13542](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13542&sa=D&source=editors&ust=1686087184907304&usg=AOvVaw2XWXtg-WuS09dTI9it5xU1)\] MinGW trunk Builds - Summary of Issues


## \[Bug [#13549](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13549&sa=D&source=editors&ust=1686087184907658&usg=AOvVaw3kIWSVox5-bKH-9eTSWyGr)\] MinGW / Windows encoding - Two issues

## \[Bug [#13556](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13556&sa=D&source=editors&ust=1686087184907991&usg=AOvVaw1g8diUekmroHrffreje6hN)\] MinGW readline Alt / Meta keys

## \[Bug [#13569](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13569&sa=D&source=editors&ust=1686087184908312&usg=AOvVaw276LVEhPv68k00UZ8QN46G)\] Windows - TestRubyOptions#test\_search - append to paths instead of replacing


## \[Bug [#13564](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13564&sa=D&source=editors&ust=1686087184908648&usg=AOvVaw0k8Swo5B7C6Dl_IFoZ-QRJ)\] Exception message management


## From attendees

## \[Bug [#12159](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12159&sa=D&source=editors&ust=1686087184909106&usg=AOvVaw14r73bZdmJllJm8eG3zqKP)\] Thread::Backtrace::Location#path returns absolute path for files loaded by require\_relative (ko1)


- ko1: I want to add Thread::Backtrace::Location#realpath to deprecate #absplute\_path instead.
- matz: no objection.

## \[Feature [#13618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13618&sa=D&source=editors&ust=1686087184909602&usg=AOvVaw3rADmKR6FcB1SuxI2rcT-5)\] \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (ko1)

- \[Bug [#13576](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13576&sa=D&source=editors&ust=1686087184909934&usg=AOvVaw1b5uOl23_juIpxUJaHDZpt)\] File#to\_path shall be deleted (shyouhei)

- akr: did you try deleting this method?
- shyouhei: no, but I should.
- akr: we must start from adding warnings.

## From non-attendees

## Write your name and your interest (what do you want to ask and to whom?) please.

## \[Feature [#13618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13618&sa=D&source=editors&ust=1686087184910659&usg=AOvVaw1G8dM5_WXvz1jRn3bJCqOH)\] \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (normalperson)


- ko1: programmers transfer execution of fibers by hand.  Eric proposes to introduce “auto”-Fiber which are OK to transfer each other on blocking situations. His implementation is great. rb\_wait\_for\_single\_fd is the ponit of switching. matz wanted this feature, too.
- shyouhei: can auto Fibers be a subject to transfer or an object?
- ko1: transfer happens only from an auto fiber to another and nothing else, AFAIK.
- ko1: This is essentially a re-introduction of Ruby 1.8 threads (without time-tick). I’m concerning about thread difficulties like locking.
- akr: is locking OK for auto Fibers?
- ko1: Fibers has not needed locking because so far, Fiber.yield has been the only mechanism to transfer execution so it was very explicit.  auto-Fiber changes that so there can be unintended transfers.
- matz: do you want to reject this?
- ko1: I don’t want to accept until we introduce a way to explicitly state where we can transfer, like async/await.
- ko1: I think it’s NG to call routines that are not Fiber-aware from proposed  auto-Fiber.  I don’t think people can insert appropriate Thread.exclusive calls because if they could, we could never have experienced resource contentions.
- matz: I think it’s hard to tag every and all of fiber-aware routines to be async.  That sounds too big.
- matz: show us your counter-proposal.
- ko1: is it OK for you to accept Eric’s proposal as-is?
- matz: I’m positive, but I know your concern so I won’t accept right now.

## \[Feature [#12694](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12694&sa=D&source=editors&ust=1686087184912007&usg=AOvVaw0_w94BMBmN-E2kPUSFzcnD)\] Want a String method to remove heading substr.


- (sonots) I implemented with a name String#remove\_prefix. If the name is ok, I want to merge it. Matz, could you decide? There were other candidates such as lchomp, trim\_prefix. I dropped trim\_prefix because trim is used to remove a list of characters rather than a substring in some languages, so I felt trim\_prefix would raise confusion. I dropped lchomp because I just had never heard.

- candidates discussed:


- lchomp
- chompl
- guilotine
- ltrim / rtrim

- NG because PHP behaves differently

- delete\_prefix

- amatsuda: remove\_prefix sounds strange if it doesn’t change the receiver.
- nobu: facets has lchomp.
- akr: I don’t want chomp’s complex newline handling for this requested method.
- ko1: remove\_prefix seems has no downsides.
- matz: accept delete\_prefix.
