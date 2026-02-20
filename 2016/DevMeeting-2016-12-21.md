---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-12-21

Date: 2016/12/21 (Wed)
Time: 14:00- 19:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/46822/
Attendees: **TBD**
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

## I want to clear my assigned tickets (shyouhei)
* Can who close outdated issues? https://bugs.ruby-lang.org/issues?set_filter=1&f%5B%5D=status_id&op%5Bstatus_id%5D=o&f%5B%5D=assigned_to_id&op%5Bassigned_to_id%5D=%3D&v%5Bassigned_to_id%5D%5B%5D=10

## About 2.4 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24

## Blocker for 2.4 release

* [Feature #12944] Change Kernel#warn to call Warning.warn (shyouhei)
* [Feature #13017] Switch SipHash from SipHash24 to SipHash13(?)
    * is [Bug #13019] related? (shyouhei)
* [Feature #12142] Hash tables with open addressing (shyouehi) it seems ruby-core:78558 must be looked at.

## Carry-over from previous meeting(s)

* Bugs that seems forgotten (shyouhei) assign who?
    * [Bug #12666] Fatal error: glibc detected an invalid stdio handle
    * [Bug #12945] Use-after-free in vm_trace.c
    * [Bug #12961] Bad value for range using infinity for Date or Time
    * [Bug #12809] passing a proc to Kernel#lambda does not create a lambda
    * [Bug #12551] Exception accessing file with long path on windows
    * [Bug #12907] rb_respond_to() return value is incorrect
    * [Bug #13000] Implement Set#include? with Hash#include?
    * [Bug #13018] end of file reached (EOFError) from SMTP
    * [Bug #13030] Unexpected T_IMEMO object when building with VMDEBUG
    * [Bug #13043] Exception#cause can become recursive/infinite
* [Feature #12732] An option to pass to `Integer`, `Float`, to return `nil` instead of raise an exception (shyouhei) conclusion?
* [Feature #12745] String#(g)sub(!) should pass a MatchData to the block, not a String (shyouhei) conclusion?
* [Feature #12753] Useful operator to check bit-flag is true or false (shyouhei)
* [Feature #11815] Proposal for method `Array#difference` (shyouhei) Matz is thinking about this.
* [Feature #2074] json の j や jj は module_function にするべき？
* [Feature #9209][Feature #11925] Struct instances creatable with named args (nobu)
* [Feature #12802] Add BLAKE2 support to Digest (nobu)
* [Feature #12861] Lexically scoped super (nobu)
* [Bug #12812] Added Coverage#result= (shyuohei)
* [Feature #12180] switch id_table.c variant (shyouhei) ko1?
* [Feature #8158] lightweight structure for loaded features index (shyouhei)
* [Feature #12839] CSV - Give not nil but empty strings for empty fields (shyouhei) assign who?
* [Feature #12854] Proc#curry should return an instance of the class, not Proc (shyouhei)
* [Bug #12855] Inconsistent keys identity in compare_by_identity Hash when using literals (shyouhei) bug or...?
* [Feature #12882] Add caller/file/line information to internal Kernel#warn calls (shyouhei)
* [Feature #7360] Adding Pathname#glob (shyouhei)
* [Feature #11286] [PATCH] Add case equality arity to Enumerable's sequence predicates. (shyouhei)
* [Feature #5446] at_fork callback API (shyouhei)
* [Feature #12780] BigDecimal#round returns different types depending on argument (shyouhei)
* [Feature #11428] system/exec/etc. should to_s their argument to restore Pathname functionality as it was in 1.8 (shyouhei)
* [Feature #12180] switch id_table.c variant (shyouhei)
* [Feature #12901] Anonymous functions without scope lookup overhead (shyouhei)
* [Feature #12906] do/end blocks work with ensure/rescue/else (shyouhei)
* [Feature #12913] A way to configure the default maximum width of pp (shyouhei)
* [Feature #12926] -l flag for line end processing should use chomp! instead of chop! (shyouhei)
* [Feature #12912] An endless range `(1..)` (shyouhei)
* [Feature #12933] Add Some and Optional (shyouhei)
* [Feature #12931] Add support for Binding#instance_eval (shyouhei)
* [Feature #12929] ternary should look ahead w/in a block (and not care about newlines) (shyouhei)
* [Feature #12898] String#match? method in addition to Regexp#match? (shyouhei)
* [Misc #12935] Webrick: Update HTTP Status codes, share them (shyouhei)
* [Feature #12962] Feature Proposal: Extend 'protected' to support module friendship (shyouhei)
* [Feature #12957] A more OO way to create lambda Procs (shyouhei)
* [Feature #12963] ?string longer than one char (shyouhei)
* [Feature #12966] net/ftp to include fxp support? (shyouhei) who's going to handle this?
* [Feature #12967] Add a default for RUBY_GC_HEAP_GROWTH_MAX_SLOTS out-of-the-box (shyouhei)
* [Feature #12969] Allow optional parameter in String#strip and related (shyouhei)
* [Feature #12968] Allow default value via block for Integer(), Float() and Rational() (shyouhei)

## From attendees

* [Feature #5481] Gemifying Ruby standard library (hsbt)
* [Feature #12995] Conditional expression taking a receiver outside the condition (shyouhei)
* [Bug #12998] paragraph mode inconsistency between `IO#each_line` and `String#each_line` (nobu)
* [Feature #13001] Add `full` option to `ObjectSpace.dump_all` (shyouhei)
* [Feature #12996] Optimize Range#=== (shyouhei)
* [Feature #10912] Add method(s) to IPAddr for determining whether an address is link local (shyouhei)
* [Bug #13005] Inline rescue is inconsistent when rescuing NoMethodError (Dürst)
* [Feature #13016] String#gsub(hash) (shyouhei)
* [Bug #8241] If uri host-part has underscore ( '_' ), 'URI#parse' raise 'URI::InvalidURIError' (shyouhei) go WHATWG or …?
* [Bug #8826] BigDecimal#div and #quo different behavior and inconsistencies (shyouhei) status?>mrkn
* [Feature #13026] Public singleton methods (shyouhei)
* [Feature #8158] lightweight structure for loaded features index (shyouhei)
* [Bug #13024] Confusing error message matching a non-ASCII string with ASCII-regex (shyouhei)
* [Feature #13009] Implement fetch for Thread.current (shyouhei)
* [Feature #13045] Passing a Hash with String keys as keyword arguments (shyouhei)
* [Feature #13048] Better way to do Regexp.new(Regexp.escape("some string")) (shyouhei)
* [Feature #9846] Regexp#to_regexp (shyouhei)
* [Feature #13047] Use String literal instead of `String#+` for multiline pretty-printing of multiline strings (shyouhei)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* [ruby-core:78633](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/78633) ruby/spec needs help from CRuby committers (eregon)
  I would like opinions and discussion from CRuby committers on this idea.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

# Log

Date: 2016/12/21 (Wed)

Time: 14:00- 18:00 (JST)

Place: Money Forward Headquater

Attendees/参加者: matz, nobu, akr, naruse, mrkn, hsbt, shyouhei

Language: mostly Japanese (sorry for non native Japanese speakers)

next time

- January 19, at MoneyForward, Inc.

Release engineering

~12/23 update bundled gem etc.

12/24 happy holidays

12/25 release

Issues

## Blocker for 2.4 release

- [[Feature #12944]](https://bugs.ruby-lang.org/issues/12944) Change Kernel#warn to call Warning.warn (shyouhei)

- nobu: nobody is against to call Warning.warn but there are pitfalls
- akr: what about stringify given arguments, then pass to Warning.warn?
- akr: is adding “\\n” to the given argument Kernel#warn’s duty or Warning.warn’s?
- akr: we want to change spec of Kernel#warn.  In order to do so we should avoid rushing this to 2.4.

- [[Feature #13017]](https://bugs.ruby-lang.org/issues/13017) Switch SipHash from SipHash24 to SipHash13(?)

- it should be safe to remain SipHash24.

- [[Bug #13019]](https://bugs.ruby-lang.org/issues/13019) related? (shyouhei)

- nobu: no opinion against it

- [[Feature #12142]](https://bugs.ruby-lang.org/issues/12142) Hash tables with open addressing (shyouehi) it seems ruby-core:78558 must be looked at.

- nobu: it is done.

## Carry-over from previous meeting(s)

- Bugs that seems forgotten (shyouhei) assign who?

- [[Bug #12666]](https://bugs.ruby-lang.org/issues/12666) Fatal error: glibc detected an invalid stdio handle

- akr: why not use ldd(1)?

- [[Bug #12945]](https://bugs.ruby-lang.org/issues/12945) Use-after-free in vm_trace.c

- nobu: I think I fixed this

- [[Bug #12961]](https://bugs.ruby-lang.org/issues/12961) Bad value for range using infinity for Date or Time

- nobu: is this a problem of coerce?
- matz: the problem is what receiver is this.

- [[Bug #12809]](https://bugs.ruby-lang.org/issues/12809) passing a proc to Kernel#lambda does not create a lambda

- akr: it is by design.

- [[Bug #12551]](https://bugs.ruby-lang.org/issues/12551) Exception accessing file with long path on windows

- nobu it’s already assigned.

- [[Bug #12907]](https://bugs.ruby-lang.org/issues/12907) rb_respond_to() return value is incorrect

- matz: this is a bug.
- assign nagachika

- [[Bug #13000]](https://bugs.ruby-lang.org/issues/13000) Implement Set#include? with Hash#include?

- akr: is this a bug?

- [[Bug #13018]](https://bugs.ruby-lang.org/issues/13018) end of file reached (EOFError) from SMTP

- assign shugo

- [[Bug #13030]](https://bugs.ruby-lang.org/issues/13030) Unexpected T_IMEMO object when building with VMDEBUG

- assign ko1

- [[Bug #13043]](https://bugs.ruby-lang.org/issues/13043) Exception#cause can become recursive/infinite

- akira: what is wrong with recusive cause?
- mrkn: what’s the expected cause in this case?

- [[Feature #12732]](https://bugs.ruby-lang.org/issues/12732) An option to pass to Integer, Float, to return nil instead of raise an exception (shyouhei) conclusion?

- shyouhei: naruse and tenderlove wrote their opinions.

- [[Feature #12745]](https://bugs.ruby-lang.org/issues/12745) String#(g)sub(!) should pass a MatchData to the block, not a String (shyouhei) conclusion?

- akr: #sg or #gs, matz’s original proposal should be accepted I think
- nobu: how about String#sed

- [[Feature #12753]](https://bugs.ruby-lang.org/issues/12753) Useful operator to check bit-flag is true or false (shyouhei)

- seems discussion is going on

- [[Feature #11815]](https://bugs.ruby-lang.org/issues/11815) Proposal for method Array#difference (shyouhei) Matz is thinking about this.

- mrkn: what about Array#delete with multiple arguments?

- [[Feature #2074]](https://bugs.ruby-lang.org/issues/2074) json の j や jj は module_function にするべき？

- akr: This is a 3PI

- [[Feature #9209]](https://bugs.ruby-lang.org/issues/9209)[[Feature #11925]](https://bugs.ruby-lang.org/issues/11925) Struct instances creatable with named args (nobu)

- akr: naming issue.

- [[Feature #12802]](https://bugs.ruby-lang.org/issues/12802) Add BLAKE2 support to Digest (nobu)

- naruse: do we need this?  no actual use yet.  SHA3 has chance.
- naruse: just let people use OpenSSL directly.
- akira: seems Tony does not demand this but saying “if someone has interest …”

- [[Feature #12861]](https://bugs.ruby-lang.org/issues/12861) Lexically scoped super (nobu)

- nobu: define_method is the cause of evil
- matz: I’m not sure if lexically-scoped super would actually get used or not.

- [[Bug #12812]](https://bugs.ruby-lang.org/issues/12812) Added Coverage#result= (shyuohei)

- mame is assigned, let him handle this

- [[Feature #12180]](https://bugs.ruby-lang.org/issues/12180) switch id_table.c variant (shyouhei) ko1?

- is it OK?

- [[Feature #8158]](https://bugs.ruby-lang.org/issues/8158) lightweight structure for loaded features index (shyouhei)

- naruse: not for 2.4 but seems OK for 2.5.

- [[Feature #12839]](https://bugs.ruby-lang.org/issues/12839) CSV - Give not nil but empty strings for empty fields (shyouhei) assign who?

- CSV has no maintenar

- [[Feature #12854]](https://bugs.ruby-lang.org/issues/12854) Proc#curry should return an instance of the class, not Proc (shyouhei)

- akr: we are not sure if it is safe, because curry is something very conceptual.

- [[Bug #12855]](https://bugs.ruby-lang.org/issues/12855) Inconsistent keys identity in compare_by_identity Hash when using literals (shyouhei) bug or...?

- akr: this is an over-optimization.

- [[Feature #12882]](https://bugs.ruby-lang.org/issues/12882) Add caller/file/line information to internal Kernel#warn calls (shyouhei)

- matz will respond

- [[Feature #7360]](https://bugs.ruby-lang.org/issues/7360) Adding Pathname#glob (shyouhei)

- akr: I understand the needs but the implementation is wrong.

1. nobu will create Dir.glob(base:) ticket
2. then implement.
3. then this ticket should use that.

- [[Feature #11286]](https://bugs.ruby-lang.org/issues/11286) [PATCH] Add case equality arity to Enumerable's sequence predicates. (shyouhei)

- shyouhei: seems the OP wants some variant of grep
- akr: regexp might be straight-forward to read but?
- matz: positive, but might be better in another name?

- [[Feature #5446]](https://bugs.ruby-lang.org/issues/5446) at_fork callback API (shyouhei)

- what about make it as a pure-ruby gem?

- [[Feature #12780]](https://bugs.ruby-lang.org/issues/12780) BigDecimal#round returns different types depending on argument (shyouhei)
- [[Feature #11428]](https://bugs.ruby-lang.org/issues/11428) system/exec/etc. should to_s their argument to restore Pathname functionality as it was in 1.8 (shyouhei)
- [[Feature #12180]](https://bugs.ruby-lang.org/issues/12180) switch id_table.c variant (shyouhei)

- [[Feature #5481]](https://bugs.ruby-lang.org/issues/5481) [https://bugs.ruby-lang.org/issues/5481](https://bugs.ruby-lang.org/issues/5481)

- \[Bug [#12998](https://bugs.ruby-lang.org/issues/12998)[\] paragraph mode inconsistency between](https://bugs.ruby-lang.org/issues/5481) [IO#each_line](https://bugs.ruby-lang.org/issues/5481) [and](https://bugs.ruby-lang.org/issues/5481) [String#each_line](https://bugs.ruby-lang.org/issues/5481)[(nobu)](https://bugs.ruby-lang.org/issues/5481)

- akr: String#each_line should be fixed in 2.5.
