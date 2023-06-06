---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-02-14

Date: 2014/02/14
Time: 16:00-19:00
Place: DeNA
Attendees: sign up required: http://cruby.doorkeeper.jp/events/8928

## Agenda

 * feature plan of 2.2.0
 * security issue of libyaml.
 * [Feature #9502] Remove deprecated definitions: akr
 * [Feature #8887] min(n), max(n), min_by(n), max_by(n): akr [slide](https://bugs.ruby-lang.org/attachments/download/3980/maxn.pdf)
 * [Feature #6083] Hide a Bignum definition: ko1 & akr
 * GC algorithm changes for 2.1.1 (ko1)

## Log

DevelopersMeeting20140214Japan

[Feature #9502] Remove deprecated definitions: akr
akr: deprecated をそろそろ消してよいのでは

akr: 消すなら早い今がよいのでは

matz: #9502ですね。消すのに賛成です


[Feature #8887] min(n), max(n), min_by(n), max_by(n): akr slide

matz: 追加してもいいと思います。前に話が出た時にも反対はなくてタイミングだけの問題だったと思いました。


[Feature #6083] Hide a Bignum definition: ko1 & akr
matz:

Even though this breaks source compatibility, it is worth changing it (to mark dangerous operation).

So go ahead.



libyamlの security issue

問題: libyaml のupstreamとバンドルしている奴の差分が大きくなるとバックポートメンテナが大変

解決: libyaml のバンドルをやめるというのもあるけど、結論はでなかったので以下の対応をやる。


trunk のバンドル libyaml の変更を 2.0, 2.1 にバックポート(hsbt がバックポートチケット作る)
trunk で変えた分は upstream に n0kada さんがパッチ出す

security@ に来ていた bigdecimal の件

mrkn の判断でセキュリティ問題ではない、ということにしたので、普通の不具合修正として対応する。

GC algorithm changes for 2.1.1 (ko1)

GCアルゴリズムの変更、現状がメモリ食いすぎなのでチューニングとコードの変更を行う。環境変数の追加、たぶんパラメータチューニングで収まる


できれば、2.1.1に入れたいので、2/17 までに ko1 が頑張る。入れるかどうかは nulsh が判断する


[Feature #9362] Minimize cache misshit to gain optimal speed GH-495
struct RValueのサイズを変えるやつ

いつマージするんですか

バイナリが壊れるので 2.2 でもためらうレベル (shyouhei)

このパッチをいれるにはどういう作業が必要でしょう (shyouhei)

  make rdoc のベンチマークじゃ不十分?

調査無しに入れるのは抵抗がある (ko1)

これが速くなる事を示す作為的な例はいくらでも書けるけどリアルワールドだと (shyouhei)

慎重になって放置されるのも微妙なので今年の前半には決めたい (shyouhei)

ベンチマークの詳細も後でください (shyouhei→ko1)

rails アプリケーションでのベンチマークが必要なら呼んでください(hsbt)


2.1 でメモリが増えてという話を聞くので、メモリが増える変更には抵抗がある (matz)

「むしろ減らすのはどうか」 (shyouhei)

Array, Stringが全部使ってる、複雑な事をやっているから (shyouhei)


利用者が選べるように作りなおすっていう手も (ko1)


conclusion: この話はメモリが増える方向だとmatzが抵抗があるので、このままの形では少なくとも入れない? (shyouhei)

メモリを減らすのをどうするかっていうのはちょっと難しいのでみんなで考えるっていう感じですかね (shyouhei)


[misc #9215] Maintenance Policy for Future Releases (2.1.0 & beyond)
Semantic Versioning じゃないので semantic versioning という言葉をつかうのをやめたい (nurse)

[Feature #9123] Make Numeric#nonzero? behavior consistent with Numeric#zero?

[Feature #8919] Queue as embedded class
まつもとさん待ちなのでまつもとさんに今決めてもらおう (shyouhei, ko1)

まつもとさんがいない?



[Feature #8850] Convert Rational to decimal String
w

興味がある人がパッチをかけばいいんじゃないでしょうか

Status: feedback

[Feature #9420] warn and puts should be atomic
writevがない環境でatomicにしてちょっと遅くなるのを許容するかどうか (glass)

(例: Windows)


今別に atomic を保証しているわけじゃないしいいのではないか


conclusion: atomic である事は保証しないがプラットフォームによっては atomic になるのを努力するかもねの方向で調整

[Feature #6869] Do not treat `_` parameter
2.0.0p0から_ではじまる引数は警告されないのでこの提案自体はreject状態

[Feature #7148] Improved Tempfile w/o DelegateClassw
