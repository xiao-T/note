## How to use SVG as a Placeholder, and Other Image Loading Techniques
## 如何用 SVG 做占位符和其他的图片加载技术

> 原文地址: <https://medium.freecodecamp.org/using-svg-as-placeholders-more-image-loading-techniques-bed1b810ab2c>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://cdn-images-1.medium.com/max/2000/0*zJGl1vKLttcJGIL4.jpg)

Generating SVGs from images can be used for placeholders. Keep reading!

根据图片生成的 SVG,用来做占位符。继续阅读！

I’m passionate about image performance optimisation and making images load fast on the web. One of the most interesting areas of exploration is placeholders: what to show when the image hasn’t loaded yet.

我热衷于图片的性能优化，让其在浏览器中加载更快。占位符就是其中之一：它会在图片未加载前显示。

During the last days I have come across some loading techniques that use SVG, and I would like to describe them in this post.

在过去的几天，我看到了一些 SVG 的加载技术，我也非常愿意为大家一一介绍它们。

In this post we will go through these topics:

- Overview of different types of placeholders
- SVG-based placeholders (edges, shapes and silhouettes)
- Automating the process.

在这里我们将会讨论以下几个主题：

- 几种不同占位符的概述
- 基于 SVG 的占位符（edges、shapes 和 silhouettes)
- 自动化

### Overview of different types of placeholders
### 几种不同占位符的概述

