---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-04-08

Date: 2015/04/08 (Wed)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan
Remote participation: Google hangout? Ask ko1.
Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.3 based on tickets.
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

* [Feature #10600] [PATCH] Queue#close (ko1)
* [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [Feature #5481] Gemifying Ruby standard library (hsbt)
* [Feature #10728] Warning for Fixnum#size to use RbConfig::SIZEOF 'long' (akr)

## From non-attendees

* [Feature #8259] atomic attribute accessors (tenderlove)
* [Feature #10932] Enable allocation tracing ASAP (tenderlove)
* [GitHub #858](https://github.com/ruby/ruby/pull/858) Add a RUBY_ENGINE_VERSION constant (tenderlove)

# Log

[https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20150408Japan](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20150408Japan&sa=D&source=editors&ust=1686086236508564&usg=AOvVaw02-lq8-NB_HSkyB1wv47zS)

Attendee:

matz, akr, hsbt, nurse, nobu, ayumin, sora, ko1

## Ruby 2.3

- Schedule: similar to Ruby 2.2 (nurse)

- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23&sa=D&source=editors&ust=1686086236509384&usg=AOvVaw16bhHguN3ku5a0MIfsqRHt)

- Favorite feature

- did you mean gem (matz) -> better exception message mechanism

- Ruby should improve exception message mechanism (ko1, nobu)
- Related: [https://bugs.ruby-lang.org/issues/10982](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10982&sa=D&source=editors&ust=1686086236509817&usg=AOvVaw1SXhuhOMutkVOcwBYm-w85)
- Reverse backtrace outout
- Internationalization (nobu)

- \-> BAD: make it difficult to Google it. (nurse)

- Make a ticket about it.

- Improve require mechanism

- Improve performance

- Search load path

- (1) Bundler solution: narrow load paths
- (2) Cache library path at require

- get mtime and check if the directory’s content is changed or not

- (3) cache directory content before (make file list file)
- (4) zip (or other format) archive

- Compatibility problem (\_\_FILE\_\_, open(\_\_FILE\_\_))

- AOT compile

- need survey (really fast?)
- Exerb approach?
- go’s advantage (packaging)
- Path problem (fixed path)

- Packaging

- See above

- grep -v (sora) -> make a ticket

- grep\_v(re)
- grep!(re) (naruse)
- grep(-/re/) (Regexp#-@, Regexp#~@) (nobu)
- Regexp#~@(nobu)

- Frozen literal string

- [https://bugs.ruby-lang.org/issues/8579](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8579&sa=D&source=editors&ust=1686086236511553&usg=AOvVaw2GYAdI6b3XQu_YauGaQhcJ)
- https://bugs.ruby-lang.org/issues/8976
- rdoc uses force\_encoding where the string is not created
- matz don’t like pragma

- gemify

- test-unit, miniruby gemify -> no problem (as we can see) (hsbt)
- We should extend this approach (hsbt)

<table class="c22"><tbody><tr class="c15"><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">-</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">well maintained</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">abandoned</span></p></td></tr><tr class="c19"><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">Used</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">Depend on maintainer</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">gemify + move into https://github.com/ruby</span></p></td></tr><tr class="c19"><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">Unused</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">?</span></p></td><td class="c11" colspan="1" rowspan="1"><p class="c6"><span class="c2">Remove</span></p></td></tr></tbody></table>

- Type of library

- Standard library

- Traditional files in ruby/lib/...

- Default gem

- Files located in ruby/lib/… (same as standard libraries)
- Do not need to write lib names into Gemfile
- visible from gem list
- We can update libs by gem command

- Bundled gem

- bundled in tarball
- They are installed at ruby install
- We can update libs by gem command

- Normal gem

- Moving stdlib to bundled gem

- host under github/ruby/ (core team don’t need touch them) <- motivation
- next action (hsbt): List up candidates of bundled gems

- GSoC (Rope) (ko1)

- Concatenation is already fast because of double sized buffer (akr)

- Other data structure is needed (Related to Rope) (akr)

- Gem first (matz)

- Methods to certify packages (chatting)

## \[Feature [#8259](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8259&sa=D&source=editors&ust=1686086236515493&usg=AOvVaw3YHInHxjFIfNmsVbHclSjf)\] atomic attribute accessors (tenderlove)

(matz) Let me survy more about it.

## \[Feature [#10932](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10932&sa=D&source=editors&ust=1686086236515913&usg=AOvVaw3D8SgoQ1NEF9XHDb02rmVM)\] Enable allocation tracing ASAP (tenderlove)

(ko1) no problem except the name and “include ObjectSpace”.

## [GitHub #858](https://www.google.com/url?q=https://github.com/ruby/ruby/pull/858&sa=D&source=editors&ust=1686086236516318&usg=AOvVaw1J_iisyTLLojiimXBtU09v) Add a RUBY\_ENGINE\_VERSION constant (tenderlove)

(matz) approved.

## \[Feature [#10728](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10728&sa=D&source=editors&ust=1686086236516734&usg=AOvVaw2-zWcHI-Dgg_YES1xppmLK)\] Warning for Fixnum#size to use RbConfig::SIZEOF 'long' (akr)

(matz) reject.

## Next meeting -> 2015/05/14 (Thu) 14:00- (JST)
