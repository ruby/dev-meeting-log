# DevMeeting-2026-04-21

https://bugs.ruby-lang.org/issues/21916

## DateTime and location

* 2026/04/21 (Tue) *15:00-17:00* JST, in-person @ Hakodate

## Next Date

* 2026/05/13 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Self introduction

- Attendees introduced themselves, and what are they work on.

## Topics

### [[Feature #21871]](https://bugs.ruby-lang.org/issues/21871) Add Module#undef_const (jeremyevans0)

* This was discussed at the February dev meeting, but the Redmine ticket didn't do a good job of explaining why I want this.
* I would like to pitch for this in person and be available to answer questions about it.
* I'll share a couple places where I would like to use this feature, taken from the main application I work on.

Preliminary discussion:

* jeremy: Gave a presentation explaining the motivation.
  * IDOR (insecure direct object reference) vulnerabilities are common in web applications.
  * The cause is often traced to directly calling methods on a model class (top level constant).
  * Preventing constant resolution for the model class inside the code that handles potentially unauthorized access can avoid the vulnerabilities.
  * This pushes the web application developer to use more secure approaches, such as calling methods on already authorized objects.
  * This could also be useful for libraries that enforce boundaries in applications, such as Packwerk.
  * There are a few potential costs: minor performance decrease in dynamic constant resolution, extra code for Ruby implementations to maintain, another feature for Ruby programmers to understand.

Discussion:

* john: Can we get what you want with a private constant? We probably can't.
* alanwu: Can you hack this with an autoload that intentionally fails to load.
* benoit: I hate the old one, which got removed, and had thread-safety problems.
* tenderlove: Do you have to `undef_const` in every module where you DON'T want to have the constant available?
    * jeremy: You could do it via a module mix-in.
    * tenderlove: Sounds like a maintenance problem.
* momchilov: Can you not use anonymous constants?
* ufuk: 
    * This will make static analysis of code really hard.
    * Also, it feels like all these use-cases are already addressed by `Ruby::Box`
* matthewdraper: It feels like the check should happen inside the method that is being called.
    * jeremy: There might be contexts where you might not want to do that.
* ko1: How do you know that a particular constant is a problem that makes you undefine them.
    * jeremy: ORM model classes are generally in this class.
* john: Can you also do this by making the constant deprecated?
* alanwu: This is the ban-list approach, but it is more secure to start from scratch and to build up allowed constants.
* ufuk: What happens with top-level constant references (i.e. `::GithubInstallation`)?
    * jeremy: That would still resolve, but that's not the case I am trying to solve.
* edouard: Can't use static-analysis with an allowlist to enforce this?
    * jeremy: Not a fan of static analysis myself. For a language like Ruby they don't work too well.
* alanwu: It feels like this is missing a bigger feature, like defining some code to run when a constant is referenced in your module.
    * byroot: That could also be used in Zeitwerk, and other tools. Not a bad idea, and almost the same mechanism that autoload uses.

Conclusion:

* matz: `undef` generally is kind of against Liskov Substitution Principle, so I am still not too hot about that. I understand the security concern, but at this point, I am not convinced.

### [[Feature #21962]](https://bugs.ruby-lang.org/issues/21962) Add deep_freeze for recursive freezing (eregon)

* A revised proposal that aims to address the previous discussion.
* It follows the behavior of `Ractor.make_shareable`, and is also consistent with `IceNine.deep_freeze`, so the semantics are already well understood and have existing usage.
* Is `Kernel#deep_freeze` OK? It seems the obvious fit, given `Kernel#freeze`.
* Semantics are are OK?
* I can implement it.

Discussion:

* jeremy: I can understand that `deep_freeze` not deeply freezing, like a `Hash` that has references that are classes, which you don't want to freeze.
    * eregon: You might want class freezing, but you never want transitive class freezing.
* ko1: How about threads?
    * eregon: Yes, we freeze thread objects too.
    * ko1: Do the threads get blocked then?
    * ko1: It feels like this proposal should be much more limited, and not have these concern cases.
* headius: The biggest thing is that JRuby and TR, we see a lot of usages where people are trying to get shareable semantics, but have to use `Ractor.make_shareable`. I would be ok if it was named `Kernel.make_shareable` as well.
* momchilov: What is the proposed relationship between `make_shareable` and `deep_freeze`?
    * eregon: For most common cases, if you `deep_freeze`, then the object would be shareable, but `shareable?` would not return true.
