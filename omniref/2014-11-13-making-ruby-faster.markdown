---
layout: omniref_post
sidebar: false
title: "Making Ruby Faster"
date: 2014-11-12 00:25:36 -0800
comments: true
categories: 
- omniref
- ruby
- C
- MRI
- programming
- optimization
- neckbeards
---

Thanks to a question about Ruby performance posted by one of our users, we recently got a 
fascinating look at some of the optimizations that are going on deep inside the guts of 
Ruby interpreter -- and Ruby symbol comparisons got 25% faster along the way! How? 
Good question...

## Say...why are Ruby symbol comparisons so slow?

This all started when one of our users posted a question about
a weird performance anomaly he noticed while benchmarking Symbol comparisons.

If you're unfamiliar with Ruby, Symbols
are this unique, quasi-string construct that are intended
to be faster replacements for the tiny string constants that litter the code in most programming 
languages (e.g. keys into hash tables). The idea is that it _should_ be faster (and 
possibly smaller) to replace string literals with "symbols": objects that are essentially just 
named integer handles that can be directly compared for equality.

So it was weird when the user gave some pretty compelling evidence *(edit: omniref is gone now; so is the evidence)*
that Symbol comparisons were, in fact, a bit _slower_ than direct comparisons of small strings (e.g. 'aa').
His data suggested that for strings up to about 100 characters, it was actually _faster_ to compare
String objects, not Symbols. Weird!

## Matz and Ko1 enter the fray...

We reached out to Matz and asked him what he thought.
Initially, he was a little skeptical, but he passed it on to the rest of the Ruby team, and it
wasn't long before ko1 put in a 
[patch](https://github.com/ruby/ruby/commit/495548ace4587df43d30b021cfa6b2f84c9d9d44) that 
made Symbol comparisons _about 25% faster_ in 2.2.0-dev. So that's cool. But how? The patch is
remarkably simple:

```diff
-static VALUE
-sym_equal(VALUE sym1, VALUE sym2)
-{
-    if (sym1 == sym2) return Qtrue;
-    return Qfalse;
-}
-
+#define sym_equal rb_obj_equal
```

...and things get even stranger when you consider that `rb_obj_equal` is exactly the same
code as before:

```c
VALUE
rb_obj_equal(VALUE obj1, VALUE obj2)
{
    if (obj1 == obj2) return Qtrue;
    return Qfalse;
}
```

What the...? We get a 25% speedup by removing a method and replacing it with an alias to
_exactly the same method?!?_ What manner of oddities lurk in this dark world, anyway?

## You've gotta go up to get down...

The crticial clue to this mystery came when another one of our users
(who obviously knows more about the internals of MRI than we do), pointed out that there's 
a nifty little optimization going on much higher up in the interpreter stack:

> I noticed that opt_eq_func dispatches directly to rb_str_equal if both operands are 
Strings, but always calls vm_search_method before comparing Symbols.

This led us to look at the body of opt_eq_func more closely,
and it was here that the mystery finally resolved itself: the code for the high-level equality-testing
operation in the ruby interpreter is doing a magical optimization
that *checks the equality comparison function being called*, and makes an end-run around it whenever 
it's a call to `rb_obj_equal`. To wit:

```c
VALUE
opt_eq_func(VALUE recv, VALUE obj, CALL_INFO ci)
{
  // ...checks for Integer, Float and String types cut out...   
  vm_search_method(ci, recv);

  if (check_cfunc(ci->me, rb_obj_equal)) {
    return recv == obj ? Qtrue : Qfalse;
  }
  // ...
}
```

This little snippet of code is first fetching the equality comparison function being used by the
objects being compared, then _checking that function_ to see if it's `rb_obj_equal`. If it is, the 
comparison is done _directly inline_, and the Ruby interpreter never has to set up a call stack, 
make a branch, etc. All of that stuff is _much slower_ than simply comparing two values. 

So when ko1 replaced the custom comparison method in Symbol with a preprocessor alias to `rb_obj_equal`, 
he wasn't just removing code duplication -- he was allowing an upstream optimization to inline some code that
would otherwise lead to method calls. Bam. That's some serious magic.