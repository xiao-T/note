## CSS Grid Layout: Using Grid Areas
## CSS 网格布局：使用网格区间

> 原文地址: <https://webdesign.tutsplus.com/tutorials/css-grid-layout-using-grid-areas--cms-27264/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

One thing we’ve mentioned, but have yet to cover properly in this series is *grid areas*. So far our grid items have each been contained within a single grid cell, but we can achieve more useful layouts by breaking beyond those boundaries. Let’s take a look!

*网格区间*我们已经提起过，但是一直还没有真正的去介绍过它。目前为止，每一个网格元素都是一个单独的网格单元，但是我们可以打破元素之间的边界实现更有用的布局。让我们来看一下！

### Defining Grid Areas

### 定义网格区间

Here’s the grid we’ve been working on: nine grid items automatically placed within three equal columns, three equal rows, split by gutters of 20px.

我们一直研究的网格布局：9 个元格自动的拍成 3 行，3 列，元素之间有 20px 的留白。

[DEMO](https://codepen.io/tutsplus/pen/zKKRdN)

Currently our items only have color styles, but let’s return to what we learned in the first tutorial and add some ```grid-column``` and ```grid-row``` rules, this time with something extra:

当前每个网格元素只是有些颜色，这次会添加一些额外的东西，我们回到第一篇教程的示例中，然后添加一些 ```grid-column``` 和 ```grid-row```：

```css
.item-1 {
  background: #b03532;
  grid-column: 1 / 3;
  grid-row: 1;
}
```

In this shorthand ```grid-column``` statement we’re effectively using ```grid-column-start``` and ```grid-column-end```, telling the item to start at grid line 1 and end at grid line 3.

```grid-column``` 是 ```grid-column-start``` 和 ```grid-column-end``` 的简写，代表着当前网格区域从第一列开始到第三列结束。

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27264/image/grid-lines.svg)

Here’s what that gives us; the first item now spreads across two cells, pushing the other items right and down according to Grid’s auto-placement algorithm.

我们去看看具体的实现；第一个元素占了两个网格单元格，导致其它元素向右、向下的自动排列。

