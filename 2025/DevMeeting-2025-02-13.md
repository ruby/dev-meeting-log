# DevMeeting-2025-02-13

https://bugs.ruby-lang.org/issues/21019

## DateTime and location

* 2025/02/13 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2025/03/13 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

- 3.4.2?
- k0kubun will take care of 3.4 branch sooner.

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #21116]](https://bugs.ruby-lang.org/issues/21116) Extract RJIT as a third-party gem (k0kubun)

* Are we okay with removing RJIT (`--rjit`) from the Ruby core?

Discussion:

* k0kubun: I think I can separate RJIT from the core.
* k0kubun: this should benefits core maintenability.
* n0kada: how should RJIT be invoked, when it gets out of the core (there cannot be command line options?)
* k0kubun: while enabling JIT at process bootstrap gets impossible, RJIT can also be enabled later, and that should be okay.
* hsbt: what happens for `ruby --rjit`
* k0kubun: let's just delete.  it's still experimental so far.

Conclusion:

* matz: no reason to object.

### [[Feature #21084]](https://bugs.ruby-lang.org/issues/21084) Declare objects have weak references (peterzhu2118)

* The current way of marking weak references uses `rb_gc_mark_weak(VALUE *ptr)`.
* This presents challenges because Ruby's GC is incremental, meaning that if the `ptr` changes (e.g. realloc'd or free'd), then we could have an invalid memory access.
* This also overwrites `*ptr = Qundef` if `*ptr` is dead, which prevents any cleanup to be run (e.g. freeing memory or deleting entries from hash tables).
* This ticket proposes `rb_gc_declare_weak_references` which declares that an object has weak references and calls a cleanup function after marking, allowing the object to clean up any memory for dead objects.

Preliminary discussion:

```c
struct D {
  VALUE v1;
  int i;
  VALUE *values; // weak
}

weaks = []
rb_gc_mark_weak(VALUE *ptr)
{
  v = *ptr;
  weaks << ptr;
}

after_mark() {
  weaks.each{|ptr|
    unless *ptr is live?
      *ptr = Qundef
    end
  }
}

mark(D *d) {
  rb_gc_mark(d->v1);
  rb_gc_mark_weak(&d->values[0]);
  ...
  d->values = realloc(d->values);
}
```

Discussion:

* peter: I found `rb_gc_mark_weak()` is not perfect.  (1) the passed pointer can move during GC which is very difficuly to be done properly. (2) references to dead objects should be updated using Qundef, which prevents deallocation of such storages.
* peter: I'm proposing an internal API that registers how to deallocate that reference at the time it is created.
* mame: I heard ko1 wants some non-declaratibe API.
* ko1: embedding number of weak references into flags is too much because there should be only one or very limited number of weak references in a process.
* peter: also as a separate project I'm moving all those GC-related flags out of each objects.
* ko1: how about declare this property in marking side.
* peter: weak maps are T_DATA.  marking function doesn't know the `self`.
* ko1: oh… I see… :confused: 

Conclusion:

* ko1: overall I understand the needs.  Let myself and peter discuss about in-depth API design.

### [[Bug #20998]](https://bugs.ruby-lang.org/issues/20998) rb_str_locktmp() changes flags of frozen strings and string literals (eregon)

* Current behavior on frozen strings is buggy (should not mutate the flags of possibly-shared frozen strings).
* What should be the behavior for rb_str_locktmp() on frozen strings? No-op or raise error (e.g. FrozenError)?

Preliminary discussion:

* locktmp
    * prohibit write
    * 

```ruby
a = +"foo"
b = a.dup
b.freeze # a -> shared <-b
b.locktmp
```
```ruby
s = ...
puts s
```


Discussion:

