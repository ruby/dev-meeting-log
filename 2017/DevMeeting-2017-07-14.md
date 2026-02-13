---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-07-14

Date: 2017/07/14 (Fri)
Time: 14:00- 18:00 (JST)
Place: Cookpad, Inc.
Sign-up: https://ruby.connpass.com/event/60055/
Attendees:
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

- [Feature #13396] Net::HTTP has no write timeout (shyouhei)
- [Bug #13407] We have recv_nonblock but not send_nonblock... can we add it? (shyouhei)
- [Feature #13395] Add a method to check for not nil (shyouhei)
- [Feature #13512] System Threads (shyouhei)
- [Feature #13516] Improve the text of the circular require warning (shyouhei)
- [Feature #13532] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)
- [Bug #13535] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?
- [Feature #13568] File#path for O_TMPFILE fds are unmeaning (sorah)
- [Feature #13563] Implement Hash#choice method. (shyouhei)
- Previous bugs that were not assigned (shyouhei)
    - [Bug #13350] File.read :newline option not respected on Linux
    - [Feature #13389] [PATCH] POP3 support timeout for TLS handshake
    - [Bug #13404] Hash#any? yields arguments to lambdas with proc semantics
    - [Bug #13429] Net::SMTP has no read timeout when connexion over TLS
    - [Bug #13513] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages
    - [Bug #13501] Process.kill behaviour for negative pid is not documented and may be wrong
    - [Bug #13521] [PATCH] Add fallback for DNS resolver registry key on Wine
    - [Bug #13557] there's no way to pass backtrace locations as a massaged backtrace
    - [Feature #10674] Net::HTTP retries idempotent requests once after a timeout, but its not configurable
    - [Bug #13542] MinGW trunk Builds - Summary of Issues
        - [Bug #13549] MinGW / Windows encoding - Two issues
        - [Bug #13556] MinGW readline Alt / Meta keys
        - [Bug #13569] Windows - TestRubyOptions#test_search - append to paths instead of replacing
    - [Bug #13564] Exception message management

## From attendees

- [Feature #13618]&nbsp;[PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (shyouhei)
- [Feature #13581]&nbsp;Syntax sugar for method reference (shyouhei)
- [Feature #13583]&nbsp;Adding `Hash#transform_keys` method (shyouhei)
- [Feature #13396]&nbsp;Net::HTTP has no write timeout (shyouhei)
- [Feature #13587]&nbsp;Improve performance of string interpolation (shyouhei)
- [Bug #13438]&nbsp;Fix heap overflow due to configure.in not being updated for HEAP_* -> HEAP_PAGE_* variable renaming (shyouhei)
- [Misc #12529]&nbsp;LEGAL file covering all the license information within Ruby (shyouhei)
- [Feature #13600]&nbsp;yield_self should be chainable/composable (shyouhei)
- [Misc #13597]&nbsp;Does read_nonblock call remalloc for the buffer if does it just set the size attribute (shyouhei)
- [Feature #13606]&nbsp;Enumerator equality and comparison (shyouhei)
- [Feature #13604]&nbsp;Exposing alternative interface of readline (shyouhei)
- [Feature #13608]&nbsp;Add TracePoint#thread (shyouhei)
- [Feature #13602]&nbsp;Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)
- [Feature #13613]&nbsp;Prefer that require/require_relative/load to tell us permission error if the target file is unreadable (shyouhei)
- [Feature #13620]&nbsp;Simplifying MRI's build system: always install (shyouhei)
- [Feature #13630]&nbsp;:[] method should accept block in nice syntax (shyouhei)
- [Feature #13639]&nbsp;Add "RTMIN" and "RTMAX" to Signal.list (shyouhei)
- [Feature #11484]&nbsp;add output offset for readpartial/read_nonblock/etc (shyouhei)
- [Feature #9001]&nbsp;Please package better standard library (shyouhei)
- [Feature #13563]&nbsp;Implement Hash#choice method. (shyouhei)
- [Feature #12533]&nbsp;Refinements: allow modules inclusion, in which the module can call internal methods which it defines.  (shyouhei)
- [Feature #13653]&nbsp;Bundled zlib helper (shyouhei)
- [Feature #12589]&nbsp;VM performance improvement proposal (shyouhei)
- [Feature #13626]&nbsp;Add String#byteslice! (shyouhei)
- [Feature #13551]&nbsp;Add a method to alias class methods (shyouhei)
- [Feature #13332]&nbsp;Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13378]&nbsp;Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381]&nbsp;[PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Feature #13676]&nbsp;to_s method is not overriden for Set (shyouhei)
- [Feature #13677]&nbsp;Add more details to error "Name or service not known (SocketError)" (shyouhei)
- [Feature #10771]&nbsp;An easy way to get the source location of a constant (shyouhei)
- [Feature #13665]&nbsp;String#delete_suffix (shyouhei)
- [Feature #9323]&nbsp;IO#writev (shyouhei)
- [Feature #13686]&nbsp;Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (shyouhei)
- [Feature #13693]&nbsp;Allow String#to_i and / or Kernel::Integer to parse e-notation (shyouhei)
- [Feature #13434]&nbsp;better method definition in C API (shyouhei)
- [Feature #13696]&nbsp;Add exchange and noreplace options to File.rename (shyouhei)
- [Feature #13683]&nbsp;Add strict Enumerable#single (shyouhei)
- [Misc #13704]&nbsp;Exclude Changelog files from documentation. (shyouhei)
- [Feature #13666]&nbsp;Development: Writing test code for new features/bug fixes can be done as specs under spec/rubyspec instead of tests under test/ (shyouhei)
- [Feature #13713]&nbsp;socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
- [Feature #13715]&nbsp;[PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
- [Feature #13719]&nbsp;[PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
- [Feature #13721]&nbsp;[PATCH] net/imap: dedupe attr keys in Net::IMAP::FetchData (shyouhei)
- [Feature #13732]&nbsp;Precise Time.now on Windows (shyouhei)
- [Feature #13733]&nbsp;Dump the delegator instead of the delegated object (shyouhei)
- [Feature #13625]&nbsp;BigDecimal short form / shorthand (shyouhei)
- [Feature #13712]&nbsp;String#start_with? with regexp (shyouhei)
- bugs that are not assigned (shyouhei)
    * [Bug #13586]&nbsp;Ruby hangs when accessing array which is modified in instance_eval after Coverage.start
    * [Bug #13574]&nbsp;Method redefinition warning
    * [Bug #13616]&nbsp;Zlib::GzipReader#pos underflows after calling #ungetbyte or #ungetc at start of file
    * [Bug #13593]&nbsp;Addrinfo#== behaves oddly
    * [Bug #13631]&nbsp;Cannot disable site and vendor directories
    * [Bug #13647]&nbsp;Some weird behaviour with keyword arguments
    * [Bug #13649]&nbsp;Net::IMAP doesn't support response from a Microsoft Exchange server (which is not compliant with RFC standards)
    * [Bug #13654]&nbsp;irb save-history extension is not concurrency-safe
    * [Bug #13655]&nbsp;external encoding named "-" (doc issue or…?)
    * [Bug #13660]&nbsp;rb_str_hash_m discards bits from the hash
    * [Bug #13671]&nbsp;Regexp with lookbehind and case-insensitivity raises RegexpError only on strings with certain characters
    * [Bug #13674]&nbsp;BigDecimal comparison with Float::INFINITY is erroneous in 2.2.x and 2.3.x
    * [Bug #13675]&nbsp;Should Zlib::GzipReader#ungetc accept nil?
    * [Bug #13700]&nbsp;Enumerable#sum may not work for Ranges subclasses due to optimization
    * [Bug #13350]&nbsp;File.read :newline option not respected on Linux
    * [Bug #13705]&nbsp;[PATCH] `cfp consistency error' occurs when raising exception in bmethod call event
    * [Bug #13716]&nbsp;Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13670]&nbsp;[BUG] Bus Error at 0xefce7b (armv7l) (ruby 2.3.4p301)
    * [Bug #13731]&nbsp;inode for Windows on ReFS
    * [Bug #13735]&nbsp;Initialization-error of sortedset

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

- [Feature #13666] Allow to write specs instead of tests (nobody wants to write test code twice). (eregon)
- [Feature #13665] Before I added String#delete_prefix. For symmetry, is it okay to commit String#delete_suffix? (sonots)
- [Feature #13743] Support linking of files opened with O_TMPFILE (mmasaki)

# Log

# Date: 2017/07/14 (Fri)
Time: 14:00- 18:00 (JST)
Place: Cookpad, Inc. Headquarter
Sign-up: [https://ruby.connpass.com/event/60055/](https://www.google.com/url?q=https://ruby.connpass.com/event/60055/&sa=D&source=editors&ust=1686087205330655&usg=AOvVaw2GuqpQAOLTu7TpnTrk0Twr)
log edit:
Attendees: duerst (Martin Dürst)
Language: mostly Japanese (sorry for non native Japanese speakers)

# Agenda

- next

- 8/31
- at Cookpad HQ, Yebisu

- next asofter

- 9/25
- at Speee HQ, Roppongi.

# About 2.5 timeframe

# [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087205332186&usg=AOvVaw2hCtA_7P-leo4qLTdg0RKR)


- naruse: I’ll release PR1 at the end of this month
- naruse: I’d like to see k0kubun’s LLVM extension to be doable without patches.
- shyouhei: what change?
- naruse: exporting APIs that is internal right now.
- ko1: we need to make APIs clean before doing so.

## Carry-over from previous meeting(s)

## \[Feature [#13512](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13512&sa=D&source=editors&ust=1686087205333390&usg=AOvVaw1oRkAAHGIMfzkaxBA5tF1P)\] System Threads (shyouhei)


- ko1: I don’t like this idea.  I don’t want more states over a Thread.
- matz: I feel needs of more explainations.

- Previous bugs that were not assigned (shyouhei)


## \[Bug [#13350](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13350&sa=D&source=editors&ust=1686087205334279&usg=AOvVaw3juizL8nSXxN-kJSJsagco)\] File.read :newline option not respected on Linux


- nobu: on Linux we have to explicitly set text mode.
- akr: text mode is the default mode when not specified.
- nobu: then we should automatically assume text mode when newline: is specified.
- matz: make it so,

## \[Feature [#13389](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13389&sa=D&source=editors&ust=1686087205335100&usg=AOvVaw3v0mN0f6Lf6PFN4W2sxvTb)\] \[PATCH\] POP3 support timeout for TLS handshake


- hsbt: pop3 has no maintainer.
- naruse: maybe shugo can review the patch?

## \[Bug [#13404](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13404&sa=D&source=editors&ust=1686087205335718&usg=AOvVaw1nkGRmSqoGbvH7O52oIy-K)\] Hash#any? yields arguments to lambdas with proc semantics


- assign ko1
- cf: #13391

## \[Bug [#13429](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13429&sa=D&source=editors&ust=1686087205336384&usg=AOvVaw1I2954ufU_K5cWL38BAryE)\] Net::SMTP has no read timeout when connexion over TLS


- should be closed so that usa can be aware of it.

## \[Bug [#13513](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13513&sa=D&source=editors&ust=1686087205336834&usg=AOvVaw1Cu-1QET4rWRRqFiQOrFm4)\] Resolv::DNS::Message.decode hangs after detecting truncation in UDP messages


- assign akr

## \[Bug [#13501](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13501&sa=D&source=editors&ust=1686087205337383&usg=AOvVaw0GuXcpAo0Ebz1EpK8XkqTU)\] Process.kill behaviour for negative pid is not documented and may be wrong


- shyouhei: doc issue?
- akr: we take negative signal or negative pids.  signal.c:rb\_f\_kill() has special codes for negative signals but not for negative pids.
- akr: what happens both signal and pid are negative?

## \[Bug [#13521](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13521&sa=D&source=editors&ust=1686087205338050&usg=AOvVaw1eQ0bi0LlDCSzROTH9isY6)\] \[PATCH\] Add fallback for DNS resolver registry key on Wine


- nobu: 3rd party issue?

## \[Bug [#13557](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13557&sa=D&source=editors&ust=1686087205338680&usg=AOvVaw2FR90c_szr8-PzUmvukYec)\] there's no way to pass backtrace locations as a massaged backtrace


- nobu: I have to discuss this with ko1.

## \[Feature [#10674](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10674&sa=D&source=editors&ust=1686087205339166&usg=AOvVaw1_hrNOdQhCLc08nbykFIyk)\] Net::HTTP retries idempotent requests once after a timeout, but its not configurable


- assign naruse

## \[Bug [#13542](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13542&sa=D&source=editors&ust=1686087205339788&usg=AOvVaw3sL5x0vSW7sqIvyWzVSM5s)\] MinGW trunk Builds - Summary of Issues


## \[Bug [#13549](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13549&sa=D&source=editors&ust=1686087205340197&usg=AOvVaw2hWJh5jaIsHVLvV-5rec34)\] MinGW / Windows encoding - Two issues

## \[Bug [#13556](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13556&sa=D&source=editors&ust=1686087205340605&usg=AOvVaw1R3JyV1EhQRvB1mXD-9JkK)\] MinGW readline Alt / Meta keys

## \[Bug [#13569](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13569&sa=D&source=editors&ust=1686087205340978&usg=AOvVaw1ePy4_g8xpOQ77PtddT4ht)\] Windows - TestRubyOptions#test\_search - append to paths instead of replacing

- \-----
- nobu: I cannot reproduce these problems.
- shyouhei: It seems the reporter is not eligible to fix them.
- hsbt: I made an environment so I’ll see if they reproduce.

## \[Bug [#13564](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13564&sa=D&source=editors&ust=1686087205341613&usg=AOvVaw2iT4c51EJv_LfWMTxY7kjI)\] Exception message management


- matz I understand that it breaks encapsulation \_by theory\_, but in practice is it worth, for instance, duplicate every time?

## From attendees

## \[Bug #13737\] "can't modify frozen String" when installing bundled gems (ko1)


- should what happen for tainted string?
- ko1: it seems there is string.c:fstring\_cmp.  Can’t we change this function?

## \[Feature [#13618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13618&sa=D&source=editors&ust=1686087205342542&usg=AOvVaw1SJPEKUC1GyYKSwAaA7a7R)\] \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (shyouhei)


- akr: Socket to DB would normally be pooled / shared among threads in normal web applications.  There shall be some locking mechanism to do so.

## \[Feature [#13583](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13583&sa=D&source=editors&ust=1686087205343077&usg=AOvVaw0NERbGFoTuSDgQDiHLrX8c)\] Adding Hash#transform\_keys method (shyouhei)


- matz: seems OK.

## \[Feature [#12589](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12589&sa=D&source=editors&ust=1686087205343574&usg=AOvVaw3OnEyP0awwJxgjvtxhL4BW)\] VM performance improvement proposal (shyouhei)


- akr: is it OK for people to have C compilers in their production environment?
- mrkn: normal people need compilers anyways because bundle install needs one.
- ko1: I think scheme compilers tends to transpile into C.
- matz: I like this idea itself.

## \[Feature [#13676](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13676&sa=D&source=editors&ust=1686087205344473&usg=AOvVaw12roiV9BIxWJ1mKmHfJbvv)\] to\_s method is not overriden for Set (shyouhei)


- assign knu
- shyouhei: what is wanted?
- akr: seems the intension is to call to\_a internally
- knu: is it OK to call inspect?
- matz: OK.

## \[Feature [#10771](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10771&sa=D&source=editors&ust=1686087205345230&usg=AOvVaw0t3sWBC1vtd8g_FyrBpF6i)\] An easy way to get the source location of a constant (shyouhei)


- nobu: we already maintain constant source locations for redefinition warnings.

## \[Feature [#13434](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13434&sa=D&source=editors&ust=1686087205345685&usg=AOvVaw1WkrfwQvIzMMj8OwICC6iY)\] better method definition in C API (shyouhei)


- ko1: is it convenient for you to show backtrace of C methods?
- akr: do we have to know that info?
- naruse: I have wanted C level stack trace for a long time.

- bugs that are not assigned (shyouhei)


## \[Bug [#13586](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13586&sa=D&source=editors&ust=1686087205346586&usg=AOvVaw2aRUKF8aDoR3DdqbrkFyKM)\] Ruby hangs when accessing array which is modified in instance\_eval after Coverage.start


- assign nobu
- nobu: seems 2.5 is not affected.

## \[Bug [#13574](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13574&sa=D&source=editors&ust=1686087205347076&usg=AOvVaw2KRQgpczmOqexg2QBovB80)\] Method redefinition warning


- akira: is it OK that we say this is the right way to suppress warning?
- matz: reject.

## \[Bug [#13616](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13616&sa=D&source=editors&ust=1686087205347715&usg=AOvVaw3smcBPrJD3MRheCx9MdXmU)\] Zlib::GzipReader#pos underflows after calling #ungetbyte or #ungetc at start of file


- naruse: I’ll merge this.

## \[Bug [#13593](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13593&sa=D&source=editors&ust=1686087205348317&usg=AOvVaw0Pfq7mZWwZxH0Qc07XIvg1)\] Addrinfo#== behaves oddly


- akr: it’s difficult by theory.

## \[Bug [#13631](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13631&sa=D&source=editors&ust=1686087205348924&usg=AOvVaw0OoQkMmTTPx3dIMGgElO6q)\] Cannot disable site and vendor directories


- assign nobu

## \[Bug [#13647](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13647&sa=D&source=editors&ust=1686087205349362&usg=AOvVaw2WJ_C4IabgIR8r-TtI50pd)\] Some weird behaviour with keyword arguments


- nobu: we can explain this behaviour.

## \[Bug [#13649](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13649&sa=D&source=editors&ust=1686087205349808&usg=AOvVaw1WxeTUQkczA0AH-pxm48JZ)\] Net::IMAP doesn't support response from a Microsoft Exchange server (which is not compliant with RFC standards)


- assign shugo

## \[Bug [#13654](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13654&sa=D&source=editors&ust=1686087205350187&usg=AOvVaw1AJZIFku61yPU6XwOG8f6z)\] irb save-history extension is not concurrency-safe


- akr: it’s same as shells.
- assign keiju

## \[Bug [#13655](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13655&sa=D&source=editors&ust=1686087205350664&usg=AOvVaw3gQmnkqeMXEkrVw2J5g3xk)\] external encoding named "-" (doc issue or…?)


- naruse: doc issue.

## \[Bug [#13660](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13660&sa=D&source=editors&ust=1686087205351069&usg=AOvVaw0hxXYQlLknRQ7P5abMgFfY)\] rb\_str\_hash\_m discards bits from the hash


- akr: it’s intentional.

## \[Bug [#13671](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13671&sa=D&source=editors&ust=1686087205351458&usg=AOvVaw3fPDCG-bxs-ltk9I2ZsIaM)\] Regexp with lookbehind and case-insensitivity raises RegexpError only on strings with certain characters


- naruse: /(?<!ass)/iu is suffient to reproduce
- martin:

- irb(main):004:0> /ßs/i =~ "sß"
- \=> 0
- irb(main):005:0> /sß/i =~ "ßs"
- \=> nil
- But I don't know the expected behavior.

## \[Bug [#13735](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13735&sa=D&source=editors&ust=1686087205352246&usg=AOvVaw39fIfz4A5Jg6CiBMVLiO84)\] Initialization-error of sortedset


- assign knu

## From non-attendees

## Write your name and your interest (what do you want to ask and to whom?) please.

## \[Feature [#13666](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13666&sa=D&source=editors&ust=1686087205352871&usg=AOvVaw3Po-o362aVMKWIvFedFf0O)\] Allow to write specs instead of tests (nobody wants to write test code twice). (eregon)


- Martin: Okay to me.
- nobu: nobody disallows to write specs at the first place, do they?

## \[Feature [#13665](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13665&sa=D&source=editors&ust=1686087205353342&usg=AOvVaw2wUqCkIJVzi2pQ2d0sIUHQ)\] Before I added String#delete\_prefix. For symmetry, is it okay to commit String#delete\_suffix? (sonots)


- shyouhei: should we add it?
- matz: no objection.

## \[Feature [#13743](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13743&sa=D&source=editors&ust=1686087205353899&usg=AOvVaw2f33NXuymDNo2g9nlJCe7g)\] Support linking of files opened with O\_TMPFILE (mmasaki)


- nobu: File.link or File#link?
- akr: what about support linkat(2) instead of a Linux-specific code in core?
- matz: I think the proposed API is the simplest.  However platform specific.
- Martin: what about making it as a gem?
- akr: linkat(2) is at POSIX since 2008.
- matz: reject this particular proposal.  Think linkat(2) request when such thing requested separatedly.
