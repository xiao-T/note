## Super-Powered Grid Components with CSS Custom Properties
## 用 CSS 自定义属性实现强大的 Grid 组件

> 原文地址: <https://css-tricks.com/super-power-grid-components-with-css-custom-properties/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

A little while ago, I wrote a well-received article about [combining CSS variables with CSS grid](https://codepen.io/michellebarker/post/super-powered-layouts-with-css-variables-css-gr) to help build more maintainable layouts. But CSS grid isn’t just for pages! That is a common myth. Although it is certainly very useful for page layout, I find myself just as frequently reaching for grid when it comes to components. In this article I’ll address using CSS grid at the component level.

前不久，我写过一篇[结合 CSS 变量和 Grid](https://codepen.io/michellebarker/post/super-powered-layouts-with-css-variables-css-gr) 构建更易维护布局的文章备受欢迎。但是，Grid 不只是这样！这是普遍的认知。尽管，它是处理布局非常有效的方式，在处理组件时，我经常会考虑 Grid 的实现方式。在这篇文章中，我会用 CSS Grid 来解决组件的相关问题。

Grid is [neither a substitute for flexbox](https://css-tricks.com/css-grid-replace-flexbox/) nor vice versa. In fact, using a combination of the two gives us even more power when building components.

Grid 和 [flexbox](https://css-tricks.com/css-grid-replace-flexbox/) 两者是不可相互替代的。事实上，构建组件时两者配合使用功能更加强大。

### Building a simple component

### 构建简单的组件

In this demo, I’ll walk through building a text-and-image component, something you might commonly find yourself building on a typical site, and which I have frequently built myself.

在这个示例中，我会构建一个简单的图文组件，这个在一些经典的站点上经常会看到，我自己也经常会构建这种组件。

This is what our first component should look like:

我们的第一个组件看起来就是这个样子：

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1534536419/grid-variables-1_o50qlk.jpg)

Let’s imagine that the elements that make up our component need to align to the same grid as other components on the page. Here is the same component with grid columns overlaid (ignoring the background):

我们想象一下：页面上某一组件中的元素需要和其它同样是 Grid 组件元素对齐。这就是组件被同样的网格线覆盖的情况（忽略背景）：

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1534536439/grid-variables-2_khpsmm.jpg)

You should see a grid of 14 columns—12 central columns of a fixed width and two flexible columns, one on either side. (These are for illustration purposes and won’t be visible in the browser.) Using ```display: grid``` on an element allows us to position that element’s *direct children* on the grid container. We’re keeping things simple, so our component has just two grid children—an image and a block of text. The image starts at Line 2 and spans seven tracks on the column axis. The text block starts at Line 9 and spans five columns.

你应该看到了一个 14 列的 Grid 布局 -- 中间 12 列是固定宽度和两列不定宽，一边一个。（这都是为了演示的，浏览器中并不可见的）在某个元素上声明 ```display: grid```，我们就可以在 Grid 容器中定位它的*直接子元素*。为了简单，我们的组件只有两个元素 -- 图片和文字块。图片从第 2 行开始横跨 7 列。~~文字块从第 9 行开始横跨 5 列~~ 文字块从第 10 行开始横跨 4 列.

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_600,f_auto,q_auto/v1534536467/grid-variables-3_xfuaac.jpg)

First, and importantly, we need to start with semantic markup. Although grid gives us the power to place its children in any layout we choose, for accessibility and non-grid-supporting browsers, the underlying markup should follow a logical order. Here is the markup we’ll be using for our grid component:

首先，也是最重要的，我们需要从有语义的标记开始。尽管，在处理布局时 Grid 可以让我们随意的定位元素，但是，为了可访问性和不支持 Grid 的浏览器，底层的标记还是应该遵循一定的顺利逻辑。以下这些标记就是我们组件要用到的：

```html
<article class="grid">
  <figure class="grid__media">
    <img src="" alt="" width="800px" height="600px" />
  </figure>
  <div class="grid__text">
    <h3>Heading</h3>
    <p>Lorem ipsum...</p>
  </div>
</article>
```

The ```.grid__media``` and ```.grid__text``` classes will be used for positioning the child items. First, we will need to define our grid container. We can use the following CSS to size our grid tracks:

类名 ```.grid__media``` 和 ```.grid__text``` 被用来定义子元素。首先，我们需要定义 Grid 容器。我们可以用以下的 CSS 定义网格轨道的大小：

```css
.grid {
  display: grid;
  grid-template-columns: 
    minmax(20px, 1fr) repeat(12, minmax(0, 100px)) minmax(20px, 1fr);
  grid-column-gap: 20px;
}
```

This will give us the grid we illustrated earlier—12 central columns with a maximum width of 100px, a flexible column on either side, and a gap of 20px between each column. If you’re curious about the ```minmax()``` function I wrote [an article about it](https://codepen.io/michellebarker/post/css-grid-more-flexibility-with-minmax), which explains what’s going on with our grid columns here.

这就是刚才插图中的实现方式 -- 中间的 12 列的宽不会大于 100px，两边各有一个不定宽的列，每列之间还有 20px 的留白。如果对方法 ```minmax()``` 有兴趣，我也写过[一篇文章](https://codepen.io/michellebarker/post/css-grid-more-flexibility-with-minmax)，里面也对当前的 Grid 布局做了解释。

In this case, as both our image and text block will be on the same grid row track (and I want the track size to just use the default ```auto```, which will size the row according to the content within), I have no need to declare my grid row sizing here. However, explicitly placing the grid items on the same row will make things easier when it comes to different component variants, which we’ll get to shortly.

在这个示例中，图片和文字块会在同一行中（我希望行的大小用默认值 ```auto```，它会根据内容自动计算），这里我没有必要声明行的大小。不管怎样，显然的当需要处理不同组件时，在同一行定位网格元素会更加容易，我们也可以很快的去处理。

> If you’re new to Grid and looking for ways to get started, I recommend heading over to [gridbyexample.com](http://gridbyexample.com/). Don’t worry too much about the ```grid-template-columns``` declaration for now—many of the points in this article can be applied to simpler grids too.

> 如果，你是新手正在学习 Grid。我推荐你前往 [gridbyexample.com](http://gridbyexample.com/)。现在无需过于担心 ```grid-template-columns``` 的声明，这篇文章中的很多知识点可以适用一些简单的 Grid。

Now, we need to place our grid items with the following CSS. Grid offers us a whole range of different ways to place items. In this case, I’m using ```grid-column```, which is the shorthand for ```grid-column-start / grid-column-end```:

现在，我们需要用以下的 CSS 定位我们的网格元素。Grid 让我们可以用不同的方式定位不同的区域范围。示例中，我用到了 ```grid-column```，它是 ```grid-column-start / grid-column-end``` 的简写：

```css
.grid__media {
  grid-column: 2 / 9; /* Start on Column 2 and end at the start of Column 9 */
}

.grid__text {
  grid-column: 10 / 14; /* Start on Column 10 and end at the start of Column 14 */
}

.grid__media,
.grid__text {
  grid-row: 1; /* We want to keep our items on the same row */
}
```

> If you find it tough to visualize the grid lines when you code, [drawing it in ASCII in the CSS comments](https://css-tricks.com/little-tip-draw-your-grid-in-ascii-in-your-css-comments-for-quick-reference/) is a handy way to go.

> 如果，通过代码很难想像出网格线，[在 CSS 注释中用 ASCII 画出来](https://css-tricks.com/little-tip-draw-your-grid-in-ascii-in-your-css-comments-for-quick-reference/)是一种简单的处理方式。

Here’s a demo of our component in action—you can toggle the background on and off to show the grid columns:

这个 DEMO 就是我们的组件演示，你可以切换显示隐藏网格背景图：

[DEMO](https://codepen.io/michellebarker/pen/ajjBXd)

### Component variants

### 变种组件

Building our component is simple enough, but what if we have many variants of the same component? Here are some examples of different layouts we might want to use:

构建组件足够简单，但是，如果我们需要处理同一组件的多个变种是，怎么办呢？接下来的示例就需要处理不同的布局：

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1534536526/grid-variables-4_rwfse3.jpg)

As you can see, the text blocks and images occupy different positions on the grid, and the image blocks span a different number of columns.

如你所见，在网格中文字块和图片的位置都不相同，并且图片在每一行所占据的列数也不相同。

Personally, I have previously worked on components that had as many as nine variants! Coding grid for these types of situations can become difficult to manage, especially when you take things like fallbacks and responsiveness into account, because our grid declarations can become difficult to scan and debug. Let’s look at ways we can make this easier to manage and maintain.

就我个人而言，我曾经处理过多达 9 个不同的组件！对于这种情况代码变得难以管理，尤其是你需要考虑回退和响应性，因为我们的代码声明变得难以审查和调试。让我们看看如何更简单的管理和维护这样的代码。

### Consistency

### 一致性

First, let’s determine which property values are going to remain consistent among our component variants, so that we can write as little code as possible. In this case, our text block should always span four columns, while our image can be larger or smaller, and align to any column.

首先，我们要确定在多个组件中那些属性是需要保持一致的，这样我们就可以尽可能的少写代码。在当前示例中，我们的文字块始终会占用四列，而图片可大可小，并且可以和任意列对其。

For our text block I’m going to use the ```span``` declaration—that way, I can be sure to keep this block a consistent width. For our image, I’m going to keep using the start line and end line instead, since the number of columns the images span varies between the different component variants, as well as its position.

对于文字块我会用到 ```span``` 的方式来声明，这样我就可以确保它们宽度的一致性。对于图片，由于每个组件中图片所占用的列数都不相同，为了更好的定位，我继续保持使用开始、结束行数的定义方式。

### Naming grid lines

### 网格线命名

Grid allows you to [name your lines](https://css-tricks.com/things-ive-learned-css-grid-layout/#article-header-id-9) and refer to them by name as well as number, which I find extremely helpful when placing items. You might not want to go too crazy with naming lines, as your grid declaration can become difficult to read, but naming a few commonly-used lines can be useful. In this example, I’m using the names ```wrapper-start``` and ```wrapper-end```. Now, when placing items against either of these lines, I know they’ll align to the central wrapper.

Grid 允许你[给网格线命名](https://css-tricks.com/things-ive-learned-css-grid-layout/#article-header-id-9)并且名称要比网线编号引用方便，当需要定位元素时，我发现这种方法非常有用。给网格线命名后代码会难以阅读，你或许不希望这样，但是，给一些常用的网格线命名还是很有用的。在这个示例中，我用了 ```wrapper-start``` 和 ```wrapper-end```。现在，当给某个元素分配这些网格线时，我就知道它们会和 wrapper 对齐。

```css
.grid {
  display: grid;
  grid-template-columns: [start] minmax(20px, 1fr) [wrapper-start] repeat(12, minmax(0, 100px)) [wrapper-end] minmax(20px, 1fr) [end];
}
```

> Pre-fixing grid line names with ```*-start``` and ```*-end``` has the added advantage of allowing us to place items by grid area. By setting ```grid-column: wrapper``` on a grid item, it will be placed between our ```wrapper-start``` and ```wrapper-end``` lines.

> 使用 ```*-start``` 和 ```*-end``` 这种有前缀的方式命名，在我们定位元素的时候会更有用。当给某个元素设置 ```grid-column: wrapper```，它就会被放置在 ```wrapper-start``` 和 ```wrapper-end``` 之间。


### Using CSS Variables for placement

### 使用 CSS 变量定义位置

[CSS variables](https://css-tricks.com/tag/css-variables/) (also known as custom properties) can allow us to write more maintainable code for our components. They enable us to set and reuse values within our CSS. If you use a preprocessor like Sass or Less, it’s likely you’re familiar with using variables. CSS variables differ from preprocessor variables in a few important ways:

[CSS 变量](https://css-tricks.com/tag/css-variables/)（也被称作自定义属性）可以让我们写的组件代码更易维护。它们可以让我们在 CSS 中设置重用的值。如果，你有用过像 Sass 和 Less 预处理器，它有点类似里面的的变量。CSS 变量和预处理中的变量还是有一些不一样的地方：

- They are dynamic. In other words, once they are set, the value can still be changed. You can update the same variable to take different values, e.g. within different components or at different breakpoints.

- CSS 变量是动态的。换句话说，一旦值被设置后，是随时可以改变的。你可以更新它的值，比如：为不同组件设置不同的断点。

- They cannot be used within selector names, property names or media query declarations. They must only be used for property values (hence they’re known as custom properties).

- 它们不能用来定义选择器、属性和媒体查询。只能用来定义属性的值（因此也称为自定义属性）。

CSS variables are set and used like this:

CSS 变量可以这样设置使用：

```css
.box {
  --color: orange; /* Defines the variable and default value */
  background-color: var(--color); /* Applies the variable as a property value */
}
```

We can take advantage of the fact that CSS variables allow defaults—so that if a variable is not found, a fallback value will be used.

我们可以利用 CSS 变量默认值的好处 -- 如果，变量没有找到，将会使用这个默认值。

You can set a default value for a variable like this:

你可以这样给变量设置一个默认值：

```css
background-color: var(--color, red);
```

So, in this case, we’re setting the ```background-color``` property to the value of ```--color``` but if that variable can't be found (e.g. it hasn't been declared yet), then the background will be red.

因此，这个示例中，我们给 ```background-color``` 设置了 ```--color```，但是，如果这个变量找不到（比如：没有声明），这时背景色将会是红色。

In the CSS code for placing our grid items, we can use a variable and declare a default value, like so:

对于我们定义的网格布局代码，我们也可以用 CSS 变量并定义一个默认值，比如：

```css
.grid__media {
  grid-column: var(--mediaStart, wrapper-start) / var(--mediaEnd, 9);
}

.grid__text {
  grid-column: var(--textStart, 10) / span 4;
}
```

We don’t need a variable for the text ```span``` declaration, as this will be the same for each component variant.

我们不需要为文字块的 ```span``` 定义变量，因为每个组件中它们都是一样的。

Now, when it comes to our component variants, we only need to define the variables for the item positions when they are different from the original:

现在，回到我们组件中，我们只需在元素位置与原位置不同的地方设置变量即可：

```css
.grid--2 {
  --mediaEnd: end;
  --textStart: wrapper-start;
}

.grid--2,
.grid--4 {
  --mediaStart: 8;
}

.grid--3 {
  --mediaStart: start;
}

.grid--4 {
  --mediaEnd: wrapper-end;
  --textStart: 3;
}
```

Here’s the result:

这是演示结果：

[DEMO](https://codepen.io/michellebarker/pen/JBZLXy)

For comparison, placing items in the same way without using variables would look like this:

为了对比，在没有变量的情况下代码就会像这样：

```css
/* Grid 2 */
.grid--2 .grid__media {
  grid-column: 8 / end;
}

.grid--2 .grid__text {
  grid-column-start: wrapper-start;
}

/* Grid 3 */
.grid--3 .grid__media {
  grid-column-start: start;
}

/* Grid 4 */
.grid--4 .grid__media {
  grid-column: 8 / wrapper-end;
}

.grid--4 .grid__text {
  grid-column-start: 3;
}
```

Quite a difference, right? To me, the version with variables is more readable and helps me understand at a glance where the elements of each component variant are positioned.

差别很大，对吧？对于我来说，使用变量的版本更易阅读并且可以帮助我们很好的理解不同组件中位置的差异。

### Variables within media queries

### 媒体查询中的变量

CSS variables also allow us to easily adjust the position of our grid items at different breakpoints by declaring them within media queries. It’s conceivable that we might want to show a simpler grid layout for tablet-size screens and move to a more complex layout once we get to desktop sizes. With the following code, we can show a simple, alternating layout for smaller screen, then wrap the variables for the more complex layout in a media query.

使用媒体查询在不同的断点为 CSS 变量设置不同的值可以让我们更容易的为网格元素分配不同的位置。想象一下，我们可以为平板电脑显示一个简单的网格布局，一旦到了桌面电脑就显示更为复杂的布局。使用下面的代码，我们为小屏幕提供了一个简单的布局，更复杂的布局用媒体查询重新定义了变量的值。

```css
.grid__media {
  grid-column: var(--mediaStart, wrapper-start) / var(--mediaEnd, 8);
}

.grid__text {
  grid-column: var(--textStart, 8) / span var(--textSpan, 6);
}

.grid--2,
.grid--4 {
  --mediaStart: 8;
  --mediaEnd: 14;
  --textStart: wrapper-start;
}

/* Move to a more complex layout starting at 960px */
@media(min-width: 960px) {
  .grid {
    --mediaStart: wrapper-start;
    --mediaEnd: 9;
    --textStart: 10;
    --textSpan: 4;
  }
  
  .grid--2 {
    --mediaStart: 9;
    --mediaEnd: end;
    --textStart: wrapper-start;
  }
  
  .grid--2,
  .grid--4 {
    --mediaStart: 8;
  }

  .grid--3 {
    --mediaStart: start;
  }

  .grid--4 {
    --mediaEnd: wrapper-end;
    --textStart: 3;
  }
}
```

Here’s the full demo in action:

这是一个完整的 DEMO 演示：

[DEMO](https://codepen.io/michellebarker/pen/XBPMZZ)

### Performance

### 性能

One thing to be aware of when using CSS variables is that changing a variable on an element will cause a style recalculation on all of that element’s children (here’s [an excellent article](https://lisilinhart.info/posts/css-variables-performance/) that covers performance of variables in great detail). So, if your component has many children, then this may be a consideration, particularly if you want to update the variables many times.

在使用 CSS 变量时，有件事还是需要注意的，一旦组件变量被改变这就会导致所有的子元素都需要重新计算（这是[一篇优秀的文章](https://lisilinhart.info/posts/css-variables-performance/)，它详细介绍了 CSS 变量的性能问题）。因此，如果你的组件有很子元素，还是需要考虑的，尤其是你需要多次更新。

### Conclusion

### 总结

There are many different ways of handling responsive design with CSS grid, and this certainly isn’t the *only* way. Neither would I advise using variables for all your grid code in every part of your project—that could get pretty confusing and would likely be unnecessary! But, having used CSS grid a lot over the past year, I can attest that this method works really well to keep your code maintainable when dealing with multiple component variants.

使用 CSS Grid 有很多方式处理相应布局，并且这不是*唯一*的方法。当然，我也不建议你在整个项目中使用 CSS 变量 -- 这样可能会导致混乱并且不是必要的！但是，鉴于过去一年我的使用经验，我可以肯定这种方法在处理多个组件时，真的可以让代码更易维护。