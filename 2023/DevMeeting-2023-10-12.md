# DevMeeting-2023-10-12

https://bugs.ruby-lang.org/issues/19883

## DateTime and location

* 2023/10/12 (Thu) 13:00-17:00 JST @ Online

## Next Date

* Next: 2023/11/07 (Tue) 13:00-17:00 JST @ Online

Discussion:

* 11/09 is Rubyworld conference day.
* matz: 11/16 is NG to me.
* matz: 11/23 is a holiday in my country.
* matz: 11/14-15 is RubyConf.
* akr: 11/20 is NG to me.

Conclusion:
* Next: 2023/11/07 (**Tue**) 13:00 - 17:00 JST @ online

## Announce

* shyouhei: there'll be only two chances for deelopmer meeting until the next release?
  * ko1: keep 2023/11/30

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #19744]](https://bugs.ruby-lang.org/issues/19744) Namespace on read (tagomoris)

* Now the PoC implementation works, can require 2 different versions of native extension libraries.
* I will introduce the motivation and the implementation idea.
* I need discussions about:
  * Several design decision points
  * APIs for apps
  * Migration paths

* Japanese: https://gist.github.com/tagomoris/811f403751f745b5360af4ecc5f74b67
* Presentation slide: https://speakerdeck.com/tagomoris/help-collisions-isolate-the-worlds
* Discussion points: https://gist.github.com/tagomoris/16df4ba46a46a20bf223ef127bf05580

Preliminary discussion:

This is planned to start @ 14:00 JST

Discussion:

* tagomoris: We want "Namespace" feature for ruby.
* tagomoris: This makes it possible to load multiple versions of a same library (that shres process-global settings etc.)
* tagomoris: A middle-sized application can suffer from dependency hell, which can be mitigated by the application using this feature.
* tagomoris: proposed feature is `NameSpace.new` creates a separated scope, and `NameSpace#require` loads into it, does not pollute the global toplevel.
* tagomoris: `Kernel#load` is a similar feature, but the difference is:
  * Our proposal can take C libraries into account,
  * A library can require something else and that does not escape the loaded namespace, unlike the case for `Kernel#load`
* tagomoris: namespace "on read" means we don't force each library authors to migrate to this path; the usage is by application side only.
* tagomoris: long-term goal is bundler/rubygems integration... but that is not our first goal.

----

* matz: I understand the needs!
* matz: other languages have module system built in.  we need to design carefully.
* matz: I was wondering how to achieve this feature for C libraries.  Kudos for the hutsle.
* tagomoris: dlopen with RTLD_LOCAL mostly does the job, but there are some glitches.
* nobu: reading symbols of other DLLs than itself is not portable...
* shyouhei: but there are some.
* akr: I remember gnome does that.
* shyouhei: Is there a way for a library to work both for namespace and for global at once?
* tagomoris: Yes bu the problem is, when a library is already loaded globally, then one wants to reload it locally.
* akr: when interpreter encounters `require` it is not clear whether that loads into a namespace or to global one.
* ko1: tagomoris' design is that you cannot refer to global ones.

```ruby=
n = NameSpace.new
n.require('json')
require('json')
```
* ko1 :arrow_up: how to refer the JSON constant inside of `n` ?

----
Re: monkey patch

* how monkey patches interface with this?
* akr: we cannot know if `class String` is reopened for monkey patch or to define a new class.
* mame: should monkey patches be separated by each namespaces?
* mame: or should they be global?
* naruse: when a global `String` class is visible form inside of a namespace that shall be reopened?
* mame: do we want to load multiple version of ActiveSupport at once?  If so monkey patches need separated among namespaces

Conclusion:

* It is basically a good feature, should be a part of our language once matured.
* Greater goal
  * It should be the way to go for rubygems/bundler to automatically detect version conflicts ( :arrow_left: this is already done ) and reroute them automatically, somehow.
* Challenges
  * How to design a module system (we need more research).
  * How to tweak `require` to achieve what tagomoris wants.
  * How to actually load multiple rails versions at once in a process.
* Things to decide in a short turn
  * Naming of this feature
  * Should the namespace be a subclass of a module?


### [[Feature #18573]](https://bugs.ruby-lang.org/issues/18573) Object#pack1 (jeremyevans0)

* For the same reason as String#unpack1, saves an array allocation when packing a single object.
* I think it would be useful to have this feature, but I prefer Array.pack1 to Object#pack1 to implement it.
* Can we add this feature, and if so, do we want to use Array.pack1, Object#pack1, or a different method for it?

Preliminary discussion:

* Options:
    * `Object#pack1(format)`
    * `Array.pack1(obj, format)`
    * `String.pack1(format, obj)``
    * `Numeric#pack1(fmt)` (Integer and Float) and `String#pack1(fmt)` (by naruse)

