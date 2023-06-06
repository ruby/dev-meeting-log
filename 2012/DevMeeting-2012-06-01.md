---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2012-06-01

参加者: @nari3 @ko1 @nahi @n0kada @shyouhei @nalsh @mrkn @takano32 @ayumin @kosaki55tea @yukihiro_matz

## CRubyのGCの構造改革

https://gist.github.com/2823121

### obj によって malloc/free を切り替える話

* objによって最適なアルゴリズムが違うから
* どうやって判定するか → heapを変えればアドレスでわかる

### 方針

1. gc.cのリファクタリング
2. インターフェイスが変わる（かも）
3. 静的にGCを切り替え可能に
4. 動的切り替え

### GCのアルゴリズムを変更

Knuthの再帰を用いたものからGCハンドブックのものに

### オブジェクトのプロファイル

オブジェクト全体から見て、極端に歳を取ってるオブジェクトはリークを疑う

* オブジェクト全体の年齢（平均
* あるオブジェクトの作られた世代、ソース上の場所
  * RVALUE
  * インスタンス変数
  * st_table
* (解放された時の年齢）

バッチ的にジョブを投げ込んで処理してくれるものがほしい、libdispatch的な

### ネイティブスレッドをラップしたAPIが欲しい

1. GC用に作る（nobu)
2. 自己責任でこっそり使う(mrkn)
3. 固まったら公開

## リリーススケジュールの確認

* 基本的には [Release Engineering](https://github.com/ruby/ruby/wiki/Release-Engineering) 参照。
* 1.9.3 の EOL がいつかは決めていないけど、2.0.0 リリース後1年はとりあえずメンテするつもり。 (naruse)
* 1.8.7 と 1.9.2 は同じスケジュール、2013年6月でEOL。
* 1.8.7 を EOL 後もメンテしたい人は申し出て欲しい (EngineYard が 1.8.6 をメンテしていたように)
* 2.0.0 の次のバージョンはなんだろう
* 3.0 はいつ出るのか → ぼくらは3.0のリリースは見れないんじゃないか
