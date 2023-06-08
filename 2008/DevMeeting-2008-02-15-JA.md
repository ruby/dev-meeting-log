---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2008-02-15

https://blade.ruby-lang.org/ruby-dev/33825

成瀬です。

Ruby M17N 会議を2008年2月15日に行ったのですが、そのログを転載しておきます。

== cgi.rb 後継の公募について

条件は以下の通り
* 名前 は cgi.rb 以外であること
* MVCの分離を行っていること

== String#gsub(regexp, hash)

String#gsub(regexp, {"&auml;"=>"\u00C4", ..}) という表記を行いたい
→採用

== replicaの出典等の情報を

→書く

== CP949とGBKの違い

→CP949は分離

== 空文字 と UTF-16 を結合する場合について

空文字は ASCII Compatible でない文字列とも結合可能にしてはどうか
→採用

== String#lengthやString#atが遅い

search_nonasciiを使って高速化
VALIDなUTF-8の場合はさらに高速化

== String#getbyte, String#setbyte

→採用

== Indexer

→保留

== inspect について

以下の方向で検討
* obj.inspect_acumulate(enc) を新設

== strftime の方向性について
strftime は locale 非依存、依存のものが必要そうならば別に追加する

== require_relative の導入について
require_relative を呼んだファイルからの相対パスで require する。
→採用

== IO.copy_stream
IO.copy_stream(filename, filename) や IO.copy_stream(stream, stream) 等で、
ファイルやストリームをコピーする。
なお、ファイル名を与えられた場合はシステムコールを呼ぶため、
Rubyは別のことをできる。
→名前について要検討

== Hash#compare_by_identityが微妙な件
Hash#compared_by(:equal?, :obj_id)に変更したい
→採用
→Hash#identifed_byの方がいいかも[ruby-dev:33817]

== Unicode Normalization

APIは以下あたり。
* String#encode("utf-8 nfc")
* String#normalize("nfc")
* Encoding::UTF_8.nfc(str)

open(fn, "r:utf-8", ...) あたりで、正規化しながら読み込む等の処理をした
いので、
それ向けの記法も検討

--
NARUSE, Yui  <naruse@airemix.com>
DBDB A476 FDBD 9450 02CD 0EFC BCE3 C388 472E C1EA

