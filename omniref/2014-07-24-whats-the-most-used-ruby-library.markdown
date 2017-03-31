---
layout: omniref_post
title: "What's the most popular Ruby library?"
sidebar: false
author: Tim Robertson
date: 2014-07-23 21:29:02 -0700
comments: true
published: true
categories:
- omniref
- ruby
- gems
- statistics
- medium data
---

Not too long ago, we were asked a great question by Eric Hu, on Twitter:

<center>
<blockquote class="twitter-tweet" lang="en"><p><a href="https://twitter.com/omniref">@omniref</a> I&#39;m curious what the most used Ruby standard libs (not gems) are. Your What&#39;s Relevant blog post comes so close. Any suggestions?</p>&mdash; Eric Hu (@HuPineapple) <a href="https://twitter.com/HuPineapple/statuses/482345009903980546">June 27, 2014</a></blockquote>
</center>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

We told Eric that we'd get back to him with that, because hey: we have a gigantic database of
_all_ of the public Ruby code, parsed and statically analyzed and indexed and generally big-data-scienced <sup>[[0]](#f0)</sup>.
So, easy, right? But the truth is, we had to think about it a bit.  It's not a
trivial question.

## So You Decide to Use Regular Expressions<sup style='font-weight:normal;font-size:small;'>[[1]](#f1)</sup>...

The problem with the Ruby standard library is that, unlike Bundler and Gemfiles,
there's no one definitive dependency specification. Sure, a file will require a library when it needs it,
but that might only end up being a few places in a big pile of code. Or inversely, in
some projects, you'll see the same library required in every. single. file. So "files that have requirements" is
a noisy metric, but the other extreme ("number of gems that use a library", say) is bad too -- because
of the way we use Gems, a lot of code is implicitly dependent on stdlib code without really acknowledging it.

People also require code in the _weirdest_ places. There's just _gobs_ of Ruby code out
there where files are required in the middle of some deeply nested method (or worse yet: in _metaprogramming code_.
have mercy, people.)  So, of course, requirements are a total mess. Thus, we did what any self-respecting hacker does when faced with
an annoyingly inexact problem: we used a regex. Specifically, this one: <sup>[[2]](#f2)</sup>

    (?:^|\n|\;)\s*require[ \t]+['"](abbrev|base64|benchmark|bigdecimal|cgi|cmath|coverage|csv|date|dbm|debug|delegate|digest|dl|drb|e2mmap|English|erb|etc|expect|extmk|fcntl|fiddle|fileutils|find|forwardable|gdbm|getoptlong|gserver|io/console|io/nonblock|io/wait|ipaddr|irb|json|logger|mathn|matrix|minitest|minitest/benchmark|minitest/spec|mkmf|monitor|mutex_m|net/ftp|net/http|net/imap|net/pop|net/smtp|net/telnet|nkf|objspace|observer|open-uri|open3|openssl|optparse|ostruct|pathname|pp|prettyprint|prime|profile|profiler|pstore|psych|pty|racc|racc/parser|rake|rdoc|readline|resolv|resolv-replace|rexml|rinda|ripper|rss|rubygems|scanf|sdbm|securerandom|set|shell|shellwords|singleton|socket|stringio|strscan|sync|syslog|tempfile|test/unit|thwait|time|timeout|tk|tmpdir|tracer|tsort|un|uri|weakref|webrick|win32ole|xmlrpc/client|yaml|zlib)['"]

## ...now you have 577,617,200 problems. <sup style='font-weight:normal;font-size:small;'>[[3]](#f3)</sup>

So what we did, you see, is that we took that monster, and we ran it against every last bit of Ruby code
that we know about -- all _578 million lines of it_.
And we broke it down by gems, gem versions and files: <sup>[[4]](#f4)</sup>

#### <center>Raw Counts: Gems, Versions, Files, Lines</center>

| Type        | Count
|------------:|:-----
|Unique Gems  | 78,470
|Gem versions | 450,239
|Files        | 15,205,676
|Lines        | 577,617,200

This is mainly just a state-of-the-state for us: we've processed about 80,000 gems, roughly 6x that
number in terms of distinct versions, and a whole mess o' files. Good to know.
But how many of those require parts of the Ruby standard library?

#### <center>Percentage of Gems/Files Requiring Any Part of the Ruby Stdlib</center>

| Type        | Raw Count | Percentage
|------------:|:----------|:----------
|Unique Gems  | 52,958  | 68%
|Distinct gem versions | 336,358 | 75%
|Files        | 1,921,430 | 13%

It looks like about 70-80% of gems have an explicit requirement for some part of the Ruby standard
library. We wondered about this -- is that number low? Our intuition says that a significant fraction
(maybe a quarter) of all Gems are just learning exercises for the author, with little or no code,
so it isn't totally unexpected.  Perhaps we'll revisit this in a later post.

More interesting is the low percentage of files that have some requirement of the Ruby standard library.
Up above we said that "files requiring a library" is a noisy metric, and this suggests that our
intuition was correct; the data is sparse. But we'll carry on with the analysis, and see what we get...

## What's the Opposite of "Data Science"? Data Superstition? That's what we do here.

Enough dodging the question: what are the most popular Ruby standard libraries, already? There are
over 100 different components in the Ruby stdlib, and while there's some difference depending on
whether you're considering Gems, Files, etc., there are essentially ~30 popular libraries,<sup>[[5]](#f5)</sup>
and a long tail of unpopular ones:

![The Ruby stdlib components are mostly not very popular](/assets/omniref-blog/require_counts.png)

So instead of listing all 100+ libraries, we'll limit this post to the top 30 most popular. They are: <sup>[[6]](#f6)</sup>

|    | Most Included by Gem | Most Included by Version | Most Included by File |
|----|----------------------|--------------------------|-----------------------|
|1  |rubygems               |rubygems               |rubygems
|2  |test/unit              |fileutils              |test/unit
|3  |yaml                   |yaml                   |fileutils
|4  |json                   |test/unit              |json
|5  |fileutils              |json                   |yaml
|6  |optparse               |optparse               |tk
|7  |net/http               |logger                 |pathname
|8  |logger                 |pathname               |stringio
|9  |uri                    |uri                    |optparse
|10 |pathname               |net/http               |uri
|11 |rake                   |stringio               |logger
|12 |stringio               |erb                    |set
|13 |ostruct                |set                    |net/http
|14 |open/uri               |ostruct                |socket
|15 |socket                 |socket                 |tempfile
|16 |erb                    |tempfile               |ostruct
|17 |cgi                    |rake                   |date
|18 |tempfile               |cgi                    |mkmf
|19 |set                    |date                   |erb
|20 |date                   |pp                     |rake
|21 |pp                     |open/uri               |cgi
|22 |mkmf                   |mkmf                   |pp
|23 |time                   |time                   |time
|24 |base                   |base                   |open-uri
|25 |singleton              |forwardable            |base
|26 |forwardable            |singleton              |openssl
|27 |benchmark              |benchmark              |forwardable
|28 |openssl                |tmpdir                 |benchmark
|29 |tmpdir                 |openssl                |singleton
|30 |timeout                |timeout                |strscan

## (If this were a "data science" paper, this would be the discussion section)

It probably isn't any surprise that rubygems
is the #1 most included part of the stdlib, given that we're analyzing a bunch of gems.  But it's
comforting to see it there, since it inidicates that our data is in the right ballpark.  More
interesting, we see test_unit at the #2 position, which makes sense, given the Ruby community's
dedication to testing, right?

YAML and JSON are both very popular, so I'm afraid that
we don't have much ammunition to offer either side in that debate.
Fileutils is where we get basic filesystem
methods like FileUtils#cd and FileUtils#mkdir, so it's
probably reasonable that it gets included in a lot of files.  And, of course,
optparse is where we get the OptionParser class, which is necessary
for nearly every program of any substance.

> Ruby Trivia: there are currently about 580 million lines of code in all of the published Ruby Gems.

The first weird thing we noticed was the "popularity" of `tk` in files, which, as far as we can tell,
isn't that popular. But when you realize that there are a gazillion<sup>[[7]](#f7)</sup> files in the
code for the Ruby Tk bindings, it starts to look more reasonable -- anything that works with Tk at all
probably ends up requiring a lot of files.

Mostly, though, things come out as we expect, with perhaps a few surprises: date and time
don't make their first appearances until #17, which seems low, given how often programmers
work with time (and given that socket is more popular than either).
But then, net/http is also pretty popular, so maybe it just reflects the influence of Rails
(and web programming in general) on Ruby's popularity.<sup>[[8]](#f8)</sup>

We won't belabor this -- but we feel like it's worth pointing out the *least* popular pice of the Ruby 
standard library: the little-known (and humorously named) Rinda.

## Footnotes

 0)<a name='f0'></a> OK, small-data-scienced. We can't all be Google.

 1)<a name='f1'></a> Let's just call this "data science: materials and methods". Science!

 2)<a name='f2'></a> We didn't use no stinkin' online regex builder to make that, either. If it makes your eyes bleed, you need to
back to data scientist school. All hail [Steven Kleene](http://en.wikipedia.org/wiki/Stephen_Cole_Kleene). Respect.

 3)<a name='f3'></a> [but Perl ain't one?](http://www.explainxkcd.com/wiki/index.php/1171:_Perl_Problems)

 4)<a name='f4'></a> Specifically, we took that regex and queried it against the file contents in our code database,
and did a `select distinct` with a `group by` clause on gem name, gem version or file path.  So we're counting the
number of gems, _gem releases_ and files that reference each different component of the standard library.  It takes
a while to run.

 5)<a name='f5'></a> The counts for the 30th most popular libs were 1029 gem requires, 9074 version requires,
and 16,003 file requires respectively.

 6)<a name='f6'></a> Yes, this is the TL;DR. (What? You wanted that earlier?)

 7)<a name='f7'></a> That's "data science" for "a lot".

 8)<a name='f8'></a> Time flies when you're requiring Rails.

 9)<a name='f9'></a> For the record, Montana likes YAML, and I'm a fan of Matrix.