---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-11-28

Please comment on your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of the ticket comments)

*DO NOT* discuss then on this ticket, please.

----
Date: 2019/11/28 13:00-17:00
Place, Sign-up, and Log: https://docs.google.com/document/d/1AZ74HXEedKksJwhEUPIlnRxAUgchndZPZYAKjGPMsFI/edit#

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

We don't guarantee to put tickets in the agenda if the comment violates the format (because it is hard to copy&paste).

**A short summary of a ticket is strongly recommended. We cannot read all discussion of the ticket in a limited time.**
A proposal is often changed during the discussion, so it is very helpful to summarize the latest/current proposal, post it as a comment in the ticket, and write a link to the comment.

# Log

https://bugs.ruby-lang.org/issues/16262
Venue
11/28 (Thu) 13:00-17:00 @ pixiv Inc. (6F)
up to 6F, and ask someone to call usa-san in Studio
come after 12:45, or you cannot enter pixiv office
Attendees
Add your name (or ask a committer to invite you)
matz
mame
samuel (with aycabta)
Soutaro
Knu
akr
a_matsuda
aycabta
usa (venue staff)
hsbt (remote)
ko1 (remote)
znz (remote)
nobu (remote)
Absent:
Martin Dürst (sorry, I have to cancel because of an urgent family matter)
Next Date
12/20 (Fri) @ pixiv 6F
Announce
About 2.7 timeframe
preview 3 done
rc1 -> the 2nd week of December?
Next Stable releases timeframe
usa: maybe the 2nd week of December?
Check security tickets
Topics
[Feature #15323] Enumerable#filter_map (jonathanhefner)
This feature has already been merged, but there may have been some confusion regarding the implementation.
It currently selects only truthy values, but should it select all non-nil values, like Array#compact? (See https://bugs.ruby-lang.org/issues/15323#note-17)
If so, is the name filter_map still a good choice? Or should it be changed to something like compact_map?
Discussion:
in short: filter_map should not remove false, and the name compact_map is preferable
matz: why want to change? reason?
matz: if we change the name and behavior, the name should be map_compact.
nobu: I only want to confirm. the original request drops both nil and false.
mame: then, there is not necessary to change.
samuel: [nil, false, true, 1].compact => [false, true, 1]
samuel: [nil, false, true, 1].filter{|x| x}.to_a => [true, 1]
samuel: naming should be consistent with above? If you write map_compact it reads like “map” then “compact”. But if you write compact_map it reads like “compact” then “map”.
[Feature #16293] Numbered parameter confirmation in Ruby 2.7 (osyo)
Numbered parameter confirmation in Ruby 2.7
Discussion:
in short: all issues have been resolved? @nobu
[Feature #16364] Top-level ruby2_keywords (mame)
Currently, there is no top-level ruby2_keywords, which would be unuseful for some simple cases.
Discussion:
#!/usr/bin/env ruby


def foo(**kw)
  kw
end


ruby2_keywords def bar(*a)
  foo(*a)
end


bar(k:1) #=> {:k=>1} with no warnings in 2.7


Discussion:
matz: of course, it’s necessary. accepted.
[Feature #16355] Raise NoMatchingPatternError when expr in pat doesn’t match (ktsj)
I’d like to fix this specification before 2.7 release.
Discussion:
in short: 1 in 2 #=> NoMatchingPatternError
akr: how to return the value iff it wrapped with ()?
matz: should always raise an exception.
ko1: what is the return value?
matz: expr itself (expr in pattern #=> expr)
ko1: anyone use it? isn’t it good enough to return nil?
matz: ok, nil
samuel: Is it indicating a semantic error by programmer? If it’s not exceptional situation, we should be careful because in the past raising exceptions introduce difficult to fix performance issues (e.g. IO#read_nonblock).
aycabta: pattern matching in Ruby 2.7 is experimental, so we are talking about grammer. discussion of performance of pattern matching is for Ruby 3.0.
p((return))          #=> SyntaxError: void expression
p((expr in pattern)) #=> SyntaxError: void expression?


if expr in pattern


else
  # unintended unreachable code
end


if nil in x
else
end


expr in x #=> nil when matched?
expr in x in (a, b, c) # cannot


begin
  expr in pattern
rescue NoMatchingPatternError # It's bad performance if users write code like this.
  # else
end


case expr
in pattern
  ...
else
  ...
end


Conclusion:
expr in pattern should raise NoMatchingError when unmatched
expr in pattern should return nil when matched. (this is unspecified, but this feature is experimental, at all)
[Misc #16188] The performance overhead of ruby2_keywords (eregon)
It’s about 10% for foo(*args) calls where foo has little code, both for MRI and TruffleRuby. That’s quite significant.
I would like to see a plan to avoid that significant overhead in Ruby 3+.
I propose to either remove ruby2_keywords in 3.0, or make ruby2_keywords explicit with send_keyword_hash (removes the overhead), or use another way (e.g., ...) to delegate in 2.7+.
Discussion:
samuel: It’s short term pain for backwards compatibility, so 10% isn’t so bad? If users only care about 2.7+ it can be avoided entirely?
usa: yes, we can put up with it.
samuel: For two companies I work with, doing Ruby instrumentation, the overhead of instrumentation code is so high, that 10% cost here is (probably) neglegible in practice.
mame: Agreed.
usa: especially, this is a result of an arbitrary micro benchmark. in realworld applications, the cost may be unobservable, I guess.
samuel: for ruby2_keywords we are optimising for user, not performance. So, can we put user concern/syntax/semantic issue first.
usa: perfectly agreed.
$ cat t.rb
def req(x)
  x
end
hash = {a: 1}
arr = [hash]
100000000.times { req(*arr) }
100000000.times { send_keyword_hash(:req, *arr) }


ruby2_keywords def foo(*a)
  send_keyword_hash(:target, *a)
end
ruby2_keywords def foo(...)
  target(...)
end


def target(*a) a end
ruby2_keywords def foo(*a)
  target(a.last)
end
ruby2_keywords def bar(*a)
  ...
  target(*a)
  ...
end
foo(**{}) #=> [{}!]
bar(**{}) #=> []


$ time ruby -v t.rb
ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-linux]


real	0m5.280s
user	0m5.266s
sys	0m0.012s


$ time ./local/bin/ruby -v t.rb
ruby 2.7.0dev (2019-11-23T07:06:30Z master b563439274) [x86_64-linux]


real	0m5.749s
user	0m5.728s
sys	0m0.020s


Conclusion:
10% slowdown is unpreferable, but acceptable
[Feature #16378] Support leading arguments together with … (eregon)
Otherwise … cannot be used in many cases.
I think it’s what most people expect.
Could be a nice way to do delegation for lexical cases.
Discussion:
samuel: I think users will assume “…” can be used in this way and be confused when it doesn’t work.
def foo(x, ...)
  bar(x+1, ...)
end


matz: already rejected, at least now. wait 2.8 or 3.0
[Feature #16345] Don’t emit deprecation warnings by default. (mame)
The discussion seems to be agreeing with deduplicated warnings [Feature #16289]. We must decide.
Background:
There are two groups of warning messages
We are talking about warning messages emitted through rb_warn
There is another function rb_warning, but rb_warning is not actively used now; we will leave error messages with it as is
Discussion:
samuel: In most of the applications I work on, we have .rspec file with --warnings. We don’t have warnings in production. We try to fix any warning during testing, but sometimes it’s not possible (3rd party Gem, etc). When I was updating gems/applications (about 10 so far), I found the warnings really helpful. Warning is only useful if someone can read it.
samuel: aws gem, with warnings enabled, generates 27,000 lines of warnings: https://travis-ci.org/aws/aws-sdk-ruby/jobs/618045761 😱
in short: no, not agreed yet
Method style: Warning.enable/disable/enabled? or Warning[]
We don’t accept blocks for Warning.enable/disable because users may think it is fiber-local.
Warning[]?
def Warning.[](cat)
end
def Warning.[]=(cat, toggle)
end
Warning[:deprecated] = true


Warning[:unknown_cat] ?
ArgumentError or KeyError?
ko1: Warning.categories? samuel: or ::keys
discuss later
samuel: Should it work with Logger and log levels? e.g. Warning[:deprecated] = Logger.new(...)
mame: not planned currently
discuss later
Conclusion:
We introduce Warning.[] and Warning.[]=
Warning[x] raises ArgumentError if x is not :deprecated.
We also introduce a command line option, -W:deprecated.
Emit deprecation warnings by default.
This “deprecation warning” means deprecation warning emitted by rb_warn. We’ll change rb_warn to rb_deprecate_warn (or similar name)
We don’t modify rb_warning invocations now. They may contain unmaintained warnings. We should examine later.
$VERBOSE = nil also disables deprecation warnings.
Warning.enable(:deprecated) will print warning messages generated through rb_warn.
[Feature #16289] (removing duplication) is also accepted.
The flag is global, not thread local nor fiber local.

[Feature #16363] Promote did_you_mean to default gem (yuki24)
Currently there are two problems with the gem being a bundled gem, one with the availability of the lib and the other about bundler.
mame: wanted to use did_you_mean from optparse but it is a bundled gem, which may be absent (uninstalled)
usa: bundler also reported a problem
Discussion:
usa: is it a promotion? not demotion?
Conslusion:
OK
[Feature #15605] json library needs more frequent releases
Should we bump the json version to 2.2.1?
Discussion:
Conclusion:
Will release 2.3 from ruby/ruby
naruse-san will ask flori to release new gem version
Charles can release the gem for the case no reply from flori

[Bug #15912] Allow some reentrancy during TracePoint events (alanwu)
I have seen quite a few people confused about Byebug’s REPL not working as they would expect due to this issue. I think it’s important to come up with some solution for this in two seven.
Discussion:
ko1 is involved in the discussion, so listen an explanation from him
akr: Zeitwerk should not use TracePoint. Instead, extend autoload appropriately, in this case
akr: Zeitwerk slide: https://speakerdeck.com/fxn/zeitwerk-a-new-code-loader?slide=49
naruse: We need to wait for more appropriate use case
Sample of Zeitwerk:
hotel.rb:
class Hotel
  include Pricing
end


hotel/pricing.rb:
module Hotel::Pricing
end


main.rb:
autoload :Hotel, "hotel"
tracer = TracePoint.new(:class) {|tp|
  if tp.self.name == "Hotel" then
    tp.self.module_eval { autoload :Pricing, "hotel/pricing" }
  end
}
tracer.enable
p Hotel


autoload is only usable after a parent module is defined.
But Zeitwerk want to use autoload before loading hotel.rb.
We may need autoload "Hotel::Pricing", "hotel/pricing".
[Feature #16142] Implement code_range in Proc and Method - Ruby master - Ruby Issue Tracking System (osyo)
Propose an API to get code position of Proc and Method so that we can get body of them (especially of a Proc).
I want to discuss method names and return values
Discussion:
samuel: This is an issue which I’ve run into when instrumenting Ruby code (e.g. AST rewriting). It’s tricky to get method source. I gave an extended proposal here: https://bugs.ruby-lang.org/issues/16142#note-5
samuel: Here is what pry has to do, to get source code for ruby method: https://github.com/pry/pry/blob/c123bce66116c2bb050ad47d0006e9df215ff3be/lib/pry/method.rb#L577-L592
matz: I agree that we should have a feature to get the end position of the range. I don’t like the name code_range. Dedicated class looks too rich for this feature.
samuel: How to handle methods that are defined dynamically?
define_method(:x) {puts foo}
path, lineno = method(:x).source_location
# ["(irb)", 1]
File.read(path) => # ???


If the source code is already loaded by the interpreter, is there any reason to read it from disk?
method(:p).source_location => nil


mame: The source code does not remain after it is parsed in the current MRI implementation (how about JRuby/CRuby?) dunno JRuby
samuel: Good point, but maybe it’s implementation detail. If source is not available, can we reconstruct from AST? in the case of define_method can we save the string (if used) or the source that defined the method in the first place?
How to handle more complex examples:
def foo(arg); end; def bar(arg); end
_, lineno = method(:bar).source_location


# So difficult for user to get method source.


# in test.rb
expr = proc {
  puts "hoge"
  puts "foo"
  puts "bar"
}


# Return [path, beg_pos.lineno, beg_pos.column, end_pos.lineno, end_pos.column]
p expr.code_location
# => ["./test.rb", 2, 12, 6, 1]


aycabta: I was thinking about implementing the feature to RDoc to take source code on IRB because IRB’s showing doc from RDoc feature is adjacent and I think it’s only one use-case.
Conclusion:
matz: commented. Let’s postpone after 2.7
[Feature #16129] Call initialize_clone with freeze: false if clone called with freeze: false (jeremyevans0)
Without this, use of clone(freeze: false) on objects not expecting it leads to unexpected state.
Allows fixing problems in delegate and set.
Discussion:
in short: allow this
def initialize_clone(orig, freeze: true)
  @foo = orig.instance_variable_get(:@foo).clone(freeze: freeze)
end


result of $csearch initialize_clone:
https://gist.github.com/535a60dce53e3a9a826240b915767bad
Incompatibility on initialize_clone method signature
Conclusion:
Try it in 2.8
[Bug #16242] Refinements method call to failed (jeremyevans0)
OK to fix modules that are refined and prepended by creating an iclass for the module (and not just the module’s origin)?
Discussion:
in short: prepend + refine
matz: It’s a bug
mame: nobu, please review the patch
[Bug #13446] refinements with prepend for module has strange behavior (jeremyevans0)
OK to fix case where prepend is used on a refined module after the module is included by creating an origin iclass during inclusion?
Discussion:
in short: prepend + refine
[Feature #16261] Enumerable#each_tuple (duerst) (zverok)
This proposal would make sense only if .: would not be reverted (which, to the best of my understaning, is now doubtful) (zverok)
Discussion:
in short: “each”'s yield *elem variant instead of yield elem
[1, 2, 3].zip([4, 5, 6]).map(&:+)            #=> error
[1, 2, 3].zip([4, 5, 6]).each_tuple.map(&:+) #=> [5, 7, 9]


[1, 2, 3].zip([4, 5, 6]).map{ _1 + _2 }
[1, 2, 3].zip([4, 5, 6]).map{|x, y| x + y }


[Feature #4539] Array#zip_with (duerst)
zip_with really comes in handy on occasion, and is available in many programming languages, in particular functional programming languages.
Discussion:
in short
[1,2,3].zip_with([6,5,4], :+) #=> [7, 7, 7]
[1,2,3].zip_with([6,5,4]) { |a,b| 3*a+2*b } #=> [15, 16, 17]




[1,2,3].map([6,5,4], [x, y, z]) {|a, b, c| a + b }


Haskell: zipWith
OCaml: List.map2
[Feature #16122] Struct::Value: simple immutable value object (zverok)
First version of the proposal was not clear enough, I am sorry for it. Clarified the descrition, added links and answered some possible questions.
Discussion:
in short: non-Enumerable, Immutable, non-Hashish Struct
A = Struct.new(:name, :email, immutable: true, enumerable: false, hashish: true, keyword_init: true)


Instead of hashish, maybe indexable?
module Struct::Value
  def self.new(*args)
    Struct.new(*args, immutable: true, enumerable: false, hashish: false)
  end
end


Conclusion:
Association between names and feature set are not clear enough.
Having helper methods to define that kind of variations sounds good. (Hash.Value(...))
Anyway, after 2.7.
[Feature #15822] Add Hash#except (zverok)
Matz: “We didn’t see the need for Hash#except yet. Any (real world use-case)? I don’t think the name except is the best name for the behavior.” Added real-life examples and name justifications.
Discussion:
in short: Hash#except :-)
Matz: I’d like to spend time to consider it later
[Feature #16166] Remove exceptional treatment of *foo when it is the sole block parameter (sawa)
Unintended arity. This must be fixed in an earlier stage before Ruby 3.
Discussion:
in short: matz decided the solution, but it brings incompatibility. Should it be changed in 2.7 or wait for 3.0?
Matz: I hope it in 2.7.  Go ahead.

time up

[Feature #16264] Real “callable instance method” object. .:method to be a first-class thing, instead of Symbol#to_proc trick (zverok)
This proposal would make sense only if .: would not be reverted (which, to the best of my understaning, is now doubtful)
Discussion:
in short: [1, 2, 3].map(&.:to_s) ?
[Misc #16291] Introduce support for resize in rb_ary_freeze and prefer internal use of rb_ary_freeze and rb_str_freeze for String and Array types (lourens)
Builds onto the capacity shrinking feature introduced by rb_str_freeze, targeting Array
Many internal uses that froze String types did not use the rb_str_freeze variation and could not benefit from resizing capacity on freeze
Implemented the same for Array
Let Array#freeze call rb_ary_freeze to expose the shrinking capability to user code too (as recommended by Shyouhei) for parity with String#freeze already doing the same
Discussion:
in short: shrink an array when it is frozen? @shyouhei
[Bug #15620] Block argument usage affects lambda semantic (alanwu)
I find the current behaviour unreasonably confusing and would like to see improvement, even though the bug doesn’t really show up in the real world often.
I have a pull request which restores the behaviour found in Ruby 2.4.x and warns about it, based off of matz’s comment in [ruby-core:94054].
Could someone confirm that is the desired fix? If it is the fix we want, could someone review the PR?
Discussion:
in short: lambda(&block).call
[Feature #16274] Transform hash keys by a hash (sawa)
Easily rename hash keys that do not follow a rule
Discussion:
hash = {created: 2019-10-23 17:54:46 +0900, updated: 2019-10-23 17:59:18 +0900, author: "foo"}
hash.transform_keys({created: :created_at, updated: :update_time})
#=> {created_at: 2019-10-23 17:54:46 +0900, update_time: 2019-10-23 17:59:18 +0900, author: "foo"}


[Misc #16375] Right size regular expression compile buffers for literal regexes and on Regexp#freeze (lourens)
Builds on Misc #16291 , I think there’s potential to apply this pattern to other types at hooks outlined at the end of the issue
A large set of literal regular expressions are quite common in Rails applications (mostly framework, but also application and dependencies)
In my Redmine boot test was able to shave 300kb off just excess regex buffer capacity
Discussion:
[Misc #16260] Symbol#to_proc behaves like lambda, but doesn’t aknowledge it (zverok)
Discussion:
f = :+.to_proc
f.lambda? # => false (should be true?)


[Feature #16348] Proposal: Symbol#start_with?, Symbol#end_with?, and Symbol#include? (naruse)
