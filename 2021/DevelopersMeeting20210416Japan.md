---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210416Japan

https://bugs.ruby-lang.org/issues/17734

## DateTime and location

* 4/16 (Fri) 13:00-17:00 JST @ Online

## Next Date

* 5/21 (Fri) 13:00-17:00 JST @ Online
  * pre-reading: 5/19 (Wed)

## Announce

## About release timeframe

* 3.0.1/2.7.3/2.6.7/2.5.9 have been released

## Check security tickets

[secret]

## Tickets

### [[Feature #17398]](https://bugs.ruby-lang.org/issues/17398) `SyntaxError` in endless method (jeremyevans0)

* I don't believe this is a bug, but maybe it is a useful feature.
* @mame has a patch that allows commands as the RHS of a endless method.
* However, @mame's patch doesn't allow direct usage with method visibility methods such as private.
* Do we want to support this syntax, or can this be closed (or moved to Feature)?

Preliminary discussion:

* mame: I created the patch, but I'm not keen to merge it.. The implemention is redundant, and the syntax will be more confusing.

Discussion:

```ruby
# current: SyntaxError
# mame's patch: Allow
def foo() = puts "bar"
```

```ruby
# current: SyntaxError
# mame's patch: SyntaxError
private def foo() = puts "bar"
```

```ruby
p var = puts("bar") # OK
p var = puts "bar"  # NG
```

* matz: If Ruby were young, I would accept mame's patch. But now, I don't know which is better.
* matz: I like the patch. I'm positive but the limitation is not preferable
* nobu: How about moving the ticket to Feature tracker and continue to discuss?

off topic:

* ko1: How about PEG?
* matz: Some mruby variant uses lemon parser generator instead of bison, for memory efficiency. It is possible to replace it

Conclusion:

* matz: move it to Feature tracker and will reply


### [[Bug #17403]](https://bugs.ruby-lang.org/issues/17403) Remove `Fixnum` and `Bignum` (jeremyevans0)

* Do we want to remove Fixnum and Bignum in Ruby 3.1?
* If not, can we decide on a later version where they can be removed, such as 3.2 or 4.0?

Preliminary discussion:

* mame: maybe postponed to 3.2 because naruse-san says 3.1 will have no incompatibility in principle

Discussion:

* naruse: No at least 3.1

Conclusion:

* postpone to 3.2


### [[Bug #16983]](https://bugs.ruby-lang.org/issues/16983) `RubyVM::AbstractSyntaxTree.of(method)` returns meaningless node if the method is defined in eval (jeremyevans0)

* `RubyVM::AbstractSyntaxTree.of(method)` relies on reading the file with the method source, which could have changed or could not exist at all.
* I don't think we can work around that without keeping the abstract syntax tree or source code of all methods in memory, which seems wasteful.
* Is the current behavior a bug, or should we just accept the current behavior as spec and close this?

Preliminary discussion:

* ko1: Maybe we should a flag to represent that a method object is created in eval (and make `AST.of` an error).

Discussion:

* (no discussion)

Conclusion:

* ko1: Will do.


### [[Bug #9542]](https://bugs.ruby-lang.org/issues/9542) `Delegator` does not delegate protected methods (jeremyevans0)

* Do we want to add a way to tell if a method was called with an implicit receiver or self?
* We may be able to implement this via a VM frame flag and a method to check it.
* If we don't want to add such a way, can we accept the current behavior as spec and close this?

Preliminary discussion:

* nobu: current behavior is expected, other than the error message.
  ```ruby
  require "delegate"

  class Cow
    def unprotected_moo
      "mooooo"
    end

    protected def protected_moo
      "guarded mooooo!"
    end
  end

  my_cow = Cow.new

  puts SimpleDelegator.new(my_cow).unprotected_moo
  # => "mooooo!"
  puts SimpleDelegator.new(my_cow).protected_moo
  # => "guarded mooooo!" in 2.0
  # => undefined method `protected_moo' (NoMethodError) in 2.1...
  puts my_cow.unprotected_moo
  # => "mooooo!"
  puts my_cow.protected_moo
  # => protected method `protected_moo' called (NoMethodError) in 2.1...
  ```

* mame: protected method must not be called in this calling style.

Discussion:

* nobu: the error message is wrong; it should be "protected method xxx called" instead of "undefined method xxx called"

Conclusion:

* matz: the behavior change between 2.0 and 2.1 is a bug fix.


### [[Bug #11230]](https://bugs.ruby-lang.org/issues/11230) Should `rb_struct_s_members()` be public API? (jeremyevans0)

* This is still public in the C-API.
* Do we want to keep it and close this issue, or do we want to remove it?
* If we want to remove it, how should it be deprecated?

Preliminary discussion:

```shell
ko1@aluminium:~$ csearch rb_struct_s_members
jameskilton-rice-1.2.0/test/test_Struct.cpp:  // can't call rb_struct_s_members, because this isn't a struct yet
jameskilton-rice-1.2.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s));
jameskilton-rice-1.2.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s2));
jameskilton-rice-1.2.0/test/test_Struct.cpp:    Array members(rb_struct_s_members(s2));
metasm-1.0.4/metasm/os/gnu_exports.rb: rb_struct_alloc rb_struct_aref rb_struct_aset rb_struct_define rb_struct_getmember rb_struct_iv_get rb_struct_members rb_struct_new rb_struct_s_members
metasm-1.0.4/metasm/os/windows_exports.rb: rb_struct_getmember rb_struct_iv_get rb_struct_members rb_struct_new rb_struct_s_members rb_svar rb_sym_all_symbols rb_symname_p rb_sys_fail rb_sys_warning
rice-2.2.0/test/test_Struct.cpp:  // can't call rb_struct_s_members, because this isn't a struct yet
rice-2.2.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s));
rice-2.2.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s2));
rice-2.2.0/test/test_Struct.cpp:    Array members(rb_struct_s_members(s2));
rice-jdguyot-1.4.0/test/test_Struct.cpp:  // can't call rb_struct_s_members, because this isn't a struct yet
rice-jdguyot-1.4.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s));
rice-jdguyot-1.4.0/test/test_Struct.cpp:  Array members(rb_struct_s_members(s2));
rice-jdguyot-1.4.0/test/test_Struct.cpp:    Array members(rb_struct_s_members(s2));
rubyosa19-0.6.2/ext/rubyosa/osx_intern.h:VALUE rb_struct_s_members(VALUE);
open script_core-0.2.6/ext/enterprise_script_service/mruby/mrbgems/mruby-struct/src/struct.c: no such file or directory
wurlinc-rice-1.4.0.4/test/test_Struct.cpp:  // can't call rb_struct_s_members, because this isn't a struct yet
wurlinc-rice-1.4.0.4/test/test_Struct.cpp:  Array members(rb_struct_s_members(s));
wurlinc-rice-1.4.0.4/test/test_Struct.cpp:  Array members(rb_struct_s_members(s2));
wurlinc-rice-1.4.0.4/test/test_Struct.cpp:    Array members(rb_struct_s_members(s2));
```

* ko1: it seems nobody use on lib or app.

Discussion:

* shyouhei: There also is `rb_struct_members()`.  What is the difference?
* nobu: `rb_struct_s_members()` takes a struct class, while `rb_struct_members()` takes its instance.
* matz: It might be difficult to implement `rb_struct_s_members()` on top of `rb_struct_members()` because doing so needs a temporary instance.
* shyouhei: It seems there also is `rb_struct_s_members_m()` which can be called via `Struct.members`.
* ko1: Users of `rb_struct_s_members()` can resort to call that method via `rb_funcall`, theoretically.
* ko1: But in reality it seems there is no practical usages.
* shyouhei: Delete it then.
* nobu: Are there really no usages...?
* naruse: We could revive this if any later.
* shyouhei: Should we provide such transition path? Or just delete it?

Conclusion:

* Everyone agreed with the removal, but it should be removed after 3.2


### [[Feature #17749]](https://bugs.ruby-lang.org/issues/17749) Add `Module#source_location` (tenderlovemaking)

  * It's similar to the const_source_location feature, but easier to use when you don't know the constant name, or enclosing const
  * This is useful for debugging

