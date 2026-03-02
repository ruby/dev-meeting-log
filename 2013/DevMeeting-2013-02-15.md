---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-02-15

# DevelopersMeeting20130215

This meeting will be held on [2013-02-15 at 15:00 Pacific Time](http://www.timeanddate.com/worldclock/meetingdetails.html?year=2013&month=2&day=15&hour=23&min=0&sec=0&p1=234&p2=248&p3=48&p4=64)) at ((<URL:irc://chat.freenode.net/#ruby-implementers).

EN <=> JA translations will be done on demand in #ruby-ja on ircnet

## Attendees

### MRI

* tenderlove (Aaron Patterson)
* matz
* mame (Yusuke Endoh)
* mrkn (Kenta Murata), if I can get up early.
* emboss (Martin Boßlet)
* ko1
* marcandre (Marc-André Lafortune)
* eregon (Benoit Daloze)
* kosaki

### Rubinius

* dbussink (Dirkjan Bussink)

### JRuby

* headius (Charles Oliver Nutter)
* enebo (Thomas E. Enebo)

### MagLev

* phlebas (Tim Felgentreff)

### MacRuby

* jballanc (Josh Ballanco)

### Topaz

* Alex_Gaynor

### mruby

* bovi (Daniel Bovensiepen)

## Moderator

* zenspider (Ryan Davis)

## Agenda

We'll keep this meeting to one hour long.

* Introduction (tenderlove)
* Ruby 2 versioning scheme, like 1.x (no number > 9)? Will 2.0.10 or 2.10.0 be allowed?
  * new version number of trunk (2.0.1dev or 2.1.0dev?)
* [Ruby Symbol issues](https://bugs.ruby-lang.org/issues/7839) (tenderlove)
* Should <kbd>YAML.load</kbd> be the safe version by default? (marcandre)
* [Digest::HMAC? Abandon it or make it non-experimental?](https://github.com/ruby/ruby/blob/eb4ae6bc542cdec0ade408c2f5ddfebba227d30f/ext/digest/lib/digest/hmac.rb#L13) (emboss)
* [Get impressions on how to implement "erasing a password from memory"](https://bugs.ruby-lang.org/issues/5741) (emboss)
* <kbd>prepend</kbd> and ancestors, instance_method, etc... (#7836, #7842, #7844)
* invite security ML members, especially non-MRI implementers
* Open Floor
* Wrap Up

## IRC Log

<pre>15:06 zenspider: ok. we start... tenderlove ... intro
      please
15:06 enebo: agreed
15:06 tenderlove: okay
15:06 tenderlove: so
15:06 tenderlove: based on feedback from the last
      meeting
15:07 tenderlove: we're not going to have strict
      moderation
15:07 zenspider: FIFTY FIVE MINUTES REMAINING! :P
15:07 tenderlove: zenspider will moderate, but he's just
      going to make sure that we stay on topic
15:07 tenderlove: and that we don't spend too much time on
      any one topic
15:07 tenderlove: the agenda is here:
      https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20130215
15:08 zenspider: we've got 10 bullet points, so 6 minutes
      per, tho we're starting a bit late
15:08 tenderlove: also based on feedback I got, there should
      be redmine tickets for (most) of these
15:08 zenspider: luckily intro and wrapup are fast
15:08 tenderlove: so we can continue discussion later
15:08 tenderlove: so, that said, let's get started
15:08 tenderlove: I guess Ruby 2 version scheme is
      first
15:08 tenderlove: I don't know who added that though...
15:08 zenspider: topic: Ruby 2 versioning scheme, like 1.x
      (no number > 9)? Will 2.0.10 or 2.10.0 be
      allowed?
15:09 tenderlove: _ko1: ?
15:09 zenspider: I'm not strictly moderating, so speak
      freely
15:09 _ko1: no. but i think it is matz's matter
15:09 zenspider: I'll just keep the topics flowing
15:09 enebo: one question....
15:09 zenspider: we'll move that to the end
15:09 enebo: Is there any techincal reason why 2.10 would
      cause issues with existing software for version
      checks?
15:10 zenspider: (afaik string sorts)
15:10 mame0: it looks just a matter of taste
15:10 enebo: If not then it is political discussion
15:10 mame0: just ask matz!
15:10 enebo: yes
15:10 zenspider: we'll wait for matz and put that later
15:10 zenspider: topic: Ruby Symbol issues (tenderlove)
      http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/52165
15:10 tenderlove: alright, so I asked about a thing to freeze
      / thaw symbol creation
15:11 tenderlove: basically on Rails team we have to play
      "whack a mole"
15:11 tenderlove: finding places where we didn't think
      foreign input could go
15:11 tenderlove: and we happen to be "to_sym" that
      thing
15:11 tenderlove: I proposed a freeze / thaw so we could
      raise an exception
15:11 tenderlove: not 100% sure that's required now
      though
15:11 tenderlove: since ko1 thinks we can GC symbols
15:12 tenderlove: (and I'm going to add a DTrace probe on
      symbol creation)
15:12 tenderlove: basically, we're tired of playing "whack a
      mole" and I'm looking for solutions
15:12 zenspider: I suggested: Symbol.ephemeral { ...symbols
      created here don't go in symbol tree... }
15:12 enebo: use string
15:12 marcandre: Is there a theoretical reason that would
      prevent us from garbage collecting Symbols?
15:12 Alex_Gaynor: id2sym I think?
15:12 _ko1: enebo++
15:13 tenderlove: enebo: ya, I'm in the slooooooowwwwwww
      process of finding all the places we do symbol
      conversion
15:13 tenderlove: and changing to string
15:13 tenderlove: (there's no technical reason we're using
      symbols anywhere in Rails AFAIK)
15:13 Alex_Gaynor: Sorry, _id2ref
15:13 tenderlove: are hash values on symbols cached?
15:13 Alex_Gaynor: Besides that it's just a matter of using a
      weakreference to GC symbols.
15:13 zenspider: speed of key lookups isn't a technical
      reason?
15:14 _ko1: marcandre: for MRI, it is implementation's
      limitation.
15:14 jballanc: could key lookups with frozen strings emulate
      using symbols?
15:14 marcandre: _ko1: which could be, with some work (maybe
      much work) overcome, right?
15:15 tenderlove: jballanc: I think if hashes on frozen
      strings were cached, yes
15:15 _ko1: marcandre: I wrote in Mailing list.
15:15 marcandre: Isn't GC of symbols the safest route? I mean
      maybe Rails can insure that it's ok everywhere, but I
      think *many* apps & libs are using to_sym
15:16 zenspider: lisp has (unintern sym) seems like we could
      benefit from that
15:16 zenspider: we need to move on
15:16 zenspider: topic: Should YAML.load be the safe version
      by default? (marcandre)
15:16 marcandre: The quesiton on my mind is: should the
      default deserialization method YAML.load be safe? The
      downside is that this can create an incompatibility.
      That is also an upside, in the sense that in many cases
      where it does, it would force the question "do you want
      to unserialize anything, or just some tags, etc..." The
      other upside, of course, is to make safe a lot of code
      that might not be. 
15:16 mame0: long! :-)
15:17 Alex_Gaynor: Yes, and the name for the other message
      should *not* be parse(), as I believe JSON uses, it
      should be load and danger_load, or eval_load, or
      something very obvious
15:17 tenderlove: no, it shouldn't be safe by default.  Same
      as Marshal.load
15:17 n0kada-freenode is now known as n0kada
15:18 marcandre: YAML.load! was suggested for unsafe
      version
15:18 emboss: load and load!  ?
15:18 tenderlove: JSON.load is unsafe
15:18 enebo: Yes!
15:18 _ko1: are there any app depends on denger load?
15:18 tenderlove: _ko1: Rails
15:18 enebo: Name should imply danger or power
15:18 _ko1: tenderlove: wow...
15:18 tenderlove: database configs
15:18 tenderlove: fixtures
15:18 tenderlove: serialized columns
15:19 marcandre: If YAML.load should be safe, say in next
      minor, and YAML.load! would be the current unsafe
      version, than a minimal migration path would be to
      alias YAML.load! today. I know it was judged too late
      to add a warning to YAML.load. What about just an
      alias? This would give plenty of time for libraries
      that want to to call load! if available, otherwise
      load, if they decide they should use the "unsafe"
15:19 marcandre:  version. Rails 4.0.0, and update releases
      for 3.2 could change their code today. Anyone upgrading
      Ruby to next minor (say in one year) would not suffer
      any incompatibility, assuming they also have
      upgradedRails at some point in in that year.
15:19 akr_: deep copy using Marshal,
      Marshal.load(Marshal.dump(obj)), is sane.  Rename
      Marshal.load to Marshal.unsafe_load is not fine.
15:19 enebo: Is YAML.eval an insane name…it implies
      evaluation
15:19 marcandre: Let's focus on YAML for now.
15:19 tenderlove: marcandre: if we neuter YAML, why bother
      using it?
15:19 tenderlove: just use JSON
15:20 marcandre: There will still be YAML.load!
15:20 marcandre: The important thing in my mind is the
      default
15:20 marcandre: Just to improve the odds that people using
      the unsafe vrsion know that they are doing it
15:21 enebo: default should be tame and the non-tame should
      be obvious but then again YAML is not obvious from this
      perspective.  Many people probably still don't know
      about !ruby and thel ike.
15:21 marcandre: agreed.
15:21 tenderlove: enebo: ruby objects aren't the only objects
      to be feared
15:21 enebo: tenderlove: no indeed
15:21 zenspider: I think this is a false sense of security.
      libraries need to be audited. pretending they're safe
      because we neuter yaml encourages ignorance
15:22 marcandre: If we agree on YAML.load and YAML.load!, we
      can create an alias in 2.0.0. I don't see the
      downside.
15:22 enebo: just pointing out YAML has surprises most people
      don't understand
15:22 tenderlove: YAML is a human readable Marshal
15:22 marcandre: YAML can also be seen as a powerful
      JSON...
15:22 zenspider: we should move on... but I suspect this
      topic is our most egregious one
15:22 enebo: zenspider: will we have a summary near end of
      hour?
15:23 zenspider: enebo: try to, yeah
15:23 enebo: ok
15:23 tenderlove: marcandre: our embedded JSON has the same
      problem
15:23 zenspider: we'll also have an open question section and
      can revisit
15:23 tenderlove: JSON.load is dangerous
15:23 marcandre: IMO, that's even more troubling.
15:23 zenspider: topic: Digest::HMAC? Abandon it or make it
      non-experimental? (emboss)
15:23 zenspider:
      https://github.com/ruby/ruby/blob/eb4ae6bc542cdec0ade408c2f5ddfebba227d30f/ext/digest/lib/digest/hmac.rb#L13
15:23 mame0: I'm neutral for adding load!, but it first
      requires matz's approval.
15:23 mame0: oops, sorry
15:23 emboss: should be quick: there is Digest::HMAC ? Keep
      it or throw it out?
15:24 emboss: if we keep it, we should review it and remove
      the warning
15:24 jballanc: is there an argument not to keep it in?
15:24 tenderlove: jballanc: the warning?
15:25 zenspider: just did a blame. that warning is 2.5 years
      old
15:25 eregon_ is now known as eregon
15:25 akr_: Who added the warning?
15:25 emboss: it's duplicate functionality with
      OpenSSL::HMAC, although probably not everybody has the
      OpenSSL extension
15:25 akr_: knu?
15:25 zenspider: akr_: knu
15:25 tenderlove: emboss: probably should just ping knu
15:25 tenderlove: he's pretty responsive
15:25 emboss: ok, will do!
15:26 zenspider: topic: Get impressions on how to implement
      "erasing a password from memory" (emboss)
15:26 tenderlove: emboss: he's @knu on twitter
15:26 zenspider: https://bugs.ruby-lang.org/issues/5741
15:26 emboss: ok, the problem is I'd really love to have
      something that allows to clear a string completely in
      memory.
15:27 emboss: use case is: web app receives a submitted
      password.
15:27 zenspider: ROT13!! :P
15:27 emboss: you'd bcrypt it or something and feel safe
      afterwards. but the actual string stays in
      memory...
15:27 kosaki2: it seems to have a long discusstion. I suggest
      you make a summarize for getting matz approval.
15:27 jballanc: is it sufficient to clear the string? or do
      we need control to overwrite explicitly?
15:27 zenspider: emboss: is this just a GC change? or
      something else?
15:27 emboss: lower-level languages allow to overwrite the
      memory, and neglecting to do so often results in CVEs
      or similar.
15:28 tenderlove: clearing the string may not work since
      char* could be shared
15:28 emboss: it's complicated, copying gc could potentially
      proliferate the string to many places. I'm not really
      sure how to approach it...
15:28 enebo: yeah COW is bigger issue for MRI I think
15:28 emboss: I guess it'd be easier in JRuby as the String
      class allows it already?
15:29 enebo: JRuby shares same byte array so we can implement
      a zeroout pretty easily
15:29 mame0: I guess that most people cannot use such a
      feature properly
15:29 enebo: JRuby implements strings using a byte array
      essentially
15:29 akr_: strings are concateneted everywhere.  So it's
      difficult to erase password derived strings.
15:29 mame0: do you have a plan to use it in some frameworks,
      such as rails?
15:29 emboss: i could write an extension, but it's worthless,
      because you'd have to replace every usage of
      String...
15:29 enebo: and we cache a string but yeah Java Strings can
      do it too
15:29 emboss: I think the only way to get people using it is
      by adding it to the Ruby String class :(
15:30 tenderlove: this problem sounds hard
15:30 _ko1: tenderlove: ++
15:30 kosaki2: personally I like this feature (if anyone
      propose a good name)
15:31 emboss: i guess it is, not expecting an answer anyway.
      Just wanted to bring it up, maybe somebody has a good
      idea eventually.
15:31 akr_: For example, net/http send HTTP basic
      authentication base64-encoded password.  It is
      difficult to erase the base64 encoded string from
      outside of net/http.
15:31 tenderlove: I think it's a good feature too, but very
      hard
15:31 mame0: what does "hard" means?
15:31 tenderlove: also if you read a password in from the
      socket, (http post or something) that creates a
      string
15:31 tenderlove: mame0: 難しい
15:31 enebo: Can IO add it? and only get secure string type
      form an IO operation?
15:32 emboss: tenderlove: yes, trying to avoid string is
      almost impossible
15:32 mame0: i know the word itself :-)
15:32 tenderlove: hehehe
15:32 tenderlove: mame0: too many points where strings are
      created
15:32 enebo: That type will not COW but will pretend it is a
      string and have a good erage and not enter
      ObjectSpace?
15:32 mame0: ah i see
15:32 zenspider: time... we'll take it to the ticket url
      above
15:32 zenspider: topic: prepend and ancestors,
      instance_method, etc... (#7836, #7842, #7844)
15:33 marcandre: I'm worried by the current behavior of
      Module.prepend (#7836, #7842, #7844)
15:33 zenspider: https://bugs.ruby-lang.org/issues/7836
15:33 marcandre: Cab we can fix it before releasing 2.0.0? I
      hope so!
15:33 marcandre: I was also wondering if other
      implementations had tackled Module.prepend, what they
      thought of the questions raised by those issues.
15:33 zenspider: https://bugs.ruby-lang.org/issues/7842
15:33 emboss: enebo: wouldn't this still require to replace
      Strings in many places?
15:33 zenspider: https://bugs.ruby-lang.org/issues/7844
15:33 enebo: emboss: yeah you would have to rewrite
      everything in terms of it and it would be fragile
15:33 _ko1: mame0: how about it?
15:34 mame0: I created the tickets for showing my intention
      that it should be fix in next minor :-)
15:34 kosaki2: It seems too late for 2.0.
15:34 mame0: The alternative is making Module#prepend as
      experimental 
15:34 mame0: in 2.0.0
15:34 enebo: Can anyone use a feature if the semantics change
      between minor releases?
15:34 zenspider: (yes please)
15:35 enebo: I suppose prepend will work the same so long as
      it is not complex usage but anything involving modules
      can quickly get complicated
15:36 enebo: taps the mic…is this thing on
15:36 tenderlove: lol
15:36 tenderlove: I haven't investigated prepend at all
15:36 tenderlove: orz
15:36 marcandre: enebo: so JRuby hasn't really looked at it,
      then?
15:36 zenspider: apparently I'm going fast on topic movement.
      hit me up if you think I should revisit something
15:36 zenspider: we've got one more topic + open q's +
      wrapup
15:37 enebo: marcandre: tbh when I looked at those issues I
      realized prepend did not work as I expected
15:37 zenspider: I agree
15:38 jballanc: enebo: I agree, is there a reason "prepend"
      shouldn't just act locally?
15:38 marcandre: Yes!
15:38 n0kada: now trying to fix prepend issues
15:38 enebo: jballanc: I htought it was local to the
      hierearchy of where it was added
15:38 marcandre: At least I see prepend as the old
      alias_method_chain
15:38 mame0: n0kada: thx, but 2.0.0-p0 will not include the
      fixes
15:38 jballanc: yes, that was my understanding too
15:39 marcandre: So it should have a similar effect, i.e. non
      local
15:39 mame0: 2.0.0-pXXX may include, but it is up to
      @nagachika
15:39 zenspider: can we get it marked as experimental?
15:39 zenspider: that seems the safest way?
15:39 marcandre: or as buggy :-)
15:39 n0kada: they will be KNOWNBUGS
15:39 marcandre: ++
15:39 kosaki2: experimental ++
15:39 mame0: knownbugs++
15:40 marcandre: KNOWNBUGS++
15:40 zenspider: both++ ?
15:40 marcandre: If we can at least agree on what it is
      supposed to be...
15:40 akr_: KNOWNBUGS += 1
15:40 tenderlove: enebo: ?
15:40 zenspider: enebo, chime in pls
15:40 enebo: I think experimental
15:40 n0kada: new features often have corner case bugs
15:40 marcandre: So basically use it, and watch out about too
      complex scenarios...
15:40 enebo: 7844 makes it sound like classes will do some
      transitive graph resolution
15:41 mame0: there will be no end if we mark all features as
      experimental just because it has known bugs
15:41 enebo: and I thought it was much much simpler than
      that
15:41 mame0: ruby will be marked as experimental
15:41 tenderlove: I agree with mame0.  All features will end
      up experimental.
15:41 jballanc: it seems that prepend used with classes is
      fairly well understood?
15:41 enebo: I could cope with NKOWNBUGS knowing that this
      conversation will continue
15:42 enebo: My only fear is people will run with the feature
      and then get angry when it changes
15:42 tenderlove: enebo: hopefully listing it in KNOWNBUGS
      would help alleviate that
15:42 enebo: tenderlove: yeah possibly
15:42 zenspider: I think the problem is that there is no
      clearly defined specification.
15:42 zenspider: ok. we should move on
15:42 zenspider: topic: invite security ML members,
      especially non-MRI implementers
15:42 zenspider: I don't know who's topic this is
15:43 zenspider: I for one welcome my new security
      overlords
15:43 tenderlove: in the name of security, do X!
15:43 tenderlove: who added this?
15:44 tenderlove: did the wiki get hacked?
15:44 enebo: why not? apparently there is not too big a
      problem with too many people talking :)
15:44 enebo: Anonymous!
15:44 zenspider: enebo: YOU SHUT IT :P
15:44 enebo: zips it
15:44 zenspider: was this added before martin got added to
      core?
15:44 tenderlove: we've added emboss to the rails sec
      team
15:44 tenderlove: fwiw
15:44 emboss: i have nothing to do with it :)
15:45 tenderlove: emboss: are you on ruby sec team?
15:45 zenspider: emboss: no... I'm saying I think it came up
      because we knew we needed you :)
15:45 emboss: tenderlove: yes
15:45 mame0:
      https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20130215/diff?version=14&version_from=13&commit=View+differences
15:45 emboss: zenspider: thx ;)
15:46 zenspider: anyone else we should add?
      nominations?
15:46 tenderlove: unak!
15:46 mame0: usa-san enjoys holiday
15:46 tenderlove: hahahaha
15:46 tenderlove: he added to the list
15:46 tenderlove: but didn't show up
15:46 tenderlove: very smart move
15:46 zenspider: haha
15:46 zenspider: ok. 
15:46 zenspider: should we revisit any topics? or open to
      questions?
15:46 zenspider: sorry I went so fast... sorta
15:47 marcandre: I propose that Matz be here next time
      ;-)
