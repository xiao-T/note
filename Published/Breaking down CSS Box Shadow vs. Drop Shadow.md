## Breaking down CSS Box Shadow vs. Drop Shadow
## CSS Box Shadow 对比 Drop Shadow

> 原文地址: [https://css-tricks.com/breaking-css-box-shadow-vs-drop-shadow/](https://css-tricks.com/breaking-css-box-shadow-vs-drop-shadow/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> ***Drop shadows***. Web designers have loved them for a long time to the extent that we used to fake them with PNG images before CSS Level 3 formally introduced them to the spec as the [box-shadow](https://css-tricks.com/almanac/properties/b/box-shadow/) property. I still reach for drop shadows often in my work because they add a nice texture in some contexts, like working with largely flat designs.

阴影。在很长一段时间内 web 开发者非常喜欢用透明图片伪造，直到 CSS3 引入 [box-shadow](https://css-tricks.com/almanac/properties/b/box-shadow/)。我经常在我的工作中使用阴影，因为它们会增加内容的质感，就像大部分平面设计一样。

> Not too long after ```box-shadow``` was introduced, a working draft for [CSS Filters](https://www.w3.org/TR/filter-effects-1) surfaced and, with it, a method for [drop-shadow()](https://www.w3.org/TR/filter-effects-1/#dropshadowEquivalent) that looks a lot like ```box-shadow``` at first glance. However, the two are different and it's worth comparing those differences.

```box-shadow``` 引入规范不久，就出现了有关的 [CSS Filters](https://www.w3.org/TR/filter-effects-1) 的草案，```CSS Filters``` 中有一个方法 [drop-shadow()](https://www.w3.org/TR/filter-effects-1/#dropshadowEquivalent)，第一印象让人感觉和 ```box-shadow``` 非常相似。但是，它们还是有区别的，值得比较两者的不同之处。

> For me, the primary difference came to light early on when I started working with ```box-shadow```. Here's a simple triangle not unlike the one I made back then.

对于我来说，他们之间的主要不同是我开始使用 ```box-shadow``` 后明白的。这是有一个简单的三角形，不像我之前做的那样。

[DEMO](https://codepen.io/team/css-tricks/pen/eGXMzo)

> Let's use this to break down the difference between the two.

让我用这个来对比两者之间的不同之处。

### Box Shadow

### Box Shadow

> Add a ```box-shadow``` on that bad boy and this happens.

添加一个 ```box-shadow```，就会发生以下的事情。

[DEMO](https://codepen.io/team/css-tricks/pen/boZvwv)

> It's annoying, but makes sense. CSS uses a [box model](https://www.w3.org/TR/CSS2/box.html), where the element's edges are bound in the shape of a rectangle. Even in cases where the shape of the element does not appear to be a box, the box is still there and that is was ```box-shadow``` is applied to. This was my "[ah-ha moment](https://css-tricks.com/moment-css-started-making-sense/)" when understanding the box in ```box-shadow```.

这很烦人，但也说得通。CSS 使用 [盒模型](https://www.w3.org/TR/CSS2/box.html)，DOM 的边缘被锁定在一个矩形盒子里面。尽管示例中的图形看起来并不像一个盒模型，但是盒模型还是存在的，```box-shadow``` 作用在盒模型上。这就是为什么当我理解 ```box-shadow``` 时候的[惊叹](https://css-tricks.com/moment-css-started-making-sense/)了。

### CSS Filter Drop Shadow

### CSS Filter Drop Shadow

> CSS Filters are pretty awesome. Take a gander at [all the possibilities](https://css-tricks.com/almanac/properties/f/filter/) for adding visual filters on elements and marvel at how CSS suddenly starts doing a lot of things we used to have to mockup in Photoshop.

CSS Filter 非常棒。在 DOM 上添加滤镜变的很容易，CSS 突然可以做很多我们曾经不得不用 Photoshop 模拟的事情。

> Filters are not bound to the box model. That means the outline of our triangle is recognized and the transparency around it is ignored so that the intended shape receives the shadow.

Filter 不受盒模型的约束。也就是说我们的三角形外边框是认可的，并且周围的透明部分会被忽略，阴影也会按照预期的显示。

[DEMO](https://codepen.io/team/css-tricks/pen/GMexNE)

### Deciding Which Method to Use

### 决定使用哪种方式

> The answer is totally up to you. The simple example of a triangle above might make it seem that ```filter: drop-shadow()``` is better, but it's not a fair comparison of the benefits or even the possibilities of both methods. It's merely an illustration of their different behaviors in a specific context.

答案完全取决于你。上面简单三角形的示例中显然 ```filter: drop-shadow()``` 会更好，但是，上面的示例对于两者优点和可能性的对比是不公平的。这仅仅是一个两者在特定场景下不同行为的示例。

> Like most things in development, the answer of which method to use depends. Here's a side-by-side comparison to help distinguish the two and when it might be best to choose one over the other.

就像大多数开发中事情一样，具体使用哪种方法取决于实际情况。这有个一对一的对比，有利于帮助我们决定，如何，何时，选择哪种方法更合适。

|         | Box Shadow | Drop Shadow | 
|---------|------------|-------------|
| Specification | [CSS Backgrounds and Borders Module Level 3](https://drafts.csswg.org/css-backgrounds-3/#the-box-shadow) | [Filter Effects Module Level 1](https://drafts.fxtf.org/filter-effects/#FilterProperty) |
|Browser Support|  [Great](http://caniuse.com/#feat=css-boxshadow)  |  [Good](http://caniuse.com/#feat=css-filters)  |
|Supports Spread Radius|  Yes, as an optional fourth value  |  No  |
|Blur Radius|  [Calculation is based on a pixel length](https://drafts.csswg.org/css-backgrounds-3/#shadow-blur)  |  [Calculation is based on the stdDeviation attribute of the SVG filter](https://drafts.fxtf.org/filter-effects/#dropshadowEquivalent)  |
|Supports ```inset``` shadows|  Yes  |  No  |
|Performance|  Not hardware accelerated  |  Hardware accelerated in browsers that support it. It's a heavy lift without it.  |


### Wrapping Up


> The difference between ```box-shadow``` and ```filter: drop-shadow()``` really boils down to the CSS box model. One sees it and the other disregards it. There are other differences that distinguish the two in terms of browser support, performance and such, but the way the two treat the box model is the key difference.

```box-shadow``` 和 ```filter: drop-shadow()``` 之间不同归根结底都是因为 CSS 盒模型。大家往往会忽略它。在那些区别中，比如：浏览器支持情况、性能等等，都是因为它们对盒模型不同的处理方式导致的。

> **Update:** Amelia identified another key difference [in the comments](https://css-tricks.com/breaking-css-box-shadow-vs-drop-shadow/#comment-1612592) where the spread of the radius for ```drop-shadow()``` is calculated differently than ```box-shadow``` and even that of ```text-shadow```. That means that the spread radius you might specify in ```box-shadow``` is not one-to-one with the default spread value for ```drop-shadow```, so the two are not equal replacements of one another in some cases.

**更新：**[评论中](https://css-tricks.com/breaking-css-box-shadow-vs-drop-shadow/#comment-1612592) Amelia 认为另外一个关键的不同点是：```drop-shadow()``` 在计算圆角半径是不同于 ```box-shadow``` 和 ```text-shadow``` 的。也就说：在 ```box-shadow``` 你可以指定圆角半径，但是这并不和 ```drop-shadow``` 默认值相同，因此，在某些场景下两者并不能替换。

> Let's cap this off with a few other great examples illustrating that. [Lennart Schoors also has a nice write-up](http://bricss.net/post/33158273857/box-shadow-vs-filter-drop-shadow) with practical examples using tooltips and icons that we [previously](https://css-tricks.com/box-shadow-vs-filter-drop-shadow/) called out.

让我们用一些更好的示例来说明这一切。[Lennart Schoors 也写过一篇很好的文章](http://bricss.net/post/33158273857/box-shadow-vs-filter-drop-shadow)并实现了很好的示例，示例中的 icons 我[之前](https://css-tricks.com/box-shadow-vs-filter-drop-shadow/)也提到过

[DEMO](https://codepen.io/Kseso/pen/Ajamv)

[DEMO](https://codepen.io/qnlz/pen/bWXJwg)

[DEMO](https://codepen.io/Kseso/pen/BEmev)