* mame: `rb_str_locktmp()` is broken when applied to a frozen string.
* akr: it is already problematic when multiple threads lock a string at once?
* nobu: that doesn't happen.  `rb_io_write()` does not use this function now (it did before though).
* shyouhei: do we need a fully reentrant reader-writer lock for locktmp? sounds overkill.
* akr: frozen string need not be locked at the first place?
* nobu: frozen strings also gets moved when compacted.
* akr:  that can be a different story here because GC doesn't happen during locktemp is in effect.
* ko1: what should happen when a string gets frozen, under locktmp is in effect?
* mame: not possible?
* nobu: `rb_str_modifiable()` already checks that situation (and raises).

Conclusion:

* nobu: eregon is right here.  `rb_str_locktmp()` need to raise when its receiver is frozen.
* shyouhei: we acknowledge that there is currently no way for an extension library to _reliably_ obtain a `char*` from a string (because objects are compacted, and  `rb_str_locktmp()` is made unavailable conditionally due to this conclusion).  We might need separate issues / proposals to fix the gap.

### [[Bug #21102]](https://bugs.ruby-lang.org/issues/21102) Unexpected encoding when concatenating ASCII string with ASCII compatible string (toy)

* Should `ascii_string + utf8_string` return an ASCII or UTF-8 string when both have 7bit coderange?

Conclusion:

* naruse: We will not change this unless there is any good reason instead of "surprising"

### [net-http 205](https://github.com/ruby/net-http/issues/205) Do not supply a default content type

* Current net-http will use `'application/x-www-form-urlencoded'` for default content type. AWS service have some issues with this behavior. Can we remove it from default content type?

Discussion:

* mame: I guess AWS SDK is getting errors due to net/http's behaviour.
* akr:  why is it difficult for the SDK to just add necessary headers?
* shyouhei: in case of S3 the content type can untimaetly be unknown.
* mame:  maybe there is no way to force net/http preventing from adding this header.
* akr: if we could ignore backward compatibility yes, changing this to octet-stream is the right thing to do.
* shyouhei: why was x-www-form-urlencoded chosen to be the encoding right now?
* knu: maybe at the time net/http was made there was no such thing like PUT; there were either GET or POST and POST was basically HTML forms back then.

Conclusion:

* hsbt: OK, let's delete `suppy_default_content_type`

### [[Bug #21049]](https://bugs.ruby-lang.org/issues/21049) Reconsider handling of the numbered parameters and "it" parameter in `Binding#local_variables`

Conclusion:

* matz: yes, the proposed behaviour matches my expectation.  accepted.
* matz: Binding#numbered_parameters etc. need separate ticket before being accepted.

---

https://bugs.ruby-lang.org/issues/21082 Alias it to its

* matz: Objection. I will reject

https://bugs.ruby-lang.org/issues/21029 Prism behavior for `defined? (;x)` differs

```
$ ruby --parser=prism -e 'p defined? ( ;x)'
nil => "expression"
$ ruby --parser=prism -e 'p defined? (();x)'
"expression"
$ ruby --parser=prism -e 'p defined? (nil;x)'
"expression"
$ ruby --parser=prism -e 'p defined? (x;x)'
"expression"
$ ruby --parser=prism -e 'def f(*);end; p defined? f(x)'
nil
```

https://bugs.ruby-lang.org/issues/21094 `Module#set_temporary_name` does not affect a name of a nested module

```ruby
# similar code but it shows correct name
c = Class.new do |c|
  self.class_eval %q{
    class B
    end
  }
end

p c::B.name "#<Class:0x0000028038e52260>::B"
A = c
p A.name    #=> A
p A::B.name #=> A::B
```

* matz: it should be fixed

