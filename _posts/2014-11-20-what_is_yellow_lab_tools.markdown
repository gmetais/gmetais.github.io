---
layout: post
title:  "What is Yellow Lab Tools?"
date:   2014-11-20 16:09:00
categories: yellowlabtools
excerpt: Description of the tool
comments: true
---

Yellow Lab Tools runs on a Node.js server and lets developers test a website (via an URL). This is done by accessing the website via [PhantomJS][phantomjs] and collecting various metrics and statistics.

These metrics are then manipulated and presented in an easy to understand manner, organized in categories of interest.

The main utility is a grading feature that provides A-to-F grades based on the performance of various utilities and operations like DOM complexity, DOM manipulation, the number of HTTP requests, network interactions, CSS complexity, and so on.

There's also a section for analyzing the JavaScript execution timeline, complete with a JavaScript profiler that lets developers see how JS scripts have been executed, what where their results and how it impacted the page performance.

All in all, Yellow Lab Tools can be really useful for both beginner and advanced coders, helping them improve or fix the way they write JS and CSS, boosting page loading speeds and their code's overall performance.

![YellowLabTools logo](/assets/logo-large.png)

It is totally free, [open source][github/yellowlabtools] and here is the link to the tool: [http://yellowlab.tool][YellowLab.tools].

[YellowLab.tools]:          http://yellowlab.tools
[github/yellowlabtools]:    https://github.com/gmetais/YellowLabTools
[phantomjs]:                http://phantomjs.org/
