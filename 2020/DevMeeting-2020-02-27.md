---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2020-02-27

# The next dev meeting

**Date: 2020/02/27 13:00-17:00**
Place/Sign-up/Agenda/Log: https://docs.google.com/document/d/1IqCMl27w7KYeCpke6-dCfPV3TLfP2ObnDDw5TNOelsc

- Dev meeting *IS NOT* a decision-making place. All decisions should be done at the bug tracker.
- Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly.
- Matz is a very busy person. Take this opportunity to ask him. If you can not attend, other attendees can ask instead of you (if attendees can understand your issue).
- We will write a log about the discussion to a file or to each ticket in English.
- All activities are best-effort (keep in mind that most of us are volunteer developers).
- The date, time and place are scheduled according to when/where we can reserve Matz's time.
- *DO NOT* discuss then on this ticket, please.

# Call for agenda items

If you have a ticket that you want matz and committers to discuss, please post it into this ticket in the following format:

```
* [Ticket ref] Ticket title (your name)
  * Comment (A summary of the ticket, why you put this ticket here, what point should be discussed, etc.)
```

Example:

```
* [Feature #14609] `Kernel#p` without args shows the receiver (ko1)
  * I feel this feature is very useful and some people say :+1: so let discuss this feature.
```

- Comment deadline: 2020/02/20 (one week before the meeting)
- The format is strict.  We'll use [this script to automatically create an markdown-style agenda](https://gist.github.com/mame/b0390509ce1491b43610b9ebb665eb86).  We may ignore a comment that does not follow the format.
- Your comment is mandatory.  We cannot read all discussion of the ticket in a limited time.

# Log

https://bugs.ruby-lang.org/issues/16561
Venue
2/27 (Thu) 13:00-17:00 @ Cookpad (12F) online (due to COVID-19)
NOTE: Cookpad’s meeting room is not available
Attendees
Add your name (or ask a committer to invite you)
matz (remote)
mame
Nobu
ko1
sotuaro
Absent:


Next Date
3/16(Mon) 13:00-17:00 @ online
Announce
Ruby 2.7 timeframe
Release 2.7.1 in March.
About 2.8/3.0 timeframe
Check security tickets
[secret]
[Misc #16483] How about stopping new *.tar.bz2 releases? (znz)
Stop new bz2 releases, keep already existing bz2 releases.
Preliminary discussion:
no opinion.
Discussion:
Too many formats. Keeping all format forever will make the maintenance a bit harder. Let’s drop less-needed formats.
gz can be handled by almost all platforms (including old one)
Those who want to use small tarballs should use the smallest format at the release time (currently, xz)
This discussion applies to only Un*x systems; zip should be kept for Windows
Conclusion:
Two tarballs look enough: gz and the latest format (Currently, xz)
Only two formats (gz and xz) will be used in 2.8.0 and later
zip should be kept, of course
naruse will reply
[Feature #9573] descendants of a module don’t gain its future ancestors, but descendants of a class, do (jeremyevans0)
A patch exists for Module#include that passes make check, is it OK to merge?
A patch exists for Module#prepend that still has many issues to resolve and requires creating origin classes for almost all modules, do we want to try to support this feature for Module#prepend?
Preliminary discussion:
nobu: The patch looks fine for “Module#include”, though I have not reviewed it yet
mame: I’m afraid about the compatibility
nobu: Even if we fix the issue, we will another contradiction (say, cyclic “include” relation)
ko1: There are some known issues about Module#include/prepend. Fixing only this issue can introduce further confusion.
Discussion:
module Mod1
end

module Mod2
end

class Class1
end

Class1.include Mod1
Mod1.include Mod2
p Class1.ancestors #=> [Class1, Mod1, Object, Kernel, BasicObject]
# No Mod2!

ko1: matz wants that Module#include does not allow duplicated inclusion, but that Module#prepend allows duplication
matz: Right. To be honest, I want both to allow, but if Module#include’d modules are allowed, super call is broken, ko1 said
ko1: The issue is already fixed, so now we can allow duplication of Module#include
akr: It will break diamond inheritance: a method may be called twice (for each include) in super() call chain


module Mod1
end

module Mod2
end

class Class1
end

Class1.include Mod1
Class1.include Mod2
Mod1.include Mod2

# current
p Class1.ancestors #=> [Class1, Mod2, Mod1, Object, Kernel, BasicObject]

# jeremy's patch
p Class1.ancestors #=> [Class1, Mod2, Mod1, Mod2, Object, Kernel, BasicObject]

# current Ruby allows duplicate M in class hierarchy

module M; end
class A; end

class B < A
  include M
end

class A
  include M
end

p B.ancestors
#=> [B, M, A, M, Object, Kernel, BasicObject]

matz: Duplication of module inclusion has been already theoretically possible (by using inheritance), so Jeremy’s patch is acceptable
akr: The problem looks to me that it is not well-defined for module-inclusion at non-initialization phase
akr: What’s use cases of module-inclusion at non-initialization-phase?
ko1: “inclusion for Enumerable” is an example.
mame: I have not understood the problem of prepend
Conclusion:
matz: Give it a try about Module#include
matz: I’m unsure about Module#prepend. Postponed.
[Feature #13675] Should Zlib::GzipReader#ungetc accept nil? (jeremyevans0)
Has matz decided whether to fix IO#ungetc? If so, is the patch OK?
Preliminary discussion:
mame: I’m afraid about the compatibility, but matz will decide it
Discussion:
matz: Early failure is a good habit.
Conclusion:
matz: Let’s apply the patch.
[Feature #11304] [PATCH] Kernel.global_variables should observe $~. (jeremyevans0)
Do we want to modify the behavior of Kernel.global_variables for $1, $2, etc.
Do we want defined? for $& $' $+ $1 $2 to depend on whether $~ is set?
Should defined? for global variable aliases always be the same as what they alias?
Preliminary discussion:
“Do we want to modify the behavior of Kernel.global_variables for $1, $2, etc.” -> already done (since 2016?)
“Do we want defined? for $& $' $+ $1 $2 to depend on whether $~ is set?” -> the current behavior
“Should defined? for global variable aliases always be the same as what they alias?” -> still inconsistent, but should we fix it or not?
alias $MATCH $&
p defined?($&) # nil
p defined?($MATCH) # "global-variable"

Discussion:
Conclusion:
matz: The current behavior looks reasonable. No change is needed, I think.
[Feature #16557] Deduplicate Regexp literals (byroot)
[#16377] made literal regexps frozen, so they can be deduplicated without further backward compatibility change
The estimated memory saving is about 1.4 MB on Redmine (and pretty much all Rails apps).
Preliminary discussion:
mame: Looks fine, if CI failure is fixed
ko1: The patch creates a regexp object, and then find already-existing instance. It is slightly suboptimal
ko1: Regexp is immutable in onigmo? If so, the optimization can be applied not only to literals but also to all regexp instances.
Discussion:
ko1: Is onigmo regex data structure immutable? If so, we can dedup all Regexp objects under lower layer (not only literals but also any regexp)
naruse: Yep. The key should be source string, encoding, and regexp options.
ko1: A simple reference count mechanism would be good enough
ko1: Regexp objects can be ignored
ko1: But there is no resource to develop… At least I cannot make effort for that
ko1: And his patch has some problems (mame: I failed to log)
akr: How about drop-in replacement of regcomp/regexec/regfree? (regcomp lookup a cache and count up the reference count. regfree count down the reference count.)
Conclusion:
ko1: I will propose the refernce count mechanism and tell the situation (I have no time to review and maintain the patch)
[Feature #15722] Kernel#case? (sawa)
"foo".case?(Symbol, String) # => true
Preliminary discussion:
bar # => "bar"
bar.case?("foo", "bar", "baz") # => true
bar.case?("qux") # => false
bar.case?(Symbol, String) # => true
bar.case?(Array) # => false
bar.case? # => false

shyouhei: Want to see actual use cases
nobu: no-argument case should raise an exception
mame: I like the feature, though its name is very arguable
akr: code search of ‘\]\.include\?’ finds many example
Discussion:
matz: I understand the idea and it is a bit helpful, but don’t understand how useful it is
mame: I like the word order like in?
akr: It does not allocate an array
naruse: If we want the reverse order version of an idiom [a, b, c].include?(x), it should not use ===
Conclusion:
matz: The name case? is not good. I want to see real use case that case? is much better than an idiom case when end
[Feature #16476] Socket.getaddrinfo cannot be interrupted by Timeout.timeout (kirs)
Would we be happy with the proposed solution leveraging getaddrinfo_a? If yes, is it good to merge?
Preliminary discussion:
mame: I’m a bit afriad about a race condition, but I’m not familiar. Expert should review the patch
Discussion:
akr: Glass_saga should review the patch
akr: getaddrinfo_a is not portable. When it is not available, it cannot be interruptable.
naruse: It is theoretically possible to implement getaddrinfo_a by our hand (by using pthread and pthread_cancel)
naruse: https://github.com/lattera/glibc/blob/master/resolv/getaddrinfo_a.c
Conclusion:
akr: Glass_saga should review the patch
The mechanism can be improved (without getaddrinfo_a)
[Feature #16463] Fixing *args-delegation in Ruby 2.7: ruby2_keywords semantics by default in 2.7.1 (eregon)
It would be good to decide.
We can also discuss it at the RubyKaigi dev meeting, but is that not too late for 2.7.1?
Preliminary discussion:
mame: We need to follow matz
Discussion:
akr: This proposal makes incompatible between 2.6 and 2.7(.1). When ruby2_keywords is not used, it should be compatible with 2.6. But if ruby2_keyword is enabled by default, user cannot control the compatibility.
matz: It has too big impact.
Conclusion:
matz: Reject. Will reply.
[Feature #16511] Staged warnings and better compatibility for keyword arguments in 2.7.1 (Dan0042)
All the benefits of separation of keyword/positional arguments.
Less disruptive migration in the near term; only the strict necessary breaks next year, fewer gems to upgrade.
Fewer incompatibilities, inconsistencies, and side effects. (specifics in note 12)
This is not theoretical, I’ve implemented it: https://git.io/JvWXA (last 4 commits; other commits with “refactor” are not strictly relevant)
The implementation touches a lot of code; it’s well-tested, but maybe too much, too late.
For some reason it seems no one understands my proposal? I can volunteer to answer questions at the dev meeting… if desired.
Discussion:
ko1: The target of this proposal is 2.7.1, so it would be good to reply anything soon
naruse: It is too late to change it in 2.7 series
Conclusion:
matz: Pending. I’ll read it and reply it (maybe after RubyKaigi)
[Feature #16378] Support leading arguments together with …
matz: Leading argument support is required for method_missing
nobu,mame: How about post arguments, keyword arguments, block arguments?
matz: I focus on method_missing
def method_missing(name, ...)
  send("on_#{ name }", ...)
end

def foo(a, b, c, ...)
  bar(a+b+c, ...)
end

shyouhei: Does it accept no argument?
matz: I think so
[Feature #16655] Each test on test-all should run srand(seed) at setup
Calling srand(0) changes the random sequence
Calling srand(0) in a test may affect other following tests
How about calling srand(seed) before all test cases.
       if seed = options[:seed]
          srand(seed)
        else
          seed = options[:seed] = srand % 100_000
          srand(seed)
          orig_args << "--seed=#{seed}"
        end

[Misc #16630] Deprecate pub/ruby/*snapshot* and use pub/ruby/snapshot/* instead (znz)
Go ahead
[Feature #16644] qualified const init (self::CONST1 = 1) should be allowed in methods
matz: The current behavior is reasonable. Syntactic assignment and meta-programming version are different.
nobu: For example, local_variable_get(:if)
[Feature #16456] Ruby 2.7 argument delegation (…) should be its own kind of parameter in Method#parameters
matz: currently, I see no need
nobu: It is related to the syntax of argument forwarding
mame: Leading argument support has been introduced. In future we may support post/keyword/block arguments. After that, what should Method#parameters return? It would be good to wait for a while
Conclusion:
pending
[Bug #12052] String#encode with xml option returns wrong result
"<\0>\0".encode("utf-16le", "utf-16le", xml: :text)
#=> "\u6C26\u3B74\u2600\u7467;"

"<\0>\0".encode("utf-32le", "utf-16le", xml: :text)
#=> "&lt;&gt;"

[Bug #12251] DelegateClass(OpenStruct) behavior in 2.3.0 different from 2.2
mame: Looks too late to change?
matz: Sound good to revert
mame: Will reply to
[Bug #12136] OpenStruct.new(format: :bar).send :format calls Kernel#format
(no public method -> there is private method -> called)
It works well with public_send
(no public method -> method_missing -> the target method is defined correctly)
**Equals [Bug #12251] **
[Feature #12055] NET::HTTPResponse is not deflating responses with custom Content-Range header
Let’s check if Content-Range unit is “bytes” or not (The patch seems nil error by nil[:unit])
naruse: pending
[Bug #12235] URI.encode issue with square brackets
Should [ be escaped by URL.encode? The WG says that they wait for browser community decisions
naruse: Will check the response from whatwg



