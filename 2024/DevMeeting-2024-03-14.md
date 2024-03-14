# DevMeeting-2024-03-14

https://bugs.ruby-lang.org/issues/20281

## DateTime and location

* 2024/03/14 (Thu) 09:00-12:00, 13:00-17:00 JST @ Online

## Next Date

* 2024/04/17 (Wed) 13:00-17:00 JST @ Online
    * 4/11 is https://2024.rubyconf.au/
    * matz: 4/17 sounds ok

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #20265]](https://bugs.ruby-lang.org/issues/20265) Deprecate and remove rb_newobj and rb_newobj_of (peterzhu2118)

* These two APIs are difficult to use, fragile to use, and requires internal knowledge of internal implementation of data types in Ruby.
* The rb_newobj function creates a T_NONE object. T_NONE objects cannot be marked and are not reclaimed by the GC, which can leak memory.
* The rb_newobj_of function requires the developer to understand flags of objects. Many flags for objects are also not public, preventing direct use by developers.
* Very few C extensions use these APIs, and are from over a decade ago.

Discussion:

* ko1: I read this proposal.  There is no problem.  Is anyone against it?
* ko1: Is this proposal trying to hide the API from public or completely remove them?
* peter: Just trying to make them private.
* ko1: OK.

Conclusion:

* matz: OK. Go ahead


### [[Feature #20306]](https://bugs.ruby-lang.org/issues/20306) Add rb_free_at_exit_p (peterzhu2118 & HParker) (peterzhu2118)

* Ticket #19993 added the new feature RUBY_FREE_AT_EXIT, which frees memory in Ruby at shutdown.
* ruby_vm_at_exit can be used to register a callback for when the VM exits.
* There is no way to check if RUBY_FREE_AT_EXIT is enabled, so this ticket adds rb_free_at_exit_p.

Discussion:

* shyouhei: What is the problem? This seems just okay.
* ko1: The `RUBY_FREE_AT_EXIT` environment variable is an environment variable.  Runtime check is needed.
* ko1: What an extension lib should do if this function returned false?
* peter: This is used from C extensions in their `atexit` (C function) callbacks.  ASAN etc. detect memory leaks because C exts don't tidy up.  We want to have a method to reclaim them.
* ko1: I have no objection except the function's name.
* matz: `rb_free_at_exit_p` sounds fine.
* shyouhei: Is this `rb_` instead of `ruby_`?
* ko1: Other `rb_` series functions cannot be called from inside of an atexit function (execution context is already destroied).  I would say `ruby_` is more appropriate here.
* peter: OK.

Conclusion:

* shyouhei: accepted under `ruby_free_at_exit_p` name.


### [[Feature #20024]](https://bugs.ruby-lang.org/issues/20024) SyntaxError metadata (kddnewton)

* Can we add some information to the syntax error about what kind of error produced it?
* What is a good API? (The discussion on the ticket says a symbol for `:type`.)

Discussion:

