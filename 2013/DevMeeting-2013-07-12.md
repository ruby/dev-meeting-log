---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-07-12

This meeting will be held on [2013-07-12 at 23:00 UTC](http://everytimezone.com/#2013-7-12,660,6bj) at irc://chat.freenode.net/#ruby-implementers.

EN <=> JA translations will be done on demand in #ruby-ja on ircnet

## Attendees

### MRI

* tenderlove (Aaron Patterson)
* matz
* sorah
* zzak
* mame
* charliesome
* duerst (Martin Dürst)
* emboss (Martin Boßlet)
* kosaki (KOSAKI Motohiro)
* knu (Akinori MUSHA)
* ko1

### Rubinius

* yorickpeterse (Yorick Peterse)

### JRuby

* headius (Charles Nutter)
* enebo (Thomas Enebo)

### MagLev

### MacRuby

* jballanc (Josh Ballanco)

### Topaz

### mruby

## Moderator

* drbrain (Eric Hodel)

## Agenda

We'll keep this meeting to one hour long.

* Introduction (tenderlove)

ADD ITEMS HERE (with redmine links)

* ruby-lang.org docs (zzak) #8636
* documentation commits (zzak)
* internationalized documentation (zzak) #8637
* non blocking reads without exceptions. Feature #5138
* (official) ruby FAQ (knu) #8638
* minitest 4.7.x and test/unit (zenspider)
* Wrap Up

## IRC Log

<pre>16:05 tenderlove: I'll start.
16:05 drbrain: I think we have done this enough that I don't
      need to watch time
16:05 tenderlove: I have to apologize for a couple things
      first
16:05 headius: indeed...I was deep into a bug involving
      ISO-2022-JP....fun
16:05 tenderlove: 1) I've been remiss in scheduling these
      meetings, so I'll try harder. orz
16:06 tenderlove: 2) I should have scheduled around naruse's
      schedule since he is doing the 2.1 management
16:06 tenderlove: so next time I think it would be nice if we
      can get naruse to join
16:06 tenderlove: anyway</pre>

### Documentation hosted on ruby-lang.org (zzak)

<pre>16:06 tenderlove: zzak: your turn!
16:06 tenderlove: ruby-lang.org docs
16:06 mame3: thank you for arranging the meeting!
16:06 zzak: thank you
16:07 zzak: i'd like feedback on putting rdoc up on
      ruby-lang.org, similar to ruby-doc.org, but we can
      maintain
16:07 tenderlove: mame3: no problem!  It's good to get matz_
      live ;-)
16:07 zzak: instead of having to ask james britt for help
      when things break
16:07 zzak: even tho he has been super helpful, it would be
      nice if we could maintain it
16:07 zenspider: yeah. he disappears from time to time
16:07 tenderlove: who is in charge of ruby-lang.org?
      hsbt?
16:07 headius: are we going moderation-free this time?
16:08 drbrain: I think we should consult with James some, as
      he's been doing this for a long time
16:08 drbrain: headius: yes
16:08 zzak: maybe we can add james as ruby-lang.org
      committer?
16:08 headius: +1 to the idea, and I'd like to see
      nightly'ish HEAD rdoc too
16:08 zzak: i wouldnt even mind if it was the same site, just
      on our domain
16:08 drbrain: docs.ruby-lang.org does not currently resolve
      anywhere
16:08 yorickpeterse: +1 for it. I recall ruby-lang had a
      pretty nice docs related page for a while but I'm not
      sure if it's still up
16:09 zenspider: that would be really nice to have
      docs.ruby-lang.org
16:09 zenspider: (again)
16:09 yorickpeterse: either way, it would be a good thing to
      have it in a central location, especially for
      newcomers
16:09 zzak: drbrain: doc.ruby-lang.org is used by
      rurema
16:09 drbrain: that may be too confusing
16:09 zzak: http://doc.ruby-lang.org/ja/
16:10 enebo: docs is what most will try
16:10 nokada: morning
16:10 zzak:  /en just redirects to
      http://www.ruby-lang.org/en/documentation/
16:10 tenderlove: nokada: good morning. :-)
16:10 zzak: id like to keep rurema apart of this
16:10 zzak: since they have worked hard on their side of
      things
