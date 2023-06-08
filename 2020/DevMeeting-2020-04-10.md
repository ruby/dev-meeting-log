---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2020-04-10

# The next dev meeting

**Date: 2020/04/10 13:00-17:00**
Place/Sign-up/Agenda/Log: https://docs.google.com/document/d/1Gcs38tvI6_dQ5sgy8CNUIVZbt0c_y_Z2GrCAoDwh5wg/edit#

- Dev meeting *IS NOT* a decision-making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about the discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place are scheduled according to when/where we can reserve Matz's time.
- *DO NOT* discuss then on this ticket, please.

# Call for agenda items

If you have a ticket that you want matz and committers to discuss, please post it into this ticket in the following format:

```
* [Ticket ref] Ticket title (your name)
  * Comment (A summary of the ticket, why you put this ticket here, what point should be discussed, etc.)
```

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss this feature.
```

- Comment deadline: 2020/04/03 (one week before the meeting)
- The format is strict.  We'll use [this script to automatically create an markdown-style agenda](https://gist.github.com/mame/b0390509ce1491b43610b9ebb665eb86).  We may ignore a comment that does not follow the format.
- Your comment is mandatory.  We cannot read all discussion of the ticket in a limited time.

# Log

https://bugs.ruby-lang.org/issues/16693
Venue
4/10 (Fri) 13:00-17:00 @ online
Next Date
2020/05/14 (Thu) @ 13:00-17:00 @ online
Announce
Ruby 2.7 timeframe
branch maintainer slide
2.7: nagachika
2.6: usa
2.5: usa
2.4: EOL
need to announce 2.4 EOL on www.r-l.o
About 2.8/3.0 timeframe
No topic
To move to 3.0, anything new is wanted
Should be fixed: https://github.com/ruby/ruby/pull/3010
Check security tickets
[secret]
[Feature #16428] Add Array#uniq?, Enumerable#uniq? (greggzst)
it seems to be an easier solution to check directly if an enumerable is uniq instead of using uniq with other means
Preliminary discussion:
[1, 3, 2].uniq? #=> true
Naming issue (difficult to understand the behavior by name)
Discussion:
matz: use case?
mame: found 50 lines by gem-codesearch: uniq.size\ ==.*.size
Conclusion:
matz: I’ll ask use case
[Feature #15166] 2.5 times faster implementation than current gcd implmentation (greggzst)
Preliminary discussion:
ko1: assigned to mrkn
Discussion:


Conclusion:
As mrkn is absent, postponed to the next meeting
[Feature #13436] Improve performance of Array#<=> with Fixnum/Float/String elements (greggzst)
performance in general
Preliminary discussion:
mame: watson should handle by himself
Discussion:
usa?: redefinition may be ignored?
ko1: Some similar (non-safe) optimizations have been introduced already
nobu: no, they check redefinition
Conclusion:
ko1: Will pass the ball to watson as he is a committer
[Feature #15921] R-assign (rightward-assignment) operator (alanwu)
Curious to hear from matz about this. I think this feature is convenient in the REPL and can improve readability in real code if used well.
Preliminary discussion:
ko1: matz should answer
Discussion:
m a => b  # -> m(**{a => b}) # unchanged
m(a => b) # -> m(**{a => b}) # unchanged
m((a => b)) # -> m((b = a))  # 2.7 SyntaxError
m (a => b)  # -> m((b = a))  # 2.7 SyntaxError


matz: I am still positive for R-assign, but => seems confusing
ko1: => is same as Hash syntax. no problem?
matz: other operator?
>= is already used as comparison
a → b
a =>> b
a ==> b
a >>> b
a |> b
a ~= b
a ~> b
a (>) b
a in to b
a :arrow_right: b
non-ASCII operator/method may be acceptable in 10 years?
matz: acceptable choice: => or =>>
nobu: R-assign for Rocket
znz: => is used in rescue => e syntax. It is already R-assignment.
Conclusion:
matz: accept. matz will write a comment to the ticket soon
[Bug #16660] Struct#deconstruct_keys inconsistent behavior (palkan)
Any feedback on the proposed behaviour in the first comment
Discussion:
klass = Struct.new(:a, :b)
s = klass.new(1, 2)
s.deconstruct_keys([:a, :a, :a]) #=> {}
s.deconstruct_keys([:c, :a]) #=> {}
s.deconstruct_keys([:a, :c]) #=> {:a=>1}


matz: Look inconsistent
ko1: The API is sufficient for pattern matching. The method should not guarantee more than needed.
mame: Agreed.
Conclusion:
matz: Will reply soon
[Bug #14541] Class variables have broken semantics, let’s fix them (jeremyevans0)
Is it OK to commit pull request to turn class variable warnings into RuntimeErrors?
Preliminary discussion:
Matz is positive because it shows warning (#8).
toplevel @@cvar shows warning without -w, but overtaken @@cvar is warned with -w
Conclusion:
matz: go ahead
[Feature #16742] RbConfig.windows? and RbConfig.host_os (eregon)
Thoughts? Should we add them?
Preliminary discussion:
mame: cygwin is a windows? WSL is a windows?
nobu: It highly depends on what feature we want to check if available or not. Path? Permission? Drive letter? UNC path? Pathname case?
Discussion:
matz: agreed with nobu. .windows? is too rough.
usa: RbConfig.has_drive_letter?, RbConfig.accept_backslack_as_path_separator?, etc.
matz: RbConfig.host_os looks good?
mame: I’m unsure which host_os or target_os is suitable
(difficult discussion…)
akr: target_os does not make sense in Ruby. The concept of build/host/target is for gcc cross compiling, but Ruby is not a compiler language. So host_os is always equal to target_os.
nobu: How about introducing RbConfig.platform that returns RUBY_PLATFORM in MRI. JRuby and other implementation may return other string that they like.
usa: RbConfig.running_platform ?
Conclusion:
matz: Reject .windows?. I’m okay for RbConfig.host_os. Will reply.
[Feature #16688] Allow #to_path object as argument to system() (Dan0042)
system/exec should be compatible with Pathname objects
Discussion:
matz: Looks good
akr: went though the spec of spawn and system, and almost all string arguments can accept Pathname (except open mode string).
Conclusion:
akr: Positive. Will reply
[Feature #16740] Deprecating and removing the broken Process.clock_getres (jeremyevans0)
Is it OK to deprecate Process.clock_getres in 3.0 and remove it in 3.1?
Preliminary discussion:
mame: I personally like to remove just the spec
mame: Let’s ask akr who introduced the API
Discussion:
akr: We don’t want to remove the method. (1) removing a method is incompatible and (2) OS implementation of clock_getres may be improved in future.
Conclusion:
akr: will reply
[Bug #6087] How should inherited methods deal with return values of their own subclass? (greggzst)
It’s been a long time and matz said it was to be fixed in 3.0
Preliminary discussion:
mame: We recently discussed informally this ticket, and matz withdrawed the old conclusion.
mame: Unless there is a person who is interested in triaging, it will be untouched because we don’t have to hurry.
Discussion:
irb(main):001:0> class A < Array; end
=> nil
irb(main):002:0> a = A.new
irb(main):003:0> a << 0 << 1 << 2 << 3
=> [0, 1, 2, 3]
irb(main):004:0> a.partition {|n| n.even? }.class
=> Array
irb(main):005:0> x, y = a.partition {|n| n.even? }
irb(main):006:0> x.class
=> Array


Conclusion:
matz: I want to see the impact of the incompatibility. Contribution is welcome.
[Bug #14413] -n and -p flags break when stdout is closed (nobu)
only in the loop / always signal on EPIPE
STDOUT only / + STDERR / generic for IO
Preliminary discussion:
akr: I think it would be useful (or Unix-ish) even without -n/-p.
akr: How about treat EPIPE error at write system call to STDOUT as error without error message (signal exception)?
akr: It would be too much for all other IOs such as sockets. STDERR may be on border line.
Discussion:
$ perl -e 'print STDERR "a\nb\n"' 2>&1 |  head -n 1
a


Conclusion:
(mame: didn’t listen the discussion)
[Feature #16684] Use the word “to” instead of “from” in backtrace (sawa)
Be free of wondering about the printed backtrace direction by using the word “to” in most-recent–call-last situation.
Conclusion:
matz: “to” is unacceptable. Removal of “from” may be acceptable
[Feature #16769] Struct.new(…, immutable: true) (k0kubun)
I wanted it today, and found the discussion of [Feature #16122] stuck with designing helpers to set a combination of attributes. Is there any objection to introduce immutable: true attribute alone first?
Preliminary discussion:
ko1: I prefer freeze: true because it is easy to explain the behavior (documentation).
Discussion:
irb(main):001:0> Struct.new("Value", :a, :b)
=> Struct::Value


Conclusion:
matz: I prefer #16122 to this proposal. Rejected.
[Feature #16746] Endless method definition
https://github.com/ruby/ruby/pull/2996
def hello(name) =
  puts("Hello, #{ name }")


hello("endless Ruby") #=> Hello, endless Ruby


def inc(x) = x + 1


p inc(42) #=> 43


x = Object.new


def x.foo = "FOO"


p x.foo #=> "FOO"


def fib(x) =
  x < 2 ? x : fib(x-1) + fib(x-2)


p fib(10) #=> 55


matz: Python has an extention language named “Coconut” which has similar oneliner method definition. I want to have similar one for Ruby.
def fib(x) = (
  stmt
  stmt
  stmt
  return expr
)


def fib(x) =
  if x < 2
    x
  else
    fib(x-1) + fib(x-2)
  end


def fib(x) = begin
  stmt
  stmt
  stmt
  return expr
end


x = 1
def foo = x # NameError: x


# empty body
def foo; end # compatible
def foo = nil
def foo = p
def foo = ()
def foo =; # syntax error


def foo=(x)=@x=x
def foo=(x)=x=>@x
def foo=>@x
def foo(@x) = @x


def foo=nil
def foo=(x)=@x=(x)=foo


Conclusion:
matz: Let’s give it a try
[Feature #16754] Pager for --help
matz: Looks good
