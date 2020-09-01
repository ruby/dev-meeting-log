---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20200826Japan

https://bugs.ruby-lang.org/issues/17041

## DateTime and location

* 8/26 (Wed) 13:00-17:00 JST @ Online
  * Postponed to 8/31 (Mon) 13:00-17:00 JST

## Next Date

* 9/25 (Fri) 13:00-17:00 JST @ Online

## Announce

## Ruby 2.7 timeframe

## About 2.8/3.0 timeframe

* Ractor will be soon merged
* version.h : 2.8 -> 3.0 (soon) maybe before RubyKaigi Takeout 2020
  * Done during the meeting
* 3.0.0-preview1 will be released after RubyKaigi Takeout 2020

## Check security tickets

[secret]

## Docker usage in our CI (shyouhei)

### Preliminary discussion:

Docker (Docker Inc) announced recently that they will charge for `docker pull`s, starting this November.  Our GitHub Actions are affected by this.

I guess there might be several things we can do:

  1. Just pay the cost.  It seems something possible.  Doing so saves our CI (as well as Docker Inc), but forked repositories are left in vain.  Sounds kind of suboptimal.
  2. Being an open-source project we can expect that Docker has some plans to support.  But at least some actions seem inevitable, such as registration to their "open source community".  I honestly have no idea what it is, and how it benefits.
  3. Could be an option to use GitHub Packages' docker registry.  As far as we stick to GitHub Actions they offer us unlimited data transfer.  They do charge for storage, though.
    * https://github.com/pricing
  4. Ultimately we could have our own AWS ECR set up.  This gives us freedom of everything at the cost of operation (which is quite high to us).

Thoughts?

### Discussion:

* mame: GH package for public repository seems free.
* hsbt: The CI image is not for Ruby users, only ruby core team. We try to use GitHub Registory by https://github.com/ruby/ruby-ci-image
* shyouhei: old one is here: https://hub.docker.com/r/shyouhei/c-compilers/tags

### Conclusion:

* Try to use GH package.


## Tickets

### [[Feature #17100]](https://bugs.ruby-lang.org/issues/17100) Ractor (ko1)

* ko1: may I merge it?
  * matz: go ahead

* mame: tried the branch with optcarrot
  * https://github.com/mame/optcarrot/blob/master/bin/optcarrot-bench-parallel-on-ractor
  * https://gist.github.com/mame/16df33d81025a0f512c0def901dc4973

* memo

```ruby
r1 = Ractor.new{C = 1}
r2 = Ractor.new{p C}

a = [1, 2, [3]]
C = a.deep_freeze
Ractor.use(a)

Ractor.new{
  p C #=> C.dup
}
```

```ruby
M = [1, 2, 3]
Ractor.new { require "foo" }
Ractor.new { require "foo" }
```

```ruby
class PP
  Config = SharedHash.new
  Config.transaction do
    Config[:sharing_detection] = false
  end
  class << self
    def sharing_detection
      Config.transaction do
        Config[:sharing_detection]
      end
    end
    def sharing_detection=(b)
      Config.transaction do
        Config[:sharing_detection] = b
      end
    end
  end
end

c = SharedHash.new
c.transaction{
  c[:cnt] += 1
}
```


### [[Feature #16345]](https://bugs.ruby-lang.org/issues/16345) Don't emit deprecation warnings by default. (akr/ko1)

* naruse: There're two principals: (1) We want to remove the feature at certain timing (2) We want to allow users to use the feature on productioin as long as possible. With these principals, verbose warning considered harmful
* ko1: If the deprecation has an objective (such as introducing a new feature), I'm okay for disabling warnings by default
* nagachika: Should `-w` print all warnings including deprecations?
* nobu: Try to implement
  * `-w -W:no-deprecated` -> all warnings but deprecations
  * `-W:no-deprecated -w` -> all warnings (including deprecations)
* remaining task: `$VERBOSE = true` in test frameworks: test-unit, minitest, rspec
  * It seems to have many difficulties
  * akr: Just enabling `$VERBOSE = true` will bring confusion
* naruse: This feature should be backported into 2.7, but this should gather feedback with 2.8/3.0 preview.

* Off-topic: can we deprecate iterator?
`$ gem-codesearch 'iterator\?'`
https://gist.github.com/ko1/8b6cbfda8707ae5f33dd729629e800a8f

#### TIMELINE

* matz's approval
* nobu's implementation
  * deprecation warning disabled by default
  * -w enables deprecation warning
* Release 2.8/3.0 preview
* backport and release 2.7.2


### [[Feature #17122]](https://bugs.ruby-lang.org/issues/17122) Add category to Warning#warn (mame)

* I'd like to hear opinions from the committers

Preliminary discussion:

* Current proposal:

```ruby
module Warning
  def self.warn(msg, category: nil)
    p msg #=> "XXX is deprecated"
    p category #=> :deprecated (or :experimental)
  end
end
```

* mame: Jeremy says that if this proposal is accepted, all warnings should belong to any category, and `Kernel#warn` should accept category keyword
* mame: I concern the compatibility
* mame's counterproposal

```ruby
module Warning
  def self.warn_deprecated(msg)
  end

  def self.warn_experimental(msg)
  end

  def self.warn(msg)
  end
end
```

Discussion:

* akr: I prefer the API that Eileen's proposal. It is a new API, so we need to admit that it is still unstable.
* akr: arity check should be performed.

```ruby
irb(main):001:0> def foo(**k); p k; end
=> :foo
irb(main):002:0> foo(x?: 1)
{:x?=>1}
=> {:x?=>1}
```

* nobu: arity check for callback is used in vm_method.c:`vm_respond_to`.

```
# possible API
Warning[:cat] = -> {|msg| raise}
Warning[:cat] = :error
```

Conclusion:

* matz: accepted with arity check.

### review NEWS (ko1)

* https://github.com/ruby/ruby/blob/master/NEWS.md
* Let's review the new features that are planned to introduce

* endless method definition
  * matz: defining setter method with this syntax should be prohibited

```
def foo; :ok; end
def foo() = :ok

def foo=(obj) = @foo = obj
def foo = 42
def foo= 42
def foo=(x) = @x = x
def foo=42   # expected: def foo; 42; end
def foo = 42 # expected: def foo; 42; end
def foo() = :ok => @x
# now: @x = (def foo() = :ok)
```

```ruby
# make it error
def foo=(x) = @x = x
```

```
# TODO: Note
def foo
  proc #=> ArgumentError
end

foo{}
```

