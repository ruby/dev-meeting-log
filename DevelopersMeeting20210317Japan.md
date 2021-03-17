---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210317Japan

https://bugs.ruby-lang.org/issues/17635

## DateTime and location

* 3/17 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 4/16 (Fri) 13:00-17:00 JST @ Online
  * pre-reading: 4/14 (Wed)

## Announce

## About 3.0/3.1 timeframe

* 3.0.1?

## Check security tickets

[secret]

## Tickets

### [[Misc #17662]](https://bugs.ruby-lang.org/issues/17662) The heredoc pattern used in tests does not syntax highlight correctly in many editors (eregon)

* Can we use something else in tests?

Preliminary discussion:

```ruby
<<-RUBY
  ruby.code.here
RUBY
```

* `<<-ruby` example:
https://github.com/ruby/ruby/blob/48b94b791997881929c739c64f95ac30f3fd0bb9/spec/ruby/core/proc/arity_spec.rb#L24

Rubymine screenshot:

![](https://i.imgur.com/FUpmpIe.png)


Discussion:

* mame: nobu is VIP in this topic
* ko1: `<<-RUBY` is preferable? Many people are using it?
* akr: `<<-RUBY` looks good
* mame, knu, naruse: some editors actually support the syntax highlighting of the style
* ko1: can we ignore emacs?
* mame: nobu uses emacs
* nobu: will try implementing `<<-RUBY` support in emacs

Conclusion:

* nobu: I'll consider to implement `<<~RUBY` in ruby-mode.el and transfer to it.


### [[Feature #15752]](https://bugs.ruby-lang.org/issues/15752) A dedicated module for experimental features (eregon)

* I'd like to finish this discussion and then we can close it.
* matz said "we need to rewrite our programs when the feature graduated from the experimental state" but this is already an issue for `RubyVM`, so I don't understand this argument.
* My feeling is ruby-core is not going to change anything here. That's OK, but be aware that `RubyVM` will become *de facto* no longer experimental and no longer MRI-specific.
* If so, I think we should update the docs to say `RubyVM` is just "a module about VM-related features".

Conclusion:

* naruse: We should use no more time for this topic


### [[Bug #11335]](https://bugs.ruby-lang.org/issues/11335) `ruby -r debug` catchpoint problem (jeremyevans0)

* This support is broken since Ruby 2.0.
* The debug library has no tests, and I'm guessing minimal usage.
* Can we remove debug from stdlib in Ruby 3.1?
* Alternatively, do we want to rewrite debug to use `TracePoint` instead of `set_trace_func`?

Preliminary discussion:

* ko1: I'm rewriting the debug.rb.

Conclusion:

* closed.


### [[Bug #17354]](https://bugs.ruby-lang.org/issues/17354) `Module#const_source_location` is misleading for constants awaiting `autoload` (jeremyevans0)

* Currently, `Module#const_source_location` returns `autoload` location and not definition location, which seems wrong.
* It could return `[]`, similar to constants defined in C where we don't know the location.
* It could trigger the `autoload`, and then return the definition location.
* Which behavior is desired for `Module#const_source_location`?
* Related, should `Module#const_defined?` trigger `autoload` instead of assuming the constant is defined?

Preliminary discussion:

* mame: I don't see what is a problem currently. Will reply.

Discussion:

* ko1: I don't see any advantage of omitting information. `nil` and `[]` does not make sense.
* akr: loading libraries makes it consistent.

Conclusion:

* pending


### [[Bug #17164]](https://bugs.ruby-lang.org/issues/17164) Threads can ignore kill/interrupt/abort (jeremyevans0)

* Basically, use of ensure/raise/rescue can make ruby unkillable without `kill -9`.
* I think this behavior is expected.  Can we confirm that?

Preliminary discussion:

* ko1: I agree it is expected behavior.
* mame: It is not intuitive to me that `Thread#kill` is rescue'able, but it is also troublesome if `Thread#kill` skips the `ensure` clause
* ko1: easy to reproduce.

```ruby=
Thread.new do
  loop do
    begin
      sleep
    ensure
      raise
    end
  rescue
    p :retry
  end
end

sleep 0.5
puts :terminate
```

Conclusion:

* matz: it is intentional. I will reply.


### [[Bug #16996]](https://bugs.ruby-lang.org/issues/16996) Hash should avoid doing unnecessary rehash (jeremyevans0)

* High performance code generally works around this by using `Hash.[]`.
* Can we remove the rehashing in `Hash#dup` and `#clone`?

Preliminary discussion:

* mame: sounds good if it passes all tests

https://github.com/ruby/ruby/blob/04eb7c7e462d1fcbab9efc7022c1b43636ab878a/test/test_set.rb#L621-L625
```ruby=
    set1 = Class.new(Set)["a", "b"]
    set2 = Set["a", "b", set1]
    set1 = set1.add(set1.clone)

    assert_equal(set2, set2.clone)
```

* http://blade.nagaokaut.ac.jp/cgi-bin/vframe.rb/ruby/ruby-core/48040?47945-48527 Usa: ""*I* expect that dup will rehash.""

* knu: I can drop the spec from Set.  Will add a reply.

Discussion:

* nobody has objection.

Conclusion:

* mame: go ahead. I think it is an implementation detail, not language spec. Leave it to nobu or Jeremy


### [[Bug #16908]](https://bugs.ruby-lang.org/issues/16908) Strange behaviour of `Hash#shift` when used with `default_proc`. (jeremyevans0)

* Currently, `Hash#shift` calls the default proc with `nil` if the hash is empty and the hash has a default proc, and returns only the value.
* This is similar to when the hash has a default value, where only the default value is returned if the hash is empty.
* I think this behavior is expected.  Can we confirm that?

Preliminary discussion:

*
```ruby=
hash = Hash.new{|h, k|
  p [h, k] #=> [{}, nil]
  h[k] = 0
}

p hash.shift # => 0
p hash.shift # => [nil, 0]
p hash.shift # => 0
p hash.shift # => [nil, 0]
p hash.shift # => 0
p hash.shift # => [nil, 0]
```

* Options
    * (1) current behavior
    * (2) always `nil` for empty Hash (doesn't call `default_proc`)
    * (3) call `default_proc` and call `Hash#shift` again for modified Hash.

* nobu: How about default, not `default_proc`?

```ruby=
p Hash.new(0).shift #=> 0 (1)
p Hash.new(0).shift #=> [nil, 0] (3)
```

```ruby=
h = { a: 1, b: 2 }; h.shift #=> [:a, 1]
h = Hash.new(1);    h.shift #=> 1
```

```ruby=
h = Hash.new{|h, k| h[k] = 1; 2}
p h[:a] #=> 2
p h[:a] #=> 1
```

```shell=
$ ./all-ruby -e 'h = Hash.new(0); p h.shift'
ruby-0.99.4-961224    nil
...
ruby-1.4.6            nil
ruby-1.6.0            0 # since 1.5.0:de7161526014
...
ruby-3.0.0            0
```

```ruby=
h = Hash.new{|h, k| h[k] = 0}
4.times{p h.shift}
#=>
# 0
# [nil, 0]
# 0
# [nil, 0]
```

```c=
/*
 *  call-seq:
 *    hash.shift -> [key, value] or default_value
 *
 *  Removes the first hash entry
 *  (see {Entry Order}[#class-Hash-label-Entry+Order]);
 *  returns a 2-element \Array containing the removed key and value:
 *    h = {foo: 0, bar: 1, baz: 2}
 *    h.shift # => [:foo, 0]
 *    h # => {:bar=>1, :baz=>2}
 *
 *  Returns the default value if the hash is empty
 *  (see {Default Values}[#class-Hash-label-Default+Values]).
 */
```

```shell=
$ rbs method Hash shift
::Hash#shift
  defined_in: ::Hash
  implementation: ::Hash
  accessibility: public
  types:
      () -> [ K, V ]?
```

* knu: `Hash#shift` is a lesser known feature and we can probably mark this as WONTFIX.  The document might have been based on the implementation rather than writing up a specification.

Discussion:

* matz: I want to prohibit calling #shift against a hash with default or default_proc in future, but I'm worried about the compatibility
* mame: how about a warning?
* akr: I'm against a warning
* mame: it is a kind of deprecation
* knu: How about type/RBS?
* mame, soutaro: there is two problems: (1) the `k` of `Hash.new {|h, k|` may be an optional type, (2) `Hash#shift` returns `[K, V] | V`
* matz, knu: (A talk about Perl's assoc list)
* naruse: if it is changed, the change should be committed for Ruby 3.2

Conclusion:

* matz: Will reply.


### [[Feature #17684]](https://bugs.ruby-lang.org/issues/17684) Remove `--disable-gems` from release version of Ruby (naruse)

* Recently this increases the cost of the ecosystem


Discussion:

* nobu: objection. I use `--disable-gems` not only for debugging. At least it should be kept in master. I'm okay for removal from release tarball.
* naruse: Keep the same between release tarball and master. We should remove the option not only from tarball but also from master.
* hsbt: How about configure option to resurruct `--disable-gems`?
* knu: objection. Allowing the configuration will lead to many ruby executables, which is confusing.
* nobu: rubygems/bundler people should reject `--disable-gems` issues. Why do you remove it from ruby itself?
* akr: 0.2 sec to 2.0 sec in Raspberry Pi (tenderlove wrote it in the ticket) is not neglectable.
* nobu, naruse: (a talk about bundle standalone option)
* akr: it would be great if we can improve the startup performance with rubygems
* naruse: Delaying load of some of rubygems code may solve the issue?
* knu: I guess the performance issue is unsolvable
* naruse: I'm checking strace of rubygems, it looks like that it reads many unneeded files. First, rubygems should be improved. I'll paste the result
* shyouhei: hsbt, please reject `--disable-gems` issues for rubygems until the performance of rubygems is improved
* akr: it would be good to explicitly declare the purpose of --disable-gems as only for debugging.
* matz: I agree that this option is not performance, but for debugging. To make sure I don't against to remove this option.
* naruse: modify `--help` message to make sure it is for debugging?

```
diff --git a/ruby.c b/ruby.c
index 113e895bb2..4023177028 100644
--- a/ruby.c
+++ b/ruby.c
@@ -303,7 +303,7 @@ usage(const char *name, int help, int highlight, int columns)
 	M("--copyright",                            "", "print the copyright"),
 	M("--dump={insns|parsetree|...}[,...]",     "",
           "dump debug information. see below for available dump list"),
-	M("--enable={gems|rubyopt|...}[,...]", ", --disable={gems|rubyopt|...}[,...]",
+	M("--enable={jit|rubyopt|...}[,...]", ", --disable={jit|rubyopt|...}[,...]",
 	  "enable or disable features. see below for available features"),
 	M("--external-encoding=encoding",           ", --internal-encoding=encoding",
 	  "specify the default external or internal character encoding"),
@@ -319,7 +319,7 @@ usage(const char *name, int help, int highlight, int columns)
 	M("parsetree_with_comment", "", "AST with comments"),
     };
     static const struct message features[] = {
-	M("gems",    "",        "rubygems (default: "DEFAULT_RUBYGEMS_ENABLED")"),
+	M("gems",    "",        "rubygems (only for debugging, default: "DEFAULT_RUBYGEMS_ENABLED")"),
 	M("did_you_mean", "",   "did_you_mean (default: "DEFAULT_RUBYGEMS_ENABLED")"),
 	M("rubyopt", "",        "RUBYOPT environment variable (default: enabled)"),
 	M("frozen-string-literal", "", "freeze all string literals (default: disabled)"),

```

----

```
$ strace -o /tmp/x -e trace=openat ruby -e0
```
https://gist.github.com/ko1/f0d8c899d580f7f69bb5608ab6d8215f

Conclusion:

* As the first step, let's update the --help to explicitly state that --disable-gems is just for debugging
* If rubygems startup becomes so fast in future, we can consider removal of `--disable-gems` (but still it is desirable for debugging)
* Or someone who happen to report about --disable-gems again, we will consider again.

### [[Bug #16608]](https://bugs.ruby-lang.org/issues/16608) `ConditionVariable#wait` should return `false` when timeout exceeded (jeremyevans0)

* @nobu requested review of this during the developer meeting.
* Is it OK to make `ConditionVariable#wait` return `false` if timed out instead of always returning `true`?
* Is it OK to add `Mutex#sleep_for`, which returns `nil` if the sleeps times out?
* Alternatively, is it OK to change the behavior of `Mutex#sleep` to return `nil` if the sleep times out?

Preliminary discussion:

* `ConditionVariable` (in Subject) is not `MonitorMixin::ConditionVariable`

```shell=
% docker run -it --rm ghcr.io/ruby/all-ruby env ALL_RUBY_SINCE=ruby-1.9 ./all-ruby -e 'require "monitor"; p Monitor.new.new_cond.class; p ConditionVariable'
ruby-1.9.0-0        MonitorMixin::ConditionVariable
                    ConditionVariable
...
ruby-2.0.0-p648     MonitorMixin::ConditionVariable
                    ConditionVariable
ruby-2.1.0-preview1 MonitorMixin::ConditionVariable
                    Thread::ConditionVariable
...
ruby-3.0.0          MonitorMixin::ConditionVariable
                    Thread::ConditionVariable
```

* shugo removed timeout in 2007 (therefore, the method returned always true), and re-introduce timeout support (but the return value was kept always true)

```=
commit 332e8fe51f0ba4107c6fef4957ddeb15a0681dec
Author: shugo <shugo@b2dd03c8-39d4-4d8f-98ff-823fe69b080e>
Date:   Sat Feb 6 12:31:52 2010 +0000

    * lib/monitor.rb (wait): supported timeout.

commit 33a9e63ad9cbd673e84d0403fb7c72cdc9b1a616
Author: shugo <shugo@b2dd03c8-39d4-4d8f-98ff-823fe69b080e>
Date:   Sat Feb 24 06:15:04 2007 +0000

    * lib/monitor.rb: rewritten using Mutex/ConditionVariable.
```

* ko1: maybe nobody checks the return value (which is always `true`), so no problem.

```ruby=
m = Mutex.new

Thread.new{
  m.synchronize{
    Thread.main.wakeup
  }
}

m.synchronize{
  m.sleep 3
  # release mutex and sleep 3 seconds
  p :wakeup
}

# vs. CV
m = Mutex.new
cv = ConditionVariable.new

Thread.new{
  m.synchronize{
    cv.signal
  }
}

m.synchronize{
  cv.wait(m, 3)
  # release mutex and sleep 3 seconds
  p :wakeup
}
```

Discussion:

* (mame: failed to log)

Conclusion:

* matz: agreed to change the return value of `Mutex#sleep`.
* naruse: agreed to change in Ruby 3.1 because this is so detail

### [[Feature #17411]](https://bugs.ruby-lang.org/issues/17411) Allow expressions in pattern matching (nobu)

* nobu: (mame: failed to log)
* matz: go ahead


## Other topics

### lib/debug.rb

ko1: can I replace it without tests?
matz: confirmed.


### Any plan for 2.6.7?

soutaro: 2.6.6 ships with WEBrick 1.4.2 and a security scanner complains of it.
naruse: Please create a backport ticket


## Looking back on the previous meeting

https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20210216Japan.md

### [[Feature #14394]](https://bugs.ruby-lang.org/issues/14394) `Class.descendants` (ko1)

* ko1: will do


### [[Bug #17592]](https://bugs.ruby-lang.org/issues/17592) Allow reading class instance variables from non-main `Ractor` (marcandre)

* ko1: will do


## Other topics 2

### [[Bug #17723]](https://bugs.ruby-lang.org/issues/17723) autoconf 2.70+ is not working with master branch

`autogen.sh`
```shell=
#!/bin/sh
PWD=
case "$0" in
*/*) srcdir="${0%/*}/";;
*) srcdir="";;
esac
exec autoreconf -is ${srcdir:+"$srcdir"}
```

* README is already updated: https://github.com/ruby/ruby/commit/e271a3d4afc47e812b50fc9c50f6bf34d2d723a6
* "Use Automake" https://bugs.ruby-lang.org/issues/12124
* `autoreconf` is included in https://packages.debian.org/ja/buster/all/autoconf/filelist

```shell=
: ${AUTORECONF=`echo "${AUTOCONF-autoconf}" | sed 's/autoconf/autoreconf/'`}

${AUTORECONF:-autoreconf} --install $*
```

### [[Feature #17548]](https://bugs.ruby-lang.org/issues/17548) Need simple way to include symlink directories in `Dir.glob` (nobu)

* When a recursive symlink exists, zsh just emits an error message and ignores that path.
* In ruby if an exception raises, no result will be available at all.

```ruby=
Dir.glob("***/*.rb")
Dir.glob("***(2)/*.rb")
```

## Other topics 3

ko1: want to accumlate Enumerable#tally results

```ruby=
h = {}
[:a,:b,:c].tally(h)
[:a,:b,:d].tally(h)

p h #=> {:a=>2, :b=>2, :c=>1, :d=>1}
```

matz: looks good. please create a proposal
