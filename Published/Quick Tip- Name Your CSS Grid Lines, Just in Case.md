## Quick Tip: Name Your CSS Grid Lines, Just in Case
## 小建议：为网格线命名，以防万一

> 原文地址: <https://webdesign.tutsplus.com/tutorials/quick-tip-name-your-css-grid-lines-just-in-case--cms-27844/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In any CSS Grid each line has an index number which we can reference to place grid items. However, we can also name each of these grid lines, making it easier to work with and maintain our grid layouts. Let’s take a look!

CSS Grid 中任何的网格线都有一个索引编号，我们可以引用它们来定位元素。然而，我们还可以给每条网格线命名，以便更加方便的使用和维护 Grid 布局。接下来让我们看一看！

### Grid is Coming

### Grid 到来了

The most common response to any Grid tutorial is “but when can I use this?” and it’s a fair question. The fact is: Grid is coming, and it will be here soon.

针对 Grid 常见的问题是“我什么时候才能使用呢？”，这是个很正常的问题。事实上：Grid 时代正在到来，并且很快。

> “CSS Grid is going to become supported-by-default in Chrome and Firefox in March of 2017.” – [Eric Meyer](http://meyerweb.com/eric/thoughts/2016/12/05/css-grid/)

> "2017年3月 CSS Grid 将在 Chrome 和 Firefox 默认支持" - [Eric Meyer](http://meyerweb.com/eric/thoughts/2016/12/05/css-grid/)

If you haven’t taken a look at it yet, now’s the time!

如果，你还没有了解过，现在是该学习的时候了！

[DEMO](https://webdesign.tutsplus.com/tutorials/css-grid-layout-quick-start-guide--cms-27238)

[DEMO](https://webdesign.tutsplus.com/tutorials/css-grid-layout-units-of-measurement-and-basic-keywords--cms-27259)

### Grid Line Numbers

### 网格线编号

When we declare a grid, each line is given an index number: 

当我们声明了一个 Grid 后，每条线都会有个索引编号：

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27844/image/grid-terms-line-numbers-.svg)

**Note**: Unless the layout is set with ```direction: rtl;```, these numbers begin at the top left of the grid, working their way to the bottom right. 

**注意**：除非声明了 ```direction: rtl;```，编号是从左上角开始，然后向右下角移动。

We can reference these numbers to place grid items:

我们可以通过网格线编号定位 Grid 元素：

```css
.item {
  grid-column: 2;
  grid-row: 3;
}
```

In this example our ```.item``` element is positioned starting on column line 2, and row line 3:

示例中的 ```.item``` 被放置在了第二列、第三行。

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27844/image/grid-terms-line-numbers-position.svg)

### Explicit Grid Line Names

### 显式网格线命名

With more complex grids, you can imagine that referencing everything by numbers might get a bit confusing. For this reason, the Grid module allows us to explicitly name our lines when we declare our grid columns and rows.

在一些复杂的网格中，可以想象的到如果通过引用网格线编号来定位会比较混乱。基于这个原因，当我们声明时 Grid 模块允许我们显式的给网格线命名。

Let’s use an example like the ones we’ve used throughout this series. Here’s our basic 3×3 grid declaration:

让我们使用一个示例，就像这个系列中的示例一样。这是一个基本的 3x3 Grid 布局。

```css
.grid-1 {
  display: grid;
  grid-template-columns: 100px auto 100px;
  grid-template-rows: 60px 130px 50px;
  grid-gap: 20px;
}
```

Now we can wrap our column and row values with line names (whatever the heck we want), using square brackets:

现在我们可以用方括号的方式给网格线命名了(任何名字都可以)：

```css
.grid-1 {
  display: grid;
  grid-template-columns: [start] 100px [centre-start] auto [last-column-start] 100px [finish];
  grid-template-rows: [header-start] 60px [main-start] 130px [main-end] 50px [row-end];
  grid-gap: 20px;
}
```

We can now position items with names, instead of numbers:

现在，我们可以用网格线名称来代替网格线编号来定位元素了：

```css
.item {
  grid-column: centre-start;
  grid-row: header-start;
}
```

Some pointers:

- These names can be tailored to your own descriptive needs, so think logically about what will help you recognize and remember areas of the grid.
- The original line numbers remain in operation, so you can still use them.
- You can declare multiple names for one line, for example: ```[main-end footer-start row-5]``` etc.
- Even if you name your grid lines, you’re not obliged to use them, so it’s a good “just in case” habit to get into.

一些提示：

- 这些名称可以根据你的描述需求定制，因此，从逻辑上思考怎么才可以帮助你认识并记住这些网格区域。
- 网格线编号还是会被保留的，你仍旧可以使用它们。
- 你还可以给一条网格线定义多个名称，比如：```[main-end footer-start row-5]```。
- 即使，你给网格线命名了，但是你没有义务一定使用它们，因此，这只是“以防万一”的良好习惯。


### Implicit Grid Line Names

### 隐式的网格线命名

When we deliberately do things, like naming grid lines, that’s said to be *explicit*. When something is implied, but not directly stated, that’s referred to as being *implicit*. We’ve covered explicitly naming grid lines, but there are also circumstances where lines are given names implicitly.

当我们特意做了某些事情，比如：给网格线命名，也就是说这是*显式的*。如果，某些事情是隐式，没有直接说明，我们就可以称为*隐式的*。显式的网格线命名我们已经介绍过，但是，还有些情况是隐式的声明网格线名称。

You may recall from a previous tutorial that it’s possible to define grid areas.

你应该记得上次教程中我们提到网格是可以定义网格区域的：

[DEMO](https://webdesign.tutsplus.com/tutorials/css-grid-layout-using-grid-areas--cms-27264)

We might define four grid areas like this:

我们可以像这样定义四个网格区域：

```css
.grid-1 {
    /* ..existing styles */
      
    grid-template-areas:    "header header header"
                            "main main sidebar"
                            "footer footer footer";
}
```

This gives us the following:

最终结果如下：

![](https://cms-assets.tutsplus.com/uploads/users/30/posts/27844/image/grid-areas.svg)

Naming a grid area ```header``` automatically assigns names to its four boundary lines too. The row lines around it become ```header-start``` and ```header-end```, and likewise the two column lines also become ```header-start``` and ```header-end```. The same applies to the other areas, which are given line names ```main-start```, ```main-end```, ```sidebar-start``` and so on.

如果，给一个网格区域命名为 ```header```，那么它周围的四条线会自动的分配相应的别名。水平的两条线就会变成 ```header-start``` 和 ```header-end```，同样垂直的两条线也会变成 ```header-start``` 和 ```header-end```。其他区域也适用同样的规则，比如 ```main-start```, ```main-end```, ```sidebar-start``` 等等。

**Note**: Looking at things in reverse, explicitly adding named lines in the same format (```header-start``` and ```header-end```) will create a named grid area of ```header```. 

**注意**：反过来看，如果用同样的格式（```header-start``` 和 ```header-end```）给网格线命名同样会得到一个名为 ```header``` 网格区域。

We can use these line names just like we have previously, to position items. And again, they exist in addition to any names you define yourself, and the original line index numbers.

我还可以像以前一样使用这些名称，定位元素。同时，不但你命名的网格线名称在，原来的网格线编号也存在。

### End of the Line

### 最后

That’s it for this quick tip! Remember: get into the habit of naming your lines and areas for easier grid management and maintenance. 

这只是一个小提示！记住：给网格线命名是个很好的习惯，也会让的网格更易管理和维护。
