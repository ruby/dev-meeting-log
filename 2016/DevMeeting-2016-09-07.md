---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-09-07

Date: 2016/09/07 (Wed)
Time: 14:00- 18:00 (JST)
Place: [TKP Kyoto Shijo Karasuma Conference Center, Kyoto](https://www.google.co.jp/maps/place/TKP%E4%BA%AC%E9%83%BD%E5%9B%9B%E6%9D%A1%E7%83%8F%E4%B8%B8%E3%82%AB%E3%83%B3%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9%E3%82%BB%E3%83%B3%E3%82%BF%E3%83%BC/@35.0013188,135.7564574,17z/data=!4m8!1m2!2m1!1z5Lqs6YO95bqc5Lqs6YO95biC5LiL5Lqs5Yy65LuP5YWJ5a-66YCa5a6k55S65p2x5YWl6YeY6Zqg55S6MjQ355Wq!3m4!1s0x600108990c8a12a3:0x3a52021f15e1ed15!8m2!3d35.0011781!4d135.7588506
), Japan
Place in Japanese: [TKP京都四条烏丸カンファレンスセンター](http://www.kashikaigishitsu.net/facilitys/cc-kyoto-shijokarasuma/access/)

dinner: http://www.kyo-taisanboku.com/original8.html

Attendees/参加者: matz, ko1, hsbt, nobu, akr, mrkn, duerst, sorah, ktsj, eregon, sonots, tenderlove, shugo, yhara, shyouhei, naruse
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe in this meeting we discuss about Ruby 2.4, based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is a very busy person.  Take this opportunity to ask him. If you can not attend, other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All activities are best-effort (keep in mind that most of us are volunteer developers).
  * The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name (see example below). Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## About 2.4 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24
* preview 2?

## About "ruby committers vs. the world"

(shyouhei) we have "ruby committers vs the world" panel discussion on Sept. 10th, what should we do?

## Carry-over from previous meeting(s)

* [Feature #10098] [PATCH] Timing-safe string comparison for OpenSSL::HMAC (shyouhei) status?
* [Feature #12591] Allow ruby to either catch misspelled "ailas" statements or, possibly more accurately, be more specific in what it reports as an error to the end-user (shyouhei)
* [Feature #9704] Refinements as files instead of modules (shyouhei) conclusion?
* [Feature #8643] Add `Binding.from_hash` (shyouhei) conclusion?
* [Feature #12650] Use UTF-8 encoding for `ENV` on Windows (shyouhei)
* [Bug #9569] `SecureRandom` should try `/dev/urandom` first (shyouhei) I'd like to hear other opinions.

## From attendees

* [Feature #12512] Import `Hash#transform_values` and its destructive version from ActiveSupport (eregon): we should discuss the name and maybe other methods like `#map_keys`, `#map_pairs`.
* [Bug #12689] Thread isolation of `$~` and `$_` (eregon): Who can describe the semantics? Is the current sharing expected?
* [Feature #6842] Add Optional Arguments to `String#strip` (sonots): I am currently working on implementing this, but am wondering about specification for regular expression argument, and arguments for strip
* [Feature #859] open-uri doesn't allow redirection to https (duerst): This seems overdue
* [Feature #7418] `Kernel#used_refinements` (shugo)
* [Feature #9451] Refinements and unary `&` (`to_proc`) (shugo)
* [Bug #10103] Unable to refine class with CONSTANT (shugo)
* [Bug #11182] Refinement with alias causes strange behavior (shugo)
* [Feature #11476] Methods defined in Refinements cannot be called via send (shugo)
* [Feature #11525] Add `Module#used` (refinement hook) (shugo)
* [Bug #11704] Refinements only get "used" once in loop (shugo)
* [Feature #12079] Loosening the condition for refinement (shugo)
* [Feature #12086] `using:` option for instance_eval etc. (shugo)
* [Feature #12533] Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shugo)
* [Feature #12534] Refinements: refine modules as well (shugo)
* [Feature #11650] Add custom error message arg to `Timeout.timeout` (nobu)
* [Bug #12548] Rounding modes inconsistency between `round` versus `sprintf` (nobu)
* [Bug #12588] When an exception is re-raised in the "`rescue`" clause, the back trace does not contain the line in that clause (nobu)
* [Feature #12690] Improve GC with external library that may use large memory (nobu)
* [Feature #12695] `File.expand_path` should resolve `~/` using `/etc/passwd` when `HOME` is not set (nobu)
* [Feature #12700] regexp heredoc support (nobu)
* [Bug #12709] POSIX-noncompliant `setenv` (nobu)
* [Feature #12732] Option to return nil instead of raise exceptions from `Integer()` and `Float()`
* [Feature #12733] Bundle bundler to ruby core

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)

# Log

Attendees: akr, naruse, mrkn, sonots, hsbt, ko1, eregon, zzak, sorah, ktsj, nobu, tenderlove, duerst, shyouhei, shugo, matz

Venue: TKP Kyoto Shijo-karasuma Conference Center

[https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20160907Japan](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20160907Japan&sa=D&source=editors&ust=1686086903371750&usg=AOvVaw3h-Ezlz0Vufuf0h-KtQA84)

# Next meeting:

- when: Oct 11th, 14:00-
- venue: TBA

- not sure, somewhere in Tokyo

## About 2.4 timeframe

- [https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24&sa=D&source=editors&ust=1686086903372978&usg=AOvVaw1-OfhAwAw8qMeZOdUl3LRj)
- preview 2 in two days

## About "ruby committers vs. the world"

(shyouhei) we have "ruby committers vs the world" panel discussion on Sept. 10th, what should we do?

- (ko1) what about call for questions?
- (nobu) maybe can we discuss issues?

## Carry-over from previous meeting(s)

- \[Feature [#10098](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10098&sa=D&source=editors&ust=1686086903374283&usg=AOvVaw2n4SLKv1rUfFtNMPX8a5aO)\] \[PATCH\] Timing-safe string comparison for OpenSSL::HMAC (shyouhei) status?

- naruse: I don’t feel comfortable to its naming.
- naruse: I think CRYPTO\_memcmp() is a bad interface so I don’t want to introduce it.

- \[Feature [#12591](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12591&sa=D&source=editors&ust=1686086903374788&usg=AOvVaw0wm3NJ3-X8jCPYBMdgPZ_w)\] Allow ruby to either catch misspelled "ailas" statements or, possibly more accurately, be more specific in what it reports as an error to the end-user (shyouhei)

- matz: use an editor that supports ruby syntax.

- \[Feature [#9704](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9704&sa=D&source=editors&ust=1686086903375178&usg=AOvVaw0m6bHPsjPvF84-cz76s8WC)\] Refinements as files instead of modules (shyouhei) conclusion?

- shugo: matz please respond.

- \[Feature [#8643](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8643&sa=D&source=editors&ust=1686086903375523&usg=AOvVaw1tqTPd7LOre1xNN2Sj4XjA)\] Add Binding.from\_hash (shyouhei) conclusion?

- ko1: I’ll ask seki-san.

- \[Feature [#12650](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12650&sa=D&source=editors&ust=1686086903375923&usg=AOvVaw3vfT0K-aK_L2vGQxFC4VLN)\] Use UTF-8 encoding for ENV on Windows (shyouhei)

- duerst: is it just we dont want to touch this, or we have agreements we would move to UTF-8 later?
- naruse: I’ll ask usa-san

- \[Bug [#9569](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9569&sa=D&source=editors&ust=1686086903376474&usg=AOvVaw2RIruyMnJUk0iVQJtTzovo)\] SecureRandom should try /dev/urandom first (shyouhei) I'd like to hear other opinions.

- akr: we can’t but neglect this issue as-is.
- shyouhei: we have no critical security issue right now.

- \[Misc [#12283](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12283&sa=D&source=editors&ust=1686086903377273&usg=AOvVaw1qFRTEG9zhm4BmpWZziyio)\] Obsolete ChangeLog and commit message in Git-style (shyouhei) situation and progress?

- naruse: no move
- duerst: PLEASE!

## From attendees

- \[Feature [#12512](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12512&sa=D&source=editors&ust=1686086903378486&usg=AOvVaw1o8l4iKDDAvXzRna3Uc4ef)\] Import Hash#transform\_values and its destructive version from ActiveSupport (eregon): we should discuss the name and maybe other methods like #map\_keys, #map\_pairs.

- matz: I have a plan to have similar methods also on Enumerable.  In doing so, map\_pairs is problematic because “pairs” does not always fit (they might not be key-value pairs).  This prevents me to introduce map\_keys/values/pairs.
- matz: OK, I accept #transform\_values.
- naruse: Is this behavior is same as ActiveSupports’s?
- mrkn: Yes.
- eregon: #map\_values, keys, pairs is intuitive on Hash and has been asked many times. The Enumerable method to create a Hash is a different issue: the input might aready be key-value pairs in which case #to\_h is enough or it might not, in which case we might want a new method (build\_hash, associate ?).

- \[Bug [#12689](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12689&sa=D&source=editors&ust=1686086903379777&usg=AOvVaw1Q5aWqzRt-bIAqhI7ZBUOT)\] Thread isolation of $~ and $\_ (eregon): Who can describe the semantics? Is the current sharing expected?

- ko1: I intentioanlly made VM this way, to behave simiarly with 1.8.x.
- ko1: Toplevel $-variables are thread local, others like variables inside of methods are not. They are normal (local) variables, subject to be shared among threads.
- matz: I think $-variables should ideally be thread-local as well as scope local.
- akr: should we deprecate $~?
- ko1: is that possible? can we deprecate $~?
- naruse: no. #gsub and #scan cannot be written without $~.
- nobu: I’m against changing this today.
- akr: agreed.  This is a long-term issue.
- akr: it can be an unwise idea to change this behaviour right now, but we should discuss the future of this $~ variable instead.
- eregon: so $~ is pretty much just a local variable (always declared in the method), except at top-level? In that case it makes sense to capture the value with a block like other local vars. Is it just literally shortcut for “md = /foo/.match(“foo”); p md” => “/foo/ =~ “foo”; p $~” ?
- ko1: a bit different.

- md = /foo/.match(“foo”); p md #=> MatchData
- 1.times{md = /foo/.match(“foo”)}; p md #=> Error

- akr: thread-local is not only top-level: def foo() /foo/ =~ "foo"; Thread.new { p $~ }.join end;foo # prints nil
- eregon: so how does this work? $ ruby23 -e '
    p = proc { p $~; "foo" =~ /foo/ };
    def foo(p);
     Thread.new {p.call}.join; # should set $~ at top-level (thread-locally)
     Thread.new {p.call}.join; # should not read it the other thread $~
    end;
    foo(p)'
    nil
    #<MatchData "foo">

- \[Feature [#6842](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/6842&sa=D&source=editors&ust=1686086903381320&usg=AOvVaw3IFJ3UGSKYzUbW4b7BLZ0M)\] Add Optional Arguments to String#strip (sonots): I am currently working on implementing this, but am wondering about specification for regular expression argument, and arguments for strip

- sonots: I want this when I write fluentd’s routing engine.
- nobu: I think we can have this sort of thing, but #strip is not the right place to add it.
- nobu: what is needed is a “left-most chomp” it seems.
- shugo: any chance python has this?
- sonots: python’s lstrip can take character class.
- mrkn: it seems python does not have things like this.
- matz: please re-submit this with proposing new name.

- \[Feature [#859](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/859&sa=D&source=editors&ust=1686086903382193&usg=AOvVaw1xlrLaTrCe2nKjtI6Yb8Gu)\] open-uri doesn't allow redirection to https (duerst): This seems overdue

- duerst: these days http->https redirects happen more often than before.
- naruse: I don’t remember where exactly is the specification inside of HTML5 where HTTPS redirection is defined
- akr: Maybe we should allow http->https by default.
- akr: Ideally it should be configurable wether redirections are allowed or not but that’s a bit too big.
- Strawman proposal from Carsten Bormann: make configurable, e.g. as follows:
    ALLOW\_REDIRECTS = { ["http:ftp"](https://www.google.com/url?q=http://ftp/&sa=D&source=editors&ust=1686086903383234&usg=AOvVaw0Pxk7j141AOjV_uDdhLXIi) => true, "ftp:http" => true, ["http:https"](https://www.google.com/url?q=http://https/&sa=D&source=editors&ust=1686086903383552&usg=AOvVaw1Dw3rn_g3i3NRYcTjURpX8)
    \=> true }
    s1 = uri1.scheme.downcase
    s2 = uri2.scheme.downcase
    s1 == s2 || ALLOW\_REDIRECTS\["#{s1}:#{s2}"\]

- \[Feature [#7418](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7418&sa=D&source=editors&ust=1686086903383847&usg=AOvVaw17J8OnqwtPlU4vaKX6lqlp)\] Kernel#used\_refinements (shugo)

- shyouhei: what is returned from this method?
- shugo: list of refinements (modules).
- shyouhei: is that useful?  What use case?
- shugo: maybe for debugging purpose.
- nobu: or from pry.
- nobu: I don’t think it’s strange, given there are #included\_modules.
- akr: is its behaviour well-defined?
- shugo: I think yes.
- matz: Should this belong Kernel# or Module. ?
- akr: I don’t think it should be a global function unless this is very frequently used.
- matz: accept Module.used\_refinements

- \[Feature [#9451](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9451&sa=D&source=editors&ust=1686086903384797&usg=AOvVaw0E4F0AZusQvrYOXx1P6qNh)\] Refinements and unary & (to\_proc) (shugo)

- matz: I started thinking this can be okay.
- nobu: I doubt if we can do this.
- akr: what about adding a primitive method Proc#to\_refined or something?
- akr: is that possible?
- matz: Object#method\_with\_refinements or something can be possible.
- matz: for &(proc), it is a good-to-have. if possible.

- \[Bug [#10103](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10103&sa=D&source=editors&ust=1686086903385541&usg=AOvVaw2AJSe7bJoCQO5dLPfv5Cx7)\] Unable to refine class with CONSTANT (shugo)

- shugo: is it okay to close?

- \[Bug [#11182](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11182&sa=D&source=editors&ust=1686086903385906&usg=AOvVaw3ExRX0MW0fSoN9e65JWwl3)\] Refinement with alias causes strange behavior (shugo)
- \[Feature [#11476](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11476&sa=D&source=editors&ust=1686086903386188&usg=AOvVaw1B_6SKDBMKdGqn0vANWFnb)\] Methods defined in Refinements cannot be called via send (shugo)

- matz: either add Object#refined\_send method or Object#method\_with\_refinements.
- nobu: or to have a send-like operator.
- ko1: what’s wrong with send being refinements-aware?
- matz: how is it difficult to modify send?  I want that way.

- \[Feature [#11525](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11525&sa=D&source=editors&ust=1686086903386714&usg=AOvVaw2_GGguviZvDQHxwAz4mcde)\] Add Module#used (refinement hook) (shugo)

- matz: reject because it is too dynamic.

- \[Bug [#11704](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11704&sa=D&source=editors&ust=1686086903387102&usg=AOvVaw3HRMqf4o3RIswRUGmCwf0I)\] Refinements only get "used" once in loop (shugo)

- akr: this is a problem of benchmark
- shyouhei: I understand what OP wanted to do

- \[Feature [#12079](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12079&sa=D&source=editors&ust=1686086903387505&usg=AOvVaw03hiMOqF2Aa9a1mdjT53am)\] Loosening the condition for refinement (shugo)

- duplicated.

- \[Feature [#12086](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12086&sa=D&source=editors&ust=1686086903387865&usg=AOvVaw02MCHo8RV5u-6-U62ax6FL)\] using: option for instance\_eval etc. (shugo)

- waiting for JRuby guys.
- shugo: please nudge.

- \[Feature [#12533](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12533&sa=D&source=editors&ust=1686086903388313&usg=AOvVaw2uFtEcPXFSJyMBx3-YK8LZ)\] Refinements: allow modules inclusion, in which the module can call internal methods which it defines. (shugo)
- \[Feature [#12534](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12534&sa=D&source=editors&ust=1686086903388593&usg=AOvVaw39HuxMflSB7zo_Tb8iLucn)\] Refinements: refine modules as well (shugo)

- nobu: why we can’t?
- shugo: it’s difficult because when “super” is called, module’s method have to seek for proper ICLASS.
- matz: maybe we can restrict calling super from inside of a refined module?

- \[Feature [#11650](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11650&sa=D&source=editors&ust=1686086903389073&usg=AOvVaw2kRTXGhWHdb5n7pdaUyGCB)\] Add custom error message arg to Timeout.timeout (nobu)

- nobu: I have no reason to reject this.
- shugo: does this mean Timeout.timeout to pass its arguments to #new?
- akr: It is considered an idiom to have message right after exception class.
- matz: accepted.

- \[Bug [#12548](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12548&sa=D&source=editors&ust=1686086903389660&usg=AOvVaw1-F26HqN1ehlV8ndn2pRVV)\] Rounding modes inconsistency between round versus sprintf (nobu)

- add optional second argument, like BigDecimal, to take symbol.

- \[Bug [#12588](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12588&sa=D&source=editors&ust=1686086903390104&usg=AOvVaw3iq3B-Q7AfBm3piSm9CWXa)\] When an exception is re-raised in the "rescue" clause, the back trace does not contain the line in that clause (nobu)

- matz: backtrace can be obtained if raised manually.

- \[Feature [#12690](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12690&sa=D&source=editors&ust=1686086903390500&usg=AOvVaw1PJs-MIhLqTO3TFETUy-yu)\] Improve GC with external library that may use large memory (nobu)

- ko1: accept.

- \[Feature [#12695](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12695&sa=D&source=editors&ust=1686086903390901&usg=AOvVaw306UgAQnhIJrivCxBTgYWS)\] File.expand\_path should resolve ~/ using /etc/passwd when HOME is not set (nobu)
- \[Feature [#12700](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12700&sa=D&source=editors&ust=1686086903391374&usg=AOvVaw3b3s_z3wv-rf7yOUsTWnTV)\] regexg heredoc support (nobu)

- matz: rejected

- \[Bug [#12709](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12709&sa=D&source=editors&ust=1686086903391761&usg=AOvVaw3yerVMOXkXcn8A8QXx2i99)\] POSIX-noncompliant setenv (nobu)
