---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210615Japan

https://bugs.ruby-lang.org/issues/17886

## DateTime and location

* 6/17 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 7/15 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Tickets

### [[Misc #17888]](https://bugs.ruby-lang.org/issues/17888) "What's Here" section for class and module documentation (BurdetteLamar). (burdettelamar@yahoo.com)

* I feel this section is very useful; some have reservations; let's discuss whether and, if so, how.

Preliminary discussion:

* nobu: it is good for rdoc to provide a summary feature for this
* mame: agreed, but as a current measure, writing them manually is only way
* mame: as long as burdette maintains it, I think it is okay. Even if he may be inactive in future and the summary is not maintained, we can just remove it

Discussion:

* usa: This is very nice to have, at the same time very hard to maintain.
* usa: Why doesn't rdoc generate TOC by default?
* akr: Smalltalk has "category" feature, which seems nice to have.
* aycabta: RDoc has a concept called categories/sections, don't know if that is what is needed here though.
* mame: That said what about this proposal?
* mame: The summary is good to have. Until rdoc implements the new category feature, I'm personally okay to keep the summary manually written.
* knu: Does yard have features like it?
* (seems nobody knows)
* knu: We may want to survey, to avoid potential naming conflicts.

Conclusion:

* go ahead.
* aycabta wants to research existing RDoc features.


### [[Bug #17887]](https://bugs.ruby-lang.org/issues/17887) Missed constant lookup after `prepend` (eregon)

* Is it a bug? (I think so, feels inconsistent and makes `prepend` useless for constants) Should it be fixed? How about Jeremy's PR?

Preliminary discussion:

* mame: I am not negative against changing the behavior, but I am just afraid if it will break some practical programs and/or libraries
* nobu: we should try it as soon as possible (before the release)

Discussion:

```ruby=
module M
  FOO = 'm'
end
class A
  FOO = 'a'
  prepend M
end
class B < A
  p FOO #=> actual: 'a', expected: 'm'
end
```

* mame: `prepend` acts as `include` when it comes to constant lookup.
* matz: I expect `m` here.
* akr: What happens when super class looks for a prepended constant?
* nobu: that must not affect.
* akr: I think constant is not lazy binding? No?

```ruby=
class A
  FOO = :A
end
class A
  def foo
    FOO
  end
end
class B < A
  FOO = :B
end
p A.new.foo #=> :A
p B.new.foo #=> :A
```

* akr: `prepend` also shall not affect super class?
* akr: Hm.  I misunderstood the issue.  It inserts M between B and A.
* ko1: In this case lexical scope wins.  This is irrelevant.
* mame: What should the following code print?

```ruby=
module M
  FOO = 'm'
end
class A
  FOO = 'a'
  prepend M
  p FOO #=> "a"
end
class A
  p FOO #=> "a"
end
```

