---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20201120Japan

https://bugs.ruby-lang.org/issues/17299

## DateTime and location

* 11/16 (Mon) 13:00-17:00 JST @ Online

## Next Date

* 12/10 (Thu) 13:00-17:00 JST @ Online
  * pre-reading: 12/8 (Tue) 13:00-15:00 JST @ Online

## Announce

## About 3.0 timeframe

Next: preview 2

* TypeProf is bundled
* reline has been improved the performance
* Ractor misc.

## Check security tickets

[secret]

### [[Feature #17303]](https://bugs.ruby-lang.org/issues/17303) Make webrick to bundled gems or remove from stdlib (hsbt)

* Any objection?

Preliminary discussion:

* `ruby -run -e httpd` should output a warning like "please gem install webrick"

Discussion:

* mame: many vulnerability reports are comingh though webrick itself is not for production but for testing/example
* matz: neutral
* naruse: steps are
  * ping normalperson
  * remove from bundled gem (still exist in test/lib)
    * 2.6/2.7's webrick are still supported until EOL
  * `gem server` should work with `gem install webrick`
    * ask @hsbt
  * `-run` should work with `gem install webrick`
    * @nobu will handle this
  * rdoc should work with `gem install webrick`
    * @aycabta will take a look
* mame: ultimately what would happen is not clear until we actually delete.

Conclusion:

* ask normalperson first, and then matz will decide


### [[Feature #17143]](https://bugs.ruby-lang.org/issues/17143) Improve support for warning categories (jeremyevans0)

* I researched the Python warning categories, and provided some analysis.
* Can we decide on warning categories so they can be implemented in time for Ruby 3?

Preliminary discussion:

* nobu: Don't we need a hierarchy like exceptions?
* mame: some categories may be orthogonal
* nobu: It should have been tags instead of categories

Discussion:

* akr: I assume that what Jeremy wants to do is to solve #17055
* akr: Those issues should be addressed per class/method, not warning category
* naruse: In regard to [#17055](https://bugs.ruby-lang.org/issues/17055), Instead of new feature to suppress warning, how about assuming `@foo = nil` is that. `instance_variable_get` and so on should also be mimicked
    ```ruby
    class Foo
      def initialize(x = "str")
        @foo = nil if cond?
      end
      def init
        @foo = nil
      end
      def foo
        @foo
      end
  
      # another idea
      attr_default_nil_ivar :foo
      attr_reader :foo # deafult nil
    end
    Foo.new.foo
    ```

    ```ruby
    class C
      attr_reader :foo
    end

    C.new.foo # no warning
    C.new.instance_variable_get(:@foo) #=> t.rb:7: warning: instance variable @foo not initialized
    ```
* akr: In regard to method redefinition warning, `alias foo foo` now works.  It has no race condition unlike method definition after `remove_method`.  I agree that it is too cryptic, so I prefer an official API like `Module#allow_method_redefinition(:foo)`
* matz: I don't like such an API
    ```ruby
    def foo
    end

    alias foo foo # a magic to suppress redefinition warning
    allow_method_redefinition(:foo) # more official? API

    def foo
    end
    ```

* akr: Another idea is adding keyword argument for `define_method`: `define_method(name, redefine: true) {}` or `define_method(name, method, redefine: true)`

* mame: how about to stop unassigned warning on `@_foo` naming?
* naruse, nobu: disagree.

* matz: How about completely deleting "uninitialize ivar" warnings?
* ko1: I object. I afraid typo. The warning is only emitted only in `-w` mode.
* naruse: I remember no experience that the warning was actually useful
* shyouhei: How about other warning categories than uninit-ivar and method-redef?
* akr: pend them until we face an actual use case for each category

* shyouhei: How many use cases is the method redefinition required?
* mame: As far as I know, Kernel#pp is the typical use case, but I'm unsure how it is often
* matz: `alias foo foo` is well-known technique, so it is not so rare

```ruby=
class C
  def bar
    @foo
  end
end

p C.allocate.bar
#=> t.rb:4: warning: instance variable @foo not initialized
#   nil

# magic comment example
class C
  def bar
    # uninitialized-warning: false
    @foo
  end
end

p C.allocate.bar
#=> nil
```

Conclusion:

* About uninit-ivar: matz will propose complete removal of the warnings
* About method-redef: matz will reply to #17055
* About the other warning categories: discuss each category with actual use cases
* Matz will reply to #17055
* Matz thinks that warning categories like "uninit-ivar" and "method-redef" are too fine-grained, but they are acceptable themselves. However I don't like them as a measure to address #17055


### [[Bug #10845]](https://bugs.ruby-lang.org/issues/10845) Subclassing String (jeremyevans0)

* In [#6087](https://bugs.ruby-lang.org/issues/6087), matz decided that in Ruby 3.0, methods for Array subclasses should return arrays and not subclass instances.
* The same issues that apply to Array subclasses also apply to String subclasses.
* Do we want to make String subclass methods return strings instead of subclass instances?
* If so, is the pull request OK?
* Note that changing the behavior of String methods will require changes in Rails.

Preliminary discussion:

* No opinion

Discussion:

* matz: is Rails affected by this?
* mame: seems so.
* akira: do someone remember why we thought we should not do this?
* (people starting to remember...)

```ruby
$ ruby -ve 'class A < Array; end; p A.new([0, 1, 2]).drop(1).class'
ruby 2.7.2p137 (2020-10-01 revision 5445e04352) [x86_64-linux]
A

$ ./miniruby -ve 'class A < Array; end; p A.new([0, 1, 2]).drop(1).class'
ruby 3.0.0dev (2020-11-18T07:28:46Z master dc3a65bd99) [x86_64-linux]
Array
```

* mame: As far as I recall, matz once changed his mind about Array change. Is matz okay about the recent change of Array methods?
* matz: Okay if the compatibility issue is acceptable. Let's experiment with preview-2.
* a_matsuda: Changing String will break Rails (as Jeremy himself says)
* matz: If rails can fix the issue rapidly, and if the libraries work, I'm okay
* naruse: I'm a bit against because Ruby 2.7 breaks applications so much. Though Ruby 3.0 doesn't provide enough benefit for Rails users to break more compatibility.
* akr: Array subclasses may have their own invariant. Returning an subclass instance will easily break the invariant, so if a user defins a subclass of Array, they should override all array-returning methods
* shyouhei: Returning a direct array instance consistently prevents such situations.
* shyouhei: or to introduce a new protoocl which interfaces with the situation well.

Conclusion:

* matz: Accept the array change and let's revert if there is a trouble
* naruse: pending this topic because this takes too long.


### [[Bug #11022]](https://bugs.ruby-lang.org/issues/11022) opening an eigenclass does not change the class variable definition context (jeremyevans0)

* This was discussed in the December 2019 meeting, but a decision was not made.
* Considering [#14012](https://bugs.ruby-lang.org/issues/14012) was rejected last meeting, can we also reject this?

Preliminary discussion:

* [DevelopersMeeting20190919Japan - Google Docs](https://docs.google.com/document/d/1oYWVhH6BEgTNy8PxMUd-MS3JUK0kQMw43q5saBp8aZk/edit#heading=h.gias3pb645mg) 
    * matz: let me consider for a while.
* [DevelopersMeeting20191220Japan - Google Docs](https://docs.google.com/document/d/18v0UWXwwKaSvOFp1UQbEnt_4p0BMTdoGgcNfjyAWTKs/edit) 
    * matz: need more time…

Discussion:

* ko1: Do you still need time matz?
* matz: I'd prefer rejecting this and keep things as-is.

Conclusion:

* matz: admit the current behavior as the spec


### [[Bug #7844]](https://bugs.ruby-lang.org/issues/7844) include/prepend satisfiable module dependencies are not satisfied (jeremyevans0)

* This was discussed in the December 2019 meeting, but a decision was not made.
* Considering the other improvements to include/prepend in 3.0, it would be a good time to fix this.

Preliminary discussion:

* [DevelopersMeeting20190919Japan - Google Docs](https://docs.google.com/document/d/1oYWVhH6BEgTNy8PxMUd-MS3JUK0kQMw43q5saBp8aZk/edit#heading=h.gias3pb645mg) 
    * matz: Let me consider for a while. My homework.
* nobu: Seems better than the current behavior.
* mame: Agreed. I think that Jeremy's compromise proposal looks reasonable, though it may break some existing code
* nobu: Let's break.

Discussion:

* ko1: did you do your homework, Matz?
* nobu: No "right" solution exist for this problem.  Cannot satisfy every possible dependency.

```ruby
module P; end
class C1; end
class C2 < C1; end
C1.prepend P
C2.prepend P
p C2.ancestors #=> [P, C2, P, C1, Object, Kernel, BasicObject]
 
module P; end
class C1; end
class C2 < C1; end
C2.include P
C1.include P
p C2.ancestors #=> [C2, P, C1, P, Object, Kernel, BasicObject]
 
module P; end
module Q
  prepend P
end
module R
  prepend P
end
class C
  include Q
  include R
end
p C.ancestors #=> [C, P, R, Q, Object, Kernel, BasicObject]
```

* nobu: Jeremy's patch seems generate a reasonable list.
* matz: what list?
* nobu: it's `P A S Q R C`.
* matz: Hmm... OK, let's apply this and see what happens.

Conclusion:

* matz: Patch accepted.

### [[Bug #11213]](https://bugs.ruby-lang.org/issues/11213) defined?(super) ignores respond_to_missing? (jeremyevans0)

* I agree with Koichi that it is best to accept the current behavior as spec.
* Can we reject this?

Preliminary discussion:

* mame: agreed with ko1 and jeremy

* ko1: we can't call `respond_to_missing?` normally on `defined?(super)` because it calls `C1#respond_to_missing?` (not `C0#respond_to_missing?`)

```ruby
class C0
  def respond_to_missing? *args
    false
  end

  def method_missing *args
    p [:method_missing] + args
  end
end

class C1 < C0
  def respond_to_missing? *args
    true
  end

  def foo
    super
    defined?(super)
  end
end

C1.new.foo
```

* ko1: I support "(1) Ignore this issue as a spec (or known issue)" because there is no person who worry about it. But we can fix this issue in a future.
* nobu: https://github.com/ruby/ruby/pull/3777

Discussion:

* 

Conclusion:

* already fixed.


### [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) Expose rb_fstring and its family to C extensions (byroot)

* The feature itself was approved, however we're still waiting on an agreement on the naming of one function (https://github.com/ruby/ruby/pull/3586)
* Both `json` and `msgpack` now call `String#-@`, and could benefit from large allocation reduction if they could call `rb_fstring_cstr` instead.

Preliminary discussion:

* ko1: will check later

Discussion:

*

Conclusion:

* already merged with `rb_interned_str`, and another issue is found. Nobu is looking.


### [[Bug #17197]](https://bugs.ruby-lang.org/issues/17197) Yielding arity for `Hash` methods (marcandre)

* `Hash#each` with arity 1: confirmed?
* `#select`, `#keep_if`, `#delete_if`, `#reject` and `to_h` should also be changed, right?
* `#map`: should only accept arity 1, right?

Preliminary discussion:

* mame: Now `Hash#each`'s arity is consistently 1

```ruby
# 2.7
{ a: 1 }.each(&->(k, v) { }) #=> ok

# master
{ a: 1 }.each(&->(k, v) { }) #=> ArgumentError
{ a: 1 }.each {|k| p k }     #=> [:a, 1]
```
https://github.com/ruby/ruby/commit/47141797bed5
https://bugs.ruby-lang.org/issues/12706

* mame: It breaks a normal use case (Hash#select with a normal block)
* mame: I like marcandre's proposal, but I'm afraid if it is too late to experiment. How about trying it in 3.1?

```ruby
# 2.7
{ a: 1 }.each {|a| p a }       #=> [:a, 1]
{ a: 1 }.each(&-> a { p a })   #=> [:a, 1]
{ a: 1 }.each(&->(k,v){ p k }) #=> :a
{ a: 1 }.select {|a| p a }     #=> :a

# the current master
{ a: 1 }.each {|a| p a }       #=> [:a, 1]
{ a: 1 }.each(&-> a { p a })   #=> [:a, 1]
{ a: 1 }.each(&->(k,v){ p k }) #=> wrong number of arguments (given 1, expected 2)
{ a: 1 }.select {|a| p a }     #=> :a

# marcandre's proposal
{ a: 1 }.each {|a| p a }       #=> [:a, 1]
{ a: 1 }.each(&-> a { p a })   #=> [:a, 1]
{ a: 1 }.each(&->(k,v){ p k }) #=> wrong number of arguments (given 1, expected 2)
{ a: 1 }.select {|a| p a }     #=> [:a, 1] # !!!
```

Discussion:

* nobu: sounds a bit big.
* mame: JFYI `Hash#select` is not a special-case of `Enumerable#select`; return type is different.
* mame: I found at least one gem would be broken by the proposed change.

Conclusion:

* matz: postpone it, at least in 3.0.


### [[Feature #17291]](https://bugs.ruby-lang.org/issues/17291) Optimize `__send__` call (mrkn)

  * Improve the performance of `__send__` call by the following ways

Preliminary discussion:

* ko1: can we ignore the overriding? I think it should raise an exception.
* mame: digress, but `define_method(:__send__) {}` shows no warning

Discussion:

* shyouhei:  The optimisation itself is okay but should we ignore redefinition of `__send__`?
* ko1: Should prohibit such things.  Exceptions shall be raised on definition of `__send__`
* shyouhei: rspec's redefining `__send__` can be rerouted somehow?
* mrkn: I think that can be reimplemented by tracepoint.
* ko1: really?
* shyouhei: rspec is a show-stopper for this optimisation.
* matz: I'm okay with this.

rsppec's redefining usage:
https://github.com/rspec/rspec-mocks/blob/461d7f6f7f688f69154e410dcd9c7690120e7dbf/lib/rspec/mocks/verifying_double.rb#L45-L53

```
/home/gem-codesearch/gem-codesearch/latest-gem/abaci-0.3.0/vendor/bundle/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/able-neo4j-1.0.0/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.1.3/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/alimentos-alu0100945645-1.0.0/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/argon-1.3.1/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/bsielski_control_flow-1.0.0/vendor/bundle/ruby/2.5.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/comiditaULL-0.1.1/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/comidita_ull-0.1.1/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/coresv_db_backup-0.1.0/vendor/bundle/ruby/2.6.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/dirwatch-0.0.9/vendor/bundle/ruby/2.5.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/dirwatch-0.0.9/vendor/bundle/ruby/2.5.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/echonet_lite-0.1.0/vendor/bundle/gems/rspec-mocks-3.6.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/em-ventually-0.1.3/lib/em-ventually/eventually/minitest.rb:            def __send__(*args, &blk)
/home/gem-codesearch/gem-codesearch/latest-gem/em-ventually-0.1.3/lib/em-ventually/eventually/testunit.rb:            def __send__(*args, &blk)
/home/gem-codesearch/gem-codesearch/latest-gem/embulk-input-druginfo_interview_form-0.1.0/vendor/bundle/ruby/2.4.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/embulk-input-druginfo_interview_form-0.1.0/vendor/bundle/ruby/2.5.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/fr-0.9.1/lib/fr/thunk.rb:    def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/fucknumber-0.1.3/vendor/bundle/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/genkaio-0.0.2/vendor/bundle/ruby/2.5.0/gems/rspec-mocks-3.9.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/genkaio-0.0.2/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/grape-extra_validators-1.0.0/vendor/bundle/ruby/2.4.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/hatena_bookmark_client_for_ruby-0.1.3/vendor/bundle/ruby/2.6.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.4.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/javascript-0.1.0/lib/javascript.rb:      def __send__(meth, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/k-yamada-net-ssh-2.3.0/lib/net/ssh/proxy/command.rb:        def __send__(data, flag)
/home/gem-codesearch/gem-codesearch/latest-gem/logstash-filter-csharp-0.1.0/vendor/bundle/jruby/2.3.0/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/logstash-filter-htmlentities-0.1.0/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.6.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/logstash-filter-zabbix-0.1.2/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/mastermind_adeybee-0.1.4/vendor/bundle/ruby/2.2.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/mdap-0.2.1/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/monero_wallet_gen-0.1.0/vendor/bundle/ruby/2.3.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/mrcooper-logstash-output-azuresearch-0.2.2/vendor/jruby/2.5.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/myco-0.1.10/lib/myco/bootstrap/instance.rb:    def __send__ message, *args
/home/gem-codesearch/gem-codesearch/latest-gem/nabea2-0.1.2/vendor/bundle/ruby/2.6.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/opal-1.0.3/opal/corelib/basic_object.rb:  def __send__(symbol, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/opal-connect-rspec-0.5.0/rspec-mocks/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/opal-rspec-0.7.1/rspec-mocks/upstream/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/pokedex-terminal-0.2.8/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/pomodorokun-0.1.0/bundle/ruby/2.4.0/gems/rspec-mocks-3.8.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/red-4.1.7/lib/source/ruby.rb:  def __send__(method,*args)
/home/gem-codesearch/gem-codesearch/latest-gem/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/rspec-mocks-diag-3.9.1.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/ruby-scheduler-0.1.3/vendor/ruby/2.6.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/ruby-scheduler-0.1.3/vendor/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/sb_prime_table-0.1.1/vendor/bundle/ruby/2.4.0/gems/rspec-mocks-3.7.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/simplenet-client-0.2.0/vendor/bundle/ruby/1.9.1/gems/rspec-mocks-3.4.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/simplenet-client-0.2.0/vendor/bundle/ruby/2.0.0/gems/rspec-mocks-3.4.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/sonixlabs-net-ssh-2.3.0/lib/net/ssh/proxy/command.rb:        def __send__(data, flag)
/home/gem-codesearch/gem-codesearch/latest-gem/spec_fill-0.1.2/vendor/bundle/ruby/2.6.0/gems/rspec-mocks-3.9.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/sublimetheme-1.0.1/path/gems/rspec-mocks-3.3.2/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/suzuko-0.1.8/vendor/bundle/ruby/2.0.0/gems/rspec-mocks-3.2.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/swift-pyrite-0.1.1/vendor/bundle/ruby/2.0.0/gems/rspec-mocks-3.3.2/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/symbolic_enum-1.1.5/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/ta910_helloworld-0.1.1/vendor/bundle/gems/rspec-mocks-3.6.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/tdiary-5.1.2/vendor/bundle/ruby/2.6.0/gems/rspec-mocks-3.9.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/tdiary-5.1.2/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.9.1/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/test_gem2_dh-0.1.0/vendor/bundle/gems/rspec-mocks-3.6.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
/home/gem-codesearch/gem-codesearch/latest-gem/vagrant-unbundled-2.2.9.0/vendor/bundle/ruby/2.7.0/gems/rspec-mocks-3.5.0/lib/rspec/mocks/verifying_double.rb:      def __send__(name, *args, &block)
```

Conclusion:

* matz: okay to prohibit redefinition of `__send__` as long as that doesn't break rspec.


### [[Feature #16043]](https://bugs.ruby-lang.org/issues/16043) `$LOAD_PATH.resolve_feature_path` should not raise (eregon)

* Seems good to me, OK to merge?

Preliminary discussion:

* mame: Maybe good

Discussion:

* no one objects.

Conclusion:

* matz: accepted


### [[Feature #17100]](https://bugs.ruby-lang.org/issues/17100) Ractor naming (eregon)

* What should be the method name to send a message? `send` seems problematic as many have reported. We should decide soon. Many good other names are listed in the issue (notably in https://bugs.ruby-lang.org/issues/17100#note-19 and https://bugs.ruby-lang.org/issues/17100#note-43).
* Specifically, `Ractor#send` conflicts with `Kernel#send`, which is a violation of the Liskov substitution principle.
* There has been (AFAIK) no official deprecation of `Kernel#send`, so it seems inadvisable to use the name for other semantics for Ractor. Deprecating `Kernel#send` seems a way to require 100 000s of changes in gems for for very little reasons, so it seems unfeasible in practice anyway.
* When should `send` and `__send__` be used? It seems gems prefer to use `send` (>10x more used than `__send__`), and only use `__send__` when required. IMHO `__send__` feels like Python's `__add__`, which I find unpretty, and feels like C's poor way of namespacing things.

Preliminary discussion:

* mame: I have no opinion at all about this

Discussion:

* akira: Will send a pull request to rails which replaces all `send` into `__send__`.

Conclusion:

* try `send`

### [[Feature #17307]](https://bugs.ruby-lang.org/issues/17307) A way to mark C extensions as thread-safe, Ractor-safe, or unsafe (eregon)

* I'd like feedback on this. Any idea for a good way to mark C extensions?
* How about defining symbols like `foo_is_thread_safe`/`foo_is_ractor_safe` to mark C extensions?

Preliminary discussion:

* mame: Why is "thread-safe" attribute only for C extension?
* ko1: "Thread-safe" attribute is just for other implementations, like TruffleRuby. MRI will not use it at all (just now)
* mame: How different "ractor-safe" and "ractor-unsafe" methods are handled? "Ractor-unsafe" C methods are executed in a dedicated critical section only for an extension?
* ko1: "Ractor-unsafe" methods may share information between Ractors. So, "Ractor-unsafe" methods can be called by only the main Ractor. Calling them from sub Ractors will raise an exception
* ko1: I prefer `rb_cext_...()`.
* nobu: `rb_ext_...`.
* mame: `rb_declare_ractor_safe`.

```c
// for (2)
#ifndef RB_EXT_THREAD_SAFE
    #define RB_EXT_THREAD_SAFE(b) 
#endif

void
Init_foo(void)
{
// (1)
#ifdef HAS_RB_EXT_THREAD_SAFE
    rb_ext_thread_safe(true);
#endif
#ifdef HAS_RB_EXT_RACTOR_SAFE
    rb_ext_ractor_safe(true);
#endif

// (2)
  RB_EXT_THREAD_SAFE(true);

// (3) mame votes for this
#ifdef RUBY_EXT_CONFIG
    rb_ext_thread_safe(true);
    rb_ext_ractor_safe(true);
#ifdef RUBY_C_EXT_DECLARTAION_NEW_CONF
    rb_ext_new_conf(true);
#endif
#endif

// (4) --> NG
#ifdef RUBY_C_EXT_DECLARTAION_MECHANISM
   RB_EXT_CONFIG(
   #if RACTOR_SAFE
     .ractor_safe = true,
   #endif
   #if THREAD_SAFE
     .thread_safe = false
   #endif
   );
#endif

}

// (4')
#define RACTOR_SAFE(b) (b ? 0x01 : 0)
#define THREAD_SAFE(b) (b ? 0x02 : 0)

#ifndef NEW_CONF
#define NEW_CONF(b) 0
#endif

#ifdef RUBY_C_EXT_DECLARTAION_MECHANISM
   RB_EXT_CONFIG(
     RACTOR_SAFE(true) |
     THREAD_SAFE(false)|
     NEW_CONF(false)
   );
#endif

```

```c
void Init_foo() {

#ifdef TRUFFLERUBY
  rb_truffleruby_thread_safe(true);
#endif

}
```

Discussion:

*

Conclusion:

* matz: pending thread_safe, accept ractor_safe


### [[Feature #16786]](https://bugs.ruby-lang.org/issues/16786) Light-weight scheduler for improved concurrency (eregon)

* @matz: The `Fiber.set_scheduler(value)` API doesn't make any sense to me and is very confusing, please see my comments there (summary: https://bugs.ruby-lang.org/issues/16786#note-70).
* Anything wrong with the obvious and clear `Thread.current.scheduler = value` or `Thread.current.fiber_scheduler = value` ?

Preliminary discussion:

* mame: who dislikes what?
  * samuel dislikes `Thread.current.fiber_scheduler = value`
  * ko1/mame/nobu/matz dislike `Thread.current.scheduler = value` because it has no mention to "fiber"
  * eregon dislikes `Fiber.set_scheduler(value)` because it is difficult to understand it is thread-local property.
      * ko1: For me, this is better than `Thread.current.scheduler`.
  * eregon suggests `Fiber.setup_scheduler(value)`
* mame: I think this is an API for professionals, not casual use, so the long explicit choice looks good to me
* nobu: `Fiber.set_scheduler(value)` can not setup other thread's scheduler. is it okay?

Discussion:

*

Conclusion:

* matz: I like `Fiber.set_scheduler(value)`
* matz: I want to call this feature as "Async fiber" instead of scheduler
  * https://github.com/ruby/ruby/blob/master/doc/scheduler.md
  * https://github.com/ruby/ruby/blob/master/scheduler.c

### [[Feature #17312]](https://bugs.ruby-lang.org/issues/17312) New methods in Enumerable and Enumerator::Lazy: flatten, product, compact (zverok)

* If there would be no objections, I'd prepare a PR

Preliminary discussion:

* mame: `Enumerable#compact` looks good
* mame: `Enumerable#product` looks a bit difficult
  * `Array#product` requires the arguments to be all `Array`s. `Enumerable#product` should do so too?

```ruby
p *[1, 2, 3].product([:a, :b], [:A, :B].to_enum)
# => no implicit conversion of Enumerator into Array (TypeError)
```

* mame: `Enumrable#flatten` should flatten `Enumerator` or not?
  * `Array#flatten` does not:

```ruby
p [[1, 2].to_enum, [3, 4].to_enum].flatten
#=> [#<Enumerator: [1, 2]:each>, #<Enumerator: [3, 4]:each>]
```

Discussion:

*

Conclusion:

* matz: accept Enumerable#compact and Enumerable::Lazy#compact
* matz: I'm negative against #product and #flatten. If they are really needed, please create another ticket with use case.

### [[Feature #17323]](https://bugs.ruby-lang.org/issues/17323) Ractor::LVar to provide ractor-local storage (ko1)

Discussion:

*

Conclusion:

*

### [[Feature #17322]](https://bugs.ruby-lang.org/issues/17322) Deprecate `Random::DEFAULT` and introduce `Random.default()` method to provide Ractor-supported default random generator (ko1)

Discussion:

* shyouhei: why not just make everything ractor-local?
* ko1: I want ractors to support randomness.  Deprecation of `Random::DEFAULT` is not my goal.

Conclusion:

* Make `Kernel#random` etc. Ractor-safe.
* Additionally to save existing programs provide `Random::DEFAULT` which acts like a proxy to `Kernel#rand` etc.

### [[Feature #17273]](https://bugs.ruby-lang.org/issues/17273) shareable_constant_value pragma

Discussion:

* ko1: I want to add `experimental_copy` that translates `C = expr` to `C = Ractor.make_shareable(deep_copy(expr))`
* matz: okay, let's try

Conclusion:

* ko1: I will do with nobu

### [[Misc #17319]](https://bugs.ruby-lang.org/issues/17319)  Rename Random::urandom to os_random and document random data sources

* rdoc: Returned value is expected to be a cryptographically secure pseudo-random number in binary form.
* [rurema](https://docs.ruby-lang.org/ja/latest/method/Random/s/urandom.html) 返り値はバイナリ形式で、暗号的に安全な擬似乱数だと期待できます。 

* naruse: reject because the name is discussed in https://bugs.ruby-lang.org/issues/9569#note-58

### [[Feature #17326]](https://bugs.ruby-lang.org/issues/17326) Add Kernel#must! to the standard library

Discussion:

- nobody likes the proposed name
- is this necesary? in the core?
- matz: I like the idea of annotating a value but this name...
- mame: It is useful not only for type checkers but also for assertion
- nobu: For assertion, is it enough to check if a value is nil or not? I think `is_a?` check is needed in many cases
- mame: That's true, but I believe that checking nil or not is also useful enough
- naruse: We don't have to decide this so soon

Conclusion:

- mame: Will write a summary of this discussion
- wait for new good name proposal, pending

### [[Feature #17331]](https://bugs.ruby-lang.org/issues/17331) Let Fiber#raise work with transferring fibers

- ko1: From Samuel I heard that fiber schedular needs this.
- matz: how?
- ko1: for example, a fiber schedular wants to inform timeouts to fibers wanting to be transferrd.
- ko1: I don't think we should have this.  Instead of raising directly into the fiber, send a message that "I want to raise!" and let the fiber itself raise it.
- matz: why?
- ko1: because there is no guarantee that the raised exception be rescued.  If an exception reaches out of a fiber it goes to the root fiber, which is dangerous.  In case of yielding fiber the exception goes to where `riase` was called.
- matz: it sounds counter-intuitive for `Fiber#raise` to restrict its receiver to yielding ones.
- matz: should excepions propagate from fibers to root at the first place?
- ko1: that is a matter of design.
- mame: I prefer `Fiber#transfer_raise` and `Fiber#resume_raise` (more explicit API) but the implecit style is acceptable

- naruse: renaming scheduler is preview 2 blocking issue
