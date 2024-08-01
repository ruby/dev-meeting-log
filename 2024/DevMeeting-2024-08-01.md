# DevMeeting-2024-08-01

https://bugs.ruby-lang.org/issues/20628

## DateTime and location

* 2024/08/01 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/09/05 (Thu) 13:00-17:00 JST @ Online
    * 9/12 is euruko.

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets
### [[Feature #20590]](https://bugs.ruby-lang.org/issues/20590) Ensure `fork` isn't called when `raddrinfo`'s background thread is in `getaddrinfo` (byroot)

* Full rationale in the ticket, too large to copy here.
* I understand that this solution is quite unorthodox, but I think it would solve a lot of problems for many people.
* I'm interested in feedback about the approach.
* It can introduce latency in `fork`, but `fork` is rarely very time sensitive.

#### Discussion:

* mame: fork could become slower, but not a big deal I guess.
* akr: I can think of no alternative than the proposed fix.
* mame: FYI a thread doing getaddrinfo() cannot be pthread_cancel()-ed.
* akr: Then a timed-out call to getaddrinfo() (which is still running in a thread) must also be taken care of.

#### Conclusion:

* mame: The protected area can be different.  the pull request needs brush-up.

### [[Feature #20594]](https://bugs.ruby-lang.org/issues/20594) A new String method to append bytes while preserving encoding (byroot)

* The idea was accepted in the last meeting, but a better name was requested.
* I think two methods would be best.
* `String#append_bytes(String) => self`
* `String#append_byte(Integer) => self`

#### Discussion:

* mame: byroot is proposing new names.  WDYT matz?
* matz: I understand that the result encoding would be the receiver's encoding.

#### Conclusion:

* matz: `String#append_bytes` (that takes a string), accepted.
* matz: `String#append_byte` (that takes an integer) is not an immediate NG but needs more discussions (e.g. what happens when -128 is passed?).  In a meantime use `255.chr` etc.

### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) Range#step semantics for non-Numeric ranges (zverok)

* The feature was approved a year ago by @matz but the PR doesn’t receive any review/comments/answers. Not sure what I should do to move forward, so submitting it to the dev. meeting.

#### Preliminary discussion:

* https://github.com/ruby/ruby/pull/7444

#### Discussion:

* shyouhei: The specification is already approved.
* mame: I have not been able to make some time to review this.

#### Conclusion:

* mame: Please review!
* knu: I will!

### [[Bug #20620]](https://bugs.ruby-lang.org/issues/20620) singleton_method undefined for module using "extend self" (jeremyevans0)

* I believe this is expected behavior and the bug can be rejected, is that correct?
* I think it is unfortunate that the `all` argument to `singleton_methods` defaults to `true`, but I don't think it's worth breaking backwards compatibility to change it.

#### Preliminary discussion:

```ruby
module ExtendSelf
  extend self

  def foo = 42
end

p ExtendSelf.foo #=> 42
p ExtendSelf.singleton_methods        # => [:foo]
p ExtendSelf.singleton_methods(false) # => []
p ExtendSelf.singleton_method(:foo)   # => singleton_method.rb:19:in `singleton_method': undefined singleton method `foo' for `ExtendSelf' (NameError)
```

```ruby
class C0
  def self.foo = :C0
end

class C1 < C0
end

p C1.foo #=> :C0
p C1.method(:foo) #=> #<Method: C1(C0).foo() t.rb:3>
p C1.singleton_method(:foo) #=> undefined singleton method `foo' for `C1' (NameError)
```

```ruby:descriptive-example.rb
class C0
  def foo = :C0_instance
  def self.bar = :C0_singleton
end

class C1 < C0
end

p C1.bar #=> :C0_singleton
p C1.new.method(:foo) #=> #<Method: C1(C0)#foo() t.rb:3>
p C1.singleton_method(:bar) #=> undefined singleton method 'bar' for 'C1' (NameError)
```

```ruby
module M
  def foo = :M
end

class C
  extend M
end

p C.foo
p C.singleton_method(:foo) #=> undefined singleton method `foo' for `C' (NameError)
```

```ruby
module M
  def foo = 42
end
obj = Object.new
obj.extend M

