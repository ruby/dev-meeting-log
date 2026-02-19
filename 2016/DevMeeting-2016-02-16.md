---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-02-16

Date: 2016/02/16 (Tue)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.4 based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is very busy person, so we can ask him directly on this opportunity. If you can not attend there, then other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All of activities are best-effort (please remind that most of us are volunteer developers).
  * The date, time and place is when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name. Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## From Attendees

* example: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [Feature #12046] Allow attr_reader :foo? to define instance method foo? for accessing @foo (mrkn)
* progress report: mmap managed heap (arena) (ko1)
* [MRI servers survey](https://docs.google.com/spreadsheets/d/1zx-1D8rywJTysD79IKLW7UzgDCAO123j4FB3-PqrBWA/edit?usp=sharing) (ko1)
* [Feature #11999]: MatchData#to_h to get a Hash from named captures (sorah)
* [Feature #12043] `NoMethodError#private_call?` (nobu)
* [Bug #11991] `Symbol#match` (nobu)
* [Feature #10121] `Dir.empty?` (nobu)
* Allow kwargs to `return`/`next`/`break` (nobu)
* [Feature #12024] Add String.buffer, for creating strings with large capacities (akr)

## From non-attendees

* [Feature #11666] `IPAddr#private?` (glass_saga)
  * naming issue: `private?` or `private_addr?`

(Additional explanation is welcome because we can't ask about it immediately)

Date: 2016/02/16 (Tue) 2pm JST

Members: hsbt, akr, mrkn, shyouhei, naruse, nobu, ko1, sorah, matz (skype)

# Next meeting: 2016/03/16 14:00- JST

# Chatting

- chkbuild repositoriy is transfered from akr’s to ruby’s.

- Ruby committers can commit to it.
- If ruby and chkbuild conflicts, committers can fix ruby or chkbuild.

- gem-codesearch

- We want to share gem-codesearch as a service.
- [https://github.com/etsy/hound](https://github.com/etsy/hound) is webapp which uses the algorithm same as [https://github.com/google/codesearch](https://github.com/google/codesearch).
- What we need is a server with big (such as 1Tbytes) storage.

## \[[Misc #12004](https://bugs.ruby-lang.org/issues/12004)\] CoC

- Matz does not want to include CoC in repository nor release tarball
- Based on PostgreSQL’s CoC v2

- [http://www.postgresql.org/message-id/56A2E9C5.2040707@commandprompt.com](http://www.postgresql.org/message-id/56A2E9C5.2040707@commandprompt.com)

- Create post on [www.ruby-lang.org](http://www.ruby-lang.org)

- Link on bugs.ruby-lang.org new account page and w.r-l.o ML list?

- Show CoC on Redmine account creation page
- Finally, Matz will post a link on the ticket #12004
- Q. Is the proposed CoC acceptable for people who wants CoC?

- matz: it is not acceptable for all people, some people wants banning

- Q. this CoC doesn’t have enforcement. Does the CoC (people think) require that?

- matz: I checked CoCs in other projects. I think it is not requirement.

- We may put contact in future

- Q. why we don’t do now?

- A. It makes responsibility to us

- Applied area: (“collaborative space”)

- A. bugs.ruby-lang.org, commit comments + etc?
- Include MLs? [http://lists.ruby-lang.org/cgi-bin/mailman/listinfo](http://lists.ruby-lang.org/cgi-bin/mailman/listinfo)

- A. No

GitHub: web [https://github.com/ruby/www.ruby-lang.org/issues](https://github.com/ruby/www.ruby-lang.org/issues)

## \[Feature [#11666](https://bugs.ruby-lang.org/issues/11666)\] IPAddr#private? (glass\_saga)

- feature issues

- conflict with private visibility

- “private?” is different from visivility

- Behavior when address family is IPv6

- nil? false?
- or treat link-local, site-local (deprecated), unique-local as true?

- Naming differencies with Addrinfo

- The name “ipv4\_private?” is clear that the method returns false on IPv6 addresses.

## \[Feature [#12046](https://bugs.ruby-lang.org/issues/12046)\] Allow attr\_reader :foo? to define instance method foo? for accessing @foo (mrkn)

- matz: Still negative; I’m not good to have difference in ivar name and method name
- behavior issues:

- Explicitly returning true or false?

- Mrkn will make a new gem.

## progress report: mmap managed heap (arena) (ko1)

Reported by ko1.

---

## \[Feature [#11999](https://bugs.ruby-lang.org/issues/11999)\] MatchData#to\_h to get a Hash from named captures (sorah)

- #to\_h is inappropriate name while non-named capture exists

- Return Hash with integer keys? ( {0 => "a", 1 => "b"} )
- #named\_captures

- matz: acceptable

- Behavior when there are multiple named captures with same name

- Return last matched value

- reg = /(?<a>b)|(?<a>x)/ \# => /(?<a>b)|(?<a>x)/
    reg.match("abc").to\_h #=> {"a" => "b"}

- Behavior when named captures didn’t match anything

- Return nil as value

- Behavior when no named captures

- #captures return \[\] when no capture, so #named\_captures returns {} when no named capture

## \[Bug [#9810](https://bugs.ruby-lang.org/issues/9810)\] Numeric#step behavior with mixed Float, String arguments inconsistent with documentation

This issue is because of invalid type, not mismatch the number of arguments.

Comparison String with Numeric should raise TypeError

matz: I once thought type mismatch also infringe the contract of arguments, therefore it can be ArgumentError but now they should be separated.

matz: It should raise TypeError.

---

## Allow kwargs to return/next/break

$ ruby -e 'def foo;return :a=>1; end'

$ ruby -e 'def foo;return a: 1; end'

\-e:1: syntax error, unexpected ':', expecting keyword\_end

def foo;return a: 1; end

 ^

matz rejected it because return is not a method call.  Matz wants to split kwargs versus trailing-hash-arg in a long term.  He wants to distinguish them mentally.  Rocket notation is not always related to kwargs while colon notation is tightly-connected, even though it is also used in hash literals.  When it comes to a method, calling a method with keyword-argument does not always create hashes.

## \[Feature [#10121](https://bugs.ruby-lang.org/issues/10121)\] Dir.empty?

- \`Dir.entries(dir).size == 2\` is not efficient.  Also not platform-agnostic.
- It is useful to write spec which checkes test files are crrectly cleaned.
- matz: accepted.

## \[Feature [#12075](https://bugs.ruby-lang.org/issues/12075)\] some container#nonempty?

- nobu: you can write \`ary&.empty?.!\`.
- mrkn: How about \`ary.include\_something?\` ?

## \[Feature [#12024](https://bugs.ruby-lang.org/issues/12024)\] Add String.buffer, for creating strings with large capacities

akr: Once matz rejected String.new(size) proposed on [#905](https://bugs.ruby-lang.org/issues/905), but now we have kwargs. given that there already is String.new with encoding argument, isn’t it OK to add kwargs to that method?

matz: accepted.

---

## \[Bug [#11991](https://bugs.ruby-lang.org/issues/11991)\] Symbol#match

## \[Feature [#12043](https://bugs.ruby-lang.org/issues/12043)\] NoMethodError#private\_call?

bikeshed problem (naming issue).

- private\_call?
- private\_callable?
- were\_you\_private?
- funcall\_style\_call?
- …
