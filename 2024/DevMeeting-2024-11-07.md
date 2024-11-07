# DevMeeting-2024-11-07

https://bugs.ruby-lang.org/issues/20781

## DateTime and location

* 2024/11/07 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2024/12/12 (Thu) 13:00-17:00 JST @ Online
* 2024/12/5 is RWC
* 12/23 is not a national holiday (any longer) though.
* ko1: I'd avoid Wed/Fri
* ko1: What about January?
    * 2025/1/9
    * mame: no agenda though.

* hsbt: We plan to have a release party

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #20794]](https://bugs.ruby-lang.org/issues/20794) Expose information about the currently running GC Module (@eightbitraptor) (eightbitraptor)

* This adds a dynamic component to `RUBY_DESCRIPTION`, when a modular GC is loaded. Is this acceptable?
* This adds a method `GC.active_gc_name`
  * Is the name OK?
  * Are the semantics OK? (`nil` unless a GC module is actively being used).

Preliminary discussion:

* nobu: `GC.config` can have `implementation: "mmtk"` or something?

Discussion:

* peterzhu: proposed is A Ruby API and modification to `RUBY_DESCRIPTION`
* matz: Is this a CRuby-only API?
* peterzhu: exactly.

----
API

* peterzhu: `GC.config` for this purpose doesn't fit because the GC implementation cannot be modified on-the-fly.
* ko1: maybe there can be concept of "readonly" configurations.
* ko1: implementation names don't fit well into `GC.stat`.
* matz: I don't have strong opinions.
* peterzhu: me neither.
* matz: What would be added to GC.config if we go that way?
* peterzhu: Maybe we add two keys one describing whether it's mmtk or built-in, and the other for mmtk-specific parameters.
* ko1: `{implementation: 'mmtk/immix'}` could be enough.
* ko1: `{mmtk_immix_param1: ...}`
* samuel: LGTM.

----
`RUBY_DESCRIPTION`

* nobu: I'm not a big fan of it.
* peterzhu: it appears only when mmtk is in motion.
* nobu: RUBY_DESCRIPTION is already too long.
* shyouhei: maybe in a future we want to split between human-readable`ruby -v` and those debugging info.
* ko1: there is no way other than reading `ruby -v` to know if `+PRISM` is in motion or not.  GC situation is better than Prism.
* ioquatix: Puma, Falcon etc. print those info and let bug reports include the version line.
* shyouhei: what about this: `RUBY_DESCRIPTION` is complex, while `ruby -v` remains simple.
* ioquatix: That way, everyone have to change their GitHub repo's issue template describing "please write down your`ruby -v` here."
* matz: Everybody already use `ruby -v` for debugging purpose, we cannot change its usages.
* nobu: OK then.  Persuaded.

`+MOD_GC[mmtk]`
`+GC[mmtk]`
`+GC[mmtk/immix]`
`+GC[mmtk/immix param param...]` -- no
`+GC[noop]`
`+GC[default]` - no
`+GC` - we don't need this -- @ko1
* original proposal
  * `+MOD_GC` with configured ruby
  * `+MOD_GC[mmtk]` with configured ruby
* ko1
  * `+GC[mmtk]`
  * `+GC[]` without extenal GC

- samuel: Is it `"+GC#{GC.config[:implementation]}"`??? - maybe a good reason to split `:implementation` key from all the other parameters?

Conclusion:

* matz: `GC.config()` contains read-only parameters and `implementation:` key, and `mmtk/immix` or something like that.
* `RUBY_DESCRIPTION` contains the following string if confugured with shared GC implementation.
  * `+GC[mmtk]`
  * `+GC[]` without extenal GC

