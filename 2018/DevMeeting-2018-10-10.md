---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-10-10

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2018/10/10 (Wed)
Time: 14:00-18:00 (JST)
Place: pixiv Inc. (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/101832/
Past meetings: https://bugs.ruby-lang.org/projects/ruby/wiki#Developer-Meetings

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.6 timeframe

## Carry-over from previous meeting(s)

## From Attendees

- [Feature #14839] How to deal with capitalizing Georgian in Unicode 11.0.0 (duerst)
    - I need feedback on this to be able to implement in in time for the Ruby 2.6 release.
- [Feature #15195] How to deal with new Japanese era (duerst)
    - We should prepare early (even if it's just to check that we need to do nothing)
- How to address increasing spam to the bug tracker. (duerst)
    -  #15212/#15213 are just two examples. They get removed (return a 404), which is good. But they reach the mailing list and its subscribers, which is a problem. Prefiltering bugs with URIs in titles seems to be a good start.
- [Misc #14632] [ANN] git.ruby-lang.org (hsbt)

## From non-attendees

- [Feature #15123] Enumerable#compact proposal (greggzst)
    - It simplifies working with large and small collections so one doesn't have to remember that can't use #compact when enumerator is returned and have to fall back to #reject(:nil?).
- [Feature #15112] Introduce the new singleton method STDERR.p (shevegen)
    - I am mostly curious what the ruby core team thinks about Kenta Murata's proposal; it probably will not take too much time away discussing it briefly, since the scope is small.
# Comment format
- [Feature #11505] Module#=== should call #kind_of? on the object rather than rb_obj_is_kind_of which only searches the ancestor heirarchy (rafaelfranca)
    - This would allow patterns as Decorator and Proxy to work with case statements.
- [Feature #14912] Introduce pattern matching syntax (greggzst)
    - Many modern languages have introduced pattern matching. I used it in scala and found it very easy to utilize and understand especially in recursion. It makes extracting data easier as well.
- [Feature #15144] Enumerator#chain (zverok)

@zverok wants to hear about stale issues:
> I am not sure if it is appropriate, but I'd also be very glad to hear about some "stale" discussions. They were typically reacted on developer meetings as "in general, good proposal (but not sure when it would be implemented/not sure about the name)", or something like that, and I'd like to know maybe we should do something to push them further? List of tickets:

- [Feature #14799] Startless range: usefulness discussed, patch provided by mame (Yusuke Endoh), waits for Matz's decision (?)
- [Feature #14784] Comparable#clamp accepting range: comment for akr (Akira Tanaka) about "needlessly big" proposal, I answered it, is it makes the proposal more likely to be accepted?
- [Feature #6284] Composition for procs: the last thing Matz has said is the operators are chosen (<< and >>), and "We need more discussion if we would add combination methods to the Symbol class." Is there a chance proc composition would make it way in the 2.6?
- [Feature #13581] Syntax sugar for method reference. The last thing Matz have said is: ".: looks best to me (followed by :::). Let me consider it for a while." Are there any choices made? Could we expect this for 2.6?
- [Feature #14781] Enumerator#generate It seems like people feel cautious enthusiasm about it, but not sure about the name. What should be done here? Voting on the name? Providing the patch with some name, and then voting for the name?

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

- Nov. 22 (Thu) @pixiv Inc. 6F studio

## About 2.6 timeframe

- Nobu’s task (show causes in backtrace) is blocker of preview3.
- mswin with MSVC14+ (only 14?) doesn’t work with Windows 10 October 2018 Update (version 1809) because of ucrtbase.dll’s internal \_\_pioinfo structure.

## Carry-over from previous meeting(s)

- It seems no carry-over issue was there.

## From Attendees

- ## \[Feature [#14839](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14839&sa=D&source=editors&ust=1686088679122674&usg=AOvVaw2bQQy5J7FdfrrgTG3UxxMO)\] How to deal with capitalizing Georgian in Unicode 11.0.0 (duerst)


- ## I need feedback on this to be able to implement in in time for the Ruby 2.6 release.

- usa: What about keep things as is, to make Georgian people aware of the issue
- duerst: Georgian characters can be detectable so theoretically it’s possible to have complete mapping of characters.

- ## \[Feature [#15195](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15195&sa=D&source=editors&ust=1686088679123584&usg=AOvVaw1K4og-kfBz0kHzDNaeBvDc)\] How to deal with new Japanese era (duerst)


- ## We should prepare early (even if it's just to check that we need to do nothing)

- duerst: When I experienced Showa -> Heisei transition, I started using AD.
- duerst: the new character itself can be handled without problem.  The problem, if any, is the decomposition of the character.  Also regexp.
- nobu: What to do then?
- usa: patch release.
- duerst: What about older versions? Backport?
- naruse: If Unicode 12 could be released until 2.6.1, I think I can release 2.6.1 with Unicode 12.

- ## How to address increasing spam to the bug tracker. (duerst)


- ## #15212/#15213 are just two examples. They get removed (return a 404), which is good. But they reach the mailing list and its subscribers, which is a problem. Prefiltering bugs with URIs in titles seems to be a good start.

- hsbt: Efforts ongoing.
- (details omitted from the log)

- \[Misc [#14632](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14632&sa=D&source=editors&ust=1686088679125253&usg=AOvVaw2BjIHPqkpZ0sBVCytCaMRN)\] \[ANN\] git.ruby-lang.org (hsbt)

- hsbt: I want to switch to git in 20 Oct.
- usa: what happens?
- hsbt: would like to forbid checking into the subversion repo.
- usa: Revision number is needed for backporting process.  I have to reroute.
- mame: Any restriction on git?
- hsbt: push --force shall be forbidden.

- \[Feature [#14609](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14609&sa=D&source=editors&ust=1686088679126066&usg=AOvVaw2lnNSBGj4AgEvocyumH5NQ)\] \`Kernel#p\` without args shows the receiver (ko1)

- It will be useful.

- Pros.

- For debug reason, it should be shorter name. Adding methods should be small as possible

- Cons.

- Compatibility（p #=> nil）→ 別名派

- Golf (shorter name of ‘nil’)
- “ruby -ep” prints “main” (when we run ‘ruby -we0’,it shows warning so we want to use ‘-ep’)
- For compatibility, Special logic to detect “p” or “obj.p” calling style to detect p #=> error or obj.p

- What happen? on: obj.send(:p, \*args)

- It feels strange: p(\*\[\]) #=> nil
- It should be fancy to detect by eyes (.p is easy to hide) -> different name

- knu: What if Object#display returned self? It does not emit a trailing LF.
- Method name candidates

- .tapp, .tappp
- .tapp(p: true)
- .tapp(pp: true)
- .P, .PP
- .disp, .dispp

- It’s okay if the new method clashed with existing methods; it wouldn’t break compatibility anyway. However, if it conflicted with a popular library, it’d damage the usefulness.

## From non-attendees

- ## \[Feature [#15123](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15123&sa=D&source=editors&ust=1686088679128709&usg=AOvVaw2cTGv7ZudKnUpc3XJNQyEP)\] Enumerable#compact proposal (greggzst)


- ## It simplifies working with large and small collections so one doesn't have to remember that can't use #compact when enumerator is returned and have to fall back to #reject(:nil?).

- usa: What does it return?
- mrkn: Returns another enumerator.
- matz: Use case not very clear to me.

- ## \[Feature [#15112](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15112&sa=D&source=editors&ust=1686088679129838&usg=AOvVaw3knTOOjSSYwngR-x4IBwz-)\] Introduce the new singleton method STDERR.p (shevegen)


- ## I am mostly curious what the ruby core team thinks about Kenta Murata's proposal; it probably will not take too much time away discussing it briefly, since the scope is small.

- shyouhei: This one conflicts with [#14609](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14609&sa=D&source=editors&ust=1686088679130477&usg=AOvVaw1ndxk_xT0ZOVkbwtggTqEg)
- mame: Does it really conflict with that? Does anyone bother inspecting STDERR?
- taru: Matz wants tapp for the other ticket.
- duerst: I propose #warn\_p.
- matz: I understand the needs for this one, but…
- usa: Honestly I want to have #p for all IOs
- naruse: Yes, like for logging.
- mame: Doesn’t it make people leave such debug outputs here and there?
- akr: What about introducing it with a longer name than #p
- usa: like #putp (\`p\` means %p)
- matz: OK, feature-wise I accept. For name…?
- ko1: “IO#puts\_inspect” ?
- akr: I think STDERR.p is acceptable but IO#p is too short.
- mame: same feeling.

- ## \[Feature [#11505](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11505&sa=D&source=editors&ust=1686088679131882&usg=AOvVaw0DJxaVuFWbTMyBIFg3ylDm)\] Module#=== should call #kind\_of? on the object rather than rb\_obj\_is\_kind\_of which only searches the ancestor hierarchy (rafaelfranca)


- ## This would allow patterns as Decorator and Proxy to work with case statements.

- mame: I don’t understand the use case shown in the issue.
- matz: I don’t think it works because the receiver of === is the class, not the object.
- matz: If we allow decorator pattern in case statement we need a sort of coercing.
- ko1: maybe use-case is here:

class Module

 def === other

 other.kind\_of?(self)

 end

end

class ArrayLike

 def kind\_of? other

 if other == Array

 true

 else

 super

 end

 end

end

case ArrayLike.new

when Array

 p :Array

end

- ## \[Feature [#14912](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14912&sa=D&source=editors&ust=1686088679134393&usg=AOvVaw144dZgFkft9EebgHKGHjsJ)\] Introduce pattern matching syntax (greggzst)


- ## Many modern languages have introduced pattern matching. I used it in scala and found it very easy to utilize and understand especially in recursion. It makes extracting data easier as well.

- shyouhei: Any updates?
- mame: We should ask the status for @k-tsj

- ## \[Feature [#15144](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15144&sa=D&source=editors&ust=1686088679135194&usg=AOvVaw2AnwhuYwKQuW-K-aX_mUEJ)\] Enumerator#chain (zverok)


- knu: Understand the needs. Name?
- akr: Why not Enumerable#+
- knu: We talked about this feature a few weeks ago, and concluded Enumerator#+ would be a good addition.  We might want to have a constructor that takes many enumerators.
- matz: I don’t like Enumerable#chain naming.
- ko1: Is this method destructive?
- akr: How can it be destructive?
- ko1: \`Enumerator.chain( e1, e2, ... )\` is considerable, but should be discussed in the different ticket.
- matz: + is acceptable but maybe difficult to method chain.
- usa: e0.+(e1, e2, ...).bar.baz.. might suffice.
- matz: maybe.
- ko1: should be joke for +(...)
- knu: I’ll be working on this in the forthcoming hackathon held on Oct 20.

## [zverok (Victor Shepelev)](https://www.google.com/url?q=https://bugs.ruby-lang.org/users/710&sa=D&source=editors&ust=1686088679136691&usg=AOvVaw0Ab3wUHbjscl-pwAh5d2tl) wants to hear about stale issues:

## I am not sure if it is appropriate, but I'd also be very glad to hear about some "stale" discussions. They were typically reacted on developer meetings as "in general, good proposal (but not sure when it would be implemented/not sure about the name)", or something like that, and I'd like to know maybe we should do something to push them further? List of tickets:

- ## \[Feature [#14799](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14799&sa=D&source=editors&ust=1686088679137240&usg=AOvVaw1BSPXlfNqL-ZwF_1tL8V4b)\] Startless range: usefulness discussed, patch provided by mame (Yusuke Endoh), waits for Matz's decision (?)


- mame: I can have 2.5 without it.
- matz: At least I want to force parens (..foo)
- mame: What about right after when?
- matz: Mmm…. That’s difficult.

- ## \[Feature [#14784](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14784&sa=D&source=editors&ust=1686088679138176&usg=AOvVaw0EQnfP5CGnYujJS11wowWM)\] Comparable#clamp accepting range: comment for akr (Akira Tanaka) about "needlessly big" proposal, I answered it, is it makes the proposal more likely to be accepted?


- Nobody is against #clamp accepting ranges.  The issue did not seem requesting that, rather it was for one-sided clamp.

- ## \[Feature [#6284](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6284&sa=D&source=editors&ust=1686088679138785&usg=AOvVaw30xn7FVqiBRSttTaThEcXt)\] Composition for procs: the last thing Matz has said is the operators are chosen (<< and \>>), and "We need more discussion if we would add combination methods to the Symbol class." Is there a chance proc composition would make it way in the 2.6?


- usa: This has been accepted. (at [https://bugs.ruby-lang.org/issues/6284#note-55](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6284%23note-55&sa=D&source=editors&ust=1686088679139291&usg=AOvVaw3uKEYU9R4IM6vkIsf9ZVKF))
- ko1: @nobu is assigned.  Waiting for him to implement.

- ## \[Feature [#13581](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13581&sa=D&source=editors&ust=1686088679139760&usg=AOvVaw3NViQCG-e7amXwSzn_h54b)\] Syntax sugar for method reference. The last thing Matz have said is: ".: looks best to me (followed by :::). Let me consider it for a while." Are there any choices made? Could we expect this for 2.6?


- matz: Honestly I still don’t find the right name.
- ko1: Do we want to encourage programmers to take method object by this?
- shyouhei: I think this issue is to provide a syntax, rather a method.
- ko1: I don’t think that matters.
- akr: Yes it is.
- ko1: It might matter you, but not for the OP.
- shyouhei: Maybe.

- ## \[Feature [#14781](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14781&sa=D&source=editors&ust=1686088679140744&usg=AOvVaw3CDIYP3AMZiqrMegQgYH80)\] Enumerator#generate It seems like people feel cautious enthusiasm about it, but not sure about the name. What should be done here? Voting on the name? Providing the patch with some name, and then voting for the name?


- Everybody is against #generate.
- duerst: We don’t vote. Or in other words, we have only one person who votes. That’s Matz. Everybody else is very welcome to provide ideas, opinions,...
- knu: There are many kinds of use cases mixed in one proposal, which need to be sorted out.  How many previous elements to look back, how to define an end of a sequence, etc.          I’ll come up with an alternative concrete API proposal.

- \[Feature [#12490](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12490&sa=D&source=editors&ust=1686088679141505&usg=AOvVaw1KjyxvKVFdUIRkZVRwqS9M)\] Remove warning on shadowing block params

- mame: It was introduced when backward incompatibility was involved, but it is no longer a problem, so why not remove it?
- knu: It’s painful when you have to change this just to silence the warning: user = users.find { |user| … }
- matz: I still think it’s not preferable to pick a conflicting name, but I understand your pain. Approved.