16:11 nokada: finished feeding the children
16:11 zenspider: I'm ignorant. what's rurema?
16:11 zzak: hi nobu!
16:11 drbrain: Ruby Reference Manual
16:11 zzak: in japanese
16:11 zenspider: like hacker's guide?
16:11 zzak: api reference
16:11 zenspider: can we make that api.ruby-lang.org ?
16:12 zzak: zenspider:
      http://bugs.ruby-lang.org/projects/rurema
16:12 zzak: docs are generated via api with rdoc
16:12 drbrain: or we can put the rdoc at
      api.ruby-lang.org
16:12 zzak: rurema has their own tules
16:12 zenspider: or even ref.ruby-lang.org
16:12 zzak: tools* haha
16:12 yorickpeterse: rdoc.ruby-lang.org ?
16:12 drbrain: I think we need to involve hsbt and okkez in
      this discussion too
16:13 drbrain: so maybe we should table it?
16:13 zzak: agree
16:13 nokada: +1
16:13 kosaki2: doc or api are better than rdoc.
16:13 mame3: rurema is good, but the primary api doc is rdoc,
      so the place is good for rdoc, i think personally
16:13 enebo: most newcomers will just bookmark from browsing
      front page do I doubt host of domain even matters, but
      yeah all parties should be involved
16:13 enebo: do=so
16:13 kosaki2: because it doesn't maintain rdoc tool
      itself.
16:13 yorickpeterse: Good point
16:14 drbrain: shall we move on to documentation
      commits?
16:14 drbrain: zzak, again
16:14 zzak: ok
16:15 tenderlove: sounds like we need a redmine ticket?
16:15 zzak: tenderlove: yes please, id like more
      feedback
16:15 drbrain: tenderlove: yes, I suppose zzak should file
      it
16:15 zzak: can do</pre>

### Separating "documentation only commits"

<pre>16:16 drbrain: on to "documentation commits" then
16:16 zzak: re: doc commits, i was going to ask about using a
      separate repo for doc only commits, eg
      http://github.com/documenting-ruby/ruby which will be
      merged regularly
16:16 zzak: but im not sure now how i feel about it
16:17 zzak: my hope is to make it easier for contributions to
      docs, and give central location for doc commits
16:17 zenspider: you mean just to be able to open up commits
      to more ppl?
16:17 zzak: when i discussed this with okkez we thought it
      would make it easier for rurema developers to follow
      the documentation changes
16:17 enebo: zzak: Would you flatten those merges?
16:17 zzak: enebo: is that best?
16:18 zenspider: ew. please don't flatten. give credit to
      contributors by allowing their commits to merge
      in
16:18 enebo: zzak: Just thinking outloud but doc-only changes
      might make it easier for other impls to see what has
      changed in code versus documentation
16:18 zzak: the problem is: rurema team has hard time
      following doc changes
16:18 enebo: zenspider: yeah I would not want to give
      credit…just pondering what merging could buy other
      impls
16:18 enebo: err want to give credit :)
16:18 zenspider: hah
16:19 mame3: any convention cannot solve the problem?
16:19 mame3: such as, marking [DOC] in the commit log
16:19 zzak: that is another option
16:20 zzak: separate repo causes more maintenance
16:20 zzak: and what do you do with change log entries?
16:20 zzak: since only committer has access to svn
16:21 tenderlove: I don't know that doc only commits require
      changelog entries :-/
16:21 zenspider: yeah
16:21 yorickpeterse: Having a separate repo could probably
      lead to nasty merge conflicts
16:21 tenderlove: I would just commit doc only with [DOC]
      (like mame says) and no changelog entry
16:21 zenspider: what problem are you trying to solve: get
      more doco contribitutions, make it easier for rurema
      team to follow, or something else?
16:21 enebo: tenderlove: +1
16:22 zzak: zenspider: both, and like enebo said, for other
      impls to separate doc from code change
16:22 tenderlove: (though if you don't add the changelog
      entry, how will you give credit? Just put it in the
      commit message?)
16:22 drbrain: historically I made ChangeLog entries for
      documentation commits