* (some lecture about Ruby's constant lookup)
* mame: It first searches the current scope (note that open class restores lexical scope), then the outer scopes. If no constant definition is found, then it traverses the inheritance relations.

* matz: I kind of understand when `prepend` does not take effect for `B`, but I think `prepend` in `A` shall be visible from `B`.
* mame: I guess this breaks things?
* matz: I feel this is too strange not to fix.
* nobu: `NEWS` shall also be fixed then.

```ruby=
module X
  FOO = :X
  class C
    FOO = :C
  end

  class C
    def foo
      p FOO
    end
  end
end

X::C.new.foo #=> :C
```

```ruby=
module X
  FOO = :X
  class C
    FOO = :C
  end

  class D < C
    def foo
      p FOO
    end
  end
end

X::D.new.foo #=> :X
```

Conclusion:

* matz: This is a bug.  To be fixed.
* nobu: I reviewed the patch

### [[Feature #17930]](https://bugs.ruby-lang.org/issues/17930) Add column information into error backtrace (mame)

* I've resurrected @yui-knk's patch. What do you think? Is the memory consumption acceptable?

Preliminary discussion:

* mame's proposal
  * The core will provide `RubyVM::AST.of(Thread::Backtrace::Location)`
    * https://github.com/ruby/ruby/pull/4558
  * The core will have a new (CRuby-specific) default gem to extend `NoMethodError#message`
    * https://github.com/ruby/did_you_mean/pull/151
    * did_you_mean is not a good place to do that, so I will create a new gem

* How to draw an underline
  * `^^^^^`
  * Alternative: Terminal escape sequence?
    * Can't copy-and-paste
    * We need to modify `Exception#message` because it escapes escape characters


Discussion:

* mame: This increases Rails' memory from 97MB to 100MB.
* ko1: You cannot trurn this off. Is this memory blow acceptabe?
* nobu: `--disable=did_you_mean` doesn't help?
* ko1: No.  Memory allocations happen before that.
* shyouhei: I understand jeremy's position.  We should not break JRubu only becasue we want cool backtraces.
* mame: `did_you_mean` has to check this feature either way and fall back to older behaviour.  This must not break existing implemntations.

Conclusion:

* matz: accepted.


### [[Feature #12913]](https://bugs.ruby-lang.org/issues/12913) A way to configure the default maximum width of `pp` (mame)

* How about `pp(obj, pp_out: $stderr, pp_width: 100)`?

Discussion:

* mame: `pp` takes arbitrary arguments so it must not "eat" any parameters, even if it is a keyword.
* ko1: I can compromise if `pp`'s own keyword has proper namespaces, like `:pp_width`
* akr: Why not let people use `PP.pp`, which accepts only one arguent?

```ruby=
pp([1,2,3], width: 1)
#=> [1, 2, 3]
#=> {:width=>1}
pp([1,2,3], pp_width: 1)
#=> [1,
#    2,
#    3]
```

* akr: it seems @mame's initial intention was to set width to 1, or to fold as much as possible.
* shyouhei: Why not add keyword arguments to `PP.pp`?
* usa: I want shortter name than `PP.pp(..., looooong_keyword: ...)`
* akr: There is `PP.singleline_pp` so there could be another method.

* mame: How about changing the default width to io/console size?
* akr: Looks good to me

Conclusion:

* mame: let's change the default size
* mame: continue to discuss a new method `PP.expand_as_possilbe` or something, and extend `PP.pp` with keywords in this ticket


### [[Bug #17561]](https://bugs.ruby-lang.org/issues/17561) The timeout option for `Addrinfo.getaddrinfo` is not reliable on Ruby 2.7.2 (jeremyevans0)

* Usage of getaddrinfo_a was reverted before 3.0, but still causes problems in 2.7.
* Do we want to revert commit 0e9d56f5e73ed2fd8e7c858fdea7b7d5b905bb64 in 2.7.4?
c4efbf663ea1849986383ca97bfc8c2609142c68 test failed on Solaris

Preliminary discussion:

* ko1: tell to glass_saga

Discussion:

* akr: Must explicitly document that this is glibc specific.
* usa: We don't want `ArgumentError`.  Simple revert is not enough.

Conclusion:

* make: usa-san please reply as a branch maintainer


### [[Bug #17098]](https://bugs.ruby-lang.org/issues/17098) `Float#negative?` reports negative zero as not negative (jeremyevans0)

* I do not think this is a bug.  0 is not positive, so -0 should not be negative.
* Can we close this?

Preliminary discussion:

* ko1: tell to mrkn

Discussion:

* akr: I don't think so.
* mame: me neither.
* akr: `+0` and `-0` shall not behave differently without a very good reason to do so.
* mame: should `+0.0.positive?` return true? I don't think so

Conclusion:

* matz: will reply


### [[Bug #16951]](https://bugs.ruby-lang.org/issues/16951) Consistently referer dependencies (jeremyevans0)

* How do we want to handle dependencies in default and bundled gems?
* My opinion is that bundled gems and default gems do not need dependencies on default gems.
* Once a gem is moved from default gem to bundled gem, then other bundled gems that need it should add it as a dependency.

Preliminary discussion:

* mame: I have no idea what this ticket wants us to do.
* nobu: Fill dependencies (`add_dependency`) in default gemspec files?

Discussion:

* usa: What does the OP want in short?
* mame: Not obvious...
* akr: It seems he want rubygems existed in 1.6.
* usa: We follow current best practices, which change over time.  The changes themselves were right decisions I believe.
* mame: Now @hsbt-san is reducing default gems gradually, so I guess it will be solved in (near) future

Conclusion:

* mame: leave it to hsbt


### [[Feature #16962]](https://bugs.ruby-lang.org/issues/16962) Make `IO.for_fd` `autoclose` option default to `false` (jeremyevans0)

* Do we want to change the `IO.for_fd` `autoclose` option to default to `false`?

Preliminary discussion:

* mame: The change is potentially dangerous; it will make a valid program leak a file descriptor. So if we change it, we need a good reason
* mame: As far as I know, the main use case of `IO.for_fd` is a command that supposed to be invoked with some fixed file descriptor open (for example, `valgrind --log-fd=num`). For this use case, `autoclose` default does not matter

Discussion:

* mame: `IO.for_fd` closes by default.
* akr: `0`, `1`, `2` are special cased. Closing them may cause stdin/out/error, which is known as leading to possible vulnerability
* mame: Yes, samuel confuses that.
* akr: It would be okay if this doesn't break backwards compatibility.
* shyouhei: Doesn't it set `autoclose` on purpose?
* akr: My understanding is that `IO.for_fd` _always_ auto-closed before.  This can be dangerous.  We added `autoclose` flag later.
* mame: Flipping this flag to leave FDs open is dangerous than flipping to the opposite side.
* akr: I remember that when `IO.for_fd` creates an `IO` object for the first time for that descriptor, it is safer to auto-close it than to leak.  However when a program extracts a descriptor from an existing `IO` object and pass it to `IO.for_fd`, that can avoid closing.  This situation happens mainly when testing something.
* akr: Samuel says that "for most case" default should be false but is it?

Conclusion:

1. It is necessary to leave `0`, `1`, `2` open.
2. "For most case" situations shall be described more because we think otherwise.
3. There must be a very good reason to flip the default in spite of expected compatibility issues.


### [[Bug #16835]](https://bugs.ruby-lang.org/issues/16835) `SIGCHLD` + `system` new behavior on 2.6 (jeremyevans0)

* I don't think the new behavior is a bug, though it doesn't appear the behavior change was intended.
* Is this a bug?
* If not, and the new behavior is expected, can this be closed?

Preliminary discussion:

```
$ ./all-ruby -e 'Signal.trap("CLD")  { puts "Child died" }; system("true")
...
ruby-1.6.0            -e:1: uninitialized constant Signal (NameError)
                  exit 1
...
ruby-1.6.8            -e:1: uninitialized constant Signal (NameError)
                  exit 1
ruby-1.8.0            Child died
...
ruby-1.8.5-preview2   Child died
ruby-1.8.5-preview3
...
ruby-2.6.0-preview2
ruby-2.6.0-preview3   Child died
...
ruby-3.0.0            Child died
```


* `man system`

> During  execution of the command, SIGCHLD will be blocked, and SIGINT and SIGQUIT will be ignored, in the process that calls system().  (These signals will be handled according to their defaults inside the child process that executes command.)

```ruby=
trap(:INT){
  p [Process.pid, :parent_INT]
}

system("ruby -e 'trap(:INT){ p [Process.pid, :child_INT]}; gets'")

__END__

$ ruby ~/src/rb/t.rb
^C[18046, :child_INT][18017, :parent_INT]
```


```ruby=
# only parent died with C-c
system("ruby -e 'trap(:INT){ p [Process.pid, :child_INT]}; gets'")

__END__

$ ruby ~/src/rb/t.rb
^CTraceback (most recent call last):
[18109, :child_INT]
        1: from /home/ko1/src/rb/t.rb:1:in `<main>'
/home/ko1/src/rb/t.rb:1:in `system': Interrupt


$ Traceback (most recent call last):
        2: from -e:1:in `<main>'
        1: from -e:1:in `gets'
-e:1:in `gets': Input/output error @ io_fillbuf - fd:0 <STDIN> (Errno::EIO)
s ^C
```

Discussion:

* usa: This is at least unintended.
* nobu: But does anyone care?
* usa: The reporter does.
* mame: This sounds OS/libc dependent.
* nobu: This area is largely undocumentd.
* usa: We don't document whether a particular signal is delivered or blocked at particular moment.
* ko1: manpage of `system(3)` says it doesn't deliver signals to the parent.
* akr: C's and Ruby's usages of `system` are different.  Consider `vi` to spawn a process.  When a user pressing `^C`, the user doesn't expect `vi` gets killed along with the spawned subprocess.  However when ruby scripts accept `^C`, it is highly expected that ruby itself and its children shall immediately terminate.
* mame: I vote for "no change" weakly
* usa: agreed

Conclusion:

* mame: no one has an opinion, so no change

### [[Bug #17763]](https://bugs.ruby-lang.org/issues/17763) Implement cache for cvars (tenderlove)

Discussion:

* usa: How fast is it?
* mame: It gets more and more faster when there are more and more modules.
* ko1: Rails uses cvars very much.
* ko1: I assumed that CVars is not recommended now a day. Can we encourage to use it by improving the performance?
    * matz: I'm against complaining optimisations "encourage bad things".
    * usa: I like class variables.
    * mame: They are complex when inherited.
* ko1: This code is difficult to maintain.
    * matz: You should complain about that.
    * ko1: This patch changes the entire cvar structure.
* akira: If you hesitate use of class variables, I think rails can use class' instance variable instead of class variables.

Conclusion:

* matz; go ahead.

## Other topics

### save_script_lines (mame)

https://github.com/ruby/ruby/commit/f1ca7f30f14106f384c2f8e9fb2ba9b46558f75d

```ruby
$ cat tt.rb
iseq = RubyVM::InstructionSequence.compile(<<END, save_script_lines: true)
foo(<<E1) + bar(<<E2)
FOO
E1
BAR
E2
END

p iseq.script_lines
p iseq.source

$ ./miniruby tt.rb
["foo(<<E1) + bar(<<E2)\n", "FOO\n", "E1\n", "BAR\n", "E2\n"]
"foo(<<E1) + bar(<<E2)"
```

```ruby
iseq = RubyVM::InstructionSequence.compile(<<END, save_script_lines: true)
foo(<<E1
FOO
E1
)
END

p iseq.script_lines #=> ["foo(<<E1\n", "FOO\n", "E1\n", ")\n"]
p iseq.source #=> "foo(<<E1\nFOO\nE1\n)"
```

```ruby=
def foo; <<E1 ; end; def bar; <<E2 ; end
  p 1
E1
  p 2
E2
```

```ruby
# for error_squiggle
RubyVM::AST.of(save_script_lines: true)
RubyVM::AST.parse(save_script_lines: true)

# for ko1's debug
RubyVM::ISeq.save_script_lines = true
```

* matz: This is just an implementation detail but how about compressing the source text?
* mame: It is possible and looks good to me

* ko1: RubyVM::ISeq.dump and load drops script_lines
* naruse: If bootsnap authors want it, we may make the text in the dump

* matz: accepted

### [Prefix ccan headers](https://github.com/ruby/ruby/pull/4568)

* mame: it makes it hard to sync to the upstream... Not so objective though