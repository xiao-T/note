## CSS Tip: Multicolor & Cutout Drop Shadows
## CSS 技巧：色彩斑斓的自定义阴影

> 原文地址: <https://alligator.io/css/css-background-drop-shadows/>      
**本文版权归原作者所有，翻译仅用于学习。**

---------

> Box shadows are boring. You heard me. They can only use one color at a time and are [slow to animate or transition](https://alligator.io/css/transition-box-shadows). You know what would be neat? Making a drop shadow that would use the colors of the element’s background. So if the top left corner of your element was red, the top left corner of the shadow would be red. If the bottom right corner was blue, the bottom left corner of the shadow would be blue. Wouldn’t that be neat? Too bad there’s no way to do it in CSS… Oh wait, **there is**. Let’s dive in.

> 盒子阴影很无聊。你听我的。他们一次只能用一种颜色，并且只能通过[animate 或者 transition 实现动画](https://alligator.io/css/transition-box-shadows)。你知道什么是整齐划一么？让阴影的颜色和元素的背景色保持一致。也就是说，如果元素左上角是红色，阴影的左上角也必须是红色；如果右下角是蓝色，阴影的右下角也必须是蓝色。这就是整齐划一。糟糕的是，在 CSS 中无法实现。等等，**有方法可以实现**。让我们一起来看看。

### The Method

### 方法

Really, all we have to do is create a pseudo-element directly behind the parent element and have it inherit relevant properties, such as ```background``` and ```border-radius```. Then we add a CSS ```blur()``` filter. This creates a second copy of the element’s background and blurs it behind the element. Easy as pie. By inheriting the background and related properties, we don’t have to specify them repeatedly.

事实上，我们只需创建一个伪元素直接放置父元素后面，并继承相关属性，比如：```background``` 和 ```border-radius```。然后，我们给伪元素添加一个滤镜 ```blur()```。这会在元素后面生成一个模糊的副本。很简单吧。因为继承了背景和相关属性，我们不需要重复设置。

Even better, if you want to animate / smoosh / squash the shadow in some way, you have the full power of CSS transforms and opacity available to you on the pseudo-element!

还可以更好，如果你想用某些方式让阴影实现平滑的动画效果，CSS 中的 transforms 和 opacity 属性在伪元素上完全有效。

What’s the catch? **The shadow won’t display in [browsers that don’t support the CSS blur filter](https://caniuse.com/#feat=css-filters)**.

有什么问题么？**[阴影在不支持的 blur 滤镜的浏览器上不显示](https://caniuse.com/#feat=css-filters)**

### The Code

### 代码

```css
/* The element that will have the blurred shadow. */
.fancy-box {
  /* We need to set the position value to something other than `static`
   * so that position: absolute for the pseudo-element will be relative
   * to this element. */
  position: relative;

  /* A nice Alligator.io-themed linear gradient. */
  /* This technique works with any and every background type. */
  background: linear-gradient(to bottom right, rgb(107,183,86), rgb(0,143,104));

  /* Whatever else you want in here. */
}

/* The shadow pseudo-element. */
.fancy-box::before {
  content: ' ';
  /* Position it behind the parent. */
  z-index: -1;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  /* Inherit the background and border radius of the parent. */
  background: inherit;
  border-radius: inherit;

  /* Blur it for maximum shadowy-ness. */
  filter: blur(15px);

  /* Hide the shadow until the element is hovered over. */
  opacity: 0;
  transition: opacity 300ms;
}

/* Show the shadow on hover. */
.fancy-box:hover::before {
  opacity: 1;
}
```

### The Result

### 演示结果

Note how it even works with transparent images! You can finally make shaped shadows! 🎉

留意下透明图片的那个是如何工作的！最终你可以实现多边形的阴影！🎉

[Here's a codepen you can play around with](https://codepen.io/tribex/pen/wmjRNe).

[查看 codepen 上的演示](https://codepen.io/tribex/pen/wmjRNe).

So how’s the performance? Well, as long as you don’t frequently change the background or adjust the blur radius, it’s about as fast as could be! Overall the performance characteristics of this technique are comparable to the method we showed you earlier for [performant box shadow transitions](https://alligator.io/css/transition-box-shadows).

那性能如何呢？好吧，只要你不频繁的改变背景或者调整模糊半径，性能是没有问题的！总体来说这种技术的性能跟早期我们展示的[box-shadow 过渡效果](https://alligator.io/css/transition-box-shadows)是相当的。

