# DevMeeting-2026-03-17

https://bugs.ruby-lang.org/issues/21877

## DateTime and location

* 2026/03/17 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2026/04/21 (Tue) 15:00-17:00 JST, in-person @ Hakodate
    * next next: 2026/05/13 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21869]](https://bugs.ruby-lang.org/issues/21869) Add receive_all Method to Ractor API for Message Batching (synacker)

* This feature is essential for high‑load services because message batching is a highly efficient technique for reducing the number of expensive I/O requests and minimizing context switches between Ractors and business logic
* The feature opens the way to vectorized message handlers, enabling batch processing of messages in a single handler invocation.
* The proposal has received positive feedback from the community on GitHub, Reddit, and Discord
* I have created a PR on GitHub: https://github.com/ruby/ruby/pull/16105

#### Conclusion:

* ko1: I will handle this

### [[Feature #21875]](https://bugs.ruby-lang.org/issues/21875) Handling of trailing commas in lambda parameters (earlopain)

* Followup to Feature #19107 (Allow trailing comma in method signature)
* During implementation I noticed that lambdas in the style of `->()` never came up
* In addition to the ticket description, @eregon created a good comparison in https://bugs.ruby-lang.org/issues/21875#note-2
* There are a few ways that a trailing comma could be handled but I think it would be best to just reject it. It's confusing and I don't think there is a use-case (if one comes up, we can reconsider).

#### Conclusion:

* matz:
   * `->(x,) {}` should be prohibited. I don't see any reason to change this.
   * `do |x, y,|` is currently allowed, and keep it as is.
   * `do |x, y, *opt,|` is currently prohibited, and keep it as is.
   * `do |x, y, kw: 1,|` is currently prohibited, and keep it as is.

### [[Feature #21942]](https://bugs.ruby-lang.org/issues/21942) Allow reading class variables (`@@foo`) from non-main Ractors (tenderlovemaking)

* Currently `@foo` is allowed to be read, but `@@foo` isn't allowed
* Rails uses `@@foo` so I'd like to make them allowed

#### Discussion:

* ko1: not sure if we should endorse class variables any more.

#### Conclusion:

* matz: I leave it to ko1

### [[Bug #21870]](https://bugs.ruby-lang.org/issues/21870) Regexp: Warnings when using slightly overlapping `\p{...}` classes (jneen)

* Warning spam on code that definitely isn't a mistake (`/[\p{Word}\p{S}]/` and other overlapping properties)
* Noted some possible ways forward in https://bugs.ruby-lang.org/issues/21870#note-20, including an easy path that I'm happy to take on
* It looks like this was discussed in DevMeeting 2026-02-12 (https://github.com/ruby/dev-meeting-log/blob/master/2026/DevMeeting-2026-02-12.md, near the bottom), unclear if any decision was made
* Could use some clarity on the best path forward, both short-term and long-term

#### Discussion:

* shyouhei:  why do we need to warn this to begin with?
* matz: seems the warning is archaic
* mame: excavated old issue that introduced this warning: https://bugs.ruby-lang.org/issues/1831
* mame: I find it nice to detect duplicated punctuation(s) in `/[!&abcde...xyz!]/` (but I have no strong opinion)
* mame: `/[aA]/i` shows no warning
* akr: is it possible to warn `[!&a-z!]`, but not `[\p{Word}\p{S}]`  ?

(... nobu, matz, akr discusses implementation ...)

* matz: possibie, technically.  Question whether it's worth though.

#### Conclusion:

* matz: I will reply


### [[Bug #21685]](https://bugs.ruby-lang.org/issues/21685) Unnecessary context-switching, especially bad on multi-core machines. (jpl-coconut) (jpl-coconut)

* Ruby exhibits excessive context switching around fast syscalls, which compromises performance. Microbenchmarks (linked from issue and PR) show the impact in isolation.
* A small (+68 LOC) [PR fixes the issue.](https://github.com/ruby/ruby/pull/15840) Microbenchmark results are included. Two production rails apps (shopify and github) see ~10% overall perf improvements with the fix, which seem likely to generalize to others.
* How to move this forward?
* How to better facilitate future contributions/discussions related to concurrency/perf, where tradeoffs might be more subtle.

#### Discussion:

* ko1: this discussion tries to improve thread scheduling so that short lived rb_thread_call_without_gvl() can gain GVL immediately
* ko1: the patch is small.
* ko1: re: how to better facilitate future contributions/discussions, we currently lack benchmark enough to tell trade offs.

#### Conclusion:

* ko1: I will handle this


### [[Feature #21861]](https://bugs.ruby-lang.org/issues/21861) C API: expose `ruby_xfree_sized`, `ruby_xrealloc_sized`, etc (byroot)

* C23 added `void free_sized(void *ptr, size_t old_size)`.
* It has both speed and correctness benefits.
* Several common allocators already support it, including latest `glibc` and the very popular `jemalloc`.
* Maps well with `ruby_sized_xfree` used internally to provide the freed size to GC statistics.
* I'd like to expose `ruby_xfree_sized` and `ruby_xrealloc_sized` to C extensions so they can make use of it too.
* I'd also like to expose `RB_FREE_SIZED`, `RB_FREE_SIZED_N` and `RB_REALLOC_SIZED_N` macros for convenience.

#### Discussion:

* shyouhei: C23 finally adds this to the canon.
* ko1: How many platforms support C23 BTW?  It seems Ubuntu 24.04 not yet supports this.
* shyouhei: This is not something mandatory though.
* mame: ruby currently has `ruby_sized_xfree` but not `ruby_xfree_sized`.  Naming? I am ok either
* akr: what should `RB_REALLOC_SIZED_N` do is not obvious from its name.
* ko1: until now `ruby_sized_xfree`'s size parameter was merely a hint; no assertions are attempted so far.  Do we align this to C23 ("the result is undefined" when size is wrong) ?
* mame: the size parameter is actually checked on RUBY_DEBUG today.

#### Conclusion:

* ko1: ok. I like C's name `ruby_xfree_sized`
* akr: need to make sure  the naming

### [[Feature #21853]](https://bugs.ruby-lang.org/issues/21853) Make Embedded TypedData a public API (byroot)

* `RUBY_TYPED_EMBEDDABLE` was introduced in 3.3 as private API which help reduce memory usage and sped up numerous core types.
* I'd like to expose this capability to C extensions, so they can get the same benefits.
* I think the API is currently fine, but we can make change before making it public if desired.

#### Discussion:

* ko1: I think it is good
* ko1: need an explanation in doc/extension.rdoc
* byroot: I can prepare it
* ko1: after 2 years of experiments it seems no flaws are found.

#### Conclusion:

* matz: I have no strong opinion
* ko1: go ahead

### [[Feature #21852]](https://bugs.ruby-lang.org/issues/21852) New improved allocator function interface (byroot)

* The current `typedef VALUE (*rb_alloc_func_t)(VALUE klass);` interface is too limited for Ractor and Embedded Typed Data
* When duping or cloning some objects, the allocator do need to receive the source object to allocate in the correct size pool.
* I propose to introduce `typedef VALUE (*rb_copy_alloc_func_t)(VALUE klass, VALUE other);`
* I have a PR that does so without breaking backward compatibility: https://github.com/ruby/ruby/pull/15795
* I believe @ko1 would like to introduce more change for Ractors.

#### Discussion:

* nobu:  It's an allocator for copy constructor.
* ko1: not actually.  it's intended for dup and clone.
* ko1:  allocate and copy_alloc are mutually exclusive;  if `other` is missing pass `Qundef`.
* ko1: real application usage is allocation of backtrace object.
* ko1: I think it's okay
* akr: needs?
* nobu:  object width is not fixed now.  if we have extra other parameter we can allocate optimal memory region at the beginning.
* akr: concerns if it can create cyclic references.  in duplicating an object is it still possible to retain object graph?
* akr: maybe that is unrelated to #dup discussion.
* akr: in the proposed patch the `backtrace_alloc()` calls `MEMCPY()`, is it by design?
* nobu:  allocation is allocation, to fill in is the duty of `initialize_copy()`
* akr: but it's too easy to copy, because there already is a same object.
* ko1: also without copying `initialize_copy` has no way to know how many rooms of underlying memory regions has already beem allocated using `backtrace_alloc()`
* akr:  Sorry, what was the intended needs of this proposal?
* nobu: we want to duplicate binding, so that we can pass one through Ractor.
* akr: Is that currently impossible?  I think I need to reassure that the proposed nuanced API solves the right problem.

#### Conclusion:

* mame: akr feels very strongly about the design of the allocator function
* akr: I will organize and write up the issue on the ticket 

---

matz reminder

* [[Feature #6012]](https://bugs.ruby-lang.org/issues/6012) Proc#source_location also return the column
  * mame: we need revert for now?
* [[Feature #21795]](https://bugs.ruby-lang.org/issues/21795) Methods for retrieving ASTs
  * matz said "I'll write my comments on issue, but not concluded yet."

---

[[Feature #21822]](https://bugs.ruby-lang.org/issues/21822) Expose Return Value in the ensure Block

* Matz said "I will reject" in the previous meeting

[[Feature#21858]](https://bugs.ruby-lang.org/issues/21858) `Kernel#Hash` considers `to_h` too

* Matz said "I will reply" in the previous meeting

[[Feature #21520]](https://bugs.ruby-lang.org/issues/21520) Feature Proposal: `Enumerator::Lazy#tee`

[[Feature #17056]](https://bugs.ruby-lang.org/issues/17056) `Array#index`: Allow specifying the position to start search as in `String#index`

* matz: I accept it.
* nobu: How about `Array#rindex`?
* matz: I accept it too. I will reply.

[[Feature #21921]](https://bugs.ruby-lang.org/issues/21921) Hash inconsistent ==, >=, <= behavior

[[Feature #21932]](https://bugs.ruby-lang.org/issues/21932) `MatchData#get_int`
[[Feature #21943]](https://bugs.ruby-lang.org/issues/21943) Add `StringScanner#get_int` to extract capture group as `Integer` without intermediate `String`
```
scanner = StringScanner.new("2024-06-15")
scanner.scan(/(\d{4})-(\d{2})-(\d{2})/)
scanner[1]          # => "2024"
scanner.get_int(1)  # => 2024
scanner.get_int(2)  # => 6
scanner.get_int(3)  # => 15
```

```
"2024" =~ /(\d+)/
$~.integer_at(1)     # => 2024 (default: 10 base)
$~.integer_at(1, 8)  # => 02024
$~.integer_at(1, 16) # => 0x2024

# integer_at should behave as String#to_i
"foo" =~ /(...)/
$~.integer_at(1) #=> 0 (== "foo".to_i)

"0xF" =~ /(...)/
$~.integer_at(1) #=> 0 (== "0xF".to_i, not 15)

"" =~ /(\d*)/
$~.integer_at(1) #=> 0 (== "".to_i)

"1_0_0" =~ /(\d+(?:_\d+)*)/
$~.integer_at(1) #=> 100 (== "1_0_0".to_i)

"b" =~ /(a)|(b)/
$~.integer_at(1) #=> nil (== nil&.to_i)?

str =~ /0x(\h+)|0([0-7]+)|(\d+)/
val = $~.integer_at(1, 16) || $~.integer_at(2, 8) || $~.integer_at(3)

str =~ /0x\h+|0[0-7]+|\d+/
val = $~.integer_at(0, 0)

"0xF" =~ /(...)/
$~.integer_at(0, 0) #=> 15 (== "0xF".to_i(0))
```

* matz: I like `get_int` but `MatchData#integer_at` is acceptable. Okay, let's choose `integer_at`

[[Feature #21951]](https://bugs.ruby-lang.org/issues/21951) Lazy load error enhancer gems to speed up boot time

[[Feature #21950]](https://bugs.ruby-lang.org/issues/21950) Add a built-in CPU-time profiler

[net-http#279](https://github.com/ruby/net-http/pull/279) Add RBS signatures from ruby/rbs repo
