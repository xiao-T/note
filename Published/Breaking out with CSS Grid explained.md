## Breaking out with CSS Grid explained
## 打破边界布局 CSS Grid 讲解

> 原文地址: [https://rachelandrew.co.uk/archives/2017/06/01/breaking-out-with-css-grid-explained/](https://rachelandrew.co.uk/archives/2017/06/01/breaking-out-with-css-grid-explained/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> [This really nice technique for creating a full width image inside a constrained column](https://cloudfour.com/thinks/breaking-out-with-css-grid-layout/) was being shared around recently. However, what it didn’t do was explain why this technique works. As part of my attempt to remove all the “it just works magic” from these techniques I thought I would explain.

最近比较流行一项非常好的技术：[在固定宽度的容器中让图片铺满整屏](https://cloudfour.com/thinks/breaking-out-with-css-grid-layout/)。但是，文章中并没有解释原理。我尝试移除那些"这么神奇"的部分，尽可能解释是它们是如何工作的。

> I’ll write more about naming when I get to that part of my series explaining the Grid Spec, however understanding this technique requires the understanding of a few things about Grid.

当我说到 Grid 规范的时候，我会介绍很多有关命名的内容。这对于理解 Grid 是有很大帮助的。

### We can position things against lines of the grid

### 在 Grid 中我可以根据网格线定位 DOM

> If you have used grid at all you are probably familiar with the concept of positioning items based on line numbers. We can achieve the breakout layout using this line-based placement of items. When we create a three column grid, as in the image below, we get 4 numbered lines. The breakout item is positioned from line 1 to line 4, the content from line 2 to 3. You can also see this in a [CodePen](https://codepen.io/rachelandrew/pen/PjYGLm).

如果，你用过 Grid，那你可能会觉得这有点类似基于行号定位的概念。我们可以基于元素所在的行号实现这种打破边界的布局。当我们创建了一个三列的 Grid，就像下面的图片所示，我们得到了 4 个行号。打破边界的元素所占位置从行 1 到行 4，content 元素从行 2 到行 3。你可以看看这个[示例](https://codepen.io/rachelandrew/pen/PjYGLm)。

![](https://rachelandrew.co.uk/perch/resources/numbered-lines-1.png)

> However the example tutorial doesn’t use line numbers at all. It’s neater than that and doesn’t require that you understand what number lines on the grid are.

但是，示例（**文中一开始提到的DEMO**）中并没有用到行号。那样更加整洁，不需要你去理解在 Grid 行号是什么意思。

### You can name lines

### 你可以给网格命名

> You need to know that you can name lines. In my next example the lines are named, I replace numbers with names to place items on the grid. [Also demonstrated on CodePen](https://codepen.io/rachelandrew/pen/zwXwmj).

你要知道你是可以给网格命名的。在我接下来的示例中是有给网格命名的。在 Grid 中我用网格的名字代替了行号。[可以看看 CodePen 上的演示](https://codepen.io/rachelandrew/pen/zwXwmj)。

![](https://rachelandrew.co.uk/perch/resources/named-lines.png)

> Is this example, as with the referenced tutorial, I have named my lines ```*-start``` and ```*-end```. This is important for the technique. Naming lines is optional as the article suggests, but the technique as described wouldn’t work without named lines.

这个示例，参考了教程，我给网格以 ```*-start``` 和 ```*-end``` 模式命名。这是很重要的一点。在教程中虽然说命名是可选的，但是，教程中描述的技术如果没有给网格命名将无法使用。

> When you name lines, you can optionally name them as ```*-start``` and ```*-end``` and this gives you a little more grid magic. We get a named grid area of the main name used. Sounds odd? Take a look at the diagram below, it shows 4 named grid lines, ```main-start``` and ```main-end``` both for columns and rows. The area marked out by the intersection of these lines can now be referenced by the name ```main```. If we had named the lines ```foo-start``` and ```foo-end``` then we would have a named area called ```foo```.

当你决定给网格命名时，你可以选择 ```*-start``` 和 ```*-end``` 这种模式，这样会让你的网格系统更神奇。你会得到一个名字为 ```main``` 的网格区域。听起来很奇怪？看一看下面的图解，这里有 4 个网格名字，```main-start``` 和 ```main-end``` 对称分布在行和列上。被网格线圈出来的区域就可以被称为  ```main```。如果，我们用 ```foo-start``` 和 ```foo-end```，那么我们将得到一个名为 ```foo``` 的区域。

![](https://rachelandrew.co.uk/perch/resources/grid-area.png)

> However this doesn’t get us to the technique used, which doesn’t used the named area, it is positioning against lines named ‘main’ and ‘full’ which are not defined anywhere.

然而，我们并没有这么使用，没有给那个区域命名为 ```foo``` ，我们只是定义了 ```main``` 和 ```full``` 两个区域。

> We have lines named ```main-start``` and ```main-end``` for our columns, therefore a named area called ```main```. We also have lines named main that can be used for start due to the following [note in the specification for line-placement:](https://www.w3.org/TR/css-grid-1/#line-placement)

我们把列的网格线以：```main-start``` 和 ```main-end``` 命名，因此有个区域就成为 ```main```。我们[依照 line-placement 相关规范](https://www.w3.org/TR/css-grid-1/#line-placement)得到了一个名为 ```main``` 的网格。

> “Note: Named grid areas automatically generate implicit named lines of this form, so specifying ```grid-row-start: foo``` will choose the ```start``` edge of that named grid area (unless another line named ```foo-start``` was explicitly specified before it).”

> "提示：网格的名字是根据网格线自动生成的，因此指定: ```grid-row-start: foo``` 会选择名为 ```foo-start``` 的网格边界（除非在它之前有另外一个名为 ```foo-start``` 的网格线）。"

> This means that we can set ```grid-column-start``` to ```main``` and Grid will position that item at the start edge of the area that is named ```main``` due to us having named grid lines of ```main-start``` and ```main-end```.

也就是说我们可以设置 ```grid-column-start: main```，由于我们已经有了名为 ```main-start``` 和 ```main-end``` 的网格线，所以名为 ```main``` 的区域就对应这两条线的边界所包含的区域。

> What of the end line? In the tutorial the item is positioned with ```grid-column: main```.

结束边界线呢？在教程中 DOM 是用 ```grid-column: main``` 来定位的。

> The final piece of the puzzle is that if the start line is a named ident (which ours is) and the end line is missing, then the end line is set to the same named ident. In [the specification for placement shorthands we can read](https://www.w3.org/TR/css-grid-1/#placement-shorthands):

最后的难题是：如果我们设置了起始位置，但是缺失结束位置，那么结束位置将和起始位置共用一个值。[在规范中有关简写我们可以发现](https://www.w3.org/TR/css-grid-1/#placement-shorthands)：

(**译者注：如果用户这么设置```grid-column: full```**，等价于```    grid-column-start: full;grid-column-end: full;```)

> “[when using ```grid-row``` and ```grid-column``` shorthands] … When the second value is omitted, if the first value is a <custom-ident>, the ```grid-row-end/grid-column-end``` longhand is also set to that <custom-ident>; otherwise, it is set to ```auto```.”

> "当使用 ```grid-row``` 和 ```grid-column``` 简写时" ... 当第二值省略时，如果设置了第一个值，那么 ```grid-row-end/grid-column-end``` 将复用第一个的值；否则，将默认为 ```auto```。

> This gives us a ```grid-column-end``` of ```main``` which will give us the end edge of the area called ```main``` thus our item stretches right across the grid covering all the column tracks between ```main-start``` and ```main-end```.

```grid-column-end: main```，指定了结束边界也是被称为 ```main``` 的区域，因此，我们项目中的元素横跨了 ```main-start``` 和 ```main-end``` 之间所有的区域。

![](https://rachelandrew.co.uk/perch/resources/implicit-named-lines.png)

> You can see this in DevTools if you inspect the element ```grid-column-start``` and ```grid-column-end``` is ```main``` for the elements constrained by the column and ```full``` for elements constrained by the full width breakout. This is shown in the below screenshot of [this CodePen of the completed example](https://codepen.io/rachelandrew/pen/xdedMy).

通过开发者工具你可以看到，如果设置了 ```grid-column-start``` 和 ```grid-column-end``` 的值都是 ```main```，那么这个元素会受到限制，但是如果是 ```full``` 则不会受到限制。下面截图的展示了 [CodePen 完整示例效果](https://codepen.io/rachelandrew/pen/xdedMy)。

![](https://rachelandrew.co.uk/perch/resources/ff-devtools-main.png)

> What this demonstrates is that when showing off a nice example it really is worth unpacking how the technique works. There is a lot to know about how line naming works, and by understanding it we can start to use this for more than just one technique.

这表明，在演示一个很好的示例时，它真正的价值：要理解时如何工作的。有很多需要你知道的，例如：如何命名，只有真正的理解它，我们才能更好的使用这项技术。





