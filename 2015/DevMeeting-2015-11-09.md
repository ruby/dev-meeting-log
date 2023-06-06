---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-11-09

Date: 2015/11/09 (Wed)
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

## From non-attendees

* [Feature #11666] IPAddr#private? (glass)

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendees: matz, nobu, hsbt, akr, naruse, sorah, yuki, ko1

# Next meeting

2015/12/07 Mon 14:00 JST at SFDC

# \[Feature [#8976](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8976&sa=D&source=editors&ust=1686086629237034&usg=AOvVaw305ggw0LyuhLDsm00vbufd)\] file-scope freeze\_string directive

## freeze dynamic string literal or not?

- pros (利点)
    (freeze string object allocated by “...#{...}...”)

- easy-to-explain, consistent: all string literals create frozen string objects.
- compatibility problem is already big enough.
- “#{exp}”.dup can be optimized so it won’t allocate extra objects like “...”.freeze.

- cons (欠点)
    (don’t freeze string object allocated by “...#{...}...”)

- shugo’s objections

- mitigate compatibility problem
- creates an extra string object (“#{exp}”.dup needs one more allocation)

- result

- matz: freeze it. simplicity matters here.

## debugging option implemeted by ko1

- current implementation:

- $ ./ruby --enable-frozen-string-literal -e 'p "" << "a"'
    \-e:1:in \`<main>': can't modify frozen String (RuntimeError)
- $ ./ruby --enable-frozen-string-literal --enable-frozen-string-literal-debug -e 'p "" << "a"'
    \-e:1:in \`<main>': can't modify frozen String, created at -e:1 (RuntimeError)

- How about enabling it by deafult? (ko1)

- Pros: easier to find a bug caused by this feature
- Cons: memory consumption (add file, line as instance variable for each string object allocated at compile time, can’t dedup)

- should we add the feature?

- matz: We should definately add it.
- matz: We can always enable it if the overhead is small enough.
- ko1: I’ll measure the overhead.

- better option name:

- We don’t need an option name if it is always enabled.

# \[Feature [#4840](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4840&sa=D&source=editors&ust=1686086629240030&usg=AOvVaw2uPK_OKxtBrOwaRgGbruU6)\] Allow returning from require

\### Before

unless cond

 class C

 end

end

\# … or …

class C

end unless cond

\### After

return if cond

class C

end

- pros

- An alternative to ”class (many lines) end if condition”

- cons

- return from non-toplevel position (in class definitions, for example) is not good idea.

- result

- matz: acceptable if matz’s constraint is properly implemented.
    matz’s constraint under top-level:

- ok

- if-statement (intended case)

- acceptable (optional)

- while-clause
- begin-clause
- rescue-clause
- ensue-clause

- forbidden (should raise an error)

- class definition
- method definition (already used for returning from method)
- block
- lambda (already used)

### Discussed use cases, examples

\# OK

if xxx

 return

end

while xxx

 return

end

begin

 raise

rescue

 return

ensure

 return

end

\# NG

1.times{

 return

}

Class.new do

 return

end

pr = Proc.new{

 return

}

def foo

 return

end

# \[Feature [#9098](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9098&sa=D&source=editors&ust=1686086629246775&usg=AOvVaw2jAu9y3Ym2D62Ll4HI044G)\] Indent heredoc against the left margin by default when "indented closing identifier" is turned on.

\# 2 spaces

 str =<<~EOS

 hello

 EOS

#=>”\\nhello\\n”

Matz: acecptable, but there could be a problem if hard tabs and spaces are both used in one heredoc.

- pros

- easy to indent text block
- ruby-mode.el doesn’t use TAB by default
- matz: spaces are encouraged today

- cons

- needs to count spaces but number of spaces for a tab is not portable

- e.g. How can we handle this?: "      a\\n\\t2\\n\\t  3" (6 spaces, 1 tab, 1 tab + 2 spaces)

- result

- matz: accepted. TAB skips to next multiples of 8 characters (spaces).
- matz: Consider ASCII only. Unicode is not required here.

Nishijima-san suggested another way to write strings like DATA for each files. It will be proposed as another ticket.

# \[Feature [#11643](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11643&sa=D&source=editors&ust=1686086629249954&usg=AOvVaw3bgMFujgdEH0PWLs9Slb_P)\] A new method on Hash to grab values out of nested hashes, failing gracefully

Matz: acceptable, but name is problem.

Candidates:

- dig
- fetch\_in
- assoc\_in (from Clojure): https://clojuredocs.org/clojure.core/assoc-in

Example:

h = {a: {b: {c: 1}}}

h.dig(:a, :b, :c) #=> 1

h.fetch\_in(:a, :b, :c) #=> 1

Note that Matz doesn’t like &.\[\] (safe navigation operator with \[\]).

Nishijima-san mentioned past rejected proposals in Rails [\[1\]](https://www.google.com/url?q=https://groups.google.com/d/topic/rubyonrails-core/hww2nP8Zx4w/discussion&sa=D&source=editors&ust=1686086629251781&usg=AOvVaw1ZekyIipEG1XSKbU892j93) [\[2\]](https://www.google.com/url?q=https://twitter.com/a_matsuda/status/592849107762475009&sa=D&source=editors&ust=1686086629252101&usg=AOvVaw28diYOnHT1nPddccQGQL85), they were rejected because such object in use cases should be wrapped with proper objects.

- pros

- useful to access JSON, especially DB-style JSON (Array of Hash)
- useful for shallow Hash/Array
- not practical to define classes to use JSON

- cons

- creating nested Hash is Perl4 style.
- shoud use dedicated class.
- useless for deeply nested hash (difficult to maintain)

- result

- matz: accepted. A preferable method name would be “dig”.
- matz: returns nil if it fails to traverse all given path

- e.g. {a: {b: {c: 1}}}.dig(:a, :b, :x) == nil (not {c: 1})

# \[Feature [#11665](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11665&sa=D&source=editors&ust=1686086629253785&usg=AOvVaw2-Hkf1kjJuY73Ta3ftAWCB)\] Support nested functions for better code organization

class C

 def foo

 # current

 x = 1

 bar = lambda { x = 10 }

 bar.call

 p x #=> 10

 # proposed

 x = 1

 def bar; x = 10; end # isolated from outer scope

 bar()

 p x #=> 1

 end

end

- pros
- cons
- other

- matz: (different from this proposal, mentioned about) new visibility to realize \*true\* private
- ?: Lambda like but can’t refer outside scope? And use it with local variable to complete this use case
- matz: Obsoleting nested def?

- result

- matz: commented at [https://bugs.ruby-lang.org/issues/11665#note-3](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11665%23note-3&sa=D&source=editors&ust=1686086629256181&usg=AOvVaw3M-5Ke8tsnKa9JoTAVJIh5)

# \[Feature [#11666](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11666&sa=D&source=editors&ust=1686086629256639&usg=AOvVaw2aoPqU-rovW6shbNPYSZ9C)\] IPAddr#private?

Matz accepted this feature. Pass to knu -san (maintainer).

# \[Feature [#11588](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11588&sa=D&source=editors&ust=1686086629257153&usg=AOvVaw0Je12v8DTmmbG_r7AMBnre)\] Implement structured warnings

- pros

- Categorized warning can be helpful.

- ex) disabling future incompatibility warning is useful for production system.

- disabling/catching warning in test is desireble
- Rails has assert\_deprecated

- cons

- It is doughtful that defining warnings by class system is good idea.
- sorah: Don’t use Kernel#warn for such case, use logger or something and stub it by your own

- result

- no conclusion.

# \[Feature [#11653](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11653&sa=D&source=editors&ust=1686086629258468&usg=AOvVaw3ybz4P1lTPCMyh8SRIdR2v)\] Add to\_proc on Hash

class Hash

 def to\_proc

 proc{|key| self\[key\]}

 end

end

h = {1 => 10,

 2 => 20,

 3 => 30}

p \[1, 2, 3\].map(&h) #=> \[10, 20, 30\]

Matz: acceptable

Matz: to\_proc is intended for & argument operator. So it is reasonable to add.

# \[Feature [#10984](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10984&sa=D&source=editors&ust=1686086629260420&usg=AOvVaw3Dtz_rIysRVt2ZLvU56NzV)\] Hash#contain? to check whether hash contains other hash

Matz: Feature is acceptable. But naming issue;

Method name candidates:

- contain?
- subhash?
- subset?

- sorah: Set class?

- actual\_hash\_include?
- hash\_include?
- super\_hash?
- \=~
- sub === h
- h > sub, sub < h Perl6 way

- This comparison is similar to Module.

Matz: I vote for “<”, “>”, “<=” and “>=” (no “<=>” and “===”).

# \[Feature [#9108](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9108&sa=D&source=editors&ust=1686086629262227&usg=AOvVaw0yao0ymha7fNRMDhrXv3Nm)\]\[Feature [#8499](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8499&sa=D&source=editors&ust=1686086629262509&usg=AOvVaw27B-oerOWdzRueNvZA7oKx)\] Importing Hash#slice, Hash#slice!, Hash#except, and Hash#except! from ActiveSupport

Current status: naming issue, select and reject are acceptable for Matz.

- select and reject return Enumerator when no block is given

- so args = \[\]; {...}.select(\*args) returns an Enumerator object

- Akr proposed the methods that accept only one Array arg for this case, not va\_arg.

- {...}.select(\[:k1, :k2, :k3\])
- Hash accepts Array as a key, we can’t distingush Array keys and argument for the method. So we can’t support both form (accepting va\_arg and Array argument)

no conclusion yet
