---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-10-21

Date: 2015/10/21 (Wed)
Time: 14:00- 18:00 (JST)
Place: Tokyo, Japan

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

* example: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [ruby-trunk - Feature #10600] [Open] [PATCH] Queue#close (ko1)

## From non-attendees

* Add a proc to autoload so that the proc can handle loading the constant instead of going through `require` [Feature #11415]  :  (tenderlove)
* Decide whether -*- should be required for frozen_string_literal magic comments [Feature #8976] : (shugo)
* Kernel#loop: return the "result" value of StopIteration [Feature #11498] : (knu)

(Additional explanation is welcome because we can't ask about it immediately)

# Log

# Decide whether -\*- should be required for frozen\_string\_literal magic comments \[Feature [#8976](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8976&sa=D&source=editors&ust=1686086593905098&usg=AOvVaw0PwQESLP4h-eULUVbRLbQv)\]

naruse: vim’s indicator is “vim: …” [http://vim.wikia.com/wiki/Modeline\_magic](https://www.google.com/url?q=http://vim.wikia.com/wiki/Modeline_magic&sa=D&source=editors&ust=1686086593905604&usg=AOvVaw3YVUpvRDjdZaL7rn27xQ7T)

nobu: should be have an indicator of pragrma.

matz: why?

nobu: to avoid misunderstanding with comments.

matz: nobody care about that.

nobu: line should not include any other information.

matz: i agree with it.

conclusion:

- accept pragma without indicators
- do not accept pragma in the comment (do not skip any words before pragma keywords)

matz: i don’t want to recommend indicators.

nobu: sould we remove indicators?

akr: should remain indicators to keep compatibility.

ko1: only coding pragma?

nobu: we have warn-indnent pragmra.

naruse: warn-indent is in 2010

nobu: i’ll make it to keep compatibility.

conclusion:

- keep compatibility for current indicator (for any pragma)

naruse: should we put pragmas in one line? or multi-lines?

akr: there are possibilities to increase pragma, so that shold accept multi-lines.

matz: i do not care about multi-lines.

akr: people may want to write copyright early.

naruse: people may want to write a comment for pragmas

conclusion:

- accept multi-lines at the beggining of programming.
- accept comments between pragmas.

akr: how about dynamic strings “foo#{bar}baz”?

nobu: how about “#{‘baz’}”?

ko1: it is easy to understand all “...” is frozen. Shugo-san’s first comment is for “compatibility”.

matz: i agree to

matz: i have another idea +“...” -> mutable, -”...” -> immutable. it is more clean than “...”.freeze. how about that, instead of the magic comment? i don’t care about pull-requests, but care about the representation of source code.

akr: …

matz: I don’t like magic comment, so that I hesitate to introduce it.

a\_matsuda: + and - seems string operation.  << -”foo” can be seen as a here document.

matz: I understand.  + and - is not good idea.

matz: another idea: make ‘’’foo’’’ frozen.  incompatibility is negligible.

matz: yet another idea: make single quoted strings frozen. This is incompatible.

MISC:

akr: should we separate this topic to another ticket? this is “how to write a pragmra”.

ko1: we should describe syntax of “how to write pragmas”

matz: I don’t like pragmas but if it is required

ko1: this pragma is for experimental feature. if we decide to reject default frozen string literal, then you remove this pragma, or remain this pragma?

matz: i will remain it.

deep\_freeze

→ traversal framework?

inspect に引数を渡したい

→ inspect2? not clean.

naruse: On 1.9 I tried to implement such logic, but I gave  up and use Encoding.default\_internal/Encoding.default\_external

# .?

nobu: syntax fixed? there are 3 possibilities.

obj.?foo =>

1. obj != nil ? obj.foo : nil
2. obj.respond\_to?(:foo) ? obj.foo : obj
3. obj.respond\_to?(:foo) ? obj.foo : nil

matz: 1.

akr: accepted?

matz: i’m not sure now.

akr: obj.&foo, obj.&&foo is proposed. how about it?

matz: i can accept original proposal.

\[ruby-core:70854\] \[Ruby trunk - Feature #11537\]

# Misc

C# Design Notes for Sep 8, 2015

[https://github.com/dotnet/roslyn/issues/5383](https://www.google.com/url?q=https://github.com/dotnet/roslyn/issues/5383&sa=D&source=editors&ust=1686086593910852&usg=AOvVaw0VPTlIBXZymQ3Wo5ldpUym)

?a syntax

matz: I want to deprecate it

akr: If people aren’t in trouble, it should be kept because of compatibility

class String

 def +@

 self

 end

 def -@

 self.freeze

 end

end

str = ''

str<<-'foo'

str<<-'foo'

p str

str = ''

puts <<-'foo'

hello

foo

p str

str = ''

puts = 'bar'

puts <<-'foo'

hello

foo

p str

irb(main):007:0> puts <<-'foo'

irb(main):008:0' hello

irb(main):009:0' foo

hello

\=> nil

current option:

1. magic comment
2. +/- (=> not bad, ex: str = <<-’foo’ # here document?) => “””...”””, “””...”
3. ‘...’ to freeze

other ideas:

1. :foo -> frozen string
2. :’foo’ -> frozen string
3. :foo:
4. %f{...}
5. ?x -> frozen
6. ?’...’ -> frozen(syntax extend)

str = ?’foo’

str = !’foo’ -> bad idea (ex: str = !’foo’.start\_with(‘f’))

# [https://twitter.com/sferik/status/642063693304451072](https://www.google.com/url?q=https://twitter.com/sferik/status/642063693304451072&sa=D&source=editors&ust=1686086593915082&usg=AOvVaw2Y2K9qgCFN4c_YBAvMSouS)

matz; how about to warn for “initialise”?

akr: found 3 cases and 1 case uses “initialise” intentionaly.

# Next

11/9 (mon) 14:00 (JST)-

@SFDC Tokyo
