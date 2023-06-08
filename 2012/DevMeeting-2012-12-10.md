---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2012-12-10

This meeting was held on 2012-12-10 at 15:00 Pacific Time at irc://chat.freenode.net/#ruby-implementers.

## Attendees

### MRI

* Eric Hodel - drbrain (moderator)
* shugo
* tenderlove (Aaron Patterson)
* wycats (Yehuda Katz)
* ko1
* matz
* n0kada (nobu)
* sora_h (sorah)
* unak (usa)
* nurse (naruse)
* emboss (Martin Boßlet)
* marcandre (Marc-André Lafortune)
* eregon (Benoit Daloze)
* akr_
* eban
* knu
* nari
* wycats
* tarui (tal)

### Rubinius

* brixen (Brian Ford)
* dbussink (Dirkjan Bussink)
* evan (Evan Phoenix)

### JRuby

* headius (Charles Nutter)
* enebo (Tom Enebo)

### MagLev

* phlebas (Tim Felgentreff)

### MacRuby

* lrz (Laurent)
* ferrous26 (Mark Rada)
* jballanc (Joshua Ballanco)

## Agenda

We'll keep this meeting to one hour long.

* Introduction
* How to improve current process (tenderlove)

### Not covered

* Refinements
  * Experimental feature?
  * Other issues after scope reduction?
* Ruby 2.0 clarifications
* Open floor
* This Meeting
  * Should we do it again?
  * When?
  * How often?
  * How long?

## Proposal of "Ruby Language Team"

### Details

* There are many implementations of Ruby
* Only one Ruby language
* We should form a group to define "Ruby" called Ruby Language Team
* The team consists of representatives from different implementation teams
* There will be a new way to propose features
  * Features require Champions
  * Champions belong to the Language Team
  * Champions refine proposals until the Language Team reaches consensus
  * When consensus is reached, proposal is approved
  * Approved proposals are added to RubySpec
  * After addition to RubySpec, the change is now considered "Ruby"
* Implementations are allowed to have experimental features
* Brixen will ensure smooth entry of feature in to RubySpec
* Specs will be in a central repository

### Concerns

* The person proposing the feature should write documentation to
* Brixen shouldn't be single point of failure
* If an implementation can't implement something, is it not "ruby"?
* Is 100% concensus really necessary?
* What about stdlib?
* Is it OK that we communicate in English?

### Results

* Rigid process is not great
* Having an executable spec that can be gradually translated to RubySpec is good
* "And I want to make sure as long as I live on this mortal
  <pre>state, you need my approval before adding something as official
  Ruby" -- matz</pre>
* We should try having more meetings among implementers
* Maybe we should maintain a flag to identify official "Ruby" features?
* Matz wants to remain dictator, but still have participation from implemeters.

## IRC Log

<pre>[15:00:27] @ drbrain set topic "Introduction"
[15:00:32] <drbrain> It's meeting time
[15:00:34] <lrz> headius: hi!
[15:00:43] <headius> lrz: hello! ltns!
[15:00:46] <drbrain> HELLO EVERYONE!
[15:00:48] <wycats> MEETINGZ!
[15:00:52] <dbussink> GOGOGO
[15:01:13] <jballanc> MERHABA! (that one's for lrz)
[15:01:14] <matz_> good morning (on my time)
[15:01:19] <evan> matz_: good morning!
[15:01:24] <jballanc> matz_: morning!
[15:01:33] <dbussink> matz_: good night :)
[15:01:35] <lrz> hi matz_ evan :)
[15:01:43] <drbrain> I think we have most everyone here
[15:01:43] <headius> I will just tell everyone "hello" now and cut down the
           greeting noise :)
[15:01:51] <enebo> heh
[15:01:56] <drbrain> if you don't have voice in this channel, please message
           tenderlove or me
[15:01:58] <marcandre> +1
[15:01:58] <enebo> I will just heh at people
[15:02:05] <drbrain> we'll be following these guidelines:
           http://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingIRCGuidelines
[15:02:17] <wycats> heh
[15:02:38] <drbrain> we'd like to keep the meeting to an hour
[15:02:45] <evan> ok
[15:02:48] <drbrain> tenderlove will be introducing topics
[15:03:04] <drbrain> then we'll give everyone who wishes to speak 2 minutes
           to talk before moving to the next person
[15:03:17] <drbrain> ebisawa will be provide translation for Japanese speakers
[15:03:31] <ebisawa> hi
[15:03:39] <drbrain> of course, I'll give extra time to finish what you're
           saying