15:47 enebo: I think we should assemble some Matz questions
      if we can out of this
15:48 tenderlove: ya
15:48 tenderlove: papa matz
15:48 tenderlove: Y U NO ATTEND
15:48 zenspider: yaml, symbols, hmac ejection... 
15:48 zenspider: I'm not sure the yaml one has a ticket, just
      ruby-core thread
15:48 enebo: _ko1: Who can we ask about GC'ing symbols in
      MRI?
15:48 enebo: :)
15:48 zenspider: tenderlove: pls make sure there is a ticket
      and assign to matz for weighin
15:48 jballanc: Just wanted to point to a ticket I had
      created re: binding semantics discussion from last
      meeting -- http://bugs.ruby-lang.org/issues/7747
15:48 _ko1: enebo: nobu
15:48 tenderlove: >_<
15:49 n0kada: _ko1: who's it
15:49 jballanc: ...would love any comments/feedback
15:49 zenspider: NOW he shows up :P
15:49 tenderlove: 50min late
15:49 enebo: get him
15:49 mame0: hello
15:49 tenderlove: haha
15:49 matz_: I am sorry
15:50 zenspider: man.... all this binding / prepend /
      refinements stuff... makes me feel old
15:50 eregon: Was late to the party, but I'm thinking
      changing YAML.load is a bad idea, there will always be
      "dangerous" methods, which are mostly features. And
      "load" is like Kernel#load which is hopefully known to
      be quite powerful. So I'm in favor of something like
      safe_load and documenting well dangerous methods
