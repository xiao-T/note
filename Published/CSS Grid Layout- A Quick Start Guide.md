## CSS Grid Layout: A Quick Start Guide
## CSS Grid 布局：快速入门指南

> 原文地址: <https://webdesign.tutsplus.com/tutorials/css-grid-layout-quick-start-guide--cms-27238/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

How we approach layout on the web is changing, and at the forefront of that change is [CSS Grid Layout](https://www.w3.org/TR/css-grid-1/). This updated quick start guide will skip the details and nuances, instead helping you get stuck in, right now.

我们如何处理 web 布局正在发生的变化，最前沿的变化是 [CSS Grid](https://www.w3.org/TR/css-grid-1/)。这篇快速入门指南将会跳过细节，让你避免陷入困境，让我们开始。

### Your Browser

### 浏览器

CSS Grid Layout (known to its friends as “Grid”) has come on leaps and bounds in the last year, and as such you’ll find [browser support](http://caniuse.com/#feat=css-grid) for it pretty solid nowadays. 

CSS Grid 布局（有人称它为 “Grid”）去年的发展突飞猛进，因此，现在你会发现浏览器已经有了很好的[支持](http://caniuse.com/#feat=css-grid)。

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27238/image/caniuse-grid.png)

### Setting up a Grid

### 设置 Grid

Grid allows us to arrange elements on a page, according to regions created by guides. 

Grid 允许我们根据网格线在页面上布置元素。

#### Terminology

#### 术语

At their simplest these guides, or *grid lines*, frame horizontal and vertical *grid tracks*. Grid tracks serve as *rows* and *columns*, with *gutters* running between them. Where horizontal and vertical grid tracks intersect, we’re left with *cells*, much like we use with tables. These are all important terms to understand.

最简单就是网格，也称为*网格线*，水平和垂直的*网格轨道*框架。网格轨道被它们之间的*间隙*分成了*行*和*列*。在网格轨道水平和垂直方向交叉的地方我们得到了*单元格*，这有点像 table。这些都是很重要的术语。

In the image below you’ll see a demo grid, showing: 


1. grid lines   
1. columns
1. rows
1. cells

下面的图片中你将会看到一个 demo，其中展示了：

1. 网格线
1. 列
1. 行
1. 单元格

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27238/image/grid-terms-lines-rows-columns-cells-3.svg)
A basic grid, highlighting grid lines, columns, rows, and cells   
基本的 Grid 布局，高亮的是网格线、列、行和单元格

For a graphic layout, it might look more familiar if we use exactly the same grid, but part some tracks to give us gutters between content areas.

图解中的布局，如果我们使用完全相同的网格，看起来有点类似，但是网格中的轨道可以在内容之间设置留白。

1.gutters  

1.间隙

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27238/image/grid-terms-gutters-2.svg)
The same grid, but this time bearing a striking resemblance to the Finnish flag   
同样的网格布局，但是这次和芬兰国旗非常相似

There’s one last term we need to clarify before moving on:

在我们继续之前有最后一个术语必须清楚：

1.grid area

1.网格区域

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27238/image/grid-terms-area-1.svg)
One of the many possible grid areas on our demo grid   
演示中众多网格区域的一部分

A *grid area* is any part of our grid fenced in by four grid lines; it can comprise any number of grid cells.

*网格区域*是有四条网格线围绕出来的任何区域；它可以包含任意多个单元格。

Time to build this grid in the browser! Let’s begin with some markup.

是时候开始在浏览器中实现 grid 布局了！让我们开始吧。

### Grid Markup

### Grid 标记

To recreate the grid above, we need a container element; anything you like:

为了实现上面的 grid 布局，我们需要一个容器；只要你喜欢，任何标记都可以：

```html
<section class="grid-1">
 
</section>
```

In it we’re going to place nine child elements.

在这个容器中我放置了 9 子元素：

```html
<section class="grid-1">
  <div class="item-1">1</div>
  <div class="item-2">2</div>
  <div class="item-3">3</div>
  <div class="item-4">4</div>
  <div class="item-5">5</div>
  <div class="item-6">6</div>
  <div class="item-7">7</div>
  <div class="item-8">8</div>
  <div class="item-9">9</div>
</section>
```

