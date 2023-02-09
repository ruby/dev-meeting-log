---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2023-02-09

https://bugs.ruby-lang.org/issues/19357

## DateTime and location

* 2023/02/09 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/03/09 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

* 3.2.1 released

## Check security tickets

[secret]

## Ordinary tickets


### [[Feature #19351]](https://bugs.ruby-lang.org/issues/19351) Promote bundled gems at Ruby 3.3 (hsbt)

* How about the list of first proposal?
* Do you have opinion for "Additional Works"?

Preliminary discussion:

* Goal: stop mirroring the source code of default gems
  * no need to manually sync
  * prevent from releaseing a wrong version that is not ofically released
  * (the development experiment of bundled gems is not so good, but one of default gems is also not so good :-)
* Note
  * Requiring users to modify Gemfile is too heavy
  * we need to keep some gems that are needed for build as default gems (e.g., un.rb)
  * we may need to keep too ones that rubygems is using (e.g., net/http?)

Conclusion:

*

### [[Feature #19420]](https://bugs.ruby-lang.org/issues/19420) Simplify MJIT implementation (k0kubun)

* Is it okay to move forward with that idea? I'd like to get early feedback before going too far with it.

Discussion:

* k0kubun: I want to ask if it is okay in terms of VM development and the new name RJIT (or something)
* ko1: as the VM developer, I am okay
* matz: The name RJIT is acceptable
* ko1: MJIT had better portability because of C compilers. Can we leave this advantage? (maybe no problem, though)
  * naruse: it's acceptable because YJIT is already available on production platforms

Conclusion:

* matz: it is okay. I leave it to k0kubun if and when you merge it

### [[Feature #18368]](https://bugs.ruby-lang.org/issues/18368) `Range#step` semantics for non-`Numeric` ranges (zverok)

* It seems that nobody is _in principle_ against it, Matz's doubts are addressed with detailed explanations.

Preliminary discussion:

```ruby
# Desirable behavior:

(timestamp1...timestamp2).step(3.hours) { ... }

# Undesirable behavior:

([]..).step([1]).take(3)        #=> [[], [1], [1, 1]]
(Set[1]..).step(Set[2]).take(3) #=> [Set[1], Set[1, 2], Set[1,2]]

# Keep as is (changing it to TypeError is unacceptable)

("a"..).step(3).first(5) #=> ["a", "d", "g"]

# Do not

(""..).step("a").take(3) #=> ["", "a", "aa"]
```

Discussion:

* akr: How about change the mode depending whether the range element responds to `succ`

```ruby
# if it responds to succ, succ is used (possible optimized by not using succ actually)
(0..).step(3).take(3)          #=> [0, 3, 6]
("a"..).step(3).take(3)        #=> ["a", "d", "g"]
(Date.today..).step(3).take(3) #=> [2023-02-09, 2023-02-12, 2023-02-15]

# if it does not respond to succ, + is used
(Time.now..).step(1.hour).take(3) #=> [2023-02-09 14:00:00, 2023-02-09 17:00:00, 2023-02-09 20:00:00]
([]..).step([1]).take(3)          #=> [[], [1], [1, 1]]
(Set[1]..).step(Set[2]).take(3)   #=> [Set[1], Set[1, 2], Set[1,2]]
```

Conclusion:

* matz: let's ask akr's proposal is good for OPs


### [[Feature #19061]](https://bugs.ruby-lang.org/issues/19061) Proposal: make a concept of "consuming enumerator" explicit (zverok)

* Questions are addressed, is it possible to move forward with it?

Preliminary discussion:

```ruby
# t.txt:
# 111
# 222
# 333

f = File.open("t.txt")
g = f.each

p g.consuming? #=> true because it is able to rewind

p g.next #=> "111\n"
p g.next #=> "222\n"
g.rewind
p g.next #=> "111\n"
p g.next #=> "222\n"
```

```ruby
g = $stdin.each

p g.consuming? #=> false because it is not able to rewind

p g.next #=> "111\n"
p g.next #=> "222\n"
g.rewind #=> Illegal seek @ rb_io_rewind
```

```ruby!
# original PoC
e1 = (1..5).to_enum
e2 = e1.consuming

p e1.next #=> 1
p e2.next #=> 2 (is this okay?)
```

