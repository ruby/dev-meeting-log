---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevelopersMeeting20210521Japan

https://bugs.ruby-lang.org/issues/17811

## DateTime and location

* 5/21 (Fri) 13:00-17:00 JST @ Online

## Next Date

* 6/17 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Tickets

### [[Feature #16038]](https://bugs.ruby-lang.org/issues/16038) Provide a public `WeakMap` that compares by equality rather than by identity (byroot)

- Would allow user code to implement interned value objects, similar to `String#-@` but for arbitrary types.
- Was initially proposed about 2 years ago, but didn't reach a conclusion.

Preliminary discussion:

* ko1: We need to be careful to implement this feature. 
  * We need to exclude a dead key (an entry remains but it is determined to be sweeped). For compare_by_identity, it is safe to assume that a key is not GC'ed (unless, the key is never looked up).
  * Also, what if a GC occurs when checking the equality?

* ko1: The API is very arguable...

Discussion:

* ko1: The use case, for instance, is to implement a cache on top of it.
* matz: Is it okay to cache a value using a WeakMap?  Because GC can reclaim cached entries.
* mame: In Rails, can GC run very frequently (maybe per request)?
* usa: It highly depends on how big the Rails app is, but maybe no
* mame: If so, this proposal may work great to reuse objects across requests.
---
* mrkn: I use WeakMap in [pycall](https://github.com/mrkn/pycall.rb/blob/master/lib/pycall/wrapper_object_cache.rb#L55)
* matz: I understand if `_id2ref` suits a needs that can be rewritten using WeakRef.
* nobu: Does pycall compare by equality, like what is proposed here?
* mrkn: ...(looking at the source code) No, it compares by identity.
* mrkn: But I do against deprecating WeakMap.
* matz: We don't.
---
* mame: I understand the need that OP says, but I'm unsure if WeakMap is appropriate for the need. Instead, a dedicated cache library may be better for the use case.
* matz: ...(reading @byroot's example) this is not a "cache" I meant a few lines above, and indeed makes sense.
* Should there be a WeakSet?
* knu: I came up with the same idea as [nobu's](https://bugs.ruby-lang.org/issues/16038#note-8).

Conclusion:

* No conclusion, continue discussing it.
* matz: Will reply

### [[Bug #13876]](https://bugs.ruby-lang.org/issues/13876) Tempfile's finalizer can be interrupted by a Timeout exception which can cause the process to hang (jeremyevans0)

* Currently, exceptions can be raised during finalizer processing (in GC context), causing process hangs.
* Can we mark when a thread is running finalizers and not check for pending interrupts in that case, as the patch does?

Preliminary discussion:

* nobu: The pullreq looks Solution 2 instead of Solution 3 that Jeremy says. A signal like SIGINT is not blocked even in finalizers
* ko1: Anyway the proposed spec looks good (patch should be reviewed)
* mame: There are two problems. One is that a finalizer is unintentionally terminated by an asynchronous exception, and the other is that an asynchronous exception (i.e., Timeout exception) is implicitly ignored if it is raised in any finalizer

Discussion:

* mame: Timeouts can be "eaten" by finalisers.
* ko1: The proposal is to block interrupts while running them.
* nobu: Agree that finalsers should not be interrupted, generally speaking.
* ko1: The patch needs review though.
* akr: Doesn't SIGINT suffer the same problem?
* mame: I guess so.
* ko1: People can press ^C again then.
* akr: It is not a wise idea to issue IO from a finaliser but tempfile...  Do their finalisers block?  Guess yes when you close a NFS-backended file descriptor...
* nobu: Are you talking about timeout inside of a finaliser?
* ko1: This request is not about timeout inside of finaliser but outside of one.
* mame: What if exceptions inside of finalisers to propagate the main thread?
* ko1: Finalisers are not guaranteed to run on the main thread.
* akr: It is super dangerous to propagate arbitrary exceptions happen at sporadic point.

Conclusion:

* Patch needs improvements.
* There are concerns (what if a finaliser calls Timeout.timeout), but basically we should block.  Accepted.

### [[Feature #17849]](https://bugs.ruby-lang.org/issues/17849) Fix `Timeout.timeout` so that it can be used in threaded Web servers (duerst)

- Ruby is used a lot in Web servers and other places with Threads and potential timeouts.
- We should make sure we can provide the best possible timeout solution for these environments.

Preliminary discussion:

* mame: Heavy... Is it possible to implement automatic masking in each ensure clause efficiently?
* mame: Masking all asynchronous exception including signal will bring a trouble

Discussion:

* knu: It is very common for an application to send a log, message or metrics over the network in `ensure`  (APM, logger, Slack notification, etc.), and it is possible that you make use of Timeout inside there.
* mame: It is ultra-hard to handle exceptions that are asynchronous.
* ko1: Masking in `ensure` can solve some isssues. But I agree not complete solution.
* akr: Sounds it is not a wise idea to have per-request asynchronous timeout.  Timeouts should be explicit everywhere.
* usa: Agreed but not a practical idea.
* akr: Is there a way to somehow block timeouts?
* nobu: handle_interrupts
* ko1: NG in general, but OK on MRI.
    ```ruby=
    begin
      ...
    ensure
      # <- (1) here
      Thread.handle_interrupts (...) {
        ...
      }
    end
    ```
    Timeout scan happen at "(1) here" in general. Fortunately, on MRI it is okay if there is no method dipsatch other than `handle_interrupts`.

Conclusion:

* matz: will reply. did.

### [[Feature #17863]](https://bugs.ruby-lang.org/issues/17863) rewrite lib/debug.rb with latest API (ko1)

* Please confirm APIs (file name, method name, etc)

Preliminary discussion:

* mame: `nonstop`?

```
# byebug
    -s, --[no-]stop           Stop when script is loaded
```

Discussion:

* Topics
    * Can we introduce remote debugger features?
    * `binding.bp` method for breakpoint explicitly
    * naming issues
    * knu: I don't think `require "debug"` should open a console session because that would force users to put `gem "debug", require: false` in their Gemfile.
    * Call for security considerations with remote debugging features (Unix domain socket, TCP)
        * permissions with the domain socket file (and its enclosing directories)

Conclusion:

* Matz: accepted.

### [[Bug #17739]](https://bugs.ruby-lang.org/issues/17739) `Array#sort!` changes the order even if the receiver raises FrozenError in given block (jeremyevans0)

* Currently, freezing an array inside Array#sort! allows further changes to the array.
* Should we prevent further changes to the array by checking whether the array is frozen each time the block returns?

Preliminary discussion:

* mame: Nothing is guaranteed about what order sort! yields pairs. I think it is okay to change the order of comparison whenever sort! is called, so the original expectation seens wrong.
* mame: Jeremy's code is subtle. Even after the array is frozen, its contents change. I'm unsure if it is acceptable.

```ruby=
array = [1, 2, 3, 4, 5]
begin
  array.sort! do |a, b|
    array.freeze if a == 3
    b <=> a
  end
rescue => err
  p err #=> #<FrozenError: can't modify frozen Array: [5, 4, 3, 2, 1]>
end
p array

array = [1, 2, 3, 4, 5]
array.sort! do |a, b|
  break if a == 3
  b <=> a
end
p array

# ruby 3.1.0dev (2021-02-03T20:48:37Z :detached: 33d6e92e0c) [x64-mswin64_140]
#=> [5, 4, 3, 2, 1]
#=> [1, 2, 3, 4, 5]

# ruby 2.7.3p183 (2021-04-05 revision 6847ee089d) [x86_64-linux]
#=> [5, 4, 3, 2, 1]
#=> [2, 1, 3, 5, 4]
```


```ruby=
array = [1, 2, 3, 4, 5]
begin
  array.sort! do |a, b|
    array.freeze if a == 3
    p [array.frozen?, array]
    b <=> a
  end
rescue => err
  p err #=> #<FrozenError: can't modify frozen Array: [5, 4, 3, 2, 1]>
end

__END__

ruby 3.1.0dev (2021-02-03T20:48:37Z :detached: 33d6e92e0c) [x64-mswin64_140]
[false, [1, 2, 3, 4, 5]]
[true, [1, 2, 3, 4, 5]]
[true, [1, 2, 3, 4, 5]]
[true, [1, 2, 3, 4, 5]]
[true, [5, 2, 3, 4, 1]]
[true, [5, 2, 3, 4, 1]]
[true, [5, 2, 3, 4, 1]]
[true, [5, 4, 3, 2, 1]]
[true, [5, 4, 3, 2, 1]]
[true, [5, 4, 3, 2, 1]]
#<FrozenError: can't modify frozen Array: [5, 4, 3, 2, 1]>
```

* nobu: The same check should be inserted to not only sort_1 but also sort_2 . If the array in question is frozen in elements' <=>, the same issue occurs.

Discussion:

* mame: it is a pity that we need to add overhead for such pathological case, but it's Ruby.

Conclusion:

* matz: accepted


### [[Bug #17631]](https://bugs.ruby-lang.org/issues/17631) `Numeric#real?` incorrectly returns true for `NaN` and `INFINITY` (jeremyevans0)

* Is the current behavior of `real?` and `complex?` expected, where `Complex#real?` is always `false` and `Numeric#real?` is always `true`?
* I believe so and think this bug should be closed, but I would like to get confirmation.

Preliminary discussion:

* mame: looks good to close.
* mame: numpy says so

```
>>> np.isreal(math.inf)
True
```

* znz: I found old discussion about `real?` at [[ruby-dev:36221]](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-dev/36221) in Japanese.

Conclusion:

* matz: close.


### [[Feature #17795]](https://bugs.ruby-lang.org/issues/17795) Around `Process.fork` callbacks API (byroot)

- Was considered in the last meeting, but didn't reach any conclusion
- Since then more use cases and argument were brought.

Preliminary discussion:

* znz: [1443976 – glibc: Remove the PID cache](https://bugzilla.redhat.com/show_bug.cgi?id=1443976)
* mame: [Linux Man Pages: [patch] getpid.2: Note that caching is removed as of glibc 2.25.](https://www.spinics.net/lists/linux-man/msg11770.html)
* ko1: I have no objection.

Discussion:

* akr: Good to know that there are actual use cases where fork level integer is insufficient.
* akr: Other possibilities include force everything that forks child processes to use `Process.fork`, and let people refine that method.
* akr: `TracePoint` event for debugger or similar tools?
* (mame: failed to log very long discussion)

Conclusion:

* mame: I'm unsure how to proceed this issue... I'll post a breif summary of the discussion at the best effort
* mame: I'd like to ask Jeremy's opinion about the issue and counterproposals.


### [[Bug #17822]](https://bugs.ruby-lang.org/issues/17822) Inconsistent visibility behavior with refinements (alanwu)

* I think the three cases in the repro script should behave the same. What do you think?
* If they should behave the same, what should be that behavior?

Preliminary discussion:

* ko1: go ahead.

### [[Bug #15928]](https://bugs.ruby-lang.org/issues/15928) Constant declaration does not conform to JIS 3017:2013 (jeremyevans0)

* Do we want to make constant assignment evaluation similar to attribute assignment (e.g. use left-to-right evaluation)?
* We recently made a similar change to make multiple assignment use left-to-right evaluation (#4443), and I think constant assignment should operate the same way.

Preliminary discussion:

* nobu: I think it is good to fix
* ko1: I have no opinion

Discussion:

* usa: "Constant assignment being slow" must not be a serious problem.
* ko1: Is performance the only concern?
* matz: I don't bother this area.
* knu: This can relate to autoload.
* naruse: I'm against breaking backwards compatibility today.

Conclusion:

* matz: it's low priority.


### [[Bug #17767]](https://bugs.ruby-lang.org/issues/17767) Cloned `ENV` inconsistently returns `ENV` or `self` (jeremyevans0)

* How should `ENV.dup` and `ENV.clone` work?
* I propose to have them return `ENV`, since they share the same storage.
* If we do want `ENV.dup` and `ENV.clone` to return `ENV`, is the patch acceptable?

Preliminary discussion:

```shell
$ gem-codesearch "\bENV.dup"
/srv/gems/aigu-1.2/bin/aigu:Aigu::CLI.new(ENV.dup, ARGV.dup).run
/srv/gems/bluenode-0.0.1/lib/bluenode/context.rb:    def initialize(basedir = Dir.pwd, env = ENV.dup, stdout = $stdout, stderr = $stderr)
/srv/gems/confinicky-0.2.2/lib/confinicky/controllers/exports.rb:          @commands << [duplicate, ENV[duplicate]]
/srv/gems/git-inside-branch-0.0.1/lib/git-inside-branch.rb:        old_env = ENV.dup
/srv/gems/git_handler-0.2.2/examples/readonly.rb:  session.execute(ARGV.dup, ENV.dup.to_hash) do |req|
/srv/gems/git_handler-0.2.2/examples/simple.rb:  session.execute(ARGV.dup, ENV.dup.to_hash)
/srv/gems/mongo-2.14.0/spec/support/common_shortcuts.rb:        # Note that ENV.dup produces an Object which does not behave like
/srv/gems/mvcli-0.1.0/lib/mvcli/app.rb:    def main(argv = ARGV.dup, input = $stdin, output = $stdout, log = $stderr, env = ENV.dup)
/srv/gems/rumm-0.1.0/app.rb:    def main(argv = ARGV.dup, input = $stdin, output = $stdout, log = $stderr, env = ENV.dup)
/srv/gems/soyuz-0.3.1/spec/soyuz/command_env_spec.rb:      before { @old_env = ENV.dup }
```

```ruby=
# /srv/gems/soyuz-0.3.1/spec/soyuz/command_env_spec.rb:      before { @old_env = ENV.dup }
    describe "#run" do
      before { @old_env = ENV.dup }

      it "sets the environment_variable" do
        subject.run
        expect(ENV["FOO"]).to eq("BAR")
      end

      after { ENV = @old_env }
    end
```

```ruby=
old = ENV.dup
ENV['foo'] = 'hoge'
p ENV['foo'] #=> "hoge"
ENV = old #=> t.rb:4: warning: already initialized constant ENV
p ENV['foo'] #=> t.rb:5:in `<main>': undefined method `[]' for #<Object:0x000001822ad32978> (NoMethodError)
```

```ruby=
old = ENV.clone
ENV['foo'] = 'hoge'
p ENV['foo'] #=> "hoge"
ENV = old #=> t.rb:4: warning: already initialized constant ENV
p ENV['foo'] #=> "hoge"
```

```shell=
$ gem-codesearch "\bENV.clone"
/srv/gems/aruba-win-fix-0.14.2/spec/aruba/jruby_spec.rb:    @fake_env = ENV.clone
/srv/gems/designshell-0.0.8/bin/designshelld:   :env=>ENV.clone,
/srv/gems/fulmar-shell-1.8.3/lib/fulmar/shell.rb:      env = ENV.clone
/srv/gems/kontena-cli-1.5.4/lib/kontena/cli/stacks/upgrade_command.rb:      prev_env = ENV.clone
/srv/gems/krates-1.7.11/lib/kontena/cli/stacks/upgrade_command.rb:      prev_env = ENV.clone
/srv/gems/panoptimon-0.4.5/spec/passthru_spec.rb:    (env = ENV.clone)['RUBYOPT']='-Ilib'; ENV.stub('[]') {|x| env[x]}
/srv/gems/pipefitter-0.2.0/bin/pipefitter:  Pipefitter::Cli.run(ARGV.clone, :environment => ENV.clone)
/srv/gems/redmine-installer-2.3.2/lib/redmine-installer/redmine.rb:        backup = ENV.clone.to_hash
/srv/gems/rspec-redo-0.1.2/lib/rspec-redo/runner.rb:      env = ENV.clone.tap { |e| e.store 'RSPEC_REDO', '1' }
```

```ruby=
# copy from /srv/gems/aruba-win-fix-0.14.2/spec/aruba/jruby_spec.rb
describe "Aruba JRuby Startup Helper" do
  before(:all) do
    @fake_env = ENV.clone
  end

  before :each do
    Aruba.config.reset

    # Define before_cmd-hook
    load 'aruba/config/jruby.rb'
  end

  before(:each) do
    @fake_env['JRUBY_OPTS'] = "--1.9"
    @fake_env['JAVA_OPTS'] = "-Xdebug"

    stub_const('ENV', @fake_env)
  end
```

* nobu: `ENV.dup` doesn't copy a singleton class, so simply `Object` instance is returned.
* ko1: maybe `ENV.clone` is misused as `ENV.to_h` in many cases.
* znz: I think warnings or error messages including `use ENV.to_h instead` are better.

Discussion:

* usa: Maybe programmers wanted to take a backup.
* mame: Yeah, and is failing.
* usa: `ENV = ENV.dup` doesn't raise but doesn't work in practice...
* ko1: So, what should we do?
* nobu: Just kill it.
* ko1: or warning?
* naruse: I don't think warnings save anything.
* knu: What is the right way to backup?
* ko1: `ENV.replace(ENV.to_h)`
* ko1: Jeremy proposes to make `ENV.dup` return itself.
* nobu: That sounds dangerous, doesn't make a backup.
* znz: ENV only special behavior is `ENV.freeze` raising error since 2.7. Other built-in `#freeze` do not raise.
* usa: `ENV.dup` does not work now, so it should raise an exception. `ENV.clone` works (but may be wrong) now, then should only warn to use `ENV.to_h`.
* knu: I agree.  There would be no use for `ENV.dup` anyway.
* naruse: Though this breaks compatibility, the formar behavior is completely already broken. I'm ok to go ahead with usa's summary.

Conclusion:

* matz: agree with usa


### [[Bug #17781]](https://bugs.ruby-lang.org/issues/17781) Resolv::DNS RequestID table allocations are never freed, causing DNS lookups to eventually hang (sam.saffron)

 * Security issue is quite severe (can cause denial of service), any chance we can do a 2.7.4 release shortly?

Preliminary discussion:

* mame: it is fixed in master. need to backport


Conclusion:

* usa: will do

### [[Bug #17866]](https://bugs.ruby-lang.org/issues/17866) Psych 4.0.0

Discussion:

* naruse: I'm negative to go Ruby 3.1 with Psych 4.0.0
* matz: agreed

### [[Bug #17869](https://bugs.ruby-lang.org/issues/17869) `Ripper.sexp`'s S-expression when using endless method definition

* nobu: will fix

### [[Feature #17837]](https://bugs.ruby-lang.org/issues/17837) `Regexp.timeout=`

* nobu's counter proposal: `Regexp.backtrack_limit=`
* knu: How should we estimate the threshold?  Should MatchData include the number of backtracks occured?
* naruse: What about checking the CPU time, say, every n million backtracks?
* We need to collect numbers.


### [[Feature #17853]](https://bugs.ruby-lang.org/issues/17853) Add `Thread#thread_id` (naruse)

Conclusion:
* matz: the name should avoid confusion between os thread id and Ruby's

### [[Feature #17873]](https://bugs.ruby-lang.org/issues/17873) Update of default gems in Ruby 3.1


---

## previous meeting

https://github.com/ruby/dev-meeting-log/blob/master/DevelopersMeeting20210416Japan.md


### [Bug #16983] RubyVM::AbstractSyntaxTree.of(method) returns meaningless node if the method is defined in eval (jeremyevans0)

* ko1: Will do.