p obj.singleton_methods        #=> [:foo]
p obj.singleton_methods(false) #=> []
p obj.singleton_method(:foo)   #=> undefined singleton method 'foo'
```

* mame: `Object#singleton_method` should retreive the method object from ancestors or not?

```
ko1@ruby-sp1:~/gem-codesearch$ ./gem-codesearch '\.singleton_method\b'
2024-05-12 /srv/gems/HDLRuby-3.3.2/lib/HDLRuby/hruby_high.rb:                self.define_singleton_method(name, &obj.singleton_method(name))
2023-11-18 /srv/gems/baltix-0.1.1/lib/baltix/loader.rb:                  [k, v.is_a?(Symbol) && kls.singleton_method(v) || v]
2023-08-31 /srv/gems/bbk-utils-1.1.0.181866/lib/bbk/utils/config.rb:        type = BBK::Config::BooleanCaster.singleton_method(:cast) if bool
2023-08-31 /srv/gems/bbk-utils-1.1.0.181866/lib/bbk/utils/config.rb:        type = BBK::Utils::Config::BooleanCaster.singleton_method(:cast) if bool
2021-11-28 /srv/gems/biovision-0.12.211128.0/app/lib/biovision/helpers/data_helper.rb:        reflection = model_class.singleton_method(:page_for_administration)
2021-11-28 /srv/gems/biovision-0.12.211128.0/app/lib/biovision/helpers/data_helper.rb:        reflection = model_class.singleton_method(:page_for_owner)
2020-12-29 /srv/gems/bitclust-core-1.3.0/lib/bitclust/ridatabase.rb:    @entry.singleton_method?
2020-12-29 /srv/gems/bitclust-core-1.3.0/test/test_rdcompiler.rb:p Foo.singleton_method(:foo)
2020-12-29 /srv/gems/bitclust-dev-1.3.0/tools/bc-rdoc.rb:    if m.singleton_method?
2020-12-29 /srv/gems/bitclust-dev-1.3.0/tools/bc-rdoc.rb:      c.singleton_method?(m.name, true)
2012-02-22 /srv/gems/cast_off-0.4.1/lib/cast_off/compile.rb:      singleton = compilation_target.singleton_method?
2022-01-31 /srv/gems/cocoapods-bin-cache-0.0.3/lib/cocoapods_plugin.rb:    oldmethod = Pod::Prebuild.singleton_method(:build)
2018-10-30 /srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/config_gems_initialization_aim-0.1.1/vendor/bundle/ruby/2.5.0/gems/reek-5.2.0/lib/reek/smell_detectors/feature_envy.rb:        return [] if context.singleton_method? || context.module_function?
2018-10-30 /srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/config_gems_initialization_aim-0.1.1/vendor/bundle/ruby/2.5.0/gems/reek-5.2.0/lib/reek/smell_detectors/utility_function.rb:        return [] if context.singleton_method? || context.module_function?
2018-10-30 /srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/reek-5.2.0/lib/reek/smell_detectors/feature_envy.rb:        return [] if context.singleton_method? || context.module_function?
2018-10-30 /srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/reek-5.2.0/lib/reek/smell_detectors/utility_function.rb:        return [] if context.singleton_method? || context.module_function?
2024-05-30 /srv/gems/contrast-agent-7.6.1/lib/contrast/agent/patching/policy/patch.rb:              method = mod.singleton_method(method_policy.method_name) unless method_policy.instance_method
2009-07-17 /srv/gems/dakrone-fastri-0.3.1.1/lib/fastri/ri_index.rb:  # Namespace::Foo.singleton_method (instead of ::). RiIndex depends on this to
2009-07-17 /srv/gems/dakrone-fastri-0.3.1.1/lib/fastri/ri_index.rb:          (is_class_method ? meth.singleton_method? : meth.instance_method?)
2009-07-17 /srv/gems/dakrone-fastri-0.3.1.1/lib/fastri/ri_index.rb:          (is_class_method ? meth.singleton_method? : meth.instance_method?)
2023-09-10 /srv/gems/deforest-1.0.2/lib/deforest.rb:              method_location = klass.singleton_method(mname).source_location
2010-11-05 /srv/gems/dietrb-0.6.1/spec/ext/completion_spec.rb:          @context.__evaluate__('def foo.singleton_method; end')
2010-11-05 /srv/gems/dietrb-0.6.1/spec/ext/completion_spec.rb:          complete('foo.').should include('foo.singleton_method')
2024-04-22 /srv/gems/elftools-1.3.1/lib/elftools/structs.rb:              old_method = obj.singleton_method(m)
2018-03-31 /srv/gems/esruby-0.2.0/resources/mruby/mrbgems/mruby-method/test/method.rb:  assert_raise(NameError) { o.singleton_method(:foo) }
2018-03-31 /srv/gems/esruby-0.2.0/resources/mruby/mrbgems/mruby-method/test/method.rb:  assert_kind_of Method, o.singleton_method(:bar)
2018-03-31 /srv/gems/esruby-0.2.0/resources/mruby/mrbgems/mruby-method/test/method.rb:  assert_raise(TypeError) { o.singleton_method(nil) }
2018-03-31 /srv/gems/esruby-0.2.0/resources/mruby/mrbgems/mruby-method/test/method.rb:  m = assert_nothing_raised(NameError) { break o.singleton_method(:bar) }
2008-02-02 /srv/gems/fastri-0.3.1.1/lib/fastri/ri_index.rb:  # Namespace::Foo.singleton_method (instead of ::). RiIndex depends on this to
2008-02-02 /srv/gems/fastri-0.3.1.1/lib/fastri/ri_index.rb:          (is_class_method ? meth.singleton_method? : meth.instance_method?)
2008-02-02 /srv/gems/fastri-0.3.1.1/lib/fastri/ri_index.rb:          (is_class_method ? meth.singleton_method? : meth.instance_method?)
2023-12-31 /srv/gems/gir_ffi-0.17.0/lib/gir_ffi/builders/method_template.rb:        "#{@builder.singleton_method? ? "self." : ""}#{@builder.method_name}"
2018-08-15 /srv/gems/grably-0.0.3/lib/grably/core/product.rb:        # ProductExpand.singleton_method on each method name.
2018-08-15 /srv/gems/grably-0.0.3/lib/grably/core/product.rb:          .flat_map { |k, v| [k, ProductExpand.singleton_method(v)] }]
2019-03-11 /srv/gems/interjectable-1.1.2/lib/interjectable/rspec.rb:        @meth = klass.singleton_method(dependency)
2022-10-18 /srv/gems/linux_stat-2.6.0/exe/linuxstat.rb:                 source = e.singleton_method(meth).source_location.to_a
2019-06-18 /srv/gems/maccro-0.2.0/lib/maccro.rb:        this.singleton_methods.map{|m| this.singleton_method(m) rescue nil }.compact
2010-03-18 /srv/gems/method_info-0.1.11/lib/method_info/ancestor_method_structure.rb:      # obj.method(:singleton_method).to_s => "#<Method: #<Object:0x5673b8>.singleton_method>"
2023-08-24 /srv/gems/mocktail-2.0.0/lib/mocktail/replaces_type/redefines_new.rb:        type_replacement.replacement_new = type.singleton_method(:new)
2023-08-24 /srv/gems/mocktail-2.0.0/lib/mocktail/replaces_type/redefines_singleton_methods.rb:        type.singleton_method(original_method.name)
2023-08-24 /srv/gems/mocktail-2.0.0/lib/mocktail/sorbet/mocktail/replaces_type/redefines_new.rb:        type_replacement.replacement_new = type.singleton_method(:new)
2023-08-24 /srv/gems/mocktail-2.0.0/lib/mocktail/sorbet/mocktail/replaces_type/redefines_singleton_methods.rb:        type.singleton_method(original_method.name)
2017-10-29 /srv/gems/morphological_metrics-space-1.0.0/test/mm/test_space.rb:    assert_equal MM::Deltas.singleton_method(:tenney),
2017-10-29 /srv/gems/morphological_metrics-space-1.0.0/test/mm/test_space.rb:    assert_equal MM::Deltas.singleton_method(:log_ratio),
2015-12-26 /srv/gems/mspec-1.9.1/spec/matchers/have_singleton_method_spec.rb:  def self.singleton_method
2015-12-26 /srv/gems/mspec-1.9.1/spec/matchers/have_singleton_method_spec.rb:    def obj.singleton_method; end
2010-12-16 /srv/gems/named-parameters-0.0.21/spec/legacy_spec.rb:      has_named_parameters :'self.singleton_method',
2010-12-16 /srv/gems/named-parameters-0.0.21/spec/legacy_spec.rb:      def self.singleton_method opts = { }
2010-12-16 /srv/gems/named-parameters-0.0.21/spec/legacy_spec.rb:        declared_parameters_for :'self.singleton_method'
2010-12-16 /srv/gems/named-parameters-0.0.21/spec/legacy_spec.rb:          declared_parameters_for :'self.singleton_method'
2023-09-06 /srv/gems/overrides_tracker-0.3.1/lib/overrides_tracker/methods_collector.rb:                            clazz.singleton_class.instance_method(method_name) || clazz.singleton_method(method_name)
2023-09-06 /srv/gems/overrides_tracker-0.3.1/lib/overrides_tracker.rb:          method = clazz.singleton_method(single_method)
2023-09-06 /srv/gems/overrides_tracker-0.3.1/spec/overrides_tracker/methods_collector_spec.rb:    let(:singleton_method) { CustomClass.singleton_method(:singleton_test_method) }
2023-09-06 /srv/gems/overrides_tracker-0.3.1/spec/overrides_tracker/methods_collector_spec.rb:    let(:added_singleton_method) { CustomClass.singleton_method(:singleton_added_test_method) }
2023-09-06 /srv/gems/overrides_tracker-0.3.1/spec/overrides_tracker/methods_collector_spec.rb:      let(:method) { CustomClass.singleton_method(:singleton_test_method) }
2023-09-06 /srv/gems/overrides_tracker-0.3.1/spec/overrides_tracker/methods_collector_spec.rb:    let(:singleton_method) { CustomClass.singleton_method(:singleton_test_method) }
2023-09-06 /srv/gems/overrides_tracker-0.3.1/spec/overrides_tracker/methods_collector_spec.rb:    let(:added_singleton_method) { CustomClass.singleton_method(:singleton_added_test_method) }
2015-06-04 /srv/gems/predef-0.8.0/test/example.rb:  def self.singleton_method arg
2015-06-04 /srv/gems/predef-0.8.0/test/example.rb:  actual = Hoge.singleton_method('arg')
2016-02-14 /srv/gems/private_please-0.1.2/lib/private_please/utils/source_file_utils.rb:            klass.singleton_method(method).source_location
2019-08-29 /srv/gems/protoboard-0.2.3/lib/protoboard/circuit_proxy_factory.rb:            unless circuit.singleton_method?
2019-08-29 /srv/gems/protoboard-0.2.3/lib/protoboard/circuit_proxy_factory.rb:            if circuit.singleton_method?
2015-03-01 /srv/gems/qed-2.9.2/demo/07_toplevel.md:    def self.singleton_method; true; end
2024-06-09 /srv/gems/raap-0.8.0/README.md:$ raap 'MyClass.singleton_method' # Run only MyClass.singleton_method
2024-06-07 /srv/gems/rbs-3.5.1/core/errors.rbs:  #     [1, 2, 3].singleton_method(:rject) # NameError with name "rject" and receiver: [1, 2, 3]
2024-06-07 /srv/gems/rbs-3.5.1/core/kernel.rbs:  #     def k.singleton_method; end
2024-06-07 /srv/gems/rbs-3.5.1/core/kernel.rbs:  #   - obj.singleton_method(sym)    -> method
2024-06-07 /srv/gems/rbs-3.5.1/core/kernel.rbs:  #     m = k.singleton_method(:hi)
2024-06-07 /srv/gems/rbs-3.5.1/core/kernel.rbs:  #     m = k.singleton_method(:hello) #=> NameError
2019-06-09 /srv/gems/rdl-2.2.0/lib/rdl/config.rb:        guess_meth(sklass, $1, true, the_klass.singleton_method(meth))
2019-06-09 /srv/gems/rdl-2.2.0/lib/rdl/wrap.rb:        loc = the_self.singleton_method(meth).source_location
2019-06-09 /srv/gems/rdl-2.2.0/lib/rdl/wrap.rb:        loc = the_self.singleton_method(meth).source_location
2024-01-28 /srv/gems/reek-6.3.0/lib/reek/smell_detectors/feature_envy.rb:        return [] if context.singleton_method? || context.module_function?
2024-01-28 /srv/gems/reek-6.3.0/lib/reek/smell_detectors/utility_function.rb:        return [] if context.singleton_method? || context.module_function?
2024-02-15 /srv/gems/rhodes-7.6.0/platform/shared/ruby/class.c: *     def k.singleton_method; end
2024-02-15 /srv/gems/rhodes-7.6.0/platform/shared/ruby/proc.c: *     obj.singleton_method(sym)    -> method
2024-02-15 /srv/gems/rhodes-7.6.0/platform/shared/ruby/proc.c: *     m = k.singleton_method(:hi)
2024-02-15 /srv/gems/rhodes-7.6.0/platform/shared/ruby/proc.c: *     m = k.singleton_method(:hello) #=> NameError
2014-04-02 /srv/gems/rietveld-1.0.1/lib/rietveld/role.rb:      __role_player__.singleton_method(name)
2015-11-27 /srv/gems/roap-1.0.0/lib/roap/roap.rb:        method ||= base.singleton_method(method_name)
2015-11-27 /srv/gems/roap_rpc-1.0.0/lib/roap_rpc/extension.rb:        klass.singleton_method(method.name).call *args
2015-11-27 /srv/gems/roap_test_helper-1.0.0/lib/roap_test_helper/roap_test_helper.rb:          method = test[:klass].singleton_method test[:method_name]
2017-09-20 /srv/gems/rotoscope-0.3.0/lib/rotoscope/call_logger.rb:      call_method_level = call.singleton_method? ? 'class' : 'instance'
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/class.c: *     def k.singleton_method; end
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/proc.c: *     obj.singleton_method(sym)    -> method
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/proc.c: *     m = k.singleton_method(:hi)
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/proc.c: *     m = k.singleton_method(:hello) #=> NameError
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/-ext-/symbol/test_inadvertent_creation.rb:      assert_nothing_raised(NameError, bug10985) {m = c.singleton_method(s.to_sym)}
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_method.rb:    assert_raise(NameError, feature8391) {o.singleton_method(:foo)}
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_method.rb:    m = assert_nothing_raised(NameError, feature8391) {break o.singleton_method(:bar)}
2016-12-29 /srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_refinement.rb:    assert_raise(NameError, bug10744) { C.singleton_method(:foo) }
2020-04-07 /srv/gems/ruby_run_js-0.1.0/lib/ruby_run_js/builtin_context.rb:            new_native_function(methods.singleton_method(method_symbol), name)
2020-04-07 /srv/gems/ruby_run_js-0.1.0/lib/ruby_run_js/builtin_context.rb:          values = methods.singleton_method(method_symbol).call
2020-04-07 /srv/gems/ruby_run_js-0.1.0/lib/ruby_run_js/builtin_context.rb:            new_native_function(methods.singleton_method(method_symbol), name)
2022-10-01 /srv/gems/rucoa-0.13.0/lib/rucoa/definition_store.rb:            method_definition.singleton_method?
2021-12-17 /srv/gems/script_core-0.3.2/ext/enterprise_script_service/mruby/mrbgems/mruby-method/test/method.rb:  assert_raise(NameError) { o.singleton_method(:foo) }
2021-12-17 /srv/gems/script_core-0.3.2/ext/enterprise_script_service/mruby/mrbgems/mruby-method/test/method.rb:  assert_kind_of Method, o.singleton_method(:bar)
2021-12-17 /srv/gems/script_core-0.3.2/ext/enterprise_script_service/mruby/mrbgems/mruby-method/test/method.rb:  assert_raise(TypeError) { o.singleton_method(nil) }
2021-12-17 /srv/gems/script_core-0.3.2/ext/enterprise_script_service/mruby/mrbgems/mruby-method/test/method.rb:  m = assert_nothing_raised(NameError) { break o.singleton_method(:bar) }
2024-02-19 /srv/gems/semian-0.21.3/lib/semian/grpc.rb:      execute = operation.singleton_method(:execute)
2024-06-08 /srv/gems/sorbet-0.5.11422/lib/hidden-definition-finder.rb:          method = klass.singleton_method(name)
2024-06-08 /srv/gems/sorbet-0.5.11422/lib/serialize.rb:        method = klass.singleton_method(method_sym)
2020-02-21 /srv/gems/sorbet_auto_typer-0.1.0/lib/sorbet_auto_typer/method_trace.rb:        T.cast(method, Method).receiver.ancestors.find { |a| !(a.singleton_method(method_name.to_sym) rescue nil).nil? }
2020-02-21 /srv/gems/sorbet_auto_typer-0.1.0/lib/sorbet_auto_typer/method_trace.rb:      #   real_owner = method.receiver.ancestors.find { |a| !(a.singleton_method(@method_name.to_sym) rescue nil).nil? }
2020-02-21 /srv/gems/sorbet_auto_typer-0.1.0/lib/sorbet_auto_typer/method_trace.rb:      #   real_owner.singleton_method(@method_name.to_sym)
2020-11-28 /srv/gems/spud-0.2.10/lib/spud/task_runners/spud_task_runner/task_dsl.rb:            define_singleton_method(method, &file_dsl.singleton_method(method))
2017-04-08 /srv/gems/tufte-1.1.0/lib/tufte/binding.rb:          define_singleton_method(name, &mod.singleton_method(name))
2016-02-15 /srv/gems/typed.rb-0.0.20/lib/typed/types/ty_singleton_object.rb:        @ruby_type.singleton_method(message)
2021-03-25 /srv/gems/yieldable-0.1.1/lib/yieldable.rb:      base.singleton_method(method_name)
```

