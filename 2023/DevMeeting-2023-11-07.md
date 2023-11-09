# DevMeeting-2023-11-07

https://bugs.ruby-lang.org/issues/19925

## DateTime and location

* 2023/11/07 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2023/11/30 (Thu) 13:00-17:00 JST @ Online
  * We have this meeting.
  * This could practically be the last one that you could discuss something new.

----
* 2023/12/14 (Thu) is Ruby Conf Taiwan
* 2023/12/19 is NG
* Next: 2023/12/20 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

* Preview 3? -> in this week!

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #18286]](https://bugs.ruby-lang.org/issues/18286) Universal arm64/x86_84 binary built on an x86_64 machine segfaults/is killed on arm64 (nobu)

Discussion:

* mame: binary has architectural dependency, which causes SEGV.
* mame: OP proposes to drop binary generation for this combination.
* nobu: my proposal is to just skip checking the arch, which happens to work for now.
* shyouhei: the problems for dropping arch check is when the two architectures divert we have no chance to be aware of.
* mame: maybe check MD5 etc on CI?
* ko1: not against both PRs.

Conclusion:

* nobu will handle this.

### [[Feature #18915]](https://bugs.ruby-lang.org/issues/18915) Change documentation for `NotImplementedError` or introduce new exception (tenderlovemaking)

* People use `NotImplementedError` for abstract classes, but documentation says it's only for "a feature is not implemented on the current platform"
* Should we change documentation to reflect actual usage?
* Introduce a new exception for this purpose?

Discussion:

* matz: My design was that NotImplError is something different from not implemented _yet_.
* mame: how to do then?
* matz: Understand the needs though.
* nobu: Why not let users define their own one?
* matz: I guess applications want to handle this situation at one place, not 100 different rescue clause.
* matz: But `NotImplementedYetError` is a bit too close to the existing one.

Conclusion:

* matz: I would like to express my feeling on the mailing list.


### [[Feature #18980]](https://bugs.ruby-lang.org/issues/18980) Re-reconsider numbered parameters: `it` as a default block parameter (k0kubun)

