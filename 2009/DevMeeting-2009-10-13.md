---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2009-10-13

* Place
  * Univ. of Tokyo, Akihabara, Tokyo, Japan
    * [map](http://maps.google.co.jp/maps?hl=en&ie=UTF8&fb=1&split=1&gl=jp&cid=14954413712536862486&li=lmd&ll=35.69919,139.772122&spn=0.002631,0.005659&z=18&iwloc=A)
* Date
  * 2009/10/13 12:00- (JST)
* Attendees
  * matz
  * ko1
  * yugui
  * akr
  * nobu (via Skype)
  * usa (via Skype)
  * naruse

# Agenda

# Log

## Release Schedule

### When will 1.9.2 be released?

* The planned Christmas release date is cancelled [ruby-core:25707]
* "Passing rubyspec" has been added to the requirements for releasing 1.9.2
  * How does this affect the release date?
  * Need to examine the current situation
  * ... looks feasible, perhaps?
  * either way, rubyspec will probably be a bottleneck
* everyone should run rubyspec more often, and report any problems found (problems with ruby or rubspec are welcome)

### The next preview & feature freeze

* release preview 2 around end of October (motion passed)
* Aim for December feature freeze  (motion passed)

## prime.rb

* timeout issue [ruby-dev:39465]
* Timeout is possible in a multitude of places
* is this an actual problem for anyone?
  * a few cases have been reported in ruby-core, what were they doing?
* is there a need for a feature to declare a critical session with a block where Timeouts etc are delayed?
  * could pose a problem if, say, ^Cs were suppressed too
  * perhaps explicitly specify what to delay/suppress?
  * will continue to explore possible solutions.

## Big5

* Big5-HKSCS is done
* Big5-UAO is being worked on by Martin, should be done in a few weeks

## Regarding maintainers

* Process for discharging maintainers [ruby-core:25764] [ruby-dev:39372]
* Some people volunteering to become maintainers: [ruby-core:26066]
  * want to accept to volunteers (matz)
  * but want to avoid the situation where new committers from adding strange, unveiled new features
  * makes sure they know that, as a rule, spec changes must be discussed first
  * however, the libraries they are volunteering to maintain are already stable enough
  * If the motivation behind volunteering as committers is to be able to add new features they want, they are likely to be disappointed. This needs to be considered.
  * there is a tendency for proposals for new features to be ignored because noone has the authority to reject them
  * how about introducing the concept of committers with limited rights?
  * for the moment, add them as contributors to Redmine, and have them close/reject tickets, and see how it turns out (motion passed)
  * the final to decision to accept them as committers will be made by matz

## Regarding inclusion of 'ffi' in the stdlib

* what will be using as 'ffi'?
  * libffi
* will we be including the source of libffi?
  * in the current gem, the source is included. If the environment already has libffi, then that will be used, otherwise the included source is used.
  * if we include the source, should we always prefer that?
* but first, will we really add it to stdlib?
  * there are problems unsolvable with dl2 (discussed later)
  * ffi uses inline assembly which makes it possible to solve those problems
* Regarding currently unsupported platforms
  * currently works on IA64, code also exists
    * information the website is just out of date?
  * Is Windows supported?
    * supposedly works with VC with a patch
    * works on MinGW
    * the new VC (2010?) doesn't have inline assembler, so it will stop working
    * someone needs to work on supporting it, or else need to get rid of VC
  * Once issues on Windows are solved, it will be possible to add to stdlib if a maintainer is found

### The dl2 problem

* it assumes that the types of arguments are consistent, but on ia64, x86_64 etc, doubles/floats can mix so this assumption doesn't hold
* even if the argument type issue is resolved, it's just a bunch of bits in memory when we call, so we can't tell what types are expected at CFunc#call
* plus, information about the arguments isn't available in the CFunc
* in dl1, although there is a precondition that they are on the stack, arguments come in still holding onto type information, so it's doable with some hard work
* ff. gets around the issue with inline assembly

## Filepath related changes  win32-unicode-test

* In the work up till kow, found out that there are major difficulties handling UTF-16LE in ruby itself, so well plan to work via UTF-8
* This means that we need path translation functionality [ruby-dev:39156]
* On unix with default_interenal set, the same problem with path translation exists
* When using a path (when accessing), we stay on the safe side and throw an error
* For now, it's GO