### [[Feature #20860]](https://bugs.ruby-lang.org/issues/20860) Merge Optional Experimental Feature MMTk into Ruby (peterzhu2118)

  - We are proposing to merge MMTk into the `ruby/mmtk` repository, which will be mirrored into `ruby/ruby`.
  - This implementation uses the GC API (introduced in [Feature #20470]).
  - Supports the NoGC and mark-sweep algorithms.
  - We do not ever anticipate replacing Ruby's default GC with MMTk but instead offer it as an alternative implementation.
  - We have our roadmap proposed in the ticket, which includes supporting generations, improving parallelism, and support moving, faster algorithms such as [Immix](https://www.steveblackburn.org/pubs/papers/immix-pldi-2008.pdf).

Discussion:

* peterzhu: current implementation are NoGC, and no-generation no-compaction mark-sweep GC.
* peterzhu: we now have separate repo but want to merge this.
* matz: no objecttion at all.
* mame: one question: how stable is CI? is it flaky?
* peterzhu: it seems to be relatively stable, but it's okay to stop CI in ruby/ruby if stability is a problem.
* mame: maybe we can turn it off later when in trouble.
* peterzhu: we also need ruby/mmtk repo
* hsbt: do you want to transfer or start from fresh one?
* peterzhu: creating new one is the easiest.
* hsbt: got it.
* ko1: what will be in ruby/mmrk?
* peterzhu: all the GC implementations (in rust)
* ko1: then ruby/ruby is...?
* peterzhu: we need some changes in current GC implementation.
* nobu: Confirmation: Apache license is compatible? Or it is OK since it is dual licence?
  * https://github.com/mmtk/mmtk-ruby#licensing
* mame: why you want to separate a repository?
* peterzhu: we want to add collaboratos from external universities.  ruby/ruby is too big for that purpose.
* ko1: Is it for 3.4?
* peterzhu: Definitely.
* ko1: can I change or break GC api in future?
* peterzhu: Yes. We will fix it.
 
Conclusion:

* matz: go ahead

### [[Feature #20811]](https://bugs.ruby-lang.org/issues/20811) `warning: in a**b, b may be too big` is really helpful? (mame)

* I think the warning is not useful. Can we remove the warning?

Discussion:

* mame: why is it like this now?
* shyouhei: nobody remembers.
* akr: do we want to suppress warning or just calculate everything?
* mame: we do calculate.

Conclusion:

* matz: let's calculate.

### [[Feature #20782]](https://bugs.ruby-lang.org/issues/20782) Introduction of Happy Eyeballs Version 2 (RFC8305) in TCPSocket.new (hsbt)

* Propose shioimm as a Ruby committer
  * How about it?

Discussion:

* naruse: looks good

Conclusion:

* matz: I will reply

### [[Feature #20859]](https://bugs.ruby-lang.org/issues/20859) Make Base64 to core class (hsbt)

* It's useful and helpful for gemification.

Discussion:

* hsbt: People avoid using Base64 to use String#pack instead.  The reason behind this seems additional gems could be hesitated.
* akr: It sounds too agressive to make it built-in.
* mame: Do we want to keep it default gem forever?
* akira: As a gem author it's okay for me to write string.pack('m'), as long as it comes with comments.
* mame: I would avoid adding new dependency in a pull request.
* shyouhei: Perl was useful.
* hsbt: I'm against making it a standard library because there already is base64 gem.  The standard libray never gets used if there is a gem.

Conclusion:

* matz: String#base64 needs a separate ticket.
* matz: `Base64` is not going to be a core class.

### [[Feature #20818]](https://bugs.ruby-lang.org/issues/20818) Allow passing a block to Hash#store to update current value (furunkel)

* currently, to update a value in a hash we have to call `#[]` followed by `#[]=`
  * this calls `#hash` twice (on key), and requires two lookups; this also happens for shortcuts such as `h[k] += c`
* the problem could be solved by introducing a block form for `Hash#store` (`Hash#update` is already taken)
  * the block is called with the current value or the default value if the key does not exist; return value = new value
  * inverse of `Hash#fetch`, which also can take block
* `h[k] = some_method(h[k])` could be written as `h.store(k) { some_method(_1) }`, more efficient and more elegant in some cases (e.g., if `k` is complex, no repetition)
* exists in other languages: e.g., `Map#computeIfAbsent|Present` (Java) or `and_modify` (Rust)
* existing implementation: GH PR#11956

Preliminary discussion:

[sample](https://ruby.github.io/play-ruby/?run=11554325899&code=h+%3D+%7Ba%3A1%2C+b%3A2%7D%0Ap+h.store%28%3Ac%2C+3%29++++++++%23%3D%3E+3%0Ap+h.store%28%3Aa%29%7B_1+*+10%7D++%23%3D%3E+nil%0Ap+h.store%28%3Ad%29%7Bp+_1%3B+%3Ad%7D++++%23%3D%3E+nil%0Ap+h.store%28%3Ae%29%7Bp+_1%3B+break%7D+%23%3D%3E+nil%0Ap+h+%23%3D%3E+%7Ba%3A+10%2C+b%3A+2%2C+c%3A+3%2C+d%3A+%3Ad%7D%0A)

```ruby
h = {a:1, b:2}
p h.store(:c, 3)        #=> 3
p h.store(:a){_1 * 10}  #=> nil
p h.store(:d){p _1; :d}    #=> nil
p h.store(:e){p _1; break} #=> nil
p h #=> {a: 10, b: 2, c: 3, d: :d}

# current
x = h[:c] #=> 3
h[:c] = x + 1
#=>
h.store(:c){ it + 3 }
```

* samuel: Isn't it convenient if it **always** returns the final value? `Hash#store(key, value) -> value`  & `Hash#store(key, &block) -> value`

Discussion:

* mame: We cannot eliminate the second lookup.  The passed block can modify the receiver itself.  It is a wrong idea to believe the original position is untouched.
* nobu: We can:

    ```ruby
    h = {a:1, b:2}
    h.update(b:__anything__) { |x, y, z| .... whatever ... }
    ```

* shyouhei: the above example makes sense only when the key collides.

Conclusion:

* matz: I don't like the style so much
* matz: If it is safe and really faster than ordinary style `h[k] = f(h[k])`, there is room to consider. Note that it should care the case where the hash is modified during the block of Hash#store.

### [[Feature #20770]](https://bugs.ruby-lang.org/issues/20770) (Re)Introduce pipe operator (AlexandreMagro)

* Initially proposed as syntactic sugar for `.then`, and later refined after discussion to work as a statement separator (like `;`) with a variable carrying the LHS expression result to the RHS in [#note-34](https://bugs.ruby-lang.org/issues/20770#note-34).
* This approach aligns with how pipes work in other languages, adapted for Ruby.
* Improves readability by transforming `p(q(r))` into a more natural `r |> q |> p`,

Preliminary discussion:

* ko1: `p(q(r))` should be `r |> q _ |> p _` rather than `r |> q |> p` with note-34 proposal?

Discussion:

```ruby
def foo(_a, _a) = _a
foo(1, 2)  #=> 1
```

* matz: I am afraid if elixir users and F# users are confortable with this proposal, but I like it
* nobu: the variable `_` is visible after the pipeline?

```ruby
1 |> 2
p _ #=> 1?
```

* matz: It is better to prohibit it, if possible
* akr: It should be simple. Just keep it assigned and untouched
* nobu: how about nesting?

```ruby
_ = 0
1 |> foo(
  p(_),        #=> 1
  binding.local_variable_get(:_), #=> 1
  (2 |> p(_)), #=> 2
  p(_),        #=> 1 by restore value, nil by akr, 2 by left assigned value
)
p _ #=> ?
binding.local_variable_get(:_) #=> ?

1 |> 1.times {
  p _ #=> 1
  2 |> 1.times {
    p _ #=> 2
  }
  p _ #=> 1
}
```

Conclusion:

* matz: I would like to try it after syntax moratorium towards Ruby 3.5

--

### [[Bug #20863]](https://bugs.ruby-lang.org/issues/20863)  `zlib.c` calls `rb_str_set_len` and `rb_str_modify_expand`(and others) without holding the GVL (ioquatix)

I was investigating some problems with `zlib.c` and the GVL, and noticed that `zstream_run_func` is executed without the GVL, but can invoke various `rb_` string functions. Those functions in turn can invoke `rb_raise` and generally look problematic. However, maybe by luck, such code path does not appear to be invoked in typical usage. I propose to fix these issues.

私は `zlib.c` と GVL に関するいくつかの問題を調査していたところ、`zstream_run_func` は GVL なしで実行されますが、さまざまな `rb_` 文字列関数を呼び出すことができることに気付きました。これらの関数は今度は `rb_raise` を呼び出すことができ、一般的に問題があるように見えます。しかし、運が良ければ、このようなコード パスは通常の使用では呼び出されないようです。私はこれらの問題を修正することを提案します。

Preliminary discussion:

* Is the PR (https://github.com/ruby/zlib/pull/88) to zlib acceptable?
  * Several string manipulation methods were invoked while the GVL was released. This is unsafe and has been cleaned up.
  * The mutex protecting multi-threaded access was not covering buffer state manipulation (e.g. in `rb_inflate_inflate`), leading to data corruption and out-of-bounds writes. This has been corrected.
  * Using `rb_str_locktmp` prevents changes to buffer while it's in use. This has been added.

Discussion:

* shyouhei: it's definitely a bug.
* nobu: I'm trying to review.

Conclusion:

* nobu: let me review.
* ioquatix: I want it be fixed in 3.4.

### [[Feature #20877]](https://bugs.ruby-lang.org/issues/20877) Introduce (public) debug assertion for holding the GVL (ioquatix)

In order to prevent issues like the above, I'd like to introduce additional debug-only checks to prevent Ruby functions from executing without the GVL, when the GVL is expected. I propose to introduce a new macro to make this easy.

上記のような問題を防ぐために、GVL が期待されるときに Ruby 関数が GVL なしで実行されないようにするための追加のデバッグ専用チェックを導入したいと思います。これを簡単にするために、新しいマクロを導入することを提案します。

Preliminary discussion:

* Is the PR (https://github.com/ruby/ruby/pull/11975) to introduce these checks internally acceptable? 

```ruby
RUBY_ASSERT(ruby_thread_has_gvl_p());
// Or maybe:
RUBY_ASSERT_THREAD_HAS_GVL;
```

* Can we add more checks to other functions in CRuby?
* Should we introduce a public macro so that other native extensions can also implement this check? Or is it sufficient just to do the checks at the boundary of the CRuby C API?
  * What about extensions like `ext/socket` etc?
  * Can we agree on a name for this macro and any related public C functions? e.g. `RUBY_ASSERT_THREAD_HAS_GVL`? It may be useful for native extension authors.

Discussion:

* ko1: is this proposal (1) add assertions to ruby internals, or (2) to external libraries?
* ioquatix: (1) is mandatory, (2) is optional for this proposal.
*  samuel: Why is it `ruby_...` rather than `rb_...`?
* ko1: `RUBY_ASSERT(ruby_thread_has_gvl_p())` is readable and it is enough for extensions. So `ruby_thread_has_gvl_p()` should be exposed.

Conclusion:

* matz: We can introduce internal checks `RUBY_ASSERT(ruby_thread_has_gvl_p());` everywhere it makes sense.
* matz: We can expose `ruby_thread_has_gvl_p()` as a public interface `include/ruby/thread.h`

### [[Feature #20876]](https://bugs.ruby-lang.org/issues/20876) Introduce `Fiber::Scheduler#blocking_operation_wait` to avoid stalling the event loop (ioquatix)

The current Fiber Scheduler performance can be significantly impacted by blocking operations that cannot be deferred to the event loop, particularly in high-concurrency environments where Fibers rely on non-blocking operations for efficient task execution. We propose to introduce a mechanism to move these blocking operations off the event loop. Please see the issue for the full proposal.

現在のファイバー スケジューラのパフォーマンスは、イベント ループに延期できないブロッキング操作によって大幅に影響を受ける可能性があります。特に、ファイバーが効率的なタスク実行のために非ブロッキング操作に依存している高同時実行環境では顕著です。これらのブロッキング操作をイベント ループから移動するメカニズムを導入することを提案します。完全な提案については、問題を参照してください。

Preliminary discussion:

*  Is the proposed `blocking_operation_wait` scheduler hook acceptable?

```ruby
class MySchduler
  # ...
  def blocking_operation_wait(work)
    # Example (trivial) implementation:
    Thread.new(&work).join
  end
end
```

* Is the proposed `RB_NOGVL_OFFLOAD_SAFE` flag acceptable?

```c
/**
 * Passing  this  flag   to  rb_nogvl()  indicates  that  the   passed function
 * is safe to offload to a background thread or work pool. In other words, the
 * function is safe to run using a fiber scheduler's `blocking_operation_wait`.
 * hook.
 *
 * If your function depends on thread-local storage, or thread-specific data
 * operations/data structures, you should not set this flag, as
 * these operations may behave differently (or fail) when run in a different
 * thread/context (e.g. unlocking a mutex).
 */
#define RB_NOGVL_OFFLOAD_SAFE  (0x4)
```

* Is the proposed implementation change to `rb_nogvl` acceptable?

```c
void *
rb_nogvl(void *(*func)(void *), void *data1,
         rb_unblock_function_t *ubf, void *data2,
         int flags)
{
    if (flags & RB_NOGVL_OFFLOAD_SAFE) {
        VALUE scheduler = rb_fiber_scheduler_current();
        if (scheduler != Qnil) {
            struct rb_fiber_scheduler_blocking_operation_state state;

            VALUE result = rb_fiber_scheduler_blocking_operation_wait(scheduler, func, data1, ubf, data2, flags, &state);

            if (!UNDEF_P(result)) {
                rb_errno_set(state.saved_errno);
                return state.result;
            }
        }
    }
```

Discussion:

* ko1: because this is a public API I want matz's approval.
* ko1: Typical situation is zlib's blocking operation, which is CPU-bound, to be off-loaded to a separate thread when a Fiber scheduler is in action.
* samuel: For another point of reference, things like libuv take a similar approach (using a background thread pool for blocking operations).
* ko1: zlib is okay.  But for maximum safety we need a new flag called `RB_NOGVL_OFFLOAD_SAFE`.

Conclusion:

* matz: go ahead

### [[Feature #20864]](https://bugs.ruby-lang.org/issues/20864) Allow `Kernel#warn` to accept `**options` and pass these to `Warning.warn` (ioquatix).

Structured logging is a practice that organizes log data in a consistent, easily parseable format. Unlike traditional unstructured logs, which often consist of plain text entries, structured logs format information in key-value pairs or structured data formats such as JSON. This allows for better automation, searchability, and analysis of log data.

I was trying to use `warn` to emit warnings when an exception occured that would otherwise be ignored. I found there was no good way to use `Kernel#warn` for structured logging, e.g. the error message, backtrace and so on, formatted as JSON. I would like to discuss if there is some way we can improve/change `Kernel#warn` to allow for structured logging output, and to allow users of `warn` to provide additional details, like the exception (or any arbitrary metadata) that can be attached to the warning.

構造化ログは、ログ データを一貫性のある、簡単に解析できる形式で整理する方法です。プレーン テキスト エントリで構成されることが多い従来の非構造化ログとは異なり、構造化ログはキーと値のペアまたは JSON などの構造化データ形式で情報をフォーマットします。これにより、ログ データの自動化、検索性、分析が向上します。

例外が発生したときに、通常は無視される警告を発するために `warn` を使用しようとしていました。JSON 形式でフォーマットされたエラー メッセージ、バックトレースなどの構造化ログに `Kernel#warn` を使用する良い方法はないことがわかりました。構造化ログ出力を可能にし、`warn` のユーザーが警告に添付できる例外 (または任意のメタデータ) などの追加の詳細を提供できるように `Kernel#warn` を改善/変更する方法があるかどうかについて話し合いたいと思います。

Discussion:

* Is such an interface acceptable? Is this idea worth pursuing?

```ruby
module Kernel
  def warn(*arguments, uplevel: ..., **options)
    message = # ... caller(uplevel+1,1) + ": " + arguments.join("\n") + ...
    ::Warning.warn(message, **options)
  end
end

module MyWarningOutput
  def warn(message, **options)
    $stderr.puts({message: message, **options}.to_json)
  end
end

Warning.extend(MyWarningOutput)
```

* Jeremy has concerns about maintainability - e.g. adding new keyword options in the future. He suggests using a single keyword argument for the user data. However the interface is slightly worse in my opinion:

```
# Proposed way:
warn "Something went wrong!", exception: error

# Jeremy's suggestion:
warn "Something went wrong!", metadata: {exception: error}
```

* If we introduce the updated interface, do we need to provide C function too?
  *  So many variants already: https://github.com/ruby/ruby/blob/0193f6c2889795014c2707d8e3dd943a303a13c3/include/ruby/internal/error.h#L511-L592
  * Some of these could emit structured logs, e.g. file path, line number, etc.

```ruby
# Why is this not okay?
warn "foo", category: :foo
```


* ko1: what happens if someone redefines Kernel#warn method?
* mame: I thought it is already mandatory for user-defined Kernel#warn to take arbitrary keyword arguments.
* mame: Actually Warning.warn might not take keyword arguments at all.  We want to check arity before calling.
* akr: I recommend some example of serialization to be in documents.  It might be difficult from the API to know the usage.

Conclusion:

* no conclusion

### [[Bug #20869]](https://bugs.ruby-lang.org/issues/20869) IO buffer handling is inconsistent when seeking (byroot)

* The ticket show multiple weirdness around `IO#ungetbyte / IO#ungetc` and `#IO#seek`.
  * This is very clearly a bug, but what the right behavior should be isn't very clear. Clarification would be welcome.

Conclusion:

* akr, nobu: continue to discuss

### [[Bug #20857]](https://bugs.ruby-lang.org/issues/20857) Ruby 3.4 seems to have backwards compatibility issues more than its predecessors

* matz: The impact looks reasonable. I would like to go as is

---

https://bugs.ruby-lang.org/issues/20750 Allow rb_thread_call_with_gvl to work when thread already has GVL

* mame: now ruby_thread_has_gvl_p is exposed
* nobu: even we change the behavior, a gem author cannot depend on it unless they drop Ruby 3.3 support
* ko1: should we expose a macro to distinguish between the old behavior and the new one?

https://bugs.ruby-lang.org/issues/20785 Should `a in b, and c` `a in b, or c` `a in b, rescue c` be syntax ok?

* matz: as a general rule, a trailing comma should be allowed in one-line pattern match
```ruby
tap do
  a in b, and c    # Prism should accept this
  a in b, or c     # Prism should accept this too
  a in b, rescue c # parse.y should handle this as modifier rescue. Prism should do the same
end

a in b,
and c
# akr: It should be the first case where a sentence ends with a comma. What should we do?
# matz: If possible, it should be a syntax error. a newline after a comma should end the sentence
```

https://bugs.ruby-lang.org/issues/20786 Flow chaining with "then" keyword

* matz: rejected

https://bugs.ruby-lang.org/issues/20790 Syntax acceptance of `*x = p rescue p 1` is different between parse.y and prism

```ruby
                    # current parse.y behavior   # matz
*x = p rescue p 1   # (*x = p) rescue (p 1)      # *x = (p rescue (p 1))
*x = p 1 rescue p 1 # (*x = p 1) rescue (p 1)    # *x = ((p 1) rescue (p 1))
a, b = p rescue p   # a, b = (p rescue p)
x = p rescue p      # x = (p rescue p)
x = p rescue p 1    # x = (p rescue p) 1 (ERROR) # x = (p rescue p 1)
x = p 1 rescue p 1  # x = ((p 1) rescue (p 1))
```

https://bugs.ruby-lang.org/issues/20793 Allow Multiple Arguments for the .is_a? Method

* matz: rejected

https://bugs.ruby-lang.org/issues/20795 Timeout method doesn't check for negative time values

```ruby
# Currently, timeout 0 does not restrict the execution time
Timeout.timeout(0) do
  puts "executed normally"
end
```

* matz: I like early failure. Let's raise an ArgumentError against negative value
* matz: zero should also be raised, but I am concerned about the incompatibility. I am not sure

https://bugs.ruby-lang.org/issues/18242 Parser makes multiple assignment sad in confusing way

```ruby
# both should be allowed
1 < 2 and a, b = 1, 2
1 < 2 and a = 1, 2
```

https://bugs.ruby-lang.org/issues/20802 It is possible to set the encoding of an IO instance to one that requires binmode when binmode is not set
```ruby
f1 = File.open('/dev/null')
f1.set_encoding('utf-16le') #=> ASCII incompatible encoding needs binmode (ArgumentError)

f1 = File.open('/dev/null')
f1.binmode
f1.set_encoding('utf-16le')
f2 = File.open('/dev/null')
f2.set_encoding('utf-8')
f1.reopen(f2)
f1.binmode?            # <= false
f1.external_encoding   # <= Encoding::UTF_16LE
```

* akr: we should check the actual behavior on Windows text mode. It should be broken after CR+LF because CR+LF will be converted to one byte and consequent bytes are off-by-one error
* akr, nobu: I guess we should copy the encoding in IO#reopen
* tompng: I am a bit afraid about the incompatibility

https://bugs.ruby-lang.org/issues/20792 String#forcible_encoding?

* matz: I think `.dup.force_encoding` is good enough

https://bugs.ruby-lang.org/issues/20808 Data#pretty_print doesn't handle private or remove attribute readers

* akr: an exception is not good in any way

```ruby
class C < Struct.new(:a)
  private :a
end

pp C.new(10)
#=> #<struct C a=10>

class D < Data.define(:a)
  private :a
end

pp D.new(10)
#=> ruby/3.3.0+0/pp.rb:431:in `public_send': private method `a' called for an instance of D (NoMethodError)
```

* akr: It should use `__send__` instead of `public_send`

https://bugs.ruby-lang.org/issues/20807 String#gsub fails when called from string subclass with a block passed
https://bugs.ruby-lang.org/issues/20852 Anonymous HEREDOC blocks

* matz: we should not touch heredoc anymore

https://bugs.ruby-lang.org/issues/20858 multiple parallel assignments are inconsistent

* matz: rejected

https://bugs.ruby-lang.org/issues/20861 Add an environment variable for tuning the default thread quantum
```ruby
def fib(n)
  n < 2 ? 0 : fib(n - 1) + fib(n - 2)
end

N = 35
th1 = Thread.new { fib(N) }
th2 = Thread.new { fib(N) }
th1.join
th1.join
```

```
$ time RUBY_THREAD_DEFAULT_QUANTUM_MS=10 ./miniruby t.rb

real    0m1.500s
user    0m1.487s
sys     0m0.007s

$ time RUBY_THREAD_DEFAULT_QUANTUM_MS=100 ./miniruby t.rb

real    0m1.419s
user    0m1.416s
sys     0m0.004s

$ time RUBY_MN_THREADS=1 RUBY_THREAD_DEFAULT_QUANTUM_MS=100 ./miniruby t.rb

real    0m1.460s
user    0m1.455s
sys     0m0.008s

$ time RUBY_MN_THREADS=1 RUBY_THREAD_DEFAULT_QUANTUM_MS=10 ./miniruby t.rb

real    0m1.459s
user    0m1.457s
sys     0m0.005s

$ time RUBY_THREAD_DEFAULT_QUANTUM_MS=1000 ./miniruby t.rb

real    0m1.455s
user    0m1.453s
sys     0m0.003s
```
