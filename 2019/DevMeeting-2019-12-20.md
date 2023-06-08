---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-12-20

Please comment on your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of the ticket comments)

*DO NOT* discuss then on this ticket, please.

----
Date: 2019/12/20 13:00-17:00
Place, Sign-up, and Log: https://docs.google.com/document/d/18v0UWXwwKaSvOFp1UQbEnt_4p0BMTdoGgcNfjyAWTKs

# NOTES

- Dev meeting *IS NOT* a decision-making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about the discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place are scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.7 timeframe

## Check security tickets

## Discussion

----

Please comment on your favorite ticket we need to discuss with *the following format*.

```
* [Ticket ref] Ticket title (your name)
  * your comment why you want to put this ticket here if you want to add.
```

Your comment is very important if you are no attendee because we can not ask why you want to discuss it.

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss this feature.
```

**Please follow the comment format strictly!**  We'll use [this script to automatically create an markdown-style agenda](https://gist.github.com/mame/b0390509ce1491b43610b9ebb665eb86).  We may ignore a comment that violates the format.

**A short summary of a ticket is strongly recommended. We cannot read all discussion of the ticket in a limited time.**
A proposal is often changed during the discussion, so it is very helpful to summarize the latest/current proposal, post it as a comment in the ticket, and write a link to the comment.

# Log

https://bugs.ruby-lang.org/issues/16393
Venue
12/20 (Fri) 13:00-17:00 @ pixiv Inc. (6F)
up to 6F, and ask someone to call usa-san in Studio
come after 12:45, or you cannot enter pixiv office
Attendees
Add your name (or ask a committer to invite you)
matz
mame
usa (venue staff)
naruse
akr
shyouhei
soutaro (will be one hour late)
Martin Dürst (late, around 14:00)
a_matsuda (late)
znz (remote)
ko1 (remote)
Absent:


Next Date
1/16 (Thu) 13–17 @ Moneyforward
Announce
About 2.7 timeframe
rc2?
tonight or tomorrow
Matz says that the next version is tentatively 2.8
He will change it to 3.0 when he thinks it’s time
But you (committers) can push major incompatibilities into master branch even if the tentative version is 2.8
Tentatively, need to write rubyspec as ruby_version_is "2.8"
Next Stable releases timeframe
Check security tickets
[secret]
Topics
[Feature #16419] FrozenError.new ignores receiver: (znz)
It seems that no one have strong opinion. So I want matz to decide.
Discussion:
in short:
FrozenError  .new(..., receiver=nil)
NameError    .new(..., receiver: nil)
NoMethodError.new(..., receiver: nil)
KeyError     .new(..., receiver: nil, key: nil)


Conclusion:
matz: go ahead, before RC2
[Feature #16420] Warning[:experimental]=false (znz)
I heard many warnings may make users trying pattern matching syntax are fewer.
Discussion:
in short: Provide a way to suppress a “pattern matching is experimental” warning
$ ruby -W:no-experimental # suppress
$ ruby -W:experimental    # enable


Conclusion:
matz: go ahead, before RC2
[Feature #16345] Stop warning command-line option (Don’t emit deprecation warnings by default.)
$ ruby -W:no-deprecated # suppress
$ ruby -W:deprecated    # enable


Conclusion:
matz: go ahead, before RC2
[Bug #16438] Check warning messages for Ruby 2.7 (ko1)
Please check newly introduced warning messages.
ko1: no problem to state Ruby 3.0? warning: $SAFE will become a normal global variable in Ruby 3.0
matz: no problem.
mame: warning: '_1' is used as numbered parameter is weird.
akr: warning: '_1' is confusing with numbered parameter
ko1: warn for def _1; end is okay?
naruse: it sounds the strength of the warning is the same as “shadowing outer variable”
usa: to define _1 method should be warn as non-default warning, IMO`
matz: I want to warn this pattern, but…
# OK: default on
_1 = 1 # warning: '_1' is reserved as numbered parameter


# The warning is unneeded
def _1; end #=> warning: '_1' is reserved as numbered parameter


1.times{
  _1
}


Reviewing a warning for keyword parameter change:
def foo(**kw)
end


foo({})


# current:
#=> test.rb:4: warning: The last argument is used as the keyword parameter
#   test.rb:1: warning: for `foo' defined here; maybe ** should be added to the call?


# modified (conclusion):
#=> test.rb:4: warning: The last argument is used as keyword parameters; maybe ** should be added to the call
#   test.rb:1: warning: The called method `foo' is defined here


# t.rb:1: warning: `_1' is used as numbered parameter
->
# t.rb:1: warning: `_1' is reserved for numbered parameter; consider another name


#=> t.rb:1: warning: non-nil $; will be deprecated
=>
#=> t.rb:1: warning: `$;' is deprecated


