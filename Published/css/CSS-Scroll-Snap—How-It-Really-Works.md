# CSS Scroll Snap — How It Really Works

## CSS Scroll Snap 的工作模式

> 原文地址: <https://blog.usejournal.com/css-scroll-snap-how-it-really-works-94d99db80bc9/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

Remember the days you needed JS to have a nice looking scrolling on your page (gallery, slide show etc.)? Say bye bye to JS and say hi to CSS Scroll Snap.

还记不记得，为了在页面上实现优雅的滚动效果（gallery, slide 等等）需要借助 JS 的那些日子？送走 JS，投入 CSS Scroll Snap 怀抱吧。

![](https://miro.medium.com/max/1400/1*p_Gok8n-msYcAwnIwows8Q.png)

Long time ago, while CSS was on level 1, we’ve introduced with a property to scroll items and snap them to their container boundaries. But just Firefox had a support of this. But nowadays, we have **`FULL BROWSERS`** support. Let’s dive in and abandon the JS to make pure CSS scrolling job.

很久以前，当时 CSS 还处于第一阶段，我们就介绍过 CSS Scroll Snap，它可以让滚动的元素紧贴容器的边界。但是，只有 Firefox 支持。到了现在，**`所有的浏览器`**都得到了支持。我们来拥抱它，放弃 JS，实现纯 CSS 的滚动效果。

## What is it good for?
## 它有什么好处？

Let’s say that we have to compare items on our website. We need an item to compare with a list of items. We don’t know the length of the list but we want to see them side by side with the main item. The list is long and have a scroll (let’s say on the X axis) and we want to see each item on the list by it’s full size and not part of it while scrolling.

假设，我们需要在页面上对比一些内容。我们需要某一个项目和一个列表中所有的项目一一对比。我们不知道列表的长度，但是，我们想把整个列表中的项目和需要对比的项目并排在一起。列表太长，需要滚动（假设是在 X 轴滚动），每当页面滚动时，我们想看到的是每个项目完整的尺寸，而不是一部分。

Without this CSS property, if we’ll scroll the list, we need to be gentle with the scrolling and bring the item as close as we can to the boundaries of the container. But with this property, we just need to scroll it a just over the 50% and the browser will complete the scrolling till the position we wanted.

在没有这个 CSS 属性的情况下，如果，我们需要滚动列表，必须小心翼翼的，尽可能的让某一元素贴近容器的边界。但是，通过利用这个 CSS 属性，我们只需滚动元素的 50%，然后，剩下的交给浏览器处理就好。

The best example will be if you’ll take your mobile device and scroll your homepage / list of apps to left, right, up or down a bit. The screen will slide to the next “page” (if you have) and you don’t need to fully scroll it. This is the same with this CSS property.

如果，你在的移动设备上，向左、向右、向上或者向下滚动一点。整个屏幕将会滚动到”下一页“（如果，有的情况下），你并不需要滚动整个页面。这将是这个属性最好的演示场景。

## Scroll snap type

With CSS scroll type property, we are declaring that the **`container`** has a **`scrolling items`** and they need to be snap to the container boundaries. We can set the snap boundaries to *`none`, `x`, `y`, `block`, `inline` or `both`*

我们定义了 **`container`** 有一组 **`scrolling items`** 元素，通过设置 CSS Scroll Type 属性，让它们在滚动时紧贴容器的边界。我们有以下几个值可选：*`none`, `x`, `y`, `block`, `inline`* 或者 *`both`*

![](https://miro.medium.com/max/1400/1*jXyv4m4mrifE5N2D4z8gJA.png)

[codepen.io](https://codepen.io/chaofix/pen/ae118504b6d7185cb9fcbe92ac9e97a7)

The difference between the options is:

这些选项之间的区别是：

- none — no snap

  none — 不需要紧贴边界

- x — snap to x axis

  x — 紧贴 x 轴边界

- y — snap to y axis

  y — 紧贴 y 轴边界

- block — snap to block axis (logical properties) — vertical axis (Same as Y axis)

  block — 紧贴 block 轴（逻辑属性）— 垂直轴（等同于 Y 轴）

- inline — snap to inline axis (logical properties) — horizontal axis (Same as X axis)

  inline — 紧贴 inline 轴（逻辑属性）— 水平轴（等同于 X 轴）

- both — snap to x and y (or inline and block) axis

  both — 紧贴 x 和 y 轴

![](https://miro.medium.com/max/1400/1*jo7P0Nnc6C9mV6Z5caT-EA.png)

There is another value that you need to declare on “scroll-snap-type” and you can choose between:

scroll-snap-type 中还有另外一个属性需要设置，它的值包括：

- mandatory
- proximity

To make it simple, mandatory will continue the scrolling as soon as the item will cross 51% of it’s width (on x / inline axis) or height (on y / block axis) while proximity need the item will scroll almost to it’s entire width or height.

简单的理解就是：值为 mandatory 时，当元素滚动到自身宽度（x/inline 轴）或者高度（y/block 轴）51% 时会自动的滚动，然后，值为 proximity 时，滚动需要接近整个元素的大小才会自动贴近边界。

（*译者注：对比了两种效果，没看出有什么差别。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/scroll-snap-type)上的翻译也比较难以理解😂*）

The syntax (and values) for scroll-snap-type will be:

scroll-snap-type 的语法和选值如下所示：

```
scroll-snap-type: none | [ x | y | inline | block ] [ mandatory | proximity ];
```

Now that we know **`where`** the items will be snap to, we can define **`how`** they will snap with scroll-snap-align on the scrolling items

现在，我们知道了滚动元素**`在哪`**紧贴边界，接下来，我们可以在滚动元素上设置 scroll-snap-align 属性来决定**`如何`**紧贴边界。

## An iOS fix
## 修复 iOS 的问题

Thanks to [Denys Mishunov](https://twitter.com/mishunov) that raised this fix for iOS users
To support iOS users, just add the following line

感谢 [Denys Mishunov](https://twitter.com/mishunov) 为 iOS 用户提供了这个修复方案

为了支持 iOS 用户，只需添加下面一行代码

```css
-webkit-overflow-scrolling: touch;
```

So the code will look like following:

因此，我们的代码应该是这个样子：

```css
.foo {
     scroll-snap-type: x mandatory;
     -webkit-overflow-scrolling: touch;
}
```

## Scroll snap align

We can define the snapping to `start`, `center` or `end`

我们可以定义滚动元素如何紧贴边界，有以下几个选择：`start`, `center` 或 `end`

To do that, we need to define “scroll-snap-align” on the items themselves.

为了实现相应的效果，我们需要给每个滚动元素定义 “scroll-snap-align”。

If we’ll define “scroll-snap-align: start” the items will be snap to the start of the container boundaries (according the axis we choose)

如果，我们给滚动的元素定义：“scroll-snap-align: start”，那么它将会紧贴容器的边界开始位置（根据我们选择的轴来定义）

![](https://miro.medium.com/max/1400/1*NzWF7ME484zD5uA08VxoKg.png)

If we’ll declare “scroll-snap-align: center;” the items will be shown on the center of the container and we’ll see the edges of the surrounding items — as you can see on the image above.

如果，我们声明了 “scroll-snap-align: center;”，那么滚动元素将会在容器中间显示，边界线围绕在元素的周围—就像你在上图中看到的一样。

In the following examples you can see 3 examples of scroll-snap-type `X`, `Y` and `Both` — All of them will snap to "start".

接下来的三示例分别对应这 scroll-snap-type 的三个不同选值：`X`, `Y` 和 `Both` — 它们的 scroll-snap-align 值都为 ~~"start"~~"center"。

[X 轴](https://codepen.io/chaofix/pen/7a8dad4299b4f0a035d7859a849d89e7)

[Y 轴](https://codepen.io/chaofix/pen/b7a81fd833f3ab1fd7d190e74a613231)

[both](https://codepen.io/chaofix/pen/206d845358b7232d935d052eb4c240e1)

## Scroll margin / Scroll padding

Both of these properties — scroll-padding and scroll-margin — are acting the same as padding / margin. They let us scroll with a space.

scroll-padding 和 scroll-margin 就相当于 padding / margin。可以控制滚动元素和边界之间的留白。

For example, using the property “scroll-padding: 10px;” will scroll the items one by one **but** with a space of 10px from the snap type (x, y, block, inline or both) boundaries

例如：设置 “scroll-padding: 10px;”，滚动元素跟容器边界之间会有个 10px 的留白。

>**These properties don’t have full supported these days**
>
>**目前，这些属性并没有完全支持**

## Browser support

According to “[caniuse](https://caniuse.com/#feat=css-snappoints)” all of the browsers are supporting this property.

根据 “[caniuse](https://caniuse.com/#feat=css-snappoints)” 记录可以知道，现在所有的浏览器都已经支持这一属性

- IE / Edge needs the prefix -ms-

  IE / Edge 需要前缀 -ms-

- Firefox is using an old writing of this property

  Firefox 中可以用旧的语法

![](https://miro.medium.com/max/1400/1*kdbqfSTIGE9YKret_-dErQ.png)

On the last example you can see a “slideshow” with both of the scrolling (X and Y)

最后一个示例，你将会看到一个可以垂直/水平滚动的幻灯片效果。

[slides demo](https://codepen.io/chaofix/pen/6535aca9fcfa4d0ea8aca690181cc128)

## Last words
## 总结

- Using this CSS property is easy

  CSS 属性更加容易使用

- We can create scrolling galleries, slide shows, entire pages and so on quickly and easy

  我们可以快速轻松的创建滚动图库、幻灯片、全屏滚动等效果

- There is an issue — in my opinion — if one of the items is much bigger than the container, we can’t see the whole item — a scroll will bring us to the next item

  在我们看来，依旧会有个小问题。如果，其中一个元素比容器还要大，我们并不能看到它的全部—滚动的时候将会跳转到下一个元素。