```ruby!
# revised PoC
e = (1..5).to_enum

p e.next      #=> 1
p e.consuming #=> can't copy execution context (TypeError)
```

Discussion:

* akr: In some platforms, tty is not seekable but the OS wrongly answers that it is seekable
* nobu: in macOS, `$stdin.rewind` in irb does not raise an exception
* matz: I am not positive because I am afraid that there are many corner cases
* knu: it is very difficult to determine that it is not consuming

Conclusion:

* matz: I will reject. I will reply


### [[Feature #19272]](https://bugs.ruby-lang.org/issues/19272) `Hash#merge`: smarter protocol depending on passed block arity (zverok)

* A novel proposal to use block arity analysis for more friendly API, allowing `hash1.merge(hash2, &:+)` without losing backward compatibility

Preliminary discussion:

```ruby!
# current
{apples: 1, oranges: 2}.merge(apples: 3, bananas: 5) { |_, o, n| o + n }

# proposed (when the arity is two)
{apples: 1, oranges: 2}.merge(apples: 3, bananas: 5) { |o, n| o + n }
{apples: 1, oranges: 2}.merge(apples: 3, bananas: 5, &:+)
```

Conclusion:

* matz: rejected


### [[Misc #19304]](https://bugs.ruby-lang.org/issues/19304) `Kernel` vs `Object` documentation (zverok)

* The question is probably only documentation-related, but I believe it is important to the general understanding of Ruby and would like to hear the core team consensus

Preliminary discussion:

* current status: rdoc has a hack to implicitly convert some Kernel methods to Object ones
  * Methods that is defined by rb_define_global_function -> Kernel
  * Others -> Object

* problem: All methods that are defined within kernel.rb are not converted

* Option 1: Stop the rdoc hack, and move the method definitions from Kernel to Object
* Option 2: Stop the rdoc hack, and show all methods as Object ones (a bit confusing)
* Option 3: Keep the rdoc hack, and implement the same hack for kernel.rb
* Option 4: Revamp rdoc and show public methods and private ones in different lists

Discussion:

* akr: I guess the RDoc hack try to distinguish module functions and other methods. I like Option 4 to distinguish them for each class/module and remove the hack. RDoc should print all the methods defined in Kernel for document Kernel, and separate them to module_function ones and non-module_function ones.
* mame: this is another issue, but currently rdoc doesn't handle module functions at all. For example, Math.acos is categorized as "Public Class Methods"
* ko1: If all documents in Object are moved to Kernel (even as module functions), it will surprise users
* matz: I don't like Option 1. I don't want to change the implementation for documents.
* matz: Personally I am okay if the rdoc does not distinguish between fork-like methods and is_a?-like methods.

Conclusion:

* based on Option 4
* stop the hack; move all methods in Object documentation should be move to Kernel's
* make rdoc handle module function correctly
* fork-like methods should be documented as module function in Kernel, and is_a?-like methods should be as non-module function in Kernel

### [[Feature #19324]](https://bugs.ruby-lang.org/issues/19324) `Enumerator.product` => `Enumerable#product` (zverok)

* For consistency with other methods "promoted" from Array
* The question of necessity of "standalone" method concept was raised, too

Discussion:

* knu: There was already Array#product that operates on arrays and returns an array, hence a new module method.  Besides the name clash, all operands are equal in the product operation and I had use cases in mind where an indefinite number of enumerable are used, that's why I thought it is reasonable to have it as a module method. (`Enumerator.product(xs, ys, zs)` rather than `xs.product(ys, zs)`, `variations = [colors, sizes, materials, …]; Enumerator.product(*variations)`)
* matz: Array#product requires all arguments to be Array
* matz: Enumerator.product is a bit more flexible; it accepts other classes
* matz: This is my reason why the former is an instance method and the latter is a class method

Conclusion:

* knu: I will reply


### [[Bug #11230]](https://bugs.ruby-lang.org/issues/11230) Should `rb_struct_s_members()` be public API? (jeremyevans0)

* This C function was previous decided to be removed from the public API after Ruby 3.2.
* The function is still exported as it is needed by `marshal.c`.
* One stated reason for removal is that `rb_struct_s_members` returns a hidden Array. `rb_struct_members` appears to return the same Array.
* Should we consider removing `rb_struct_members` from the public API as well?