[15:04:01] <drbrain> if you don't understand something in English please ask
           ebisawa in #ruby-core
[15:04:08] <drbrain> ebisawa will only be translating on-demand
[15:04:13] <drbrain> so please ask her if you do not understand
[15:04:26] <drbrain> or if you don't know how to say something in English
[15:04:45] <shugomaeda> is she ebi-chan?
[15:04:54] <wycats> ebisawa no?
[15:04:55] <tenderlove> shugomaeda: yes, it is :-)
[15:04:57] <drbrain> if you wish to speak during someone else's turn please
           say "!"
[15:05:08] <shugomaeda> oh, I see
[15:05:13] <drbrain> I will be enforcing time limits with voice permisions
[15:05:22] <drbrain> but I will give you warning
[15:05:31] <lrz> sounds very well organized :)
[15:05:35] <tenderlove> lrz: lol
[15:05:36] <drbrain> tenderlove: would you like to start?
[15:05:39] <tenderlove> Sure
[15:05:46] <tenderlove> Thanks for coming everyone.
[15:05:52] <tenderlove> I'm excited to have all of you here
[15:06:16] <tenderlove> If you check out the list of attendees on the wiki:
[15:06:17] <tenderlove>
           http://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20121210
[15:06:38] <tenderlove> You'll see we have participants from every Ruby
           implementation that I can think of
[15:07:13] <tenderlove> So thanks for coming.
[15:07:31] <tenderlove> The first think I want to talk about is improving our
           current communication process
[15:07:42] <tenderlove> we have many Ruby implementations

[15:07:54] @ drbrain set topic "Improving the Ruby design process"

[15:08:09] <tenderlove> but the important thing is that we're all
           implementing the same language, Ruby
[15:08:30] <tenderlove> the Ruby language is our common ground
[15:08:48] <tenderlove> we implement the same language, so I think that we
           should be on the same team
[15:09:05] <tenderlove> working for the same goals
[15:09:10] <tenderlove> but doing it together
[15:09:36] <tenderlove> For example, when someone says "What is Ruby?", it's
           something that *we* need to answer
[15:09:41] <tenderlove> not just MRI team, but all of us
[15:09:58] <tenderlove> so I'd like to propose an experiment
[15:10:48] <tenderlove> I'd like to propose a Ruby Language Team
[15:11:16] <tenderlove> an experiment to give implementers equal voice, and a
           vehicle for driving communication among implementers
[15:11:54] <tenderlove> Of course this is a different style of development
           than what we do today
[15:12:45] <tenderlove> MRI is just an implementation
[15:12:58] <tenderlove> one that has a strong voice in driving forward the
           Ruby specification
[15:13:25] <tenderlove> but like other implementations, it should be able to
           experiment with new features without defining "Ruby"
[15:13:46] <tenderlove> we need a way to describe the difference between MRI
           and "Ruby"
[15:14:53] <tenderlove> a Language Team allows MRI to separate experimental
           features from features that matz_ indends to propose as new
           features to "Ruby"
[15:15:05] <tenderlove> the same method can be used for any implementor to
           propose new features
[15:15:19] <tenderlove> for example, shugomaeda worked on refinements
[15:15:28] <tenderlove> and Rubinius is interested in standardizing channels
           and actors
[15:15:52] <tenderlove> we need a place for everyone to discuss the future of
           "Ruby" abstracted from the details of the experimental
           implementations that people are working on
[15:16:33] <tenderlove> this is where the Ruby Language Team comes in
[15:16:44] <tenderlove> we can use a framework for defining new features
[15:17:09] <tenderlove> Someone proposes a feature to the mailing list
[15:17:19] <tenderlove> (this could be anyone)
[15:18:05] <tenderlove> The person who wants the change must find someone on
           the Ruby Language Team to champion this feature
[15:19:11] <tenderlove> The champion, is someone who is interested in the
           feature enough to put in the work on designing the feature
[15:19:29] <tenderlove> it's that person's responsibility to work with the
           rest of the team to define the feature
[15:19:40] <tenderlove> for trivial features, this means one person, for
           non-trivial, maybe several people
[15:19:49] <tenderlove> but in no way is it "design by committee"
[15:20:05] <tenderlove> once the champion can refine the feature such that
           the group reaches consensus
[15:21:09] <tenderlove> sorry, when the feature is proposed, we keep it in a
           wiki
[15:21:31] <tenderlove> the important thing about this is that at any point,
           an implementer can look at the proposal for it's current status