15:50 zenspider: I want my old ruby back
15:50 zenspider: GET OFF MY LAWN
15:50 eregon: yeah sounds like it went from a tree to a
      complex multi graph
15:51 marcandre: It can still be a tree!
15:51 zenspider: that's a good point about Kernel#load. I
      hadn't made that connection
15:51 eregon: as zenspider said earlier, is there a clear
      spec to wrap thoughts around it?
15:51 jballanc: personally, I would love it if Ruby's
      superclass/module lookup semantics could be implemented
      in Ruby, if only for reference purposes
15:52 zenspider: 8 minutes left
15:52 tenderlove:
      https://bugs.ruby-lang.org/issues/7780
15:52 tenderlove: presumably
15:52 kosaki2: i dislike to use 'safe' for method name. It is
      too unclear.
15:53 zenspider: any more about symbols?
15:54 akr_: load_anyobj
15:54 akr_: just an idea.
15:54 enebo: I think we should use most likely name for safe
      name that a new programmer would think they should
      use
15:55 enebo: which strikes me is load
15:55 mame0: ruby <- dangerous
15:55 enebo: mame0: Danger is its middle name
15:55 n0kada: red is the sign for danger
15:55 zenspider: hah
15:56 zenspider: 5 minutes
15:56 zenspider: 4
15:56 zenspider: should we summarize? we haven't had time in
      any of our previous meetings
