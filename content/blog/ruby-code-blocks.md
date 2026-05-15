---
title: "Ruby Code Blocks"
date: 2019-01-13T00:00:00-05:00
tags: []
aliases:
  - /blog/2019/01/13/ruby-code-blocks/
---

# Code Blocks

Ruby code blocks are from the closure family

Ruby blocks are found throughout ruby. They are a powerful feature that allow
you to pass snippets of code to enumerable methods (e.g. each, select, detect)
as well as custom methods useing the yield keyword.

Blocks are nothing new, they use the computer science concept called closures.
This concept was invented by Peter J. Landin in 1964. Closures were adopted by
a version of a Lisp called Scheme in 1975.

## What is a block

Blocks are a concept that allows programmers to extend their applications to have
a domain specific language (DSL). A DSL is a form of metaprogramming that makes
it easier to program and configure complex systems.

If you have ever used a method on an Array to loop through elements in that Array
you have used a block.

```ruby
["alex", "gina", "pam"].each do |inserted_name|
  puts "Hello, #{inserted_name}"
end
```

A block starts with `do` and terminates with `end`. A block can have a parameter
defined between the pipes `|x|` at the begin of a block definition. When defining
a block on a single line use braces `arr.each { |i| }`.

This single line line block is often called an inline block.

```ruby
["alex", "gina", "pam"].each { |inserted_name| puts "Hello, #{inserted_name}" }
```

When writing a block that will need to be more than one line it `do` / `end` should
be used. These are called multi-line blocks.

## Methods

All ruby methods can be associated with a block. A block allows programmers to group
statements together with a method call.

```ruby
def something
  puts "do something"
end
```

If a the method does not call `yield` the block will not be called. The method
will ignore the block being passed.

```ruby
something do
  puts "do something else"
end
```

Here the method `something` is being passed a block. However as seen below shows
the output of the `puts` statement in the method is called and the statement in
the block is not called.

```
do something
```

To execute the block passed to method it will need to be yielded from within the
method.

## Yield

An associated block can be invoked within the method using the keyword `yield`.
This allows the programmer to control the flow of the block’s execution. Without
calling `yield` the block is ignored, as seen above.

```ruby
def something_else
  puts "Begin"
  yield
  puts "End"
end
```

The contents of the block will be executed when the `yield` statement is called.
Once the block is yielded the method is returned control of execution.

```ruby
something_else do
  puts "yielded code"
end
```

The method executing the block can run any code before or after the block is invoked.

```
Begin
yielded code
End
```

When `something_else` is called with a block the method prints out the string
`Begin`. The block is then invoked, printing out `yielded code`. The method is then
returned control printing out the final string `End`.

## Passing Arguments

Yield blocks can be passed arguments within a method. These arguments can then
be used within the blocks as a parameter.

```ruby
def run_three
  [1,2,3].each do |i|
    yield(i)
  end
end
```

The parameter will be defined between the pipes `|x|`. The arity of the parameters
should match the number of arguments being passed into the yield statement.

```ruby
run_three do |i|
  puts "and a #{i}"
end
```

The output:

```
and a 1
and a 2
and a 3
```

Above the we can see that the method `run_three` is being envoked with a block that
accepts an argument. Within the `run_three` method we have an array of 3 numbers
`[1,2,3]`. The method iterates through the array and calls yield with each value in
the array.

## Blocks as Arguments

A block can be passed around as an argument to a method. This can be done by adding
an argument to the method with an ampersand `&block`.

```ruby
def pass_method(&block)
  block.call
end
```

Invoking the method `call` on the block will execute the block. When the passed
block is stored as a variable it is changed to a Proc.

```ruby
pass_method { puts "yielded code" }
```

The block can still have params passed to it, like a block that is yielded.

```ruby
def my_iterator(things, &block)
  things.each do |thing|
    block.call(thing)
  end
end

van = []

my_iterator ['John', 'Paul', 'George', 'Ringo'] do |beatle|
  van << beatle
end

puts "#{van.join(', ')} are in the van"
```

A block is not execute at the time it is encountered. The current context is
stored with the block for later use before entering the method. This mean local
variables called in the block will have the same value a when the block was passed
to the method call.

## Conclusion

A block can be used to create some commonly used code that allows you to
inject custom code where it is used. This comes in handy when you need to dry
up your code but find that the code differs in a few key places.