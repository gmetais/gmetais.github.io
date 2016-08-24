---
layout: post
title:  "What I expect from a modern image lazyloader"
date:   2016-08-24 15:03:50
categories: webperf
excerpt: How to build a good lazyloader in 2016
comments: true
---

*What should a good lazyloader be in 2016?*

Using a lazyloader is a great way to improve the loading spead of a page. However, during my webperf audits, I've seen some lazyloaders that were causing more problems than improvements. I'm also sad to see some massively used lazyloaders not evolving and not implementing new technologies.

I could create "the-lazyloader-of-my-dreams.js", but 1) I don't have time and 2) it wouldn't fix the other lazyloaders.

## My wish list for lazyloaders maintainers

1. Can be loaded as an async script. 
2. Doesn't wait until end of body, DOM Ready of Window.onload to load images.
3. Shows image while loading, for progressive JPEG support.
4. Detects visibility.
5. Is compatible with iframes
6. Is compatible with divs or JS widgets
7. Is compatible with background images (and media queries).
8. Is compatible with srcset or <picture> to load responsive images.
9. Detects if previous images were loaded too slowly and loads lower resolution images for the next images.
10. Uses a single scroll event listener instead of one per image.
11. Throttles the scroll event.
12. Uses a [Passive Event Listener][passive-event-listener].
13. Is compatible with the [Interaction Observer API][interaction-observer].
14. Detects cross-domains in URLs and [preconnect][preconnect] them asap.
15. Alerts developers about specifying dimensions, to avoid reflows.


## Benchmark of some existing lazyloaders 

|                        | [aFarkas/lazysizes][1] | [vvo/lazyload][2] | [tuupola/jquery_lazyload][3] | [verlok/lazyload][4]
|------------------------|:-:|:-:|:-:|:-:|
| 1 - async              | ✔ | ✗ | ✔ | ✔ |
| 2 - doesn't wait       | ✗ | ✔ | ✗ | ✗ |
| 3 - visibility         | ✔ | ✔ | ✔ | ✔ |
| 4 - progressive        | ✔ | ✔ | ✗ | ✔ |
| 5 - iframes            | ✔ | ✔ | ✗ | ✔ |
| 6 - divs               | ✔ | ✗ | ✗ | ✗ |
| 7 - background         | ✔ | ✗ | ✔ | ✔ |
| 8 - srcset, picture    | ✔ | ✗ | ✗ | ✔ |
| 9 - detect slowness    | ✗ | ✗ | ✗ | ✗ |
| 10 - single listener   | ✔ | ✔ | ✔ | ✔ |
| 11 - throttling        | ✔ | ✔ | ✗ | ✔ |
| 12 - passive event     | ✗ | ✗ | ✗ | ✗ |
| 13 - interaction       | ✗ | ✗ | ✗ | ✗ |
| 14 - preconnect        | ✗ | ✗ | ✗ | ✗ |
| 15 - dimensions        | ✔ | ✗ | ✔ | ✔ |
| SCORE:                 | 10/15 | 6/15 | 5/15 | 9/15 |
| Stars on GitHub        | 5K | 800 | 6K | 500 |
|------------------------|---|---|---|---|

<br>
---
<br>


(You can create an issue or a pull request on [gmetais/gmetais.github.io][github-blog] if I made a mistake, if the benchmark needs an update or if you'd like to see a script added.)




[passive-event-listener]:       https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md
[interaction-observer]:         https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
[preconnect]:                   https://www.igvita.com/2015/08/17/eliminating-roundtrips-with-preconnect/
[github-blog]:                  https://github.com/gmetais/gmetais.github.io
[1]:                            https://github.com/aFarkas/lazysizes
[2]:                            https://github.com/vvo/lazyload
[3]:                            https://github.com/tuupola/jquery_lazyload
[4]:                            https://github.com/verlok/lazyload