15:56 enebo: My only argument for safer default is continued
      security issues because new programmers don't
      understand Yaml is not a better syntax for XML
15:56 zenspider: I think as long as the wiki gets updated
      with links to the issue tracker we don't really need a
      summary here
15:56 enebo: not just a
15:56 _ko1: version number issue?
15:56 zenspider: tenderlove has a ticket for symbols... jsut
      needs to update the wiki
15:57 tenderlove: enebo: we can add documentation
15:57 marcandre: Ideally, Ruby would be dangerous for the
      right reasons. If you want to load code, that *has* to
      be dangerous, by definition. If you want to read JSON,
      it shouldn't be, at least by default. I'll stop
      repeating myself, promised!
15:57 zenspider: _ko1: thank you. yes.
15:57 enebo: tenderlove: at a minimum
15:57 tenderlove: I don't understand why we're treating it
      differently than Marshal
15:57 zenspider: marcandre: that still seems like a "we need
      to audit our libraries" issue more than a marshalling
      issue
15:57 tenderlove: because it's human readable, so you assume
      safe?
15:58 zenspider: do we need a ticket for the versioning
      question?
15:58 Alex_Gaynor has left ()
15:58 zenspider: who's was that?
15:58 enebo: Substitute YAML for COM and Ruby for Windows…not
      try this all again
