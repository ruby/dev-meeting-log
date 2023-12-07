# DevMeeting-2023-11-30

https://bugs.ruby-lang.org/issues/19997

## DateTime and location

* 2023/11/30 (Thu) 13:00-17:00 JST @ Online
* 2023/12/07 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/12/20 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

* RC1 will be released in this week

## Check security tickets

[secret]

## Ordinary tickets

※ Matz happens to be absent this time.  Some issues can be postponed until his come back.

### [[Feature #19993]](https://bugs.ruby-lang.org/issues/19993) Optionally free all memory at exit (HParker & peterzhu2118) (peterzhu2118)

* Environment variable `RB_FREE_ON_SHUTDOWN` to free all memory during shutdown.
* Useful for using memory checkers (e.g. Valgrind, ASAN) to find memory leaks.
* Has found 8 memory leaks in Ruby.
* The feature is incomplete, and is currently experimental (emits an experimental warning when enabled).

Preliminary discussion:

* matz: basically acceptable if it is optional, but the name and API needs consideration
* mame: I have never seen `RB_` prefix in env vars
* ko1: I think `RUBY_` is suitable

Discussion:

* ko1: naming?
* peter: `RB_` was a mistake.
* ko1: other issues?
* peter: The feature is kind of incomplete (for instacne lacks C API for C extensions), can be improved later though.
* matz: `RUBY_FREE_ON_SHUTDOWN` looks good.
* knu: I have no strong opinion, but `SHUTDOWN` is a good word for that? How about `EXIT`?
* mame: `RUBY_FREE_AT_EXIT` or `ON_EXIT`?
* matz: I am ok for both `SHUTDOWN` and `EXIT`
* naruse: `EXIT` sounds better
* znz: There is `Kernel#at_exit` and `trap(:EXIT)`

Conclusion:

* matz: I replied

### [[Feature #11322]](https://bugs.ruby-lang.org/issues/11322) OpenUri: RuntimeError: HTTP redirection loop (hsbt)

* https://github.com/ruby/open-uri/pull/18
* Is `max_redirects` okay for this?

Discussion:

* default value?
* hsbt: 64 shall be an okay value.

Conclusion:

* Pull request approved, will eventually be merged.  Any comments may be posted sooner.


### [[Feature #19998]](https://bugs.ruby-lang.org/issues/19998) Emit deprecation warnings when the old (non-Typed) Data_XXX API is used (byroot)

* The new API is available since 2010, and the old API is officially deprecated since 2015.
* Yet some relatively popular gem maintainers didn't know about it (https://github.com/mudge/re2/pull/116#pullrequestreview-1725029520)
* In most cases the transition is rather straightforward.
* Getting rid of the old API would allow to reclaim 8B in TypedData.
* Could we start emitting deprecation warnings in 3.3 or 3.4 to at least surface the remaining issues?
* I'm happy to help any high profile gem migrate.

Preliminary discussion:

* matz: basically acceptable, I leave details to ko1 and nobu

Discussion:

* shoyuhei: okay for gems, but aren't they used internally?
* ko1: This proposal is to issue warning on build time, which users rarely see. 
* shyouhei: `bundle install` doesn't show warnings.
* ko1: should be acceptable then.

Conclusion:

* ko1: Let's accept the build-time warning.

----

* mame: do we delete this API in future?
* ko1: is that even possible?
* mame: OP wants so.
* ko1: I didn't think so...
* hsbt: Issuing a deprecation warning without deletion plan makes no to little sense.  Maybe something different than deprecation? "Obsoletion" for instance?

* ko1: I will reply

### [[Feature #20018]](https://bugs.ruby-lang.org/issues/20018) Get the error codes as Socket::EAI_XXX when getaddrinfo and getnameinfo fail (shioi)

review wanted for https://github.com/ruby/ruby/pull/9018

Discussion:

* nobody is strongly against it.
* akr: It seems fine
* akr: I guess Socket::ResolutionError.new(mesg, error_code) may be proposed in future.
* akr: It is not required now, though.
* mame: Merged
* nobu: The test assetsion need to be tweaked. I will do

