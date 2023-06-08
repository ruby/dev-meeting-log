---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-08-31

* Date: 2013-08-31
* Time: 13:00-18:00
* attendees: see http://cruby.doorkeeper.jp/events/5117
* 議事録: https://docs.google.com/document/d/1qRJ0I5Iq06x7SRovzZ1_GSxLe9Raw1d4hBYDX7gBTHg/edit?usp=sharing

## Agenda

### Ruby 2.1 release schedule

* [[ruby-master:ReleaseEngineering210]]
* When to release preview1?
* misc #8835    Introducing a semantic versioning scheme and branching policy (knu)

### misc

* misc #8843: ChangeLog のエンコーディングを UTF-8 に変更しませんか (kosaki)

### Features of Ruby 2.1

* [ruby-core:56824] [ruby-trunk - Feature #8823][Open] Run trap handler in an independent thread called "Signal thread"
  * 手を付けない (Queue が使えるようになってちょっと便利になる)
* Feature #6373    Object#self (marcandre)
  * itself は妥協できる (by matz)
* Feature #7292    Enumerable#to_h (marcandre)
  * matz が返事をする
* [ruby-core:56824] [ruby-trunk - Feature #8823][Open] Run trap handler in an independent thread called "Signal thread"
  * 手を付けない (Queue が使えるようになってちょっと便利になる)
* Feature #6373    Object#self (marcandre)
  * itself は妥協できる (by matz)
* Feature #7292    Enumerable#to_h (marcandre)
  * matz が返事をする
* Feature #8840    Yielder#state (marcandre)
  * matz が返事をする
* Feature #8842    Integer#[] with range (mame)
  * accept
* Feature #8026    Module#prepended_modules (marcandre)
  * matz が聞く
* Feature #2509    Object#deep_freeze (marcandre)
  * reject
* Feature #7274    UnboundMethod#bind (marcandre)
  * reject
* Feature #8846    Publicize Module#include (a_matsuda)
  * accept
* Feature #8657    Make Find.find respect the encodings of arguments (ktsj)
  * accept
* Feature #8696    Process.setproctitle and Process.argv0 (knu)
  * accept
* Feature #8579    Frozen string syntax (charliesome)
  * accept (suffix)
* Feature #8809    Process.clock_getres (akr)
  * accepted
* Feature #8700    Integer#bit_length (akr)
  * accepted (two complement)
* Feature #8738    Integer#single_bit? (akr)
  * reject (low priority)
* Feature #8748    Integer#popcount (akr)
  * reject (low priority)
* Feature #8796    Use GMP to accelerate Bignum operations (akr)
  * accept
* Feature #3620    Add Queue, SIzedQueue and ConditionVariable implementations in C in addition to ruby ones (ko1)
  * conditional accept
* Feature #8838    Enhancing Numeric#step (knu)
  * accept
* Feature #8804    ONCE syntax
  * reject
* [ruby-dev:47613] [ruby-trunk - Feature #8779][Open] Binding#yourself
  * accepted: Binding#receiver

### w.r-l.o redesign

* https://github.com/ruby/www.ruby-lang.org/issues/282

### Announcement

* Heroku supports ruby-core (ayumin)
