---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-02-07

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/02/07 (Thu)
Time: 13:00-17:00 (JST)
Place: MoneyForward.com (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/117659/

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

* [Feature #15554] warn/error passing a block to a method which never use a block (ko1)
  * I want to share the ticket and survey on some product.

## From non-attendees

* [Feature #6470] Make attr_accessor return the list of generated method (eregon)
  * I think not all related issues were considered. There are use cases, both for private attr_* and other modifiers (e.g., volatile, atomic, tracing accesses, getter/setter decorators, etc).
* [Bug #14353] $SAFE should stay at least thread-local for compatibility (eregon)
  * I would like matz's opinion on this. It seems the compatibility risks and debugging problems is not worth making $SAFE global, unless there is a good reason to be global? Should there still be $SAFE checks at all, for e.g., tainted Strings?
* [Feature #5653] "I strongly discourage the use of autoload in any standard libraries" (eregon)
  * It would be useful to reach a decision for this. What does it gain, and at what cost for compatibility? Is there going to be a replacement?


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

- 3/11 (Mon) 13:00~17:00 @MoneyForward (Minato-ku) [https://ruby.connpass.com/event/120056/](https://www.google.com/url?q=https://ruby.connpass.com/event/120056/&sa=D&source=editors&ust=1686088785981892&usg=AOvVaw0T7mN0poF3uTgIIWcm55Yl)
- 4/17 (Wed) 12:00~ @ Fukuoka (before RubyKaigi)

## About 2.7 timeframe

- 2.6.2 after Unicode 12.0 (Mar.)?

## Check security tickets (confidencial)

- discussed

## Carry-over from previous meeting(s)

- It seems no carry-over issue was there.

### Handling issues on Developer Meeting Ticket

- Don’t copy commented issues to description to avoid missing comments

## Non-attendees

- \[Feature [#6470](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6470&sa=D&source=editors&ust=1686088785983493&usg=AOvVaw1PdMl13KoY_EDVRagTkDDC)\] Make attr\_accessor return the list of generated method (eregon)

- What’s volatile?  No one understand.
- Matz: if volatile is needed, it would be better to define volatile\_attr\_reader etc.

- \[Bug [#14353](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14353&sa=D&source=editors&ust=1686088785983920&usg=AOvVaw1wZ0AoegBq2upgDAxadJtQ)\] $SAFE should stay at least thread-local for compatibility (eregon)

- I would like matz's opinion on this. It seems the compatibility risks and debugging problems is not worth making $SAFE global, unless there is a good reason to be global? Should there still be $SAFE checks at all, for e.g., tainted Strings?
- Naruse: $SAFE should be removed
- Matz: Make $SAFE no effect in 2.7 or 3.0

- \[Feature [#5653](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/5653&sa=D&source=editors&ust=1686088785984400&usg=AOvVaw3uFbyJJ7z2Z1T7-bSWngqx)\] "I strongly discourage the use of autoload in any standard libraries" (eregon)

- It would be useful to reach a decision for this. What does it gain, and at what cost for compatibility? Is there going to be a replacement?
- (investigated the circumstance and history of Rails…)
- Matz: keep autoload (for a while)? Seems impossible to remove it now.  May be removed in 4.0 :-)

- \[Feature [#14344](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14344&sa=D&source=editors&ust=1686088785984931&usg=AOvVaw2Mp8dfwGJY7liX9Dr_2FzK)\] refine at class level (kddeisz)

- Shorter refinement syntax proposed based on common use cases. Proposed either refine\_class or using blocks for shorthand. What do you think?
- Matz: I understand the needs, but I don’t like the style.  Please propose other styles.

- \[Feature [#14680](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14680&sa=D&source=editors&ust=1686088785985502&usg=AOvVaw0oRIOx89LW09Y57IVu7vqw)\] Adding +@ and -@ to hash and array (kddeisz)

- Since we have -@ and +@ for strings and it's very useful I'd like to propose adding the same API to hash and array.
- Urabe: It is arguable that -@ reads better than .freeze
- Matz: I don’t see the strong reason to add

- \[Feature [#15567](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15567&sa=D&source=editors&ust=1686088785985999&usg=AOvVaw0KGNvt-nz2jlz0MEzAdkky)\] Allow ensure to match specific situations. (ioquatix)

- Matz: wait real-world use case
- Mame: I don’t want to see a strange API that depends the difference between throw and break.

- \[Feature [#15560](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15560&sa=D&source=editors&ust=1686088785986447&usg=AOvVaw3B8WWcBJU9SVlKqxNwFgfx)\] Add support for read/write offsets. (ioquatix)

- Can we get consensus on whether we can add this?
- Doesn’t the current Ruby have this feature? … Check later.
- Mame: There seems to be no feature for that (surprisingly).  Cannot we use StringIO for the use case?
- Znz: IO#pread?
- Mrkn: How about a view (or slice) of a string?
- Matz: Let’s discuss it the next meeting.

- \[Feature [#10098](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10098&sa=D&source=editors&ust=1686088785987121&usg=AOvVaw39R0RCNWZFI52yM8i1_53n)\] Timing-safe string comparison for OpenSSL::HMAC (bdewater)

- The feature is done (the title is old, it will be implemented in String) but blocked on picking a method name. I would like to ask for opinions so this can move forward.
-  ???

- \[Feature [#15331](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15331&sa=D&source=editors&ust=1686088785987694&usg=AOvVaw2-O25QGfrjvxegzNpuWa5Z)\] \[PATCH\] Faster hashing for short string literals

- Looking for feedback on this patch.
- Nobu: I tested, but cannot confirm the speed up
- Matz: Umm.

- \[Feature [#15374](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15374&sa=D&source=editors&ust=1686088785988155&usg=AOvVaw3QRqfdtSZ8EjLWR0kHX-vf)\] Proposal: Enable refinements to #method\_missing

- waited one month.
- Matz: will reject.

- \[Feature [#14609](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14609&sa=D&source=editors&ust=1686088785988531&usg=AOvVaw0SXOh4ZEiUOe5AOEmKmKZZ)\] Kernel#p without args shows the receiver (osyo)

- I feel this feature is very useful and some people say :+1: so let discuss about this feature.
- Need to discuss when we have ko1.  He left, so discuss it at the next meeting.

- \[Feature [#15588](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15588&sa=D&source=editors&ust=1686088785988995&usg=AOvVaw03lNnRHutJsehMUK5ehfFh)\] String#each\_chunk and #chunks

- Looking for feedback on this patch.
- Matz: use case.

## From Attendees

- \[Feature [#15554](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15554&sa=D&source=editors&ust=1686088785989657&usg=AOvVaw2NCvhcWoJr0dNSuWYXagqT)\] warn/error passing a block to a method which never use a block (ko1)

- I want to share the ticket and survey on some product.
- Ko1: It requires many fixes, but it actually found some real bugs.
- Matz: feels that it is less significant comparing to compatibility issue.
- Matz: deprecated the feature itself.  Replied.

- \[Feature #5653\] "I strongly discourage the use of autoload in any standard libraries"

- knu: Autoloading itself is a feature that you can't miss in application development.  Especially in Rails, it takes a very long time to load all ActiveRecord model files eagerly because each one of them accesses the database to load the table schema, and it'd be painful to have to load everything just to run, for example, a single unit test.
- Matz: OK, I give up. Autoload will stay, at least until before Ruby 4.x.

- \[Feature [#15581](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15581&sa=D&source=editors&ust=1686088785990428&usg=AOvVaw30uAyGhZC4h8gyr4UZGb53)\] Split tool/\* files to tool and script directories

- What's a concern about this?

-  \[Feature [#4475](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4475&sa=D&source=editors&ust=1686088785990909&usg=AOvVaw27xlk3LKzX1DQQQCEaoCwh)\] default variable name for parameter (aycabta)

- In [#15483](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15483&sa=D&source=editors&ust=1686088785991192&usg=AOvVaw2bmf2CIJGOgSPuDqgsj9hC), matz said "I feel the expression ary.map(&(:to\_i << :chr)) is far less readable than ary.map{|x|x.to\_i.chr}.", and "And this can lead to the default block parameter like it."
- nobu is wriing a patch for \`@1\`. so, let’s try it.

- \[Feature [#5632](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/5632&sa=D&source=editors&ust=1686088785991758&usg=AOvVaw2yDl8-zc2shq5i1UtP4aPt)\] Attempt to open included class shades it instead. (mame)

- Matz: sorry, it was not mame’s task. reject it.

- \[Feature [#11076](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11076&sa=D&source=editors&ust=1686088785992194&usg=AOvVaw3pLnbJZrj2FEJ5vklFg538)\] Enumerable method count\_by

- Matz: ok, introduce \`tally\`.

- \[Feature [#15573](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15573&sa=D&source=editors&ust=1686088785992578&usg=AOvVaw2u5jvQDkSE0svJtZipOH6U)\] Permit zero step in Numeric#step and Range#step

\>> (10 .. 0).step(-1).to\_a

\=> \[10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0\]

 (0 .. 0).step(0).to\_a loops infinitely