[15:21:40] <tenderlove> the champions refine the feature based on feedback
           from the group and bring it back on the agenda for another meeting
[15:21:54] <tenderlove> eventually the team reaches consenus
[15:22:05] <tenderlove> and it's proposed for a future version of Ruby
[15:22:16] <tenderlove> at this point, brixen will turn the feature into
           well-define RubySpecs
[15:22:25] <tenderlove> the spec will be brought back to the Team to make
           sure it matches what everyone agreed to
[15:22:29] <brixen> !
[15:22:37] <tenderlove> once it is in the specification, implementors should
           implement
[15:22:41] <tenderlove> *all implementors
[15:23:04] <tenderlove> if people do not want to implement, they should
           object earlier
[15:23:17] <tenderlove> the process of getting consensus is the process of
           determining that all implementors are comfortable with the feature
[15:23:19] <tenderlove> done
[15:23:22] <tenderlove> sorry I took so long
[15:23:22] <drbrain> brixen: your turn
[15:23:23] <tenderlove> :(
[15:23:30] <brixen> hello
[15:23:36] <brixen> thanks all for attending
[15:23:52] <brixen> I just wanted a quick clarification on "brixxen will turn
           feature into RubySpecs"
[15:24:00] <brixen> er brixen rather
[15:24:19] <brixen> in my rubyconf proposal, I asked for the person proposing
           the feature to write documentation
[15:24:22] <brixen> and then rubyspecs
[15:24:27] <brixen> which I can certainly help with
[15:24:39] <brixen> but my question would be: why is that not the proposed
           process?
[15:25:07] <drbrain> brixen: when done, please say "done" or "end"
[15:25:12] <brixen> yes, sorry
[15:25:15] <wycats> !
[15:25:16] <brixen> done
[15:25:24] <drbrain> I don't want to hand the floor to someone while you're
           still speaking :D
[15:25:28] <drbrain> wycats: ok
[15:25:44] <wycats> RubySpec would be considered the endpoint of the proposal
           process.
[15:26:24] <wycats> Proposals themselves could use the language of RubySpec
           to provide examples, but brixen is the one with a coherent
           understanding of how to integrate a new feature into the wider
           RubySpec project
[15:27:02] <tenderlove> !
[15:27:05] <wycats> So brixen would be responsible for making sure that any
           new features that got consensus would be integrated smoothly into
           RubySpec in a way that he felt comfortable with as the
           "specification author"
[15:27:10] <wycats> done
[15:27:13] <drbrain> tenderlove: ok
[15:27:24] <tenderlove> So I put together an example here:
           https://github.com/tenderlove/ruby/wiki/The-Ruby-Programming-Language
[15:27:29] <tenderlove> because I'd like people to see it
[15:27:42] <tenderlove> A list of proposals:
           https://github.com/tenderlove/ruby/wiki/Straw-Man-Proposals
[15:27:42] <evan> !
[15:27:49] <tenderlove> example proposal:
           https://github.com/tenderlove/ruby/wiki/IO.readlines-should-use-keyword-arguments
[15:27:54] <tenderlove> done
[15:27:57] <drbrain> evan: ok
[15:28:28] <evan> The question of RubySpec usage is one of complete test for
           a feature
[15:28:59] <evan> if a champion provides a complete test suite for a feature
           in any form, I believe that will be succifient
[15:29:18] <wycats> !
[15:29:30] <evan> The big worry of having one person (brixen) write RubySpec
           after the fact is the act of having to discover all the features
           and edge cases after the fact
[15:29:59] <evan> if they're all documented in tests by the champion, a
           process of converting them to RubySpec could be by easily by a
           number of people
[15:30:10] <lrz> !
[15:30:14] <evan> so the issue is really the completeness of a test suite
           available for a feature
[15:30:15] <drbrain> evan: please wrap up
[15:30:15] <evan> done
[15:30:25] <drbrain> I will go with lrz first, then wycats
[15:30:27] <drbrain> lrz: ok
[15:30:50] <lrz> ok, what if a feature gets accepted by the language team,
           but cannot be implemented in a specific implementation of the
           language
[15:30:56] <lrz> (for ex. if it does not make sense)
[15:31:08] <lrz> does that implementation cannot call itself "ruby"?
[15:31:09] <lrz> done
[15:31:15] <drbrain> wycats: ok
[15:31:22] <enebo> !
[15:31:33] <wycats> lrz: the implementation should object to the feature when
           it is raised, which will mean it does not reach consensus
[15:31:45] <wycats> evan: i think it makes sense to have a well-defined
           feature, including with tests
[15:31:59] <headius> !
[15:32:07] <lrz> so every implementation has veto power on all features...
[15:32:08] <wycats> but we should avoid getting overly concerned about
           whether the tests are in "RubySpec style"
[15:32:09] <wycats> done
[15:32:19] <wycats> wait
[15:32:24] <drbrain> wycats: continue
[15:32:54] <wycats> lrz: I would not use the term "veto power". It is a
           consensus process. Implementations should avoid objecting to
           features they have minor disagreements with.
[15:32:55] <wycats> done
[15:33:16] <drbrain> enebo: ok
[15:33:18] <enebo> I have 2 questions: 1) What is concensus (really 100%)?
           2) How fine-grained is the scope of enhancement?