Discussion:

* nobu: Unfortunately the usage of the functions is not warned

```
$ gem-codesearch rb_struct_s_members | sort
...
2023-02-01 /srv/gems/oj-3.14.1/ext/oj/custom.c:        ma   = rb_struct_s_members(clas);
2023-02-01 /srv/gems/oj-3.14.1/ext/oj/dump_object.c:        VALUE       ma = rb_struct_s_members(clas);
2023-02-01 /srv/gems/oj-3.14.1/ext/oj/rails.c:    ma = rb_struct_s_members(rb_obj_class(obj));
```

* mame: At that time we decided to remove the method, there is no pratical usage
* mame: However, unfortunately, now oj uses it.
* naruse: It is impossible to remove it
* nobu: I don't see why we need to remove it

Conclusion:

* keep it as is unless there is special reason why we need to remove it

### [[Feature #19347]](https://bugs.ruby-lang.org/issues/19347) Add `Dir.fchdir` (jeremyevans0)

* This was discussed last developer meeting, with 4 possible approaches.
* I've given comments for each approach. Can we decide on an approach?

Discussion:

* 1. `Dir.fchdir(int)`
* 2. `Dir.chdir(int)`
* 3. `dir = Dir.for_fd(int); dir.chdir`
* 4. `IO#fchdir`

---

* matz: To manage a directory (e.g. get entries), `Dir` instance is needed. So how about idea 3?
* nobu: `Dir.for_fd(int)` -- fdopendir
* nobu: `Dir#chdir` -- fchdir

Conclusion:

* `dir = Dir.for_fd(int); dir.chdir` 

### [[Feature #19388]](https://bugs.ruby-lang.org/issues/19388) Deprecate `SecurityError` (jeremyevans0)

* `SecurityError` was used historically for `$SAFE` violations. It hasn't been used since Ruby 3.0.
* I think we should deprecate `SecurityError` in Ruby 3.3 and remove it Ruby 3.4.  Is this OK?

Discussion:

```
re.c:        rb_raise(rb_eSecurityError, "can't modify literal regexp");
```

* mame: It looks like this was a typo!
  * https://github.com/ruby/ruby/commit/9b383bd6cf96e1fe21c41528dec1f3ed508f335b#diff-c3675fa319803b2f5a775defa40694edb9a761baa3a54fa78e1fdef8f918cc7cR1447

```
ruby.c:        rb_raise(rb_eSecurityError, "no %s allowed while running setuid", s);
ruby.c:        rb_raise(rb_eSecurityError, "no %s allowed while running setgid", s);
```

* mame: These usages are not related to `$SAFE`
* mame: Some gems may be using SecurityError for non-`$SAFE` usages
* matz: It is a pain to prohibit users from using it for their own purposes. Keep it

Conclusion:

* matz: will reply

### [[Feature #19422]](https://bugs.ruby-lang.org/issues/19422) Make `--enabled-shared` mandatory on macOS (nobu)

* From the troubles around linker on macOS, I propose --enable-shared option mandatory on macOS.
* This patch enables the option by default, and abort if --disable-shared option is given explicitly.

Preliminary discussion:

Discussion:
* naruse
  * dynamic_lookupがXcode 14で警告が出る
  * 別の拡張ライブラリのシンボルを参照
    * RTLD_GLOBALをつけていてもdynamic_lookupがないとtwo-level name spaceだと見えなくなる
  * flat_namespaceで他のシンボルと衝突 (rbsの場合はOSのシンボルと当たっていたからrbsが悪い?)
    * これが問題となる場合、どちらにせよ他のOSで問題になるのでは?
  * --enable-sharedなrubyが、--disable-sharedなライブラリを読めない
  という4つの問題があり、すべてdynamic_lookupをやめたのに起因する
  * macOSのchained fixupsに悩まされて勉強しているんだけれど、DLLを読み込むときにセグメントごとにrelocation(rebase)とシンボル解決(bind)ができるのがよいのか。その代わり起動時のシンボル解決がワンパスになるので変なこと (-undefined dynamic_lookup) すると死ぬ恐れがあると



Conclusion:

*

## memo

mame: https://c-ares.org/
