# DevMeeting-2025-10-23

https://bugs.ruby-lang.org/issues/21606

## DateTime and location

* 2025/10/23 (Tue) 13:00-17:00 JST @ Online

## Next Date

* 2025/11/13 (Tue) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets
### [[Feature #21572]](https://bugs.ruby-lang.org/issues/21572) Make illegal variable in alternation pattern a syntax error (kddnewton)

* Can we make this into a parse-time syntax error as opposed to a compiler error?

Preliminary discussion:

```ruby
case foo
in { a: } | Array
  "matched: #{a}"
end
```

Discussion:

* matz: parse error is definitely better
* nobu: Should it render error when alternations have different variable definitons?
* mame: seems alternations with local variables are totally NG

Conclusion:

* matz: Accepted, waitning for implementations


### [[Misc #21609]](https://bugs.ruby-lang.org/issues/21609) Propose Stan Lo (@st0012) as a core committer (k0kubun)

* To speed up the ZJIT development, can we let him merge his PRs by himself?

Preliminary discussion:

*

Discussion:

* no objection

Conclusion:

* matz: Go forward.


### [[Misc #21646]](https://bugs.ruby-lang.org/issues/21646) Propose Luke Gruber as a core committer (jhawthorn)

* He's been contributing to CRuby since 2019 fixing a variety of bugs and being active on redmine both filing and fixing issues. For the past few years he's made a lot of improvements to help stabilize Ractors,

Preliminary discussion:

*

Discussion:

*

Conclusion:

* matz: No objection!


### [[Feature #20163]](https://bugs.ruby-lang.org/issues/20163) Add `Integer#popcount` (tenderlovemaking)

* Add a method for counting "on" bits in an integer
* Raises an exception for negative numbers
* Method would be helpful for bitsets, bitboards, and other data structures

Discussion:

* matz: So what's the use case?
* akr: I can think of succinct data structures.  See also [[Feature #8748](https://bugs.ruby-lang.org/issues/8748)]
* mame: Actually popcount against ruby's bignum is less useful than other languages
* akr: agreed because they are immutable.
* nobu:  What is actually needed is an array of bits?
* nobu: Maybe we need popcount for strings in stead of integers
* akr: strings are not XOR-able, which reduces its applicability.

Conclusion:

*


### [[Bug #21640]](https://bugs.ruby-lang.org/issues/21640) Core `Pathname` is missing 3 methods / is partially-defined (eregon)

* This seems a bad user experience: partially-defined class missing some methods, `require "pathname"` doesn't load the gem as expected (even with Bundler), etc
* I think it would be better to keep Pathname as a default gem and not core, it avoids all 6 problems.
* If we absolutely must have Pathname in core, how to fix these 6 problems then?

Discussion:

* tmpdir.rb defines `Dir#mktmpdir` (and `Dir::Tmpname`)
* it should also define `Pathname#mktmpdir` ?

Conclusion:

*


### [[Feature #21615]](https://bugs.ruby-lang.org/issues/21615) Introduce `Array#values` (matheusrich)

- Seems reasonable to have an API to simplify traversal of hash/array values
- There is some precedence, since both `Hash`es/`Array`s have `#values_at`.
- If approved, should it be added to Set too?

Conclusion:

* matz: Hash and Array are not polymorphic. Will reject.

----

Ractor local GC

----

https://bugs.ruby-lang.org/issues/21616 date ライブラリを deprecated させたい

* akr: localtime, fixoff, utcの3モードがある。
    * RDBからtimezoneのない、DateやTime型が返ってくる
    * Pythonだとnaiveとawareがある
    * Pythonでは常にawareを使えばよい
* naruse: JavaやJavaScript (Temporal) ではUTCモード (Instant) のものをその用途に使っている
    * https://github.com/ruby/ruby/blob/3b190855ba965c179693a5baf25f365d9d445c09/timev.h#L20 実体としてはtzmode
* nobu: UTCモードかはTime#utc?で判別出来ると見せかけて、offsetが0でも真を返すのは注意
* akr: もともとはlocaltimeとgmtモード(後にutc)だったが、後にfixoffを加えた
* https://www.rfc-editor.org/rfc/rfc9557#section-2.2
* matsuda: いったんはRailsは気にせず進めて頂ければ

https://bugs.ruby-lang.org/issues/21573 Simpler syntax errors

current:

```
$ echo -e "1 +x 2 do\ndo" | ./miniruby -wc
-:1: warning: '+' after local variable or literal is interpreted as binary operator even though it seems like unary operator
-:1: warning: possibly useless use of + in void context
-:1: warning: possibly useless use of a literal in void context
./miniruby: -:1: syntax errors found (SyntaxError)
> 1 | 1 +x 2 do
    |      ^ unexpected integer, expecting end-of-input
    |        ^~ unexpected 'do', ignoring it
    |        ^~ unexpected 'do', expecting end-of-input
> 2 | do
    | ^~ unexpected 'do', ignoring it
```

The old style that flycheck (Emacs syntax check plugin for Ruby) supports
```
$ echo -e "1 +x 2 do\ndo" | RBENV_VERSION=3.3.1 ruby -wc
-:1: warning: `+' after local variable or literal is interpreted as binary operator
-:1: warning: even though it seems like unary operator
-:1: syntax error, unexpected integer literal, expecting `do' or '{' or '('
1 +x _2_ do
-: compile error (SyntaxError)
```

The new style proposed by kddnewton
```
$ echo -e "1 +x 2 do\ndo" | SIMPLE_ERROR=1 ./miniruby -wc
./miniruby: -:1: syntax errors found (SyntaxError)
-:1:5: unexpected integer, expecting end-of-input
-:1:7: unexpected 'do', expecting end-of-input
-:1:7: unexpected 'do', ignoring it
-:2:0: unexpected 'do', ignoring it
```

https://bugs.ruby-lang.org/issues/21168 `a[cmd 1, 2 do end]` and `a[cmd 1, 2 do end] = 3`

(timeout)

https://bugs.ruby-lang.org/issues/21552 allow `String.strip` and similar to take a parameter similar to `String.delete`
```ruby
"fooab".chomp("ab") #=> "foo"
"fooba".chomp("ab") #=> "fooba"

"fooab".strip("ab") #=> "foo"
"fooba".strip("ab") #=> "foo"
```

https://bugs.ruby-lang.org/issues/7845 Strip doesn't handle unicode space characters in ruby 1.9.2 & 1.9.3 (does in 1.9.1)
https://bugs.ruby-lang.org/issues/21554 Which `make` should be supported?
https://bugs.ruby-lang.org/issues/20437 Could the licensing conditions be made less ambiguous?
https://bugs.ruby-lang.org/issues/21625 Allow IO#wait_readable together with IO#ungetc
https://bugs.ruby-lang.org/issues/21634 Combining read(1) with eof? causes dropout of results unexpectedly on Windows
https://bugs.ruby-lang.org/issues/21619 logger: Context API
https://bugs.ruby-lang.org/issues/21617 Add Internationalized Domain Name (IDN) support to URI

https://bugs.ruby-lang.org/issues/21520 Feature Proposal: Enumerator::Lazy#tee
https://bugs.ruby-lang.org/issues/17316 On memoization

```ruby
instance_variable_set_unless_defined(:@foo) do
  long_calc
end
instance_variable_set_if_absent(:@foo) do
  long_calc
end
```

akr: now because of object shape, "assign if absent" pattern is not a fast style.

https://bugs.ruby-lang.org/issues/12282 `Hash#dig!` for repeated applications of Hash#fetch
https://bugs.ruby-lang.org/issues/21545 `#try_dig`, a dig that returns early if it cannot dig deeper
https://bugs.ruby-lang.org/issues/21551 Ractor isolation error points to the wrong place
https://bugs.ruby-lang.org/issues/21564 Extend `permutation`, `repeated_permutation`, `combination` and `repeated_combination` arguments

```ruby
# find right combination of letters
def brute_force
  [*'a'..'z'].repeated_permutation(*3..6).find do |chars|
    correct_combination?(chars)
  end
end

(3..6).flat_map { [*'a'...'z'].repeated_permutation(it).to_a }.find { ... }

def brute_force
  (3..6).each do |n|
    [*'a'..'z'].repeated_permutation(n) do |chars|
      return chars if correct_combination?(chars)
    end
  end
end
```

---

https://bugs.ruby-lang.org/issues/21622 Prism wrongly accepts command call to be a key of keyword argument
https://bugs.ruby-lang.org/issues/21618 Allow to use the build-in prism version to parse code
https://bugs.ruby-lang.org/issues/21630 Suggest @Earlopain for core contributor
https://bugs.ruby-lang.org/issues/15590 Add dups to Array to find duplicates
http://bugs.ruby-lang.org/issues/21446 StackOverflow when changing visibility in reopened refinement
https://bugs.ruby-lang.org/issues/21637 Tracing global variable assignment
https://bugs.ruby-lang.org/issues/21646 Propose Luke Gruber as a Ruby committer
https://bugs.ruby-lang.org/issues/21642 Introduce `IO::ConnectionResetError` and `IO::BrokenPipeError` as standardized IO-level exceptions
https://bugs.ruby-lang.org/issues/19017 Net::HTTP may block when attempting to reuse a persistent connection
