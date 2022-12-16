---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2022-12-15

https://bugs.ruby-lang.org/issues/19170

## DateTime and location

* 2022/12/15 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2023/01/19 (Thu) 13:00-17:00 JST @ Online

## Announce

## About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Misc #19178]](https://bugs.ruby-lang.org/issues/19178) Distribution of Ruby and Standard Library gems. (ioquatix)

* Not sure what to discuss

### [[Feature #19134]](https://bugs.ruby-lang.org/issues/19134) `**` is not allowed in `def foo(...)` (ko1/shugo)
### [[Feature #19165]](https://bugs.ruby-lang.org/issues/19165) Method (with no param) delegation with *, **, and ... is slow (ko1/shugo)

```ruby!
# fast
ruby2_keywords def foo(*args)
  bar(*args)
end

# slow both in 3.1 and 3.2 (1)
def foo(*args, **kwargs)
  bar(*args, **kwargs)
end

# fast in 3.1, slow in 3.2 (2)
def foo(...)
  bar(...)
end
```

* https://bugs.ruby-lang.org/issues/19134 caused (2)
* ko1's improvement will improve both (1) and (2)
  * But it requires modification of MJIT and YJIT (and TypeProf)

```ruby
def foo(*args, **kwargs)
  bar(*args, k1: 1, **kwargs, k2: 2)
end

def foo(*, **)
  bar(*, k1: 1, **, k2: 2)
end

def foo(...)
  bar(...)
end

def foo(...)
  bar(*, k1: 1, **, k2: 2)
end
```

https://bugs.ruby-lang.org/issues/19230

* Discussion 1: `def foo(...); bar(**); end` should be allowed?
  * `def foo(...); bar(*); end` is allowed from master, so we have no compat. issue if we prohibit them.
  * `def foo(...); bar(&); end` is working on Ruby 3.1 so it will be compat. issue if we prohibit them.
  * ```ruby
    def f(...) # ... is syntactic sugar to *, **, & ?
      g(*)                 # prohibited
      h(**)                # prohibited
      i(*, new_key: 1, **) # prohibited
      j(&)                 # works as Ruby 3.1
    end

    def f(&)
      j(&)
    end

    def f(*, **) # "no problem" by Matz
      g(*)
      g2(*, *)
      h(**)
      i(*, new_key: 1, **)
    end
    ```
  * matz: ok, I have no reason to allow them. Conclusion is written in the above code comments.
* Disucssion 2: `RubyVM::AST` parses `def foo(...)` and returns `[*, &]`. Should we return `[*, **, &]` ?
  * `def foo(...)` uses `ruby2_keywords` and it returns `[*, &]`.
  * Shugo's patch doesn't use `ruby2_keywords` so it returns `[*, **, &]`, but it has some slowdown.
  * ```ruby
    def foo(...)
    end
    method(:foo).parameters #=> [[:rest, :*], [:keyrest, :**], [:block, :&]]
    ```
  * shugo: before my patch, MRI has a flag to represent `...`.
  * matz: choose `[*, &]` for Ruby 3.2 to keep the compatibility. No need to show the `...` flag. In a long term, RubyVM::AST should return `...`
* Discussion 3: Should we merge https://github.com/ruby/ruby/pull/6920 to Ruby 3.2?
  * Performance penalty is fixed.
  * `def foo(*, **); bar(*, **); end` is also improved.
  * YJIT and MJIT should be supported but no time for Ruby 3.2.0
    * MJIT has no problem
    * YJIT seems have some 
  * shugo: `def f(...)` is not slow because the implementation will be backed, so the importance of this patch is not high.
  * matz: Ok, try it on Ruby 3.3.
* Other point: Should we prohibit it? (caused by implmenetation details)
  * ```ruby
    def f(*, **, &)
      p(...)
    end
    f 1, 2, k:1
    # 1
    # 2
    # {:k=>1}
    ```
  * matz: ok, prohibit it.


Conclusion:

* Prohibit `bar(*)` in `def foo(...)` as Ruby 3.1
  * should be implemented
* Prohibit `bar(...)` in `def foo(*, **, &)` as Ruby 3.1
  * should be implemented
* Prohibit `bar(**)` in `def foo(...)` as Ruby 3.1
  * by reverting shugo's patch
* RubyVM::AST should return `[*, &]` against `def foo(...)` as Ruby 3.1
  * by reverting shugo's patch
* ko1's improvement will be merged for Ruby 3.3

### Fix the count of encodings

ko1: naruse-san, can I implement it for Ruby 3.2? And how many is the limit?
martin: 1024 is too many
naruse: 256?

### IRB maintainer

k0kubun: reline is now maintained by ima1zumi-san and hasumikin-san
k0kubun: How about the status of irb maintainer?

### [[Feature #18033]](https://bugs.ruby-lang.org/issues/18033) Time.new to parse a string (nobu)

* matz: looks like samuel and eregon are against the API `Time.new`
* a_matsuda: I want this API asap for Rails development
* matz: okay

* matz: BTW, is this intentional?

```
irb(main):001:0> Time.new(0)
=> 0000-01-01 00:00:00 +091859

irb(main):021:0> Time.new(1887)
=> 1887-01-01 00:00:00 +091859
irb(main):022:0> Time.new(1888)
=> 1888-01-01 00:00:00 +0900
```

## special variables

```ruby
$, # Array#join($,)
$/ # Kernel#gets
$\ # Kernel#print
$; # String#split

ary.join      # == ary.join($,)
ary.join(nil) # == current: ary.join($,), desire: ary.join("")
```

## Review of all logs of dev-meeting 2022

https://github.com/ruby/dev-meeting-log