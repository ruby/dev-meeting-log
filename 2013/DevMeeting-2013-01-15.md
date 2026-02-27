---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-01-15

This meeting will be held on [2013-01-15 at 15:00 Pacific Time](http://timeanddate.com/worldclock/meetingdetails.html?year=2013&month=1&day=15&hour=23&min=0&sec=0&p1=234&p2=248&p3=48&p4=64/) at irc://chat.freenode.net/#ruby-implementers.

EN &lt;=&gt; JA translations will be done on demand in #ruby-ja on ircnet

## Attendees

### MRI

* tenderlove (Aaron Patterson)
* matz
* kosaki
* eregon
* sorah
* marcandre (Marc-André Lafortune)
* duerst (Martin Dürst)
* shugo
* emboss (Martin Boßlet)

### Rubinius

* evan
* dbussink
* brixen

### JRuby

* headius
* enebo

### MagLev

* phlebas (Tim Felgentreff)

### MacRuby

* jballanc (Joshua Ballanco)

## Moderator

* drbrain (Eric Hodel)

## Agenda

We'll keep this meeting to one hour long.

* Introduction (tenderlove)
* Refinements
  * Experimental feature?
  * Other issues after scope reduction
* Keyword Arguments
  * #7662, #7663, and #7664
* Isolated binding specifier (jballanc)
* Open Floor
* Wrap Up

## Summary

## Refinements

* They are officially an experimental feature (no other impl needs them)
* JRuby's current implementation is an earlier form of the spec
* Currently refinements can be used without a require
* Using refinements will cause a warning (even without -w)

### Conclusion

* Wiki and tests are updated with latest spec
  https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/RefinementsSpec
* The goal for "non-experimental" status is by Ruby 2.1

### Open Questions

* Can we have a dummy require that enables refinements?

## Keyword Arguments

* Should be documented in doc/syntax (but needs review)
* People want non-optional keyword arguments

### Conclusion

No issues on current kw args from implementers, but people want non-optional args.

### Action Items

* headius opened a ticket for non-optional args, #7701
* Add examples of non-optional args "in the wild" to his ticket

### Isolated Binding Specifier

* A new binding specifier defined here: #6710
* Binding semantics are not well defined
* Some things should not be allowed
  * Changing values of locals in someone else's binding
* Maintaining MRI compatibility hinders perf, difficult to implement
* Secure datastructures can be inadvertently exposed
* Maybe we should remove Proc#binding?
* Security shouldn't be a motivator (because everything is exposed in ObjectSpace)
* Isolated binding could be used for moving Procs between processes
  * No binding means we could marshal a proc

### Conclusion

More than just "isolated" binding is desired

### Action

* Move discussion for more features to "isolated" redmine ticket

## Open Floor

* New "Common Ruby" project
  https://bugs.ruby-lang.org/projects/common-ruby
  * Should we actively put new features there?
  * We need to publicize it as "where new language features go"
* Issue #7564 needs review from matz
  * Matz said "I will see"

## IRC Log

```
15:00 ferrous26: tenderlove: you did?
15:01 tenderlove: I did!
15:01 drbrain: If you don't have voice and are a ruby
      implementer please /msg me or tenderlove
15:01 ferrous26: cool, thanks!
15:01 tenderlove: :-)
15:01 drbrain: tenderlove will be starting with the
      introduction
15:01 shugomaeda: Is matz here?
15:01 drbrain: if you wish to comment please say "!"
15:01 drbrain: please limit your comments to 2 minutes
15:01 zenspider: waves
15:01 drbrain: I will give priority to people who haven't
      spoken on the topic
15:02 drbrain: see the /topic for the agenda
15:02 headius: might help pacing to prepare your comment in a
      separate buffer and then paste it, btw
15:02 headius: so we're not waiting on typing too much
15:02 tenderlove: I guess we're waiting on matz?
15:02 drbrain: I will cut off new "!" at 45 to 50 minutes
      into the meeting to keep this to one hour
15:04 tenderlove: it seems we have more people participating
      than are in the wiki
15:05 tenderlove: add your name and email address here:
15:05 tenderlove:
      https://gist.github.com/2c3b41ed83e5d062f636
15:05 drbrain: and IRC nick
15:05 tenderlove: I'll send out a survey after we're done so
      that we can improve the meeting!
15:05 tenderlove: yes, and IRC nick
15:05 tenderlove: (I'll add the nick to the attendees list in
      the wiki but not your email address)
15:05 matz_: Sorry to be late
15:06 tenderlove: no problem
15:06 tenderlove: drbrain: shall we start?
15:06 drbrain: tenderlove: please start
15:06 tenderlove: thanks for coming everyone
15:06 tenderlove: like I was saying earlier, it seems we have
      more people attending than in the attendee list on the
      wiki
15:07 tenderlove: so if you're not on this list, please
      comment with your name, email, and IRC:
15:07 tenderlove:
      https://gist.github.com/2c3b41ed83e5d062f636
15:07 tenderlove: (IRC nick)
15:07 tenderlove: I'll add the nicks to the wiki, and use
      your email for a survey afterword
15:07 tenderlove: so we'll stick to 1 hour
15:07 tenderlove: So first on the agenda is refinements
15:08 tenderlove: AFAIK, they are now experimental?
15:08 tenderlove: what are the details of this?
15:08 tenderlove: end
15:08 tenderlove: maybe shugomaeda can talk about it?
15:08 drbrain: oh, and finish your comment with "end"
      please
15:08 shugomaeda: !
15:08 drbrain: shugomaeda: ok
15:08 shugomaeda: I believe they are experimental
15:08 shugomaeda: end
15:08 matz_: It's experimental, so that other implementation
      has no obligation.
15:09 tenderlove: lol
15:09 headius: !
15:09 drbrain: headius: ok
15:09 tenderlove: !
15:09 headius: I went around and around on the refinements
      issue a lot, so I've seen many different forms
15:09 headius: I believe in the end what we decided was that
      there were still too many questions and changes
      happening to force it into 2.0, so it's marked
      experimental
15:09 shugomaeda: !
15:09 headius: I'm not sure which is the current form,
      though…and I have neglected to implement any of the
      later forms in JRuby yet, but that is on my
      roadmap
15:10 headius: end
15:10 drbrain: tenderlove: ok
15:10 tenderlove: when we say experimental, does that mean
      that we have to "opt-in"?  or is there a warning?
15:10 tenderlove: how do users know that it's
      experimental?
15:10 tenderlove: end
15:10 drbrain: shugomaeda: ok
15:10 shugomaeda: headius: currently, refinements are enabled
      by default, but a warning shown. Is it OK?
15:11 shugomaeda: end
15:11 headius: do I answer directly or ! again? :)
15:11 drbrain: headius: ok
15:11 tenderlove: lol
15:11 drbrain: it's ok to answer directly
15:11 headius: That seems acceptable to me…I would have liked
      to have an opt-in like require 'refinements' but I
      understand the difficulty of doing a lexical feature
      that's optional
15:11 headius: is the wiki up-to-date with the current
      impl?
15:12 headius: and tests
15:12 tenderlove: shugomaeda: does that mean you need to run
      with `-w` to see the warning?
15:12 headius: end
15:12 shugomaeda: !
15:12 tenderlove: sorry :(
15:12 drbrain: shugomaeda: ok
15:12 shugomaeda: tenderlove: a warnging is shown without
      -w
15:12 shugomaeda: headius: wiki and tests are up-to-date with
      the current impl
15:12 jballanc: !
15:12 shugomaeda: end
15:12 drbrain: jballanc: ok
15:13 jballanc: would it be possible to have a dummy
      require?
15:13 headius: shugomaeda: (sidebar) thank you…I will base
      JRuby impl on that
15:13 shugomaeda: !
15:13 jballanc: or should we do like we currently do with
      fork and throw?
15:13 jballanc: end
15:13 tenderlove: (here is the refinement page:
      https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/RefinementsSpec)
15:13 drbrain: shugomaeda: ok
15:13 shugomaeda: jballanc: i guess it's possible if
      permitted by matz
15:14 headius: !
15:14 shugomaeda: like marshal.so
15:14 shugomaeda: end
15:14 drbrain: headius: ok
15:14 headius: If anyone has continuing concerns about the
      feature, I strongly reccommend voicing them on the
      bug
15:14 headius: and this means actual reasons why you don't
      like it, things you would like to see changed, etc,
      rather than just "this sucks, don't do it"
15:14 headius: end
15:15 drbrain: is there anything else outstanding for
      refinements?
15:15 headius: oh, I have one ?
15:15 drbrain: headius: ok
15:15 headius: matz_: is the intent to finalize these for
      2.1? or a put a different way…how much time do we have
      to finalize refinements?
15:15 dbussink: !
15:16 drbrain: dbussink: ok
15:16 drbrain: oops, I assume headius is done?
15:16 dbussink: On refinements, since the feature is
15:16 dbussink: experimental does that mean we still keep the
      possibility open of removing them if they don't work
      out?
15:16 headius: yeah just asked the question… end
15:16 dbussink: and if so, what would be criteria we would
      judge the feature on?
15:16 dbussink: end
15:16 drbrain: matz_: ?
15:17 matz_: The 2.0 refinement spec is minimal. I believe we
      have no further issue here.
15:17 matz_: But I admit I am not perfect, so that there
      might be holes in the spec.
15:17 matz_: I'd like to have time to see them.
15:18 zenspider: !
15:18 dbussink: matz_: so that means the answer would be that
      they will not be removed from now on?
15:18 matz_: dbussink: I don't think I am going to remove
      refinement.
15:18 matz_: end
15:18 drbrain: zenspider: ok
15:19 zenspider: I'd like to see the wiki page expanded
      defining backtraces, debugging, and any other means of
      figuring out where behavior is coming from / defined.

15:19 zenspider: end
15:19 drbrain: shugomaeda: matz_: any comment?
15:19 headius: !
15:19 drbrain: headius: ok
15:20 headius: I have not had a chance to review latest
      version of this in wiki, but I stand by assertions that
      refinenements affecting methods not defined in their
      scope like #method, #send, and so on is a bad
      idea
15:20 zenspider: (esp from outside the refinement)
15:20 headius: I believe most of that was backed off for 2.0
      spec
15:20 shugomaeda: !
15:20 headius: I'd support having some additional utility
      methods for reflective stuff from within refinement
      that don't affect existing methods
15:21 headius: mostly because existing methods should behave
      like they do now without having to guess about active
      refinements
15:21 headius: end
15:21 drbrain: shugomaeda: ok
15:21 drbrain: (I think we should move on to the next topic
      soon)
15:21 shugomaeda: The wiki says "Any indirect method access
      such as Kernel#send, Kernel#method, and
      Kernel#respond_to? shall not honor refinements in the
      caller context during method lookup."
15:21 shugomaeda: end
15:21 headius: thank you
15:22 headius: my question about timing of making it
      non-experimental is still open
15:22 drbrain: matz_: ?
15:22 headius: I mostly just want to know how long we have to
      discuss and "refine" the feature
15:22 zenspider: !
15:22 matz_: My goal is 2.1, but you know, we are open
      source.  We don't have exact roadmap
15:22 drbrain: after matz_ answers we will move on to the
      next topic, we can reopen this if time remains
15:23 drbrain: zenspider: please wait
15:23 drbrain: matz_: done?
15:23 matz_: end
15:23 drbrain: tenderlove: next topic please
15:23 zenspider: I vote 2.1 ... sooooo christmas :P
15:23 headius: matz_: ok, acceptable answer
15:23 zenspider: end :P
15:23 tenderlove: OK!  Keyword arguments
15:23 headius: yay kwargs
15:24 tenderlove: I mainly put these on the agenda because
      wycats_ had issues, but I believe he was happy with
      matz_'s responses
15:24 tenderlove: so....
15:24 tenderlove: anyone have questions / issues?
15:24 drbrain: !
15:24 drbrain: drbrain: ok
15:24 headius: hah
15:24 drbrain: I documented keyword arguments in doc/syntax,
      can someone check it to make sure I did it right?
15:24 drbrain: or if I missed anything?
15:24 drbrain: end
15:24 tenderlove: drbrain: where?
15:25 drbrain: in ruby trunk, doc/syntax/methods.rdoc and
      doc/syntax/calling_methods.rdoc
15:25 enebo: claps
15:25 tenderlove: <3<3<3
15:25 zenspider: !
15:25 drbrain: zenspider: ok
15:25 headius: 888888
15:25 zenspider: I STILL WANT OPTIONAL COLON ON THE MESSAGE
      NAME! GIVE ME SMALLTALK KEYWORD MESSAGES! RAWR!
15:25 zenspider: end
15:25 zenspider: :D
15:25 headius: hah
15:25 headius: !
15:26 zenspider: (no really)
15:26 drbrain: headius: ok
15:26 enebo: fillibuster?
15:26 headius: I did have one concern I raised on the main
      issue, but I think matz_ and I will agree to disagree
      here… I wanted to support non-optional keyword args,
      like def foo(a:, b:)
15:26 headius: I'd like to hear thoughts, but I accept it
      isn't matz's plan right now
15:26 headius: end
15:27 tenderlove: !
15:27 drbrain: tenderlove: ok
15:27 tenderlove: I would like non-option kw arguments as
      well
15:27 enebo: !
15:27 tenderlove: I'm afraid we're going to end up with lots
      of code that's like
15:27 tenderlove: def foo(a: raise("omg"), b: raise)
15:28 tenderlove: end
15:28 tenderlove: end
15:28 drbrain: enebo: ok
15:28 enebo: Actually I was just going to ask what the
      use-case is and the only one I have seen to date is
      error reporting
15:28 zenspider: !
15:28 enebo: So I half wonder if there is not a way to deal
      with that aspect somehow
15:28 enebo: end
15:28 drbrain: zenspider: ok
15:28 tenderlove: !
15:28 zenspider: eww? end
15:28 drbrain: tenderlove: ok
15:29 tenderlove: enebo: it's all over the rails codebase
      today
15:29 enebo: !
15:29 tenderlove: we actually have a special method called
      assert_valid_keys
15:29 tenderlove: that makes sure particular options hashes
      have particular keys
15:29 tenderlove: and raises if those keys are missing
15:29 tenderlove: end
15:29 drbrain: enebo: ok
15:29 matz_: !
15:29 marcandre: !
15:29 enebo: We also have def foo(a); @a = a all over the
      place?
15:30 enebo: end
15:30 drbrain: matz_: ok
15:30 matz_: submit non optional keyword arg to the
      redmine.
15:30 matz_: I don't think we saw it before
15:30 matz_: end
15:30 _ko1: matz_++
15:30 drbrain: marcandre: ok
15:30 marcandre: assert_valid_keys is different
15:30 marcandre: it checks that other keys have not been
      passed.
15:30 marcandre: AFAIK it doesn't require keys
15:31 headius: !
15:31 marcandre: I would like to know the reasons for not
      having mandatory keys.
15:31 marcandre: I think it would be useful
15:31 marcandre: end
15:31 drbrain: headius: ok
15:31 headius: It was a passing comment I made mostly calling
      out consistency with positional args…perhaps it was
      missed; I will submit as a top-level issue
15:31 headius: since matz is interested in having it
      submitted, I'll handle submitting a top-level issue and
      we can discuss all this there
15:31 tenderlove: 8888
15:32 headius: it would be a non-breaking change to add it
      because no code in 2.0 can define non-optional
      keywords
15:32 headius: end
15:32 drbrain: I think we are done with keyword
      arguments
15:32 drbrain: thanks, headius, for taking care of submitting
      the feature request
15:32 drbrain: tenderlove: next topic please
15:32 tenderlove: k
15:33 tenderlove: next up is isolated binding specifier
15:33 tenderlove: which I think _ko1 proposed
15:33 wycats_: tenderlove: I was happy with the responses I
      got on my tickets, yes
15:33 tenderlove: but jballanc was interested
15:33 zenspider: definition or url pls?
15:33 wycats_: tl;dr: *args can take keyword arguments
15:33 wycats_: if **kwargs are not supplied
15:33 wycats_: that was sufficient for me
15:33 drbrain: wycats_: please wait for end of
      discussion
15:33 tenderlove: urgh
15:33 tenderlove: trying to find the link
15:33 tenderlove: jballanc: do you have it handy?
15:34 jballanc:
      http://bugs.ruby-lang.org/issues/6710#change-27897
15:34 jballanc: sorry...had to find it again
15:34 tenderlove: hereis the issue ^^
15:34 tenderlove: I'll hand this over to jballanc since he
      requested the topic
15:34 jballanc: I've also prepared a gist:
      https://gist.github.com/4543026
15:34 jballanc: tenderlove: thanks!
15:35 jballanc: so, I discussed this a bit in my RubyConf
      2011 talk and also informally with a few others
15:35 jballanc: essentially, the semantics of bindings (esp.
      bindings for procs) in MRI are not well defined
15:35 jballanc: there are many things you can do with
      bindings that should probably be disallowed
15:35 headius: (thank you for summary…I was about to
      ask)
15:36 jballanc: such as changing the values of locals not
      actually referenced within a proc/block from the
      binding of that proc/block
15:36 jballanc: essentially, these are accidents of the way
      bindings and procs/blocks are implemented in MRI
15:36 headius: !
15:36 jballanc: unfortunately, keeping the same semantics as
      MRI prohibits any implementation other than one that
      mirrors MRI's
15:37 jballanc: this also means that many optimizations are
      not possible
15:37 jballanc: I have some ideas on how we might proceed,
      but I'll turn over the floor for now
15:37 jballanc: end
15:37 drbrain: headius: ok
15:37 headius: wycats and I have discussed many times over
      the years the perils of bindings that extend their
      reach too far
15:37 headius: optimization is just one of them, and I know
      that's not compelling enough usually
15:37 headius: you can end up holding large graphs of
      objects, keeping objects from GCing, and so on
15:38 headius: you can also inadvertently expose secure data
      to other libraries and calls very easily
15:38 headius: def method; password = get_secure_password();
      do_something { } ....
15:38 _ko1: !
15:38 headius: do_something can basically see everything in
      your environment and even modify it
15:39 headius: I have made arguments against this that were
      accepted wrt the old retry behavior, for example
15:39 headius: I'm not sure how much of this would be fixed
      by isolated binding stuff from ko1 but I wanted to get
      this out there
15:39 headius: end
15:39 drbrain: _ko1: ok
15:39 _ko1: I'm one of criminal to continue Proc#binding.  I
      have no objection to remove it  if matz and community
      say okay.
15:39 _ko1: end
15:39 zenspider: hah
15:40 jballanc: !
15:40 drbrain: jballanc: ok
15:40 zenspider: !
15:40 lrz: !
15:40 jballanc: heh...so, removing entirely is actually a
      possibility I hadn't considered
15:40 jballanc: I think it may actually be for the best...but
      I would need to think more
15:41 jballanc: I'm concerned, though, that the issues are
      not just isolated to procs
15:41 jballanc: procs are just the most obvious
15:41 jballanc: hmm...
15:41 jballanc: end
15:41 drbrain: lrz: ok
15:41 _ko1: !
15:41 lrz: _ko1++
15:41 enebo: ++
15:41 lrz: Proc#binding is a real problem
15:42 tenderlove: [thumbs-up]
15:42 lrz: it makes optimizing local variables very
      hard
15:42 akr_: !
15:42 lrz: i believe headius wrote a blog post about this a
      while ago, anyways, removing it would be a very good
      idea
15:42 lrz: end
15:42 drbrain: zenspider: ok
15:42 zenspider: jballanc: wrt your question in the ticket, I
      think isolated is a fine name. I also like sandbox.
      END
15:42 drbrain: akr_: ok
15:43 akr_: security is not good reason for this issue.
15:43 akr_: There are many way to get objects such as
      ObjectSpace.
15:43 akr_: end.
15:43 drbrain: _ko1: ok
15:43 _ko1: Some others say that isolated binding is frinedly
      for "Proc migration" between processes (advanced
      feature). end
15:44 headius: !
15:44 drbrain: headius: ok
15:44 headius: ObjectSpace is certainly a big security hole,
      but the existence of one hole doesn't mean we shouldn't
      close others
15:44 enebo: !
15:44 jballanc: !
15:44 headius: isolating the binding for a Proc would indeed
      make it easier to migrate/marshal it…that's a pretty
      good case
15:45 headius: if all procs were isolated (no #binding) then
      we could consider the possibility of marshaling procs
      as a top-level standard feature
15:45 headius: end
15:45 drbrain: enebo: ok
15:45 enebo: I just wantd to know how besides OS can you get
      access?
15:45 enebo: end
15:45 enebo: sorry all thumbs
15:45 drbrain: jballanc: ok
15:46 _ko1: !
15:46 jballanc: re: ObjectSpace...my issue with Proc#binding
      is that you can get a hold of locals that are normally
      not available
15:46 jballanc: I don't believe this is possible with
      ObjectSpace
15:46 headius: related to that… OS would also require you to
      recognize an arbitrary string as being the secure data
      you want, as opposed to eval("password",
      proc.binding)
15:46 jballanc: right
15:46 jballanc: also, to _ko1's original proposal, I'd like
      to have more control than just to say "isolated" for
      eval
15:47 jballanc: this could, potentially, eventually replace
      SAFE levels
15:47 jballanc: end
15:47 drbrain: _ko1: ok
15:47 _ko1: Security risk should be discussed separately.  If
      ObjectSpace has, then we need analysis and
      solution.
15:47 _ko1: redmine is nice to discuss.
15:47 _ko1: end
15:47 drbrain: do we need matz_'s opinion?
15:48 drbrain: … I think we are done with this topic,
      though
15:48 jballanc: !
15:48 drbrain: jballanc: ok
15:48 jballanc: ok, so just to close...
15:48 matz_: let us discuss on redmine
15:48 jballanc: yeah, was going to ask...should I make new
      feature proposal?
15:49 jballanc: or continue on _ko1's?
15:49 drbrain: jballanc: yes, please
15:49 headius: +1
15:49 jballanc: will do!
15:49 jballanc: end
15:49 drbrain: ok, now it is open floor time until the end of
      the hour
15:49 zenspider: !
15:49 headius: I want to point out there's a new "Common
      Ruby" project in redmine where this stuff should
      go
15:49 drbrain: zenspider: ok
15:49 enebo: :)
15:49 tenderlove: headius: link?
15:49 zenspider: I'd really like matz to weigh in on a
      backwards incompatibility and change of protocol to
      method_missing / respond_to / respond_to_missing
15:49 zenspider: I did my best to diagram all of the
      different interactions and how they've changed over
      time.
15:49 zenspider: It seems like this change is intentional,
      but undocumented and has unintended consequences.
15:49 zenspider: Requiring a no-op `def respond_to?(*) super
      end` does NOT make sense.
15:49 zenspider: https://bugs.ruby-lang.org/issues/7564
15:49 zenspider: matz?
15:49 headius:
      https://bugs.ruby-lang.org/projects/common-ruby
15:50 zenspider: end
15:50 _ko1: zenspider++
15:50 mrkn is now known as __mrkn__
15:50 zenspider:
15:50 drbrain: matz_: ?
15:50 zenspider: (rawr)
15:51 tenderlove: !
15:51 drbrain: tenderlove: ok
15:51 tenderlove: we're getting close to the end, so just as
      a reminder, make sure to add your email here:
      https://gist.github.com/2c3b41ed83e5d062f636
15:51 tenderlove: (if you're not on the list)
15:51 tenderlove: end
15:51 zenspider: maaaaaaaaaaaaaaatz
15:52 matz_: zenspider: I will see
15:52 tenderlove: burn
15:52 zenspider: laughs
15:52 drbrain: is there any other business?
15:52 headius: hah
15:52 _ko1: !
15:52 drbrain: _ko1: ok
15:52 headius: !
15:53 _ko1: next schedule of this meeting
15:53 _ko1: and 2.1 release.
15:53 _ko1: end
15:53 zenspider: christmas!
15:53 zenspider: LIKE ALWAYS
15:53 drbrain: headius: ok
15:53 tenderlove: !
15:53 headius: Yeah I want schedule for next meeting and 2.1
      as well...
15:54 headius: Also, I wanted to ask about the common ruby
      project in redmine and whether we should actively be
      migrating feature issues there
15:54 headius: obviously new features and feature changes
      should start going there…and we probably should make
      people aware of that
15:54 headius: end
15:54 zenspider: !
15:54 drbrain: for meeting schedule, how about 2 weeks for
      ruby 2.0 issues, and 1 month for other issues?
15:54 unak: headius++
15:54 drbrain: tenderlove: ok
15:55 tenderlove: drbrain: that's what I was going to
      say....
15:55 drbrain: zenspider: ok
15:55 zenspider: drbrain: THERE IS PROTOCOL. YOU CUT!
15:55 headius: that sounds good
15:55 zenspider: end
15:55 enebo: +1
15:55 tenderlove: matz_: does that seem acceptable?  Also, if
      we're meeting about Ruby 2.0, we should make sure that
      mame is here
15:56 tenderlove: so I think we need to make sure we schedule
      with mame's time in mind.
15:56 zenspider: and luis lavena and other core windows
      ppl.
15:56 tenderlove: end
15:56 emboss: !
15:56 zenspider: !
15:56 drbrain: emboss: ok
15:57 emboss: sorry, question already answered. hit the key
      accidentally. end
15:57 drbrain: zenspider: ok
15:57 zenspider: um. tuesdays are hard for me, drbrain, and
      tenderlove. can we do mondays or wednesdays? END
15:57 zenspider: tho... actually I like missing the meeting
      we're currently missing... :)
15:57 drbrain: I can't be moderator tuesday the 29th, I won't
      be able to see
15:57 drbrain: (eye doctor)
15:58 headius: schedule over email thread, perhaps
15:58 tenderlove: headius: yes.
15:58 shugomaeda: mame is not available week days
15:58 drbrain: matz_: is the schedule of two weeks for a ruby
      2.0 meeting acceptable
15:58 headius: I have EU and AU travel in feb
15:58 matz_: Endo-san (mame) has his day job.  So it hard for
      him to attend weekday meeting
15:58 drbrain: I can be moderator on a weekend
15:59 zenspider: what about IU OU and UU?
15:59 headius: required kwargs:
      https://bugs.ruby-lang.org/issues/7701
15:59 drbrain: headius: thank you
15:59 drbrain: ok, let's schedule via an email to
      ruby-core
15:59 sora_h: +1
15:59 matz_: drbrain: acceptable.  Probably we should have
      meeting on weekend
15:59 tenderlove: seems good
15:59 drbrain: ok
15:59 drbrain: this meeting is now closed
```