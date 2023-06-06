---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2019-08-29

Please comment on your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of the ticket comments)

*DO NOT* discuss then on this ticket, please.

----

Date: 2019/08/29 (Thu)
Time: 13:00-17:00 (JST)
Place and Sign-up: https://connpass.com/event/139231/
log: https://docs.google.com/document/d/1XypDO1crRV9uNg1_ajxkljVdN8Vdyl5hnz462bDQw34/edit

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

**A short summary of a ticket is strongly recommended.  We cannot read all discussion of the ticket in a limited time.**
A proposal is often changed during the discussion, so it is very helpful to summarize the latest/current proposal, post it as a comment in the ticket, and write a link to the comment.

# Log

Next Date
9/19 (Fri) 13:00-17:00 @ moneyforward
optional: 9/2 (Mon) 13:00-16:00 @ zoom
Ann
#release channel in ruby Slack
mainly for release managers to progress their process
About 2.7 timeframe
preview 2
numbered parameter will be introduced
pipeline operator is self-rejected
foo\n# comment\n.bar should be fixed
feature freeze (Nov.)
Check security tickets
Topics
[Feature #11460] Unhelpful error message when naming a module with the same name as an existing class (nobu)
Discussion:
in short: “More helpful error message, please!”
class A; end
module A; end #=> TypeError: X is not a module
              #   FILE:line: previous definition of X was here


matz: Go ahead.
[Misc #15805] Let memory sizes of the various IMEMO object types be reflected correctly (methodmissing)
Only the imemo_tmpbuf type’s auxiliary malloc heap buffer is factored into obj_memsize_of, other imemo types also alloc on the heap
PR: https://github.com/ruby/ruby/pull/2140
Discussion:
in short: “memsize_of of IMEMO objects is not precise!”
already committed. done.
[Misc #15806] Explicitly initialise encodings on init to remove branches on encoding lookup (methodmissing)
Currently rb_enc_from_index and related methods explicitly check for encoding table init on each call - I think this should be initialised at boot time instead as even the simple case of loading the interpreter requires the encoding table to be loaded anyways (symbol.c)
PR: https://github.com/ruby/ruby/pull/2128
Discussion:
in short: “lazy initialization of rb_enc_init is really needed? nobu?”
usa: It should not work on windows with Japanese path name.
[Feature #12093] Eval InstructionSequence with binding (nobu)
Discussion:
ko1: do you want to allow ISeq#compile to accept a binding? Use case? Create another ticket.
[Bug #11326] Defining a writer as a Struct member allowed? (jeremyevans0)
Should we ban struct members ending in =?
Do we also want to ban struct members ending in ! or ??
Discussion:
in short: “Struct.new(:x=)”
matz: accepted.
usa: note that it’s incompatibility if we can do it now. but this does not mean I disagree this change, of course.
[Feature #15588] String#each_chunk and #chunks (Glass_saga)
name: #each_slice and #slices are better?
size as characters or bytes
should we support :strip parameter?
Discussion:
in short: “A feature to take every n chars”
matz: It seem good to have this feature. I don’t like all names proposed so far (chunk* and slice*).
shyouhei: I don’t see any use case. Without it, we cannot propose alternative names.
matz: Indeed, use case is needed too.
# usa: just idea, a use case for static length records
str.each_slice(7, 10, 20) do |zip, tel, name|
  ...
end


[Feature #15553] Addrinfo.getaddrinfo supports timeout (Glass_saga)
I’d like to have some feedback for patch2.diff
Discussion:
in short: “A patch to make getaddrinfo interruptible, please review”
usa: akr should review this, but he does not attend. Skip.
[Misc #15723] Reconsider numbered parameters (sikachu)
Reading from description, I think there are a few supporters of removing @1, @2 in favor of just having @ refer to the first argument.
I also believe having just @ is enough for major use cases, and for the readability of the code when multiple implicit receivers are used.
Proposal: Remove @1 and @2 and have only @ for the first argument in Ruby 2.7+
Discussion:
in short: "single1 @"
matz: I’d like to pick _1, _2, _3, and so on. _0 is equivalent to |_0|. Prohibit use of both _0 and _1 simultaneously.
nobu: Will do.
osyo: If there is a variable named _0, …?
matz: Always warn such a use case.
durest: is @ rejected?
matz: Yes.
ko1: _0 and _1 is sequential but different meaning(|_0| and |_1,|). it is confusing. But I have no counterproposal, though.
Obsolete _0, … plan:
local variables (parameters and assigned variable) → force warning (Don’t use) on Ruby 2.7 and syntax error on Ruby 3.
method invocation
vcall: x = _0 # expect _0() outside block. → force warning on Ruby 2.7 and syntax error on Ruby 3.
vcall: 1.times{ _0 } → block parameter (incompatibility)
vcall: 1.times{|i| _0 } → force warning on Ruby 2.7 and syntax error on Ruby 3.
method invocation (x = _0(), x = foo._0) → no warning
method name (def _0(); end) → no warning
[Feature #15998] Allow String#-@ to deduplicate tainted string, but return an untainted one (ko1)
taint removal schedule
Discussion:
in short: “When will taint be removed?”
ko1:
warn $SAFE = 1 and all relevant methods (taint, tainted?)
remove the feature itself (and warn)
shyouhei: setuid’ed ruby automatically sets $SAFE = 1.
naruse: I created https://bugs.ruby-lang.org/issues/16131
[Feature #15868] Implement File.absolute_path? (nobu)
Discussion:
matz: go ahead
[Feature #16029] Expose fstring related APIs to C-extensions (nobu)
Discussion:
ko1, nobu: now it is difficult to expose them because of current implementation.
[Feature #15778] caller_locations(debug: true) to access bindings of the stack. (eregon)
Can we introduce the API? If not, why not? If not, please propose a way to support such functionality on other Ruby implementations (e.g., JRuby, TruffleRuby).
Discussion:
in short: “Wants a legitimate way to get caller bindings (for debug)”
ko1: I’m against introducing this feature because it would break caller-callee relationship; a callee should not depend on what the caller has in its scope. At the very least it should not be used in production code.
matz: if JRuby developers accepts this, I accept this. But are they okay? I’d like eregon to confirm them.
matz: I’m okay for debug. It represents debug-purpose clearly.
[Feature #16035] Allow non-finalizable objects such as Integer, static Symbol etc in ObjectSpace::WeakMap (nobu)
Discussion:
ko1: It makes the object non-collectable. Is it intended? Need to confirm.
matz: I think it seems needed. I’ll comment and accept.
[Feature #16038] Provide a public WeakMap which compare by equality rather than identity (nobu)
Discussion:
matz: want OP to elaborate euse case
[Bug #15908] Detecting BOM with non-UTF encoding (nobu)
Discussion:
in short: On Windows, often text files may be Unicode(UTF-16LE-BOM) or OEM(Codepage). Currently there is no easy way to open them properly.
naruse: I understand there theorecally exists a situation this feature is useful. But I think in practice it doesn’t exist. If needs be, the user should have already managed to do that on their own.
usa: I often use “BOMed UTF-8 or CP932” CSV file for Excel. But, it’s only generating. I’ve never written a script which reads such CSV file…
[Feature #14781] Enumerator.generate (zverok)
Matz seems to have liked the idea and name proposed; implementation patch provided; the name should be decided upon: either generate, or, maybe, produce (looking like more-or-less oppose to reduce)
Discussion:
in short: Enumerator.generate(1, &:succ).take(5), the names generate and produce are proposed
knu: I think this is a nice feature to add, and the newly proposed name “produce” sounds good. I withdraw the name “from”, and the idea of optionally giving multiple seeds.
knu: As I commented in the ticket, I’d like to add support for StopIteration so the proc itself can define an end without requiring user to always use take_while to stop the stream before getting a NoMethodError.
matz: produce sounds reasonable.
[Feature #14784] Comparable#clamp with a range (zverok)
The issue generally agreed on, except for behavior with upper bound with excluding-end range. ArgumentError is proposed for this case.
Discussion:
in short: "Raising an exception is proposed for clamp(1...2)"
3 .clamp(1 ...2 ) #=> ArgumentError
3r.clamp(1r...2r) #=> ArgumentError


knu: Kernel#rand(range) accepts an exclusive range, but that’s because rand() only deals with Float and Integer which always have a predecessor. (clamp() deals with Rational in addition to Float and Integer)
matz: Accepted.
[Feature #15815] Add option to raise NoMethodError for OpenStruct (mtsmfm)
We can use Symbol#to_proc with enumerable stuff when we use OpenStruct instead of Hash. For example: OpenStruct.new(JSON.parse(users)).map(&:id)
But it can’t prevent typo. For Hash, it has Hash#fetch to raise KeyError.
Discussion:
in short: “Raise NoMethodError for undefined key of OpenStruct”
matz: seems reasonable.
mame: OpenStruct.new(a: 1, exception: 1) should make a key exception for compatibility. (i.e. when giving an exception keyword argument, the first Hash argument is required)
[Feature #16103] Make the dot-colon method reference frozen (nobu)
Discussion:
ko1: sounds reasonable
matz: sounds reasonable
[Feature #15864] Proposal: Add methods to determine if it is an infinite range (mrkn)
I proposed some candidates from mathematical terms
Discussion:
in short: “Alternative name proposal of Range#endless?: upper_unbounded?, right_unbounded?, or unbounded_above?”
matz: use case is needed. The proposed names have chance.
[Feature #14183] “Real” keyword argument (mame)
Jeremy and I agreed with Jeremy’s proposal. I’d like to ask for matz’s final confirmation.
Proposal summary:
keyword arguments are separated from positional arguments
Always need to use foo(kw: 1) or foo(**opt) to pass a keyword argument
Always need to use def foo(kw: 1) or def foo(**opt) to accept a keyword argument
But for compatibility, “Jeremy’s hack” remains (in Ruby 3)
Only when a method does not accept keyword arguments,
and when a call passes keyword arguments,
the keyword arguments was converted to a Hash object as the last argument
def foo(opt = {})
end
foo(kw: 1) # OK


Discussion:
in short: “Jeremy made the branch of keyword separation ready. Let’s merge it!”
matz: Go ahead.
[Feature #15955] UnboundMethod#apply (mame)
The ticket proposes a shortcut to unbound_method.bind(obj).call(args…) without allocation of a Method object. There are some use cases, and looks reasonable to me. What do you think?
Discussion:
in short: “Faster shorthand for unbound_method.bind(obj).call(args...). Use cases: pp, sorbet-runtime, zeitwerk”
…naming discussion…
mame: How about UnboundMethod#bind_call ?
matz: Go ahead.
[Feature #16115] Keyword arguments from method calls or ignore extra hash keys in splat (mame)
It proposes a triple splat (foo(***opt)) to pass a keyword hash with filtering out unknown keywords. What do you think?
def foo(kw: 1)
end
opt = { kw: 2, unknown: 3 }
foo(***opt)


Discussion:
in short: “A feature to ignore unknown keywords”
ko1: The argument spec should be selected by one who define the method. I’m skeptical to allow
mame: Adding a parameter to an existing method definition is casual when version up. But client code cannot use the new feature if it works with both old and new versions.
amatsuda, naruse: Skeptical.
ko1: Do you need a method to get the list of supported keywords of a method? Would it suffice?
[Feature #15991] Allow questionmarks in variable names (osyo)
Proposal: Allow ? in variable names
In #5781, matz is explicitly against an instance variable that ends with ?
How about allowing only local variables ?
Discussion:
in short: Allow foo? = 1
nobu: Seems to me it’s too difficult to implement it. It will likely conflict with the ternary operator, multiple assignment and so on.
[Feature #16113] Partial application: fetch(urls).map(&JSON.:parse.w(symbolize_names: true)) (zverok)
Discussion:
matz: I don’t like it, will comment.
many: As for this example, what’s wrong with the normal block notation? You could do it in much shorter code.
[Feature #16122] Struct::Value: simple immutable value object (zverok)
Discussion:
in short: “immutable variant of Struct.”
knu: def initialize(*); super.freeze; end is not enough?
knu: Why not Enumerable? I’d write a plain old ruby class.
mame: The API should be Struct.new(:a, :b, mutable: false) or what not.

