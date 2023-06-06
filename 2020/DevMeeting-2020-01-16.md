---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2020-01-16

# The next dev meeting

**Date: 2020/01/16 13:00-17:00**
Place/Sign-up/Agenda/Log: https://docs.google.com/document/d/1NxNMc7tlt-olPRyRQDhAjZWw30Atr6a-rfQxQ9BRXfY

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

- Comment deadline: 2020/01/09 (one week before the meeting)
- The format is strict.  We'll use [this script to automatically create an markdown-style agenda](https://gist.github.com/mame/b0390509ce1491b43610b9ebb665eb86).  We may ignore a comment that does not follow the format.
- Your comment is mandatory.  We cannot read all discussion of the ticket in a limited time.

# Log

https://bugs.ruby-lang.org/issues/16454
Venue
1/16 (Thu) 13:00-17:00 @ MoneyForward (21F)
Attendees
Add your name (or ask a committer to invite you)
matz (remote)
mame (remote)
usa (remote) will join at 14:30 or a little later
soutaro
Akr
hsbt
Absent:
Martin Dürst
Next Date
2/27 13:00-17:00 @ Cookpad Tokyo office MTG1 (12F)
Announce
Ruby 2.7 timeframe
release 2.7.1 soon if it is needed.
if we don’t have to hurry, release 2.7.1 in March.
About 2.8/3.0 timeframe
Next Stable releases timeframe
Check security tickets
[secret]
2.7.1 issues (keyword argument separation)
[Bug #16486] Hash.ruby2_keywords?(hash) and Hash.ruby2_keywords!(hash) (mame)
It turned out that we sometimes need to deal with ruby2_keywords flag explicitly. I’d like to add the feature as class methods. (@naruse says that backport to 2.7.1 is okay)
Background jobs used in Rails (ActiveJob, Resque, Sidekiq, …) need it. It passes method call arguments between processes using their own serializer.
Serialization of arguments needs it too in general
Discussion:
in short:
What we should provide as Hash.ruby2_keywords!? mutating version, non-mutating version, or both?
Should it raise a FrozenError if a hash is frozen? -> maybe so?
non-mutating solves both; now it looks good
a good method name? Hash.dup_with_ruby2_keywords
Hash.ruby2_keywords(hash) ???
Hash.as_ruby2_keywords(hash) ?
Hash.flag_ruby2_keywords(hash) ?
Hash.mark_ruby2_keywords(hash) ?
Hash#ruby2_keywords!? => No
Not an instance method, proposing to be a singleton method.
Not intended to be used by users.
Hash.ruby2_keywords_for_rpc
RubyVM.ruby2_keywords_hash(hash) (matz: not positive, eregon: negative)
Using refinements???
Ruby.ruby2_keywords_hash(hash) (soutaro: +1)
Hash.ruby2_keywords_hash(hash), Hash.ruby2_keyword_hash?(hash) (matz:+1)
dup to make it non ruby2_keywords
What if Ruby 3 decouples keyword args completely
ruby2_keywords remains in Ruby3.
ruby2_keywords def flag_kwhash(*args)
  args.last
end

kw = flag_kwhash(**h)

We don’t have to consider a class that inherits from Hash because passing it as a keyword converts to a raw Hash
Also ruby2_keywords gem will need to support them.
Discussion:
matz: future plan?
mame: will be deleted with ruby2_keywords.
mame: mutation version or non-mutation version (create new object)?
matz: I like non-mutation.
mame: ok. what is name?
akr: why is it singleton method? why not an instance method?
mame: it is because non-casual use method. we want to hide.
hsbt: Rails has many singleton method, so it is not special method, we feel.
Conclusion:
matz: I like non-mutating version; frozen Hash does not make sense then
matz: method name: Hash.ruby2_keywords_hash(hash) (non-mutating) and Hash.ruby2_keywords_hash?(hash)
Hash.ruby2_keywords_hash(hash) returns new ruby2 keywords hash.
Hash.ruby2_keyword_hash?(hash) returns true if hash is ruby2 keywords.
[Feature #16463] Fixing *args-delegation in Ruby 2.7: ruby2_keywords semantics by default in 2.7.1 (eregon)
This would be a way to make transition to Ruby 2.7 a lot simpler. Advantages are: easier migration (no explicit ruby2_keywords, no confusing warnings, better warnings when migrating), better debugging, more compatible.
Right now it’s confusing warnings, forcing most gems to use ruby2_keywords which is just a workaround and the code will need to change again.
What do you think?
I think if we don’t it it will delay Ruby 2.7 adoption very significantly. I’d also think Rubyists will get a very negative opinion about the keyword arguments change due to ruby2_keywords and delegation issues, when there is no need and we can make it easier.
https://docs.google.com/presentation/d/1j6voqhfq46-MsEm_vUJsBJiktNvoA6niz3fea9awmco/edit#slide=id.g76731bd9ba_0_128
Discussion:
in short:
eregon’s proposal: Automatically set ruby2_keywords to all methods that accepts *args
Advantage:
It seems to bring a better migration path that requires less modification.
According to eregon, his experimental branch passes Rails test suite.
Future migration is possible; we can remove ruby2_keywords flag in future (described in the next)
Risk:
There are a few pitfalls (which look not so frequent).
2.7.0 and documents are also already published. It works, even if it is not best. The policy change from now will bring a big confusion to users.
Decision: Should we change it in 2.7.1?
Migration from 3.X to 4.0 (when ruby2_keywords flag is deprecated):
ruby2_keywords is considered “a mark” for whete to fix (when ruby2_keywords flag is deprecated)
However, it turned out that it is not mandatory:
if a warning is displayed when a flagged Hash is passed as keywords, the migration is possible
# in 3.X with eregon's proposal
def bar(key: 1); end
# in the current approach, `ruby2_keywords` mark is required for this method; in eregon's, it is automatically added
def foo(*args)
  # `args.last` is automatically flagged under eregon's proposal
  bar(*args) #=> warning: a ruby2_keywords hash is passed as keywords; use **opt explicitly
end
foo(key: 42)

# in 3.X (fixed)
def bar(key: 1); end
def foo(*args, **opt)
  bar(*args, **opt) # no warning
end
foo(key: 42)

pitfalls
A call foo(**{}) to def foo(*a) will pass a = [{}] (the Hash is flagged)
If a Hash is flagged unintentionally, it may cause a difficult-to-debug issue:
The following code is valid in 3.0, but will break under eregon’s proposal
# Code that Jeremy wrote
def a(h, **opts) [h, opts] end
# assumption: This method is designed not to accept keywords, and relies on Jeremy's compatibility layer
def b(meth, *vs) vs.map{|v| args = [meth, v]; send(*args)} end
p b(:a, 1, "2", c: 3)

concerns:
Changing the semantics after 2.7.1 may bring a big confusion to users
It may have new pitfalls that we have not noticed yet: weird use cases, performance issues, etc.
(I know that it is a kind of FUDs, but I really concern)
what to determine:
Is it possible to change it in 2.7.1? Or is it too late?
If so, should we change it?
naruse: ~~how many gems are supporting ruby2_keywords?? ~~
naruse: how many gems need ruby2_keywords but not support yet? (the number of gems which gains the benefit of this change = pros); I assume applications don’t hit this issue.
eregon: GitHub shows only 142 usages of ruby2_keywords (so probably almost all gems still need to migrate to Ruby 2.7)
akr: benchmark is needed because it can slow down.
mame: there are many changes and additional proposal seems harmful.
naruse: if this proposal is better we need to consider. we need to understand pros. and cons (performance and unknown pitfalls).
matz: I understand. I will consider about it. I want to know other examples which is diffilt to solve.
naruse: we need to decide before March to release ruby 2.7.1.
matz: I will make decision by the end of January
[Feature #16494] Allow hash unpacking in non-lambda Proc (zverok)
allow map { |foo:, bar:| ... } just like array unpacking to map { |foo, bar| ... } is allowed. The ticket contains pragmatic examples.
Discussion:
in short:




$ ruby -e '[{foo: 42}].each {|foo:| p foo }'
-e:1: warning: Using the last argument as keyword parameters is deprecated; maybe ** should be added to the call
-e:1: warning: The called method is defined here
42

# workaround 1
def foo(foo:)
  foo
end
[{foo: 42}].each {|h| foo(**h) }

# workaround 2
[{foo: 42}].each {|h| h[:foo] }

define_method(:foo) {|key:| p key }
foo(key: 42)     #=> OK (42)
foo({foo: 42})   #=> NG (warn on 2.7, error on 3.0)
foo(**{foo: 42}) #=> OK (42)

mame: will reply
eregon: this would mix data hashes with keyword arguments, seems opposite of separation
[Feature #16501] Support marshaling of ruby2_keywords flag (mame)
Marshal.dump and load do not support ruby2_keywords flag, so serializing and deserializing a flagged Hash drops the flag.
This is not critical because AFAIK there is no real application that requires this feature. It would be helpful to make drb support the separation, but if #16486 is accepted, drb can work around the issue.
But if we introduce it, it should be into 2.7.1.
Discussion:
in short:
matz: go ahead
[Feature #16466] *args -> *args delegation should be warned when the last hash has a ruby2_keywords flag (mame)
In 2.7.0, no warnings are displayed at second and later stages of multi-stage delegation. It looks a bug.
I think it is not critical, but in principle, it should be warned. I’d like to determine it before 2.7.1.
Discussion:
in short:
eregon: confusing if some cases warn and some don’t, better warn for all (this fix) or none (my proposal, warn for all in Ruby 3.warn).
eregon: seems implementation bug, let’s fix it first anyway
matz: if we accept #16463 we don’t need to change anything, right?
eregon: Right
matz: let’s postpone this until next dev-meeting. By then I will conclude #16463
def baz(**kw)
end

def bar(*args) # ruby2_keywords is needed
  baz(*args)
end

ruby2_keywords def foo(*args)
  bar(*args)
end

foo(k: 1)

Topics
[Feature #8709] Dir.glob should return sorted file list (eregon)
It causes non-determinism on e.g., Linux, which causes complex bugs (which are not worth investigating)
Many other languages seem to sort by default (C’s glob(3), bash, perl, gmake, …)
TruffleRuby already sorts Dir.glob to avoid this issue
Quite a few people like this idea: https://twitter.com/eregontp/status/1217036685059461120
Discussion:
in short:
$ ruby -e 'p Dir.glob("foo/{a,b,c,d}")'
["foo/a", "foo/b", "foo/c", "foo/d"]

$ ruby -e 'p Dir.glob("foo/{a,c,b,d}")'
["foo/a", "foo/c", "foo/b", "foo/d"]

Dir.glob("foo/{a,b}/{a,b}/{a,b}/{a,b}/{a,b}")
p Dir.glob("foo/{a,c,?}") # => ["foo/a", "foo/c", " "foo/b", "foo/d", "foo/a", "foo/c"]

$ echo foo/{a,c,?}
foo/a foo/c foo/a foo/b foo/c foo/d

ko1: adding a keyword sort: true may be good. The default is true?
eregon: ^ sounds good
nobu: brace pattern should save the order.
ko1: really?
mame: agreed with ko1
nobu: It is difficult to determine the order of Dir.glob("foo/{[ad],*}", sort: true) result if preserving the brace pattern order.
eregon: same as Dir.glob("foo/[ad]", sort: true) + Dir.glob("foo/*", sort: true) (Ruby already includes duplicates in that case)
nobu: GLOB_NOSORT option looks nice.
ko1: What if thoushands of entries are in a directory. It may delay first yield of the given block for Dir.glob(pattern, &block).
akr: No difference. It reads all entries before the first yield.
nobu: No, it should not read all first, on non-Windows.
akr: if we will change Dir.entries and Dir.children, they should be consistent with Dir.glob. Since glob-flags is not appropriate for Dir.entries and Dir.children, keyword argument is better to specify this sort-or-nosort behavior.
There is no one who is against to change the default.
Whether is an API to disable sort needed?
What API is good? (Dir.glob already has base: keyword argument)
Conclusion:
matz: go ahead (for sort: true)
Make Dir.glob(pattern, sort: true) sort the result.
sort: true is the default.
sort: false to make the result unsorted.
Dir.foreach, Dir.entries, or anything else don’t sort at this time. (Out of scope.)
[Bug #8841] Module#included_modules and prepended modules (mame)
Module#include? and Module#included_modules regard prepended modules as included (not well documented); Module#included is not called when the module is prepended. Is this right?
IMO, changing the behavior is no longer acceptable (without any actual trouble). How about just changing the document?
Discussion:
in short: inconsistent?
Module#include? and Module#included_modules regard prepended modules as included (not well documented)
Module#included is not called when the module is prepended
module M
  def self.included args
    p [:included, M, args]
  end
end
module P
  def self.included args
    p [:included, P, args]
  end
  def self.prepended args
    p [:prepended, P, args]
  end
end

class C
  include M
  #=> [:included, M, C]

  prepend P
  #=> [:prepended, P, C]
end

p C.included_modules #=> [P, M, Kernel]
p C.prepended_modules #=> NoMethodError

Conclusion:
Reject.
matz: Intended behaviour.
matz: A module is not prepended form the view of its subclass even if prepended in its superclass. But, it want to detect it is mixed in.
eregon: Module#included_modules is “all modules (non-class) included in mod.ancestors”
[Feature #8026] Need Module#prepended_modules (mame)
It is accepted six years ago, but not implemented yet. I’ve never heard any actual trouble, but should we still add the feature?
Discussion:
module Mixin
  prepend A
end

module Outer
  prepend Mixin
end

Mixin.prepended_modules   #=> [A]
Outer.prepended_modules   #=> [Mixin] (A is not included)

in short: same as previous ticket.
mame: chainging included_modules can affect compatibility.
znz: For compatibility, adding new keyword argument to exclude prepended modules?
Conclusion:
Add Module#prepended_modules which returns array of directly prepended modules.
No true | false option.
[Bug #9815] attr_reader doesn’t warn on a uninitialized instance variable (mame)
A reader method defined by attr_reader :foo is not warned as “instance variable @foo not initialized”. Is it intentional?
Discussion:
in short:
nobu: intentional spec. -> closed.
Conclusion:
Close
[Bug #10388] Operator precedence problem in multiple assignment (massign) (mame)
"a, b = c = 1, 2 is currently taken as a, b = (c = 1), 2; I’d expect it to be taken as a, b = (c = 1, 2)." Jeremy gave a try to implement but seemed difficult due to the limitation of LALR(1) parser. Let’s give up.
Discussion:
in short:
nobu: it is possible to warn if RHS has assignment on multiple assignments.　People can use the current syntax intentionally.
matz: I’m open to add warnings with -W.
Conclusion:
Reject. Won’t change the syntax definition.
Open to add warnings.
[Bug #10475] Array#flatten should not accept a nil argument (mame)
Should we add a document that Array#flatten accepts nil? Negative argument too?
Discussion:
in short:
-1, nil, noarg => unlimited
should be written about -1 in doc, nil should raise.
rdoc: nothing about level.
rurema (doc in Japanese)：nil, noarg is written.
pre-devmeeting:
ko1: nil is documented, so we can’t remove this behavior.
eregon: OP says it’s not documented (but probably it should)
mame: it seems no advantages to remove it.
nobu: should we write -1 in doc? I don’t want to write it. how about hidden feature?
mame: should we warn on -1? too much?
devmeeting:
akr: How about -2 or -3???
nobu: Same as -1
matz: -1 is from perl.
ko1: why undocument for -1?
usa: we can change the behavior in future, if it’s not documented. (Of course, we don’t have such future plan)
naruse: I object to change the behavior.
Conclusion:
Update the RDoc for nil.
The case with negative numbers is hidden/undocumented feature.
No change in implementation. (array.flatten(-1) continues working.)
[Bug #10929] NilClass#to_proc and & don’t mix? (mame)
I think it is not worth adding.
Discussion:
in short: (rejected)
[Bug #11014] String#partition doesn’t return correct result on zero-width match (mame)
I’d like to confirm if the current behavior is inteneded.
Discussion:
in short:
We should focus on String#partition; String#scan and #split should not change just for consistency reason.
Since 71afefd5d1f, no change.
p "foo\n\bar\n".partition(/^/)     #=> ["foo\n\bar\n", "", ""]
p "foo".partition(/^=*/)           #=> ["foo", "", ""]
p "foo".partition(/^=+/)           #=> ["foo", "", ""]
p "foo\n===\nbar".partition(/^=+/) #=> ["foo\n", "===", "\nbar"]
p "foo".partition(/(?=o)/)         #=> ["f", "", "oo"]

# not matched
p 'foo'.partition(/x/) #=> ["foo", "", ""]

# Python doesn't support regex on it?

>>> "foo".partition("o")
('f', 'o', 'o')
>>> "foo".partition("x")
('foo', '', '')

Conclusion:
matz: A bug. Fix it.
akr: Empty match should be at the left most position.
p "foo\n\bar\n".partition(/^/)     #=> cur: ["foo\n\bar\n", "", ""]
                                   #=> fix: ["", "", "foo\n\bar\n"]
p "foo".partition(/^=*/)           #=> cur: ["foo", "", ""]
                                   #=> fix: ["", "", foo"]

[Feature #16432] Using _1 inside binding.irb will cause unintended behavior (osyo)
Calling binding.irb in a block that uses _1 and using _1 in irb will cause unintended behavior.
Should it be a runtime error?
Discussion:
in short:
1.times{
  p _1 #=> 0
  eval("[:a, :b].each{p _1}")
  #=> 0
  #=> 0
}

mame: it can cause confusing bug.
ko1: we have two solutions:
separate numbered parameter scope between in/out eval.
Issue: we can’t see _1 variables from debugging reason.
allow Binding#local_variable_get("_1") for this purpose? How to get uplevel binding?
Same semantics without eval.
issue: we can’t write numbered parameters in this kind of context.
# 2.7.0 behavior
1.times{p _1
  eval('p _1')            #=> OK (0)
  eval('[:a].each{p _1}') #=> OK, but confusing (0)
}

# 1.
1.times{p _1
  eval('p _1')            #=> NameError
  eval('[:a].each{p _1}') #=> OK (:a)
}

# 2.
1.times{p _1
  eval('p _1')            #=> OK (0)
  eval('[:a].each{p _1}') #=> SyntaxError
}

1.times{binding.irb}

# start IRB session
irb> p _1
#=>
# 2.7.0 NameError
# 1. NameError
# 2. NameError

1.times{binding.irb;_1}
# start IRB session
irb> p _1
#=>
# 2.7.0 OK (0)
# 1. NameError
# 2. OK (0)

irb> [:a].each{p _1}
#=>
# 2.7.0 OK, but confusing (0)
# 1. OK (:a)
# 2. SyntaxError

matz: vote for 1
Conclusion:
Solution 1. Nobu will fix parse.y
hopefully backport to 2.7.1 (March 2020)
[Feature #16441] Enumerable#take_while_after (zverok)
Just like take_while, but also returns the matched element; practical examples are shown
Discussion:
in short:
[1, 2, 3, 4, 5].take_while_after {|n| n < 3 } #=> [1, 2, 3]

mame: adding a method looks very ad-hoc to me
nobu: flip-flop is cool, .select { !!(_1 == '<<'.._1 == '>>') } is cooler? !! is not cool, though
str = <<DOC
prologue
<<
1
2
3
>>
epilogue
DOC
str.each_line(chomp: true).drop_while { _1 != '<<' }.chunk_while { _1 != '>>' }.first #=> ["<<", "1", "2", "3", ">>"]

# Using Enumerable#slice_after
array.slice_after {|n| n < 3 }.first
array.lazy.slice_after {|n| n < 3 }.first

Conclusion:
Reject.
[Feature #16435] Array#to_proc (zverok)
Make map(&[:foo]) it just a shortcut for map { |hash| hash[:foo] }. Ambitious, but justified. Two alternative approaches proposed (dig and just [])
Discussion:
in short:
# in perspective of the proposer
data.map {|x| x[:name]}  # too long
data.map{_1[:name]}      # using _1
data.map(&[:name])       # better?

data.map {|x| x.dig(:foo, :bar)}  # too long
data.map{_1.dig(:foo, :bar)}      # using _1
data.map(&[:foo, :bar])           # better?

Note that pluck is unusable for this purpose
ko1: Array#to_proc seems more general feature. But this proposal is too specific usage IMO.
Conclusion:
matz: Reject
Too specific for the name.
Array#to_proc, if any, should be a projection.
[Bug #16383] TracePoint does not report calls to attribute reader methods (jeremyevans0)
Do we want to support this?
Discussion:
in short:
ko1: I’m afraid about the performance degeradation of the proposed patch. I’ll measure a benchmark.
ko1: +30% execution time even without enabling TracePoint. (too bad)
Conclusion:
matz: good to have, but not a showstopper
[Feature #5321] Introducing Numeric#exact? and Numeric#inexact? (jeremyevans0)
Can we add Numeric#exact? ? This is necessary to fix Bug #5179.
Discussion:
How about Numeric.new.exact? => raise an error.
What does exact mean?
(1/3).exact?
Scheme does not define exact? by the type of values. It’s orthogonal attribute of each value.
naruse: Does #5179 need to fix?
Conclusion:
Continue discussion with mrkn.
[Bug #9790] Zlib::GzipReader only decompressed the first of concatenated files (jeremyevans0)
Can we implement Zlib::GzipReader.each_file using the patch?
Discussion:
in short:
ko1: how this spec is authorized? file is a suitable method name?
https://www.futomi.com/lecture/japanese/rfc1952.html#s2_2
https://tools.ietf.org/html/rfc1952#section-2.2
A gzip file consists of a series of “members” (compressed data sets).
each_member?
Python 3.8 looks the same as zcat
nobu: each_member is really needed? It may be enough to add an option for zcat-like behavior
mame: it is possible in pure Ruby to implement each_member if needed
ko1: real use case? Is there a famous source to produce such gzip data?
https://github.com/exAspArk/multiple_files_gzip_reader
mame: can each_file return an Enumerator? Seems difficult to implement it
matz: How about always behaving like zcat? Is an option to keep the old behaviors really needed?
akr: The traditional behavior should be kept
akr: gzip(1) describes concatenation of gzip files in ADVANCED USAGE. https://www.gnu.org/software/gzip/manual/gzip.html#Advanced-usage
Conclusion:
matz: it should behave like zcat. Handling each member should be deleted.
[Misc #16487] Potential for SIMD usage in ruby-core (alanwu)
It would be great if we can come up with a stance on this and document it. It doesn’t have to be permanent; we can revise it later, but I think the policy can be very helpful to new contributors.
Related, I think we can put something in the Github pull request template to help guide new contributors. I think Sorbet’s repo uses this to good effects.
Discussion:
in short:
Conclusion:
https://bugs.ruby-lang.org/issues/16487#note-18 is good conclusion.
[Feature #16461] Proc#using (alanwu)
This looks like a serious change worth discussing.
Discussion:
in short:
Difficult to discuss it without shugo
Conclusion:
Suspended.
May discuss at RubyKaigi, April 2020.
[Feature #16495] Inconsistant Quotes in Error Messages (mame)
This is the third time that we receive a ticket about the backtick and quote in error messages: #12321 for inconvenience in Slack, #13589 for weird appearance and inconvenience in syntax highlighting, this ticket for weird appearance.
I know that it is an old-school custom and think that it is hard to change, but I’d like just to hear matz’s feelings.
Discussion:
in short: Use consistent quote in messages.
ko1: I heard Matz about this ticket and he only said “culture diferences”, on markdown, we should use block quote.
Tools may be using the quotes to parse error messages.
Print two messages: if printing to machine, keep the quote. If human is reading, print with single quotes.
Conclusion:
Need more investigation:
How much efforts are required to change Ruby code.
How many tools are depending the current quotes.
[Feature #16484] Remove xmlrpc and net-telnet from bundled gems (hsbt)
Does anyone have an objection?
Discussion:
Embedded environments sometimes need them.
Install with rubygems if you need.
net-telnet assumes network connection.
Any reason to make gem install difficult?
Not found.
Bundled gem implies Ruby committers continue maintanance.
No update for three years.
Test failures are fixed.
Conclusion:
Remove from bundled gems.
[Feature #15973] Let Kernel#lambda always return a lambda (ko1)
How about to prohibit passing Proc for lambda to solve this issue completely?
Or how about to obsolete lambda because we already have ->?
To prohibit passing Proc objects because of syntax definition.
Discussion:
Proposing to deprecate lambda(&block) (lambda call with Proc object as block)
Benefits of obsoleting lambda?
Simplify for new Ruby developers. (no duplicated syntax.)
Suspend for short term (for years)
Don’t want more incompatibilities just after Ruby 2.7
Migration pass to deprecate lambda(&block).
Print warning at 3.0. (No compatibility layer.)
“The lambda call is meaningless. Delete it.”
“Delete meaningless lambda”
Raises error at 3.1 (or 3.2, …)
Conclusion:
lambda(&b) will be prohibited.
Print warning at 3.0. (No compatibility layer.)
Raises error at 3.1 (or 3.2, …)
[Feature #16499] define_method(non_lambda) should not change the semantics of the given Proc (ko1)
I have no strong opinion, but we need to check as related issue of #15973.
Discussion:
define_method(name) do end: The block is treated like lambda.
define_method(name, &proc) is converted automatically to define_method(name, &lambda).
The semantics of return changes.
define_method(name, proc) is converted automatically to define_method(name, lambda) (same with & version).
The conversion is sometimes useful:
Example in RSpec:
let(:foo) { return 42 }
https://stackoverflow.com/questions/44374077/rspec-execute-code-in-let-and-return-value
Many code examples found which passes received blocks to define_method.
/home/gem-codesearch/gem-codesearch/latest-gem/card-1.99.1/tmpsets/set/mod014-machines/abstract/machine.rb
module MachineClassMethods
  attr_accessor :output_config

  def collect_input_cards &block
    define_method :engine_input, &block
  end

  def prepare_machine_input &block
    define_method :before_engine, &block
  end

  def machine_engine &block
    define_method :engine, &block
  end

  def store_machine_output args={}, &block
    output_config.merge!(args)
    return unless block_given?
    define_method :after_engine, &block
  end
end

Conclusion:
matz: No strong opinion.
Reject because of compatibility.
[Bug #16406] (lambda_proc << normal_proc).lambda? should return false
Does this need backport to 2.7 (and 2.6)?
Conclusion:
Backport
[Bug #11878] Comparison of prepended modules
Inconsistent with ancestors order.
module A; end

module I
  include A
end

p A < I #=> false
p A > I #=> true

module P
  prepend A
end

# current: same as include
p A < P #=> false
p A > P #=> true

call-seq:
   mod < other   ->  true, false, or nil

Returns true if <i>mod</i> is a subclass of <i>other</i>.

Discussion:
Current behaviour: Same with include
Conclusion:
Rejected.
< is based on mixin/inheritance relations.
