---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-05-17

Date: 2016/05/17 (Tue)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details. If you want to attend remotely, also please ask ko1.
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

## Carry-over from previous meeting(s)

* [Feature #12157] Is the option hash necessary for future Rubys? (shyouhei)
* [Feature #11925] `Struct` construction with kwargs (ko1)

## From attendees

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Feature #12324] Support OpenSSL 1.1.0 (and drop support for 0.9.6/0.9.7) (hsbt) We need to get aproval of Matz
* [Misc #12283] Obsolete ChangeLog and commit message in Git-style (shyouhei) next move?
* [Feature #12352] New hash syntax broken for numeric keys (shyouhei)
* [Feature #6647] Exceptions raised in threads should be logged (shyouhei) what was the reason against defaulting true?
* [Feature #12244] Add a way to `integer - integer % num` (shyouhei)
* [Bug #12337] inconsistency between Fixnum#coerce and Bignum#coerce (akr)
* [Feature #12005] Unify Fixnum and Bignum into Integer (naruse,mrkn,akr)
* [Feature #10548] remove callcc (hsbt)
* [Feature #11868] Proposal for `RubyVM::InstructionSequence.compile` to return an object containing the syntax error information currently written to `STDERR` (shyouhei)
* Assignee: ruby-core (duerst)
* [Feature #12263] Feature request: `&&.` operator (shorthand for `foo && foo.method`) (shyouhei)
* [Feature #11098] Thread-level allocation counting (shyouhei)
* [Bug #12295] Ripper not emitting `on_parse_error` for global variable name syntax errors (shyouhei) is this the right design?
* [Feature #12281] Allow lexicaly scoped use of refinements with `using {}` block syntax (shyouhei)
* [Feature #12317] Name space of a module (shyouhei)
* [Bug #9569] `SecureRandom` should try `/dev/urandom` first (shyouhei) I'd like to hear other opinions.
* [Bug #12292] Race between `OpenSSL::SSL::SSLSocket#stop` and `#connect` can cause a segmentation fault (shyouhei) who can handle this? seems well written to me.
* [Feature #12075] some container#nonempty? (naruse)
* [Feature #12338] bypass Exception.new (nobu)
* [Feature #12357] Random#initialize with a String (nobu)
* [Feature #12378] arbitrary size Random.new_seed (nobu)
* [Feature #11090] Enumerable#each_uniq and #each_uniq_by (shyouhei)
* [Feature #12275] String unescape (shyouhei) lets limit the scope to "invert #dump's effect"
* [Feature #12345] A module's private constants are given with `Module#constant(false)` (shyouhei) intended behaviour?
* [Bug #12359] Named captures "conflict" warning is unnecessary and limits uses of named captures (shyouhei)
* [Feature #12361] Proposal: add `Geo::Coord` class to standard library (shyouhei)
* [Feature #12306] `Object`/`String#blank?` (duerst)
* [Feature #12386] Move definition of `ONIG_CASE_MAPPING` compilation switch o... (duerst)
* lots of commit activity to Oniguruma (https://github.com/kkos/oniguruma/commits/master) (duerst)
* [Bug #12368] default encoding of `Integer#chr` (duerst)


## From non-attendees

* [Bug #4388] open-uriで環境変数http_proxyを使うときに認証付きのProxyが使えません
* [Feature #5899] chaining comparisons
* [Feature #6739] One-line `rescue` statement should support specifying an exception class
* [Feature #7314] Convert Proc to Lambda doesn't work in MRI
* [Feature #8895] Destructuring Assignment for Hash
* [Bug #10708] In a function call, double splat of an empty hash still calls the function with an argument
* [Feature #11735] `String#squish` and `String#squish!`
* [Feature #12138] Support `Kernel#load_with_env`
* [Feature #12299] Add Warning module for customized warning handling
* [Feature #12300] Allow `Object#clone` to take `freeze: false` keyword argument to not freeze the clone
* [Feature #12333] `String#concat`, `Array#concat`, `String#prepend` to take multiple arguments
* [Feature #12334] Final/Readonly Support for Fields / Instance Variables
* [Feature #12350] Introduce Array#find! that raises an error if element not found
* [Bug #12359] Named captures "conflict" warning is unnecessary and limits uses of named captures
* [Feature #12360] More useful return values from bang methods

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Next meeting:

June 13, 14:00-, at MoneyForward

(next candidate: July 6, 19, 21) -> fixed. July 19

Milestones

- 2.4 release on Dec.
- Kaigi on Sept.
- previrew @ kaigi maybe
- what should happen in June?

- slide check?
- previrew 1 maybe?  Then we can call for slides.
- conclusion: Next meeting will confirm to release preview1

## \[Feature [#12324](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12324&sa=D&source=editors&ust=1686086801763960&usg=AOvVaw2GnnN2YH_EDRwNAaeZh_vJ)\] Support OpenSSL 1.1.0 (and drop support for 0.9.6/0.9.7) (hsbt) We need to get aproval of Matz

- matz: OK to give him a commit grant.
- compatibility? is it OK to drop OpenSSL < 1.0?
- hsbt: we need to describe maintainer policy:

[https://bugs.ruby-lang.org/projects/ruby/wiki/DeveloperHowtoJa](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/DeveloperHowtoJa&sa=D&source=editors&ust=1686086801764565&usg=AOvVaw1QgCVlxhODutt2HAXy0fFK)

- introduce ruby/openssl subproject goal and status.

## \[Misc [#12283](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12283&sa=D&source=editors&ust=1686086801764860&usg=AOvVaw08c2xBuImeNrckn5RGNWIv)\] Obsolete ChangeLog and commit message in Git-style (shyouhei)

- (shyouhei) seems nobody is against.
- (Martin) I support it.
- next action?

- Matz needs a ChangeLog because he wants to look at commit log in file, not by svn log (because \`svn log\` needs internet).  But it’s OK for him to auto-generate.
- ChangeLog is needed as long as we stick to svn.
- (conclusion) lets auto-generate ChangeLog, like we do in version.h.
- nobu and naruse will look at it.

## \[Feature [#12352](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12352&sa=D&source=editors&ust=1686086801765554&usg=AOvVaw3L6-4ykRB0bozIpq8wLeJg)\] New hash syntax broken for numeric keys (shyouhei)

- matz: reject.  If this syntax is allowed { 1: 2 } should be considered { :’1’ => 2 }.

## \[Feature [#6647](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6647&sa=D&source=editors&ust=1686086801765965&usg=AOvVaw1CN_huQqOaw5vopE51EFrh)\] Exceptions raised in threads should be logged (shyouhei) what was the reason against defaulting true?

- Thread\[.#\]report\_on\_exception= is accepted
- Matz does not want this default true, because it breaks current situations where programmer expects dead threads to silently exit.
- what happens on join?

- Threads dead before join shall report exceptions.  If that is not a desired behaviour, explicitly set false.

## \[Feature [#12244](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12244&sa=D&source=editors&ust=1686086801766558&usg=AOvVaw1rDtSCTYABrkgKHoUXAtcr)\] Add a way to integer - integer % num (shyouhei)

- shyouhei: it’s slow.
- ko1: you can speed this up by dispatch in Ruby, say, in prelude.
- matz: is it time to speed up kwargs in general?
- matz: API OK, speed concern.
- ko1: @naruse please show us speed comparison.

## \[Feature [#12005](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12005&sa=D&source=editors&ust=1686086801767324&usg=AOvVaw3On1tXiL_wjCfgud-opkUk)\] Unify Fixnum and Bignum into Integer (naruse,mrkn,akr)

- akr: there is only one problem for this: do this now, or don’t.
- pros/cons:

- pros: long term benefit in education.
- cons: short term incompatibility.

- matz: let’s try.

## \[Bug [#12337](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12337&sa=D&source=editors&ust=1686086801768213&usg=AOvVaw03qVnOzwZVbJ7emnr4c4Rb)\] inconsistency between Fixnum#coerce and Bignum#coerce (akr)

- Matz: TypeError was an intended behavior, to prevent data loss.  So no need to backport this behaviour to previous releases.
- But as we try Fixnunm/Bignum unification, things gets much simpler.

## \[Feature [#10548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10548&sa=D&source=editors&ust=1686086801768965&usg=AOvVaw3Z0Ltp8x0wC8MOUw5MPaEg)\] remove callcc (hsbt)

- hsbt: current status?
- ko1: we historically tried callcc but was not successful.  The intension of this issue is we wish to treat callcc-related issues to be 3rd-party issue.
- We will not remove callcc, but we cannot guarantee that it works correctly (we suspect there might be quite some undiscovered bugs), and we don’t have any manpower to support it

## bugs.ruby-lang.org Assignee: ruby-core confusion (duerst)

- when we look at the “my page” from the top page of redmine, there are lots of issues that are not farmiliar at all.
- the reason for this is “ruby-core” assignment, which dupes assignment to each subscribers of it.
- this is frustrating.
- naruse:  I intended ruby-core to manage projects and roles. It is not for assigning.
- hsbt: will look for a way to prevent assignment.
- Yui changed all “Assignee: ruby-core” to “Assignee: nobody” with a global operation. Thanks! But this doesn’t yet prevent future assignments.

## \[Feature [#12263](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12263&sa=D&source=editors&ust=1686086801769875&usg=AOvVaw1sYflZ4K1B3A8hoMaCIJsi)\] Feature request: &&. operator (shorthand for foo && foo.method) (shyouhei)

- matz: example seems illustrative.  any real-world use-case?

## \[Bug [#4388](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4388&sa=D&source=editors&ust=1686086801770265&usg=AOvVaw2QwktX9Z-wvZxMCfb60ktg)\] open-uriで環境変数http\_proxyを使うときに認証付きのProxyが使えません

- akr: hsbt already introduced this at r54432.
- lets ask if trunk is ok.

## \[Feature [#5899](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/5899&sa=D&source=editors&ust=1686086801770708&usg=AOvVaw2bCofo1kjPawWj7mpoSQWA)\] chaining comparisons

- Hmm.
- This is about language design.  Matz should decide.
- should parser convert x < y < z to x < y && y < z ?
- a < b == c < d has shift/reduce conflict.

- should limit to gt/lt.

- Matz: doesn’t sould yummy.

## \[Feature [#12157](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12157&sa=D&source=editors&ust=1686086801771599&usg=AOvVaw2RmEWhMPoPgdg9K1CCTa8y)\] Is the option hash necessary for future Rubys? (shyouhei)

- Matz: positive.  It is good in long term
- akr: migration path?

- warning when a kwargs-called method receives that in option hash.

## \[Feature [#11925](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11925&sa=D&source=editors&ust=1686086801772138&usg=AOvVaw21m6Sazf1G-4L5R5WzUrBz)\] Struct construction with kwargs (ko1)

- convenient, but is #create an appropriate name?
- no one is against? (except naming)
- nobu: Hash#to\_struct(klass)
- create! doesnt follow naming convention.

## \[Feature [#6739](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6739&sa=D&source=editors&ust=1686086801772792&usg=AOvVaw26uC9J02HloGnXbYoPWIVh)\] One-line rescue statement should support specifying an exception class

- no good syntax so far.
- nobu: all the proposed syntax, except for rescue when, generated syntx conflicts.
- shyouhei: rescue modifier is too easy to write.
- naruse: practical use case is io.close rescue nil
- mrkn: another real world application is if expr resuce false

## \[Feature [#7314](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7314&sa=D&source=editors&ust=1686086801773502&usg=AOvVaw3GjaEkAYxJ1bO_4XzCBw33)\] Convert Proc to Lambda doesn't work in MRI

- The current behaviour is not “unpredictable” in a sense.

## \[Feature [#8895](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8895&sa=D&source=editors&ust=1686086801773816&usg=AOvVaw3JW7_-mcdV6FMhJ5oHSZr4)\] Destructuring Assignment for Hash

- ko1: elixir does this.
- shyouhei: maybe 99% use case of hash destruction was already solved by kwargs?
- akr: the OP propses use case with MatchData.
- matz: reject this specific one

## \[Bug [#10708](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10708&sa=D&source=editors&ust=1686086801774320&usg=AOvVaw2VPl0imIfs46oRebR7XEwG)\] In a function call, double splat of an empty hash still calls the function with an argument

- This is a side-effect of optional hash argument treatment.
- akr: this should be fixed by abondoning optional hash.

- this could be one of a migration path to force \*\*kwargs.

## Oniguruma and/or Onigumo situations? (duerst)

- Duerst: oniguruma has updates.
- naruse: I hadn’t know Oniguruma is on GitHub now [https://github.com/kkos/oniguruma](https://www.google.com/url?q=https://github.com/kkos/oniguruma&sa=D&source=editors&ust=1686086801774940&usg=AOvVaw2hC4rXiREAKvSrZHfL_4Rv). I’ll check it.

## \[Bug #12368\] default encoding of Integer#chr

- As usa says script encoding doesn’t exist run time.
- defaults to utf-8?
- naruse: script encoding doesn’t work well if the code uses variables.
- naruse will think about it a bit more.

## \[Feature [#12306](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12306&sa=D&source=editors&ust=1686086801775501&usg=AOvVaw2wiXXqfWSOkhF25_4jVrof)\] Object/String#blank (duerst)

- akr: regexp match without object allocation can be an alternative. (see #[8110](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8110&sa=D&source=editors&ust=1686086801775748&usg=AOvVaw35IGX11fZf_5r8SoTc1NHi))
- akr: How about the name Regexp#match?(str)
- naruse: blank can be special-cased to be even faster.
- naruse will write an implementation
- matz isn’t very positive, but may be okay for String only (for other classes, this will remain the responsibility of Rails)

## \[Feature [#8110](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8110&sa=D&source=editors&ust=1686086801776426&usg=AOvVaw1_dlp4bXoHfJA-j9GCzhNk)\] Regex methods not changing global variables (akr)

- matz: approved.
- implementation is very easy
- name: “match?” ?

## \[Feature [#12075](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12075&sa=D&source=editors&ust=1686086801777056&usg=AOvVaw1GBR4zE1XPKwCN-wIbhCo6)\] some container#nonempty? (naruse)

- matz: I want this in ActiveSupport.
- matz: Enumerable#any? seems useful.
- naruse: Yeah, it works for me.
- naruse: Feedback until someone find the case any? doesn’t work.

## Enumerable#sum (akr)

- akr: I want Range#sum
- mrkn: I can think of useful case of Hash#sum

- e.g. { 1 => 0.2, 2 => 0.3, 4 => 0.1, 5 => 0.4 }.sum {|k, v| k \* v } #=> 3.2

- matz: OK.

## \[Feature [#12357](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12357&sa=D&source=editors&ust=1686086801778120&usg=AOvVaw1jtU57mVVgA9cbF7G606Rx)\] Random#initialize with a String (nobu)

- akr: you can pass bignum to Random.new\_seed
- naruse: bignum is not useful when people port code from different languages e.g. python
- akr: array of integers must be more “appropriate” than binary string.
- nobu: I want to pass Random.raw\_seed to it
- akr: why? no need seems there be. 128 bits should be sufficient.
- naruse: Linux’s urandom (or getrandom) manual says that.

## Random::XXX new class for different RNG (nobu)

- nobu: I want some different RNG that share some part of default one.
- naruse: I object the idea to unify Random and SecureRandom
- nobu: I intend MT and
- akr: how about split utility methods into a module

## \[Feature [#11735](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11735&sa=D&source=editors&ust=1686086801779323&usg=AOvVaw0Cx7bjFPyikO53rk2nUATy)\] String#squish and String#squish!

- shyouhei: useful when writing a long SQL
- nobu: is this unicode aware?
- mrkn: difficult to use in SQL becasue it breaks spaces inside string literals.
- ko1: can be useful than #rjust.
- akr: this can be as strange as Unicode-aware strip
- nobu: how about extending #tr\_s so that it accepts arbitrary character class?
- shyouhei: how about #strip that accepts regexp? -> NG, squish is not a strip.
- akr: is this worth providing in-core?
