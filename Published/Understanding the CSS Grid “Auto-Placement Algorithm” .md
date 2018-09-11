## Understanding the CSS Grid “Auto-Placement Algorithm”
## 理解 CSS Grid 的 “自动排列算法”

> 原文地址: <https://webdesign.tutsplus.com/tutorials/understanding-the-css-grid-auto-placement-algorithm--cms-27563/>    
**本文版权归原作者所有，翻译仅用于学习。**

---------

In one of our earlier introduction tutorials to CSS Grid we looked at [fluid columns and better gutters](https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259). We learned that it isn’t necessary to specify exactly where we want to position our grid items; if we declare our grid’s properties, Grid will slot our items in according to its auto-placement algorithm.

在早期的教程中我们讨论过[fluid columns 和 better gutters](https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259)。我们知道在定位排列元素的时候并不需要严格按照文档顺序逻辑排列；如果，我们声明了 Grid 的属性，它的元素安会根据自己的算法自动排列。

In this tutorial we’ll take a look at how that algorithm goes about its work and how we can influence it.

在这篇教程中，我们会探讨自动排列算饭是如何工作的，我们如何控制排列算法。

#### Setup

#### 设置

If your browser isn’t setup to support Grid you’ll need to sort that out to follow along. Read this:

如果，你的浏览器未开启对 Grid 的支持，你需要开启它。看看这个：

