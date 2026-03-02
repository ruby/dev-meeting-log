---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-05-14

# DevelopersMeeting20150514Japan

Date: 2015/05/14 (Thu)
Time: 14:00- 19:00 (JST)
Place: Tokyo, Japan
Remote participation: Slack? Google hangout? Ask ko1.
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

* exapmle: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [Feature #11084] Use rb-readline instead of ext/readline (hsbt)
* [Feature #11083] Gemify net-telnet (hsbt)
* [Feature #11082] Remove condition of RUBY_VERSION < 1.9 (hsbt)
* slack (sorah)
* [Feature #11049] Enumerable#grep_v (inversed grep) (sorah)

## From non-attendees

* [Bug #10967] Remove "private attribute?" warning (zzak)
* [Feature #10984] Hash#contain? (zzak)
* [Bug #10856] Splat with empty keyword args
* [Feature #11140] autoload should call `Kernel.require` to give rubygems a chance to handle loading (tenderlove)
* [Feature #11151] Numeric#positive? and Numeric#negative?

# Log

Attendee: ko1,ayumin,akr,hsbt,naruse; matz,sora_h

## [[Feature #11084]](https://bugs.ruby-lang.org/issues/11084) Use rb-readline instead of ext/readline

hsbt: Ruby needs readline. openssl, zlib to build. rb-readline is a pure Ruby library. Replacing current readline with rb-readline (as a bundled gem) helps build problems. Current readline will be a gem. Now windows ruby installer already use rb-readline.

Problem is no readline library

 (1) no readline

 (2) libedit doesn’t satisfy Ruby’s requirement

ayumin: Alternative or completely replacing?

hsbt: No need to use current readline.

akr: How compatible?

hsbt: No problem using irb.

akr: How many terminals is supported?

hsbt: No idea.

ayumin: Should we support complete readline? Need to ask Matz.

matz: I accept to introduce rb-readline to solve build problems. Gemify is also welcome. Name is problem. Using the name “readline” should be considered. It will conflict current library (behavior).

There are several ideas.

1. Introduce with different name (example rb-readline). “readline” users require rb-readline when current readline can not be loaded (because of build failure)
2. Introduce with same name and replace current readline.
3. User should install rb-readline manually (only one gem)

akr: I feel several difficulties. Because no heavy users in committers, we can’t evaluate quality. We don’t know author’s idea. Using same name with different implementation is danger. And I believe Ruby should use GPL libraries.

Next action:

hsbt: I continue to use rb-readline and try it.

## [[Feature #11083]](https://bugs.ruby-lang.org/issues/11083) Gemify net-telnet

hsbt: no maintainer so it should be gemify.

ayumin: bundle it?

hsbt: Not sure.

Matz: I accept bundled gem.

## [[Feature #11082]](https://bugs.ruby-lang.org/issues/11082) Remove condition of RUBY_VERSION < 1.9 (hsbt)

hsbt: Title is wrong. Not < 1.9, but <= 1.9.

## For path name

akr: no problem.

## Remove such condition

hsbt: Should we remove all such conditions? Rubygems only has such code.

nurse: No problem.

matz: No problem.

## Use newer features aggressively

hsbt: For example, adding keyword parameters support for many options

akr: I have experienced to break compatibility because of such action.

ayumin: Positive because using newer features helps to evaluate such features.

ko1: Use newer features with keeping compatibility.

## [[Feature #11049]](https://bugs.ruby-lang.org/issues/11049) Enumerable#grep_v (inversed grep) (sorah)

matz: are you okay to use the name “grep_v”

sora_h: Personally, grep_v is acceptable.

matz: Other languages?

nurse: grep command has --inverse

ayumin: keyword parameter ?

matz: grep(pattern, inverse: true) ?

matz: but grep_v() is okay. -> accept on ticket.

naruse: How about reject(pattern) ?

## [[Bug #10967]](https://bugs.ruby-lang.org/issues/10967) Remove "private attribute?" warning (zzak)

akr: self.private_something_method is accepted recently.

naruse: test can find problem, so warning is not needed.

matz: accept .

## [[Feature #10984]](https://bugs.ruby-lang.org/issues/10984) Hash#contain? (zzak)

akr: “contain” is too general. “subhash”?

n0kada: “contain?” seems similiar to “include?”

akr: do we really use? we need concrete examples.

## [[Bug #10856]](https://bugs.ruby-lang.org/issues/10856) Splat with empty keyword args

def foo

end

foo(\*\*{}) #=> error

def foo k1: 1

end

foo(\*\*{}) #=> okay

def foo(\*\*)

end

foo(\*\*{}) #=> okay

def foo(h)

end

foo(\*\*{}) #=> okay

The first one seems wrong code, so an error is reasonable.

## [[Feature #11140]](https://bugs.ruby-lang.org/issues/11140) autoload should call Kernel.require to give rubygems a chance to handle loading (tenderlove)

nobu: no problem. -r already calls replaced require().

matz: accept.

## [[Feature #11151]](https://bugs.ruby-lang.org/issues/11151) Numeric#positive? and Numeric#negative?

akr: well usecase.

nurse: can we introduce new syntax to make more lightweight block?

ayumin: why not use ActiveSupport?

matz: accept because it has usecase.

---

# How to implement Range#include? and Range#cover?

nobu: [https://bugs.ruby-lang.org/issues/11113](https://bugs.ruby-lang.org/issues/11113) specialize Time object

How to make general solution?

akr: Only String should use succ.

Next action:

Nobu: Impement Range specializes String and try it.

New syntax proposals

# [[Feature #11141]](https://bugs.ruby-lang.org/issues/11141)  new syntax suggestion for abbreviate definition on block parameters in order
[https://bugs.ruby-lang.org/issues/11141](https://bugs.ruby-lang.org/issues/11141)

Matz: negative

# Passing passed block

ko1: Passing passed block without making a Proc object improves performance of block passing. How about to introduce “&” syntax?

def foo

 bar(&)

end

def foo(&b)

 bar(&b)

end

Matz: positive

# [ruby-list:50135] [ANN] Enumerator::Parallel v0.1.1

Matz: how about to introduce it as bundled gem?

---

# [ruby-list:50120] [ANN] Kasen(下線) v0.1.1

Matz: I like this idea.

Which is favorite?

[1, 2, 3].map { |n| (n + 1).to_s }

[1, 2, 3].map &(_ + 1).to_s     # Kasen enables

[1, 2, 3].map { ($1 + 1).to_s } # use #11141

[1, 2, 3].map { (@1 + 1).to_s } # use #11141

[1, 2, 3].map { (_ + 1).to_s }  # use #11141

[1, 2, 3].map (_ + 1).to_s

[1, 2, 3].map &:+.curry{1}

[1, 2, 3].map &(+ 1)

([1, 2, 3].map + 1).to_s.to_a

Next action: ko1 will add discussion into 11141.

# [[Feature #11105]](https://bugs.ruby-lang.org/issues/11105) ES6-like hash literals
[https://bugs.ruby-lang.org/issues/11105#change-52447](https://bugs.ruby-lang.org/issues/11105#change-52447)

Problem 1: It seems “Set”

Problem 2: What happen with non local variables?

{@a} #=>

1. {:@a => @a}
2. {:a => @a}
3. Error (only local variables are accepted)

Matz: negative.

other idea is providing feature of binding. However, it needs long strokes.

a = b = 1

local_variables #=> [:a, :b]

local_variables #=> {:a => 1, :b => 1}

local_variables_set #=> {:a => 1, :b => 1}

local_variables_get(%i(a b)) #=> {:a => 1, :b => 1}

Binding#local_variable_set(:a, 1)

{a, b, c}

binding.local_variables(%i(a b c))

## Next meeting

2015/06/12 14:00-18:30 @ SFDC JP
