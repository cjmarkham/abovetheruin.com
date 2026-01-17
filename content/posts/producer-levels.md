+++
date = '2026-01-17T17:21:00Z'
title = 'Producer Levels'
+++

I've added a couple of "passive producers" to **Above the Ruin**. 

Their purspose is to produce an item after n hours. For example, the water collector will 
produce water every 8 hours*. The bee house will produce honey every 16 hours*.

**all timings to be confirmed*

But how do you tell the player that the item is ready to be collected? Some games show a tool tip above
the producer to alert you. While this was an option, I wanted to see if I could have the producer visually tell
the player that it's ready.

So I came up with several sprites to represent a producer. Take the water collector pictured below. 
It has several different variations to show a level of full-ness. 

![Water collector levels](/images/water-collector-levels.png)

Within the producer module, I swap out the sprites (bottom 2 in this case) depending on the production level of the producer.

I think this is a good way to show the production level, but I'm concerned it may not be that clear for those
that are visually impaired. There's also the case where the item can be placed behind others. Swapping the bottom 2
sprites won't be enough as they will be hidden.

I'll keep playing with the concept and see if I can refine it.
