---
lang: ja
tags: Ruby, ruby-dev-meeting
---

# DevCamp-2008-08-11

- Term: 2008/08/11 (Mon) - 13 (Wed)
- Place: OpenSourceLab., Matsue city, Shimane, Japan
- People
    - Matz
    - shugo
    - ko1 (arrival at YGJ 19:15 8/11, departure from YGJ 19:55 8/13)
    - nobu
    - yugui
    - aamine
    - knu (arrives at YGJ 07:10 8/12, departs from YGJ 07:10 8/14)
    - akr (if possible)
    - shyouhei
    - okkez

Since all attendees are Japanese, following contents are written in Japanese.

# Broadcast (ustream.tv)

[http://www.ustream.tv/channel/yugui](http://www.ustream.tv/channel/yugui)

# Events

- 8/11
    - 粛々と開発等
- 8/12
    - 9:30 OSSlab 集合
    - 9:50 島根大学集合
    - 10:00 Ruby (MRI) の今後について議論
        - この agenda は誰のタスク？
    - 13:00 OSSlab に戻り
    - 14:00 高校生の見学？
    - その後は粛々と開発等
- 8/13
    - 粛々と開発等

# Agenda / Issues

合宿で議論したい内容，議論して欲しい内容を追記してください．

## trunk

- トレース命令のポリシ（ささだ）
    - 入れる
- Class#dup をどうするのか（ささだ）
    - まつもとさんが対応した
- Cからのyield呼び出しの高速化（まつもと）
- universal newlineの是非 (usa)
    - 入れる．田中さんが実装を頑張る
    - t: Universal newline, 無し: 環境依存 (win: text, unix: binary), b: bin
- rails のテストで SEGV する件をなんとかする (ささだ)
    - r18391 (ただし、actionpack でのトリガーは不明)
- Thread#priority をどうするか (ささだ)
    - えんどうさんには obsolete にしたら？　と言われている
    - obsolete (1.8.x で警告？)
- 処理系内部のトレースのためのabstract APIの策定 (yugui)
    - 1.9.2 以降でもいいので今後話し合う．dtrace を想定していたらしい．
- 1.9.1に含む機能を話し合う (yugui)
    - プラットフォームメンテナ募集の用意(yugui)
    - 話し合った
- nightly build && nightly coverageの方針決定、人のアサイン(yugui)
    - 話し合った
- 1.9.1から削除するライブラリを確認する (okkez)

## Redmine (yugui)

- 寄せられているバグを修正
- RESTful API実装

## リファレンス

- 島根大プロジェクト関連
- ライセンス変更
- システムのバグをひたすらfix
- リファレンス自体は原則書かない (あまり時間がなさそうなので)
- リファレンスを書く (okkez)

## リリース関連 (shyouhei)

- ポリシーについて
    - 議論中

## 島根大関連

今回は、島根大学の特定研究プロジェクト「オープンソース・ソフトウェアの安定化とビジネスモデルの構築に関する研究」（7/25 プレスリリース）

本研究では、プログラミング言語Rubyの安定化・高度化の研究と安定化を担保するためのドキュメンテーション化の手法の検討を進める。また、産官学と開発コミュニティの連携によるオープンソース・ソフトウェアの開発スタイルに関して、ビジネスモデルの構築と情報サービス産業の生産性に関する実証的・理論的研究を産官学と開発コミュニティの連携によって行い、またその中で教育研究機関としての大学の果たす役割に関しての検証を行う。

このうち前者「プログラミング言語Rubyの安定化・高度化の研究と安定化を担保するためのドキュメンテーション化の手法の検討」と位置づけています。（島根大学 野田）
