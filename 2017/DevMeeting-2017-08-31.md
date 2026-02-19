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
- [Feature #13581] Syntax sugar for method reference (shyouhei)
- [Bug #13438] Fix heap overflow due to configure.in not being updated for HEAP_* -> HEAP_PAGE_* variable renaming (shyouhei)
- [Misc #12529] LEGAL file covering all the license information within Ruby (shyouhei)
- [Feature #13600] yield_self should be chainable/composable (shyouhei)
- [Misc #13597] Does read_nonblock call remalloc for the buffer if does it just set the size attribute (shyouhei)
- [Feature #13606] Enumerator equality and comparison (shyouhei)
- [Feature #13604] Exposing alternative interface of readline (shyouhei)
- [Feature #13608] Add TracePoint#thread (shyouhei)
- [Feature #13602] Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)
- [Feature #13613] Prefer that require/require_relative/load to tell us permission error if the target file is unreadable (shyouhei)
- [Feature #13620] Simplifying MRI's build system: always install (shyouhei)
- [Feature #13630] :[] method should accept block in nice syntax (shyouhei)
- [Feature #13639] Add "RTMIN" and "RTMAX" to Signal.list (shyouhei)
- [Feature #11484] add output offset for readpartial/read_nonblock/etc (shyouhei)
- [Feature #9001] Please package better standard library (shyouhei)
- [Feature #13563] Implement Hash#choice method. (shyouhei)
- [Feature #12533] Refinements: allow modules inclusion, in which the module can call internal methods which it defines.  (shyouhei)
- [Feature #13653] Bundled zlib helper (shyouhei)
- [Feature #13626] Add String#byteslice! (shyouhei)
- [Feature #13551] Add a method to alias class methods (shyouhei)
- [Feature #13332] Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13378] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381] [PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Feature #13677] Add more details to error "Name or service not known (SocketError)" (shyouhei)
- [Feature #9323] IO#writev (shyouhei)
- [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (shyouhei)
- [Feature #13693] Allow String#to_i and / or Kernel::Integer to parse e-notation (shyouhei)
- [Feature #13434] better method definition in C API (shyouhei)
- [Feature #13696] Add exchange and noreplace options to File.rename (shyouhei)
- [Feature #13683] Add strict Enumerable#single (shyouhei)
- [Misc #13704] Exclude Changelog files from documentation. (shyouhei)
- [Feature #13713] socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
- [Feature #13715] [PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
- [Feature #13719] [PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
- [Feature #13732] Precise Time.now on Windows (shyouhei)
- [Feature #13733] Dump the delegator instead of the delegated object (shyouhei)
- [Feature #13625] BigDecimal short form / shorthand (shyouhei)
- [Feature #13712] String#start_with? with regexp (shyouhei)
- bugs that are not assigned (shyouhei)
    * [Bug #13674] BigDecimal comparison with Float::INFINITY is erroneous in 2.2.x and 2.3.x
    * [Bug #13675] Should Zlib::GzipReader#ungetc accept nil?
    * [Bug #13700] Enumerable#sum may not work for Ranges subclasses due to optimization (**mrkn**)
    * [Bug #13705] [PATCH] `cfp consistency error' occurs when raising exception in bmethod call event
    * [Bug #13716] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13670] [BUG] Bus Error at 0xefce7b (armv7l) (ruby 2.3.4p301)

## From attendees

* [Feature #13780] String#each_grapheme (naruse)
* [Misc #13704] [PATCH] Exclude Changelog files from documentation. (hsbt)
* [Feature #12733] Bundle bundler to ruby core (hsbt)
* [Feature #13847] Gem activated problem for default gems (hsbt)
  * https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a (ja)
  * Related with https://bugs.ruby-lang.org/issues/10320
* [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (a_matsuda)
- [Misc #13747] MinGW trunk build available on Appveyor (shyouhei)
- [Feature #13751] Suppert SSHFP resource records in rubysl-resolv (shyouhei)
- [Bug #13752] Can't observe sibling refinements (shyouhei)
- [Feature #13683] Add strict Enumerable#single (shyouhei)
- [Feature #13763] Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)
- [Feature #13765] Add Proc#bind (shyouhei)
- [Feature #13767] add something like python's buffer protocol to share memory between different narray like classes (shyouhei)
- [Feature #13777] Array#delete_all (shyouhei)
- [Feature #13618] [PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (shyouhei)
- [Feature #13784] Add Enumerable#filter as an alias of Enumerable#select (shyouhei)
- [Misc #13804] Protected methods cannot be overridden (shyouhei)
- [Feature #13803] Add Socket::Ifaddr.vhid on supported platforms (shyouhei)
- [Feature #13807] A method to filter the receiver against some condition (shyouhei)
- [Feature #13805] Make refinement scoping to be like that of constants (shyouhei)
- [Feature #13789] Dir - methods (shyouhei)
- [Misc #13810] Inconsistency between Date and Time.strftime("%v") (shyouhei)
- [Feature #12282] Hash#dig! for repeated applications of Hash#fetch (shyouhei)
- [Feature #13821] Allow fibers to be resumed across threads (shyouhei)
- [Feature #9450] Allow overriding SSLContext options in Net::HTTP (shyouhei)
- [Feature #13820] Add a nill coalescing operator (shyouhei)
- [Feature #13770] Can't create valid Cyrillic-named class/module (shyouhei)
- [Feature #13839] String Interpolation Statements (shyouhei)
- [Misc #13840] Collection methods - stability (shyouhei, duerst (propose to reject))
- [Feature #13801] Implement case equality test for Set#=== (duerst)
- bugs that are not assigned (shyouhei)
    * [Bug #13549] MinGW / Windows encoding - Two issues
    * [Bug #13754] bigdecimal with lower precision that Float
    * [Bug #13746] windows-pr gemのRuby　2.4 32bit版でのSEGV
    * [Bug #13758] TestRubyOptions#test_segv_setproctitle segfaults on AARCH64
    * [Bug #13716] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13691] Word- and symbol array literals not valid where regular array is
    * [Bug #13769] IPAddr#ipv4_compat incorrect behavior
    * [Bug #13768] SIGCHLD and Thread dead-lock problem
    * [Bug #13773] Improve String#prepend performance if only one argument is given
    * [Bug #13774] for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment
    * [Bug #13748] [PATCH] Fix mul overflow detection for LLP64 arch.
    * [Bug #13781] Should the safe navigation operator invoke `nil?`
    * [Bug #13795] Hash#select return type does not match Hash#find_all
    * [Bug #13811] Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)
    * [Bug #13818] Licence issue with use of Onigmo rather than Oniguruma library files
    * [Bug #13827] Improve performance of `Base64.urlsafe_encode64`
    * [Bug #13829] NUL char in $0
    * [Bug #13833] String#scanf("%a") incorrectly requires a sign on the (binary) exponent
    * [Bug #13835] Using 'open-uri' with 'tempfile' causes an exception
    * [Bug #13757] TestBacktrace#test_caller_lev segaults on PPC
    * [Bug #13167] Dir.glob is 25x slower since Ruby 2.2
    * [Bug #13844] Toplevel returns should fire ensures

## From non-attendees

* [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (aycabta)


Write your name and your interest (what do you want to ask and to whom?) please.

# Log

# Date: 2017/08/31 (Thu)
Time: 14:00- 18:00 (JST)
Place: Cookpad, Inc. Headquarter
Sign-up: [https://ruby.connpass.com/event/60055/](https://ruby.connpass.com/event/60055/)
log edit:
Attendees: matz (Yukihiro Matsumoto), nobu (Nobuyoshi Nakada), ko1 (Koichi Sasada, partially), naruse (Yui Naruse), akr (Akira Tanaka), hsbt (Hiroshi SHIBATA), matsuda (Akira Matsuda), shyouhei (Shyouhei Urabe), sorah (Sorah Fukumori), mrkn (Kenta Murata), mame (Yusuke Endo), kosaki (Motohiro KOSAKI), knu (Akinori MUSHA),  duerst (Martin Dürst)
Language: mostly Japanese (sorry for non native Japanese speakers)

# Agenda

- next

- 9/25
- at Speee HQ, Roppongi.

# About 2.5 timeframe

# [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25)


- naruse: I’ll release PR1 after stable releases are published

## Hackathon for Ruby 2.5

- [https://chouseisan.com/s?h=cbab178ad925432ca81f9a64b2563b95](https://chouseisan.com/s?h=cbab178ad925432ca81f9a64b2563b95)

## Invitation to "mini RubyKaigi" (a_matsuda)

- 3-6 committers

## Carry-over from previous meeting(s)

## [[Feature #13396]](https://bugs.ruby-lang.org/issues/13396) Net::HTTP has no write timeout (shyouhei)

## [[Bug #13407]](https://bugs.ruby-lang.org/issues/13407) We have recv_nonblock but not send_nonblock... can we add it? (shyouhei)

## [[Feature #13395]](https://bugs.ruby-lang.org/issues/13395) Add a method to check for not nil (shyouhei)

## [[Feature #13516]](https://bugs.ruby-lang.org/issues/13516) Improve the text of the circular require warning (shyouhei)

## [[Feature #13532]](https://bugs.ruby-lang.org/issues/13532) Enable :encoding key or open-uri (open()) similar as to how File.read() and File.readlines() already allow for (shyouhei)

## [[Bug #13535]](https://bugs.ruby-lang.org/issues/13535) Installing Ruby2.4.1 on Solaris 10 (shyouhei) response?

## [[Feature #13568]](https://bugs.ruby-lang.org/issues/13568) File#path for O_TMPFILE fds are unmeaning (sorah)


- raising exception

## [[Feature #13563]](https://bugs.ruby-lang.org/issues/13563) Implement Hash#choice method. (shyouhei)


- naming issue.

## [[Feature #13581]](https://bugs.ruby-lang.org/issues/13581) Syntax sugar for method reference (shyouhei)


- naming issue.

## [[Bug #13438]](https://bugs.ruby-lang.org/issues/13438) Fix heap overflow due to configure.in not being updated for HEAP_\* -> HEAP_PAGE_\* variable renaming (shyouhei)

## [[Misc #12529]](https://bugs.ruby-lang.org/issues/12529) LEGAL file covering all the license information within Ruby (shyouhei)

## [[Feature #13600]](https://bugs.ruby-lang.org/issues/13600) yield_self should be chainable/composable (shyouhei)

## [[Misc #13597]](https://bugs.ruby-lang.org/issues/13597) Does read_nonblock call remalloc for the buffer if does it just set the size attribute (shyouhei)

## [[Feature #13606]](https://bugs.ruby-lang.org/issues/13606) Enumerator equality and comparison (shyouhei)

## [[Feature #13604]](https://bugs.ruby-lang.org/issues/13604) Exposing alternative interface of readline (shyouhei)

---
## [[Feature #13608]](https://bugs.ruby-lang.org/issues/13608) Add TracePoint#thread (shyouhei)

## [[Feature #13602]](https://bugs.ruby-lang.org/issues/13602) Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)

## [[Feature #13613]](https://bugs.ruby-lang.org/issues/13613) Prefer that require/require_relative/load to tell us permission error if the target file is unreadable (shyouhei)

## [[Feature #13620]](https://bugs.ruby-lang.org/issues/13620) Simplifying MRI's build system: always install (shyouhei)

## [[Feature #13630]](https://bugs.ruby-lang.org/issues/13630) :[] method should accept block in nice syntax (shyouhei)

## [[Feature #13639]](https://bugs.ruby-lang.org/issues/13639) Add "RTMIN" and "RTMAX" to Signal.list (shyouhei)

## [[Feature #11484]](https://bugs.ruby-lang.org/issues/11484) add output offset for readpartial/read_nonblock/etc (shyouhei)

## [[Feature #9001]](https://bugs.ruby-lang.org/issues/9001) Please package better standard library (shyouhei)

## [[Feature #13563]](https://bugs.ruby-lang.org/issues/13563) Implement Hash#choice method. (shyouhei)

## [[Feature #12533]](https://bugs.ruby-lang.org/issues/12533) Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shyouhei)

## [[Feature #13653]](https://bugs.ruby-lang.org/issues/13653) Bundled zlib helper (shyouhei)

## [[Feature #13626]](https://bugs.ruby-lang.org/issues/13626) Add String#byteslice! (shyouhei)

## [[Feature #13551]](https://bugs.ruby-lang.org/issues/13551) Add a method to alias class methods (shyouhei)

## [[Feature #13332]](https://bugs.ruby-lang.org/issues/13332) Forwardable#def_instance_delegator nil (shyouhei)

## [[Feature #13378]](https://bugs.ruby-lang.org/issues/13378) Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)

## [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) [PATCH] Expose rb_fstring and its family to C extensions (shyouhei)

## [[Feature #13677]](https://bugs.ruby-lang.org/issues/13677) Add more details to error "Name or service not known (SocketError)" (shyouhei)

## [[Feature #9323]](https://bugs.ruby-lang.org/issues/9323) IO#writev (shyouhei)

## [[Feature #13686]](https://bugs.ruby-lang.org/issues/13686) Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_\* (shyouhei)

## [[Feature #13693]](https://bugs.ruby-lang.org/issues/13693) Allow String#to_i and / or Kernel::Integer to parse e-notation (shyouhei)

## [[Feature #13434]](https://bugs.ruby-lang.org/issues/13434) better method definition in C API (shyouhei)

## [[Feature #13696]](https://bugs.ruby-lang.org/issues/13696) Add exchange and noreplace options to File.rename (shyouhei)

## [[Feature #13683]](https://bugs.ruby-lang.org/issues/13683) Add strict Enumerable#single (shyouhei)

## [[Misc #13704]](https://bugs.ruby-lang.org/issues/13704) Exclude Changelog files from documentation. (shyouhei)

## [[Feature #13713]](https://bugs.ruby-lang.org/issues/13713) socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)

## [[Feature #13715]](https://bugs.ruby-lang.org/issues/13715) [PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)

## [[Feature #13719]](https://bugs.ruby-lang.org/issues/13719) [PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)

## [[Feature #13732]](https://bugs.ruby-lang.org/issues/13732) Precise Time.now on Windows (shyouhei)

## [[Feature #13733]](https://bugs.ruby-lang.org/issues/13733) Dump the delegator instead of the delegated object (shyouhei)

## [[Feature #13625]](https://bugs.ruby-lang.org/issues/13625) BigDecimal short form / shorthand (shyouhei)

## [[Feature #13712]](https://bugs.ruby-lang.org/issues/13712) String#start_with? with regexp (shyouhei)

- bugs that are not assigned (shyouhei)


## [[Bug #13674]](https://bugs.ruby-lang.org/issues/13674) BigDecimal comparison with Float::INFINITY is erroneous in 2.2.x and 2.3.x

## [[Bug #13675]](https://bugs.ruby-lang.org/issues/13675) Should Zlib::GzipReader#ungetc accept nil?

## [[Bug #13700]](https://bugs.ruby-lang.org/issues/13700) Enumerable#sum may not work for Ranges subclasses due to optimization (mrkn)

## [[Bug #13705]](https://bugs.ruby-lang.org/issues/13705) [PATCH] `cfp consistency error' occurs when raising exception in bmethod call event

## [[Bug #13716]](https://bugs.ruby-lang.org/issues/13716) Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures

## [[Bug #13670]](https://bugs.ruby-lang.org/issues/13670) [BUG] Bus Error at 0xefce7b (armv7l) (ruby 2.3.4p301)


## From attendees

## [[Feature #13780]](https://bugs.ruby-lang.org/issues/13780) String#each_grapheme (naruse)


- matz: the unit is grapheme cluster. so the name should be each_grapheme_cluster.


## [[Misc #13704]](https://bugs.ruby-lang.org/issues/13704) [PATCH] Exclude Changelog files from documentation. (hsbt)

## [[Feature #12733]](https://bugs.ruby-lang.org/issues/12733) Bundle bundler to ruby core (hsbt)

## [[Feature #13847]](https://bugs.ruby-lang.org/issues/13847) Gem activated problem for default gems (hsbt)


## [https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a](https://gist.github.com/hsbt/968fcbb7acfa9405d82040a3c219f43a) (ja)

- Related with [https://bugs.ruby-lang.org/issues/10320](https://bugs.ruby-lang.org/issues/10320)


## [[Misc #13747]](https://bugs.ruby-lang.org/issues/13747) MinGW trunk build available on Appveyor (shyouhei)

## [[Feature #13751]](https://bugs.ruby-lang.org/issues/13751) Suppert SSHFP resource records in rubysl-resolv (shyouhei)

## [[Bug #13752]](https://bugs.ruby-lang.org/issues/13752) Can't observe sibling refinements (shyouhei)

## [[Feature #13683]](https://bugs.ruby-lang.org/issues/13683) Add strict Enumerable#single (shyouhei)

## [[Feature #13763]](https://bugs.ruby-lang.org/issues/13763) Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)

## [[Feature #13765]](https://bugs.ruby-lang.org/issues/13765) Add Proc#bind (shyouhei)

## [[Feature #13767]](https://bugs.ruby-lang.org/issues/13767) add something like python's buffer protocol to share memory between different narray like classes (shyouhei)

## [[Feature #13777]](https://bugs.ruby-lang.org/issues/13777) Array#delete_all (shyouhei)

## [[Feature #13618]](https://bugs.ruby-lang.org/issues/13618) [PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (shyouhei)

## [[Feature #13784]](https://bugs.ruby-lang.org/issues/13784) Add Enumerable#filter as an alias of Enumerable#select (shyouhei)

## [[Misc #13804]](https://bugs.ruby-lang.org/issues/13804) Protected methods cannot be overridden (shyouhei)

## [[Feature #13803]](https://bugs.ruby-lang.org/issues/13803) Add Socket::Ifaddr.vhid on supported platforms (shyouhei)

## [[Feature #13807]](https://bugs.ruby-lang.org/issues/13807) A method to filter the receiver against some condition (shyouhei)

## [[Feature #13805]](https://bugs.ruby-lang.org/issues/13805) Make refinement scoping to be like that of constants (shyouhei)

## [[Feature #13789]](https://bugs.ruby-lang.org/issues/13789) Dir - methods (shyouhei)

## [[Misc #13810]](https://bugs.ruby-lang.org/issues/13810) Inconsistency between Date and Time.strftime("%v") (shyouhei)

## [[Feature #12282]](https://bugs.ruby-lang.org/issues/12282) Hash#dig! for repeated applications of Hash#fetch (shyouhei)

## [[Feature #13821]](https://bugs.ruby-lang.org/issues/13821) Allow fibers to be resumed across threads (shyouhei)

## [[Feature #9450]](https://bugs.ruby-lang.org/issues/9450) Allow overriding SSLContext options in Net::HTTP (shyouhei)

## [[Feature #13820]](https://bugs.ruby-lang.org/issues/13820) Add a nill coalescing operator (shyouhei)

## [[Feature #13770]](https://bugs.ruby-lang.org/issues/13770) Can't create valid Cyrillic-named class/module (shyouhei, duerst)

## [[Feature #13839]](https://bugs.ruby-lang.org/issues/13839) String Interpolation Statements (shyouhei)

## [[Misc #13840]](https://bugs.ruby-lang.org/issues/13840) Collection methods - stability (shyouhei, duerst (propose to reject))

- [[Feature #13801]](https://bugs.ruby-lang.org/issues/13801) Implement case equality test for Set#=== (duerst)
- bugs that are not assigned (shyouhei)


## [[Bug #13549]](https://bugs.ruby-lang.org/issues/13549) MinGW / Windows encoding - Two issues

## [[Bug #13754]](https://bugs.ruby-lang.org/issues/13754) bigdecimal with lower precision that Float

## [[Bug #13746]](https://bugs.ruby-lang.org/issues/13746) windows-pr gemのRuby 2.4 32bit版でのSEGV

## [[Bug #13758]](https://bugs.ruby-lang.org/issues/13758) TestRubyOptions#test_segv_setproctitle segfaults on AARCH64

## [[Bug #13716]](https://bugs.ruby-lang.org/issues/13716) Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures

## [[Bug #13691]](https://bugs.ruby-lang.org/issues/13691) Word- and symbol array literals not valid where regular array is

## [[Bug #13769]](https://bugs.ruby-lang.org/issues/13769) IPAddr#ipv4_compat incorrect behavior

## [[Bug #13768]](https://bugs.ruby-lang.org/issues/13768) SIGCHLD and Thread dead-lock problem

## [[Bug #13773]](https://bugs.ruby-lang.org/issues/13773) Improve String#prepend performance if only one argument is given

## [[Bug #13774]](https://bugs.ruby-lang.org/issues/13774) for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment

## [[Bug #13748]](https://bugs.ruby-lang.org/issues/13748) [PATCH] Fix mul overflow detection for LLP64 arch.

## [[Bug #13781]](https://bugs.ruby-lang.org/issues/13781) Should the safe navigation operator invoke nil?

## [[Bug #13795]](https://bugs.ruby-lang.org/issues/13795) Hash#select return type does not match Hash#find_all

## [[Bug #13811]](https://bugs.ruby-lang.org/issues/13811) Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)


We couldn’t investigate it because there is not enough reproduction instruction..

## [[Bug #13818]](https://bugs.ruby-lang.org/issues/13818) Licence issue with use of Onigmo rather than Oniguruma library files

## [[Bug #13827]](https://bugs.ruby-lang.org/issues/13827) Improve performance of Base64.urlsafe_encode64

## [[Bug #13829]](https://bugs.ruby-lang.org/issues/13829) NUL char in $0

## [[Bug #13833]](https://bugs.ruby-lang.org/issues/13833) String#scanf("%a") incorrectly requires a sign on the (binary) exponent

## [[Bug #13835]](https://bugs.ruby-lang.org/issues/13835) Using 'open-uri' with 'tempfile' causes an exception

## [[Bug #13757]](https://bugs.ruby-lang.org/issues/13757) TestBacktrace#test_caller_lev segaults on PPC

## [[Bug #13167]](https://bugs.ruby-lang.org/issues/13167) Dir.glob is 25x slower since Ruby 2.2

## [[Bug #13844]](https://bugs.ruby-lang.org/issues/13844) Toplevel returns should fire ensures


## From non-attendees

## [[Feature #13686]](https://bugs.ruby-lang.org/issues/13686) Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_\* (aycabta)


## Write your name and your interest (what do you want to ask and to whom?) please.
