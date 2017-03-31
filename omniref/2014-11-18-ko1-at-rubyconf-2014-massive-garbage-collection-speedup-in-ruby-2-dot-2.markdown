---
layout: omniref_post
sidebar: false
title: "Ko1 at RubyConf 2014: Massive Garbage Collection Speedup in Ruby 2.2"
date: 2014-11-18 12:49:51 -0800
comments: true
author: Tim Robertson
categories: 
- omniref
- ruby
- programming
- MRI
- rubyconf
- garbage collection
- memory management
---

Today at RubyConf 2014, [Koichi Sasada](http://www.atdot.net/~ko1/) (Ko1) gave a detailed 
description of some massive  speedups that are going to be released as part of Ruby 2.2. In 
addition to some minor changes to APIs and internal code cleanups, the the big wins for Ruby 2.2
appears to be a series of performance optimizations: a massive speedup of the performance of 
keyword arguments, the long-desired addition of garbage collection for Ruby symbols, and not
least, the addition of *incremental garbage collection*, which promises a huge speedup in 
GC pause times for most Ruby applications.

## Keyword Arguments on Speed

Keyword parameters are a relatively recent addition to Ruby, and with major libraries
like Rails promising a migration to kwargs in upcoming versions, the performance of 
function calls containing kwargs is going to be important. So it was good news when Ko1 
announced that these calls will be getting *15 times faster* in Ruby 2.2.  He showed a
rather covincing test case where a test case of 10 million calls to a kwarg-containing
function went from 17 seconds in Ruby 2.1, to *just over 1 second* in Ruby 2.2. That kind
of performance jump is going to make Ruby 2.2 a required upgrade for many users of 
the new Ruby syntax!

## Ruby Is Finallly Trashing Symbols (sorta)

The next major improvement announced by Ko1 was the long-desired, and much-needed addition
of garbage collection for symbols in Ruby 2.2.  This is an issue that has bitten users
of Rails and other long-running server processes that make use of a lot of symbols: Ruby
has historically never collected garbage symbols, which meant that they'd gradually 
accumulate, eating up memory until the server was restarted.  In Ruby 2.2, symbols will
be garbage collected like all other Ruby objects, which will come as welcome news
to most Ruby users. 

Ko1 gave the following example:

```ruby
before = Symbol.all_symbols.size
1_000_000.times{|i| i.to_s.to_sym} # Make 1M symbols
after = Symbol.all_symbols.size; p [before, after]

# Ruby 2.1
#=> [2_378, 1_002_378] # not GCed 
# Ruby 2.2 (dev)
#=> [2_456, 2_456] # GCed! 
```

## All Your Garbage Are Belong to Us...Eventually

But the biggest win for Ruby users in 2.2 has to be the addition of *incremental garbage
collection* to MRI. Users of the 2.1 series have been enjoying a speedup owing to the 
addition of [generational collection](https://bugs.ruby-lang.org/issues/8339)
to the interpreter, but this was only a first step. Generational GC dramatically 
improves the performance of minor garbage collection pauses, but the interpreter still has 
to "stop the world" to perform expensive, "major" collections periodically.  These pauses 
can be very expensive -- tens of milliseconds or more -- so it's been a long-held goal
to reduce their cost.

In Ruby 2.2, the same amount of work is (obviously) being done, but the cost of a major
collection is now spread out -- hence, "incremental" garbage collection -- so that the
worst-case GC time is dramatically improved.  Bottom line: real-world, worst-case garbage 
collection performance of Ruby applications should have dramatic improvements. Ko1 showed 
a test case where the worst-case GC pause time dropped from 16ms to about 3ms -- slightly
better than a *500% improvment* in worst-case collection times!

![Big GC performance improvements coming in Ruby 2.2](/assets/omniref-blog/incremental_gc_performance.png)

## (...but don't take our word for it!)

Ko1 has been nice enough to post his [slides from the talk](http://t.co/0YeiuHCvBR), so
you can read through all of the details on how these performance improvments are
being achieved. Check them out!