In the past [I have written about placeholders and lazy-load of images](https://medium.com/@jmperezperez/lazy-loading-images-on-the-web-to-improve-loading-time-and-saving-bandwidth-ec988b710290), and also [talked about it](https://www.youtube.com/watch?v=szmVNOnkwoU). When doing lazy-loading of images it’s a good idea to think about what to render as a placeholder, since it can have a big impact in user’s perceived performance. In the past I described several options:

早前我有写过有关[占位符和图片懒加载](https://medium.com/@jmperezperez/lazy-loading-images-on-the-web-to-improve-loading-time-and-saving-bandwidth-ec988b710290)的文章，[演讲](https://www.youtube.com/watch?v=szmVNOnkwoU)中也说过。在处理懒加载的时候给图片加一个占位符是很不错的想法，这在用户体验上会有很大的提升。早前我也介绍过几种方法：

![](https://cdn-images-1.medium.com/max/2000/0*jlMM144vAhH-0bEn.png)

Several strategies to fill the area of an image before it loads.

在图片加载前填充图像区域的几种策略。

- **Keeping the space empty for the image**: In a world of responsive design, this prevents content from jumping around. Those layout changes are bad from a user’s experience point of view, but also for performance. The browser is forced to do layout re calculations every time it fetches the dimensions of an image, leaving space for it.

- **图像区域留空**：在做响应布局的时候，这可以避免布局抖动。布局的抖动从用户体验角度来说是很不友好的，当然性能也不好。每次浏览器获取到图片的大小都会重新计算布局，为其留出空间。

- **Placeholder**: Imagine that we are displaying a user’s profile image. We might want to display a silhouette in the background. This is shown while the main image is loaded, but also when that request failed or when the user didn’t set any profile picture at all. These images are usually vector-based, and due to their small size are a good candidate to be inlined.

- **占位符**：想象一下我们需要一张显示用户资料的图片。我们可以在背景中先显示一张缩略图。在主图片未加载、请求失败或者用户什么都没设置的时候会显示缩略图。这些图片通常都是矢量图，非常小，一般会内嵌到页面上。

- **Solid colour**: Take a colour from the image and use it as the background colour for the placeholder. This can be the dominant colour, the most vibrant… The idea is that it is based on the image you are loading and should help making the transition between no image to image loaded smoother.

- **纯色**：从图片中取一个色值，可以用来做为占位符的背景色。为了图片和占位符之间能够平滑过度，颜色可以是图片的主色调。

- **Blurry image**: Also called blur-up technique. You render a tiny version of the image and then transition to the full one. The initial image is tiny both in pixels and kBs. To remove artifacts the image is scaled up and blurred. I have written previously about this on [How Medium does progressive image loading](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d), [Using WebP to create tiny preview images](https://medium.com/@jmperezperez/using-webp-to-create-tiny-preview-images-3e9b924f28d6), and [More examples of Progressive Image Loading](https://medium.com/@jmperezperez/more-examples-of-progressive-image-loading-f258be9f440b).

- **模糊的图片**：也称为模糊技术。首先，显示一张小图片，然后显示完整的图片。初始图片像素和质量都很小。为了视觉效果图片需要放大和模糊处理。我曾经也写过：[Medium 如何处理图片渐进加载技术](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d)、[如何用 WebP 创建小的预览图片](https://medium.com/@jmperezperez/using-webp-to-create-tiny-preview-images-3e9b924f28d6)、[更多的图片渐进加载技术](https://medium.com/@jmperezperez/more-examples-of-progressive-image-loading-f258be9f440b)。

Turns out there are many other variations and lots of smart people are developing other techniques to create placeholders.

有很多的变种技术，很多聪明的开发者也在用其他的技术创建占位符。

One of them is having gradients instead of solid colours. The gradients can create a more accurate preview of the final image, with very little overhead (increase in payload).

其中之一就是用渐变代替纯色。渐变可以生成更精准的图片预览，只需很小的开销（有效的增加负载）

![](https://cdn-images-1.medium.com/max/1600/0*ecPkBAl69ayvRctn.jpg)
Using gradients as backgrounds. Screenshot from Gradify, which is not online anymore. Code [on GitHub](https://github.com/fraser-hemp/gradify).  
用渐变生成的背景。截图来自 Gradify，线上地址已无效。[GitHub](https://github.com/fraser-hemp/gradify) 上的代码。

Another technique is using SVGs based on the image, which is getting some traction with recent experiments and hacks.

另外一项技术是使用基于图片生成的 SVG，这是通过最近的研究获得的启发。

### SVG-based placeholders
### 基于 SVG 的占位符

We know SVGs are ideal for vector images. In most cases we want to load a bitmap one, so the question is how to vectorise an image. Some options are using edges, shapes and areas.

我们都知道 SVG 是矢量图。大多情况下我们只是想加载一张位图，因此问题是如何让位图矢量化。有以下几种方式可选：edges、shapes 和 areas。

#### Edges

In [a previous post](https://medium.com/@jmperezperez/drawing-images-using-edge-detection-and-svg-animation-16a1a3676d3) I explained how to find out the edges of an image and create an animation. My initial goal was to try to draw regions, vectorising the image, but I didn’t know how to do it. I realised that using the edges could also be innovative and I decided to animate them creating a “drawing” effect.

[上一篇](https://medium.com/@jmperezperez/drawing-images-using-edge-detection-and-svg-animation-16a1a3676d3)文章中我谈论过如何找出图片的轮廓并以动画的形式展现。我最开始的目的只是尝试画出轮廓，把图片矢量化，但是我不知道如何处理。我意识到用 edges 实现它是一种比较新颖的方法，我决定用动画的形式实现“绘画”的效果。

[DEMO](https://codepen.io/jmperez/pen/oogqdp)

#### Shapes

SVG can also be used to draw areas from the image instead of edges/borders. In a way, we would vectorise a bitmap image to create a placeholder.

SVG 也可以基于图片绘制图形来代替轮廓和线框。通过某种方式，我们可以把图片矢量化做为占位符。

Back in the days I tried to do something similar with triangles. You can see the result in my talks [at CSSConf](https://jmperezperez.com/cssconfau16/#/45) and [Render Conf](https://jmperezperez.com/renderconf17/#/46).

早前，我曾经尝试用三角形做类似的事情。你们可以在 [CSSConf](https://jmperezperez.com/cssconfau16/#/45) 和 [Render Conf](https://jmperezperez.com/renderconf17/#/46) 看看我的演讲。

[DEMO](https://codepen.io/jmperez/pen/BmaWmQ)

The codepen above is a proof of concept of a SVG-based placeholder composed of 245 triangles. The generation of the triangles is based on [Delaunay triangulation](https://en.wikipedia.org/wiki/Delaunay_triangulation) using [Possan’s polyserver](https://github.com/possan/polyserver). As expected, the more triangles the SVG uses, the bigger the file size.

codepen 上的演示是基于 SVG 占位符概念的很好的证明，它有 245 三角形组合而成。这些三角形是由 [Possan’s polyserver](https://github.com/possan/polyserver) 基于 [Delaunay triangulation](https://en.wikipedia.org/wiki/Delaunay_triangulation) 生成的。如你所见，越多的 SVG 三角形，文件也越大。

### Primitive and SQIP, a SVG-based LQIP technique

### Primitive 和 SQIP ，基于 LQIP 的技术

Tobias Baldauf has been working on another Low-Quality Image Placeholder technique using SVGs called [SQIP](https://github.com/technopagan/sqip). Before digging into SQIP itself I will give an overview of [Primitive](https://github.com/fogleman/primitive), a library on which SQIP is based.

Tobias Baldauf 已经利用 SVG 实现了一种称为 [SQIP](https://github.com/technopagan/sqip) 低质量图片占位符的技术。在深入 SQIP 之前，我会先介绍一下 [Primitive](https://github.com/fogleman/primitive)，这是基于 SQIP 的一个库。

Primitive is quite fascinating and I definitely recommend you to check it out. It converts a bitmap image into a SVG composed of overlapping shapes. Its small size makes it suitable for inlining it straight into the page. One less roundtrip, and a meaningful placeholder within the initial HTML payload.

Primitive 非常的迷人，我强烈推荐你们去了解它。它可以把一张位图转换成多个图形重叠的 SVG。生成的 SVG 代码非常少，适合直接内嵌到页面上。少加载一次，而且在页面初始化会有个富有意义的占位符。

Primitive generates an image based on shapes like triangles, rectangles and circles (and a few others). In every step it adds a new one. The more steps, the resulting image looks closer to the original one. If your output is SVG it also means the size of the output code will be larger.

Primitive 可以基于一些形状生成一张图片，比如：三角形、矩形和圆形（还有一些其他的图形）。每次只生成一个图形。生成的越多，看起来越接近原图。如果，你导出 SVG，也就说明代码会更多。

In order to understand how Primitive works, I ran it through a couple of images. I generated SVGs for the artwork using 10 shapes and 100 shapes:

为了理解 Primitive 如何工作的，我用一张图片做了几个例子。我生成了 10 个 shapes 和 100 个 shapes 的 SVG 缩略图：

![](https://cdn-images-1.medium.com/max/800/1*y4sr9twkh_WyZh6h0yH98Q.png)
![](https://cdn-images-1.medium.com/max/800/1*cqyhYnx83LYvhGdmg2dFDw.png)
![](https://cdn-images-1.medium.com/max/800/1*qQP5160gPKQdysh0gFnNfw.jpeg)

Processing [this picture](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square.jpg) using Primitive, using [10 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-10.svg) and [100 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-100.svg).

[这张图片](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square.jpg)用 Primitive 渐进的生了 [10 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-10.svg) 和 [100 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-100.svg)。

![](https://cdn-images-1.medium.com/max/800/1*PWZLlC4lrLO4CVv1GwR7qA.png)![](https://cdn-images-1.medium.com/max/800/1*khnga22ldJKOZ2z45Srh8A.png)![](https://cdn-images-1.medium.com/max/800/1*N-20rR7YGFXiDSqIeIyOjA.jpeg)

Processing [this picture](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square.jpg) using Primitive, using [10 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-10.svg) and [100 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-100.svg).

[这张图片](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-618463-square.jpg)用 Primitive 渐进的生了 [10 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-618463-square-10.svg) 和 [100 shapes](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-618463-square-100.svg)。

When using 10 shapes the images we start getting a grasp of the original image. In the context of image placeholders there is potential to use this SVG as the placeholder. Actually, the code for the SVG with 10 shapes is really small, around 1030 bytes, which goes down to ~640 bytes when passing the output through SVGO.

当使用 10 shapes 的时候，我么就可以看到图片的大概。SVG 作为图片的占位符是非常有意义的。事实上，10 shapes 的 SVG 的代码真的非常小，大约 1030B，如果经过 SVGO 处理后会压缩到 640B 左右。

```html
<svg xmlns=”http://www.w3.org/2000/svg" width=”1024" height=”1024"><path fill=”#817c70" d=”M0 0h1024v1024H0z”/><g fill-opacity=”.502"><path fill=”#03020f” d=”M178 994l580 92L402–62"/><path fill=”#f2e2ba” d=”M638 894L614 6l472 440"/><path fill=”#fff8be” d=”M-62 854h300L138–62"/><path fill=”#76c2d9" d=”M410–62L154 530–62 38"/><path fill=”#62b4cf” d=”M1086–2L498–30l484 508"/><path fill=”#010412" d=”M430–2l196 52–76 356"/><path fill=”#eb7d3f” d=”M598 594l488–32–308 520"/><path fill=”#080a18" d=”M198 418l32 304 116–448"/><path fill=”#3f201d” d=”M1086 1062l-344–52 248–148"/><path fill=”#ebd29f” d=”M630 658l-60–372 516 320"/></g></svg>
```

The images generated with 100 shapes are larger, as expected, weighting ~5kB after SVGO (8kB before). They have a great level of detail with a still small payload. The decision of how many triangles to use will depend largely on the type of image (eg contrast, amount of colours, complexity) and level of detail.

如果生成 100 shapes，就很大了，如你所见，经 SVGO 处理后大约 5K（处理前 8K）。但是在细节上会有很大的提升，负载也不会很大。决定使用多少三角形很大情况下依赖图片的类型（比如：对比度、色彩、复杂度）和对细节的要求。

It would be possible to create a script similar to [cpeg-dssim](https://github.com/technopagan/cjpeg-dssim) that tweaks the amount of shapes used until a [structural similarity](https://en.wikipedia.org/wiki/Structural_similarity) threshold is met (or a maximum number of shapes in the worst case).

可以创建一个类似 [cpeg-dssim](https://github.com/technopagan/cjpeg-dssim) 的脚本来调整 shapes 的数量，以达到数量和结构二者最优（或者在最坏情况下 shapes 的最大值）。

These resulting SVGs are great also to use as background images. Being size-constrained and vector-based they are a good candidate for hero images and large backgrounds that otherwise would show artifacts.

这些 SVG 非常适合用来做背景图。由于大小受限和矢量，所以它们是 hero 图片和大图很好得选择。

#### SQIP

In [Tobias’ own words](https://github.com/technopagan/sqip):

引用 [Tobias 自己的话](https://github.com/technopagan/sqip):

> SQIP is an attempt to find a balance between these two extremes: it makes use of [Primitive](https://github.com/fogleman/primitive) to generate a SVG consisting of several simple shapes that approximate the main features visible inside the image, optimizes the SVG using [SVGO](https://github.com/svg/svgo) and adds a Gaussian Blur filter to it. This produces a SVG placeholder which weighs in at only ~800–1000 bytes, looks smooth on all screens and provides an visual cue of image contents to come.

> SQIP 致力于在两个极端间寻找一种平衡：它利用 [Primitive](https://github.com/fogleman/primitive) 生成有几种简单图形组成近似原图的 SVG，利用 [SVGO](https://github.com/svg/svgo) 压缩 SVG 并添加一个高斯模糊。这样生成的 SVG 占位符仅仅只有大约 800–1000B，在所有的设备看起来都很平滑，并提供了图片视觉提示。

The result is similar to using a tiny placeholder image for the blur-up technique (what [Medium](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d) and [other sites](https://medium.com/@jmperezperez/more-examples-of-progressive-image-loading-f258be9f440b) do). The difference is that instead of using a bitmap image, eg JPG or WebP, the placeholder is SVG.

最终结果看起来就像用了一张很小的且经过模糊处理的图片占位符（就像[Medium](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d) 和 [其它网站](https://medium.com/@jmperezperez/more-examples-of-progressive-image-loading-f258be9f440b)）。不同之处就是他们都用的是位图，比如：JPG 或者 WebP，我们用的是 SVG。

If we run SQIP against the original images we’ll get this:

如果原图经过 SQIP 处理后，我们会得到以下的图片：

![](https://cdn-images-1.medium.com/max/1200/0*yUY1ZFP27vFYgj_o.png)![](https://cdn-images-1.medium.com/max/1200/0*DKoZP7DXFvUZJ34E.png)

The output images using SQIP for [the first picture](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-sqip.svg) and [the second one](https://jmperezperez.com/svg-placeholders/%28/assets/images/posts/svg-placeholders/pexels-photo-618463-square-sqip.svg).

以上的图片都由 SQIP 处理过，[第一张](https://jmperezperez.com/assets/images/posts/svg-placeholders/pexels-photo-281184-square-sqip.svg)和[第二张](https://jmperezperez.com/svg-placeholders/%28/assets/images/posts/svg-placeholders/pexels-photo-618463-square-sqip.svg)。

The output SVG is ~900 bytes, and inspecting the code we can spot the ```feGaussianBlur``` filter applied to the group of shapes:

导出的 SVG 大约 900B，检查我们代码，可以发现我们通过 ```feGaussianBlur``` 方法给整组图形添加了滤镜。

```html
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 2000 2000"><filter id="b"><feGaussianBlur stdDeviation="12" /></filter><path fill="#817c70" d="M0 0h2000v2000H0z"/><g filter="url(#b)" transform="translate(4 4) scale(7.8125)" fill-opacity=".5"><ellipse fill="#000210" rx="1" ry="1" transform="matrix(50.41098 -3.7951 11.14787 148.07886 107 194.6)"/><ellipse fill="#eee3bb" rx="1" ry="1" transform="matrix(-56.38179 17.684 -24.48514 -78.06584 205 110.1)"/><ellipse fill="#fff4bd" rx="1" ry="1" transform="matrix(35.40604 -5.49219 14.85017 95.73337 16.4 123.6)"/><ellipse fill="#79c7db" cx="21" cy="39" rx="65" ry="65"/><ellipse fill="#0c1320" cx="117" cy="38" rx="34" ry="47"/><ellipse fill="#5cb0cd" rx="1" ry="1" transform="matrix(-39.46201 77.24476 -54.56092 -27.87353 219.2 7.9)"/><path fill="#e57339" d="M271 159l-123–16 43 128z"/><ellipse fill="#47332f" cx="214" cy="237" rx="242" ry="19"/></g></svg>
```
SQIP can also output an image tag with the SVG contents Base 64 encoded:

SQIP 也可以导出 base64 的图片：

```html
<img width="640" height="640" src="example.jpg” alt="Add descriptive alt text" style="background-size: cover; background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAw…<stripped base 64>…PjwvZz48L3N2Zz4=);">
```

#### Silhouettes

We just had a look at using SVGs for edges and primitive shapes. Another possibility is to vectorise the images “tracing” them. [Mikael Ainalem](https://twitter.com/mikaelainalem) shared [a codepen](https://codepen.io/ainalem/full/aLKxjm/) a few days ago showing how to use a 2-colour silhouette as a placeholder. The result is really pretty:

我们已经了解了利用 SVG 的 edges 和 shapes 实现占位符的方法。另外一种方案是 “tracing”。前几天 [Mikael Ainalem](https://twitter.com/mikaelainalem) 在 [codepen](https://codepen.io/ainalem/full/aLKxjm/) 分享了一些示例：如何用两种颜色实现占位符。最终结果非常的棒：

![](https://cdn-images-1.medium.com/max/1600/1*r6HbVnBkISCQp_UVKjOJKQ.gif)

The SVGs in this case were hand drawn, but the technique quickly spawned integrations with tools to automate the process.


- [Gatsby](https://www.gatsbyjs.org/), a static site generator using React supports these traced SVGs now. It uses [a JS PORT of potrace](https://www.npmjs.com/package/potrace) to vectorise the images.

- [Craft 3 CMS](https://craftcms.com/), which also added support for silhouettes. It uses [a PHP port of potrace](https://github.com/nystudio107/craft3-imageoptimize/blob/master/src/lib/Potracio.php).

- [image-trace-loader](https://github.com/EmilTholin/image-trace-loader), a Webpack loader that uses potrace to process the images.

示例中的 SVG 是手动输出的，但是很快就衍生出很多的自动化工具。

- [Gatsby](https://www.gatsbyjs.org/)，一个用 React 实现可以生成 SVG 的静态站点。它用 [JS 版 的 potrace](https://www.npmjs.com/package/potrace) 实现了图片的矢量化。

- [Craft 3 CMS](https://craftcms.com/)， 也添加了对 silhouettes 的支持，用的是 [PHP 版的 potrace](https://github.com/nystudio107/craft3-imageoptimize/blob/master/src/lib/Potracio.php)。

- [image-trace-loader](https://github.com/EmilTholin/image-trace-loader)，一个 webpack loader 也是用 potrace 处理的图片。

It’s also interesting to see a comparison of the output between Emil’s webpack loader (based on potrace) and Mikael’s hand-drawn SVGs.

有兴趣的也可以看看 webpack loader 和手绘两者的输出对比。

[对比](https://twitter.com/nemtsovy/status/920647706799955970/photo/1?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E920647706799955970&ref_url=https%3A%2F%2Fmedium.freecodecamp.org%2Fmedia%2F8161aeb53a3e9974301b1cbbf0a9814a%3FpostId%3Dbed1b810ab2c)   
> ***需要翻墙***

I assume the output generated by potrace is using the default options. However, it’s possible to tweak them. Check [the options for image-trace-loader](https://github.com/EmilTholin/image-trace-loader#options), which are pretty much [the ones passed down to potrace](https://www.npmjs.com/package/potrace#parameters).

假设自动化程序都用了默认设置。但是，选项还是可以设置的。[webpack loader 的设置选项](https://github.com/EmilTholin/image-trace-loader#options) 和 [JS potrace 设置选择项](https://www.npmjs.com/package/potrace#parameters)几乎一致。

### Summary

We have seen different tools and techniques to generate SVGs from images and use them as placeholders. The same way [WebP is a fantastic format for thumbnails](https://medium.com/@jmperezperez/using-webp-to-create-tiny-preview-images-3e9b924f28d6), SVG is also an interesting format to use in placeholders. We can control the level of detail (and thus, size), it’s highly compressible and easy to manipulate with CSS and JS.

我也看到过利用不同的工具和技术生成图片的 SVG 占位符。WebP 格式是缩略图的一种理想格式，SVG 用作占位符也是很有新意的。我们可以控制 SVG 的细节（从而影响它的大小），它有高度可压缩性，也可以很容易的通过 CSS 和 JS 操作。

### Extra Resources

This post made it to [the top of Hacker News](https://news.ycombinator.com/item?id=15696596). I’m very grateful for that, and for all the links to other resources that have been shared in the comments on that page. Here are a few of them!

- [Geometrize](https://github.com/Tw1ddle/geometrize-haxe) is a port of Primitive written in Haxe. There is also a [JS implementation](https://github.com/Tw1ddle/geometrize-haxe-web) that you can try out directly [on your browser](http://www.samcodes.co.uk/project/geometrize-haxe-web/).

- [Primitive.js](https://github.com/ondras/primitive.js), which is a port of Primitive in JS. Also, [primitive.nextgen](https://github.com/cielito-lindo-productions/primitive.nextgen), which is a port of the Primitive desktop app using Primitive.js and Electron.

- There are a couple of Twitter accounts where you can see examples of images generated with Primitive and Geometrize. Check out [@PrimitivePic](https://twitter.com/PrimitivePic) and [@Geometrizer](https://twitter.com/Geometrizer).

- [imagetracerjs](https://github.com/jankovicsandras/imagetracerjs), which is a raster image tracer and vectorizer written in JavaScript. There are also ports for [Java](https://github.com/jankovicsandras/imagetracerjava) and [Android](https://github.com/jankovicsandras/imagetracerandroid).

- [Canvas-Graphics](http://thomas.weinert.info/Canvas-Graphics/), a partial implementation of the JS Canvas API in PHP around GD.

这篇文章能被 [Hacker News 收录并进入热门榜单](https://news.ycombinator.com/item?id=15696596)。我非常感激，也会在 Hacker News 评论中把所有涉及到资源分享出来。这里也有一些！

- [Geometrize](https://github.com/Tw1ddle/geometrize-haxe) 是用 Haxe 实现的 Primitive。也有一个 [JS 版的](https://github.com/Tw1ddle/geometrize-haxe-web)，可以用[浏览器](http://www.samcodes.co.uk/project/geometrize-haxe-web/)上浏览。

- [Primitive.js](https://github.com/ondras/primitive.js)，是一个 JS 版本的 Primitive。另外，[primitive.nextgen](https://github.com/cielito-lindo-productions/primitive.nextgen)，是用 Primitive.js 和 Electron 实现的 Primitive 桌面应用。

- 在 Twitter 上也可以看到一些通过 Primitive 和 Geometrize 的实例，比如：[@PrimitivePic](https://twitter.com/PrimitivePic) and [@Geometrizer](https://twitter.com/Geometrizer)（***需要翻墙***）。

- [imagetracerjs](https://github.com/jankovicsandras/imagetracerjs), JavaScript 实现的图片 tracer 和 vectorizer。也有 [Java](https://github.com/jankovicsandras/imagetracerjava) 很 [Android](https://github.com/jankovicsandras/imagetracerandroid) 版。

- [Canvas-Graphics](http://thomas.weinert.info/Canvas-Graphics/)，是 PHP 结合 JS Canvas API 对 GD 的部分实现。

### Related Posts

If you have enjoyed this post, check out these other posts I have written about techniques loading images:

如果，你喜欢这篇文章，还可以看看我之前写的有关图片加载的文章：

[DEMO](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d)

[DEMO](https://medium.com/@jmperezperez/using-webp-to-create-tiny-preview-images-3e9b924f28d6)

[DEMO](https://medium.com/@jmperezperez/more-examples-of-progressive-image-loading-f258be9f440b)

You can read more of my articles [on jmperezperez.com](https://jmperezperez.com/svg-placeholders/).

你可以在[我的博客](https://jmperezperez.com/svg-placeholders/)上看到更多的文章。


