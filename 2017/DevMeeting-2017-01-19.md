---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-01-19

# DevelopersMeeting20170119Japan

Date: 2017/01/19 (Wed)
Time: 14:00- 19:00 (JST)
Place: Money Forward inc. Headquarter
Sign-up: https://ruby.connpass.com/event/47745/
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

## About 2.5 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25

## Carry-over from previous meeting(s)

* Postponed issues to avoid last-minute commits to 2.4:
    - [Feature #12180] switch id_table.c variant (shyouhei)
    - [Feature #12944] Change Kernel#warn to call Warning.warn (shyouhei)
        - related:
        - [Feature #13124] Should #puts convert to external encoding? (shyouhei)
    - [Feature #13017] Switch SipHash from SipHash24 to SipHash13 (shyouhei) I'm confident it's OK to go to 2.5.
    - [Bug #12998] paragraph mode inconsistency between IO#each_line and String#each_line(nobu)
    - [Feature #8158] lightweight structure for loaded features index (shyouhei)
    - [Feature #12854] Proc#curry should return an instance of the class, not Proc (shyouhei)
* [Feature #5481] Gemifying Ruby standard library (hsbt)
 * Waiting approval of Matz
* [Feature #12901] Anonymous functions without scope lookup overhead (shyouhei)
* [Feature #12906] do/end blocks work with ensure/rescue/else (shyouhei)
* [Feature #12926] -l flag for line end processing should use chomp! instead of chop! (shyouhei)
* [Feature #12912] An endless range `(1..)` (shyouhei)
* [Feature #12933] Add Some and Optional (shyouhei)
* [Feature #12931] Add support for Binding#instance_eval (shyouhei)
* [Feature #12929] ternary should look ahead w/in a block (and not care about newlines) (shyouhei)
* [Misc #12935] Webrick: Update HTTP Status codes, share them (shyouhei)
* [Feature #12962] Feature Proposal: Extend 'protected' to support module friendship (shyouhei)
* [Feature #12957] A more OO way to create lambda Procs (shyouhei)
* [Feature #12966] net/ftp to include fxp support? (shyouhei) who's going to handle this?
* [Feature #12967] Add a default for RUBY_GC_HEAP_GROWTH_MAX_SLOTS out-of-the-box (shyouhei)
* [Feature #12969] Allow optional parameter in String#strip and related (shyouhei)
* [Feature #12968] Allow default value via block for Integer(), Float() and Rational() (shyouhei)
* [Feature #12995] Conditional expression taking a receiver outside the condition (shyouhei)
* [Bug #12998] paragraph mode inconsistency between `IO#each_line` and `String#each_line` (nobu)
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

## From attendees

* Missed bugs to be assigned
    * [Bug #13111] Degraded performance for delegated methods through Forwardable module (shyouhei) assign who?
    * [Bug #13098] miniruby fails with SEGV on NetBSD/powerpc (shyouhei) look at the backtarce (shyouhei)
    * [Bug #13125] MRI has too much Qtrue : Qfalse; (shyouhei)
    * [Bug #13127] DRb `load': connection closed (DRb::DRbConnError) when client exit's from within a loop iterating over remote objects (shyouhei)
* [Feature #13048] Better way to do Regexp.new(Regexp.escape("some string")) (shyouhei)
* [Feature #8661] Add option to print backstrace in reverse order(stack frames first & error last) (shyouhei)
* [Feature #13077] [PATCH] introduce String#fstring method (shyouhei)
* [Feature #13083] {String|Symbol}#match{?} with nil returns falsy as Regexp#match{?} (shyouhei)
* [Feature #12508] Integer#mod_pow (shyouhei)
* [Feature #13067] TrueClass,FalseClass to provide `===` to match truthy/falsy values. (shyouhei)
* [Feature #8661] Add option to print backstrace in reverse order(stack frames first & error last) (shyouhei)
* [Misc #13072] Current state of date standard library (shyouhei)
    - related:
    * [Bug #12981] Date.parse raises an Argument error under a specific condition (shyouhei) assign who?
    * [Bug #13101]  Date#rfc2822 and Time#rfc2822 don't return the same format
* [Bug #13064] Inconsistent behavior with `next` inside `begin`/`end` across different implementations. (shyouhei) spec or...?
* [Feature #13077] [PATCH] introduce String#fstring method (shyouhei)
* [Feature #13083] {String|Symbol}#match{?} with nil returns falsy as Regexp#match{?} (shyouhei)
* [Feature #13097] Deprecate Socket.gethostbyaddr and Socket.gethostbyname (shyouhei)
* [Feature #13067] TrueClass,FalseClass to provide `===` to match truthy/falsy values. (shyouhei)
* [Bug #13104] math.rb affects Rational literals (nobu)
* [Bug #13105] `String#to_f` and `String#to_r` don't stop successive underscores (nobu)
* [Feature #13109] `using` in refinements is required to be physically placed before the refined method call (shyouhei)
* [Bug #12705] yielding args to a lambda uses block/proc rather than lambda/method semantics (shyouhei) status?
* [Bug #9569] SecureRandom should try /dev/urandom first (shyouhei) I now think it's OK
* [Feature #13095] [PATCH] io.c (rb_f_syscall): remove deprecation notice (shyouhei)
* [Feature #13122] Special syntax for Hash#default_proc (shyouhei)


## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* In-tree copy of ruby spec (see http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/78985) (duerst)
* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

# Log

Date: 2017/01/19 (Wed)

Time: 14:00- 19:00 (JST)

Place: Money Forward inc. Headquarter

Sign-up: [https://ruby.connpass.com/event/47745/](https://ruby.connpass.com/event/47745/)

log edit: [https://docs.google.com/document/d/1ZKk-vxoYkq8b2H4ml2z4NhoHsi3GdZqhNXNgpB2vTv8/edit](https://docs.google.com/document/d/1ZKk-vxoYkq8b2H4ml2z4NhoHsi3GdZqhNXNgpB2vTv8/edit)

log: [https://docs.google.com/document/d/1ZKk-vxoYkq8b2H4ml2z4NhoHsi3GdZqhNXNgpB2vTv8/pub](https://docs.google.com/document/d/1ZKk-vxoYkq8b2H4ml2z4NhoHsi3GdZqhNXNgpB2vTv8/pub)

## Next meeting

- 2/22 at MoneyForward HQ
- Another place?

## About 2.5 timeframe

## [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25)

- 合宿

- Want to do
- 1Q - 2Q
- May through July?

## Current status of Ruby 2.4

- Mentainer not fixed yet.
- RA, nagachika, and usa should be consulted.

- March is too late.

- akr: 2.1’s security fix expires in this Feb.

## Carry-over from previous meeting(s)

## \[Feature [#12180](https://bugs.ruby-lang.org/issues/12180)\] switch id\_table.c variant (shyouhei)


- ko1: funny-falcon’s open addressing hash is beating mine.
- matz: why not just commit?
- ko1: I want to let him merge himself.
- akr: isn’t it too big for a new comer?
- ko1: I see. I’ll do.

## \[Feature [#12944](https://bugs.ruby-lang.org/issues/12944)\] Change Kernel#warn to call Warning.warn (shyouhei)


- nobu: what spec?
- shyouhei: give up passing Arrays to it.
- akr: compatibility concern?
- matz: why not just try it.

## \[Feature [#13124](https://bugs.ruby-lang.org/issues/13124)\] Should #puts convert to external encoding? (shyouhei)


- shyouhei: what’s this?
- akr: does it depend on internal encoding?
- naruse: yes.
- nobu: the String knows its own encoding.  why not honor that?
- naruse: current idea is that we don’t automatically encode things.
- akr: I think it’s OK for particular IO that needs encoding, it’s convenient to have such feature.
- naruse: OK, I’ll respond.

## \[Feature [#13017](https://bugs.ruby-lang.org/issues/13017)\] Switch SipHash from SipHash24 to SipHash13 (shyouhei)


- shyouhei: I'm confident it's OK to go to 2.5.
- nobu: SipHash24 was too strong.
- hsbt: who is going to apply?
- shyouhei: I will
- matz: go ahead.

## \[Bug [#12998](https://bugs.ruby-lang.org/issues/12998)\] paragraph mode inconsistency between IO#each\_line and String#each\_line(nobu)


- ko1: ideal behaviour?
- nobu: I think IO’s is better.
- matz: (nod)
- nobu: I already merged this.

## \[Feature [#8158](https://bugs.ruby-lang.org/issues/8158)\] lightweight structure for loaded features index (shyouhei)


- ko1: nobody but nobu can say if it’s OK.
- nobu: I’ll take a look.
- hsbt: why not let funny-falcon merge this?
- matz: I take the idea.

## \[Feature [#12854](https://bugs.ruby-lang.org/issues/12854)\] Proc#curry should return an instance of the class, not Proc (shyouhei)


- ko1: general idea?
- matz: basically we honor sub-classing but Array has exceptional situations.
- shyouhei: seems there are applications for this feature?
- matz: seems they are different proposals than applications of #curry.
- matz: I’ll ask for use cases.

## \[Feature [#5481](https://bugs.ruby-lang.org/issues/5481)\] Gemifying Ruby standard library (hsbt)


- hsbt: Waiting approval of Matz
- matz: rgr.
- naruse: isn’t it better to convert one by one?
- akr: there can be distinct discussions for each libraries.
- nobu: there are 3 kinds of libraries:

- standard libray,
- default gem, which are inside of source tree, comes with gemspec
- bundled gem, which are out of tree, installed automatically on make install

- hsbt: I’m talking about default gem.
- hsbt: practically what happens would be to create gemspec for each libraries.
- akr: I see concerns of dependency hell.
- hsbt: it’s OK we create separate ML threads for each library to discuss.

## \[Feature [#12901](https://bugs.ruby-lang.org/issues/12901)\] Anonymous functions without scope lookup overhead (shyouhei)


- ko1: matz said we should automatically optimize procs when possible, not explicitly stating like this.
- matz: (nod)
- akr: is that possible? it’s difficult to detect.
- ko1: lets make it possible.  say, by restricting use of Proc#binding.
- ko1: one problem on restricting binding access is that should kill debuggers to inspect running methods.  But I think that’s acceptable.
- ko1:

- a=b=c=nil; iter do; use(a); use(b); eval(“use(c)”); end # NG
- a=b=c=nil; iter do; binding; use(a); use(b); eval(“use(c)”); end # OK
- x = 1; func{|a| a+1}

- matz will respond, ko1 to create a new ticket that refer this one.

## \[Feature [#12906](https://bugs.ruby-lang.org/issues/12906)\] do/end blocks work with ensure/rescue/else (shyouhei)


- shyouhei: OP is wise because he do not talk about { … }
- matz: I don’t favor too small rescue blocks.
- naruse: I write this occasionaly.
- akira: and when I do so, I have to re-indent everything.
- naruse: HTTP or RDB networking codes tends to do error handling.  There are chances for its usage.
- akr: generally speaking it is good to reduce indent.
- matz: Let me think about it. -> OK

## \[Feature [#12926](https://bugs.ruby-lang.org/issues/12926)\] -l flag for line end processing should use chomp! instead of chop! (shyouhei)


- nobu: perl behaves like chomp.
- akr: isn’t it a bug?
- matz: that’s because chomp is newer than \-l.
- akr: chomp is ruby1.1+, \-l already exists in ruby 0.95
- matz: OK.

## \[Feature [#12912](https://bugs.ruby-lang.org/issues/12912)\] An endless range (1..) (shyouhei)


- shyouhei: what about opossite side ...1?
- mrkn: I think what is requested is about sequence, not range.
- akr: Range is defined over #succ, so 1.. is possible.
- nobu: 1..\*
- akr: 1.step works. isn’t it enough?
- shyouhei: is iteration the only usage?
- naruse: array index.
- mrkn: index for multi-dimensional array can be a possible application. Python’s numpy has such feature.
- akr: I doubt if we need a dedicated syntax for this.
- nobu: (1..) seems visually odd.
- akr: for now, use step and drop.

## \[Feature [#12933](https://bugs.ruby-lang.org/issues/12933)\] Add Some and Optional (shyouhei)


- nobu: start as a gem.
- matz: agree.

## \[Feature [#12931](https://bugs.ruby-lang.org/issues/12931)\] Add support for Binding#instance\_eval (shyouhei)


- shyouhei: what is needed ultimately is to pass a block to Binding#eval
- ko1: I don’t like it.  We have to reconstruct everything if we do this.
- nobu: I think it’s not faster, if not impossible.
- nobu will reject.

## \[Feature [#12929](https://bugs.ruby-lang.org/issues/12929)\] ternary should look ahead w/in a block (and not care about newlines) (shyouhei)


- naruse: perl people tends to write it.
- nobu: it’s possible if parens are there, but I don’t want to look ahead.
- shyouhei: Martin says we already look-ahead for periods.
- matz: Python ignores newlines inside of parens.

## \[Misc [#12935](https://bugs.ruby-lang.org/issues/12935)\] Webrick: Update HTTP Status codes, share them (shyouhei)


- nobu: is it Webrick?
- akr: Webrick has this code but no one wants to require the whole.
- akira: nobody looks at anywhere but Rack.

## \[Feature [#12962](https://bugs.ruby-lang.org/issues/12962)\] Feature Proposal: Extend 'protected' to support module friendship (shyouhei)


- akr: use of protected is discouraged.
- hsbt: deprecate protected?
- akira: possible now. Rails now deletes most use of protected.
- naruse: I think it’s RuboCop’s duty to warn such thing.
- akr: it’s a good thing anyway to update rdoc to discourage usages.

## \[Feature [#12957](https://bugs.ruby-lang.org/issues/12957)\] A more OO way to create lambda Procs (shyouhei)


- akr: I don’t understand the OP’s intension.
- shyouhei: I think they want a subclass of lambda.  that’s not possible today.
- akr: it is possible. complicated though. ->  commented.

## \[Feature [#12966](https://bugs.ruby-lang.org/issues/12966)\] net/ftp to include fxp support? (shyouhei) who's going to handle this?


- shugo is handling.

## \[Feature [#12967](https://bugs.ruby-lang.org/issues/12967)\] Add a default for RUBY\_GC\_HEAP\_GROWTH\_MAX\_SLOTS out-of-the-box (shyouhei)


- ko1: I’m not sure if this parameter is a sane default.
- ko1: also, I think there should be a comprehensive approach to parameterize GC, not individually like this.

## \[Feature [#12969](https://bugs.ruby-lang.org/issues/12969)\] Allow optional parameter in String#strip and related (shyouhei)


- naruse: I think regexp is the best for this purpose.
- naruse: I don’t like the tr-like argument string.

## \[Feature [#13016](https://bugs.ruby-lang.org/issues/13016)\] String#gsub(hash) (shyouhei)


- akr: what’s wrong with union?
- shyouhei: that’s not what I want.
- shyouhei: All what I want to do is to table lookup. Not regular expression.
- akr: performance concern.
- shyouhei: understand that point.
- naruse: you have to sort by length before union. Otherwise カ can match before ガ.

## \[Bug [#8241](https://bugs.ruby-lang.org/issues/8241)\] If uri host-part has underscore ( '\_' ), 'URI#parse' raise 'URI::InvalidURIError' (shyouhei)


- naruse: I think this is done.

## \[Bug [#8826](https://bugs.ruby-lang.org/issues/8826)\] BigDecimal#div and #quo different behavior and inconsistencies (shyouhei) status?>mrkn


- mkrn: I'll start to fix it as soon as possible.

## \[Feature [#13048](https://bugs.ruby-lang.org/issues/13048)\] Better way to do Regexp.new(Regexp.escape("some string")) (shyouhei)


- naruse: quick look at the github result seems every usage of Regexp.new(Regexp.escape()) are bad.
- akr: Regexp.exact() ?

## \[Bug [#13111](https://bugs.ruby-lang.org/issues/13111)\] Degraded performance for delegated methods through Forwardable module (shyouhei) assign who?


- nobu: I did this already I think.

## \[Bug [#13098](https://bugs.ruby-lang.org/issues/13098)\] miniruby fails with SEGV on NetBSD/powerpc (shyouhei) look at the backtarce (shyouhei)


- nobu: will take a look.

## \[Bug [#13125](https://bugs.ruby-lang.org/issues/13125)\] MRI has too much Qtrue : Qfalse; (shyouhei)


- nobu: nobody is against the needs of macro but the name?
- shyouhei: will update the ticket.

## \[Bug [#13127](https://bugs.ruby-lang.org/issues/13127)\] DRb \`load': connection closed (DRb::DRbConnError) when client exit's from within a loop iterating over remote objects (shyouhei)


- assign seki

## \[Feature [#13067](https://bugs.ruby-lang.org/issues/13067)\] TrueClass,FalseClass to provide \=== to match truthy/falsy values. (shyouhei)


- matz: I give up.

## \[Bug [#13064](https://bugs.ruby-lang.org/issues/13064)\] Inconsistent behavior with next inside begin/end across different implementations. (shyouhei) spec or...?


- ko1: it’s 1 to me.
- matz: fail early is good but is it a syntax error?
- ko1:  it is.  Because you can’t place next here statically.
- akr: should the JIS/ISO specify as such?

## \[Bug [#13104](https://bugs.ruby-lang.org/issues/13104)\] math.rb affects Rational literals (nobu)


- matz: it’s ugly, but mathn is ugly by nature.

## \[Bug [#9569](https://bugs.ruby-lang.org/issues/9569)\] SecureRandom should try /dev/urandom first (shyouhei) I now think it's OK


- shyouhei: I will

- rename Random.raw\_seed to Random.urandom
- try to use that from SecureRandom
- if it fails, fallback to OpenSSL.

- \[Bug #13135\] Regexp.last\_match returns nil with s.rindex(//)

- everyone: isn’t it a bug?
- ko1: assign shugo.
- akr: it seems to be an optimization.
- akr: regular expression should set $~.  Optimization should be done against empty string.
