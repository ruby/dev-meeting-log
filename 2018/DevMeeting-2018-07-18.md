---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2018-07-18

Date: 2018/07/18 (Thu)
Time: 14:00-18:00 (JST)
Place: MoneyForward HQ (beware: new location) (Tokyo, Japan)
Sign-up: https://ruby.connpass.com/event/92314/

Please comment your favorite ticket numbers you want to ask to discuss with your *SHORT* comment or summary.
(your summary/comment will help us because we don't need to read all of ticket comments)

*DO NOT* discuss then on this ticket, please.

Past meetings: <https://bugs.ruby-lang.org/projects/ruby/wiki#Developer-Meetings>

# NOTE

Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
We will write a log about discussion to a file or to each ticket in English.
All activities are best-effort (keep in mind that most of us are volunteer developers).
The date, time and place is scheduled according to when/where we can reserve Matz's time.

# Agenda

## Next dev-meeting

## About 2.6 timeframe

## Carry-over from previous meeting(s)

* [Feature #14784] One-sided Comparable#clamp (with endless/startless ranges) (zverok)
    - more reasonable version of Object#enumerate proposed for the previous meeting.
* [Feature #14859] Timeout in VM (normalperson)
    - Still needs some work, mainly wondering if the idea of moving this part of stdlib into core VM is acceptable or not. No semantic changes except speed improvement.

## From Attendees

(will be edited later)
(if you have a write access permission, please list directly)

- [Bug #14887] Array#delete_if does not use #delete (shyouhei)
  - Is it by design, or a bug?
- [Feature #13050] Readline: expose rl_completion_quote_character variable (nobu)
- [Feature #14850] Add official API for setting timezone on Time (nobu)
- [Feature #14869] Proposal to add Hash#=== (nobu)
- [Feature #14877] Calculate age in Date class (nobu)
- [Feature #14473] Add Range#subrange? (tarui)
- [Bug #14908] Enumerator::Lazy creates unnecessary Array objects. (nobu)
- [Feature #14912] Introduce pattern matching syntax (mame)
    - pattern matching
- opt_to_s (nobu)
    - see below for his patch
- [Feature #13626] Add String#byteslice! (aycabta)
- [Feature #14618] Add display width method to String for CLI (aycabta)
- [Misc #14917] Add RDoc documents to tar ball (aycabta)
- [Feature #14918] Use Reline for fallback of ext/readline (aycabta)
- [Feature #14919] Add String#byteinsert (aycabta)
- [Feature #14916] Proposal to add Array#=== (aycabta)

## From non-attendees

(will be edited later)
(if you have a write access, please list directly)

- [Feature #14111] ArgumentErrorが発生した時メソッドのプロトタイプをメッセージに含む (esjee)
    - Suggestion: include method parameters when generating an ArgumentsError message. Nobu's patch in the issue provides functionality to make writing a ruby gem to provide this functionality possible.
- [Bug #14878] Add command line argument to deactivate JIT (k0kubun)
    - Please discuss the necessity of the flag and its name in the proposal.
- [Feature #12306] Implement String #blank? #present? and improve #strip and family to handle unicode (sam.saffron)
- [Feature #14913] Extend case to match several values at once (zverok)
    - some steps towards better pattern matching
- [Feature #14914] Add BasicObject#instance_exec_with_block (jeremyevans0)
- [Feature #14915] Deprecate String#crypt, move implementation to string/crypt (jeremyevans0)

# Log

optDate: 2018/07/18 (Thu)

Time: 14:00-18:00 (JST)

Place: MoneyForward HQ (Tokyo, Japan)

## Next dev-meeting

- 8/9

- @ pixiv

- 9/13

- @ Cookpad

## About 2.6 timeframe

- Naruse: nothing to do right now.
- Mame: feature freeze?
- Naruse: expected in Oct. maybe.

## Carry-over from previous meeting(s)

- \[Feature [#14784](https://bugs.ruby-lang.org/issues/14784)\] One-sided Comparable#clamp (with endless/startless ranges) (zverok)

- more reasonable version of Object#enumerate proposed for the previous meeting.
- isn’t it enough with accepting nil ? (e.g. .clamp(from, nil) )

- \[Feature [#14859](https://bugs.ruby-lang.org/issues/14859)\] Timeout in VM (normalperson)

- Still needs some work, mainly wondering if the idea of moving this part of stdlib into core VM is acceptable or not. No semantic changes except speed improvement.
- Ko1 will check the patch and will comment in Aug.

## From Attendees

- \[Feature [#14473](https://bugs.ruby-lang.org/issues/14473)\] Add Range#subrange? (tarui)

- subrange?
-  is ambiguous
- < and <= is a little confusable

- Set already uses them, but we will not use them so frequently

- include? is already used as it’s an element or not
- cover? is acceptable, let it go (matz)

- \[Feature [#14912](https://bugs.ruby-lang.org/issues/14912)\] Introduce pattern matching syntax (mame)

- pattern matching
- Matz: positive for the proposal itself, though we need to discuss many details
- Matz: Pin var\_ should be changed
- Matz: one-liner is slightly negative
- Matz: guard must be able to write in pattern
- Akr: & pattern is needed: \[self\] + values can be avoided by a pattern: \[A, 1, 1\] => A & \[1, 1\]
- ko1:  want to write \[\] in pattern instead of ()
- Mame: NoMatchingError is needed?
- Usa: is it a good idea to call deconstruct implicitly? is it better to write deconstruct explicitly?
- Mame: the result of deconstruct must be cached
- Tarui: cant distinguish between Array and Struct

case \[1, 2, 3\]
in a, 2 => b, c
 p a, b, cl
end

case \[1, 2, 3\]
in \[a, 2 => b, c\]
 p a, b, c
end

case Struct(2, 3)
in a, 2=> b, c
 p a, c #=> Struct, 3
end

obj = Just(foo);
Case obj
In Just(foo)
In Nothing
End

Case obj
In Maybe(nil)
In Maybe(foo)
End

- opt\_to\_s (nobu)

- Naruse: Is it fast?
- Nobu: not measured yet.
- Naruse: do that first.

## From non-attendees

- \[Feature [#14111](https://bugs.ruby-lang.org/issues/14111)\] ArgumentErrorが発生した時メソッドのプロトタイプをメッセージに含む (esjee)

- Suggestion: include method parameters when generating an ArgumentError message. Nobu's patch in the issue provides functionality to make writing a ruby gem to provide this functionality possible.
- \`method\_name\` is true method name, not \`callee\`.
- current implementation by nobu returns callee, so, change the implement to return true name.  after then, discuss about the necessity of callee.

- \[Bug [#14878](https://bugs.ruby-lang.org/issues/14878)\] Add command line argument to deactivate JIT (k0kubun)

- Please discuss the necessity of the flag and its name in the proposal.
- Use \--disable-jit and \--disable=jit, because we can already use \--disable=gems and \--disable-gems

- \[Feature [#12306](https://bugs.ruby-lang.org/issues/12306)\] Implement String #blank? #present? and improve #strip and family to handle unicode (sam.saffron)

- blank? Is not the best name, but acceptable (space\_only? or something might be better)
- present? Is not acceptable at all
- matz will accept the feature itself of blank?, but hope another good name
- TBW

- \[Feature [#14913](https://bugs.ruby-lang.org/issues/14913)\] Extend case to match several values at once (zverok)

- some steps towards better pattern matching

- \[Feature [#14914](https://bugs.ruby-lang.org/issues/14914)\] Add BasicObject#instance\_exec\_with\_block (jeremyevans0)

- Real use case, please

- \[Feature [#14915](https://bugs.ruby-lang.org/issues/14915)\] Deprecate String#crypt, move implementation to string/crypt (jeremyevans0)

- To remove String#crypt, we first need to remove the dependency to String#crypt of WEBrick.  We first listen Eric Wong (WEBrick maintainer)’s opinion.
- If Eric agrees with the removal in future, we then ask Jeremy to release compatibility-layer gem by 2.6.  If the release is succeeded, we can deprecate the core method.
- And finally, we will remove the core method in future, after Eric removed the dependency to String#crypt from WEBrick in any way.

---

Carry over:

- \[Bug [#14887](https://bugs.ruby-lang.org/issues/14887)\] Array#delete\_if does not use #delete (shyouhei)

- Is it by design, or a bug?

- \[Feature [#13050](https://bugs.ruby-lang.org/issues/13050)\] Readline: expose rl\_completion\_quote\_character variable (nobu)
- \[Feature [#14850](https://bugs.ruby-lang.org/issues/14850)\] Add official API for setting timezone on Time (nobu)
- \[Feature [#14869](https://bugs.ruby-lang.org/issues/14869)\] Proposal to add Hash#=== (nobu)
- \[Feature [#14877](https://bugs.ruby-lang.org/issues/14877)\] Calculate age in Date class (nobu)
- \[Bug [#14908](https://bugs.ruby-lang.org/issues/14908)\] Enumerator::Lazy creates unnecessary Array objects. (nobu)
