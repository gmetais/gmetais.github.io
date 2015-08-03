---
layout: post
title:  "Use SVG to optimize non-vector images"
date:   2015-07-31 12:17:00
categories: image-optim
excerpt: An optimization technic for image optimization freaks
comments: true
---

*An optimization technic for image optimization freaks.*


This morning, you need to add to some website an image that contains both a photography and text. Pretty easy to do with your favorite image editor.

Done!

![Just created image](/assets/optimized-90.jpg)

You save it as a JPEG and you see the weight on your drive: **511KB!**

So it’s time to optimize it, and here comes the hard part... If you optimize the image as a JPEG below the 80% quality, you’re able to obtain a small weight, but the quality of the text suffers, with some **approximations and artefacts** due to the compression algorithm. It’s too sad, because your background photography is still good with a 65% compression...

![JPEG encoding artefacts](/assets/not-so-sharp.png)

You try as a PNG: the quality is perfect, but it’s even bigger than the original JPEG! So you end up trying to balance JPEG encoding and text quality and finally choose a quality of 90%.

--

| JPEG 100%:                        | 511KB    |
| PNG:                              | 587KB    |
| **JPEG 90%:**                     | **84KB** |

--

Well, there might be a solution for this problem ...


### Keep the background and the text in two separated images...

Save the background in JPEG and optimize it as much as you want.

![optimized background](/assets/background-65.jpg)

Save the text as a PNG with a transparent background and optimize it losslessly ([ImageOptim][ImageOptim] on Mac or [Kraken.io][Kraken.io] online).

![optimized text](/assets/text-alone.png)


### ... and use SVG to stick them into a single image file

You will argue, you could insert these two images in HTML and position them on top of each other. Yes, definitely! Even better, write the text in HTML directly if you can!

**But what if you REALLY need a single image file**, like if it needs to be inserted in your Wordpress, in a carousel, in any template that does not allow anything but an image? Or maybe you simply want to keep the semantics of a single real image in your markup.

A little known SVG functionnality lets you insert a non-SVG image inside an SVG, base64 encoded. Use your text editor and create an SVG file like this:

```xml
<svg width="800" height="600">
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/jpg;base64,{{base64-encoded-background}}" />
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/png;base64,{{base64-encoded-top}}" />
</svg>
```

Then compare with JPEG encoding test files. The result file is not always smaller than JPEG. It will depend on the optimization capacity of both JPEG and PNG files separately.

But wait! **Compare the weights after compressing your SVG** (with gzip or zip). Unlike the other image formats, SVG is an uncompressed format.

Make sure your server correctly serves your SVG files gzipped. That’s a very common configuration mistake. Gzip is what will prevent base64 encoding to make performance implications.

![check gzip compression](/assets/gzip.png)


### Result

![result svg file](/assets/result.svg)

--

| JPEG background 65% alone:        | 32KB     |
| PNG text alone:                   | 17KB     |
| Result SVG:                       | 64KB     |
| **Result SVG gzipped:**           | **47KB** |

--

As you can see, base64 encoding is not a problem: the gzipped SVG is even smaller than the sum of the JPEG and the PNG.

*The text could also be directly written with SVG. Of course, in this case, don't base64 encode it.*



### Browser compatibility

**Be careful!** Safari 8 is not able to display base64 encoded images inside an SVG if the SVG is loaded via `<img>`. It's working correctly with an `<object>` tag. It seems to be corrected in Safari 9, if anyone can test and tell me.

Sorry my old IE8 friend, you're not able to understand SVG images...


### I started a small tool

[Here][svg-image-merge] is a command line tool I wrote, that will do the technical part for you: base64 encode the files and inject them into the SVG.


--

*Special thanks to <a target="_blank" href="https://twitter.com/sarasoueidan">Sara Soueidan</a> — Front-end and SVG expert — for reviewing this article.*


[svg-image-merge]:      https://github.com/gmetais/svg-image-merge
[ImageOptim]:           https://imageoptim.com/
[Kraken.io]:            https://kraken.io/
