---
title: spread
category: pattern_transformers
---


The `spread` function allows you to take a pattern transformation
which takes a parameter, such as `slow`, and provide several
parameters which are switched between. In other words it 'spreads' a
function across several values.

Taking a simple high hat loop as an example:

~~~~ haskell
d1 $ sound "ho ho:2 ho:3 hc"
~~~~

We can slow it down by different amounts, such as by a half:

~~~~ haskell
d1 $ slow 2 $ sound "ho ho:2 ho:3 hc"
~~~~

Or by four thirds (i.e. speeding it up by a third; `4%3` means four over
three):

~~~~ haskell
d1 $ slow (4%3) $ sound "ho ho:2 ho:3 hc"
~~~~

But if we use `spread`, we can make a pattern which alternates between
the two speeds:

~~~~ haskell
d1 $ spread slow [2,4%3] $ sound "ho ho:2 ho:3 hc"
~~~~

There is a nice trick you can use here -- if you pass ($) as the
function to spread values over, you can put functions in the list
instead of values. For example:

~~~~ haskell
d1 $ spread ($) [density 2, rev, slow 2, striate 3, (# speed "0.8")]
    $ sound "[bd*2 [~ bd]] [sn future]*2 cp jvbass*4"
~~~~

Above, the pattern will have these transforms applied to it, one at a time, per cycle:

* cycle 1: `density 2` - pattern will increase in speed
* cycle 2: `rev` - pattern will be reversed
* cycle 3: `slow 2` - pattern will decrease in speed
* cycle 4: `striate 3` - pattern will be granualized
* cycle 5: `(# speed "0.8")` - pattern samples will be played back more slowly

After `(# speed "0.8")`, the transforms will repeat and start at `density 2` again.

~~~~ haskell
spread :: (a -> t -> Pattern b) -> [a] -> t -> Pattern b
~~~~

(The above is difficult to describe, if you don't understand Haskell,
just ignore it and read the below..)

The `spread` function allows you to take a pattern transformation
which takes a parameter, such as `slow`, and provide several
parameters which are switched between. In other words it 'spreads' a
function across several values.

Taking a simple high hat loop as an example:

~~~~ haskell
d1 $ sound "ho ho:2 ho:3 hc"
~~~~

We can slow it down by different amounts, such as by a half:

~~~~ haskell
  d1 $ slow 2 $ sound "ho ho:2 ho:3 hc"
~~~~

Or by four thirds (i.e. speeding it up by a third; `4%3` means four over
three):

~~~~ haskell
  d1 $ slow (4%3) $ sound "ho ho:2 ho:3 hc"
~~~~

But if we use `spread`, we can make a pattern which alternates between
the two speeds:

~~~~ haskell
d1 $ spread slow [2,4%3] $ sound "ho ho:2 ho:3 hc"
~~~~

There's a version of this function, `spread'` (pronounced "spread prime"), which takes a *pattern* of parameters, instead of a list:

~~~~ haskell
d1 $ spread' slow "2 4%3" $ sound "ho ho:2 ho:3 hc"
~~~~

This is quite a messy area of Tidal - due to a slight difference of
implementation this sounds quite different! One advantage of
using `spread'` though is that you can provide polyphonic parameters, e.g.:

~~~~ haskell
d1 $ spread' slow "[2 4%3, 3]" $ sound "ho ho:2 ho:3 hc"
~~~~

There's another version of `spread` called `fastspread`. True to its name, the result is faster, because it squeezes all the variations into one cycle. As the following gives two parameters to `slow`, it goes twice as fast as if you'd used `spread`:

~~~~ haskell
d1 $ fastspread slow [2,4%3] $ sound "ho ho:2 ho:3 hc"
~~~~

In previous versions of Tidal, `spread` was actually the same as `fastspread`. Now, `slowspread` is an alias of `spread`, but you may as well type the latter, as it's shorter!