[15:33:41] <wycats> !
[15:33:42] <enebo> 2 is particularly troubling to me since I could see this
           almost replacing redmine
[15:33:45] <enebo> done
[15:33:48] <drbrain> headius: ok
[15:33:54] <headius> ok...neato
[15:34:11] <headius> so first off, I suppose the idea so far…especially the
           idea that there's a central authority for specs
[15:34:22] <headius> that may be brixen alone but probably would be better as
           a blessed team that can help implement
[15:34:41] <headius> given a complete set of incoming tests, or a really
           well-written english specification, I think that's going to
           produce the cleanest rubyspec results
[15:35:09] <tenderlove> !
[15:35:11] <headius> my concern is making sure brixen is not the sole
           responsible party, which means we need a way to ensure incoming
           features have enough coverage via test cases or english spec
           before hand-off
[15:35:12] <headius> done
[15:35:20] <drbrain> wycats: ok
[15:35:26] <headius> (sole responsible party as in brixen shouldn't have to
           do everything alone)
[15:35:59] <wycats> enebo: consensus means "100%", but a good consensus
           process is one where people aim to reach consensus, not focus on
           exercising their veto
[15:36:16] <wycats> in terms of Redmine, I'd expect it to become a bug
           tracker for the MRI implementation, rather than a place to discuss
           new features
[15:36:26] <evan> !
[15:36:30] <wycats> it would still be an appropriate place to discuss MRI
           implementation of new features or patches containing experimental
           features
[15:36:44] <wycats> Agreed with headius about making sure brixen has enough
           help
[15:36:57] <wycats> however, I think it's important to have a single "spec
           editor" with a sense of the full aesthetics
[15:36:57] <wycats> done
[15:37:10] <brixen> !
[15:37:15] <drbrain> tenderlove: ok
[15:37:33] <emboss> !
[15:38:00] <tenderlove> lol, I think wycats covered what I wanted to say.
           Though, I'd like to hear from matz_ ;-)
[15:38:01] <zenspider> !
[15:38:01] <tenderlove> done
[15:38:08] <drbrain> evan: ok
[15:38:26] <evan> A champion may wish to introduce a platform feature
[15:38:33] <evan> thats not capable of being implemented everywhere
[15:38:40] <evan> an example would be fork
[15:38:48] <wycats> !
[15:38:52] <evan> these feature should be consider the exception rather than
           the rule
[15:39:03] <evan> and would likely be explained to be optional
[15:39:04] <evan> done
[15:39:07] <drbrain> emboss: ok (brixen will follow since emboss hasn't
           spoken)
[15:39:17] <emboss> Two quick questions: 1. How would the process extend to
           extensions that are part of stdlib (net/http, openssl...) and 2.
           What should happen in the case of let's say OpenSSL where there is
           no RubySpec yet? Write some asap?
[15:39:22] <emboss> Done
[15:39:25] <drbrain> brixen: ok
[15:39:47] <brixen> I want to emphasize that my proposal calls for a complete
           documentation and rubyspec before voting to implement
[15:39:55] <tenderlove> !
[15:40:01] <brixen> and I'd like there to be core Ruby features and
           standardized optional Ruby features
[15:40:03] <brixen> done
[15:40:04] <drbrain> zenspider: ok
[15:40:15] <zenspider> Redmine already has multiple projects. It would be a
           good place to make one specifically for talking about proposals.
           We need something permanent and trackable as we've had too much
           stuff fall through the cracks when it is just email and IRC.
[15:41:10] <zenspider> rubyspec shouldn't be a requirement before voting to
           implement. ANY form of specification (unit test, english,
           whatever) should be required
