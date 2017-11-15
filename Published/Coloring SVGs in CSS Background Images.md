## Coloring SVGs in CSS Background Images
## 如何设置 CSS 背景图中的 SVG 的颜色

> 原文地址: [https://codepen.io/noahblon/post/coloring-svgs-in-css-background-images](https://codepen.io/noahblon/post/coloring-svgs-in-css-background-images)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> I love using ```SVG``` in ```CSS``` background images but it sucks that you can't alter the ```fill``` color easily within your ```CSS```. Here are a few ways around that.

在 ```CSS``` 背景中我喜欢用 ```SVG```，但是，这会导致你无法用 ```CSS``` 很好改变它们的颜色。围绕这个问题这有几个方法。

> ### ```SVG``` in ```CSS``` backgrounds

```CSS``` 背景中的 ```SVG```

> Using ```SVG``` in ```CSS``` backgrounds allows you to use CSS's powerful background sizing and position properties. This makes sizing SVGs much simpler because the image easily scales to the size of your element. Plus you don't have ```SVG``` cluttering up your markup.

```CSS``` 背景中的 ```SVG``` 也可以通过 ```background-size``` 和 ```background-position``` 很好的控制。相对图片，```SVG``` 更容易控制大小。而且，```SVG```标签并不会混入 ```CSS``` 中。

> There are also some nice performance benefits over inline ```SVG```. An ```SVG``` in a background image can be cached. Using image sprites and embedding ```SVG``` as a ```data URI``` can also improve performance.

内嵌 ```SVG``` 对性能优化也是有帮助的。它是可以被缓存的。并且通过雪碧图或者 ```data URI``` 内嵌 ```SVG``` 也是可以提升性能的。

### CSS masks

> This is my favorite method, but your browser support must be very progressive. With the ```mask``` property you create a mask that is applied to an element. Everywhere the mask is opaque, or solid, the underlying image shows through. Where it’s transparent, the underlying image is masked out, or hidden. The syntax for a CSS ```mask-image``` is similar to ```background-image```.

这是我最喜欢的方法，但是需要浏览器的支持。通过 ```mask``` 属性创建一个遮罩层，应用于某些元素。通过控制它的透明度来控制被遮罩元素的显示隐藏。如果，遮罩是透明的，被盖住的元素就会显示，否则会隐藏。```mask-image``` 的语法类似 ```background-image```。

```css
.icon {
    background-color: red;
    -webkit-mask-image: url(icon.svg);
    mask-image: url(icon.svg);
}
```

> Here I'm setting an SVG as the mask. The fill of the icon in the SVG doesn't matter because it masks the background layer which is the color red. Therefore, the result is a red icon. For webkit, I'm using the prefix.

我用 ```SVG``` 设置了一个遮罩。无需关心 ```SVG``` 是什么颜色，最终的颜色是由 ```background``` 决定的。以上代码，最终结果是红色的。为了兼容 ```webkit```，我用了前缀。

> Your background can be a color, image, gradient -- whatever.

你的背景可以是任何你想设置的，比如：纯色、图片、渐变。

[Demo1](https://codepen.io/noahblon/pen/wzsyC)

> There are a bunch of properties related to ```mask```, such as ```mask-position```, ```mask-repeat```, and ```mask-size```, which align with ```CSS background``` properties. You can use the ```mask``` shorthand like the ```background``` shorthand:

有一些列属性是和 ```mask``` 相关的，比如：```mask-position```、```mask-repeat``` 和 ```mask-size```。这些属性和 ```background``` 属性是一样的，你可以使用类似 ```background``` 的简写语法。

```css
.icon {
    background-color: red;
    -webkit-mask:  url(icon.svg) no-repeat 50% 50%;
    mask: url(icon.svg) no-repeat 50% 50%;
}
```

> Masks are supported in most browsers. The IE team have CSS masks listed as under consideration. Firefox currently doesn't support external SVG masks.

```mask``` 得到了大部分浏览器的支持。IE 的团队也在考虑中。Firefox 当前是不支持的外部 ```SVG``` 的。

**（译者注：随着时间的推移对 ```mask``` 的支持情况会有改善，最终以具体浏览器实现为准，也可以关注 [Caniuse](http://caniuse.com/#feat=css-masks)）**

- [http://status.modern.ie/masks](http://status.modern.ie/masks)
- [http://caniuse.com/#feat=css-masks](http://caniuse.com/#feat=css-masks)

### CSS filters

> You can apply Photoshop-like effects to DOM elements with CSS filters. Filters can be chained, with each filter acting on the result of its predecessor.

像 Photoshop 那样在 ```CSS``` 中也可以使用 ```filter```。```filter``` 的值是链式的，滤镜的效果是跟具体的值的前后顺序有关的。  

**(译者注：这一点和 ```transform``` 一样)**

```html
<svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewbox="0 0 24 24">
    <path fill="red" d="M12 21.35l-1.45-1.32c-5.15-4.67-8.55-7.75-8.55-11.53 0-3.08 2.42-5.5 5.5-5.5 1.74 0 3.41.81 4.5 2.09 1.09-1.28 2.76-2.09 4.5-2.09 3.08 0 5.5 2.42 5.5 5.5 0 3.78-3.4 6.86-8.55 11.54l-1.45 1.31z"/>
</svg>
```

```css
.icon-blue {
    -webkit-filter: hue-rotate(220deg) saturate(5);
    filter: hue-rotate(220deg) saturate(5);
}
```

> In this example, the icon has a pure red fill set in the SVG. The ```hue-rotate``` filter rotates the hue 220 degrees around the rgb color wheel. This makes the icon blue. The algorithm for hue-rotation isn't extremely accurate, so although the output should be pure blue it’s a little off. One way to fix this and boost up the color to full saturation is to add a ```saturation``` filter. In the example, I added an arbitrarily large value of five to the chain, which increases the saturation by 500%.

在这个示例中，```SVG``` 是纯红色的。```filter: hue-rotate(220deg)``` 代表：色调在RGB色盘上转旋220度。这样，```icon``` 就变成了蓝色。```hue-rotate``` 的算法并不是那么准确，所以输出的蓝色是有色差的。通过设置 ```saturation``` 属性调整颜色的饱和度是可以修复的。在示例中，我添加了一个足大的值，它可以提到5倍的饱和度。

> By chaining filters together, you can make many colored icons from one colored icon input. With different combinations of ```hue-rotate```, ```invert```, ```brightness```, and ```saturation``` filters, I've created the ROYGBIV rainbow spectrum and some other basic colors like cyan and magenta.

通过组合不同的 ```filter``` 属性值，你可以变换不同颜色的 ```icon```。通过组合 ```hue-rotate```、```invert```、```brightness``` 和 ```saturation```，我创建了一个彩虹光谱：ROYGBIV 和一些基本颜色，比如：青色和洋红。

**(译者注：ROYGBIV 下面 Demo 中第一行色值的简称。)**

[Demo2](https://codepen.io/noahblon/pen/HxKpJ)

> Creating a grayscale set of filters is pretty easy. You add a ```grayscale``` filter and adjust a ```brightness``` filter to the amount of gray you need.

创建灰色的过滤器非常简单。你只需添加一个 ```grayscale``` 属性，并通过 ```brightness``` 调整亮度。

> This technique isn't super intuitive. It will probably require some trial and error to find the colors you need, especially since the algorithms aren't perfect. In the future, it would be fantastic if ```CSS filters``` could also work in HSL space, as they would be much more intuitive.

这个技巧并不是那么直观。直到你经过不断尝试过后，才能确定你需要的颜色，尤其是算法也不是那么完美。将来，如果 ```CSS filter``` 也能支持 HSL，那就完美了。

> CSS filters are supported well in most browsers. IE has them listed as "In Development", which means they are in active development and should be added to IE soon.

大多数浏览器都支持 ```CSS filter```。IE也在考虑之中。

**(译者注：具体支持情况，请以最新为准)**

> - [https://status.modern.ie/filters](https://status.modern.ie/filters)
> - [http://caniuse.com/#feat=css-filters](http://caniuse.com/#feat=css-filters)

### Data URI

> As Chris Coyier demonstrated in his article, [Probably Don't Base-64 SVG](https://css-tricks.com/probably-dont-base64-svg/), properly formatted, you can pop SVG XML right into your CSS. Using this technique, and a bit of Sass magic, I've created some functions that allow you to dynamically change the color.

正如 Chris Coyier 博客中说的，[千万不要把 SVG Base64 格式化](https://css-tricks.com/probably-dont-base64-svg/)，你只需要把 SVG 代码片段正确的放入 CSS 中即可。利用这项技术配合 Sass ，我创建一个 Sass 函数，可以动态的改变 ```icon``` 的颜色。

[Demo3](https://codepen.io/noahblon/pen/xGbXdV)

> With this setup, add the path coordinates of your ```SVG``` to a ```Sass``` map with a ```key``` and invoke the function with your parameters. You call the function with the icon's name, which corresponds to the key in the map. You can pass in arguments for ```color```, ```stroke-color```, and  ```stroke-width```. You can also do other fun stuff like pass in any SVG valid CSS, as I did with the ```stroke-dasharray``` in the last example.

通过这个方法，你可以在 ```Sass``` 中添加 ```SVG```，并设置一个 ```key``` 然后传入参数调用方法。你可以通过 ```icon``` 的名字调用函数，这个名字是和 ```key``` 对应的。在参数中你可以传入 ```color```、```stroke-color``` 和  ```stroke-width```。你还可以做一些有趣的处理，比如：我在最后一个示例中就用到了 ```stroke-dasharray```。

> With proper URL encoding, this will work in IE. Obviously its quite intricate to get right.

通过正确的 URL 编码，IE中也是可以正常运行的。显然，想正确使用相当的复杂。

> - [Probably Don't Base-64 SVG](https://css-tricks.com/probably-dont-base64-svg/)
> - [Optimizing SVGs in data URIs](https://codepen.io/tigt/post/optimizing-svgs-in-data-uris) by [Taylor Hunt](https://codepen.io/Tigt/)

### SVG Background Sprite

> Creating sprites for SVG uses the same concepts as normal image sprites. The sprite contains all of the versions of an image. By manipulating the ```background-size``` and ```background-position``` in your CSS, you choose which version to display. Here's how to set up an SVG sprite:

创建 SVG 雪碧图和图片雪碧图一样。一张图片包含了所有的 ```icon```。通过控制 ```background-size``` 和 ```background-position```，来决定显示那一个。以下是设置 SVG 雪碧图的方法。

[Demo4](https://codepen.io/noahblon/pen/JdojNv)

> Use the ```xmnls``` namespace attribute or the SVG won't work in your background. Also include the ```xmlns:xlink``` attribute if using links, which I am in the ```use``` tags. The ```width``` and ```height``` need to be large enough to contain all of the images. In the example I'm using twenty-four 24x24 icons stacked vertically, so my ```SVG``` dimensions are 24x576 (24 times 24 = 576).

仅仅用 ```xmnls``` 命名空间，```SVG``` 并不能正常工作。因为我用到了 ```use``` 标签，所以还需要添加 ```xmlns:xlink```。宽、高要足够大用来容纳所有的图片。在示例中，有24个24 x 24的 ```icon``` 垂直排列，所以我的 ```SVG``` 尺寸是24 x 576（24 x 24 = 576）。

> I'm using the ```symbol``` and ```use``` tags to keep the file size low. Define the icon shape as a ```symbol``` and place it in a ```defs``` block. Give it an ```id```. Employ the ```use``` tag to stamp out versions of this ```symbol```. In the ```use``` tag, the ```y``` coordinate is adjusted so the icons stack on top of each other at 24 unit increments. The ```fill``` attribute colors the icon. Here's a demo with a bunch of different colored icons defined using this technique.

我用标签 ```symbol``` 和 ```use``` 保证文件足够小。通过 ```symbol``` 定义了一个 ```icon``` 的图形并把它放在 ```defs``` 块中。设置一个 ```id```，标签 ```use``` 通过 ```id``` 来引用它。在 ```use``` 中，可以通过属性 ```y``` 来控制显示哪个 ```icon```。属性 ```fill``` 决定了 ```icon``` 的颜色。下面是一个利用这项技术实现的Demo。

[Demo5](https://codepen.io/noahblon/pen/bpsxv)

> I'm using Sass here to make the background positioning easier.

我用到了 ```Sass``` 这样处理 ```background-position``` 更加容易。

> This technique works great everywhere ```SVG``` is supported. Because you need to manually create and add new icon versions to the sprite, it’s pretty inflexible.

任何支持 ```SVG``` 的地方，这项技术都能很好的工作。因为你需要手动的管理所有的 ```icon```，这就导致非常的不灵活。

> Those are the methods I could think of for coloring an SVG as a background-image with CSS. Are there more? Probably!

这就是我能想到的所有方法。你或许还有更好的方法。