> Taint deprecation warnings are now issued in regular mode in addition to verbose warning mode. [Feature #16131]
`warning: Object#taint is deprecated and will be removed in Ruby 3.2`

TODO: remove this sentense.

### [[Feature #17039]](https://bugs.ruby-lang.org/issues/17039) Remove `Time#succ` (znz)

* `Time#succ` is obsolete since 1.9.2.

Preliminary discussion:

* mame: looks good

Discussion:

* 

Conclusion:

* matz: go ahead

See also: 
* [Feature #17136: Remove special behavior from $KCODE](https://bugs.ruby-lang.org/issues/17136)
* [Feature #15547: deprecate iterator?](https://bugs.ruby-lang.org/issues/15547#change-87256)

### [[Feature #17040]](https://bugs.ruby-lang.org/issues/17040) cleanup include/ruby/backward* (shyouhei)

* Is anybody against it?

Preliminary discussion:

* mame: have `#include "ruby.h"` included these files automatically? If so, it may be good to take a period that they are not automatically included but able to include by `#include "ruby/backward.h"`
* nobu: it is included by `#include "st.h"` and so on.

Discussion:

http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-dev/35813

```
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/blockenspiel-0.4.5/ext/unmixer_mri/mkmf.log:1: #include <ruby/backward/classext.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/blockenspiel-0.4.5/ext/unmixer_mri/unmixer_mri.c:#include <ruby/backward/classext.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/blockenspiel-0.4.5/ext/unmixer_mri/mkmf.log:3: #include <ruby/backward/classext.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/blockenspiel-0.4.5/ext/unmixer_mri/unmixer_mri.c:#include <ruby/backward/classext.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/blockenspiel-0.4.5/ext/unmixer_mri/mkmf.log:3: #include <ruby/backward/classext.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/blockenspiel-0.4.5/ext/unmixer_mri/unmixer_mri.c:#include <ruby/backward/classext.h>
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/include/ruby/ruby.h:# include "ruby/backward.h"
/srv/gems/uninclude-1.3.0/ext/uninclude/uninclude.c:#include <ruby/backward/classext.h>
/srv/gems/unmixer-0.5.0/ext/unmixer/unmixer.c:# include <ruby/backward/classext.h>
/srv/gems/zscan-2.0.9/ext/pack/internal/compilers.h:#include "ruby/backward/2/gcc_version_since.h"
/srv/gems/zscan-2.0.9/ext/pack/internal-27/compilers.h:#include "ruby/backward/2/gcc_version_since.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/backward/2/limits.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/anyargs.h:#include "ruby/backward/2/stdarg.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/anyargs.h:# include "ruby/backward/cxxanyargs.hpp"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/arithmetic/fixnum.h:#include "ruby/backward/2/limits.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/arithmetic/long_long.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/arithmetic/off_t.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/arithmetic/size_t.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/core/rhash.h:# include "ruby/backward.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/core/rstruct.h:# include "ruby/backward.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/error.h:#include "ruby/backward/2/attributes.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/bignum.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/class.h:#include "ruby/backward/2/stdarg.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/error.h:#include "ruby/backward/2/assume.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/error.h:#include "ruby/backward/2/attributes.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/gc.h:#include "ruby/backward/2/attributes.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/intern/numeric.h:#include "ruby/backward/2/attributes.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/memory.h:#include "ruby/backward/2/limits.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/memory.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/memory.h:#include "ruby/backward/2/assume.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/method.h:#include "ruby/backward/2/stdarg.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/rgengc.h:#include "ruby/backward/2/attributes.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/value.h:#include "ruby/backward/2/long_long.h"
/srv/gems/zscan-2.0.9/ext/pack/ruby/internal/value.h:#include "ruby/backward/2/limits.h"
```

https://github.com/osyo-manga/gem-unmixer/blob/master/ext/unmixer/unmixer.c#L20
* 

```
/srv/gems/OpalKelly-5.2.2/ext/OpalKelly/FrontPanelDLL_wrap.cxx:#include <st.h>
/srv/gems/RocketAMF-ouvrages-1.0.0/ext/rocketamf_ext/class_mapping.c:#include <st.h>
/srv/gems/RocketAMF-ouvrages-1.0.0/ext/rocketamf_ext/serializer.h:#include <st.h>
/srv/gems/WireAPI-0.5/amq_field_list.c:#include <st.h>
/srv/gems/acunote-ruby-prof-0.9.2/ext/ruby_prof/ruby_prof.h:#include <st.h>
/srv/gems/adamb-ruby-opencv-0.0.7/ext/opencv.h:#include <st.h>
/srv/gems/adamh-ruby-prof-0.7.3/ext/ruby_prof.h:#include <st.h>
/srv/gems/afeld-opencv-0.0.8/ext/opencv/opencv.h:#include <st.h>
/srv/gems/alimentos-alu0100945645-1.0.0/vendor/bundle/ruby/2.3.0/gems/ffi-1.9.25/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/alinta-ffi-1.9.19/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/nokogiri-1.6.6.2/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/appoptics_apm-4.12.0/ext/oboe_metal/src/oboe_wrap.cxx:#include <st.h>
/srv/gems/appoptics_apm_mnfst-4.5.2/ext/oboe_metal/src/oboe_wrap.cxx:#include <st.h>
/srv/gems/approveapi-1.0.8/vendor/bundle/ruby/2.6.0/gems/ffi-1.10.0/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/archipelago_rbtree-0.3.0/ext/archipelago_rbtree.c:#include <st.h>
/srv/gems/aspirin-0.0.1/ext/aspirin.h:#include <st.h>
/srv/gems/bantic-ruby-opencv-0.0.8/ext/opencv.h:#include <st.h>
/srv/gems/bonanza-ruby-opencv-0.0.13.20170125170729/ext/opencv/opencv.h:#include <st.h>
/srv/gems/cairo-1.16.6/ext/cairo/rb_cairo_context.c:#  include <st.h>
/srv/gems/cairo-1.16.6/ext/cairo/rb_cairo_device.c:#  include <st.h>
/srv/gems/cairo-1.16.6/ext/cairo/rb_cairo_surface.c:#  include <st.h>
/srv/gems/call_stack-0.1.0.0/ext/call_stack/call_stack.c:#include <st.h>
/srv/gems/callwith-0.0.2/ext/callwith_ext.c:#include <st.h>
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/ruby-debug-base-0.10.4/ext/ruby_debug.c:#include <st.h>
/srv/gems/cbc-wrapper-2.9.9.4/ext/cbc-wrapper/cbc_wrap.c:#include <st.h>
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/extensions/x86_64-darwin-17/2.5.0-static/msgpack-1.3.1/mkmf.log:3: #include <st.h>
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/gems/ffi-1.11.1/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/chida_fib-0.1.0/shoulda/ruby/1.8/gems/rcov-1.0.0/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/chida_fib-0.1.0/shoulda/ruby/1.8/gems/rcov-1.0.0/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/classicCMS-0.2.3/vendor/bundle/gems/ffi-1.0.11/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/classiccms-0.7.5/vendor/bundle/gems/ffi-1.0.11/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/codders-curb-0.8.0/ext/curb_multi.c:  #include <st.h>
/srv/gems/comiditaULL-0.1.1/vendor/bundle/ruby/2.3.0/gems/ffi-1.9.18/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/comidita_ull-0.1.1/vendor/bundle/ruby/2.3.0/gems/ffi-1.9.18/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/config_gems_initialization_aim-0.1.1/vendor/bundle/ruby/2.5.0/gems/ffi-1.9.25/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/config_gems_initialization_aim-0.1.4/vendor/bundle/ruby/2.5.0/gems/ffi-1.9.25/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/couchbase-1.3.15/ext/couchbase_ext/couchbase_ext.h:#include <st.h>
/srv/gems/coupa-libxml-ruby-1.1.4/ext/libxml/ruby_xml_xpath_context.c:#include <st.h>
/srv/gems/crystalizer-0.2.2/lib/ruby2cext/compiler.rb:        "#include <st.h>",
/srv/gems/cups-0.1.10/ext/ruby_cups.h:#include <st.h>
/srv/gems/curb-0.9.10/ext/curb_multi.c:  #include <st.h>
/srv/gems/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/ffi-1.9.25/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rcov-1.0.0/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rcov-1.0.0/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/dirty_history-0.7.3/dirty_history/ruby/1.9.1/gems/rcov-0.9.11/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/dirty_history-0.7.3/dirty_history/ruby/1.9.1/gems/rcov-0.9.11/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/dirty_history-0.7.3/dirty_history/ruby/1.9.1/gems/rcov-0.9.9/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/dirty_history-0.7.3/dirty_history/ruby/1.9.1/gems/rcov-0.9.9/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/domo-0.0.5/vendor/bundle/ruby/1.9.1/gems/nokogiri-1.5.0/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/dramsay-rubyosa-0.4.0/src/rbosa.c:#include <st.h>
/srv/gems/dramsay-rubyosa-0.4.0/src/rbosa_sdef.c:#include <st.h>
/srv/gems/evdispatch-0.4.2/ext/revdispatch/revdispatch.cc:#include <st.h>
/srv/gems/evilr-1.0.0/ext/evilr/evilr.c:#include <st.h>
/srv/gems/ferret-0.11.8.7/ext/r_analysis.c:#  include <st.h>
/srv/gems/ferret-0.11.8.7/ext/r_index.c:#  include <st.h>
/srv/gems/ferret-0.11.8.7/ext/r_search.c:#  include <st.h>
/srv/gems/ferret-0.11.8.7/ext/r_utils.c:#  include <st.h>
/srv/gems/fluent-plugin-detect-memb-exceptions-0.0.2/vendor/bundle/ruby/2.0.0/gems/msgpack-1.1.0/ext/msgpack/mkmf.log:3: #include <st.h>
/srv/gems/fotonauts-curb-0.7.7/ext/curb_multi.c:  #include <st.h>
/srv/gems/fxruby-1.6.42/ext/fox16_c/core_wrap.cpp:#include <st.h>
/srv/gems/fxruby-1.6.42/ext/fox16_c/dc_wrap.cpp:#include <st.h>
/srv/gems/fxruby-1.6.42/ext/fox16_c/swigruby.h:#include <st.h>
/srv/gems/ghazel-curb-0.7.15.1/ext/curb_multi.c:  #include <st.h>
/srv/gems/gigpark-rcov-0.8.6/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/gigpark-rcov-0.8.6/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/glebm-nokogiri-1.4.2.1/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/globegit-postgresql-plruby-0.5.4/src/plruby.h:#include <st.h>
/srv/gems/gonzui-1.2/ext/xmlformatter/xmlformatter.c:#include <st.h>
/srv/gems/gosu-0.15.2/src/RubyGosu.cxx:#include <st.h>
/srv/gems/gss-0.0.7/vendor/bundle/ruby/1.9.1/gems/nokogiri-1.6.2.1-x86-mingw32/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/gtk2-3.4.3/ext/gtk2/global.h:#  include <st.h>
/srv/gems/guardtime-0.0.5/ext/guardtime.c:#  include <st.h>
/srv/gems/gus-curb-0.8.7/ext/curb_multi.c:  #include <st.h>
/srv/gems/hooligan495-rcov-0.8.1.3.0/ext/rcovrt/callsite.c:#include <st.h>
/srv/gems/hooligan495-rcov-0.8.1.3.0/ext/rcovrt/rcovrt.c:#include <st.h>
/srv/gems/horseman-0.0.5/vendor/ruby/1.9.1/gems/nokogiri-1.5.2/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/poll.c:#  include <st.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/tryopen.c:#  include <st.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/nokogiri-1.6.7.2/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/raindrops-0.16.0/ext/raindrops/linux_inet_diag.c:#  include <st.h>
/srv/gems/itextomml-1.6.0/ext/itex2MML_ruby.c:#include <st.h>
/srv/gems/jeremy-ruby-prof-0.6.1/ext/ruby_prof.c:#include <st.h>
/srv/gems/jk-ferret-0.11.8.3/ext/r_analysis.c:#  include <st.h>
/srv/gems/jk-ferret-0.11.8.3/ext/r_index.c:#  include <st.h>
/srv/gems/jk-ferret-0.11.8.3/ext/r_search.c:#  include <st.h>
/srv/gems/jk-ferret-0.11.8.3/ext/r_utils.c:#  include <st.h>
/srv/gems/jmoses-couchbase-1.3.6/ext/couchbase_ext/couchbase_ext.h:#include <st.h>
/srv/gems/katsuya-rcov-0.9.7.1/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/katsuya-rcov-0.9.7.1/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/kballard-osx-plist-1.0.3/ext/plist/plist.c:#include <st.h>
/srv/gems/kgio-2.11.3/ext/kgio/poll.c:#  include <st.h>
/srv/gems/kgio-2.11.3/ext/kgio/tryopen.c:#  include <st.h>
/srv/gems/kytea-0.1.5/ext/mykytea_wrap.cxx:#include <st.h>
/srv/gems/libxml-ruby-3.2.0/ext/libxml/ruby_xml_xpath_context.c:#include <st.h>
/srv/gems/libxml-ruby-r19mingw-1.1.4/ext/libxml/ruby_xml_xpath_context.c:#include <st.h>
/srv/gems/mastermind_adeybee-0.1.4/vendor/bundle/ruby/2.2.0/gems/ffi-1.9.10/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/mchung-rcov-0.8.3.3/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/mchung-rcov-0.8.3.3/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/memprof-0.3.10/ext/memprof.c:#include <st.h>
/srv/gems/mergulhao-rcov-0.8.1.3.0/ext/rcovrt/callsite.c:#include <st.h>
/srv/gems/mergulhao-rcov-0.8.1.3.0/ext/rcovrt/rcovrt.c:#include <st.h>
/srv/gems/mkrf-0.2.3/test/sample_files/syck-0.55/ext/ruby/ext/syck/syck.h:#include <st.h>
/srv/gems/mkrf-0.2.3/test/sample_files/syck-0.55/lib/syck.h:#include <st.h>
/srv/gems/mochilo-2.0/ext/mochilo/mochilo.h:#include <st.h>
/srv/gems/mrpin-amf-2.1.12/ext/rocketamf_ext/class_mapping.c:#include <st.h>
/srv/gems/mrpin-amf-2.1.12/ext/rocketamf_ext/serializer.h:#include <st.h>
/srv/gems/mrpin-rocketamf-2.0.1/ext/rocketamf_ext/class_mapping.c:#include <st.h>
/srv/gems/mrpin-rocketamf-2.0.1/ext/rocketamf_ext/serializer.h:#include <st.h>
/srv/gems/namelessjon-curb-0.2.4/ext/curb_multi.c:#include <st.h>
/srv/gems/nokogiri-fitzsimmons-1.5.5.3/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/nokogiri-maglev--1.5.5.20120817130721/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/np-ferret-0.11.6/ext/r_search.c:#include <st.h>
/srv/gems/osx-plist-1.0.3/ext/plist/plist.c:#include <st.h>
/srv/gems/pbosetti-rubyosa-0.5.2/src/rbosa.c:#include <st.h>
/srv/gems/pbosetti-rubyosa-0.5.2/src/rbosa_sdef.c:#include <st.h>
/srv/gems/pgsql-1.5.1/lib/conn.c:    #include <st.h>
/srv/gems/plist4r-1.2.2/ext/osx_plist/plist.c:#include <st.h>
/srv/gems/posix-spawn-0.3.15/ext/posix-spawn.c:#include <st.h>
/srv/gems/qpid_messaging-1.39.0/ext/cqpid/cqpid.cpp:#include <st.h>
/srv/gems/qpid_proton-0.31.0/ext/cproton/cproton.c:#include <st.h>
/srv/gems/rackjour-0.1.8/vendor/gems/gems/ruby-debug-base-0.10.3/ext/ruby_debug.c:#include <st.h>
/srv/gems/rblineprof-0.3.7/ext/rblineprof.c:  #include <st.h>
/srv/gems/rbtrace-0.4.14/ext/rbtrace.c:#include <st.h>
/srv/gems/rbtree-0.4.2/rbtree.c:#include <st.h>
/srv/gems/rbtree2-0.0.3/ext/rbtree.c:#include <st.h>
/srv/gems/rbtree3-0.6.0/rbtree.c:#include <st.h>
/srv/gems/rcov-1.0.0/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/rcov-1.0.0/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/rdf-smart-0.0.168/ext/rsm.h:#include <st.h>
/srv/gems/rdp-ruby-prof-0.7.4/ext/ruby_prof.h:#include <st.h>
/srv/gems/relevance-rcov-0.9.2.1/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/relevance-rcov-0.9.2.1/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/rhack-1.4.0/ext/curb/curb_multi.c:  #include <st.h>
/srv/gems/rhack-1.4.0/ext/curb-original/curb_multi.c:  #include <st.h>
/srv/gems/rsanheim-rcov-0.8.1.5.4/ext/rcovrt/callsite.c:#include <st.h>
/srv/gems/rsanheim-rcov-0.8.1.5.4/ext/rcovrt/rcovrt.c:#include <st.h>
/srv/gems/ruby-cntk-0.1.1/ext/cntk/cntk_wrap.cxx:#include <st.h>
/srv/gems/ruby-debug-base-0.10.4/ext/ruby_debug.c:#include <st.h>
/srv/gems/ruby-eigen-0.0.11/ext/eigen/eigen_wrap.cxx:#include <st.h>
/srv/gems/ruby-internal-0.8.5/ext/internal/module/module.c:#include <st.h>
/srv/gems/ruby-internal-0.8.5/ext/internal/node/node.c:#include <st.h>
/srv/gems/ruby-libvirt-0.7.1/ext/libvirt/common.c:#include <st.h>
/srv/gems/ruby-libvirt-0.7.1/ext/libvirt/domain.c:#include <st.h>
/srv/gems/ruby-libvirt-catphish-0.7.1/ext/libvirt/common.c:#include <st.h>
/srv/gems/ruby-libvirt-catphish-0.7.1/ext/libvirt/domain.c:#include <st.h>
/srv/gems/ruby-opencv-0.0.18/ext/opencv/opencv.h:#include <st.h>
/srv/gems/ruby-prof-danielhoey-0.8.1/ext/ruby_prof/call_tree.h:#include <st.h>
/srv/gems/ruby-prof-danielhoey-0.8.1/ext/ruby_prof/ruby_prof.h:#include <st.h>
/srv/gems/ruby-rpm-1.3.1/ext/rpm/package.c:#include <st.h>
/srv/gems/ruby-watchman-0.0.2/ext/ruby-watchman/watchman.c:#include <st.h>
/srv/gems/ruby2cext-0.2.0/lib/ruby2cext/compiler.rb:                            "#include <st.h>",
/srv/gems/rubyosa-0.4.0/src/rbosa.c:#include <st.h>
/srv/gems/rubyosa-0.4.0/src/rbosa_sdef.c:#include <st.h>
/srv/gems/s0nspark-rubyosa-0.5.2/src/rbosa.c:#include <st.h>
/srv/gems/s0nspark-rubyosa-0.5.2/src/rbosa_sdef.c:#include <st.h>
/srv/gems/sa-ferret-0.11.6.3/ext/r_analysis.c:#include <st.h>
/srv/gems/sa-ferret-0.11.6.3/ext/r_index.c:#include <st.h>
/srv/gems/sa-ferret-0.11.6.3/ext/r_search.c:#include <st.h>
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/nokogiri-1.6.6.2/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/scalm-RocketAMF-1.0.0/ext/rocketamf_ext/class_mapping.c:#include <st.h>
/srv/gems/scalm-RocketAMF-1.0.0/ext/rocketamf_ext/serializer.h:#include <st.h>
/srv/gems/sdsykes-ferret-0.11.6.19/ext/r_search.c:#include <st.h>
/srv/gems/sfcc-0.5.0/ext/sfcc/sfcc.h:# include <st.h>
/srv/gems/shoes-3.0.1/req/binject/ext/binject_c/binject.c:#include <st.h>
/srv/gems/shoes-3.0.1/req/ftsearch/ext/ftsearchrt/ftsearch.c:#include <st.h>
/srv/gems/shoes-3.0.1/shoes/ruby.h:#include <st.h>
/srv/gems/silhouette-2.0.0/silhouette_ext.c:#include <st.h>
/srv/gems/skaes-ruby-prof-0.7.3/ext/ruby_prof.h:#include <st.h>
/srv/gems/spicycode-rcov-0.8.2.1/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/spicycode-rcov-0.8.2.1/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/sstephenson-rubyosa-0.5.1/src/rbosa.c:#include <st.h>
/srv/gems/sstephenson-rubyosa-0.5.1/src/rbosa_sdef.c:#include <st.h>
/srv/gems/supplement-2.11/lib/supplement.c:    #include <st.h>
/srv/gems/swf_embed-0.0.6/ext/swf_embed.c:#include <st.h>
/srv/gems/swipe-rails-0.0.5/vendor/bundle/gems/nokogiri-1.6.0/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/taf2-curb-0.5.4.0/ext/curb_multi.c:  #include <st.h>
/srv/gems/thorsson_cups-0.1.3/ext/ruby_cups.h:  #include <st.h>
/srv/gems/threatstack-agent-ruby-0.2.1/ext/libinjection/libinjection_wrap.c:#include <st.h>
/srv/gems/u-1.0.2/ext/u/rb_u_string_format.c:#  include <st.h>
/srv/gems/vagrant-actionio-0.0.9/vendor/bundle/gems/ffi-1.4.0/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/vagrant-compose-yaml-0.1.3/vendor/bundle/ruby/2.2.0/gems/ffi-1.9.10/ext/ffi_c/Struct.h:#include <st.h>
/srv/gems/vagrant-compose-yaml-0.1.3/vendor/bundle/ruby/2.2.0/gems/nokogiri-1.6.7.1/ext/nokogiri/nokogiri.h:#include <st.h>
/srv/gems/valo-rcov-0.8.3.4/ext/rcovrt/1.8/callsite.c:#include <st.h>
/srv/gems/valo-rcov-0.8.3.4/ext/rcovrt/1.8/rcovrt.c:#include <st.h>
/srv/gems/vim-jar-0.1.2.0001/bundler/ruby/1.8/gems/curb-0.7.8/ext/curb_multi.c:  #include <st.h>
/srv/gems/vim-jar-0.1.2.0001/bundler/ruby/1.8/gems/ruby-debug-base-0.10.4/ext/ruby_debug.c:#include <st.h>
/srv/gems/vorbis_comment-1.0.2/vorbis_comment_ext.c:#include <st.h>
/srv/gems/xmlhash-1.3.7/ext/xmlhash/xmlhash.c:# include <st.h>
```

```
ko1@aluminium:~$ gem-codesearch "include <rubyio.h>"
/srv/gems/aarontc-serialport-1.4.0/ext/native/serialport.h:   #include <rubyio.h>
/srv/gems/amp-0.5.3/ext/amp/bz2/bz2.c:    #include <rubyio.h>
/srv/gems/amp-pure-0.5.0/ext/amp/bz2/bz2.c:#include <rubyio.h>
/srv/gems/blackwinter-libxslt-ruby-1.0.1/ext/libxslt/libxslt.h:#include <rubyio.h>
/srv/gems/bluetooth-1.0/ext/bluetooth/linux/ruby_bluetooth.c:#include <rubyio.h>
/srv/gems/bluetooth-1.0/ext/bluetooth/win32/ruby_bluetooth.cpp:#include <rubyio.h>
/srv/gems/brianmario-bzip2-ruby-0.2.5/ext/bzip2.c:#include <rubyio.h>
/srv/gems/bz2-0.2.2/ext/bz2/bz2.c:#include <rubyio.h>
/srv/gems/bzip2-ruby-0.2.7/ext/bzip2.c:#include <rubyio.h>
/srv/gems/bzip2-ruby-rb20-0.2.7/ext/bzip2/common.h:#  include <rubyio.h>
/srv/gems/cairo-1.16.6/ext/cairo/rb_cairo_surface.c:#  include <rubyio.h>
/srv/gems/cehoffman-ruby-serialport-0.7.0/ext/serialport.h:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/gems/puma-3.12.1/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/chinwag-1.2.1/ext/chinwag/rb_chinwag_ext.h:#include <rubyio.h>
/srv/gems/clogger-2.3.0/ext/clogger_ext/clogger.c:#  include <rubyio.h>
/srv/gems/cmpi-bindings-0.9.9/ext/cmpi-bindings/cmpi_wrap.c:#include <rubyio.h>
/srv/gems/ebb-0.3.2/ext/ebb_ffi.c:#include <rubyio.h>
/srv/gems/evdispatch-0.4.2/ext/revdispatch/util.h:#include <rubyio.h>
/srv/gems/fastgeoip-0.2.0/ext/ruby_geoip.h:#include <rubyio.h>
/srv/gems/fluent-plugin-detect-memb-exceptions-0.0.2/vendor/bundle/ruby/2.0.0/gems/cool.io-1.5.0/ext/iobuffer/mkmf.log:3: #include <rubyio.h>
/srv/gems/fluent-plugin-detect-memb-exceptions-0.0.2/vendor/bundle/ruby/2.0.0/gems/cool.io-1.5.0/ext/iobuffer/mkmf.log: 3: #include <rubyio.h>
/srv/gems/hornetseye-openexr-1.0.2/ext/openexrinput.cc:#include <rubyio.h>
/srv/gems/hornetseye-openexr-1.0.2/ext/openexroutput.cc:#include <rubyio.h>
/srv/gems/hparra-serialport-0.7.2/src/serialport.h:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/hybridgroup-serialport-1.2.1/ext/native/serialport.h:   #include <rubyio.h>
/srv/gems/icanhasaudio-0.1.3/ext/icanhasaudio/native.h:#include <rubyio.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/kgio.h:#  include <rubyio.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/my_fileno.h:#  include <rubyio.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/sock_for_fd.h:#  include <rubyio.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/kgio-2.10.0/ext/kgio/tryopen.c:#  include <rubyio.h>
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/raindrops-0.16.0/ext/raindrops/my_fileno.h:#  include <rubyio.h>
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/kgio-2.11.3/ext/kgio/kgio.h:#  include <rubyio.h>
/srv/gems/kgio-2.11.3/ext/kgio/my_fileno.h:#  include <rubyio.h>
/srv/gems/kgio-2.11.3/ext/kgio/sock_for_fd.h:#  include <rubyio.h>
/srv/gems/kgio-2.11.3/ext/kgio/tryopen.c:#  include <rubyio.h>
/srv/gems/launch-2.0.0/ext/launch.c:#include <rubyio.h>
/srv/gems/libxsl-ruby-0.3.6/ext/xml/libxml-ruby/libxml.h:#include <rubyio.h>
/srv/gems/libxsl-ruby-0.3.6/ext/xml/libxslt.h:#include <rubyio.h>
/srv/gems/libxslt-ruby-1.2.0/ext/libxslt/libxslt.h:#include <rubyio.h>
/srv/gems/libxslt-ruby-r19mingw1-0.9.7/ext/libxslt/libxslt.h:#include <rubyio.h>
/srv/gems/libxslt-ruby19-0.9.8/ext/libxslt/libxslt.h:#include <rubyio.h>
/srv/gems/logstash-filter-csharp-0.1.0/vendor/bundle/jruby/2.3.0/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/logstash-filter-htmlentities-0.1.0/vendor/bundle/jruby/1.9/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/logstash-output-scalyr-0.1.5/vendor/bundle/jruby/2.5.0/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/mkrf-0.2.3/test/sample_files/libxml-ruby-0.3.8/ext/xml/libxml.h:#include <rubyio.h>
/srv/gems/mmap-0.2.6/ext/mmap/mmap.c:#include <rubyio.h>
/srv/gems/monoxit-serialport-1.2.1.2/ext/native/serialport.h:   #include <rubyio.h>
/srv/gems/mrcooper-logstash-output-azuresearch-0.2.2/vendor/jruby/2.5.0/gems/puma-2.16.0-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/net-geoip-0.07/ext/ruby_geoip.h:#include <rubyio.h>
/srv/gems/newt-0.9.7/ext/ruby_newt/ruby_newt.c:#include <rubyio.h>
/srv/gems/nmea-0.4/ext/nmea.h:#include <rubyio.h>
/srv/gems/nmea-0.4/ext/ruby_nmea.cpp:#include <rubyio.h>
/srv/gems/nmea-0.4/serialport/serialport.c:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/openwsman-2.6.2/ext/openwsman/openwsman.i:#include <rubyio.h>
/srv/gems/openwsman-2.6.2/ext/openwsman/openwsman.i:#include <rubyio.h>
/srv/gems/pfcas-0.2.5/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/puma-4.3.5/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/puma-simon-3.7.2/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/rack-tctp-0.9.14/ext/engine/engine.c:#include <rubyio.h>
/srv/gems/raindrops-0.19.1/ext/raindrops/my_fileno.h:#  include <rubyio.h>
/srv/gems/rdf-smart-0.0.168/ext/rsm.h:#include <rubyio.h>
/srv/gems/revdev-0.2.1/ext/revdev/revdev.c:#include <rubyio.h>
/srv/gems/rhodes-7.1.17/lib/extensions/serialport/ext/serialport.h:   #include <rubyio.h>
/srv/gems/romp-rpc-0.2.0/ext/romp_helper.c:#include <rubyio.h>
/srv/gems/rsense-server-0.5.18/vendor/gems/puma-2.8.2-java/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/ruby-ivy-0.1.0/ext/ivy/rb_ivy.h:#include <rubyio.h>
/srv/gems/ruby-lua-0.4.0/ext/rlua.h:#include <rubyio.h>
/srv/gems/ruby-oci8-2.2.8/ChangeLog:    * ext/oci8/oci8.h: don't include <rubyio.h> which is not used
/srv/gems/ruby-oci8-master-2.0.7/ChangeLog:     * ext/oci8/oci8.h: don't include <rubyio.h> which is not used
/srv/gems/ruby-serialport-0.7.0/ext/serialport.h:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/ruby-xpath-0.4.0/ext/xpath/rb_utils.h:#include <rubyio.h>
/srv/gems/ruby-xpath-0.4.0/ext/xpath/xpath.h:#include <rubyio.h>
/srv/gems/ruby-xslt-0.9.10/ext/xslt_lib/rb_utils.h:     #include <rubyio.h>
/srv/gems/ruby-xslt-0.9.10/ext/xslt_lib/xslt.h: #include <rubyio.h>
/srv/gems/serialport-1.3.1/ext/native/serialport.h:   #include <rubyio.h>
/srv/gems/shoes-3.0.1/req/binject/ext/binject_c/binject.c:#include <rubyio.h>
/srv/gems/shoes-3.0.1/req/ftsearch/ext/ftsearchrt/ftsearch.c:#include <rubyio.h>
/srv/gems/sinotify-0.0.2/ext/src/sinotify.c:#include <rubyio.h>
/srv/gems/sleepy_penguin-3.5.2/ext/sleepy_penguin/sleepy_penguin.h:#  include <rubyio.h>
/srv/gems/staugaard-ruby-serialport-0.7.0/ext/serialport.h:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/supplement-2.11/lib/supplement/locked.c:    #include <rubyio.h>
/srv/gems/supplement-2.11/lib/supplement/terminal.c:    #include <rubyio.h>
/srv/gems/supplement-2.11/lib/supplement.c:    #include <rubyio.h>
/srv/gems/swerling-sinotify-0.0.2/ext/src/sinotify.c:#include <rubyio.h>
/srv/gems/tauplatform-1.0.3/lib/extensions/serialport/ext/serialport.h:   #include <rubyio.h>
/srv/gems/tdiary-5.1.2/vendor/bundle/ruby/2.6.0/gems/puma-4.3.0/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/toholio-serialport-0.7.2/src/serialport.h:#include <rubyio.h>  /* ruby io inclusion */
/srv/gems/wendell-puma-2.9.2/ext/puma_http11/mini_ssl.c:#include <rubyio.h>
/srv/gems/willcannings-ebb-0.2.0/src/ebb_ruby.c:#include <rubyio.h>
/srv/gems/wiretap-0.11/ext/testserver/server.cpp:#include <rubyio.h>
/srv/gems/wiretap-0.11/ext/thread_worker.cpp:#include <rubyio.h>
/srv/gems/zeromq-0.0.2/extension/extension.h:#include <rubyio.h>
/srv/gems/zmq-2.1.4/rbzmq.c:#include <rubyio.h>
```

Conclusion:

* matz: remove and try it with the preview


### [[Feature #16989]](https://bugs.ruby-lang.org/issues/16989) Sets need ♥️, aka the "Set Program" (marcandre)

* Bring `Set` into core
* Insure interoperability with `Array` (e.g so `array & set` works and is efficient)
* Shorthand syntax for static frozen sets of string/symbols (e.g. `%ws{hello world}`)

Preliminary discussion:

* mame: we discussed this issue at the previous meeting

> matz: Positive to introduce Set into core. But we need to first introduce a set literal. { x, y, z } is good, but JavaScript uses it as another meanings, so it would bring confusion. I have no idea.
> knu: As a set library maintainer, I'll first communicate, and try to solve the easy issues that can be sorted out in the library side.

Discussion:

* 

Conclusion:

* knu is absent, so postponed


### [[Feature #17055]](https://bugs.ruby-lang.org/issues/17055) Allow suppressing uninitialized instance variable and method redefined verbose mode warnings (jeremyevans0)

* This keeps the default behavior the same as before, but allows opt-in to supress the warnings.
* This allows for high-performance code (uninitialized instance variables) and safe code (redefining methods without removing in multi-thread environments) to work without issuing warnings in verbose mode.
* Is it OK to commit the patch?

Preliminary discussion:

* mame: 

Discussion:

* matz: I'd like to separate the two topics of "uninitialized ivar" and "method redefinition"
* matz: I don't like the callback API
* naruse: can we specially handle `@iv = nil` in initialize method?
  * all instance variables are initialized as nil by default
  * if there is `@iv = nil` in initialize, the warning should be suppressed
* ko1: it would be possible, if we can accept some corner cases

```ruby
class C
  def initialize
    if false
      @iv = nil
    end
    @iv # the warning would be suppressed wrongly
  end
end

C.new
```

* mame: how about accessing the ivar before initalization

```ruby
class C
  def initialize
    p @iv # the warning would be suppressed wrongly
    @iv = nil
  end
end

C.new
```

Conclusion:

* matz: sorry but postpone it


### [[Feature #14844]](https://bugs.ruby-lang.org/issues/14844) Future of RubyVM::AST? (eregon)

* Other Ruby implementations need to be able to implement it too given more and more gems use it. Where do we move it?

Preliminary discussion:

* ko1: we cannot guarantee the compatibility of the API
* mame: we may move it out from RubyVM even if we do not guarantee the compatibility
* ko1: it may be better to bundle parser gem?

Conclusion:

* matz: let's move it out.  Will reply


### [[Feature #15752]](https://bugs.ruby-lang.org/issues/15752) A dedicated module for experimental features (eregon)

* Create `ExperimentalFeatures` and move things there or `RubyVM` exists on all Ruby implementations? Please choose.

Preliminary discussion:

* mame: is there any problem (for us, MRI developers) if other Ruby implementations support `RubyVM`?
* mame: Anyway, we never guarantee its comaptibility

Discussion:

* naruse: this ticket was created for resolve_feature_path, and it is already moved out.
* matz: AST is also being moved out. So, I don't think there is a motivation for the new name space.

Conclusion:

* matz: will reply.


### [[Bug #17017]](https://bugs.ruby-lang.org/issues/17017) Range#max & Range#minmax incorrectly use Float end as max (dan0042)

* `(1..3.1).max` has resulted in `3.1` since ruby 1.9 and has now been "fixed" to return `3`. Matz, please confirm?

Preliminary discussion:

```ruby
(1..3.1).max
#=> 
# -1.8   : 3
# 1.9-2.7: 3.1
# master : 3

(1..3.1).each.max
#=> 3

# master
(1..3).min #=> 1
(1..3).max #=> 3
(1..3.5).min #=> 1
(1..3.5).max #=> 3
(1.5..3).min #=> 1.5
(1.5..3).max #=> 3
(1.5..3.5).min #=> 1.5
(1.5..3.5).max #=> 3.5

# 2.7
(1..3).min #=> 1
(1..3).max #=> 3
(1..3.5).min #=> 1
(1..3.5).max #=> 3.5 ##
(1.5..3).min #=> 1.5
(1.5..3).max #=> 3
(1.5..3.5).min #=> 1.5
(1.5..3.5).max #=> 3.5
```

* mame: the method has returned 3.1 since 1.9, and it has been changed (by jeremy) at master
* mrkn: the question is the behavior of `Range#max` for float range. 
* mame: If `Range#max` is just an optimization of `Enumerable#max`, `(1.0..3.1).max` should raise an exception (if we ignore compatibility issue)

Conclusion:

* matz: `Range#max` is different from `Enumerable#max`
* matz: it should keep 2.7 behavior


### [[Feature #17103]](https://bugs.ruby-lang.org/issues/17103) Add a :since option to `ObjectSpace.dump_all` (byroot)

* On large heap, `dump_all` can take over a minute and generate huge (6+GiB) files.
* `since: 42`, means only objects matching `generation && generation >= 42` would be dumped.
* Sometimes you are only interested in objects allocated past a certain point, this make that use case much faster and efficient.

Preliminary discussion:

* ko1: will ask to write a rdoc
* ko1: What will happen when trace_object_allocations are not enabled?

Conclusion:

* matz: accepted


### [[Feature #17104]](https://bugs.ruby-lang.org/issues/17104) Do not freeze interpolated strings when using frozen-string-literal (eregon)

* It seems many people agree there is no point to freeze interpolated strings.
* Interpolated strings are not "simple literals" (just like `1 + 2` is not a literal) and they involve mutation anyway, it seems of very little value to freeze, and it's inconvenient.
* @bughit: The reasons are, it will reduce allocations, be more logical, less surprising and produce simpler code (when a mutable string is needed and you don't want extra allocations)

Discussion:

* mame: corner case.

```
$ ruby --dump=i -e '"#{ "foo" }xx"'
== disasm: #<ISeq:<main>@-e:1 (1,0)-(1,10)> (catch: FALSE)
0000 putstring                              "fooxx"                   (   1)[Li]
0002 leave

$ ruby --dump=i -e '# frozen-string-literal: true
"#{ "foo" }xx"'
== disasm: #<ISeq:<main>@-e:1 (1,0)-(2,14)> (catch: FALSE)
0000 putobject                              "fooxx"                   (   2)[Li]
0002 leave
```

Conclusion:

* matz: I accept.


### [[Feature #13381]](https://bugs.ruby-lang.org/issues/13381) Expose rb_fstring and its family to C extensions (byroot)

* It's only missing a decision on the names. Could this be discussed again?

Preliminary discussion:

* new suggestions: `rb_str_pool` (eregon) or `rb_interned_str` (byroot)
* ko1: they may be slower if Ractor is merged

Discussion:

* 

Conclusion:

* ko1: let me consider with nobu later


### [[Bug #17105]](https://bugs.ruby-lang.org/issues/17105) A single `return` can return to two different places in a proc inside a lambda inside a method (eregon)

* Is current implementation correct? Should it not be a `LocalJumpError` if the surrounding lambda is no longer on stack?

Preliminary discussion:

```ruby
def m1
  lambda {
    Proc.new {
      return :return # return from the lambda
    }.call
  }.call
  :return_value
end

p m1 #=> :return_value

# ruby-1.6.0            :return
# ...
# ruby-1.6.8            :return
# ruby-1.8.0            :return_value
# ruby-1.8.1            :return_value
# ruby-1.8.2-preview1   :return
# ...
# ruby-1.8.7-p374       :return
# ruby-1.9.0-0          :return_value
#...
# ruby-2.7.1            :return_value


def m2
  lambda {
    Proc.new {
      return :return # return from the lambda
    }
  }.call.call
  :return_value
end

p m2 #=> :return (eregon expects LocalJumpError)

# ruby-1.6.0            :return
# ...
# ruby-2.7.1            :return
```

* mame: it looks intended, though I have no preference

> is this behavior intentional? or is it a bug?

* mame: I think it is intentional (though I'm not against the change)

> what are actually the semantics of return inside a proc?

* mame: it returns from outer method/lambda stack frame (which is determined completely dynamically)

Discussion:

* 

Conclusion:

* matz: it is intentional, but I can accept the behavior change. Will reply.


### [[Feature #17125]](https://bugs.ruby-lang.org/issues/17125) Remove `Thread.exclusive` (znz)

* `Thread.exclusive` is deprecated since 2.3.

Preliminary discussion:

* mame: looks good
* ko1: looks good


Discussion:

```
ko1@aluminium:~$ gem-codesearch 'Thread\.exclusive'
/srv/gems/3mix-castronaut-0.5.0.2/vendor/rack/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/ActiveSambaLdap-0.0.7/test/command.rb:    Thread.exclusive do
/srv/gems/IOWA-1.0.2/microprojects/DiskCache/src/iowa/Mutex.rb:                         Thread.exclusive do
/srv/gems/IOWA-1.0.2/microprojects/LRUCache/src/iowa/Mutex.rb:                          Thread.exclusive do
/srv/gems/IOWA-1.0.2/src/iowa/Mutex.rb:                         Thread.exclusive do
/srv/gems/Pistos-ramaze-2009.06.12/lib/ramaze/reloader.rb:      # Run cycle in a Thread.exclusive, by default no threads are used.
/srv/gems/Pistos-ramaze-2009.06.12/lib/ramaze/reloader.rb:          Thread.exclusive{ cycle }
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:              Thread.exclusive(){ @connstatus << value if @connstatus_last != value} if var == 'CONNSTATUS'
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:                Thread.exclusive(){@global_res << s}
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:                  Thread.exclusive(){ @missed_chats_puff << chat_id }
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:                  Thread.exclusive(){
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:        Thread.exclusive(){
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:        Thread.exclusive(){
/srv/gems/SkypeR-0.0.9/lib/skyper/service.rb.org:          Thread.exclusive(){
/srv/gems/SystemTimer-1.2.3/lib/system_timer.rb:  Thread.exclusive do    # Avoid race conditions for monitor and pool creation
/srv/gems/active-record-without-callbacks-0.0.4/lib/active-record-without-callbacks.rb:    Thread.exclusive do
/srv/gems/activeldap3-1.2.3/test/command.rb:    Thread.exclusive do
/srv/gems/activesambaldap-0.1.0/test/command.rb:    Thread.exclusive do
/srv/gems/acts_as_lookup-0.2.1/lib/acts_as_lookup.rb:    Thread.exclusive do
/srv/gems/adammck-rubysms-0.8.2/lib/rubysms/application.rb:                             Thread.exclusive do
/srv/gems/ahoy-0.1.4/lib/ahoy/contact_list.rb:      Thread.exclusive do
/srv/gems/akamai_bookmarklet-0.1.2/vendor/gems/ruby/1.8/gems/rack-1.1.0/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/rack-1.5.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/rack-1.6.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/api_hammer-0.19.0/lib/api_hammer/rails/unmunged_request_params.rb:    # Thread.exclusive is not optimal but we need to ensure that any other params parsing occurring in other
/srv/gems/api_hammer-0.19.0/lib/api_hammer/rails/unmunged_request_params.rb:    @unmunged_params ||= Thread.exclusive do
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/1.9.1/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/apl-library-0.0.90/vendor/bundle/ruby/1.9.1/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/archipelago-0.3.0/lib/archipelago/current.rb:        Thread.exclusive do
/srv/gems/archipelago-0.3.0/lib/archipelago/current.rb:        Thread.exclusive do
/srv/gems/auto_error-0.0.18/lib/core_ext/proc_ext.rb:      Thread.exclusive do
/srv/gems/autogc-0.0.3/lib/autogc.rb:    Thread.exclusive do
/srv/gems/autumn-3.1.11/lib/autumn/leaf.rb:      return unless Thread.exclusive { stems.ready?.all? }
/srv/gems/autumn-3.1.11/lib/autumn/misc.rb:    Thread.exclusive { super unless full? }
/srv/gems/autumn-3.1.11/lib/autumn/stem_facade.rb:        Thread.exclusive { msg.each_line { |line| privmsgt chans.to_a, line.strip unless line.strip.empty? } }
/srv/gems/autumn-3.1.11/spec/ctcp_spec.rb:      Thread.exclusive { 15.times { @stem.ctcp_reply_ping "Pinger", "ABC123" } }
/srv/gems/bbmb-2.3.3/History.txt:* Replaced Thread.exclusive with Mutex#synchronize
/srv/gems/bdb-0.2.6.5/lib/bdb/environment.rb:          Thread.exclusive { yield }
/srv/gems/belgium_2050_model-0.0.7/lib/belgium_2050_model/belgium_2050_model_result.rb:    Thread.exclusive do
/srv/gems/bogo-config-0.2.2/lib/bogo-config/config.rb:        objects = Thread.exclusive do
/srv/gems/bone-0.3.2/vendor/drydock-0.6.8/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&print_at_lamb) : print_at_lamb.call
/srv/gems/bone-0.3.2/vendor/drydock-0.6.8/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&position_lamb) : position_lamb.call
/srv/gems/bootstrap-sass-3.4.1/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/bootstrap-sass-backport-3.3.1.0/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/bougyman-autumn-3.1.11/lib/autumn/leaf.rb:      return unless Thread.exclusive { stems.ready?.all? }
/srv/gems/bougyman-autumn-3.1.11/lib/autumn/misc.rb:    Thread.exclusive { super unless full? }
/srv/gems/bougyman-autumn-3.1.11/lib/autumn/stem_facade.rb:        Thread.exclusive { msg.each_line { |line| privmsgt chans.to_a, line.strip unless line.strip.empty? } }
/srv/gems/bougyman-autumn-3.1.11/spec/ctcp_spec.rb:      Thread.exclusive { 15.times { @stem.ctcp_reply_ping "Pinger", "ABC123" } }
/srv/gems/buzzcore-0.6.6/lib/buzzcore/extra/thread_utils.rb:            Thread.exclusive do
/srv/gems/buzzcore-0.6.6/lib/buzzcore/extra/thread_utils.rb:            Thread.exclusive do
/srv/gems/buzzcore-0.6.6/lib/buzzcore/extra/thread_utils.rb:            Thread.exclusive do
/srv/gems/buzzcore-0.6.6/lib/buzzcore/extra/thread_utils.rb:            Thread.exclusive do
/srv/gems/buzzware-buzzcore-0.2.3/lib/buzzcore/thread_utils.rb:         Thread.exclusive do
/srv/gems/buzzware-buzzcore-0.2.3/lib/buzzcore/thread_utils.rb:         Thread.exclusive do
/srv/gems/buzzware-buzzcore-0.2.3/lib/buzzcore/thread_utils.rb:         Thread.exclusive do
/srv/gems/buzzware-buzzcore-0.2.3/lib/buzzcore/thread_utils.rb:         Thread.exclusive do
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/rack-1.3.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/rack-1.3.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/rack-1.3.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/capistrano-progress-0.0.1/lib/capistrano/progress/parallel_status.rb:    Thread.exclusive do
/srv/gems/carbon-3.0.1/lib/carbon/query.rb:      @pool || Thread.exclusive do
/srv/gems/cassiopeia-0.2.0/lib/cassiopeia/action_controller_server_mixin.rb:        Thread.exclusive do
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.0.pre/vendor/bundle/gems/rack-1.4.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/rack-1.4.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/rack-1.4.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/chroma-js-1.0.7/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/chump-0.5.1/lib/chump.rb:    Thread.exclusive { @buff = str + @buff }
/srv/gems/classicCMS-0.2.3/vendor/bundle/gems/rack-1.4.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/classiccms-0.7.5/vendor/bundle/gems/rack-1.4.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/codders-SystemTimer-1.2.3.1/lib/system_timer.rb:  Thread.exclusive do    # Avoid race conditions for monitor and pool creation
/srv/gems/comboy-autumn-3.1/lib/autumn/leaf.rb:      return unless Thread.exclusive { stems.ready?.all? }
/srv/gems/comboy-autumn-3.1/lib/autumn/misc.rb:    Thread.exclusive { super unless full? }
/srv/gems/comboy-autumn-3.1/lib/autumn/stem_facade.rb:        Thread.exclusive { msg.each_line { |line| privmsgt chans.to_a, line.strip unless line.strip.empty? } }
/srv/gems/cond-0.3.2/lib/cond/thread_local.rb:      Thread.exclusive {
/srv/gems/continuent-tools-core-0.13.0/providers/awsec2.rb:        index = Thread.exclusive{ (region_index = region_index+1) }
/srv/gems/csquare-cast-0.2.2/lib/cast/tempfile.rb:      Thread.exclusive do
/srv/gems/data_miner-3.0.0/CHANGELOG:  * Replace class-level mutexes with simple Thread.exclusive calls
/srv/gems/data_miner-3.0.0/lib/data_miner/active_record_class_methods.rb:      @data_miner_script || ::Thread.exclusive do
/srv/gems/data_miner-3.0.0/lib/data_miner.rb:    @logger || ::Thread.exclusive do
/srv/gems/data_miner-3.0.0/lib/data_miner.rb:    @model_names || ::Thread.exclusive do
/srv/gems/de.oddb-2.0.1/lib/oddb/html/util/lookandfeel.rb:    Thread.exclusive {
/srv/gems/decc_2050_model-3.5.1/lib/model/model_result.rb:    Thread.exclusive do
/srv/gems/deep_test-1.2.2/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/deep_test-1.2.2/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/deep_test_pre-2.0/lib/telegraph/ack_sequence.rb:      Thread.exclusive do
/srv/gems/delano-drydock-0.6.8/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&print_at_lamb) : print_at_lamb.call
/srv/gems/delano-drydock-0.6.8/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&position_lamb) : position_lamb.call
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rack-1.4.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rack-1.5.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/dispatch_queue-1.1.0/lib/dispatch_queue.rb:      Thread.exclusive { values << value }
/srv/gems/dispatch_queue-1.1.0/test/test_dispatch_queue.rb:      Thread.exclusive{ results << value }
/srv/gems/dk-bdb-0.2.4.2/lib/bdb/environment.rb:          Thread.exclusive { yield }
/srv/gems/docdiff-0.5.0/lib/docdiff/diff/speculative.rb:        Thread.exclusive {tg.list.each {|t| t.kill if t != Thread.current}}
/srv/gems/docdiff-0.5.0/lib/docdiff/diff/speculative.rb:        Thread.exclusive {tg.list.each {|t| t.kill if t != Thread.current}}
/srv/gems/drctrl-0.1.1.1/lib/drctrl.rb:        Thread.exclusive do
/srv/gems/drnic-liquid-2.1.0/test/extra/breakpoint.rb:    Thread.exclusive do
/srv/gems/drydock-0.6.9/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&print_at_lamb) : print_at_lamb.call
/srv/gems/drydock-0.6.9/lib/drydock/console.rb:    RUBY_VERSION =~ /1.9/ ? Thread.exclusive(&position_lamb) : position_lamb.call
/srv/gems/eac-rack-1.1.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/edgar-rack-1.2.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/empathy-0.1.0/empathy.mspec:    "Thread.fork","Thread.allocate","Thread.abort_on_exception","Thread.exclusive"
/srv/gems/empathy-0.1.0/rubyspec/core/thread/exclusive_spec.rb:  describe "Thread.exclusive" do
/srv/gems/empathy-0.1.0/rubyspec/core/thread/exclusive_spec.rb:      Thread.exclusive { ScratchPad.record true }
/srv/gems/empathy-0.1.0/rubyspec/core/thread/exclusive_spec.rb:      Thread.exclusive { :result }.should == :result
/srv/gems/fc-webicons-0.0.4/vendor/bundle/ruby/1.9.1/gems/rack-1.4.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/ffi-libsodium-0.4.7/lib/libsodium.rb:Thread.exclusive do
/srv/gems/fsdb-0.7.4/History.txt:- use db cache_mutex instead of Thread.exclusive.
/srv/gems/fsdb-0.7.4/lib/fsdb/formats.rb:        Thread.exclusive do # Dir.chdir is not threadsafe
/srv/gems/fsdb-0.7.4/lib/fsdb/persistent.rb:      Thread.exclusive do
/srv/gems/fsdb-0.7.4/lib/fsdb/server.rb:      Thread.exclusive {@transactions += 1}
/srv/gems/fsdb-0.7.4/lib/fsdb/server.rb:      Thread.exclusive {@transactions -= 1}
/srv/gems/fsdb-0.7.4/lib/fsdb/util.rb:  # Dir.glob does) because #glob operates under Thread.exclusive, because it
/srv/gems/fsdb-0.7.4/lib/fsdb/util.rb:    Thread.exclusive do
/srv/gems/fsdb-0.7.4/test/lib/db-for-test.rb:  Thread.exclusive do
/srv/gems/fsdb-0.7.4/test/lib/db-for-test.rb:  Thread.exclusive do
/srv/gems/ghazel-SystemTimer-1.2.1.1/lib/system_timer.rb:  Thread.exclusive do    # Avoid race conditions for monitor and pool creation
/srv/gems/global_2050_model-0.0.2/lib/global_2050_model/global_2050_model_result.rb:    Thread.exclusive do
/srv/gems/grape-reload-0.1.0/lib/grape/reload/grape_api.rb:        Thread.list.size > 1 ? Thread.exclusive { Grape::Reload::Watcher.reload! } : Grape::Reload::Watcher.reload!
/srv/gems/grape-reload-0.1.0/lib/grape/reload/grape_api.rb:        Thread.list.size > 1 ? Thread.exclusive { Grape::Reload::Watcher.reload! } : Grape::Reload::Watcher.reload!
/srv/gems/graphael-on-rails-0.5.1/vendor/bundle/gems/rack-1.4.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/grosser-parallel-0.3.1/lib/parallel.rb:    require 'thread' # to get Thread.exclusive
/srv/gems/grosser-parallel-0.3.1/lib/parallel.rb:        index = Thread.exclusive{ current+=1 }
/srv/gems/hiraku-rack-1.0.0.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/rack-1.6.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/jasmine-sauce-0.2.2/lib/sauce/jasmine/runner.rb:              Thread.exclusive do
/srv/gems/jason-o-matic-deep_test-1.2.2.15/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/jason-o-matic-deep_test-1.2.2.15/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/jperkins-deep_test-1.2.2/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/jperkins-deep_test-1.2.2/lib/deep_test/metrics/queue_lock_wait_time_measurement.rb:        Thread.exclusive do
/srv/gems/jstorimer-deep-test-2.0.0/lib/telegraph/ack_sequence.rb:      Thread.exclusive do
/srv/gems/kastner-rack-0.3.186/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/kjvarga-rack-1.0.0/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/lgustafson-validatable-1.8.7/lib/validatable/object_extension.rb:    # Compatibility check (Ruby 1.9 uses Thread.exclusive whereas Ruby 1.8 uses
/srv/gems/lgustafson-validatable-1.8.7/lib/validatable/object_extension.rb:      omname = Thread.exclusive do
/srv/gems/link-checker-0.7.2/lib/link_checker.rb:        Thread.exclusive { @links << page }
/srv/gems/link-checker-0.7.2/lib/link_checker.rb:            Thread.exclusive { results << response }
/srv/gems/link-checker-0.7.2/lib/link_checker.rb:            Thread.exclusive { results <<
/srv/gems/link-checker-0.7.2/lib/link_checker.rb:    Thread.exclusive do
/srv/gems/lnbackup-2.4/lib/lnbackup/backup.rb:      Thread.exclusive do
/srv/gems/lnbackup-2.4/lib/lnbackup/backup.rb:      Thread.exclusive do
/srv/gems/lock_and_cache-6.0.0/CHANGELOG:  * Don't use deprecated Thread.exclusive
/srv/gems/lock_and_cache_msgpack-4.1.0/CHANGELOG:  * Don't use deprecated Thread.exclusive
/srv/gems/lock_method-0.5.5/CHANGELOG:  * A singleton doesn't need its own single-use mutex - just use Thread.exclusive
/srv/gems/lock_method-0.5.5/lib/lock_method.rb:    @config || ::Thread.exclusive do
/srv/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/log4r-color-1.2.2/lib/log4r-color/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/log4r-color-1.2.2/lib/log4r-color/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/log4rails-1.1.11/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/log4rails-1.1.11/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/log_simulator-0.2.1/lib/resolve.rb:            Thread.exclusive do
/srv/gems/logical-insight-0.4.7/lib/insight/instrumentation/instrument.rb:        Thread.exclusive do
/srv/gems/logmerge-1.0.3/lib/logmerge/resolv.rb:          id = Thread.exclusive {
/srv/gems/logmerge-1.0.3/lib/logmerge/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/logmerge-1.0.3/lib/logmerge/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/lowang-parallel-0.4.2/lib/parallel.rb:require 'thread' # to get Thread.exclusive
/srv/gems/lowang-parallel-0.4.2/lib/parallel.rb:        index = Thread.exclusive{ current+=1 }
/srv/gems/m2r-2.1.0/lib/m2r.rb:    #   but it uses Thread.exclusive to achive that.
/srv/gems/m2r-2.1.0/lib/m2r.rb:      Thread.exclusive do
/srv/gems/m2r-2.1.0/test/unit/m2r_test.rb:      Thread.exclusive do
/srv/gems/mack-0.8.3.1/lib/gems/rack-0.9.1/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/mack-0.8.3.1/lib/mack/utils/reloader.rb:        Thread.exclusive {
/srv/gems/manveru-innate-2009.07/lib/innate/state.rb:      Thread.exclusive(&block)
/srv/gems/manveru-innate-2009.07/lib/innate/view.rb:      Thread.exclusive{
/srv/gems/manveru-ramaze-2009.07/lib/ramaze/reloader.rb:      # Run cycle in a Thread.exclusive, by default no threads are used.
/srv/gems/manveru-ramaze-2009.07/lib/ramaze/reloader.rb:          Thread.exclusive{ cycle }
/srv/gems/martinstannard-activemerchant-0.1.0/test/extra/breakpoint.rb:    Thread.exclusive do
/srv/gems/masover-castronaut-0.5.0.1/vendor/rack/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/merb-core-1.1.3/spec10/public/webrat/test_app/gems/gems/fastthread-1.0.1/ext/fastthread/fastthread.c: *     Thread.exclusive { block }   => obj
/srv/gems/merb-core-1.1.3/spec10/public/webrat/test_app/gems/gems/rack-0.4.0/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/merb_merchant-1.4.1/test/extra/breakpoint.rb:    Thread.exclusive do
/srv/gems/mizuno-0.6.11/lib/mizuno/reloader.rb:            Thread.exclusive do
/srv/gems/mizuno-0.6.11/lib/mizuno/reloader.rb:            Thread.exclusive { reload! }
/srv/gems/mongoid-bolt-1.1.0/test/helper.rb:      Thread.exclusive do
/srv/gems/monk-glue-0.0.1/lib/monk/glue/reloader.rb:        Thread.exclusive { reload! }
/srv/gems/mtn_log4r-1.1.12/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/mtn_log4r-1.1.12/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/multi_mime-1.1.0/CHANGELOG.md:* [Ensure loading an adaptor is Thread.exclusive](https://github.com/karlfreeman/multi_mime/commit/4c39deeb98f8b6fa86d49c50c6c3eec0c626d22a) - [@karlfreeman](https://github.com/karlfreeman).
/srv/gems/nbudin-castronaut-0.7.5/vendor/rack/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/nddrylliog_youtube_it-2.1.4/lib/youtube_it/client.rb:      Thread.exclusive do
/srv/gems/net-irc-0.0.9/lib/net/irc/server.rb:          Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/channel_manager_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/channel_manager_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/channel_manager_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/channel_manager_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/channel_manager_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc-0.0.9/spec/net-irc_spec.rb:                   Thread.exclusive do
/srv/gems/net-irc2-0.0.15/lib/net/irc/server.rb:    Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/channel_manager_spec.rb:      Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/channel_manager_spec.rb:      Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/channel_manager_spec.rb:      Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/channel_manager_spec.rb:      Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/channel_manager_spec.rb:      Thread.exclusive do
/srv/gems/net-irc2-0.0.15/spec/net-irc_spec.rb:      Thread.exclusive do
/srv/gems/net-mdns-0.4/lib/net/dns/resolv.rb:          id = Thread.exclusive {
/srv/gems/net-mdns-0.4/lib/net/dns/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/net-mdns-0.4/lib/net/dns/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/nightcrawler_swift-1.0.0/Changelog.md:  - Remove "Thread.exclusive is deprecated, use Mutex" warning
/srv/gems/objectbouncer-0.1.4/lib/objectbouncer/class_methods.rb:        Thread.exclusive do
/srv/gems/ohm-3.1.1/benchmarks/common.rb:    Thread.exclusive { @value += 1 }
/srv/gems/ohm_util-0.1/benchmarks/common.rb:    Thread.exclusive { @value += 1 }
/srv/gems/orca-0.4.1/lib/orca/logger.rb:      Thread.exclusive { puts "#{@node.to_s} [#{@package.bold}] #{out}" }
/srv/gems/orientdb4r-0.5.1/lib/orientdb4r.rb:      Thread.exclusive {
/srv/gems/p8-castronaut-0.6.1.1/vendor/rack/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/pastry-0.3.2/lib/pastry.rb:    Thread.exclusive do
/srv/gems/path-log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/path-log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/peck-0.5.4/lib/peck.rb:        spec_index = Thread.exclusive { current_spec += 1 }
/srv/gems/perforce-1.0.2/lib/perforce.rb:        Thread.exclusive {
/srv/gems/pirj-sinatra-contrib-1.3.0/lib/sinatra/reloader.rb:            Thread.exclusive { Reloader.perform(klass) }
/srv/gems/pistol-0.0.2/lib/pistol.rb:        Thread.exclusive { reload! }
/srv/gems/powerhome-activeldap-3.2.3/test/command.rb:    Thread.exclusive do
/srv/gems/progress-3.5.2/CHANGELOG.markdown:* Fix `Thread.exclusive` deprecation in Ruby 2.3.0 [#4](https://github.com/toy/progress/pull/4) [@katsuma](https://github.com/katsuma)
/srv/gems/ptomato-ramaze-2009.02.1/lib/ramaze/reloader.rb:      # Run cycle in a Thread.exclusive, by default no threads are used.
/srv/gems/ptomato-ramaze-2009.02.1/lib/ramaze/reloader.rb:          Thread.exclusive{ cycle }
/srv/gems/puppet-parse-0.1.4/lib/vendor/puppet/external/lock.rb:    Thread.exclusive {flock(LOCK_UN) if $reader_count[self.stat.ino] == 1}
/srv/gems/qoobaa-rack-1.0.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/rack-insight-0.6.4/lib/rack/insight/instrumentation/instrument.rb:        Thread.exclusive do
/srv/gems/rack-unreloader-1.7.0/CHANGELOG:* Avoid use of Thread.exclusive, preventing warnings on ruby 2.3 (celsworth, jeremyevans) (#6)
/srv/gems/rackjour-0.1.8/vendor/gems/gems/rack-1.0.1/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/railscast-assets-0.0.2/vendor/bundle/gems/backbone-forms-on-rails-0.10.0/vendor/bundle/gems/rack-1.4.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/railscast-assets-0.0.2/vendor/bundle/gems/rack-1.4.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/ramaze-2012.12.08/lib/ramaze/reloader.rb:      # Run cycle in a Thread.exclusive, by default no threads are used.
/srv/gems/ramaze-2012.12.08/lib/ramaze/reloader.rb:          Thread.exclusive{ cycle }
/srv/gems/ramaze.ch.oddb.org-1.0.0/lib/oddb/html/util/lookandfeel.rb:    Thread.exclusive {
/srv/gems/rb_facebook-0.0.8/lib/rb_facebook.rb:            Thread.exclusive do
/srv/gems/rb_facebook-0.0.8/lib/rb_facebook.rb:            Thread.exclusive do
/srv/gems/rbnacl-7.1.1/CHANGES.md:- Remove use of Thread.exclusive when initializing library ([#128])
/srv/gems/refe-0.8.0.3/ChangeLog:         Thread.exclusive.
/srv/gems/refe-0.8.0.3/NEWS:    (Object#marshal_dump, Thread.exclusive, etc.)
/srv/gems/relevance-castronaut-0.7.5/vendor/rack/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/remix-0.4.9/lib/remix.rb:    # in deadlock. `Thread.exclusive` is used.
/srv/gems/remix-0.4.9/lib/remix.rb:        Thread.exclusive do
/srv/gems/remix-0.4.9/lib/remix.rb:    # in deadlock. `Thread.exclusive` is used.
/srv/gems/remix-0.4.9/lib/remix.rb:        Thread.exclusive do
/srv/gems/rffw-0.0.5/lib/rffw/server/client.rb:      Thread.exclusive {
/srv/gems/rffw-0.0.5/lib/rffw/server/client.rb:      Thread.exclusive {
/srv/gems/rffw-0.0.5/lib/rffw/server/client.rb:      while client = Thread.exclusive{@@clients.pop}
/srv/gems/rffw-0.0.5/lib/rffw/server/client.rb:      Thread.exclusive{ @@clients.delete self }
/srv/gems/rgd2-ffij-0.0.3/lib/gd2/font.rb:      Thread.exclusive do
/srv/gems/rgd2-ffij-0.0.3/lib/gd2/font.rb:      Thread.exclusive do
/srv/gems/rhodes-7.1.17/platform/shared/ruby/miniprelude.c:"  #    Thread.exclusive { block }   => obj\n"
/srv/gems/rhodes-7.1.17/platform/shared/ruby/prelude.c:"    warn \"Thread.exclusive is deprecated, use Mutex\", caller\n"
/srv/gems/rhodes-7.1.17/spec/framework_spec/app/spec/core/thread/exclusive_spec.rb:  describe "Thread.exclusive" do
/srv/gems/rjspotter-innate-2009.06.31/lib/innate/state.rb:      Thread.exclusive(&block)
/srv/gems/rjspotter-innate-2009.06.31/lib/innate/view.rb:      Thread.exclusive{
/srv/gems/rjspotter-ramaze-2009.06.31/lib/ramaze/reloader.rb:      # Run cycle in a Thread.exclusive, by default no threads are used.
/srv/gems/rjspotter-ramaze-2009.06.31/lib/ramaze/reloader.rb:          Thread.exclusive{ cycle }
/srv/gems/rmrwi-0.1.9/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/ro-1.4.6/lib/ro/git.rb:      Thread.exclusive do
/srv/gems/ruby-activeldap-0.8.3.1/test/command.rb:    Thread.exclusive do
/srv/gems/ruby-breakpoint-0.5.1/lib/breakpoint.rb:    Thread.exclusive do
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/doc/ChangeLog-0.60_to_1.1:    * eval.c (thread_set_critical): remove Thread.exclusive, add
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/doc/ChangeLog-2.3.0:  * prelude.rb (Thread.exclusive): warn as deprecated.
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/prelude.c:"    warn \"Thread.exclusive is deprecated, use Thread::Mutex\", caller\n"
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/prelude.rb:  #    Thread.exclusive { block }   => obj
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/prelude.rb:  # only block other threads which also use the Thread.exclusive mechanism.
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/prelude.rb:    warn "Thread.exclusive is deprecated, use Thread::Mutex", caller
/srv/gems/ruby-compiler-0.1.1/vendor/ruby/sample/drb/name.rb:    Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/resolv.rb:          id = Thread.exclusive {
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell/command-processor.rb:      Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell/process-controller.rb:          Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell/system-command.rb:      Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell/system-command.rb:      Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell/system-command.rb:      Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/shell.rb:    Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/thread.rb:  def Thread.exclusive
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/thread.rb:    Thread.exclusive do
/srv/gems/ruby_on_ruby-0.0.1/vendor/javascripts/emscripted-ruby/lib/thread.rb:    Thread.exclusive do
/srv/gems/rubycut-sinatra-contrib-1.4.0/lib/sinatra/reloader.rb:            Thread.exclusive { Reloader.perform(klass) }
/srv/gems/rubykiq-1.0.2/CHANGELOG.md:* [Ensure loading a client is Thread.exclusive](https://github.com/karlfreeman/rubykiq/commit/0a68c9dc670f94efe8a344869db0b7ba4a97d1d7) - [@karlfreeman](https://github.com/karlfreeman).
/srv/gems/rubykiq-1.0.2/lib/rubykiq/client.rb:      Thread.exclusive do
/srv/gems/rubykiq-1.0.2/lib/rubykiq.rb:    Thread.exclusive do
/srv/gems/rubysl-thread-2.1/spec/exclusive_spec.rb:  describe "Thread.exclusive" do
/srv/gems/rubysl-thread-2.1/spec/exclusive_spec.rb:      Thread.exclusive { ScratchPad.record Thread.critical }
/srv/gems/rubysl-thread-2.1/spec/exclusive_spec.rb:      Thread.exclusive { :result }.should == :result
/srv/gems/rubysl-thread-2.1/spec/exclusive_spec.rb:      Thread.exclusive {}
/srv/gems/rubysl-thread-2.1/spec/exclusive_spec.rb:      lambda { Thread.exclusive { raise Exception } }.should raise_error(Exception)
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/activerecord-session_store-0.1.1/lib/active_record/session_store/session.rb:          Thread.exclusive { setup_sessid_compatibility! }
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/bootstrap-sass-3.3.5.1/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/rack-1.6.4/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:            Thread.exclusive do
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:            Thread.exclusive do
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:          Thread.exclusive do
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:      Thread.exclusive do
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:      Thread.exclusive do
/srv/gems/scalarium-0.4.4/lib/scalarium/cli.rb:      Thread.exclusive do
/srv/gems/scout_realtime-1.0.5/lib/vendor/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/scout_realtime-1.0.5/lib/vendor/sinatra-contrib-1.4.1/lib/sinatra/reloader.rb:            Thread.exclusive { Reloader.perform(klass) }
/srv/gems/screw-0.0.1/lib/screw/logger.rb:      Thread.exclusive do
/srv/gems/screw-0.0.1/lib/screw/logger.rb:        Thread.exclusive do
/srv/gems/seamusabshere-active_merchant-1.4.2.3/test/extra/breakpoint.rb:    Thread.exclusive do
/srv/gems/session-3.2.0/README:    - added Thread.exclusive{} wrapper when io is read to works in multi
/srv/gems/space-0.0.9/lib/space/events/sources.rb:          Thread.exclusive do
/srv/gems/space-0.0.9/lib/space/events/sources.rb:          Thread.exclusive do
/srv/gems/space_observatory-0.0.2/exec/with-space-observatory.rb:            Thread.exclusive do
/srv/gems/space_observatory-0.0.2/exec/with-space-observatory.rb:      Thread.exclusive do
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive{ @threads << Thread.current }
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:                Thread.exclusive{ @timers[Thread.current] = timeout }
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive{ @threads.delete(Thread.current) }
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:                Thread.exclusive{ @timers.delete(Thread.current) }
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive{ do_lock }
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive do
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive{clean} if cln
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:        # not perform a Thread.exclusive, and as such this method should
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:        # only be called from within a Thread.exclusive{}
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:            Thread.exclusive do
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:                        Thread.exclusive do
/srv/gems/splib-1.4.3/lib/splib/Monitor.rb:                            Thread.exclusive do
/srv/gems/stomper-2.0.6/lib/stomper/support.rb:    Thread.exclusive do
/srv/gems/style-sass-1.0.0/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/supplement-2.11/lib/supplement.c: *     Thread.exclusive { ... }  -> obj
/srv/gems/swipe-rails-0.0.5/vendor/bundle/gems/rack-1.4.5/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/system_timer-1.2.4/lib/system_timer.rb:  Thread.exclusive do    # Avoid race conditions for monitor and pool creation
/srv/gems/table_warnings-1.0.1/lib/table_warnings.rb:    @registry || Thread.exclusive do
/srv/gems/tauplatform-1.0.3/platform/shared/ruby/miniprelude.c:"  #    Thread.exclusive { block }   => obj\n"
/srv/gems/tauplatform-1.0.3/platform/shared/ruby/win32/miniprelude.c:"  #    Thread.exclusive { block }   => obj\n"
/srv/gems/tauplatform-1.0.3/spec/framework_spec/app/spec/core/thread/exclusive_spec.rb:  describe "Thread.exclusive" do
/srv/gems/technomancy-rack-0.3.0/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/tennpipes-base-3.6.6/lib/tennpipes-base/reloader/rack.rb:          Thread.list.size > 1 ? Thread.exclusive { Tennpipes.reload! } : Tennpipes.reload!
/srv/gems/timocratic-rack-1.0.0/lib/rack/reloader.rb:        Thread.exclusive {
/srv/gems/toolbox_esten-1.0.0/tasks/converter/network.rb:          Thread.exclusive { write_cached_files path, name => contents[name] }
/srv/gems/toolmantim-zeroconf-0.0.2/lib/net/dns/resolv.rb:          id = Thread.exclusive {
/srv/gems/toolmantim-zeroconf-0.0.2/lib/net/dns/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/toolmantim-zeroconf-0.0.2/lib/net/dns/resolv.rb:          id = Thread.exclusive { @id = (@id + 1) & 0xffff }
/srv/gems/torquebox-console-0.3.0/vendor/bundle/jruby/1.9/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/tubeclip-0.1.2/lib/tubeclip/client.rb:      Thread.exclusive do
/srv/gems/unicorn-timeout-1.0.0/README.md:    # Block that will run (within Thread.exclusive, on monitor thread) just before sending signal to process
/srv/gems/unicorn-timeout-1.0.0/lib/unicorn/timeout.rb:        Thread.exclusive do
/srv/gems/unicorn-timeout-1.0.0/lib/unicorn/timeout.rb:        Thread.exclusive do
/srv/gems/upsert-2.9.10/CHANGELOG:  * Stop using Thread.exclusive - thanks @hpetru https://github.com/seamusabshere/upsert/pull/67
/srv/gems/urbivore-0.0.1/lib/urbivore/logger.rb:        Thread.exclusive do
/srv/gems/vagrant-actionio-0.0.9/vendor/bundle/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/vagrant-actionio-0.0.9/vendor/bundle/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/vagrant-actionio-0.0.9/vendor/bundle/gems/rack-1.5.2/lib/rack/reloader.rb:          Thread.exclusive{ reload! }
/srv/gems/vagrant-compose-yaml-0.1.3/vendor/bundle/ruby/2.2.0/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/vagrant-compose-yaml-0.1.3/vendor/bundle/ruby/2.2.0/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/vagrant-unbundled-2.2.9.0/vendor/bundle/ruby/2.7.0/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/vagrant-unbundled-2.2.9.0/vendor/bundle/ruby/2.7.0/gems/log4r-1.1.10/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/vinted-log4r-1.1.11/lib/log4r/repository.rb:# Using Thread.exclusive seems to be more efficient than using
/srv/gems/vinted-log4r-1.1.11/lib/log4r/repository.rb:# Using Thread.exclusive, 5000 iterations:
/srv/gems/whitewash-2.1/ChangeLog.mtn:wrap global variables handling in Thread.exclusive
/srv/gems/wires-0.6.2/lib/wires/base/launcher.rb:        Thread.exclusive do
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive{ @threads << Thread.current }
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:                Thread.exclusive{ @timers[Thread.current] = timeout }
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive{ @threads.delete(Thread.current) }
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:                Thread.exclusive{ @timers.delete(Thread.current) }
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive{ do_lock }
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive do
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive{clean} if cln
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:        # not perform a Thread.exclusive, and as such this method should
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:        # only be called from within a Thread.exclusive{}
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:            Thread.exclusive do
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:                        Thread.exclusive do
/srv/gems/woolen_common-0.0.14/lib/woolen_common/splib/Monitor.rb:                            Thread.exclusive do
/srv/gems/wrap_method-0.0.0/lib/wrap_method.rb:    Thread.exclusive do
/srv/gems/youtube_it-2.4.2/lib/youtube_it/client.rb:      Thread.exclusive do
/srv/gems/yt_analytics-0.0.4/lib/yt_analytics/client.rb:      Thread.exclusive do
/srv/gems/zeevex_concurrency-0.0.2/spec/event_loop_spec.rb:      Thread.exclusive do
/srv/gems/zeevex_concurrency-0.0.2/spec/event_loop_spec.rb:      Thread.exclusive do
```
* https://bugs.ruby-lang.org/issues/11904

Conclusion:

* matz: go ahead



### [[Feature #14723]](https://bugs.ruby-lang.org/issues/14722) python's buffer protocol clone (mrkn)

* I renamed `buffer` to `memory_view`.
    * This name is already used in Python for the Python object wrapper of `PyBuffer`.
* I've added tests for some functions and a simple memory view implementation of a String for testing these functions.
    * https://github.com/ruby/ruby/pull/3261/files
 
Discussion:

* 

Conclusion:

* mrkn: no time, ask akr to review the patch



### [[Feature #16812]](https://bugs.ruby-lang.org/issues/16812) Allow slicing arrays with ArithmeticSequence (mrkn)

* I found the wrong test case, so the implementation was also in wrong state.  The proposed patch has been fixed.
* There are opposing opinions in the following case:
  ```ruby
  [0,1,2,3,4,5][0..10] #=> [0, 1, 2, 3, 4, 5]
  [0,1,2,3,4,5][6..10] #=> []
  [0,1,2,3,4,5][7..10] #=> nil
  
  [0,1,2,3,4,5][(3..0)%-2] #=> [3, 1]
  [0,1,2,3,4,5][(4..0)%-2] #=> [4, 2, 0]
  [0,1,2,3,4,5][(5..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(6..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(7..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(8..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(9..0)%-2] #=> [5, 3, 1]
  ```
  or
  ```ruby
  [0,1,2,3,4,5][(3..0)%-2] #=> [3, 1]
  [0,1,2,3,4,5][(4..0)%-2] #=> [4, 2, 0]
  [0,1,2,3,4,5][(5..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(6..0)%-2] #=> [4, 2, 0]
  [0,1,2,3,4,5][(7..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(8..0)%-2] #=> [4, 2, 0]
  [0,1,2,3,4,5][(9..0)%-2] #=> [5, 3, 1]
  ```
  or
  ```ruby
  [0,1,2,3,4,5][(3..0)%-2] #=> [3, 1]
  [0,1,2,3,4,5][(4..0)%-2] #=> [4, 2, 0]
  [0,1,2,3,4,5][(5..0)%-2] #=> [5, 3, 1]
  [0,1,2,3,4,5][(6..0)%-2] #=> nil???
  [0,1,2,3,4,5][(7..0)%-2] #=> nil
  [0,1,2,3,4,5][(8..0)%-2] #=> nil
  [0,1,2,3,4,5][(9..0)%-2] #=> nil
  ```
  Python follows the former.
  ```
  >>> [0, 1, 2, 3, 4, 5][6::-2]
  [5, 3, 1]
  ```
  
Discussion:

* mame: Python's behavior looks strange
* akr: agreed, need to find discussion about this behavior in Python
* akr: how does Python's document explain this behavior?

Conclusion:

* mrkn: Will survey Python


----


https://github.com/ruby/ruby/commit/9156a04d918
