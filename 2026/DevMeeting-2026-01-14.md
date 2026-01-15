# DevMeeting-2026-01-14

https://bugs.ruby-lang.org/issues/21777

## DateTime and location

* 2026/01/14 (Wed) 13:00-17:00 JST @ Online

## Next Date

* 2026/02/12 (Wed) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Bug #21780]](https://bugs.ruby-lang.org/issues/21780) Change the default size of `Enumerator.produce` back to infinity (zverok)

* While the new argument is a good addition to the API, I argue that `Float::INFINITY` would be a more friendly default, corresponding to the most straightforward usage

#### Discussion

* mame: Knu wanted to make it `nil` before, but @zverok wanted otherwise.
* mame: The point here is how trustworthy is this size attribute is.
* akr: whichever ways we go the behaviour must be documented.

#### Conclusion

*


### [[Bug #21803]](https://bugs.ruby-lang.org/issues/21803) `Addrinfo#connect_internal` should raise `IO::TimeoutError` on user-specified timeouts (shioimm)

- I’d like to make `Socket.tcp` consistently raise `IO::TimeoutError` when a user-specified timeout occurs, rather than raising `Errno::ETIMEDOUT` in some cases.

#### Discussion

* mame: no wonder raising `Errno::ETIMEDOUT`  when system call times out.
* akr:  Exception classes has not been well thought out until now.
* mame: `IO::TimeoutError` was introduced recently because we wanted IO#timeout=
* akr: why not take Sam's proposal (to introduce toplevel `::TimeoutError`) in [#18630](https://bugs.ruby-lang.org/issues/18630)?
* matz: I'm not against it.
* ko1: `::TimeoutError` is used though.

```
$ ~/gem-codesearch/gem-codesearch '^(class|module) TimeoutError\b'
2023-03-13 /srv/gems/LilyPond-Ruby-0.1.5.3/lilypond-2.24.1/lib/python3.10/asyncio/exceptions.py:class TimeoutError(Exception):
2023-03-13 /srv/gems/LilyPond-Ruby-0.1.5.3/lilypond-2.24.1/lib/python3.10/concurrent/futures/_base.py:class TimeoutError(Error):
2023-03-13 /srv/gems/LilyPond-Ruby-0.1.5.3/lilypond-2.24.1/lib/python3.10/multiprocessing/context.py:class TimeoutError(ProcessError):
2017-06-11 /srv/gems/busser-robot-0.1.3/vendor/robot/src/robot/errors.py:class TimeoutError(RobotError):
2016-03-17 /srv/gems/canvas-jobs-0.11.0/lib/delayed/worker.rb:class TimeoutError < RuntimeError; end
2007-05-31 /srv/gems/concurrent-0.2.2/lib/concurrent/actors/mailbox.rb:class TimeoutError < RuntimeError
2008-06-03 /srv/gems/concurrent-selectable-0.1.1/lib/concurrent/selectable/common.rb:class TimeoutError < RuntimeError
2011-06-04 /srv/gems/dohruby-0.3.8/lib/doh/util/doh_socket.rb:class TimeoutError < RuntimeError
2011-06-04 /srv/gems/dohruby-0.3.8/lib/doh/util/file_edit.rb:class TimeoutError < RuntimeError
2018-04-29 /srv/gems/dragonfly_puppeteer-0.1.0/node_modules/gh-got/node_modules/p-timeout/index.js:class TimeoutError extends Error {
2018-04-29 /srv/gems/dragonfly_puppeteer-0.1.0/node_modules/p-timeout/index.js:class TimeoutError extends Error {
2014-10-03 /srv/gems/dstruct-0.0.1/lib/rex/exceptions.rb:class TimeoutError < Interrupt
2012-01-30 /srv/gems/exact_target_sdk-1.0.1/lib/exact_target_sdk/errors.rb:class TimeoutError < Error
2014-06-17 /srv/gems/exercism-analysis-0.1.1/vendor/python/logilab/common/proc.py:class TimeoutError(ResourceError):
2014-09-07 /srv/gems/librex-0.0.999/lib/rex/exceptions.rb:class TimeoutError < Interrupt
2020-10-07 /srv/gems/msgpack-rpc-0.7.0/lib/msgpack/rpc/exception.rb:class TimeoutError < RPCError
2016-03-20 /srv/gems/pylintr-0.1.3/bin/pylint_2_7/lib/python2.7/site-packages/pip/_vendor/requests/packages/urllib3/exceptions.py:class TimeoutError(HTTPError):
2013-01-24 /srv/gems/redial-ruby-agi-2.0.1/lib/ruby-agi/error.rb:class TimeoutError < Exception
2011-04-19 /srv/gems/redpack-1.0.6/rblib/redpack/exception.rb:class TimeoutError < Error
2018-11-20 /srv/gems/rex-2.0.13/lib/rex/exceptions.rb:class TimeoutError < Interrupt
2024-05-15 /srv/gems/rex-core-0.1.32/lib/rex/exceptions.rb:class TimeoutError < Interrupt
2016-11-19 /srv/gems/rigid-0.2.1/vendor/requests/packages/urllib3/exceptions.py:class TimeoutError(HTTPError):
2006-10-09 /srv/gems/ruby-agi-2.0.0/lib/ruby-agi/error.rb:class TimeoutError < Exception
2014-04-07 /srv/gems/session-3.2.0/test/session.rb:class TimeoutError < StandardError; end
2014-06-05 /srv/gems/ssl_scan-0.0.6/lib/ssl_scan/exceptions.rb:class TimeoutError < Interrupt
```

