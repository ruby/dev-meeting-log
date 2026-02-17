---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-02-20

Date: 2018/02/20 (Tue)
Time: 14:00- 18:00 (JST)
Place: Moneyforward, Inc.
Sign-up: https://ruby.connpass.com/event/78979/
Attendees: (add your name here if you would like to participate)
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

* [Feature #14478] String #uminus should de-dupe unconditionally (ko1)
* [Feature #14462] MJIT enabled should be displayed in the version string (k0kubun, eregon)
* [Feature #14491] MJIT needs internal debugging methods: MJIT.enable, MJIT.disable, MJIT.trace (k0kubun)
* [Feature #14468] Add Proc#dig (ko1)
* [Feature #8158] lightweight structure for loaded features index https://bugs.ruby-lang.org/issues/8158#change-70337 (ko1)
* [Feature #13618] the name of auto-fiber (ko1)
* [Feature #14385] Deprecate back-tick for Ruby 3 (hsbt)
* [Feature #12338] bypass Exception.new (nobu)
* [Bug #14324] Should Exception#full_message include escape sequences? (nobu)
* [Feature #14476] same_all?

## From non-attendees

* [Bug #14353] $SAFE should stay at least thread/fiber-local for compatibility (eregon). Currently it causes very confusing errors and breaks any existing usage of $SAFE in libraries, etc.
* [Bug #14400] IO#ungetc and IO#ungetbyte documentation is inconsistent with the behavior (eregon). Can someone knowing about this reply?
* [Bug #14062] Top-level return allows an argument (eregon). Should it forbidden? Better change sooner than later for reducing compatibility risks.
  Ko1 said "matz also said to avoid mistake, return expression with argument (example: "return foo") should be syntax error."
* [Feature #14332] Module.used_refinements to list refinement modules (eregon). Opinions?
* [Bug #14036] Signature of rb_uint2big and rb_int2big (eregon). Does anyone know why they take a `VALUE` and not (unsigned/signed) `long`?
* [Feature #9918] Exception#cause should be shown in output and #inspect (eregon). Anyone opposed to this? Currently it makes #cause almost useless. See http://engineering.appfolio.com/appfolio-engineering/2017/12/28/ruby-and-nested-exceptions as an example of what's needed currently to display it in Minitest.

## Carry-over from previous meeting(s)

- matz
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

## Date: 2018/02/20 (Tue)

## Time: 14:00- 18:00 (JST)

## Place: MoneyForward Inc.

## Sign-up: [https://ruby.connpass.com/event/73509/](https://www.google.com/url?q=https://ruby.connpass.com/event/73509/&sa=D&source=editors&ust=1686087534727438&usg=AOvVaw1jDaOHj58-KAWvuj258UUc)

## log edit: [https://docs.google.com/document/d/1XbUbch8\_eTqh21FOwj9a\_X-ZyJyCBjxkq8rWwfpf5BM/edit](https://www.google.com/url?q=https://docs.google.com/document/d/1XbUbch8_eTqh21FOwj9a_X-ZyJyCBjxkq8rWwfpf5BM/edit&sa=D&source=editors&ust=1686087534728028&usg=AOvVaw0OtexNETjIckmV8KeegrgY)

## log: TBD

# Agenda

Next Developper Meetings

2018/03/15, Cookpad

## About 2.6 timeframe

- Preview 1 will be released after MJIT is merged.

## About 2.5.1

- mame: 2.5.0’s pow is broken
- naruse: 2.5.1 will be released before April (maybe ealier)

## About 2.2 and 2.3

- Usa: expected do as before

## toolchain versioning

## Revisions r61785, r61786, r61787 were introduced by the request from @naruse. However [shyouhei](https://www.google.com/url?q=https://bugs.ruby-lang.org/users/10&sa=D&source=editors&ust=1686087534729949&usg=AOvVaw0zCOxnLHfD-SAJpu3U4FWv) thinks the request was somewhat vague.

- Do we have to support such old toolchains? (shyouhei)

- Things we discussed:


- Let’s make configure show the version of BASERUBY

- That way we can detect failures on the CI matrix.


## From attendees

## \[Feature [#14223](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14223&sa=D&source=editors&ust=1686087534730902&usg=AOvVaw0hKDkPhy2smcNbHV7h3t95)\] Enable #to\_proc by Refinements at &hoge (nobu)


- Matz: LGTM.

## \[Feature [#14371](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14371&sa=D&source=editors&ust=1686087534731456&usg=AOvVaw0138Z7WbBbP8sVNjKJtboU)\] New option "recursive: true" for Hash#transform\_keys! (nobu)


- Mrkn: This is deep\_stringify\_keys!
- Shyouhei: Understand the needs.
- Mame: Should this be called recursive: true ?
- Mrkn: This method has some assumption on the receiver’s structure. E.g. JSON
- Knu: This method changes values as well as keys, which seems wrong.
- Matz: This particular API seems NG to me.

## \[Bug #14380\] Expected transform\_keys! to work just as transform\_keys, but it doesn't (mame)


- Shyouhei: This behaviour was inherited from ActiveSupport.
- Mrkn: we can fix it by preserving entries that conflict.
- Matz: I have strong opinion on this.  Isn’t the current behaviour acceptable in sake of space efficiency?

## \[Bug [#14374](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14374&sa=D&source=editors&ust=1686087534732782&usg=AOvVaw23Evsh07gKVvWVGJqwzzPf)\] for does not splat elements (nobu)


- Ko1: is it me?
- Nobu: because it’s since 1.9
- Matz: please fix.

## \[Feature [#14313](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14313&sa=D&source=editors&ust=1686087534733360&usg=AOvVaw01wZt0etef0uPW3YdiOET2)\] Support creating KeyError with receiver and key from Ruby (mrkn/kou)


- Mrkn: I was asked to bring this.
- Matz: Understand the needs.
- Shyouhei: Should it be keyword arguments?

- Maintainers of csv (mrkn/kou)


- Mame: JEG2.
- Mrkn: But he doesn’t have the repo access bit, nor gem release right.
- Mame: He’s active on twitter etc.  You should ask his current status.

## \[Feature [#4831](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4831&sa=D&source=editors&ust=1686087534734216&usg=AOvVaw000s7NUFCbXTK8QZm0leib)\] Integer#prime\_factors (mrkn)


- Mrkn: name?
- Shyouhei: yugui is on the ticket.

## \[Feature [#14235](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14235&sa=D&source=editors&ust=1686087534734701&usg=AOvVaw3sHZaMuYBRg9KZEliD_l-L)\] Merge MJIT infrastructure with conservative JIT compiler (k0kubun)

## \[Feature [#14386](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14386&sa=D&source=editors&ust=1686087534735005&usg=AOvVaw0rq2r3IXro64FgAE0y6TSU)\] Add option to let Kernel.#system raise error instead of returning false (k0kubun)


- Nobu: is this request to raise error when spawn fails, or when the spawned process fails?
- Mrkn: I don’t like the RuntimeError.
- K0kubun: RuntimeError is from Rake.
- Mrkn: I see similarity for discussion on Integer(). \[ruby-core:77171\] \[Feature#12732\]
- Akr: There are \`exception: true\`

## \[Bug [#14353](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14353&sa=D&source=editors&ust=1686087534735695&usg=AOvVaw3VLRR_Wp944LrfkBaowubO)\] $SAFE should stay at least thread-local for compatibility (ko1)


- Matz: This feature is something to extinct in future.
- Naruse: Is there actual use case other than tests?

- \[Bug #4443\] odd evaluation order in a multiple assignment (mame)

- Matz: I would like to fix it if possible, but no idea how.

- \[Feature #4475\] default variable name for parameter (mame)

- Matz:rejected

- \[Feature #4830\] Provide Default Variables for Array#each and other iterators (mame)

- Matz:ditto.

- \[Feature #4513\] allow whitespace following EOL continuation backslash (mame)

- Usa: nobody uses ancient editor without detection of such spaces.
- Shyouhei: the example is about IRB and that’s a different story than the core.
- Matz: it would be a kind doing to warn backslash-space.

## From non-attendees

## \[Feature [#14382](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14382&sa=D&source=editors&ust=1686087534736923&usg=AOvVaw1k6X3Y-H2yxkyqjbUiHTjO)\] Make public access of a private constant call const\_missing (jeremyevans0)


- Nobu: sounds like a bug to me
- Matz: I’m positive, but not sure if it’s 100% safe.
- Matz: Let’s try.
- Nobu: I’d like to review the patch.

## \[Feature [#14385](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/14385&sa=D&source=editors&ust=1686087534737578&usg=AOvVaw1e4UbeKafR4WFVvojciJ0F)\] Deprecate back-tick for Ruby 3 (hsbt)


- Matz: I see several objections are there.
- Matz: I have a rough idea to use backtick as a namespace separator.
- Shyouhei: should we do something for 2.6?
- Naruse: as a release management, 2.6 is good timing to warn because it has good benefit for paying compatibility cost.
- Mame: warn this?
- Akr: warning, if any, should not be annoying this case because it should / would happen on parsing.
- Mame: should we also deprecate def \`; end; self.\` ? If so, warning on parsing is dangerous.
- Matz: I think we don’t necessarily warn this as soon as 2.6.

## \[Feature [#13969](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/13969&sa=D&source=editors&ust=1686087534738407&usg=AOvVaw08WBvi8j7alCbpQ1j1kSuU)\] Dir#each\_child (znz)


- Matz: OK.
