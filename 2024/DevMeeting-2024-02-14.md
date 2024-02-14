# DevMeeting-2024-02-14

https://bugs.ruby-lang.org/issues/20075

## DateTime and location

* 2024/02/14 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2024/03/14 (Thu) 09:00-12:00, 13:00-17:00 JST @ Online
* Let's try having this meeting earlier (easier for US people to join).

## Announce

### About release timeframe

- naruse: 3.3.1 is still WIP.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #16495]](https://bugs.ruby-lang.org/issues/16495) Inconsistent quotes in error messages (mame)

* Implemented a prototype.
* Error message: ``undefined method `foo'`` -> ``undefined method 'foo'``
* Backtrace: ``from test.rb:2:in `foo'`` -> ``from test.rb:2:in 'foo'``
* It caused 140 test failures in make test-all. Do we want to go forward?

Discussion:

* mame: I made a patch.
* mame: many `test-all` tests fail
* shyouhei: are they severe?
* mame: most of them are silly ones.  But there are some.  for instance test-unit parses the backtrace.
* shyouhei: it's definitely better sooner than later then.

Conclusion:

* matz: give it a try

### [[Feature #19117]](https://bugs.ruby-lang.org/issues/19117) Include the method owner in backtraces, not just the method name (mame)

* Implemented a prototype.
* ``from test.rb:2:in `instance_meth'`` -> ``from test.rb:2:in `Foo#instance_meth'``
* ``from test.rb:2:in `class_meth'`` -> ``from test.rb:2:in `Foo.class_meth'``
* ``from test.rb:2:in `singleton_meth'`` -> ``from test.rb:2:in `singleton_meth'``
* It caused 8 test failures in make test-all. Do we want to go forward?

Discussion:

* mame: I want to do this with #16495.

Conclusion:

* matz: give it a try

Other than that...
* ko1: should this honour set_temporary_name?
* mame: it seems nobody seriously thought about that case until now.  This is an open problem.

```ruby
c = Class.new do
  def foo
    raise
  end
end

# c.new.foo
# -e:4:in `#<Class:0x03520050>#foo': unhandled exception
# from -e:8:in `<main>'

c.set_temporary_name "<C>"
c.new.foo
# -e:4:in `<C>#foo': unhandled exception
# from -e:13:in `<main>'
```

### [[Bug #20188]](https://bugs.ruby-lang.org/issues/20188) `Module#const_source_location` returns wrong information when real constant was defined but autoload is still ongoing (byroot)

* `const_source_location` continues to return the `autoload` location even after the constant was defined.
* It only start returning the constant location once the `autoload` has completed.
* There is debate whether this should be considered a bug or a feature request.
* Patch: https://github.com/ruby/ruby/pull/9549

Preliminary discussion:

```ruby
File.write("/tmp/const.rb", <<~RUBY)
module Const
  p Object.const_source_location(:Const) #=> ["autoload.rb", 7]
end

Const
RUBY

autoload :Const, "/tmp/const"

p Const
p Object.const_source_location(:Const) #=> ["/tmp/const.rb", 1]
```

Discussion:

* mame: `const.rb` _defines_ `Const`,  but its `const_source_location` returns that of autoload, not the constant itself.
* matz: Understand the request.
* akr: What's happening?
* nobu: Because an autoload can fail, autoloaded constant remains to be an autoload until the load itself ultimately finishes.
* akr: This could be an interesting problem when other threads infer the location at this very moment.
* shyouhei: What is the original problem?
* hsbt: It was originally reported to zeitwerk.  Using that library, nobody is aware of their using autoload.  Then a call to const_source_location can either return autoloaded or autoloading ones.
* ko1: Also gem authors cannot control whether their gems are autoloaded or not at all.
* akr: Understand. this should be consistent between "autoload" and "require".

Conclusion:
* matz: it seems like a bug to me.
* ko1: nobody here is against fixing this.
* shyouhei: @nobu_nakada please try.

### [[Feature #20080]](https://bugs.ruby-lang.org/issues/20080) Introduce #bounds method on Range (stuyam)

* Easy array deconstruction for setting begin and end as variables from a range.
  * `first, last = (1..10).bounds #=> [1, 10]`
* Easy serialization and rehydration of ranges.
  * `array = (1...10).bounds #=> [1, 10, true]`
  * `Range.new(*array)`

Discussion:

* matz: Returning `true` is not what `bounds` is expected to do.
* shyouhei: What is the purpose of this proposal?
* mame: The OP originally wanted to deconstruct a range in pattern mathcing.  That was rejected, then this one came.

