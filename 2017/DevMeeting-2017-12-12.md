---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-12-12

Date: 2017/12/12 (Tue)
Time: 14:00- 18:00 (JST)
Place: Speee Inc.
Sign-up: https://ruby.connpass.com/event/73509/
log edit: https://docs.google.com/document/d/1XbUbch8_eTqh21FOwj9a_X-ZyJyCBjxkq8rWwfpf5BM/edit
Attendees: (add your name here if you would like to participate) Martin Dürst
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

## About 2.5 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25

## Carry-over from previous meeting(s)


## From attendees

The tickets below are old tickets whose status is open or assigned (and mame can somewhat understand)

- matz
  * [Feature #11256] anonymous block forwarding
  * [Feature #4288] Allow invoking arbitrary method names with foo."something" syntax
  * [Bug #4352] [patch] Fix eval(s, b) backtrace; make eval(s, b) consistent with eval(s)
  * [Bug #4443] odd evaluation order in a multiple assignment
  * [Feature #4475] default variable name for parameter
  * [Feature #4830] Provide Default Variables for Array#each and other iterators
  * [Feature #4513] allow whitespace following EOL continuation backslash
  * [Feature #4514] #deep_clone and #deep_dup for Objects
  * [Feature #4521] NoMethodError#message may take very long to execute
  * [Feature #4539] Array#zip_with
  * [Feature #5044] #zip with block return mapped results
  * [Feature #4541] Inconsistent Array.slice()
  * [Feature #4592] Tempfileを直接保存したい
  * [Feature #4780] String#split with a block
  * [Feature #4818] Add method marshalable?
  * [Feature #4824] Provide method Kernel#executed?
  * [Feature #4907] enumerable#permutation and combination
  * [Feature #4910] Classes as factories
  * [Feature #4921] Remove intern.h
  * [Feature #4938] Add Random.bytes [patch]
  * [Feature #5007] Proc#call_under: Unifying instance_eval and instance_exec
  * [Feature #5064] HTTP user-agent class
  * [Feature #5120] String#split needs to be logical
  * [Feature #5123] Alias Hash 1.9 as OrderedHash
  * [Feature #5129] Create a core class "FileArray" and make "ARGF" its instance
  * [Feature #5133] Array#unzip as an alias of Array#transpose
  * [Bug #5179] Complex#rationalize and to_r with approximate zeros
  * [Feature #5206] ruby -K should warn
  * Ruby3.0 depretation warnings
       - %<whatever>?
       - backtick (`)?
       - -K
       - etc.
  * Ruby3 Concurrent model (AutoFiber? Guild?)
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
- mrkn
  * [Feature #4968] BigDecimal#sqrt は BigMath.sqrt へ移動すべき
- hsbt
  * [Bug #5060] Executables in bin folder conflict with their gem versions.
- sorah
  * [Feature #14141] Add a method to Exception for retrieving formatted exception for logging purpose (Exception#{formatted,display})
- anyone
  * [Feature #4017] [PATCH] CSV parsing speedup
  * [Bug #4157] test_pty で、たまに出る Failure
  * [Feature #4483] PStoreをデフォルトで複数のスレッドから扱えるようにしたい

## From non-attendees

* [Feature #14143] Thread.report_on_exception should be true by default. (eregon): Can we have the opinion of matz on this?
* [Feature #11816] Partial safe navigation operator (marcandre)
* [Feature #14015] Enumerable & Hash yielding arity (marcandre)

# Log

Date: 2017/12/12 (Tue)
Time: 14:00- 18:00 (JST)
Place: Speee Inc.
Sign-up: https://ruby.connpass.com/event/73509/
log edit: https://docs.google.com/document/d/1XbUbch8_eTqh21FOwj9a_X-ZyJyCBjxkq8rWwfpf5BM/edit
log: TBD
Agenda
Meeting plans
12/26 (Tue) at Speee, register at Connpass
1/24 (Wed) at Speee, register at Connpass

About 2.5 timeframe
https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering25
RC1 expected soon (tomorrow?)


Carry-over from previous meeting(s)
From attendees
The tickets below are old tickets whose status is open or assigned (and mame can somewhat understand)
matz
[Feature #11256] anonymous block forwarding
Matz: it’s a good property to avoid naming variables.  Also I personaly like it.  However prompting such name-less programming might let people write cryptic codes.
Matz: hmm… Made up my mind. Accepted.
[Feature #4288] Allow invoking arbitrary method names with foo."something" syntax
Matz: use __send__.
[Bug #4352] [patch] Fix eval(s, b) backtrace; make eval(s, b) consistent with eval(s)
Matz: let’s try this in 2.6.
[Bug #4443] odd evaluation order in a multiple assignment
[Feature #4475] default variable name for parameter
[Feature #4830] Provide Default Variables for Array#each and other iterators
[Feature #4513] allow whitespace following EOL continuation backslash
[Feature #4514] #deep_clone and #deep_dup for Objects
[Feature #4521] NoMethodError#message may take very long to execute
[Feature #4539] Array#zip_with
[Feature #5044] #zip with block return mapped results
[Feature #4541] Inconsistent Array.slice()
[Feature #4592] Tempfileを直接保存したい
[Feature #4780] String#split with a block
[Feature #4818] Add method marshalable?
[Feature #4824] Provide method Kernel#executed?
[Feature #4907] enumerable#permutation and combination
[Feature #4910] Classes as factories
[Feature #4921] Remove intern.h
[Feature #4938] Add Random.bytes [patch]
[Feature #5007] Proc#call_under: Unifying instance_eval and instance_exec
[Feature #5064] HTTP user-agent class
[Feature #5120] String#split needs to be logical
[Feature #5123] Alias Hash 1.9 as OrderedHash
[Feature #5129] Create a core class "FileArray" and make "ARGF" its instance
[Feature #5133] Array#unzip as an alias of Array#transpose
[Bug #5179] Complex#rationalize and to_r with approximate zeros
[Feature #5206] ruby -K should warn
[Feature #14043] Introduce Process.last_status as an alias for $?
Matz: OK.
Ruby3.0 depretation warnings
%?  Already error
backtick (`)?
Matz: I’d like to let people override backticks in 3
Naruse: migration will be add %x before `...`
Sorah: People tend to wrongly use backtick. This is a side topic, but I’d like to remove both backtick and %x if we sunset current `
Or, disable interpolation for %x?
Yui-knk: what to do for backtick heredocs? I.e. <<`End` is replaced with self.send(:`, <<”End”) which is clumsy
-K
Sorah: ko1?
Naruse: why we have -K is ko1 complained for deletion before.
Ko1: I no longer use it.
Etc.
Hsbt: $-something?
Akr: we no longer need for instance $/, etc.
$/	input record separator (default argument for “gets”)
$\	output record separator (“print” prints it at last)
$,	default separator for Array#join and print
$;	default separator for String#split
Akira: what about all other global variables?
Matz: global variables are not going to fade out.
Akira: class variables?
Ruby3 Concurrent model (AutoFiber? Guild?)
Matz: would like to hear about this topic from ko1.  Maybe next meeting?
nobu
[Feature #2324] Dir instance methods for relative path
[Bug #3618] inf-ruby prompt and tab completion
[Feature #4924] mkmf have_header fails with C++ headers
ko1
[Feature #2447] reduce GC pressure by symbol table without String instance
[Feature #3187] Allow dynamic Fiber stack size
[Feature #3731] Easier Embedding API for Ruby
[Bug #4040] SystemStackError with Hash[*a] for Large a
naruse
[Feature #2567] Net::HTTP does not handle encoding correctly
[Feature #2631] Allow IO#reopen to take a block
[Bug #4173] TestProcess#test_wait_and_sigchild が、たまに失敗する
akr
[Feature #3608] Enhancing Pathname#each_child to be lazy
[Feature #3848] Using http basic authentication for FTP with Open URI
[Feature #4560] [PATCH] lib/net/protocol.rb: avoid exceptions in rbuf_fill
knu
[Feature #3953] TCPSocket / UDPSocket do not accept IPAddr objects.
mame
[Feature #4247] New features for Array#sample, Array#choice
suke
[Bug #4405] WIN32OLE & Threads incompatible
kosaki
[Feature #4464] [PATCH] add Fcntl::Flock object for easier use of POSIX file locks
yugui
[Feature #4831] Integer#prime_factors
mrkn
[Feature #4968] BigDecimal#sqrt は BigMath.sqrt へ移動すべき
hsbt
[Bug #5060] Executables in bin folder conflict with their gem versions.
sorah
[Feature #14141] Add a method to Exception for retrieving formatted exception for logging purpose (Exception#{formatted,display})
Nobu: direction?
Sorah: depends on global tty
Akr: eh…
Mame: what about implementing #display first, then think about the name of method returning a String?
Akr: I propose #long_message
Akira: or #full_message
Sorah: *_message sounds they don’t interact with TTYs.
Matz: accept #full_message
anyone
[Feature #4017] [PATCH] CSV parsing speedup
[Bug #4157] test_pty で、たまに出る Failure
[Feature #4483] PStoreをデフォルトで複数のスレッドから扱えるようにしたい
From non-attendees
[Feature #14143] Thread.report_on_exception should be true by default. (eregon): Can we have the opinion of matz on this?
Matz: Understand the needs but not for 2.5.  For future versions, compatibility concerns should be considered.
Ko1: I think we should make this default on ideally. We need a good transition path. No idea though.
Akr: it sounds finding a transition path is not a practical story.
Naruse: I’ll comment that it’s too late for 2.5.
Matz: OK, let’s do it in RC1. To see what happens.
[Feature #11816] Partial safe navigation operator (marcandre)
Matz: I’ll respond.
[Feature #14015] Enumerable & Hash yielding arity (marcandre)
nobu: it’s due to we didn’t have rb_yield_values before.
Nobu: Changing this can break existing codes.  Maybe add warning?
Matz: warning might be an option.
Akr: this example uses lambda, which should be strict about parameter passing.  I think exceptions are what it should behave.
Matz: I understand there has inconsistency and error prone. Let me find the reason for status quo, as well as a solution.
Ko1: is it OK we won’t fix this in 2.5?
Matz: OK.
https://bugs.ruby-lang.org/issues/12882 (jeremyevans)
Matz: OK, accepted.
Shyouhei: I’ll merge.
Mame: isn’t jeremy a committer?
Hsbt: no.
Matz: but it might be a good time for him to be.

https://bugs.ruby-lang.org/issues/11925  (k0kubun)
K0kubun: made a patch
Matz: understand the needs. Name?
Shyouhei: is keyword_args: OK?
Sorah: no other methods have this keyword
Akira: the concept named keyword arguments are not exposed into Ruby’s world.
Mame: if we split kwargs and argv, we don’t even need this method?
Conclusion:
No create! feature
keyword_init: true
https://bugs.ruby-lang.org/issues/14162 (k0kubun)
Nobu: this is a bug. Will fix.
https://bugs.ruby-lang.org/issues/12275 (shyouhei)
Shyouhei: tadd wanted you guys to review the patch.
https://bugs.ruby-lang.org/issues/14142 (akira)
Matz: seems okay to me.
Knu: this feature has a minor compatibility issue
Nobu: I don’t think that cause any trouble.
https://bugs.ruby-lang.org/issues/14052 (akr)
Akr: naming?
Nobu: what about use Array instead?
Shyouhei: sample
Akr: that’s NG because what is generated is different.
Matz: with_chars sounds no good.