Conclusion:
warn for def _1; end as default warning
“used” should be “reserved”
[Bug #15267] File.basename + File.extname does not restore the original name (usa)
Please remove the special check of Windows
# 2.6
File.extname("file.") #=> ""


# 2.7
File.extname("file.") #=> "." in non-windows
File.extname("file.") #=> ""  in windows. should be "."
File.extname("file.rb.") #=> "." is OK on windows?


Discussion:
usa: is this intended?
nobu: no, maybe some historical reason of existing code
Conclusion:
will be fixed as soon as possible by usa
— ↑ Ruby 2.7
— ↓ Ruby 2.8 (or 3.0)
[No ticket] ruby-signature (soutaro)
An introduction to ruby-signature, and discussion to get alignment on how the library can be integrated to ruby and how the committers get involved.
https://docs.google.com/document/d/1QVma3srpqGkGgL37yPEK5LoW6tzI09UIaOL-vh-vgUQ/edit#
Discussion
@akr: Will need some mechanism to branch on version of branch
Conclusion
racc Parser generator executable is included in ruby/ruby
Will move to ruby/ruby in 2020 Q1
Will release a gem for 2020 (ruby-signature gem)
Will be a bundled gem (or default gem) as of Dec 2020 (Ruby 2.8/3.0)
Talk to @soutaro on slack, file a issue on GI issues (ruby/ruby-signature), or anything else for RBS definitions
[Feature #16264] Real “callable instance method” object. .:method to be a first-class thing, instead of Symbol#to_proc trick (zverok)
This proposal would make sense only if .: would not be reverted (which, to the best of my understaning, is now doubtful)
Discussion:
in short: [1, 2, 3].map(&.:to_s) ?
&.:to_s returns a MethodOfArgument object
[Misc #16291] Introduce support for resize in rb_ary_freeze and prefer internal use of rb_ary_freeze and rb_str_freeze for String and Array types (lourens)
Builds onto the capacity shrinking feature introduced by rb_str_freeze, targeting Array
Many internal uses that froze String types did not use the rb_str_freeze variation and could not benefit from resizing capacity on freeze
Implemented the same for Array
Let Array#freeze call rb_ary_freeze to expose the shrinking capability to user code too (as recommended by Shyouhei) for parity with String#freeze already doing the same
Discussion:
in short: shrink an array when freezing it? @shyouhei
shyouhei: looks good
nobu: looks good
ko1: I have some minor concerns. I’ll tell them to nobu. nobu, please review and merge the patch if it is okay
[Bug #15620] Block argument usage affects lambda semantic (alanwu)
I find the current behaviour unreasonably confusing and would like to see improvement, even though the bug doesn’t really show up in the real world often.
I have a pull request which restores the behaviour found in Ruby 2.4.x and warns about it, based off of matz’s comment in [ruby-core:94054].
Could someone confirm that is the desired fix? If it is the fix we want, could someone review the PR?
Discussion:
# OK
def pass_after_use(&block)
  b = block
  lambda(&block).call
end


pass_after_use {|_arg| }


# NG
def direct_pass(&block)
  lambda(&block).call #=> actual: ArgumentError, expected: no error
end


direct_pass {|_arg| }


matz: the issue should be fixed, but minor. I leave ko1 when it should be merged
Conclusion
Implementation is very hacky, but okay (@ko1)
Ask @alanwu to delete the added warning messages
Will merge now (for 2.7)
[Feature #16274] Transform hash keys by a hash (sawa)
Easily rename hash keys that do not follow a rule
Proposal to extend Hash#transform_keys to accept Hash to define the translation
Discussion:
hash = {created: 2019-10-23 17:54:46 +0900, updated: 2019-10-23 17:59:18 +0900, author: "foo"}
hash.transform_keys({created: :created_at, updated: :update_time})
#=> {created_at: 2019-10-23 17:54:46 +0900, update_time: 2019-10-23 17:59:18 +0900, author: "foo"}


# the same as
TABLE = { created: :created_at, updated: :update_time }
hash.transform_keys {|k| TABLE.fetch(k, k) }


"foo".gsub(/./, { "f" => "F", "o" => "O" }) #=> "FOO"


Same as String#gsub
Faster than block
How big is the performance improvement?
C implmentation may be faster than Ruby implementation
Conclusion
Approved; add Hash#transform_keys and Hash#transform_keys!, no values version
When? after 2.7
[Misc #16375] Right size regular expression compile buffers for literal regexes and on Regexp#freeze (lourens)
Builds on Misc #16291 , I think there’s potential to apply this pattern to other types at hooks outlined at the end of the issue
A large set of literal regular expressions are quite common in Rails applications (mostly framework, but also application and dependencies)
In my Redmine boot test was able to shave 300kb off just excess regex buffer capacity
Discussion:
Conclusion
No strong opinion
Will merge next month (after 2.7)
[Misc #16260] Symbol#to_proc behaves like lambda, but doesn’t aknowledge it (zverok)
Discussion:
f = :+.to_proc
f.lambda? # => false (should be true?)


Symbol#to_proc returns another type of proc other than proc and lambda
What is lambda?
Lambda checks arity
Where to return to
Conclusion
Should be true because it checks the arity
Next action: merge it after 2.7
[Bug #6087] How should inherited methods deal with return values of their own subclass? (mame)
Matz said “We will fix this (to consistently return Arrays) in 3.0.” seven years ago. Now is the time. Final confirmation.
Discussion:
in short: Should all methods return Array uniquely in Ruby 3.0, right?
Does the same go to String? [Bug #10845]
(Changing String may break SafeBuffer of Rails?)
Matz: a method which returns a gathered result will return an Array. a method which returns a modified receiver will return an object whose class is receiver’s class.
class A < Array
end
a = A.new
a.flatten.class #=> A
a.rotate.class  #=> Array
(a * 2).class   #=> A
(a + a).class   #=> Array
(a1 + a2).class #=> A1


Conclusion
Will fix in 3.0
Will have a meeting to check methods one by one next year
[Feature #14183] “Real” keyword argument (jeremyevans0)
Is it OK to merge branch to remove deprecated support for positional hash <-> keyword conversion after 2.7 released?
Discussion:
in short: Final confirmation
Conclusion:
Matz: give it a try
https://bugs.ruby-lang.org/issues/14183#note-100
[Bug #11022] opening an eigenclass does not change the class variable definition context (jeremyevans0)
Discussed during September dev meeting, matz wanted to consider for a while. Has a decision been made?
Discussion:
in short: Class variable lookup just ignores singleton classes currently. Is it intentional?
module Mod1
  class << Object.new
    @@cv = 1
  end
  p @@cv #=> 1
end


Conclusion:
matz: need more time…
[Bug #7844] include/prepend satisfiable module dependencies are not satisfied (jeremyevans0)
Discussed during September dev meeting, matz wanted to consider for a while. Has a decision been made?
Discussion:
in short:
module A; end
module B; include A; end
module C; prepend A; end
module D; include B; prepend C; end
p D.ancestors #=> [A, C, D, B, A]?
              #=> [C, D, B, A]?


Conclusion:
matz: need more time…
[Bug #14240] warn four special variables: $; $, $/ $\ (jeremyevans0)
Do we still want to warn regarding these variables? If so, should the warnings be during parsing or at runtime (if variables are aliased and then modified)?
Discussion:
parsing time -> cannot warn $FS in English.rb
runtime -> may overlook?
akr: let’s delete English.rb (in future)
Conclusion:
warn whenever the variable is (re)written (i.e., dynamically)
alias should also be warned
2.8
[Feature #10463] :~@ and :!@ are not parsed correctly (jeremyevans0)
matz decided in July that def ~@ and def !@ should continue to work. However, can we fix the parser to not treat :~@ and :!@ as :~ and :!?
Discussion:
in short: Currently, :!@ => :!. Should :!@ raise a SyntaxError instead?
p :a == :'a' #=> true
p :! == :'!' #=> true
p :!@ == :'!@' #=> false


Conclusion
matz: see no advantage
[Feature #16377] Regexp literals should be frozen (byroot)
Regexp literals always reference the same mutable instance. This allow to leak global state with instance_variable_set
Change already accepted by Matz about 2 years ago: https://bugs.ruby-lang.org/issues/8948#note-14, but then nothing happened.
Discussion:
in short: /foo/.frozen? should be true
usa: How treat /#{gets}/.frozen? ?
mame: should return frozen object
akr: there is a discussion to make string literal with interpolation non frozen.
Regexp is immutable from Ruby program. Any reason to reject?
Making Regexp literals frozen prohibits adding instance variables.
knu: may add singleton methods to regexp instances. Not a very strong opinion, 3.0 would be a good chance.
Conclusion:
matz: give it a try in 2.8
[Bug #16406] (lambda_proc << normal_proc).lambda? should return false (alanwu)
I think this is more intuitive than the current behavior.
Discussion:
in short: (lambda_proc << normal_proc).lambda? should return false
nobu:
f << g == ->(...) { f.call(g.call(...)) } == λx.f (g x)
p (lambda{} << lambda{}).lambda? #=> true
p (lambda{} << proc{}).lambda?   #=> true  # expect false
p (proc{} << lambda{}).lambda?   #=> false # expect true
p (proc{} << proc{}).lambda?     #=> false


p (lambda{} >> lambda{}).lambda? #=> true
p (lambda{} >> proc{}).lambda?   #=> true  # expect false
p (proc{} >> lambda{}).lambda?   #=> false # expect true
p (proc{} >> proc{}).lambda?     #=> false


Conclusion
Should be fixed in 2.8

timeout