15:58 enebo: not=now
15:58 zenspider: enebo?
15:58 marcandre: Because it's human readable and used 99.9%
      of cases for basic types.
15:58 enebo: zenspider: I did not add that
15:58 zenspider: hrm...
15:58 tenderlove: marcandre: site your sources :P
15:58 tenderlove: cite
15:59 marcandre: 85% of statistics are made up :-)
15:59 zenspider: I heard it is up to 89%
15:59 zenspider: versioning was drbrain... who isn't actually
      here
15:59 zenspider: so that can be tabled until next
      meeting
15:59 mame0: marcandre suggested "load" for safe and "load!"
      for dangerous in future, and adding an alias "load!" 
      to "load" as a first step in 2.0.0. 
15:59 mame0: matz: what do you think?
16:00 zenspider: ok. let's get the wiki updated with issue
      links and we're pretty much done
16:01 matz_: I think marcandre's idea is good, but not for
      2.0.0 coming in 9 days
16:01 marcandre: Of course, for 2.0.0 would only be adding
      the alias
16:02 mame0: indeed. you are good release manager
16:02 _ko1: mame0: will you write known bug documents?
16:02 _ko1: about prepend
16:03 mame0: where should we write?
16:03 mame0: please contribute!
16:03 matz_: If I were mame0, I'd add load! (or whatever)
      _after_ 2.0.0