[15:41:11] <zenspider> done
[15:41:13] <drbrain> matz_: we would like your opinion
[15:41:18] <dbussink> !
[15:41:20] <tenderlove> <3<3<3
[15:41:40] <matz_> I'm listening
[15:41:48] <zenspider> :P
[15:41:51] <drbrain> ok, then dbussink: ok
[15:41:51] <enebo> !
[15:42:14] <dbussink> drbrain: ok to who first?
[15:42:21] <drbrain> dbussink: you
[15:42:25] <drbrain> matz is listening still
[15:43:12] <dbussink> ok, i would also like to request feedback from a larger
           group of the mri team. A lot of development is happening in japan
           and i feel that this would not work well if they will not support
           it too
[15:43:32] <dbussink> what i'm asking is whether there are objections there,
           maybe language or process wise
[15:43:53] <dbussink> are people comfortable with the idea and also with
           aspects such as this happening in english
[15:43:55] <dbussink> done
[15:44:25] <drbrain> _ko2, nurse, n0kada: if you have a comment, please bring
           it up
[15:44:35] <drbrain> or other .jp ruby developer
[15:44:39] <drbrain> enebo: ok
[15:44:55] <enebo> I am still fairly concerned from moving from a master
           surgeon sort of design (or Matz-driven design) to one of 100%
[15:45:03] <enebo> 100% consensus
[15:45:12] <wycats> !
[15:45:47] <enebo> I am not always in agreement with Matz and would like a
           process for new features but JVM had 100% consensus for features
           and it ended up giving us a horrible generics implementation
[15:46:14] <enebo> I like the idea in general but the 100% consensus bit rubs
           me
[15:46:15] <enebo> done
[15:46:18] <jballanc> !
[15:46:25] <drbrain> wycats: ok, then jballanc and tenderlove
[15:46:34] <zenspider> !!
[15:46:49] <wycats> No implementation would be required to wait for approval
           before beginning implementation
[15:47:04] <wycats> I would expect matz_ to continue working much the same
           way he has worked so far -- implementing features experimentally
[15:47:18] <wycats> but now, he would bring those proposals to a wider group
           to get feedback and try to reach implementor consensus
[15:47:30] <wycats> re: optional features, that's totally fine, of course
[15:47:39] <wycats> for example Function#toString in JavaScript is optional
[15:47:45] <wycats> but if implemented, it has a well-defined specification
[15:47:49] <wycats> and finally
[15:47:54] <wycats> the endpoint of the process would be something like
[15:48:02] <wycats> 1) general consensus about the proposal
[15:48:09] <wycats> 2) specification language by brixen et al
[15:48:18] <wycats> 3) consensus about the actual specification language
[15:48:28] <wycats> it would be totally fine if people wanted to wait for
           spec language before implementing
[15:48:36] <wycats> done
[15:48:41] <zenspider> -!
[15:48:49] <drbrain> jballanc: ok
[15:48:58] <jballanc> I would like to +1 part of what brixen said (about
           standardized optional features) and suggest the SRFI (Scheme
           Request for Implementation) model might be good to follow
[15:49:26] <jballanc> in particular, that the standardized optional features
           are tied to a mechanism to include them at runtime
[15:49:43] <jballanc> i.e. similar to tail-calls in 1.9 and (maybe?)
           refinements in 2.0
[15:49:49] <jballanc> done
[15:49:55] <wycats> !
[15:49:59] <drbrain> we have ten minutes left in our scheduled hour
[15:50:14] <drbrain> I'm unsure how close we are to wrapping up this agenda
           item though.
[15:50:22] <drbrain> I suggest we defer the remaining items to a future
           meeting
[15:50:30] <headius> email thread?
[15:50:39] <drbrain> please help us bring this to a close, with your future
           comments
[15:50:47] <drbrain> I would still like to hear from matz though
[15:50:57] <wycats> I think the existence of a proposal process is a
           bootstrap for the remaining agenda items
[15:51:00] <drbrain> or any other japanese developer
[15:51:16] <matz_> !
[15:51:17] <zenspider> ditto... we need sign on by ruby-core or this is all a
           wash
[15:51:20] <drbrain> matz_: ok
[15:51:37] <matz_> Ok, I've heard your oopinion
[15:52:02] <matz_> The basic point is that you want to participate in the
           decision of improving the language
[15:52:13] <matz_> And it's OK for me
[15:52:23] <matz_> having champion is fine
[15:52:33] <headius> !
[15:53:01] <matz_> as long as I approve the feature
[15:53:35] <matz_> but having spec before implementation is too much
           restriction I believe
