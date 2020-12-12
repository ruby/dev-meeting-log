---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20201210Japan

https://bugs.ruby-lang.org/issues/17346

## DateTime and location

* 12/10 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2021/01/13 (Wed) 13:00-
  * pre-reading: 2021/01/12 (Tue) 13:00-

## Announce

## About 3.0 timeframe

* preview 3 or rc1
  * Need to try removal of WEBrick 

## Check security tickets

[secret]


## 3.0 urgent tickets

### [[Feature #17371]](https://bugs.ruby-lang.org/issues/17371) Reintroduce `expr in pat` (ktsj)

 * Is it OK?

Preliminary discussion:

* The proposal:

```ruby
p(1 in a) #=> true (side effect: a = 1)
p(1 in 2) #=> false
```

Discussion:

* matz: not against it basically, but do you want it in 3.0?
* k-tsj: I want to remove experimental flag for pattern match features as a whole in 3.1.  In order to do so we need to add experimental features this month.

Conclusion:

* matz: go ahead as an experimental feature

---

Related:

* The proposal: R-assign should return the assigned value?
```ruby
1 => a  #=> 1 (return the assigned value)
```

* nobu: Re: value of `=>`, is this okay?
```ruby
1=>a # r-assign
p(1=>a) # hash: p(**{1 => a})
```

Conclusion:

* ktsj: postpone it. we can continue to discuss towards 3.1

---

* ktsj: How about allowing the typical r-assign case as an official (not experimental) feature?
```ruby=
1 => [a, b] #=> warning: experimental
1 => a      #=> no warning (only if the target is one variable)

# master: warning: One-line pattern matching is experimental,
#                  and the behavior may change in future versions of Ruby!
```

Conclusion:

* matz: go ahead 

### [[Bug #17369]](https://bugs.ruby-lang.org/issues/17369) Introduce non-blocking `Process.wait`, `Kernel.system` and related methods.

* ko1: `Process::Status.wait` has been merged. Okay?

* nobu: concerns:
    * is the name okay?
    * is it okay to change existing CAPI?
    * it can break marshal result of `Process::Status`

#### About naming
* matz: The name seems OK
* matz: It's not OK to change C API
* akr: Ideally we want a PID class.

#### About hooks
* mame: adding a hook is okay?
* nobu: It can't be helped

#### About data structure
* akr: error should not be a part of the struct. the status doesn't exist when it fails. (Also, Ruby 2.7 has no error in Process::Status.)

#### About marshalling
* nobu: This may break Marshal compatibility because it changes the representation to T_DATA?
* akr: I confirm the incompatibility: T_OBJECT -> T_TYPEDDATA

Conclusion:

* matz: In general, I accept the feature. The name `Process::Status.wait` is okay. I want nobu to review, shepherd, and re-implement (if needed)
* naruse: I'll revert if the implementation does not meet the deadline
* nobu: will do soon
* matz: naruse-san, let me know if the rc1 date is fixed

### [[Feature #17303]](https://bugs.ruby-lang.org/issues/17303) Make webrick to bundled gems or remove from stdlib (hsbt)

* duerst: Does it remain as a normal gem?
* naruse: Yes.
* a_matsuda: Rails uses capybara, and I wonder capybara can use webrick...
* naruse: Is it hard for capybara to use what rails uses (puma?)
* a_matsuda: ... or let it explictly depend on webrick.  Both are possible.
* a_matsuda: Found that rack uses webrick.  It is autoloaded.

Conclusion:

* matz: OK to remove as long as we have one more RC before the final 3.0.

* mame: nobu, please deal with un.rb that currently depends on webrick
* need to handle rubygems (-> hsbt) and rdoc (-> aycabta)

### [[Feature #17351]](https://bugs.ruby-lang.org/issues/17351) Deprecate Random::DEFAULT (eregon)

* Is it OK?

Preliminary discussion:

* ko1: can we put Class for it?
* nobu: it should be something rather than Class

* mame: Here was the original motivation of `Random::DEFAULT`

```ruby
def foo(random: Random::DEFAULT)
end
```

* ko1: it can be rewrite with.

```ruby
def foo(random: Random)
end
```

* mame: However, we cannot find this use case by gem-codesearch
* mrkn: If we need to do so, now we can use `Random` itself

* ko1: How can we deprecate it? Warn it when it is accessed?
* nobu: Use deprecate_constant

Discussion:

* akr: compatible with older versions?
* nobu: From 1.9, it is available. Only Random.seed is introduced from 3.0.

