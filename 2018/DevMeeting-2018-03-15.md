---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-03-15

Date: 2018/03/15 (Thu)
Time: 14:00-18:00 (JST)
Place: Cookpad, Inc.
Sign-up: https://connpass.com/event/80317/
Attendees: Martin Dürst (from ~16:00) (add your name here if you would like to participate)
Regrets: (add your name here if you often attend, but can't attend this time)
Language: mostly Japanese (sorry for non native Japanese speakers)

Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is a very busy person.  Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
  * We will write a log about discussion to a file or to each ticket in English.
  * All activities are best-effort (keep in mind that most of us are volunteer developers).
  * The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name (see example below). Explain details on the ticket. If you cannot attend the meeting, we appreciate a short summary because we can understand it more easily (long discussion is difficult to read, especially in a non-native language). Your motivation is also welcome.

## About 2.6 timeframe

* [[ruby-master:ReleaseEngineering26]]

## From attendees

* [Feature #12732] An option to pass to `Integer`, `Float`, to return `nil` instead of raise an exception (mrkn)
* [Feature #14476] Adding same_all? for checking whether all items in an Array are same (mrkn)
* [Feature #14362] use BigDecimal instead of Float by default (mrkn)
* [Feature #14044] Introduce a new attribute `step` in Range (mrkn)

## From non-attendees

* [Feature #14245] Add File.read etc. (shugo)
* [Feature #14579] Hash value omission (shugo)
* [Bug #14541] Class variables have broken semantics, let's fix them (eregon). I'd like more opinions and thoughts on whether we can change them.
* [Bugs #14380] Change Hash#transform_keys! and break compatibility with Ruby 2.5 and ActiveSupport, or not?
* [Feature #11473] Immutable String literal in Ruby 3 (hsbt): Do you really want to change it at Ruby 3? If It's yes, We should add a warning with destructive action on Ruby 2.6.

## Carry-over from previous meeting(s)

- matz
  * [Feature #4514] #deep_clone and #deep_dup for Objects
  * [Feature #4521] NoMethodError#message may take very long to execute
  * [Feature #4539] Array#zip_with
  * [Feature #4592] Tempfileを直接保存したい
  * [Feature #4780] String#split with a block
  * [Feature #4818] Add method marshalable?
  * [Feature #4824] Provide method Kernel#executed?
  * [Feature #4907] enumerable#permutation and combination
  * [Feature #4910] Classes as factories
  * [Feature #5007] Proc#call_under: Unifying instance_eval and instance_exec
  * [Feature #5064] HTTP user-agent class
  * [Feature #5129] Create a core class "FileArray" and make "ARGF" its instance
  * [Feature #5133] Array#unzip as an alias of Array#transpose
  * [Bug #5179] Complex#rationalize and to_r with approximate zeros
- nobu
  * [Feature #2324] Dir instance methods for relative path
  * [Bug #3618] inf-ruby prompt and tab completion
  * [Feature #4924] mkmf have_header fails with C++ headers
- ko1
  * [Feature #2447] reduce GC pressure by symbol table without String instance
  * [Feature #3187] Allow dynamic Fiber stack size
  * [Feature #3731] Easier Embedding API for Ruby
  * [Bug #4040] SystemStackError with Hash[*a] for Large _a_
- naruse
  * [Feature #2567] Net::HTTP does not handle encoding correctly
  * [Feature #2631] Allow IO#reopen to take a block
  * [Bug #4173] TestProcess#test_wait_and_sigchild が、たまに失敗する
- akr
  * [Feature #3608] Enhancing Pathname#each_child to be lazy
  * [Feature #3848] Using http basic authentication for FTP with Open URI
  * [Feature #4560] [PATCH] lib/net/protocol.rb: avoid exceptions in rbuf_fill
- knu
  * [Feature #3953] TCPSocket / UDPSocket do not accept IPAddr objects.
- mame
  * [Feature #4247] New features for Array#sample, Array#choice
- suke
  * [Bug #4405] WIN32OLE & Threads incompatible
- kosaki
  * [Feature #4464] [PATCH] add Fcntl::Flock object for easier use of POSIX file locks
- yugui
  * [Feature #4831] Integer#prime_factors
- hsbt
  * [Bug #5060] Executables in bin folder conflict with their gem versions.
- anyone
  * [Feature #4017] [PATCH] CSV parsing speedup
  * [Bug #4157] test_pty で、たまに出る Failure
  * [Feature #4483] PStoreをデフォルトで複数のスレッドから扱えるようにしたい

# Log

## Date: 2018/03/15 (Thu)

## Time: 14:00- 18:00 (JST)

## Place: Cookpad Inc.

## Sign-up: [https://ruby.connpass.com/event/73509/](https://www.google.com/url?q=https://ruby.connpass.com/event/73509/&sa=D&source=editors&ust=1686087561414743&usg=AOvVaw0MvDKXts21YuDXj-jK4dBx)

## log edit: https://docs.google.com/document/d/1RT0ijSo8uJ4Awn3CEvuYkjH0TVeXSYgeAFNmVGYC3ak/edit#

## log: TBD

## Agenda

### Next Developper Meetings

2018/04/19 (Thu) @ Speee

### About 2.6 timeframe

- Preview 1 has been released with MJIT.

### Stable versions

Hopefully, there will be a release in March.

Maintainers starting Apr 2018:

- 2.2: EOL
- 2.3: usa
- 2.4: usa
- 2.5: nagachika

### From attendees

- \[Feature [#12732](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12732&sa=D&source=editors&ust=1686087561417823&usg=AOvVaw2TnZIF1U2DQv_gdsTY_QRI)\] An option to pass to Integer, Float, to return nil instead of raise an exception (mrkn)

- Resolved
- Mrkn will file the conclusion.

- \[Feature [#14476](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14476&sa=D&source=editors&ust=1686087561419003&usg=AOvVaw1nMdUcpLTgTebmSY6mx8m5)\] Adding same\_all? for checking whether all items in an Array are same (mrkn)

- Definition of “same”? -> \`\==\`, for mrkn’s case
- Empty array? -> true, \[\].all?{} #=> true
- same\_all? -> bad name: unnatural English
- all\_same? Or all\_equal? -> natural, but it seems to use Object#equal? (assert\_same uses Object#equal?)
- uniform? -> “uniform array” means an array contains same type values
- constant? -> easy to misunderstand
- uniform\_values? -> verbose
- Usage examples:

- ary.all\_equal?
- ary.all\_equal?{|e| e.foo}
- ary.all\_same?
- ary.all\_same?{|e| e.foo}
- ary.uniform?
- ary.uniform?{|e| e.foo}
- ary.uniform\_values?
- ary.uniform\_values?{|e| e.foo}

- \[Feature [#14362](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14362&sa=D&source=editors&ust=1686087561422488&usg=AOvVaw2_hvsNqgrOfzLr6297Pr0O)\] use BigDecimal instead of Float by default (mrkn)

- Reject; Unacceptable performance & too incompatible
- Matz will respond.

- \[Feature [#14044](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14044&sa=D&source=editors&ust=1686087561423466&usg=AOvVaw0GwSuqgDrE8GunO-JRw_zE)\] Introduce a new attribute step in Range (mrkn)

\# current

p 1.step(10, by: 2) #=> #<Enumerator: 1:step(10, {:by=>2})>

p 1.step(by: 2)         #=> #<Enumerator: 1:step({:by=>2})>

\# extend

class Enumerator::ArithmeticSequence < Enumerator

 def first; end # already in Enumerator

 def last; end  # new

 def step; end  # new

 def inspect

 ...

 end

end

p 1.step                #=> (1.step)

p 1.step(10)            #=> (1.step(10))

p 1.step(10, by: 2) #=> (1.step(10, by:2))

p 1.step(by: 2)         #=> (1.step(by:2))

- do-else-end

- [https://twitter.com/joker1007/status/974173396006129664](https://www.google.com/url?q=https://twitter.com/joker1007/status/974173396006129664&sa=D&source=editors&ust=1686087561426418&usg=AOvVaw2q8_CQZXSdPalY8l51qnYe)
- [https://twitter.com/joker1007/status/974173641347756032](https://www.google.com/url?q=https://twitter.com/joker1007/status/974173641347756032&sa=D&source=editors&ust=1686087561426964&usg=AOvVaw3BcH-hjx7hhdeoEw8zk755)
- [https://twitter.com/joker1007/status/974176512554369027](https://www.google.com/url?q=https://twitter.com/joker1007/status/974176512554369027&sa=D&source=editors&ust=1686087561427354&usg=AOvVaw3V1Mly5xFZ0U-J2GAlK3zD)
- Will be SyntaxError in 2.6-preview2
- All of begin/do/def (experimental)

- \[Feature [#14594](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14594&sa=D&source=editors&ust=1686087561428206&usg=AOvVaw0_3PqMYBkujy7MvDqZ4PgH)\] Rethink yield\_self's name

- “then”?
- (1) Possible to use this method name by other libraries (like promise)
- (2) No built-in methods like this …
- Comitters objected the suggestion but matz accepted it

- \[Feature [#14324](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14324&sa=D&source=editors&ust=1686087561429118&usg=AOvVaw1c6q2vp2QY4pVrVrVu8RVg)\] Should Exception#full\_message include escape sequences?

- Keyword arguments

- order: :top/:bottom
- highlight: true/false

- \[Feature [#12745](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12745&sa=D&source=editors&ust=1686087561430363&usg=AOvVaw1tDFqKwKQnlGDs3x0HfNHu)\] String#(g)sub(!) should pass a MatchData to the block, not a String

- akr: how about gsubm, \`m\` means MatchData

### From non-attendees

- \[Feature [#14245](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14245&sa=D&source=editors&ust=1686087561432012&usg=AOvVaw0jvA6MnYelIK_DHGCz5QmG)\] Add File.read etc. (shugo)

- Accepted
- FYI: On 2.5, deprecation warning is added for this feature. We’ll remove this feature on 2.6.

- \[Feature [#14579](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14579&sa=D&source=editors&ust=1686087561432998&usg=AOvVaw0C65RhU4bMUl22QlfZ1Vjm)\] Hash value omission (shugo)

- Reject because matz doesn’t like it.

- \[Bug [#14541](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14541&sa=D&source=editors&ust=1686087561433611&usg=AOvVaw2Tb7I-ab-vMNeUgBB4NrHV)\] Class variables have broken semantics, let's fix them (eregon). I'd like more opinions and thoughts on whether we can change them.

- usa: I use class variables in a correct way when I love class variables
- ideas:

1. Prohibit inheritance; turn it into just a instance variable of a class; leave @@ just for compat

- Incompatibility
- confusion

2. Remove class variables

- huge incompatibility

3. Prohibit toplevel cvar assignment (because we already show warning without -w option)
4. Show stronger warning

Example of “toplevel” and “overtaken”  warning; note that “overtaken” is only shown with -w.

% ruby -w -e '
class A
 @@v = :A
 def self.show() p @@v end
end
class B
 @@v = :B
 def self.show() p @@v end
end
A.show
B.show
@@v = :TOP
A.show
B.show
'
:A
:B
\-e:12: warning: class variable access from toplevel
\-e:4: warning: class variable @@v of A is overtaken by Object
:TOP
\-e:8: warning: class variable @@v of B is overtaken by Object
:TOP

- Matz likes changing warnings to error

- Both “toplevel” and “overtaken” warning
- (Hopefully) We’ll release preview2 soon, it’s good timing to release this change early

- What exception class to raise?

- Use existing: ScriptError, SecurityError?
- Create new: ClassVariableError, BadSmellError, DeprecatedError, ObsoleteBehaviorError?

- What if we mistake, bit difficult to remove it later

- Matz approved NameError

- \[Bugs [#14380](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14380&sa=D&source=editors&ust=1686087561437379&usg=AOvVaw0pk0MwFPDwO3j1ab2-XQaL)\] Change Hash#transform\_keys! and break compatibility with Ruby 2.5 and ActiveSupport, or not?

- It’s considered bug. amatsuda says ActiveSupport will follow Ruby 2.6.

- \[Feature [#11473](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11473&sa=D&source=editors&ust=1686087561438106&usg=AOvVaw0y-nrmEQkQhCvcuDlyIR8Z)\] Immutable String literal in Ruby 3 (hsbt): Do you really want to change it at Ruby 3? If It's yes, We should add a warning with destructive action on Ruby 2.6.
