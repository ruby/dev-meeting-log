---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-01-10

Date: 2014/01/10
Time: 17:00-18:30
Place: Cookpad
Attendees: sign up required: http://cruby.doorkeeper.jp/events/8003

Agenda
agenda page copied to here, due to redmine maintenance.
=begin
= DevelopersMeeting20140110Japan

* Date: 2014/01/10
* Time: 17:30-19:00
* Place: Cookpad
* Attendees: sign up required: http://cruby.doorkeeper.jp/events/8003

= Agenda

* 2.1.0 retrospective (ko1)
  * semantic version number
  * who is a mainter of 2.1.0?

* 2.1.1 release schedule (ko1)

* Security Release Process(hsbt)
  * private issue tracking
    * salesforce will support

* Official Announce Policy

* What is the ideal development environment? (ko1)
  * cloud resource(hsbt)
  * private computation resource

* Dreams
  * 2.2
    * Cache multiple compiled regexps: https://github.com/ruby/ruby/pull/497
    * RVALUE size
    * Revisitting method cache
    * Better i18n support (built-in normalization, upcase/downcase incl. Unicode,...)
    * Hide Bignum and Rational internal (also Complex?)
  * 3.0
=end

(ログは次ページ)

ログ

* Security Release Process(hsbt)
  * private issue tracking
    * salesforce will support

hsbt: ticket 管理する方法が無いのでなんとかしたい
ayumin: force.com を提供できる
matsuda: redmine でいいんじゃないの？
hsbt: あるけど、今誰も使っていない。また、セキュアではない。
sorah: 別ドメインで redmine を非公開に分けるのも手では
hsbt: Heroku なので secure では
matsuda: ruby の security 管理に ruby が信頼できるのか？
ayumin: force.com を提供できる
hsbt: あとは関係者に聞いてみる
nurse: とりあえず force のアカウントが欲しい
ayumin: オペレーションを考えて欲しい
matsuda: そもそもセキュリティissueが何件も積み残ってて管理しなきゃいけない状況みたいなのがおかしいのでは？
hsbt: Security 担当を置いて、ping し続けるのがいいのかなー
ko1: RA に専任担当者をつけるように働きかけると良いか
hsbt: お見積もりを笹田に送る


* Official Announce Policy

hsbt: 前回のアナウンスでごちゃごちゃしてしまったので、どうすれば良いか
akr: 外から見た意思決定者がわかりづらいのでは
ayumin: ブランチメンテながアナウンスの最終決定権があることを明示するべき
sora: 言語の壁もあるわけなので齟齬は避けられない。会議で方針が決まったとしても最終的に何を承認するかチケットの上で文章に残したほうがいいんじゃないか (e.g. feature ticket で matz に書いてもらっているように)
ko1: 会議は意思決定の場ではないとするのが正しそう
mrkn: feature ticket 等でも matz に承認の旨を書いてもらっていますね
hsbt: 今回の反省としては usa さんにもうちょっとコミュニケーションを取るべきだった?
amatsuda: Redmine のチケットでやるのは、Redmine に mention 機能がないので不便。運用まわんなそう。
  (ayumin: force.comはできます)
nurse: watcherに勝手に加えればいいじゃん
sorah: ruby-core/dev購読してたら問答無用で来るんだから気付くわけない


* 2.1.0 retrospective (ko1)

nurse: 遠藤さんが報道で使いやすい文言をアナウンスするというのをやってくれたのに継承できなかったのは残念 (文言がコピペできた)
nurse: 2.2 ではちゃんと

nurse: 燃え尽きなかった
nurse: Ruby CI でかい。
ko1: チケット棚卸し会議をやる必要があったなあ

hsbt: 互換性高くスピードアップした 2.2 すばらしいなと思います。ささださんお疲れさまでした

* 2.2 release schedule

nurse: クリスマス。matz による決定事項 [要出典]
nurse: リリースマネージャーを引き継ぎたいという方がいなければわたしが

* who will maintain 2.1.0?
  * semantic version number
* 2.1.1 release schedule

