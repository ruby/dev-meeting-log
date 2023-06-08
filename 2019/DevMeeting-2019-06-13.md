---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-06-13

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/06/13 (Thu)
Time: 14:00-17:30 (JST)
Place and Sign-up: https://ruby.connpass.com/event/132888/
log: https://docs.google.com/document/d/1XP_e-3utXlCaxZLJrov6c428ZNaMPmxa3eh9MASrzkI/edit#

# NOTES

- Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.7 timeframe

## Check security tickets

## Carry-over from previous meeting(s)

None.

## From Attendees

## From non-attendees

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

We don't guarantee to put tickets in agenda if the comment violate the format (because it is hard to copy&paste).

# Log

DevelopersMeeting20190613Japan
https://bugs.ruby-lang.org/issues/15874
Next Date
7/11 (Thu) 13:00-17:00 moneyforward
Ann
7/14, 15 Ruby Development camp @ https://marumo.net/gasshuku-plan001/
About 2.7 timeframe
Preview 1 Done
Preview 2 Summer at earliest?
Check security tickets
Nothing.
Topics
[Bug #15645] It is possible to escape Mutex#synchronize without releasing the mutex (alanwu)
Mame: it is already fixed by jeremyevans
Nobu: I checked his change and looks good
ko1: I’ll check it later.
[Bug #15360] “ThreadError: deadlock; recursive locking” error when recursive lock shouldn’t be possible (alanwu)
I tested this PR https://github.com/ruby/ruby/pull/2131 and it seems to fix both tickets. Could someone take a look?
[Bug #15792] GC can leave strings used as hash keys in a corrupted state (alanwu)
bad fstring + shared string interaction can lead to use-after-free
I have a PR https://github.com/ruby/ruby/pull/2183 for a case of this with String#b that I think we should also fix.
Could someone mark the ticket for backporting to all supported versions? It affects all supported versions as wanabe-san showed in the thread.
Already closed by @jeremyevans0
[Feature #14111] ArgumentErrorが発生した時メソッドのプロトタイプをメッセージに含む (nobu)
Include the method prototype in the message when ArgumentError occurred
[Feature #14145] Proposal: Better Method#inspect (nobu)
usa: noone against to introduce source location, so implement it first
ko1: how should it show prototype?
usa: thought it has a patch but it’s not completed, like block
ko1: we can implement it easily
usa: so, show both
ko1: isn’t it too long? should show by multi lines?
usa: it’s pretty printer’s job. inspect don’t have to care about it now
ko1: ok, let’s go
[Feature #15725] Add Array#reverse_sort, #revert_sort!, #reverse_sort_by, and #reverse_sort_by! (shikachu)
I would like to propose these methods as the preferred way for developers to create reverse sorted array, which is to call sort(_by).reverse! internally. By introducing these methods, we can discourage users to use them instead of do array.sort.reverse (without bang) or array.sort { |a, b| b <=> a } which would create an unnecessary Array (after .sort).
matz: I don’t see why this is needed. I don’t feel it attractive.
akr: Is this faster?
mame, nobu: The proposed patch just does sort.reverse! so it wouldn’t be fast.
[Bug #15733] Inconsistent __FILE__ and Kernel#__dir__ (tagomoris)
[Feature #15777] Module#autoload?(cname, inherit=true) (byroot)
By default autoload? also check in the ancestors chain, such option would be consistent with Module#const_defined? and totally backward compatible.
For more context this would be needed in Zeitwerk: https://github.com/fxn/zeitwerk/pull/17
matz: ok.
nobu: The patch has a problem. I’ll see.
[Proposal #15836] Make Module#name and / or Symbol#to_s return their internal fstring. (byroot)
I realize this is unlikely to be accepted because it has a very significant backward compatibility impact, but I’ll still make a case for it.
It’s surprising for both of these methods to allocate new strings when intuitively we’re accessing some static metadata.
These two methods are extensively used in metaprogramming code and other DSL, they are responsible for a significant amount of allocations and duplications.
Ideally to address the backward compatibility issues, the behavior would only change if frozen_string_literal: true is set, but I’m unsure this is actually possible to implement.
[Misc #15723] Reconsider numbered parameters. (eregon)
To matz: Could you agree on Jeremy’s single-argument patch?
[Feature #15897] it as a default block parameter
A keyword it has never been seriously considered because adding a new keyword brings a big incompatibility.
However, I’d like to show that it is possible to add it as a keyword with much smaller impact than we thought.
support multiple arguments |a, b, ...| or single parameter |e| ?
single argument ideas
@
@0
it
$it
\it
_
options [[1, 2, 3]].each{...}
(nothing) : mame mrkn k0kubun
single1 |e| (@) : [1,2,3] mame ko1 knu hsbt znz k0kubun
single2 |e,| (@1) : 1
multiple1 (@1, @2) current impl: 1, 2
mutitple2 (@, @1, @2) : [1, 2, 3], 1, 2 naruse usa hsbt osyo aycabta
NOTE: this is not a vote, just preference of the attendees.
ary.sort{|a, b| a<=>b}
ary.sort{@1<=>@2}
ary.sort_by{f(@1)}
ary.sort_by{f(*@)}
[[1, 2, 3]].each do
  @  #=> [1, 2, 3]
  @1 #=> 1
  @[0] = 42 # FrozenError?
  @1 #=> 1? 42?
end

[Feature #15160] ArgumentError: year too big to marshal (nobu)
naruse: we need to keep backward compatibility. When an old Ruby attempts to load the marshal data that a new Ruby dumps, it should raise an exception. There seems to be a validity check in an old Ruby’s Marshal#load, so the new format must be invalid for the checking code.
nobu: By setting the length more than 8, it may be possible. I’ll try.
[Bug #15210] UTF-8 BOM should be removed from String in internal representation (nobu)
A method to set the encoding on an IO by BOM?
matz: the feature is acceptable, but the name detect_bom is not good because the name does not describe that this method sets new encoding
usa: so, set_encoding_by_bom
martin: passing the default encoding for when BOM is not found
[Feature #15563] #dig that throws an exception if a key doesn’t exist (k0kubun)
Previously Matz said #dig! is a bad name and asked a real-world use case.
How about #deep_fetch for the name? See 15563#note-6 for the real-world use case.
I often use Ruby for configuration management, and I’ve seen the real-world use case at least 2 years ago, 5 months ago, and today.
How about passing to block like #fetch?
All #dig implements follow to specification. So hard to change #dig.
def deep_dig obj, *orig_keys
  keys = orig_keys.dup
  while true
    if r = obj.dig(*keys)
      break
    end
    dig.pop
  end
  if keys == orig_keys
    return r
  else
    # error
  end
end

[Feature #15903] Move RubyVM.resolve_feature_path to Kernel.resolve_feature_path (eregon)
eregon: @matz is it OK to add Kernel.resolve_feature_path (only class method)? I think RubyVM is not a good place for resolve_feature_path which is not MRI-specific. Moving the method is necessary for other Ruby implementations to implement it, and keep the clean separation that RubyVM is only defined on MRI.
nobu: How about $LOAD_PATH.resolve_feature_path?
[Feature #15799] pipeline operator (nobu)
# pipeline operator examples

foo|>bar|>baz
foo.bar.baz

foo |> bar 1, 2 |> baz
foo.bar(1, 2).baz

1..|>each{|x| puts x}
(1..).each{|x| puts x}

x |> (y) |> z # NG
x.(y).z == x.call(y).z

x |> [y] |> z # NG
x[y].z

x |> y = 42 |> z # NG
(x.y=(42)).z

x |> send(:y=, 42) |> z
# $ ./ruby -e 'x |> y = 2 |> z'
# -e:1: syntax error, unexpected '=', expecting end-of-input
# x |> y = 2 |> z
#        ^

ret = 1.. |> take(10)
# which ?
  ret = (1..).take(10)    # different meaning
  (ret = 1..) |> take(10) # current impl

ret = (1.. |> take(10)) # write this!

p x |> y |> z
  (p(x)).y.z # current impl
  p(x|>y|>z) # different meaning

$ ./ruby -v -e 'p(1|>succ|>succ)'
ruby 2.7.0dev (2019-06-13T08:41:29Z feature/pipeline-i.. 66f9256db6) [x86_64-darwin18]
last_commit=Pipeline operator in arg
3

other |> ideas
one-line block
1.times do |x|> puts x
ary = ary.map do |x|> x + 1

ary.sort |> @1 * @2
e.g.:
// C# (JavaScript too)
var square = x => x * x;
// is a shorthand of
var square = (x) => { return x * x; }

rightward assignment
x.y.z |> a
x.y.z => a
x.y.z |>= a


Time up

[Bug #15908] Detecting BOM with non-UTF encoding (nobu)
[Feature #14912] Introduce pattern matching syntax (pitr.ch)
Could the pattern matching be made available as a first-class citizen to be used as a filter when searching in data structures and to be able to implement Actor receive method
Consider contractions log_messages.find in [:error, message] { puts message }
Non-symbol matching of Hashes would be very desirable
Elaborated in https://bugs.ruby-lang.org/issues/14912#note-22
[Feature #15797] Use realpath(3) instead of custom realpath implementation if available (jeremyevans0)
Is this OK to commit? It may cause regressions on less common Unix not tested by Travis (e.g. FreeBSD, NetBSD, AIX, Solaris), though we could just fallback to current implementation in that case.
Do we want to add workarounds for Mac OS <=10.5 (10.5 was released in October 2007)?
[Feature #15915] @1 cannot get from meta-programming
[Feature #15901] Enumerator::Lazy#eager
[Feature #15896] Symbol#+
[Feature #15894] Remove support for IA64
[Feature #15902] Add a specialized instruction for .nil?
[Misc #15893] open-uri: URI.open status
[Feature #15881] Optimize deconstruct in pattern matching
[Feature #15878] Make exit faster by not running GC