Conclusion:

* matz: reject. I will reply


### [[Bug #20203]](https://bugs.ruby-lang.org/issues/20203) `TestEnumerable` test failures with GCC 14 (alanwu)

* Using `qsort_r` to sort causes corruption when the comparison function is reentered using continuation/fiber and when using GC compaction due to undefined behavior
* Should we stop using `qsort_r` for correctness?

Discussion:

* akr: I want to know what is going on.
* nobu: it seems GC compaction smashes the array during `qsort_r` is in operation.
* shyouhei: curious if reinventing qsort_r fixes this?
* akr: seems negative.
* nobu: GC currently does not check whether an array is in use or not?
* akr: This started to sounds like a GC's problem, rather than qsort's.
* mame: why modifying the array during sort is okay now?
* nobu: `ary_make_substitution()` prevents array smashing operations.
* ko1: ... but not from GC.
* akr: what I understand:
    1. GCC's `qsort_r` allocates a scratch buffer with malloc.
    1. `qsort_r` copies array contents from Ruby's space to tha buffer.
    1. GC works.  Compaction moves objects around. but the scratch buffer is untouched.
    1. `qsort_r` writes the buffer back, which holds now-invalid pointers.
    1. Boom.
* mame: we could pin down everything inside of that array.
* akr: Or just roll our own version of qsort.  Now I understand that's feasible.

Conclusion:

* mame: Alan seems right.  Let's resort to our own version.


### [[Feature #20108]](https://bugs.ruby-lang.org/issues/20108) Introduction of Happy Eyeballs Version 2 (RFC8305) in Socket.tcp (shioimm)

- Fixed the points raised at the last DevMeeting and in the comments.
  - When an IP address is specified as an argument, or when the host is single-stack, name resolution is now performed on the main thread.
  - `fast_fallback` option is added.

Discussion:

* (everybody is looking at the pull request)
* shyouhei: glitchy corner cases aside, overall design sounds reasonable to me.
* akr: basically seems worth trying.
* akr: re: then fast_fallback naming...
* shioimm: the name was sugested by @shugo.
* shyouhei: it seems he took that name from okhttp.

