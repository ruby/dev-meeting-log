---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-08-31

Date: 2017/08/31 (Fri)
Time: 14:00- 18:00 (JST)
Place: Cookpad, Inc.
Sign-up: https://ruby.connpass.com/event/60057/
Attendees: matz (Yukihiro Matsumoto), nobu (Nobuyoshi Nakada), ko1 (Koichi Sasada, partially), naruse (Yui Naruse), akr (Akira Tanaka), hsbt (Hiroshi SHIBATA), matsuda (Akira Matsuda), shyouhei (Shyouhei Urabe), sorah (Sorah Fukumori), mrkn (Kenta Murata), mame (Yusuke Endoh), kosaki (Motohiro KOSAKI), knu (Akinori MUSHA),  duerst (Martin Dürst)
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

## Hackathon for Ruby 2.5

* https://chouseisan.com/s?h=cbab178ad925432ca81f9a64b2563b95

## Invitation to "mini RubyKaigi" (a_matsuda)


## Carry-over from previous meeting(s)

- [Feature #13396] Net::HTTP has no write timeout (shyouhei)
- [Bug #13407] We have recv_nonblock but not send_nonblock... can we add it? (shyouhei)
- [Feature #13395] Add a method to check for not nil (shyouhei)
- [Feature #13516] Improve the text of the circular require warning (shyouhei)
- [Feature #13532] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)
- [Bug #13535] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?
- [Feature #13568] File#path for O_TMPFILE fds are unmeaning (sorah)
- [Feature #13563] Implement Hash#choice method. (shyouhei)
- [Feature #13581]&nbsp;Syntax sugar for method reference (shyouhei)
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
- [Feature #13626]&nbsp;Add String#byteslice! (shyouhei)
- [Feature #13551]&nbsp;Add a method to alias class methods (shyouhei)
- [Feature #13332]&nbsp;Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13378]&nbsp;Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381]&nbsp;[PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Feature #13677]&nbsp;Add more details to error "Name or service not known (SocketError)" (shyouhei)
- [Feature #9323]&nbsp;IO#writev (shyouhei)
- [Feature #13686]&nbsp;Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (shyouhei)
- [Feature #13693]&nbsp;Allow String#to_i and / or Kernel::Integer to parse e-notation (shyouhei)
- [Feature #13434]&nbsp;better method definition in C API (shyouhei)
- [Feature #13696]&nbsp;Add exchange and noreplace options to File.rename (shyouhei)
- [Feature #13683]&nbsp;Add strict Enumerable#single (shyouhei)
- [Misc #13704]&nbsp;Exclude Changelog files from documentation. (shyouhei)
- [Feature #13713]&nbsp;socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
- [Feature #13715]&nbsp;[PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
- [Feature #13719]&nbsp;[PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
- [Feature #13732]&nbsp;Precise Time.now on Windows (shyouhei)
- [Feature #13733]&nbsp;Dump the delegator instead of the delegated object (shyouhei)
- [Feature #13625]&nbsp;BigDecimal short form / shorthand (shyouhei)
- [Feature #13712]&nbsp;String#start_with? with regexp (shyouhei)
- bugs that are not assigned (shyouhei)
    * [Bug #13674]&nbsp;BigDecimal comparison with Float::INFINITY is erroneous in 2.2.x and 2.3.x
    * [Bug #13675]&nbsp;Should Zlib::GzipReader#ungetc accept nil?
    * [Bug #13700]&nbsp;Enumerable#sum may not work for Ranges subclasses due to optimization (**mrkn**)
    * [Bug #13705]&nbsp;[PATCH] `cfp consistency error' occurs when raising exception in bmethod call event
    * [Bug #13716]&nbsp;Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13670]&nbsp;[BUG] Bus Error at 0xefce7b (armv7l) (ruby 2.3.4p301)

## From attendees

* [Feature #13780] String#each_grapheme (naruse)
* [Misc #13704] [PATCH] Exclude Changelog files from documentation. (hsbt)
* [Feature #12733] Bundle bundler to ruby core (hsbt)
* [Feature #13847] Gem activated problem for default gems (hsbt)
  * https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a (ja)
  * Related with https://bugs.ruby-lang.org/issues/10320
* [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (a_matsuda)
- [Misc #13747]&nbsp;MinGW trunk build available on Appveyor (shyouhei)
- [Feature #13751]&nbsp;Suppert SSHFP resource records in rubysl-resolv (shyouhei)
- [Bug #13752]&nbsp;Can't observe sibling refinements (shyouhei)
- [Feature #13683]&nbsp;Add strict Enumerable#single (shyouhei)
- [Feature #13763]&nbsp;Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)
- [Feature #13765]&nbsp;Add Proc#bind (shyouhei)
- [Feature #13767]&nbsp;add something like python's buffer protocol to share memory between different narray like classes (shyouhei)
- [Feature #13777]&nbsp;Array#delete_all (shyouhei)
- [Feature #13618]&nbsp;[PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (shyouhei)
- [Feature #13784]&nbsp;Add Enumerable#filter as an alias of Enumerable#select (shyouhei)
- [Misc #13804]&nbsp;Protected methods cannot be overridden (shyouhei)
- [Feature #13803]&nbsp;Add Socket::Ifaddr.vhid on supported platforms (shyouhei)
- [Feature #13807]&nbsp;A method to filter the receiver against some condition (shyouhei)
- [Feature #13805]&nbsp;Make refinement scoping to be like that of constants (shyouhei)
- [Feature #13789]&nbsp;Dir - methods (shyouhei)
- [Misc #13810]&nbsp;Inconsistency between Date and Time.strftime("%v") (shyouhei)
- [Feature #12282]&nbsp;Hash#dig! for repeated applications of Hash#fetch (shyouhei)
- [Feature #13821]&nbsp;Allow fibers to be resumed across threads (shyouhei)
- [Feature #9450]&nbsp;Allow overriding SSLContext options in Net::HTTP (shyouhei)
- [Feature #13820]&nbsp;Add a nill coalescing operator (shyouhei)
- [Feature #13770]&nbsp;Can't create valid Cyrillic-named class/module (shyouhei)
- [Feature #13839]&nbsp;String Interpolation Statements (shyouhei)
- [Misc #13840]&nbsp;Collection methods - stability (shyouhei, duerst (propose to reject))
- [Feature #13801] Implement case equality test for Set#=== (duerst)
- bugs that are not assigned (shyouhei)
    * [Bug #13549]&nbsp;MinGW / Windows encoding - Two issues
    * [Bug #13754]&nbsp;bigdecimal with lower precision that Float
    * [Bug #13746]&nbsp;windows-pr gemのRuby　2.4 32bit版でのSEGV
    * [Bug #13758]&nbsp;TestRubyOptions#test_segv_setproctitle segfaults on AARCH64
    * [Bug #13716]&nbsp;Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13691]&nbsp;Word- and symbol array literals not valid where regular array is
    * [Bug #13769]&nbsp;IPAddr#ipv4_compat incorrect behavior
    * [Bug #13768]&nbsp;SIGCHLD and Thread dead-lock problem
    * [Bug #13773]&nbsp;Improve String#prepend performance if only one argument is given
    * [Bug #13774]&nbsp;for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment
    * [Bug #13748]&nbsp;[PATCH] Fix mul overflow detection for LLP64 arch.
    * [Bug #13781]&nbsp;Should the safe navigation operator invoke `nil?`
    * [Bug #13795]&nbsp;Hash#select return type does not match Hash#find_all
    * [Bug #13811]&nbsp;Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)
    * [Bug #13818]&nbsp;Licence issue with use of Onigmo rather than Oniguruma library files
    * [Bug #13827]&nbsp;Improve performance of `Base64.urlsafe_encode64`
    * [Bug #13829]&nbsp;NUL char in $0
    * [Bug #13833]&nbsp;String#scanf("%a") incorrectly requires a sign on the (binary) exponent
    * [Bug #13835]&nbsp;Using 'open-uri' with 'tempfile' causes an exception
    * [Bug #13757]&nbsp;TestBacktrace#test_caller_lev segaults on PPC
    * [Bug #13167]&nbsp;Dir.glob is 25x slower since Ruby 2.2
    * [Bug #13844]&nbsp;Toplevel returns should fire ensures

## From non-attendees

* [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (aycabta)


Write your name and your interest (what do you want to ask and to whom?) please.

# Log

# Date: 2017/08/31 (Thu)
Time: 14:00- 18:00 (JST)
Place: Cookpad, Inc. Headquarter
Sign-up: [https://ruby.connpass.com/event/60055/](https://www.google.com/url?q=https://ruby.connpass.com/event/60055/&sa=D&source=editors&ust=1686087248435633&usg=AOvVaw3JQJAmWFQIQ5iiGiAhflf_)
log edit:
Attendees: matz (Yukihiro Matsumoto), nobu (Nobuyoshi Nakada), ko1 (Koichi Sasada, partially), naruse (Yui Naruse), akr (Akira Tanaka), hsbt (Hiroshi SHIBATA), matsuda (Akira Matsuda), shyouhei (Shyouhei Urabe), sorah (Sorah Fukumori), mrkn (Kenta Murata), mame (Yusuke Endo), kosaki (Motohiro KOSAKI), knu (Akinori MUSHA),  duerst (Martin Dürst)
Language: mostly Japanese (sorry for non native Japanese speakers)

# Agenda

- next

- 9/25
- at Speee HQ, Roppongi.

# About 2.5 timeframe

- # [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087248437047&usg=AOvVaw1HOMVQwc0SAHj_rXp4stDU)


- naruse: I’ll release PR1 after stable releases are published

## Hackathon for Ruby 2.5

- [https://chouseisan.com/s?h=cbab178ad925432ca81f9a64b2563b95](https://www.google.com/url?q=https://chouseisan.com/s?h%3Dcbab178ad925432ca81f9a64b2563b95&sa=D&source=editors&ust=1686087248438320&usg=AOvVaw0jiITyyng-2AyhqcQ84eEg)

## Invitation to "mini RubyKaigi" (a\_matsuda)

- 3-6 committers

## Carry-over from previous meeting(s)

- ## \[Feature [#13396](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13396&sa=D&source=editors&ust=1686087248439334&usg=AOvVaw3-fGaFhcM2KlhY_y_L2yuy)\] Net::HTTP has no write timeout (shyouhei)

- ## \[Bug [#13407](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13407&sa=D&source=editors&ust=1686087248439873&usg=AOvVaw2YJTo3FZIqnKNjwQOuq03Q)\] We have recv\_nonblock but not send\_nonblock... can we add it? (shyouhei)

- ## \[Feature [#13395](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13395&sa=D&source=editors&ust=1686087248440342&usg=AOvVaw15bBEqC1LdFOmjjgeNdFCE)\] Add a method to check for not nil (shyouhei)

- ## \[Feature [#13516](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13516&sa=D&source=editors&ust=1686087248440777&usg=AOvVaw3KgdfA_xrkmWCH_Kl-cyf1)\] Improve the text of the circular require warning (shyouhei)

- ## \[Feature [#13532](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13532&sa=D&source=editors&ust=1686087248441257&usg=AOvVaw3IT9ett_AF2-6HhMt89leR)\] Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)

- ## \[Bug [#13535](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13535&sa=D&source=editors&ust=1686087248441745&usg=AOvVaw0vy26BeKZLtlUF8IIYh3vg)\] Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?

- ## \[Feature [#13568](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13568&sa=D&source=editors&ust=1686087248442162&usg=AOvVaw1VboLQX9rbkfJAGwfgVo2f)\] File#path for O\_TMPFILE fds are unmeaning (sorah)


- raising exception

- ## \[Feature [#13563](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13563&sa=D&source=editors&ust=1686087248442744&usg=AOvVaw3YrUc0niTfCDFtcDvRHz2F)\] Implement Hash#choice method. (shyouhei)


- naming issue.

- ## \[Feature [#13581](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13581&sa=D&source=editors&ust=1686087248443476&usg=AOvVaw3nIE35NNPirRuIshiXu_DL)\] Syntax sugar for method reference (shyouhei)


- naming issue.

- ## \[Bug [#13438](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13438&sa=D&source=editors&ust=1686087248444216&usg=AOvVaw1jHe1RISZwwWs-q6Xe-6Me)\] Fix heap overflow due to configure.in not being updated for HEAP\_\* -> HEAP\_PAGE\_\* variable renaming (shyouhei)

- ## \[Misc [#12529](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12529&sa=D&source=editors&ust=1686087248444865&usg=AOvVaw2nHZ2tKp-38XzFDO_a2Y5C)\] LEGAL file covering all the license information within Ruby (shyouhei)

- ## \[Feature [#13600](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13600&sa=D&source=editors&ust=1686087248445352&usg=AOvVaw0A_SwByifBGUtYSrNOeMV8)\] yield\_self should be chainable/composable (shyouhei)

- ## \[Misc [#13597](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13597&sa=D&source=editors&ust=1686087248445756&usg=AOvVaw2jMQLGDP5S0STVkMhmKUMf)\] Does read\_nonblock call remalloc for the buffer if does it just set the size attribute (shyouhei)

- ## \[Feature [#13606](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13606&sa=D&source=editors&ust=1686087248446232&usg=AOvVaw2TRN4qpIthl4bX42-KtntP)\] Enumerator equality and comparison (shyouhei)

- ## \[Feature [#13604](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13604&sa=D&source=editors&ust=1686087248446747&usg=AOvVaw0orG4YS8WpJkN7Q0SL2gXJ)\] Exposing alternative interface of readline (shyouhei)

- \-----
- ## \[Feature [#13608](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13608&sa=D&source=editors&ust=1686087248447468&usg=AOvVaw2jNbM2WcIRAe9NpU71Fryy)\] Add TracePoint#thread (shyouhei)

- ## \[Feature [#13602](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13602&sa=D&source=editors&ust=1686087248448071&usg=AOvVaw1ZOTzKNoPx16A5oh3aefn-)\] Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)

- ## \[Feature [#13613](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13613&sa=D&source=editors&ust=1686087248448609&usg=AOvVaw3JBka6kTJEYPs0oZnsn6Pb)\] Prefer that require/require\_relative/load to tell us permission error if the target file is unreadable (shyouhei)

- ## \[Feature [#13620](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13620&sa=D&source=editors&ust=1686087248449144&usg=AOvVaw1fkEA2It7SAksl0tB_WzCy)\] Simplifying MRI's build system: always install (shyouhei)

- ## \[Feature [#13630](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13630&sa=D&source=editors&ust=1686087248449767&usg=AOvVaw0bNJAu9TH9TuPcI4Dn8Xfj)\] :\[\] method should accept block in nice syntax (shyouhei)

- ## \[Feature [#13639](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13639&sa=D&source=editors&ust=1686087248450388&usg=AOvVaw0tiWUZScGHyqj-9S5q7tjj)\] Add "RTMIN" and "RTMAX" to Signal.list (shyouhei)

- ## \[Feature [#11484](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11484&sa=D&source=editors&ust=1686087248450964&usg=AOvVaw2zndrSGKl9iuSAbTzb0j8V)\] add output offset for readpartial/read\_nonblock/etc (shyouhei)

- ## \[Feature [#9001](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9001&sa=D&source=editors&ust=1686087248451532&usg=AOvVaw1ZzGRPzSEjpGRoZNKpWgIz)\] Please package better standard library (shyouhei)

- ## \[Feature [#13563](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13563&sa=D&source=editors&ust=1686087248452162&usg=AOvVaw2_wyOEoPiEtjBVRs0-kuU2)\] Implement Hash#choice method. (shyouhei)

- ## \[Feature [#12533](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12533&sa=D&source=editors&ust=1686087248452789&usg=AOvVaw23wc-w7X07v2deCmSCs-Re)\] Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shyouhei)

- ## \[Feature [#13653](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13653&sa=D&source=editors&ust=1686087248453393&usg=AOvVaw3zsDISNcIvPs8gk-pd_916)\] Bundled zlib helper (shyouhei)

- ## \[Feature [#13626](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13626&sa=D&source=editors&ust=1686087248454018&usg=AOvVaw3Ce8Gtwfhje739UgE44RN9)\] Add String#byteslice! (shyouhei)

- ## \[Feature [#13551](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13551&sa=D&source=editors&ust=1686087248454548&usg=AOvVaw2fUsOzfvSiLoqDMbLQTfba)\] Add a method to alias class methods (shyouhei)

- ## \[Feature [#13332](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13332&sa=D&source=editors&ust=1686087248455086&usg=AOvVaw2_kF4dBUP-dkmC3huyOfBF)\] Forwardable#def\_instance\_delegator nil (shyouhei)

- ## \[Feature [#13378](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13378&sa=D&source=editors&ust=1686087248455762&usg=AOvVaw1P18CD0ALL6o4sYqXgpml_)\] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)

- ## \[Feature [#13381](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13381&sa=D&source=editors&ust=1686087248456346&usg=AOvVaw3meRHh_YiIEwgrIyfaIS_c)\] \[PATCH\] Expose rb\_fstring and its family to C extensions (shyouhei)

- ## \[Feature [#13677](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13677&sa=D&source=editors&ust=1686087248456833&usg=AOvVaw2a2pTFqaTvTrLzp-V1BLfy)\] Add more details to error "Name or service not known (SocketError)" (shyouhei)

- ## \[Feature [#9323](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9323&sa=D&source=editors&ust=1686087248457365&usg=AOvVaw3exATmD5NSic34DNxKCOCG)\] IO#writev (shyouhei)

- ## \[Feature [#13686](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13686&sa=D&source=editors&ust=1686087248457844&usg=AOvVaw1eu5nSkp5qy9x06PS1rsEj)\] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on\_\* (shyouhei)

- ## \[Feature [#13693](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13693&sa=D&source=editors&ust=1686087248458271&usg=AOvVaw2YqvbaRFRk0ikfCkgwlilt)\] Allow String#to\_i and / or Kernel::Integer to parse e-notation (shyouhei)

- ## \[Feature [#13434](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13434&sa=D&source=editors&ust=1686087248458776&usg=AOvVaw3QYdt7mUmFcB0IN6-vxr48)\] better method definition in C API (shyouhei)

- ## \[Feature [#13696](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13696&sa=D&source=editors&ust=1686087248459367&usg=AOvVaw3um9tviugUPTjiNSm4IcuB)\] Add exchange and noreplace options to File.rename (shyouhei)

- ## \[Feature [#13683](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13683&sa=D&source=editors&ust=1686087248459901&usg=AOvVaw0T1_1NuFRGDMwKrUlh28Or)\] Add strict Enumerable#single (shyouhei)

- ## \[Misc [#13704](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13704&sa=D&source=editors&ust=1686087248460392&usg=AOvVaw1_bw6VlP6eSwoiz6gRkMdK)\] Exclude Changelog files from documentation. (shyouhei)

- ## \[Feature [#13713](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13713&sa=D&source=editors&ust=1686087248460872&usg=AOvVaw1Bg8vHmN_Ps0m-YjQ2b87L)\] socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)

- ## \[Feature [#13715](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13715&sa=D&source=editors&ust=1686087248461383&usg=AOvVaw2zD9Hkk_ExwqZ39JkVdwh8)\] \[PATCH\] avoid garbage from Symbol#to\_s in interpolation (shyouhei)

- ## \[Feature [#13719](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13719&sa=D&source=editors&ust=1686087248461977&usg=AOvVaw3T94NXC_LGbgSazYFYYe5q)\] \[PATCH\] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)

- ## \[Feature [#13732](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13732&sa=D&source=editors&ust=1686087248462605&usg=AOvVaw19P4qJ37ksdyVBk2wc2uCL)\] Precise Time.now on Windows (shyouhei)

- ## \[Feature [#13733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13733&sa=D&source=editors&ust=1686087248463256&usg=AOvVaw2I94z2H9iIK3UwbEFe4Ulg)\] Dump the delegator instead of the delegated object (shyouhei)

- ## \[Feature [#13625](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13625&sa=D&source=editors&ust=1686087248463854&usg=AOvVaw01NpLQVd_SHjJdVQ8qIlI3)\] BigDecimal short form / shorthand (shyouhei)

- ## \[Feature [#13712](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13712&sa=D&source=editors&ust=1686087248464453&usg=AOvVaw1X9g__KMZyyjC17wu-eRvC)\] String#start\_with? with regexp (shyouhei)

- ## bugs that are not assigned (shyouhei)


- ## \[Bug [#13674](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13674&sa=D&source=editors&ust=1686087248465292&usg=AOvVaw1e3UizuNMl9z-Ecsz4vepS)\] BigDecimal comparison with Float::INFINITY is erroneous in 2.2.x and 2.3.x

- ## \[Bug [#13675](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13675&sa=D&source=editors&ust=1686087248465905&usg=AOvVaw3l31bLW3V0d7DPrHsmq5Bu)\] Should Zlib::GzipReader#ungetc accept nil?

- ## \[Bug [#13700](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13700&sa=D&source=editors&ust=1686087248466526&usg=AOvVaw0XJWj6L0k0SeC0ogHdB6CX)\] Enumerable#sum may not work for Ranges subclasses due to optimization (mrkn)

- ## \[Bug [#13705](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13705&sa=D&source=editors&ust=1686087248467126&usg=AOvVaw3qm_Qux1FBAzlnaUrlEYmB)\] \[PATCH\] \`cfp consistency error' occurs when raising exception in bmethod call event

- ## \[Bug [#13716](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13716&sa=D&source=editors&ust=1686087248467657&usg=AOvVaw31NFqFUW6liDUolVOdIe09)\] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures

- ## \[Bug [#13670](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13670&sa=D&source=editors&ust=1686087248468237&usg=AOvVaw0SNkXcwOTwt1x6TvmypAIf)\] \[BUG\] Bus Error at 0xefce7b (armv7l) (ruby 2.3.4p301)


## From attendees

- ## \[Feature [#13780](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13780&sa=D&source=editors&ust=1686087248468997&usg=AOvVaw2deS9Zb2oSnBS_xAz7Me0s)\] String#each\_grapheme (naruse)


- ## matz: the unit is grapheme cluster. so the name should be each\_grapheme\_cluster.


- ## \[Misc [#13704](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13704&sa=D&source=editors&ust=1686087248469840&usg=AOvVaw2GG95VopuQ9MYRGBvNeKzB)\] \[PATCH\] Exclude Changelog files from documentation. (hsbt)

- ## \[Feature [#12733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12733&sa=D&source=editors&ust=1686087248470451&usg=AOvVaw2rOgti2OpgNg5JU10MPHCx)\] Bundle bundler to ruby core (hsbt)

- ## \[Feature [#13847](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13847&sa=D&source=editors&ust=1686087248470938&usg=AOvVaw0jwreXuPZnL_F4ZAU0a9_q)\] Gem activated problem for default gems (hsbt)


- ## [https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a](https://www.google.com/url?q=https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a&sa=D&source=editors&ust=1686087248471518&usg=AOvVaw0pw8poXncxT_FiymVKbY8h) (ja)

- ## Related with [https://bugs.ruby-lang.org/issues/10320](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10320&sa=D&source=editors&ust=1686087248472114&usg=AOvVaw0Gce_ebZC_JoNnL94M5z_4)


- ## \[Misc [#13747](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13747&sa=D&source=editors&ust=1686087248472663&usg=AOvVaw0Qq--l11He0lq5Z8e-A61S)\] MinGW trunk build available on Appveyor (shyouhei)

- ## \[Feature [#13751](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13751&sa=D&source=editors&ust=1686087248473254&usg=AOvVaw1MGp-hjIkoMp-463ALS69o)\] Suppert SSHFP resource records in rubysl-resolv (shyouhei)

- ## \[Bug [#13752](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13752&sa=D&source=editors&ust=1686087248473899&usg=AOvVaw0lSZECyxz3PPPdDiimvA_1)\] Can't observe sibling refinements (shyouhei)

- ## \[Feature [#13683](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13683&sa=D&source=editors&ust=1686087248474471&usg=AOvVaw2eaIn2rbL3FARturD5-FV4)\] Add strict Enumerable#single (shyouhei)

- ## \[Feature [#13763](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13763&sa=D&source=editors&ust=1686087248475051&usg=AOvVaw0jyuMUQ08wNN9TVbkZ_E3H)\] Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)

- ## \[Feature [#13765](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13765&sa=D&source=editors&ust=1686087248475708&usg=AOvVaw1qaicvgPxCeP7NyVRGv-tU)\] Add Proc#bind (shyouhei)

- ## \[Feature [#13767](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13767&sa=D&source=editors&ust=1686087248476306&usg=AOvVaw1d2Ytv2BczJOTf_9nhtO8a)\] add something like python's buffer protocol to share memory between different narray like classes (shyouhei)

- ## \[Feature [#13777](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13777&sa=D&source=editors&ust=1686087248476948&usg=AOvVaw2dMUpqlvCbuOo4rtiGumM5)\] Array#delete\_all (shyouhei)

- ## \[Feature [#13618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13618&sa=D&source=editors&ust=1686087248477644&usg=AOvVaw0nVQybSwJjE6TU0I696Uml)\] \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (shyouhei)

- ## \[Feature [#13784](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13784&sa=D&source=editors&ust=1686087248478317&usg=AOvVaw1qGIizSgeVwz5KzKN-5-gh)\] Add Enumerable#filter as an alias of Enumerable#select (shyouhei)

- ## \[Misc [#13804](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13804&sa=D&source=editors&ust=1686087248478958&usg=AOvVaw3_MMeFelbCNGje1glRKPeH)\] Protected methods cannot be overridden (shyouhei)

- ## \[Feature [#13803](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13803&sa=D&source=editors&ust=1686087248479602&usg=AOvVaw0zWA_FGwHoQCoFZe3unsk4)\] Add Socket::Ifaddr.vhid on supported platforms (shyouhei)

- ## \[Feature [#13807](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13807&sa=D&source=editors&ust=1686087248480274&usg=AOvVaw1-UIDFjcuO0EVFZKJr3F3N)\] A method to filter the receiver against some condition (shyouhei)

- ## \[Feature [#13805](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13805&sa=D&source=editors&ust=1686087248480892&usg=AOvVaw1lWumto5jqVgfYuQ7NwizI)\] Make refinement scoping to be like that of constants (shyouhei)

- ## \[Feature [#13789](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13789&sa=D&source=editors&ust=1686087248481463&usg=AOvVaw1wI8HXjLqFj8PYVxChA4hm)\] Dir - methods (shyouhei)

- ## \[Misc [#13810](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13810&sa=D&source=editors&ust=1686087248482017&usg=AOvVaw0rLW2CGEyeDp8c47C6JHWn)\] Inconsistency between Date and Time.strftime("%v") (shyouhei)

- ## \[Feature [#12282](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12282&sa=D&source=editors&ust=1686087248482584&usg=AOvVaw0P_1qTyAboqZ1Jezu4ajrQ)\] Hash#dig! for repeated applications of Hash#fetch (shyouhei)

- ## \[Feature [#13821](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13821&sa=D&source=editors&ust=1686087248483204&usg=AOvVaw2A4BS3bKp92Qb4g7tgeluR)\] Allow fibers to be resumed across threads (shyouhei)

- ## \[Feature [#9450](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9450&sa=D&source=editors&ust=1686087248483844&usg=AOvVaw3p4UknTP8HSVS9Pur4JGxE)\] Allow overriding SSLContext options in Net::HTTP (shyouhei)

- ## \[Feature [#13820](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13820&sa=D&source=editors&ust=1686087248484447&usg=AOvVaw2gQU0HypT_cYCn_jjd7Mqa)\] Add a nill coalescing operator (shyouhei)

- ## \[Feature [#13770](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13770&sa=D&source=editors&ust=1686087248485050&usg=AOvVaw0HlZHJKJhk0mHt1wWGtAU-)\] Can't create valid Cyrillic-named class/module (shyouhei, duerst)

- ## \[Feature [#13839](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13839&sa=D&source=editors&ust=1686087248485702&usg=AOvVaw2ZZRaxGPuW1bXridk8duat)\] String Interpolation Statements (shyouhei)

- ## \[Misc [#13840](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13840&sa=D&source=editors&ust=1686087248486253&usg=AOvVaw2F4vjCppZO6_6qo6CflSsQ)\] Collection methods - stability (shyouhei, duerst (propose to reject))

- \[Feature [#13801](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13801&sa=D&source=editors&ust=1686087248486834&usg=AOvVaw0Xe_uLGLwT5JJPRduy2Do_)\] Implement case equality test for Set#=== (duerst)
- ## bugs that are not assigned (shyouhei)


- ## \[Bug [#13549](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13549&sa=D&source=editors&ust=1686087248487663&usg=AOvVaw2xAe1ZAbjxcwZ8ZJFwQVTV)\] MinGW / Windows encoding - Two issues

- ## \[Bug [#13754](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13754&sa=D&source=editors&ust=1686087248488261&usg=AOvVaw05cVy5vMt5QJT27I3sSvJi)\] bigdecimal with lower precision that Float

- ## \[Bug [#13746](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13746&sa=D&source=editors&ust=1686087248488851&usg=AOvVaw2nGMCubqOowsFgpS0fFark)\] windows-pr gemのRuby 2.4 32bit版でのSEGV

- ## \[Bug [#13758](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13758&sa=D&source=editors&ust=1686087248489320&usg=AOvVaw1mbLq6mOojMydjKuuhvr7y)\] TestRubyOptions#test\_segv\_setproctitle segfaults on AARCH64

- ## \[Bug [#13716](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13716&sa=D&source=editors&ust=1686087248489747&usg=AOvVaw0CODpdXTSimLfM4I0Qam_b)\] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures

- ## \[Bug [#13691](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13691&sa=D&source=editors&ust=1686087248490254&usg=AOvVaw1lzFaHXSU2e2fO-vx31LWH)\] Word- and symbol array literals not valid where regular array is

- ## \[Bug [#13769](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13769&sa=D&source=editors&ust=1686087248490720&usg=AOvVaw1H8XlbE-FKW965siy4GSeF)\] IPAddr#ipv4\_compat incorrect behavior

- ## \[Bug [#13768](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13768&sa=D&source=editors&ust=1686087248491171&usg=AOvVaw1whAN143uBgX2l5fKgGH5F)\] SIGCHLD and Thread dead-lock problem

- ## \[Bug [#13773](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13773&sa=D&source=editors&ust=1686087248491766&usg=AOvVaw0N9LDgsEEbM_DdAgKDUn1i)\] Improve String#prepend performance if only one argument is given

- ## \[Bug [#13774](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13774&sa=D&source=editors&ust=1686087248492348&usg=AOvVaw2v1WqoBrGzj56HIBx8ryby)\] for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment

- ## \[Bug [#13748](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13748&sa=D&source=editors&ust=1686087248492943&usg=AOvVaw1m_EqVkrVESIxNBWLg9rM7)\] \[PATCH\] Fix mul overflow detection for LLP64 arch.

- ## \[Bug [#13781](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13781&sa=D&source=editors&ust=1686087248493514&usg=AOvVaw0rN3pwh4XrH_yPsQK6vLSb)\] Should the safe navigation operator invoke nil?

- ## \[Bug [#13795](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13795&sa=D&source=editors&ust=1686087248494150&usg=AOvVaw1WRiCLjKKmTS1XkvXAaUoP)\] Hash#select return type does not match Hash#find\_all

- ## \[Bug [#13811](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13811&sa=D&source=editors&ust=1686087248494717&usg=AOvVaw1vX83eOfh89adYUhVBV12F)\] Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)


We couldn’t investigate it because there is not enough reproduction instruction..

- ## \[Bug [#13818](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13818&sa=D&source=editors&ust=1686087248495448&usg=AOvVaw3NtVG1B_sh4z7Qf4Mm13tv)\] Licence issue with use of Onigmo rather than Oniguruma library files

- ## \[Bug [#13827](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13827&sa=D&source=editors&ust=1686087248496109&usg=AOvVaw2lI4oij8nHP6enF5y9YrMT)\] Improve performance of Base64.urlsafe\_encode64

- ## \[Bug [#13829](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13829&sa=D&source=editors&ust=1686087248496810&usg=AOvVaw1ivZ0aG5GPe53RBUXP20BA)\] NUL char in $0

- ## \[Bug [#13833](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13833&sa=D&source=editors&ust=1686087248497418&usg=AOvVaw1sD9eIzOvqS6taMRcUKEQF)\] String#scanf("%a") incorrectly requires a sign on the (binary) exponent

- ## \[Bug [#13835](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13835&sa=D&source=editors&ust=1686087248498063&usg=AOvVaw1ZTqzvYF2ymsNiMLiG8JFL)\] Using 'open-uri' with 'tempfile' causes an exception

- ## \[Bug [#13757](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13757&sa=D&source=editors&ust=1686087248498655&usg=AOvVaw2ubAeH0s-MyTCSDqCZYRlS)\] TestBacktrace#test\_caller\_lev segaults on PPC

- ## \[Bug [#13167](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13167&sa=D&source=editors&ust=1686087248499272&usg=AOvVaw09EjI9NbJ2DtSjdg0jQBRU)\] Dir.glob is 25x slower since Ruby 2.2

- ## \[Bug [#13844](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13844&sa=D&source=editors&ust=1686087248499866&usg=AOvVaw3lKIDh9x6Rtb8PvidTGtEL)\] Toplevel returns should fire ensures


## From non-attendees

- ## \[Feature [#13686](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13686&sa=D&source=editors&ust=1686087248500655&usg=AOvVaw0y0uxR7qjEbjWE4viyrmKt)\] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on\_\* (aycabta)


## Write your name and your interest (what do you want to ask and to whom?) please.