* At RubyKaigi 2022, [many attendees preferred `it` over `_1`](https://youtu.be/ajm3lr6Y9yE?si=d6Sry32Zlkvtj-Nc&t=3002), [Matz did not reject `it` unlike `_` or `@`](https://youtu.be/ajm3lr6Y9yE?si=hwC4ska1wft1zuUm&t=3333), and [he said he shall think about `it` again](https://youtu.be/ajm3lr6Y9yE?si=9ibnZGD7Q6JoKyMA&t=3394). Could you think about `it` again now?
* It's been a year since the Kaigi. We've had enough time discussing `it` on the ticket.  Is there anything that needs more discussions?

Discussion:

* matz: should "`it`" be visible when there are explicit number parameters, or not?
* k0kubun: whichever works for me.
* matz: but we have to choose which.
* k0kubun: well.. then I guess it's safer to avoid mixing explict params and `it`.
* ko1: you should experiment and evaluate the impact of the incompatibility
* nobu: is the following behavior okay?

```ruby=
def foo
    yield 1, 2
end

foo do
    p it #=> [1, 2]
end
```

* k0kubun: okay

Conclusion:

* matz decides it on 30th Nov.


### [[Feature #19324]](https://bugs.ruby-lang.org/issues/19324) `Enumerator.product` => `Enumerable#product` (zverok)

* The method introduced is inconsistent with `Array#product` being an instance method, and in general is defined unlike any similar `Enumerable` or `Array` methods;
* It also has a one-letter difference from a useful method `Enumerator.produce`, that does a very different thing;
* It was just introduced in the last version, so I believe the "window of opportunity" to move the method (without breaking much code) is not closed yet.

Discussion:

* knu: I am against deleting the class method because it is intentionally a class method to be used in "all operands are equal" situations, as pointed out by Martin.
* knu: I won't object to adding an instance method version.
* mame: `Enumerable#product` could be something different from `Array#product`.
* knu: I'm not sure.  Is it okay to have an overridden method in Array that has a different return value type from that of the super method in Enumerable?  Maybe Enumerable#product should return an array just like other methods, but a`.lazy` version could be added and used here.
* mame: I'd rather say I don't like the `Array#product` behaviour...

```ruby=
[1, 2].product([3, 4])              #=> [[1, 3], [1, 4], [2, 3], [2, 4]]
[1, 2].to_enum.product([3, 4])      #=> [[1, 3], [1, 4], [2, 3], [2, 4]]
[1, 2].lazy.product([3, 4])         #=> #<Enumerator::Lazy ... >
[1, 2].lazy.product([3, 4]).force   #=> [[1, 3], [1, 4], [2, 3], [2, 4]]
```

Conclusion:

* matz: I'm fine with adding a lazy version of product in Lazy as long as the new Enumerable#product returns an array.  It's our (rather unfortunate) convention that most methods in Enumerable return an array.
* knu: will respond.

### [[Bug #19969]](https://bugs.ruby-lang.org/issues/19969) Regression of memory usage with Ruby 3.1 (hsbt)

* Revert or enable `rehash` with only large Hash?

Discussion:

* hsbt: Should we revert the whole feature, or apply nobu's patch?
* naruse: I can wait for nobu if he can fix it in time.
* nobu: Is it i hurry?
* naruse: We need to address this problem in 3.3.

Conclusion:

* nobu will refine his patch.

### [www.ruby-lang.org needs a Privacy Policy page](https://github.com/ruby/www.ruby-lang.org/issues/3134)

C.F.
  * https://proxy.golang.org/privacy
  * https://www.python.org/privacy/
  * https://foundation.rust-lang.org/policies/privacy-policy/

Discussion:

* hsbt: OP needs a privacy policy.
* hsbt: Not that bad idea to have one, ideas?

Conclusion:

* hsbt tries to write a draft and will ask devs for review.

### [[Feature #18551]](https://bugs.ruby-lang.org/issues/18551) Make `Range#reverse_each` to raise an exception if endless (kyanagi)

* I mentioned this issue for the last meeting, but it seems that it was missed.
* Since [[Feature #18515]](https://bugs.ruby-lang.org/issues/18515) has been merged, could you please make a decision on this issue?

Preliminary discussion:

*

Discussion:

* shyouhei: what exception class?
* mame: TypeError is proposed.
* shyouhei: is that optimal...?
* mame: beginless ranges already raise TypeError for `#each`.

Conclusion:

* matz: raising TypeError accepted.


### [[Feature #19370]](https://bugs.ruby-lang.org/issues/19370) Anonymous parameters for blocks? (zverok)

* I argue that we should just allow that for consistency; the resulting behavior would be not more confusing than the regular variable shadowing.

Preliminary discussion:

```ruby=
def m(*)
  ->(*) { p(*) } #=> knu: raise a SyntaxError
end

m(1).call(2)
#=> expected 2, actual 1
```

* Should we change it to 2, or prohibit `p(*)` in a block `->(*) { ... }`(, or do nothing)?

Discussion:

* shyouhei: Status quo is confusing at least.
* matz: keeping things as-is not desirable.
* knu: The confusion should be fixed, but please do not completely disable the use of anonymous parameter delegation from inside of a block; it is our primary use case for parameter delegation to wrap a method call with transaction/synchronization/instrumentation blocks.  Can we just disable it only when it is confusing?

What if we try to "shadow" outer-scope splat?

```ruby=
def m(*)
  # matz: SyntaxError because both m and the proc accept * (conflict)
  proc {|* | p(*) }
  proc {|x, * | p(*) }

  # matz: should work as Ruby 3.2
  proc { p(*) }        #=> 1
  proc {|x| p(*) }     #=> 1
  proc {|x, *r| p(*) } #=> 1
  proc {|x,| p(*) }    #=> 1
end

m(1).call(2, 3)

# --------------------------------

def m2(*)
  # matz: SyntaxError because both m and the proc accept * (conflict)
  proc {|* | proc { p(*) } } #=> 2
  proc { proc {|* | p(*) } } #=> 3
end

m2(1).call(2).call(3)

# --------------------------------

# Should we allow this?

def my_p(*a, **b, &c)
  p [a, b, c]
end

def m3
  proc {|& | proc {|** | proc {|* | my_p(*, **, &) } } }
end

m3.call {|| :blk }.call(kw: 1).call(42) #=> [[42], {:kw=>1}, #<Proc...>]

# --------------------------------

def m4(...)
  proc {|* | p(...) } #=> 1

  proc {|* | p(*) }   #=> 2?
end
        
m4(1).call(2)

```

Conclusion:

* We want:
  * to prohibit anonymous argument passing when there are multiple asterisks.  Make them onymous.
  * to prohibit anonymous arguments _of blocks_ to be passed to elsewhere.  Only method arguments are allowed.
  * to allow writing `proc { |*| ... }`; which just throws its taking arguments away.
  * to allow writing `def m(*) = @mutex.synchronize { g(*) }`; blocks without splats are okay.


### [[Bug #19983]](https://bugs.ruby-lang.org/issues/19983) Nested `*` seems incorrect (eregon)

* `def m(*); ->(*) { p(*) }; end` is not SyntaxError but uses the "wrong" `*`.
* Could we make that SyntaxError, or could we support anonymous rest in blocks?

Preliminary discussion:

*

Discussion:

* (This issue was discussed with the above one.)

Conclusion:

* This specific `def m(*); ->(*) { p(*) }; end` is a SyntxError.


### Re: happy eyeballs

* coe401: Made a state transision diagram and want you to review.
* naruse: some corner cases exist but overall seems okay.
* akr: (revirwed) v4c --"connect finished"--> v46_wait_to_resolv_or_connect seems strange.
* akr: "what causes a state transision" seems nuanced.
* coe401: I thought I could merge several states because state input/output are identical.
* akr: Yes.  Could result in a giant event loop.

----

* shioi: I want this PR to be merged: https://github.com/ruby/ruby/pull/4247
* akr: SocketError#error_code is too much. It is only defined for getaddrinfo and getnameinfo
  * introduce Socket::ResolutionError (that inherits from SocketError)
  * introduce Socket::ResolutionError#error_code


### [[Feature #19979]](https://bugs.ruby-lang.org/issues/19979) Allow methods to declare that they don't accept a block via `&nil` (ufuk)

* Can we reconsider the introduction of `&nil` as a signal to declare that a method does not accept a block?
* This would make many public APIs safer to use and would enable better static analysis than alternative approaches.

Discussion:

* matz: I want ko1's automatic detection of unused block passing, but this is also what we may have
* ko1: I will give it a try

Conclusion:

* matz: Will reply


### [[Feature #14602]](https://bugs.ruby-lang.org/issues/14602) Version of dig that raises error if a key is not present (sinsoku)

* The last comment (one before mine) was a year ago, so I think all the method name candidates have come up.
* Would you like to choose one from these candidates?

Discussion:

method name candidates:
- hash.lookup(:name, :first)
- hash.pick(:name, :first)
- hash.traverse(:name, :first)
- hash.dig_for(:name, :first)
- hash.reveal(:name, :first)
- hash.uncover(:name, :first)
- hash.unfold(:name, :first)
- hash.dive(:name, :first)
- hash.enlight(:name, :first)
- hash.scoop(:name, :first)
- hash.reel(:name, :first)
- hash.fetch_each(:name, :first)
- hash.fetch_all(:name, :first)
- hash.fetch_tail(:name, :first)
- hash.fetch_end(:name, :first)
- hash.dig_each(:name, :first)
- hash.dig_tail(:name, :first)
- hash.dig_all(:name, :first)
- hash.dig_end(:name, :first)
- hash.dig_bottom(:name, :first)
- hash.dig_deep(:name, :first)
- hash.dig_down(:name, :first)
- hash.delve(:name, :first)
- hash.drill(:name, :first) # as in drill down
- hash.shovel(:name, : first)
- hash.retrieve(:name, :first)

----

* matz: I don't like deep_* naming (but the idea)
* akr: I like fetch_* naming
* matz: Agreed, I like fetch_* or *_fetch
* akr: I want to avoid introducing a new vocabulary
* matz: Agreed
* akr: How about the combination of dig and fetch
* matz: dig_fetch or fetch_dig? I an not sure how they sound for native speakers
* akira: what's wrong with deep_*?
* matz: e.g. deep_copy means everything inside, which is something different from what is requested here (only what I want).
* matz: recursive_fetch is long?
* nobu: I expect `fetch` to accept a block for when no key is found
* mame:

```ruby=
h = { :foo => { :bar => { :baz => 42 }}}
h.dig_fetch(:foo, :baar) {|obj, key| p [obj, key] } #=> [{ :bar => { :baz => 42 } }, :baar]
```

* mame: How about this?

```ruby
h[:foo][:baar][:baz]
              ^^^^^^
h[:foo][:bar].fetch(:baaz)
```

* akira: How about this?

```ruby
h[:foo, :baar, :baz]
```

* matz: It does not sound "recursive"
* knu: `Array#[]` can take multiple arguents and has different semantics.

Conclusion:

* no conclusion


### [[Misc #19980]](https://bugs.ruby-lang.org/issues/19980) Is the Ruby 3.3 ABI frozen? (flavorjones)

* Is the Ruby 3.3 ABI frozen now? If a native gem is built against Ruby 3.3.0_preview2, is there any reason to believe that it wouldn't work with Ruby 3.3 final when it is released?
* In the past, precompiled native gems (nokogiri, sqlite3, etc.) released support for a new version of Ruby weeks after Ruby's release, slowing adoption of Ruby in some cases.
* I would like to cut a release of rake-compiler-dock as soon as possible to allow gem maintainers to release native gems that support Ruby 3.3 ahead of 3.3.0 final release. When can I do this safely?

Discussion:

* mame: basically we can prohibit changes to public header files before releasing the GA version.
* shyouhei: sounds reasonable, but ultimately up to naruse.

Conclusion:

* will ask naruse


## random tickets

### [[Feature #19987]](https://bugs.ruby-lang.org/issues/19987) add sample method to Range

### [[Bug #19986]](https://bugs.ruby-lang.org/issues/19986) Win32: `HOME` is set to just `HOMEDRIVE` if `HOMEPATH` is unset
### [[Bug #19985]](https://bugs.ruby-lang.org/issues/19985) Support `Pathname` for `require`

```
$ ruby -v --disable-gems -rpathname -e 'require Pathname.new("foo")'
ruby 3.2.2 (2023-03-30 revision e51014f9c0) [x86_64-linux]
-e:1:in `require': no implicit conversion of Pathname into String (TypeError)
        from -e:1:in `<main>'

$ ./local/bin/ruby -v --disable-gems -rpathname -e 'require Pathname.new("foo")'
ruby 3.3.0dev (2023-10-18T12:14:27Z wip-interruptible-.. f8f6523b96) [x86_64-linux]
-e:1:in `require': no implicit conversion of Pathname into String (TypeError)
        from -e:1:in `<main>'
```

```
$ ruby2.0 --disable-gems -rpathname -e 'require Pathname("./testpath"); puts $"'
:ok
enumerator.so
/opt/local/lib/ruby2.0/2.0.0/aarch64-darwin22/enc/encdb.bundle
/opt/local/lib/ruby2.0/2.0.0/aarch64-darwin22/enc/trans/transdb.bundle
/opt/local/lib/ruby2.0/2.0.0/aarch64-darwin22/pathname.bundle
/opt/local/lib/ruby2.0/2.0.0/pathname.rb
/Users/nobu/src/ruby/master/src/testpath.rb

$ ruby --disable=gems -rpathname -e 'require Pathname("./notexist"); puts $"'
-e:1:in `require': no implicit conversion of Pathname into String (TypeError)
	from -e:1:in `<main>'
```

### [[Bug #19977]](https://bugs.ruby-lang.org/issues/19977) (nil..nil) === x can raise an exception, differing from Range#cover?

* knu: This should be considered a bug; a corner case that is missed out from the last fix.  Matz already approved the behavior of (nil..nil) that it matches (`===`) any object without raising error.

Conclusion:

* Let's fix it.

### [[Bug #19916]](https://bugs.ruby-lang.org/issues/19916) `URI#to_s` can serialize to a value that doesn't deserialize to the original

```ruby=
require "uri"

url = URI("example.com")
p url          #=> #<URI::Generic example.com>
p url.to_s     #=> "example.com"
p url.host     #=> nil
p url.hostname #=> nil
p url.path     #=> "example.com"

url.scheme = "https"
p url      #=> #<URI::Generic https:example.com>
p url.to_s #=> "https:example.com"

url.hostname = "foo"
p url.to_s #=> "https://fooexample.com"
p url.to_s #=> "https://foo/example.com" # with Jeremy's patch
```

* (failed to log)
* mame: Changing the scheme leads to a broken URI object. A broken URI object may not round trip by serialize and deserialize. Right
* knu: Right. I am against fixing URI::Generic this way.  It's putting an HTTP(S) specific logic in to URI::General.  Not all URI schemes require the path component to start with the slash.
* knu: It is not appropriate to parse a path string with URI() without specifying any scheme or base URI.  If you know the piece of text is a path in the HTTPS protocol, you should say something like this:
  ```ruby
  url = URI.join("https:", "example.com") #=> #<URI::HTTPS http:/example.com>
  url.hostname = "foo"
  p url      #=> #<URI::HTTPS https://foo/example.com>
  p url.to_s #=> "https://foo/example.com"
  ```
* akr: I think it should be rejected

### [[Bug #19971]](https://bugs.ruby-lang.org/issues/19971) Confusing arity of a `Proc` with implicit rest parameter
### [[Bug #19931]](https://bugs.ruby-lang.org/issues/19931) `to_int` is not for implicit conversion?

* ko1: `to_str` is called.

```ruby=
def (obj = Object.new).to_str
  "bar"
end

p "foo" + obj #=> "foobar"
```

### [[Bug #19908]](https://bugs.ruby-lang.org/issues/19908) Update to Unicode 15.1

### [[Bug #19989]](https://bugs.ruby-lang.org/issues/19989) Fix refinement refine performance
### [[Bug #19975]](https://bugs.ruby-lang.org/issues/19975) `ISeq#to_binary` loses hidden local variable indices
### [[Bug #19970]](https://bugs.ruby-lang.org/issues/19970) Eval leaks callcache and callinfo objects on arm32 (linux)

