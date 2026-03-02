---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-02-23

* Date: 2013/02/23
* Time: 11:30-13:30 JST
* Place: near place of [Ruby 20th anniversary party](http://ruby20th.herokuapp.com/) Tokyo, Japan
* attendees: See http://www.atdot.net/~ko1/file/ruby/200lunch/list

This is lunch meeting. So enjoy lunch with discussion.

You need a registration on this site: http://www.atdot.net/~ko1/file/ruby/200lunch/ to prepare place and lunch. Please register before 2/10 (Sun).

# Agenda

## Ruby 2.0.0 retrospective

mame-san presented summary of Ruby 2.0.0.

### release schedule

* Making branch was late (Aug. 2012)
* さっさとブランチを切るべきであった（あとはメンテナが頑張ればよい）
  * フリーズするのがもっと遅くてもよかった
* コードフリーズしているのにブランチ切らないのは良くなかった
* 新機能フリーズ後、バグ修正のためにブランチ切らない期間が欲しかった
* Maybe tried to push too much new features (because it is versioned "2.0")
* ブランチを切ったら、リリースマネージャが新しい枝からバックポートする、という運用が良かったかもしれない
  * It will be difficult to test the branch is stable, because everyone uses only trunk
* Branching is heavy task...
  * It is helpful if branches are tested with CI
  * with travis-ci etc.?
  * github?
    * Move CRuby to github?
    * Is anyone not using git?
    * Need to fix tools that depend on SVN
* Should we move on to pull-request based development?
  * ブランチをマージした後が大変なのは変わらないのでは
  * TODO: discuss this on ML
* mame さんが強権を発生したほうが良かったのではないか
* もっとスケジュールを圧縮出来たのは？

### features of 2.0.0

* "refinements" or "refinement"?
  * Who cares
* Perhaps Enumerator::Lazy may not be stable enough because it had many fixes before release
* ditto: Module#prepend
* ditto: TracePoint
* まつもとさんはどこまで仕様を追っていたか？
  * prepend, refinement が
  * keyword arg の **foo が 2 つ以上の時の挙動とか、細かいことが気になる
* What is changed by Onigmo?
  * Some new features
  * メンテナがついた（けど最近アクティブじゃない）
* How can we make Rubyists use 2.0.0 features?
  * Prove it (Prove what?)
  * Force to write a guide or tutorial when adding a feature?
  * Blogging?
  * Show use cases for each feature
  * heroku を使ってもらう
  * Update "Matz 日記" (note: Japanese blog of Matz, whose last update is 2011)
  * Hackathon with famous Ruby bloggers
* 1.9 とコンパチ、という噂が 2.0 によって、みんながよく試してくれた（細かい gem で試してもらった）
* We'd like to say it's 100% compatible, with Matz' criteria

### Release management

* バグチケットを全部みる機会を持てなかった
* メールはあんまり見てなかったんでは
  * コミッタだけのメールがあれば良かった
    * 前田さんがメンテしている
  * ruby-core にメールがあふれている
  * wiki でやってるのか？
* 大事な情報をいかにきちんと伝えるか？
  * facebook とかで伝える？
* やる気が無くなったときのバックアップ
  * マネージャを複数人
  * ステータスを公開する
  * 勝手に補佐指名していた
  * もっと明示的に補佐を指名するべきであった
* distributor との連携を取りたかった
  * 連携で何をするか？→リリースの時間をもっと短くしたかった
  * rbenv は柴田さんがリクエストしていた
  * rvm はヤバイ雰囲気（head には興味が無い）
* 機能判定会議は良かった
* Mostly on-schedule
* アナウンスを頑張った（ニュースサイトに取り上げられたか）
  * メディアが何を求めているか、後で聞く
* @nagachika さんに 2.0.0 のメンテナを任せた

### support platform list

* matz is using Ubuntu
* Added naruse to FreeBSD
* Added sora, nobu to MacOSX
* Tier2
  * NetBSD (naruse, kambe)??
* 3rd
  * Haiku?
* Remove bcc32
* What does "mantaining" mean?
  * Someone who can help when some issue is raised on the platform
  * サポートされなければ、後で変える

## Maintenance policy of Ruby 2.0.0 and before

* 1.9.3: [ruby-core:47927]
  * How long does it last?
    * Up to sponsorships for Ruby Association
    * Have one year for security fixes after decided to end 1.9.3
* 2.0.0: [ruby-core:52534]
  * Maintainer: Chikanaga-san
  * Focus on stability, do not add new features
  * How the "experimental" mean? May they change during 2.0.0?
    * I'd like to omit specification changes because 2.0.0 is stable version (@nagachika)
    * Matz judges for each specification change
  * Make a ticket for each backport
  * コミット後、バックポートをすぐにするか？
    * 近永さんが自分でルールを作り運用する
  * バックポーチは基本は近永さんがする
  * 明示的にやるな、と言う
  * SEGV だとチケットを作って欲しい
  * バックポートチケットの作り方は redmine で頑張る
  * doc/test は？
    * doc は入れる。test もこけなければ入れる
  * いつリリースされるか？
    * てきとーに。
  * アドバイス：あとでルールを変えても良い。深く考えない方がいい。

## After Ruby 2.0.0

* Who manage? -> mame (matz's approved)

### Version number

* 2.0.1? 2.1.0?
  * I'd prefer not to release too moch patch-levels (matz)
  * 2.0.x is for fixing bugs of specification-level
  * 細かい話は実際に何かがおこったとき
* Next release will be 2.1.0
  * 2.0.x is spawned from 2.0.0 branch
  * Do we need to change ABI version to "2.1.0"?

### Treatment of "experimental features" (refinement, prepend?)

* Modifications of refinement in future could be backported to 2.0.0? (nagachika)
  * 12月はきついんじゃないか？
  * 速く出したいか？　時間をかけたいか？
* そもそもなぜ入らなかったのか？
  * 効率？
  * そもそもまつもとさんの欲しい機能は？

### Schedule

* イベントドリブンじゃないと駄目なんじゃないの？
  * rubyconf とか rubykaigi で議論して？
  * IRC meeting?
    * ちょっとつらそう
* 2013 X'mas release?
  * Can't decide without an list of features of 2.1.0

### What are next features?

* Remove Fixnum and Bignum
* Integer#/ returns Rational
* Replace Float literal by Rational literal
* パターンマッチが欲しい（辻本さん御願い）
* 並列化の話

## Development Resources

### bugs.ruby-lang.org in future(by hsbt)

* who are maintaner?
* We need to increase the number of maintaner.
* how to upgrade ruby/rails/redmine

### www.ruby-lang.org in future(by hsbt)

* who are maintaner?
* We need to increase the number of maintaner.
* how to upgrade ruby/rails/CMS

### RubySpec fails on MRI trunk/2.0.0

* How to collaborate RubySpec with MRI?

### [ANN] Heroku supports tools for ruby-core(by ayumin)

* rubyci.org
* 1.9.3 maintenance tool
* anything else?
  * 2.0.0 maintenance tool? (nagachika)