[15:53:52] <matz_> OK, I want hear from headius now
[15:54:00] <enebo> !
[15:54:01] <drbrain> headius: ok
[15:54:23] <drbrain> (then tenderlove, enebo, wycats)
[15:54:47] <headius> I want to summarize salient points as I see them: 1. a
           specific, appointed group of champions from the implementations
           through which all features must pass, consensus or not; 2. wiki
           for tracking current shape of feature; 3. incoming test suites
           probably limitedto specific frameworks (test/unit + rspec +
           rubyspec?); 4. end product always to be rubyspecs once feature is
           deemed worthy of inclusion
[15:55:09] <headius> I think there's a lot of process details we can flesh
           out through a separate thread, not needing direct synchronous
           discussion
[15:55:20] <headius> but perhaps we can all agree to that general framework
           and take some work offline
[15:55:21] <headius> done
[15:55:24] <drbrain> tenderlove: ok
[15:55:31] <evan> !
[15:55:35] <tenderlove> I agree with headius
[15:55:42] <tenderlove> I wanted to address matz concern
[15:55:57] <brixen> !
[15:56:06] <tenderlove> matz_: as part of the language team you have
           authority to approve or reject any particular feature
[15:56:30] <tenderlove> and people are allowed to implement without a spec,
           but those features should not be considered "official" (I would
           say)
[15:56:39] <dbussink> !
[15:56:40] <shugomaeda> !
[15:56:41] <tenderlove> done
[15:56:45] <drbrain> shugomaeda: ok
[15:57:02] <drbrain> (then enebo, wycats, evan, brixen, dbussink)
[15:57:03] <n0kada> !
[15:57:09] <shugomaeda> tenerlove: what's the difference from other members?
           you said 100% consensus
[15:57:16] <shugomaeda> done
[15:57:20] <drbrain> n0kada: ok
[15:57:34] <drbrain> (if you haven't spoken before, you get priority)
[15:57:41] <n0kada> answering dbussink's question (sorry to be late) about
           "this happening in english", it'd be less comfortable to me, but
           acceptable.
[15:57:54] <tenderlove> !
[15:57:55] <n0kada> end
[15:58:01] <drbrain> enebo: ok
[15:58:04] <enebo> I agree with what headius just said also as a summary
[15:58:12] <enebo> _matz: as experiment do you think you should get 100%
           consensus by team before approving it yourself? :)
[15:58:28] <matz_> of course not
[15:58:29] <enebo> This would be a large change to how things are working now
           and I am curious on your thoughts
[15:58:36] <enebo> ok thank you
[15:58:36] <enebo> don
[15:58:39] <enebo> and done
[15:58:52] <drbrain> at the end of the hour I will not accept ! from new
           speakers
[15:58:56] <drbrain> wycats: ok
[15:59:04] <drbrain> (so if you wish to speak, please ! soon)
[15:59:09] <akr_> !
[15:59:10] <eregon> !
[15:59:20] <sora_h> !
[16:00:00] <marcandre> !
[16:00:04] <drbrain> evan tells me wycats walked away
[16:00:10] <drbrain> so, evan: ok
[16:00:12] <wycats> I am here
[16:00:17] <wycats> how does evan know I walked away
[16:00:22] <tenderlove> lol
[16:00:25] <lrz> lol
[16:00:26] <sora_h> lol
[16:00:39] <drbrain> oops! sorry, evan, please continue, then wycats
[16:00:40] <drbrain> I am sorry!
[16:00:45] <evan> I want to ask a question and defer directly to matz for the
           answer: to be clear, will you allow the language team to veto one
           of your features? That will mean that MRI may not have that
           feature.
[16:01:10] <evan> I defer to matz_ for the answer directly.
[16:01:22] <evan> done
[16:01:25] <drbrain> wycats: ok
[16:01:26] <dbussink> drbrain: ?
[16:01:41] <wycats> So I want to clarify a few things
[16:01:51] <wycats> I actually would not expect this to significantly change
           how Matz does development
[16:02:04] <evan> wait wait, can you wait for matz to answer me?
[16:02:04] <evan> soryr.
[16:02:18] <wycats> and the Team cannot veto an MRI feature
[16:02:29] <zenspider> or that
[16:02:56] <drbrain> matz_: did you see evan's question?
[16:03:31] <wycats> I wanted to clarify something first
[16:03:38] <drbrain> wycats: please be quick
[16:04:00] <wycats_> This proposal shouldn't significantly alter the way matz
           does development
