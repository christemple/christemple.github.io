---
layout: post
title: Shorthand syntax for each, map, reduce & more
date: 2014-11-23
category: ruby
redirect_to: https://medium.com/@christemple/shorthand-syntax-for-each-map-reduce-more-a790ba12edc9
---

Ruby has a nice shorthand syntax for methods that use blocks and accept Procs, which is very useful when working with enumerables e.g. each, map, reduce etc.

<!--more-->

If you’ve been unaware of this syntax, to sum up the numbers in an array you’ve probably been writing code that looks like this:

```ruby
[1,2,3,4].reduce do |total, number|
  total + number
end
=> 10
[1,2,3,4].reduce { |total, number| total + number }
=> 10
```

The shorthand way of summing up these numbers can be done using the following:

```ruby
[1,2,3,4].reduce(&:+)
=> 10
[1,2,3,4].reduce(100, &:+)
=> 110
```

As a personal preference I find this shorthand method much cleaner and easier to read.  Another example of how we can use this with our own objects would be:

```ruby
users.map(&:name)
=> [“Phil Coulson”, “Nick Fury”]
# Assuming a collection of user objects that have a ‘name’ method
```

## How does it work?

The ampersand (&) character does something special when it is used on the last argument of a method call or definition: it will attempt to call the to_proc method on the argument itself.

```ruby
[1,2,3,4].reduce(&:+)
```

becomes:

```ruby
[1,2,3,4].reduce(:+.to_proc)
```

[Symbol#to_proc](http://ruby-doc.org/core-2.1.4/Symbol.html#method-i-to_proc) creates a Proc object that is responsible for calling the :+ method on the object that gets yielded by reduce, for example if we were to write the to_proc method it might look like:

```ruby
def to_proc
  Proc.new { |obj, *args| obj.send(self, *args) }
end
```

\* This example assumes you already know how the [Object#send](http://ruby-doc.org/core-2.1.4/Object.html#method-i-send) methods works.

Given what we know so far, we can assume the following:

```ruby
[1,2,3,4].reduce(0, &:+)
```

becomes:

```ruby
[1,2,3,4].reduce(0, :+.to_proc)
```

`to_proc` would return a Proc similar to:

```ruby
[1,2,3,4].reduce(0, &(Proc.new { |number, total| number.send(:+, total) }))
=> 10
```

So each time our reduce methods yields our number, it will:

1. 1. Call the Proc object the Symbol class created passing (total, number)
2. 2. Sends the `:+` method to be called on number passing total as a parameter.

## Recap

```ruby
#Shorthand way of summing numbers
[1,2,3,4].reduce(&:+)

# Longer/Verbose way
[1,2,3,4].reduce { |number, total| number + total }
```

Hopefully you find this shorthand syntax as useful and clean as I do.  

Let me know what you think.
