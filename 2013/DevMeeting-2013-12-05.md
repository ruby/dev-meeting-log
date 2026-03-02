---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-12-05

* When: Friday, December 5, 2013 19:00-21:00 JST
* Where: Tokyo, Japan
* How: In person, streaming live, and irc @ [irc.freenode.net#ruby-core-meeting](irc://chat.freenode.net/#ruby-core-meeting)

<em>Update</em>: A full summary of the meeting can be found at the bottom of this page.

## Attendees

* hsbt

### Venue (in-person)

* zzak
* sorah
* mrkn
* naruse
* ko1
* matz

### IRC-only

* Add your irc handle below if you wish to participate
* Moderator(s) will be indicated with a (m) after their handle
* Translator(s) will be indicated with a (t) after their handle
* n0kada
* unak
* hone
* tarui
* nobu
* hsbt

## Agenda

* 2.1 roadmap and schedule
* 2.1.0 branch maintainer
* \>= 2.1 maintenance policy #9215
* Versioning Policy after Ruby 2.1
  * #8835
  * http://d.hatena.ne.jp/nurse/20131111#1384136384
  * https://gist.github.com/hsbt/7719305
  * https://gist.github.com/sorah/7803201
* Maintenance policy for Ruby 1.8.7 and 1.9.2
  * #9216
* 1.9.3 EOL timeline and maintenance policy
  * #9217
* Policy announcements of all versions (1.8.7, 1.9.2, 1.9.3, 2.0.0, 2.1.0)
  * #9219
* Maintainer policy for appointment and discharge
  * #9218
* GenGC / RGenGC documentation #8962
* Error hiding #7688
* rurema migration (pending okkez or ktou attendance)
* ADD AGENDA HERE

## Streaming

http://www.ustream.tv/channel/mrkn-live

## IRC Log

* Brief Summary of discussion on skype/irc https://gist.github.com/zzak/7821769
* The video http://www.ustream.tv/recorded/41393753

## Summary

Ruby-core met twice last week in Japan to discuss the future plans for releasemanagement and backport maintenance of Ruby.

Season's greetings everyone!

You can watch the recording from Thursday's meeting on[ustream](http://www.ustream.tv/recorded/41393753), thanks to[@mrkn](https://twitter.com/mrkn) for the recording!

Although we didn't have a video recording from the meeting on Friday, werecorded our skype conference and is available on[soundcloud](https://soundcloud.com/zzak-2/ruby-core-developer-meeting). Thanksto [@sora_h](https://twitter.com/sora_h) for the recording and moderating skype!

Below I have summarized the meetings for each day and organized them intoagenda items. Each agenda item should have an associated ticket on our [bugtracker](http://bugs.ruby-lang.org/) that you can follow for more information.I will be updating these tickets with our conclusion shortly!

## 2013-12-05

Last Thursday several ruby-core committers, including Matz, met to discuss thefuture of Ruby. Here's the summary by topic:

### 2.1.0 Release Schedule

[Version 27 on bug tracker](http://bugs.ruby-lang.org/versions/27)

- We will see RC1 this week (2013-12-8 - 2013-12-14)
- RC2 is only necessary should a major issue be found on RC1
- A new branch will be created, and code freeze will be activated
- All bug fixes must be approved by the release manager ([@nalsh](https://twitter.com/nalsh))
- 2.1.0 will be released on christmas 2013 (2013-12-25)

### 2.1.0 Maintainer

[misc. #9215](http://bugs.ruby-lang.org/issues/9215)

- Undecided
- [@nalsh](https://twitter.com/nalsh) will maintain 2.1.0 for 2 months after release

### Semantic Versioning

[misc. #8835](http://bugs.ruby-lang.org/issues/8835)

Translations of this discussion are available here:

- [English](https://gist.github.com/sorah/7803201)
- [Japanese](https://gist.github.com/hsbt/7719305)

To summarize:

- We accept proposal of [@hsbt](https://twitter.com/hsbt)
- The following proposal will begin with 2.1.0

#### Version Schema `ruby-{MAJOR}.{MINOR}.{TEENY}`

- `MAJOR`: increased when incompatible change which can't be released in MINOR
- "MAJOR is for party", reserved for special events
- `MINOR`: increased every christmas, may be API incompatible
- `TEENY`: security or bug fix which maintains API compatibility
- `PATCH`: number of commits since last MINOR release (will be reset at 0 when releasing MINOR)

`TEENY` may be increased more than 10 (such as `2.1.11`), and will be releasedevery 2-3 months.

Matz and other attendees were concerned about `TEENY >= 10`.

#### Branches

We will maintain the following branches:

- trunk
- `ruby_{MAJOR}_{MINOR}`
- `ruby_{MAJOR}_{MINOR}` across `TEENY` releases
- tags will be used for each release

#### API Compatibility

- Removing API is an incompatible change
- such as (`STDIO_READ_DATA_PENDING`, etc.) must wait for 2.2.0
- an incompatible change must increase `MINOR`

#### ABI Compatibility

- Scheme of `{MAJOR}.{MINOR}.0`
- Keep ABI compatibility with same `MINOR` release, so `TEENY` is fixed at 0
- Retain compatibility across `TEENY` releases

We are concerned with changes required for existing tools, such as:

- svn
- backport / merger

#### Documentation

We need to announce our plans for removing patch level semantics, in order touse `{MAJOR}.{MINOR}.{TEENY}`.

### Maintenance Policy for 1.8.7 and 1.9.2

[misc. #9216](http://bugs.ruby-lang.org/issues/9216)

1.8.7 and 1.9.2 will be supported for security fixes until June 2014.

- [@hone02](https://twitter.com/hone02) and [@_zzak](https://twitter.com/_zzak) will assume maintainership
- After the 6 month maintenance period, we can add more committers to extend another 6 months.

In the past we have supported vendors who wish to maintain legacy versions. In2009 the maintenance of Ruby `1.8.6` was transferred to Engine Yard when theyreleased `1.8.6-p369`.

We were given permission from Matz to release backport versions of Ruby forsecurity fixes.

- It should be up to the maintainer
- Security releases only, even after EOL

We made an important decision on this policy on Friday, please read the summaryfor that day in the next major section.

### Maintenance Policy for 1.9.3

[misc. #9217](http://bugs.ruby-lang.org/issues/9217)

- 1.9.3 maintenance is commissioned by the Ruby Association
- It will receive security fixes until March 2014
- Additional 3 month security maintenance period may be supported by [@unak](https://twitter.com/unak)

### Maintenance Policy for Future Versions of Ruby

[misc. #9215](http://bugs.ruby-lang.org/issues/9215)

- 2.0.0 will received normal maintenance until February 2014 and 1 year of security maintenance
- Each MINOR release starting with 2.1.0 will receive 2 years normal maintenance and 3 years security support

We want to encourage a more routine process to improve user expectation andexperience.

It's encouraged that each release manager decide on a roadmap for theirversion, although an absolute schedule and maintenance plan is difficult.

### Policy Announcements

[misc. #9219](http://bugs.ruby-lang.org/issues/9219)

The [following pdf](https://bugs.ruby-lang.org/images/2013-12-05-ruby_maintenance_scheme.pdf) describesthe current maintenance plan for all versions. Thank you[@mrkn](https://twitter.com/mrkn) for creating this document!

We will offer a conclusive policy document for all versions during the announceof the 2.1.0 release.

I will publish this document shortly!

### Maintainer Appointment and Discharge

[misc. #9218](http://bugs.ruby-lang.org/issues/9218)

We would like to develop a process for adding or removing branch maintainers,in order to prevent sudden death of branches.

Should a maintainer become inactive, we can rely on the following procedures:

- nobu can help as global maintainer (patch monster)
- any committer is responsible for contributing to priority maintenance (such as security)
- we will decide the next maintainer during discussion on mailing list, and request their assignment

Anyone can replace the role of release manager, should they become inactive.

Every issue can escalate to Matz, should he become inactive maybe we need toreplace Matz.. (we should fire Matz!)

Asking to declare an expected maintenance period during appointment was rejected, see below:

[@nagachika](https://twitter.com/nagachika), current maintainer of `2.0.0` madethe following comment:

I never publish maintenance period for 2.0.0. I thought I can maintain it
for 2 years in "normal maintenance mode" and then turn into "security only
maintenance mode".
Now I feel a little hard to keep current pace of activity for maintenance
for 2.0.0.

It was suggested that [@unak](https://twitter.com/unak) may begin maintenance of2.0.0 in March 2014.

### GC Merits and Documentation

[misc. #8962](http://bugs.ruby-lang.org/issues/8962)

I requested Matz order Koichi-kun to write a document for extension authors, heapproved for the following reasons:

- Extension authors need a guide to implement Write Barrier
- Writing extensions using WB is difficult
- (nobu's children screaming loudly on skype)

However, encouraging the use of WB will cause serious problems. So Koichi willguide them to write extensions without using WB.

Since we only implement WB internally, an advanced extension author shouldrefer to the internal implementation of WB usage. Such examples could be Array,more details on this will follow in the development of this documentation.

Koichi adds, "its ok to be shady".

I will help with authoring this document.

### Error Hiding

[Feature #7688](http://bugs.ruby-lang.org/issues/7688)

We discussed this ticket, and concluded the following statements:

- The current status has improved in many cases, but it's not perfect
- Matz is undecided on the best way to improve performance
- Okay to experiment after 2.1.0 is released

### Various other discussion and events during Thursday

After watching the video I wrote a [roughsummary](https://gist.github.com/zzak/7821769), since most of thediscussion was in English. The following other events took place during themeeting:

- [@a_matsuda](https://twitter.com/a_matsuda) commits typo patches, he is the typo monster!
- [Martin Duerst commits too!](https://github.com/ruby/ruby/commit/737c7d8)
- [@hone02](https://twitter.com/hone02) joined the skype call to discuss his feedback on maintainer appointment and discharge
- [@a_matsuda](https://twitter.com/a_matsuda) will propose gemifying the stdlib at the next meeting

