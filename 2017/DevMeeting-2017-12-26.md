---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2017-12-26

Date: 2017/12/26 (Tue)
Time: 14:00- 18:00 (JST)
Place: Speee Inc.
Sign-up: https://ruby.connpass.com/event/74486/
log edit: https://docs.google.com/document/d/1N9zFqYKPTFBu7VYxp32qT2vRqbLPLi2GnDDzgEmnxDA/edit
Attendees: (add your name here if you would like to participate) Kenta Murata, Yusuke Endoh
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

## From attendees

* Obsolete `$SAFE` from Ruby 2.6? (ko1) [Feature #5455]
* [PATCH] auto fiber schedule for rb_wait_for_single_fd and rb_waitpid [Feature #13618]
* Re considering to name `Guild`
* Merge MJIT infrastructure with conservative JIT compiler [Feature #14235]
* Deprecated Ruby 2 features for Ruby 3.
 * warn four special variables: [Bug #14240]
* Define English.rb aliases by default and eliminate the library [Feature #14138]
* [PATCH] resurrection of # -*- warn_past_scope: true -*- [Feature #14153]

## From non-attendees

## Carry-over from previous meeting(s)

## Open/assigned old issues

  * [Bug #14229] An exception in eval has strange message
  * [Bug #14230] Binding#source_location
  * [Bug #4352] [patch] Fix eval(s, b) backtrace; make eval(s, b) consistent with eval(s)

The tickets below are old tickets whose status is open or assigned (and mame can somewhat understand)

- matz
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
- anyone
  * [Feature #4017] [PATCH] CSV parsing speedup
  * [Bug #4157] test_pty で、たまに出る Failure
  * [Feature #4483] PStoreをデフォルトで複数のスレッドから扱えるようにしたい

# Log

Date: 2017/12/12 (Tue)
Time: 14:00- 18:00 (JST)
Place: Speee Inc.
Sign-up: https://ruby.connpass.com/event/73509/
log edit: https://docs.google.com/document/d/1XbUbch8_eTqh21FOwj9a_X-ZyJyCBjxkq8rWwfpf5BM/edit
log: TBD
Agenda
About 2.6 timeframe
From matz
About concurrency
Ko1: The name of “Thriber” is should be “Thread”. It shouldn’t include the essence of “Fiber”. Because it is automatically switching its contexts.
Mame: “Fiber”’ is manually context switching, not automatically.
Matz: Node’s coroutine is automatic context switching.

From attendees
Obsolete $SAFE from Ruby 2.6? (ko1) [Feature #5455]
$SAFE will be thread-local, instead of proc-local.
Guild
naming
MJIT




From non-attendees


Carry-over from previous meeting(s)


Open/assigned old issues
[Bug #14229] An exception in eval has strange message
Matz: This is bug, should be fixed.
[Bug #14230] Binding#source_location
Matz: Accept.
[Bug #4352] [patch] Fix eval(s, b) backtrace; make eval(s, b) consistent with eval(s)
Warn to guide to Binding#source_location.
The tickets below are old tickets whose status is open or assigned (and mame can somewhat understand)
matz
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
anyone
[Feature #4017] [PATCH] CSV parsing speedup
[Bug #4157] test_pty で、たまに出る Failure
[Feature #4483] PStoreをデフォルトで複数のスレッドから扱えるようにしたい