### [[Misc #20015]](https://bugs.ruby-lang.org/issues/20015) Privacy policy for ruby-lang.org (hsbt)

* I would like to review https://github.com/ruby/www.ruby-lang.org/pull/3144

Discussion:

* naruse: nothing particular to add.

Conclusion:

* shyouhei: Let's merge it.


### [[Feature #20005]](https://bugs.ruby-lang.org/issues/20005) Add C API to return symbols of native extensions resolved from features (tagomoris)

* The new C API for extensions, to resolve&return symbols of other extensions
* https://github.com/ruby/ruby/pull/8991 (tests are not written yet)
* This provides a better way to check whether a dependency extension is correctly loaded previously, or not (than call-and-crash trial)
* #19744 (or similar namespace/package-like features) requires extensions to access symbols of other extensions via this (or similar) API
* So if this gets merged into Ruby earlier, extension maintainers can start the work to support namespace/package features earlier

Preliminary discussion:

* matz: sounds okay

Discussion:

* mame: This can also be used for non-function pointers.
* nobu: On windows functions and others are separated.
* mame: Retutning `void*` is just okay I think.
* mame: matz prefers rb_ext_ over rb_dln_ prefix though.

Conclusion:

* matz: Accepted. I will reply

### [[Bug #20008]](https://bugs.ruby-lang.org/issues/20008) f(**kw, &block) calls block.to_proc before kw.to_hash (jeremyevans0)

* I think this is a bug as it goes against expected evaluation order.
* Is fixing this using a new VM instruction and the peephole optimizer acceptable?

Preliminary discussion:

* ko1: Adding a new insn is ok, but using the peephole optimizer to fix the issue is not good, I think

Discussion:

* mame: ko1 concerns the fix (doing this in peephole optimizer seems strange).
* mame: the problem itself is legit.

Conclusion:

* nobu: should be okay to fix this as a bug -- once a pull request passes reviews.


### [[Bug #20012]](https://bugs.ruby-lang.org/issues/20012) Fix keyword splat passing as regular argument (jeremyevans0)

* Should we fix this for Ruby 3.3, or wait until Ruby 3.4 (my preference would be 3.3)?
* Should we backport this fix to Ruby 3.2 or 3.1 (my preference would be both)?

Preliminary discussion:

```ruby
def f(obj)
  obj[:b] = 2
end
h = { a: 1 }
f(**h)
p h  #=> {:a=>1, :b=>2}
```

Discussion:

* mame: this is clearly a bug.

Conclusion:

* matz: should be fixed soon.


### [[Bug #18886]](https://bugs.ruby-lang.org/issues/18886) Struct aref and aset don't trigger any tracepoints. (jeremyevans0)

* I implemented support using the same approach used for attr_{reader,writer}.
* Is the pull request acceptable?

Preliminary discussion:

* mame: benchmark results is welcome.

Discussion:

* shyouhei: Struct aref is heavily optimised.
* mame: ko1 wants to know how slow the pull request is when tracepoint is _disabled_.

Conclusion:

* matz: Go ahead
* ko1: I will reply


### [[Bug #19779]](https://bugs.ruby-lang.org/issues/19779) `eval "return"` at top level raises `LocalJumpError` (jeremyevans0)

* I developed a pull request to fix this.
* Is the pull request acceptable?

Conclusion:

* matz: Go ahead


### [[Feature #20011]](https://bugs.ruby-lang.org/issues/20011) Reduce implicit array allocations on caller side of method calling (jeremyevans0)

* Some of these optimizations have the same theoretical issues as `f(*a, &lvar)` and `f(*a, &@ivar)` currently.
* I think the benefit of the optimizations exceeds the costs (unexpected behavior in pathological cases).
* Are these optimizations acceptable?
* If not acceptable, should we fix the theoretical issues with `f(*a, &lvar)` and `f(*a, &@ivar)`, at the cost of performance?

Discussion:

* mame: seems ad-hoc to me.
* nobu: doing this one in peephole optimisation is right.

Conclusion:

* mame: up to ko1.


