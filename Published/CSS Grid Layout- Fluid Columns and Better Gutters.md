## CSS Grid Layout: Fluid Columns and Better Gutters
## CSS Grid 布局：Fluid Columns and Better Gutters

> 原文地址: <https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In this tutorial we’re going to take the grid from our previous tutorial and use it as a playground to look further into Grid. We’ll improve the way we define our gutters, explore flexible layouts, the *fr* unit, and introduce the ```repeat()``` function.


在这篇教程中我们将继续探讨上篇文章提到的 Grid，并用一个示例进一步了解 Grid。我们会改善定义间隙的方式，探讨弹性布局，*fr* 单位 和介绍 ```repeat()``` 方法。

### Flexible Units

### 弹性单位

The whole point of Grid is to enable us to properly control layout on the web, so let’s make our static grid fluid before we go any further. If you recall, we were defining our grid with static pixel measurements:

Grid 的重点是让我们正确的控制 web 布局，在进一步了解弹性布局之前，先让我们看看我们的固定布局。回想一下，我们曾经用固定像素定义的 Grid 布局：

```css
grid-template-columns: 150px 150px 150px;
grid-template-rows: auto auto auto;
grid-gap: 20px;
```

It’s quite possible to use other units here too, such as ems, or rems for example. Or even [more unusual units like vh and vmin](https://webdesign.tutsplus.com/articles/7-css-units-you-might-not-know-about--cms-22573). 

这里用其它单位也是可以的，比如：em、rem。还有[更多不常用的单位，比如：vh 和 vmin](https://webdesign.tutsplus.com/articles/7-css-units-you-might-not-know-about--cms-22573)。

In this case we’re going to change our pixel units for percentages. 

这个示例中我们会用百分比替换固定像素单位。

```css
grid-template-columns: 33.33% 33.33% 33.33%;
```

Hmm, that’s a bit messy, but it sort of does the job. You’ll notice that the column widths now add up to 100%; our gutters will be subtracted from them automatically. ```grid-gap``` will accept fixed or flexible units, which means we can perfectly combine fluid columns and fixed gutters without any complex calculations on our part.

嗯，有点乱，但是这可以正常工作。现在，你会注意到每一行的列宽加起来正好是100%；~~间隙将会被自动的扣减~~(**译者住：根据 [DEMO](http://output.jsbin.com/mumoxaf) 发现计算每一列的宽度时，并不会扣减留白的大小，也就说：留白 + 每一行总列宽会大于100%**)。```grid-gap``` 可以接受固定和非固定单位，也就是说：不需经过复杂的计算我们可以很好的融合不固定列宽和固定留白。

#### Repeat

Let’s write this is a neater way, using the ```repeat()``` function:

用 ```repeat()``` 方法，我们可以写出更整洁的代码：

```css
grid-template-columns: repeat(3, 33.33%);
```

This states “*repeat 33.33% three times*” giving us three columns. And we don’t even *need* the ```grid-template-rows``` declaration because the ```auto``` value is assigned as default anyway. 

这句话代表：“*重复三次 33.33%*” 赋值给 ```grid-template-columns```。我们甚至*不需要*声明 ```grid-template-rows```，因为它的默认值就是 ```auto```。

**Note**: when columns are defined using % values, they’ll use exactly those values and add any ```grid-gap``` on top. You’ll notice the grid above has been pushed to the right because it’s now 99.99% wide *plus* the grid-gaps.

**注意**：当列宽的单位用 % 时，每一列都会根据父级精确的计算，然后总宽会在这个基础上加上 ```grid-gap```。你应该会注意到上面的 grid 布局右边有部分已经超出，这是因为这一行的总宽是 99.99% *加上* grid-gap。

### The fr Unit

### fr 单位

One final improvement can be made to our simple grid, and it will solve the width problem we just mentioned; we’re going to introduce the *fr*, or *fraction* unit. A single fr unit describes “*one piece of however many pieces we’re splitting this into*”. For example, we could declare our columns using:

为了让我们的 grid 布局更加简洁我们需要进一步的改进，这也会解决刚才提到的总宽度的问题；我们会介绍 *fr* 或者*分数*单位。1 fr 代表“*整个空间被平均分配后的其中之一*”。比如：我们可以这么声明 columns：

```css
grid-template-columns: 1fr 1fr 1fr;
```

Here there’s a total of three fr units, so each column would be one third wide. Here’s another example:

这里一共有 3 个 fr 单位，因此，每一列都是 1/3 。这里是另外一个示例：

```css
grid-template-columns: 2fr 1fr 1fr
```

Now there’s a total of four fr units, so the first column would take up half the available width, with the other two columns each taking a quarter.

现在，一共 4 个 fr 单位，第一列是 1/2 宽，其余两列分别是 1/4 宽。

These units are really powerful, especially in combination with other units of measurement:

这个单位非常有用，特别是和其它计量单位结合使用的时候：

```css
grid-template-columns: 300px 1fr 3fr 20%;
```

Here we’ve declared four columns: 

- the first is fixed at 300px wide
- the last is a flexible 20% of the grid container element wide
- then the fr units are calculated, also taking gutters into account, leaving the second column with one piece of the remaining space
- and the third with three pieces.

以上，我们声明了 4 列：

- 第一列固定宽 300px
- 最后一列是父级网格容器的 20%
- 然后，每一个 fr 单位都会根据去除间隙后剩余空间计算，第二列只是其中一份
- 第三列就是剩余空间的 3/4

It looks like this, perfectly highlighting auto-placement as our nine items shift to fill the gaps:

看起来就像这样，9 个元素完美的自动排列，它们之间也会有留白：

[DEMO](https://codepen.io/tutsplus/pen/yaavEO)

But back to our original grid. Let’s replace the clumsy and inaccurate 33.33% value with 1fr: 

回到原来的示例中。让我们用 1fr 代替笨拙又不正确的 33.33%：

```css
grid-template-columns: repeat(3, 1fr);
```

Finished pen:

最总结果：

[DEMO](https://codepen.io/tutsplus/pen/zKKRdN)

### Conclusion

### 总结

So, to recap:

1. Grid will accept flexible units in combination with fixed units of measurements.
1. The ```repeat()``` function will save us time and keep our stylesheets maintainable.
1. The *fr*, or *fraction* unit is a very powerful way of describing grid templates.

概括一下：

1. Grid 中可以混合使用弹性和固定计量单位。
1. ```repeat()``` 方法可以节省我们的时间，也会让样式表更易维护。
1. *fr* 或者 *fraction* 单位在定义声明 grid templates 时非常有用。

We’ve come a long way in just two tutorials, but you’re now the proud owner of a very neat and concise grid! In the next tutorial we’ll explore grid areas, taking a look at the ```span``` keyword and some practical layouts.

这两篇教程中我们走了很长一段路，你也学会了简洁有用的 grid 布局，应该为自己而自豪！接下来的教程中，我们会探讨 grid areas，看看关键字 ``span``` 和其它有用的布局。

#### Useful Resources

- [The ```<fraction>``` type and *fr* unit](https://www.w3.org/TR/2011/WD-css3-values-20110906/#proportions) on W3C   
- Again, follow [@rachelandrew](https://twitter.com/rachelandrew)

#### 更多有用的资源

- W3C 上相关 [```<fraction>``` 和 *fr* 单位的介绍](https://www.w3.org/TR/2011/WD-css3-values-20110906/#proportions)
- 在 twitter 上关注 [@rachelandrew](https://twitter.com/rachelandrew)