---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-02-17

https://bugs.ruby-lang.org/issues/18557

## DateTime and location

* 2022/02/17 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2022/03/17 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* 3.1.1 this week! hopefully
* 3.2.0 preview 1: will be released after 3.1.1

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #13110]](https://bugs.ruby-lang.org/issues/13110) Byte-based operations for String (shugo)

  * Are there any objections to introduce byteindex, byterindex and byteoffset? (not bytesplice)
  * Should the method names be `byte_index`, `byte_rindex`, and `byte_offset`?

Preliminary discussion:

* String#byte_index, String#byte_rindex, MatchData#byte_offset are proposed
* String#byte_splice is also proposed?

```ruby
# encoding: utf-8
s = "あああいいいあああ"
#    　　　　　　^^^^
p s.byteindex(/ああ/, 4) #=> 18
x, y = Regexp.last_match.byteoffset(0) #=> [18, 24]
s.bytesplice(x...y, "おおお") # s[x...y] = "おおお"
p s #=> "あああいいいおおおあ"
```

Discussion:

* shugo: bytesplice was arguable, so I'd like to focus on `String#byte_index`, `String#byte_rindex`, and `MatchData#byte_offset`
* shugo: underscore is needed?
* matz: I prefer byterindex to `byte_rindex`. I want them to be consistent with existing methods like `String#byteslice`
* akr: I'm afraid if the regexp engine may cause buffer overflow. Is it safe?
* matz: As I recall, oniguruma does a proper check for the case
* naruse: It may be good to raise an exception if the index is not a proper bound in terms of encoding
* znz: How about adding a new alias `byte_slice` to `byteslice`?
* matz: it's too much

Conclusion:

* naruse: Let me consider for a while whether it is safe in terms of encoding.


### [[Feature #18249]](https://bugs.ruby-lang.org/issues/18249) The ABI version of dev builds of CRuby does not correspond to the ABI (peterzhu2118)

  - ABI checking for development Ruby.
  - Calculates the MD5 hash of header files during compilation of Ruby, stores this MD5 in native extensions, and compares the values when loading the binary.
  - Prevents incompatible binaries from being loaded, which can cause bugs, crashes, or other incorrect behavior.
  - Summary of motivation here: https://bugs.ruby-lang.org/issues/18249#note-15

Preliminary discussion:

* mame: I'm afraid if this check prevents our productivity. I sometimes compile native extensions with a bit older ruby version, do "git pull" and make, and re-run a test script with already-compiled extensions. It is indeed a bit dangerous, but it works in almost all cases
* ko1: I often rewrite assert.h to enable/disable some compile options 

Discussion:

* naruse: Even without this change, I think it is possible in a user code to detect the (virtual) version from the actual installation
* mame: As far as I know, the current Peter's patch lets "make install" calculate a hash from some header files. Naruse-san says the same thing is possible after installed by calculating a hash from installed header files ($PREFIX/include/ruby/config.h and so on)
* naruse: In my experience, it is no so frequent to need to recompile ruby even if header files are change a little
* ko1: I guess this year the experience will change. Whenever we change some classes to support VWA, it will break ABI. Recompilation will be more and more frequent
* (mame: failed to log...)
* akr: Even if the change of headers is backward-compabile, the hash will change. Is it okay?
* knu: Will the hash change depending on compile options?
* mame: I guess it will do if config.h is changed. Nobu said some compiler options will be reflected only on rbconfig.rb. Currently rbconfig.rb is not a target to calculate 
* peterzhu: TruffleRuby provides manual ABI version. How about manual ABI version in Ruby?
* nobu: it is a bit hard to manage the version in hand
* matz: We may manage it automatically
* shyouhei: manual ABI versioning is better
* ko1: peter do you know how TruffleRuby manages its ABI version?
* peterzhu: in a text file
* mame: When do we need to increase the ABI version?
* ko1: Mainly whenever we change any data structure exposed in public header files
* mame: In practical, whenever we change header file?
* ko1: Adding a public function does not require the increase
* akr: It is not forward-compatible.
* (mame: failed to log)
* ko1: Will the ABI version be included in the release version? Or it is only for master branch?
* peter: Curently, the check is done only for development branch
* mame: I don't like a check only for development branch. When we try to create a tarball for release, something weird may happen.
* ko1: If so, we can leave the ABI version for release branch.
* mame: Peter, are you considering about performance overhead of the ABI checking?
* peter: It would be small enough
* mame: So what is the problem?
* peter: no problem
---
* akr: IMO, the manual ABI versioning is good, but I don't think the runtime ABI checking is needed
* akr: Versioning should be optimistic, not pessimistic. I don't like semantic versioning
* mame: I agree with it. Only manual ABI versioning will solve compilation time issue. At least I want some way to escape the runtime version check, such as `RUBY_ABI_CHECK=0` or something, if it is introduced
* mame: Let's continue to discuss it in the ticket

Conclusion:

* matz: Let's manage the ABI version manually
* matz: We do not have to increase the ABI version when only something is added. Only increase it only when something existing is changed or deleted
* matz: I have replied to the ticket

### [[Feature #18513]](https://bugs.ruby-lang.org/issues/18513) Hide patchlevel from `ruby -v` (hsbt)

* There is no reason to display it now.

Preliminary discussion:

```
$ ruby -v
ruby 3.1.0p0 (2021-12-25 revision fb4df44d16) [x86_64-linux]
          ^^

$ ruby -e 'p RUBY_DESCRIPTION'
"ruby 3.1.0p0 (2021-12-25 revision fb4df44d16) [x86_64-linux]"
           ^^
```

Discussion:

* matz: looks good
* hsbt: a macro in version.h is not removed yet. In feuture?
* mame: Will we keep a constant "RUBY_PATCHLEVEL"?
* hsbt: keep it for a while because some tools depends on it.

Conclusion:

* matz: Agreed, replied


### [[Feature #18498]](https://bugs.ruby-lang.org/issues/18498) Introduce a public `WeakKeysMap` that compares by equality (byroot)

- Ruby's current weak reference constructs are not very usable.
- The most useful weak collection that is offered by most platform is `WeakKeysMap`
- The keys would be weak references, but the values would be strong references
- It would use equality semantic, just like a regular Hash

Preliminary discussion:

* nobu: Why is "member" needed?
* ko1: It is useful for dedup mechanism
* mame: What methods does the class implement? Hash-compatible + member?
* ko1: I'll ask it. IMO it is a good start to provide the methods that ObjectSpace::WeakMap implements
* nobu: A new top-level constant is too much, I think
* ko1: Can we implement WeakKeysMap#each? Even if it is possible, it is hard. I think we shouldn't provide it 
* nobu: BTW, the current implementation of ObjectSpace::WeakMap#each looks dangerous

Discussion:

* mame: the proposal has four points
  * Do we provide this class?
    * A new class similar to `ObjectSpace::WeakMap` but equality-based lookup instead of identitfy-based lookup
    * Only keys are weak. The values are strongly referenced in the proposed class (In `ObjectSpace::WeakMap`, both keys and values are weakly referenced)
  * If so, what name is good?
  * How about "member" method?
  * What set of method does the class implement?

```ruby=
h = ObjectSpace::WeakMap.new
h["str"] = true
p h["str".dup] #=> nil (because the lookup is based on identity)

# the proposal
h = WeakKeysMap.new
str = "str"
h[str] = true
p h["str".dup] #=> true
GC.start
p h["str"] #=> nil

p h.member("str".dup).object_id == str.object_id #=> true
```

