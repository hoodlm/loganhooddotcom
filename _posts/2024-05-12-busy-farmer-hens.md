---
layout: post
title: "The Busy Farmer's Hens"
excerpt: "A barnyard allegory about recency bias in system modeling"
imgpath: "/assets/img/posts/hens-1"
---

*A barnyard allegory about recency bias in system modeling.*

The Busy Farmer has a small but efficient all-organic farm. He wants to diversify his business, and he likes having fresh eggs each day, so he buys two dozen hens. Like everything else on his farm, the hens are simple and efficient: he feeds the chickens in the morning and picks up eggs from their coop in the evening. With 24 hens, he consistently gets two dozen eggs a day.

After a couple years, he's starting to get fewer eggs - only 12 eggs a day instead of 24 - but he's still spending nearly as much on chicken feed.

Although he is very busy, he decides to spend a day observing his chickens. He sits outside the coop all day, and whenever he sees a hen lay an egg, he ties a ribbon around her leg. At the end of the day, there are 12 eggs, 12 chickens with ribbons, and 12 chickens without ribbons. That evening after dinner, the farmer rounds up the hens without ribbons - the 12 that didn't lay an egg - and butchers them.

The next day, he feeds the chickens as normal - but with half as much feed - satisfied that he's improved the efficiency of his farm once again. But, at the end of the day he collects only **6** eggs!

### The wrong system model

The farmer has a simple system: he provides an input of chicken feed, and the hens produce eggs as an output.

![A system flow diagram, showing 24 hens producing 24 eggs a day]({{ page.imgpath }}/24eggs.png)

He observes that the system has started to become less efficient - he is providing the same input but getting only 50% of the output. He gathered some information about the inner workings of the system by watching his hens for a day, but ultimately took the wrong action because of an incorrect understanding of how a key component of his system - the flock of hens - degrades over time.

The farmer's model is that hens can function in two modes:

1. Productive: lay exactly one egg a day
2. Unproductive: lay zero eggs

...and that the system had degraded because half of his hens had become unproductive, while the other half are still fully productive. To improve the efficiency of the system, he just needs to figure out which hens are unproductive and remove them.

![Same diagram as above. 12 chickens are grayed out, and the output is 12 eggs a day.]({{ page.imgpath }}/12eggswrong.png)

In fact, the system's actual behavior is:

* Each hen lays an egg at some frequency. There are many factors, including the age of a hen, that can change the rate of egg-laying.

For young healthy hens, the frequency is daily. But, egg-laying gradually slows down as hens age. The farmer did not realize is that *every* hen in the flock was running at half-productivity! So, removing half of the chickens from his flock did not improve the efficiency of the system, but only reduced his egg production by another 50%.

![All 24 chickens are grayed out, "each chicken lays an egg every 2 days".]({{ page.imgpath }}/12eggs2.png)

![The same diagram as above, but 12 chickens are crossed out to indicate they've been butchered. Output: 6 eggs/day]({{ page.imgpath }}/6eggs.png)

---

*Credit: the graphics in this post use icons by [sbed](https://opengameart.org/content/95-game-icons), [delapouite](https://delapouite.com/), and [Lorc](https://lorcblog.blogspot.com/) under [CC-BY 3.0 license](https://creativecommons.org/licenses/by/3.0/), found on [game-icons.net](https://game-icons.net).*
