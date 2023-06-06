---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-10-19

Date: 2017/09/25 (Mon)
Time: 14:00- 18:00 (JST)
Place: Speee, Inc.
Sign-up: https://ruby.connpass.com/event/67914/
log edit: https://docs.google.com/document/d/1XTTzUINO2Fegt-eFgGTY8oGF3uTy5Bz4ZXJdKzyFp34/edit#heading=h.ujsctc6nvlk
Attendees: (add your name here if you would like to appear)
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

- bugs that are not assigned (shyouhei)
    * [Bug #13675]&nbsp;Should Zlib::GzipReader#ungetc accept nil?
    * [Bug #13700]&nbsp;Enumerable#sum may not work for Ranges subclasses due to optimization (**mrkn**)
    * [Bug #13716]&nbsp;Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
    * [Bug #13549]&nbsp;MinGW / Windows encoding - Two issues
    * [Bug #13746]&nbsp;windows-pr gemのRuby　2.4 32bit版でのSEGV
    * [Bug #13768]&nbsp;SIGCHLD and Thread dead-lock problem
    * [Bug #13773]&nbsp;Improve String#prepend performance if only one argument is given
    * [Bug #13774]&nbsp;for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment
    * [Bug #13781]&nbsp;Should the safe navigation operator invoke `nil?`
    * [Bug #13795]&nbsp;Hash#select return type does not match Hash#find_all
    * [Bug #13811]&nbsp;Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)
    * [Bug #13818]&nbsp;Licence issue with use of Onigmo rather than Oniguruma library files
    * [Bug #13827]&nbsp;Improve performance of `Base64.urlsafe_encode64`
    * [Bug #13829]&nbsp;NUL char in $0
    * [Bug #13833]&nbsp;String#scanf("%a") incorrectly requires a sign on the (binary) exponent
    * [Bug #13835]&nbsp;Using 'open-uri' with 'tempfile' causes an exception
    * [Bug #13167]&nbsp;Dir.glob is 25x slower since Ruby 2.2
    * [Bug #12036]&nbsp;Enumerator's automatic rewind behavior
    * [Bug #13872]&nbsp;Duplicate assignment no longer silences "assigned but unused variable" warning
    * [Bug #13848]&nbsp;BigDecimal.new('200.') raises an exception
    * [Bug #13880]&nbsp;`BigDecimal(string)` should raise on invalid values in `string`
    * [Bug #13876]&nbsp;Tempfile's finalizer can be interrupted by a Timeout exception which can cause the process to hang
    * [Bug #13885]&nbsp;Random.urandom と securerandom について
    * [Bug #10104]&nbsp;Fileutils cp_r fails on sockets or fifes even if File.mknod and File.mkfifo are defined
- [Feature #13653]&nbsp;Bundled zlib helper (shyouhei) hsbt?
- [Feature #13381]&nbsp;[PATCH] Expose rb_fstring and its family to C extensions (shyouhei) ko1?
- [Feature #13434]&nbsp;better method definition in C API (shyouhei) ko1?
- [Feature #13713]&nbsp;socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
- [Feature #13715]&nbsp;[PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
- [Feature #13719]&nbsp;[PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
- [Feature #13732]&nbsp;Precise Time.now on Windows (shyouhei)
- [Feature #13733]&nbsp;Dump the delegator instead of the delegated object (shyouhei)
- [Feature #13625]&nbsp;BigDecimal short form / shorthand (shyouhei)
- [Feature #13712]&nbsp;String#start_with? with regexp (shyouhei)
* [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (a_matsuda)
- [Feature #13751]&nbsp;Suppert SSHFP resource records in rubysl-resolv (shyouhei)
- [Bug #13752]&nbsp;Can't observe sibling refinements (shyouhei)
- [Feature #13763]&nbsp;Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)
- [Feature #13765]&nbsp;Add Proc#bind (shyouhei)
- [Feature #13767]&nbsp;add something like python's buffer protocol to share memory between different narray like classes (shyouhei)
- [Feature #13777]&nbsp;Array#delete_all (shyouhei)
- [Misc #13804]&nbsp;Protected methods cannot be overridden (shyouhei)
- [Feature #13807]&nbsp;A method to filter the receiver against some condition (shyouhei)
- [Feature #13805]&nbsp;Make refinement scoping to be like that of constants (shyouhei)
- [Feature #12282]&nbsp;Hash#dig! for repeated applications of Hash#fetch (shyouhei)
- [Feature #9450]&nbsp;Allow overriding SSLContext options in Net::HTTP (shyouhei)
- [Feature #13820]&nbsp;Add a nill coalescing operator (shyouhei)
- [Feature #13770]&nbsp;Can't create valid Cyrillic-named class/module (shyouhei) status?
- [Misc #13840]&nbsp;Collection methods - stability (shyouhei, duerst (propose to reject))
- [Feature #13581]&nbsp;Syntax sugar for method reference (shyouhei)
- [Feature #13869]&nbsp;Filter non directories from Dir.glob (shyouhei)
- [Feature #8948]&nbsp;Frozen regex (shyouhei)
- [Feature #13881]&nbsp;Use getcontext/setcontext on OS X (shyouhei)
- [Feature #13883]&nbsp;Change from gperf 3.0.4 to gperf 3.1 (shyouhei)
- [Feature #13890]&nbsp;Allow a regexp as an argument to 'count', to count more interesting things than single characters (shyouhei)
- [Feature #13873]&nbsp;Optimize Dir.glob with FNM_EXTGLOB (shyouhei)
- [Feature #13245]&nbsp;[PATCH] reject inter-thread TLS modification (shyouhei)
- [Feature #13901]&nbsp;Add branch coverage  (shyouhei)
- [Feature #12753]&nbsp;Useful operator to check bit-flag is true or false (shyouhei)
- [Feature #8499]&nbsp;Importing Hash#slice, Hash#slice!, Hash#except, and Hash#except! from ActiveSupport (shyouhei)
- [Feature #13922]&nbsp;Consider showing warning messages about same-named aliases - either directly or perhaps via the "did you mean gem" (shyouhei)
- [Feature #13924]&nbsp;Add headings/hints to RubyVM::InstructionSequence#disasm (shyouhei)
- [Feature #10849]&nbsp;Adding an alphanumeric function to SecureRandom (shyouhei)

## From attendees

* [Feature #9323] IO#writev (glass)
* [Feature #13867] Copy offloading in IO.copy_stream (glass)
* [Feature #11666] IPAddr#private? (glass)
* [Feature #13610] IPAddr has no public method to get the current subnet mask (glass)
* [Feature #8047] IPAddr makes host address with netmask (glass)
- [Feature #8661]&nbsp;Add option to print backstrace in reverse order(stack frames first & error last) (shyouhei) objection appealed.
- [Misc #13968]&nbsp;[Ruby 3.x perhaps] - A (minimal?) static variant of ruby (shyouhei)
- [Feature #13969]&nbsp;Dir#each_child (shyouhei)
- [Feature #12115]&nbsp;Add Symbol#call to allow to_proc shorthand with arguments (shyouhei)
- [Feature #5964]&nbsp;Make Symbols an Alternate Syntax for Strings (shyouhei) seems updated since closed
- [Feature #12533]&nbsp;Refinements: allow modules inclusion, in which the module can call internal methods which it defines.  (shyouhei)
- [Feature #13984]&nbsp;BigDecimal should be immutable/frozen and return itself on #dup (shyouhei)
- [Feature #13985]&nbsp;Avoid exception for #dup/#clone on Rational and Complex (shyouhei)
- [Feature #13936]&nbsp;Make regular expressions debugable (shyouhei)
- [Bug #13986]&nbsp;Integer#fdiv with Complex returns unexpected value (shyouhei)
- [Feature #13983]&nbsp;Rational and Complex should be frozen (shyouhei)
- [Feature #14022]&nbsp;String#surround (shyouhei)
- bugs that are not assigned (shyouhei)
    * [Bug #13930]&nbsp;Exception is caught in rescue above ensure
    * [Bug #13970]&nbsp;Base64 urlsafe_decode64 unsafe use of tr.
    * [Bug #14010]&nbsp;RubyVM logic in forwardable backported to 2.3, not removed
    * [Bug #14016]&nbsp;URI IPv6 address can't be used to open socket
    * [Bug #14015]&nbsp;Enumerable & Hash yielding arity
    * [Bug #13887]&nbsp;test/ruby/test_io.rb may get stuck with FIBER_USE_NATIVE=0 on Linux

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [Feature #13300] Strip chroot path from $LOADED_FEATURES when calling Dir.chroot (jeremyevans0)
* [Feature #12882] Add caller/file/line information to internal Kernel#warn calls (jeremyevans0)
* example: [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (aycabta)

# Log

Date: 2017/10/19 (Mon)
Time: 14:00- 18:00 (JST)
Place: Speee, Inc.
Sign-up: https://ruby.connpass.com/event/67914/
log edit: https://docs.google.com/document/d/1XTTzUINO2Fegt-eFgGTY8oGF3uTy5Bz4ZXJdKzyFp34/edit#heading=h.ujsctc6nvlk
log: TBD
Agenda
next
11/29
at MoneyForward HQ.
About 2.5 timeframe
https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25
preview1 released.
naruse: no freeze expected.
naruse: preview2?
naruse: RC expected in Dec.
合宿
This weekend.
In Nikko.
Don’t forget to vote beforehand!
Carry-over from previous meeting(s)
bugs that are not assigned (shyouhei)
[Bug #13675] Should Zlib::GzipReader#ungetc accept nil?
[Bug #13700] Enumerable#sum may not work for Ranges subclasses due to optimization (mrkn)
[Bug #13716] Unexpected or undocumented (or maybe both) behaviour when mixing String#scan with named captures
[Bug #13549] MinGW / Windows encoding - Two issues
[Bug #13746] windows-pr gemのRuby　2.4 32bit版でのSEGV
[Bug #13768] SIGCHLD and Thread dead-lock problem
[Bug #13773] Improve String#prepend performance if only one argument is given
[Bug #13774] for methods defined from procs, the binding of the resulting bound method proc does not have access to the original proc's closure environment
[Bug #13781] Should the safe navigation operator invoke nil?
[Bug #13795] Hash#select return type does not match Hash#find_all
[Bug #13811] Ruby 2.4.1 fails to compile inside qemu armhf - signal 11 (Segmentation fault)
[Bug #13818] Licence issue with use of Onigmo rather than Oniguruma library files
[Bug #13827] Improve performance of Base64.urlsafe_encode64
[Bug #13829] NUL char in $0 (nobu)
matz: I think we don’t need this.
[Bug #13833] String#scanf("%a") incorrectly requires a sign on the (binary) exponent
[Bug #13835] Using 'open-uri' with 'tempfile' causes an exception
[Bug #13167] Dir.glob is 25x slower since Ruby 2.2
[Bug #12036] Enumerator's automatic rewind behavior
[Bug #13872] Duplicate assignment no longer silences "assigned but unused variable" warning
[Bug #13848] BigDecimal.new('200.') raises an exception
[Bug #13880] BigDecimal(string) should raise on invalid values in string
[Bug #13876] Tempfile's finalizer can be interrupted by a Timeout exception which can cause the process to hang
[Bug #13885] Random.urandom と securerandom について
[Bug #10104] Fileutils cp_r fails on sockets or fifes even if File.mknod and File.mkfifo are defined
[Feature #13653] Bundled zlib helper (shyouhei) hsbt?
hsbt: zlib, openssl, and readline are currently mandatory to compile ruby.  They are not skipped.  How about shipping our own copy of  zlib?
akr: is it possible to build out-of-place?
hsbt: not tested…
nobu: not seems to be.
[Feature #13381] [PATCH] Expose rb_fstring and its family to C extensions (shyouhei) ko1?
[Feature #13434] better method definition in C API (shyouhei) ko1?
[Feature #13713] socketの便利メソッドのdatagramのUNIXSocket用対応 (shyouhei)
[Feature #13715] [PATCH] avoid garbage from Symbol#to_s in interpolation (shyouhei)
[Feature #13719] [PATCH] net/http: allow existing socket arg for Net::HTTP.start (shyouhei)
[Feature #13732] Precise Time.now on Windows (shyouhei)
[Feature #13733] Dump the delegator instead of the delegated object (shyouhei)
[Feature #13625] BigDecimal short form / shorthand (shyouhei)
[Feature #13712] String#start_with? with regexp (shyouhei)
matz: accepted.  What about the MatchData?
akr: it seems the use case is lexer, and in doing so $’ must be obtainable.
[Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (a_matsuda)
[Feature #13751] Suppert SSHFP resource records in rubysl-resolv (shyouhei)
[Bug #13752] Can't observe sibling refinements (shyouhei)
[Feature #13763] Trigger "unused variable warning" for unused variables in parameter lists (shyouhei)
[Feature #13765] Add Proc#bind (shyouhei)
[Feature #13767] add something like python's buffer protocol to share memory between different narray like classes (shyouhei)
[Feature #13777] Array#delete_all (shyouhei)
k0kubun: Array#partition works, but creates a temporary array which is not efficient
akr: sounds like what is wanted is a destrucive Array#partition
knu: partition!
matz: delete_all is NG
mame: what if appending outer-scope array inside of the block of Array#delete_if
knu: slice! behaves similarly (returns what was deleted)
glass: what if passing optional parameter to Array#delete_if
[Misc #13804] Protected methods cannot be overridden (shyouhei)
[Feature #13807] A method to filter the receiver against some condition (shyouhei)
[Feature #13805] Make refinement scoping to be like that of constants (shyouhei)
[Feature #12282] Hash#dig! for repeated applications of Hash#fetch (shyouhei)
[Feature #9450] Allow overriding SSLContext options in Net::HTTP (shyouhei)
[Feature #13820] Add a nill coalescing operator (shyouhei)
[Feature #13770] Can't create valid Cyrillic-named class/module (shyouhei) status?
[Misc #13840] Collection methods - stability (shyouhei, duerst (propose to reject))
matz: our sort is not stable.
[Feature #13581] Syntax sugar for method reference (shyouhei)
[Feature #13869] Filter non directories from Dir.glob (shyouhei)
akira: Yes, I want this.
naruse: I’m wondering the API.
akr: what happens for symlink that is directory?
akira: Rails want to read what is linked.
naruse: current patch does not follow, but I can change it.
naruse: Python provides struct dirent
nobu: I have a patch for that.
matz: I don’t want to add new class for struct dirent.
[Feature #8948] Frozen regex (shyouhei)
ko1: we have two issues: if we should freeze all regexp instances, or regexp literals only.  The proposed patch is former.
akr: breaking backwards compatibility sounds not well-doing.
hsbt: it seems several use case can be found around test doubles.
akr: what is the merit of frozen regex literal? there seems nothing?
ko1: What is in my mind is parallelism.  We can pass regexp instances around Guilds if they were frozen.
naruse: I don’t think we need to freeze Regexp until real benefits are there.
[Feature #13881] Use getcontext/setcontext on OS X (shyouhei)
[Feature #13883] Change from gperf 3.0.4 to gperf 3.1 (shyouhei)
[Feature #13890] Allow a regexp as an argument to 'count', to count more interesting things than single characters (shyouhei)
[Feature #13873] Optimize Dir.glob with FNM_EXTGLOB (shyouhei)
matz: accepted.
[Feature #13245] [PATCH] reject inter-thread TLS modification (shyouhei)
[Feature #13901] Add branch coverage (shyouhei)
[Feature #12753] Useful operator to check bit-flag is true or false (shyouhei)
akr: counter-proposed.
matz: I like allbits? etc.
knu: I prefer anybit?
shyouhei: all_bits_set?
[Feature #8499] Importing Hash#slice, Hash#slice!, Hash#except, and Hash#except! from ActiveSupport (shyouhei)
knu: proposed Hash#select! differs in API.
shyouhei: what if we accept Hash#slice and not slice!
matz: slice!’s return value sounds odd to me.
[Feature #13922] Consider showing warning messages about same-named aliases - either directly or perhaps via the "did you mean gem" (shyouhei)
[Feature #13924] Add headings/hints to RubyVM::InstructionSequence#disasm (shyouhei)
[Feature #10849] Adding an alphanumeric function to SecureRandom (shyouhei)
From attendees
[Feature #9323] IO#writev (glass)
glass: I mean the purpose is atomicity.
glass: Also, normal says it’s faster
shyouhei: what happens if atomicity breaks?
glass: partial writes are hidden.
nobu: what happens if encoding conversions happens?
naruse: encoding conversions shall happen before entire call to writev() to happen.
akr: that almost kills the merit.
akr: what’s the use case?
glass: I heard that network IO might be optimized when call to writev().
matz: I prefer extending IO#write than a new method.
[Feature #13867] Copy offloading in IO.copy_stream (glass)
glass: The reason why I added this optional argument is that people might want to kill this feature, like btrfs’s link.
naruse: sounds no one would bother such thing.
shyouhei: go ahead if this changes no API.
[Feature #11666] IPAddr#private? (glass)
glass: I want to detect V4 private address.
knu: ipaddress gem has this feature.
assign knu
[Feature #13610] IPAddr has no public method to get the current subnet mask (glass)
knu: what about add prefix method?
[Feature #8047] IPAddr makes host address with netmask (glass)
knu: I understand the motivation but let me think about the API.
[Feature #8661] Add option to print backstrace in reverse order(stack frames first & error last) (shyouhei) objection appealed.
[Misc #13968] [Ruby 3.x perhaps] - A (minimal?) static variant of ruby (shyouhei)
[Feature #13969] Dir#each_child (shyouhei)
[Feature #12115] Add Symbol#call to allow to_proc shorthand with arguments (shyouhei)
[Feature #5964] Make Symbols an Alternate Syntax for Strings (shyouhei) seems updated since closed
[Feature #12533] Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shyouhei)
[Feature #13984] BigDecimal should be immutable/frozen and return itself on #dup (shyouhei)
[Feature #13985] Avoid exception for #dup/#clone on Rational and Complex (shyouhei)
[Feature #13936] Make regular expressions debugable (shyouhei)
[Bug #13986] Integer#fdiv with Complex returns unexpected value (shyouhei)
[Feature #13983] Rational and Complex should be frozen (shyouhei)
matz: accepted.
[Feature #14022] String#surround (shyouhei)
bugs that are not assigned (shyouhei)
[Bug #13930] Exception is caught in rescue above ensure
[Bug #13970] Base64 urlsafe_decode64 unsafe use of tr.
[Bug #14010] RubyVM logic in forwardable backported to 2.3, not removed
[Bug #14016] URI IPv6 address can't be used to open socket
[Bug #14015] Enumerable & Hash yielding arity
[Bug #13887] test/ruby/test_io.rb may get stuck with FIBER_USE_NATIVE=0 on Linux
auto fiber? (hsbt)
ko1: I think it’d be accepted in 2.6 at the fastest.  Also mame said “fiber” is wrong.
hsbt: I think you have to mention Eric about the current situation.
#614 instance_methods(ancestor) (mame)
mame: not just true/false but specify a parent class.
matz: use case?
mame: maybe Module#conflict? can be made upon this.
akr: I don’t think so.  Module inclusion might not be linear, so conflict cannot but list up source class of every and all of methods.
From non-attendees
Write your name and your interest (what do you want to ask and to whom?) please.
[Feature #13300] Strip chroot path from $LOADED_FEATURES when calling Dir.chroot (jeremyevans0)
mame: what happens if the loaded file is outside of the changed root?
naruse: is it safe to load such thing?
shyouhei: should be, as long as it was loaded before chroot
akr: it’s very difficult to provide a generic solution.
nobu: should LOAD_PATH also be modified?
shyouhei: seems yes.
akr: it sounds reasonable to reject out-tree load paths.  However for loaded features…
nobu: what if prohibiting require once after chroot is called.
[Feature #12882] Add caller/file/line information to internal Kernel#warn calls (jeremyevans0)
shyouhei: it seems warn should automatically show the caller.
akr: that’s difficult.  For instance delegator’s warning shall point to 3 level upper frame.  It is up to each library what to show.
matz: I prefer option #1 that jeremy proposes. Naming?
example: [Feature #13686] Add states of scanner to tokens from Ripper.lex and Ripper::Filter#on_* (aycabta)
