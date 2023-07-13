# DevMeeting-2023-07-13

https://bugs.ruby-lang.org/issues/19599

## DateTime and location

* 2023/07/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* Next: 2023/08/24 (Thu) 13:00-17:00 JST @ Online
  * ko1: last chance for 3.3 big features
  * big features?
    * yarp
      * ko1: need to fix its "real" name
    * rjit
    * lrama

## Announce

## About release timeframe

* preview would be after some big features discussed :arrow_up: above gets landed.

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18572]](https://bugs.ruby-lang.org/issues/18572) Performance regression when invoking refined methods (shugo)

* Should we limit usage of using to fix the performance issue?
  * https://bugs.ruby-lang.org/issues/18572#note-10

  ```ruby
  class Foo
    def original
    end
    def refined
    end
  end
  module FooRefinements
    refine Foo do
      def refined
        raise "never called"
      end
    end
  end
  FOO = Foo.new

  t = Time.now
  100000.times do
    FOO.refined
  end
  if Time.now - t > 0.007
    puts "slow"
    exit 1
  else
    puts "fast"
    exit 0
  end
  ```

Preliminary discussion:

* Ask shugo and Matz

Discussion:

* shugo: this is a bug and I ask ko1 to fix.
* shugo: eregon says we should prohibit `using` inside of a block.  Thoughts?
* ko1: is it bad?
* shugo: the slowdown is not only for refined call sites, but for call sites that uses non-refined (original) methods.
* ko1: it's because method cache is invalidated.
* shugo: how a method is cached changed in 3.0.
* ko1: We need to rethink about that.

----

* ko1: re:block
* shugo: current behaviour is when `using`-ed inside of a block, that affects to the enclosing module.
* ko1: the fix above is good enough for the situation.
* shyouhei: should that be prohibited?
* shugo: prohibiting is easy but I need to know the reason to do so.
* shyouhei: understood
* shugo: if eregon needs that I want a separate ticket for discussuion.

Conclusion:

* ko1: I will fix this.


### [[Feature #19714]](https://bugs.ruby-lang.org/issues/19714) Add `Refinement#refined_module` (shugo)

  * Is it OK to introduce the alias?

Preliminary discussion:

* There already is `#refined_class`.

Discussion:

* matz: `class.module_eval` sounds wrong so there also is `#class_evel`.  But what about `refined_module` which returns a mixture of classes and modules?
* mame: what about `refined_module` only then, because `Class < Module` holds?
* shugo: what about `refined_targets`?
* matz: refined_targets acceptable.
* shugo: maybe just `target` suffice, because that should be `Refinement#target`.
* matz: okay.

```ruby
module R1
  refine String do
    p self # is a Refinment object
    p self.target #=> String
  end
  refine Array do
  end
end

p R1.refinements.map{|r| r.refined_class}
#=> [String, Array]

p R1.refinements.map{|r| r.target}
#=> [String, Array]
```

Conclusion:

* matz: `Refinement#target` accepted.


### [[Feature #19720]](https://bugs.ruby-lang.org/issues/19720) Warning for non-linear Regexps (eregon)

* I think this is the best way to avoid ReDoS and ensure there are no too slow Regexps in a Ruby program/app.
* So let’s add `Warning[:regexp] = true`?
* I will help to address the non-linear regexps in stdlib, default gems and bundled gems.

Preliminary discussion:

*

Discussion:

* mame: this warning warns for regexp that cannot be optimized
* matz: but the programmer wants to use the regular expression.  It is irritating to be warned then.
* shyouhei: there are expressions that can be re-written in a fast way.
* matz: but those slow features are there on purposes.  I don't want people from stop using them.
* mame: in practice if this warning is enabled people find "problem"s on 3rd party gems, should that be a desirable feature?
* ko1: there already is a rubocop cop for slow regexp.  That should be enough for eliminating them.

Conclusion:

* matz: I don't want to discourage a part of the language.


### [[Feature #19730]](https://bugs.ruby-lang.org/issues/19730) Remove transient heap (peterzhu2118)

- Variable Width Allocation supports all types that used transient heap (arrays, objects, hashes, structs).
- Transient heap is no longer used for hashes.
- Transient heap is used for limited cases for arrays, objects, and structs.
- Removing the transient heap has no performance impact, significant memory decrease (5-10% decrease).

Preliminary discussion:

* ko1: Is it ok? > ok

Discussion:

* ko1: What makes VWA faster than transient heap?
* peter: Almost no performance imporovement but improves memory usage.
* ko1: Is there a performance result between with VMA & without VMA benchmark?
* peter: https://shopify.engineering/ruby-variable-width-allocation#Benchmark
* ko1: is the number with YJIT?
* peter: without.  I don't think transient heap interfaces with YJIT.
* ko1: how long does it take a string to need a separate allocation?
* peter 640

Conclusion:

* ko1: OK.  Let's delete it.


### [[Feature #19729]](https://bugs.ruby-lang.org/issues/19729) Store object age in a bitmap (eightbitraptor)

* Allows more control of generational GC (number of generations, when are objects old).
* Adds a bitmap to each heap page (`heap_page->age_bits`).
* Frees up one of the two `FL_PROMOTED` bits from `RBasic->flags`.
* The remaining `FL_PROMOTED` used as fast path optimization for the write barrier.
* Benchmarks show no performance impact, slight memory increase (due to the extra bitmap).

Preliminary discussion:

* ko1 looks and handle this.
* mame: Dont use optcarrot as an evaluation for GC improvement

Discussion:

* ko1: I have no problem with this ticket.
* peter: we are working on GC API for e.g. new GC, and this is one step towards that.

Conclusion:

* ko1: go ahead


### [[Bug #11064]](https://bugs.ruby-lang.org/issues/11064) `#singleton_methods` for objects with special `singleton_class` returns an empty array (jeremyevans0)

* Is it OK to make {NilClass,TrueClass,FalseClass}#singleton_method always raise an exception?

Preliminary discussion:

```ruby
p nil.singleton_methods       #=> []
p nil.singleton_method(:nil?) #=> actual: #<Method: NilClass#nil?()>
                             #=> expected: undefined singleton method `nil?' for `nil'
p nil.method(:nil?) #=> #<Method: NilClass#nil?()>

p nil.singleton_class.singleton_class? #=> false

def nil.foo
  # This is NilClass#foo, not nil's singleton class' foo.
end
nil.singleton_method(:foo) # matz expects this to return a Method object
```

* Ask Matz

Discussion:

* shyouhei: why does `nil.singleton_method(:nil?)` work today?
* matz: it's special-cased.
* nobu: What about making `NilClass` the actual singleton class of `nil`?
* shugo: What would be the class of `nil` then?
* nobu: would be `Object`
* mame: sounds surreal!
* shugo: what about returning non-empty array for `nil.singleton_methods`?
* matz: that could be possible.

Conclusion:

* matz: will respond.


### [[Bug #19293]](https://bugs.ruby-lang.org/issues/19293) The new `Time.new(String)` API is nice... but we still need a stricter version of this (jeremyevans0)

* It was apparently decided that `Time.new("2023-01")` and similar should raise an exception in 3.2.1, but that didn't happen.
* Do we want to raise an exception in this case and similar cases, and backport the changes to Ruby 3.2.3?

Preliminary discussion:

* Should we still handle this after release Ruby 3.2.1+?
    * with Ruby 3.3


Discussion:

* shyouhei: my feeling is we should backport this, but should ultimately be decided by the maintainer.
* naruse: I'm for fixing this in 3.3+. For 3.2, though it's up to nagachika, I wouldn't merge if I was still the maintainer.

Conclusion:

* (should consult @nagachika)


### [[Bug #19749]](https://bugs.ruby-lang.org/issues/19749) Confirm correct behaviour when attaching private method with `#define_method`  (jeremyevans0)

* Currently, `#define_method` does not change visibility when defining a method using the same body as it currently has.
* This seems like an oversight and not intentional behavior, can we fix it?

Preliminary discussion:

```ruby
def bar; end

foo = Object.new

# The default visibility is private because of TOPLEVEL
# So should this define a private method?
foo.singleton_class.define_method(:bar, method(:bar))

# Actually it defines a public method. Why?
foo.bar # No error.
```

* Ask Matz: Is it expected?

```ruby
class C
  foo = Object.new
  foo.singleton_class.define_method(:bar) { p :ok }
  foo.bar #=> :ok

  private

  foo = Object.new
  foo.singleton_class.define_method(:baz) { p :ok }
  foo.baz #=> :ok

  foo = Object.new
  foo.singleton_class.class_exec do
    private
    define_method(:qux) { p :ok } # looks like it should define a private method
    foo.singleton_class.define_method(:qux) { p :ok } # should this define a private or public method?
  end
  foo.qux #=> private method `qux' called

  foo = Object.new
  foo.singleton_class.class_exec do
    private
    Object.new.singleton_class.class_exec do
      foo.singleton_class.define_method(:quux) { p :ok }
    end
  end
  foo.quux #=> :ok
end
```

```ruby=
class C; private; define_method(:foo) {}; end; C.new.foo # private
```

```clike
8dced4d2c0f (ko1                       2015-03-08 21:22:43 +0000 1777) const rb_cref_t *
20c38381a84 (nobu                      2013-12-24 14:04:31 +0000 1778) rb_vm_cref_in_context(VALUE self, VALUE cbase)
1fc33199736 (nobu                      2013-12-24 07:28:11 +0000 1779) {
e95de48f1d7 (ko1                       2017-10-26 08:41:34 +0000 1780)     const rb_execution_context_t *ec = GET_EC();
e95de48f1d7 (ko1                       2017-10-26 08:41:34 +0000 1781)     const rb_control_frame_t *cfp = rb_vm_get_ruby_level_next_cfp(ec, ec->cfp);
8dced4d2c0f (ko1                       2015-03-08 21:22:43 +0000 1782)     const rb_cref_t *cref;
ef45a57801a (Jeremy Evans              2019-07-31 17:03:11 -0700 1783)     if (!cfp || cfp->self != self) return NULL;
303cd88d408 (ko1                       2015-12-09 07:15:48 +0000 1784)     if (!vm_env_cref_by_cref(cfp->ep)) return NULL;
2b5bb8a0875 (ko1                       2019-04-05 08:15:11 +0000 1785)     cref = vm_get_cref(cfp->ep);
ae166317a4c (ko1                       2015-03-08 19:50:37 +0000 1786)     if (CREF_CLASS(cref) != cbase) return NULL;
20c38381a84 (nobu                      2013-12-24 14:04:31 +0000 1787)     return cref;
1fc33199736 (nobu                      2013-12-24 07:28:11 +0000 1788) }
```

Discussion:

* mame: even if the current context is private `define_method` defines a public method.
* mame: OTOH we can tweak the context (by e.g. class_exec) to make `define_method` define a private method.
* matz: `class_exec` introduces a new cref so dfaulting to public sounds normal.
* matz: (reading through the code)
* matz: I started to think that define_method shall always define a public method and let people explicitly do `private deinfe_method...`
* matz: ... but it's too late.
* ko1: when define_method is called with receiver it seems natural to define a public method
* mame: there is no way to distinguish if a method is called by explicitly stating its receiver or not.

Conclusion:

* matz: reject Jeremy's patch because method visibility is a property of a class that has the method, not method itself.
* matz: keep current behavior
  * Matz preference:
    * always public -> too late
    * depend on whether receiver is present (with receiver, it's public) -> seems to difficult to implement
    * keep current behavior


### [[Bug #19726]](https://bugs.ruby-lang.org/issues/19726) Script loaded twice when requiring self (jeremyevans0)

* Do we want to add the full path of the main script to `$LOADED_FEATURES` to prevent it from being loaded again if required?

Preliminary discussion:

a.rb:
```ruby
#!/usr/bin/env ruby

require "./b"

C = 42

return unless __FILE__ == $0

puts C
```

b.rb:
```ruby
require "./a"
```
This results in:
```
$ ./a.rb 
./a.rb:5: warning: already initialized constant C
/home/johannes/t/a.rb:5: warning: previous definition of C was here
42
```

Discussion:

* mame: this is because the main script is not included in the `$LOADED_FEATURES`.
* mame: OP proposes to add it.
* nobu: Why does OP need to do such thing at the first place?
* shyouhei: if this change breaks something it means the problematic code intentionally loads twice
* nobu: adding a file-without-an-extension into `$LOADED_FEATURES` could be a problem
  * if a command's file name conflicts with some libary there would become no way to load that library then. 

```
$ ruby -e '$LOADED_FEATURES << "foo"; p require "foo"'
false
```

```ruby
# /path/to/bin/irb
#!/usr/bin/env ruby

require "irb"
# before: loads ".../lib/irb.rb" or ".../lib/irb.so"
# after: does not load anything
```

* akr: proposed solution breaks existing codes, which is NG.
* shyouhei: the one who loads the main script should only be affected.

Conclusion:

* matz: reject because of `irb` issue. if there is real issue, consider more.

### [[Bug #19544]](https://bugs.ruby-lang.org/issues/19544) Custom quotes inconsistency (jeremyevans0)

* Do we want to support whitespace characters as terminators for `%w` arrays?
* I don't think it is worth making changes to support this.

Preliminary discussion:

* nobu: Recently I fixed this at https://bugs.ruby-lang.org/issues/19563 (probably)

Conclusion:

* nobu fixed this before discussing anything.


### [[Feature #19755]](https://bugs.ruby-lang.org/issues/19755) `Module#class_eval` and `Binding#eval` use caller location by default (byroot)

* Code that use `eval` or `class_eval` without providing a `filename` is very hard to track down.
* Defaulting to something like `"(eval in #{caller_locations(1, 1).first.path})"` would be way better than the current situation.

Preliminary discussion:

* mame: I am basically for it
* mame: Performance to take the caller's filename and lineno?
* nobu: I think it does not matter comparing to parsing and compilation
* mame: Some gems may compare `__FILE__ == "(eval)"`?

```
mame@gem-codesearch:~$ gem-codesearch -f \.rb '"(eval)"' | grep __FILE__
2020-06-06 /srv/gems/galaaz-0.5.0/lib/util/exec_ruby.rb:      eval(code, RCbinding, __FILE__, __LINE__ + 1) if (options[["eval"]] >> 0)
mame@gem-codesearch:~$ gem-codesearch -f \.rb "'(eval)'" | grep __FILE__
```

* mame: No case found

Discussion:

* nobu: rubyspec has specs to see if a backtrace include `(eval)` so we have to fix them.
* mame: The first format proposed may show non-existing line

```ruby=
1: s = <<END
2: 
3: def foo = nil
4: END
5: 
6: eval s
7: p method(:foo) #=> #<Method: foo@(eval in /tmp/foo.rb):8>   or   (eval at /tmp/foo:6):2
```

* mame: Rather I like the second proposed format
* shyouhei, akr, ko1, nobu: agreed
* matz: I have no strong opinion. I am okay whether we change or not change it, as long as the compatibility issue is okay.
* nobu: when `nil` is passed as a filename what will happen?

```
eval "foo", nil, 10 # (eval)
```

* ko1: it should be handled as default

Conclusion:

* matz: give it a try, `(eval at /tmp/foo:6):2` format
* matz: class_eval, module_eval, instance_eval, Kernel#eval, Binding#eval


### [[Feature #19351]](https://bugs.ruby-lang.org/issues/19351) Promote bundled gems at Ruby 3.3 (hsbt)

* Share the current status of this.
* How show deprecate message of this feature? It's hard to detect dependency of Gemfile in `require`.

Preliminary discussion:

*

Discussion:

* hsbt: We would like to promote several gems to bundled ones.
* hsbt: This would make everyone tweak their Gemfile, which could be a lot of trouble.
* hsbt: options:
  * Patch bundler
    * pros: works
    * cons: gem authors would never be aware of the transition
      * naruse: maybe rubocop can help
      * runtime warning when requiring them can also be an option
        * naruse: I want such suggestion for everything that once has been in our official code base.
  * Just warn and leave `LoadError`
* mame: should people write dependencies explicitly or leave them forever?
* hsbt: I want everyone be explicit.
* akr: what happens for people who don't use bundler (e.g. from irb)?
* hsbt: no warning then, and after making them bundled gems `require` would just fail.
* naruse: it's nice 

Conclusion:

* hsbt: will reply


### [[Feature #19630]](https://bugs.ruby-lang.org/issues/19630) Deprecate `Kernel.open("|command-here")` due to frequent security issues (mdalessio)

* Even experienced developers can forget about the `|` feature, leading to potential security issues.
* Proposal is to deprecate `Kernel.open` pipe commands in 3.3, and plan removal in Ruby 4.
* Should we also deprecate pipe commands in `IO` methods that support them? (`IO.binread`, `foreach`, `readlines`, `read`, `write`)

Discussion:

* shyouhei: what should people do instead for the purpose?
* matz: Should be `IO.popen`
* akr: not against deprecation
* mame: should open-uri also be affected?
* akr: okay.
* nobu: for Kernel.open only?
* akr: I support deleting pipes everywhere.  It's too hard to remember which one supports which.

```ruby
File.read("|ls") #=> No such file or directory
IO.read("|ls")   #=> current: files in cwd
                 #=> new: No such file or directory ?
```

```
$ gem-codesearch "IO.foreach\\(\"\|"
2017-01-09 /srv/gems/exerb-6.0.1/vendor/mkexports.rb:    IO.foreach("|#{self.class.nm} --extern --defined #{objs.join(' ')}", &block)
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_io.rb:    IO.foreach("|" + EnvUtil.rubybin + " -e 'puts :foo; puts :bar; puts :baz'") {|x| a << x }
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/win32/mkexports.rb:    IO.foreach("|#{self.class.nm} --extern --defined #{objs.join(' ')}", &block)
mame@gem-codesearch:~$ gem-codesearch "IO.readlines\\(\"\|"
2023-01-09 /srv/gems/ruby-staci-2.2.9/ext/oci8/oraconf.rb:  | #{IO.readlines("|otool -L #{file}").join('  | ')}
$ gem-codesearch "IO.read\\(\"\|"
2012-04-11 /srv/gems/freshtrack-0.7.0/lib/freshtrack/time_collectors/punch.rb:        time_data = IO.read("| punch list #{project} #{option_str}")
2020-03-17 /srv/gems/mork-0.15.0/spec/mork/npatch_spec.rb:      b = IO.read("|convert #{fname} gray:-").unpack 'C*'

```

* nobu: it seems nobody on the ticket is requestig to deprecate them though.
* akr: if we are going to separate files and commands in the long term why not do so today?

* nobu: [io-pipe](https://github.com/nobu/io-pipe)
  ```ruby
  require "io/pipe"
  using IO::Pipe
  `ls`.each {|fn| ...}
  ```

Conclusion:

* matz: 3.3 to add deprecation warning for all methods that accepts `|` today.

### [[Feature #19600]](https://bugs.ruby-lang.org/issues/19600) `Comparable#clamp?` (nobu)

* Currently, we have pairs of non-predicate and predicate methods like `String#match` and `String#match?`. Along this line, I propose the following. They are brain-friendly, and make programmers happier by saving them from terminology hell.

  1. Since by #19588, `Comparable#clamp`'s behavior is made the same as `Range#cover?` for range arguments, alias `Range#cover?` as `Range#clamp?`.
  2. Synchronize the specification of `Comparable#between?` with `Comparable#clamp`, i.e.,
    a. Allow `Comparable#between?` to take a range argument, and
    b. Allow `Comparable#between?` to take `nil` as either or both of its arguments, or as either or both ends of its range argument.
  3. Alias `Comparable#between?` as `Comparable#clamped?`

Discussion:

* nobu: clamp (as an operation) doesn't sounds right for adding `?`
* akr: what should this return? `true` for what?
* shyouhei: OP says it should be an alias of `cover?`
* nobu: not against `Comparable#between?` extension part.
* akr: that shall be a separate issue though.
* matz: To me `between` takes two arguments is the nature of the operation.

Conclusion:

* matz: will reject

### [[Feature #19752]](https://bugs.ruby-lang.org/issues/19752) Support `--backtrace-limit` in `RUBYOPT` (nobu)

* The `--backtrace-limit` option is not currently allowed to appear in the `RUBYOPT` environment variable, but that appears to be a mistake.
* Unlike other long options which are not allowed in `RUBYOPT` (e.g. `--copyright`, `--version`, `--dump` and `--help`) it does not cause the interpreter to terminate, and cannot cause harm if read from the environment.
* During the initial discussion about the `--backtrace-limit` feature, Matz [suggested](https://bugs.ruby-lang.org/issues/8661#note-27) that he expected `RUBYOPT` to allow this option.
* The `--backtrace-limit` option should also allow a value of -1 since that is legitimate (and the default), and be documented on Ruby’s `man` page.

Discussion:

* shyouhei: there are options that takes effect in RUBYOPT, and those which don't.
* mame: I added this option but didn't intend to prohibit RUBYOPT for it.
* mame: the proposed pull request does have a glitch though.  It allows negative integer for the limit (for non-obvious reason).
* matz: `RUBYOPT=--backtrace-limit=-1 ruby --backtrace-limit=5` doesn't work.  Direct arguments takes precedence.
* nobu: The proposed patch prefers RUBYOPT to the command line option.
* matz: No, it should be fixed.
* matz: ~~I would prefer `--no-backtrace-limit`~~ Changed my mind, -1 is okay to me.
* matz: `RUBYOPT=--backtrace-limit=5 ruby --backtrace-limit=-1` will work

Conclusion:

* matz: The idea that `--backtrace-limit` (and `=-1` means unlimited) takes effect on RUBYOPT accepted.
* matz: but the proposed PR has prolems.  Can't immediately merge it.


### [[Feature #19757]](https://bugs.ruby-lang.org/issues/19757) Add new C API to create a subclass of `Data` (nobu)

* I propose a C API `rb_data_define` which crates a subclass of `Data`.

  ```C
  /**
   * Defines an anonymous data class.
   *
   * @endinternal
   *
   * @param[in]  super           Superclass  of the  defining  class.   Must be  a
   *                             descendant of ::rb_cData, or 0 as ::rb_cData.
   * @param[in]  ...             Arbitrary number of  `const char*`, terminated by
   *                             NULL.  Each of which are the name of fields.
   * @exception  rb_eArgError    Duplicated field name.
   * @return     The defined class.
   */
  VALUE rb_data_define(VALUE super, ...);
  ```

```ruby=
class Foo < Data
end
Foo.define(:a, :b, :c)


C = Foo.define(:a, :b)
p C.ancestors #=> [C, Foo, Data, Object, Kernel, BasicObject]
```

```clike=
VALUE rb_struct_define_without_accessor(const char *class_name, VALUE super, rb_alloc_func_t alloc, ...);
```

### [[Feature #19057]](https://bugs.ruby-lang.org/issues/19057) Hide implementation of `rb_io_t`.

* naruse: it is severe that Ruby 3.3 does not work with unicorn. I will revert the change when I will release the next preview