#### Discussion:

* mame: `extend self` is a bit tricky but not a necessary part of the problem.
* mame: `singleton_methods` lists a method which is not accessible via `singleton_method`.
* akr: sounds like a issue of `singleton_methods` side?
* matz: I'd rather find it a `singleton_method`'s problem?
* mame: @jeremyevans0 thinks the current behaviour is legit.

#### Conclusion:

* matz: `singleton_method`'s expected behaviour is "identical to `method` method except it stops searching when it encounters a non-singleton."

### [[Bug #20637]](https://bugs.ruby-lang.org/issues/20637) SyntaxError class definition in method body can be bypassed (jeremyevans0)

* Is this expected behavior, or a bug?
* If this is considered a bug, I expect fixing it may result in significant backwards compatibility issues.

#### Preliminary discussion:

```ruby
require_relative 'finder'

class SingletonClassInDefFinder < Finder
  def rec node, in_def = nil, in_sclass = nil
    if node.type == :class_node && in_sclass
      pp [nloc(node), nlines(node).lines.first.chomp]
    end

    if node.type == :def_node
      in_def = true; in_sclass = false
    elsif node.type == :singleton_class_node && in_def
      in_sclass = true
    end

    node.child_nodes.compact.each{|n| rec n, in_def, in_sclass}
  end
end

Finder.run
```

