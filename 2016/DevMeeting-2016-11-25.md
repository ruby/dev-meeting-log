---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2016-11-25

# DevelopersMeeting20161125Japan

Date: 2016/11/25 (Fri)
Time: 14:00- 19:00 (JST)
Place: Salesforce.com Tokyo office
Sign-up: http://ruby.connpass.com/event/43506/

Attendees/参加者: http://ruby.connpass.com/event/43506/
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

## I want to clear my assigned tickets (shyouhei)
* Can who close outdated issues? https://bugs.ruby-lang.org/issues?set_filter=1&f%5B%5D=status_id&op%5Bstatus_id%5D=o&f%5B%5D=assigned_to_id&op%5Bassigned_to_id%5D=%3D&v%5Bassigned_to_id%5D%5B%5D=10

## About 2.4 timeframe

* https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering24

## Carry-over from previous meeting(s)

* [Feature #12695] File.expand_path should resolve ~/ using /etc/passwd when HOME is not set (shyouhei) conclusion?
* [Feature #4897] Define Math::TAU and BigMath.TAU. The "true" circle constant, Tau=2*Pi. See http://tauday.com/ (shyouhei) wating for matz (or time?)
* [Feature #12721] public_module_function (shyouhei)
* [Feature #12732] An option to pass to `Integer`, `Float`, to return `nil` instead of raise an exception (shyouhei) conclusion?
* [Feature #12745] String#(g)sub(!) should pass a MatchData to the block, not a String (shyouhei) conclusion?
* [Feature #12753] Useful operator to check bit-flag is true or false (shyouhei)
* [Feature #12754] Want to use prepared buffer with `Array#pack` (shyouhei)
* [Feature #12752] Unpacking a value from a binary requires additional '.first' (shyouhei)
* [Feature #12746] class Array: alias .prepend to .unshift ? (shyouhei)
* [Feature #11815] Proposal for method `Array#difference` (shyouhei)
* [Feature #12770] Hash#left_merge (shyouhei)
* [Feature #12760] Optional block argument for `itself` (shyouhei)
* [Feature #12775] Random subset of array (shyouhei)
* [Bug #10290] segfault when calling a lambda recursively after rescuing SystemStackError (shyouhei)
* [Bug #12688] Thread unsafety in autoload (shyouhei)
* [Feature #12786] String#casecmp? (shyouhei)
* [Feature #12612] Switch Range#=== to use cover? instead of include? (shyouhei)
* [Bug #9244] unexpected behaviour of 'require' when $LOAD_PATH gets changed (shyouhei)
* [Feature #12719] `Struct#merge` for partial updates (shyouhei)
* [Feature #12715] Allow ruby hackers to omit having to specify class or module mandatory, if they know exactly what they want to do (shyouhei)
* [Feature #12698] Method to delete a substring by regex match (shyouhei)
* [Feature #12697] Why shouldn't Module meta programming methods be public? (shyouhei)
* [Feature #8960] Add Exception#backtrace_locations (shyouhei) another problem is issued or...?
* [Feature #10208] Passing block to Enumerable#to_h (knu) a better name?

## From attendees

* [Bug #11929] No programatic way to check ability to dup/clone an object (duerst)
* [Feature #12979] Avoid exception for #dup on Integer (and similar cases) (duerst)
* [Feature #2074] json の j や jj は module_function にするべき？
* [Feature #9209][Feature #11925] Struct instances creatable with named args (nobu)
* [Feature #12063] KeyError#receiver and KeyError#name (nobu)
* [Feature #12802] Add BLAKE2 support to Digest (nobu)
* [Feature #12861] Lexically scoped super (nobu)
* [Bug #12952] Incompatibility of a method signature between `Float#round` and `BigDecimal#round` (yui-knk)
* [Feature #12953]&nbsp;(Float, Integer, Rational)#round(half: :down) (shyouhei)
* [Bug #12666] Fatal error: glibc detected an invalid stdio handle (shyouhei)
* [Bug #12945] Use-after-free in vm_trace.c (shyouhei)
* [Bug #12961] Bad value for range using infinity for Date or Time (shyouhei)
* [Bug #12831] /\X/ (extended grapheme cluster) can't pass unicode.org's GraphemeBreakTest (shyouhei)
* [Bug #12809] passing a proc to Kernel#lambda does not create a lambda (shyouhei)
* [Bug #12812] Added Coverage#result= (shyuohei)
* [Feature #12813] Calling chunk_while, slice_after, slice_before, slice_when with no block (shyouhei) cf #2172
* [Feature #12180] switch id_table.c variant (shyouhei) ko1?
* [Feature #8158] lightweight structure for loaded features index (shyouhei)
* [Feature #12839] CSV - Give not nil but empty strings for empty fields (shyouhei) assign who?
* [Feature #12854] Proc#curry should return an instance of the class, not Proc (shyouhei)
* [Bug #12855] Inconsistent keys identity in compare_by_identity Hash when using literals (shyouhei) bug or...?
* [Feature #12871] Using the algorithm like math.fsum of Python for Array#sum (shyouhei) mrkn?
* [Feature #12882] Add caller/file/line information to internal Kernel#warn calls (shyouhei)
* [Feature #7360] Adding Pathname#glob (shyouhei)
* [Feature #11286] [PATCH] Add case equality arity to Enumerable's sequence predicates. (shyouhei)
* [Feature #5446] at_fork callback API (shyouhei)
* [Feature #12780] BigDecimal#round returns different types depending on argument (shyouhei)
* [Feature #11428] system/exec/etc. should to_s their argument to restore Pathname functionality as it was in 1.8 (shyouhei)
* [Bug #12907] rb_respond_to() return value is incorrect (shyouhei)
* [Feature #12180] switch id_table.c variant (shyouhei)
* [Bug #12551] Exception accessing file with long path on windows (shyouhei) ping?
* [Feature #12901] Anonymous functions without scope lookup overhead (shyouhei)
* [Feature #12906] do/end blocks work with ensure/rescue/else (shyouhei)
* [Bug #12914] FTPTest#test_list_read_timeout_exceeded causes EPROTOTYPE on Mac OS X 10.10 (shyouhei) assign who?
* [Bug #12919] Net::FTP does not have a default open_timeout (shyouhei) assign who?
* [Feature #12913] A way to configure the default maximum width of pp (shyouhei)
* [Feature #12902] How about Enumerable#sum uses initial value rather than 0 as default? (shyouhei)
* [Feature #12926] -l flag for line end processing should use chomp! instead of chop! (shyouhei)
* [Feature #12912] An endless range `(1..)` (shyouhei)
* [Feature #12933] Add Some and Optional (shyouhei)
* [Feature #12931] Add support for Binding#instance_eval (shyouhei)
* [Feature #12929] ternary should look ahead w/in a block (and not care about newlines) (shyouhei)
* [Feature #12898] String#match? method in addition to Regexp#match? (shyouhei)
* [Feature #12944] Change Kernel#warn to call Warning.warn (shyouhei)
* [Misc #12935] Webrick: Update HTTP Status codes, share them (shyouhei)
* [Feature #12962] Feature Proposal: Extend 'protected' to support module friendship (shyouhei)
* [Feature #12957] A more OO way to create lambda Procs (shyouhei)
* [Feature #12963] ?string longer than one char (shyouhei)
* [Feature #12965] net/ftp module to support optional path parameter for status (shyouhei) who's going to handle this?
* [Feature #12966] net/ftp to include fxp support? (shyouhei) who's going to handle this?
* [Feature #12967] Add a default for RUBY_GC_HEAP_GROWTH_MAX_SLOTS out-of-the-box (shyouhei)
* [Feature #12969] Allow optional parameter in String#strip and related (shyouhei)
* [Feature #12968] Allow default value via block for Integer(), Float() and Rational() (shyouhei)
* [Feature #12978] Symbol after keyword (nobu)

## From non-attendees

Write your name and your interest (what do you want to ask and to whom?) please.

* example: [Feature #10917] Add `GC.stat[:total_time]` when GC profiling enabled (ko1)
* [Bug #12958] Breaking change in how `#round` works (yui-knk listed on this agenda)
  Is there any migration path for changing behavior of `round`? For example only warning on Ruby 2.4 and chnage behavior on Ruby 2.5.

# Log

Date: 2016/10/11 (Tue)

Time: 14:00- 18:00 (JST)

Place: SFDC Japan, Tokyo

Attendees/参加者: Matz, ko1, naruse, nakada, duerst (Martin Dürst), shyouhei, mrkn

Language: mostly Japanese (sorry for non native Japanese speakers)

next time

12/21 at Money Forward

Release engineering

RC1? at 12/1

Issues

- \[Bug [#12958](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12958&sa=D&source=editors&ust=1686086982357143&usg=AOvVaw0NDpSqKnj-yfX0tPogEifH)\] Breaking change in how #round works (yui-knk listed on this agenda) Is there any migration path for changing behavior of round? For example only warning on Ruby 2.4 and chnage behavior on Ruby 2.5.

- mrkn: what problem are there?
- yui-knk(behind shyouhei): want to know how ruby-core thinks abot the incompatibility.
- shyouhei: it seems the rails breakage is about ActionView
- naruse: it’s better for rails to have a dedicated method to round a float.

- \[Feature [#12963](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12963&sa=D&source=editors&ust=1686086982358050&usg=AOvVaw3cwEmrnvvy00w3HPtYY8l6)\] ?string longer than one char

- matz: I feel not confident with : :
- shyouhei: “::” vs “: :” are different in meaning.
- ko1: I’m against introducing this to 2.4.  That’s too hurry.
- matz: what is really wanted is symbol
- duerst: It’s usual to have a space between words
- duerst: I like e.g. “foo: :bar”, it looks like too faces smiling at each other
- duest: I don’t think ? has any connection with symols or strings, it feels unnatural for this purpose
- naruse: what about $foo?

- matz: or \`foo (like lisp)

- continue discussing.
- mrkn: x.round(half::"up") -> x.round(half: :"up")
- nobu: x.round(half:: "up") -> NG

- \[Feature [#12953](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12953&sa=D&source=editors&ust=1686086982359288&usg=AOvVaw3V9ygz-hsVvt_swmL2zw5y)\] (Float, Integer, Rational)#round(half: :down)

- mrkn: I think it’s OK
- matz: seems nobody is against it.

- \[Feature [#12063](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12063&sa=D&source=editors&ust=1686086982359763&usg=AOvVaw1yJrkhp1xX6JkDo-FW0cUT)\] KeyError#receiver and KeyError#name

- shyouhei: I’m not sure about the patch but the feature itself seems OK
- nobu: I see no problem on the patch.
- ko1: it’s not a child of ArgumentError.
- matz: Ruby’s exceptions classes are not well considered… Anyway Python used KeyError.

- \[Bug [#11929](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11929&sa=D&source=editors&ust=1686086982360357&usg=AOvVaw09ORi3MJDKhyp-t7eNo4Is)\] No programatic way to check ability to dup/clone an object (duerst)

- matz: respond\_to? is insufficient to check if it can be dup’ed, because it could raise exceptions.
- matz: There is no way to change identity of a fixnum.
- naruse: I’m not sure what OP wants to do.
- duerst: I’d prefer failing silently.
- matz: I want a separate proposal for that.

- \[Bug [#12831](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12831&sa=D&source=editors&ust=1686086982361004&usg=AOvVaw3wxnsNV7JXzrEAU2jh6bKs)\] /\\X/ (extended grapheme cluster) can't pass unicode.org's GraphemeBreakTest

- duerst: not currently implemented.
- naruse: I’ll take a look.

- \[Feature [#12695](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12695&sa=D&source=editors&ust=1686086982361455&usg=AOvVaw333VB1MV_A4CvVMy91FLww)\] File.expand\_path should resolve ~/ using /etc/passwd when HOME is not set

- matz: OK

- \[Feature [#4897](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4897&sa=D&source=editors&ust=1686086982361810&usg=AOvVaw3Wf38x3K9dIZPW0PyAj2aD)\] Define Math::TAU and BigMath.TAU. The "true" circle constant, Tau=2\*Pi. See [http://tauday.com/](https://www.google.com/url?q=http://tauday.com/&sa=D&source=editors&ust=1686086982362018&usg=AOvVaw07n4iBP3yKDVQR7gDx-cLf)

- mrkn: last time I suggested TWO\_PI.
- matz: I don’t think TWO\_PI satisfies TAU fans.
- matz: I want to leave it rejected.

- \[Feature [#12721](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12721&sa=D&source=editors&ust=1686086982362503&usg=AOvVaw1odhJ7Cmx7SbV7S8HsHV4W)\] public\_module\_function

- nurse: わかる
- matz: I’m not sure what benefits are there.

- \[Feature [#12732](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12732&sa=D&source=editors&ust=1686086982362909&usg=AOvVaw1mh-_HNvXSsZG5HaAIkDg5)\] An option to pass to Integer, Float, to return nil instead of raise an exception

- also: \[Feature [#12968](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12968&sa=D&source=editors&ust=1686086982363374&usg=AOvVaw3h8J-mCeo6Ex66gNqQTde5)\] Allow default value via block for Integer(), Float() and Rational()

- naruse: what’s wrong with rescue?
- shyouhei: raising exception then rescueing is too big compared to what’s needed here; we only need to convert a string to an integer.

- \[Feature [#12745](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12745&sa=D&source=editors&ust=1686086982363808&usg=AOvVaw3jAV6P5jU9u5OWGashJ7YI)\] String#(g)sub(!) should pass a MatchData to the block, not a String

- matz: 1 is NG. I prefer 4 -> 2 -> 3
- ko1: I fear there should be considerable amount of overheads in 3.
- naruse: scan should also be handled. 4 and 2 are  not applicablte.

- \[Feature [#12753](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12753&sa=D&source=editors&ust=1686086982364280&usg=AOvVaw0ZgRlBJB9A0DQLR22QUtIG)\] Useful operator to check bit-flag is true or false

- shyouhei: what is wanted is to check if certain bit is set or not.  &==0 is a bit too operational.
- matz: name? “and?” seems wrong.
- naruse: what about “bittest?”

- \[Feature [#12979](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12979&sa=D&source=editors&ust=1686086982364759&usg=AOvVaw339X5hZEVqiBmZJatKc-Xf)\] Avoid exception for #dup on Integer (and similar cases)

- matz: OK.

- \[Feature #12871\] Using the algorithm like math.fsum of Python for Array#sum

- assign mrkn

- \[Feature [#12754](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12754&sa=D&source=editors&ust=1686086982365227&usg=AOvVaw3KjK8rg1wT0zr4ilOUaz3M)\] Want to use prepared buffer with Array#pack

- matz: sounds reasonable.

- \[Feature [#12752](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12752&sa=D&source=editors&ust=1686086982365649&usg=AOvVaw0ruGcRHSrgB5Zy4CKMKKpr)\] Unpacking a value from a binary requires additional '.first'

- matz: I would prefer keyword arguments but index:0 doesn’t much differ from .first
- naruse: write”(‘C’)\[0\]” and let the interpreter optimize.
- ko1: possibilities are:

- new method
- extra argument
- .first

- \[Feature [#12746](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12746&sa=D&source=editors&ust=1686086982366330&usg=AOvVaw33nDawYeIELvxVxVyMscpu)\] class Array: alias .prepend to .unshift ?

- mrkn: is “prepend” a valid english word?
- shyouhei: they say it’s more natural
- matz: OK

- \[Feature [#11815](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11815&sa=D&source=editors&ust=1686086982366807&usg=AOvVaw3B4a-E_npjc6Xa0QVGdKiE)\] Proposal for method Array#difference

- shyouhei: real-world example is shown (poker bot)

- \[Feature [#12978](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12978&sa=D&source=editors&ust=1686086982367200&usg=AOvVaw2PQtd3fWx38Z2qvD28OvpL)\] Symbol after keyword

- matz: reject. I can’t but read this being a String, not Symbol.

- \[Feature [#12770](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12770&sa=D&source=editors&ust=1686086982367570&usg=AOvVaw2LXlj5eXh9xPsU6SDmPoI5)\] Hash#left\_merge

- akira: Rails has reverse\_merge.
- naruse: the proposed functionality is not the identical to reverse\_merge.
- ko1: chechk if hash entry is nil, is something new (not orignal behaviour of merge)

- \[Feature [#12760](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12760&sa=D&source=editors&ust=1686086982368038&usg=AOvVaw17Kpd7_0cotPmd5XdqZywX)\] Optional block argument for itself

- matz: object.{|x|...} is NG.

- \[Feature [#12775](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12775&sa=D&source=editors&ust=1686086982368441&usg=AOvVaw14Fvb6nvjcltuy9qWxJ22m)\] Random subset of array

- mrkn: random length array does not make sense to me.
- shyouhei: it seems the OP uses this for test.

- \[Bug [#10290](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10290&sa=D&source=editors&ust=1686086982368872&usg=AOvVaw2imE3M1XBhmWGzOwUgi2mi)\] segfault when calling a lambda recursively after rescuing SystemStackError

- nudge nobu

- \[Bug [#12688](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12688&sa=D&source=editors&ust=1686086982369228&usg=AOvVaw2NVTNMkIgJk1UGSg-BXRbq)\] Thread unsafety in autoload

- assign ko1

- \[Feature [#12786](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12786&sa=D&source=editors&ust=1686086982369570&usg=AOvVaw32s4bddE8q0B53POLPDuTa)\] String#casecmp?

- naruse: There could be cases where downcase strings are equal, while upcase strings arent.
- duerst: This feature might be implemented similarily like //i, or upcase(downcase()) == upcase(downcase()); string.downcase(:fold) is (almost?) completely identical to string.upcase.downcase
- akira: recently rubocop suggests casecmp so people find this method.
- matz: is “casecmp?” OK?
- shyouhei: to me yes.

- \[Feature [#12612](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12612&sa=D&source=editors&ust=1686086982370168&usg=AOvVaw0wuKzX3vReQbShIonFL-le)\] Switch Range#=== to use cover? instead of include?

- naruse: instead of cover?, we should define dedicated Range#===, with optimization for integer (&co.) cases. It can keep compatibility.
- ko1: maybe akr is interested in it.
- shyouhei: introducing incompatibility only because it is inefficient is kind of weak.

- \[Bug [#9244](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9244&sa=D&source=editors&ust=1686086982370660&usg=AOvVaw2rVxUdqnDG76EduDEHeGJH)\] unexpected behaviour of 'require' when $LOAD\_PATH gets changed

- matz: I don’t immediately think it’s a bug.

- \[Feature [#12719](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12719&sa=D&source=editors&ust=1686086982371037&usg=AOvVaw1EJfRZWAhZoGksKipFYnjn)\] Struct#merge for partial updates

- matz: “merge” sounds wrong.
- akira: “assign”?
- shyouhei: what about the feature itself, apart from the naming thing?
- matz: use case not clear to me.

- \[Feature [#12715](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12715&sa=D&source=editors&ust=1686086982371642&usg=AOvVaw1gC8BUWQE1yLs-vVN31XED)\] Allow ruby hackers to omit having to specify class or module mandatory, if they know exactly what they want to do

- matz wants feedback from OP.

- \[Feature [#12698](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12698&sa=D&source=editors&ust=1686086982372057&usg=AOvVaw1aCJ-80xOQKNbBSMks9OUy)\] Method to delete a substring by regex match

- duerst: what about allowing String#delete to accept regexps? (this would be instead of gremove)
- mrkn: the OP does not tell us what is wrong with gsub.

- \[Feature [#12697](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12697&sa=D&source=editors&ust=1686086982372498&usg=AOvVaw13frj5syIrtTMiq0OQbafi)\] Why shouldn't Module meta programming methods be public?

- \[Feature [#12780](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12780&sa=D&source=editors&ust=1686086982372792&usg=AOvVaw2sYLrGMHy3xse0fAsBcU3R)\] BigDecimal#round returns different types depending on argument

- \[Feature [#12813](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12813&sa=D&source=editors&ust=1686086982373088&usg=AOvVaw1kM8Xujo60PJPyfkxA0q52)\] Calling chunk\_while, slice\_after, slice\_before, slice\_when with no block

- matz will think about it.

- \[Feature [#12882](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/12882&sa=D&source=editors&ust=1686086982373487&usg=AOvVaw2q87y_tcLG47HqB4YgZNPX)\] Add caller/file/line information to internal Kernel#warn calls
