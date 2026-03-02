---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2010-08-27

* Place
  * Epochal Tsukuba (RubyKaigi2010)
* Date
  * 2010/08/27 10:00- (JST)
* Attendees
  * matz (need bento)
  * ko1 (need bento)
  * mrkn (need bento)
  * nagai (need bento)
  * akr (need bento)
  * yugui (need bento)
  * shyouhei (need bento)
  * mame (need bento)
  * nobu (need bento)
  * rocky (need bento - seems like the thing to do)
  * tarui (need bento)
  * tenderlove (need bento)
  * drbrain (need bento)
  * usa (need bento)
  * kosaki (need bento)
  * kazu (need bento)
  * naruse (need bento)
  * nahi (need a bento)
  * shugo (need bento; I prefer curry)
  * kou (need bento)
  * tetsu (need bento)

If you need a bento (lunch), please write it.  Expense will be collected.

'''Bento order deadline is 8/24''' <- Order is closed.  We prepare 17 bentos (RubyKaigi staffs have own bento).

# Agenda

* Ruby 1.9.3 release plan (by yugui/mame)
  * Before Jul, 2011 (RubyKaigi? RubyConf?)
    * Timeline base?
    * No deadline, no code
  * Features
    * classbox?
    * MVM <- impossible
    * debug features
    * keyword argument (2.0?)
    * tuning (ko1)
* Ruby 1.9.1 support plan?
  * yugui's matter?
  * call for maintainer?
  * 1.9.1 users?
    * yugui
    * Debian squeeze uses 1.9.1
      * ignore it?
      * give 1.9.1 to Debian?
      * no, it is 1.9.2 shortly before the 1.9.2 release [ruby-core:31976]
  * yugui-san decided to release 1.9.1 bugfix until Feb, 2011
* Ruby 2.0 release plan (by matz)
  * matz; no plan about timeline
  * matz: I want to make a ruby_1_9 branch
  * yugui: 2.0?
    * classbox
    * traits
    * keyword arguments
    * compatibility?
    * -> matz: 100% compatible
      * crap
  * yugui: 1.9 should include 2.0 features, gradually
  * yugui: 1.9 branch should not be made
  * mame: can make compatible 1.9 release?
  * acceptable if well documented
  * if we have a compatibility issue on 1.9 and 2.0 development, then create 1.9 branch
* Ruby 1.8.* release plan (by syouhei)
  * knu: ruby 1.8.8 will be release in this year.
  * knu: ruby 1.8.8 will be last release of 1.8 series.
  * knu: if there are serious issue with 1.9 series, we will release 1.8.9.
  * akr: Should 1.8.8 include 1.9.2 methods which 1.9.1 doesn't have?
  * knu: We can ignore 1.9.1.
  * knu: NEWS document is really important.
  * akr: How to maintain NEWS file? -> flush
* The license of Ruby (by naruse)
  * naruse: GPL3 incompatible. OK: BSD -> Ruby, NG: Ruby -> BSD
  * naruse: How about to change license Ruby's and BSD?
  * ..some lawyers discussion...
  * matz: i'm thinking to change only GPL2 or later.  However, not enough consideration.  plus BSD seems OK.  We need more law knowledge.
  * how about triple license? (BSD, GPL2 or later, Ruby's)
  * triple doesn't have any merits.
  * BSD + Ruby's?
  * matz: if no objection, changing license is ok. I wait  objections for 2 weeks.
  * akr: we can't change part of ruby source code (such as win32.c).
* Windows support in the future (by usa)
  * usa: how about to stop win32 support? (ADDED: usa is a Windows platform mgr, who is 'Usaku Nakamura')
  * nahi: On Windows, JRuby is better than CRuby. (ADDED: nahi is one of a JRuby commiter, who is 'Hiroshi Nakamura', not usa)
  * ... rubyspec discussion ...
  * usa: only mswin32. not cygwin/mingw.
  * usa: any other maintainers?
  * aaron: luis and roger?
  * usa: good.
  * matz: how about to search next maintainer before 1.9.3 release?
  * usa: agreed.
  * maintain mswin64?
  * usa: absolutely.  win64 is interesting.
* Ruby continuous integration (by tenderlove)
  * arron: Should have standard CI?
  * akr: before making such great system, we should make tests green
  * nahi: Can chkbuild split tests into small part?
  * akr: Please use your favorite tool.
  * ...
* Debugger/Profiler/Tracer interface. See [[threadframe-links]] \(by Rocky Bernstein)
* Specification of dynamic loading (see [ruby-dev:41774]) and Virtual File System Interface (if we have some time (low priority), by nagai)
* Bug ticket squash (everyone)
* #3675
  * String#prepend => OK
  * String#append, String#>> => pending
  * Array#prepend, Array#append => pending
* proposal for Standard C API
  * based on Rubinius's
  * will be porting 1.9.
  * approved by matz.