16:22 zzak: same
16:22 drbrain: but due to zzak there seem to be many, many
      more documentation commits now :D
16:22 zzak: only to cover my ass
16:22 enebo: tenderlove: perhaps generate a doc contribution
      list as part of release "Thanks to the kind documenter:
      ..."
16:22 zzak: <3
16:23 zzak: having an identifier in commit message will help
      with generating interesting statistics for sure
16:23 kosaki2: Hmm. I love to see api.ruby-lang.org has rss
      feed for tracking change. [DOC] convension may not work
      because we are human, we can't think all commits have
      proper doc tag.
16:23 yorickpeterse: What's the problem with the current
      documentation setup? As in, why would one consider it
      difficult to document a part of Ruby? Because there's
      lots of C code?
16:23 enebo: kosaki2: Run script to only find commits with
      '#' in changed lines?
16:24 mame3: kosaki: is it really solved by separating
      repo?
16:24 zzak: yorickpeterse: its a confidence thing, some
      people prefer to send me patches privately or via
      documenting-ruby.org
16:24 enebo:  /^\s+#/
16:24 mame3: many existing committers will commit doc patch
      to trunk, instead of the new repo
16:24 kosaki2: mame3: no. I mean we need automatic
      detection.
16:24 kosaki2: enebo: yes, like this.
16:24 headius: something should be diffable along the
      toolchain, no?
16:24 mame3: kosaki2: i know
16:24 headius: yml intermediate format?
16:24 tenderlove: enebo: you need a regexp for C style
      comments too ;-)
16:24 enebo: I think a tool could detect doc commits
      easily
16:25 mame3: oops i see
16:25 enebo: tenderlove: yeah regexps can do anytyhing
16:25 zzak: i dont want to take up a lot of time, so i
      propose we tag doc commits with [DOC] and we can tool
      around this
16:25 enebo: :)
16:25 tenderlove: enebo: /zombocom/
16:25 tenderlove: zzak: sounds good</pre>

### Internationalized Documentation

<pre>16:25 drbrain: ok, shall we move on to "internationalized
      documentation"?
16:25 zzak: ok
16:26 zzak: so i want to add support for embedded
      interationalized docs to ruby
16:26 zzak: here is a demonstration:
      https://gist.github.com/zzak/5986014
16:26 zzak: my idea is based around i18n from rails
16:26 zzak: basically, source docs stay the same, in
      english
16:26 zzak: and we could provide another doc/<lang> directory
      with translations for these methods
16:26 drbrain: Kouhei told me he would work on this at
      RubyKaigi
16:27 yorickpeterse: ! :)
16:27 drbrain: based on similar work he did for yard
16:27 zzak: which rdoc will learn to look for, based on the
      locale, or given flag
16:27 zzak: drbrain: yeh, we discussed this at clearcode
      lunch with okkez
16:27 drbrain: as I recall, his idea was english in .rb and
      .c files
16:27 drbrain: with translated languages in separate
      files
16:27 zzak: but i was 二日酔い
16:27 yorickpeterse: Given you want i18n docs and an easier
      way for people to contribute, wouldn't it possibly be a
      better idea to use something entirely different
      instead? [...]
16:28 tenderlove: zzak: so this would just require a new
      directive in rdoc?
16:28 yorickpeterse: As in, the source code retains whatever
      it has now but there will be some extra
      repository/website that's effectively "The Universal
      Ruby documentation" that would hold true for all
      implementations