* akr: `raise TimeoutError` won't work if it is a module, then.
* nobu: You can `def TimeoutError.exception = ...`
* akr: Would rather import `::Timeout` into core; this already exists in stdlib.

```ruby
module Timeout
  class Error < RuntimeError
    include Timeout
  end
end
begin
  ...
rescue Timeout
end

rescue *(defined?(TimeoutError) ? [TimeoutError] : [IO::TimeoutError, Errno::ETIMEDOUT])

module IO::WaitReadable; end
module TimeoutError; end # raise TimeoutError, "foo"
module ITimeout; end
module Time::Out; end
module ErrorModule::Timeout; end
module Exception::Timeout; end
module Ruby::ETimeout; end
module Err::Timeout; end
```

```
lib/fileutils.rb:            raise Errno::EEXIST, d
lib/fileutils.rb:    raise Errno::ENOTDIR, path unless force or File.directory?(path)
lib/find.rb:    paths.collect!{|d| raise Errno::ENOENT, d unless File.exist?(d); d.dup}.each do |path|
lib/prism/ffi.rb:            raise Errno::EISDIR.new(filepath)
```

#### Conclusion

* matz: I like the direction to include a common module for timeout situations; we need a good name.
* shioimm: `Addrinfo#connect_internal` keeps raising `Errno::ETIMEDOUT` (it has been, since before the changeset in question).

### [[Feature #21800]](https://bugs.ruby-lang.org/issues/21800) `Dir.foreach` and `Dir.each_child` to optionally yield `File::Stat` object alongside the children name (byroot)

* It's common for development tools to need to recursively scan the file system, but it's slower than it should be in Ruby because the API impose N+1 `stat` syscalls.
* By providing `File::Stat` alongside the entry name we can leverage `readdir`'s `d_type` and avoid a `stat(2)` call in most cases.
* This would benefit many popular gems such as Zeitwerk, rubocop, etc.

#### Preliminary discussion

```ruby
def each_child
  yield path, stat
end

Dir.each_child do |*path|
  path.last
end
```

#### Discussion

