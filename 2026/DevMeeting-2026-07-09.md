# DevMeeting-2026-07-09

https://bugs.ruby-lang.org/issues/22107

## DateTime and location

* 2026/07/09 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2026/08/06 (Tue) 13:00-17:00 JST

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #22097]](https://bugs.ruby-lang.org/issues/22097) Add Proc#with_refinements (shugo)

* For maintainability, I've replaced the hand-written iseq deep-copy with an in-memory IBF dump+load round-trip
* Should it warn or raise when called with different modules for the same block?
  * I prefer a performance warning (`Warning[:performance]`) over an exception
* Should we prevent calls to the original `Proc`?
  * In the intended use cases, only the refined `Proc` is called, but I would prefer not to enforce it.
* Should the memo lookup be lock-free?
  * The current implementation uses `RB_VM_LOCKING()` for both lookup and store, which adds ~15 ns overhead per call in multi-Ractor mode
  * See https://github.com/shugo/ruby/pull/112 for details and benchmarks
  * It introduces `IMEMO_TYPE_EXT_BIT` to extend `imemo_type` beyond 16 types.

Discussion:

* matz: The feature itself is acceptable but I don't like the name `with_refinements`
* shugo: `Proc#dup_with_refinements(R1, R2)`?
* matz: I don't like `Proc#dup(refinements: [R1, R2])`
* matz: how about `Proc#refine(R1, R2)`?
* shugo: I don't like it. It looks destructive.
* mame: How about:

```ruby
-> { ... }.using(R)
```

* shugo: `Proc#using` also looks destructive
* shugo: matz, please propose the name
* nobu:

```ruby
Proc.new(refinements: [R]) { }
```

Conclusion:

* matz: I prefer a non-destructive version
* matz: Let me consider the name.

### [[Feature #22132]](https://bugs.ruby-lang.org/issues/22132) Scala-like for comprehensions (shugo)

* Are `then` and `when` acceptable? (`yield` and `if` are not usable due to grammar conflicts)
* Should a comprehension scope loop variables, or leak them like `for ... do`?
  * I think it should scope them. Leaking is harmful when comprehensions are recursive: the loop variables are shared across recursive calls, and an outer binding can be overwritten by an inner one before it is read.

Discussion:

```ruby
1.times do |x|
1.times do |y|
ennd

do
  y for 0...height
  x for 0...width
  next if x + y != 10
  yield [x, y]
end

for user in User.ordered_by {},
    post in Post when post.owner == user do
end
```

* matz: I am now interested in supporting a monad

Conclusion:

* matz: Let me consider

### [[Feature #22186]](https://bugs.ruby-lang.org/issues/22186) Increase the embeddable size limit for substrings created by `str_subseq()` (himura467)

Discussion:

* In short, this improves `str_subseq()` to exploit VWA (currently it does not)
* himura467: It improves fluentd 1.24x faster
* ko1: I am interested in the substr patterns in fluentd (How does it use substr?)
* akr: How about the result for the other benchmarks than fluentd?
* himura467: Many does not change, I think

Conclusion:

* ko1: Please perform the benchmark on another machine

### [[Feature #22118]](https://bugs.ruby-lang.org/issues/22118) Introduce Basic Bit Operations into String (hasumikin)

* Are these methods suitable as the first subset?
* Is the `lsb_first:` keyword acceptable?

Preliminary discussion:

* https://github.com/ruby/ruby/pull/16784 `IO::Buffer#bit_count` is introduced without discussion. (Is it ok?)

Discussion:

```ruby
0.to_msb_offset #=> 7
1.to_msb_offset #=> 6
...
7.to_msb_offset #=> 0
8.to_msb_offset #=> 15
9.to_msb_offset #=> 14
n.to_msb_offset #=> 7 - n % 8 + n / 8 * 8

def to_msb_offset(n) = 7 - n % 8 + n / 8 * 8
```

```ruby
IO::Buffer.for("\x01\x02\x03").bit_count(2, 1) #=> 2
```