[DEMO](https://codepen.io/tutsplus/pen/RGoPbL)

The same can be done with rows, which would give us a much larger area in the top left of our grid.

对于行我也可以同样的处理，这样右上角的区域就会更大。

```css
.item-1 {
  background: #b03532;
  grid-column: 1 / 3;
  grid-row: 1 / 3;
}
```

#### Spanning Cells

#### 横跨单元格

Using what is perhaps an easier syntax we can switch ```grid-column-end``` for the ```span``` keyword. With ```span``` we aren’t tied to specifying where the area ends, instead defining how many tracks the item should spread across:

```grid-column-end``` 还有一种更简单的语法就是使用关键字：```span```。使用 ```span``` 我们不需要指定区域的结束位置，只需定义好跨越几个网格轨道：

```css
.item-1 {
  background: #b03532;
  grid-column: 1 / span 2;
  grid-row: 1 / span 2;
}
```

This gives us the same end result, but if we were to change where we want the item to start we would no longer be obliged to change the end. 

这么处理我们也会得到同样的效果，但是，如果我们想改变网格元素开始的位置时，结束位置这种情况下就不需要跟着改变了。

In the following demo you can see we’ve emptied the layout by removing four of the items. We’ve declared positioning on two of our items: the first spans two columns and two rows, whilst the fourth starts on column 3, row 2, and spans downward across two tracks:

看下面的 demo 你会发现我们清除了 4 个元素。我们也为其中的两个元素重新定义了位置：第一元素，占据两行两列，同时，第四个元素从第三列、第二行开始向下占用两行：

[DEMO](https://codepen.io/tutsplus/pen/gwLpEE)

The remaining items fill the available space automatically. This highlights perfectly how a grid layout doesn’t have to reflect the source order of the elements.

剩余的元素在可用空间里自动分布。这完美的展示了网格布局是不必按照元素原来顺序排列布局的。

**Note**: there are many situations where the source order absolutely *should* reflect the presentation–don’t forget about accessibility. 

**注意**：大多情况下元素的原顺序*应该*和具体显示顺序保持一致 - 不要忘记可访问性。

### Named Areas

### 命名空间

Using the numbering methods we’ve described so far works just fine, but [Grid Template Areas](https://www.w3.org/TR/css-grid-1/#grid-template-areas-property) can make defining layouts even more intuitive.

目前为止，使用数字编号的方式可以很好的工作，但是，[网格模版区域](https://www.w3.org/TR/css-grid-1/#grid-template-areas-property)可以让定义网格更加直观。

Specifically, they allow us to name areas on the grid. With those areas named, we can reference them (instead of line numbers) to position our items. Let’s stick to our current demo for the moment and use it to make ourselves a rough page layout comprising:

- header
- main content
- sidebar
- footer

尤其是，我们可以给网格区域命名。使用区域名称，我们可以引用定位元素（代替行号）。继续来看我们的 demo，用它来实现一个粗糙的页面布局，主要包括：

- 头部
- 主内容
- 侧边
- 底部

We define these areas on our grid container, almost as though we’re drawing them out:

我们在网格容器上定义区域名称，就像我们绘制网格一样：

```css
.grid-1 {
    /* ..existing styles */
     
    grid-template-areas:    "header header header"
                            "main main sidebar"
                            "footer footer footer";
}
```

#### Positioning the Items

#### 定位元素

Now we turn our attention to the items, ditching the ```grid-column``` and ```grid-row``` rules in favor of ```grid-area```:

现在让我们关注下各个元素，放弃 ```grid-column``` 和 ```grid-row``` 用 ```grid-area``` 代替：

```css
.item-1 {
  background: #b03532;
  grid-area: header;
}
.item-2 {
  background: #33a8a5;
  grid-area: main;
}
.item-3 {
  background: #30997a;
  grid-area: sidebar;
}
.item-4 {
  background: #6a478f;
  grid-area: footer;
}
```

Our first item is slotted into the header, spanning across all three header columns. Our second item is assigned the main content area, the third becomes our sidebar, and the fourth our footer. And these needn’t follow the source order either–```.item-4``` could just as easily be positioned in the header area.

第一个元素放置在头部，跨越 3 列。主内容区域分配给第二个元素，第三个元素就变成了侧边栏，第四个元素分配了整个底部。定位这些元素我们并不需要按照源顺序，比如：```.item-4``` 也可以被放置在头部区域。

[DEMO](https://codepen.io/tutsplus/pen/bwBVYp)

As you can see, this makes laying out a page much easier. In fact, while we’re in the mood for visually representing our grids, why not go even further and use emojis?!

正如你看到的，这使得实现一个页面布局很容易。事实上，虽然我们让网格布局实现了可视化，为什么不更进一步使用 emojis 呢？！

[DEMO](https://codepen.io/tutsplus/pen/wzorjy)

### Nesting Grid Areas

### 网格嵌套

A given web page will contain all kinds of nested components, so let’s see how that works with Grid.

一个特定的页面会包含多种嵌套组件，我们来看看网格是如何处理嵌套的。

When we declare a grid container ```display: grid```; only its direct descendants become *grid items*. Content we add to those child elements will be completely unaffected by Grid unless we specifically say otherwise. 

当我们声明一个网格容器 ```display: grid```；只是它的子元素会变成 *网格元素*。除非我们有特别声明，添加到这些元素中的内容将完全不受 Grid 的影响。

In our example, we’re going to add ```.item-5``` , ```.item-6```, and ```.item-7``` back into the markup, nesting them in ```.item-2```. 

在我们的演示中，我们会在 ```.item-2``` 中嵌套 ```.item-5``` , ```.item-6```, 和 ```.item-7``` 几个标签。

```html
<section class="grid-1">
  <div class="item-1">1</div>
   
  <div class="item-2">
    <div class="item-5">5</div>
    <div class="item-6">6</div>
    <div class="item-7">7</div>
  </div>
   
  <div class="item-3">3</div>
  <div class="item-4">4</div>
</section>
```

So now we need to declare our ```.item-2``` also a grid container, setting up its grid with two columns and two rows.

现在，我需要把 ```.item-2``` 定义成一个网格容器，设置成两行两列。

```css
display: grid;
 
grid-template-columns: 1fr 30%;
grid-template-rows: auto auto;
grid-gap: 20px;
 
grid-template-areas: "header header"
                     "article sidebar";
```

We can use the names “header”, “article”, and “sidebar” again here; there’s no confusion, because everything stays in context. These grid areas only apply to the grid within ```.item-2```.

我们再一次用 “header”, “article”, 和 “sidebar” 来命名；这不会混乱，因为上下文不同。这些只会对 ```.item-2``` 有效。

[DEMO](https://codepen.io/tutsplus/pen/yaVZWA)

### Conclusion

### 总结

To sum up what we’ve been talking about:

1. ```grid-column``` offers us a shorthand way of defining where an item starts and ends.
1. We can also use the ```span``` keyword to make our rules more flexible.
1. ```grid-template-areas``` give us the power to name our grid areas (even using emojis if the mood takes us).
1. We can also nest grids by declaring grid items as ```display: grid```; and following the same process.

我们来总结一下：

1. ```grid-column``` 是定义列头和列尾简写。
1. 我们可以是使用关键字 ```span``` 让 CSS 更加灵活。
1. ```grid-template-areas``` 可以给网格区域命名（emoji 也可以使用）。
1. 我们可以通过定义子元素为 ```display: grid``` 实现网格的嵌套；遵守同样的网格布局规则。

Once again we’ve learned some useful aspects of the CSS Grid Layout spec, and we’re getting closer and closer to real world use cases! [In the next tutorial](https://webdesign.tutsplus.com/tutorials/css-grid-layout-going-responsive--cms-27270) we’ll look at some more complex layouts and see how responsive design fits into the equation.

我们又学到了 CSS Grid 布局中几个有用的方面，并且我们的示例越来越接近真是场景！[下篇教程中](https://webdesign.tutsplus.com/tutorials/css-grid-layout-going-responsive--cms-27270)，我们会看到更加复杂的布局和如何实现响应布局。

#### Useful Resources

#### 更多有用的资源

- Have I mentioned this already? Follow [@rachelandrew](https://twitter.com/rachelandrew)

- 我已经提到过了！关注 [@rachelandrew](https://twitter.com/rachelandrew)