* akr: In terms of "Third party object extension", identity-based lookup is more desirable than equality-based?
* matz: Indeed
* hsbt: Is this the embedded class?
* ko1: yes
* nobu: It's the difficult to extract gem or libraries.
* akr: In terms of "WeakSet", "avoiding cycles in an object graph" is mentioned as a use case, but how is it a problem?
---
* matz: First, it is acceptable to introduce a variant of WeakMap whose only key is weak (corresponding Java's WeakHashMap)

The name of class

* matz: Second, the name.  WeakKeysMap. WeakKeyMap (or whatever) looks better to me. `ObjectSpace::WeakKeyMap` like current `ObjectSpace::WeakMap`?
* matz: Place. I (matz) is against having it under `WeakRef`. I think it should be under `ObjectSpace` a la `WeakMap`.

The name of `#member(key)`

* matz: The name `member` is not acceptable. How about `getkey(key)`?
* ko1: Underscore is neede?
* knu: rubocop dislke `get_...` method.
* mame: How about `key?(key)` that returns the key or nil?
* ko1: `key(key)`?
* matz: I like `getkey(key)`, but there is room to discuss. I'd like to hear opinion from the implementor

Methods

* ko1: How about to start with `ObjectSpace::WeakMap` with `#member` functionality?
* mame: compare_by_identity?

```
hs = Hash.instance_methods.sort
wm = ObjectSpace::WeakMap.instance_methods.sort
p [hs - wm]

[[:<, :<=, :>, :>=, :assoc, :clear, :compact!, :compare_by_identity, :compare_by_identity?, :deconstruct_keys, :default, :default=, :default_proc, :default_proc=, :delete, :delete_if, :dig, :empty?, :except, :fetch, :fetch_values, :filter!, :flatten, :has_key?, :has_value?, :invert, :keep_if, :key, :merge, :merge!, :rassoc, :rehash, :reject!, :replace, :select!, :shift, :slice, :store, :to_hash, :to_proc, :transform_keys, :transform_keys!, :transform_values, :transform_values!, :update, :value?, :values_at]]
```

* mame: ObjectSpace::WeakMap#each is fragile. Is WeakKeyMap#each needed?
* matz: We may remove it. Let's start with the minimum set

Conclusion:

* matz: Acceptable for the class as a whole.
* matz: I like to name `ObjectSpace::WeakKeyMap`
* matz: I like to name `ObjectSpace::WeakKeyMap#getkey(key)` for the behavior of `member`
* matz: Let's start with the minimul method set
  * `ObjectSpace::WeakKeyMap#[](key)`
  * `ObjectSpace::WeakKeyMap#[]=(key, val)`
  * `ObjectSpace::WeakKeyMap#getkey(key)`
  * `ObjectSpace::WeakKeyMap#inspect #=> #<ObjectSpace::WeakKeyMap size=100>`
* mame: "Third party object extension" might be impossible without compare_by_identity. We need to confirm the OP if it is okay

### [[Feature #18568]](https://bugs.ruby-lang.org/issues/18568) Explore lazy RubyGems boot to reduce need for `--disable-gems` (headius)

* With more and more default gems, RubyGems has become a major part of Ruby's startup time.
* On CRuby, the base boot time is reduced by 80% using `--disable-gems`.
* Several ruby-core members would like to remove `--disable-gems` but others and some users need it for fast command line tools.
* It seems like it is time to eliminate the overhead of booting RubyGems and default gemspecs.

Preliminary discussion:

* mame: I believe no one will be against the improvement of Rubygems.
* ko1: I'll ask headius what should be discussed on this meeting.

Discussion:

* ko1: no response from headius.

Conclusion:

* matz: Looks good


### [[Feature #18571]](https://bugs.ruby-lang.org/issues/18571) Remove the bundled sources from release package after Ruby 3.2 (hsbt)

Preliminary discussion:

* mame: looks good, but Aaron seems to be a bit against the removal https://github.com/ruby/psych/issues/535
* mame: In terms of release management, we don't want to bundle such an external library source
* mame: In ruby-build, the bundled version of libffi/libyaml is actually used?

Discussion:

* akr: Keep the mechanism to use the specified code of the third-party libraries if the package is expanded approproately. It is useful when we want to use a library newer than OS provides. I agree that bundling them in Ruby tarball is not needed

Conclusion:

* matz: Please be good.


### [[Bug #18487]](https://bugs.ruby-lang.org/issues/18487) Kernel#binding behaves differently depending on implementation language of items on the stack (jeremyevans0)

* Is it OK to limit Kernel#binding to only look at the preceding frame, instead of the closest Ruby-level frame?
* If so, is the patch acceptable?

Preliminary discussion:

* ko1: is it okay to prevent `public :binding; public_send(:binding)`?
* mame: Jeremy's PR seems to allow creating a binding object for c frame. Is it really needed?

Discussion:

```ruby
class Magic
  define_singleton_method :modify_caller_env!, method(:binding).to_proc >> ->(bndg) { bndg.local_variable_set(:my_var, 42) }
end

my_var = 1

Magic.modify_caller_env!

p my_var #=> 42
```

* ko1: Alan Wu commented an exception is good enough when attempting to create a binding from C frame. I agree with Alan Wu
* mame: Agreed
* matz: I agree to raise an exception on such cases.
* ko1: If it is raised, C-extensions which calls `rb_funcll(binding)` doesn't work. Is it okay?
* matz: Okay.

Conclusion:

* matz: I agree that it is a bug. Raising an exception is good.

### [[Bug #16663]](https://bugs.ruby-lang.org/issues/16663) Add block or filtered forms of Kernel#caller to allow early bail-out (jeremyevans0)

* Since the last developer meeting, a prototype has been created.
* Is the behavior of the prototype acceptable?
* Incorrect usage with `to_enum` raises `StopIteration` instead of crashing.  Is that acceptable?

Preliminary discussion:

* mame: It looks simple enough and reasonable to me
* nobu: looks good
* jhawthorn: Looks good! Would be useful for Rails.

Discussion:

* Previous decision: `Thread.each_caller_location do |frame| ... end` == `caller.each {|frame| ... }`

```ruby=
# Is this reasonable?
Thread.to_enum(:each_caller_location).next  #=> StopIteration

Thread.each_caller_location  #=> nil (mame and nobu like "no block given (LocalJumpError)")

def foo
  Thread.to_enum(:each_caller_location)
end
foo.to_a #=> ["test.rb:4:in `to_a'", "test.rb:4:in `<main>'"]
```

Conclusion:

* matz: Okay


### [[Bug #14103]](https://bugs.ruby-lang.org/issues/14103) Regexp absense operator has no chance to `^C` (jeremyevans0)

* One possible way around this is checking for interrupts and potentially yield to another thread every N times during backtracking.
* I submitted a pull request to do that (N = 1000), and it fixes the example given.
* Is such an approach acceptable? If so, is the patch acceptable?
* One issue not addressed by the patch is the modification of the string being searched by another thread during regexp evaluation (potentially causing a crash), but I'm not sure whether that is possible.

Preliminary discussion:

* mame: The approach itself looks reasonable
* mame: It would be better to fix the issue in oniguruma/onigmo side
* mame: I don't want to see regexec.c depends on ruby headers directly
* nobu: oniguruma/onigmo provides `CHECK_INTERRUPT_IN_MATCH_AT`, which should be reused

```
Merge Onigmo 6.1.1
    
    * Support absent operator https://github.com/k-takata/Onigmo/issues/82
    * https://github.com/k-takata/Onigmo/blob/Onigmo-6.1.1/HISTORY
    
    git-svn-id: svn+ssh://ci.ruby-lang.org/ruby/trunk@57603 b2dd03c8-39d4-4d8f-98ff-823fe69b080e
```

Discussion:


Conclusion:

* no one is against the fix
* nobu: there is room to improve the patch. I'll talk with Jeremy

### [[Feature #16989]](https://bugs.ruby-lang.org/issues/16989) Sets: need ♥️ (greggzst)

* matz has already accepted adding Sets as default. However, there hasn’t been any updates on the progress

Discussion:

* mame: A draft patch

```diff=
diff --git a/prelude.rb b/prelude.rb
index b1e477a3ea..893476d890 100644
--- a/prelude.rb
+++ b/prelude.rb
@@ -20,3 +20,13 @@ def pp(*objs)

   private :pp
 end
+
+module Enumerable
+  def to_set(...)
+    require 'set'
+    self.to_set(...)
+  end
+  alias to_set to_set
+end
+
+autoload(:Set, "set")
```

* ko1: Alternative

```ruby
module Enumerable
  def to_set(klass = Set, ...)
    klass.new(self, ...)
  end
end

autoload(:Set, "set")
```

* knu: I prefer ko1's version. Anyway, do we introduce this really?

Conclusion:

* matz: It looks a good start. I want it to be implemented in C, though
* matz: It is a separate topic about a literal


### [[Feature #18339]](https://bugs.ruby-lang.org/issues/18339) GVL instrumentation API (byroot)

- It would allow to instrument the GVL which is a key metric for threaded environments, and to tune concurrency in applications.
- I implemented a (posix only for now) draft: https://github.com/ruby/ruby/pull/5500.
- The overhead when no hook is registered is just a single unprotected boolean check, so close to zero.
- `rb_gvl_hook_t * rb_gvl_event_new(rb_gvl_callback callback, rb_event_flag_t event)` to register a hook.
- `bool rb_gvl_event_delete(rb_gvl_hook_t * hook)` to unregister it.
- Is the general idea acceptable?
- Any API change before I try to implement the Windows version?

Preliminary discussion:

* ko1: I wrote a comment.

Discussion:

*

Conclusion:

* ko1: I'll handle it


### [[Feature #12962]](https://bugs.ruby-lang.org/issues/12962) Adding "friend" method visibility (tenderlovemaking)

* Allow libraries with Object Oriented internals to call "not public" methods
* Useful when "protected" is too restricted, but the method shouldn't be "public"
* Refinements can't help because they are lexically scoped
  * It's easier to add method visibility than to move all code around

Preliminary discussion:

```ruby=
class B
end

class A
  friend B          # class B is authorized as a friend of class A
  protected def foo # this method can be called from only friends of class A
  end
end

class B
  def bar
    A.new.foo # callable because class B is a friend of class A
  end
end

class C
  def baz
    A.new.foo # NOT callable because class C is not a friend of class A
  end
end
```

* mame: I have wanted something similar to create mutual objects.
* mame: I'm unsure if the complexity like "friend" pays for it or not

```ruby=
class C1
  attr_reader :c2_instance
  # we don't want to make this method public
  def init_for_c2(c2)
    @c2_instance = c2
  end
end
class C2
  attr_reader :c1_instance
  def init_for_c1(c1)
    @c1_instance = c1
  end
end

c1 = C1.new
c2 = C2.new
c1.init_for_c2(c2)
c2.init_for_c1(c1)


# with frind

class C1
  friend C2

  def initialize
    @c2 = C2.new
    @c2.c1 = self
  end
end

class C2
  attr_reader(:c1)
  protected attr_writer(:c1) # setter
end


# with friend

class Node
end

class IfNode < Node
  friend Node

  def eval
    if @cond.eval
      @body.eval
    else
      @else.eval
    end
  end
end

class Stmt < Node
  friend Node

  protected def eval
    ...
  end
end

# joking

class A
  frined Object
  protected def foo
    # callable objects from any objects
  end
end

class Object
  frind Object 
end
```

* ko1: it is difficult to implement it efficiently
* mame: Is the following allowed?

```ruby=
class Z
end

class A
  friend Z
end
 
class B < A
  protected def foo; end
end

class Z
  def foo
    B.new.foo # is it possible to call?
  end
end
```

Discussion:

* matz: I wonder if this is really needed...?
* ko1: We can use `send` to avoid visibility restriction

Conclusion:

* matz: I don't understand the need and don't want to introduce complexity. I will reject


### [[Bug #18580]](https://bugs.ruby-lang.org/issues/18580) `Range#include?` inconsistency for beginless String ranges (zverok)

* `(...'z').include?('ww')` works for strings, due to historical reasons
* there is no semantical justification for it, and it is confusing for users

Preliminary discussion:

```ruby=
('a'..'z').include?('ww') #=> false
(..'z').include?('ww')    #=> true
```

```ruby=
irb(main):001:0> 'ww' < 'z'
=> true
```

* mame: indeed looks weird, but I have no good solution

* `Range#cover?` and `Range#===` returns `true`.
```ruby=
('a'..'z').cover?('ww') #=> true
(..'z').cover?('ww')    #=> true
```

* mame: It might be good to prohibit Range#include? for a beginless range. A beginless range cannot enumerate, so the concept "include" is difficult. But I'm unsure if it is just for consistensy, so  the incompatibility may matter more

Discussion:

* matz: I wonder if Range#include? should not work against a beginless range?
* akr: why?
* matz: It does not respond to "each".
* akr: When does Range#include? behave like Range#cover? ?
* akr: Range#include? for an endless range behaves like Range#cover? too
* akr: Even Range#include? for a range whose both begin and end are there behaves like Range#cover? too

```ruby
("b"..).include?("aa")        #=> false
("zz"..).include?("aaa")      #=> false
("zz".."aaa").include?("aaa") #=> false
("zz".."aaa").include?("zz")  #=> false
```

* akr: I think it is theoretically possible to implement the method properly implementing comparison function in the order defined by String#succ, but anyone need it? I believe no one use it, so how about letting it be simply cover? ?

Conclusion:

* matz: Give me time to consider it


### [[Feature #18585]](https://bugs.ruby-lang.org/issues/18585) Promote find pattern to official feature (ktsj)

* Is it OK to to promote find pattern to official feature?

Conclusion:

* matz: Go ahead


### [[Misc #18362]](https://bugs.ruby-lang.org/issues/18362) mswin builds & vs2022 (nobu)

* What can we do for the bug?

Preliminary discussion:

* nobu: why is `ASSUME` defined by configure discarded in the header?

Conclusion:

* nobu: don't mind. I will do it good


### [[Feature #18576]](https://bugs.ruby-lang.org/issues/18576) Rename `ASCII-8BIT` encoding to `BINARY` (byroot)

- The `ASCII-8BIT` name is very confusing for beginners.
- Even for people who know what it is, it can easily be misread as `US-ASCII`
- It has been aliased as `BINARY` for a very long time, and in the vast majority of the case it's a better description.
- I expect the backward compatibility problems to be very minimal (limited to `Encoding#name`).

Discussion:

* matz: Even if a string is ASCII-8BIT, we want to handle characters in the ASCII range (0x00--0x7f) as ASCII characters. This is why the name "ASCII-8BIT" is picked.
* matz: But it is acceptable to rename it with "BINARY"
* ko1: The following search results is not so huge, but not so small. 

```
$ gem-codesearch '== "ASCII-8BIT"' 

/srv/gems/abaci-0.3.0/vendor/bundle/gems/nokogiri-1.6.8/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/abaci-0.3.0/vendor/bundle/gems/rack-2.0.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/able-neo4j-1.0.0/vendor/bundle/jruby/1.9/gems/logstash-codec-plain-1.0.0/spec/codecs/plain_spec.rb:          insist { a.encoding.name } == "ASCII-8BIT"
/srv/gems/akamai_bookmarklet-0.1.2/vendor/gems/ruby/1.8/gems/rack-1.1.0/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/alphasights-prawn-0.10.4/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/amiba-1.0.2/bin/amiba-wp-import:  if post[:post_content].encoding.name == "ASCII-8BIT"
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/nokogiri-1.6.6.2/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/rack-1.5.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/rack-1.6.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/1.9.1/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/apl-library-0.0.90/vendor/bundle/ruby/1.9.1/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/apl-library-0.0.90/vendor/bundle/ruby/2.1.0/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/argon-1.3.1/vendor/bundle/ruby/2.7.0/gems/nokogiri-1.10.9/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/autocompl-0.2.2/test/dummy/vendor/bundle/ruby/2.3.0/gems/nokogiri-1.7.0.1/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/autocompl-0.2.2/test/dummy/vendor/bundle/ruby/2.3.0/gems/rack-2.0.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/better-faraday-1.2.0/lib/better-faraday.rb:          if body.encoding.name == "ASCII-8BIT"
/srv/gems/better-faraday-1.2.0/lib/better-faraday.rb:          if body.encoding.name == "ASCII-8BIT"
/srv/gems/blobby-s3-1.1.1/lib/blobby/s3_store.rb:        return s if s.encoding.name == "ASCII-8BIT"
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/rack-1.3.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/rack-1.3.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/rack-1.3.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/cassandra-cql-1.2.2/lib/cassandra-cql/utility.rb:        string.encoding.name == "ASCII-8BIT"
/srv/gems/celsius-primo-0.1.4/spec/spec_helper.rb:    if interaction.response.body.encoding.name == "ASCII-8BIT"
/srv/gems/celsius-primo_ubpb-0.1.4/spec/spec_helper.rb:    if interaction.response.body.encoding.name == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.0.pre/vendor/bundle/gems/rack-1.4.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.0.pre/vendor/bundle/gems/rack-1.4.1/test/spec_chunked.rb:    response.body.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/rack-1.4.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/rack-1.4.1/test/spec_chunked.rb:    response.body.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/rack-1.4.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/rack-1.4.1/test/spec_chunked.rb:    response.body.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/gems/nokogiri-1.10.4/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/gems/rack-2.0.7/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/chatops-rpc-0.0.2/fixtures/chatops-controller-example/vendor/bundle/ruby/2.5.0/gems/sass-3.7.4/lib/sass/util.rb:      elsif str.encoding.name == "ASCII-8BIT"
/srv/gems/classicCMS-0.2.3/vendor/bundle/gems/rack-1.4.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/classicCMS-0.2.3/vendor/bundle/gems/rack-1.4.1/test/spec_chunked.rb:    response.body.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/classiccms-0.7.5/vendor/bundle/gems/rack-1.4.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/classiccms-0.7.5/vendor/bundle/gems/rack-1.4.1/test/spec_chunked.rb:    response.body.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/cloudsmith-api-0.57.1/vendor/bundle/ruby/2.6.0/gems/vcr-3.0.3/lib/vcr/structs.rb:            return string.force_encoding(encoding) if encoding == "ASCII-8BIT"
/srv/gems/collmex-ruby-0.2.0/lib/collmex/request.rb:      response.body.force_encoding("ISO8859-1") if response.body.encoding.to_s == "ASCII-8BIT"
/srv/gems/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/vcr-3.0.3/lib/vcr/structs.rb:            return string.force_encoding(encoding) if encoding == "ASCII-8BIT"
/srv/gems/date_n_time_picker_activeadmin-0.1.2/vendor/bundle/ruby/2.6.0/gems/nokogiri-1.12.5-x86_64-linux/lib/nokogiri/html4/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rack-1.4.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/devise_sociable-0.1.0/vendor/bundle/gems/rack-1.5.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/domo-0.0.5/vendor/bundle/ruby/1.9.1/gems/nokogiri-1.5.0/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/eac-rack-1.1.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/edgar-rack-1.2.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/fc-webicons-0.0.4/vendor/bundle/ruby/1.9.1/gems/rack-1.4.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/fullcirclegroup-fullcirclegroup-prawn-0.2.99.2/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/fullcirclegroup-prawn-0.2.99.3/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/glebm-nokogiri-1.4.2.1/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/goodwill-prawn-edge-0.10.0/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/grape-extra_validators-2.0.0/vendor/bundle/ruby/2.6.0/gems/rack-2.0.8/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/graphael-on-rails-0.5.1/vendor/bundle/gems/rack-1.4.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/gss-0.0.7/vendor/bundle/ruby/1.9.1/gems/nokogiri-1.6.2.1-x86-mingw32/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/hallelujah-cassandra-cql-1.0.4/lib/cassandra-cql/utility.rb:        string.encoding.name == "ASCII-8BIT"
/srv/gems/hiraku-rack-1.0.0.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/horseman-0.0.5/vendor/ruby/1.9.1/gems/nokogiri-1.5.2/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/inky-rb-1.4.2.0/lib/inky.rb:      if html_string.encoding.name == "ASCII-8BIT"
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/nokogiri-1.6.7.2/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/rack-1.6.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/logstash-codec-plain-3.0.2/spec/codecs/plain_spec.rb:          insist { a.encoding.name } == "ASCII-8BIT"
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/nokogiri-1.7.0-java/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/rack-1.6.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/kavu-prawn-core-0.4.100/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/kjvarga-rack-1.0.0/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/logstash-codec-plain-3.1.0/spec/codecs/plain_spec.rb:          insist { a.encoding.name } == "ASCII-8BIT"
/srv/gems/logstash-filter-htmlentities-0.1.0/vendor/bundle/jruby/1.9/gems/rack-1.6.8/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/logstash-codec-plain-3.0.2/spec/codecs/plain_spec.rb:          insist { a.encoding.name } == "ASCII-8BIT"
/srv/gems/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/rack-1.6.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/mdg-1.0.1/vendor/bundle/ruby/2.3.0/gems/rack-1.6.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/moonrelay-0.2.1/lib/moonrelay/cli/proxy_command.rb:              if m.encoding.name == "ASCII-8BIT"
/srv/gems/mountapi-0.11.1/vendor/bundle/ruby/2.7.0/gems/rack-2.0.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/mrcooper-logstash-output-azuresearch-0.2.2/vendor/jruby/2.5.0/gems/logstash-codec-plain-3.0.6/spec/codecs/plain_spec.rb:          insist { a.encoding.name } == "ASCII-8BIT"
/srv/gems/mrcooper-logstash-output-azuresearch-0.2.2/vendor/jruby/2.5.0/gems/rack-1.6.10/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/mssql-0.0.2/lib/simple_update.in:        if value.class == String && value.encoding.to_s == "ASCII-8BIT"
/srv/gems/mssql-0.0.2/lib/simple_update.sql:        if value.class == String && value.encoding.to_s == "ASCII-8BIT"
/srv/gems/mssql-0.0.2/lib/table_output.rb:    if value.class == String && value.encoding.to_s == "ASCII-8BIT" #timestamps and binary data
/srv/gems/mumukit-content-type-1.11.1/vendor/bundle/ruby/2.6.0/gems/nokogiri-1.12.3-x86_64-linux/lib/nokogiri/html4/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/mustwin-vcr-2.9.3/lib/vcr/structs.rb:            return string.force_encoding(encoding) if encoding == "ASCII-8BIT"
/srv/gems/nokogiri-1.13.1/lib/nokogiri/html4/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/nokogiri-fitzsimmons-1.5.5.3/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/nokogiri-maglev--1.5.5.20120817130721/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/ohai-17.9.0/lib/ohai/plugins/ec2.rb:      if ec2[:userdata] && ec2[:userdata].encoding.to_s == "ASCII-8BIT"
/srv/gems/piglop-prawn-0.10.2.3/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/prawn-core-0.8.4/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/prawn-git-2.0.1/spec/document_spec.rb:    str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/puppet-7.14.0/lib/puppet/pops/types/p_binary_type.rb:      @binary_buffer = (bin.encoding.name == "ASCII-8BIT" ? bin : bin.b).freeze
/srv/gems/puppet-retrospec-1.8.0/vendor/pup410/lib/puppet/pops/types/p_binary_type.rb:      @binary_buffer = (bin.encoding.name == "ASCII-8BIT" ? bin : bin.dup.force_encoding("ASCII-8BIT")).freeze
/srv/gems/qoobaa-rack-1.0.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/rackjour-0.1.8/vendor/gems/gems/rack-1.0.1/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/railscast-assets-0.0.2/vendor/bundle/gems/backbone-forms-on-rails-0.10.0/vendor/bundle/gems/rack-1.4.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/railscast-assets-0.0.2/vendor/bundle/gems/rack-1.4.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/react_on_rails-13.0.1/lib/react_on_rails/helper.rb:          if original_url_normalized.encoding.to_s == "ASCII-8BIT"
/srv/gems/ruby-secret_service-0.0.8/lib/secret_service/secret.rb:        (str.encoding.to_s == "ASCII-8BIT"))
/srv/gems/sass-3.7.4/lib/sass/util.rb:      elsif str.encoding.name == "ASCII-8BIT"
/srv/gems/satoko-prawn-0.2.99.6/spec/document_spec.rb:      str.encoding.to_s.should == "ASCII-8BIT"
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/nokogiri-1.6.6.2/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/rack-1.6.4/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/sass-3.4.18/lib/sass/util.rb:      elsif str.encoding.name == "ASCII-8BIT"
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/sass-3.4.19/lib/sass/util.rb:      elsif str.encoding.name == "ASCII-8BIT"
/srv/gems/scout_realtime-1.0.5/lib/vendor/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/script_core-0.3.2/ext/enterprise_script_service/mruby/mrbgems/mruby-string-ext/test/numeric.rb:  if __ENCODING__ == "ASCII-8BIT"
/srv/gems/spiderfw-1.0.1/lib/spiderfw/model/base_model.rb:                        elsif val.encoding == "ASCII-8BIT" && val.is_a?(String)
/srv/gems/spiral_form-0.1.1/vendor/bundle/gems/nokogiri-1.10.4/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/spiral_form-0.1.1/vendor/bundle/gems/rack-2.0.7/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/sprokovuln-0.2.0/vendor/ruby/gems/rack-2.0.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/swipe-rails-0.0.5/vendor/bundle/gems/nokogiri-1.6.0/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/swipe-rails-0.0.5/vendor/bundle/gems/rack-1.4.5/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/symbolic_enum-1.1.5/vendor/bundle/ruby/2.7.0/gems/nokogiri-1.10.9/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/tdiary-5.2.0/vendor/bundle/ruby/2.7.0/gems/nokogiri-1.11.6-x86_64-linux/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/tdiary-5.2.0/vendor/bundle/ruby/3.0.0/gems/nokogiri-1.12.5-x86_64-linux/lib/nokogiri/html4/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/torquebox-console-0.3.0/vendor/bundle/jruby/1.9/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/vagrant-actionio-0.0.9/vendor/bundle/gems/rack-1.5.2/lib/rack/lint.rb:        input.external_encoding.name == "ASCII-8BIT"
/srv/gems/vagrant-compose-yaml-0.1.3/vendor/bundle/ruby/2.2.0/gems/nokogiri-1.6.7.1/lib/nokogiri/html/document.rb:            unless string_or_io.encoding.name == "ASCII-8BIT"
/srv/gems/vcr-6.0.0/lib/vcr/structs.rb:            return string.force_encoding(encoding) if encoding == "ASCII-8BIT"
/srv/gems/watson_tts_asr_client-0.1.0/lib/watson_tts_asr_client.rb:      service_type = data.encoding.name == "ASCII-8BIT" ? :asr_url : :tts_url
/srv/gems/watson_tts_asr_client-0.1.0/lib/websocket/client_factory.rb:      if @data.encoding.name == "ASCII-8BIT"
```

```
$ gem-codesearch "== 'ASCII-8BIT'"

/srv/gems/active_record_encoding-0.10.1/lib/active_record_encoding.rb:          value.force_encoding('UTF-8') if value.encoding.name == 'ASCII-8BIT'
/srv/gems/adyen-admin-0.0.18/spec/spec_helper.rb:    http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/angular-rails4-templates-0.4.1/vendor/ruby/2.1.0/gems/execjs-2.6.0/lib/execjs/encoding.rb:        if string.encoding.name == 'ASCII-8BIT'
/srv/gems/box_cli-0.1.1/spec/spec_helper.rb:    RUBY_VERSION == '1.8.7' || (http_message.body.encoding.name == 'ASCII-8BIT' || !http_message.body.valid_encoding?)
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.8/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/candlepin-api-0.4.0/bundle/ruby/1.9.1/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/candlepin-api-0.4.0/bundle/ruby/gems/yard-0.8.7/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/cassette_explorer-0.1.1/spec/spec_helper.rb:    http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.0.pre/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.0.pre/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/yard-0.8.3/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/challah-0.8.3/vendor/bundle/gems/yard-0.8.3/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/yard-0.8.2.1/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/yard-0.8.3/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'
/srv/gems/challah-rolls-0.2.0/vendor/bundle/gems/yard-0.8.3/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'
/srv/gems/classiccms-0.7.5/vendor/bundle/gems/execjs-1.4.0/lib/execjs/encoding.rb:          if string.encoding.name == 'ASCII-8BIT'
/srv/gems/cloudsmith-api-0.57.1/vendor/bundle/ruby/2.6.0/gems/vcr-3.0.3/features/Upgrade.md:    http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/cloudsmith-api-0.57.1/vendor/bundle/ruby/2.6.0/gems/vcr-3.0.3/features/configuration/preserve_exact_body_bytes.feature:          http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/cloudsmith-api-0.57.1/vendor/bundle/ruby/2.6.0/gems/vcr-3.0.3/lib/vcr/configuration.rb:    #       http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/coder-0.4.0/spec/coder_spec.rb:        str.encoding.name.should be == 'ASCII-8BIT'
/srv/gems/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/vcr-3.0.3/features/Upgrade.md:    http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/vcr-3.0.3/features/configuration/preserve_exact_body_bytes.feature:          http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/dadapush_client-1.0.1/vendor/bundle/ruby/2.3.0/gems/vcr-3.0.3/lib/vcr/configuration.rb:    #       http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/data_objects-0.10.17/lib/data_objects/spec/shared/encoding_spec.rb:          @values.first.encoding.name.should == 'ASCII-8BIT'
/srv/gems/data_objects-0.10.17/lib/data_objects/spec/shared/encoding_spec.rb:          @values.first.encoding.name.should == 'ASCII-8BIT'
/srv/gems/deg-yard-0.8.7.4/spec/cli/yardoc_spec.rb:      Encoding.default_internal.name.should == 'ASCII-8BIT'

/srv/gems/deg-yard-0.8.7.4/spec/cli/yardoc_spec.rb:      Encoding.default_external.name.should == 'ASCII-8BIT'

/srv/gems/doge_coin-1.0.2/lib/doge_coin/configuration.rb:          http_message.body.encoding.name == 'ASCII-8BIT' || !http_message.body.valid_encoding?
/srv/gems/einstein-2.0.1/spec/spec_helper.rb:    request.body.encoding.name == 'ASCII-8BIT' || !request.body.valid_encoding?
/srv/gems/encoding-kawai-0.5/spec/encoding-kawai_spec.rb:          str.encoding.name.should == 'ASCII-8BIT'
/srv/gems/endicia_ruby-0.2.5/spec/spec_helper.rb:    http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/execjs-2.8.1/lib/execjs/encoding.rb:        if string.encoding.name == 'ASCII-8BIT'
/srv/gems/filter_io-0.2.10/lib/filter_io.rb:      if [@buffer, data[0]].any? { |x| x.encoding.to_s == 'ASCII-8BIT' }
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/execjs-2.6.0/lib/execjs/encoding.rb:        if string.encoding.name == 'ASCII-8BIT'
/srv/gems/ish_lib_manager-0.0.1/test/dummy/vendor/bundle/ruby/2.3.0/gems/rack-protection-1.5.3/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/ivanvc-logstash-input-s3-3.1.1.4/vendor/local/gems/rack-protection-1.5.3/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/logstash-filter-htmlentities-0.1.0/vendor/bundle/jruby/1.9/gems/rack-protection-1.5.3/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/logstash-input-fifo-0.9.1/vendor/bundle/jruby/1.9/gems/rack-protection-1.5.3/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/logstash-input-salesforce-3.2.0/spec/inputs/salesforce_spec.rb:          if i.response.body.encoding.to_s == 'ASCII-8BIT'
/srv/gems/logstash-input-salesforce-3.2.0/spec/inputs/salesforce_spec.rb:          if i.response.body.encoding.to_s == 'ASCII-8BIT'
/srv/gems/logstash-input-salesforce-3.2.0/spec/inputs/salesforce_spec.rb:          if i.response.body.encoding.to_s == 'ASCII-8BIT'
/srv/gems/madad-1.0.0/spec/spec_helper.rb:    http_message.body.encoding.name == 'ASCII-8BIT' || !http_message.body.valid_encoding?
/srv/gems/marquise-0.2.0/lib/marquise.rb:			if s.encoding.to_s == 'ASCII-8BIT' or !s.force_encoding('UTF-8').valid_encoding?
/srv/gems/mdg-1.0.1/vendor/bundle/ruby/2.3.0/gems/rack-protection-1.5.3/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/mochilo-2.0/genperf.rb:encodings = Encoding.list.reject {|e| e.name == 'ASCII-8BIT'}
/srv/gems/mongo-2.17.0/spec/integration/grid_fs_bucket_spec.rb:        actual.encoding.name.should == 'ASCII-8BIT'
/srv/gems/mrcooper-logstash-output-azuresearch-0.2.2/vendor/jruby/2.5.0/gems/rack-protection-1.5.5/spec/path_traversal_spec.rb:        body.should == 'ASCII-8BIT'
/srv/gems/mumukit-content-type-1.11.1/vendor/bundle/ruby/2.6.0/gems/rouge-3.26.0/lib/rouge/lexer.rb:        return if encoding == 'US-ASCII' || encoding == 'UTF-8' || encoding == 'ASCII-8BIT'
/srv/gems/mustwin-vcr-2.9.3/features/configuration/preserve_exact_body_bytes.feature:          http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/mustwin-vcr-2.9.3/lib/vcr/configuration.rb:    #       http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/onstomp-1.0.12/spec/onstomp/connections/serializers/stomp_1_spec.rb:          ser.encoding.name.should == 'ASCII-8BIT'
/srv/gems/proxy_pac_rb-3.0.0/lib/proxy_pac_rb/encoding.rb:          if string.encoding.name == 'ASCII-8BIT'
/srv/gems/puppet-forge-server-1.10.1/lib/puppet_forge_server/utils/encoding.rb:      text.encoding.name == 'ASCII-8BIT'
/srv/gems/rhodes-7.4.1/spec/framework_spec/app/spec/core/encoding/aliases_spec.rb:      Encoding.aliases['BINARY'].should == 'ASCII-8BIT'
/srv/gems/rhodes-7.4.1/spec/framework_spec/app/spec/core/string/shared/chars.rb:      "&%".encode('ASCII-8BIT').chars.to_a.all? {|c| c.encoding.name.should == 'ASCII-8BIT'}
/srv/gems/roebe-0.5.57/lib/roebe/documentation/encoding.rb:      cmd("  if target.encoding.to_s == 'ASCII-8BIT'")+
/srv/gems/rouge-3.28.0/lib/rouge/lexer.rb:        return if encoding == 'US-ASCII' || encoding == 'UTF-8' || encoding == 'ASCII-8BIT'
/srv/gems/rpdoc-0.1.9/lib/rpdoc/postman_response.rb:        data[:body] = body.encoding.to_s == 'ASCII-8BIT' ? body.force_encoding("ISO-8859-1").encode("UTF-8") : body
/srv/gems/rubko-0.2/lib/rubko/plugins/db.rb:			if param.encoding.name == 'ASCII-8BIT'
/srv/gems/saxondale-0.0.0/lib/saxondale.rb:#           if parent && asset && asset.encoding.name == 'ASCII-8BIT'
/srv/gems/sc_core-0.0.7/test/dummy/vendor/bundle/ruby/2.2.0/gems/execjs-2.6.0/lib/execjs/encoding.rb:        if string.encoding.name == 'ASCII-8BIT'
/srv/gems/sqreen-1.25.0/lib/sqreen/rules/matcher_rule.rb:        encoded8bit = str.encoding.name == 'ASCII-8BIT'
/srv/gems/sqreen-1.25.0/lib/sqreen/safe_json.rb:      encoded8bit = str.encoding.name == 'ASCII-8BIT'
/srv/gems/tauplatform-1.0.3/spec/framework_spec/app/spec/core/encoding/aliases_spec.rb:      Encoding.aliases['BINARY'].should == 'ASCII-8BIT'
/srv/gems/tauplatform-1.0.3/spec/framework_spec/app/spec/core/string/shared/chars.rb:      "&%".encode('ASCII-8BIT').chars.to_a.all? {|c| c.encoding.name.should == 'ASCII-8BIT'}
/srv/gems/vcr-6.0.0/lib/vcr/configuration.rb:    #       http_message.body.encoding.name == 'ASCII-8BIT' ||
/srv/gems/web-connect-0.4.17/lib/netfira/web_connect/models/support/setting.rb:        if value.encoding.name == 'ASCII-8BIT'
/srv/gems/webmaster_tools-0.1.9/spec/spec_helper.rb:    http_message.body.encoding.name == 'ASCII-8BIT' ||
```


Conclusion:

* matz: It seems to introduce compatibility issues.


### [Feature #18579: Concatenation of ASCII-8BIT strings shouldn't behave differently depending on string contents](https://bugs.ruby-lang.org/issues/18579) 

```ruby
p(("abc".force_encoding("EUC-JP") + "ほげ".encode('Windows-31J')).encoding)
#=> #<Encoding:Windows-31J>
```