[DEMO](https://webdesign.tutsplus.com/tutorials/css-grid-layout-quick-start-guide--cms-27238)

### It Started With a Grid

### 从 Grid 开始

Here’s a demo grid to kick things off; it’s a container which we’ve declared to be ```display: grid```; and holds eighteen child elements. We’ve stated that it should have five columns, each of equal width, at least the same amount of rows, and a narrow gutter all round of 2px.

从一个 Grid 的演示开始；这个容器我们声明了 ```display: grid```；并且它有 18 子元素。容器有 5 列并且是等宽的，同样也有 5 行，每个元素之间还有 2px 的留白。

```css
grid-template-columns: repeat(5, 1fr);
grid-template-rows: repeat(5, 1fr);
grid-gap: 2px;
```

[DEMO](https://codepen.io/tutsplus/pen/VKorxp)

So far so good, and you can see that Grid has taken our eighteen items and pushed them in, working from the top left, moving across to the right, then moving down row by row. This behaviour is much like the way floats work.

目前为止没什么问题，你可以看到 Grid 容器中有 18 个元素，它们从左上角开始，向右、向下自动排列。这种行为有点类似 float。

**Note**: the left-to-right behaviour would be reversed if we were dealing with a ```direction: RTL;``` layout.

**注意**：如果，我们设置了 ```direction: RTL;``` 就会变成自右向左。

#### Throwing a Spanner in the Works

#### 从中作梗

That’s all nice and neat, but let’s see what happens when our items don’t fit so perfectly. To ```.item-7``` we’ll add some rules to make it bigger by spanning two columns and two rows:

非常漂亮、整齐，但是，假如有个元素特别处理后会发生什么呢？给 ```.item-7``` 添加一些规则让它变大占用两行两列。

```css
.item-7 {
  background: #e03f3f;
  grid-column: span 2;
  grid-row: span 2;
}
```

How does that look now?

让我们看看它如何工作的？

[DEMO](https://codepen.io/tutsplus/pen/XjvVgp)

Not too bad! ```.item-7``` fills up more space, so ```.item-8``` and ```.item-9``` are positioned further over. ```.item-10``` then looks to see if there’s a vacant space next to ```.item-9```, and when it sees there isn’t it moves *down* a row and starts again on the far left. The other items continue to tuck in in the same way. 

不算太糟糕！```.item-7``` 占用了更多的空间，因此，```.item-8``` 和 ```.item-9``` 被挤压到后面了。这时 ```.item-10``` 会先看一下 ```.item-9``` 后面是否有足够的空间，如果没有它就会向*下*移动到另外一行从左边开始排列。其它的元素也遵循同样的规则。

But wait, what if we also make ```.item-9``` a little overweight?

但是等等，如果我们让 ```.item-9``` 也变大后会怎么样呢？

[DEMO](https://codepen.io/tutsplus/pen/ORKzzG)

Now it gets interesting; ```.item-9``` no longer fits into the column at the end so it’s pushed down to the next row. As it can’t fit in anything beyond ```.item-7``` it stays put. ```.item-10```, you might imagine, would tuck itself in underneath ```.item-6``` again, but, if you remember, it searches for a vacant column, then failing that it moves *down a row* and shunts across to the left again. This is an important concept to grasp.

现在有趣的事情发生了；```.item-9``` 不再是排在列的最后了，它被挤压到下面一行了。因为在 ```.item-7``` 之后没有足够的空间了。对于 ```.item-10``` 你想应该还是会在 ```.item-6``` 下面，但是，你要记住，它会搜索可用的空间，这时就会移动到*下一列*，再次回到了左边。这是一个非常重要的概念。

### Say “Hello” to grid-auto-flow

### 介绍 grid-auto-flow

If you look at the previous demo you’ll see that ```.item-18```, upon failing to find space next to ```.item-17```, has moved down a row. We only actually defined five rows, but Grid has assumed we’ll want another row tacking on. This is owing to ```grid-auto-flow```, which belongs to the grid element, and whose default value is ```row```. We can change this value to ```column``` if we want, which would totally alter the way our grid looks:

如果，你看上一个 demo 会发现 ```.item-18``` 在 ```.item-17``` 后面没有找到足够的空间，移动到了下一行。我们仅仅定义了五行，但是 Grid 会认为需要更多的空间。这是由 Grid 容器上属性 ```grid-auto-flow``` 决定的，它的默认值是 ```row```，我们可以把它的值设置成 ```column```，这样整个 Grid 看起来就变了：

[DEMO](https://codepen.io/tutsplus/pen/QKeQWE)


This looks *sort of* similar, but you’ll notice that our items have slotted in top left, then moved down to fill each row, then moved across to the second column and so on. So now when an item is too big for its boots, the following item searches for the next vacant row space, then failing that shifts over to the next column.

看起来和*排序*类型，但是，你会注意到所有的元素都是从左上角开始排列的，然后向下填满每一个行，在移动到第二列，以此类推。因此，假如一个元素太大，那么它会向下一行搜索空间，如果下一行没有足够的空间，那么它就会占用下一列的空间。

### Dense Makes Sense

### 体验 Dense

We can add another keyword to our ```grid-auto-flow``` property, and it’s possibly my favourite CSS keyword to date: ```dense```. Its default counterpart is ```sparse``` (my second favourite). Let’s do the following:

我们可以在属性 ```grid-auto-flow``` 中添加一个我最喜欢的 CSS 关键字：```dense```。它的默认值是 ```sparse```（我也很喜欢）。让我们看看会发生什么：

```css
grid-auto-flow: row dense;
```

**Note**: we don’t need to include ```row``` here, it’s implied, but this highlights how the syntax works.

**注意**：我们没有必要设置 ```row```，它是默认的，但是，为了演示语法我这里也写了。

[DEMO](https://codepen.io/tutsplus/pen/ORKQmM)


Now our friend ```.item-10```, upon finding that there’s no space next to ```.item-9```, first checks what’s *above* before moving to another row. 

现在，元素 ```.item-10``` 发现 ```.item-9``` 后面没有足够的空间，首先，它会先检查一下*上面*的空间，然后才会移动到下一行。

Thanks to this change in the placement algorithm our items are now densely packed, which gives us a more efficiently filled grid. This does mean, however, that the visual layout doesn’t necessarily reflect the document source order, which may not always be helpful to the user.

多亏了这个改变，我们的元素在排列的时候更加紧凑，让我们的网格利用率更高。也就是说，视觉布局并不一定严格遵循文档流的顺序，当然，这对用户来说不一定就是好的。

### Conclusion

### 总结

Let’s recap:


1. If we haven’t specifically defined an item’s location, Grid’s auto-placement algorithm will place it in the next available (and large enough) slot.
1. Where there’s no available slot in the current row, it will begin searching the following row, even if that leaves gaps.
1. We can switch this search order by altering ```grid-auto-flow``` from ```row``` to ```column```.
1. ```grid-auto-flow``` accepts a keyword to describe the “packing” approach. By default this value is ```sparse```, but we can alter this to ```dense``` which attempts to fill in all available gaps.

让我们概括一下

1. 如果，我们特别的声明某个元素，Grid 元素会自动的排列在下一个可用空间（足够大）。
1. 如果当前行没有足够空间，它会检索下一行，并且会把留白排除在外。
1. 我们可以通过设置 ```grid-auto-flow``` ```row``` 和 ```column``` 来切换不同的检索顺序。
1. ```grid-auto-flow``` 可以通过关键字来设置”包裹“的行为。默认值是 ```sparse```，但是，我们可以把值设置成 ```dense``` 这样元素就会尝试填充所有的有效空间。

#### Useful Resources

- [grid-auto-flow](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-auto-flow) on MDN
- [The grid-auto-flow property](https://www.w3.org/TR/css-grid/#grid-auto-flow-property) specification

#### 更多有用资源

- MDN 上的 [grid-auto-flow](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-auto-flow)
- [grid-auto-flow property](https://www.w3.org/TR/css-grid/#grid-auto-flow-property) 规范
　