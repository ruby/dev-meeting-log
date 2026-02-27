---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-08-09

This meeting will be held on [2013-08-09 at 23:00 UTC](http://everytimezone.com/#2013-9-9,660,6bj) at irc://chat.freenode.net/#ruby-implementers.

EN &lt;=&gt; JA translations will be done on demand in #ruby-ja on ircnet

## Attendees

### MRI

* tenderlove (Aaron Patterson)
* matz
* naruse
* charliesome
* ko1 (if I can wakeup)
* emboss (Martin Boßlet)

### Rubinius

### JRuby

* headius (Charles Oliver Nutter)

### MagLev

### MacRuby

### Topaz

### mruby

## Moderator

* drbrain (Eric Hodel)

## Agenda

We'll keep this meeting to one hour long.

* Introduction (tenderlove)
* Frozen string syntax (#8579)
* Hash#+ (#6225)
* Deprecate :: for method calls (#8377)
* Process.clock_gettime (#8658) (tenderlove)
* Wrap Up

## IRC Log

```
17:04 tenderlove: here's the wiki:
      https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeeting20130809
```
### Frozen string syntax (#8579)

```
17:04 drbrain: I guess we start with "frozen string syntax"
      from charliesome
17:04 tenderlove: yep
17:04 drbrain: if anyone else needs voice please /msg
      drbrain
17:04 tenderlove: charliesome: tell us about frozen string
      syntax
17:04 tenderlove: :-)
17:04 charliesome: sure!
17:05 charliesome: So I'd like to see a special syntax in
      Ruby for creating frozen strings
17:05 charliesome: Ruby has mutable strings, which means
      every string literal must be duped when it is
      evaluated
17:06 charliesome: Since most strings are not ever modified,
      this is wasteful
17:06 matz: I like the idea
17:06 charliesome: A frozen string syntax would create the
      string object once, freeze it, and always reuse the
      same object every time
17:06 charliesome: matz: excellent :)
17:07 matz: Notation is the issue
17:07 charliesome: There are three different ideas for syntax
      I've seen
17:07 charliesome: My suggestion: %f(hello world)
17:07 charliesome: _ko10's suggestion: "hello world"f
17:07 charliesome: mame0's suggestion: %q(hello world)o
17:08 charliesome: Obviously I prefer my suggestion :) But we
      should discuss which syntax we should use
17:08 charliesome: Does anyone have any feedback?
17:08 matz: suffix can be used for normal strings, not only %
      quotes
17:09 charliesome: suffix raises a question about how
      interpolation should be handled
17:09 charliesome: when it is used with double quotes
17:10 drbrain: matz: %r%regexp%i allows options
17:10 mame0: as with /reg#{ foo }exp/o ?
17:10 matz: I don't recommend /re/o behavior
17:11 matz: because it requires more complex cache
      mechanism
17:12 charliesome: What if "#{ foo }"f did not perform any
      deduplication
17:12 charliesome: and created a new object every time, but
      frozen
17:12 matz: or we can prohibit string interpolation
17:12 matz: hmm
17:13 mame0: do you want to prohibit /#{ foo }/o too?
17:13 charliesome: If we prohibited interpolation, I see no
      advantage over using %f
17:13 mrkn720_: I want to use 'f' suffix for floating point
      numbers.
17:13 matz: It seems the idea from charliesome better
17:13 charliesome: If we used %f, it is also immediately
      obvious that the string literal is frozen, as f comes
      first
17:13 tenderlo_ is now known as tenderlove
17:14 matz: I mean to interpolate but frozen
17:14 mame0: dwindling notation source
17:14 charliesome: matz: oh I see
17:15 matz: OK,let's give up back tick and use it for frozen
      strings ;-)
17:15 charliesome: matz:
      https://github.com/charliesome/cheap_strings :p
17:16 mame0: awesome
17:16 matz: nice
17:16 nurse: lol
17:16 nokada: frozen backtick :)
17:17 nurse: it should be Ruby 3.0 ;-)
17:17 drbrain: do we need %f and %F (like %q and %Q)?
17:17 matz: probably
17:18 charliesome: matz: did you say you didn't like %f
      ?
17:18 matz: since frozen string is a good idea, I prefer
      concise notation (like suffixes)
17:19 matz: but having two notations for
      allowing/not-allowing interpolation is nice idea
17:19 charliesome: matz: do you have a final decision, or do
      you need time to think?
17:20 matz: so I think we need %f and %F or ""strf and
      'str'f
17:20 matz: I think we need to time to decide the notation,
      or voting?
17:20 matz: I accept the concept anyway
17:21 charliesome: matz: I am ok with voting
17:21 charliesome: I'd be happy with either syntax
17:21 akr_: Most concise notation is '...' itself.  Making it
      frozin is very incompatible, though.
17:21 charliesome: akr_: I don't think we can afford to
      introduce this incompatibility
17:22 tenderlove: should we move to the next topic?
17:22 drbrain: next topic is Hash#+
17:22 drbrain: I don't know who proposed it
17:22 tenderlove: before we move, should charliesome make a
      slide for the next meeting?
17:22 charliesome: tenderlove: what do you mean by
      that?
17:22 tenderlove: it seems matz agrees about the feature, but
      I'm not sure how we'll decide the syntax
17:24 matz: %f and %F are bottom line
17:24 charliesome: I am happy to make a slide
17:24 matz: so if no better idea comes, I will accept
      them
17:24 tenderlove: heh, okay
17:24 charliesome: :)
```
### Hash#+ (#6225)

```
17:24 tenderlove: Hash#+
17:25 tenderlove: I think that was charliesome too?
17:25 charliesome: it was me on behalf of zzak
17:25 matz: why not use Hash#merge ?
17:25 charliesome: There has been lots of discussion on
      redmine, and I believe zzak wanted to seek an official
      decision
17:25 drbrain: yes, I think so:
      https://bugs.ruby-lang.org/issues/6225#note-17
17:26 charliesome: oh ok
17:26 drbrain: oops, #note-15:
      https://bugs.ruby-lang.org/issues/6225#note-15
17:26 charliesome: So it's rejected?
17:28 drbrain: matz: ?
17:29 matz: I guess so.
17:29 charliesome: Ok
17:29 charliesome: Should we move on?
17:29 matz: I still concern about merge is not a mere
      addition
17:30 drbrain: ok
```
### Deprecate :: for method calls (#8377)

```
17:30 drbrain: next is "deprecate :: for method calls" like o
      = Object::new
17:30 tenderlove: we should have the original reporter submit
      a slide for decision
17:30 tenderlove: if they still care about the feature
17:31 drbrain: ok, I will update the issue
17:31 matz: I am not positive
17:31 matz: It's not worth incompatibility
17:32 tenderlove: I don't really use it, but some people
      do
17:32 tenderlove: it doesn't cost anything to leave the way
      it is, does it?
17:32 charliesome: tenderlove: not really, no
17:32 charliesome: The suggestion to deprecate it was mainly
      based on the fact that it is not very commonly
      used
17:33 charliesome: I mainly see it used by beginners where it
      is a source of confusion
17:33 tenderlove: flipflop operator is also not commonly used
       ;-)
17:33 charliesome: I believe there's also a ticket about
      removing flip flop :p
17:33 charliesome: But I see your point
17:33 matz: I shouldn't have added flopflot
17:33 mame0: we should see: grep -r ::[a-z] ruby-trunk/lib |
      wc -l
17:34 mame0: 6617
17:34 nurse: the issue is not for language itself, it's the
      issue for guides or tutorials
17:34 mame0: !?
17:34 akr_: Net::HTTP::Proxy()
17:34 tenderlove: matz: I've used it for text processing. It
      was pretty handy in that case
17:34 tenderlove: nurse: I agree
17:34 nurse: the regexp also hits Foo::BAR
17:34 nurse: no
17:35 charliesome: akr_: cases like this are a good example
      of why it can be confusing
17:35 charliesome: akr_: since ruby rarely requires explicit
      parentheses on calls, and this is one such case where
      you need parens
17:37 mame0: find ruby-trunk -name \*.rb | xargs grep ::[a-z]
      | wc -l  => 1937
17:37 matz: I admit some might confuse, but removing
      everything that can cause confusion is not the goal of
      the language design
17:37 spastorino has left ()
17:37 mame0: impact too big
17:37 akr_: I think you should test the impact in proposal.
      For example, test-all passes without test changes or
      not?
17:37 matz: I declare rejection over removing :: for method
      calls
```
### Process.clock_gettime (#8658) (tenderlove)

```
17:38 drbrain: the last topic is "Process.clock_gettime" by
      tenderlove
17:38 tenderlove: huh
17:38 tenderlove: I think I already discussed this with
      akr_
17:38 tenderlove: I was just wondering what the status was
      since I saw it was discussed at the last in-person
      meeting
17:38 tenderlove: I prefer this over Time#elapsed
17:39 matz: Having this kind of method is OK, but is it
      possible to implement it portably?
17:39 charliesome: Sorry, my wifi dropped out
17:40 akr_: Somewhat.
17:40 charliesome: where were we?
17:40 drbrain: charliesome: portability of
      Process.clock_gettime
17:40 charliesome: drbrain: ah
17:40 akr_: CLOCK_REALTIME works portably.  Other clocks not
      yet.  Some may be possible.  But not all clocks.
17:42 tenderlove: hmmm
17:42 tenderlove: Should I drop the Time#elapsed
      proposal?
17:42 tenderlove: I prefer clock_gettime()
17:43 charliesome: tenderlove: I don't think clock_gettime is
      a very "ruby-ish" method name
17:43 tenderlove: charliesome: I think we can worry about
      method name later
17:43 tenderlove: also, it's a low level API
17:44 matz: it's a mere wrapper for system call
17:44 nurse: major OSes have monotonic clocks, so maintainers
      should implement it.
17:45 nurse: almost all OSes CLOCK_MONOTONIC and there's no
      special works other than darwin and windows
17:45 tenderlove: nurse: should they be implemented *inside*
      clock_gettime(), or do we provide separate methods for
      each
17:45 matz: Does Java platform provide similar
      functionality?
17:45 tenderlove: (given this is a mere system call
      wrapper)
17:45 nurse: yeah, JRuby uses internally
17:46 tenderlove: System.nanoTime() ?
17:47 drbrain: Java has a CLOCK_MONOTONIC if the OS provides
      it:
      http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6458294
17:47 drbrain: yes, via System.nanoTime()
17:48 matz: It's OK to add clock_gettime to CRuby
17:48 drbrain: ok
17:48 tenderlove: alright
17:48 matz: But it will not be a part of *spec*
17:48 tenderlove: I'll review akr_'s patch more closely.
      Maybe I can help somehow
17:48 matz: so other implementation of Ruby may not provide
      it
17:49 drbrain: that is the end of the agenda
```