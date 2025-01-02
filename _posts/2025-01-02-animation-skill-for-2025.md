---
layout: post
title: "A goal For 2025: Learn to animate"
excerpt: "Learning to create simple technical animations."
imgpath: "/assets/img/posts/2025-animation"
---

I spent parts of 2024 intentionally working on two technical skills: [studying compilers](/2024/03/17/ulang-v001.html) and [learning the Rust language](/2024/03/03/four-months-of-rust.html). The compiler work was (so far) purely academic, but learning Rust turned out to be immediately practical - I was in a "right time, right place" position toward the end of the year to jump into some high-priority Rust software development at work.

This year, I want to learn to animate, particularly create technical animations of algorithms, protocols, and processes.

I took a first look at [Manim](https://www.manim.community/), a Python-based animation library. It was easy to get started. With a couple hours of learning and playing, I was able to hack together two simple animations.

### DNS Proxy

Demonstrating a DNS Proxy sending queries to two downstream resolvers concurrently.

Source code: [caesar\_cipher.py](https://github.com/hoodlm/JunkDrawer/blob/7da57bec9df8049b49e067b5762e0b412462d427/py/manim-playground/caesar_cipher.py)

<iframe width="100%" height="480" src="https://www.youtube-nocookie.com/embed/NO4IM55SL1c?si=SXPSqDaAJFtMIBha" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### TQIJOY PG CMBDL RVBSUA, KVEHF NZ WPX.

Demonstrating a Caesar Cipher, iterating through the 26 rotations of the pangram "Sphinx of black quartz, judge my vow."

Source code: [dns\_proxy.py](https://github.com/hoodlm/JunkDrawer/blob/7da57bec9df8049b49e067b5762e0b412462d427/py/manim-playground/dns_proxy.py)

<iframe width="100%" height="480" src="https://www.youtube-nocookie.com/embed/f1K6oxmPhco?si=pfUKH2wXmiKnjK4x" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Why?

First and foremost - creating these animations was fun. It has potential to be a creative and artistic outlet. But there is a practical professional motivation, too.

The whiteboard is a practical and effective tool for collaborative troubleshooting, learning sessions, and brainstorming. Modern office designs put dry erase whiteboards on every possible surface for a reason!

What is the magic of "getting in front of the whiteboard" for explaining ideas and processes? Maybe there's something special about the physical form of the whiteboard, or the neuromuscular-kinetic engagement of manipulating dry erase markers. But I think the critical element is that the **shapes and arrows are added to the picture one at a time as someone draws them**. The audience sees the process as it unfolds in the presenter's drawing.

The handful of people who are present for the whiteboarding session bask in our newfound clarity, and of course we remember to take a picture to share our completed picture, to share with the rest of the team:

![An inscrutible whiteboard diagram]({{ page.imgpath }}/whiteboarding.jpg)

Unfortunately, not so helpful to the rest of the team, who weren't there to see the process of scribbling.

A cleaned-up static diagram looks better, but you still don't get to see the *process* of requests moving from one stage to another:

![A cleaned up diagram of a DNS request traversing a proxy]({{ page.imgpath }}/resolver-static.drawio.png)

Animation might be the best-of-both-worlds - I can show a *process* happening, even narrate it with a voiceover, much like whiteboarding - but a recorded animation is a *durable artifact* that can be rewatched, shared with other people, used to train and teach dozens or hundreds of people instead of the three people that happened to be there for a whiteboarding session.

Will animation be a practical day-to-day activity? This will come down to time efficiency. Manim is powerful and pretty user-friendly, but as a beginner, these animations still each took 2-3 hours to put together. I have my entire life of practicing writing and drawing, where I can pick up a dry-erase marker and my brain can just about automatically convert thoughts into scribbles. For animation to be as practical for day-to-day collaboration, I need to find the right toolset - and invest sufficient practice in learning that toolset - to be able to whip up an animation like the ones above in a 30 minute block, not half a day.

---
