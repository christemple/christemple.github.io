---
layout: post
title: Delegating with Forwardable in Ruby
date: 2014-11-26
category: ruby
---

Delegation in programming is simply a matter of passing responsibility to an object more suitable to handle a request.
It's good to keep things simple and delegate when you can, that's why Ruby has made doing so easy by providing the Forwardable module.

<!--more-->

Including the [Forwardable module](http://www.ruby-doc.org/stdlib-2.0/libdoc/forwardable/rdoc/Forwardable.html)
in our classes gives us the ability to forward a request/method call to an underlying attribute more appropriate to deal with it.

## Forwardable in the wild

Let's assume that we're building an app for an agency with agents who have different levels of clearance.

```ruby
# agent.rb
class Agent
  def initialize(name)
    @name = name
  end

  def has_clearance?(clearance_level_required)
    clearance_level < clearance_level_required 
  end
end
```

If we lived in a simple world, this code would suffice in getting the job done; 
reality however is that now there are multiple aspects to security at this organisation such as: 
access rights to files, rooms, secret headquarters etc.

Given all of these extra security features, should the agent be responsible for taking care of them all?
Nope! If the agent is anything like me, they would delegate it.

```ruby
# agent.rb
class Agent
  attr_reader :name, :clearance_level
  def initialize(name, level)
    @name = name
    @clearance_level = ClearanceLevel.new(level)
  end
end

# clearance_level.rb
class ClearanceLevel
  def initialize(agent)
    @level = level
  end
  
  def is_newb?
    @level.zero?
  end 

  def is_secret?
    @level > 4
  end

  def is_top_secret?
    @level == 10
  end

  def has_clearance?(level_required)
    @level <= level_required 
  end
end
```

Okay, so now the ClearanceLevel class is responsible for deciding on what level of clearance an agent 
has, however now if we want to do anything useful with our agent the code is going to look 
something like this:

```ruby
agent = Agent.new("Phil", 10)
if agent.clearance_level.has_clearance?(4)
  puts "Welcome to Sector 4 #{agent.name}."
  if agent.clearance_level.is_top_secret?
    puts "You've got top secret agent clearance!" 
  end
end
```

Having to reference `agent.clearance` each time to find out information on an agents' clearance
level could get repetitive, it would be a lot nicer if we could just ask clearance questions on 
`agent`, for example:

```ruby
agent = Agent.new("Phil", 10)
if agent.has_clearance?(4)
  puts "Welcome to Sector 4 #{agent.name}."
  if agent.is_top_secret?
    puts "You've got top secret agent clearance!" 
  end
end
```

### Without Forwardable
Without knowing about the Ruby Forwardable module, to accomplish this we would write wrapper functions in `agent.rb` 

```ruby
# agent.rb
class Agent
  attr_reader :name, :clearance_level
  def initialize(name, level)
    @name = name
    @clearance_level = ClearanceLevel.new(level)
  end

  def has_clearance?(level)
    @clearance_level.has_clearance?(level)
  end

  def is_top_secret?
    @clearance_level.is_top_secret?
  end
  
  [...]
end

``` 
### With Forwardable
But, since we now know that the Forwardable module can forward  method calls to an object more responsible,
we can use it to delegate the wrapper methods to `@clearance_level` in the `agent` like so:

```ruby
# agent.rb
class Agent
  extend Forwardable

  attr_reader :name, :clearance_level
  def initialize(name, level)
    @name = name
    @clearance_level = ClearanceLevel.new(level)
  end

  def_delegators :@clearance_level, :is_newb?, :is_secret?, :is_top_secret?, :has_clearance?
end

``` 

So by using the Forwardable module we can now call our clearance methods directly on our agent objects
were they'll be delegated to the `@clearance_level`, meaning we've been able to keep `agent.rb` simple 
and clean instead of littering it with wrapper methods. Yay!

I hope you find this helpful.

Let me know ways that you've used Forwardable!
