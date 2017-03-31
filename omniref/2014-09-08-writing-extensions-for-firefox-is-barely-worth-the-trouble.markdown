---
layout: omniref_post
sidebar: false
title: "Writing Extensions for Firefox is Barely Worth the Trouble"
date: 2014-09-08 08:47:19 -0700
comments: true
author: Tim Robertson
categories:
- omniref
- extensions
- addons
- javascript
- browsers
---

Dear Mozilla,

It really hurts me to say what I'm about to say. I'm an old-school Mozilla fanboy. I was the guy
obsessively reloading the Navigator source code webpage in anticipation of its release. I watched the
[Mozilla launch party livestream](https://archive.org/details/DNALOUNGE-2002-06-12), ran nightly
builds to keep up with development progress, and was the last of my friends -- by years -- to
use Firefox for all of my browsing.  I even owned a copy of
[Code Rush](http://en.wikipedia.org/wiki/Code_Rush)...on VHS. So I'm on your side here.

But times have changed. Increasingly, the battle for web developers is a one-sided competition,
and Firefox isn't winning. When I tell other coders that I try to do web development in Firefox,
they give me sad, withering looks, as if I'm some sort of historical re-enactor of the Browser Wars.
I'm an anachronistic throwback, a man in a tattered,
[lizard-decorated uniform](https://blog.mozilla.org/jay/files/2010/02/mozilla.gif), tugging a
cannon behind my horse and buggy, while everyone else zips around the future in self-driving cars.

![I bet this car has a working debugger console.](/assets/omniref-blog/robot_cars.jpg)

There are lots of examples of this, but today I'm going to talk about one: Firefox's absolutely horrible
tools for developing browser extensions.

## Why do you keep hitting yourself?

A while ago we decided to develop some browser extensions that would integrate Omniref into developer's
browsers. We wanted to build a simple tool that would give instant access to Ruby documentation to the
people who need it most. We initially planned to target Chrome
(because that's the majority of our traffic), but because I'm a Mozilla fan, we also decided
to release an addon for Firefox, even though it didn't make a lot of sense (in terms of audience size).

Development of the Chrome extension was a pleasant experience -- the tools, documentation and release
process were all top-notch. But Firefox? Well...I can't think of a single positive thing to say. From
the documentation to the approval process, writing a Firefox extension is one punch in the face after another.
It's to the point where we're wondering why we should continue doing it.  And that should scare you.

## Oh, uh...you wanted a console?

I'll give you an example: let's say you want to debug your Firefox addon code (I know, I know...that's
totally outrageous! Who writes bugs in their code? But for the sake of argument, bear with me.)  In Chrome,
you open the inspector, you set breakpoints in your code, and you look at the output in the console.
It's magic. (The cars are *driving themselves*, people!)

Want to get debug output from your addon code in Firefox? Oh. Huh.
[I guess you need to read all of this](https://developer.mozilla.org/en-US/docs/Tools/Browser_Console). And hey,
be sure not to miss the subtle pullout text *near the bottom of the page* where it's revealed for the first time that
*none of this stuff is enabled* without a special setting in your browser config! Because that's not important information.

Of course, there's also the problem that *all of that other stuff might be wrong*. Because if you do what
most developers have to do and search for *"debugging firefox addons"* (since you're not getting to these pages from the docs),
you might also find [this alternative guide to getting a browser console](https://developer.mozilla.org/en-US/docs/Tools/Browser_Toolbox).
How do these things relate? It's all helpfully documented in a mishmash of unintegrated blog posts, so it's
basically impossible to tell.

The only thing I know *for sure* is that the usual web inspector *didn't* work,
because that was the obvious, first thing I tried. And it didn't work (unlike in Chrome).

![Let's all write a Firefox addon!](/assets/omniref-blog/eyestab.png)

But OK, let's assume that you're an elite, dedicated coder, and you manage through hurculean effort
to get a console working. Want to *actually write to it* from your addon? Oh. You're one of *those* guys.
OK. According to [this other blog post](https://hacks.mozilla.org/2013/08/the-browser-console/), you
need only discover the magical incantation that will make `console` work the way you expect it to
work in Javascript:

    let console = Cu.import("resource://gre/modules/devtools/Console.jsm").console;
    console.log("hello", yourObject);

...there. isn't that intuitive?

## New feature: Debugging!

OK, So now we have console, and we can write to it. Celebrate the small victories. But what if you want to *interact*
with any of that code you're writing? No sweat. Firefox totally has you covered...as of
[release 31](https://developer.mozilla.org/en-US/Add-ons/Add-on_Debugger) (release date: *July 2014*). Because, you see,
the using the JS debugger in addon code is a
[shiny new feature](https://blog.mozilla.org/addons/2014/04/08/add-on-debugger-now-in-firefox-nightly/)!
(Just don't push it too hard -- it's still "experimental"). And more great news -- as of Firefox 32
(released: September! of 2014!) you can now even use *the console* to *interact* with that debugger.
Wow. I'm overwhelmed. Truly, the future is now.

![No, the future is now.](/assets/omniref-blog/future_is_now.jpg)

## But hey, who needs to debug code?

But I digress. I don't know about you, but I know that *I* don't write any bugs into my code. So let's move
on to more important things: once you've written your addon code, how do you load it into the browser? It's
obviously not *that* important, since you only do it about 3.5 million times a day while developing an addon,
but still. It's the little things, right?

If you want to reload a development extenion in Chrome, you hit "reload" on the extension page. Boom.
But Firefox isn't satisfied with anything so obvious. In Firefox, you get *two great choices*: you can either
*restart your browser completely* (using the `cfx run` command -- which requires *Python* to run), or you can
[install another, third-party addon](https://addons.mozilla.org/en-US/firefox/addon/autoinstaller/), so that
your addons can load your addons. I'm not joking.

## OK, but what about distribution?

I could go on for days, but I think you get the picture.  Let's move on to distribution.

Once you've managed to successfully produce a working addon by pounding rocks together, now it's time for
code review! Yay! Fortunately, the review process is an easy, three-step system:

    0 Package your code and submit for review.
    1 Wait
    2 GOTO 1

Haha! Just kidding! It's actually a four-step process:

    3 Get rejected for silly reasons.

So far, we've had code rejected for using a version of jQuery that was one patchlevel too new to be recognized by
the reviewer's scan tools as jQuery; because we used regexes that were "too general" (in some unspecified way);
and because we didn't provide the reviewers with information that they submission tools never asked us to provide.
But all of that would be forgiveable, if it weren't for the fact that each review cycle takes *weeks* to complete.
Couple a capricious rejection process with a latency that's measured in weeks, and you've got the primary
reason that we've released *five full updates* to our Chrome extension, in the time that we're still awaiting
*one* full approval from Mozilla.  It's infuriating.

After being rejected for using that too-modern version of jQuery, we waited for *two weeks* to get reviewed
again (without advancing at all in the queue) before giving up and resubmitting for a "preliminary review"...which
still took a couple of weeks.  Meanwhile, Google's approval time for our Chrome plugin has consistently been *less than
a day*.



[![And lo, the angel did say: "perhaps you should use the debugger!" And the angel did laugh and laugh....][2]][1]

[2]: http://www.marriedtothesea.com/012710/stop-hitting-yourself.gif
[1]: http://www.marriedtothesea.com/index.php?date=012710

## Why do we stay in this relationship?

If we've learned nothing from the last 30 years of software development, it's that the future goes where
developers lead. And you're losing developers. Omniref is a website by and for hackers.
and Firefox's share of traffic is declining. And as I said before, my friends and colleagues *already think I'm crazy for wanting
to use Firefox* to write code. You've got a mindshare problem, and it makes perfect sense: faced with declining traffic
from Firefox and a punishing developer ecosystem, many developers are shrugging, sighing, and moving over to Chrome.
I get it, but it's sad.

Mozilla, this relationship has problems. And it's not us, it's you. You need to change. You need to organize
your documentation, clean up your developer tools, and dedicate time and money to *basic things* -- like
*making the debugger work*. You need to re-examine your review policies, and figure out why Chrome can manage
to approve extensions in a few days, when you take *weeks* to review the same code.

I don't know what the problems are, so I can't begin to suggest how to fix them. But I know what the
solution looks like -- it looks like Chrome. I don't want to live in a two-browser world, but
[that's where we're heading](http://www.w3schools.com/browsers/browsers_stats.asp) unless you up your game.
And you start by focusing on developers. You've got a history of this -- [MDN](http://developer.mozilla.org)
is a fantastic, well-organized resource for web devs -- so I know you can do it.  It's just a matter of finding
the will.

## Finally, a note to our users:

I want to take a moment to say that we're surprised and gratified by the positive response to the extensions.
Thanks! But if you're a Firefox user, I want to say that *I'm sorry* that we've made a number of updates to
the Chrome version of our extension over the past few weeks, but haven't been able to release updates
to our Firefox addon as quickly. We just had a new version released, and you should soon be getting an update
to version 1.0.2, which fixes a number of bugs that we've already fixed in Chrome. With any luck we'll get
full approval from Mozilla in the near future.

*Update:* credited third image to [@drewtoothpaste](https://twitter.com/drewtoothpaste), who is the creator of
what we originally thought was a reddit meme. Thanks Mr. Toothpaste, and our apologies for the late attribution.