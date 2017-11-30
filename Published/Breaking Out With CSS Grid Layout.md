## Breaking Out With CSS Grid Layout
## 利用 CSS Grid 打破布局的边界

> 原文地址: <https://cloudfour.com/thinks/breaking-out-with-css-grid-layout/> 
**本文版权归原作者所有，翻译仅用于学习。**

---------

> A while back I shared [a simple technique](https://cloudfour.com/thinks/breaking-out-with-viewport-units-and-calc/) for allowing certain elements to fill the full viewport width from within a fixed-width container:

早些时候，我分享了[一项简单的技术](https://cloudfour.com/thinks/breaking-out-with-viewport-units-and-calc/)，允许某些元素即便在固定宽度的容器中，它还是会铺满全屏。

```css
.u-containProse {
  max-width: 40em;
  margin-left: auto;
  margin-right: auto;
}

.u-release {
  margin-left: calc(-50vw + 50%);
  margin-right: calc(-50vw + 50%);
}
```

> As concise as this technique was, it had [one big, dumb caveat](https://cloudfour.com/thinks/breaking-out-with-viewport-units-and-calc/#one-big-dumb-caveat): On some platforms, you’d see a horizontal scrollbar unless you set ```overflow-x``` to ```hidden``` on the ```body``` element.

这项技术非常简单，但是它有个隐藏的bug：在一些平台上，你将会看到有一个水平滚动条，除非你在 ```body``` 上设置 ```overflow-x: hidden```。

> It’s now a year later and every modern browser supports (or [will soon support](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/suggestions/6514853-update-css-grid)) [CSS Grid Layout](http://jensimmons.com/post/feb-27-2017/learn-css-grid) (hooray!!). I thought it might be fun to reexamine this problem with a fancy set of modern layout tools at our disposal.

一年以后的现在，每个现代浏览器都已经支持（或者[准备支持](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/suggestions/6514853-update-css-grid)）[CSS Grid](http://jensimmons.com/post/feb-27-2017/learn-css-grid)（万岁！）。我想用现在的布局技术重新审视这个问题会很有趣。

### Our Goal

### 我们的目的

> In [my last article about Grid](https://cloudfour.com/thinks/first-css-grid-layout/), I mentioned how important it was to approach layout problems with fresh eyes so you aren’t misguided by years of workarounds and CSS muscle memory. So let’s review what we hope to accomplish from a *design* standpoint:

在[我上篇有关 Grid 的文章中](https://cloudfour.com/thinks/first-css-grid-layout/)，我提到用新的思路解决布局问题是多么的重要，所以不要被思维定势影响你。让我们回顾一下我们希望通过设计的角度考虑问题：

> - We want to limit the width of prose content [to promote readability](http://practicaltypography.com/line-length.html).
> - But sometimes, the reader may benefit from an element being full-width. For example, a nice photograph or a detailed chart.

- 我们希望文章的内容有一个限定的宽度，这会[有利于阅读](http://practicaltypography.com/line-length.html)。
- 但是有时候，读者会从一个全屏的元素上得到收益。例如，一个漂亮的图片或者详细的图表。

![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-sketch-r3.jpg)

> If we sketch this out, we can see that what we’re really after isn’t so much a fixed-width container with margins… it’s more of a three-column layout, with a main column flanked by two gutters. Most content will fill only the middle portion, but our super-fancy visual content will span all three.

如果我们把我们的想法勾画出来，我们看到，这并不是一个真正固定宽度的布局，它更像是一个三列布局，中间是主要内容，两边是留白。主要的内容将在中间显示，但是我们的视觉修饰的部分将布满全屏。

> With that in mind, it’s time to write some CSS!

考虑到这一点，是时候开始写 CSS 了！

### Column Like You Sees ‘Em

### 如你所见

> Let’s start by styling our containing element, which will wrap the article content:

让我们开始写容器元素的样式，它将包含主要文章内容：

```css.Prose {
  display: grid;
  grid-template-columns: 
    [full-start] minmax(1em, 1fr) 
    [main-start] minmax(0, 40em) [main-end]
    minmax(1em, 1fr) [full-end];
}
```

> Here we’ve defined three columns. The first and last are our gutters, with a minimum size of ```1em``` but the ability to stretch to fill one unit of available space. Between them is our main content column, which can stretch to a maximum width of ```40em```.

我们定义了三列。第一和最后一个就是我们的留白，最小宽度是 ```1em```，但是，如果有更多的可用空间它们是可以伸缩的。在它们之间就是主要内容那一列，它的最大宽度是 ```40em```。

![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-lines.svg)

> We’ve also assigned names (in brackets) to [the dividing lines between columns](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Layout_using_Named_Grid_Lines). These are entirely optional, but they’ll make the rest of our styles easier to read, write and maintain.

我们给[每一列之间分割线定义名字](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Layout_using_Named_Grid_Lines)（括号内的内容）。这都是可选的，但是，这会让你的代码更容易读、写和维护。

> Next, we instruct all child elements of ```Prose``` to neatly occupy our ```main``` column:

接下来，我们让 ```Prose``` 所有的元素都占据 ```main``` 那一列。

```css
.Prose > * {
  grid-column: main;
}
```

> And finally, we make a class for elements we’d like to span the full container width:

最后，我们给那些我们想让它占据全屏元素设置一个类：

```css
.Prose-splash {
  grid-column: full;
}
```

> And that’s it! Here’s the end result, no ```overflow-x``` or negative margins required:

就是这样！这是最后结果，没有 ```overflow-x```，没有负 ```margin```：

[![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-breakout-demo-1-r2-opt.gif)](https://codepen.io/tylersticka/pen/bWvaxP/left?editors=1100)

> But that’s not all…

但，这不是全部

### Cool New Tricks

### 更酷更新的技巧

> Because full-width elements are based on their container and not the viewport, this technique works great for asymmetric layouts:

因为全宽元素是基于它的容器，而不是基于可视窗口，所以这项技术对非对称布局非常有用：

[![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-breakout-demo-2-r2.png)](https://codepen.io/tylersticka/pen/wdmymG?editors=1100)

> This also means full-width elements may reliably align with content above and below, which provides some fun opportunities for styling special elements like block-quotes or code blocks:

也就是说全宽元素可以跟上下的内容完全对其，这对于一些特定的元素，比如：引用内容和代码宽提供更有趣的可能性：

[![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-breakout-demo-3-r2.png)](https://codepen.io/tylersticka/pen/KmoQYy/left?editors=1100)

> We can also consider utilizing the gutter space in novel ways that previously required much more hackery:

我们也可以考虑用新颖的方式利用留白部分，这在以前需要更多的 css hack 技术：

[![](https://29comwzoq712ml5vj5gf479x-wpengine.netdna-ssl.com/wp-content/uploads/2017/05/grid-breakout-demo-4-r2.jpg)](https://codepen.io/tylersticka/pen/PmRRqb/left?editors=1100)

### Inevitable Downsides

### 缺点

> While this technique has some really promising qualities, there are also some drawbacks to consider:

尽管这项技术非常好，但是还是有一些缺点需要考虑的：

- You can’t ```float``` immediate children of an element with ```display: grid```.

- 父级是 ```display: grid``` 的子元素不能使用 ```float```。

- Grid is being adopted [very rapidly](http://caniuse.com/#feat=css-grid), but it’s still not as well supported as what I shared last year.

- 虽然 Grid [正在接纳](http://caniuse.com/#feat=css-grid)，但是在去年我分享的时候，它的支持情况并不是很好。

- Many aforementioned benefits also apply to [using *:not() selectors instead](http://daverupert.com/2017/03/full-bleed-with-not/).

- 上面的很多好处也适用 [ *:not() 选择器](http://daverupert.com/2017/03/full-bleed-with-not/)。

> Those caveats may be deal-breakers for many, but I predict these sorts of techniques will grow more attractive as CSS Grid Layout becomes more and more prevalent. It seems only natural for our content to extend the composition of our interfaces.

对于这些警告很多人会比较在意，但是，我预测 CSS Grid 这些技术会越来越流行。

> Special thanks to [Erik Jung](https://cloudfour.com/is/erik/) for his demo assistance and technical review of this article.

特别感谢 [Erik Jung](https://cloudfour.com/is/erik/) 提供的演示 Demo 和对这篇文章的技术审查。
