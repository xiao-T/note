
## Flexbox: space-between, Flexbox's Unsung Hero
##  Flexbox 的幕后英雄：space-between

> 原文地址: [https://codepen.io/noahblon/post/a-practical-guide-to-flexbox-understanding-space-between-the-unsung-hero](https://codepen.io/noahblon/post/a-practical-guide-to-flexbox-understanding-space-between-the-unsung-hero)     
**本文版权归原作者所有，翻译仅用于学习。**

-----

> The more I use ```flexbox```, the more one rule comes up over and over again: ```justify-content: space-between```. In this post, I'll demonstrate how I approach a layout problem with ```flexbox``` and how useful the ```space-between``` pattern is for tackling tricky layouts very efficiently.

随着使用 ```flexbox``` 的次数增加，其中一个规则 ```justify-content: space-between```，也频繁的出现。在这篇文章中，我将演示如何有效的利用 ```space-between``` 解决 ```flexbox``` 中棘手的问题。

> ### space-between    

### space-between   

> ```space-between``` is a value for the ```justify-content``` property. It places all flex items equally along the main axis. Any leftover space that the flex items don't take up is equally distributed between the items. It's like floating items to the left and right, but you don't have to clear anything AND you can do it horizontally OR vertically. That's a pattern that comes up basically always.

```space-between``` 是属性 ```justify-content``` 其中的一个值。它可以让所有的 ```flex``` 子元素沿着主轴的方向平均分布。所有为被暂用的剩余空间，也会在子元素之间平均分布。看起来就像子元素浮动在左边和右边，但是这并不需要清除浮动，并且你可以控制子元素水平和垂直排列。这是一个很基本的布局。

> The coolest thing about ```space-between``` is the items you are spacing out maintain their intrinsic size. This makes for some very efficient markup because you really don't need many wrapping elements - many times you can just let flex position items and you're good.

更神奇的是：```space-between``` 可以让子元素保持原有大小。实现起来非常方便，并需要多余的元素去包裹它们，你只需要在父级设置 ```display: flex``` 即可。

> As with most things, IMO it's best explained with a demo. Let's imagine we have an awesome project to build a Super Smash Bros. tournament app and a designer hands us this:

很多情况下，IMO 是一个很好的示例。我们想象一下，我通过一个很棒的项目创建了一个比赛App： Super Smash Bros，设计 UI 如下：    

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/example.png)   

> Here's how I'd approach building the header section - the part with the images of the fighters, the icons, their names, the score, etc. - with flexbox and space-between.

现在让我们先利用 ```space-between``` 处理 App 头部的，它包括了图片、icons、名字、比分等等。

> ### Figure 1 - Breaking down the layout problem

### 图1：分拆布局     

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/figure1.jpg)

> The first thing I like to do when approaching a layout problem is to break it into its components. There is the background image, which we can handle with absolutely positioning and taking it out of the flow.

首先，在开始前，我会考虑把布局拆分成组件形式。例如：上面这个列子中有一个背景图，我会用绝对定位处理，让它脱离文档流。

> That leaves the top content to position on top of the images. There are two regions:

让其他内容覆盖在背景图的上面。这有两个区域：

> 1. A top section with some icon buttons, one of the left and two on the right.
> 2. A bottom section with the fighter's name, the final score, icons and so on.

1. 顶部：包含了一些 icon 按钮，一个在左边，两个在右边。
2. 底部：包含了参赛者名字、比分、icons 等等。

> For these two sections, the main axis is vertical. Setting display: flex and flex-direction: column with justify-content: space-between fixes the flex items to the top and bottom of the header. 

对于这两个区域来说，它们的主轴是垂直的。通过设置 ```display: flex; flex-direction: column; justify-content: space-between``` 让它们分别固定在顶部和底部。

> To illustrate how this works, I've given the sections a set height, but normally we can just let the size of the content within a flex item determine it's size. As I move through the following examples, I will remove these set heights. Here's a demo.

为了演示它是如何工作的，我在外层上设置了高度，但是通常下我们让内容来决定大小。当我通过以下示例后，我会删除它们的高度。这是示例。

