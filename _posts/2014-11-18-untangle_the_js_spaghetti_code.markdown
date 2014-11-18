---
layout: post
title:  "Untangle the JavaScript spaghetti code with YellowLabTools"
date:   2014-11-18 10:03:00
categories: yellowlabtools
excerpt: An introduction to JavaScript profiling with Yellow Lab Tools
---

As a front-end architect and freelancer, I often do performance audits for websites. One recurrent problem is dealing with page making a heavy usage of JavaScript. They can load up to dozens of scripts, including:

 - non-documented and non-commented jQuery or vanilla JS code,
 - jQuery plugins doing some magical transformations to the DOM,
 - over-minified external scripts the marketing said « it’s just a small tag, don’t worry ».

How to audit that? How to understand what’s happening on the page? In-browsers JS profilers are nice when you want to detect the slowest functions included in all the scripts, but they are of no help if you need to understand why these functions are so slow.



Phantomas
---------

I played a lot with <a target="_blank" href="https://github.com/macbre/phantomas">Phantomas (by Maciej Brencz)</a>. In case you don't know this tool, it’s great. It loads a page with <a target="_blank" href="http://phantomjs.org/">PhantomJS (a headless browser)</a>, analyzes various aspects of the page and outputs metrics about performances and quality.

![phantomas](/assets/phantomas.jpg)

I was fascinated by the fact that it can detect and count JavaScript interactions with the DOM. But counting is not enough, I wanted to have more information: 

 - When did the DOM interactions happen during page load?
 - Which script and which function made the call?
 - Could I detect any kind of redundancy, any unoptimized loops?

So I wrote a Phantomas module that logs everything: DOM queries, readings, writings, bindings. 

Erm not exactly everything… 

Actually only functions can be intercepted. Assignments such as « element.className = 'foo' » are not caught (maybe one day with <a target="_blank" href="https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver">MutationObserver</a>).

It also records most of jQuery’s functions, the ones that interact with the DOM.


Yellow Lab Tools
----------------

When profiling a webpage, there can be thousands of DOM interactions, that are hard to analyze. It needed an HTML UI, and that's what Yellow Lab Tools brings in.


When you launch a test (on www.aol.com for the next screenshot), you'll see a **timeline** that looks like this:

![YellowLabTools timeline screenshot](/assets/YLTtimeline1.jpg)

This timeline shows the loading of the page. Each bar represents a bunch of JavaScript interactions with the DOM.

The page loaded in 4,932ms, but it looks like JavaScript took half of this time! Don’t worry, there aren't 2.5 seconds of JS execution when you load www.aol.com on a real browser. It's 3 or 4 times faster. Spying some JavaScript with some more JavaScript necessarily slows down the execution.

So what can we see on this timeline?

![YellowLabTools timeline explainations](/assets/YLTtimeline2.jpg)

Want a piece of advice here? **Avoid manipulating the DOM before it is completely rendered. Which means no scripts in the middle of the body**.

For the rest, do whatever you want but **prioritize** wisely. Use the visually colorated steps to organize your JS execution by priority. For example binding the login button in your header could be one of the first actions, so place it at the end of the body. Binding some buttons in the footer can probably wait until « window.onload ».

And of course the main rule is **reduce the number of DOM interactions**. AOL’s home page has 2,000 of them and that’s a lot.



Profiling details
-----------------

Below the timeline Yellow Lab Tools provides the entire list of recorded DOM interactions. This example is the beginning of google.com - short and efficient:

![YellowLabTools profiler screenshot](/assets/YLTprofiler1.jpg)

On each line you can show more information — such as the JS call stack — by clicking on the question mark symbol.



How to use Yellow Lab Tools?
----------------------------

It’s online, and it's here: <a target="_blank" href="http://yellowlab.tools">YellowLab.tools</a>.



Common bad practices i've seen
---------------------------------

#### 1. The duplicated queries

The result could be put into a variable to avoid queries :

![Repeated getElementById queries example](/assets/YLTprofiler2.jpg)


#### 2. The dead (or useless) JS code

A red warning icon on a line means:

 - the query didn't return anything
 - an action is called on an empty jQuery object.

The following extract is probably a piece of code meant for another page:

![Unused code example](/assets/YLTprofiler3.jpg)


#### 3. Many elements binded one by one

On the following extract, a tracker binds 887 click events and it takes 104ms. If you are binding more than 5 elements, consider using <a target="_blank" href="http://davidwalsh.name/event-delegate">event delegation</a>.

![Binding loop example](/assets/YLTprofiler4.jpg)


#### 4. The read/write loop

Modern browsers optimize the JavaScript execution by buffering the **writing*** DOM queries. But before executing a reading query, the browser needs to clear the writing buffer.

If you want to take advantage of this behavior try to group reading queries together and writing queries together, not like the following example:

![Read/write loop example](/assets/YLTprofiler5.jpg)


#### 5. The heavy jQuery plugin

When developers add a plugin to a page, they generally don’t read its code. They’re just happy it does what they need at the time. But some plugins have huge performance impact or are a clear overkill.

The following extract shows the jScrollPane plugin in action on a page. Just a part of it, because it makes 446 interactions and it lasts for 229ms.

![Heavy jQuery plugin example](/assets/YLTprofiler6.jpg)



What’s next for Yellow Lab Tools?
---------------------------------

I’ll keep on improving the tool. Please give your opinion and <a target="_blank" href="https://github.com/gmetais/YellowLabTools/issues">report bugs</a> by opening tickets on the GitHub project page.

I also have ideas for its future:

**Add intelligence:** because analyzing long listings is fastidious. Every recursive pattern seen in the profiler looks like it is optimizable. So we could probably build some intelligent problem detection algorithms.

**Diversify:** I plan to add more tools inside YLT, for deep CSS analyzing, image optimizations... I know there are already plenty of tools on the market, but I like tools that go deep into the details, like for example the JS profiler does.

**Add a nice dashboard:** Phantomas offers plenty of interesting metrics. I started to work on a dashboard (have a look at the « Grades », the second tab on the results page). But it needs UX improvements. I want it to be easy to understand and efficient to work with.

**Refactor the code:** A human readable tool is nice, but nothing’s better than automated performance monitoring. The next YLT version will be API first.




#### Thanks for reading, and I hope you enjoy <a target="_blank" href="http://yellowlab.tools">my tool</a>!

> Special thanks to <a target="_blank" href="https://twitter.com/stefanjudis">Stefan Judis</a> - creator of <a target="_blank" href="http://perf-tooling.today/">perf-tooling.today</a> for reviewing this article. And thank you <a target="_blank" href="https://github.com/macbre">Maciej Brencz</a> for your excellent tools Phantomas and Analyze-CSS.
