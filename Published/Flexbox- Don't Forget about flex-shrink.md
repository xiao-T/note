## Flexbox: Don't Forget about flex-shrink
## Flexbox: 不要忽略 flex-shrink

> 原文地址: [https://codepen.io/noahblon/post/practical-guide-to-flexbox-dont-forget-about-flex-shrink](https://codepen.io/noahblon/post/practical-guide-to-flexbox-dont-forget-about-flex-shrink)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> As I learned flexbox, I was captivated by the way items grow to fit their container. But, recently I've realized its just as important to think about the way items shrink. Let me show you what I mean.

我在学习 Flexbox 时，我一直没有明白子元素是如何在父级容器中填充的。但是，最近终于明白了子元素是如何在父及容器中伸缩填充的了。让我给你们一一解释。

>A typical usecase for flexbox is a set of items that we want to be spaced out evenly in a row. Something like this: 

一个典型的例子：有一个 flexbox 容器，我们让它的子元素均匀的排列在一行。就像这样：

**Demo1:**     
[https://codepen.io/xiao-T/pen/NvoNqK](https://codepen.io/xiao-T/pen/NvoNqK)
> 译者注：原作者 Demo 有问题，我做了调整。具体原因原作者在下面也有提到。

> We create a flex context in a container with ```display: flex```. Flex items are spaced out evenly along the main axis with ```justify-content: space-between```. Everything looks great... until we start adding more items.

我创建了一个 flexbox 容器。通过属性 ``` justify-content: space-between ``` 设置它的子元素沿着主轴方向均匀分布。一切看起来那么完美，直到我又添加了更多子元素后，就出现问题了。

** Demo2: **   
[https://codepen.io/xiao-T/pen/wqNGev](https://codepen.io/xiao-T/pen/wqNGev)
> 译者注：原作者 Demo 有问题，我做了调整。具体原因原作者在下面也有提到。

> Ugh, the images are all squished up! This happens because by default, the value for ```flex-shrink``` on items in a flex context is 1. That means that whenever the size of a set of flex items adds up to be larger than our flex context element, the items shrink down to fit.

呃，```flex-item``` 都被挤压变形了！这是因为 ```flex-shrink``` 默认值是```1```。也就是说：如果一组 ```flex-item``` 超出了父级容器的大小，那么这些 ```flex-item``` 将被压缩，来适应父及容器的大小。

>(NOTE: Since this post, Chrome has changed the way it handles images in flex context. They now maintain their minimum instrinsic size. For this reason, the images are not shrunk to fit anymore. However, the ideas behind this post is still valid.)

(备注：在我写这边文章的时候，Chrome已经修改了图片在 ```flex``` 容器中的处理方式。图片并没有被压缩。不管怎么说，这并不影响以上的观点。)

>  译者注：对于这点大家可以对比译者的 Demo 和原作者的 Demo。

> The fix is simple: ```set flex-shrink: 0``` on the items that need to remain a fixed size. Setting overflow: auto on the container clears up the issue of the images now overflowing their container.

修复它也非常简单：只要在 ```flex-item``` 设置 ```flex-shrink: 0```，这样设置后 ```flex-item``` 并不会被压缩，而是保持原有的大小。然后，我们只需在父容器上设置 ```overflow:hidden``` 来修复那些溢出隐藏的 ```flex-item```。

**Demo3**
[https://codepen.io/noahblon/pen/ZGOWdj](https://codepen.io/noahblon/pen/ZGOWdj)

> A slightly more complex example is this typical native mobile application style:  

移动应用的布局是一个典型的略显复杂的例子：

**Demo4**    
[https://codepen.io/noahblon/pen/BNjjvj](https://codepen.io/noahblon/pen/BNjjvj)

> A fixed size header and a footer are clamped to the top and bottom of the viewport and a flexible content area fills up the remaining space. The main area should scroll when the content overflows and the header and footer should remain a fixed size.

一个固定大小的 ```header``` 和 ```footer``` 分别占据着可视窗口的顶部和底部，还有一个可伸缩的容器 ```main``` 占据了剩余的空间。```main``` 区域的内容如果过多，它是可以滚动的，但是这并不影响 ```header``` 和 ```footer``` 的大小。

> To achieve this pattern we set .app to be the full size of the viewport, give it a flex context and set the ```flex-direction``` to apply vertically. Our header and footer are a fixed height, and the main content area is set to grow or shrink to fit the remaining space. Unlike ```flex-shrink```, the default value for ```flex-grow``` is 0, meaning flex items won't grow to fill space unless you tell them to.

为了实现这种布局，我新建一个 ```.app``` 元素并设置它的大小和可视窗口一样，给它设置了 ```flex``` 上下文，通过 ```flex-direction``` 控制子元素垂直排列。```header``` 和 ```footer``` 固定高度，```main``` 通过设置 ```grow``` 和 ```shrink``` 让它占据剩余的空间。```flex-grow``` 和 ```flex-shrink``` 不一样，它的默认值是```0```，也就是说，```flex-item``` 不会自动放大占据所有的空间，除非你设置 ```flex-grow``` 一个值。

> But, we have a problem once we start putting in content. The elements we want to remain a fixed size start to shrink when the content in ```.main``` overflows.

但是，随着 ```.main``` 内容的增多，我发现一个问题：```header``` 和 ```footer``` 被压缩了。

**Demo5**   
[https://codepen.io/noahblon/pen/NqrYra](https://codepen.io/noahblon/pen/NqrYra)

> This happens for two reasons. First, as we already know, all flex items have a ```flex-shrink``` value of 1 by default. They will shrink if the content overflows the container setting the flex context. Secondly, the full size of the .main container is used in the flex calculation, not just the visible portion. This is because by default, the ```flex-basis``` value of a flex item is auto. The ```flex-basis``` is the size basis that is used when calculating how items flex and a value of auto means the full size is used. To fix this, we just stop the header and footer from shrinking with ```flex-shrink: 0```.

之所以会这样，有两个原因。第一，正如我们所知，所有的 ```flex-item``` 都有一个默认值 ```flex-shrink: 1```。如果内容在一个 ```flex``` 容器中溢出了，这些 ```flex-item``` 将会被压缩。第二，```.main``` 的弹性计算，并不仅仅是可见区域。这是因为 ```flex-item``` 的属性 ```flex-basis``` 的默认值是 ```auto```。通过设置 ```flex-basis``` 可以控制 ```flex-item``` 的填充模式，如果 ```flex-basis``` 的值是 ```auto``` 元素会占据所有的空间。如果想避免这种行为，我们可以只简单设置 ```header``` 和 ```footer``` 的属性 ```flex-shrink: 0``` 来禁止它们被压缩。

**Demo6**   
[https://codepen.io/noahblon/pen/WvxzMJ](https://codepen.io/noahblon/pen/WvxzMJ)

> You're going to run into problems like this often when working with flexbox. When I'm approaching a layout, I like to first think about what items need to remain a fixed size, and what items will flex. I also think about whether I need to handle overflow. With those two ideas in mind, flexbox layout becomes a matter of turning off shrinking on items that need to be a fixed size. Items that are flexible are set to grow and shrink, and overflow is handled if necesarry.

当你使用 ```flexbox``` 时会经常遇到类似的问题。当我开始写一个新的布局结构时，我首先会想一下，那些子元素需要固定大小，那些需要弹性计算。也会考虑是否需要处理溢出的内容。经过这两点的考虑，```flexbox``` 布局变成了一个子元素是否需要伸缩的问题。子元素通过设置 ```flex-grow``` 和 ```flex-shrink``` 来控制是否伸缩，在必要的情况下处理一下溢出的问题。

> For more practical tips about flexbox, you can check out [my blog](https://codepen.io/noahblon/posts/popular/) or [subscribe to my feed](https://codepen.io/noahblon/public/feed/). You can also [follow me on Codepen](https://codepen.io/noahblon/). 

更多有关 ```flexbox``` 的资讯，你可以[关注](https://codepen.io/noahblon/posts/popular/)或者[订阅](https://codepen.io/noahblon/public/feed/)我的博客，或者在 [Codepen](https://codepen.io/noahblon/) 上关注我。