[16:04:10] <wycats_> MRI can add whatever feature it wants
[16:04:20] <wycats_> and nobody can veto Matz adding a feature to MRI
[16:04:27] <wycats_> nor can anyone require a spec before any implementor
           adds a feature
[16:04:45] <shugomaeda> (matz_ doesn't have voice, does he?)
[16:04:54] <matz_> do I?
[16:04:56] <drbrain> oops
[16:04:57] <wycats_> the goal of this is to have a clear way to explain what
           features are generally agreed to by the implementors, so that
           people who want to target "Ruby" have a sense of what is supported
[16:05:07] <drbrain> wycats: your time is up
[16:05:09] <wycats_> ok
[16:05:13] <wycats_> my IRC flaked out hard
[16:05:29] <drbrain> matz_: can you respond?
[16:05:57] <drbrain> (then akr_, eregon, sora_h, marcandre. I have pending !s
           from brixen and dbussink, too)
[16:06:13] <matz_> OK, I am not THAT positive about having rigid process
[16:06:51] <tenderlove> (reminder, use simple words.  Sorry folks. :( /cc
           wycats_ )
[16:07:18] <matz_> But I agree with having executable spec for new spec that
           can gradually be translated in to RubySpec
[16:07:53] <drbrain> matz_: specifically, evan wants to know: Will you allow
           the language team to veto one of your features?
[16:08:17] <matz_> And I want to make sure as long as I live on this mortal
           state, you need my approval before adding something as official
           Ruby
[16:08:30] <tenderlove> lol
[16:08:34] <matz_> and don't try to kill me ;-)
[16:08:41] <drbrain> lol
[16:08:41] <zenspider> bwahahaa
[16:08:43] <tenderlove> hahaha
[16:08:57] <sora_h> :)
[16:08:59] <drbrain> matz_: done?
[16:09:04] <matz_> almost
[16:09:32] <matz_> we can improve the process
[16:09:47] <matz_> And way to communicate each other
[16:10:09] <matz_> I'd like to have this kind of communication time to time
           in the future
[16:10:10] <matz_> done
[16:10:22] <akr_> I think many features will be optional.  For example, mruby
           doesn't have Bignum, so it is optional in the specification if we
           approve mruby as Ruby.
[16:10:24] <akr_> done
[16:10:39] <drbrain> eregon: ok
[16:10:53] <eregon> I believe 100% consensus from every member of the team is
           idealistic, I doubt each member can look at every feature, may the
           feature be very well defined (which would be a clear win and a
           champion should help). So I think it's more reasonable to just
           reach consensus between partipants, have matz approval, and maybe
           require at least one approval by implementation for important
           features
[16:11:11] <eregon> Sorry if I misunderstood.
[16:11:48] <eregon> What's most needed, I believe, is more discussion on
           important features from implementers.
[16:11:49] <drbrain> eregon: done?
[16:11:49] <eregon> done
[16:11:52] <drbrain> sora_h: ok
[16:12:04] <sora_h> for a question from dbussink, I'm acceptable (sorry for
           answering to too far log)