Fork [this starter pen](http://codepen.io/tutsplus/pen/YGWpVJ/) if you want to follow along. I’ve added some basic styles to visually differentiate each grid item.

如果，你像看效果，可以看 [codepen 的 demo](http://codepen.io/tutsplus/pen/YGWpVJ/) 。我给每个元素都添加了一些基本的样式，为了让它们之间看起有区别。

### Grid Rules

### Grid 规则

Firstly we need to declare that our container element is a grid using a new value for the ```display``` property:

首先，我们需要用 ```display``` 一个新的属性把容器定义为 grid:

```css
.grid-1 {
  display: grid;
}
```

Right, that was easy. Next we need to define our grid, by stating how many grid tracks it will have, both horizontally and vertically. We do that with the ```grid-template-columns```  and ```grid-template-rows``` properties:

对，就这么简单。接下来我们需要定义我们的 grid，为了决定在水平和垂直方向分别有几个网格轨道，我们可以使用 ```grid-template-columns``` 和 ```grid-template-rows``` 两个属性：

```css
.grid-1 {
  display: grid;
  grid-template-columns: 150px 150px 150px;
  grid-template-rows: auto auto auto;
}
```

You’ll notice three values for each. The values for ```grid-template-columns``` state that “all three columns should be 150px wide”. The three values for ```grid-template-rows``` state something similar. Each one would actually be ```auto``` by default, taking height from the content, but we’ve stated them here in order to clearly visualise what’s going on.

你会注意到以上两个属性分别有三个值。```grid-template-columns``` 的值代表“三列都是 150px 宽”。```grid-template-rows``` 的值有点类似。每个值默认情况下都是 ```auto```，高度根据内容来计算，但是我们还是明确的说明了，为了更清楚的知道会发生什么。

So what do we have now?

现在你想看看是什么效果吗？

[Codepen Demo](https://codepen.io/tutsplus/pen/WGxorA)

Each of our items has been automatically assigned a grid area in chronological order. It’s not bad, but we’re missing gutters. Time to fix that.

每个元素都自动的按照顺序分配空间。这样很好，但是，我们没有留白。现在让我们解决它。

### Mind the Gap

### 关注 Gap

Grid comes with a purpose-made solution for declaring gutter measurements. We can use ```grid-column-gap``` and ```grid-row-gap```, or the shorthand ```grid-gap``` property.

Grid 有专有的属性解决留白大小的问题。我们可以用属性 ```grid-column-gap``` 和 ```grid-row-gap```，简称 ```grid-gap```。

Let’s add a fixed gutter of 20px to our ```.grid-1``` element.

让我们给 ```.grid-1``` 元素添加一个固定的 20px 的留白。

```css
.grid-1 {
  display: grid;
  grid-template-columns: 150px 150px 150px;
  grid-template-rows: auto auto auto;
  grid-gap: 20px;
}
```

And now, we’re left with a nice, neat grid:

现在，我们得到一个整齐的网格布局：

[Codepen Demo](https://codepen.io/tutsplus/pen/zKqLKy)

### Conclusion

### 总结

That’s it, you’re up and running with Grid! Let’s recap the four essential steps:


1. Create a container element, and declare it ```display: grid```;
1. Use that same container to define the grid tracks using the ```grid-template-columns``` and ```grid-template-rows``` properties.
1. Place child elements within the container.
1. Specify the gutter sizes using the ```grid-gap``` properties.

就这些，你现在可以使用 Grid 了！让我们来概括下四个必要的步骤：

1. 创建一个容器并声明 ```display: grid```;
1. 在同一个容器上使用属性 ```grid-template-columns``` 和 ```grid-template-rows``` 声明网格轨道。
1. 在容器中放置子元素。
1. 使用属性 ```grid-gap``` 设置留白的大小。

In [the next part of this series](https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259) we’ll take a deeper look at Grid’s syntax, explore flexible layouts, the *fr* unit, the ```repeat()``` function, and take our simple grid much further. See you there!

在[这个系列接下来的章节中](https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259)我们将深入探讨 Grid 的语法，探索弹性布局：*fr*、```repeat()``` 方法并让我们的网格布局更进一步。到时见！