nurse: 誰かがだす
amatsuda: stable branch で若干のパフォーマンス修正を入れるのはありなのか
sorah: securityはsecurity, bugはbug, improvementはimprovementで分けてリリースしてほしい、過去のRailsみたいにごちゃ混ぜにして破滅するような事にはなってほしくない
ayumin: 賛成
nurse: 基本バグ修正というのは既定路線、例外はあります。(その時のリリースマネージャーの判断ですが)
amatsuda: 深刻な performance regression はバグなんですよね。


* What is the ideal development environment? (ko1)
  * what services do you want? (ko1)
ko1: wishlist が必要?
nurse: CI はコミット毎に実行したい
???: プラットフォーム増やしたい
hsbt: OS X Server, Windows がほしい
???: 管理を誰がやるのかという問題が
akr: 開発用マシンといえば wake on SSH がほしい
ayumin: ruby コア開発に関する事をカンファレンス等で発表する際に旅費に困らないようにしたい (日本 Ruby の会が支援とか)

お金があったらやりたいこと（開発編）
(1) 開発マシン
ビルド、テストラン
手でビルド
コミッターはログイン可能なサーバ
git でブランチにコミットすると、勝手にいろいろやってくれる環境とか
make all check までの結果を教えてくれたり、test.rb とかの結果を返してくれたり。
奥には distcc 可能な環境でちょっぱやビルド・テスト・テストラン
条件絞り込んで複数マシン・環境・リビジョンで実行
やり方
bisect 的に絞り込む（不具合の絞り込み）
直線的に実行する（性能変化の箇所を探る）
1台のマシンで絞り込み（bisect + repositories）
n台のマシンで絞り込み（nth-sect + repositories）
Mac や Solarisのような特殊なサーバーを買う
(2) テストマシン (CI)
いろいろな環境でテスト
OS
Linux
CentOS (バージョン複数)
Debian (バージョン複数)
Ubuntu (バージョン複数)
RHEL?
Linux以外
FreeBSD
Windows
OS X ?
コンパイラ
gcc (versions)
LLVM + clang
コンパイルオプション
-O0,2,3
libc
これは選択肢はどれくらいあるのか？
リビジョン毎にテストを回していきたい (現状chkbuildはperiodicallyで複数の変更をまとめてテストしてる)
結果をレポート
結果を保管

テストの種類
定期テスト
1 revision に 1 回
機能テスト
性能テスト（perforamnce regression test）
クラウドリソースでの定量的な評価方法があるか不明
ストレステスト
1 revision に沢山
(3) バイナリパッケージ作成・配布
テスト結果がOKであればバイナリパッケージを作成する
CentOS
RHEL
↑以上のOSはニーズが高いはず
Debian
Ubuntu
FreeBSD
NetBSD
↓以下のOSはニーズ少なめ
DragonFly BSD

お金があったらやりたいこと（ruby-lang まわり編）

 * stable, snapshot のバイナリ配布(rpm, deb, rvm)、s3 とほぼ同一
 * 災害対策に s3 にパッケージを配置したい
 * ruby-lang.org の DNS を Route53 にする
 * doc のるりまサーチが groonga で heroku 使うの難しいので ec2 で何とかする
 * 監視サービスの利用
 * 翻訳

お金があったらやりたいこと（コミュニティ活動編）
(1) カンファレンスへの出席補助
旅費全額は厳しいけどコミッター全員のRubyConfの本体参加費くらいは出せませんかね、とRubyの会には提案してみてある(卜部)

(2) 開発会議補助
現在は、Rubyの会からお茶菓子代などを出してもらっている。
笹田が未踏で支援されていたときは、みんなで松江に行ってワイワイと開発を進めることができたいので、その辺のを再現できないかなぁ。

開発会議には大まかに2つある：
意思決定会議
時間を決めて決めたいことを決めて臨む
ticket 棚卸し会議
ダラダラ時間の限り続ける
（いや、ダラダラしなくてもいいんだけど。
1 ticket にかける時間を決めてキッチリやるのも良い）
hack 会議
一緒の場所でダラダラ続ける

会議環境をよくするためにかけられるお金はあるだろうか。Polycom 環境確保？

(3) Matz の秘書

(4) Ruby 開発部屋

hacker’s room.
技術書とか沢山並んでるといいね！

(5) 開発者用マシン

(6) 開発ネタグラント

(6-1) テーマ公募
(6-2) 賞金稼ぎ型
