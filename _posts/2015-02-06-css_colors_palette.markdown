---
layout: post
title:  "Keep an eye on the number of different colors in your CSS"
date:   2015-02-06 22:09:00
categories: yellowlabtools
excerpt: and generate the colors palette
comments: true
---

[Yellow Lab Tools][YellowLab.tools] now retrieves the list of colors defined in CSS on a page from analyze-css (by [@macbre][@macbre]). It then transforms the list in a nice colors palette, where each color is sized by number of occurrences.

![YellowLabTools colors palette](/assets/palette-etsy.png)



### How to get your own?

1. Go to [Yellow Lab Tools][YellowLab.tools]

2. Enter the URL of a webpage

3. Once the test is complete, click on the "Different colors" line.

    ![YellowLabTools colors line](/assets/colors-line.png)

4. The palette is on this page. Here you go!



### Good practices

Try to maintain a small palette.

Switching to a CSS pre-compiler such as Less or Sass can help. Set a list of authorised colors in variables and don't allow any other color that is not a variable to be set on a property.

Be extra-careful with colors close to each other. This tool can help you by telling wich colors could be merged as the human eye wouldn't see the diffences: [css-colorguard][css-colorguard].



[YellowLab.tools]:          http://yellowlab.tools
[@macbre]:                  https://github.com/macbre/analyze-css
[css-colorguard]:           https://github.com/SlexAxton/css-colorguard