```
ko1@ruby-sp1:~/finders$ time find /srv/gems/ -name '*.rb' | ruby singleton_class_in_def_finder.rb
["/srv/gems/tomdz-soap4r-1.5.8.20120202093209/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
["/srv/gems/mumboe-soap4r-1.5.8.7/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-r19-1.5.9/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-ruby1.9-2.0.5/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-sgonyea-1.6.0/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-spox-1.6.0/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap5r-2.0.3/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
["/srv/gems/DefV-soap4r-1.5.8.2/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/eSpace_soap4r-1.5.8/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
["/srv/gems/etapper-0.0.5/vendor/gems/soap4r-1.5.8.2/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/hands-soap4r-1.5.8.4/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/ikra-0.0.2/lib/sourcify/spec/method/to_source_from_def_end_block_w_nested_class_spec.rb:30", "          class BB"]
["/srv/gems/malagant-soap4r-1.5.8.20141127181857/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
["/srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_class.rb:488", "        class X"]
["/srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/test_class.rb:520", "          class X"]
["/srv/gems/ruby-compiler-0.1.1/vendor/ruby/test/ruby/marshaltestlib.rb:377", "      class C; self; end"]
["/srv/gems/snaury-soap4r-1.5.8.1/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-1.5.8/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/soap4r-ruby19-1.5.9/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
["/srv/gems/soap4r-straightjacket-1.5.9/test/soap/marshal/marshaltestlib.rb:420", "      class C; self; end"]
["/srv/gems/sunteya-soap4r-1.5.8.0/test/soap/marshal/marshaltestlib.rb:424", "      class C; self; end"]
{:FILES=>2945839, :FAILED=>15205}
```