----
* re: @hanazuki's IP address list comment:
* shyouhei: understand what she says but...
* shioimm: requestig IP address list each time could be slow
* akr: this could not be that slow (only talks to localhost's kernel and no external IOs)
* mame: just don't check and invoke threads then? Users can specify fast_callback. 

Conclusion:

* shioimm: what to do next?
* mame: let the mentors review your pull request.
* hsbt: nobody in this meeting is against this.


### [[Misc #20238]](https://bugs.ruby-lang.org/issues/20238) Use prism for mk_builtin_loader.rb (kddnewton)

* I would like to propose that we use prism for mk_builtin_loader.rb.
* This would allow us to use the latest Ruby syntax in builtin classes, as opposed to the base Ruby version.

Discussion:

* k0kubun: background:
    * Ruby's builtin classes are partially written in ruby (like array.rb).  They use `Primitive....`
    * Because builtin classes have to be built before ruby itself is built, these `.rb` files have to be parsed using BASERUBY.
    * We want to use newer ruby syntax in our newer code.
    * Prism can be used in BASERUBY, and can parse newer ruby codes using old rubies.
* k0kubun: The problem is, it needs C extensions.
* mame: not in favor to complicate our build system any more.
* hsbt: Ubuntu could be possible, but other cases like *BSD are headache.
* akr: is there any reason why we need new syntax?
* k0kubun: pattern matching, endless method definition etc.
* k0kubun: I tend to want to use cool features in yjit files.

Conclusion:

* k0kubun: will reply.


### [[Feature #20205]](https://bugs.ruby-lang.org/issues/20205) Enable `frozen_string_literal` by default (byroot)

* A proposed migration plan for allowing to enable `frozen_string_literal` by default in the future.
* Introduce "chilled strings", they behave like frozen strings, but on mutation they emit a deprecation warning and become regular mutable strings again.
* Implemented as a USER_FLAG on RString, proof of concept at https://github.com/Shopify/ruby/pull/549.
* The warning can include the location of the mutation and/or the location of the string allocation. The later is a bit more costly.
* `--disable-frozen-string-literal` remains forever as a way to keep running older code that can't be updated.
* `# frozen_string_literal: false` remains forever as a very minimal and cheap way to make older code compatible.

Discussion:

* matz: I'm positive about the feature itself.  Glad they propose a transition path now.
* mame: every string instance now has a flag (chilled), and warning is issued if that string is modified.
* hsbt: performance seems negligible?
* matz: it seems the focus is now on cultural thing rather than speed.
* mame: Let me leave my -1 to this.

Conclusion:

* matz: i will reply


### [[Bug #20218]](https://bugs.ruby-lang.org/issues/20218) aset/masgn/op_asgn with keyword arguments (jeremyevans0)

* Do we want to make it a syntax error to pass keyword arguments in these cases?
  * There was already a desire to make passing blocks in these cases a syntax error (see #19918)?
* If keyword arguments should be allowed, should we treat keywords as keywords or as positional arguments (currently: aset treats as positional, op_asgn treats as keywords)?

Preliminary discussion:

```ruby
o = []
def o.[]=(*args, **kw)
  p args #=> [1, {:a=>1}, 2]
  p kw   #=> {}
end

o[1, a:1] = 2
o.[]=(1, 2, a: 1)
```

Discussion:

* matz: Mmmmmmm…
* mame: block is already prohibited.

Conclusion:

* matz: Let's give up with these symtax.


### [[Bug #20229]](https://bugs.ruby-lang.org/issues/20229) Empty keyword splat in array not removed in ARGSPUSH case (jeremyevans0)

* Is it OK to fix this as part of an optimization that adds pushtoarraykwsplat instruction?

Preliminary discussion:

```ruby
a = [1]
kw = {}
[1, **kw]  #=> expected: [1], actual: [1]
[*a, **kw] #=> expected: [1], actual: [1, {}] #<= inconsistent
```

Discussion:

* shyouhei: didn't even know this is possible.

Conclusion:

* matz: this is a bug. go ahead


### [[Misc #20242]](https://bugs.ruby-lang.org/issues/20242) `--parser=prism_without_warning` (kddnewton)

* Could we add `--parser=prism_without_warning`? It hard to run the tests/specs that test against the output of stderr.
* This would do the exact same thing as --parser=prism it just wouldn't warn.

Discussion:

* mame: `--parser=prism` renders error, which itself prevents tests to pass.
* nobu: stop `--parser=prism` from rendering warnings?
* hsbt: stop tests from interfacing with warnings.
* nalsh: the warning shall be an experimental one.
* mame: proposed change:
    * `ruby --parser=prism` prints the warning
    * `ruby -W:no-experimental --parser=prism` prints no warning

Conclusion:

* don't add compile options.
* ruby should not show the warning when `-W:no-experimental --parser=prism`

### [[Feature #20210]](https://bugs.ruby-lang.org/issues/20210) Invalid source encoding raises ArgumentError, not SyntaxError (kddnewton)

* Could we make it raise a syntax error?

Discussion:

* nobu: should we continue to parse when an unknown encoding is specified by the magic comment?
* naruse: no
* akr: in some use cases, it may be useful. For example, code coloring
* mame: at least the interpreter should stop parsring when encountering an invalid encoding magic comment
* mame: prism as a library may choose other behavior
* yui-knk: Is it okay to change the exception type from ArgumentError to SyntaxError
* naruse: It is incompatible. Is there any advantage that beats the incompatibility?

Conclusion:

* continue the discussion


### [[Feature #20257]](https://bugs.ruby-lang.org/issues/20257) Rearchitect Ripper (yui-knk)

* I want to have a chance to explain new Ripper's architecture.
* In short, Ripper uses semantic value stack to manage Ruby Object returned by Ripper callback methods then Ripper can't execute semantic analysis which needs AST Node. New architecture enables it by adding another stack to Ripper parser with Lrama's update.

Discussion:

* ko1: any user-facing changes?
* yui-knk: apart from small bug fixes, no.
* mame: performance?
* yui-knk: using ripper already implies the program has no interest at all for performance I believe.

Conclusion:

* yui-knk: I will make it happen.


### [[Misc #20260]](https://bugs.ruby-lang.org/issues/20260) ISEQ flag for prism compiler (kddnewton)

* Would it be alright to add a flag to iseqs to say it came from the prism compiler? We need this to support error highlight.

Preliminary discussion:

* mame: Simply add `Thread::Backtrace::Location#ast_node` if prism becomes the only Ruby parser?
  * `Proc#ast_node` or something too?
  * And `RubyVM::AbstractSyntaxTree` will be removed?
* mame: If prism is not finally decided yet, `RubyVM.prism_ast_node_for(loc)` as a temporal method?

Discussion:

* mame: We need to make `RubyVM::AST.of(loc)` raise an exception if loc has an iseq compiled from Prism.
* naruse: It is usable to determine if the iseq is complied from Prism or parse.y
* mame: Yes
* mame: Not only iseq, but also program counter are needed to identify node_id.
* ko1: `Thread::Backtrace::Location#node_id` is acceptable
* mame: I guess it will allow `Prism.parse(loc.path).node_for(loc.node_id)`

Conclusion:

* Introduce `Thread::Backtrace::Location#node_id` that returns node_id, regardless of complied from parse.y or Prism.

### [[Feature #20249]](https://bugs.ruby-lang.org/issues/20249) Print only backtraces in rb_bug(), by default

* ko1: worth to discuss

Discussion:

* ko1: rb_bug() prints too much.
* ko1: namely loaded features and memory maps are rarely used.
* ko1: current proposal is to have a "crash report mode" which controls whether dump a detailed list or not.
* ko1: as an interpreter dev:  we don't need this.  we can use GDB etc.
* ko1: as a user: nobody would seriously turn the "crash report mode" and run their program again.
* ko1: another option is dump the details only when dump to a file.
* shyouhei: Why rb_bug() prints many lines by default is that we need these info from users.
* nalsh: Agreed.  I'm against changing the default.
* ko1: the OP is not the end user.  He is a C extension developer and his extension is experiencing SEGVs.
* nalsh: OK to save those people.
* matz: no preference over the env name.

Conclusion:

* ko1: feature accepted, the env name shall be discussed on the issue.


### [[Bug #20225]](https://bugs.ruby-lang.org/issues/20225) Inconsistent behavior of regex matching for a regex has a null loop (makenowjust)

* Onigmo has strange behavior for null (empty) loops with captures.
  * Null-loop check depends on capture status.
* This behavior causes some bugs and prevents memoization (cache) optimization.
* I would like to fix null-loop check to capture-free.
  * This behavior is not common; JavaScript uses capture-free null-loop check.
  * It is hard to implement capture-aware null-loop check correctly and impossible to provide efficient implementation.
    * e.g. `ruby -e '(1..10).each { |n| /((?=(\3a|a))(?=(\2a|a))){#{n}}/ =~ "aaaa"; p $~ }'`
    * This example shows capture status changes oscillationally.

Preliminary discussion:

```
/(a|\2b|())*/.match("aaabbb") #=> #<MatchData "aaabbb" 1:"" 2:"">
/(a|()|\2b)*/.match("aaabbb") #=> #<MatchData "aaa" 1:"" 2:"">
```

Discussion:

* makenowjust: the reason behind this is because onigmo detects infinite loop by looking if there is a capture or not.
* nobu: surprised we can do forward-reference `\2`.
* makenowjust: I want `/(a|\2b|())*/.match("aaabbb")` to match `"aaa"`, i.e. bail out when inner loop matches to an empty string.
* shyouhei: at least the status quo is confusing.
* nobu: I'd rather prohibit this regexp.
* mame: does prohibiting this regexp fix the problem?
* makenowjust: I guess yes... but not proven.

Conclusion:

* akr: give it a try.


### [[Feature #20244]](https://bugs.ruby-lang.org/issues/20244) Show the conflicting another chdir block

  * `Dir.chdir` is warning when in another chdir block.
    ```
    $ ruby -e 'Dir.chdir {' -e 'Dir.chdir("/")' -e '}'
    -e:2: warning: conflicting chdir during another chdir block
    ```
    If two chdirs are far apart, it can be difficult to find conflicting blocks.

  * To help the debugging, I propose to improve the warning message to show the conflicting block.
    ```
    $ ./ruby -e 'Dir.chdir {' -e 'Dir.chdir("/")' -e '}'
    -e:2: warning: conflicting chdir during another chdir block
    -e:1: warning: here
    ```
Discussion:

```ruby
Dir.chdir("/home/mame") do
  Dir.chdir("/") #=> conflicting chdir during another chdir block 
end
```

```
test.rb:2: warning: conflicting chdir during another chdir block
test.rb:1: warning: here

test.rb:2: warning: conflicting chdir during another chdir block
test.rb:1: warning: ... the chdir block is here

foo/bar.rb:2: warning: conflicting chdir during another chdir block at baz/qux.rb:1
```

* mame: byroot wants the single-line warning instead of two lines
* nobu: ctags (?) requires one line for one code location
* naruse: nobu should discuss with byroot in the ticket

Conclusion:

* continue discussion


## Random topic

https://bugs.ruby-lang.org/issues/20247
https://bugs.ruby-lang.org/issues/20254
https://bugs.ruby-lang.org/issues/20187#note-6