[16:12:11] <sora_h> done
[16:12:17] <tenderlove> sora_h: <3<3
[16:12:24] <drbrain> marcandre: ok
[16:12:33] <marcandre> An important point is distinguishing Ruby from CRuby.
           Currently distinction is slim. Implementation dependent and
           independent tests are mixed in test/*. Feature requests are on
           ruby-core, along with CRuby bug reports. I'm not sure we need
           separate mailing lists though.
[16:12:33] <marcandre> I wish there was an official way to write all (new)
           implementation independent specs & tests. RubySpec is of course
           the prime candidate for this.
[16:12:58] <marcandre> An improved feature request / design process would be
           great, in particular having an updated description of requests.
[16:13:03] <marcandre> done.
[16:13:05] <shugomaeda> !
[16:13:26] <drbrain> shugomaeda: since you have only spoken once, please speak
[16:13:31] <drbrain> (then brixen and dbussink) to close
[16:13:37] <drbrain> shugomaeda: … ok
[16:13:39] <shugomaeda> it's hard for us to maintain both test unit and
           rubyspec for all features
[16:14:09] <shugomaeda> I propose to make a flag for test unit to identify
           implementation-defined features
[16:14:15] <shugomaeda> done
[16:14:19] <drbrain> brixen: ok
[16:14:33] <brixen> to clarify experimenting, documenting, and rubyspec...
[16:14:41] <brixen> any implementation can experiment
[16:14:52] <brixen> before proposing a feature to be "Ruby", it needs docs
           and rubyspec
[16:15:08] <zenspider> !
[16:15:12] <brixen> as for approval, I'm explicitly asking whether matz will
           allow the team to override his decision
[16:15:15] <drbrain> zenspider: too late ☹
[16:15:21] <brixen> in other words, everyone on the team has the same vote
[16:15:30] <brixen> matz can vote no just like anyone else
[16:15:36] <brixen> will matz accept this proposal?
[16:15:37] <brixen> done
[16:15:48] <drbrain> dbussink: ok
[16:16:19] <dbussink> what i would like to point out is that what is
           implemented in CRuby but i seen as implementation dependent, often
           ends up not being so
[16:16:34] <dbussink> that is because of the place it has in the community
           now, that other implementations have to follow
[16:16:54] <dbussink> that is why i think it is very important for features
           in that area to be properly specified
[16:17:32] <dbussink> and experiments need to be very explicit, otherwise
           reality can catch up on us, independent of whether we decide
           something is optional  / implementation dependent
[16:17:34] <dbussink> done
[16:17:42] <drbrain> matz_: do you have any closing comments?
[16:17:58] <matz_> hmm
[16:18:01] <drbrain> (thank you all for participating and making my job as a
           moderator easy)
[16:18:18] <evan> matz_: perhaps brixen's last question?
[16:19:16] <matz_> I am not going to stop being the dictator, but I want you
           guys to participate in the process of improving the language
[16:19:28] <headius> clarifying a point made earlier: 100% consensus means
           any no vote acts as veto…so the question is really whether matz
           can inject a feature into standard ruby without 100% consensus
[16:19:47] <dbussink> matz_: i think there are two different situations, you
           having to bless every feature
[16:20:05] <dbussink> matz_: and the language team being able to say no to a
           feature you propose
[16:20:07] <drbrain> please, we are waiting for matz
[16:20:42] <evan> matz_: if you wish to add a feature String#to_ruby, can I
           say "no" and it will not be added?
[16:20:49] <matz_> headius, I would keep the privilege, but I promise I will
           not use it often
[16:21:45] <drbrain> matz_: done?
[16:21:47] <matz_> evan, you have to persuade me, but no just saying no
[16:21:55] <shugomaeda> !
[16:21:56] <headius> that is a satisfactory answer for me
[16:22:01] <wycats> matz_: keep in mind you can add String#to_ruby to MRI if
           you want
[16:22:02] <matz_> and I am very easy to be persuaded
[16:22:19] <wycats> but you need to persuade other implementers if you want
           THEM to implement
[16:22:40] <matz_> wycats, yest
[16:23:05] <dbussink> wycats: the point i was trying to make is that is not
           always true. it is a very real situations that other
           implementations don't really have a choice but to follow
[16:23:07] <drbrain> I think we should now close the meeting
[16:23:16] <drbrain> for the next meeting we will need to have a shorter
           topic introduction.
[16:23:16] <wycats> thanks everyone!
[16:23:17] <enebo> matz_: your answer is reasonable to me
[16:23:20] <drbrain> I will cut off ! sooner, such as 45 minutes after start
           of meeting
[16:23:28] <drbrain> shugomaeda: feel free to speak whenever now
[16:23:57] <lrz> awesome organization, congrats to drbrain and tenderlove :)
[16:23:59] <drbrain> feel free to remain and discuss whatever you feel like
[16:23:59] <shugomaeda> I believe only matz should have veto power
[16:24:11] <shugomaeda> because Ruby is his language
[16:24:29] <lrz> shugomaeda: i agree and wanted to write the exact same words
[16:24:29] <brixen> thank you matz and everyone
[16:24:30] <shugomaeda> I believe he don't do evil things
[16:24:36] <brixen> drbrain: will the logs continue? I have to leave soon
[16:24:38] <dbussink> thanks everyone!
[16:24:41] <drbrain> brixen: yes
[16:24:43] <headius> the major sticking points seem to be: veto power for
           anyone, sequence of approval and rubyspecs
[16:24:46] <brixen> drbrain: thanks
[16:24:52] <headius> I'd like to discuss over a longer thread somewhere
[16:24:59] <dbussink> drbrain: thanks for moderating :)
[16:24:59] <drbrain> brixen: I will post meeting logs to the wiki...
[16:25:02] <headius> maybe tenderlove could summarize in a ruby-core email
[16:25:08] @ drbrain set topic "last meeting: http://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20121210"</pre>