* nobu:  They want `dirent.d_type`
* akr: Is this reliable?
* matz: Linux man page says "not supported by all file systems".
* nobu: They'd return `DT_UNKNOWN` then.
* shyouhei: is this even POSIX?
* matz: not sure...
* akr:  No, this is out of POSIX.
* nobu: We already check `d_type` existence at `./configure`
* akr: Generating `File::Stat` seems too much to me.
* shyouhei: What does the OP wants in this?
* akt:  Seems they want to see if the entry is a directory or not (for recursion).
* shyouhei: yielding `File::Stat` sounds overkill to me then.
* nobu:  Would be enough to yield a symbol.
* nobu:  If we opt to yield `File::Stat` we would have another problem:  should we get tht using `stat` or `lstat`?
* mame: API?  Do we want to break existing codes?
  ```ruby
  Dir.new(:path).enum_for(:each_child).to_a # => breaks if block yields 2 elems
  ```
* akr: new method?
* shyouhei: or a new keyword argument?
* akr:  It would be difficult to write type signature (yield params depends on kwargs).
```ruby
Dir.each_dirent(path) do |name, f_type|
Dir.each_child_with_file_type(path) do |name, f_type|
Dir.each_child(path, with_type: true) do |name, f_type|
  # what is the f_type?
  f_type #: Dir::DT_UNKNOWN (= 0), Dir::DT_FIFO (= 1), ...?
  f_type #: :UNKNOWN, :FIFO, :REG, ...
         #: or other idea?
end
```

#### Conclusion

* No conclusion but we had fruitful discussion; let's continue discussion.

### [[Feature #21788]](https://bugs.ruby-lang.org/issues/21788) Promote `Thread::Monitor` to a core class (byroot)

* Monitor is about as useful as Mutex and yet one is a core class and the other is a "stdlib" extension.
* Bringing it into core allow to make is about as fast as mutex, while it is currently ~20% slower.
* I don't personally think `MonitorMixin` should be made core though. It can remain in `monitor.rb`.

#### Discussion

* ko1: Do we just want a recursive Mutex?  Why not just extend Mutex then?
* ko1: Monitor is a much broader concept I guess.

#### Conclusion