16:03 _ko1: yabuhebi (backfire) :(
16:04 mame0: agreed, thx
16:04 enebo: matz_: but an alias from load! to load seems
      fairly harmless?
16:04 tenderlove: enebo: there's currently no safe_load in
      trunk
16:05 zenspider: the alias does seem easy to sneak in for
      2
16:05 enebo: tenderlove: The idea is to promote this for the
      upcoming change assuming making load safe if the
      desired goal
16:06 tenderlove: sounds like we have many volunteers for
      YAML maintenance ;)
16:06 _ko1: meeting is up?
16:06 zenspider: _ko1: yes, meeting is done
16:06 enebo: tenderlove: my patch: alias :load! :load
16:06 _ko1: zenspider: thanks
16:06 zenspider: thanks everyone
16:07 marcandre: thanks
16:07 mame0: thanks</pre>

## Post Meeting Discussion

<pre>16:07 _ko1: matz_: do you think about next version number? /
      release date?
16:07 matz_: Even though in Ruby "!" means "dangerous", bang
      methods in standard library uses it to show side
      effect.
16:07 _ko1: next of 2.0.0
16:07 enebo: matz_: yeah true, but many people now use it to
      mean danger as well outside of core
16:07 jballanc: thanks all
16:08 tenderlove: that seems legitimate.  The side effect of
      loading an arbitrary object is "can eval code"
      ;-)