[**Demo1**](https://codepen.io/noahblon/pen/EjVjJV)    
[https://codepen.io/noahblon/pen/EjVjJV](https://codepen.io/noahblon/pen/EjVjJV)

> Note: We need to actually keep the background outside of the flex context because of [a bug in Firefox](https://github.com/philipwalton/flexbugs/issues/18) with space-between erroneously applying space to absolutely positioned elements. To do this I've wrapped the content in an absolutely positioned element - .header-content - as well and set the flex context there.

⚠️：事实上我需要包一层 ```.header-content``` 用来放背景图，然后，我们在 ```.header-content``` 设置 ```flex``` 上下文。这是因为 [Firefox 的一个 bug](https://github.com/philipwalton/flexbugs/issues/18)：```space-between``` 错误的导致绝对定位的元素并没有脱离文档流。

> ### Figure 2 - Flexing and grouping items

### 图2：Flex 分组。

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/figure2.jpg)

> Now let's tackle the top section. This time the items are fixed to the sides of the header, but in the horizontal direction. Set ```display:flex``` on the section sets up our flex context. We don't need to explicitly set the ```flex-direction``` because the value is row, or horizontal, by default. ```justify-content: space-between``` is used again to fix the items to the sides of the flexbox. The two icons on the right are wrapped in an element to fix them on the right side together. Using a wrapper like this is an easy and efficient way to group flex items together. In the right wrapper we can use another cool flexbox trick and give it ```display: inline-flex```. This is similar to setting ```display: inline-block``` on each of the items to allow them to sit next to each other. But, unlike ```inline-block```, we don't need to set each item, and we're not left with an annoying space between the items that needs to be killed. 

现在让我们解决顶部 ```section``` 的问题。这次子元素是水平排列在 ```header``` 的两边的。通过给 ```section``` 设置 ```display: flex``` 生成一个 ```flex``` 上下文环境。默认情况下子元素是水平排列的，我们并不需要明确的指明 ```flex-direction```。```justify-content: space-between``` 再一次被用来让子元素分布在父级的两边。在右边有两个 ```icons``` 被一个元素包裹起来是为了更好彼此贴近。在这我们可以使用另外一个很酷的技巧： ```display: inline-flex```。它看起来很像 ```display: inline-block``` 让子元素水平排列。但是，不像 ```inline-block``` 我们不用在每个子元素上设置，也不用担心元素之间的空格问题。

[**Demo2**](https://codepen.io/noahblon/pen/YXyXbQ)   
[https://codepen.io/noahblon/pen/YXyXbQ](https://codepen.io/noahblon/pen/YXyXbQ)

> ### Figure 3 - The efficiency of space-between

图3：```space-between``` 的效率

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/figure3.jpg)

> Now for the bottom section. This is just two boxes stacked on top of each other. Similar to the wrapper used to group the icons in the top section, we don't need to do anything to these items. Again, I've given these items a set ```height``` for illustration, but really I can just let this section ```shrink-wrap``` to it's content.

现在我们来解决底部的布局问题。它包括上下两部分。这有点类似顶部右边的分组，我暂时不对子元素做任何处理。为了演示效果，我给子元素设置了 ```height```，但是实际应用中是不需要设置的，只需要弹性适应。

[**Demo3**](https://codepen.io/noahblon/pen/doYoxW)   
[https://codepen.io/noahblon/pen/doYoxW](https://codepen.io/noahblon/pen/doYoxW)

### Figure 4

### 图4

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/figure4.jpg)

> The top part of the bottom section is similar to the header. The items are fixed to both sides on the horizontal axis. Simple enough! ```justify-content: space-between```. Is this starting to get repetitive? I hope so.

底部的上半部分有点类似 ```haader```。它的子元素是水平排列在两边的。用 ```justify-content: space-between``` 很容易实现。看起来有点重复了。

[**Demo4**](https://codepen.io/noahblon/pen/bdVVbm)  
[https://codepen.io/noahblon/pen/bdVVbm](https://codepen.io/noahblon/pen/bdVVbm)

### Figure 5

### 图5

![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/18515/figure5.jpg)

> The bottom part of the bottom section has items fixed to each side, and an item in the middle. Again, ```space-between``` has us covered - a third item in a ```space-between``` flex context is placed in the center. So powerful!

底部的下半部分，其中两个元素分布在两边，另外一个在中间。```space-between``` 再次出现了，第三个元素完美的居中，就是它的功劳。太棒了！

[**Demo5**](https://codepen.io/noahblon/pen/waKKBY)  
[https://codepen.io/noahblon/pen/waKKBY](https://codepen.io/noahblon/pen/waKKBY)

### Figure 6 - All together now!

### 图6：整合

> Here it all is with the real content plugged in and styled.

以下的示例都是真实的内容和样式。

[**Demo6**](https://codepen.io/noahblon/pen/GJppMX)  
[https://codepen.io/noahblon/pen/GJppMX](https://codepen.io/noahblon/pen/GJppMX)   

> And here it is with the background, an ```SVG``` I created to make the images blend into one another and be dynamically colored. Stay tuned for a post about that!

背景我是通过 ```SVG``` 混合图片实现的。敬请关注这个帖子。

[**Demo7**](https://codepen.io/noahblon/pen/MwabGx)  
[https://codepen.io/noahblon/pen/MwabGx](https://codepen.io/noahblon/pen/MwabGx)

> For more practical tips about flexbox, you can check out [my blog](https://codepen.io/noahblon/posts/popular/) or [subscribe to my feed](https://codepen.io/noahblon/public/feed/). You can also [follow me on Codepen](https://codepen.io/noahblon/).

更多有关 ```flexbox``` 的资讯，你可以[关注](https://codepen.io/noahblon/posts/popular/)或者[订阅](https://codepen.io/noahblon/public/feed/)我的博客，或者在 [Codepen](https://codepen.io/noahblon/) 上关注我。

