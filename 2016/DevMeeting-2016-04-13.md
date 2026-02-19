---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-04-13

Date: 2016/04/13 (Wed)
Time: 13:00- 18:00 (JST)
Place: Tokyo, Japan
pub: log: https://docs.google.com/document/d/11N2pK5mzoyqsIHyifxLycXUzcKtEj9vTmIYi3_ZTAS0/pub

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

## Carry-over from previous meetings

* [Feature #9969] Add `File.empty?` as alias to `File.zero?` (shyouhei)
* [Feature #10617] Change multiple assignment in conditional from parse error to warning (shyouhei)
* [Feature #11547] remove top-level constant lookup (shyouhei)
* [Feature #12020] Documenting Ruby memory model (shyouhei)

## From Attendees

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Bug #12271] `Time#to_time` removes timezone information (yui-knk)
* [Feature #12157] Is the option hash necessary for future Rubys? (shyouhei)
* [Bug #12181] ブロックがたくさんあるファイルを編集するとruby-modeが重い (shyouhei) who should take care of it?
* [Bug #12179] Build failure due to VPATH expansion (shyouhei) who should take care of it?
* [Bug #12183] require "win32ole" すると終了ステータスが必ず 0 になる (shyouhei) who should take care of it?
* [Bug #12184] Cygwin LANG=ja_JP.SJIS 環境でコマンドライン引数に日本語が渡せない (shyouhei) who should take care of it?
* [Feature #7361] Adding Pathname#touch (shyouhei) what's next?
* [Feature #12236] Introduce `mmap` managed heap (ko1)
* [Bug #12159] Thread::Backtrace::Location#path returns absolute path for files loaded by require_relative (ko1)
  * "path" representation
* [Feature #11925] Struct construction with kwargs (ko1)
* [Bug #11878] Comparison of prepended modules (naruse)
* How we use twitter account for announcement (sorah)
* [Feature #12275] String unescape (shyouhei)
* [Bug #12274] accessing to instance variable should be fast. (shyouhei)
* [Feature #12080]Enumerable#first, Array#last with block (shyouhei)
* [Feature #12217] Introducing Enumerable#sum for precision compensated summation and revert r54237 (mrkn)

## From non-attendees

(Additional explanation is welcome because we can't ask about it immediately)

* [Feature #11210] IPAddr has no public method to get the current subnet mask (eregon) Who can review this?
* [Feature #6647] Exceptions raised in threads should be logged, Thread#report_on_exception= (eregon) Can we agree on the feature?
* [Feature #12026] Support warning filters (jeremyevans0)
* [Feature #12092] Allow Object#clone to yield cloned object before freezing (jeremyevans0)

# Log

Attendees: matz, sorah, naruse, ko1, mame, kosaki, hsbt, nobu, tarui, akr, shyouhei, mrkn

## Next meeting

5/17/2016 (Tue) JST

venue: TBD

## \[Feature [#9969](https://bugs.ruby-lang.org/issues/9969)\] Add File.empty? as alias to File.zero? (shyouhei)

(discussing about current behavior of related methods)

- matz: does it make sense?
- (others): yes
- matz: ok
- kosaki: do you say “zero sized file”?

## \[Feature [#10617](https://bugs.ruby-lang.org/issues/10617)\] Change multiple assignment in conditional from parse error to warning (shyouhei)

- ko1: counter proposal. returning array can affect performance. how about to make mltiple assignment as a void expression?

- example: [http://www.atdot.net/~ko1/diary/201502.html#d16](http://www.atdot.net/~ko1/diary/201502.html#d16)

- naruse: I doubt the reporter’s true working code is
    a, b = some\_method\_returning\_array\_or\_nil
    if a || b
    \# method returned an array (possibly empty)
    else
     # method returned nil.
    end
- nobu: does author want to use this proposal? does he want it?

- a, b = …; if a ….

- ko1: i can’t understand what happen on it.
- matz: I want to use it. accept.

## \[Feature [#11547](https://bugs.ruby-lang.org/issues/11547)\] remove top-level constant lookup (shyouhei)

- matz: lets try this to see how much codes break with it.

## \[Feature [#12020](https://bugs.ruby-lang.org/issues/12020)\] Documenting Ruby memory model (shyouhei)

## \[Feature [#11210](https://bugs.ruby-lang.org/issues/11210)\] IPAddr has no public method to get the current subnet mask (eregon) Who can review this?

- assign to knu-san
- naruse: mentioned to knu

## \[Feature [#6647](https://bugs.ruby-lang.org/issues/6647)\] Exceptions raised in threads should be logged, Thread#report\_on\_exception= (eregon) Can we agree on the feature?

Matz is positive to provide Thread#report\_on\_exception, but negative to turning it on by default.

## \[Feature [#12026](https://bugs.ruby-lang.org/issues/12026)\] Support warning processor

- matz: agree with customizable warning

- should not use global variables
- there are many design choise. continue to discuss

## \[Feature [#12092](https://bugs.ruby-lang.org/issues/12092)\] Allow Object#clone to yield cloned object before freezing (jeremyevans0)

## \[Feature #[12217](https://bugs.ruby-lang.org/issues/12217)\] Introducing Enumerable#sum for precision compensated summation and revert r54237 (mrkn)

- matz: Adding Array#sum is ok
- Revert r54237 and optimization for Float

## \[Bug [#12271](https://bugs.ruby-lang.org/issues/12271)\] Time#to\_time removes timezone information

- nobu: It is a bug, so I commited
- yui\_knk: ActiveSupport was testing this behavior (FYI)

## \[Feature [#12157](https://bugs.ruby-lang.org/issues/12157)\] Is the option hash necessary for future Rubys?

- We have no matz for time when discussed this issue, so pend until next meeting.

## \[Bug [#12181](https://bugs.ruby-lang.org/issues/12181)\] ブロックがたくさんあるファイルを編集するとruby-modeが重い

- Who can look this issue? Nobu?
- nobu will investigate and make some action.

## \[Bug [#12179](https://bugs.ruby-lang.org/issues/12179)\] Build failure due to VPATH expansion

- Weird. Nobu will investigate.

## \[Bug [#12183](https://bugs.ruby-lang.org/issues/12183)\] require "win32ole" すると終了ステータスが必ず 0 になる

- Assigned to appropriate maintainer.

## \[Bug [#12184](https://bugs.ruby-lang.org/issues/12184)\] Cygwin LANG=ja\_JP.SJIS 環境でコマンドライン引数に日本語が渡せない

- nobu: Cygwin only supports UTF-8
- nobu(?) will reply something

## \[Feature [#7361](https://bugs.ruby-lang.org/issues/7361)\] Adding Pathname#touch

- actually touch(1) does lot of thing. What ticket author wanted?
- Encourage author to make feature request more solid.

## \[Feature [#12236](https://bugs.ruby-lang.org/issues/12236)\] Introduce mmap managed heap (ko1)

## \[Feature [#11925](https://bugs.ruby-lang.org/issues/11925)\] Struct construction with kwargs (ko1)

- Understood and +1 for this feature, but maybe naming is a problem
- We also have to wait a Matz.

# How we use twitter account for announcement (sorah)

- few months ago, we talked about having a twitter account for announcement. after a while, @mrkn signed an account up and delegated to @sorah.
- How we use this account? @sorah is thinking to post www.ruby-lang.org feeds. One consideration is current w.r-l.o feed contains community news. The account should contain or shouldn’t? (= only release informations)

- Conclusion: only for release notes.

- note that @sorah is also a w.r-l.o editorial.
- next action: @sorah will continue setting up.

\[FYI\] Cookpad’s styleguide of the number of characters in a line:

[https://github.com/cookpad/styleguide/blob/master/ruby.ja.md#line-columns](https://github.com/cookpad/styleguide/blob/master/ruby.ja.md#line-columns)
