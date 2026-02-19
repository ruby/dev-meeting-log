---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-09-25

Date: 2017/09/25 (Mon)
Time: 14:00- 18:00 (JST)
Place: Speee, Inc.
Sign-up: https://ruby.connpass.com/event/67822/
Attendees: matz (Yukihiro Matsumoto), nobu (Nobuyoshi Nakada), ko1 (Koichi Sasada, partially), naruse (Yui Naruse), akr (Akira Tanaka), shyouhei (Shyouhei Urabe), mrkn (Kenta Murata), mame (Yusuke Endoh), knu (Akinori MUSHA), duerst (Martin Dürst), yuki (Yuki Nishijima) (add your name here if you would like to appear)
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

- [Feature #13396] Net::HTTP has no write timeout (shyouhei) Sorry, what was the conclusion of it?
- [Bug #13438] Fix heap overflow due to configure.in not being updated for HEAP_* -> HEAP_PAGE_* variable renaming (shyouhei) Sorry, what was the conclusion of it?
- [Feature #13608] Add TracePoint#thread (shyouhei)
- [Feature #13602] Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)
- [Feature #13613] Prefer that require/require_relative/load to tell us permission error if the target file is unreadable (shyouhei)
- [Feature #13620] Simplifying MRI's build system: always install (shyouhei)
- [Feature #13630] :[] method should accept block in nice syntax (shyouhei) OK to close?
- [Feature #11484] add output offset for readpartial/read_nonblock/etc (shyouhei)
- [Feature #9001] Please package better standard library (shyouhei)
- [Feature #12533] Refinements: allow modules inclusion, in which the module can call internal methods which it defines.  (shyouhei)
- [Feature #13653] Bundled zlib helper (shyouhei)
- [Feature #13626] Add String#byteslice! (shyouhei)
- [Feature #13551] Add a method to alias class methods (shyouhei)
- [Feature #13332] Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13378] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- [Feature #13381] [PATCH] Expose rb_fstring and its family to C extensions (shyouhei)
- [Feature #13677] Add more details to error "Name or service not known (SocketError)" (shyouhei)
- [Feature #9323] IO#writev (shyouhei)
- [Feature #13693] Allow String#to_i and / or Kernel::Integer to parse e-notation (shyouhei)
- [Feature #13434] better method definition in C API (shyouhei)
- [Feature #13696] Add exchange and noreplace options to File.rename (shyouhei)
- [Feature #13683] Add strict Enumerable#single (shyouhei)
- [Feature #13713] socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
- [Feature #13715] [PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
- [Feature #13719] [PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
- [Feature #13732] Precise Time.now on Windows (shyouhei)
- [Feature #13733] Dump the delegator instead of the delegated object (shyouhei)
- [Feature #13625] BigDecimal short form / shorthand (shyouhei)
- [Feature #13712] String#start_with? with regexp (shyouhei)
* [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (a_matsuda)
- [Feature #13751] Suppert SSHFP resource records in rubysl-resolv (shyouhei)
- [Bug #13752] Can't observe sibling refinements (shyouhei)
- [Feature #13763] Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)
- [Feature #13765] Add Proc#bind (shyouhei)
- [Feature #13767] add something like python's buffer protocol to share memory between different narray like classes (shyouhei)
- [Feature #13777] Array#delete_all (shyouhei)
- [Feature #13618] [PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid (shyouhei)
- [Feature #13784] Add Enumerable#filter as an alias of Enumerable#select (shyouhei)
- [Misc #13804] Protected methods cannot be overridden (shyouhei)
- [Feature #13807] A method to filter the receiver against some condition (shyouhei)
- [Feature #13805] Make refinement scoping to be like that of constants (shyouhei)
- [Misc #13810] Inconsistency between Date and Time.strftime("%v") (shyouhei)
- [Feature #12282] Hash#dig! for repeated applications of Hash#fetch (shyouhei)
- [Feature #13821] Allow fibers to be resumed across threads (shyouhei)
- [Feature #9450] Allow overriding SSLContext options in Net::HTTP (shyouhei)
- [Feature #13820] Add a nill coalescing operator (shyouhei)
- [Feature #13770] Can't create valid Cyrillic-named class/module (shyouhei)
- [Misc #13840] Collection methods - stability (shyouhei, duerst (propose to reject))
- bugs that are not assigned (shyouhei)
    * [Bug #13675] Should Zlib::GzipReader#ungetc accept nil?
    * [Bug #13700] Enumerable#sum may not work for Ranges subclasses due to optimization (**mrkn**)
    * [Bug #13716] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13549] MinGW / Windows encoding - Two issues
    * [Bug #13754] bigdecimal with lower precision that Float
    * [Bug #13746] windows-pr gemのRuby　2.4 32bit版でのSEGV
    * [Bug #13758] TestRubyOptions#test_segv_setproctitle segfaults on AARCH64
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

## From attendees

- [Bug #13931] correct install_name of libruby on macOS (libruby.2.5.0.dylib -> libruby.2.5.dylib) (naruse)
- [Bug #13917] Comparable#clamp is slower than using Array#min,max. (naruse)
- [Feature #13919] Add a new method to create Time instances from unix time and nsec

- [Feature #13581] Syntax sugar for method reference (shyouhei)
- [Feature #13867] Copy offloading in IO.copy_stream (shyouhei)
- [Feature #13869] Filter non directories from Dir.glob (shyouhei)
- [Feature #8948] Frozen regex (shyouhei)
- [Bug #13857] frozen string literal: can freeze same string into two unique frozen strings (shyouhei)
- [Feature #13881] Use getcontext/setcontext on OS X (shyouhei)
- [Feature #13883] Change from gperf 3.0.4 to gperf 3.1 (shyouhei)
- [Feature #13890] Allow a regexp as an argument to 'count', to count more interesting things than single characters (shyouhei)
- [Feature #13873] Optimize Dir.glob with FNM_EXTGLOB (shyouhei)
- [Feature #13245] [PATCH] reject inter-thread TLS modification (shyouhei)
- [Feature #13893] Add Fiber#[] and Fiber#[]= and restore Thread#[] and Thread#[]= to their original behavior (shyouhei)
- [Feature #13901] Add branch coverage  (shyouhei)
- [Feature #12753] Useful operator to check bit-flag is true or false (shyouhei)
- [Feature #8499] Importing Hash#slice, Hash#slice!, Hash#except, and Hash#except! from ActiveSupport (shyouhei)
- [Feature #10344] [PATCH] Implement Fiber#raise (shyouhei)
- [Feature #13922] Consider showing warning messages about same-named aliases - either directly or perhaps via the "did you mean gem" (shyouhei)
- [Feature #13924] Add headings/hints to RubyVM::InstructionSequence#disasm (shyouhei)
- [Feature #13923] Idiom to release resources safely, with less indentations (shyouhei)
- [Feature #10849] Adding an alphanumeric function to SecureRandom (shyouhei)
- [Feature #13919] Add a new method to create Time instances from unix time and nsec (shyouhei)
- [Feature #13884] Reduce number of memory allocations for "and", "or" and "diff" operations on small arrays (shyouhei)
- [Feature #13936] Make regular expressions debugable (duerst)
- [Feature #13934] Being able to set a default encoding other than Unicode on a "per-project" basis (duerst)
- bugs that are not assigned (shyouhei)
    * [Bug #13794] Infinite loop of sched_yield
    * [Bug #12036] Enumerator's automatic rewind behavior
    * [Bug #13872] Duplicate assignment no longer silences "assigned but unused variable" warning
    * [Bug #13848] BigDecimal.new('200.') raises an exception
    * [Bug #13880] `BigDecimal(string)` should raise on invalid values in `string`
    * [Bug #13851] getting "can't modify string; temporarily locked" on non-frozen instances
    * [Bug #13876] Tempfile's finalizer can be interrupted by a Timeout exception which can cause the process to hang
    * [Bug #13885] Random.urandom と securerandom について
    * [Bug #10104] Fileutils cp_r fails on sockets or fifes even if File.mknod and File.mkfifo are defined
    * [Bug #13917] Comparable#clamp is slower than using Array#min,max.
    * [Bug #13887] test/ruby/test_io.rb may get stuck with FIBER_USE_NATIVE=0 on Linux
    * [Bug #13926] Non UTF response headers raise an Argument error since 2.4.2p198
    * [Bug #13925] string.split(pattern, 1) should return [self.dup], but it returns [self]
    * [Bug #13931] correct install_name of libruby on macOS (libruby.2.5.0.dylib -> libruby.2.5.dylib)


## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (aycabta)

# Log

Date: 2017/09/25 (Mon)
Time: 14:00- 18:00 (JST)
Place: Speee, Inc.
Sign-up: [https://ruby.connpass.com/event/67822/](https://ruby.connpass.com/event/67822/)
log edit: [https://docs.google.com/document/d/1lkipHlUzcSSse01EFGvlABuCmY7WwXun9\_TkMayQ6\_s/edit](https://docs.google.com/document/d/1lkipHlUzcSSse01EFGvlABuCmY7WwXun9_TkMayQ6_s/edit)
log: [https://docs.google.com/document/d/1lkipHlUzcSSse01EFGvlABuCmY7WwXun9\_TkMayQ6\_s/pub](https://docs.google.com/document/d/1lkipHlUzcSSse01EFGvlABuCmY7WwXun9_TkMayQ6_s/pub)
Attendees: matz (Yukihiro Matsumoto), nobu (Nobuyoshi Nakada), ko1 (Koichi Sasada, partially), naruse (Yui Naruse), akr (Akira Tanaka), shyouhei (Shyouhei Urabe), mrkn (Kenta Murata), mame (Yusuke Endoh), knu (Akinori MUSHA), duerst (Martin Dürst), yuki (Yuki Nishijima) (add your name here if you would like to appear)
Language: mostly Japanese (sorry for non native Japanese speakers)

# Agenda

## next

- 10/12, 10/13, 10/17, 10/19, or …?

- Fixed: 10/19

- at?


- Speee, Inc. at Roppongi.


## About 2.5 timeframe

## [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25)


- shyouhei: no blocker now.
- naruse: I’ll release sooner.

## Carry-over from previous meeting(s)

## \[Feature [#13396](https://bugs.ruby-lang.org/issues/13396)\] Net::HTTP has no write timeout (shyouhei) Sorry, what was the conclusion of it?


- naruse: I’ll implement this.

## \[Bug [#13438](https://bugs.ruby-lang.org/issues/13438)\] Fix heap overflow due to configure.in not being updated for HEAP\_\* -> HEAP\_PAGE\_\* variable renaming (shyouhei) Sorry, what was the conclusion of it?


- mrkn: I remember we should not support old OpenBSD.
- knu: And the reporter is the maintainer of the ruby package(s) in OpenBSD ports.
- shyouhei: then should we accept this?
- nobu: yes.

## \[Feature [#13608](https://bugs.ruby-lang.org/issues/13608)\] Add TracePoint#thread (shyouhei)


- ko1: I think we don’t need this.
- naruse: I wanted this when I created a tracer.
- ko1: the tracer shall run on the thread. Thread.current should suffice

## \[Feature [#13602](https://bugs.ruby-lang.org/issues/13602)\] Optimize instance variable access if $VERBOSE is not true when compiling (shyouhei)


- ko1: is it worth for a 5-6% speedup?
- mame: this should affect optcarrot...

## \[Feature [#13613](https://bugs.ruby-lang.org/issues/13613)\] Prefer that require/require\_relative/load to tell us permission error if the target file is unreadable (shyouhei)


- akr: we should define errors that are “fatal” and those aren’t.  For instance, ENOENT is not fatal but EPERM is.
- shyouhei: it sounds reasonable for someone want to know when there \_are\_ some nasty files.
- mame: should we raise exceptions for errors other than ENOENT? or to warn only?

## \[Feature [#13620](https://bugs.ruby-lang.org/issues/13620)\] Simplifying MRI's build system: always install (shyouhei)


- the thread is going on.

## \[Feature [#13630](https://bugs.ruby-lang.org/issues/13630)\] :\[\] method should accept block in nice syntax (shyouhei) OK to close?


- matz: I think it should be accepted.
- nobu: mruby doesn’t work this way, BTW.
- shyouhei: OK, lets close.

- \[Feature [#11484](https://bugs.ruby-lang.org/issues/11484)\] add output offset for readpartial/read\_nonblock/etc (shyouhei)

- akr: sounds OK to me.
- shyouhei: seems nobody is against the feature itself but the API?
- akr: keyword argument sounds reasobable.
- matz: off\_out doesn’t charm me.
- nobu: what about buffer\_offset?
- knu: seems IO.write has optional 3rd argument, offset, which is used to seek the file.

## \[Feature [#9001](https://bugs.ruby-lang.org/issues/9001)\] Please package better standard library (shyouhe)


- duerst: This particular ticket seems no feature but for long term, what should we do for standard libraries?
- mrkn: I think current goal is to bundle things that are only needed for installing rubygems
- akira: close this and let people individually name replacemets.

## \[Feature [#12533](https://bugs.ruby-lang.org/issues/12533)\] Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shyouhei)


- matz: this sounds like a bug instead of local rebinding?
- akr: this is how ruby works and can be explained by our method dispatching algorithm -- not intuitive though.
- nobu: include inside of refinements works differently than outer ones.

module Extensions

 def vegetables ; raise ; end

 def potatoe ; raise ; end

end

module Refinary

 refine String do

 include Extensions

 end

end

using Refinary

module Extensions

 def vegetables ; potatoe ; end

 def potatoe ; "potatoe" ; end

end

puts "tomatoe".vegetables

## \[Feature [#13653](https://bugs.ruby-lang.org/issues/13653)\] Bundled zlib helper (shyouhei)


- shyouhei: postpone because hsbt is absent.

## \[Feature [#13626](https://bugs.ruby-lang.org/issues/13626)\] Add String#byteslice! (shyouhei)


- mame: sounds reasonable because we have both slice and slice!
- matz: agreed.

## \[Feature [#13551](https://bugs.ruby-lang.org/issues/13551)\] Add a method to alias class methods (shyouhei)


- shyouhei: ah, I want this feature.
- matz: I prefer opening singleton class.

## \[Feature [#13332](https://bugs.ruby-lang.org/issues/13332)\] Forwardable#def\_instance\_delegator nil (shyouhei)


- nobu: I don’t think this is much meaningful.
- matz: I fell it’s over-engineering.
- knu: ActiveSupport::Delegate also doesn’t support this comples feature (but only allow\_nil)
- matz: I’d like to hear use cases.

## \[Feature [#13378](https://bugs.ruby-lang.org/issues/13378)\] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)


- nobu: I’m handlig this. WIP.

## \[Feature [#13381](https://bugs.ruby-lang.org/issues/13381)\] \[PATCH\] Expose rb\_fstring and its family to C extensions (shyouhei)


- matz: postpone because ko1 is absent.

## \[Feature [#13677](https://bugs.ruby-lang.org/issues/13677)\] Add more details to error "Name or service not known (SocketError)" (shyouhei)


- akr: we currenly only show the return value of gai\_strerror(). Seems OK to extend though.
- akr: patch is welcomed.

## \[Feature [#9323](https://bugs.ruby-lang.org/issues/9323)\] IO#writev (shyouhei)


- akr: do we call syscalls many times, or should we buffer?
- mame: I want to know how this speeds things up.
- knu: maybe atomic write is the point.

## \[Feature [#13693](https://bugs.ruby-lang.org/issues/13693)\] Allow String#to\_i and / or Kernel::Integer to parse e-notation (shyouhei)


- mame: haha
- duerst: 1e+3 doesn’t sound like a integer.
- nobu: should we accept minus?
- akr: yes because 10e-1 shall be OK according to this request.
- mrkn: backwards compatibility is to huge in #to\_i
- shyouhei: so should we recommend using #to\_r, then round?
- mrkn: yes.
- mrkn: it seems the behaviour of #to\_i comes with atoi().

## \[Feature [#13434](https://bugs.ruby-lang.org/issues/13434)\] better method definition in C API (shyouhei)


- shyouhei: postpone because ko1 is absent now.

## \[Feature [#13696](https://bugs.ruby-lang.org/issues/13696)\] Add exchange and noreplace options to File.rename (shyouhei)


- akr: This should definitely not be an extension to #rename.
- nobu: It’s hard to introduce in-core.

## \[Feature [#13683](https://bugs.ruby-lang.org/issues/13683)\] Add strict Enumerable#single (shyouhei)


- shyouhei: わかる
- mame: …but is there such use case?
- shyouhei: Seems the request was rerouted from AS.
- matz: honestly I don’t like Enumerable#single name.
- akira: maybe we should just reject it, then the AS’s pull request should advance.
- mame: what about the feature itself?
- matz: think we need more use case.

## \[Feature [#13618](https://bugs.ruby-lang.org/issues/13618)\] \[PATCH\] auto fiber schedule for rb\_wait\_for\_single\_fd and rb\_waitpid (shyouhei)


- matz: async { File.read }
- matz: async File.read
- ko1: that is better than nothing.
- ko1: what if a library author did this, its user can never be aware of this async call.
- ko1: If a Fiber body is small, it may have no problem.  However when someone write a framework that wraps existing external library, that should result in a problematic situation.

## \[Feature [#13821](https://bugs.ruby-lang.org/issues/13821)\] Allow fibers to be resumed across threads (shyouhei)


- ko1: it’s technically impossible.

## \[Bug [#13931](https://bugs.ruby-lang.org/issues/13931)\] correct install\_name of libruby on macOS (libruby.2.5.0.dylib -> libruby.2.5.dylib) (naruse)


- nobu: compatibility version shall be, and is, “2.4.0” ATM.  But we had bugs before.  I don’t want to change the AS-IS behaviour.

- naruse: compatibility version shall be “2.4.0” or “2.4”, and linked libray should be libruby.2.4.dylib


- \[Bug [#13917](https://bugs.ruby-lang.org/issues/13917)\] Comparable#clamp is slower than using Array#min,max. (naruse)

- nobu: the prposed patch seems roughly okay
- mame: we don’t optimize Comparable#clamp for literals, because no practical usage can be thought for such thing. NEWS shall be consulted.
- naruse: I’ll respond as such. nobu will merge the pull request.

## \[Feature [#13893](https://bugs.ruby-lang.org/issues/13893)\] Add Fiber#\[\] and Fiber#\[\]= and restore Thread#\[\] and Thread#\[\]= to their original behavior (shyouhei)


- akr: This is clear, but too late.

## \[Feature [#10344](https://bugs.ruby-lang.org/issues/10344)\] \[PATCH\] Implement Fiber#raise (shyouhei)


- ko1: if we allow Fiber#raise, auto fiber shall introduce weired behaviour.
- akr: I don’t think auto fiber should transfar exceptions.

## \[Feature [#13923](https://bugs.ruby-lang.org/issues/13923)\] Idiom to release resources safely, with less indentations (shyouhei)


- shyouhei: This is what C++ resolves using RAII
- nobu: or using in C#
- mrkn: or context in Python.
- matz: or with in Python
- matz: I don’t want to add new keywords.
- nobu: I now think it should be done using a library, not by the language.
- matz: what about introducing ensure\_mod, just like rescue\_mod?
- nobu: should that expose ensure’s scope?
- knu: foo rescue bar ensure baz sounds like baz would be executed immediately at the line.
- akr: I’d like to propose non-nesting block:
    baz {
     open(“foo”) @|f1|
     open(“bar”) @|f2|
     …
    }
    which means:
    baz {
     open(“foo”) {|f1|
       open(“bar”) {|f2|
         …
       }
     }
    }
- knu: rescue after a non-nesting block will be consumed by the enclosing (outer) block, so there’s no way to put a rescue for the non-nesting block, I guess?
- shyouhei: This proposal is too big to discuss at this issue.
- matz: returning to the issue, let’s hear what the OP thinks about the nobu’s library.


## \[Feature [#13919](https://bugs.ruby-lang.org/issues/13919)\] Add a new method to create Time instances from unix time and nsec (shyouhei)


- naruse: I want it.
- nobu: it should be possible.
- shyouhei: should we pass Float? can it be Rational instead?
- akr: you can pass Rational.
- mame: Is Time.of possible?
- knu: .of sounds NG…
- akr: what about optional argument that describes the unit of second argument?
    Time.at(123, 456, :nanosecond)
- matz: \`Time.at(100000, 123, :nsec)\`? \`Time.at(100000, 123,:msec)\`? \`Time.at(100000, nsec: 123)\`?
- ko1: This seems to be the first kind of API
- akr: No, we already have CLOCK\_GETTIME
- ko1: what to do about Time.at(1, msec: 234, nsec: 567)
- naruse: I think no practical usage are there for specifying both msec and nsec.
- matz: OK, accepted.  But specifying nil shall raise exceptions.

## \[Bug [#13887](https://bugs.ruby-lang.org/issues/13887)\] test/ruby/test\_io.rb may get stuck with FIBER\_USE\_NATIVE=0 on Linux


- ko1: this is a bug that should be fixed. I’ll do.