Preliminary discussion:

* ko1: weakly related: https://github.com/ruby/ruby/pull/4370
* mame: I don't see actual use case. But I guess it would be useful for memory profiling purpose or what not? I'm for this proposal
* ko1: I have two concerns about the patch. (1) a class created by `Class.new` will have a different result (allocated site) from `const_source_location` (site assigned to a constant). (2) `rb_const_source_location_of()`  seems to be declared and not defined.
* ko1: Aaron said it is allocation site. https://github.com/ruby/ruby/pull/4324#issuecomment-816319444
* mame: `const_source_location` and `source_location` is similar but different. I don't think it is a good idea to introduce similar features in terms of maintenance, unless it is really needed.

Discussion:

```ruby
module A
  class B
  end
end

p A::B.source_location
```

Conclusion:

* matz: I don't bother but at least there are people who need this...
* mame: I'll ask the usecase.

### [[Feature #17753]](https://bugs.ruby-lang.org/issues/17753) Add `Module#namespace` (tenderlovemaking)

  * `A::B.namespace` will return `A`
  * This is to help with finding "sibling" constants of `B` so we don't have to parse the constant name
  * This is also useful for debugging

Preliminary discussion:

* it seems difficult to name...

Discussion:

* nobu: It resembles the nesting of a module but not the same.
* matz: I don't want to accept this, at least, as-is. If the receiver is literally like `M::N`, `M::N.namespace` is obvious. So, it will make sense in form like `some_klass.namespace`, but I'm unsure if it deserves as the name.
* matz: I'd like to use the general good word "namespace" for another concept.
* knu: There is demodulize in Rails.
    * https://www.rubydoc.info/github/rubyworks/facets/String:modulize
    * akira: this is very slightly different from the proposed one.
* mame: We will next want a method that returns `B` from `A::B`
* ko1: What does they return?
    * `Class.new{}.namespace`
    * `C.namespace` # toplevel module

Conclusion:

* matz: Please explain use case, and propose other names then `namespace`. (outer_scope also looks weird)


### [[Feature #17762]](https://bugs.ruby-lang.org/issues/17762) A simple way to trace object allocation (mame)

* I want `require "objspace/trace"` or something which is a very useful debugging tool to identify the allocation site of an object.

Preliminary discussion:

* mame: Enabling a feature just by `require` is arguable. And extending `Kernel#p`  is more arguable.

Discussion:

* matz: looks good.

Conclusion:

* matz: accepted


### [[Feature #17682]](https://bugs.ruby-lang.org/issues/17682) `String#casecmp` performance improvement (dan0042)

* Is it ok to make `casecmp` faster?

Preliminary discussion:

* mame: It is a bit difficult to maintain, but if noby says okay, I'm okay

Discussion:

* shyouhei: naruse can you review?
* naruse: I will.


### [[Feature #15198]](https://bugs.ruby-lang.org/issues/15198) Add `Array#intersect?` (marcandre)

* Ok to add?

Preliminary discussion:

* ko1: is there many use cases for this?
* mame: There are already Array#intersection and Array#union. Can we add `Array#intersect?`? `Array#union?` looks non-sense. (I'm not against, but just want to confirm)
* knu: set.rb has already `Set#intersect?` and I've seen many use cases for this.
* knu: Extending `include?()` to accept an arbitrary number of values could be an option, but that would open Pandora's box when many other builtin classes have `include?.
* nobu: Should it accept multiple arrays?
* -- maybe, but not part of this request.