* ufuk: You could define a whole `deep_freeze` protocol and make sure for certain objects that raises (like `Thread`, `IO`). But, maybe there is no reason to create a new protocol, since it sounds like you really want the shareable protocol not anchored to `Ractor`.
* hawthorn: For a lot of use-cases, what you really want is not a protocol on all objects, but probably only defined on collections (like `Array`, `Hash`, etc) and nothing on other objects. You might want deep freeze an array of ORM models, but if that froze the model objects deeply, then it would also freeze the backing cache and other data stores, which isn't the behaviour you want.
* ioquatix: I have come across problems due to mutability in Rack, and am in favor of this. But, I feel like this should be a language level feature, and not something that users opt-in. We don't have the semantic model for a feature like this in Ruby. But, don't let perfect kill the usable by default.
* headius: Why can't we move `make_shareable` under the `Ruby` namespace?
    * byroot: The semantics of `make_shareable` are intimately tied to the implementation of Ractors, especially 

Conclusion:

* matz: I see the need for immutable/frozen objects. Your `deep_freeze` proposal has a line where it won't freeze certain objects. I can deep freeze everything, including classes, and if you have a problem, don't do that.
 
  If deep freeze is simply recursive freeze (handling recursion internally), then I am somewhat open (but not fully). If you have to make choices on what you freeze and what you don't, I am not positive.
  
  I still want performance numbers on how slow it is for people to use `ice_nine`.


### Rewrite Regexp engine (makenowjust)

Preliminary Discussion:

* makenowjust: Gave a presentation on the topic
    * Ruby's Regexp is Onigmo, but Onigmo is an engine not only for Ruby.
    * We need a Ruby-dedicated Regexp engine
    * Onigmo creates issues for Ruby
    * Ruby has 4-stage Regexp parsing, because Onimgo doesn't implement Ruby's Regexp

Discussion:

* makenowjust: I am volunteering to do this work, and have already started on it. Project name is Project Naraku.
* matz: Do you have any specific roadmap?
    * makenowjust: The aim is to get it merged into Ruby core this year. Some parts are implemented.
* eregon: Can you not do certain things with Onigmo and implement the rest in a custom library?
    * makenowjust: We don't want to bring in a large library dependency for just like that. We can fix all the problems.
* alanwu: Is Naraku going to be bigger in code-size and other aspects than Onigmo? The name applies so.
    * makenowjust: The name is derived from Japanese manga where Onigmo and Naraku have a relationship in the story.
* ioquatix: In Ruby's currect regexp VM, we have some memoization optimizations that are helping with security. Can Project Naraku give the same guarantees?
    * makenowjust: I am the person who wrote those optimization and the primary goal of the project is to be able to do more similar optimizations in the first place, and make it even better.
* eregon: Is it memoization in the new one, or is it based on DFA?

Conclusion:

* matz: I support this work, and looking forward to merging it when it is ready to use.

### devmeeting log (ufuk)

* ufuk: Presented the [Ruby Developer Meeting Archive](https://paracycle.github.io/dev-meeting/) website that he built which pulls meeting notes from Github and makes them accessible.

  The Ruby core team should make something this the default interface for meeting notes, since it would make it much more accessible for people who are interested in how these decisions are made.

### [[Feature #20205]](https://bugs.ruby-lang.org/issues/20205) Enable `frozen_string_literal` by default (hsbt)

* progress?

Preliminary discussion:

* matz: Frozen string literals turned out to not have a huge benefit, about 5% performance improvement and not much memory reduction. Half to the community still uses the old behaviour, so adoption has not been as fast as I hoped. This prevents me from making the final decision.

Discussion:

* byroot: Looking at the stats from gems, we should look at how old the gem code is. Additionally, a lot of gems are already compatible, since generally strings are not mutated.

  There is an opt-out that people can use if they want to use the old behaviour, so this is not such a critical decision. I would very much like us to decide on a path forward, and remove this annotation one way or another.
* alanwu: A note on optimizations: If everything is mutable by default, then JIT has to check the object every time, so some optimizations can't be made. Frozen string literals allow the JIT to do extra optimizations, and removing them would remove those. However, I also understand that not everyone uses the JIT.
* headius: Given that chilled strings exist and people can still opt-out of the new behaviour, it sounds like we have plan to move forward to making frozen string literals the default.
* mame: Based on my analysis in 2025, of the latest version of all gems, only 40% of the files had the annotation defined. Jean had already done a research at the time based on files of gem dependencies of `lobsters` app, and about 60% of the files have the annotation, and the app test suite passes with the flag enforced. I personally don't like this feature, but the decision belongs to matz.

Conclusion:

* matz: This is the current situation, I have not made a final decision yet.

## Statements from Matz

Please continue pushing me on the bug tracker, I am open to having my mind changed.
