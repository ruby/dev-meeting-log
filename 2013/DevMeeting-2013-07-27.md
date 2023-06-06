---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-07-27

* Date: 2013-07-27
* Time: 13:00-18:00
* attendees: see http://cruby.doorkeeper.jp/events/4906
* 議事録: https://docs.google.com/document/d/1Ie6HNoxCptIUe52I3XCxDLn52FtE_RWaF8lrDIXAVmc/pub

## Agenda

### Ruby 2.1 release schedule

* Next meeting date is 31 Aug

### Features of Ruby 2.1

* [ruby-core:38666] [ruby-trunk - Feature #5138][Assigned] Add nonblocking IO that does not use exceptions for EOF and EWOULDBLOCK
  * [slide](https://dl.dropboxusercontent.com/u/582984/again_5138.pdf)
* [ruby-dev:42151] [Feature #3753] value of def-expr
  * [slide](https://dl.dropboxusercontent.com/u/957833/def-expr.pdf)
* [ruby-core:55993] [ruby-trunk - Feature #8632][Assigned] Remove warnings for Refinements
* tail call optimization の言語としての位置づけ
* [ruby-core:56148] [ruby-trunk - Feature #8678][Assigned] Allow invalid string to work with regexp
* Thread に名前をつける話 https://twitter.com/tanaka_akr/status/296422146236895232
* [Enumerable#to_h](http://bugs.ruby-lang.org/issues/7292)
* [Module#prepended_modules](http://bugs.ruby-lang.org/issues/8026)
* [Set#rehash](http://bugs.ruby-lang.org/issues/6589)
* [Set#intersect](http://bugs.ruby-lang.org/issues/6588)
* [Hash#slice, Hash#except](https://bugs.ruby-lang.org/issues/8499)
* [Rational number literal](https://bugs.ruby-lang.org/issues/8430)
* キーワード引数の別名
  * https://twitter.com/n0kada/status/356963333309599746
* [ruby-core:55538] [ruby-trunk - Feature #8539] Unbundle ext/tk
* [ruby-core:56123] [ruby-trunk - Feature #8671] support SEEK_DATA and SEEK_HOLE
* [ruby-dev:46523] [ruby-trunk - Feature #7368] rb_str_each_line()のパフォーマンス向上とリファクタリング
* Feature #8643 Add Binding.from_hash
* Feature #8629 Method#parameters should include the default value
* Feature #8553 Bignum#size (and Fixnum#size)
* Feature #8658 Process.clock_gettime
* Feature #6065 Allow Bignum marshalling/unmarshalling from C API

### Announcement

* Upgrading bugs.ruby-lang.org to redmine 2.3.
* Ruby Prize