Discussion:

* (no discussion)

Conclusion:

* matz: OK.


### [[Feature #17795]](https://bugs.ruby-lang.org/issues/17795) `before_fork` and `after_fork` callback API (byroot)

- Many libraries out there use various tricks to detect the process being forked, most commonly `Process.pid != @pid`, but `glibc` no longer cache the PID so these libraries end up doing syscalls in tight loops.
- If a callback API is too complicated, I proposed a simplee alternative that is to have `Kernel.fork` be a simple delegator to `Process.fork` https://github.com/ruby/ruby/pull/4361, This would makes it easy for libraries to decorate fork to have their own callbacks on either before or after fork.

Preliminary discussion:

* ko1: I want this for debugger implementation (3 hooks).
* mrkn: It is good to provide both after_fork_parent and after_fork_child instead of one after_fork.
* mame: I'm unsure if Kernel#fork and Process.fork are enough. Process.daemon is really needed?
* nobu: `IO.popen("-")` is also a fork
* mame: How about extension libraries? Maybe we don't have to care them?

Discussion:

* mrkn: Python provides C-API to call registered hooks. https://github.com/python/cpython/blob/master/Modules/posixmodule.c#L570-L588
* background ticket: https://bugs.ruby-lang.org/issues/5446#note-18
* shyouhei: Understand both byroot's needs and jeremyevens' concern.
* ko1: Is it possible to reroute Jeremy's problem?
* shyouhei: At least user-defined `fork` appears on backtrace on errors, which is better than silently break things.
* akr: Jeremy's concern looks valid. An appropriate way to handle DB connection before and after fork depends on each application, not libraries. So, this proposed API may lead to misuse. Rather, supporting PID monitoring looks good. If getpid is slow, it may be good to provide another way such as Process.fork_level that returns how many "fork" is called.

```ruby
p Process.fork_level #=> 0
if fork
  p Process.fork_level #=> 0
else
  p Process.fork_level #=> 1
end
```

* akr: Process.daemon is very special, so it doesn't have to call the hooks (or update fork_level)

* mame: There are two fork methods: Kernel#fork and Process.fork. All libraries that want to wrap them need to wrap both.
* ko1: A library A wraps only Kernel#fork, and library B wraps only Process.fork

Conclusion:

* akr: Will counter-propose fork_level.


### [[Feature #17016]](https://bugs.ruby-lang.org/issues/17016) Add `Enumerable#accumulate` (parkerfinch)

* Is the name `#accumulate` acceptable?
* Is this feature ok to add?

Preliminary discussion:

* mame: I'm unsure about use case
* mrkn: cumulative sum is reasonable, but other examples are not so convincing
* knu: eregon's code looks great and intuitive

```ruby
val = 0
p (0..).lazy.map { |x| val += x }.first(4) # => [0, 1, 3, 6]

val = 0
p [0] + (1..).lazy.map { |x| val += x }.first(3) # => [0, 1, 3, 6]
```

* knu: This may be generalization of `Enumerator.produce` in which a value to yield and a value to pass to the next iteration are the same.

Discussion:

```ruby
[1, 2, 3].accumulate(0, &:+) => [0, 1, 3, 6]
```

* https://reference.wolfram.com/language/ref/Accumulate.html
* https://reference.wolfram.com/language/ref/FoldList.html

* mame: ↓ is crystal clear to me

```ruby
ret = []
accum = 0
ary.each do |e|
  accum += e
  ret << accum
end
```

Conclusion:

* conclusion is not reached

### [[Bug #17775]](https://bugs.ruby-lang.org/issues/17775) Backport to fix build failures

[for 3.0](https://github.com/ruby/ruby/pull/4358) ✔
[for 2.7](https://github.com/ruby/ruby/pull/4359)
[for 2.6](https://github.com/ruby/ruby/pull/4360)
