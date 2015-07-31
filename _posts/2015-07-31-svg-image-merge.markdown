---
layout: post
title:  "Use SVG to optimize non-vectorial images"
date:   2015-07-31 12:17:00
categories: image-optim
excerpt: for image optimization freaks
comments: true
---

*An optimization technic for image optimization freaks*


This morning, you need to add to some website an image that contains both a photography and text. Pretty easy to do with your favorite image editor.

Done!

![Just created image](/assets/optimized-90.jpg)

You save it as a JPEG and you see the weight on your drive: **511KB!**

So it’s time to optimize it, and here comes the hard part… If you optimize the image as a JPEG below the 80% quality, you’re able to obtain a small weight, but the quality of the text suffers, with some **approximations and artefacts** due to the compression algorithm. It’s too sad, because the background is still good with a 65% compression…

![JPEG encoding artefacts](/assets/not-so-sharp.png)

You try as a PNG: the quality is perfect, but it’s even bigger than the original JPEG! So you end up trying to balance JPEG encoding and text quality and finally choose a quality of 90%.

--

| JPEG 100%                        | 511KB    |
| PNG                              | 587KB    |
| **JPEG 90%**                     | **84KB** |

--

Well, there might be a solution for this problem ...


### Keep the background and the text in two separated images…

Save the background in JPEG and optimize it as much as you want.

![optimized background](/assets/background-65.jpg)

Save the text as a PNG with a transparent background and optimize it losslessly.

![optimized text](/assets/text-alone.png)


### ... and use SVG to stick them into a single image file

You could insert these two images in HTML with CSS positioning. **But if you really need a single image file**, like if it needs to be inserted in your Wordpress or in a carousel. 
Use your text editor to create an SVG file like this:

```xml
<svg width="800" height="600">
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/jpg;base64,{{base64-encoded-background}}" />
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/png;base64,{{base64-encoded-top}}" />
</svg>
```

Then compare with other formats. The result file is not always smaller than JPEG. It will depend on the possibility to optimize both JPEG and PNG files separately.

But wait! Make sure your server correctly serves your SVG files gzipped. That’s a very common mistake.

![check gzip compression](/assets/gzip.png)


### Result

![result svg file](/assets/result.svg)

--

| JPEG background 65% alone        | 32KB     |
| PNG text alone                   | 17KB     |
| Result SVG                       | 64KB     |
| **Result SVG gzipped**           | **47KB** |

--


### Browser compatibility

Sorry my old IE8 friend, you're not able to understand SVG images...


### I started a small tool

[Here][svg-image-merge] is a small command line tool I wrote, that will do the technical part for you: base64 encode the files and insert them into the SVG.



[svg-image-merge]:      https://github.com/gmetais/svg-image-merge