16:08 _ko1: tenderlove: addd while(1);
16:08 matz_: I understand.  @enebo
16:08 enebo: matz_: we need to add unicode hazard symbol
      perhaps
16:08 tenderlove: hahaha
16:08 matz_: @enebo indeed. LoL
16:09 zenspider has left ("ERC Version 5.3 (IRC client for
      Emacs)")
16:10 kosaki2_: !
16:10 tenderlove: kosaki2_: yes
16:10 kosaki2_: usa-san said me the meanings of topic "invite
      security ML members, especially non-MRI
      implementers"
16:10 kosaki2_: He want to invite non MRI implementers to
      ruby security ML.
16:11 kosaki2_: I fully agree it.
16:11 mame0: who will we invite?
16:11 kosaki2_: I think just current attendee.
16:12 _ko1: (hide)
16:12 tenderlove: kosaki2_: who does he want?
16:12 kosaki2_: If someone interested
16:12 tenderlove: I nominate _ko1
16:12 mame0: っ _ko1
16:12 tenderlove: hahaha
16:12 _ko1: jruby and rubinius guys
16:12 tenderlove: I think Charles is on the ruby sec
      team
16:12 tenderlove: I don't know about rbx guys
16:12 kosaki2_: I think jruby guys already subscribed
16:12 mame0: headius is already the member
16:12 mame0: oops
16:13 _ko1: sorry, i don't know sec ml completely
16:13 mame0: you should join
16:13 mame0: then
16:13 kosaki2_: mame++
16:13 tenderlove: hahaahahahah
16:14 kosaki2_: so plz contact sec ml if other implementers
      are interest to subscribe sec ml. 
