---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-01-10

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/01/10 (Thu)
Time: 13:00-17:00 (JST)
Place: Cookpad Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/113278

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.7 timeframe

## Check security tickets

## Carry-over from previous meeting(s)

## From Attendees

## From non-attendees

----

Please comment your favorite ticket we need to discuss with *the following format*.

```
* [Ticket ref] Ticket title (your name)
  * your comment why you want to put this ticket here if you want to add.
```

Your comment is very important if you are no attendee because we can not ask why you want to discuss about it.

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss about this feature.
```

I don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

## Next dev-meeting

- 2/7 (Thu) 13:00~17:00 @MoneyForward (Minato-ku)

## About 2.7 timeframe

- trunk is already 2.7.0

## Check security tickets (confidencial)

- discussed

## Carry-over from previous meeting(s)

- It seems no carry-over issue was there.

## From Attendees

- \[Feature [#6012](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6012&sa=D&source=editors&ust=1686088754535579&usg=AOvVaw0DYkadb4v_GKRStjMtRup4)\] Proc#source\_location (ko1)

- nobu: this breaks compatibility
- ko1: how about specifying the new behaviour by some optional parameter?
- matz: we don’t have to take care of old behavior in this case
- mame: pry is using source\_location#last
- naruse: pry will be pleasured in this change, I guess :)
- ko1: I’m against to break compatibility
- usa: if you really concern to keep compatibility, you should propose this feature as new method.
- shyouhei: this feature seems not yet mature.

- Frozen string literal \[Feature [#11473](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11473&sa=D&source=editors&ust=1686088754536407&usg=AOvVaw13Tnt10FAIXkkJxjodRIIB)\]

- Matz: give up on Ruby 3.0. Maybe far future.
- Matz: Maybe we should provide our coding style to recommend style and obsolete bad style by something like rubocop.

- Special variable for block parameter

- $\_
- @1, @2, @3
- Ko1: Which \`@1\` mean \`{|x|\` or \`{|x, |\`
- Znz: \`@0\` means \`{|x|\`
- Akr: ary.map { .to\_i }
- Knu: … as in Lua

- Mark for a method which doesn’t receive a proc

- matz: I’m planning to introduce a new syntax to specify that the method does not accept any blocks.  proposals?
- \`&nil\`
- \`&!\`
- \`!&\`
- \`&void\`
- Matz: Ok, \`&nil\`.
- Ko1: Objection.  Many pull-reqs “added &nil” will be opened
- Ko1: I have been thinking of making Ruby automatically detect if a method actually takes a block even by checking if it uses a block argument or it has a \`yield\` keyword in it.  To achieve this, I want to ban \`yield\` from within \`eval\`, and blockless call of proc/lambda/Proc.new etc.  I believe that is the way to go.  Please warn for “proc” and “Proc.new” without block as obsolete.
- Matz: accepted.
- Akr: “lambda without block” has been already warned since 2003 (ruby-1.8.0), How about changing it to an exception?
- Matz: accepted.

- \[Bug [#15404](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15404&sa=D&source=editors&ust=1686088754538170&usg=AOvVaw31ZO5mhdUmNMEYKto2oUCg)\] Endless range has inconsistent chaining behaviour (mame)

- Can we prohibit (1.. ..1) and (1..1)..1 as a SyntaxError?
- matz: ok, prohibit
- usa: please add test cases into test\_syntax.rb

- \[Feature [#14799](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14799&sa=D&source=editors&ust=1686088754539462&usg=AOvVaw0toGN0MO7D6UC8ye2TDGmX)\] Startless range (aycabta)

- Matz: try it.

- \[Bug [#15460](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15460&sa=D&source=editors&ust=1686088754540122&usg=AOvVaw2f-5lxGDUfxHTJhp48qRuq)\] Behaviour of String#setbyte changed (shyouhei)

- What is the expected / desired way to fix this?

- \[Bug [#7300](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7300&sa=D&source=editors&ust=1686088754540937&usg=AOvVaw008CVVKmO4MCizb1pQkSuD)\] Hash#\[\] の挙動が 1.9.3 と異なっている (mame)

- I think we can remove the compatibility layer for 1.9: Hash\[\[nil\]\] #=> {}

- \[Misc [#15347](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15347&sa=D&source=editors&ust=1686088754541910&usg=AOvVaw1_-d4yiUVBUs42ZGifTBVb)\] Require C99 (k0kubun)
- \[Feature [#15445](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15445&sa=D&source=editors&ust=1686088754542438&usg=AOvVaw3ANVV15DdxHMRwx7244F8Z)\] Reject '.123' in Float() method (mrkn)

- Matz: I’d like to keep the behavior.  Rather, I’d like to allow “123.” too.

- \[Bug [#15500](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15500&sa=D&source=editors&ust=1686088754542957&usg=AOvVaw0l7YusQW9g5Jj143F7ekuZ)\] Behavior of require method in 2.5 is different from 2.4 and 2.6 (mrkn)

- usa: It seems bug.  let’s ask hsbt-san

- \[Feature [#15477](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15477&sa=D&source=editors&ust=1686088754543353&usg=AOvVaw2C7UzRKIlWWh_kh4OW4RRJ)\] Proc#arity returns -1 for composed lambda Procs of known arguments

- matz: ok

## Non-attendees

- \[Feature [#14444](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14444&sa=D&source=editors&ust=1686088754543852&usg=AOvVaw3zLzc-nVcGyzgijBoljC-E)\] MatchData: alias for #\[\]

- Naruse: \`next\_page = response.dig('meta', 'pagination', 'next')&.slice(/&page=(\\d+)/, 1)\` is better

- \[Feature [#14784](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14784&sa=D&source=editors&ust=1686088754544311&usg=AOvVaw3xlp7zd6QPlTPCpmMKKOmg)\] Comparable#clamp with a range

- usa: the reporter seems have no interest about startless/endless clamp.  But we recognize that we can’t approve range as parameter before accepting startless/endless clamp.
- akr: we have to consider many corner cases if accepting Range.  Request for feedback.

- \[Feature [#14145](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14145&sa=D&source=editors&ust=1686088754544742&usg=AOvVaw14g3ejganKFa9wtMpKUd9C)\] Better Method#inspect

- usa: a patch is welcome.  We might need to discuss the implementation detail, though.
- Matz: the proposal is accepted
- Naruse: just an idea, ArgumentError which is raised by arity check should also show such signature.

- \[Bug [#15428](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15428&sa=D&source=editors&ust=1686088754545283&usg=AOvVaw11AVNqySAxQODodVBWSg0L)\] Refactor Proc#>> and #<< (it regards the same problem as [#15483](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15483&sa=D&source=editors&ust=1686088754545740&usg=AOvVaw03KfHz_lopng9KsWiI68A5) above, but approaches it from a different point of view)

- Mame: Use a block.  You then want syntactic sugar for passing an argument, and then for partial application, blah blah blah.
- knu: We should not add fancy new features around procs and symbols if the real problem is that block syntax is not good enough, like \`|x|\` is always necessary. (See above for the default block parameter syntax)
- Matz: raise an error when the argument does not respond to :call

- \[Misc [#15486](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15486&sa=D&source=editors&ust=1686088754546262&usg=AOvVaw3tFjfB46g2upHRhrvQ9202)\] Default gems README.md

- \[Misc [#15487](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15487&sa=D&source=editors&ust=1686088754546571&usg=AOvVaw2L3FVRq0lFMXE5icARUxhB)\] Clarify default gems maintenance policy
- \[Feature [#15373](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15373&sa=D&source=editors&ust=1686088754546991&usg=AOvVaw1fpWn1tbOlszpd0Y1oQ3T3)\] Proposal: Enable refinements to #method and #instance\_method

- matz: go ahead

- \[Feature [#15374](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15374&sa=D&source=editors&ust=1686088754547871&usg=AOvVaw0TUenSfP3cWZOgmxxhKk0l)\] Proposal: Enable refinements to #method\_missing

- matz: wait a month

- \[Bug [#15416](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15416&sa=D&source=editors&ust=1686088754548505&usg=AOvVaw1-dJuOnDnBuBFzmRNI6bsy)\] 配列リテラル内の引数を伴う括弧なしのメソッド呼び出しで syntax error

- History:

- 2006: r10235 removes command from aref\_args.
- 2007: r13821 removes warning from call\_args.

- \*: Should we emit a warning against both cases?
- knu: There's so much existing code out there written as follows:

- expect(command arg).to eq(value)
- f.(Math.sqrt 2)
- f\[Math.sqrt 2\]

- \[Bug [#2250](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/2250&sa=D&source=editors&ust=1686088754549639&usg=AOvVaw00u1-6Bl-BN-B9niL6ZPnw)\] IO::for\_fd() objects' finalization dangerously closes underlying fds (eregon)

- The current behavior is dangerous and has caused many spurious test/spec failures which are hard to find and debug. I plan to submit a patch, but I'd like opinions regarding compatibility.

- \[Bug [#15488](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15488&sa=D&source=editors&ust=1686088754550232&usg=AOvVaw0bVnY7a6j1_E0PYROhUWJJ)\] const\_defined?("File::NULL") の挙動

- What is the expected behavior?
- matz: ok, try to return “true” in this case. if we find something wrong, will give up.

- \[Feature [#15456](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15456&sa=D&source=editors&ust=1686088754550683&usg=AOvVaw1rbi1FBPNq7p8bIgg-spcX)\] Adopt some kind of consistent versioning mechanism

- More uniformity within the ecosystem would be nice.
- Naruse replied it.

- \[Feature [#11473](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11473&sa=D&source=editors&ust=1686088754551064&usg=AOvVaw3nQ0Xt3ZYROzd1Qo0APNpk)\] Immutable String literal in Ruby 3

- Mame: As I recall, matz said that this has been cancelled at the old developers' meeting. I'd like to confirm.

- Matz: Rubocop can be used to agitate people to frozen string, but looks premature for Ruby 3.  So try it in Ruby 4.
- knu: Rails generators could add the magic comment to auto-generated files to promote the style.
- Akira Matsuda: Rails itself uses frozen string literal.  It seems that frozen-string-literal by default would not be a big problem.
