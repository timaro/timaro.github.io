---
layout: omniref_post
sidebar: false
title: "Matz at RubyConf 2014: Will Ruby 3.0 be Statically Typed?"
date: 2014-11-17 11:00:10 -0800
comments: true
author: Tim Robertson
categories: 
- omniref
- ruby
- programming
- MRI
- rubyconf
---

We're at RubyConf 2014, where [Matz](http://en.wikipedia.org/wiki/Yukihiro_Matsumoto) has 
just given his keynote, focusing on the future of the Ruby language. 

Matz presented a few major ideas for the next version of Ruby, including improvements in 
concurrency support, and JIT compilation (which he said might result in a 2x-4x speedup...maybe),
but he spent *most* of the talk going into the details of how Ruby might go about implementing
static typing.

## Is Statically Typed Ruby Still Ruby?

Matz referenced [feature #9999](https://bugs.ruby-lang.org/issues/9999) as partial motivation 
for the talk, where it was suggested by Davide D'Agostino that "type annotations" could be 
used to "suggest" types to the interpreter:

```ruby
def connect(r -> Stream, c -> Client) ->  Fiber
```

Matz noted that so-called "optional" typing is sort of infectious: in any real
program, "optional" static typing quickly becomes static typing with 99% coverage, or it isn't
very useful at all. Also, his feeling is that static typing is actually a violation
of the [Don't Repeat Yourself](http://en.wikipedia.org/wiki/Don't_repeat_yourself) 
principle...but it's also clear that it offers a number of benefits,
such as easier refactoring, and possible performance improvements.  And most importantly, Ruby 
without dynamic typing "isn't really Ruby"...so where does that leave us?

## Your Types Are Looking a Bit Soft...

To square this circle, Matz introduced everyone to the notion of "Soft Typing", introduced
by Mike Fagan in his [1991 dissertation](http://scholarship.rice.edu/handle/1911/16439).

The general idea of Fagan's work is that while static typing is good for catching errors,
it's too strict, and excludes a lot of "good" programs. So Fagan proposed a new kind of system
of no-declaration, best-effort type checking where *"the soft type checker inserts explicit 
run-time checks" to catch type violations.  Matz gave a few simple examples in his talk:

```ruby
a = 1 # the type of a is now Integer
```

or, perhaps more interesting:

```ruby
def foo(x)
  x.to_int  # now all x must have .to_int
end

foo(1)   # OK
foo('a') # no good! doesn't have to_int!
```

More concretely, Matz described the envisioned system as one where a "type" is defined
by a set of methods and the names / types of their arguments, so that, for example,
classes would be defined as types by their method signatures. The interpreter would then
use those types to variable and argument types at runtime.

Interestingly, Matz suggested that this kind of system would initially be implemented as
a static analyzer, and that it would require new restrictions
on some common Ruby methods, such as `require` and `methods_missing`, in order to 
support a type-checked universe. It isn't totally clear what those restrictions would
need to be, but it's a fun thought experiment.

## Still "Just an Idea"

Will Ruby 3.0 be the first Ruby with type checking? Again, Matz emphasized that 
this is just an idea, and that, historically, only about
30% of prior ideas from RubyConf have made it into production.  But it if this pans
out, it could be the biggest language change that Ruby has seen in years. Exciting!