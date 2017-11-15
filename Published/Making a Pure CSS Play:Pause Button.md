## Making a Pure CSS Play/Pause Button
## 纯 CSS 播放／暂停 ICON

> 原文地址: [https://css-tricks.com/making-pure-css-playpause-button/](https://css-tricks.com/making-pure-css-playpause-button/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> Globally, the media control icons are some of the most universally understood visual language in any kind of interface. A designer can simply assume that every user not only knows ▶️ = play, but that users will seek out the icon in order to watch any video or animation.

在全球范围内，媒体控制图标在用户交互界面中最常见的。设计师可以简单的假设用户不但知道 ▶️ = 播放，而且他们也会根据图标来确认并观看视频和动画。

> [Reportedly introduced in the 1960s by Swedish engineer Philip Olsson](https://www.reddit.com/r/explainlikeimfive/comments/1tasqz/eli5_why_is_the_play_button_a_sideways_triangle/) the play arrow was first designed to indicate the direction where the tape would go when reading on reel-to-reel tape players. Since then, we switched from cassettes to CDs, from the iPod to Spotify, but the media controls icons remain the same.

[在20世纪60年代](https://www.reddit.com/r/explainlikeimfive/comments/1tasqz/eli5_why_is_the_play_button_a_sideways_triangle/)，播放箭头第一次由瑞典工程师 Philip Olsson 用来指示磁带播放器的播放方向。自从那时，一直延续到 CD、iPod、Spotify，媒体控制图标一直保持不变。

> The play ▶️ icon is standard symbol (with its own unicode) of starting an audio/video media along with the rest of the symbols like stop, pause, fast-forward, rewind, and others.

播放 ▶️ 图标是标准的启动音频／视频的符号（它拥有自己的 unicode），就像其它的符号，比如：停止，暂停，快进，回退等等也同样拥有属于自己的 unicode。


> There are unicode and emoji options for play button icons, but if you wanted something custom, you might reach for an icon font or custom asset. But what if you want to shift between the icons? Can that change be smooth? One solution could be to use SVG. **But what if it could be done in 10 lines of CSS?** How neat is that‽

unicode 和 emoji 中都有播放图标，但是，如果你想自定义，你可以使用字体图标或者其它素材。但是如果你想在图标之间切换呢？能平滑过渡么？SVG 是一种解决方案。**但是，如果能用10行 CSS 代码实现？** 是不是更好呢‽

> In this article, we'll build both a play button and a pause button with CSS and then explore how we can use CSS transitions to animate between them.

在这篇文章中，我将用 CSS 创建两个图标：播放和暂停按钮，并演示一下，如何使用 CSS transitions 让它俩平滑切换。

### Play Button

### 播放按钮

#### Step one

#### 第一步

> We want to achieve a triangle pointing right. Let's start by making a box with a thick border. Currently, boxes are the preferred base method to make triangles. We'll start with a thick border and bright colors to help us see our changes.

我们想实现一个指向右边的三角符号。先让我们创建一个有很大边框的方形盒子。目前，方形盒子是制作三角符号最基本的方法。一开始我会大边框和鲜亮的颜色，以便我们观察变化。

```html
<div class='button play'></div>
```

```css
.button.play {
  width: 74px;
  height: 74px;
  border-style: solid;
  border-width: 37px;
  border-color: #202020;
}
```
![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_322,f_auto,q_auto/v1507227397/play-pause-in-css_1_oxihom.png)

#### Step two

#### 第二步

> Rendering a solid color border yields the above result. Hidden behind the color of the border is a neat little trick. How is the border being rendered exactly? Let's change the border colors, one for each side, will help us see how the border is rendered.

以上就是一个渲染成纯色实体边框的结果。有一个小技巧：可以不设置 ```border-color```（**译者注：默认是黑色**）。边框是如何渲染的呢？让我们改变一下边框每个边的颜色，能让我们更好理解它是如何渲染的。

```html
<div class='button play'></div>
```

```css
.button.play{
  width: 74px;
  height: 74px;
  border-style: solid;
  border-width: 37px 37px 37px 37px;
  border-color: red blue green yellow;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_320,f_auto,q_auto/v1507227412/play-pause-in-css_2_ea93sg.png)

#### Step three

#### 第三步

> At the intersection of each border, you will notice that a 45-degree angle forms. This is an interesting way that borders are rendered by a browser and, hence, open the possibility of different shapes, like triangles. As we'll see below, if we make the ```border-left``` wide enough, it looks as if we might achieve a triangle!

在每个边框的连接处，你会看到一个45度角。这是浏览器渲染边框有意思的地方，于是，这就打开了实现其它形状的可能性，比如：三角形。让我们看下面，如果让 ```border-left``` 足够宽，看起来就像一个三角形！

```html
<div class='button play'></div>
```

```css
.button.play {
  width: 74px;
  height: 74px;
  border-style: solid;
  border-width: 37px 0px 37px 74px;
  border-color: red blue green yellow;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_324,f_auto,q_auto/v1507227426/play-pause-in-css_3_ertpwb.png)

#### Step four

#### 第四步

> Well, that didn't work as expected. It is as if the inner box (the actual div) insisted on keeping its width. The reason has to do with [the box-sizing property](https://css-tricks.com/box-sizing/), which defaults to a value of ```content-box```. The value ```content-box``` tells the div to place any border on the ***outside***, increasing the width or height.

但是，这并没有按照预期的显示。这是因为 div 有 width。这是因为[属性 ```box-sizing```](https://css-tricks.com/box-sizing/) 默认的值是 ```content-box```。```content-box``` 的意思是：任何的 border 是额外计算的，最终 DOM 的整体宽高增加了。

> If we change this value to ```border-box```, the border is added to the ***inside*** of the box.

如果，我们把这个值设置为 ```border-box```，这样，边框的大小并不影响 DOM 的整体大小。

```html
<div class='button play'></div>
```

```css
.button.play {
  box-sizing: border-box;
  width: 74px;
  height: 74px;
  border-style: solid;
  border-width: 37px 0px 37px 74px;
  border-color: red blue green yellow;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_168,f_auto,q_auto/v1507227443/play-pause-in-css_4_mhk99j.png)

#### Final step

#### 最后一步

> Now we have a proper triangle. Next, we need to get rid of the top and bottom part (red and green). We do this by setting the ```border-color``` of those sides to ```transparent```. The widths also gives us control over the shape and size of the triangle.

现在，我们已经得到一个正常三角形。接下来，让我们去掉顶部和底部（红色和绿色）。我们可以把边框的 ```border-color``` 设置为 ```transparent```。宽度可以控制三角形的大小和形状。

```html
<div class='button play'></div>
```

```css
.button.play {
  box-sizing: border-box;
  width: 74px;
  height: 74px;
  border-style: solid;
  border-color: #202020;
  border-width: 37px 0px 37px 74px;
  border-color: transparent transparent transparent #202020;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_168,f_auto,q_auto/v1507227463/play-pause-in-css_5_eqomuf.png)

> [Here's an animation to explain that](https://codepen.io/chriscoyier/full/lotjh), if that's helpful.

[这是一个很有帮助的动画演示](https://codepen.io/chriscoyier/full/lotjh)。

### Pause Button

### 暂停按钮

#### Step one

#### 第一步

> We'll continue making our pause symbol by starting with another thick-bordered box since the previous one worked so well.

借鉴上一个示例的经验我还是继续使用大边框的方形盒子继续制作暂停符号。

```html
<div class='button pause'></div>
```

```css
.button.pause {
  width: 74px;
  height: 74px;
  border-style: solid;
  border-width: 37px;
  border-color: #202020;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_322,f_auto,q_auto/v1507227475/play-pause-in-css_6_k8iyco.png)

#### Step two

#### 第二步

> This time we'll be using another CSS property to achieve the desired result of two parallel lines. We'll change the ```border-style``` to ```double```. The ```double``` property in ```border-style``` is fairly straightforward, doubles the border by adding a transparent stroke in between. The stroke or empty gap will be 33% of the given border width.

这次我们将使用另外一个属性去实现两条平行线的效果。设置 ```border-style： double```。浏览器对 ```double``` 的处理也相当简单，直接在边框上添加了一条透明区域。这样正好让边框平均分为三份。

```html
<div class='button pause'></div>
```

```css
.button.pause{
  width: 74px;
  height: 74px;
  border-style: double;
  border-width: 0px 37px 0px 37px;
  border-color: #202020;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_338,f_auto,q_auto/v1507227492/play-pause-in-css_7_xd0mz7.png)

#### Final step

#### 最后一步

> ```border-width``` property. Using the ```border-width``` is what will make the transition work smoothly in the next step.

```border-width``` 属性。用这个属性可以让接下来的变换更加顺畅。

```html
<div class='button pause'></div>
```

```css
.button.pause{
  width: 74px;
  height: 74px;
  border-style: double;
  border-width: 0px 0px 0px 37px;
  border-color: #202020;
}
```

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_118,f_auto,q_auto/v1507227504/play-pause-in-css_8_weyyye.png)

### Animating the Transition

### 过度动画

> In the two buttons we created above, notice that there are a lot of similarities, but two differences: ```border-width``` and ```border-style```. If we use CSS transitions we can shift between the two symbols. There's no transition effect for ```border-style``` but ```border-width``` works great.

在上面，我们创建了两个按钮，它们有很多相似之处，也有不同，比如：```border-width``` 和 ```border-style```。利用 CSS transitions 我们可以在两个 icon 之间切换。```border-style``` 是没有切换效果的，但是，```border-width``` 有。

> A ```pause``` class toggle will now animate between the play and pause state.

通过一个类 ```pause``` 切换两个按钮的状态。

> Here's the final style in SCSS:

最终，我用到了 SCSS。

```html
<div class='button'></div>
```

```css
.button {
  box-sizing: border-box;
  height: 74px;
  
  border-color: transparent transparent transparent #202020;
  transition: 100ms all ease;
  will-change: border-width;
  cursor: pointer;

  // play state
  border-style: solid;
  border-width: 37px 0 37px 60px;

  // paused state
  &.pause{
    border-style: double;
    border-width: 0px 0 0px 60px;
  }
}
```

[Demo1](https://codepen.io/xihadd/pen/mBqBqM)

### A Bonus Without JS

### 不使用 JS

> In the demo above, I used JavaScript to toggle the classes between play state and pause state. This works perfectly, but what if you don't want to use JavaDScript（**译者注：原作者笔误，应该是 JavaScript**） at all? Let's take it a step further and [use an HTML checkbox](https://css-tricks.com/the-checkbox-hack/) and skin it with the CSS we just used. We’ll use the ```checked``` state of the checkbox to toggle between play and pause:

上面的例子中，我用了 JavaScript 切换两个按钮的状态。这么处理效果非常好，但是，如果你不想用 JavaScript 呢？让我们更进一步，[使用 HTML checkbox](https://css-tricks.com/the-checkbox-hack/) 配合上面的 CSS。通过 checkbox 的 ```checked``` 属性切换两个状态。

```html
<!-- 这个地方原作者demo有点问题  -->
<div class="playpause">
  <input type="checkbox" value="" id="playPauseCheckbox" name="playPauseCheckbox" />
  <label for="playPauseCheckbox"></label>
</div>
```

```css
.playpause {
  label {
    display: block;
    box-sizing: border-box;
    
    width: 0;
    height: 74px;
    
    cursor: pointer;

    border-color: transparent transparent transparent #202020;
    transition: 100ms all ease;
    will-change: border-width;
    
    // paused state
    border-style: double;
    border-width: 0px 0 0px 60px;
  }
  input[type='checkbox'] {
    visibility: hidden;
    
    &:checked + label {
      // play state
      border-style: solid;
      border-width: 37px 0 37px 60px;
    }
  }
}
```

[Demo2](https://codepen.io/xihadd/pen/oGoGMB)

> I would love your thoughts and feedback. Please add them in the comments below.

我很希望得到你们的想法和反馈。欢迎在下面添加评论。