### [[Misc #19980]](https://bugs.ruby-lang.org/issues/19980) Is the Ruby 3.3 ABI frozen? (flavorjones)

* Precompiled native gems (nokogiri, sqlite3, etc.) have always released support for a new version of Ruby many days or weeks after Ruby's release, slowing adoption of Ruby in some cases.
* rake-compiler-dock v1.4.0.rc1 has been released with support for Ruby 3.3.0-preview3, primarily for testing by gem maintainers.
* I would like to ship a final release of rake-compiler-dock as early as possible to allow gem maintainers to release native gems that support Ruby 3.3 before the 3.3.0 final release. When can I do this safely?

Discussion:

* shyouhei: basically we want this?
* naruse: We are currently planning to basically controll what is comitted under `include/`

Conclusion:

* naruse: Let me have some time, I'm accommodating to achieve this goal.


### [[Feature #20024]](https://bugs.ruby-lang.org/issues/20024) SyntaxError subclasses (kddnewton)

* Many tools rely on the specific error message coming from syntax errors.
* Many specs require a specific error message for syntax errors.
* It seems like the error message is hiding a "type" for syntax errors. Could we subclass SyntaxError to make this interface easier on the consumer? It would allow us to change the error message in the future, and also allow a nicer interface for tooling.

Preliminary discussion:

* examples
    * Invalid break
    * unexpected node
    * can't make alias
    * Invalid escape
* matz: I like all-lowercase message

Discussion:

* mame: asked matz and he has no strong objecton.
* naruse: yui-knk says there could be several syntax errors in a single exception.
* knu: Making this happen would effectively make how to parse a ruby script (then fails) a part of the language, which could make parsers difficult?
* naruse: It already is. irb is parsing the error message...

Conclusion:

* keep discussion


### [[Feature #18980]](https://bugs.ruby-lang.org/issues/18980) Re-reconsider numbered parameters: `it` as a default block parameter (k0kubun)

* Reminder to resume the discussion from the previous dev-meeting.
* gem-codesearch results: https://gist.github.com/k0kubun/7e143189365a5ca8593b7e4fd0bb3c0e

Discussion:

* shyouhei: Everybody except matz is okay with this...
* k0kubun: Searched for rubygems, there _ware_ several gems which define `Kernel#it`.  They are all ancient ones that seems dead I believe.
* k0kubun: `{ _1; it }` should either be an error, or both work.

```ruby=
# Ruby 3.4
def foo
  it #=> method call
  _1 #=> method call
  1.times do
    p it #=> 0
    it = "foo"
    p it #=> "foo"

    p _1 #=> 0
    _1 = "foo" # Syntax Error
    p _1 #=> N/A

    p foo #=> method call
    foo = 1
    p foo #=> local var

    it "foo" do # method call (rspec)
    end
  end
  1.times do ||
    p _1 # Syntax Error
    it # method call
  end
  1.times do
    ["Foo"].any? {|| it } # method call
  end
  yield_1_and_2 do # yield 1, 2
    p _1 #=> 1
    p it #=> 1
  end
  yield_ary do # yield [1, 2]
    p _1 #=> [1, 2]
    p it #=> [1, 2]
  end
  1.times do
    p [_1, it] # Syntax Error
    p [_2, it] # Syntax Error
  end
end
```

Conclusion:

* matz: accept `it` on Ruby 3.4.
  * ruby 3.3 will warn and ruby 3.4 will use the new semantics
  * The warning should be printed always (even without $VERBOSE)

### [[Bug #19877]](https://bugs.ruby-lang.org/issues/19877) Non intuitive behavior of syntax only applied to literal value (kddnewton)

* `if (1; cond1..cond2); end` becomes a flip-flop, because `1` and `()` are dropped
* `(1; (2; 3; (4; /(?<a>)/))) =~ s` assigns to `a`, because integers and `()` are dropped
* This behavior seems unintentional and confusing. You have to know which values will be dropped in order to know if it's going to become a flip-flop/named capture. No tooling parsers do this (I checked prism, parser, ruby_parser, lib-ruby-parser, and syntax tree). It appears that some implementations do (JRuby/TruffleRuby) but not others (Natalie, artichoke).
* There has previously been discussion of adding these nodes back into the tree for universal_parser. That would probably mean this behavior would change, since they would now be in the parse tree.
* I would like to propose requiring that to be a named capture assignment that it be the only part of the expression, and for a flip-flop the same except that it follows `and`/`or` like it currently does.

