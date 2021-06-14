# Ruby_Notes
A general doc for Ruby notes



# Glossary

## **blocks**
A block is a chunk of code that is passed into a method - such as `map` or `each` - to be executed. There are two ways to pass a block into a method. We can use `{...}` brace syntax for blocks that only contain a single line of code, or, we can use `do...end` syntax for multiline blocks. Brace `{...}` blocks and `do...end` blocks are functionally equivalent, we just prefer do...end for blocks that contain many lines.

Blocks can accept parameters if we name them between pipes (`|param_1, param_2, etc`.|). We know that when we pass a block into `map`, `map` will execute the block, passing in every element of the array one by one. It will take each evaluation of the block and make that value an element of the new array that the `map` method will return.

Blocks are somewhat similar to methods in that both can contain lines of code as well as take in parameters. However, an important distinction to make is that the `return` keyword pertains to *methods*, not *blocks*. Let's take a look at a common pitfall:

```ruby
# Correct:
def double_eles(arr)
  arr.map do |ele|
    ele * 2
  end
end
double_eles([1,2,3]) #=> [2,4,6]

# Incorrect:
def double_eles(arr)
  arr.map do |ele|
    return ele * 2
  end
end
double_eles([1,2,3]) #=> 2
```

Looking at the incorrect implementation of `double_eles`, we use the `return` keyword within the block. If we use return we will be returning out of the entire `double_eles` method on the first iteration of map. In other words, using `return` in a block will not make the block evaluate to the return value. The `return` will force the outer method to evaluate to the return value. This is a huge reason why we should treat *blocks* and *methods* as somewhat distinct concepts.

# **Using Methods as Blocks**

TLDR: `:even?` for example, is a symbol represending the `Integer#even?` method. Placing the `&` prior to that symbol converts that symbol into a block of code, meaning `&:even?` is converted to `{|n| n.even? }`.

It is very, very common to have blocks that take an argument and call a single method. For example:

```ruby
["a", "b", "c"].map { |str| str.upcase }  #=> ["A", "B", "C"]
[1, 2, 5].select { |num| num.odd? }       #=> [1, 5]
```

Ruby allows us to use cleaner syntax when we have simple blocks that follow the above pattern. Let's refactor the above example to use this shortcut:

```ruby
["a", "b", "c"].map(&:upcase) #=> ["A", "B", "C"]
[1, 2, 5].select(&:odd?)      #=> [1, 5]
```

You may find this syntax quite strange. Let's notice a few things about how we are using `map`. We call `map` and pass in a single argument, `&:upcase`. `:upcase` is a symbol referring to the String#upcase method. We use `&` to convert this "method" into an object that we can pass into `map`. In Ruby, we cannot directly pass a method into another method, so we need to use the `&` operator. In the next lecture we'll explore the intricacies of `&`.

For now, here's a hard and fast rule you can use to optimize some blocks. If you are calling a method like `map`, passing a block that has this general structure:

```ruby
array.map { |block_param| block_param.method }
```

Then you can rewrite it as:

```ruby
array.map(&:method)
```

When employing this trick, be aware of what `&:method` you are using. The method you choose should be compatible with your data:

```ruby
["a", "b", "c"].map(&:upcase) # => ["A", "B", "C"]
[1, 2, 3].map(&:upcase)       # NoMethodError: undefined method 'upcase' for Integer
```

The second map is invalid because we can't use `upcase` on numbers!

[Source](https://open.appacademy.io/learn/full-stack-online/software-engineering-foundations/blocks-notes)
