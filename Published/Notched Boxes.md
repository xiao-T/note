## Notched Boxes
## 切角盒子

> 原文地址: <https://css-tricks.com/notched-boxes/>
**本文版权归原作者所有，翻译仅用于学习。**

---------

Say you're trying to pull off a design effect where the corner of an element are cut off. Maybe you're [a Battlestar Galactica fan](https://twitter.com/mikeBithell/status/942679894173147138)? Or maybe you just like the unusual effect of it, since it avoids looking like a typical rectangle.

假设你正尝试实现一个带切角的效果。或许你是 [Battlestar Galactica](https://twitter.com/mikeBithell/status/942679894173147138) 迷?或许你只是喜欢不寻常效果，让它看起来不一样。

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_910,f_auto,q_auto/v1520269162/notched_l1myqr.png)

I suspect there are many ways to do it. Certainly, you could use [multiple backgrounds](https://css-tricks.com/css-basics-using-multiple-backgrounds/) to place images in the corners. You could just as well use a flexible SVG shape placed in the background. I bet there is also an exotic way to use gradients to pull it off.

我猜测有多种方式实现这种效果。当然，你可以使用 [multiple backgrounds](https://css-tricks.com/css-basics-using-multiple-backgrounds/) 在切角的地方放置图片。你也可以使用弹性的 SVG shape 做为背景。我敢打赌用渐变也可以实现。

But, I like the idea of simply taking some scissors and clipping off the dang corners. We essentially can do just that thanks to [clip-path](https://css-tricks.com/almanac/properties/c/clip/). We can use the ```polygon()``` function, provide it a list of ```X``` and ```Y``` coordinates and clip away what is outside of them.

但是，我更喜欢用剪刀剪裁切角的简单想法。用 [clip-path](https://css-tricks.com/almanac/properties/c/clip/) 就可以实现。我们可以用 ```polygon()``` ，提供一系列的 ```X```、 ```Y``` 坐标，然后裁掉它们以外的部分。

Check out what happens if we list three points: middle top, bottom right, bottom left.

如果我们列出三个点：中上，右下，左下。看看会发生什么。

```css
.module {
  clip-path: 
    polygon(
      50% 0,
      100% 100%,
      0 100%
    );
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1520269284/triangle_ydvhy4.png)

Instead of just three points, let's list all eight points needed for our notched corners. We could use pixels, but that would be dangerous. We probably don't really know the pixel width or height of the element. Even if we did, it could change. So, here it is using percentages:

让我们列出所有的8个点代替这3个点。我们可以使用像素单位，但是会有问题。我们很可能不知道 DOM 真实的 width、height。一旦我们这么做，必然会有改变。所以，我们用百分比：

```css
.module {
  clip-path: 
    polygon(
      0% 5%,     /* top left */
      5% 0%,     /* top left */
      95% 0%,    /* top right */
      100% 5%,   /* top right */
      100% 95%,  /* bottom right */
      95% 100%,  /* bottom right */
      5% 100%,   /* bottom left */
      0 95%      /* bottom left */
    );
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_892,f_auto,q_auto/v1520269676/all-percentage-notches_xkmthu.png)

That's OK, but notice how the notches aren't at perfect 45 degree angles. That's because the element itself isn't a square. That gets worse the less square the element is.

好了，但是，为什么切角不是完美的45度角呢。这是因为 DOM 不是正方形。如果 DOM 越接近正方形误差越小。

We can use the ```calc()``` function to fix that. We'll use percentages when we have to, but just subtract from a percentage to get the position and angle we need.

我们可以用 ```calc()``` 解决这个问题。我们使用百分比，只需减去我们需要的角度就可以得到正确的位置。

```css
.module {
  clip-path: 
    polygon(
      0% 20px,                 /* top left */
      20px 0%,                 /* top left */
      calc(100% - 20px) 0%,    /* top right */
      100% 20px,               /* top right */
      100% calc(100% - 20px),  /* bottom right */
      calc(100% - 20px) 100%,  /* bottom right */
      20px 100%,               /* bottom left */
      0 calc(100% - 20px)      /* bottom left */
    );
}
```

And you know what? That number is repeated so many times that we may as well make it a [variable](https://css-tricks.com/making-custom-properties-css-variables-dynamic/). If we ever need to update the number later, then all it takes is changing it once instead of all those individual times.

你看到了吧？有个数字重复了多次，我可以把它变成一个[变量](https://css-tricks.com/making-custom-properties-css-variables-dynamic/)。如果，以后我们需要改变它，我们只需改变一个地方，其他就会自动更新。

```css
.module {
  --notchSize: 20px;
  
  clip-path: 
    polygon(
      0% var(--notchSize), 
      var(--notchSize) 0%, 
      calc(100% - var(--notchSize)) 0%, 
      100% var(--notchSize), 
      100% calc(100% - var(--notchSize)), 
      calc(100% - var(--notchSize)) 100%, 
      var(--notchSize) 100%, 
      0% calc(100% - var(--notchSize))
    );
}
```

[Ship it](https://codepen.io/chriscoyier/pen/yvQQxM).


This may go without saying, but make sure you have enough padding to handle the clipping. If you wanna get really fancy, you might use CSS variables in your padding value as well, so the more you notch, the more padding there is.

确保有足够大的 padding 保证用来剪裁，这或许不用说。如果，你想变得更加灵活，你应该用 CSS 变量存储 padding 的值，切角越大，padding 就越大。