https://bugs.ruby-lang.org/issues/21035 Clarify or redefine Module#autoload? and Module#const_defined?
```
# 1.rb
module M
  autoload :Foo, './2'

  constants            # => [:Foo] (as expected)
  const_defined?(:Foo) # => true (as expected)
  autoload?(:Foo)      # => 'foo' (as expected)
 
  Foo
end

# 2.rb
module M
  p constants            # => [:Foo] (as expected)
  p const_defined?(:Foo) # => false (NOT expected)
  p autoload?(:Foo)      # => nil (NOT expected)

  class Foo
  end
end
```
https://bugs.ruby-lang.org/issues/21105 Improve Ruby Stack Trace to Include Exact Error Position (Column Number)
https://bugs.ruby-lang.org/issues/20957 RangeError on Array#values_at with negative ranges
```
[0, 1, 2, 3].values_at(10)       #=> [nil]
[0, 1, 2, 3].values_at(10..10)   #=> [nil]
[0, 1, 2, 3].values_at(-10)      #=> [nil]
[0, 1, 2, 3].values_at(-10..-10) #=> 'Array#values_at': -10..-10 out of range (RangeError)
[0, 1, 2, 3].values_at(-5..-5)   #=> RangeError
[0, 1, 2, 3].values_at(-5..-4)   #=> RangeError
[0, 1, 2, 3].values_at(-5...-4)  #=> RangeError
[0, 1, 2, 3].slice(-5..-4)       #=> nil (why?)
[0, 1, 2, 3].values_at(-4..-4)   #=> [0]
[0, 1, 2, 3].values_at(-4...-4)  #=> []
```

```
% ruby -e 'a = [0,1,2,3]
-6.upto(6) {|i|
  -6.upto(6) {|j|
    x = begin a.values_at(i..j) rescue :err end
    print x.inspect.gsub(/\s/, ""); print "\t"
}
puts
}
'|lineup 
:err :err :err :err  :err    :err      :err :err  :err    :err      :err          :err              :err                 
:err :err :err :err  :err    :err      :err :err  :err    :err      :err          :err              :err                 
[]   []   [0]  [0,1] [0,1,2] [0,1,2,3] [0]  [0,1] [0,1,2] [0,1,2,3] [0,1,2,3,nil] [0,1,2,3,nil,nil] [0,1,2,3,nil,nil,nil]
[]   []   []   [1]   [1,2]   [1,2,3]   []   [1]   [1,2]   [1,2,3]   [1,2,3,nil]   [1,2,3,nil,nil]   [1,2,3,nil,nil,nil]  
[]   []   []   []    [2]     [2,3]     []   []    [2]     [2,3]     [2,3,nil]     [2,3,nil,nil]     [2,3,nil,nil,nil]    
[]   []   []   []    []      [3]       []   []    []      [3]       [3,nil]       [3,nil,nil]       [3,nil,nil,nil]      
[]   []   [0]  [0,1] [0,1,2] [0,1,2,3] [0]  [0,1] [0,1,2] [0,1,2,3] [0,1,2,3,nil] [0,1,2,3,nil,nil] [0,1,2,3,nil,nil,nil]
[]   []   []   [1]   [1,2]   [1,2,3]   []   [1]   [1,2]   [1,2,3]   [1,2,3,nil]   [1,2,3,nil,nil]   [1,2,3,nil,nil,nil]  
[]   []   []   []    [2]     [2,3]     []   []    [2]     [2,3]     [2,3,nil]     [2,3,nil,nil]     [2,3,nil,nil,nil]    
[]   []   []   []    []      [3]       []   []    []      [3]       [3,nil]       [3,nil,nil]       [3,nil,nil,nil]      
[]   []   []   []    []      []        []   []    []      []        [nil]         [nil,nil]         [nil,nil,nil]        
[]   []   []   []    []      []        []   []    []      []        []            [nil]             [nil,nil]            
[]   []   []   []    []      []        []   []    []      []        []            []                [nil] 
```

https://bugs.ruby-lang.org/issues/20953 Array#fetch_values vs #values_at protocols

* matz: if beginless range or endless range is passed, fetch_values should raise a RangeError consistently

