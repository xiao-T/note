## CSS Grid Layout: Going Responsive With auto-fill and minmax()
## CSS 网格布局：使用 auto-fill 和 minmax() 实现响应布局

> 原文地址: <https://webdesign.tutsplus.com/tutorials/css-grid-layout-going-responsive--cms-27270/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

Throughout this series we’ve become familiar with Grid syntax, learned about some efficient ways of laying out elements on a page, and said goodbye to some old habits. In this tutorial we’re going to apply all of that to some practical responsive web design.

通过这个系列的学习我们已经熟悉了 Grid 的语法，学会了处理页面布局的一些高效方法，对于一些旧习惯可以说再见了。在这篇教程中，我们会继续学习处理响应布局的方法。

### Media Queries

### 媒体查询

Let’s use the demo from where we left off last time.

我们继续使用上次的 Demo。

[DEMO](https://codepen.io/tutsplus/pen/XjNxbW)

It comprises two declared grids; our main grid and the nested grid within one of our items. We can control when these grids come into effect using media queries, meaning we can completely redefine our layout at different viewport widths.

Demo 中有两个网格; 父级网格和嵌套在其中一个元素中的网格。我们可以通过媒体查询控制网格何时生效，也就是说我们可以为不同的视窗重新定义布局。

Begin by duplicating the first grid declaration, and wrapping the duplicate in a mobile-first media query (I’m using 500px as the breakpoint, but that’s completely arbitrary):

首先，复制一份第一个网格布局的声明，然后，把它放在移动优先的媒体查询规则中（我用的 500px 作为一个断点，其实，这完全可以很随意的）:

```css
.grid-1 {
    /* grid declaration styles */
}
 
 
@media only screen and (min-width: 500px) {
 
    .grid-1 {
        /* grid declaration styles */
    }
 
}
```

Now, within the first declaration we’ll change how our grid is defined, placing the whole thing in a single column. We’ll list just one column in our ```grid-template-columns``` rule, make sure the four rows we now have are defined with ```grid-template-rows```, and arrange the layout with ```grid-template-areas```:

现在，改变第一条网格布局中的定义，让整个网格变成单列的。我们只需在 ```grid-template-columns``` 定义一条规则，为了确保有四行我们需要用到 ```grid-template-rows```，然后，用 ```grid-template-areas``` 来定义网格区域名称：

```css
.grid-1 {
    display: grid;
    width: 100%;
    margin: 0 auto;
     
    grid-template-columns: 1fr;
    grid-template-rows: 80px auto auto 80px;
    grid-gap: 10px;
     
    grid-template-areas:    "header"
                            "main"
                            "sidebar"
                            "footer";
}
```

We’ve also made our ```grid-gap``` just 10px by default, to account for smaller screens.

我们还用 ```grid-gap``` 定义了 10px 的留白，为了在更小的屏幕上可以访问。

[Here’s what that gives us](http://codepen.io/tutsplus/pen/NRbOGL/). You’ll notice that we’re also using our media query to change the ```padding``` and ```font-size``` on our ```.grid-1 div``` items.

[可以在这看看具体的样子](http://codepen.io/tutsplus/pen/NRbOGL/)。你们应该也注意到了我们也用媒体查询改变了 ```.grid-1 div``` 的 ```padding``` 和 ```font-size```。

#### Our Nested Grid

#### 嵌套的网格

That takes care of the main layout, but we still have the nested grid which remains stubbornly in its two column layout under all circumstances. To fix that we’ll do exactly the same as before, but using a different breakpoint to suggest a content-first approach:

这只对父级网格有效，但是，第二个嵌套的网格始终还是会保持两列。用之前同样的方式可以处理这种问题，但是，这次使用不同的断点，建议使用内容优先的规则：

```css
.item-2 {
    /* grid declaration styles */
}
 
 
@media only screen and (min-width: 600px) {
 
    .item-2 {
        /* grid declaration styles */
    }
 
}
```

Check out [the end result on CodePen](http://codepen.io/tutsplus/pen/mAOzVq/).

[在 CodePen](http://codepen.io/tutsplus/pen/mAOzVq/) 查看最终结果。

A couple of things to note here: 

- As we’ve said before, you can *visually* arrange grid items irrespective of the source order, and media queries mean we can have different visual orders for different screen widths. However, nesting has to remain true to the source; our nested grid items must always be visually and *actually* descendants of their parent.
- CSS transitions don’t have any influence over Grid layout. When our media queries kick in, and our grid areas move to their new positions, you won’t be able to ease them into place.

注意事项：

- 正如我们之前说的，你在实现*视觉*上的网格布局时可以不管元素的原顺序，然后，配合媒体查询我们可以为不同宽度的屏幕实现不同的视觉效果。然而，嵌套的元素必须保证原顺序的真实性；它们对于父级必须一直是可见的，并且*必须是*父级的子元素。（*译者注：就是说嵌套的网格单元不受父级单元的顺序影响*）
- CSS transitions 对网格布局中的的属性无效。当媒体查询规则生效时，网格区域会立即生效，并不会有过度的效果。

### auto-fill and minmax()

### auto-fill 和 minmax()

Another (sort of) responsive approach to Grid is well suited to masonry-type layouts; blocks which size and flow automatically, depending on the viewport. 

网格的另外一种响应布局非常适合 masonry 类型的布局；根据屏幕大小，自动调整元素块的大小。

#### auto-fill

Our approach up until now has been to dictate how many tracks there are and watch the items fit accordingly. That’s what is happening in this demo; we have ```grid-template-columns: repeat(4, 1fr)```; which says “create four columns, and make each one a single fraction unit wide”.

目前为止，我们的布局有几行几列都是明确知道的。当前的 demo 也是这么处理的；我们定义了 ```grid-template-columns: repeat(4, 1fr)```；也就是说：创建 4 列，每一列都是相同的宽度。

[DEMO](https://codepen.io/tutsplus/pen/JREEdA)

With the ```auto-fill``` keyword we can dictate how *wide* our tracks are and let Grid figure out how many will fit in the available space. In this demo we’ve used ```grid-template-columns: repeat(auto-fill, 9em)```; which says “make the columns 9em wide each, and fit as many as you can into the grid container”. 

我们可以定义每个网格轨道有多*宽*，然后，使用关键字 ```auto-fill``` 让网格自动计算出在有限的空间可以填充多少网格轨道。在这个 demo 中我们定义了 ```grid-template-columns: repeat(auto-fill, 9em)```；也就是说：每一列都是 9em 宽，网格容器中尽可能多的填充网格轨道。

[DEMO](https://codepen.io/tutsplus/pen/rrjjpy)

**Note**: this also takes our gutters, the ```grid-gap```, into account.

**注意**：在计算的时候 ```grid-gap``` 也是需要考虑的。

The container in these demos has a dark background to show clearly how much space is available, and you’ll see that it hasn’t been completely filled in the last example. So how do we do that?

Demo 中的容器都有一个黑色背景，为了展示有多少可以用空间，在最后一个示例中，你会发现并没有完全的被填充。我们如何处理呢？

#### minmax()

The ```minmax()``` function allows us to set a minimum and a maximum size for a track, enabling Grid to work within them. For example we could setup three columns, the first two being 1fr wide, the last being a maximum of 1fr, but shrinking no smaller than 160px:

方法 ```minmax()``` 可以让我们为网格轨道设置一个最小和最大值，网格自动的取舍。比如，我们可以设置三列，第一、第二列为 1fr 宽，最后一列宽最大值为 1fr，可以自由伸缩，但是不能小于 160px。

```css
grid-template-columns: 1fr 1fr minmax(160px, 1fr);
```

All the columns will shrink as you squish the window, but the last column will only be pushed so far. [Take a look](http://codepen.io/tutsplus/pen/rrjywv/).

所有的列将会根据窗口大小自动伸缩，但是最后一列会有个最小限制。[看一下效果](http://codepen.io/tutsplus/pen/rrjywv/)。

Back to our ```auto-fill``` demo, if we were to change our column width for ```minmax(9em, 1fr)``` Grid would place as many 9em tracks as possible, but then expand them to a maximum of 1fr until the container is filled:

回去看下 ```auto-fill``` 的 demo，如果，我们把列宽设置成 ```minmax(9em, 1fr)```，容器会尽可能多展示 9em 宽的网格轨道，但是，它们会在有限的空间里扩张到最大值 1fr：

[DEMO](https://codepen.io/tutsplus/pen/kkggdx)

**Caveat**: Grid will recalculate the tracks upon page reload (try squishing the browser window and hitting refresh) but it won’t do so on window resize. Media queries can be used to alter the values, but they still won’t play nice with window resize.

**警告**：Grid 会在页面重新加载的时候重新计算网格轨道（改变浏览器窗口后刷新页面试试），~~但是，window resize 并不会重新计算~~。利用媒体查询可以改变布局，但是还是不能配合 window resize 一起使用。    
> **译者注：测试发现在 window resize 的时候也会重新计算，原作者提到是 chrome 早起的 bug**    
> 参考资料
> - <https://bugs.chromium.org/p/chromium/issues/detail?id=642806&can=1&q=grid%20window%20resize%20&colspec=ID%20Pri%20M%20Stars%20ReleaseBlock%20Component%20Status%20Owner%20Summary%20OS%20Modified>
> - <https://github.com/rachelandrew/gridbugs/issues/16>
> - <https://stackoverflow.com/questions/45465620/css-grid-layout-strange-behavior-on-window-resize?answertab=votes#tab-top>

### Conclusion

### 总结

Let’s wrap up with some bullets:

- Media queries can help us *completely* rearrange Grid layouts by redefining ```grid-template-areas``` (and other things) for different scenarios.
- CSS transitions don’t have any effect on changes made to the grid layout.
- The ```auto-fill``` keyword is useful for filling up grid containers.
- The ```minmax()``` function complements ```auto-fill``` nicely, making sure containers are properly filled, but doesn’t give us “responsiveness” in the true sense of the word.

我们来概括一下：

- 通过媒体查询重新定义 ```grid-template-areas``` （或者其它的属性）为不同的场景实现不同的 Grid 布局
- CSS transitions 对 Grid 布局中的相关属性无效
- 关键字 ```auto-fill``` 处理完全填满 grid 容器的布局时非常有用
- 方法 ```minmax()``` 配合 ```auto-fill``` 使用，可以确保容器正确的填充，但是，这并不是真正意义上的“响应式”。

With the lessons learned in this series, you’re armed to go out and start playing with Grid! Stay tuned for more Grid tutorials, practical exercises, solutions to common layout problems, and updates.

通过这一系列的课程学习，你已经可以放心大胆的使用 Grid ！敬请关注更多 Grid 教程、真实练习、常用布局的解决方案和更新。

#### Useful Resources

- Rachel Andrew’s [Grid by Example 29: minmax() 和 spanning columns 和 rows](http://codepen.io/rachelandrew/pen/GZQYQa)
- Video: Rachel Andrew (obviously) [demonstrating minmax() on the Tuts+ homepage layout](https://www.youtube.com/watch?v=paBou8Fy25k&feature=youtu.be)
- W3C Editor’s Draft: [auto-fill](https://drafts.csswg.org/css-grid/#valdef-repeat-auto-fill)
- W3C Editor’s Draft: [minmax()](https://drafts.csswg.org/css-grid/#valdef-grid-template-columns-minmax)

#### 有用资源

- Rachel Andrew’s [Grid 示例 29: minmax() and spanning columns and rows](http://codepen.io/rachelandrew/pen/GZQYQa)
- 视频: Rachel Andrew (obviously) [minmax() 在 Tuts+ 首页的演示](https://www.youtube.com/watch?v=paBou8Fy25k&feature=youtu.be)
- W3C 草案: [auto-fill](https://drafts.csswg.org/css-grid/#valdef-repeat-auto-fill)
- W3C 草案: [minmax()](https://drafts.csswg.org/css-grid/#valdef-grid-template-columns-minmax)
