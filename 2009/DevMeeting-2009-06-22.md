---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2009-06-22

* Place
  * Univ. of Tokyo, Akihabara, Tokyo, Japan
* Date
  * 2009/06/22 13:00-
* Attendees
  * matz (skype)
  * yugui
  * ko1
  * naruse
  * akr
  * nobu
* Purpose
  * 1.9.2 release plan
  * Toward RubyKaigi2009

# Agenda

* 残っている大きなバグを確認 (yugui)
* 今開発中の機能を確認 (yugui)
  * → クリスマスに1.9.2を出せそうかどうか (yugui)

Please write your name at the end of your topics such as -> (ko1).

# Log

Matz, ko1, shyouhei, akr, nobu, naruse and I held a meeting yesterday.
We decided a plan for Ruby 1.9.2.

== Schedule
17 Jul (RubyKaigi2009)
  * releases a patch level release of the 1.9.1.
  * releases 1.9.2 preview release 1.
25 Aug
  * 1.9.2 preview release 2
25 Sep
  * 1.9.2 preview release 3
  * we aim to freeze the feature of 1.9.2 at the time.
25 Oct
  * 1.9.2 Release candidate 1
  * 1.9.2 will be in maintenance mode as 1.9.1 is now in.
25 Nov
  * 1.9.2 Release candidate 2
25 Dec
  * 1.9.2

== New/Changed Features
Some libraries were improved/reimplemented.
* Socket
* Time
* pipe, pty and open3

see http://svn.ruby-lang.org/repos/ruby/trunk/NEWS for more detail.

=== TODO
Some features need more discussions. We must decide detail of the
features by 25 Sep.
* Enumerable#gather
  * [ruby-dev:38392]
* Time#+ can accept a String as a rhs.
  * Do we need #to_rational in addition to #to_r ?
  * [ruby-core:23729]
  * assigned to akr and matz.
* Should the encoding for ENV be the locale encoding?
  * assigned to naruse.
* we need an option for IO methods which makes the methods skip BOMs.
  * [ruby-dev:37075]
  * assigned to naruse.
* Should we increase the version of Marshal format?
  * for "\u"
  * [ruby-dev:36750]
  * assigned to shyouhei and matz.

=== If possible
I hope to include the following features into the 1.9.2. But they might
take too many time for deciding their specs and implementing them.
* SQLite as a standard library
  * [ruby-dev:38463]
  * assigned to naruse
* improvement of YARV opcode set.
  * assigned to ko1.
* debugger support
  * assigned to ko1
* profiler support
  * assigned to ko1
* dtrace support on FreeBSD, OpenSolaris and OSX.
  * assigned to yugui and ko1.

The features will be included in the 1.9.2 if they will be in time for
the feature freeze.

-- Yugui (Yuki Sonoda)  <yugui@yugui.jp>
