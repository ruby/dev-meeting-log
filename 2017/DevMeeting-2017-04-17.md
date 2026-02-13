---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-04-17

Date: 2017/04/17 (Mon)
Time: 14:00- 19:00 (JST)
Place: Cookpad Inc. Headquarter
Sign-up: https://ruby.connpass.com/event/53124/
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

* [Bug #13257] Symbol#respond_to?(:singleton_class) should be false (naruse)
* [Misc #13163] Uncaught exceptions may not be reported when Thread#report_on_exception=true and Thread#abort_on_exception=true (naruse)
* [Bug #13181] Unexpected line in rescue backtrace (shyouhei) what's this?
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
* [Feature #13265] TracePoint for basic operation redefinition (shyouhei)
* [Bug #12834] `prepend` getting prepended even if it already exists in the ancestors chain (shyouhei)
* [Bug #7976] TracePoint call is at call point, not call site (shyouhei)
* [Feature #12901] Anonymous functions without scope lookup overhead (shyouhei) status?
* [Feature #12573] Introduce a straightforward way to discover whether a process is running (shyouhei)
* [Feature #13263] Add companion integer nth-root method to recent Integer#isqrt (shyouhei)
* [Feature #9453] Return symbols of defined methods for `attr` and friends (shyouhei) look at the last comment
* [Feature #13302] Provide a (force) --enable-openssl switch for ruby ./configure (or similar) (shyouhei)
* [Feature #13303] String#any? as !String#empty? (shyouhei)

## From attendees


- [Feature #13334] Removed deprecated mathn extentions. (hsbt)
- Assign who? section (shyouhei)
    - [Bug #13228] s[i]=c(assigning a character) for String is slower than Array on Linux
    - [Bug #13264] Binding#irb does not work in context of frozen object
    - [Bug #12642] Net::HTTP populates host header incorrectly when using an IPv6 Address
    - [Bug #13196] Improve keyword argument errors when non-keyword arguments given
    - [Bug #13320] rescue blocks get an entry in backtrace locations
    - [Bug #13324] IRB Segmentation Fault from eval infinite loop
    - [Bug #13330] Array.include? is slow for symbols
    - [Bug #13336] Default Parameters don't work
    - [Bug #13337] Eval and Later Defined Local Variables
    - [Bug #13338] MinGW SEGV in test/ruby/test_keyword.rb svn 58034, ok in svn 58021.
    - [Bug #13390] MinGW build test-all SEGV, issue in test framework or error recovery?
    - [Bug #13350] File.read :newline option not respected on Linux
    - [Feature #13362] [PATCH] socket: avoid fcntl for read/write_nonblock on Linux
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
    - [Bug #13319] GC issues seen with GCC7
    - [Bug #13404] Hash#any? yields arguments to lambdas with proc semantics
    - [Bug #13406] URI.parse
    - [Bug #13413] --with-static-linked-ext doesn't install extension files on `make install`
    - [Bug #13420] Integer#{round,floor,ceil,truncate} should always return an integer, not a float
    - [Bug #13429] Net::SMTP has no read timeout when connexion over TLS
- [Feature #13309] Locale paramter for Integer(), Float(), Rational() (shyouhei)
- [Feature #12733] Bundle bundler to ruby core (shyouhei) updates?
- [Feature #11302] Dir.entries and Dir.foreach without [".", ".."] (shyouhei)
- [Feature #13332] Forwardable#def_instance_delegator nil (shyouhei)
- [Feature #13334] Removed deprecated mathn extentions. (shyouhei)
- [Bug #12921] Retrieve user and password for proxy from env (shyouhei)
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

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

* [Feature #12886] URI#merge doesn't handle paths correctly (hsbt)

# Log

## Date: 2017/04/17 (Mon)

## Time: 14:00- 19:00 (JST)

## Place: Cookpad Inc. Headquarter

## Sign-up: [https://ruby.connpass.com/event/53124/](https://www.google.com/url?q=https://ruby.connpass.com/event/53124/&sa=D&source=editors&ust=1686087122714817&usg=AOvVaw3a19jCb1ija_qokyu5Q5Tj)

## log edit: [https://docs.google.com/document/d/1\_tWfg7\_t-JRXgGlMaOGnpyipGP7P-lKQ18NtciWhKIY/edit](https://www.google.com/url?q=https://docs.google.com/document/d/1_tWfg7_t-JRXgGlMaOGnpyipGP7P-lKQ18NtciWhKIY/edit&sa=D&source=editors&ust=1686087122715256&usg=AOvVaw3HWhoiEjigR-s344HlZvby)

## log: TBD

## Next meeting

- 5/19

- at speee, roppongi

## About 2.5 timeframe

## [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25&sa=D&source=editors&ust=1686087122716162&usg=AOvVaw1NHej4Qvc-yFM-fEqLMqsg)

- Previrew 1 in June?
- 合宿

- Nov-Dec?
- Shimane? or Kanto?

## Current status of Ruby 2.4

- 2.4.1 released.
- Chikanaga-san is handling.

## Carry-over from previous meeting(s)

- \[Bug [#13257](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13257&sa=D&source=editors&ust=1686087122717130&usg=AOvVaw3Yl_NmZhZZ9FG6_giUNPjB)\] Symbol#respond\_to?(:singleton\_class) should be false (naruse)

- akr: I feel it’s OK to undef them.
- matz: I doubt to check behaviours using respond\_to?
- nalsh: class << obj; creates a singleton class if not.  This is not good.
- nobu: It seems what OP wants is actually a list of extended modules, which is not currently obtainable.

- \[Misc [#13163](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13163&sa=D&source=editors&ust=1686087122717673&usg=AOvVaw1IZ57-EVL30reHOPM1MTzw)\] Uncaught exceptions may not be reported when Thread#report\_on\_exception=true and Thread#abort\_on\_exception=true (naruse)

- nobu: OK to me.

- \[Bug [#13181](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13181&sa=D&source=editors&ust=1686087122718015&usg=AOvVaw0u5gK9S1SaXGDkoKUb0XOb)\] Unexpected line in rescue backtrace (shyouhei) what's this?

- assign ko1

- \[Feature [#8263](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8263&sa=D&source=editors&ust=1686087122718367&usg=AOvVaw3CI5ixgYjvqJ1jWi2mFqYW)\] Support discovering yield state of individual Fibers (shyouhei)

- assign ko1

- \[Feature [#8576](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8576&sa=D&source=editors&ust=1686087122718747&usg=AOvVaw2ZpGuPGQVRusMrUFsJ0m6b)\] Add optimized method type for constant value methods (shyouhei)

- assign ko1

- \[Bug [#13188](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13188&sa=D&source=editors&ust=1686087122719067&usg=AOvVaw0nCRireWQg6PBH9B5SGEor)\] Reinitialize Ruby VM. (shyouhei) New C API ...?

- assign ko1

- \[Feature [#2740](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/2740&sa=D&source=editors&ust=1686087122719374&usg=AOvVaw1XLgx4NhP2WYFLWsw0uSZP)\] Extend const\_missing to pass in the nesting (shyouhei)

- matz: does this still work?
- matz: also, I don’t like autoloading in general.

- \[Feature [#13211](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13211&sa=D&source=editors&ust=1686087122719754&usg=AOvVaw1dp4lOPhhTfEuXSjSw4dCr)\] Hash#delete taking a splat (shyouhei)

- mrkn: I once wanted this too
- akr: return value? array for arity >= 2?
- nobu: I wonder if this is already there in ActiveSupport
- mrkn: That’s Hash#except!
- shyouhei: seems the OP wants to split a hash into two.
- mrkn: That’s Hash#extract!
- akr: seems what is requested is not a good design, compared to what is wanted.

- \[Bug [#11567](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11567&sa=D&source=editors&ust=1686087122720399&usg=AOvVaw2Wpy7Pj_d1s-lFn7srmp8t)\] Segmentation fault CFUNC :gets (shyouhei)

- nobu: it seems race between read and close
- shyouhei: seems fixed.

- \[Feature [#13172](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13172&sa=D&source=editors&ust=1686087122720851&usg=AOvVaw13_IQxwS5ux-FtfsZFvV49)\] Method that yields object to block and returns result (shyouhei) new name candidate

- shyouhei: #pass is proposed.
- matz: It doesn’t sound nice.

- \[Feature [#13224](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13224&sa=D&source=editors&ust=1686087122721227&usg=AOvVaw1VN_BA5tsjD_NfcypGNzwv)\] Add FrozenError as a subclass of RuntimeError (shyouhei)

- matz: I don’t want to have infinite number of exceptions but this seems OK.

- \[Misc [#13230](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13230&sa=D&source=editors&ust=1686087122721526&usg=AOvVaw0iiLv4H6E-YYemH6dbKDG0)\] Better Do ... while structure (shyouhei)

- shyouhei: I don’t understand this.
- matz: reject

- \[Feature [#13245](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13245&sa=D&source=editors&ust=1686087122721872&usg=AOvVaw3l7GE0SIGs4jPRTAlmPx2X)\] \[PATCH\] reject inter-thread TLS modification (shyouhei)

- ko1: we would like to forbid this in a future.
- shyouhei: what about adding warning.
- akr: is this need?  We have no problem right now.  Is this worth introducing backwards incompatibility?

- \[Feature [#13252](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13252&sa=D&source=editors&ust=1686087122722338&usg=AOvVaw0pu8e0KMmwJ6RTgwRoGkKs)\] C API for creating strings without copying (shyouhei)

- ko1: the proposal is not doable
- nobu: maybe we need to have separate deallocator
- assign ko1

- \[Bug [#13249](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13249&sa=D&source=editors&ust=1686087122722755&usg=AOvVaw003p3ZYWUzscqo_a1oyODI)\] Access modifiers don't have an effect inside class methods in Ruby >= 2.3 (shyouhei) design issue or bug?

- naruse: wasn’t this intentional?
- ko1: no idea.
- matz: It’s OK to me to deprecate this, if we can warn this.

- \[Feature [#13265](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13265&sa=D&source=editors&ust=1686087122723252&usg=AOvVaw0Lslt4sN1A9Hudnyo2yUzm)\] TracePoint for basic operation redefinition (shyouhei)

- assign ko1

- \[Bug [#12834](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12834&sa=D&source=editors&ust=1686087122723543&usg=AOvVaw36zBIrc7vQR1uqY3j9M3OV)\] prepend getting prepended even if it already exists in the ancestors chain (shyouhei)

- akr: doesn’t this loop forever?
- ko1: no. I fixed that loop.
- matz: I wanted to change include’s behaviour in 1.9 but not successful then.
- matz: I started thinking prepend shall be applied many times. For include..?

- \[Bug [#7976](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7976&sa=D&source=editors&ust=1686087122724088&usg=AOvVaw2p-KtWbppMpYV_kPxrkhEP)\] TracePoint call is at call point, not call site (shyouhei)

- ko1: わかる
- assign ko1

- \[Feature [#12901](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12901&sa=D&source=editors&ust=1686087122724435&usg=AOvVaw2B47DTJgvTqndFMevZAOMW)\] Anonymous functions without scope lookup overhead (shyouhei) status?

- pass this time.

- \[Feature [#12573](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12573&sa=D&source=editors&ust=1686087122724726&usg=AOvVaw2FRbUZ8LDKn090Oj2eN_Qi)\] Introduce a straightforward way to discover whether a process is running (shyouhei)

- akr: locked pidfile does not need this method.

- \[Feature [#13263](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13263&sa=D&source=editors&ust=1686087122725023&usg=AOvVaw0hrAzvMDvkye9S6_wHonE3)\] Add companion integer nth-root method to recent Integer#isqrt (shyouhei)

- shyouhei: I have no idea when this is used.
- akr: same as above.
- mrkn: what about asking the OP?

- \[Feature [#9453](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9453&sa=D&source=editors&ust=1686087122725413&usg=AOvVaw0x9NWsSDTLaeLvtHEG4qO8)\] Return symbols of defined methods for attr and friends (shyouhei) look at the last comment

- shyouhei: the last comment says the situation changed.
- akr: that sounds reasonable…
- matz: but definition of multiple methods at once is something special here.
- matz: will respond.

- \[Feature [#13302](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13302&sa=D&source=editors&ust=1686087122725940&usg=AOvVaw2seQ-DVeSjCGyST5deqfor)\] Provide a (force) --enable-openssl switch for ruby ./configure (or similar) (shyouhei)

- shyouhei: openssl needs miniruby and doesn’t stop if absent.
- naruse: --enable-openssl sounds strange, it should be --with-openssl
- nobu: what about let --with-ext=openssl to abort if absent.

- \[Feature [#13303](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13303&sa=D&source=editors&ust=1686087122726359&usg=AOvVaw1U0mFhxOZCRQvorvRkWLq_)\] String#any? as !String#empty? (shyouhei)

- matz: I don’t like any? because Enumerable#any? cannot be applied to String straight-forward.
- matz: str.not\_empty? seems inferior than !str.empty?
- naruse: !str.empty? does not work for nil.  I want to do str\_or\_nil&.not\_empty?
- name candidates:

- NG: present
- NG: empty?
- candicate: non\_empty?
- candicate: not\_empty?

## From attendees

- \[Feature [#13334](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13334&sa=D&source=editors&ust=1686087122727138&usg=AOvVaw3-81xF28oFa1wyObik1N--)\] Removed deprecated mathn extentions. (hsbt)

- matz: OK.

- Assign who? section (shyouhei)

- \[Bug [#13228](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13228&sa=D&source=editors&ust=1686087122727516&usg=AOvVaw3OrGcAYZ0djovpk7gdSTEU)\] s\[i\]=c(assigning a character) for String is slower than Array on Linux

- shyouhei: that search\_nonascii is unavoidable
- naruse: 5x slower on my machine
- nobu: no difference on my macine
- shyouhei: what’s the cause?

- \[Bug [#13264](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13264&sa=D&source=editors&ust=1686087122728004&usg=AOvVaw1GUEhDi9aC9VySoQM2DfU7)\] Binding#irb does not work in context of frozen object
- \[Bug [#12642](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12642&sa=D&source=editors&ust=1686087122728243&usg=AOvVaw2henhyrMNVVFzpaQaO3fYj)\] Net::HTTP populates host header incorrectly when using an IPv6 Address
- \[Bug [#13196](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13196&sa=D&source=editors&ust=1686087122728472&usg=AOvVaw3gQOrI1i15WYSdG17rNx0U)\] Improve keyword argument errors when non-keyword arguments given
- \[Bug [#13320](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13320&sa=D&source=editors&ust=1686087122728702&usg=AOvVaw3uGH5geBofyKPtxaeWwwcB)\] rescue blocks get an entry in backtrace locations
- \[Bug [#13324](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13324&sa=D&source=editors&ust=1686087122728936&usg=AOvVaw0KuqeaDG90KRvRMkGIsLPK)\] IRB Segmentation Fault from eval infinite loop
- \[Bug [#13330](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13330&sa=D&source=editors&ust=1686087122729161&usg=AOvVaw3t-3l5uoG_aVGKuBzd6ZJ3)\] Array.include? is slow for symbols
- \[Bug [#13336](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13336&sa=D&source=editors&ust=1686087122729380&usg=AOvVaw2vWsjbotLVHIrIf03NcnWc)\] Default Parameters don't work
- \[Bug [#13337](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13337&sa=D&source=editors&ust=1686087122729600&usg=AOvVaw022wLG4nTHcFP5bzHwT4BE)\] Eval and Later Defined Local Variables
- \[Bug [#13338](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13338&sa=D&source=editors&ust=1686087122729825&usg=AOvVaw2Jz6C_XVAV2dEkbJWWLYUK)\] MinGW SEGV in test/ruby/test\_keyword.rb svn 58034, ok in svn 58021.
- \[Bug [#13390](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13390&sa=D&source=editors&ust=1686087122730101&usg=AOvVaw3L0KetkjBjV389m7xBb63e)\] MinGW build test-all SEGV, issue in test framework or error recovery?
- \[Bug [#13350](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13350&sa=D&source=editors&ust=1686087122730360&usg=AOvVaw3GWGiD7UMBP_qBgAQ81YHp)\] File.read :newline option not respected on Linux
- \[Feature [#13362](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13362&sa=D&source=editors&ust=1686087122730596&usg=AOvVaw2y5VyOtAy1FEoT9XuuD6LU)\] \[PATCH\] socket: avoid fcntl for read/write\_nonblock on Linux
- \[Bug [#13351](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13351&sa=D&source=editors&ust=1686087122730894&usg=AOvVaw1agye7Y672JRS_iNelwxjv)\] net/http: Net::HTTP.start sets wrong default arg value
- watson's

- \[Bug [#13341](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13341&sa=D&source=editors&ust=1686087122731174&usg=AOvVaw3vJ6t3wO8Q2y04WuX3aREV)\] Improve performance of implicit type conversion
- \[Bug [#13342](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13342&sa=D&source=editors&ust=1686087122731399&usg=AOvVaw27ukCh-R931VFwE2UkaR4v)\] Improve yielding block performance
- \[Bug [#13354](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13354&sa=D&source=editors&ust=1686087122731628&usg=AOvVaw0JRmZ6VawTuwrVyH7rtVKd)\] Improve Time#<=> performance
- \[Bug [#13357](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13357&sa=D&source=editors&ust=1686087122731856&usg=AOvVaw0MLZ4oAZSFGuaay_8Ng72V)\] Improve Time#+ & Time#- performance
- \[Bug [#13365](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13365&sa=D&source=editors&ust=1686087122732081&usg=AOvVaw3Sdh0751gPOaztmvprXucs)\] Improve performance of rb\_equal() with special constants
- \[Bug [#13368](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13368&sa=D&source=editors&ust=1686087122732296&usg=AOvVaw3EMl45Dx7j_0iSH8a1cPVJ)\] Improve performance of Array#sum with float elements
- \[Bug [#13374](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13374&sa=D&source=editors&ust=1686087122732523&usg=AOvVaw0Bzq-BKS1sGumzB90PYZ47)\] Fix one of performance regressions in method calling
- nobu: I call him for a volunteer.

- \[Bug [#13373](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13373&sa=D&source=editors&ust=1686087122732863&usg=AOvVaw1Zzt66ekSUc5p5MhnuLsgH)\] FileUtils methods for copy, move and remove directories is not providing a decent error trace for letting know if it was success or fail
- \[Bug [#13101](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13101&sa=D&source=editors&ust=1686087122733155&usg=AOvVaw0S6WzfImSZKHBDIk6T71v1)\] Date#rfc2822 and Time#rfc2822 don't return the same format
- \[Bug [#13312](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13312&sa=D&source=editors&ust=1686087122733397&usg=AOvVaw0NvGAiVJTkuITHACuYV474)\] String#casecmp raises TypeError instead of returning nil
- \[Bug [#12684](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12684&sa=D&source=editors&ust=1686087122733647&usg=AOvVaw1B71EWK5X0wYFW0kJZ5h-Y)\] Delegator#eql? missing
- \[Feature [#13389](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13389&sa=D&source=editors&ust=1686087122733885&usg=AOvVaw3qhJYmeXdT91A7QSw523Aq)\] \[PATCH\] POP3 support timeout for TLS handshake
- \[Bug [#13319](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13319&sa=D&source=editors&ust=1686087122734124&usg=AOvVaw3Qk_84H8GKog-iocFGXCUq)\] GC issues seen with GCC7
- \[Bug [#13404](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13404&sa=D&source=editors&ust=1686087122734368&usg=AOvVaw1Fj8gboJGtZqr96R1tuN7W)\] Hash#any? yields arguments to lambdas with proc semantics
- \[Bug [#13406](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13406&sa=D&source=editors&ust=1686087122734594&usg=AOvVaw2etzx_RXQvnPD950Wa6FJW)\] URI.parse
- \[Bug [#13413](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13413&sa=D&source=editors&ust=1686087122734818&usg=AOvVaw10EVTXSJqb3Cfjp3eNXRNm)\] --with-static-linked-ext doesn't install extension files on make install
- \[Bug [#13420](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13420&sa=D&source=editors&ust=1686087122735076&usg=AOvVaw0tcHdRALnLpYGaPzvootlR)\] Integer#{round,floor,ceil,truncate} should always return an integer, not a float
- \[Bug [#13429](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13429&sa=D&source=editors&ust=1686087122735308&usg=AOvVaw1BBOXe0clUmoEpBomguCe2)\] Net::SMTP has no read timeout when connexion over TLS

- \[Feature [#13309](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13309&sa=D&source=editors&ust=1686087122735562&usg=AOvVaw1rGW67kwupctHy1kyijQfa)\] Locale paramter for Integer(), Float(), Rational() (shyouhei)
- \[Feature [#12733](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12733&sa=D&source=editors&ust=1686087122735890&usg=AOvVaw2tobSod-IAIf2rr87LCe_J)\] Bundle bundler to ruby core (shyouhei) updates?
- \[Feature [#11302](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11302&sa=D&source=editors&ust=1686087122736152&usg=AOvVaw02HbM97bM7N9nUbBdiJY0s)\] Dir.entries and Dir.foreach without [".", ".."](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/shyouhei&sa=D&source=editors&ust=1686087122736354&usg=AOvVaw3-W3en2lWsHTPSDYFndQxH)
- \[Feature [#13332](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13332&sa=D&source=editors&ust=1686087122736579&usg=AOvVaw0pe0UheK0Fwp9xHdqLXO7f)\] Forwardable#def\_instance\_delegator nil (shyouhei)
- \[Feature [#13334](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13334&sa=D&source=editors&ust=1686087122736821&usg=AOvVaw00taDTe9RfdTfOrVwdTMNk)\] Removed deprecated mathn extentions. (shyouhei)
- \[Bug [#12921](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12921&sa=D&source=editors&ust=1686087122737071&usg=AOvVaw3d94DzH7HwfCeq06BwXy8M)\] Retrieve user and password for proxy from env (shyouhei)

- akr: what about whitelisting OSes? modern ones should work.

- \[Feature [#13333](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13333&sa=D&source=editors&ust=1686087122737363&usg=AOvVaw0RO799t-dpJ57QUR8uv1a8)\] block to yield (shyouhei)
- \[Feature [#13378](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13378&sa=D&source=editors&ust=1686087122737613&usg=AOvVaw0VLNl2HUo3YRd71ELvsvcS)\] Eliminate 4 of 8 syscalls when requiring file by absolute path (shyouhei)
- \[Feature [#13381](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13381&sa=D&source=editors&ust=1686087122737914&usg=AOvVaw0ZzPuneHgL-5dhXt4Y63dA)\] \[PATCH\] Expose rb\_fstring and its family to C extensions (shyouhei)
- \[Bug [#13315](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13315&sa=D&source=editors&ust=1686087122738143&usg=AOvVaw20FjCYC07kPJH1fh2oviWj)\] Single "%" at the end of printf format string appears in the result (shyouhei)
- \[Feature [#13385](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13385&sa=D&source=editors&ust=1686087122738482&usg=AOvVaw1i69spS9hplpKcgcoRqaQh)\] \[PATCH\] Make Resolv::DNS::Name validation similar to host and dig commands (shyouhei)
- \[Feature [#13383](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13383&sa=D&source=editors&ust=1686087122738775&usg=AOvVaw169MO9VP2ms4nQ0xDwde3H)\] \[PATCH\] Module#source\_location (shyouhei)
- \[Feature [#12063](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12063&sa=D&source=editors&ust=1686087122739055&usg=AOvVaw2WROCb0I75VVt8B1BnCm8J)\] KeyError#receiver and KeyError#name (shyouhei) status?
- \[Bug [#13397](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13397&sa=D&source=editors&ust=1686087122739290&usg=AOvVaw09O6cDZV4CRPYn0UnilTVq)\] #object\_id should not be signed (shyouhei)
- \[Feature [#13396](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13396&sa=D&source=editors&ust=1686087122739524&usg=AOvVaw0BHIX0J88T-fi08ousH9Fa)\] Net::HTTP has no write timeout (shyouhei)
- \[Bug [#13407](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13407&sa=D&source=editors&ust=1686087122739763&usg=AOvVaw2aEUIT4URByDcPOJdGnrxe)\] We have recv\_nonblock but not send\_nonblock... can we add it? (shyouhei)
- \[Feature [#13395](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13395&sa=D&source=editors&ust=1686087122740027&usg=AOvVaw23iF5bw-l5ysjWDsR9DhFc)\] Add a method to check for not nil (shyouhei)

## From non-attendees

- \[Feature [#12886](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12886&sa=D&source=editors&ust=1686087122740552&usg=AOvVaw32R8CY3zlSZzr_NRQLzNHP)\] URI#merge doesn't handle paths correctly (hsbt)

- hsbt: cf [https://github.com/ruby/ruby/pull/1469](https://www.google.com/url?q=https://github.com/ruby/ruby/pull/1469&sa=D&source=editors&ust=1686087122740944&usg=AOvVaw1P7511zXU7IWMhMorJbG0r)
- hsbt: the fix seems partial. status?
- nobu: the fix did not change behaviours.
- hsbt: what about this issue then?
- akr: martin’s comment #3 seems reasonable.