Conclusion:

* matz: go ahead.

### [[Feature #17323]](https://bugs.ruby-lang.org/issues/17323) Ractor-local storage (marcandre)

* What API to use?

Discussion:

* ko1: how about `LVar#per_ractor_value` instead of `LVar#value` ?

```ruby=
CACHE = Ractor::LVar.new{ {} }

# rejected because user expects unique 
p CACHE.value #=> {}

# new proposal
CACHE.per_ractor_value[:a] = 1

Ractor.new{
  CACHE.per_ractor_value #=> {} empty hash
}
```

```ruby=
# like Thread#[]

Ractor.current[:cache] = {:a}

r = Ractor.new do
  Ractor.current[:cache] = {:b}
end.take

p Ractor.current[:cache] #=> {:a}
```

```ruby=
# With special key instead of Symbol literals

CACHE_KEY = Symbol.new
Ractor.current[CACHE_KEY] = {:a}

r = Ractor.new do
  Ractor.current[CACHE_KEY] = {:b}
end.take

p Ractor.current[CACHE_KEY] #=> {:a}
```

```ruby=
# current avaiable way to handle Ractor local variables

p Thread.main
#=> #<Thread:0x000002992d6fca98 run>

p Ractor.new{ Thread.main }.take
#=> #<Thread:0x0000029932cb1c68 dead>

Thread.main[:key] = 1
Ractor.new{
  p Thread.main[:key] = 2 #=> 2
}.take
p Thread.main[:key]       #=> 1
```

Conclusion:

* matz: accept `Ractor#[sym]` as `Thread#[sym]`. `Symbol.new` will be discussed for Ruby 3.1.

### [[Feature #17378]](https://bugs.ruby-lang.org/issues/17378) Ractor#receive with filtering like other actor langauge (ko1)

* I could not find this simple pattern description.

Discussion:

* (very long explanation with some slides by ko1)

Conclusion:

* matz: go ahead as experimental (`Ractor.receive_if{ ... }`)

## report: rb_ext_ractor_safe(bool flag)

Now all extensions are ractor unsafe.

```ruby=

Init_foo(void)
{
  rb_ext_ractor_safe(true);

  rb_define_method(...); // ractor-safe method (can run on other Ractors)
}
```

## normal tickets

### [[Feature #16697]](https://bugs.ruby-lang.org/issues/16697) Hash.ruby2_keywords_hash?(value) should support any object (eregon)

* Yes/no?

Preliminary discussion:

```ruby
Hash.ruby2_keywords_hash?("str")
#=> expected: false, 
#=> actual: TypeError

ruby2_keywords def foo(*args)
  if args.last.is_a?(Hash) && Hash.ruby2_keywords_hash?(args.last)
  else
  end
end
```

* mame: not negative
* nobu: look good

Discussion:

* shyouhei: is `ruby2_keywords def foo(*args)` the exepcted usage?
* mame: yes.
* akr: why is this needed?
* mame: use case: https://github.com/ruby/ruby/pull/2966/files#r394741112
* nobu: I don't bother this behaviour.
* mame: I cannot understand the use case, but I'm okay for the change. I don't know if this is backported to 2.7 or not
* mame: ah, if 2.7 and 3.0 have different semantics of Hash.ruby2_keywords_hash?, it may bring another trouble. Maybe we should ask nagachika-san if he backport this change to 2.7 or not
* mame: umm. I'm now afraid if this change brings new confusion. It is trivial to workaround the issue.

Conclusion:

* mame: I'll ask nagachika-san


### [[Feature #17055]](https://bugs.ruby-lang.org/issues/17055) Allow suppressing uninitialized instance variable and method redefined verbose mode warnings (jeremyevans0)

* Can we completely remove uninitialized instance variable warnings?  Then we don't need this feature. I would very much like that in Ruby 3.

Preliminary discussion:

* mame: Agreed
* ko1: It can speed up optcarrot a bit.

Discussion:

* nobu: I'm unsure if the warning is helpful. It might be, but I can't remember
* ko1: thought it was dangerous but on a second thought I now think we can give it a try.
* a_matsuda: I don't think anybody complains about it immediately (people will not be aware of this if warnings disappear).
* a_matsuda: I don't remember any typo saved by this though.
* a_matsuda: Maybe rubocop remains as-is so ruby side warnings might not be that important.

Conclusion:

* matz: go ahead


### [[Bug #17280]](https://bugs.ruby-lang.org/issues/17280) Dir.glob with FNM_DOTMATCH matches ".." and "." and results in duplicated entries (jeremyevans0)