* akr: I don't think `lsb_first:` keyword is needed.
* matz: I want to allow MSB first too.
* akr: `bit_count` should accept offset and length, I think
* matz: I am not sure if it is really needed
* akr: `String#bit_set` should accept value: `str.bit_set(offset, value) # value = 0/1 or true/false`
* akr: `String#bit_clear` is not needed, I think
* akr: I think `String#getbit(offset)` and `String#setbit(offset, value)` are good
* akr: `String#bit_flip` is really needed?
* mame: `"1".bitwise_and("11")` should raise an exception, right?

Conclusion:

* akr: I will write your counterproposal
* mame: If hasumikin-san agreed with the counterproposal, let's discuss it next meeting

### [[Feature #20163]](https://bugs.ruby-lang.org/issues/20163) Introduce #bit_count method on Integer (jhawthorn)

 * matz asked for real-world use cases. Various are now listed in https://bugs.ruby-lang.org/issues/20163#note-27 and following comments
 * `String#bit_count` (#22082 / #22118) doesn't cover this case: the bitmap always fits in a fixnum, and String would require extra allocations per operation, and does not have an efficient operation for doing creating mask from `(1 << x) - 1`.
 * `Integer#bit_count` fits well alongside Integer's existing bit methods: `bit_length`, `Integer#[]`, `#allbits?`/`#anybits?`, and the bitwise operators.)
 * Implementation: https://github.com/ruby/ruby/pull/17696
 * Can this be accepted?

Discussion:

```ruby
[nil].count        #=> 1
[nil, false].count #=> 2
```

* mame: I think `bit_count` is not a good name
* matz: No, I think it is good
* mame: Ok
* matz: I understand the use case
* mame: Should it allow offset? Or postpone?

Conclusion:

* matz: I want to decide if I accept `Integer#bit_count` after `String#bit_count` is settled
* matz: I will add a reply to `IO::Buffer#bit_count` https://github.com/ruby/ruby/pull/16784

### [[Feature #18915]](https://bugs.ruby-lang.org/issues/18915) New error class: NotImplementedYetError or scope change for NotImplementedError (koic)

* A new exception class `AbstractMethodError` inheriting from `ScriptError` has been designed.
* Existing code may define an `AbstractMethodError` inheriting from something other than `ScriptError`. In that case a superclass mismatch raises a `TypeError`.
* Is Ruby 4.1 an acceptable timing to introduce `AbstractMethodError` despite the incompatibility risk?

Discussion:

```
mame@gem-codesearch:~$ gem-codesearch '^class AbstractMethodError'
2022-10-17 /srv/gems/abstract_method_error-0.1.0/lib/abstract_method_error/version.rb:class AbstractMethodError < StandardError
2022-10-17 /srv/gems/abstract_method_error-0.1.0/lib/abstract_method_error.rb:class AbstractMethodError < StandardError; end
2025-02-13 /srv/gems/ach_client-5.3.4/lib/ach_client/abstract/abstract_method_error.rb:class AbstractMethodError < RuntimeError
2017-09-30 /srv/gems/etude_for_ruby-0.2.3/docs/dev/_objects_and_data_structures.md:class AbstractMethodError < StandardError
2017-09-30 /srv/gems/etude_for_ruby-0.2.3/docs/dev/objects_and_data_structures.md:class AbstractMethodError < StandardError
2018-01-18 /srv/gems/filigree-0.4.1/lib/filigree/abstract_class.rb:class AbstractMethodError < RuntimeError
2014-04-04 /srv/gems/gorillib-0.6.0/lib/gorillib/exception/raisers.rb:class AbstractMethodError      < NoMethodError ; end
2014-09-23 /srv/gems/gorillib-model-0.0.3/lib/gorillib/core_ext/exception.rb:class AbstractMethodError      < NoMethodError ; end
2017-11-11 /srv/gems/temperature_converter_bl-1.0.0/lib/custom_exceptions/abstract_method_exception.rb:class AbstractMethodError < StandardError
```

* mame: so many use cases of SubclassResponsibility
  * https://github.com/search?q=%2F%5Eclass+SubclassResponsibility+%2F+language%3ARuby&type=code

Conclusion:

* matz: let me consider

### [[Feature #21998]](https://bugs.ruby-lang.org/issues/21998) Add {Method,UnboundMethod,Proc}#source_range (eregon)

* Could matz reply there?

Conclusion:

* matz: ... let me respond

### [[Feature #22085]](https://bugs.ruby-lang.org/issues/22085) `String#to_f` and `Kernel#Float` shouldn't issue out of range warnings (byroot)

* Here is another example of the warning needing to be worked around: https://github.com/ruby/json/pull/1044

Conclusion:

* matz: let me consider and I will reply

### [[Misc #22180]](https://bugs.ruby-lang.org/issues/22180) Provide official Windows (mswin) binary packages and a version manager (hsbt)

* All three components (relocatable zip, build automation, and a PEP 773-style version manager) are implemented and working. I'd like to discuss the three decisions in the ticket:
  * make these official artifacts hosted on ruby-lang.org
  * sign them with a project identity like Ruby association
  * host `rbmanager` repo under the ruby organization

Discussion:

* ko1: Is it for users? Or expert developers?
* hsbt: both.
* ko1: Is `rb` command only for Windows?
* hsbt: yes
* ko1: how to install `rb`?
* hsbt: winget or zip
* mame: Does it add any task to the release process?
* hsbt: Basically no, it will automatically put a release when a relase tag is pushed
* naruse: As a release manager, it looks good

Conclusion:

* matz: it is great if it works well on Windows. Go ahead. I will reply

### [[Feature #21951]](https://bugs.ruby-lang.org/issues/21951) Lazy load error extension gems to speed up boot time (hsbt)

* I rewrite to make `autoload` with C instead of `gem_prelude.rb`.
  * `Process.warmup` load error extensions eagerly now.
* How about this proposal?

Conclusion:

* matz: It looks okay, I will reply

### [[Feature #22135]](https://bugs.ruby-lang.org/issues/22135) Remove obsolete `ObjectSpace#_id2ref` (nobu)

* It has been marked deprecated since 4.0, but discouraged for years.
* The tests are green including the bundled gems and benchmark tests.

Conclusion:

* matz: Okay, I will reply

### [[Feature #22136]](https://bugs.ruby-lang.org/issues/22136) `sprintf` shouldn't raise ArgumentError when $DEBUG is set (byroot)

* It's the only method I know of with such behavior.
* I think it's counter productive, if I run some code with `$DEBUG = true` I expect its behavior not to change (aside from priting debug information).

Discussion:

* nobu: I want to raise an exception even in non-debug mode
* ko1: It will definitely break existing applications
* mame: I want to remove $DEBUG
* ko1: I am curious about the motivation to remove the exception

Conclusion:

* matz: I will follow byroot, make $DEBUG just more verbose mode, It should not change the behavior basically. I will reply


### [[Feature #22137]](https://bugs.ruby-lang.org/issues/22137) Change `Symbol#to_s` to return frozen strings (byroot)

* It has been returning a "chilled string" for two versions now.
* Very large codebases have adopted `Symbol.alias_method(:to_s, :name)` with nice allocation reduction and no compatibility issues.
* I think we should return a frozen string in 4.1.
* If we're really worried, then at least emit a non-verbose warning.

Discussion:

* nobu: can we delete `STR_CHILLED_SYMBOL_TO_S` flag?
* byroot: yes.

Conclusion:

* matz: Ok, I will reply


### [[Feature #22138]](https://bugs.ruby-lang.org/issues/22138) Add `RB_NOGVL_PENDING_INTERRUPT_FAIL` flag for `rb_nogvl`. (ioquatix)

- Additive change to protect against race condition. Is it acceptable?

Discussion:

* matz: I care the name. `RB_NOGVL_INTR_FAIL ` and `RB_NOGVL_PENDING_INTERRUPT_FAIL` are inconsistent
* ko1: How about `RB_NOGVL_MASKED_INTR_FAIL`?

Conclusion:

* ko1: I will ask samuel

### [[Bug #22133]](https://bugs.ruby-lang.org/issues/22133) Ruby's default SIGINT handling ignores `Thread.handle_interrupt` masking. (ioquatix)

- Can we fix it?

Discussion:

* akr: I am not against

Conclusion:

* ko1: no objection. I will reply.

### [[Feature #21704]](https://bugs.ruby-lang.org/issues/21704) Expose `rb_process_status_for` to C extensions. (ioquatix)

- Is the new public function name acceptable to @akr?
- Can we merge it?

Discussion:

```c
pid = waitpid(...., &status, ...);
if (pid != -1) {
  val = rb_process_status_new(pid, status);
}
else {
  val = rb_process_status_error(errno);
}

val = rb_process_status_for(pid, status, error);
```

Conclusion:

* akr: I see the motivation. I will reply
* matz: I prefer `VALUE rb_process_status(rb_pid_t pid, int status, int error);` without `_for`

### [[Feature #22175]](https://bugs.ruby-lang.org/issues/22175) Add `Range#clamp`
* `Range#clamp` returns a new `Range` whose begin and end values are clamped to the given bounds.
* A range counterpart of `Comparable#clamp`. While `Comparable#clamp` clamps a single value, `Range#clamp` clamps both endpoints of a range.

```ruby
range.clamp(min, max) -> range
range.clamp(bounds)   -> range
```

Discussion:

* matz: I understand the use case
* matz: I prefer `clamp` to `intersection`. I agree with three mame's points

Conclusion:

* matz: I will reply

### [[Feature #22185]](https://bugs.ruby-lang.org/issues/22185) Add alignment directives to `Array#pack` and `String#unpack`
* `Array#pack` and `String#unpack` can express byte skips and absolute positions with `x`, `X`, and `@`, but they do not currently have a direct way to align the current position.
* Alignment directive and alignment mode

 * `x!` for relative alignment and `@!` for absolute alignment.


Discussion:

```ruby
[1, 2].pack("!{C i}")
[1, 2].pack("C x!i i")

[1, 2].pack("!{a b c d}")
[1, 2].pack("a x!b b x!c c x!d d")

[1, 2].pack("!{C v C V}")
[1, 2].pack("C x!v v x!C C x!V V")
[1, 2].pack("C x1  v x0  C x3  V")

buffer = +"zzzzz".b
[1].pack("@0 C", buffer: buffer)
p buffer  #=> "\x01"
```

Conclusion:

* matz:  I accept `x!4`, `x!i`, and `@!4`
* matz: Alignment mode is postponed
* matz: I will reply

---
```ruby
class C
  define_method :foo do |str|
    /foo|bar/ =~ str
    bar('bar')
    p $~ #=> #<MatchData "bar">
  end

  define_method :bar do |str|
    /foo|bar/ =~ str
  end

  self.new.foo 'foo'
  p $~ #=> #<MatchData "bar">
end
# そりゃそうだろう、という反応を得た
```

---

https://bugs.ruby-lang.org/issues/22139 Prohibit `END{}`/`Kernel#at_exit` in non-main Ractor

* matz: Go ahead

https://bugs.ruby-lang.org/issues/18995 IO#set_encoding sometimes set an IO's internal encoding to the default external encoding
https://bugs.ruby-lang.org/issues/22174 Set operations (&, ^, collect!, flatten, classify, divide) do not preserve compare_by_identity

https://bugs.ruby-lang.org/issues/22108 Computed hash keys with (expr): syntax
https://bugs.ruby-lang.org/issues/22111 Non-symbolic hash keys with `expr : value` syntax
* matz: I will reject the two

https://bugs.ruby-lang.org/issues/22182 Optimize method chains by destructively updating intermediate objects
