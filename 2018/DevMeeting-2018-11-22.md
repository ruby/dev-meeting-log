---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-11-22

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2018/11/22 (Thu)
Time: 14:00-18:00 (JST)
Place: pixiv Inc. (Tokyo, Japan)
Sign-up: https://connpass.com/event/105368/

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.6 timeframe

## Check security tickets

## Carry-over from previous meeting(s)

## From Attendees

* [Feature #15230] RubyVM.resolve_feature_path (mame)
  * I'd like this feature to investigate what will be loaded by require(feature).
* [Feature #15231] Remove `Object#=~` (mame)
  * The method looks useless, and made a trouble at least for me.  I'd like to hear opinions from other committers.
* [Feature #11689] Add methods allow us to get visibility from Method and UnboundMethod object. (yui-knk)
  * I want to introduce this feature to help meta programming. I'd like to hear opinions from other committers.
* [Feature #15286] Proposal: Add Kernel.#expand(*args) (aycabta)
* [Feature #15289] Accept "target" keyword on `TracePoint#enable` (ko1)
* [Feature #15287] New TracePoint events to support loading features (ko1)
* [Bug #6087] How should inherited methods deal with return values of their own subclass? (mame)
  * 6 years ago, matz decided that class A < Array; end; A.new.flatten.class #=> A in 2.X, Array in 3.0. Just confirm: has the decision been still unchanged?

## From non-attendees

* [Bug #14127]  CSV generating UTF-16LE encoded file without BOM (nobu)
  * Although this is not a bug of csv.rb, I'd suggest to enable "`bom|`" flag when writing instead.
    https://github.com/nobu/ruby/tree/bug/14127-bom-header
* [Bug #15285] lambda return behavior regression from #14639 (nobu)
* [Feature #15220] Adding OpenSSL 1.1.1 on Travis CI gcc-8 case (jaruga)
  * To detect an issue for the latest OpenSSL early and guarantee a Ruby version supporting a OpenSSL version.
* [Feature #15301] `Symbol#call`, returning method bound with arguments (zverok)
* [Feature #15302] Proc#with and Proc#by, for partial function application and currying (Ritchie Buitre)
  * Convenient methods for functional programming.
* [Bug #14968] make all pipes and sockets non-blocking by default (normalperson)
* [Feature #13618] Thread::Light for 2.6 [ruby-core:89900] (normalperson)
* [Feature #15317] How to deal with obsolete property values in Unicode 11.0.0 (duerst)
  * A clear idea on this is needed to upgrade to Unicode 11.0.0.
* [Feature #13890] Allow a regexp as an argument to 'count', to count more interesting things than single characters (duerst)
  * I find this missing regularly, and writing it by hand in Ruby is clumsy, so addition would be valuable.
* [Feature #12698] Method to delete a substring by regex match (duerst)
* [Feature #10548] remove callcc (Callcc is now going obsoleted. Please use Fiber.) (ioquatix)
  * It's unofficially deprecated, unsupported in most implementations of Ruby, and not used very much. Do you think it's a good idea to drop support by 3.0?
* [Feature #15327] Proposal: Enable refinements to `#respond_to?` (osyo)
* [Feature #15326] Proposal: Enable refinements to `#public_send` (osyo)
* [Feature #15114] Symbol#to_proc does not work with refinements? (osyo)
  * I want to more refinements!
* [Feature #15330] Proposal: autoload_relative (marcandre)
  * This feature is actually more useful than autoload and should be added to 2.6

----

Please comment your favorite ticket we need to discuss with *the following format*.

```
* [Ticket ref] Ticket title (your name)
  * your comment why you want to put this ticket here if you want to add.
```

Your comment is very important if you are no attendee because we can not ask why you want to discuss about it.

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss about this feature.
```

I don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

## Next dev-meeting

- 2018/12/12 (Wed) 13:30-17:00
    pixiv 6F (Tokyo, Japan)

## About 2.6 timeframe

- RC1 will be released on the end of November
- Code frozen around 12/20
- 2.6.0 at 12/25

## Check security tickets (confidencial)

(not logged)

## Carry-over from previous meeting(s)

- It seems no carry-over issue was there.

## From Attendees

- ## Mame: How about changing the meeting time? 14:00-18:00 → 13:00-17:00


- OK but 12/12 is 13:30-.

- ## \[Feature [#15144](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15144&sa=D&source=editors&ust=1686088712085884&usg=AOvVaw224TkP6VNsxLlh6RTQcaae)\] Enumerator#chain (zverok)


- ## A.k.a. Enumerator#+

- ## knu posted an implementation

- Matz: why not Enumerable#chain ?
- Knu: definitely agreed!

- ## \[[Bug #14127](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14127&sa=D&source=editors&ust=1686088712086925&usg=AOvVaw2Z35sH67fhIcvo-nrSZt2y)\] generating UTF-16LE encoded file without BOM (nobu)


- ## Although this is not a bug of csv.rb, I'd suggest to enable "bom|" flag when writing instead. [https://github.com/nobu/ruby/tree/bug/14127-bom-header](https://www.google.com/url?q=https://github.com/nobu/ruby/tree/bug/14127-bom-header&sa=D&source=editors&ust=1686088712087403&usg=AOvVaw07eDDCfswI7U6fwA0R3jBj)

- Usa: can this be made for “r+” and / or “a”?
- Nobu: the proposed patch writes bom only when the pos is zero.
- Naruse: I want it to avoid writing bom twice, when the file already has one.
- Knu: what should happen for StringIO…?
- Knu: We need a concrete specification first as to when a BOM is written.
- Ko1: file a new ticket for this.

- ## \[Feature [#15230](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15230&sa=D&source=editors&ust=1686088712088348&usg=AOvVaw3deEsz-Hf8yfQK_tb_PV3_)\] RubyVM.resolve\_feature\_path (mame)


- ## I'd like this feature to investigate what will be loaded by require(feature).

- Mame: I want it because I am writing a static analyzer and that wants this kind of thing.
- Shyouhei: I have concerns that this method can be absued.
- Mame: true, but I don’t plan to abuse this.
- Shyouhei: I know you don’t but once this is merged people should start using it anyways.
- Usa: I think it’s a good feature in general; the proposal seems like a rough cut though.
- Matz: I have no strong objection.

- ## \[Feature [#15231](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15231&sa=D&source=editors&ust=1686088712089554&usg=AOvVaw3_0vwrX-wvbeDBvo8epvhX)\] Remove Object#=~ (mame)


- ## The method looks useless, and made a trouble at least for me. I'd like to hear opinions from other committers.

- (nobody is against removal)
- Ko1: NilClass#=~ should remain.
- Mame: what about !~
- Matz: why not delete it also?
- Usa: does it bother?
- Knu: people might have their own =~, so deleting !~ affect them.
- Akira: it seems sequel defines =~ and not for !~

- ## \[Feature [#11689](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11689&sa=D&source=editors&ust=1686088712090731&usg=AOvVaw1QbOo0e_-o7CbtOKGiGO_b)\] Add methods allow us to get visibility from Method and UnboundMethod object. (yui-knk)


- ## I want to introduce this feature to help meta programming. I'd like to hear opinions from other committers.

- Matz: the concept is not if a method is “visible” or not, but if a method is “callable” or not.  So I feel a bit NG to call it visibility.

- ## \[Feature [#15286](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15286&sa=D&source=editors&ust=1686088712091500&usg=AOvVaw1TAEJbmyxvEayuVDw3aTvF)\] Proposal: Add Kernel.#expand(\*args) (aycabta)


- Matz: I don’t like the name.
- Ko1: only the name?
- Matz: I don’t like the feature itself, neither.
- Knu: I have a feeling that it should not call a method.
- Knu: what about Binding#expand ?
- Mrkn: { a: a } is much shorter than binding.expand(a) …
- Naruse: tell us a more realistic use-case.

- ## \[Bug [#15285](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15285&sa=D&source=editors&ust=1686088712092361&usg=AOvVaw24KvmZ8QvBPdvTjFxTY_pR)\] lambda return behavior regression from [#14639](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14639&sa=D&source=editors&ust=1686088712092576&usg=AOvVaw33j50fJzH80cNRKsmeVlf5) (nobu)

- ## \[Feature [#15289](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15289&sa=D&source=editors&ust=1686088712092980&usg=AOvVaw0unzlueUD80UBrUMffkaf5)\] Accept "target" keyword on TracePoint#enable (ko1)


- Matz: OK, do it yourself.

- ## \[Feature [#15287](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15287&sa=D&source=editors&ust=1686088712093478&usg=AOvVaw2QaM8Jxu1jN7Vj2dhgdT5w)\] New TracePoint events to support loading features (ko1)


- Matz: Ok except naming “loaded”. Koichi should name good name.

- ## \[Bug [#6087](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6087&sa=D&source=editors&ust=1686088712093961&usg=AOvVaw0Zl5DkaYXuC2iRWWZJONkJ)\] How should inherited methods deal with return values of their own subclass? (mame)


- ## 6 years ago, matz decided that class A < Array; end; A.new.flatten.class #=> A in 2.X, Array in 3.0. Just confirm: has the decision been still unchanged?

- Matz: It is not obvious in each case if the operation is collecting elements (i.e. making an Array object) or making an altered version of the container object.
- Knu: select() is a good example.  It may be considered a method for obtaining a subset or a method that collects matching elements into an array.  Hash#select() changed to return a Hash because it seemed more useful than returning an alist.
- Nobu: Methods defined by Enumerable all return an array.
- Knu: Making all Array methods return an Array object would make it harder to define your own array-like container by subclassing Array.

- ## \[Feature [#15317](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15317&sa=D&source=editors&ust=1686088712094822&usg=AOvVaw0BWiht9rsVqDDWwtE_dkZ5)\] How to deal with obsolete property values in Unicode 11.0.0 (duerst)


- ## A clear idea on this is needed to upgrade to Unicode 11.0.0.

- Martin: one property value has disappeared.
- Naruse: program error is preferred here because we can be aware of such breakage before actually deploying the code.
- Mame: what about issuing a warning instead?

- ## \[Feature [#13890](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13890&sa=D&source=editors&ust=1686088712095732&usg=AOvVaw03cQlKTH78s-fYWBUNZdV2)\] Allow a regexp as an argument to 'count', to count more interesting things than single characters (duerst)


- ## I find this missing regularly, and writing it by hand in Ruby is clumsy, so addition would be valuable.


- ## \[Feature [#12698](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12698&sa=D&source=editors&ust=1686088712096354&usg=AOvVaw0ErVMZ3NLQBNWZMFHyq0tF)\] Method to delete a substring by regex match (duerst)


## From non-attendees

- ## \[Feature [#15220](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15220&sa=D&source=editors&ust=1686088712096857&usg=AOvVaw3orANVpeO1wzaiWnTRYl5P)\] Adding OpenSSL 1.1.1 on Travis CI gcc-8 case (jaruga)


- ## To detect an issue for the latest OpenSSL early and guarantee a Ruby version supporting a OpenSSL version.

- Shyouhei: I had already added one.

- ## \[Feature [#15301](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15301&sa=D&source=editors&ust=1686088712097438&usg=AOvVaw08rZdhyo_hoxgXp0lUAcmI)\] Symbol#call, returning method bound with arguments (zverok)


- Matz: I’m very faintly against it.

- ## \[Feature [#15302](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15302&sa=D&source=editors&ust=1686088712097954&usg=AOvVaw37vDYz8udDMp2y5h2bPMyY)\] Proc#with and Proc#by, for partial function application and currying (Ritchie Buitre)


- ## Convenient methods for functional programming.

- Matz: which is which? (looks confused at the first glance)
- Matz: I don’t like the name.
- Ko1: Is the naming only concern?
- Matz: Not against the feature but I feel the notation is not right.

- ## \[Bug [#14968](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14968&sa=D&source=editors&ust=1686088712099037&usg=AOvVaw1kbmTiRYvunskq26MzAlrW)\] make all pipes and sockets non-blocking by default (normalperson)


- Shyouhei: AFAIK there is a problem on Windows.
- Martin: Greg is working on it.
- Shyouhei: Is mingw32 the same with MSVC?
- Naruse: No, but in this case they are not that different because it is about kernel.
- Shyouhei: Greg says it works for him.
- Matz: so the timer thread is already gone?
- Ko1: yes.
- Shyouhei: Eric says that caused race condition around blocking regions.
- Usa: so it is already backwards incompatible? No?
- Matz: maybe we should try it and fix or back out as necessary?
- Naruse: so Eric should commit it today.

- ## \[Feature [#13618](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13618&sa=D&source=editors&ust=1686088712100090&usg=AOvVaw2Yz0V2EUrQG2cH57pWgdm3)\] Thread::Light for 2.6 [ruby-core:89900](https://www.google.com/url?q=http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/89900&sa=D&source=editors&ust=1686088712100336&usg=AOvVaw2E2xLnXfEImCceXhiNoWCQ)


- Matz: will respond.

- ## \[Feature [#10548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10548&sa=D&source=editors&ust=1686088712100796&usg=AOvVaw0zEsCysogJ_umPhZc6Zb2g)\] remove callcc (Callcc is now going obsolete. Please use Fiber.) (ioquatix)


- ## It's unofficially deprecated, unsupported in most implementations of Ruby, and not used very much. Do you think it's a good idea to drop support by 3.0?

- Ko1: callcc has many issues.  We have some workarounds, but that must be insufficient.
- Mame: I don’t think it’s worth deleting because nobody actually uses this feature in production, thus deletion has no practical benefit.
- Matz: deleting codes that concern reentrancy might benefit for speed?
- Usa: for 3x3?
- Matz: may not make it 3x faster.

- ## \[Feature [#15327](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15327&sa=D&source=editors&ust=1686088712101815&usg=AOvVaw0DyqtlUC70v-eY9yFbFhNa)\] Proposal: Enable refinements to #respond\_to? (osyo)


- Matz: Mmm.
- Ko1: when respond\_to says false, we can send that method anyways?
- Usa: yes.
- Knu: there are implicit and explicit respond\_to. Is it for explicit one?
- Usa: yes.
- Matz: respond\_to does not interact with refinements right now is/was intentional because refinements are file-scope; it should be immediately obvious what method is callable or not at looking at the source code.
- Ko1: what about respond\_to\_missing?
- Matz: redefining respond\_to\_missing at a refinements should be against local rebinding.
- Shyouhei: but I think we can undef a method inside of a refinement.
- (everybody started thinking...)
- Ko1: OK, let’s not think about it for a while.

- ## \[Feature [#15326](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15326&sa=D&source=editors&ust=1686088712103265&usg=AOvVaw3AC8Vwzk343AgoNcmaXwTZ)\] Proposal: Enable refinements to #public\_send (osyo)


- Matz: this one makes sense. Accepted.

- ## \[Feature [#15114](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15114&sa=D&source=editors&ust=1686088712103966&usg=AOvVaw1MAJ1l4hfeQN7N5jetOuxt)\] Symbol#to\_proc does not work with refinements? (osyo)


- ## I want to more refinements!

- Matz: sounds like a bug?
- assign nobu

- ## \[Feature [#15330](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/15330&sa=D&source=editors&ust=1686088712104834&usg=AOvVaw2Cjz0EDUw_mngr-bISmGIQ)\] Proposal: autoload\_relative (marcandre)


- ## This feature is actually more useful than autoload and should be added to 2.6

- Matz: why people love autoload, why…?
- Naruse: What is new in this issue is not why, but how much people love autoloads.
- Ko1: Marc-Andre recently changed requires in stdlib to require\_relative.  I guess it is natural for him to think about autoloads next.
- Shyouhei: why not make a new method called autoload\_relative, which is an alias of require\_relative? :trollface:
- Knu: It is understandable to me that this may cover most popular use cases of autoload.
