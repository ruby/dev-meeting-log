# DevMeeting-2025-04-15

https://bugs.ruby-lang.org/issues/21134

## Self introduction



## DateTime and location

* 2025/04/15 (Tue) 16:00-19:00 JST @ Matsuyama

## Next Date

* 2025/05/08 (Thu) 13:00-17:00 JST @ Online

## Announce

### Release managers / Branch maintainers

* Ruby 3.5 (or 4.0?): naruse
  * preview1? -> this week
* Ruby 3.4: k0kubun
    * Shopify Ruby Infrastructure team is helping with maintenance
    * Released 3.4.3 last night
    * Aim to release a new version every 2 months
    * Please file a backport PR, if possible
* Ruby 3.3: nagachika
    * Last week released 3.3.8
    * Try to release every quarter, next release is aimed for July
* Ruby 3.2: hsbt
    * Maintenance status already
    * Aim to release 1-2 versions in the next year
* Ruby 3.1: EOL (2025/03)

## Issues

### [[Feature #21221]](https://bugs.ruby-lang.org/issues/21221) Proposal to upstream ZJIT (maximecb)

* The YJIT team has been working on ZJIT, a more advanced Ruby JIT
* We aim for this JIT to be in a usable state in time for 3.5
* We would like to discuss upstreaming it after RubyKaigi to make development easier

Discussion:

* mame: how to maintain YJIT and ZJIT?
    * YJIT will get bugfixes, but main development will happen on ZJIT
* matz: what is the relationship between YJIT/ZJIT? will they be multi-tiered JITs?
    * we could build a multi-tiered JIT architecture, with interpreter level 0
    * however, that makes the architecture more complex
    * the goal is to make ZJIT do more or less optimization to control compilation time vs optimization
* zzak: how do you make ZJIT configurable like YJIT? and how much memory will it need?
    * it will be similar to YJIT in both respects
* ko1: is ZJIT being developed in Rust? are you not using the infrastructure in LLVM?
    * yes, in Rust again
    * not planning to use LLVM. it is a big dependency, and it doesn't work that well for JITs since its compilation speed is slow.
* ko1: do you make a new loader/linker for persisted code?
    * yes, we will have to build one, since compiled code has a lot of pointers which need to be resolved at load time.
* mame: when we add a new instruction, do we need to add to interpreter, YJIT and ZJIT? that is a lot of overhead? is the plan to remove YJIT?
    * yes, the goal is to remove YJIT. but we want to have it in place for 3.5
* matz: YJIT doesn't work that well with Ractors, do you have any plans to improve that?
    * jhawthorn: we've already improved that situation. there is already a blogpost that shows that YJIT makes Ractors run faster.

Conclusion:

* matz: go ahead!

### [[Feature #21254]](https://bugs.ruby-lang.org/issues/21254) Inline YARV instructions for `Class#new` (tenderlovemaking)

* Patch inlines YARV instructions for calls to `new`
* Allocation performance is very good (24% faster, at minimum but reaches 3x depending on parameters)
* Memory usage increases but not significantly
* `caller` changes inside `initialize`

Discussion:

* tenderlovemaking: [presentation](https://speakerdeck.com/tenderlove/rubykaigi-dev-meeting-2025)
* naruse: can we mimic the stack trace with inlining?
    * headius: we haven't had a problem with this in JRuby, so it shouldn't matter (JRuby doesn't have `Class#new` in the stack traces)
    * byroot: i want the frame gone anyway. it is too noisy, and doesn't add value.
* headius: aren't there other places where we omit stack frames?
    * jhawthorn:
        ```ruby
         # frozen_string_literal: true

         foo = Hash.new { |h,k|
             puts caller
             h[k] = :hi
         } 

         foo["hello"]
         ```
         this code does not push a frame for `Hash#[]` already since we generate an optimized instruction.
* tagomoris: if someone overrides the `new` method, does that result in a different stack trace that shows the user-defined `new` method?
    * yes

Conclusion:

* matz: memory increase is acceptable. go ahead.

### [[Feature #21216]](https://bugs.ruby-lang.org/issues/21216) Implement Set as a core class (jeremyevans0)

* I propose to implement Set as a core class.
* I have a pull request that adds a value-less `st_table` (named `set_table`), for a 33% memory savings.
* Core Set speeds up the majority of Set methods, with 47% of benchmarked cases being at least twice as fast.
* Open questions are:
  * How to expose this in the C-API?
  * Whether to share types with `st.c` (currently, all types are renamed from `st_*` to `set_*`)?
  * Where the code should live (potentially `st.c` if types are shared)?
  * Where to use this internally (potentially, fstring table and proposed refinement call cache)?

Discussion:

* jeremyevans0: Where the code should live (potentially `st.c` if types are shared)?
    * jhawthorn: I think they should all live in the same file, since that would make it easier to change implementation later
* alanwu: does Set preserve insertion order?
    * currently it does since the Hash based implementation does
    * but we could switch it later if we don't care about insertion order
* byroot: let's not allow access to set table internals.
* headius: the big challenge we had when implementing Set in JRuby was things like `#hash`
    * we don't have an ivar named `@hash` but kept the public API compatibility
    * passing all tests, but had to change some tests testing implementation vs behaviour
* akr: if Set is more first-class citizen then developers might have a hard time choosing between Set or Array
* colby: there is a `set` gem published on Rubygems.org. Currently there are 16 dependencies on this gem.
    * The plan is to no-op the gem on Ruby 3.5
    * No need to remove the gem

Conclusion:

* matz: As long as we don't change the return value of existing methods to return `Set` instead of `Array`, and as long as we don't add language features for sets (like Set literals), then I am ok with this implementation.

### [[Feature #21219]](https://bugs.ruby-lang.org/issues/21219) `Object#inspect` accept a list of instance variables to display (byroot)

* Redefining `#inspect` can be important to avoid secret leak or to avoid very large `inspect` representations
* Right now implementing a custom `#inspect` isn't as simple as it seems (e.g. circular references, object_id, etc)
* Should we make it easy to keep the default `#inspect` but hide some instance variables?

Preliminary discussion:

Options:

* `def inspect = super(instance_variables: [:@host, :@user])` (byroot)
* `def pretty_print_instance_variables` (is available now) (mame)
  * `def inspect_instance_variables` ([nobu](https://github.com/ruby/ruby/compare/master...nobu:ruby:inspect_instance_variables?expand=1))
    * mame: it seems prints all ivars.
    * byroot: I think I like this better over my initial proposal. Also backward compatibility is trivial.
* `def inspect_include_variable?(ivar)` (jeremy)
  * matz: negative (too complex on inspect)

Discussion:

* zzak: opt-out seems harder to work with
    * byroot: it is not opt-out, you can return the list of ivar names to print as well
* jhawthorn: there is actually another approach where the Ruby callback method receives a hash of ivar keys and values, so that code can do transformation on printed values, if it wants to
    * byroot: i like that approach even better, but makes the proposal more complex

Conclusion:

* matz: 
    * I agree with the basic idea. 
    * I am not sure how to specify, we have several candidates
    * Keep discussing which approach is best, and I will decide.

### [[Feature #21262]](https://bugs.ruby-lang.org/issues/21262) Proposal: `Ractor::Port` (ko1)

* Considering with [`Channel`](https://bugs.ruby-lang.org/issues/21121), `Port` concept seems better for me.
* I found that `take`/`yield` is so difficult to implement correctly and efficiently enough to support `Ractor.select` in 4 years, so I'm voting to remove them.

Preliminary discussion:

* matz: basically I like it.

Discussion:

* tamagoris: will we ever have bidirectional communication with Ractors?
    * ko1: you can use Ports to send messages between Ractors. Ractor A can send messages to Ractor B's port and vice versa.
* maxbernstein: is Port serializable?
    * ko1: no, since Ractor is not serializable
* AlexMomchilov: Can we use IPC in ports for multi-process communitication (like Erlang)
    * ko1: Right now, it's not supported. But we could make it in the future with same API.

Conclusion:

* matz: go ahead.

### namespace?