16:14 kosaki2_: done
16:14 tenderlove: I'm already on Rails sec team, so I can't
      join
16:14 tenderlove: hahaha
16:14 tenderlove: okay
16:14 _ko1: matz_: (repeat) do you have any thought  about
      next (of 2.0.0) version number? / release date?
16:14 mame0: the next is 2.1.0, okay?
16:15 _ko1: matz_: i want to ask current rough idea.
16:15 emboss: tenderlove: are the other rails sec guys on
      ruby's sec list? I guess it couldn't hurt to know about
      Ruby vulns even from a Rails perspective. Much overlap
      anyway.
16:15 matz_: I think we are going to move on 2.1
      development.
16:15 tenderlove: no
16:16 _ko1: matz_: release date?
16:16 tenderlove: emboss: you're on both, so we will rely on
      you
16:16 mame0: just an idea: 2.1.0 around the Xmas at this
      year
16:16 matz_: And we will release 2.0.1 as a maintenance
      version
16:16 emboss: haha, fair enough ;)
16:16 mame0: too early?
16:17 matz_: mame0: sounds fine
16:17 mame0: great
16:17 _ko1: mame0: you continue 2.1.0 release manager ?
16:17 _ko1: it sounds great
16:18 mame0: if no one is against it
16:19 _ko1: thanks matz, mame.
16:19 _ko1: m&m
16:19 mame0: but I'm busier and busier than before
16:19 mame0: I'm not sure if I have enough time
16:19 emboss: _ko1: m&m -> eminem ;)
16:20 jballanc has left ()
16:21 mame0: is there no topic?
16:21 mame0: the meeting is over, but we still get matz
16:22 _ko1: already finished.
16:22 mame0: we should exploit the chance
16:22 kosaki2_: exploit :)
16:28 _ko1: > 09:15 matz_      > And we will release 2.0.1 as
      a maintenance version
16:28 _ko1: no 2.0.0-pXXX version?
16:28 mame0: I'm not interested in 2.0.1 :-)
16:30 matz_: _ko1: it depends on bugs we will see
16:30 _ko1: matz_: i got it
16:31 mame0: 2.0.0-pXX will be released as before
16:31 mame0: 2.0.1 will be released if needed
16:32 mame0: and we will focus 2.1.0 development towards the
      Xmas
16:32 _ko1: seems good
16:32 mame0: I'll send announcement after 2.0.0-p0
16:32 _ko1: prepend fix -pXX ?
16:33 akr_: We consumes versions more rapidly than 1.x. 
      single digit restriction may be bigger problem.
16:33 mame0: it is up to @nagachika
16:33 mame0: minor release every year
16:33 mame0: 2.1 -> 2013
16:33 _ko1: we have 80 years if we increment minor.
16:33 mame0: 2.2 -> 2014
16:34 mame0: matz's life is so long
16:34 _ko1: we have iPS cells
16:37 mame0: there are only Japanese people
16:38 mame0: they shall now enjoy TGIF
16:38 mame0: jealous
16:38 _ko1: TGIF?
16:38 _ko1: Thank God, It's Friday
16:40 mame0: 2.1.0 should complete refinement
16:40 mame0: hang in there
16:41 _ko1: i bet my 100 JPY coin: we can't wrap up
      refinement in this year
16:45 n0kada: Tgif: a drawing tool
16:48 matz_: I have leave (in reality)
16:48 matz_: I leave my xchat logged in
16:49 _ko1: thanks matz
16:50 mame0: thank you!</pre>
