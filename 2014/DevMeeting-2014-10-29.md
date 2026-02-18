---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-10-29

Date: 2014/10/29
Time: 19:00
Place: Ebisu Garden Place Tower 12F, Cookpad's new office
Remote participation: Google hangout?

## Agenda (please add your name when you propose something)

- [Bug #10416] Create mechanism for updating of Unicode data files downstreams when we want (duerst)
- [Feature #5458] DL should be removed (hsbt)
- [Bug #10314] Default argument lookup fails in Ruby 2.2 for circular shadowed variable names (hsbt)
- [Feature #9612] Gemify OpenSSL(hsbt)
 - https://docs.google.com/document/d/1QYaVO2kfejQrvhUXCAf4wYz3pnhsIGY6MTiQIG_eODo
 - https://gist.github.com/hsbt/32b2fb67d3023931bae3
 - https://github.com/hsbt/openssl (experimental to gemify)
- [misc #10287] rename COLON3 to COLON2_HEAD. (hsbt)
- [Feature #8976] file-scope freeze_string directive (akr)
 - https://github.com/akr/ruby/compare/frozen-string
 - http://www.atdot.net/sp/view/onchdn
- [Feature #10344] [PATCH] Implement Fiber#raise (ko1)
- [Feature #10440] Optimize keyword and splat argument (ko1)

- Mac mini from Ruby-no-Kai: https://github.com/ruby-no-kai/official/issues/150

- Release Plan of 2.2.0
 - preview2 date: 11/10-12(TBD)
 - rc date: 12/1-12(TBD)
 - release date: 12/25
 - https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering22
- Next meeting

 * 11/26(Wed) 19:00-

# Log

# \[Bug [#10416](https://bugs.ruby-lang.org/issues/10416)\] Create mechanism for updating of Unicode data files downstreams when we want (duerst

summary:

- Unicode の定義ファイルをいつ更新するか?
- 今の実装はファイルが存在しなければ取得する、make update-unicode を実行すると更新される
- 今は最新版を落とす、となっているが7.0.0 に明記してダウンロードするようにした方がよい(2015年に8.0.0が出る)
- ディレクトリやファイル構造をどうするか→nobuに一任する

knu: Unicode 7からは毎年半ばに定期リリースされることになった: [http://www.unicode.org/versions/index.html#schedule](http://www.unicode.org/versions/index.html#schedule)

naruse: 2.2では7.0.0指定にすべき

martin: latestの方が好み（だが、ここではおいておく）

martin: 7.0.0を8.0.0に変えた時、うまく（改めて）ダウンロードしてビルドして欲しい

naruse: unicode\_normalizeだけでなくOnigmo等、それぞれ個別に新バージョンに対応する必要がある

martin: 個々のファイルには、バージョン番号が入っていないものもある（過去との互換性）

akr: ディレクトリ構造にバージョンを入れれば反映出来るだろう

ダウンロードおよび置き場に関してはnobuが実装するので、どうしたいかだけ伝えればOK

# \[Feature [#5458](https://bugs.ruby-lang.org/issues/5458)\] DL should be removed (hsbt)

akr: dlを消す時にlibffiのソースを添付するという話だった。（libffiをWindowsでビルドするのは大変なので、CRubyにバンドルし、ビルド時に一緒にビルドして欲しい）

knu: libyamlも同様の理由で同梱している

naruse: libffiはpermissiveなライセンスだったはず（※MIT License）

knu: 余談だけど1.9, 2.0, 2.1のDL/Fiddleの差異や、ffi gemともまた違うという件についてkakasi gemというのを作ったのでご覧ください [https://github.com/knu/kakasi\_ffi](https://github.com/knu/kakasi_ffi) （どのバージョンのrubyでも使えるdl gemを作る場合の非互換性について参考になるかも）

akr: すぐDLを消すとWindowsで困る（usaさんが怒る）のでは？

akr: libffiソースバンドルチケットを作って、remove dlの依存先にしてはどうか

hsbt が関係者(tenderloveとunak)に remove 作業の詳細をもう一度確認して、どこまでやるかのラインを決めてもらう

# \[Bug [#10314](https://bugs.ruby-lang.org/issues/10314)\] Default argument lookup fails in Ruby 2.2 for circular shadowed variable names (hsbt)

matz: ローカル変数代入と同じように見えるから、挙動もあわせるべき

akr: （foo = fooでnilが代入されるというのは）役に立つ挙動ではなく、おそらく誤って踏んでいると思われるため、強い警告にすべき（-wなしでも警告が出る）

matzがこれに関しては一貫性を取る（このリグレッションは諦めてもらう）と決定

# \[Feature [#9612](https://bugs.ruby-lang.org/issues/9612)\] Gemify OpenSSL(hsbt)

akr: experimental 実装について gemのバージョンは、OpenSSL (OpenSSL::OPENSSL\_VERSION)そのものではなく、OpenSSL拡張ライブラリのバージョン (OpenSSL::VERSION) にするべき（※libsslランタイムのバージョンはOpenSSL::OPENSSL\_LIBRARY\_VERSION）

akr: ext/openssl に脆弱性があったら、ruby 本体をリリースせざるを得ないのでrubyリリースエンジニア側には gem 化のメリットはなさそう

naruse: openssl ライブラリのメンテナ自身が継続的にメンテナンスするということであれば gem にする分には反対しませんが、それが出来ない状態で gem にするのは反対

matz: 異議はありません

naruse さんが issue にコメントする

# \[misc [#10287](https://bugs.ruby-lang.org/issues/10287)\] rename COLON3 to COLON2\_HEAD. (hsbt)

\*: COLON3がおかしいのはおっしゃる通りだが、名前が「HEAD」はちょっと違う気がするので「TOP」?

akr: 「議論はしましたがいい名前が浮かばない」

ko1: matz にふっておきましょう

# \[Feature [#8976](https://bugs.ruby-lang.org/issues/8976)\] file-scope freeze\_string directive (akr)

matz: 遠い未来には賛成

ko1: 現在確認している反対意見

- コード断片の局所性が下がる（上を見ないと分からない）
- コピペ互換性が下がる

\*: コピペ互換性に関しては今までにもたくさん反例があるのでは

ko1: 文字列はimmutableにすべきだった？（初期設計の失敗？）

matz: 文字列をimmutableにするのが現在のトレンドではある

matz: 遠い未来を考えると、むしろ互換性のために（この古いコードは）mutableであると宣言することになるので、それが可能かつ自然な表記にすべき

\*: true/falseなので可能ではある（knu: t/nilではないのね）

\*: 述語は mutable-string-literal とか？

knu: 返した文字列を呼出先が勝手にいじってしまうので、毎回dupしたものを返しているという事例を最近見た。メソッドの仕様として「これはmutableなもの/immutableなものを返す」と明記する必要が出てくるかも？

akr: デフォルトimmutableにするとどのくらいの変更が必要かやってみた（test-allが通るまで手を入れた）ものがある。（<<要URL>>）

knu: evalの挙動も要検討（マジコメがない場合、evalの置かれたファイルのマジコメを使うべきなのかどうか）

\*: 実験的実装としては、（既存の挙動を変えるものではないので）比較的安全なのでは。

matz: 嫌な予感がするので2.2では（実験的実装も含めて）やめましょう

2.3（以降）に照準を合わせる

# \[Feature [#10344](https://bugs.ruby-lang.org/issues/10344)\] \[PATCH\] Implement Fiber#raise (ko1)

ko1: Thread#raiseは危険なのでなくした経緯

matz: Fiber#raiseはyieldの場所でraiseされると決まっているので安全

ko1: セミコルーチンの親子・裏表関係からして気持ち悪さがある; うまく言えない

反対はなし
matzは賛成

# \[Feature [#10440](https://bugs.ruby-lang.org/issues/10440)\] Optimize keyword and splat argument (ko1)

ko1: 99%は互換

matz: 非互換については了承しました
