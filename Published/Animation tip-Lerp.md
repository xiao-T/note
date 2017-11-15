
## Animation tip: Lerp
##  动画技巧：Lerp

> 原文地址: [https://codepen.io/rachsmith/post/animation-tip-lerp](https://codepen.io/rachsmith/post/animation-tip-lerp)     
**本文版权归原作者所有，翻译仅用于学习。**

-----

> Ahhhhhhhhhhh I'm blogging again on CodePen! It's been a little while, my bad.               

我再次在CodePen上更新我的博客了！个人原因有段时间没更新了。

> Today I want to talk about a little animation trick that I use all the time called lerp. Lerp is the nickname for Linear Interpolation between two points. It's a fairly simple effect to implement but can really improve the look of your animations if you're moving an object from a point A to point B.    

今天，我想聊一聊一个有关动画的小技巧，我称它为：**Lerp**。**Lerp** 是```两点之间线性差值```的简称。这是一个相当容易实现的效果，但是如果你在A、B两点移动，会让动画体验更好。

### HOW DOES IT WORK?
### 如何工作

> Given you have a current position for an object, and a position you want to move that object towards - you can linearly interpolate to a percentage of the distance between those points, and update the position by that amount on each frame.

确定一个当前位置对象和你需要移动目标位置对象。动画的每一帧，你可以按照两点之间的距离百分比来线性的更新。

```
// to run on each frame
function lerp(position, targetPosition) {
// update position by 20% of the distance between position and target position
  position.x += (targetPosition.x - position.x)*0.2;
  position.y += (targetPosition.y - position.y)*0.2;
}
```

> By doing this, the amount the object moves becomes smaller as the distance between position and target decreases. This means the object will slow down as it gets closer to its target, creating a nice easing effect.       

这么做之后，移动的距离会随着两点之间距离减少越来越小。也就是说随着越来越接近目标位置，移动对象会慢下来，这样就有一个缓冲的效果。

### Lerp in action - some examples
### Lerp的实际应用- 一些示例

> Here is an example of a ball following the user's mouse or touch. If we make the ball move to the place the mouse is as soon as it moves, the ball movement can be very fast and/or disjointed. We might also be able to see separate "ball frames" if we move our mouse really fast.

这个示例中的球会跟着用户鼠标和触摸移动。当球移动某个位置时是非常快速的。甚至如果我们移动的够快，你会看到球会跟指针脱离。

**Demo1**    
[https://codepen.io/rachsmith/pen/avXmyV](https://codepen.io/rachsmith/pen/avXmyV)

> Here's the same demo, except this time we're using lerp. Instead of moving the ball right to the mouse position, we'll move it 10% of the distance towards that position on each frame.                  
       
这有一个相同的示例，但是这个示例中我用了 **Lerp**。再球向目标位置移动时，每次都是移动两点之间距离的10%。

**Demo2**    
[https://codepen.io/rachsmith/pen/yYZapV](https://codepen.io/rachsmith/pen/yYZapV)

> Notice the ball movement is a lot smoother and an overall more pleasing effect.    

注意⚠️球的移动平滑了很多，并且整体效果好很多。

> Here's another example of using lerp. This time we've got a scrolling indicator that updates as you scroll down the "page".    

这有另外一个示例也用了 **Lerp**。当你向下滚动页面的时候，你会看到滚动指示符的位置也在更新。

**Demo3**       
[https://codepen.io/rachsmith/pen/rOPMvz](https://codepen.io/rachsmith/pen/rOPMvz)     

**Demo4**    
[https://codepen.io/rachsmith/pen/epxdxe](https://codepen.io/rachsmith/pen/epxdxe)

>If we add lerp to this example we're lerping the indicator towards the scroll percentage instead of pointing it at the actual position - this smoothes out the movement of the indicator. I also employed this technique for a [parallax scrolling example](https://codepen.io/rachsmith/full/8ed66a170bd1a9c69e9a68b87599d136) for a [previous blog post](https://codepen.io/rachsmith/post/how-to-move-elements-on-scroll-in-a-way-that-doesn-t-suck-too-bad).

如果，你的示例中用了 **Lerp** 指示符在向前移动时会平滑很多，而不是瞬间移动到实际位置。上一片文章中的滚动视差示例中我也用了 **Lerp**

> So, the lerp "trick" is a great tool to have up our web animation sleeves to combat linear or jagged movement. If you have used or do use lerp in your CodePens be sure to share your examples with us in the comments below!

**Lerp** 能够很好让我们的web动画平滑的移动来避免瞬间移动。如果，你在CodePens的示例中也使用了 **Lerp**，请一定要在评论中分享。