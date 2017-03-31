---
layout: omniref_post
sidebar: false
title: "Programmers are hipster librarians"
date: 2014-09-19 9:42:14 -0700
comments: true
author: Tim Robertson
categories:
- omniref
- announcements
- features
- ruby
- programming
---

In a world where the solution to nearly any problem is available online, for free, the
most effective programmers know how to quickly get answers to their programming questions. How?
In practice, programming often reduces to a simple loop:

```ruby
while coding do
  next unless Problem.unsolved?
  frustration = 1
  question = Question.formulate(Problem)

  while frustration < MAX_FRUSTRATION
    answer = PersonalExperience.search(question) || InterTubes.search(question)
    break unless answer.nil?
    question.rephrase!
    frustration += 1
  end

  Problem.solve!(answer) || raise IGiveUp("I'm moving to the wilderness and raising goats.")
end
```

Anyone can grind away on the outer loop, but we've all seen what happens to an `O(n^2)`<sup>[[0]](#f0)</sup> algorithm
with an inefficient inner loop <sup>[[1]](#f1)</sup>. For programmers, the
thing that separates a novice from a pro is the the ability to get answers quickly. If you have a
limited personal knowledge base, the act of *formulating a good question* is critically difficult.
As an experienced programmer, you've spent years building up a highly specialized mental index that
gives you quick access to a lot of programming information.

In short, you're a not an expert programmer; you're a librarian in an ironic t-shirt.

## ...a hipster librarian, using artisinal, free-range hash tables

It makes me a little sad to say this, but unless you're of a Certain Age, you may never have
seen one of these in real life:

![The original Google.](/assets/omniref-blog/card_catalog.jpg)

No, that's not (just) a piece of kitsch in a startup designer's apartment -- it's an index! Once upon
a time, if you wanted to find something in a library, you walked up to one of these, looked
up whatever terms you cared about <sup>[[2]](#f2)</sup>, and
then you found the index cards that related to your topic by flipping through the correct drawer.
Those cards then pointed you to the books you cared about.

To use a card catalog, you had to be creative enough to think of all the terms that might relate to
your question, look stuff up in the cards, fetch the books, read them, come up with more words, look
those up, etc. until you found your answer.

Sound familiar? We've replaced the rooms full of furniture with rooms full of computers <sup>[[3]](#f3)</sup>, but the
process today is no less quaint.

## OK, so you're old. What’s the point?

The point is, when it comes to answering programming questions, *we're still running an inefficient
algorithm*: the prerequiste of getting an answer is knowing how to ask the right question.
And that takes experience.

But why? What's the one thing you usually know when you have a problem with code? *You know where
you are in the code*. You know the package, the class, the method...you often know the *specific
lines* that are causing you trouble. So why don't we organize our programming knowledge that way?

!["My terminal? Yeah, you've probably never emulated it."](/assets/omniref-blog/hipster_coder.jpg)

Say that you're new at Ruby, and you want to know how to delete elements that match a certain
condition. Wouldn't it be convenient if the documentation for Hash#delete,
already had an answer for someone with the same problem?  Or perhaps you're getting an exception
when loading a database in Rails. So you go to the page for PG:Error and see why you might be 
getting that exception. That's not a card catalog -- that's an index built for programmers.

## Programmers live in code. Coding knowledge should live there too.

Despite living through a revolution in the availability of free programming knowledge on the
internet, most of us are still stabbing in the dark when we need to find answers to hard questions.
Programmers need access to all sorts of knowledge: questions, answers, blog posts, books, bugs, commits,
comments, and so on. And programmers live in code. So why not bring the two together? That's our goal:
we're creating a community where anyone can attach programming knowledge to the code where it belongs.

We've been adding features to Omniref that make it possible for anyone to ask questions on a particular
package, or even a particular line of code. And in the coming weeks we're
going to be expanding on these features in new and (we hope) exciting ways. Our goal is to build a code-based
reference, by and for the programming community. We want to make a place where you can find anything: blogs,
books, questions, bugs, etc. simply by *looking for the code* you care about.

## Footnotes

 0)<a name='f0'></a> Yes, OK...this is technically `O(n*m)`. Let's assume that in the limit of zero knowledge, you're a
terrible question asker, but incredibly persistent. This seems consistent with the most annoying people
we know.

 1)<a name='f1'></a> There are a [lot of goat videos](https://www.youtube.com/watch?v=PpccpglnNf0) out there.
Coincidence? I think not.

 2)<a name='f2'></a> For example, `FORTRAN` (or perhaps, `MISERY` or `LANGUAGES, ANCIENT`).

 3)<a name='f3'></a> [I can't resist](https://pbs.twimg.com/media/Bvvvi_pIYAAbrMw.jpg:large).