16:28 yorickpeterse: (I'm thinking out loud here)
16:29 zzak: tenderlove: i dont think so, because translations
      aren't inline
16:29 yorickpeterse: Given you'd put this in ruby-core I
      think lots of people will have this idea of "Oh, this
      is ruby-core. I have to be very careful with my
      commits" and might be scared off because of that.
16:29 zzak: they are in external files, like i18n
16:29 drbrain: tenderlove: I think only `ri` and the HTML
      generation would need to change
16:29 drbrain: for Kouhei's idea
16:29 zzak: my demo just used DATA for conciseness
16:29 drbrain: I don't have any problem with this idea for
      rdoc
16:30 kosaki2: hmm... if embedded doc has English, Japanese
      and Zulu, only trilingual person can update it?
16:30 tenderlove: kosaki2: I think we only embed
      English
16:30 tenderlove: then store other languages out of
      band
16:30 mame3: kosaki2: the proposal is like ja.po
16:31 tenderlove: sounds like it's really just a little
      update to RDoc, then figure out where to store the lang
      docs?
16:31 kosaki2: understood. thanks.
16:31 zzak: yorickpeterse: so far ruby-lang.org has been
      fairly successful generating translations of the new
      jekyll site
16:31 zzak: from contributors*
16:31 zzak: tenderlove: yes
16:31 yorickpeterse: well that was what I was thinking: pull
      in the core API + user contributed stuff in Jekyll (or
      something along those lines)
16:31 tenderlove: another redmine ticket? ;-)
16:31 zzak: but its up to eric when we release it, maybe
      4.1?
16:31 zzak: rdoc 4.1*
16:32 mame3: I'm not sure if the proposal requires any change
      in ruby
16:32 zzak: we should build rdoc api first
16:32 tenderlove: mame3: I agree.
16:32 zzak: only change to ruby is adding some more
      files
16:32 tenderlove: zzak: possibly
16:32 tenderlove: language storage location sounds like an
      implementation detail. ;-)
16:33 zzak: tenderlove: <3
16:33 zzak: ill open a ticket for this
16:33 drbrain: some tools for translators to track
      documentation updates would be nice, too
16:33 drbrain: those can be built in to rdoc, though
16:33 zzak: that's it for me, thank you so much for your time
      everyone <3<3<3
16:33 drbrain: I believe the next topic is up to
      tenderlove</pre>

### [[Feature #5138]](https://bugs.ruby-lang.org/issues/5138) Non-blocking reads without exceptions.

<pre>16:33 drbrain: non blocking reads without exceptions.
16:33 tenderlove: yay
16:33 tenderlove: yes
16:33 drbrain: https://bugs.ruby-lang.org/issues/5138
16:33 tenderlove: I want this feature to happen
16:34 tenderlove: matz_ seemed positive on the feature
16:34 zenspider: this is the thing that blows out method
      cache?
16:34 tenderlove: I will make another proposal slide for the
      2.1 meeting
16:34 tenderlove: but I would like some feedback from
      matz_
16:34 drbrain: it generates large backtraces that are
      immediately collected
16:34 tenderlove: specifically on method names
16:34 enebo: exceptions are very expensive for jruby
16:34 matz_: Yes, I am positive
16:34 tenderlove: (I would like a higher probability of
      getting accepted this time)
16:34 matz_: I am not excited by names tho
16:35 tenderlove: do you have any ideas on names?
16:35 headius: #read_nonblock?
16:35 matz_: try_*
16:36 enebo: maybe_read :)
16:36 zenspider: *ish
16:36 tenderlove: I don't think get_* will work (as I
      mentioned here:
      https://bugs.ruby-lang.org/issues/5138#note-50 )
16:36 headius: I was half-suggesting adding ? as the
      non-exception version
16:36 kosaki2: I dislike maybe*
16:36 headius: read_nonblock?, write_nonblock?
16:36 mame3: may_i_read_nonblock?
16:36 enebo: sorry I should not joke here
16:36 yorickpeterse: Based on the current standard that would
      imply it were a predicate method
16:36 kosaki2: matz: do you suggest try_read and
      try_write?
16:36 headius: get_ and set_ seem really weird to me
16:37 drbrain: does it need "nonblock" in the name?
16:37 zenspider: I don't like them looking like predicates.
      but this is bikeshed afaiac
16:37 headius: tenderlove pointed out a couple oddities with
      that pattern
16:37 kosaki2: headius: i agree.
16:37 matz_: no, I meant I don't like try_* something
      names
16:37 mame3: headius: read_nonblock is not weird?
16:37 kosaki2: matz: ah, ok.
16:37 headius: read_nonblock_quiet
16:38 tenderlove: how about Read_nonblock()
16:38 headius: read_nonblock!
16:38 kosaki2: tenderlove: why capitalized?
16:38 tenderlove: kosaki2: sorry, it was a joke
16:38 tenderlove: can't joke
16:38 headius: so yeah, this is bikeshed but I like try_ best
      and get_ worst
