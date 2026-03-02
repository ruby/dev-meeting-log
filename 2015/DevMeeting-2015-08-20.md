---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-08-20

Date: 2015/08/20 (Tue)
Time: 14:00- 19:00 (JST)
Place: Tokyo, Japan
Remote participation: Slack? Google hangout? Ask ko1.
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
* did you mean gem (nishijima)
* Progress report about compilation (ko1)

## From non-attendees

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendee: akr, ayumin, hsbt, ko1, matz, naruse, nobu, kosaki, usa, yuki, amatsuda

# did_you_mean gem (Yuki Nishijima)

- Naming: did_you_mean or correctable?

- did_you_mean is acceptable, but it’s determined by what feature set is bundled.

- Bundled gem or stdlib? It's going to be reuiqred in prelude (Yuki still doesn't know what it is)

- Bundled gem
- It’ll be required when a Ruby process spins up
- Rescue LoadError and just ignore when it doesn’t exist

- Ability to skip a correction attempt when raised within a certain method
- Completely disable the feature for apps running in production (env flag? method that does it?)

- Why want to disable in production? -> Because it makes rails slow.
- Why slow? -> Rails parses the message of NameError exception for rails’ autoload feature.

- `time ruby --disable-gem -e1`: 0.061s
- `time ruby -e1`: 0.083s
- `time ruby -rdid_you_mean -e1`: 0.109s
- (ruby 2.2.2p95 (2015-04-13 revision 50295) [x86_64-darwin14])
- Like --disable-gem, we can extend --disable-did_you_mean

- \-> We should change Rails, and we should offer a new API to take the qualified name of the Class/Module.
- Current Rails parses error message like “NameError: undefined local variable or method `foo' for main:Object”

- Use NameError#name
- NameError#name doesn’t include parent modeule names which is included in the error message. We need a new API including this.
- How about adding a new method that returns the binding?

- More succinct message.  Delete two empty line and indent.

```
% ruby -rdid_you_mean -e '
class C
 def bar
 end
end
C.new.baz
'
-e:6:in `<main>': undefined method `baz' for #<C:0x007f790863e880> (NoMethodError)

   Did you mean? #bar

zsh: exit 1     /home/akr/alias/ruby -rdid_you_mean -e

% ocaml
       OCaml version 4.01.0

# let bar = 1;;
val bar : int = 1
# baz;;
Error: Unbound value baz
Did you mean bar?
# let bas = 2;;
val bas : int = 2
# baz;;
Error: Unbound value baz
Did you mean bas or bar?
# let bat = 3;;
val bat : int = 3
# baz;;
Error: Unbound value baz
Did you mean bas, bat or bar?
```

#

empty line is not needed because lines of display is essential resource. indent is ok.



- How to disable this?

- Use `--disable=’ commandline option.
- Need to implement API in ruby level to take what features are disabled.

- global method? global variable? constant?

- NameError for constant reference

- module M
     A::B::C
    end
- failed to refrence A -> needs binding and the name :A.
- failed to refernce B -> needs A and the name :B.
- failed to refrence C -> needs B and the name :C.

# Release Schedule

[https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23)

# Subversion Repository Migration

current machine is old debian.

Migration is required before next Spring.

Options:

- Migrate to new Subversion server (on AWS)

- Good: no changes.

- Migrate to Git server ( on AWS)

- related migrations are required.

- GitHub

- Term of service; GitHub Issues are not portable

- Partial migration (use git for HEAD and continue to use SVN for maintaining branches)

Problem: If we migrate to git, we need to migrate SVN related tools (auto date update, etc)

# Magic Comment for Frozen String Literal by default

Default of the status of String Literal will be frozen from Ruby 3.0.

- it has been rejected before 2.2 by matz
- it has been rejected again early 2.3 development by matz
- amatsuda: tired of seeing pull requests adding “..”.freeze here and there. This is ugly.
- amatsuda: please reconsider akr’s magic comment solution [[https://bugs.ruby-lang.org/issues/8976](https://bugs.ruby-lang.org/issues/8976)]
- matz: accepted. As a migration path, magic comment is reasonable. naming is still problem.

- command line option to frozen string literal by default

- It is acceptable but can’t be a migration path

- Ruby 3.0 will interpret string literal frozen by default.

- akr’s challenge. [https://github.com/akr/ruby/tree/frozen-string](https://github.com/akr/ruby/tree/frozen-string)
- String.new(“...”) is preferred over “...”.dup.
- magic comment candidates

- freeze_string: true [[https://bugs.ruby-lang.org/issues/8976](https://bugs.ruby-lang.org/issues/8976)]
- freeze_string_literal: true
- frozen_string_literal: true [prefered by matz]
- freezing_string_literal: true
- mutable_string_literal: false
- immutable: string [[https://github.com/ruby/ruby/pull/487](https://github.com/ruby/ruby/pull/487)]

- command line option candidates

- \--enable-frozen-string-literal

- command line option is weaker than magic comment

Frozen Regexp Literal

It doesn’t need a migration path.

Next Meeting (18, September)

Next Meeting (21, October)