* kevin: The problem we are trying to solve is that currently irb etc. parse the message using regular expression.
* kevin: We want to provide location information.
* shyouhei: The problem sounds legit.
* kevin: API proposed (comment #14 in the ticket).  I need feedback.
* ko1: You want to introduce Ruby's API? Nobody would be against adding Ruby level API to get richer information.
* kevin: Exactly.
* mame: IRB case could be fixed by addind additional info into the message. In case of irb, they want to know if the line in question can continue editiing.  In that case recoverable or not is the only information.
* ko1: What is needed here is a machine-readable robust API to know what exactly is the problem
* keven: I also want the error message to be extensible in the future.  My original issue was to change the error message but was rejected like "oh that's backward compatible".
* ko1: Like returning multiple errors at once?
* kevin: Yes.
* matz: Other exceptions are classified by classes but SyntaxError has multiple roles.  That's the point.  yui-knk proposes splitting the classes. this one adds symbols.  Either is okay for me.  I prefer simpler one.
* ko1: Is the API proposed in comment #14 the concrete propsal?
* kevin:  Yes.
* mame: I don't think the API is what irb needs.
* matz: Could be somewhat overkill?
* aaron: There are two problems: one is people tend to test error messages, the other is irb wants to know if the error is recoverable.
* mame: If we provided metadata here I'm afraid we need metadata for everything else.
* aaron: Another question is should error messages be written in English forever? Maybe internationalization could happen later.
* mame: Internationalizing error messages is really a bad idea (it becomes basically impossible to google around).
* matz: Yeah, trust us.  We had that enough.

Conclusion:

* kevin: I'll split the ticket into two: one for recoverability and one for the message itself.

----
* ko1: Can we implement it (=recoverability) collectly?
* kevin: I guess all we need is just mimic irb
* ko1: I remember there are some cases when a ruby program can or cannot recover.
* mame: "Recoverable" terminology could be a bad naming.
* akr: parser-encouneted-syntax-error-at-eof-p, to be precise?

### [[Misc #20238]](https://bugs.ruby-lang.org/issues/20238) Use prism for mk_builtin_loader.rb (kddnewton)

* I would like to propose that we use prism for mk_builtin_loader.rb.
* There are lots of different thoughts on the issue — using bundler/using the Ruby library/using the C library. I would like to get a direction/understanding of what to do.

Discussion:

* kevin: using Prism for mk_builtin_loader benefits when we use newer syntax that BASERUBY does not understand.
* kevin: In order to do so we provide a tiny C program that just converts Ruby program into JSON.
* k0kubun: `make up` creates or downloads what is missing.  That could be a non-problem.
* hsbt: naruse-san is against the idea.
* kevin: If everything is done in C it could be in fact complex.  But generating JSON only could be really small.
* shyouhei: Maybe not the C itself but adding extra complexity to the build system itself is (was) his concern.
* ko1: also naruse-san concerns furure extension to the tool, even if it is small now.
* kevin: No extension.  Just a converted.
* shyouhei: Does this work when cross-compile?
* ko1: This would work because the JSON is machine-agnostic.
* kevin: Yes.

Conclusion:

* kevin: I (or k0kubun) will try to convice others.

### [[Bug #20310]](https://bugs.ruby-lang.org/issues/20310) ASAN fake stacks need to be marked during GC for non-current execution context (kjtsanaktsidis)

* When the GC is marking machine stacks, and ASAN is enabled, it needs to detect pointers on the stack which point to ASAN "fake stack" entries, and mark those "fake stacks" as well. For example, V8 does that here: https://github.com/v8/v8/blob/b639938e99fa6b5ffa9c859b18c72a251fd56942/src/heap/base/stack.cc#L57
* In CRuby, we already do that for the current execution context here: https://github.com/ruby/ruby/blob/23dc7aa2c5a104e73563134a595124804379f049/gc.c#L6401
* However, this is only called for the current execution context (i.e. the thread/fiber which is performing the GC work). Machine stacks for other threads & fibers are not yet marked in this way.
* I propose to unify all the machine stack marking for all kinds of machine stacks into a single function in gc.c, `rb_gc_mark_machine_context`. PR for this is here: https://github.com/ruby/ruby/pull/10122. All machine-stack marking for all fibers/threads flows through the same code path with this PR.
* Alternative approach would be to write a helper for marking ASAN fake stack values, and call it from various places in `cont.c` and `vm.c`.
* I would like a decision on whether the refactoring of machine stack marking into a single function is worthwhile, or whether it's too ASAN specific and I should just add ASAN-related code to each place we do machine stack marking currently.
* (sorry, this is my first dev meeting agenda item, so apologies if this is not the kind of thing discussed in dev meetings!)

Discussion:

* shyouhei: Maybe it is just a good refactoring.
* ko1: I'm not sure how to mark a non-working fiber's machine stack
* KJ: `rb_execution_context_mark` now handles fibers and machine-stacks in the same way.

Conclusion:

* ko1: OK, the pull request seems LGTM.

### [[Feature #20275]](https://bugs.ruby-lang.org/issues/20275) Avoid extra backtrace entries for rescue and ensure (eregon)

* OK to hide/avoid the extra backtrace entries?
* I think it helps to avoid confusion (there is no call semantically for a `rescue` or `ensure` section). e.g. `["-:4:in '<main>'", "-:4:in '<main>'"]` seems unclear (it looks like a recursive call of `<main>`)
* Doing so would make it consistent with other Ruby implementations.

Conclusion:

* matz: OK to remove `rescue in` and `ensure in` lines from the backtrace

----
* ko1: related: There are "block in" prefix.  Should this be retained?

```ruby

1.times{
  raise
}
__END__
t.rb:3:in `block in <main>': unhandled exception
	from <internal:numeric>:237:in `times'
	from t.rb:2:in `<main>'

JRuby's case:
$ RBENV_VERSION=jruby-9.4.5.0 rbenv exec jruby -e '
1.times{
  raise
}
'
RuntimeError: No current exception
  <main> at -e:3
   times at org/jruby/RubyFixnum.java:302
  <main> at -e:2

$?:1
```

* matz: Should be discussed separatedly.
* shyouhei: Are you open to remove or against it?
* matz: I don't want to remove this now, but open for discusssion.

### [[Feature #20261]](https://bugs.ruby-lang.org/issues/20261) Add symbol synonyms for `''` (empty string) and `nil` for IO method line separator arguments (BurdetteLamar)

* For value `''` (read paragraph), add synonym `:paragraph`.
* For value `nil` (read all), add synonym `:slurp`.
* Thus the user can write `gets(:paragraph)` or `gets(:slurp)`, instead of `gets('')` or `gets(nil)`.
* The term _slurp_ is well-established in Perl and Python to mean <i>read all</i>, but a different term can be used in Ruby if there's a better one.

Discussion:

* matz: slurp...
* mame: nobu says these expressions are perl origin (not desinged with readablility in mind)
* matz: definitely better readable but...
* ko1: I guess nobody uses these features.  Is it worth?
* mame: I had a chance to use `''`.

Conclusion:

* matz: Kind of difficult to decide right now.  Let me think about it for a while.

### [[Misc #20287]](https://bugs.ruby-lang.org/issues/20287) DevMeeting before or after RubyKaigi (duerst)

- It would be great to plan ahead, so that people can make their flight/hotel reservations.

Discussion:

* ko1: I booked a meeting room there in 5/14.
* ko1:  BTW there currently is no agenda for the meeting.  Any idea?
* shyouhei: What was disscussed at the last RubyKaigi? I wasn't there.
* ko1: Same as this time (looking at the redmine tickets).  I thought we want to focus more on in-person discussions.

Conclusion:

(Nothing to conclude for this issue)

### [[Feature #20202]](https://bugs.ruby-lang.org/issues/20202) Memoized endless method definitions (matheusrich)

* Do Matz and others have opinions on the syntax?
* If this is approved, should the ivar name match the method named exactly? For example, should it behave like `def foo = (@foo ||= :value)` or should we use special naming conventions like `def foo = (@_foo ||= :value)`?

Discussion:

* matz: This could theoretically possible but I see no use case of it.
* shyouhei: This memoised method definition cannot take parameters.
* aaron: Also it is bad for object shapes.
* KJ: Theoretically it could be possible to reroute object shape issues though.

Conclusion:

* matz: reject. Will reply.

### [[Feature #4247]](https://bugs.ruby-lang.org/issues/4247) New features for Array#sample, Array#choice (matheusrich)

- This is quite old, but I wonder if we wanna move forward with at least some of this. If it is the case we might create a different issue.
- I find weighted sampling very useful, in particular for games. Maybe Hash would be a better candidate than Array?

Preliminary discussion:

* mrkn: This is my opinion about this topic: https://hackmd.io/@mrkn/SJPyb9Uaa
    * Sorry for writing it in Japanese.  I didn't have enough time for translating it in English before this DevMeeting.

Discussion:

(attendee read mrkn's comment)

* mame: Understand there are needs.  But should this be in-core? I think a separate library would be much better.
* shyouhei: Something like topological sort could be thought of.
* mame: In order to properly sample, some preprocess is needed, and that could be O(n).
* akr:  In-core sampling feature and preprocessor library, for instance?
* mame: Wonder what other languages are doing.

(reading python docs)

* akr: problem is if the weights are small integers or floats.  In case of floats the algorithm has to sum up float numbers, which has a pitfall.

Conclusion:

* The request itself is kind of vague.  At least we need more concrete API.  Even when such API is proposed, it would perhaps be better done by a separate gem.

---

* mame: various weighted sampling algorithms without replacement

Naive algorithm
* Preprocess: O(1) if the sum of weights is known, O(n) if not
    * `ary.sample(weights: [Float])` -> O(n)
    * `ary.sample(weights: [Float], total_weight: Float)` -> O(1) (the result is unspecified if total_weight != weights.sum)
* Sampling: O(nk)

Binary search with cumulated weights (available only when k == 1)
* Preprocess: O(1) if cumulated weights are given, O(n) if not
    * `ary.sample(weights: [Float])` -> O(n)
    * `ary.sample(cum_weights: [Float])` -> O(1)
* Sampling: O(log n)

Efraimidis-Spirakis A
* Preprocess: (maybe) the same as Naive algorithm
* Sampling: O(n + k log k)

Efraimidis-Spirakis A-Res
* Preprocess: (maybe) the same as Naive algorithm
* Sampling: O(k log k log (n/k))

Conclusion:

* matz: Ok, reject it once

### [[Feature #13557]](https://bugs.ruby-lang.org/issues/13557) Allow to pass Array of `Backtrace::Location` to `Exception#set_backtrace` (byroot)

- Proposed patch: https://github.com/ruby/ruby/pull/10017
- I think this is better than passing an Array of String because with strings `Exception#backtrace_locations` returns `nil`, making `backtrace_locations` unreliable.
- No objections to the feature or patch?

Discussion:

* shyouheI: I wanted this before (deleting a part of backtrace).
* ko1: Other use case is to switch backtraces between threads.
* mame: We already allow arrays of strings.  There is no downside adding this.
* ko1: Setting backtrace using string does not interface backtrace_locations.  What should hapen for objects?

```ruby
def foo
  raise
end

begin
  foo
rescue => e
  # current behavior
  e.set_backtrace ['bar']
  pp e.backtrace #=> ["bar"]
  pp e.backtrace_locations #=> ["t.rb:3:in `foo'", "t.rb:7:in `<main>'"]
  
  e.set_backtrace(x = caller_locations)
  pp e.backtrace #=> ?
  pp e.backtrace_locations #=> x
end
```

with [PR](https://github.com/ruby/ruby/pull/10017) ([playground](https://ruby.github.io/play-ruby/?run=8039169153)):

```ruby
def foo
  raise
end

begin
  foo
rescue => e
  e.set_backtrace(caller_locations)
  pp e.backtrace            #=> ["-e:5:in '<main>'"]
  pp e.backtrace_locations  #=> ["-e:5:in '<main>'"]
  raise
end

----

def foo
  raise
end

begin
  foo
rescue => e
  e.set_backtrace ["hello"]
  e.set_backtrace(caller_locations)

  pp e.backtrace           #=> ["-e:5:in '<main>'"] override #backtrace
  pp e.backtrace_locations #=> ["-e:5:in '<main>'"]
end

----

def foo
  raise
end

begin
  foo
rescue => e
  e.set_backtrace(caller_locations)
  e.set_backtrace ["hello"]

  pp e.backtrace           #=> ["hello"]
  pp e.backtrace_locations #=> ["-e:5:in '<main>'"] # do not override backtrace_locations
end

----

def foo
  raise
end

begin
  foo
rescue => e
  e.set_backtrace(caller_locations + [""])
end

=begin
-e:8:in 'Exception#set_backtrace': wrong argument type String (expected frame_info) (TypeError)

e.set_backtrace(caller_locations + [""])
^^^^^^^^^^^^^^^^^^^^^^^
from -e:8:in '<main>'
from -e:5:in '<main>'
-e:2:in 'Object#foo': unhandled exception
from -e:6:in '<main>'
=end

----

def foo
  raise
end

begin
  foo
rescue => e
  e.set_backtrace([""] + caller_locations)
end

=begin
-e:8:in 'Exception#set_backtrace': backtrace must be an Array of String or an Array of Thread::Backtrace::Location (TypeError)

e.set_backtrace([""] + caller_locations)
^^^^^^^^^^^^^^^^^^^^^^^
from -e:8:in '<main>'
from -e:5:in '<main>'
-e:2:in 'Object#foo': unhandled exception
from -e:6:in '<main>'
=end
```

* matz: The two exception should be consistent
* mame: Should we separate `set_backtrace` and `set_backtrace_locations`?
* nobu: `Kernel#raise` accepts both an Array of Strings and an Array of Backtrace::Locations as the third argument
* mame: So only `set_backtrace` is enough? Makes sense

```ruby
$l = caller_locations(0)
def foo
  raise RuntimeError, 'bar', $l
end

begin
  foo
rescue => e
  p e
  p e.backtrace           #=> ["-e:1:in '<main>'"]
  p e.backtrace_locations #=> ["-e:1:in '<main>'"]
end
```

Conclusion:

* matz: Please make the exception consistent. Accept other things

### [[Feature #14066]](https://bugs.ruby-lang.org/issues/14066) Add CAA DNS RR on Resolv (dentarg)

* Proposed patch: https://github.com/ruby/ruby/pull/1732
* Is there anything blocking this from being upstreamed into ruby? CAA is a fairly standard and widely adopted DNS record type now.

Conclusion:

* No blocking, merged already.

### [[Feature #20309]](https://bugs.ruby-lang.org/issues/20309) Bundled gems for Ruby 3.5 (hsbt)

* Let's discuss target gems to migrate bundled gems.

Discussion:

* shyouhei: For instance `logger`'s use case could be 99% from rails.  adding it to rails's dependency could just suffice.
* mame: could be annoying to add `gem "open-uri"` everywhere.
* nobu: rubygems itself uses io-console.  Could be hard to remove.
* mame: `ruby -run` is cool, but hard to think of it used in conjunction with `bundle exec...`
* nobu: `bundle exec rake` depends `un`, JFYI.
* hsbt: rdoc could be a big thing we might want to remove next?
* ko1: could be okay to download rdoc right at the moment of `make install`
* mame: who runs the rdoc then? cross compilation could fail.

Conclusion:

(No conclusion for this ticket)

### [[Feature #19744]](https://bugs.ruby-lang.org/issues/19744) Namespace on read (tagomoris)

* The branch under development: https://github.com/tagomoris/ruby/pull/2
* I want to demonstrate the current features and behaviors
* I need feedback about what is missing for further discussions

Discussion:

* moris: We currently can create a separate namespace.  Monkey patch the String class inside of it.  That would not be seen from outside of the namespace.
* matz: Great.
* moris: String works that way.  When it comes to JSON, `class JSON` inside of a namespace creates a separate class instead of reopening outer-space JSON class.
* moris:  This design is because we expect each namespace to call their own `require 'json'`.  There is no require for String, so in-core classes are exceptional.
* mame: You have a list of the exceptional classes?
* moris: No, it's detected runtime.  It collects classes just before evaluation of a ruby program starts.
* ko1: So the object space inside of a namespace is kind of clean, builtin-only environment?
* moris:  Not really.  Libraries required outside of a namespace are in fact visible.
* matz: Do namespaces nest?
* moris: No, namespaces made inside of a namespace are made shiblings.
* moris: Global variables are also namespaced.  They are copy-on-read (process-global variables are copied when a namespace first touches it).
* mame: What about $LOADED_FEATUERS?
* moris: $LOADED_FEATURES is special-cased per-namespace empty array.
* moris:
    ```ruby:inside of a namespace
    module Foo
      def self.foo
        String.new.wow
      end
    end
    
    class String
      def wow = wow
    end
    ```
    This is not supported yet, planned to work.
* moris: What I want to achieve in this feature is to separate a library, which would potentially pollute global states, into a namespace.
----
* mame: performance?
* moris: one-shot check whether we are in a namespace or not is done.  This is a penalty.  Also String monkey-patch is done using Refinements.
* mame: I guess people don't want a new feature when applications that do _not_ use it has performance penalty.


Conclusion:

*


### [[Bug #20300]](https://bugs.ruby-lang.org/issues/20300) Hash: set value and get pre-existing value in one call

Discussion:

* shyouhei: What is the use case of it?
* mame: `Set#add?` below.
* nobu: I personally want this for ENV rather than Hash.  I want to preserve old value.
    ```ruby
    begin
      old = ENV.exchange_value(key, new)
      yield
    ensure
      ENV.set(key, old)
    end
    ```
* akr: ENV's case needs no performance though.
* shyouhei: It is possible to improve Set#add? without this proposal. It is good to add a new element blindly and check the change of size

Conclusion:

* matz: If this is not needed by Set#add?, we need a good use case

### [[Bug #20301]](https://bugs.ruby-lang.org/issues/20301) `Set#add?` does two hash look-ups (jeremyevans0)

* This doesn't seem like a bug to me, as there was never a guarantee of a single hash lookup.
* Fixed proposed is adding Hash#exchange_value and using it.
* Alternative fix would be adding block support to Hash#store, yielding existing value if already set (similar to Hash#update).
* Adding/modifying Hash just for Set seems undesirable, especially as I would like to rewrite Set in C as a core class.

Discussion:

*  shyouhei: why not:
    ```ruby
    def add?(key)
      n = size
      add(key)
      m = size
      return n == m
    end
    ```

Conclusion:

* shyouhei: Will reply.


### [[Bug #19231]](https://bugs.ruby-lang.org/issues/19231) Integer#step and Float::INFINITY - inconsistent behaviour when called with and without a block (jeremyevans0)

* If receiver and step are both integers and end value is a float, can we change to always yielding integers?
* Alternatively, we could just tell users to use `nil` instead of `Float::INFINITY` for the end value.

Discussion:

* shyouhei: What's this?
* mame: mrkn says ArithmeticSequence is doing something.
* nobu: the current behaviour is odd.

Conclusion:

* shyouhei: Let's start from investigating what is going on.


### [[Feature #20331]](https://bugs.ruby-lang.org/issues/20331) Should parser warn hash duplication and when clause? (yui-knk)

* What is the best approach to handle hash duplication warnings and when clause duplication warnings.

Discussion:

* kevin: We should keep these warnings.
* shyouhei: Yeah I'm against removing this but should parser render them...? Because `0x1` vs `0b1` is a semantic comparison.
* aaron: This should be "somebody has to do that".  JRuby uses the parser, LSP usess the parse, if we don't provide the semantics then they have to do them by themselves.

----
* shyouhei: Is this only for integer?
* yui-knk: No, all literals are subject to the duplication check.
* akr: what about just leave the check for compiler and parser does nothing?
* nobu: it's hard for compilers to issue a warning here because compiler needs to reconstruct the original source code from the given AST then.

* matz: Let's warn the duplicated pair that are easy to compare
    * It is okay for each parsers to have different scheme whether they warn a particular situation or not.
    * warn
        *  `{ 111111111111111111111111111 => 1, 111111111111111111111111111 => 2 }`
    * border: leave it up to each parser developer
        *  `{ 1_1 => 1, 11 => 2 }`
    * do not warn: just remove the warning on the following cases
        * `{ "\x00" => 1, "\0" => 2 }`
        *  `{ 1 => 1, 0b1 => 2 }`
        *  `{ 1.0 => 1, 1.00 => 2, 1e0 => 3 }`
* mame: Isn't it hard for everyone (LSP etc.) to have their own routine that warns `{ "\x00" => 1, "\0" => 2 }`?
* shyouhei: I guess nobody particularly uses `1e0` as a key of a Hash.  Having to implement bignums only for that purpose sounds too much.
* matz: I'd also use universal parser for PicoRuby.  A large code base would not be very much amusing to me.

* shyouhei: do not warn `case 1; when 1.0; when 1.00; end`? 

```
ruby 3.3.0dev (2023-09-15T04:27:19Z master fe0225ff4d) [x64-mswin64_140]
t.rb:5: warning: duplicated `when' clause with line 3 is ignored
1
```
```
case 1
when 1
  p 1
when 1
  p 2
end
```
Conclusion:

* Nobody is against suppressing the warning, at the very least.
* It's up to each parsers whether a parser issue a warning or not for each situations.
* matz's mood is it'd be  nice if a parser does very simple comparison like strcmp.

### [[Bug #20090]](https://bugs.ruby-lang.org/issues/20090) Anonymous arguments are now syntax errors in unambiguous cases (matheusrich)

- I'm not sure this is the best place to ask, but can we release 3.3.1? There are a couple of bugs that have been holding me back from updating to 3.3 in some projects. In particular, this one (#20090). It even [affects Rubocop](https://github.com/rubocop/rubocop/issues/12571).
- I know there are also [other bugfixes](https://github.com/ruby/ruby/pull/9371) and [memory](https://github.com/ruby/ruby/pull/9795) [leaks](https://github.com/ruby/ruby/commit/aeffb5e21de6000a3dcfa0ca88c6ba3c3c42d8db) fixed on main right now.

Discussion:

* mame: This issue itself have already been fixed.

Conclusion:

*


## random topics

from nobu

* https://bugs.ruby-lang.org/issues/20329 Clean up `--dump` sub-options
* https://bugs.ruby-lang.org/issues/20307 `Hash#update` from compare_by_identity hash can have unfrozen string keys
* https://bugs.ruby-lang.org/issues/20293 Add `Warning.categories` method that returns the warning category names
* https://bugs.ruby-lang.org/issues/20291 Add `--warning=category` option the same as `-W:category`
* ~~https://bugs.ruby-lang.org/issues/20277 Remove stale `String` test conditionals~~
* https://bugs.ruby-lang.org/issues/20244 Show the conflicting another `chdir` block

from ko1

* https://bugs.ruby-lang.org/issues/15554#note-12 warn/error passing a block to a method which never use a block
    * matz: if it could be detected implicitly it would be nice.  But not always possible.
    * matz: some abuse like intentionally ignoring blocks are prohibited by this.
    * matz: it could be difficult to write "don't bother" methods like passing anything to super.