Discussion:

* mame: is `if (cond1..cond2)` a flip-flop?  Having a smart distinction is not that easy.

```ruby=
expr if (cond1..cond2) # a flip-flop
expr if (1; cond1..cond2) # a flip-flop
expr if (method; cond1..cond2) # also a flip-flop
expr if (method; (mmm; cond1..cond2)) # also a flip-flop
expr if begin cond1..cond2; end # not a flip-flop
```

* knu: is there no warning for that `1;`?
* nobu: _that_ should cause a warning (literal in void context). But for methods there are nothing.
* knu: placing a range in a `if` effectively acknowledges that the author knows what a flip-flop is ... Okay, no that's too harsh.

(looking at the implementation why this happens)

```ruby=
(/(?<a>)/) =~ s # do not define a
```

* matz: `if (foo; cond1..cond2)` should not be a flip-flop
* matz: `if (cond1..cond2)` should be a flip-flop

```ruby=
# matz's intention
expr if cond1..cond2

expr if (cond1..cond2)                # a flip-flop
expr if (((cond1..cond2)))            # a flip-flop
expr if (;;; cond1..cond2)            # Don't care
expr if (1; cond1..cond2)             # NOT a flip-flop
expr if (method; cond1..cond2)        # NOT a flip-flop
expr if (method; (mmm; cond1..cond2)) # NOT a flip-flop
expr if begin cond1..cond2; end       # a flip-flop
```

* matz: I approve master's behavior for `/(?<a>)/ =~ s;`

```ruby=
/(?<a>)/ =~ s;        # a is defined

(111; /(?<a>)/) =~ s; # 3.2: a is defined,     master: a is not defined
(foo; /(?<a>)/) =~ s; # 3.2: a is not defined, master: a is not defined
(/(?<a>)/) =~ s       # 3.2: a is defined,     master: a is not defined
```

Conclusion:

* matz: mame please write it


### [[Bug #20025]](https://bugs.ruby-lang.org/issues/20025) Parsing identifiers/constants is case-folding dependent (kddnewton)

* Codepoint 0xB5 in Windows-1253 encoding is parsed as a constant, not an identifier, even though it is a lowercase codepoint
* Would it be okay to check the [[:upper:]] ctype instead of case-folding to determine if a codepoint is uppercase?

Preliminary discussion:

*

Discussion:

* nobu: I have already fixed this.
* naruse: The fix breaks compatibility.
* naruse: rb_enc_isupper / islower can be missing on encodings
* nobu: I guess not, POSIX-defined APIs are everywhere.
* mame: Windows-1253 has uppercase "μ", which has been a local variable for us, would be a constant with this patch.

```
irb(main):001> "µ" == "μ"
=> false
irb(main):002> "µμ".unpack("U*")
=> [181, 956]
irb(main):003>
```

```
irb(main):001> s = "µ"
=> "µ"
irb(main):002> s.unpack("U")
=> [181]
irb(main):003> s.upcase
=> "Μ"
irb(main):004> s.upcase.downcase
=> "μ"
irb(main):005> s.upcase.downcase.unpack("U")
=> [956]
```

Conclusion:

* naruse: Let me take a look.


### [[Bug #19392]](https://bugs.ruby-lang.org/issues/19392) Endless method vs `and`/`or` (zverok)