* Do we want to change the behavior to exclude ".." and duplicate results?
* If so, is the pull request acceptable?

Preliminary discussion:

* The following seems to have duplicated entries like "bar" and "bar/.".
* This is because the recursive match (`**/`) removes `..` but not `.`
  * remove `/.` too?
```ruby
% ruby -e 'p Dir.glob("**/*", File::FNM_DOTMATCH)'
[".", "bar", "bar/.", "bar/.baz", "bar/.baz/.", "bar/.baz/qux"]
```

* The following ".." seems unexpected?
* This is because `**` is not a recursive match
  * `**` is equal to `*`, which is a bit confusing. A warning will solve the issue?
```ruby
% ruby -e 'p Dir.glob("**", File::FNM_DOTMATCH)' 
[".", "..", "bar"]
```

* This is expected?
```ruby
% ruby -e 'p Dir.glob("*", File::FNM_DOTMATCH)' 
[".", "..", "bar"]
```

* mame: I think we can postpone it to Ruby 3.1. Any hasty change looks unreasonably risky to me.

Discussion:

* akr: Is "." returned or not? 
* nobu: zsh seems to have similar behaviour.
* matz: "don't repeat a file twice" doesn't sound a bad thing to me.
* akr: what is "a file"? it's unclear.  "bar" and "bar/." is the same (hard-linked) file.  If we specify Dir.glob don't repeat hard-linked files, hard-linked files other than "." and ".." will also be unappeared.  Another specification is "don't return . and .." (like zsh).  However it changes Dir.glob doesn't return ".", "." is not repeated, though.
* nobu: `/.` is in fact a hard link.
* znz: it seems zsh special-cases `.` and `..` regardless of `GLOB_DOTS`.

Conclusion:

* matz: try to remove "." and ".." on 3.1 (early timing to check compatibility issues)

### [[Bug #17162]](https://bugs.ruby-lang.org/issues/17162) Dir['**/*'] : stack smashing detected when listing big amount of directories (jeremyevans0)

* Do we want to add an internal recursion limit to try to prevent this?

Preliminary discussion:

* nobu: The issue is not Dir.[] but stack overflow detection bug.
* mame: If so, the limitation is not needed

Discussion:

* ko1: it is impossible to detect machine stack overflow.

Conclusion:

* mame: let's postpone it to 3.1
* matz: agreed


### [[Bug #17218]](https://bugs.ruby-lang.org/issues/17218) Range#step sometimes behaves unexpectedly with Rational endpoints and increment (jeremyevans0)

* This is caused by calling `rb_int_*` functions directly instead of Ruby methods.
* Do we want to switch `arith_seq_last` to using Ruby methods, and if so, should all of the `rb_int_*` be switched, or just the minimal number?
* The pull request only changes three of the `rb_int_*` to `rb_funcall`, is it OK?

Preliminary discussion:

* mrkn: will look

Conclusion:

* mkrn: already fixed


### [[Bug #13663]](https://bugs.ruby-lang.org/issues/13663) `String#upto` doesn't work as expected (jeremyevans0)

* I think the current behavior of `String#upto` in this case is expected and not a bug.
* Is it OK to close this?

Preliminary discussion:

```ruby
'x'.upto('ac').to_a #=> []
# expectation: ["x", "y", "z", "aa", "ab", "ac"]
```

* mame: agreed with closing

Conclusion:

* matz: ok to close.

### [[Feature #17342]](https://bugs.ruby-lang.org/issues/17342) Add Hash#fetch_set (maxlap)

* Yes/no?

Preliminary discussion:

* mame: I think the focus is whether we want to use false or nil for this code pattern
* nobu: I don't see any reason why this is built-in

```ruby
cache[key] ||= initalization

class Hash
  def fetch_set(key)
    if self.key?(key)
      self[key]
    else
      if block_given?
        self[key] = yield
      else
        raise ...
      end
    end
  end
end

CACHE = {}
def memo_foo(*args)
  CACHE[args] ||= begin
    ... # mame doesn't like indent here (but this proposal is irrelevant)
  end
end

CACHE = {}
def memo_foo(*args)
  return CACHE[args] if CACHE.has_key?(args)
  ...
  return CACHE[args] = ret
end

cache.fetch_set(key) { initialization }

cache.fetch(key) { cache[key] = initialization }

# rejected
cache.fetch(key, set: true) { initialization }

store.fetch_or_set(key) { initialization }
store.init(key) { initialization }
store.cache(key) { initialization }
store.memoize(key) { initialization }
```

