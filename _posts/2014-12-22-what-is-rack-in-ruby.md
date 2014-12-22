---
layout: post
title: What is Rack in Ruby?
date: 2014-12-22
category: ruby
---

If you have been in the Ruby community for any length of time, you have likely heard about Rack, probably along the lines of something being Rack-based, implements Rack or Rack middleware. Hopefully this post will help you understand exactly what everyone is talking about.

<!--more-->

[Rack](http://rack.github.io/) describes itself as:

> a minimal interface between webservers supporting Ruby and Ruby frameworks

While the concise explanation is helpful, it may still leave newcomers to programming slightly confused about what that actually means.

A minimal interface is nothing more than a component that is common ground and sits between two other components that need to work together.

When building web apps with Ruby we always have two components that need to work together; the web server and the Ruby app.  

To understand why Rack is so useful let's assume we want to build a personal website about our hobbies, experiences, portfolio etc. using [Ruby on Rails](http://rubyonrails.org/) and see how our web app would handle requests from users when we **don't** and **do** use Rack. 

## Life without the Rack interface

We just installed Rails and created our app and have just noticed that it uses [WEBrick](http://www.ruby-doc.org/stdlib-2.0/libdoc/webrick/rdoc/WEBrick.html) as its web server because that’s what it has been built to talk to. 

So here’s the journey of what a request for your portfolio page might look like:

![If Rack did not exist]({{ site.url }}/images/rack-in-ruby/if-rack-didnt-exist.png)

Everything is working perfectly and we finish building our website.

After some time passes we find out that a new web server called [Puma](http://puma.io) is available for Ruby apps that is much faster than WEBrick.  The Ruby community is excited about using it, yet the most popular framework for Ruby (Rails), the one that we chose for our app, only knows how to speak to WEBrick and it would take months of work to get it to talk to Puma. 

This is going to cause issues:

* The Ruby on Rails community may forever be behind with their web server.
* Developers are hesitant to build new web servers knowing it takes a lot of work for others to switch to it.

We need something that will give us flexibility in the web server and framework we use without sacrificing lots of time to switch around.  Something in the middle that both web servers and Ruby apps can agree to speak to who can translate for both of them.  Thus Rack is born!

## Reality with the Rack interface

Thankfully we have the Rack interface to be the common ground between our web servers and Ruby apps.  Now that we’re using the Rack interface, a request for our portfolio page will look like this:

![How Rack works in Ruby]({{ site.url }}/images/rack-in-ruby/rack-does-exist.png)

So we can see that our WEBrick web server and Rails app both know how to communicate with Rack.  This means that we can now change our web server or Ruby app framework at any time without any worry about communication between our server and app, so long as what we’re switching to uses the Rack interface.

### Example: Switching from WEBrick to Puma

The Puma webserver has been “built for speed and parallelism”, and lucky for us it implements the Rack interface, which makes switching to it a simple matter of replacing the WEBrick webserver with the Puma one:

![Example of Puma Rack and Rails]({{ site.url }}/images/rack-in-ruby/puma-rack-rails.png)

### Example: Switching from Ruby on Rails to Sinatra

The simple flexibility also applies to the Ruby framework side of this portfolio request.  We can simply decide to start using the Rack based Ruby framework known as Sinatra as our Ruby framework and replace our Ruby on Rails app without any modifications to the webserver we’re using: 

![Example of Puma Rack and Sinatra]({{ site.url }}/images/rack-in-ruby/puma-rack-sinatra.png)

## Wrap up

The Rack interface sits between our chosen web servers and Ruby apps to give us complete flexibility in switching between what technologies we want to use in our stack.

Benefits of Rack:

* Easily switch web servers 
* Easily switch Ruby frameworks 

### Coming soon

Now that we know how Rack works we can start building some cool Rack middleware. 