```
[1, 2, 3].values_at(0..)     #=> [1, 2, 3]
                  # ~~~ -> values_at(0..ary.length-1)
[1, 2, 3].values_at(0..4)    #=> [1, 2, 3, nil, nil]
[1, 2, 3].fetch_values(0..)  #=> IndexError
[1, 2, 3].fetch_values(4..)  #=> IndexError
[1, 2, 3].fetch_values(4..5) #=> IndexError
[1, 2, 3].fetch_values(4..) { true } #=> IndexError
[1, 2, 3].fetch_values(-2)   #=> [2]
[1, 2, 3].fetch_values(-2..) #=> IndexError
[1, 2, 3].fetch_values(-3..) #=> IndexError
[1, 2, 3].fetch_values(-4..) #=> IndexError
[1, 2, 3].fetch_values(-4..) { true } #=> IndexError
[1, 2, 3].fetch_values(..1)  #=> IndexError
[1, 2, 3].fetch_values(..-2) #=> IndexError
[1, 2, 3].fetch_values(..-4) #=> IndexError
[1, 2, 3].fetch_values(..-4) { true } #=> IndexError
[1, 2, 3].fetch_values(..3)  #=> IndexError
[1, 2, 3].fetch_values(..3) { true } #=> IndexError
[1, 2, 3].fetch_values(nil..nil)  #=> IndexError

[1, 2, 3].fetch(-4) #=> IndexError
[1, 2, 3].fetch(-3) #=> 1
[1, 2, 3].fetch(2)  #=> 3
[1, 2, 3].fetch(3)  #=> IndexError
```
https://bugs.ruby-lang.org/issues/21015 Add in a `-g` flag, like `-s` but with a few more quality of life features
https://bugs.ruby-lang.org/issues/21110 Should Marshal.dump always use object links for repeated Float values?

```
s = "str"
Marshal.old_dump([s, 1.0, 1.0, s]) #=> ["str", 1.0, 1.0, "otherstr", object-link 4]
"\x04\b[\tI\"\bstr\x06:\x06ETf\x061f\x061@\x06"

Marshal.new_load(s) #=> 0:[], 1:"str", 2:1.0, 3:1.0, 4:"otherstr", object-link 4
```

https://bugs.ruby-lang.org/issues/19555 Allow passing default options to `Data.define`
https://bugs.ruby-lang.org/issues/21097 `x = a rescue b in c` and `def f = a rescue b in c` parsed differently between parse.y and prism
https://bugs.ruby-lang.org/issues/20682 Slave PTY output is lost after a child process exits in macOS

---

https://bugs.ruby-lang.org/issues/21033 Allow lambdas that don't access `self` to be Ractor shareable

```ruby
x = 1
1.instance_eval("binding").local_variables #=> [:x]
Kernel.binding.eval("self")
1.send(:eval, "binding")
```

```ruby
# other explicit method?
Proc.new{ ... }.isolate
```

matz: I prefer the idea but it may have escape hatches

https://bugs.ruby-lang.org/issues/21028 Method for finding why an object isn't Ractor shareable

https://bugs.ruby-lang.org/issues/20971 Deprecate `rb_path_check`

https://bugs.ruby-lang.org/issues/20996 Embed Ruby 3.4 Failure
https://bugs.ruby-lang.org/issues/21111 RbConfig::CONFIG['CXX'] quietly set to "false" when Ruby cannot build C++ programs

katei: I'll check other languages.

https://bugs.ruby-lang.org/issues/21104 Net::HTTP connections failing in Ruby >= 3.4.0 on macOS with Happy Eyeballs enabled

good luck shioi-san

https://bugs.ruby-lang.org/issues/21119 Programs containing `Dir.glob` with a thread executing a CPU-heavy task run very slowly.

https://bugs.ruby-lang.org/issues/20919 IO#seek and IO#pos= do not clear the character buffer in some cases while transcoding

ask akr, nobu to review the PR.

https://bugs.ruby-lang.org/issues/21121 Ractor channels

ko1: I also want it.

---

https://bugs.ruby-lang.org/issues/21100 DevMeeting before or after RubyKaigi2025
