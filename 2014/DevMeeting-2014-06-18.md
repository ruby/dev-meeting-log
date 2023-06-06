---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-06-18

Date: 2014/06/18
Time: 18:30 -
Place: DeNA.com headquaters http://qwik.jp/asakusarb/HowToPassHikarieEntrance.html
Attendees: sign up required: http://cruby.doorkeeper.jp/events/11795

# Agenda

* [Feature #9711] Remove test-unit and minitest from stdlib. (sorah & kou & hsbt)
 * [Feature #9852] How to bundle test-unit2 and minitest5
 * https://github.com/ruby/ruby/pull/626/files
* Call for feature proposal 2014(hsbt)
 * announce?
 * dead line?
* Edit wiki homepage [https://bugs.ruby-lang.org/] (zzak)
 * how to edit / gain access to edit?
* [Feature #9880] Dir#fileno (akr)
* [Feature #9863] Hide Float internal (shyouhei)
* [Feature #9888] Hide Complex internal (shyouhei)
* [Feature #9889] Hide Hash internal (shyouhei)
* [Feature #9916] Hide Struct internal (shyouhei)
* [Feature #9857] Pathname#birthtime (znz)
* [Feature #9179] MatchData#values_at should support named capture

# Log

DevelopersMeeting20140618Japan
attendee: shyouhei, hsbt, ko1, sora_h, akr, ktou, naruse, duerst
on-line: matz, tal, nobu,


[Feature #9711] Remove test-unit and minitest from stdlib. (sorah)
https://github.com/ruby/ruby/pull/626/files
hsbt: Ryan removed lib/minitest, but he want to bundle minitest.gem instead in Ruby 2.2
sorah: lib/test/unit in Ruby 2.1 is a wrapper of minitest 4, which is blocker for minitest 5, due to incompatibility
sorah: many applications depend test/unit, including old products. They don’t use bundler, and aren’t depending on any gems. They will need to install test-unit.gem if we removed test/unit.
akr: minitest’s implementation is simple. it is good for ruby’s test.
akr: unit test is culture. When ruby bundles unit test library, it means that Ruby promote unit test culture.
akr: the expectation is almost compatibility
kou: I’m planning to introduce `diff_lcs.gem` to test-unit 2, so I’m aware to add dependency in Ruby
kou: Most users use rubygems, so I don’t think bundling test frameworks is must.
ko1: so, how should we do for non-rubygem users?
martin: Bundling nothing is unfamiliar for beginners
kou: teach Rubygems for such users
hsbt: minitest 5 is bundled at least for 2.2
akr: I can’t recommend any testing frameworks because they easily break API compatibility
ko1: “We bundle nothing, use rubygems” is a one choice
martin: We had carefully added incompatibility for String, Hash, etc. But we don’t consider incompatibilities for testing library?
kou: In 1.9 we made big incompatibility in 1.9 with such guides. Migrating testing framework is much easier than that, just adding one gem. We don’t need to be scare for that.
hsbt: Q. I’ll introduce default gems for minitest 5 at least, kou-san, do you want to bundle test-unit 1.x or 2.x or you don’t want to bundle test-unit?
kou: I don’t care
kou: I don’t maintenance test-unit 1.x
hsbt: should be latest…
hsbt: If we bundle test-unit 2.x for Ruby 2.2, and then kou-san add dependency on test-unit 3.x and release it. And we leave bundled test-unit at 2.x for future?
kou: I don’t like that situation…
hsbt: We need a discussion when test-unit don’t support Windows or adding difficult dependency such gtk
nurse: diff-lcs is acceptable for me
kou: If I added unacceptable dependency, can I unbundle test-unit from Ruby?
kou: I don’t want to be blocked to add dependencies by Ruby team
(committers): Yes.
kou: Should I have a migration term before removal?
(committers): No.
conclusion: We don’t take responsibility to maintain bundled gem. (it should be documented) We respect upstreams’ maintenance policy.
We may remove bundled gem after next release if it become unacceptable or such reason.
We say “upgrade gem” when user encountered bundled gems’ bug in bug trackers
We may say “please give up” when user encountered bundled gems’ bug, but fixed version doesn’t work on Ruby, which bundled a gem with the bug due to Ruby’s incompatibility.
kou: How we guarantee that bundled gem works well on a Ruby to be released?
hsbt: I’ll make daily CI with trunk Ruby for bundled gem.
kou: I can’t decide yet, for now
[TODO: hsbt] gems/bundle_gems の呼称 and decide maintenance policy of core team
[TODO: hsbt] make bundle_gems installer working on Windows and BSDs


[Feature #9179] MatchData#values_at should support named capture
Call for feature proposal 2014(hsbt)
announce: Yui Naruse will announce it on ruby-core/ruby-dev/twitter/www.ruby-lang.org and so on until 6/24
dead line: 7/24
Next developers meeting: 7/26, ~13:00-
venue: cookpad (tentative)
sorah: I guess I can host in our office, please remind me later
Edit wiki homepage https://bugs.ruby-lang.org/
how to edit / gain access to edit?

sorah: By the way, I want to design bugs.ruby-lang.org wiki as page for committers. we should move documentation page such as maintenance policy to www.ruby-lang.org to separate.
naruse: Redmine admin bit is required to edit wiki
[Feature #9880] Dir#fileno (akr)
[Feature #9863] Hide Float internal (shyouhei)
[Feature #9888] Hide Complex internal (shyouhei)
[Feature #9889] Hide Hash internal (shyouhei)
[Feature #9916] Hide Struct internal (shyouhei)
Try them!! Check compatibility.
[Feature #9857] Pathname#birthtime (znz)
Basically OK. Continue to discuss to improve implementation.