Discussion:

* nobu: should this be defined on Object?
* ko1: current proposal is for only String and Array
* matz: I don't think we need the Array version.
* naruse: There are use cases when we want to pack an Integer.
* shyouhei: we could optimise Array#pack, like we already do for Array#min
* naruse: It seems the intention is not for speed, but the focus is on elgonomics.
* mame: should we have this method for Object?
* ko1: what else?
* naruse: I suggest instance method of String and Integer/Float because most of targets are these classes.
* akr: I started to agree with naruse.
* shyouhei: I guess this is because perl had no objects yet when we introduced this feature to ruby.
* naruse: kazuho's example: https://github.com/search?q=repo%3Akazuho%2Frat%20pack&type=code
* akr: resolv.rb has more than 10 times of pack invocation on one-element array.

(everbody is reading https://docs.ruby-lang.org/ja/latest/doc/pack_template.html ...)

```ruby=
# current
[codepoint].pack('U')
[digest].pack('m0')

# option 1
codepoint.pack1('U')
digest.pack1('m0')

# option 2
Array.pack1(codepoint, 'U')
Array.pack1(digest, 'm0')

# option 3
String.pack1('U', codepoint, 'U')
String.pack1('m0', digest)

# option 4
codepointt.pack1('U')
digest.pack1('m0')
```

* ko1: what happens when someone do `"str".pack1("UU")` ?
* naruse: do nothing?
* akr: or raise "too few arguments" error, as Array#pack does now.
* ko1: `unpack1` ignores multiple specifiers.

```
irb(main):001:0> [1].pack('U')
=> "\u0001"
irb(main):002:0> [1].pack('U').unpack1('UU')
=> 1
```

Conclusion:

* matz: do we really need it ...?  It's just eliminating 2 characters ( `[`, `]` )
  * at least class methods, `Array.` and `String.`, are not acceptable because `Array` and `String` are not related to the `pack` method.
  * `Object#pack1` is also unacceptable because all objects do not need this method.
  * `String#pack1` and `Integer#pack1` is doubtful because I'm not sure really it is needed.
* we need more pressure to persuade matz.

### [[Bug #19866]](https://bugs.ruby-lang.org/issues/19866) Future of `readline.rb` (jeremyevans0)

* Seems odd for a standard library to attempt to load a non-standard library first, and fallback to loading a standard library.
* Are we OK with how lib/readline.rb currently works?  If so, we can close this.

Preliminary discussion:

* hsbt: I think it is okay to close this ticket as it is. I believe nobody rewrites the code `require 'readline'` to `require 'reline'`.

Discussion:

* mame: Current readline library tries to load libreadline, then tries reline on failrue.
* akr: The same thing we did for set before.
* hsbt: I don't think we should force people using reline
* naruse: This readline.rb is a transition path for old programs.

```ruby=
# readline.rb

begin
  require 'readline.so'
rescue LoadError
  require 'reline' unless defined? Reline
  Object.send(:remove_const, :Readline) if Object.const_defined?(:Readline)
  Readline = Reline
end
```

Conclusion:

* nobody in the meeting agrees with the proposal.
* naruse: it could perhaps make sense if we remove this file entirely from our project, but modifying it makes no sense at all.


### [[Bug #16927]](https://bugs.ruby-lang.org/issues/16927) String#tr won't return the expected result for some sign with diacritics (jeremyevans0)

* Do we plan to convert String#tr (and potentially other String methods) from operating on codepoints to operating on grapheme clusters at some point?

Preliminary discussion:

Discussion:

* ko1: String#tr does not consider graheme clusters.
* naruse: Does this make sense...?
* akr: Does String#tr work for multi-byte characters at the forst place?
* naruse: yes.  it works character-by-character
* akr: ... but not for combinded character. OK I got the problem.
* akr: `"aeiou".gsub(/[aeiou]/, {"a"=>"ą̂", "e"=>"ę̂", "i"=>"į̂", "o"=>"ǫ̂", "u"=>"ų̂"})`


Conclusion:

* naruse: rejected for `String#tr`. will reply.
* knu: there is no alternative to do what OP wants right now, though.


### [[Bug #18903]](https://bugs.ruby-lang.org/issues/18903) Stack overflow signal handling seems to be triggered once and then not working after (jeremyevans0)

* Should we raise fatal error instead of SystemStackError for stack overflows in C code?
* VM stack overflow can be safely rescued, since we check for the stack overflow before overflowing.
* C stack overflow cannot be safely rescued in all cases.

Preliminary discussion:

* ko1: this behavir strongly depends on the platform, and I think it is okay to reimain. Furthermore , fatal doesn't help the once C stack overflow.

Discussion:

* shyouhei: what is going on?
* nobu: do we need to rescue from a stack overflow?
* mame: maybe a job queue want to avoid crashing
* ko1: what happens when this happens in a thread?
* ko1: it is kind if exceptions happen multiple times, given it does once.
* nobu: We could improve this, but making it fatal is OK to me.

Conclusion:

* nobu: I will try to investigate the issue on M2


### [[Misc #18984]](https://bugs.ruby-lang.org/issues/18984) Doc for Range#size for Float/Rational does not make sense (kyanagi)

* Range#size may return a non-nil value even if the range can't be iterated.
* Should it return nil?

Discussion:

```ruby
(0.51..5.quo(2)).size  #=> 2
```

* akr: I don't think it's worth breaking backwards compatibility for this behaviour.
* matz: I would say the size of a range is undefined when that range is not iterable.
* mame: what about beginless ranges?
* akr: returns number of block invocation of Range#each method? TypeError otherwise.

Conclusion:

* matz: Let's change the behavior

```ruby
(0.51..5.quo(2)).size #=> TypeError
(..0).size            #=> TypeError
```

### [[Feature #18515]](https://bugs.ruby-lang.org/issues/18515) Add Range#reverse_each implementation (kyanagi)

* This enables #reverse_each for huge or beginless ranges.
* If this is acceptable, [Feature #18551] should also be considered.

Preliminary discussion:

Proposed method: https://github.com/ruby/ruby/pull/8525/files#diff-3e202665cd48326cc6415f1139a8ec7acd9a1833c51630644b126f49718e6cffR1124

```ruby=
class Range
  def reverse_each
    if range is Integer
      ... # specialize version
    else
      super # call Enumereable#reverse_each
    end
  end
end
```

Discussion:

* ko1: OP wants to add some special cases.
* ko1: It doesn't seem problematic to me.
* mame: A bit of concern for code bloats.
* mame: Wonder why we have Enumerable#reverse_each at the first place?
* akr: What to do if one want to do Range#reverse_each if we lack one?
* naruse: downto can be used.

```ruby=
upper.downto(lower) {}
(lower..upper).reverse_each {}

(upper - 1).downto(lower) {}
(lower...upper).reverse_each {}
```

* nobu: I don't think it works for non-integers: `"99".succ` and `"099".succ` is same, `"100"`.

Conclusion:

* matz: I will reply later


### [[Feature #13933]](https://bugs.ruby-lang.org/issues/13933) Add Range#empty? (Dan0042)

* "empty?" is a basic concept for core classes
* Related to the implementation of Range#overlap? (empty_region_p) so why not make it a public interface?
* For beginless ranges we cannot use (..0).none?
* For string ranges we cannot use ("a".."b").size
* There's an edge case with e.g. (...-Float::INFINITY) or (..."") with several possible approaches listed in the ticket

Discussion:

* (failed to log)
* matz: I reject `Kernel#minimum?` and `Kernel#maximum?`. I don't want to pollute the Kenel name space for such a non-important thing
* akr: We need to do ethier give up Range#empty? or admit special case of `(...-Float::INFINITY).empty? #=> false`
* matz: Give up Range#empty? until there is an explicit use case
* akr: How about Range#overlap? ? It also has a similar corner case:
  * `(...-Float::INFINITY).overlap?(...-Float::INFINITY) #=> true`
* matz: Let's write a document for the special case of minimal value

Conclusion:

* matz: Reject `Range#empty?`. Add a document about the special case to `Range#overlap?`

### [[Feature #19842]](https://bugs.ruby-lang.org/issues/19842) Merging MaNy project (ko1)

* ko1: ready to merge.

```
$ RUBY_MN_THREADS=1 ./miniruby -e '50000.times { Thread.new { sleep } }'
-e:1:in `initialize': can't create Thread: Cannot allocate memory (ThreadError)
        from -e:1:in `new'
        from -e:1:in `block in <main>'
        from <internal:numeric>:237:in `times'
        from -e:1:in `<main>'
[BUG] heap_page_body_free: munmap failed
ruby 3.3.0dev (2023-10-12T08:22:32Z master 1c871c08d9) [x86_64-linux]

-- C level backtrace information -------------------------------------------
mmap: Cannot allocate memory
/home/mame/work/ruby/miniruby(rb_print_backtrace+0x14) [0x55effe0a5bc3] /home/mame/work/ruby/vm_dump.c:812
/home/mame/work/ruby/miniruby(rb_vm_bugreport) /home/mame/work/ruby/vm_dump.c:1143
/home/mame/work/ruby/miniruby(bug_report_end+0x0) [0x55effde89d56] /home/mame/work/ruby/error.c:1042
/home/mame/work/ruby/miniruby(rb_bug_without_die) /home/mame/work/ruby/error.c:1042
/home/mame/work/ruby/miniruby(die+0x0) [0x55effddd65cb] /home/mame/work/ruby/error.c:1050
/home/mame/work/ruby/miniruby(rb_bug) /home/mame/work/ruby/error.c:1052
/home/mame/work/ruby/miniruby(heap_page_body_free+0xe) [0x55effddd6d37] /home/mame/work/ruby/gc.c:2045
/home/mame/work/ruby/miniruby(rb_objspace_internal_object_p) /home/mame/work/ruby/gc.c:2037
/home/mame/work/ruby/miniruby(ruby_vm_destruct+0x168) [0x55effe09f688] /home/mame/work/ruby/vm.c:2952
/home/mame/work/ruby/miniruby(rb_ec_cleanup+0x2a7) [0x55effde93ef7] /home/mame/work/ruby/eval.c:268
/home/mame/work/ruby/miniruby(ruby_run_node+0x56) [0x55effde94736] /home/mame/work/ruby/eval.c:328
/home/mame/work/ruby/miniruby(rb_main+0x21) [0x55effdde10b6] ./main.c:39
/home/mame/work/ruby/miniruby(main) ./main.c:58
/lib/x86_64-linux-gnu/libc.so.6(0x7fe443423a90) [0x7fe443423a90]
[0x7fe443423b49]
[0x55effdde1105]
```

```
[    9/26177] TestLastThread#test_last_thread = 1.04 s
  1) Failure:
TestLastThread#test_last_thread [/home/mame/work/ruby/test/-ext-/gvl/test_last_thread.rb:6]:
assert_separately failed with error message
pid 87392 exit 0
| RUBY_MN_THREADS = 1 (default: 0)


[   12/26177] TestSystem#test_system_closed = 0.03 s
  2) Failure:
TestSystem#test_system_closed [/home/mame/work/ruby/test/ruby/test_system.rb:150]:
assert_separately failed with error message
pid 87397 exit 0
| RUBY_MN_THREADS = 1 (default: 0)


[   80/26177] TestMkmfFlags#test_try_cflag_invalid_opt = 0.22 s
  3) Failure:
TestMkmfFlags#test_try_cflag_invalid_opt [/home/mame/work/ruby/test/mkmf/test_flags.rb:43]:
assert_separately failed with error message
pid 87441 exit 0
| RUBY_MN_THREADS = 1 (default: 0)
```

```
$ RUBY_MN_THREADS=1 ./local/bin/ruby -v
ruby 3.3.0dev (2023-10-12T08:22:32Z master 1c871c08d9) [x86_64-linux]
```

Conclusion:

* naruse: go ahead.

### Chat

* https://bugs.ruby-lang.org/issues/19422
  * `--enable-shared`: libruby.so 作るか
  * `--with-static-linked-ext`: ruby もしくは libruby に extension を混ぜる

Discussion:

* Should we make `--enable-shared` as default?
* Should we make to prevent to use `--disable-shared` with macOS?
  1. default to `--enable-shared`, and warn `--disable-shared`
  2. reject `--disable-shared`

```ruby=
class Foo
  def initialize
    @cache2 = self.class
  end
  def self.m
  end

  def a
    self.class.m
  end

  def b
    (@cache1 ||= self.class).m
  end

  def c
    @cache2.m
  end
