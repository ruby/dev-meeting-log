---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2008-09-22

* Place
  * Univ. of Tokyo, Akihabara, Tokyo, Japan
* Date
  * 2008/09/22 13:00 - 18:00
* Attendees
  * matz via skype
  * akr
  * yugui
  * ko1
* Purpose
  * Discuss 1.9.1 spec before feature freeze

# Agenda

* ABI fixの前に`struct RArray`の埋め込みはやらないのか? (yugui) -&gt; 公開したくない構造体を別のファイルにする
* `include/ruby/`から`node.h`を移動させたい (yugui) -&gt; ripper 問題がなければいいのでは
* class of class of class is `Class`は仕様かバグか? (yugui) -&gt; yugui さんががんばる
* DelegateClass#class を1.8仕様にするか否かの結論を (unak) -&gt; 1.8 に戻す，その他のメソッドについては未定
* Regexp#to_s を Regexp#inspect の alias に (nurse) -&gt; rejected by akr
* Method#parameters, Proc#parameters (ko1) -&gt; rejected (wait for named parameter)
* Method#source_location -&gt; nobu matter
* Encoding::default_internal (Michael Selig, ruby-core:18774) -&gt; pending (今週中)
* 凍結の範囲はどこまでか (添付ライブラリも含まれるか (rubygems のアップグレードは間に合うのか (mame) [RubyKaigi2008 で新しい文字コード変換の追加が含まれないのは matz の見解でした] -&gt; 含まれる．ただし，事前に悲鳴をあげれば 10 月まで待つ
* 文字コード変換あたりの名前の整理 (duerst, matz, ruby-dev:36389): 結論: 現在のままでよい (matz ruby-dev:36420)
* lib/1.9 or lib/1.9.1 ? -&gt; suffix を付ければいいのではという案
* binread -&gt; 入れる
* Hash#eachの順序 =&gt; 公式にRuby 1.9 featureとして認定
* inspect の Encoding -&gt; external encoding と同じならそのまま，それ以外はバイナリ
* tempdir, tmpfile どちらかに対応したい -&gt; tmp に統一?
* ticket の Feature な issue を一応全部見直した．