* Not only `and`/`or` are affected, but also trailing `if`/`unless`: #19392#note-7
* When met with the behavior, it is very hard for users to diagnose the problem: #19731 which is a serious impediment in feature usage;
* With all due respect, I don't think that closing the issue was justified (the provided justification was apparently assuming the ticket requires change of `and`/`or` priorities, not endless method behavior);
* After its closing, me and @Eregon provided detailed justifications about its importance (#19392#note-10, #19392#note-13);
* The patch was **provided** by @yui-knk #19392#note-12 - https://github.com/ruby/ruby/pull/8054. As far as I can see, it was approved by @nobu.

Discussion:

* yui-knk: because nobu said "there are technical difficulties", I accepted the challenge and created the patch, but I didn't say I liked it.
* akr: Because `a = b and c` is parsed as `(a = b) and c`, I expect `def a = b and c` to be `(def a = b) and c`.
* naruse: akr's intuition is the same as matz's comment #9 https://bugs.ruby-lang.org/issues/19392#note-9
* naruse: Does anyone actually have a problem with this?
* mame: The first example mentioned in the ticket is a tweet as a "Funny thing" https://twitter.com/lucianghinda/status/1617783952353406977
* mame: It would be possible for Rubocop to warn the pattern.

```ruby=
 def foo =  c1?  and c2?
(def foo =  c1?) and c2? # akr's intuition
 def foo = (c1?  and c2?)

 foo = c1?  and c2?
(foo = c1?) and c2?

 def foo =  1  if cond?
(def foo =  1) if cond?  # akr's intuition
 def foo = (1  if cond?)
```

```c
%nonassoc  modifier_if modifier_unless modifier_while modifier_until keyword_in
%left  keyword_or keyword_and
%right keyword_not
%nonassoc keyword_defined
%right '=' tOP_ASGN
```

Conclusion:

* matz: no change

### [[Bug #20044]](https://bugs.ruby-lang.org/issues/20044) Add runtime flag and environment variable for prism (kddnewton)

* We would like to add a runtime flag and environment variable to activate prism
* We would like to mark it as experimental, so that it could be removed easily in the future
* We would like to get this in for 3.3 so that we can test it on a stable branch in our CI
* Is the naming/API okay?

Discussion:

* matz: a command line argument is enough? envvar is really needed? I don't thik so
* hsbt: If `--prism` is introduced, `--ripper` or something is also introduced?
* nobu: `--parser=prism` and `--parser=old`
* ko1: `--enable-prism`, `--enable-prism-parser`
* matz: Let's introduce `--parser=prism`.
  * `--parser=yyparse` `--parser=lrama`...
* mame: should the option print a warning or something?
* ko1: How about including "+prism" to `RUBY_DESCRIPTION`?
    * `ruby 3.3.0dev (2023-12-07T02:37:08Z master 214f6d6598) +prism [x86_64-linux]`
* ko1: `+prism` or `+PRISM`?

```
[master]$ ruby --jit --parser=prism -v
ruby 3.2.2 (2023-09-30 revision 2d6067dc7d) +YJIT +prism [x86_64-linux]
```

* matz: do we need to show this kind of information? User specifies `--parser=prism` option so there is no trouble without information.
* mame: There is no way to check this process uses prism or not. It can become trouble on production or CI.
* matz: warning is too much.
* hsbt: On ruby 3.3 it is reasonable to show warnings because it is experimental.
* matz: to try this feature warning becomes troubles.

Conclusion:

* matz: Ok, introduce only `--parser=` command line option.
    * `--parser=prism`
    * `--parser=parse.y`
    * The option will remain in the future
        * if parse.y is removed in the future, `--parser=parse.y` should report an error
* matz: no environment variables
    * Use `RUBYOPT=--parser=prism` instead.
* matz: Add `+prism` to `RUBY_DESCRIPTION`
    * I don't care `+prism` or `+PRISM`
    * It should be removed if prism becomes default
* matz: no warnings

### Random topics

https://bugs.ruby-lang.org/issues/8444

```ruby=
def foo
    f1 = Fiber.new do
        // =~ ""
        p $& #=> ""
    end
    f2 = Fiber.new do
        p $& #=> nil
    end
    f1.resume
    f2.resume
end

foo
```

https://bugs.ruby-lang.org/issues/17037

```ruby=
def foo
    yield 1
end

foo do
    p _0 #=> [1]
end

foo do
    p _1 #=> 1
end

def foo
    yield 1, 2, 3
end

foo do
    _0 #=> [1, 2, 3]
end

foo do
    _1 #=> [1, 2, 3]
end
```

