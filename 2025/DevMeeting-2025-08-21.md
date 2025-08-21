# DevMeeting-2025-08-21

https://bugs.ruby-lang.org/issues/21508

## DateTime and location

* 2025/08/21 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/09/11 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #20925]](https://bugs.ruby-lang.org/issues/20925) Allow boolean operators at beginning of line to continue previous line (tenderlovemaking)

* It seems like Matz approved this feature
* Prism has [a PR to support it](https://github.com/ruby/prism/pull/3337)
* Should we merge the PR to support it?

Discussion
- nobu: I have a parse.y implementation.
- shyouhei: Is this allowed? 
    ```ruby=
    foo
      # && bar
      && baz
    ```
- nobu: yes it does.
- akr: `&&` and `and` have different precedences, not even adjacent.  There are other operators in between.  Should they also be able to insert newlines?
- mame: I don't like everything become looser.

Conclusion:

* matz: go ahead


### [[Feature #21532]](https://bugs.ruby-lang.org/issues/21532) Implement `Pathname` in Ruby (tenderlovemaking)

* @Eregon has made a [PR that moves Pathname to Ruby](https://github.com/ruby/pathname/pull/57)
* I reviewed the translation to Ruby and it seems good. Can we merge it?

Discussion:

* akr: Does it handle `$&` well for `Pathname#sub`?
* shyouhei: It seems yes.  Not everything is translated back to Ruby.  `Pathname#sub` remains in C.

Conclusion:

* akr: Persuaded.  OK to merge.


### [[Feature #21533]](https://bugs.ruby-lang.org/issues/21533) Introduce `Time#am?` and `Time#pm?` (matheusrich)

* Thoughts on the name and functionality?
* I think this is the less ambiguous way to have info about a part of the day that works across cultures (and is consistent with strftime)

Discussion:

* akr: No.  12pm is culture dependent.
* mame: OP says the proposal is consistent to strftime.
* naruse: strftime just mimics libc routine, not our invention.
* akr: aaand libc's strftime is locale dependent.
* matz: linux man page has definitive `%p` description by the way, though.
* mame: natural interpretation


FYI https://docs.oracle.com/javase/jp/8/docs/api/java/util/Calendar.html `AM_PM` field

```
10 am
11 am
12 pm
 1 pm
 2 pm
```

* akr: description by 国立天文台
```
10 am
11 am
12 am
 1 pm
 2 pm
```

* ko1: Java's definition is `hour < 12` (same as the OP's pull request)
* naruse: https://en.wikipedia.org/wiki/12-hour_clock#Confusion_at_noon_and_midnight
    * https://www.govinfo.gov/content/pkg/GOVPUB-GP-6eda8715b8f5cb1d8514325b97334d4f/pdf/GOVPUB-GP-6eda8715b8f5cb1d8514325b97334d4f.pdf
      <img width="952" alt="image.png (1.1 MB)" src="https://img.esa.io/uploads/production/attachments/21206/2025/08/21/160341/9783b438-9dec-4757-98cd-31dc076d033b.png">

Conclusion:

* There is no consesus whether 12:00 is AM or PM across the entire planet.
* `strftime` merely provides you a basic compatibility with other systems and we don't want that spread across other parts of the language.


---
https://github.com/rubygems/rfcs/pull/60/files

akr: a gem usually at least depends libc.
   * they are many library and their versions like openssl
   * *BSD distributes kernel and libc. but other libraries are varied
* shyouhei: what is supported OSes?
    * naruse: What Ruby Central wants
* ko1
    * dependency for newer kernel APIs
    * dependency for CPU features

---

https://bugs.ruby-lang.org/issues/21039 Ractor.make_shareable breaks block semantics (seeing updated captured variables) of existing blocks

* `Ractor.shareable_proc` 

ko1:  I want to discuss aobut the spec of sharable proc with outer variables.

```ruby
get "/", &(Ractor.shareable_proc do
  "Hello world"
end)

get "/" do # and get calls sharable_proc
  "Hello world"
end
```

New rule is proposed:

```ruby
# NG: Raise an exception when creating a shareable proc
# The reason is because we're setting a local after the proc
# is created. This can cause possible race conditions / crashes

foo = 123
Ractor.shareable_proc { foo }
foo = Object.new # reassignment isn't allowed
```

The rule is proposed to avoid confusion such as:

```ruby
counter = 0
get "/" do # assume the proc gets copied here so counter is 0
  "Hello world #{counter}"
end

counter += 123
```

```
controller = Sinatra.new do
  enable :logging
  helpers MyHelpers
end

map('/a') do
  run Sinatra.new(controller) { get('/') { 'a' } }
end
```

```ruby
def define mid, var
  define_method(mid) &Ractor.sharable_labmda do
    return var
  end
  var = nil
end

define :foo1, 1
define :foo2, 2
```

Quited from https://bugs.ruby-lang.org/issues/21039#note-24
> If people or I see this problem, I think they would react "WTF, Ruby is broken, nothing can be relied on anymore, not even local variable assignments".

ko1's summary and comment:
* To prohibit local variable assignment, we need to change the logic around local variables.
* as jhawthon said, we can assume:
    * `foo = 123; Ractor.shareable_proc { foo }` as
    * `foo = 123; Ractor.shareable_proc(foo) {|foo| foo }` (implicitly shadow'ed) as new syntax (`Ractor.shareable_proc` is a marker)
 * The problem is, if we allow to accept any Proc (like Sinatra's case), we can't assume which local variables are shadow'ed
* matz accept this implicit behavior https://bugs.ruby-lang.org/issues/21039#note-9
* Another idea: The issue is, new local variable semantics caused by implicitly because of `Ractor.shareable_proc(&bl)`. So introduce new shorter syntax? For example, `-->(...){ ... }`

```ruby
counter = 0
get "/", --> do # assume the proc gets copied here so counter is 0
  "Hello world #{counter}"
end

counter += 123
```

* shyouhei: reminds me of https://bugs.ruby-lang.org/issues/12901
* akr: it could be possible to define a method and then use that method for handler, but that would be ugly.
* mame: `-do`?
```ruby
counter = 0
get "/" -do # assume the proc gets copied here so counter is 0
  "Hello world #{counter}"
end

counter += 123
get "/" do<shareable: true> # assume the proc gets copied here so counter is 0
  "Hello world #{counter}"
end
```

0. `Ractor.sharable_proc` as proposed
    * `Ractor.make_sharable(proc_object)` #-> prohibited
    * `Ractor.sharable_proc(&proc_object)` prohibitted
        * Sinatra-like is dead
1. revert `Ractor.sharable_proc` and return to `make_shareable` (ruby 3.4 way)
   * `Ractor.make_sharable(proc_object)` #-> allowed
   * `Ractor.sharable_proc{}` # not defined
2. `Ractor.sharable_proc` allows non literal block
    * `Ractor.make_sharable(proc_object)` # prohibited
    * `Ractor.sharable_proc(&proc_object)` # allowed
        * sinatra-like is rescued
        * `Ractor.sharable_proc(self: nil){ ... }`
        * `nil.instance_eval{ proc{ ... } }`
          ```ruby
            get "/", &nil.instance_eval{ -> do
            end }
            get "/" -do
              @foo = 42
              ERB.result(binding) # implicitly refer to @foo
            end
          ```
        realistic sinatra app https://railsgirls.jp/sinatra-app
3. prevent lvar assignment proposed by eregon
    * matz: no. I can accept to change the behavior in the block, but I can't accept to chage the behavior of outer variables.
4. `make_shareable` somewhat "freeze"s the local binding of the proc
    * matz: no, same as 3
5. introduce new syntax like `-do`
   * matz: not in favour of this specific `-do` look & feel, but can be an option
   * matz: `--> do` does not seem shareable at all.

----

https://bugs.ruby-lang.org/issues/21458 Test 'make install'?

* shyouhei: Great idea.  It is difficult to verify the installation is correct or not though.
* mame: I think it is a good small start to check only if `make install` does not fail or not. We should discuss how to verify what is installed

https://bugs.ruby-lang.org/issues/21518 Statistical helpers to `Enumerable`

* `Enumerable#average`
* `Enumerable#median`

* matz: no strong pro or contra.
* naruse: `Enumerable#sum` can be implemented if each elements have `+` methods but `#average` also requires division.
* shyouhei: `Enumerable#sort` needs something more than `+` though.

https://bugs.ruby-lang.org/issues/21385 Namespace: Suggesting a rename

* matz: Ruby Box!

https://bugs.ruby-lang.org/issues/21528 `SyntaxError#message` may have broken encoding with multibyte source under Prism

* nobu: It must be fixed

https://bugs.ruby-lang.org/issues/21529 Deprecate the `/o` modifier and warn against using it

* matz: I will reject

https://bugs.ruby-lang.org/issues/21520 Feature Proposal: `Enumerator::Lazy#lazy_each`

https://bugs.ruby-lang.org/issues/21527 Proposal: `Math.log1p` and `Math.expm1`

* matz: accepted

https://bugs.ruby-lang.org/issues/21515 Add `&return` as sugar for `x=my_calculation; return x if x`

https://bugs.ruby-lang.org/issues/21538 `initialize_dup` not called when duping `class`/`module`

* matz: It would be nice to have it fixed...?

https://bugs.ruby-lang.org/issues/21111 `RbConfig::CONFIG['CXX']` quietly set to "false" when Ruby cannot build C++ programs

* nobu: https://github.com/nobu/ruby/tree/find_cxx

https://bugs.ruby-lang.org/issues/21540 prism allows `foo && return bar` when parse.y doesn't

```
foo && return       # OK in parse.y, OK in prism
foo && return foo   # NG in parse.y, OK in prism
foo && return(foo)  # NG in parse.y, OK in prism
foo && (return foo) # OK in parse.y, OK in prism

->{return(1) + 1 }.call #=> 2
```

* matz: Fix it in the prism side
* nobu: ditto for break and next.

https://bugs.ruby-lang.org/issues/17316 `@result @||= expensive_calculation`

* matz: I am against the syntax `@||=`

https://bugs.ruby-lang.org/issues/12282 `Hash#dig!` for repeated applications of `Hash#fetch`

* matz: `path` in `fetch_path` resembles a file path in Ruby

https://bugs.ruby-lang.org/issues/21545 `#try_dig`, a dig that returns early if it cannot dig deeper

* matz: How about `{ a: "foo" }.dig(:a, :b, weak: true) #=> nil`?
* akr: `{ a: "foo" }.dig(:a, :b, exception: false) #=> nil`?
* matz: It is also acceptable, if the keyword is suitable
* ko1: what does `weak` mean?
* matz: Ok the name is not good

https://bugs.ruby-lang.org/issues/21543 Point `ArgumentError` to the call site

```ruby
class TestClass
  def foo(x, y)
  end

  def bar
    foo(1)
  end

  def main
    bar
  end
end

TestClass.new.main
```
```
$ ruby test.rb
foo.rb:2:in 'TestClass#bar': wrong number of arguments (given 1, expected 2) (ArgumentError)

    foo/bar.rb:6: caller
    6 |    foo(1)
           ^
    foo.rb:2: callee
    2 |  def foo(x, y)
             ^
        from foo/bar.rb:6:in 'TestClass#bar'
        from foo.rb:10:in 'TestClass#main'
        from foo.rb:14:in '<main>'
```

```
$ gcc t.c
t.c: In function ‘main’:
t.c:4:3: error: too few arguments to function ‘f’
    4 |   f(1);
      |   ^
t.c:1:1: note: declared here
    1 | f(int x, int y){ return x; }
      | ^

$ cat t.c
f(int x, int y){ return x; }

main(){
  f(1);
}
```

----

https://x.com/blackenedgold/status/1957662677012214111
https://github.com/rust-lang/rust/pull/144232
> Implement support for `become` and explicit tail call codegen for the LLVM backend by xacrimon · Pull Request #144232 · rust-lang/rust


```ruby
# Rust style
def foo
  return bar # not tail-call
  become bar # tail-call!
  be bar # old proposal
end
def bar
end

def foo
  # conflict with current syntax
  bar and return
  bar then return

  # ok (current: syntax error)
  and return bar
  return and bar
  return then bar
  return and then bar
  then return bar
end
```

```
> function f() print("f") return g() end function g() print("g") print(debug.traceback()) end f()
2025-08-20 10:31:46: f
2025-08-20 10:31:46: g
2025-08-20 10:31:46: stack traceback:
	[string "function f() print("f") return g() end functi..."]:1: in function 'g'
	(...tail calls...)
	[string "function f() print("f") return g() end functi..."]:1: in main chunk
```
