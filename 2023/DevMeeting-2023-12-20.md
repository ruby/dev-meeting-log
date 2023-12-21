# DevMeeting-2023-12-20

https://bugs.ruby-lang.org/issues/20046

## DateTime and location

* 2023/12/20 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2024/01/17 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #13383]](https://bugs.ruby-lang.org/issues/13383) Module#source_location (matheusrich)

* This feature feels very natural to me (when debugging)
* Given that we have `Method#source_location`, it was surprising that Module#source_locations isn't implemented yet.
* Since classes can be reopened, maybe it should be in the plural `Module#source_locations`?
* What can we do to move this forward?

Preliminary discussion:

```ruby=
class A::B::C
end

class A
    class B
        class C
        end
    end
end
```

Discussion:

* matz: I don't see what is the use case
* matz: If we introduce this, one may want all source_locations for any object. Is it reasonable?
* matz: I don't think it is needed

Conclusion:

* matz: Rejected because the use case is not clear.


### [[Bug #19683]](https://bugs.ruby-lang.org/issues/19683) ruby-3.3.0-preview1 does not build with BSD make without --with-baseruby (jeremyevans0)

* This has not been fixed as of ruby-3.3.0-rc1, and should be fixed before the final release of Ruby 3.3.0.

Discussion:

* nobu: jeremy created a patch: https://github.com/ruby/ruby/pull/9299
* mame: polyglot is interesting

Conclusion:

*

### Please take over rubylang/ruby docker maintenance (mrkn)

https://github.com/ruby/ruby-docker-images

I apologize for being unable to attend the meeting. Here is the explanation of the reason of this: Due to my current professional commitments, I find myself unable to dedicate sufficient time to the ruby/ruby-docker-images project. My job doesn't involve working with Ruby, and my personal time to contribute to Ruby-related projects is limited. Consequently, it's impractical for me to simultaneously contribute to both the bigdecimal and ruby-docker-images projects. My interest primarily lies in bigdecimal and Ruby's number system, rather than in the maintenance of docker images. Therefore, I am looking to pass on the responsibility of maintaining the ruby-docker-images project to someone else.

* hsbt: I will do

### [[Feature #20053]](https://bugs.ruby-lang.org/issues/20053) M:N Threads, now w/ macOS support (kqueue) (ko1)

- It's already December.  Should we merge this?

----

- matz: How mature is M:N?
- ko1: works when disabled...
- matz: That's normal.
- mame: :grin: not so much.
- ko1: ... and works for me when enabled.
- shyouhei: is this patch seriously tested on FreeBSD?
- matz: I think it's okay if it works, but it's up to naruse when we should merge this.
- ko1: Just tested this on a FreeBSD box and it needs a tweak.  But seems basically okay.
- naruse: Let's make it a try then.

### TODOs for Ruby 3.3

* soutaro: bundle RBS 3.4

### katei's playground

Katei created a way to compile a wasm ruby per pull request to ruby/ruby.  Let us review.

### katei: simplify the build system

* looks like that it is unnecessarily complicated

## Towards Ruby 3.4

### [[Feature #19117]](https://bugs.ruby-lang.org/issues/19117) Include the method owner in backtraces, not just the method name (mame)

* `Object#<main>`, `Object#block in foo`
* Should we also modify the result of `Kernel#caller`?
* Should we add `Thread::Backtrace::Location#self`?
  * ko1: I don't think so. `Thread::Backtrace::Location#class_string` or `method_prefix` or something
* Included methods and extended methods?

```ruby
def foo
  pp caller(0)
  pp caller_locations(0)
  raise
end

foo
#=> ["t.rb:3:in `foo'", "t.rb:6:in `<main>'"]
#   ["t.rb:3:in `main.foo'", "t.rb:6:in `<main>'"]
```

```
["t.rb:3:in `Object#foo'",
 "t.rb:8:in `block in foo'",
 "<internal:numeric>:237:in `Integer#times'",
 "t.rb:7:in `<main>'"]

["t.rb:3:in `Object#foo'",
 "t.rb:8:in `block in foo'",
 "t.rb:7:in `Enumerable#map",
 "t.rb:7:in `<main>'"]
```

```ruby
class C
  def foo
    bar
  end
end

o = C.new
def o.bar
  debugger do: 'bt'
#(rdbg:#debugger) bt
#=>#0    #<C:0x00007f1b5229d140>.bar at #target.rb:10
#  #1    C#foo at target.rb:4
#  #2    <main> at target.rb:13
end

o.foo
```

### Namespace

```ruby!
ns1 = NameSpace.new
ns1.require("foo", "1.0.0")
foo1 = ns1::Foo.new

ns2 = NameSpace.new
ns2.require("foo", "2.0.0")
foo2 = ns2::Foo.new
```

### random topics

* should we remove ABI version?
  * https://github.com/ruby/ruby/pull/8867

* https://bugs.ruby-lang.org/issues/20069 Buffer class in stdlib

* https://bugs.ruby-lang.org/issues/20063 Inconsistent behavior with required vs optional parameters
  * matz: I don't care which. I don't want any overhead to make it consistent

* https://bugs.ruby-lang.org/issues/20041 Array destructuring and default values in parameters
  * matz: The same as above

* https://bugs.ruby-lang.org/issues/20064 Inconsistent behavior between array splat *nil and hash splat **nil
  * matz: The use case example convinced me. Accepted.

* https://bugs.ruby-lang.org/issues/20054 Replace the use of `def` in endless method definitions with a new sigil
  * matz: Rejected

* https://bugs.ruby-lang.org/issues/20049 Destructive drop_while for Array and Hash
  * matz: The use case is not clear to me, especially for Hash
  * matz: It is possible to add Array#drop_while!
  * mame: Should we also add Array#take_while!
  * matz: ... I don't think so
  * mame: Will we introduce it for Ruby 3.3?
  * matz: It is too late for Ruby 3.3.
  * matz: Anyway I want to ask the use case before I accept it

* https://bugs.ruby-lang.org/issues/20038 Strings with mixed escapes not detected around interpolation

* https://bugs.ruby-lang.org/issues/20031 Regexp using greedy quantifier and unions on a big string uses a lot of memory
  * mame: If anyone create a patch to optimize it, we may consider. I will set the ticket as Feedback
  * naruse: there is an algorithm to search multple pattern search https://github.com/unruledboy/WuManber

```ruby!
str = "foo" + "x" * 100000000
str =~ /.*(foo|bar)/
str =~ /.*foo/ # this is fast thanks to the optimization
```

```
$ time ruby -e 'str = "foo" + "x" * 100000000; str =~ /.*(foo|bar)/'

real    0m2.921s
user    0m1.335s
sys     0m1.587s
```

* https://bugs.ruby-lang.org/issues/20029 coroutine/arm64/Context.S does not support PAC/BTI

* https://bugs.ruby-lang.org/issues/20027 Add Range Deconstruction
  * matz: Rejected

* https://bugs.ruby-lang.org/issues/18576 Rename `ASCII-8BIT` encoding to `BINARY`