16:38 yorickpeterse: How many non-blocking methods are there
      currently? If you end up with multiple ones you might
      go for something like
      `IO.open('...').nonblock.read('...')` instead
16:39 yorickpeterse: But that would be a bit inconsistent
      with what's out there currently.
16:39 headius: has anyone considered the option of having a
      flag to turn off exception raising?
16:39 headius: so you'd do io.nonblock_raises = false
16:39 zenspider: that's crazytalk... who wants nice and
      clean?
16:39 tenderlove: headius: that seems good
16:39 mame3: yorickpeterse: the proposal is for performance
      improvement, so creating a new object may matter, i
      guess
16:39 matz_: I am against introducing new state in IO.
16:39 enebo: no sideeffects please :)
16:40 matz_: read_nonblock(exception: fase) may be
      better
16:40 matz_: fase -> false
16:40 enebo: third-party libs passed an io would stop working

16:40 mame3: matz_: really?
16:40 kosaki2: anyway, please write down full doc for that.
      I'm worry about it bring confusion w/ current several
      nonblocking way.
16:40 headius: state and options both have the issue that the
      non-exception version needs to have special return
      values
16:40 tenderlove: read_nonblock(size, exception: false)
      ?
16:40 matz_: mame3: really.
16:40 headius: so passing option changes how return value
      looks
16:40 mame3: the behavior is so different just by adding a
      keyword
16:41 enebo: read_non_block(size, **kwargs_options)
16:41 headius: read_nonblock(size) => EAGAIN where
      read_nonblock(size, exception: false) returns
      :waitreadable
16:41 mame3: it may not be actual matter, but it is
      surprising to me
16:41 enebo: It is explicit
16:41 yorickpeterse: Perhaps an idea to note down all the
      syntax possible variations for this idea in the
      corresponding Redmine issue?
16:41 kosaki2: i like read_nonblock(exception: false)
16:41 yorickpeterse: * possible syntax variations
16:41 headius: perhaps all interested parties should just
      commit to discussing on the bug
16:41 zenspider: yes please
16:41 tenderlove: :-(
16:41 enebo: headius: +1
16:42 tenderlove: the ticket has been open for 2 years
16:42 tenderlove: and I want slides that will actually get
      approved
16:42 headius: if it's just down to names, let's all make it
      work
16:42 enebo: tenderlove: At least everyone here agrees to do
      it now
16:42 headius: yeah
16:42 enebo: tenderlove: we just need to bikeshed the API
      :)
16:43 drbrain: matz_: do you have any more comments on this
      issue?
16:43 headius: if we come up with acceptable names, is this
      ok for 2.1?
16:43 tenderlove: (btw, read_nonblock is already -1
      arity)
16:43 zenspider: ok. so matz is against more IO state, and
      try_* naming.
16:43 zenspider: do we want a new name or an arg?
16:44 matz_: no more. we have fallen in to bikeshed
16:44 tenderlove: :')
16:44 tenderlove: :'(
16:44 zenspider: tenderlove: so we'll do whatever your slide
      say will be done :P
16:44 drbrain: tenderlove: can you update the ticket with our
      current discussion?
16:44 tenderlove: sure
16:44 tenderlove: 負けた
16:44 drbrain: since knu is not here we should move on to
      zenspider
16:44 mame3: akr might have an opinion... but I cannot
      remember right now</pre>

### minitest and test/unit relationship

<pre>16:44 drbrain: minitest 4.7.x and test/unit
16:44 tenderlove: mame3: I will ask him :-)
16:45 drbrain: akr_ is here but idle
16:45 zenspider: can we wake him?
16:45 akr_: I'm neutral, as said in ruby-core.
16:46 mame3: okay, sorry for false alarm
16:46 zenspider: (akr_: thanks for emacsc! I just found
      it!)
16:46 zenspider: drbrain: me?
16:46 drbrain: zenspider: you
16:47 zenspider: so... test/unit subclasses minitest and all
      of the MRI tests use test/unit.
16:47 zenspider: So if I change something in minitest I can
      break all/some of the MRI tests.
