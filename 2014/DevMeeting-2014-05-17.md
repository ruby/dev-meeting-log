---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-05-17

Date: 2014/05/17
Time: 13:00 -
Place: TBD
Attendees: sign up required: http://cruby.doorkeeper.jp/events/10778

## Agenda

* [Feature #9772] `IO#statfs` and `File::Statfs`(nalsh)
* [Feature #9647] `File::Stat#birthtime`(nalsh)
* [Feature #9816] 文字列内の数字を数値として比較するメソッド(nalsh)
* date(nalsh)
* [Feature #9513] Hide `Rational` internal (akr)
* [Feature #9826] `Enumerable#slice_between` (akr)
* [Feature #9071] `Enumerable#slice_after` (akr)
* [Feature #9770] `Etc.uname` (akr)
* [Feature #9842] system configuration variables (`sysconf()`, `confstr()`, `pathconf()` and `fpathconf()`) (akr)
* [Feature #9834] `Float#{next_float,prev_float}` (akr)
* [Feature #9632] remove doxygen?(hsbt)
* [Feature #9711] Remove test-unit and minitest from stdlib. Can I remove test-unit? /cc sora_h (hsbt)
* remove rubyforge url(hsbt)
* give up `callcc` (tarui)
* [`Hash#comprized?`] (https://gist.github.com/nobu/dfe8ba14a48fc949f2ed) , http://olivierlacan.com/posts/proposal-for-a-better-ruby-hash-include/ (hone02)
* [ANN] chkbuildXXX.hsbt.org

# Log

attendee: sora_h, shyouhei, akr, naruse, ko1, hsbt, tarui

online: matz, n0kada,

[https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20140517Japan](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20140517Japan)

## [[Feature #9772]](https://bugs.ruby-lang.org/issues/9772) IO#statfs and File::Statfs

本当に必要なのか？(rubygem でいいんでないのという議論)

POSIXに入っているし、Pythonが入れてる

statvfsという名前で入れる

とりあえずfstypeが分かればいいんじゃないの？statvfsはoverkillなのでは?

Matz: 色々込み入ってるので core には入れないで test 配下へ. 欲しいということがあったら gem にしてください.

## [[Feature #9647]](https://bugs.ruby-lang.org/issues/9647) File::Stat#birthtime

何に使うのこれ

- ctimeよりは便利ではある

- というかctimeが使い道がなすぎる

BSDにはある

windowsにもある

- windowsのctimeはファイルバックアップのときにすり変わるのではないか

Linuxにはない(fsに記録されてるけど取り出す方法がない)

python にもあるが、stat() を直接返すらしく、対応していないOSでは何もしない

とりあえず用途を見つけてきていただきたい

損する人いる? いなそう?

入れてメンテナンスコストがあがるということはないだろう

ないときはNotImplementedError

## [[Feature #9816]](https://bugs.ruby-lang.org/issues/9816) 文字列内の数字を数値として比較するメソッド

numericcmpという名前はない

考慮する話：

… なんか色々ある

akr: Ruby の配列に変換して比較できると思うけど.

ありえるパターン(Debian, Windows, etc…)を列挙してあるべき仕様を考えるのが良いのではないか

全部入りは無理

組み込みでは最低限必要なものに絞って、色々考えるならGemに。

RubyとGemのバージョンぐらい

「バージョンを比較するものである」

「複数の数字が入ってるときにどうするか」「数字以外が入ってるときはどうするか」を決めて再提案

numericcmpとかではなくバージョンを比較するものであるとわかりやすい名前にする必要もある

## date

bundle gem にして、time が依存する箇所だけ ext に持っていく. 切り離す準備だけしておくのはどうか. いつやるかは未定.

## [[Feature #9513]](https://bugs.ruby-lang.org/issues/9513) Hide Rational internal (akr)

これはOKなのでは

→matzがOKと返信する

## [[Feature #9826]](https://bugs.ruby-lang.org/issues/9826) Enumerable#slice_between (akr)

ニーズはある

matz: 機能としては採用してあげたいけど、この名前では採用できない。これはbetweenではない。

#slice? → Array#slice があるのでNG

## [[Feature #9071]](https://bugs.ruby-lang.org/issues/9071) Enumerable#slice_after (akr)

対称性

継続行をくっつける

→accept

## [[Feature #9770]](https://bugs.ruby-lang.org/issues/9770) Etc.uname (akr)

test の中で uname -r を叩いているのを見かけるので組み込みで用意してもよさそう.

グローバルな設定を取り出すものを Etc の下に作るのはよさそう

 \* Windows ではどうするか

 \* sys-uname

について書く.

→accept

## [[Feature #9842]](https://bugs.ruby-lang.org/issues/9842) system configuration variables (sysconf(), confstr(), pathconf() and fpathconf()) (akr)

Matz: sysconf と confstr で同じ機能だけど、数値と文字列を返すかで違う、何とかマージできないかなあ

Windows でどうしよう →NotImplementedError

## [[Feature #9834]](https://bugs.ruby-lang.org/issues/9834) Float#{next_float,prev_float} (akr)

これは用途があまり明らかでない→テストで便利(printfのテストとか)。

## [[Feature #9632]](https://bugs.ruby-lang.org/issues/9632) [offtopic] remove doxygen?

ccan フォルダの追加に伴って doxygen の警告が凄いでてきた、そもそも使ってないなら消したい

ko1: 消すのではなくて、デフォルトで動くのはやめて make doxygen とかしたら動くようにしたらどうか

Matz: デフォルトでは動かさないようにして、何かレポートきたら誰か頑張る.

## [[Feature #9711]](https://bugs.ruby-lang.org/issues/9711) Remove test-unit and minitest from stdlib. Can I remove test-unit? /cc sora_h (hsbt)

lib/test, lib/minitest を使うのはもうやめている.

lib/{test,minitest} を消す

案1.  test-unit2 と minitest5 をリポジトリの lib に入れる

案2. どちらかをリポジトリに入れる

案3. パッケージングでやる

案4. …

sorah: とりあえず lib/test は消してみました。悲鳴が上がるのを待つ

結論はでないので、2.2 以降でバンドルするテストライブラリを模索する. 須藤さん、ryan に ping する issue を作る(hsbt)

## remove rubyforge url(hsbt)

Changelog に残っているものはそのままにして rake.1 ruby.1 のようなやつに残っているものは適切そうなやつに置きかえ作業をする.

## give up callcc (tarui)

matz: 2.2 で外すにしても移行パスが必要

ko1: gem にしてメンテナンスサイクルを分けよう

チケットがまだないので、作って反対意見がないか確認。

方針としては無くしていく。

## [Hash#comprized?](https://gist.github.com/nobu/dfe8ba14a48fc949f2ed) , [http://olivierlacan.com/posts/proposal-for-a-better-ruby-hash-include/](http://olivierlacan.com/posts/proposal-for-a-better-ruby-hash-include/) (hone02)

これは何か: あるハッシュがハッシュの一部にあるかを調べる

ユースケースが思いつかない、テストで使うのは便利かもしれない

ほんとに欲しければ bugs に登録してください

Matz: 名前がイマイチ

## [ANN] chkbuildXXX.hsbt.org

Ruby Association の資金で4台マシンを用意しました. 必要に応じてアカウント作るのでご連絡ください.

intel以外が欲しい

qemuの上でarm使いましょう

ko1: とある大学のラックにマシンをおけそうなのでマシン持ってる人は連絡してくだい

pLinux が何なのかよくわからないので RubyCI のサーバー名を変える

## 次回予定を決める

6/18仮やるなら夕方

- 主な議題: testのライブラリどうする、すとうさんに ping する
- 場所はクックパッド予定

7/26仮やるなら昼ごろ

- 主な議題: プレゼン大会(募集開始はいつやる?)

# Summary

## Attendance[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Attendance)

- in person: sora_h, shyouhei, akr, naruse, ko1, hsbt, tarui
- online: matz, n0kada

## \[Feature [#9772](https://bugs.ruby-lang.org/issues/9772 "Feature: IO#statfs and File::Statfs (Rejected)")\] IO#statfs and File::Statfs[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9772-IOstatfs-and-FileStatfs)

not accepted. A gem should be made first.

## \[Feature [#9647](https://bugs.ruby-lang.org/issues/9647 "Feature: File::Stat#birthtimeの追加 (Closed)")\] File::Stat#birthtime[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9647-FileStatbirthtime)

accepted

## \[Feature [#9816](https://bugs.ruby-lang.org/issues/9816 "Feature: 文字列内の数字を数値として比較するメソッド (Assigned)")\] 文字列内の数字を数値として比較するメソッド[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9816-%E6%96%87%E5%AD%97%E5%88%97%E5%86%85%E3%81%AE%E6%95%B0%E5%AD%97%E3%82%92%E6%95%B0%E5%80%A4%E3%81%A8%E3%81%97%E3%81%A6%E6%AF%94%E8%BC%83%E3%81%99%E3%82%8B%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89)

This feature is about comparing characters as numbers, like for Gem versions. For instance, 11 > 9 #=> true

- still need more discussion
- also need a good name

## date[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#date)

going separate it into a bundled gem

## \[Feature [#9513](https://bugs.ruby-lang.org/issues/9513 "Feature: Hide Rational internal (Closed)")\] Hide Rational internal (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9513-Hide-Rational-internal-akr)

accepted

## \[Feature [#9826](https://bugs.ruby-lang.org/issues/9826 "Feature: Enumerable#slice_between (Closed)")\] Enumerable#slice_between (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9826-Enumerableslice_between-akr)

Matz likes the idea, but it needs a better name. There is already Array#slice.

## \[Feature [#9071](https://bugs.ruby-lang.org/issues/9071 "Feature: Enumerable#slice_after (Closed)")\] Enumerable#slice_after (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9071-Enumerableslice_after-akr)

accepted

## \[Feature [#9770](https://bugs.ruby-lang.org/issues/9770 "Feature: Etc.uname (Closed)")\] Etc.uname (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9770-Etcuname-akr)

accepted

## \[Feature [#9842](https://bugs.ruby-lang.org/issues/9842 "Feature: system configuration variables (sysconf(), confstr(), pathconf() and fpathconf()) (Closed)")\] system configuration variables (sysconf(), confstr(), pathconf() and fpathconf()) (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9842-system-configuration-variables-sysconf-confstr-pathconf-and-fpathconf-akr)

accepted

## \[Feature [#9834](https://bugs.ruby-lang.org/issues/9834 "Feature: Float#{next_float,prev_float} (Closed)")\] Float#{next_float,prev_float} (akr)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9834-Floatnext_floatprev_float-akr)

accepted

## \[Feature [#9632](https://bugs.ruby-lang.org/issues/9632 "Feature: [PATCH 0/2] speedup IO#close with linked-list from ccan (Closed)")\] [offtopic] remove doxygen?[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9632-offtopic-remove-doxygen)

dropping support for doxygen

## \[Feature [#9711](https://bugs.ruby-lang.org/issues/9711 "Feature: Remove test-unit and minitest from stdlib. (Closed)")\] Remove test-unit and minitest from stdlib. Can I remove test-unit? /cc sora_h (hsbt)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Feature-9711-Remove-test-unit-and-minitest-from-stdlib-Can-I-remove-test-unit-cc-sora_h-hsbt)

continuing to discuss about bundling test libraries test-unit/minitest.

## remove rubyforge url(hsbt)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#remove-rubyforge-urlhsbt)

accepted

## give up callcc (tarui)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#give-up-callcc-tarui)

accepted: going to fade out callcc support

## Hash#comprized? (hone02)[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Hashcomprized-hone02)

[http://olivierlacan.com/posts/proposal-for-a-better-ruby-hash-include/](http://olivierlacan.com/posts/proposal-for-a-better-ruby-hash-include/)

- need a good name
- unclear what the usecase is
- continue to discuss on redmine

## [ANN] chkbuildXXX.hsbt.org[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#ANN-chkbuildXXXhsbtorg)

Using Ruby Association's funds, 4 machines have been prepared for chkbuild. chkbuild is the Ruby CI platform powering [http://rubyci.org](http://rubyci.org/). We also want something besides Intel like running ARM on top of QEMU.

## Next Meetings[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingSummary20140517Japan#Next-Meetings)

- 6/18, 18:30 JST: discussing [Feature [#9711](https://bugs.ruby-lang.org/issues/9711 "Feature: Remove test-unit and minitest from stdlib. (Closed)")], [doorkeeper](http://cruby.doorkeeper.jp/events/11795?utm_campaign=event_11795_7773&utm_medium=email&utm_source=not_replied_message)
- 7/26: big features for 2.2