end

require "benchmark/ips"

Benchmark.ips do |b|
  v = Foo.new
  b.report("a") { v.a }
  b.report("b") { v.b }
  b.report("c") { v.c }
  b.compare!
end
```

```
$ ruby a.rb
Warming up --------------------------------------
                   a     1.618M i/100ms
                   b     1.777M i/100ms
                   c     1.853M i/100ms
Calculating -------------------------------------
                   a     16.278M (± 0.6%) i/s -     82.495M in   5.068093s
                   b     17.868M (± 0.4%) i/s -     90.631M in   5.072200s
                   c     18.575M (± 2.1%) i/s -     94.517M in   5.091071s

Comparison:
                   c: 18574670.1 i/s
                   b: 17868353.8 i/s - 1.04x  slower
                   a: 16277899.7 i/s - 1.14x  slower

$ ruby --yjit a.rb
Warming up --------------------------------------
                   a     2.678M i/100ms
                   b     2.668M i/100ms
                   c     2.418M i/100ms
Calculating -------------------------------------
                   a     31.016M (± 0.4%) i/s -    155.347M in   5.008674s
                   b     31.278M (± 0.3%) i/s -    157.402M in   5.032373s
                   c     31.465M (± 0.3%) i/s -    159.571M in   5.071369s

Comparison:
                   c: 31465345.7 i/s
                   b: 31278311.4 i/s - same-ish: difference falls within error
                   a: 31016048.5 i/s - 1.01x  slower
```