#### Discussion:

* akr: This has been like this for a long time.
* shyouhei: Problem is you can assign an outer-scope constant like `::A = 1`.
* akr: If this shall be banned syntactically, we need to double the entire syntax ( one for outer-def, one for inner-def).

#### Conclusion:

* matz: I admit I cut a corner when I designed here, but I don't think it's worth prohibiting people from explicitly writing such esoteric codes.
* mame: Why `def foo; A=1; end` is banned then?
* shyouhei: It feels strange to me for a method to dynamically define a constant.

### [[Bug #19231]](https://bugs.ruby-lang.org/issues/19231) Integer#step and Float::INFINITY - inconsistent behaviour when called with and without a block (jeremyevans0)

* Returns float without block, yields integer with block.
* Behavior should probably be consistent.
* I think it's would make the most sense to always return and yield integer if step is integer, because Integer + Integer should result in Integer.
* However, with non-infinite float as the `to` value, it returns and yields floats, so returning/yielding integer would be inconsistent with that.
* How should this be handled?

#### Preliminary discussion:

```ruby
0.step(Float::INFINITY, 10) { |offset| p offset.class; break }
#=> Integer

0.step(100.0, 10) { |offset| p offset.class; break }
#=> Float
```

```ruby
0.step(Float::INFINITY, 10) do |n|
  n / 2 #=> 0, 0, 1, 1, 2, 2, ...
  n & 1 #=> 0, 1, 0, 1, 0, 1, ...
end
```

