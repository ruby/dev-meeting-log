---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-12-07

Date: 2015/12/07 (Mon)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.3 based on tickets.
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

* example: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* Report about experimental ISeq loader (ko1)
* Dreams if we have a budget (ko1)
* [Feature #9098] tabs in indented heredoc (nobu)

## From non-attendees

* [Feature #11777] Change `NameError#local_variables` to return the list of local variables where the method is raised (Yuki Nishijima & ko1)
* [ANN] Replace infrastructure of svn.ruby-lang.org at 2015-2016. I will build new server at AWS tokyo region using Debian Jessie.

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendees: matz (skype), nobu (skype), naruse, sorah, ko1, duerst, zunda

Next meeting
2016/01/18 (Mon)

[Feature #11777] Change NameError#local_variables to return the list of local variables where the method is raised (Yuki Nishijima & ko1)
Matz: Accepted. Please note that it should be internal use. Documents should say that.

[ANN] Replace infrastructure of svn.ruby-lang.org at 2015-2016. I will build new server at AWS tokyo region using Debian Jessie. (hsbt)
Shared.

Report about experimental ISeq loader (ko1)
Matz: Accepted to introduce to Ruby 2.3 as experimental feature.

Matz: naming is important.

Dreams if we have a budget (ko1)
ko1: mac mini was provided by Yasulab via Ruby no Kai. Any other ideas?

nurse: VPS is welcome.

nurse: Azure.

ko1: travel fee (1,000,000 JPY?) for hackathon in same place.

ko1: physical machines for benchmarks (300,000 JPY)

nobu: development machine (400,000 JPY)

nurse: icc (and other softwares)

martin: grant

ko1: education to grow other MRI developer

-> summarise, make ticket, and show at RubyKaigi


Minor: add cygruby230.def to .gitignore (martin)
nobu: approved

[Feature #9098] tabs in indented heredoc
matz: choose 2.

check open tickets https://bugs.ruby-lang.org/projects/ruby-trunk/issues