16:47 zenspider: But test/unit uses a lot of
      unnecessary/clever mechanisms and makes assumptions
      about minitest's internals, making it hard for me to
      fix if something goes wrong.
16:47 zenspider: I'm open to other solutions, at this point
      I'm considering not updating minitest in MRI anymore
      and leaving it at 4.7.x.
16:47 zenspider: I'm already at 5.0 and it would totally
      break test/unit in MRI.
16:48 zenspider: even tho it is much cleaner and easier to
      extend
16:48 zenspider: I'm also confused as to why test/unit
      installs a test-unit gem spec when it isn't
      test-unit... but that's an aside
16:48 tenderlove: zenspider: you should be test/unit
      maintainer too
16:48 zenspider: NO. I should NOT
16:49 matz_: zenspider: do you mean you will leave the
      bundled on 4.7 forever?
16:49 zenspider: I doubt anything is _forever_... but that's
      what I'm considering
16:49 zenspider: minitest/autorun already does a gem
      activate, so if you have newer minitest installed
      you'll automatically switch over to it
16:50 enebo: zenspider: If you do that will test/unit then
      break?
16:50 enebo: Or does it still use stdlib
16:50 tenderlove: enebo: if you require both at the same
      time, it blows up
16:50 zenspider: right
16:51 zenspider: just like using rspec and minitest/spec at
      the same time :)
16:51 enebo: I see
16:51 zenspider: test/unit does things I cannot comprehend.
      so I would not make a good maintainer for it
16:52 matz_: I see
16:52 zenspider: objections? concerns?
16:52 zenspider: I'd like to see minitest be able to move
      forward over time inside MRI... but I don't see how at
      this point.
16:52 akr_: I couldn't understand the answer to enebo's
      question.
16:52 enebo: I am not on MRI but I see one obivous issue…what
      happens if a serious problem for test/unit is found
      which must be fixed in miniunit?  Forked
      miniunit?
16:53 headius: if minitest is allowed to change to new API,
      test/unit could just bundle current version
      itself
16:53 headius: vendor in a way
16:53 zenspider: akr_: short answer: yes it can break by
      using minitest gem + test/unit
16:53 matz_: who is the original author of test/unit compat
      layer for minitest?
16:53 headius: or is the heirarchical relationship between
      the two considered spec now?
16:53 kosaki2: I doubt we can get a conclusion today about
      this topic. It's really difficult issue.
16:53 akr_: I wrote the compat layer at first.  However nobu
      updated many things.
16:54 tenderlove: can't we just find someone to make
      test/unit work on minitest 5?
16:54 zenspider: matz_: nobu + ... damnit. forgetting his
      name
16:54 zenspider: youngest committer. my brain is broken
16:54 tenderlove: sora
16:54 zenspider: right. thanks.
16:55 headius: zenspider: just use refinements
16:55 matz_: OK, minitest 5 is out there as a gem, right?
      zenspider
16:55 zenspider: enebo: I _doubt_ that situation will come up
      ... I've been doing 4.7.x releases to patch up a couple
      bugs and merging it straight to MRI
16:55 zenspider: matz_: yes
16:56 matz_: so if someone can come up with new compat layer
      for minitest 5, there should be no problem.
16:56 matz_: until that, we can stay 4.7 as a bundled
      version
16:56 mame3: the problem will occur again when minitest 6
      will be released ;-)
16:56 zenspider: matz_: alternative... what if I ported all
      the tests to minitest?
16:57 zenspider: for tests themselves, it is mostly just a
      matter of superclass
16:57 matz_: zenspider: could you port a single test, and
      submit to the redmine please?
16:57 zenspider: sure
16:58 tenderlove: RESOLUTION COMPLETE
16:58 matz_: preferably ported test to work on both 4.7 and
      5.0
16:58 zenspider:
      s/Test::Unit::TestCase/MiniTest::Unit::TestCase/ (for
      4.7). then fix assert_nots
16:58 mame3: well, there is many existing tests written in
      test/unit in the world
16:58 mame3: will they also break?
16:59 zzak: i can cover for knu
16:59 zzak: if there is time after
16:59 zenspider: mame3: yes, there is. I suspect a lot of
      them have either switched to the actual test-unit
      gem