Discussion:

```
zsh % ./miniruby --dump=i -e'{}[k]||=v'
== disasm: #<ISeq:<main>@-e:1 (1,0)-(1,9)> (catch: FALSE)
0000 putnil                                                           (   1)[Li]
0001 newhash                                0
0003 putself
0004 opt_send_without_block                 <calldata!mid:k, argc:0, FCALL|VCALL|ARGS_SIMPLE>
0006 dupn                                   2
0008 opt_aref                               <calldata!mid:[], argc:1, ARGS_SIMPLE>
0010 dup
0011 branchif                               23
0013 pop
0014 putself
0015 opt_send_without_block                 <calldata!mid:v, argc:0, FCALL|VCALL|ARGS_SIMPLE>
0017 setn                                   3
0019 opt_aset                               <calldata!mid:[]=, argc:2, ARGS_SIMPLE>
0021 pop
0022 leave
0023 setn                                   3
0025 adjuststack                            3
0027 leave
```

Conclusion:

* matz: The use case looks artificial. And I'm not fully satisfied the name `fetch_or_set`. I reject `init` `cache` `memoise`.


### [[Feature #17361]](https://bugs.ruby-lang.org/issues/17361) lambda(&block) does not warn with lazy proc allocation (znz)

* optimization affects warnings

Preliminary discussion:

* ko1: will do

```ruby=
block = proc {p 12}
lambda(&block) #=> warning
#=> warning: lambda without a literal block is deprecated; use the proc without lambda instead

it = foo{p 12}
it.call #=> 12

```

```ruby=
def foo &block
  lambda(&block) #=> no warning (bug? it should print a warning)
end

it = foo{p 12}
it.call #=> 12
```

Conclusion:

* ko1: This is a bug, let me fix.


### [[Feature #17314]](https://bugs.ruby-lang.org/issues/17314) Method declarations (marcandre)

* Have `Module#protected`, `private`, `public` accept a single Array argument of symbols (and also `private_class_method/public_class_method` for consistency)
* Have `Module#attr_reader`, `attr_writer`, `attr_accessor` return an array of the method newly defined methods instead of `nil`
* Have `Module#alias_method` return the symbol of the method defined instead of the receiver
* Notes: All points are related but not dependant and are requested separately. `Forwardable#def_delegators` and `ActiveSupport`'s `delegate` already return array of symbols

Preliminary discussion:

```ruby=
private [:foo, :bar] # Allow this
attr_accessor :foo, :bar #=> [:foo, :foo=, :bar, :bar=] instead of nil

private attr_accessor :foo, :bar
```

```
$ ruby2.2 -w -e 'class X; private; attr :foo; end'
-e:1: warning: private attribute?
$ ruby2.3 -w -e 'class X; private; attr :foo; end'
```
* nobu: how about `private *attr_accessor :foo`
* mame: parentheses are required
* nobu: What about `alias`?

Conclusion:

* matz: no idea... okay, accepted

### [[Feature #17363]](https://bugs.ruby-lang.org/issues/17363) Timeouts (marcandre)

* Add `timeout` parameter to `Queue.pop`, `Ractor.receive`, ...
* And/or `Timeout::wake` that would make these operations safer by raising only if thread is asleep (and presumably in a safe state)

Preliminary discussion:

* ko1: I'll add timeout to Ractor.receive, yield, take, and select. Will do by 3.0.
* ko1: I have no opinion about Queue.pop
* ko1: `Timeout.wake` should be discussed towards 3.1

Discussion:

* ko1: I'd like to confirm: `queue.pop(timeout: 0)` always fails?
* mame: Doesn't `queue.pop(timeout: 0)` return a value if any value is ready to pop?
* ko1: Unsure.
* akr: The behavior must be explicitly written in rdoc
* ko1: What's happen when timeout? I thought that it returns nil
* mame: doesn't it raise an exception? Unless, it cannot distinguish nil's meaning like the issue #17357 
* akr: raising an exception is slow but returning nil is ambiguous.
* mame: Then, will `exception: false` be also wanted?
* ko1: It looks difficult to decide it by Ruby 3.0

Conclusion:

* ko1: will reply


### [[Feature #17357]](https://bugs.ruby-lang.org/issues/17357) `Queue#pop` should have a block form for clsoed queues (marcandre)

* Adding block form similar to `fetch` ok?

Preliminary discussion:

* mame: I'm unsure if this is really needed. We can easily work around, for example, by wrapping the value by an array. I'm not against, though.
* ko1: no objection.

Discussion:

* nobu: The same discussion was done when introducing `Queue#close`
* naruse: Unsure about the use case

Conclusion:

* naruse: postpone it to 3.1


### [[Feature #15975]](https://bugs.ruby-lang.org/issues/15975) Add `Array#pluck`/`Enumerable#pluck` method (connorshea)

- It pulls values out of an array of hashes (e.g. array of user hashes, `users.pluck(:username)` gets all their usernames)
- Please reconsider adding the method. I've added a comment to the issue with an example of when it'd be useful for me.
- Is matz opposed to the method existing at all, or just the name?

Preliminary discussion:

* mrkn: Use activesupport.

```ruby
[{:foo => 1, :bar => 2}, {:foo => 3, :bar => 4}].pluck(:foo)   #=> [1, 3]
[{:foo => 1, :bar => 2}, {:foo => 3, :bar => 4}].map{_1[:foo]} #=> [1, 3]
```

Conclusion:

* matz: agreed with rejection


### [[Feature #12650]](https://bugs.ruby-lang.org/issues/12650) Use UTF-8 encoding for ENV on Windows (larskanis)

* See this with the next topic together

Preliminary discussion:

* mame: already merged

Conclusion:

* naruse: okay


### [[Feature #16604]](https://bugs.ruby-lang.org/issues/16604) Set default for Encoding.default_external to UTF-8 on Windows (larskanis)

* Both have been postponed to ruby-3.0 years ago
* `default_external = UTF-8` is already de facto standard

Preliminary discussion:

* mame: already merged

Conclusion:

* naruse: okay


### [[Feature #17365]](https://bugs.ruby-lang.org/issues/17365) Adding `channel` to Ractor push api (marcandre)

* yes/no?

Preliminary discussion:

* ko1: I have counter proposal to add a pattarn-matching Ractor receive

Conclusion:

* ko1: let's discuss this in #17378

### [[Bug #15661]](https://bugs.ruby-lang.org/issues/15661) Disallow concurrent Dir.chdir with block (jonathanhefner)

* This change is causing some issues in Rails (e.g. https://github.com/rails/rails/commit/ae5ecfe26c8 and https://github.com/rails/rails/issues/40756).
* I have proposed an "escape hatch" mechanism in https://bugs.ruby-lang.org/issues/15661#note-15.  Summary: wait until the end of the initial `chdir` block to check and raise error.
* Can this be addressed for Ruby 3.0?

Preliminary discussion:

* nobu: I think it is good to raise an exception only when it is called in a different thread
* mame: I'm unsure if the escape hatch is okay. We cannot detect if pwd is unintentionally changed (especially, by a third-party library) in a block chdir.

Discussion:

* nobu's proposal: https://github.com/ruby/ruby/pull/3861/files

```ruby=
# Rakefile
task :foo do
  Dir.chdir("somedir")
end

# The implementation of Rakefile
def task(name)
  Dir.chdir("aaa") do
    yield
  end
end

# We need to change the code above to:
def task(name)
  pwd = Dir.pwd
  Dir.chdir("aaa")
  yield
ensure
  Dir.chdir(pwd)
end

# By this proposal
def task(name)
  Dir.chdir("aaa") do
    yield
    Dir.chdir("aaa")
  end
end
```

```ruby=
# The warning makes sense even in single-thread program
Dir.chdir("aaa") do
  third_party_method # may call Dir.chdir("bbb")
  # cwd is changed; it is unintentional
end
```

* ko1: How about raising an exception if different thread calls Dir.chdir, and showing a warning if the same thread calls Dir.chdir
* When Thread A is running `Dir.chdir("aaa") do ... end`
  * Thread B attempts to run `Dir.chdir("bbb")`         #=> an exception
  * Thread B attempts to run `Dir.chdir("bbb") { ... }` #=> an exception
  * Thread A attempts to run `Dir.chdir("bbb")`         #=> a warning
  * Thread A attempts to run `Dir.chdir("bbb") { ... }` #=> no warning

```ruby=
Dir.chdir("aaa") do
  Thread.new do
    Dir.chdir("bbb")    # exception
    Dir.chdir("bbb") {} # exception
  end
end

Dir.chdir("aaa") do
  Dir.chdir("bbb") # warning
end

Dir.chdir("aaa") do
  Dir.chdir("bbb") do # ok
  end
end
```

Conclusion:

* matz: go ahead
