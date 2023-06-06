---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-03-11

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/03/11 (Mon)
Time: 13:00-17:00 (JST)
Place: MoneyForward.com (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/120056/

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

We don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

## Next dev-meeting

- 4/17 (Wed) 12:00~ @ Fukuoka (before RubyKaigi)
- [https://bugs.ruby-lang.org/issues/15459](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15459&sa=D&source=editors&ust=1686088807813014&usg=AOvVaw33GYZsSI8JRkgjBmvEg_J6)

## About 2.7 timeframe

- 2.6.2 after Unicode 12.0 (Mar.)?
- 2.6.3 (Apr)
- 2.7.0-preview1u89

- waiting introducing \`@1\`. maybe at RubyKaigi

- [https://bugs.ruby-lang.org/issues/4475](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4475&sa=D&source=editors&ust=1686088807814126&usg=AOvVaw0o9b28jc1FMBO7H_M80jNn)
- matz: I deny \`@0\`, and also deny assignment to \`@1\`

- \[\[1,2,3\]\].each {|a,| p a }
- →→ [https://hackmd.io/eb38KJgKQEeGEgsbSACUWg](https://www.google.com/url?q=https://hackmd.io/eb38KJgKQEeGEgsbSACUWg&sa=D&source=editors&ust=1686088807815060&usg=AOvVaw3sox1EoIqNiSX51QJ2IIGo)

## Check security tickets (confidencial)

done

## Carry-over from previous meeting(s)

- \[Feature [#14609](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14609&sa=D&source=editors&ust=1686088807816269&usg=AOvVaw1Lso0aFxPXY6p2PFqchgG0)\] Kernel#p without args shows the receiver (aycabta)

- the name of the method is not determined yet.

### Handling issues on Developer Meeting Ticket

- Don’t copy commented issues to description to avoid missing comments

## Non-attendees

- \[Feature [#14799](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14799&sa=D&source=editors&ust=1686088807817656&usg=AOvVaw0eJLK1wzZe4nKEvT5wc7V9)\] Startless range

- mame: I’ve forgotten to commit.  just a moment.

- \[Bug [#15620](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15620&sa=D&source=editors&ust=1686088807818306&usg=AOvVaw1kG5EHCt9IcOrWGVl10V1x)\] Block argument usage affects lambda semantic

- nobu: yes, it’s a bug.

- \[Misc [#15617](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15617&sa=D&source=editors&ust=1686088807818847&usg=AOvVaw1ZOXNQuyq4m-K4TPc-5Lkb)\] Release 2.5.4? (blowmage)

- usa: nagachika-san is planning to release in this month.

- \[Feature [#15323](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15323&sa=D&source=editors&ust=1686088807819427&usg=AOvVaw3Ffx7IeBOdb-IYLB1S1lTG)\] Proposal: Add Enumerable#filter\_map

- naruse: Sequel uses \`select\_filter\` as another meaning
- usa: also need \`select\_map\` as alias?
- matz: so, \`select\_collect\`? :)
- mame: is lazy not enough?
- mame: if this method is accepted, other methods with map will be also requested.
- matz: filter\_map is not simply combination of filter and map.  so mame’s concern is needless fears.
- matz: ok, accepted \`filter\_map\`.

- \[Feature [#14145](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14145&sa=D&source=editors&ust=1686088807821271&usg=AOvVaw19TZLc6Un_f7wkINwVEvBj)\] Proposal: Better Method#inspect

- ko1: I’m against to show the name of parameters.  we only need line no.  doesn’t it?
- matz: ask the original author.
- ko1: I’ll ask.
- ko1: BTW, how do you think about this opinion?
- all: hmm…
- naruse: isn’t it good showing both parameters and line no?
- ko1: isn’t it too long?
- naruse: path name is already long.
- ko1: ah, may be so.  but source location is useful than parameters.

- \[Feature [#15653](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15653&sa=D&source=editors&ust=1686088807822552&usg=AOvVaw1lFzrLjvR7z8WZaj8HaRbI)\] Proposal: Add Time#floor

- naruse: isn’t it \`trunc\`?  see SQL.
- akr: truncate means rounding to zero.  conceptually, zero is the first time, but we often assume the zero time as UNIX epoch.
- naruse: I see.
- mrkn, knu: There’s a typical use case in test code in Rails.  Sub-second fragment often gets lost through serialization/deserialization for DB and we often do Time.at(time.to\_i) in test code as a workaround.
- matz: ok, accepted.

## From Attendees

- \[Misc [#15615](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15615&sa=D&source=editors&ust=1686088807823855&usg=AOvVaw3L0yJFGl4vvgDCDBK_4SP5)\] File.birthtimeがLinux環境で有効なファイル作成時刻を得られなかった場合の挙動について

- should raises \`NotImplementedError\` both \`File.birthtime\` and \`File::Stat#birthtime\` if birthtime is not available.
- akr: \`respond\_to?\` should always return \`true\`.
- akr: we also have to implement \`birthtime\` to \`Pathname\`, don’t it?
- usa: yes

- \[Feature [#15195](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15195&sa=D&source=editors&ust=1686088807825181&usg=AOvVaw18hIx4cQMyqIFeAcjqeQ9j)\] How to deal with new Japanese era

- martin: just a reminder.
- naruse: I’m planning to release 2.6.2 sooner.  and 2.6.3 will be released at Apr.
- martin: ok

- \[Bug [#15598](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15598&sa=D&source=editors&ust=1686088807825845&usg=AOvVaw3HxZNx9mrlaizCXJ9OsDXz)\] Deadlock on mutual reference of autoloaded constants

- (see next)

- \[Bug [#15599](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15599&sa=D&source=editors&ust=1686088807826382&usg=AOvVaw0FC4M1IbKveX8VLd-OJICo)\] Mixing autoload and require causes deadlock and incomplete definition.

- akr: IMO, these are bugs.  I propose that autoload should use one global lock instead of locks per constants.

- \[Feature [#15618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15618&sa=D&source=editors&ust=1686088807827068&usg=AOvVaw1YxYIUKCrY0nOrtjUlAsM_)\] Implement Enumerator::Yielder#to\_proc

- matz: ok, accepted.

- \[Feature [#15553](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15553&sa=D&source=editors&ust=1686088807827592&usg=AOvVaw09m8pL3nElUSPkY8PmJSjE)\] Addrinfo.getaddrinfo supports timeout

- akr: this implementation is not acceptable, but the feature is ok.
- glass\_saga: I see.  BTW, is using Resolv acceptable?
- akr: yes.
- Even if it doesn’t support getaddrinfo with timeout (non glibc), it should ignore because we want to use the same code on macOS and Linux.

- \[Bug [#15652](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15652&sa=D&source=editors&ust=1686088807828439&usg=AOvVaw0jqIkEMEGjBnSaD0J0gL3v)\] Profiler\_\_ is not working correctly (ruby 2.6)

- usa, naruse: kill it

- \[Feature [#15626](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15626&sa=D&source=editors&ust=1686088807828930&usg=AOvVaw0CjjyDjrMBPfxpPr2TXR48)\] Manual Compaction for MRI’s GC (\`GC.compact\`)

- matz: current status?
- ko1: I think this is mergeable.  But one incompatibility exists.   if an C extension keeps pointers without mark (expecting to mark via Ruby code), GC cannot change the pointers.
- shyouhei: for debugging, set moved pointer as reserved and causes SEGV if the reserved pointer is accessed.
- matz: ok, go ahead