16:59 zenspider: the one that I helped fork into a gem from
      1_8 branch
17:00 akr_: Why ruby itself don't switch to test-unit?
17:00 akr_: Just because no one proposed?
17:00 mame3: can trunk depend on gem?
17:00 zenspider: I don't understand the question, akr_
17:00 tenderlove: (we have about 5 min before our hour is
      up)
17:01 kosaki8: akr: do you mean ruby-trunk bundle test-unit
      again?
17:01 akr_: Yes.
17:01 zenspider: mame3: using gems in stdlib has been
      propesed 20 times. afaik, it has been approved but
      nobody has made it work to approval
17:01 akr_: Not proposal, though.</pre>

### Official Ruby FAQ

<pre>17:01 zzak: re: knu "official ruby FAQ"
      https://twitter.com/knu/status/350245444259033088
17:01 tenderlove: zenspider: no, he means to rebundle
      test/unit
17:01 kosaki8: akr: if someone volunteer to maintain, it's an
      option.
17:01 tenderlove: rather than inherit from m/t
17:01 zenspider: I can table my stuff so zzak can
      finish
17:02 drbrain: ok zzak
17:02 zzak: we just want to introduce doc/faq.rdoc
17:02 zzak: similar to
      http://www.ruby-doc.org/docs/ruby-doc-bundle/FAQ/FAQ.html
17:02 zzak: but maintained by ruby-core
17:02 zenspider: yes please
17:03 mame3: i have never seen the FAQ
17:03 drbrain: I think we should import it to
      ruby-lang.org
17:03 yorickpeterse: +1 for an up to date, maintained
      FAQ
17:04 mame3: +1 for zzak's upcoming hard work
17:04 zzak: w
17:04 tenderlove: +1 for +1s
17:04 zenspider: ((test-unit gem has 1.1m downloads,
      supporting my assertion that test/unit tests have
      switched to gem))
17:04 zzak: drbrain: do you think this overlaps your syntax
      guides too much?
17:04 tenderlove: ;-)
17:05 drbrain: zzak: I don't think so, I think the syntax
      guide should be complete, comprehensive and
      detailed
17:05 drbrain: the FAQ should be the opposite
17:05 zenspider: anything that helps get more eyeballs on it
      and understanding ruby better is welcome
17:05 drbrain: and should reference a more-complete
      document
17:06 tenderlove: we should wrap it up.
17:06 yorickpeterse: the suggestion in that twitter convo
      about the "1.9.1 directory" is actually a good thing to
      put in a FAQ
17:06 zzak: ok, i will import this and work on revising and
      updating to current behavior</pre>

### Ruby developer meeting

<pre>17:06 tenderlove: I guess there is a dev meeting on the 27th
      JST?
17:06 zzak: just call me tenderdoclove
17:07 yorickpeterse: zzak: if you need any help just ping,
      I'm more or less a documentation monster (at least
      around $WORK)
17:07 tenderlove: zzak: lol
17:07 tenderlove: I can't find the ruby-core email.  What is
      the deadline for slides?
17:07 zzak: <3 thanks everyone for your time and
      feedback
17:08 zzak: tenderlove: [ruby-dev:47467]
17:08 zzak: you mean?
17:08 tenderlove: I thought there was one to ruby-core
17:08 tenderlove: not -dev
17:08 akr_: [ruby-core:55689]
17:08 tenderlove: ah, got it
17:08 tenderlove:
      https://bugs.ruby-lang.org/issues/8288
17:09 tenderlove:
      https://bugs.ruby-lang.org/versions/27
17:09 tenderlove: I guess this needs to be updated
17:09 zzak: http://cruby.doorkeeper.jp/events/4906
17:09 zzak: oh ok
17:09 tenderlove: Looks like the meeting is on the 27th, so
      if you have slides for new features then I guess the
      27th JST is the deadline
17:09 headius: I have to run...thanks everyone
17:09 tenderlove: headius: thanks!
17:09 zzak: headius: thank you
17:09 headius has left ()
17:09 tenderlove: official time is over</pre>
