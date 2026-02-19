---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-03-16

Date: 2016/03/16 (Wed)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.4 based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is very busy person, so we can ask him directly on this opportunity. If you can not attend there, then other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All of activities are best-effort (please remind that most of us are volunteer developers).
  * The date, time and place is when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name. Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## From Attendees

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #10098] Timing-safe string comparison for `OpenSSL::HMAC` (naruse)
* [Bug #12139] return `OpenSSL::Random.random_bytes(n)` call takes to long. `OpenSSL::` bug on windows. (naruse)
* [Feature #12142] `Hash` tables with open addressing (in particular, question about 32/64-bit indices) (duerst)
* [Feature #12125] Proposal: Shorthand operator for `Object#method` (hsbt)
* Onigmo maintenance (duerst)
* upper/lower case conversion (duerst)
* Gem search (duerst)

## From non-attendees

* [Feature #12026] Support warning filters (jeremyevans0)
* [Feature #12092] Allow `Object#clone` to yield cloned object before freezing (jeremyevans0)
* [Feature #12172] `Array#max` and `Array#min` (mame)
* [Feature #9969] Add `File.empty?` as alias to `File.zero?` (shyouhei)
* [Bug #12121] 異なる名前空間にある同名の定数により `Module.constans` の結果の並びが変わる (shyouhei)
* [Bug #12112] `Resolv.getname` with IPv6 noop (shyouhei)
* [Bug #12162] `OpenSSL::PKCS7` seems to create broken objects (nested asn.1 error) (shyouhei)
* [Bug #12091] Freezing a `SortedSet` breaks `Enumerable` (shyouhei)
* [Bug #12126] [PATCH] openssl: accept moving write buffer for `write_nonblock` (shyouhei)
* [Feature #12039] `Fixnum#infinite?`/`Bignum#infinite?` or `Numeric#infinte?`, consistent with `Float#infinite?` and `BigDecimal#infinite?` (shyouhei)
* [Feature #10617] Change multiple assignment in conditional from parse error to warning (shyouhei)
* [Feature #12094] parameterized property assignment: `o.prop(arg) = 1` (shyouhei)
* [Feature #12096] New notation for instance variables and class variables (shyouhei)
* [Bug #12104] Procs keyword arguments affect value of previous argument (shyouhei)
* [Bug #12106] Behavior of double splatting of hashes with non symbol key is different according to splatted hash position (shyouhei)
* [Feature #11997] A method to read a file with interpolations (shyouhei)
* [Feature #12116] `Fixnum#divmod`, `Bignum#divmod` with multiple arguments (shyouhei)
* [Feature #12119] `next_prime` for lib/prime.rb (shyouhei)
* [Feature #12134] Comparison between `true` and `false` (shyouhei)
* [Feature #11547] remove top-level constant lookup (shyouhei)
* [Feature #12005] Unify `Fixnum` and `Bignum` into `Integer` (shyouhei)
* [Bug #11844] Please update unicode-licensed files (license issue) (shyouhei)
* [Bug #11816] Partial safe navigation operator (shyouhei)
* [Misc #12124] Use Automake (shyouhei)
* [Feature #12020] Documenting Ruby memory model (shyouhei)


(Additional explanation is welcome because we can't ask about it immediately)

# Log

Date: 2016/03/16 14:00- JST

Attendees: Matz, ko1, nobu, akr, naruse, mrkn, shyouhei, sorah, martin, hsbt

## Next meeting

2016/04/13 13:00 @ Money Forward

## \[[#10098](https://bugs.ruby-lang.org/issues/10098)\] Timing-safe string comparison for OpenSSL::HMAC (naruse)

- (n0kada) name not fixed.
- Rack implements this in pure-ruby

- but sounds not a wise idea to do this in Ruby.

- It might be a good idea to use libc-provided one, if any.
- Should this be a String’s method?
- Why not gem?

- This kind of things are difficult to properly implement.  Worth providing by default.

- Does OpenSSL has this feature?

- Yes.  There is CRYPTO\_memcmp.
- Why not just export this to ruby?

- naruse: How about OpenSSL.foo\_bar() ?

- OpenSSL.memcmp() ?

- akr: Does it consider encoding?

- Naruse: Shouldn’t. It’ll be useful if it compares each other as a binary.

- OpenSSL.memcmp(str1, str2)

- The name means

- “OpenSSL”: secure (timing safe)
- “memcmp: binary comparison

- Return value?

- true/false.  -1/0/1 makes almost no sense for us.

## [[Feature #12125]](https://bugs.ruby-lang.org/issues/12125) Proposal: Shorthand operator for Object#method (hsbt)

- (Matz) I want this. But all proposed syntax so far don’t feel right.

- Proposed: \-> .> .<...> .:

- Approaches:

- Shorthand for making Procs
- Shorthand for Object#method

- Matz: #method is a normal method that can be overridden. This kind of feature should be syntactic.
- Still waiting for a new syntax.

## Onigmo maintenance (duerst)

- (Dürst) Who is the maintainer of onigumo?
- K-takata san (not that active though)
- (Naruse) Onigmo is on github, should send PR as usual when needed.

github.com/k-takata/Onigmo

## upper/lower case conversion (duerst)

- (Dürst) under development.  Works most part.

- Want to make UTF-8 conversion by default, and explicit parameter passing for ASCII-conversion.

- Titlecase and String#swapcase

- In unicode there is a special case called titlecase cf [http://unicode.org/faq/casemap\_charprop.html#4](http://unicode.org/faq/casemap_charprop.html#4)
- How should swapcase behave?
- Or, how should swapcase behave in general?
- Other languages:

- Perl uses tr
- Python has swapcase.
- Also javascript

- Matz: maybe it’s from Python.

## [[Bug #11844]](https://bugs.ruby-lang.org/issues/11844) Please update unicode-licensed files (license issue) (shyouhei)

- This table was from NetBSD.
- The table is a data, not an “expression of author” that a copyright should apply.
- Is unicode’s license GPL compatible?
- [http://www.unicode.org/Public/MAPPINGS/OBSOLETE/EASTASIA/JIS/JIS0212.TXT](http://www.unicode.org/Public/MAPPINGS/OBSOLETE/EASTASIA/JIS/JIS0212.TXT)
- It seems unicode.org has version 2 of those files.
- Lets update our copy to the latest ones.

## [[Bug #11816]](https://bugs.ruby-lang.org/issues/11816) Partial safe navigation operator (shyouhei)

- (shyouhei) current status?

- No concrete answer

- (n0kada) there can be a flaw for e.g. binary operator, foo&.bar + 1 ?

- (a&.b.c.d.e...).foo -> foo should be called always.
- So it is safer than the opposite.
- Edge case : a&.b.c += i

- Other languages:

- groovy : no chain
- Coffee : it does
- Swift : it does
- C# : it does

## [[Feature #12142]](https://bugs.ruby-lang.org/issues/12142) Hash tables with open addressing (in particular, question about 32/64-bit indices) (duerst)

- 4G entry hash is rare, but not an unrealistic thing in long term.
- It is definitely a good thing to provide a fast alternative for 99% use case, however.
- Any ideas?

- 32bit by default?

- Is 4G entry hash a real thing today?  Can we wait for this a few more years?
- 4G entry requires 6word \* 4G = 100GB

- Compile-time switch using configure?

- Not a wise idea because a user cannot distinguish which version of ruby they are running.

- Switch 16bit->32bit->64bit smoothly?

## [[Feature #12020]](https://bugs.ruby-lang.org/issues/12020) Documenting Ruby memory model (shyouhei)

- Koichi is going to dump his opinion to reply the thread.
- (Matz) I don’t want to bother such in-detail memory

## [[Bug #12039]](https://bugs.ruby-lang.org/issues/12039) Fixnum#infinite?/Bignum#infinite or Numeric#infinte, consistent with Float#infinite? and BigDecimal#infinite? (shyouhei)

- For about JSON, #finite? (not #infinite?) is much better because JSON also has to reject NaNs.
- (Matz) positive.  Float <-> Integer polymorphism is a good thing.
- Add both #finite? and #infinite?

## [[Feature #12005]](https://bugs.ruby-lang.org/issues/12005) Unify Fixnum and Bignum into Integer (shyouhei)

- Nobody is negative.
- Mrkn is assigned for this
- (n0kada) coerce can be a problem.
- (usa): Is the internal implementation changed?

1. Current Fixnum and Bignum C implementation move under Integer

- (usa): Will C API be changed?

1. Expected to keep compatibility as far as we can.
2. For example rb\_fix\_hoge, and LONG2FIX.

- Proposed migration process

1. Move every method from Fixnum/Bignum to Integer
    This solves Fixnum/Bignum rdoc duplication problem.
2. Delete bignum; ::Bignum = ::Integer

## Gem search (duerst)

- Gem codesearch spec
- Codesearch is done using rubygems-mirror
- 500GB storage, memory unknown

## \[ruby-core:74355\] Ruby+OMR: how should we proceed?

- Thanks a lot for contacting us.
- Normal pull-request is the ideal way.
- We have to look at actual code to judge details.

## [[Feature #12026]](https://bugs.ruby-lang.org/issues/12026) Support warning filters (jeremyevans0)

- This is related to [https://bugs.ruby-lang.org/issues/11588](https://bugs.ruby-lang.org/issues/11588)
- (Matz) I thought structured warning was overkill.
- (naruse) how about squashing all seen warning strings.
- (ko1) regular expression seems too shortcoming.
- (akr) It’s not a bad idea to provide a primitive to do something on warnings.

## [[Feature #12092]](https://bugs.ruby-lang.org/issues/12092) Allow Object#clone to yield cloned object before freezing (jeremyevans0)

- Use case?
- There are two ways to create a cloned object, namely #clone and #dup.  The difference is singleton classes, and frozenness.
- (n0kada) why not avoid singleton method and use dup->extend->freeze maneuver?

- That way you have to manage all and every calls to extend

## [[Feature #12172]](https://bugs.ruby-lang.org/issues/12172) Array#max and Array#min (mame)

- Nobody is against Array#max
- (shyouhei): Why only opt\_newarray\_max?

- (mame) When Array include another module which redefines max/min, current implementation can’t detect it.
- (mame) opt\_newarray\_max is only for literal.

- Nobody except ko1 is against opt\_newarray\_max

- Koichi afraids that increase VM complexity and other people want to add other methods.
- However, \[...\].min or .max are used CPU intensive cases. So that it is acceptable. If other techniques are invented, then Koichi will remove them.

## [[Feature #9969]](https://bugs.ruby-lang.org/issues/9969) Add File.empty? as alias to File.zero? (shyouhei)

- (akr) File.zero? is too bad in naming.
- Nobody is against it.

## [[Bug #12121]](https://bugs.ruby-lang.org/issues/12121)異なる名前空間にある同名の定数により Module.constants の結果の並びが変わる (shyouhei)

- The reason behind this is we introduced id\_table, which does not preserve order.
- We did not, and do not want to, warrant such thing.
- This is a doc issue.

## [[Bug #12112]](https://bugs.ruby-lang.org/issues/12112) Resolv.getname with IPv6 noop (shyouhei)

- Assign to akr

## [[Bug #12162]](https://bugs.ruby-lang.org/issues/12162) OpenSSL::PKCS7 seems to create broken objects (nested asn.1 error) (shyouhei)

- Assign to openssl

## [[Bug #12091]](https://bugs.ruby-lang.org/issues/12091) Freezing a SortedSet breaks Enumerable (shyouhei)

- Assign to knu

## [[Bug #12126]](https://bugs.ruby-lang.org/issues/12126) \[PATCH\] openssl: accept moving write buffer for write\_nonblock (shyouhei)

- Seems ok

## [[Feature #10617]](https://bugs.ruby-lang.org/issues/10617) Change multiple assignment in conditional from parse error to warning (shyouhei)

- Koichi: i want to make it “void” expression for performance.

## [[Feature #12094]](https://bugs.ruby-lang.org/issues/12094) parameterized property assignment: o.prop(arg) = 1 (shyouhei)

- Nobu is going to update the current status

## [[Feature #12096]](https://bugs.ruby-lang.org/issues/12096) New notation for instance variables and class variables (shyouhei)

- This is metaprogramming.  And having a metaprogramming in syntax is \`\`wrong’’.
- Being able to write identical variable in different form is problematic (both for users and editors)

## [[Bug #12104]](https://bugs.ruby-lang.org/issues/12104) Procs keyword arguments affect value of previous argument (shyouhei)

- Ko1 will update his current opinion.

## [[Bug #12106]](https://bugs.ruby-lang.org/issues/12106) Behavior of double splatting of hashes with non symbol key is different according to splatted hash position (shyouhei)

- Assign to nobu.

## [[Feature #11997]](https://bugs.ruby-lang.org/issues/11997) A method to read a file with interpolations (shyouhei)

- Nobu thinks this is not that simple to do.  He will add comments about it.

## [[Feature #12116]](https://bugs.ruby-lang.org/issues/12116) Fixnum#divmod, Bignum#divmod with multiple arguments (shyouhei)

Good example.

## [[Feature #12119]](https://bugs.ruby-lang.org/issues/12119) next\_prime for lib/prime.rb (shyouhei)

- Assign to yugui.

## [[Feature #12134]](https://bugs.ruby-lang.org/issues/12134) Comparison between true and false (shyouhei)

- Use partition method.

## [[Feature #11547]](https://bugs.ruby-lang.org/issues/11547) remove top-level constant lookup (shyouhei)

- Matz issue.

## [[Misc #12124]](https://bugs.ruby-lang.org/issues/12124) Use Automake (shyouhei)

- (naruse) common.mk is also used by nmake.
- (naruse) Current patch fails CI.
- Generally ok, but it must works with nmake.

## [[Bug #12139]](https://bugs.ruby-lang.org/issues/12139) return OpenSSL::Random.random\_bytes(n) call takes to long. OpenSSL:: bug on windows. (naruse)

- (naruse) How about using Random.naw\_seed on Windows
- (akr) ok while it doesn’t cost too much entropy.
