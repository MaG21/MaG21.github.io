---
title: "Erabo Pre-Selector Development Process"
layout: post
date: 2019-06-20 20:24
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Erabo
- MacOS
star: true
category: blog
author: MaG
description: "Erabo Pre-Selector Development Process"
---

Currently I work with an incredible multidisciplinary team of 5 people. Long story short, three of us decided to work on a personal project called Erabo.

1. Chris – Product designer.
2. Manuel – myself.
3. Ivan – Software Developer

This post is about the development process of [Erabo Pre-Selector](https://erabo.app), which was the app I had to complete for this project.

## Design Phase
![Screenshot](/assets/images/erabo-evo.png)
<figcaption class="caption">Evolution of Erabo Pre-Selector</figcaption>

<br>

Chris began the Design Sprint by interviewing photographers. Then using knowledge gathered from those interviews he came up with the following designs:
1. Erabo Pre-Selector, draft 1. [pdf](/assets/pdf/erabo-1-draft.pdf){:target="_blank"}
2. Erabo Pre-Selctor, final draft #5. [pdf](/assets/pdf/erabo-final.pdf){: target="_blank" }

Check out [Erabo Pre-Selector Official Site](https://erabo.app). Also Chris has a website <https://chrisvpr.com>.

## Testing and debugging process

Development can be a slow at times. I'm just goind to talk about the debugging process which I find more interesting of mentioning here.

Before release, I started profiling the app using Apple's profiling tool. To get useful insight I used ~2.1k high quality JPEGs (~16 MiB each) to see how the application behaves under high loads.
![Profiler](/assets/images/profiling-init.png){: class="bigger-image" }

From the image above we can see there's too much work being done on the Main Thread, that's not good. Looking futher, we can conclude the Main Thread is losing too much time decoding images. The following image shows the problematic line:

![Caveat](/assets/images/caveat.png)

How do we fix it?<br>
Moving work away from the Main Thread. The Main Thread shouldn't be decoding all those images, a background queue should be used instead. Like this:

![Caveat fixed](/assets/images/caveat-fixed.png)

Lets see how does it look after this little fix:

![Profile Fixed](/assets/images/profiler-fixed.png){: class="bigger-image" }

Remarkable. A profiler makes this whole process feels like I'm cheating.

After many little tweaks here and there, the app started to perform quite well. Next day I received a screenshot from a beta photographer that was working on a project for a customer.

![Memory Leak](/assets/images/memory-leak.png)
<figcaption class="caption">Screenshot from beta user</figcaption>

<br>

There's no doubt, that's a memory leak. To fix this bug I used Leaks from Instruments, unfortunately I forgot to take any screenshot from this process,
but the issue was simpler than I thought. I had some retain cycles that where causing the memory leak, easy fix.

wait, retain cycle?

It's simple, Swift uses something called Automatic Reference Counting (ARC) to track and manage memory. ARC has a little problem, sometimes it needs information to decide when the life of an object ends, this happens when one object retains a reference of another object and vice versa, hence the name, retain cycle.

After this we were ready to start the App Store review process.

