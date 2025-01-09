# DevMeeting-2025-01-09

https://bugs.ruby-lang.org/issues/20949

## DateTime and location

* 2025/01/09 (Thu) 13:00-17:00 JST @ Online

## Next Date

* 2025/02/13 (Thu) 13:00-17:00 JST @ Online

## Announce

### About release timeframe

## Check security tickets

[secret]

## Ordinary tickets

### [[Feature #20993]](https://bugs.ruby-lang.org/issues/20993) Allow `class <constant-path> = <expression>` syntax (byroot)

* Useful for defining `Struct` and `Data` subclasses. See [Bug #20943]
* It's just sugar for `<constant-path> = <expression>; class <constant-path>`
* Would be allowed for `module` as well.

Preliminary discussion:

```ruby
MyStruct = Struct.new(:foo, :bar)
class MyStruct
  # body
end

class MyStruct < Struct.new(:foo, :bar)
  # ...
end

class MyStruct = Struct.new(:foo, :bar)
  # body
end

class expr
end
class (Foo < Bar)
end

class (expr)::C
end

struct MyStruct
  p self #=> MyStruct
  p MyStruct.new
  field :foo, :bar
  p MyStruct.new(foo: 1, bar: 2)
  field :baz
  p MyStruct.new(foo: 1, bar: 2, baz: 3)
end

struct MyStruct(foo, bar)
end

class MyStruct = Struct.new(
  :foo,
  :bar,
  :baz,
  :qux
)
  Foo = 1
end

case class MyStruct
end
```

```ruby
module R
  refine String do
    C = 1
  end
  p C #=> 1
end

module R
  refine String do
    C = 1
  end

  refine Object do
    C = 2
  end
  p C #=> 2
end
# t.rb:5: warning: not defined at the refinement, but at the outer class/module
# t.rb:9: warning: not defined at the refinement, but at the outer class/module
# t.rb:9: warning: already initialized constant R::C
# t.rb:5: warning: previous definition of C was here
```

```
F = Class.new do
  C = 1
end

Cls.class_eval do
  Foo = 1
end
Cls::Foo

p C
p F::C #=> t.rb:8:in '<main>': uninitialized constant F::C (NameError)
```

```
Measure = Data.define(:amount, :unit) do
  NONE = Data.define
end

f = -> do
  NONE = Data.define
end
Measure = Data.define(:foo, :bar, &f)

lambda(&f)
```

Discussion:

* matz: I think a class reopen `S = Struct.new(...); class S; ...; end` or a class inheritance `class S < Struct.new(...); ...; end` are enough. I don't think solving this issue is worth adding a new syntax.
* (many random ideas are proposed...)
* matz: is it possible to fix #20943 so that NONE should be defined under Measure?
* ko1: it will degrade performance

Conclusion:

* matz: rejected.


### [[Feature #20878]](https://bugs.ruby-lang.org/issues/20878) A new C API to create a String by adopting a pointer (byroot)

* Following the last discussion I think the API could receive a pointer to the `free` function to use.
* e.g. `str = rb_enc_str_adopt(buf->ptr, buf->len, buf->capa, rb_utf8_encoding(), free);`
* This allow the API to fallback to a copy if the free function isn't compatible with `ruby_xfree`

Discussion:

* nobu: I am still against such an API.
* nobu: I would like to investigate the benchmark implementation. I hope to improve.
* akr: if tuning is the goal, saving a pointer to the free function in a string instance may be an overhead. We may have two C APIs: one is for a buffer allocated by xmalloc, and the other is for a buffer allocated by non-xmalloc (if really needed).

Conclusion:

* (matz: want to wait for nobu's trial)

### [[Feature #21005]](https://bugs.ruby-lang.org/issues/21005) Update the source location method to include line start/stop and column start/stop details (eregon)

* How about adding end line and start/end columns to `#source_location`? It seems straightforward and compatible.
* How about a new `#code_location` method returning an object with line/column/offset info and also a convenient method to return the source code snippet of that location? See https://bugs.ruby-lang.org/issues/21005#note-3

Preliminary discussion:

```ruby
def foo
<<END; end
xxx      #^- end of "foo"?
END
  #^- or here?

def foo; <<A; end; def bar; <<B; end
FOO
A
BAR
B
```

```ruby
class Demo
  include Initable[%i[req name], [:key, :default, proc { Object.new }]]
end

# ↓

class Demo
  eval %q(
    def initialize(name, default = Object.new)
      @name = name
      @default = default
    end
  )
end

class Demo
  include Initable[%i[req name], [:key, :default, proc { <<-END }]]
    foo
    bar
  END
end

method(:foo).prism_node
f = proc {}
f.prism_node

Prism.for(method(:foo))
Prism.for(f)
```


Discussion:

*

Conclusion:

* matz: Proc#source_location should return `[file, start_line, start_column, end_line, end_column]` as [[Feature #6012]](https://bugs.ruby-lang.org/issues/6012) was [concluded](https://github.com/ruby/dev-meeting-log/blob/28e0f45f334b31e60c7e07d25b6b6cb3655b2c75/2024/DevMeeting-2024-10-03.md?plain=1#L176).
* matz: `Prism.node_for(Proc | Method| ...)` is also preferable. I left the method name to Kevin or Aaron.

### [[Feature #21000]](https://bugs.ruby-lang.org/issues/21000) A way to avoid loading constant required by a type check (nobu)

```ruby
if defined?(NameSpace::ClassName) and obj.is_a?(NameSpace::ClassName)

# ↓

if obj and defined?(NameSpace::ClassName) === obj
```

* want to avoid repetation of the constant
* want to avoid kicking autoload

```ruby
class TrueClass
  def true?
  end
end

p defined?(obj.is_a?(Foo).true?) #=> "method" if Foo is defined and obj is Foo
p defined?(obj.is_a?(Foo).true?) #=> nil      if Foo is not defined or if obj is not Foo
```

```ruby
p defined?(-(obj.is_a?(Foo) && 1)) #=> "method" if Foo is defined and obj is Foo
p defined?(-(obj.is_a?(Foo) && 1)) #=> nil      otherwise

p defined?(Foo === obj)
p defined?(String === 1) #=> truthy
```

no conclusion

## [[Bug #20965]](https://bugs.ruby-lang.org/issues/20965) `it` vs `binding.local_variables`

* matz: the following behavior is a bug. It should be fixed

```ruby
"foo".tap do
  _1
  "bar".tap do
    p binding.local_variable_get(:_1) #=> "foo"
  end
end
```

* mame: Shouldn't we stop treating numbered parameters and “it” like local variables? If we need to access them via binding, we should not extend local_variable_get, etc., but rather create a dedicated method
* (no objection)
* mame: I will create a ticket later

```ruby
p(proc { _1; binding.local_variables }.call)  # [:_1] => []

"foo".tap do
  _1
  "bar".tap do
    p binding.local_variables         #=> current -> [:_1], proposed -> []
    p binding.local_variable_get(:_1) #=> current -> "foo", proposed -> NameError
    p binding.numbered_parameters         #=> []
    p binding.numbered_parameter_get(:_1) #=> NameError
  end
  p binding.local_variable_get(:_1)     #=> "foo" => NameError
  p binding.numbered_parameters         #=> [:_1]
  p binding.numbered_parameter_get(:_1) #=> "foo"
  p binding.it_defined? #=> true
  p binding.it_get(1)   #=> "foo"
  p eval('_1') #=> NameError (from older versions)
end

[1].each{
  p it
  [2].each{
    p it #=> should be 2
  }
}
```

```ruby
it = 5
[1].each{
  p it #=> 5
  [2].each{
    p eval('it') #=> 5
  }
}

## another file
[1].each{
  p it #=> 1
  [2].each{
    p it         #=> 2
    p eval('it') #=> undefined local variable or method 'it' for main (NameError)
  }
}

```

```
$ ruby -ve '
1.times do
  p _1
> binding.irb
> end
> '
ruby 3.3.1 (2024-04-23 revision c56cd86388) [x86_64-linux]
0
irb(main):001> ls
Object.methods: inspect  to_s
IRB::HelpersContainer#methods: conf
PP::ObjectMixin#methods: pretty_print  pretty_print_cycle  pretty_print_inspect  pretty_print_instance_variables
Kernel#methods:
  !~                          <=>                       ===                    class
  clone                       define_singleton_method   display                dup
  enum_for                    eql?                      extend                 freeze
  frozen?                     hash                      inspect                instance_of?
  instance_variable_defined?  instance_variable_get     instance_variable_set  instance_variables
  is_a?                       itself                    kind_of?               method
  methods                     nil?                      object_id              pretty_inspect
  private_methods             protected_methods         public_method          public_methods
  public_send                 remove_instance_variable  respond_to?            send
  singleton_class             singleton_method          singleton_methods      tap
  then                        to_enum                   to_s                   yield_self
BasicObject#methods: !  !=  ==  __id__  __send__  equal?  instance_eval  instance_exec
locals: _  _1
irb(main):002> _1
-e:2:in `block in <main>': undefined local variable or method `_1' for main (NameError)

_1
^^
Did you mean?  _
        from <internal:kernel>:187:in `loop'
        from <internal:prelude>:5:in `irb'
        from -e:4:in `block in <main>'
        from <internal:numeric>:237:in `times'
        from -e:2:in `<main>'
```

## [[Feature #20925]](https://bugs.ruby-lang.org/issues/20925) Allow boolean operators at beginning of line to continue previous line

nobu: is it ok to treat only `&&`, `||`, `and` and 'or'?
matz: good
mame: `&` doesn't have to continue a line, right?
matz: ok
akr: i want a clear criteria for line continuation
shyouhei: it's a feeling, so we have to discuss it individually

```ruby
# akr: how about ^, <, =
1
^ 2

1
< 2

foo.bar.baz.qux
= 1

# ko1's: three-space indent for condition
if request.secret_key_base.present? &&
   request.encrypted_signed_cookie_salt.present? &&
   request.encrypted_cookie_salt.present?
  request.encrypted_cookie
end

# from debug gem
        ObjectSpace.each_iseq do |iseq|
          if DEBUGGER__.compare_path((iseq.absolute_path || iseq.path), self.path) &&
             iseq.first_lineno <= self.line &&
             iseq.type != :ensure # ensure iseq is copied (duplicated)
            yield iseq
          end

# mame's
if request.secret_key_base.present?
&& request.encrypted_signed_cookie_salt.present?
&& request.encrypted_cookie_salt.present?
  request.encrypted_cookie
end
```

* ko1: investigated other language linters' behaviors

<img width="351.75" alt="image.png (35.6 kB)" src="https://img.esa.io/uploads/production/attachments/21206/2025/01/09/10534/122170fb-4697-4947-b8d8-38e4ba0869fb.png">

https://go.dev/play/p/Nu3IZhYeSfq

<img width="776.25" alt="image.png (22.5 kB)" src="https://img.esa.io/uploads/production/attachments/21206/2025/01/09/10534/6e621918-ab44-4785-b1b6-faae8b587255.png">

https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=532635c536587542f4aca10cd635855d

<img width="672" alt="image.png (12.5 kB)" src="https://img.esa.io/uploads/production/attachments/21206/2025/01/09/10534/7268dda7-01c4-4bdc-a03e-ee0d406b6dfe.png">

https://prettier.io/playground/#N4Igxg9gdgLgprEAuEAdKA3AhgJwARaFHEmlnkWVVF4C8eAjANzroCWAZngBTV-8CBeAHyM86AGQSCg2XNki8AJnR4pM+Zq1lFAZgCUeYKxgB6U+gC+6EABoQEAA4w20AM7JQuHBADuABVwEDxQsABtfLABPD3sAIxwsMABrOBgAZSwAWzgAGTYoOGQOcLc4eMSUtPTHJIKAc2QYHABXcpAyrLYm1va4AA9HOBw2HNhwgBVhqFw2OBCSsLL7NwawuABFFoh4YtL2gCs3fvS1ze3dpEXlkABHC7h-H0cQkCw3AFpCuAATX7sQM0sGwwg0AMIQLJZLDIN5hMIA1ZQerrACCMGabDiLXg-mG+UKeyW7QAFjAsmEAOoktjwNy1MBwdLBWlsDC0qKwsBuWIgDBtACSUD+sHSYBGzlRwvSMCi6yJN0cPjKlMSjlhSvmwwwRXsBTKOBgTyw9WhCvatRwBthcSwcTgCPsSoKMEpbB+MBJyAAHAAGew4OD3NiB42mmFXfb2GB2t0er1IJT2FplCZ2hZRkBwLL2n5-H65LDIlomuAAMQgOGhGIasKwOIgIEsliAA

<img width="690.75" alt="image.png (17.0 kB)" src="https://img.esa.io/uploads/production/attachments/21206/2025/01/09/10534/220f3c58-6796-4c90-990e-3402e08728c0.png">

https://black.vercel.app/?version=stable&state=_Td6WFoAAATm1rRGAgAhARYAAAB0L-Wj4AKZAN9dAD2IimZxl1N_Wg0-ASLt-SiE2GGPCZO80tmeTKdHumDx3f9ojdj2Qt0JgnxRgXJP05ZNnFsVT020HOAocYfhvfkjUEm1HGWYXeqrfaMXuz0AMNnenC9lXcqDzRQBBONcdDwnC7rJ5J9bRQoWqd98SxtftzmtTVLwz_KsVw-Vx93f8IKpKvsehB0zyfhQAbOFquSQgffSOqfRSTBMHiZ1q3lGqTvUnqqkGU7L_mnbahSjL8vhRdJmICsbHrJ4KY1tk62BdS8slIfikU8MAzI6XOA3elgs5q61pV2VG9ysUQAAAPNkxz5qQIfqAAH7AZoFAACc11ZVscRn-wIAAAAABFla

## [[Feature #20987]](https://bugs.ruby-lang.org/issues/20987) Add dbg - minimal debugging helper

* `P=1` envval
* `ruby -d` (check `$DEBUG`)
* `puby` (another command)
* `ruby -W:p app.rb`

---

https://bugs.ruby-lang.org/issues/21009 Removed old archives from top-level of cache.ruby-lang.org.

* matz: accepted

https://bugs.ruby-lang.org/issues/20920 When loading a file, `__FILE__` gets relative paths expanded only when they start with "./"

```
$ ruby foo.rb
foo.rb   # This should be kept

$ ruby ./foo.rb
./foo.rb # This should be kept

$ ruby -e 'load "foo.rb"'
foo.rb # This should be a full path

$ ruby -e 'load "./foo.rb"'
/full/path/to/foo.rb
```

* matz: Let's try it

https://bugs.ruby-lang.org/issues/20953 `Array#fetch_values` vs `#values_at` protocols

* matz: `Array#fetch_values` should expand a Range (as `Array#values_at`)

https://bugs.ruby-lang.org/issues/20968 `Array#fetch_values` unexpected method name in stack trace

* matz: I like koic's expetation if possible

https://bugs.ruby-lang.org/issues/20205 Enable `frozen_string_literal` by default
https://bugs.ruby-lang.org/issues/20974 Required and optional anonymous parameter show differently in `Proc#parameters`

* matz: `p(proc { |(_a)| }.parameters)` should return `[[:opt]]` instead of `[[:opt, nil]]`

```ruby
proc {|(_a), (_a)| }.parameters
#=> [[:opt, nil], [:opt, nil]]

proc {|a, b| }.parameters
#=> [[:opt, :a], [:opt, :b]]
```

https://bugs.ruby-lang.org/issues/20971 Deprecate `rb_path_check`
https://bugs.ruby-lang.org/issues/20564 Switch default parser to Prism
https://bugs.ruby-lang.org/issues/20980 `Range#size` new TypeError vs semi-open ranges
https://bugs.ruby-lang.org/issues/20498 Negated method calls
https://bugs.ruby-lang.org/issues/20884 reserve "Ruby" toplevel module for Ruby language
https://bugs.ruby-lang.org/issues/20899 Reconsider adding Array#find_map

```ruby
[1, 2, 3].find_map { it * 2 if it.even? }   #=> 4
[1, 2, 3].find { break it * 2 if it.even? }
[1, 2, 3].find { it.even? }&.then { it * 2 }
```

https://bugs.ruby-lang.org/issues/21015 [DRAFT] Add in a `-g` flag, like `-s` but with a few more quality of life features

```
$ ruby -s -e '' -- -$
ruby: invalid name for global variable - -$ (NameError)

$ ruby -s -e '' -- -あ
ruby: invalid name for global variable - -あ (NameError)
```

https://bugs.ruby-lang.org/issues/21018 Show invalid command line option more properly
