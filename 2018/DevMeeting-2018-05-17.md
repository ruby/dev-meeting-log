---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-05-17

Date: 2018/05/17 (Thu)
Time: 14:00-18:00 (JST)
Place: Cookpad Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/85917/

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

From this time, we use a ticket to make dev-meeting agenda page instead of a wiki page <https://bugs.ruby-lang.org/projects/ruby/wiki>.

# NOTE

Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
We will write a log about discussion to a file or to each ticket in English.
All activities are best-effort (keep in mind that most of us are volunteer developers).
The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

https://bugs.ruby-lang.org/issues/14769

## About 2.6 timeframe

preview2 will be released RubyKaigi 2018 day 1 or 2

## From Attendees

* [Bug #14699] Subtle behaviors with endless range (mame)
* [Feature #14724] chains of inequalities (Martin)
  * Proposal by gotoken (Kentaro Goto) to allow `0 <= a < 10` as a shortcut of `0<=a && a<10`, and so on; patch by Nobu avaliable

(will be edited later)
(if you have a write access permission, please list directly)

## From non-attendees

* [Feature #14697] Introducing Range#% as an alias to Range#step (mrkn)
  * Matz already commented LGTM.  Please judge whether this is acceptable.
* [Feature #14724] chains of inequations (mrkn)
  * This new syntax can also reduce the duplicated evaluations of common terms, such as `timeval.tv_sec` in the descriptiohn of the proposal.


Functional programming: (zverok)
* [Feature #6284] Add composition for procs
  * 6-year-old proposal. Matz: "Positive about adding function composition. But we need method name consensus before adding it? Is `#*` OK for everyone?"
* [Feature #13581] Syntax sugar for method reference
  * 1-year-old proposal. Matz: "I am for adding syntax sugar for method reference. But I don't like proposed syntax (e.g. `->`). Any other idea?"
* [Feature #11161] Proc/Method#rcurry working like curry but in reverse order
  * 3-year-old proposal with absolutely no reaction. I've added real-life examples there several months ago.
* [Feature #14390] UnboundMethod#to_proc
* [Feature #14423] Enumerator from single object (`Object#enumerate`)

Misc:
* [Bug #14575] Switch `Range#===` to use `cover?` instead of `include?` (zverok)
* [Feature #14473] Add Range#subrange? ( greggzst )
* [Feature #14097] Add union and difference to Array ( ana06)
* [Feature #14105] Introduce xor as alias for Set#^ ( ana06)
* [Misc #14760] cross-thread IO#close semantics (normal)

Docs (probably not to discuss on development meeting, but I am not sure where I can post it to have it more visible) (zverok):

* Method: https://bugs.ruby-lang.org/issues/14483
* Proc: https://bugs.ruby-lang.org/issues/14610
* MatchData: https://bugs.ruby-lang.org/issues/14450
* yield_self: https://bugs.ruby-lang.org/issues/14436



(will be edited later)
(if you have a write access, please list directly)

# Log

Date: 2018/05/17 (Thu)

Time: 14:00-18:00 (JST)

Place: Cookpad Inc. (Tokyo, Japan)

logs

## Next dev-meeting

- 5/31 11:30- at RubyKaigi
- 6/21 14:00- at Cookpad

## About 2.6 timeframe

- Usa: preview2...
- Hsbt: next week?
- Naruse: not that immediate.
- Naruse: hopefully the day before the Kaigi.

## About Neon EOL

- Neon (one of our oldest host) is running debian wheezly, which will be EOLed this month.

## From Attendees

- \[Bug [#14699](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14699&sa=D&source=editors&ust=1686088412433702&usg=AOvVaw1ttGbU3l48pozEO6DJxl64)\] Subtle behaviors with endless range (mame)

- Mame: what to do with #max for endless ones?
- Usa:

- Exception?
- Retuen nil?

- Akr: is it possible to detect if a range is endless or not?
- Mame: #end can be used that case.
- Mrkn: I like nil. (by text chat)
- Ko1: let’s discuss this issue with mrkn.

- \[Feature [#14724](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14724&sa=D&source=editors&ust=1686088412434545&usg=AOvVaw1jUAcXq0NOu6mm5oDQT3Vd)\] chains of inequalities (Martin)

- Proposal by gotoken (Kentaro Goto) to allow 0 <= a < 10 as a shortcut of 0<=a && a<10, and so on; patch by Nobu avaliable
- Ko1: what do you think matz?
- Matz: I understand people want to write this.
- Matz: what’s this nobu’s patch?
- Nobu: that patch breaks existing code.
- Matz: what code?
- Nobu: shell.rb
- Ko1: so this patch breaks DSLs that use < characters.
- Usa: what about #< to return self instead?
- Matz: I think I tested that before, but did not work.
- Akr: ruby up to 0.51 behave like that.

## From non-attendees

- \[Feature [#14697](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14697&sa=D&source=editors&ust=1686088412435626&usg=AOvVaw1A-3RSUYUxUvA4qD3qatwn)\] Introducing Range#% as an alias to Range#step (mrkn)

- Matz already commented LGTM. Please judge whether this is acceptable.
- Akr: I think this should go.
- (nobody at the meeting had any objections)

- \[Feature [#14724](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14724&sa=D&source=editors&ust=1686088412436022&usg=AOvVaw305MtH6t3YM4pgM8ZiXEpc)\] chains of inequations (mrkn)

- This new syntax can also reduce the duplicated evaluations of common terms, such as timeval.tv\_sec in the descriptiohn of the proposal.
- Same issue as above. See above.

- Object#then (Usa)

- Usa: no one can commit this feature but matz.
- Matz: I already did it for mruby.

- Jemalloc (shyouhei)

- Shyouhei: We are going to change configure default, OK?

- \[Feature #5400\] Remove flip-flops in 2.0 (mame)

- Mame: now is the time?
- Matz: now?
- Usa: everyone who use this feature are in this meeting, I think
- Akr: is this worth deleting?  Nobody uses this means it has no benefit.

Functional programming: (zverok)

- \[Feature [#6284](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6284&sa=D&source=editors&ust=1686088412436970&usg=AOvVaw1e6WTyZ04eKWMecLybZhN_)\] Add composition for procs

- 6-year-old proposal. Matz: "Positive about adding function composition. But we need method name consensus before adding it? Is #\* OK for everyone?"
- (ko1 is describing the Ruby Hack Challenge results)
- Usa: is it OK to add #compose at the first place? Apart from the operators?
- Shyouhei: I think it’s OK to have the feature itself but for the operators?
- Mame: do we need composition?
- Matz: it seems comment #60 introduces Symbol#>>.
- Shyouhei: that sounds radical.
- Matz: shouldn’t we start with Proc…
- Matz: and I think we need both OOP-ish / FP-ish way.

- \[Feature [#13581](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13581&sa=D&source=editors&ust=1686088412437700&usg=AOvVaw126jlihr_iWWOsRIkk5AK9)\] Syntax sugar for method reference

- 1-year-old proposal. Matz: "I am for adding syntax sugar for method reference. But I don't like proposed syntax (e.g. \->). Any other idea?"
- (reviewed comment #21)
- Preference by the attendees is .:
- Matz: \`.:\` is better than \`->\`
- Mame: there is a patch cf #12125 [https://bugs.ruby-lang.org/issues/12125](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12125&sa=D&source=editors&ust=1686088412438185&usg=AOvVaw1dW4TYF62KFZX7ahnMdfqv)

- \[Feature [#11161](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11161&sa=D&source=editors&ust=1686088412438389&usg=AOvVaw3arbQyV0LtZ4duGpWFYMG1)\] Proc/Method#rcurry working like curry but in reverse order

- 3-year-old proposal with absolutely no reaction. I've added real-life examples there several months ago.
- Mame: I don’t think reverse currying works for variadic methods
- Akr: the “real-life” example is adding keyword arguments, which is not what currying is talking about

- \[Feature [#14390](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14390&sa=D&source=editors&ust=1686088412438732&usg=AOvVaw2QkMfPWunWxFpjcXC3FgFo)\] UnboundMethod#to\_proc

- What is the expected behaviour here?

- \[Feature [#14423](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14423&sa=D&source=editors&ust=1686088412438995&usg=AOvVaw0WzSELKsMgtw7XD3fS_Dm8)\] Enumerator from single object (Object#enumerate)

- Naruse: interesting proposal
- Akr: adding method to Object sounds too radical to me.
- Usa: I think there is a chance if this is a method of Enumerable.
- Shyouhei: my feeling is this should start as a gem.

Misc:

- \[Bug [#14575](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14575&sa=D&source=editors&ust=1686088412439470&usg=AOvVaw0Hq_vgGfveiKPE_2F9thvW)\] Switch Range#=== to use cover? instead of include? (zverok)

- Seems there is a compatibility issue.
- Usa: does it matter? No one actually expects Range#=== to behave as #cover?
- Matz: the problem, if any, is String.  What about keep string’s behaviour as is?

Docs (probably not to discuss on development meeting, but I am not sure where I can post it to have it more visible) (zverok)

- Method: [https://bugs.ruby-lang.org/issues/14483](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14483&sa=D&source=editors&ust=1686088412439975&usg=AOvVaw1yJEalFlTkURkExPYkMuW3)
- Proc: [https://bugs.ruby-lang.org/issues/14610](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14610&sa=D&source=editors&ust=1686088412440184&usg=AOvVaw3kOXJ2tBJzUxz4C7BF96-G)
- MatchData: [https://bugs.ruby-lang.org/issues/14450](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14450&sa=D&source=editors&ust=1686088412440368&usg=AOvVaw0FH8pUXFdD9100eTNadvsJ)
- yield\_self: [https://bugs.ruby-lang.org/issues/14436](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14436&sa=D&source=editors&ust=1686088412440545&usg=AOvVaw1R6-Pbaa_2zNruuecmUsHd)

- Naruse: This meeting is not for documents. Just assign to docs member group.

- \[Feature [#14473](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14473&sa=D&source=editors&ust=1686088412440903&usg=AOvVaw2guhkrCicaME5pjIrwXDnW)\] Add Range#subrange? ( greggzst )

- Shyouhei: what to do with String this case?
- Akr: the simplest is to only concern the both ends.
- Usa: Hash has \`>\` and \`<\`
- Mame: use case not clear.

- \[Feature [#14097](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14097&sa=D&source=editors&ust=1686088412441296&usg=AOvVaw1oJt2z3tm24I8E-48RF_HO)\] Add union and difference to Array ( ana06)

- Matz: create #union and #union!, or create mutating #union ?
- Matz: I want to add mutating method but #union does not sound mutating.
- Knu: Set has #union, which is not destructive.
- Akr: to me it seems mutating or not is not the key point of this proposal.
    The key point is provide a (readable) name addition to an operator, I guess.

- \[Feature [#14105](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14105&sa=D&source=editors&ust=1686088412441681&usg=AOvVaw1MCrbehASxWzXoVnOCFbZr)\] Introduce xor as alias for Set#^ ( ana06)

- Assign to knu

- \[Misc [#14760](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14760&sa=D&source=editors&ust=1686088412441929&usg=AOvVaw1qUeOTtyITJ9CgW22-Mivg)\] cross-thread IO#close semantics (normal)

- Ko1: I made it theoretically possible to unblock IO#select. However there is a difficult problem for  IO#copy\_stream.