* ko1:  We want to know the use case (isn't a recursive Mutex enough?)

### [[Feature #21821]](https://bugs.ruby-lang.org/issues/21821) Add `Stack` and `SizedStack` classes. (byroot)

* Identical to `Queue` and `SizedQueue` but LIFO ordering.
* Should they be namespaced under `Thread::` like `Queue` or is it just historical?

#### Discussion:

* mame:  The OP wants a thread-safe stack.
* akr: usage?
* mame: Connection pool, where recent connections are preferred over the least used one.
* matz: Rejected his request to make Queue dual-ended.
* ko1: I'm not against dequeue though because C++ has one.
* mame:  What about `Queue#unpush` ?
* akr:  The problem is whether we want to add a new class for it, or extend Queue.
* ko1:  If there is queue class and there'll be  stack class, I'd also want priority queue class.

#### Conclusion:

* Consensus are that it would be nice to have something like this, but there were no consensus for the API: Namespace? Class? Method?
* matz: `#unpush` is a bad name but feature wise I can compromise.


### [[Feature #21264]](https://bugs.ruby-lang.org/issues/21264) Extract `Date` library from Ruby repo in the future (jeremyevans0)

* Do we want to extract `date` to bundled gems?
* I'm fine keeping it as stdlib, and if it needs a maintainer, I volunteer to maintain it.
* If we want to move `Date` to bundled gems, I think we should move a subset of `Date` to core.
  * A brief outline of my idea for this: https://bugs.ruby-lang.org/issues/21264#note-10

#### Discussion:

* mame: Extraction of Date would not happen today even if we are going to.
* matz: Over time we have extended Time class and it has very little use cases today.
* akira: I won't bother if Date is internally built on top of Time, but the concept of Date itself is necessary for us.
* akr: Conceptual date (that does not point to a specific millisecond of chronological time) is actually very less usable.  Python once had such thing, but they eventually abondoned such feature.
* mame: TOML has such data type (date without timezone).  It would be very nice for us to have something that can express TOML's.

#### Conclusion:

* Don't worry, this is not an end.  We are still discussing.

### [[Feature #13683]](https://bugs.ruby-lang.org/issues/13683) Add strict `Enumerable#single` (mame)

* ActiveSupport now has `Enumerable#sole`. https://github.com/rails/rails/pull/40914
* I want this on the core too. It's good that the intention of having only one is explicitly stated in the code.

#### Preliminary discussion

```
["foo"].sole.upcase  #=> "FOO"
[].sole              #=> ArgumentError
["foo", "bar"].sole  #=> ArgumentError

["foo"].one        #=> "foo"
[].one             #=> nil
["foo", "bar"].one #=> nil

["foo"].one!        #=> "foo"
[].one!             #=> ArgumentError
["foo", "bar"].one! #=> ArgumentError
```
```ruby
ary = [42]
ary => [elm]
```

```
#  /srv/gems/magic-presenter-1.0.0/lib/magic/presenter/base.rb
                                def model_class
                                        Presentable.classes
                                                        .select { Presenter.for(_1) == self }
                                                        .sole
                                rescue Enumerable::SoleItemExpectedError => error
                                        raise Lookup::Error, "#{error.message
                                                        .sub('items', 'model classes')
                                                        .sub('item',  'model class')
                                        } for #{self}"
                                end
```

#### Discussion

* mame: I saw this method in ActiveRecord.
* mame:  This issue was rejected as "no use case" years ago but I see real world use cases now.
* matz: Not against this feature if there are use cases.
* matz: Naming wise I'm against `#single`  but `#sole` is acceptable.
* mame: @headius suggests `#one!` (there is `#one?`)
* nobu: Do we also want to introduce `SoleItemExpectedError`?
* matz:  ArgumentError is preferred, but would break Rails.

#### Conclusion

* matz:  Let me consider.  Positive for the feature though.

### [[Feature #6012]](https://bugs.ruby-lang.org/issues/6012) `Proc#source_location` also return the column (mame)

* Quite a few gems are using the idiom `source_location.last`.
* The idiom will return a column number (of the end of the code range) instead of a line number.
* This incompacibility actually affected rspec and pry (though they are already fixed).
* Is this incompatibility really ok? I feel it is disproportionately incompatible for unclear use cases.

#### Discussion

* mame: current master has incompatibility:

```ruby:test.rb
# master branch
# test.rb
f = -> {  }
f.source_location #=> in Ruby 4.0.0: ["test.rb", 1]
                  #=> in the master: ["test.rb", 1, 4, 1, 9]
# matz: new method or new keyword 
f.source_location(full: true)   #=> ["test.rb", 1, 4, 1, 9]
f.source_location(column: true) #=> ["test.rb", 1, 4, 1, 9]
f.source_location_with_column   #=> ["test.rb", 1, 4, 1, 9]
# ko1: I am not sure if it is "full". In the future we may want to add more information

f.source_location.last #=> in Ruby 4.0.0: the first lineno
                       #=> in the master: the last columnno

foo(*f.source_location)
f.source_location.join(":") #=> file:lineno
# f.source_location.take(2).join(":") #=> file:lineno

# akr-san:
# See https://bugs.ruby-lang.org/issues/20884#note-3
Ruby::CodeLocation = Data.define(:path, :first_lineno, :first_column, :last_lineno, :last_column)

class Ruby::CodeLocation
  def to_ary = [path, first_lineno]
  def last = first_lineno
end

# eregon:
f.code_location # => Ruby::CodeLocation
f.code_location.{start_line
start_column
start_offset
end_line
end_column
end_offset
code # source code for that Proc/Method
}
```

Docs:
```
/*
 * call-seq:
 *    prc.source_location  -> [String, Integer, Integer, Integer, Integer]
 *
 * Returns the location where the Proc was defined.
 * The returned Array contains:
 *   (1) the Ruby source filename
 *   (2) the line number where the definition starts
 *   (3) the position where the definition starts, in number of bytes from the start of the line
 *   (4) the line number where the definition ends
 *   (5) the position where the definitions ends, in number of bytes from the start of the line
 *
 * This method will return +nil+ if the Proc was not defined in Ruby (i.e. native).
 */
```

* akr: we should introduce a dedicated Data class.
* eregon: See  https://bugs.ruby-lang.org/issues/20884#note-3
* mame: alternative approach is to introduce a new method for this purpose and keep source_location as-is.
* matz: or a keyword argument.  Whichever.
* mame: use case?
* akr: pry/irb's show_source?

```ruby
# matz: method(:foo).source_location_with_???(including_heredoc: false | true, ...)
def foo; <<END; end; def bar; <<END2; end
heredoc
END2 <= This should be ignored if we want to extract the range of bar
#{code}
END
heredoc for bar
END2
```

```
def foo; end; def bar; end
# With code_location, correct
# Without, source of foo `def foo; end; def bar; end`
```

* eregon: For RDoc, want to show only `def foo; end` for "source of foo", no?
* eregon: Add `Ruby::CodeLocation#end_of_heredoc_offset` ?

* eregon: If returns `def` to `end`, then can easily find `def` to  end-of-heredoc with  adding more lines until it parses.
  But if we return `def` to  end-of-heredoc, hard to find `end`.
* multiple: Need to document this edge case.
* eregon: heredoc is like "C preprocessor", gone after lexer, so often problematic for code location, etc. But also very rarely used that way.
* concrete use cases, see https://bugs.ruby-lang.org/issues/6012#note-49
* eregon: what if we always include heredoc? Seems fine except to location AST node, so then we need {Method,Proc}#ast as well for that use case.
  Probably very few use cases which don't want heredoc and don't need AST.
  Can be worked around anyway with "find DefNode in `Prism.parse(method.code_location.source)`".
* eregon: To be parseable need to include heredoc (in case there is a herdoc spanning across `end`)
* eregon: I wonder how hard it is to get end-of-heredoc for implementation though

#### Conclusion


### [[Feature #21795]](https://bugs.ruby-lang.org/issues/21795) Methods for retrieving ASTs (eregon)

* This seems very useful and convenient, and abstract over implementation details like `node_id` (which I think make little sense to expose to users).
* To address @mame's concern about not working for `eval` code, how about keeping `eval` code in memory by default?
* TruffleRuby would implement this using source byte offset+length and not `node_id`. I can even prototype an implementation if useful.

#### Preliminary discussion

1. The name
    * `Proc#ast`
    * `Prism.node_for(proc_obj)` <- ko1 favoite?
    * `Proc#prism_node`
    * `Ruby.node_for(obj)`
    * `Proc#syntax_tree` <- matz favoite?
        * `String#syntax_tree`, `Array#syntax_tree`, ... 
          * eregon: cannot work for non-literals, sounds too expensive to keep footprint-wise
        * `Binding#syntax_tree` : eregon: does not make sense, there is no "end" for `binding`
        * `Thread::Backtrace::Location#syntax_tree`: does not end with "end"?
          * eregon: that's for a call, would return a Prism::CallNode or similar
        * `Class#syntax_trees`: it could be multiple syntax trees if consider open class
    * need for corresponding to `Module#const_source_location`?
    * matz: I don't see the need for so convenient way to get a node, but am not against
2. eval (keep the source in memory?)
    * or raise an error if the source code is not available
    * erb?
    * `RubyVM.keep_script_lines = true`
    * `RubyVM.keep_script_lines_only_for_eval = true`
    * `eval(src, keep_script_lines: true)`
    * `Ruby.keep_script_lines`
    * eregon: must be public not-CRuby-specific API, so can be implemented on JRuby/TruffleRuby
3. file modification (validate source hash)
4. Which Prism::Node definition
    * seems no problem to use Prism::Node. If using too old `prism` gem version in Gemfile, will be a clear error saying "update prism gem" (this is a feature of `Prism.parse(code, version: "current")`.
5. parse.y
    * could use extended source_location to make it work, see https://bugs.ruby-lang.org/issues/21795#note-3. Or something more internal if #syntax_tree is a core method.

```ruby
rescue
  raise $!
end

# test.rb:2: RuntimeError (RuntimeError)
#
#   raise $!
#   ^^^^^
#        from test.rb:1: in rescue
```

#### Discussion

* matz: I'm okay to have this kind of functionality. Do we need to record AST information?
* eregon: No we can get Prism node by re-parsing the script.
* eregon: This is the best solution to migrate from CRuby-only RubyVM::AbstractSyntaxTree to something that works on all Rubies.
* eregon: If we use `node_id` to implement it on CRuby, then it doesn't work with parse.y (until parse.y implements the Prism Ruby API)
* eregon: We could use "extended source location data" like start/end line/column/byte_offset instead, that works on all Ruby implementation. In fact, that works and is implemented in https://github.com/ruby/prism/pull/3808
* eregon: `Method#ast` is way more easy to discover than `Prism.node_for`
* eregon: it's messy to validate source hash (= check for file modification) if not implemented in core
* matz: I prefer `Proc#syntax_tree`
* naruse: matz, Do you think the getting AST is 1st class methods? (easy to get)
* matz: I don't think it should be 1st class, but I can accept to locate `Proc#....`.
* kaneko.y: At least I foresee we may want `Class#syntax_tree` for the class definition, `Module#syntax_tree`, etc. in the near future
* eregon: Should we return a single Prism::Node, or [Prism::Node, Prism::ParseResult] in case the user wants more context? We need to parse the whole source anyway.
* naruse: I think the AST information should be hidden information, but `Proc#ast` is revealing it easility.
  * eregon: Prism is public stable API, seems no problem?
* kaneko.y: I support it. For example, can we change the AST specification?
* naruse: This design seems encouraging the macro feature.
* eregon: People will do it, whether core or not, it's just more discoverable in core. And removing reliance on CRuby-only RubyVM::AbstractSyntaxTree is good and finally resolving that tech debt.

---- 
* eregon: Re AST vs CST, I see Prism as AST because e.g. `Prism.parse("1 ? 2 : 3")` returns a `IfNode`, not a `TernaryConditionNode`. BTW RubyVM::AbstractSyntaxTree is also not abstract if Prism is not abstract, it seems developers always refer to AST. CST would mean "one per grammar rule" which is the case for neither of them.

----

* naruse: how about `eval`?  can we return `nil` for that?
* ko1: now `keep_script_lines` feature, so we can continue to use it (we may need to reconsider API)

----

* mame: how to handle file modification?
    * 1. record all scrippt or AST -> memory consumption issue
    * 2. record hash value for script
    * 3. don't care on returing wrong node
* eregon: this seems difficult if not implemented in core (would need method to get hash of loaded source)
* byroot: Not sure this is worth the cost and complexity. ISeq are already very large.

----

mame: ruby/prism and ruby/ruby can be different at a moment, on development version, right?
eregon: no problem because it should be same.
kaneko.y: 

----

give up writing the discussion.

#### Conclusion

* matz: I'll write my comments on issue, but not concluded yet.

### [[Misc #21804]](https://bugs.ruby-lang.org/issues/21804) Getting setup-ruby Earlier (eregon)

* To get it out earlier, we need release managers (@hsbt @nagachika @k0kubun) to also run the relevant steps for setup-ruby: https://github.com/ruby/setup-ruby/pull/848#issuecomment-3728626978
* Are they OK to do that?
* For JRuby and for RubyInstaller, Charles Nutter and Lars Kanis are already OK with it. So it's just missing CRuby release managers now.

#### Discussion

* The complete flow: https://github.com/ruby/setup-ruby/pull/848#issuecomment-3740249262

#### Conclusion

* naruse: I will comment to that ticket

### [[Feature #8948]](https://bugs.ruby-lang.org/issues/8948) Frozen regex (eregon)

* Can we merge it now for 4.1? Then there is plenty of time for the few gems that rely on mutable Regexp to adapt.
* We can also revert it if it turns out to be too disrupting.

#### Discussion

* matz: there are some code that will break by this change
* eregon: they are very rare

#### Conclusion

* matz: try freezing Regexp instances, but no subclasses, and revisit it to check compatibility issues.

### [[Feature #21826]](https://bugs.ruby-lang.org/issues/21826) Deprecating `RubyVM::AbstractSyntaxTree` (eregon)

* OK to deprecate now/for 4.1? If not, when?
* And maybe remove it in 4.2?

#### Discussion

* matz: I'm okay to mention it is deprecated in release note or somewhere else, but not okay to show any warning.
* eregon: we need `{Proc,Method}#syntax_tree` to replace `RubyVM::AbstractSyntaxTree.of(Proc|Method)`

#### Conclusion

* matz: I will add the deprecation to the rdoc of `RubyVM::AST` but not introduce any warning

### [[Feature #21827]](https://bugs.ruby-lang.org/issues/21827) Deprecating `Ripper` (eregon)

* Removing Ripper would remove a significant maintenance overhead, for all Ruby implementations.
* It is however an established/public API, even though `Ripper.sexp` is still marked experimental and it hasn't been very stable.
* So how about deprecating it in 4.1?

#### Discussion

* on the issue: API used in many places, cannot deprecate with warning yet
* eregon: maybe for now we recommend migrating to Prism insted in documentation?

----

mame: nobu, kaneko.y, you are maintaining the ripper. what do you think about it?
kaneko.y: one of my motivations to implement lrama was to make it easy to maintain ripper, so I don't say it should be deprecated.
matz: too much usage, need time and work to migrate so to early for deprecation warning.
matz: we need to help community to migrate away from Ripper first

#### Conclusion

* eregon: I make a documentation PR about Ripper to recommend using Prism instead for new code
* naruse: the comment should be polite for ripper developers
* eregon: Who should I add to review the PR?

(timeout)

---

https://bugs.ruby-lang.org/issues/21768 Remove deprecated functions

* https://github.com/ruby/ruby/pull/15447
* https://github.com/nobu/ruby/tree/deprecate-rdata

https://bugs.ruby-lang.org/issues/19979 Allow methods to declare that they don't accept a block via `&nil`
https://bugs.ruby-lang.org/issues/21773 Support for setting encoding when a block is passed to `Net::HTTPResponse.read_body`
https://bugs.ruby-lang.org/issues/21781 Add `fetch_values` method on `ENV`
https://bugs.ruby-lang.org/issues/21808 Inconsistency in support of additional newlines with boolean logical operators on new line
https://bugs.ruby-lang.org/issues/21797 Make `Etc.nprocessors` cgroup-aware on Linux
https://bugs.ruby-lang.org/issues/21813 Add `[:forward, :...]` symbol tuple to indicate forwarding arguments when calling `Method#parameters`

```ruby
def foo(*, **, &)
  p method(__method__).parameters
  #=> [[:rest, :*], [:keyrest, :**], [:block, :&]]

  binding.eval "print(#{(method(__method__).parameters.dig(0,1))})" 
  #== print(*)
end

def bar(...)
  p method(__method__).parameters
  #=> actual: [[:rest, :*], [:keyrest, :**], [:block, :&]]
  #=> expected: [[:forwarding, :...]]
  
  # use case
  binding.eval "print(#{(method(__method__).parameters.dig(0,1))})" 
  #== print(*) <= does not work
end

bar
```

https://bugs.ruby-lang.org/issues/21822 Expose Return Value in the ensure Block
similar ticket: https://bugs.ruby-lang.org/issues/18083

```ruby
begin
  ...
ensure => ret, err
  ...
end
```

https://bugs.ruby-lang.org/issues/21825 Status of the universal parser implementing the Prism API
https://bugs.ruby-lang.org/issues/21767 Consider procs which `self` is Ractor-shareable as Ractor shareable