#### Discussion:

* matz: sounds like a bug to me.
* mame: which one is the bug?
* shyouhei: `0.step(Float::INFINITY, 10)` shall yield Float?
* akr: I don't think `something.step(anything).first` should be anything different from `something`.
* mame: `step(Float::INFINITY)` was a shorthand of "do this forever".  Nobody actually meant type conversion.
* akr: Found that [`Numeric#step`'s document](https://docs.ruby-lang.org/en/master/Numeric.html#method-i-step) does describe what is going on (the "implementation notes" section).  The intent is to prevent  arithmetic errors from accumulating.

#### Conclusion:

* matz: "inconsistent behaviour when called with and without a block" is a bug.
* matz: Agree with @eregon that `Integer#step(something, Integer)` shall yield Integers.
* mame: What about ArithmeticSequence?
* matz: should be the same as block-taking ones.

## misc

### [[Misc #20652]](https://bugs.ruby-lang.org/issues/20652) Memory allocation for gsub has increased from Ruby 2.7 to 3.3 (mame)

```ruby
def bar
  /b/ =~ 'b'
end

def foo
  /a/ =~ 'a'
  bar
  p $~ #=> #<MatchData "a">
end

foo
```

```ruby
def foo
  str.gsub(/a/){ }
end

foo
```

#### Discussion:
- shyouhei: this was introduced by fixing [[Bug #17507]](https://bugs.ruby-lang.org/issues/17507)
- mame: It's just another normal bug.

### [[Feature #20625]](https://bugs.ruby-lang.org/issues/20625) Object#chain_of (mame)
```ruby
hierarchy = employee.chain_of(&:manager)
```

#### Discussion:

- knu: This reminds me of `Enumerator.produce`
- mame: yes, and it aims to be a more handy variant.
- matz: there could be situations when this is useful but...
- knu: We already have Enumerator#chain, so `chain_of` might not be the best name.
- akr: traversing a tree using this method can or cannot be easy depending on how the tree is constructed.
- knu: I have long had it in mind to make `Enumerator.produce` more accessible so you could use it easily in typical use cases, so I'll take this opportunity to think about improving it.  e.g. adding `until`/`before` options

### [[Bug #20648]](https://bugs.ruby-lang.org/issues/20648) Improve performance of CGI::Util::pretty (originally reported as security issue, later decided to not be a security risk) (mame)
#### Discussion:
- mame: It's taking like O(n^2) time.
- mame: We need some volunteer in this area.

### [[Feature #20443]](https://bugs.ruby-lang.org/issues/20443) Allow Major GC's to be disabled (ko1)

#### Discussion:

- ko1: `GC.config(...)` is accepted.
- ko1: configuration names are still open.
- ko1: current naming is`rgengc_allow_full_mark`.  Is it okay?
- matz: No problem to me.

### Namespace on read (hsbt)

- mame: Do we plan to merge it this year?
- matz: Yes?
- mame:  When? I guess it's getting harder and harder.
- hsbt: Maybe @tagomoris should merge his patch set.

### [[Feature #20508]](https://bugs.ruby-lang.org/issues/20508) Explicit access to *, **, &, and ... (mame)
#### Discussion:
- mame: is this... possible?
- matz: I guess this should not be a Binding's method
- mame: Do we need this?
- ko1: debuggers need this feature.
- ko1: what happens for `binding.arguments` if there are optional arguments?

```ruby
def foo(x)
  x = 2
  binding.arguments #=> [1] or [2]?
end

foo(1)

def bar(x=1)
  binding.arguments #=> [] or [1]
end

bar()

def baz(one, *)
  binding.local_variable_get(:*)

  1.times{|*|
    p(*) # method arguments are passed here
    binding.local_variable_get(:*) # also method parameters? block parameters?
  }
end
```

- mame: this is very much like `(super)`.

### [[Misc #20509]](https://bugs.ruby-lang.org/issues/20509) Document importance of #to_ary and #to_hash for Array#== and Hash#== (mame)

```ruby
obj = Object.new
def obj.to_ary = raise("unreachable")
def obj.==(n) = raise("==")
p [] == obj #=> obj == []  #=> == (RuntimeError)

obj = Object.new
def obj.==(n) = raise("==")
p [] == obj #=> false
```

https://github.com/ruby/ruby/commit/1d5e19f624bc09d66d80ec7bc89db9dbc895b477
#### Discussion:

* matz: the behavior is intentional. documentation should be added.

### [[Feature #20525]](https://bugs.ruby-lang.org/issues/20525) Percent string literal with indentation support or String#dedent (mame)
#### Discussion:

- mame: the proposed syntax seems no-go but there could be a room for `#dedent`?
- knu: `%r{...}iumx` is valid.  There could also be a room for %q/%Q strings followed by flags.
- nobu: Should the first new line be omitted?  What if the string starts with a non-space character?

```ruby
foo = %{
  foo
  bar
}d
```

- akr: why use a long delimiter name like `<<~MARKDOWN`? You can write `<<~EOS` or `<<~MD`

```php
echo <<<END
  foo
END,<<<END
  bar
END;
```

```ruby
puts <<END, <<END
  foo
END
  bar
END
```
```ruby
p(<<END, x) #=> "1\n", 1
#{ x = 1 }
END
```

### [[Bug #20640]](https://bugs.ruby-lang.org/issues/20640) Evaluation Order Issue in f(**h, &h.delete(key))

```ruby
def f(*a)
  p a
end

a = [1, 2, 3]
f(*a, a.pop) #=> [1, 2, 3, 3]
p a #=> [1, 2]
```

```
ruby-1.9.0-0          [1, 2, 3]
                      [1, 2]
...
ruby-2.2.6            [1, 2, 3]
                      [1, 2]
ruby-2.2.7            [1, 2, 3, 3]
                      [1, 2]
...
ruby-2.2.10           [1, 2, 3, 3]
                      [1, 2]
ruby-2.3.0-preview1   [1, 2, 3]
                      [1, 2]
...
ruby-2.3.3            [1, 2, 3]
                      [1, 2]
ruby-2.3.4            [1, 2, 3, 3]
                      [1, 2]
...
ruby-2.3.8            [1, 2, 3, 3]
                      [1, 2]
ruby-2.4.0-preview1   [1, 2, 3]
                      [1, 2]
ruby-2.4.0-preview2   [1, 2, 3]
                      [1, 2]
ruby-2.4.0-preview3   [1, 2, 3, 3]
                      [1, 2]
...
ruby-3.3.0            [1, 2, 3, 3]
                      [1, 2]
```

```ruby
def f(a:)
  p a
end

h = {a: 1}
foo = Object.new
foo.singleton_class.define_method(:to_proc) do
  h.clear
  proc {}
end
f(**h, &foo) #=> expected: 1, actual: missing keyword: :a
```
