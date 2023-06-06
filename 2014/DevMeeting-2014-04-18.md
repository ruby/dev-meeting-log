---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-04-18

Date: 2014/04/18
Time: 19:00 -
Place: DeNA Co., Ltd. [map](https://www.google.co.jp/maps/place/%E6%B8%8B%E8%B0%B7%E3%83%92%E3%82%AB%E3%83%AA%E3%82%A8/@35.659104,139.703742,17z/data=!3m1!4b1!4m2!3m1!1s0x60188b5856fffff9:0x220c27f839771068) [more info](http://dena.com/intl/company/overview/)
Attendees: sign up required: http://cruby.doorkeeper.jp/events/10462

## Agenda

* [Feature #9711] Remove test-unit and minitest from stdlib [hsbt]
  * bundled test-unit and minitest are obsoleted.

* [Feature #9612] Gemify OpenSSL [zzak]
  * requires thread_native.h to be public

* [Bug #9613] Warn about unsafe ossl ciphers [zzak]
  * before releasing r45274, we should warn as it breaks backwards compatibility

* [Misc #9741] Policy for posting security and general announcements [hone]

* [Bug #9671] 2.1 Backport Request #9592 OpenSSL Regression 2.1.0 [hone]

* Status of Branch Maintainer for 2.0.0? [hone]

* When is the next release of maintained Rubies: [hone]
  * 1.9.3
  * 2.0.0
  * 2.1.2

* Release Engineering of patch release
  * backports: test before actual backport
  * release: test for packages to release

## Log

DevelopersMeeting20140418Japan

skype: matz, nobu

attendee: hone, naruse, ko1, taru, martin, hsbt, zzak, shyouhei, aaron

Release Engineering of patch release
Conclusion
hsbt will talk to nagachika (current Ruby 2.1 maintainer) about Heroku and hsbt’s request for a Ruby 2.1 bugfix release
hsbt has requested to be a submaintainer of 2.1 and needs nagachika’s approval
the current state of releasing is expensive because it requires machine and human resources. Ruby needs to be tested with many configurations. hsbt and ko1 will examine a CI env to assist in the release process.
we would like to release more often to handle critical bugs like a SEGV
need to figure out how to handle already backported commits on the release branch not in the hotfix
revert the commits (or find some way not to include them) that don’t apply
include them
matz says there’s no reason to distinguish hotfixes from a normal bugfix
hsbt: nagachika さんに heroku と hsbt の要望をお伝えする
hsbt: hotfix をすぐに出せるようなCI環境の構築を頑張る
nagachika: hsbt がサブメンテナとして参加していいか決める


* hsbt のオーダー、ユーザー企業の要望としてはビルドできない、SEGVが起きるというようなcriticalな不具合はすぐに直してリリースしてほしい、そのために hsbt が 2.1 のサブメンテナのような形で関わりたい

 * 二人でメンテナンスするのが良いかどうかは nagachika さんにおまかせする

 * 今日の内容は nagachika さんに hsbt が連絡する

* 例えば hotfix をすぐに出したい

 * すぐに出せるような仕組みは hsbt も頑張る

* 複数人でやるとバックポートが貯まっていた後にcriticalなパッチをバックポートして検証するという時のコストが高くなる

* hotfix モデルにする場合はブランチ設計が必要(hsbt がやりたいと言っていたこと)

* hotfix と stable リリースは区別する必要がない


* まつもとさんは（いやいや）ok>tenny2桁


hsbt: user comapny want to release patch release when it has critical bugs like SEGV. hsbt want to handle it as sub-maintainer.

ko1: heroku also want it.

* up to nagachika about maintaining branch with 2 persons

* hsbt reports to nagachika today’s discusion

* for example immediate hotfix

  * hsbt collabolate to relase hotfix


naruse: current 2.1 branch already has some backports after 2.1.1. how handle them is the issue if we release 2.1.2 immediately. There’s some option (1) revert such commits (2) include them

naruse: if we choose hotfix model (release 2.1.1 with few ciritical fix), we need design new branching model


matz: agree with two digit teeny (with complaint)

matz: no need to distinct hotfix and stable releases

[Feature #9711] Remove test-unit and minitest from stdlib [hsbt]
Conclusion
- hsbt will separate test library for test-all and copy it to test/

- sora_h will decide how handle llib/test/unit

- ryan will decide handle lib/minitest



- we use test/unit (with minitest) for our test-all -> we can solve it easy

- minitest4 conflicts with minitest5/minitest.gem

[Feature #9612] Gemify OpenSSL [zzak]
Conclusion
- ko1 will finish this in 1 month. zzak will check if it’s not done. (June/2014)

[Bug #9613] Warn about unsafe ossl ciphers [zzak]
Conclusion
- it breaks backward compatibility

- if emboss allows the backport of this patch into 2.1 and 2.0, we will merge it.

- it needs to warn and not raise.

[Bug #9671] 2.1 Backport Request #9592 OpenSSL Regression 2.1.0 [hone]
Conclusion
This depends on the result of “Release Engineering of patch release”
hsbt will discuss this issue with nagachika (maintainer of 2.1)
[Misc #9741] Policy for posting security and general announcements [hone]
Conclusion
There was a policy decided.

publically disclosed issue - get consensus on ruby-core/security mailing list members, active ruby branch maintainers (usa & chikanaga)
privately disclosed issue - get consensus on github (https://github.com/ruby/www.ruby-lang.org) with the w.r-l.o reviewers
everything else - get consensus on github (https://github.com/ruby/www.ruby-lang.org) with the w.r-l.o reviewers


ruby-core request to announce to w.r-l.o team


two kind of security issues:

known issue and unknown issue


hsbt: known issues: get consensus of github & w.r-l.o reviewers

hsbt: unknown issues: get consensus on ruby-core/security@ members + branch maintainers (usa & chikanaga).

other issues: get consensus on https://github.com/ruby/www.ruby-lang.org

Status of Branch Maintainer for 2.0.0? [hone]
Conclusion
- see [ruby-core:62051]

When is the next release of maintained Rubies: [hone]
Conclusion
depends on the result of “Release Engineering of patch release.”

1.9.3 : when a big security issue occurs. No more bug fix releases.
https://www.ruby-lang.org/en/news/2014/01/10/ruby-1-9-3-will-end-on-2015/
2.0.0 : at the beginning in June
2.1.2 : depends on the result “Release Engineering of patch release.”
Next Meeting:
5/17: http://cruby.doorkeeper.jp/events/10778


Topics:

features to be included in 2.2
2.2 release engineering https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering22

## Summary
