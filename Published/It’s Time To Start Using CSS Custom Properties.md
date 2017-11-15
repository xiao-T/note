## It’s Time To Start Using CSS Custom Properties
## 是时候使用 CSS 自定义属性了

> 原文地址: [https://www.smashingmagazine.com/2017/04/start-using-css-custom-properties/](https://www.smashingmagazine.com/2017/04/start-using-css-custom-properties/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> Today, CSS preprocessors are a standard for web development. One of the main advantages of preprocessors is that they enable you to use variables. This helps you to avoid copying and pasting code, and it simplifies development and refactoring.

今天，CSS 预编译已经成了 web 开发的标准。预编译中一个重要的功能就是它支持变量。这有利于避免复制黏贴重复代码，并简化了开发和重构。

> We use preprocessors to store colors, font preferences, layout details — mostly everything we use in CSS.

在预编译中我们可以用变量存储：颜色、字体、布局等等，大部分数据。

> But preprocessor variables have some limitations:

但是预编译中的变量也有它的局限性：

> - You cannot change them dynamically.
> - They are not aware of the DOM’s structure.
> - They cannot be read or changed from JavaScript.

- 不能动态改变
- 不受 DOM 结构的影响（**译者注：比如继承**）
- 不能通过 JavaScript 读取和改变。

> As a silver bullet for these and other problems, the community invented CSS custom properties. Essentially, these look and work like CSS variables, and the way they work is reflected in their name.

为了解决这些问题，社区提出了 CSS 自定义属性的概念。事实上，它们看起来就像 CSS 变量，它们的工作模式就像它们的名字那样。

> Custom properties are opening new horizons for web development.

自定义属性为 web 开发打开了新的视野。

#### FURTHER READING ON SMASHINGMAG:

#### SMASHINGMAG 上的更多资讯：

> - [A Detailed Introduction To Custom Elements](https://www.smashingmagazine.com/2014/03/introduction-to-custom-elements/)
> - [Houdini: Maybe The Most Exciting Development In CSS You’ve Never Heard Of](https://www.smashingmagazine.com/2016/03/houdini-maybe-the-most-exciting-development-in-css-youve-never-heard-of/)
> - [Turn Your AMP Up To 11: Everything You Need To Know About Google’s Accelerated Mobile Pages](https://www.smashingmagazine.com/2016/02/everything-about-google-accelerated-mobile-pages/)
> - [A Better iOS Architecture: A Deep Look At The Model-View-Controller Pattern](https://www.smashingmagazine.com/2016/05/better-architecture-for-ios-apps-model-view-controller-pattern/)

- [自定义元素的详情介绍](https://www.smashingmagazine.com/2014/03/introduction-to-custom-elements/)
- [你不知道的 CSS 神奇一面](https://www.smashingmagazine.com/2016/03/houdini-maybe-the-most-exciting-development-in-css-youve-never-heard-of/)
- [有关 Google AMP 必须知道的](https://www.smashingmagazine.com/2016/02/everything-about-google-accelerated-mobile-pages/)
- [更好的 iOS 架构：深入理解 MVC 模式](https://www.smashingmagazine.com/2016/05/better-architecture-for-ios-apps-model-view-controller-pattern/)

### Syntax To Declare And Use Custom Properties 

### 自定义属性声明和使用语法

> The usual problem when you start with a new preprocessor or framework is that you have to learn a new syntax.

通常当你开始使用新的预编译器或者框架时，你需要学习它的新语法。

> Each preprocessor requires a different way of declaring variables. Usually, it starts with a reserved symbol — for example, ```$``` in Sass and ```@``` in LESS.

每个预编译器都有它们自己的声明变量的语法。通常，以几个符号开始，比如：在 Sass 中是```$```，在 LESS 中使用 ```@```。

> CSS custom properties have gone the same way and use ```--``` to introduce a declaration. But the good thing here is that you can learn this syntax once and reuse it across browsers!

自定义属性也用同样的方式，用 ```--``` 开始一段声明。好处是：你只需学习一次，在各个浏览器之间都是可以用的。

> You may ask, “Why not reuse an existing syntax?”

你或许有疑问：为什么不用重用已有的语法？

> [There is a reason](http://www.xanthir.com/blog/b4KT0). In short, it’s to provide a way for custom properties to be used in any preprocessor. This way, we can provide and use custom properties, and our preprocessor will not compile them, so the properties will go directly to the outputted CSS. *And*, you can reuse preprocessor variables in the native ones, but I will describe that later.

[这有原因](http://www.xanthir.com/blog/b4KT0)。长话短说，它提供一种在预编译器中使用自定义属性的方式。通过这种方式，我们可以使用自定义属性，但是在预编译器中并不会处理它们，自定义属性将直接输出到 CSS 中。所以，你可以继续使用预编译器，稍后我会介绍。

> (Regarding the name: Because their ideas and purposes are very similar, sometimes custom properties are called the CSS variables, although the correct name is CSS custom properties, and reading further, you will understand why this name describes them best.)

（关于命名：因为它们想法和目的非常相似，有时候自定义属性也被称为 CSS 变量，尽管正确的名字是 CSS 自定义属性，进一步阅读，你会理解为什么要这么命名。）

> So, to declare a variable instead of a usual CSS property such as ```color``` or ```padding```, just provide a custom-named property that starts with ```--```:

所以，定义一个变量代替常规的 CSS 属性，例如：```color``` 和 ```padding```。只需用 ```--``` 开始自定义一个属性名即可。

```css
.box{
  --box-color: #4d4e53;
  --box-padding: 0 10px;
}
```

> The value of a property may be any valid CSS value: a color, a string, a layout value, even an expression.

自定义属性的值可以是任何有效的 CSS 值：颜色、字符串、布局相关值，甚至可以是一个表达式。

> Here are examples of valid custom properties:

这是一个有关有效自定义属性的示例：

```css
:root{
  --main-color: #4d4e53;
  --main-bg: rgb(255, 255, 255);
  --logo-border-color: rebeccapurple;

  --header-height: 68px;
  --content-padding: 10px 20px;

  --base-line-height: 1.428571429;
  --transition-duration: .35s;
  --external-link: "external link";
  --margin-top: calc(2vh + 20px);

  /* Valid CSS custom properties can be reused later in, say, JavaScript. */
  --foo: if(x > 5) this.width = 10;
}
```

> In case you are not sure what ```:root``` matches, in HTML it’s the same as ```html``` but with a higher specificity.

示例中的 ```:root```，如果你不确定代表谁，在 HTML 中它相当于 ```html```，但是有更高的意义。

> As with other CSS properties, custom ones cascade in the same way and are dynamic. This means they can be changed at any moment and the change is processed accordingly by the browser.

与其它 CSS 属性一样，自定义属性有相同的级联和动态性。也就是说你可以随时随地改变它，并且浏览器会做出响应。

> To use a variable, you have to use the ```var()``` CSS function and provide the name of the property inside:

使用 CSS 变量，你必须使用 ```var()``` 方法，并提供一个属性名字作为参数：

```css
.box{
  --box-color:#4d4e53;
  --box-padding: 0 10px;

  padding: var(--box-padding);
}

.box div{
  color: var(--box-color);
}
```

#### DECLARATION AND USE CASES

#### 声明和使用的示例

> The ```var()``` function is a handy way to provide a default value. You might do this if you are not sure whether a custom property has been defined and want to provide a value to be used as a fallback. This can be done easily by passing the second parameter to the function:

```var()``` 方法有一种很便利的方式提供默认值。如果，你不确定自定义属性有没有定义，但是还是想使用它，你可以给方法提供一个默认值。通过给方法提供第二个参数可以很容易实现：

```css
.box{
  --box-color:#4d4e53;
  --box-padding: 0 10px;

  /* 10px is used because --box-margin is not defined. */
  margin: var(--box-margin, 10px);
}
```

> As you might expect, you can reuse other variables to declare new ones:

如果，你愿意，你还可以通过重用其它的变量定义新的变量：

```css
.box{
  /* The --main-padding variable is used if --box-padding is not defined. */
  padding: var(--box-padding, var(--main-padding));

  --box-text: 'This is my box';

  /* Equal to --box-highlight-text:'This is my box with highlight'; */
  --box-highlight-text: var(--box-text)' with highlight';
}
```

### Operations: +, -, *, / 

### 运算：+，-，*，／

> As we got accustomed to with preprocessors and other languages, we want to be able to use basic operators when working with variables. For this, CSS provides a ```calc()``` function, which makes the browser recalculate an expression after any change has been made to the value of a custom property:

我们习惯了预编译器和其它语言，当使用 CSS 变量的时候，我们也希望可以使用运算符。对于这个，CSS 提供了 ```calc()``` 方法，如果自定义属性有任何的改变，浏览器都会根据表达式重新计算：

```css
:root{
  --indent-size: 10px;

  --indent-xl: calc(2*var(--indent-size));
  --indent-l: calc(var(--indent-size) + 2px);
  --indent-s: calc(var(--indent-size) - 2px);
  --indent-xs: calc(var(--indent-size)/2);
}
```

> A problem awaits if you try to use a unit-less value. Again, ```calc()``` is your friend, because without it, it won’t work:

如果，你尝试使用一个没有单位的值这会有问题。再次，```calc()``` 是你的朋友，如果没有它，这将不能实现：

```css
:root{
  --spacer: 10;
}

.box{
  padding: var(--spacer)px 0; /* DOESN'T work */
  padding: calc(var(--spacer)*1px) 0; /* WORKS */
}
```

### Scope And Inheritance

### 作用域和继承

> Before talking about CSS custom property scopes, let’s recall JavaScript and preprocessor scopes, to better understand the differences.

在讨论 CSS 自定义属性作用域前，先让我们回顾一下 JavaScript 和预编译器的作用域，这样可以很好理解它们之间的区别。

> We know that with, for example, JavaScript variables (```var```), a scope is limited to the functions.

我都知道，JavaScript 中 ```var``` 声明的变量的作用域是仅限于函数内的。

> We have a similar situation with ```let``` and ```const```, but they are block-scope local variables.

```let``` 和 ```const``` 有类似情况，但是它们是块和局部作用域。

> A ```closure``` in JavaScript is a function that has access to the outer (enclosing) function’s variables — the scope chain. The closure has three scope chains, and it has access to the following:

Javascript 中的```闭包```是指：函数内可以访问外部变量 - 作用域链。闭包有三个作用域链，按照以下的规则访问变量：

> - its own scope (i.e. variables defined between its braces),
> - the outer function’s variables,
> - the global variables.

- 自己当前作用域（例如：大括号之间定义的变量）
- 函数外变量
- 全局变量

![](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-780w-opt.png)

> ([View large version](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-large-opt.png))

([查看大图](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-large-opt.png))

> The story with preprocessors is similar. Let’s use Sass as an example because it’s probably the most popular preprocessor today.

预编译器也有类似的情况。让我们看一个 Sass 的示例，Sass 应该是当今最流行的预编译器了。

> With Sass, we have two types of variables: local and global.

Sass中，我们有两类变量：局部和全局。

> A global variable can be declared outside of any selector or construction (for example, as a mixin). Otherwise, the variable would be local.

任何选择器和构造方法（例如：mixin）之外都可以声明全局变量。否则，将被声明为局部变量。

> Any nested blocks of code can access the enclosing variables (as in JavaScript).

任何嵌套在块内的代码都可以访问内部变量（类似 Javascript）

![](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-scss-780w-opt.png)

> ([View large version](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-scss-large-opt.png))

([查看大图](https://www.smashingmagazine.com/wp-content/uploads/2017/03/closure-scss-large-opt.png))

> This means that, in Sass, the variable’s scopes fully depend on the code’s structure.

也就是说，Sass 中，变量的作用域完全依赖代码的结构。

> However, CSS custom properties are inherited by default, and like other CSS properties, they cascade.

然而，CSS 自定义属性默认是继承，类似其它 CSS 属性，而且它们是联动的。

> You also cannot have a global variable that declares a custom property outside of a selector — that’s not valid CSS. The global scope for CSS custom properties is actually the ```:root``` scope, whereupon the property is available globally.

你不能在选择器之外声明全局自定义属性 - 那是无效的 CSS。其实，CSS 自定义属性全局作用域是 ```:root```，这里面的属性是全局有效的。

> Let’s use our syntax knowledge and adapt the Sass example to HTML and CSS. We’ll create a demo using native CSS custom properties. First, the HTML:

让我们用我们所学知识重写 Sass 的示例，并适用于 HTML 和 CSS。我们将用 CSS 自定义属性重新演示这个示例。首先，HTML 如下：

```html
global
<div class="enclosing">
  enclosing
  <div class="closure">
    closure
  </div>
</div>
```

> And here is the CSS:

以下是 CSS：

```css
:root {
  --globalVar: 10px;
}

.enclosing {
  --enclosingVar: 20px;
}

.enclosing .closure {
  --closureVar: 30px;

  font-size: calc(var(--closureVar) + var(--enclosingVar) + var(--globalVar));
  /* 60px for now */
}
```

[DEMO](https://codepen.io/malyw/pen/MJmebz)

#### CHANGES TO CUSTOM PROPERTIES ARE IMMEDIATELY APPLIED TO ALL INSTANCES

#### 改变自定义属性会立即响应

> So far, we haven’t seen how this is any different from Sass variables. However, let’s reassign the variable after its usage:

目前为止，我们还没有看到自定义属性和 Sass 任何不同之处。但是，让我在变量使用之后重新定义：

> In the case of Sass, this has no effect:

在这个 Sass 示例中，这是无效的

```css
.closure {
  $closureVar: 30px; // local variable
  font-size: $closureVar +$enclosingVar+ $globalVar;
  // 60px, $closureVar: 30px is used

  $closureVar: 50px; // local variable
}
```

[DEMO](https://codepen.io/malyw/pen/bgWerv)

> But in CSS, the calculated value is changed, because the ```font-size``` value is recalculated from the changed ```--closureVar``` value:

但是在 CSS 中，最终的值是会变化的，这是因为 ```font-size``` 会在 ```--closureVar``` 值改变后重新计算：

```css
.enclosing .closure {
  --closureVar: 30px;

  font-size: calc(var(--closureVar) + var(--enclosingVar) + var(--globalVar));
  /* 80px for now, --closureVar: 50px is used */

  --closureVar: 50px;
}
```

[DEMO](https://codepen.io/malyw/pen/WRjxOy)

> That’s the first huge difference: If you reassign a custom property’s value, the browser will **recalculate all variables and ```calc()``` expressions** where it’s applied.

这是一个非常大区别：如果你重新赋值自定属性的值，那么浏览器会**重新计算所有的变量和 ```calc()``` 的表达式**。

#### PREPROCESSORS ARE NOT AWARE OF THE DOM’S STRUCTURE

#### 预编译器并不受 DOM 结构的影响

> Suppose we wanted to use the default ```font-size``` for the block, except where the ```highlighted``` class is present.

假设，除非有类 ```highlighted```，我将使用默认默认的 ```font-size``。

> Here is the HTML:

HTML 如下：

```html
<div class="default">
  default
</div>

<div class="default highlighted">
  default highlighted
</div>
```

> Let’s do this using CSS custom properties:

CSS 自定义属性如下：

```css
.highlighted {
  --highlighted-size: 30px;
}

.default {
  --default-size: 10px;

  /* Use default-size, except when highlighted-size is provided. */
  font-size: var(--highlighted-size, var(--default-size));
}
```

> Because the second HTML element with the ```default``` class carries the ```highlighted``` class, properties from the ```highlighted``` class will be applied to that element.

因为第二个 HTML 元素有 ```default``` 和 ```highlighted``` 两个类，自定义属性将会从类 ```highlighted``` 应用到元素上。

> In this case, it means that ```--highlighted-size: 30px```; will be applied, which in turn will make the ```font-size``` property being assigned use the ```--highlighted-size```.

在这个示例中，```--highlighted-size: 30px``` 将被使用，```font-size``` 的值会用 ```--highlighted-size``` 重新定义。

> Everything is straightforward and works:

一切都那么顺畅：

[DEMO](https://codepen.io/malyw/pen/ggWMvG)

> Now, let’s try to achieve the same thing using Sass:

现在，我们尝试用 Sass 实现同样的效果：

```scss
.highlighted {
  $highlighted-size: 30px;
}

.default {
  $default-size: 10px;

  /* Use default-size, except when highlighted-size is provided. */
  @if variable-exists(highlighted-size) {
    font-size: $highlighted-size;
  }
  @else {
    font-size: $default-size;
  }
}
```

> The result shows that the default size is applied to both:

结果显示两种都应用了默认的值：

[DEMO](https://codepen.io/malyw/pen/PWmzQO)

> This happens because all Sass calculations and processing happen at compilation time, and of course, it doesn’t know anything about the DOM’s structure, relying fully on the code’s structure.

这是因为 Sass 所有的计算和处理都在编译时发生，当然，它并不知道 DOM 的结构，也不完全依赖代码结构。

> As you can see, custom properties have the advantages of variables scoping and add the usual cascading of CSS properties, being aware of the DOM’s structure and following the same rules as other CSS properties.

如你所见，自定属性有变量作用域的优点，并且可以像普通 CSS 属性一样可以级联。它受 DOM 结构的影响就像其它 CSS 属性。

> The second takeaway is that CSS custom properties are **aware of the DOM’s structure and are dynamic**.

第二个优点：CSS 自定义属性**依赖 DOM 结构并且是动态的**。

### CSS-Wide Keywords And The ```all``` Property

### CSS 保留关键字和 ```all``` 属性

> CSS custom properties are subject to the same rules as the usual CSS custom properties. This means you can assign any of the common CSS keywords to them:

CSS 自定义属性和普通的属性遵循同样的规则。也就是说你可以给它们赋值任何 CSS 关键字：

> - ```inherit```   
> This CSS keyword applies the value of the element’s parent.
> - ```initial```   
> This applies the initial value as defined in the CSS specification (an empty value, or nothing in some cases of CSS custom properties).
> - ```unset```    
> This applies the inherited value if a property is normally inherited (as in the case of custom properties) or the initial value if the property is normally not inherited.
> - ```revert```    
>This resets the property to the default value established by the user agent’s style sheet (an empty value in the case of CSS custom properties).

- ```inherit```    
继承父级元素的值
- ```initial```     
CSS 规范中定义的初始值（空值或者没有值）
- ```unset```    
如果值继承来的就用继承的值（例如：自定义属性），否则用初始值
 - ```revert```    
重置属性的值为默认值，既：浏览器默认的值（对于自定属性来说是空值）

> Here is an example:

以下是示例：

```css
.common-values{
  --border: inherit;
  --bgcolor: initial;
  --padding: unset;
  --animation: revert;
}
```

> Let’s consider another case. Suppose you want to build a component and want to be sure that no other styles or custom properties are applied to it inadvertently (a modular CSS solution would usually be used for styles in such a case).

让我们考虑另外一种情况。假设，你想新建一个组件，并且确保不会受到其它样式和自定义属性的影响（通常情况下，CSS 模块化是一个解决方案）

> But now there is another way: to use the ```all``` [CSS property](https://developer.mozilla.org/en/docs/Web/CSS/all). 
This shorthand resets all CSS properties.

但是，现在有另外一方式：使用 ```all``` [属性](https://developer.mozilla.org/en/docs/Web/CSS/all)。这样可以重置所有的 CSS 属性。

> Together with CSS keywords, we can do the following:

属性 ```all``` 和 上面的提到 CSS 关键字，我们可以这么用：

```css
.my-wonderful-clean-component{
  all: initial;
}
```

> This reset all styles for our component.

这将会重置我们组件中的所有样式。

> Unfortunately, the ```all``` keyword [doesn’t reset custom properties](https://drafts.csswg.org/css-variables/#defining-variables). There is an ongoing [discussion about whether to add the ```--``` prefix](https://github.com/w3c/webcomponents/issues/300#issuecomment-144551648), which would reset all CSS custom properties.

但是，关键字 ```all``` 并[不能重置自定义属性](https://drafts.csswg.org/css-variables/#defining-variables)。这有一个[有关是否添加前缀 ```--``` 的讨论](https://github.com/w3c/webcomponents/issues/300#issuecomment-144551648)，用来重置所有的自定义属性。

> So, in future, a full reset might be done like this:

所以，以后，我们或许可以这么用：

```css
.my-wonderful-clean-component{
  --: initial; /* reset all CSS custom properties */
  all: initial; /* reset all other CSS styles */
}
```

### CSS Custom Properties Use Cases

### CSS 自定义属性用例

> There are many uses of custom properties. I will show the most interesting of them.

CSS 自定义属性有很多使用场景。我将展示那些最有趣的用法。

#### EMULATE NON-EXISTENT CSS RULES

#### 模仿不存在的 CSS 规则

> The name of these CSS variables is “custom properties,” so why not to use them to emulate non-existent properties?

既然被称作“自定义属性”，我们为什么不用它来模拟那些不存在的属性呢？

> There are many of them: ```translateX/Y/Z```, ```background-repeat-x/y``` (still not cross-browser compatible), ```box-shadow-color```.

有很多，例如：```translateX/Y/Z```, ```background-repeat-x/y``` (还是不能解决兼容问题), ```box-shadow-color```。

> Let’s try to make the last one work. In our example, let’s change the box-shadow’s color on hover. We just want to follow the DRY rule (don’t repeat yourself), so instead of repeating ```box-shadow```’s entire value in the ```:hover``` section, we’ll just change its color. Custom properties to the rescue:

让我们以最后一个（```box-shadow-color```）为例做个 DEMO。在示例中，当指针移动到 DOM 上的时候我们改变 ```box-shadow``` 的颜色。我们遵循 DRY（不要让自己重复） 规则，为了避免在 ```:hover``` 时候整个替换 ```box-shadow``` 的值，我只改变颜色。自定属性就可以实现：

```css
.test {
  --box-shadow-color: yellow;
  box-shadow: 0 0 30px var(--box-shadow-color);
}

.test:hover {
  --box-shadow-color: orange;
  /* Instead of: box-shadow: 0 0 30px orange; */
}
```

[DEMO](https://codepen.io/malyw/pen/KzZXRq)

#### COLOR THEMES

#### 颜色主题

> One of the most common use cases of custom properties is for color themes in applications. Custom properties were created to solve just this kind of problem. So, let’s provide a simple color theme for a component (the same steps could be followed for an application).

自定义属性通常被用来做应用的颜色主题。自定义属性被创建就是来解决这类问题的。所以，让我们为组件提供一个单色主题（实际应用中可以遵循同样的思路）。

> Here is the [code for our button component](https://codepen.io/malyw/pen/XpRjNK):

这是一段 [button 组件的代码](https://codepen.io/malyw/pen/XpRjNK)

```css
.btn {
  background-image: linear-gradient(to bottom, #3498db, #2980b9);
  text-shadow: 1px 1px 3px #777;
  box-shadow: 0px 1px 3px #777;
  border-radius: 28px;
  color: #ffffff;
  padding: 10px 20px 10px 20px;
}
```

> Let’s assume we want to invert the color theme.

我们假设我们需要改变颜色。

> The first step would be to extend all of the color variables to CSS custom properties and rewrite our component. So, the [result would be the same](https://codepen.io/malyw/pen/EZmgmZ):

首先，我们先提取所有的颜色值赋值给自定义属性，并重写组件。所以，[结果是和之前一样的](https://codepen.io/malyw/pen/EZmgmZ):

```css
.btn {
  --shadow-color: #777;
  --gradient-from-color: #3498db;
  --gradient-to-color: #2980b9;
  --color: #ffffff;

  background-image: linear-gradient(
    to bottom,
    var(--gradient-from-color),
    var(--gradient-to-color)
  );
  text-shadow: 1px 1px 3px var(--shadow-color);
  box-shadow: 0px 1px 3px var(--shadow-color);
  border-radius: 28px;
  color: var(--color);
  padding: 10px 20px 10px 20px;
}
```

> This has everything we need. With it, we can override the color variables to the inverted values and apply them when needed. We could, for example, add the global ```inverted``` HTML class (to, say, the ```body``` element) and change the colors when it’s applied:  


万事俱备。如果，我们需要我们可以给这些颜色变量重新赋值。例如：我们可以添加一个全局（也就是说：给 ```body``` 添加）类 ```inverted```，当有个这个类的时候改变颜色：

```css
body.inverted .btn{
  --shadow-color: #888888;
  --gradient-from-color: #CB6724;
  --gradient-to-color: #D67F46;
  --color: #000000;
}
```

> Below is a demo in which you can click a button to add and remove a global class:

下面的例子中，当你点击按钮的时候会切换这个全局的类：

[DEMO](https://codepen.io/malyw/pen/dNWpRd)

> This behavior cannot be achieved in a CSS preprocessor without the overhead of duplicating code. With a preprocessor, you would always need to override the actual values and rules, which always results in additional CSS.

这种行为 CSS 预编译器在没有过多代码开销的情况下是不能实现的。使用预编译器，你将会重写所有的规则和对应的值，这样就会增加了 CSS 的量。

> With CSS custom properties, the solution is as clean as possible, and copying and pasting is avoided, because only the values of the variables are redefined.

CSS 自定义属性，让解决方案尽可能的轻便，也避免了复制、黏贴代码，因为，我们只需给变量重新赋值即可。

### Using Custom Properties With JavaScript 

### JavaScript 中使用自定义属性

> Previously, to send data from CSS to JavaScript, we often had to [resort to tricks](https://blog.hospodarets.com/passing_data_from_sass_to_js), writing CSS values via plain JSON in the CSS output and then reading it from the JavaScript.

先前，CSS 和 JavaScript 通信，我们需要[使用这个技巧](https://blog.hospodarets.com/passing_data_from_sass_to_js)，CSS 的值以 JSON 来代替，然后 JavaScript 来读写这个 JSON。

> Now, we can easily interact with CSS variables from JavaScript, reading and writing to them using the well-known ```.getPropertyValue()``` and ```.setProperty()``` methods, which are used for the usual CSS properties:

现在，我们使用 ```.getPropertyValue()``` 和 ```.setProperty()``` 这两个方法，可以很容易的实现 CSS 变量和 JavaScript 的交互，它们常用来修改普通的 CSS 属性：

```javascript
/**
* Gives a CSS custom property value applied at the element
* element {Element}
* varName {String} without '--'
*
* For example:
* readCssVar(document.querySelector('.box'), 'color');
*/
function readCssVar(element, varName){
  const elementStyles = getComputedStyle(element);
  return elementStyles.getPropertyValue(`--${varName}`).trim();
}

/**
* Writes a CSS custom property value at the element
* element {Element}
* varName {String} without '--'
*
* For example:
* readCssVar(document.querySelector('.box'), 'color', 'white');
*/
function writeCssVar(element, varName, value){
  return element.style.setProperty(`--${varName}`, value);
}
```

> Let’s assume we have a list of media-query values:

我们假设我们有一组媒体查询的值：

```css
.breakpoints-data {
  --phone: 480px;
  --tablet: 800px;
}
```

> Because we only want to reuse them in JavaScript — for example, in [Window.matchMedia()](https://developer.mozilla.org/en/docs/Web/API/Window/matchMedia) — we can easily get them from CSS:

因为，我们只想在 JavaScript 中重用它们 - 例如：[Window.matchMedia()](https://developer.mozilla.org/en/docs/Web/API/Window/matchMedia) - 通过以上两个方法可以很容易实现：

```javascript
const breakpointsData = document.querySelector('.breakpoints-data');

// GET
const phoneBreakpoint = getComputedStyle(breakpointsData)
  .getPropertyValue('--phone');
```

> To show how to assign custom properties from JavaScript, I’ve created an interactive 3D CSS cube demo that responds to user actions.

为了展示如何在 JavaScript 重新赋值自定义属性，我创建了一个 3D 立方体用来演示。

> It’s not very hard. We just need to add a simple background, and then place five cube faces with the relevant values for the ```transform``` property: ```translateZ()```, ```translateY()```, ```rotateX()``` and ```rotateY()```.

不算太难，我们只需添加一个简单的背景，然后通过属性 ```transform```：```translateZ()```, ```translateY()```, ```rotateX()``` 和 ```rotateY()``` 设置 5 个相对立的面。

> To provide the right perspective, I added the following to the page wrapper:

为了提供正确的视图，我在接下来的页面中添加以下代码：

```css
#world{
  --translateZ:0;
  --rotateX:65;
  --rotateY:0;

  transform-style:preserve-3d;
  transform:
    translateZ(calc(var(--translateZ) * 1px))
    rotateX(calc(var(--rotateX) * 1deg))
    rotateY(calc(var(--rotateY) * 1deg));
}
```

> The only thing missing is the interactivity. The demo should change the X and Y viewing angles (```--rotateX``` and ```--rotateY```) when the mouse moves and should zoom in and out when the mouse scrolls (```--translateZ```).

唯一缺少就是交互性。示例中如果鼠标移动视图应该改变 X 和 Y 的角度（```--rotateX``` 和 ```--rotateY```）或者鼠标滚动滚轮视图应该放大缩小(```--translateZ```)。

> Here is the JavaScript that does the trick:

这是 JavaScript 的处理方式：

```javascript
// Events
onMouseMove(e) {
  this.worldXAngle = (.5 - (e.clientY / window.innerHeight)) * 180;
  this.worldYAngle = -(.5 - (e.clientX / window.innerWidth)) * 180;
  this.updateView();
};

onMouseWheel(e) {
  /*…*/

  this.worldZ += delta * 5;
  this.updateView();
};

// JavaScript -> CSS
updateView() {
  this.worldEl.style.setProperty('--translateZ', this.worldZ);
  this.worldEl.style.setProperty('--rotateX', this.worldXAngle);
  this.worldEl.style.setProperty('--rotateY', this.worldYAngle);
};
```

> Now, when the user moves their mouse, the demo changes the view. You can check this by moving your mouse and using mouse wheel to zoom in and out:

现在，当用户移动鼠标的时候，demo 视图也会改变。你可以通过移动鼠标和滚动滚轮来放大缩小。

[DEMO](https://codepen.io/malyw/pen/xgdEQp)

> Essentially, we’ve just changed the CSS custom properties’ values. Everything else (the rotating and zooming in and out) is done by CSS.

事实上，我们只需改变 CSS 自定义属性的值。所有的事情（比如：翻转、放大、缩小） CSS 会自动处理

> **Tip:** One of the easiest ways to debug a CSS custom property value is just to show its contents in CSS generated content (which works in simple cases, such as with strings), so that the browser will automatically show the current applied value:

**提示：** 调试 CSS 自定义属性最简单的方式是通过 CSS content 显示属性值（内容会以简单字符串展示），浏览器会自动显示当前的自定义属性值：

```css
body:after {
  content: '--screen-category : 'var(--screen-category);
}
```

> You can check it [in the plain CSS demo](https://codepen.io/malyw/pen/oBWMOY) (no HTML or JavaScript). (Resize the window to see the browser reflect the changed CSS custom property value automatically.)

> 你可以查看这个简单的 [DEMO](https://codepen.io/xiao-T/pen/KyKaZz)（没有 HTML 和 JavaScript）。（改变视窗的大小 CSS 自定义属性也会随着一起改变）   
（**译者注：原链接404，我自己又重新写了一个，不知道跟原作者的有没有差异**）

### Browser Support

### 浏览器支持情况

> CSS custom properties are [supported in all major browsers](http://caniuse.com/#feat=css-variables):

CSS 自定义属性支持所有[主流浏览器](http://caniuse.com/#feat=css-variables):

![](https://www.smashingmagazine.com/wp-content/uploads/2017/04/css-variables-large-opt.png)

> ([View large version](https://www.smashingmagazine.com/wp-content/uploads/2017/04/css-variables-large-opt.png))

([查看大图](https://www.smashingmagazine.com/wp-content/uploads/2017/04/css-variables-large-opt.png))

> This means that, you can start using them natively.

也就是说，你现在就可以使用了。

> If you need to support older browsers, you can learn the syntax and usage examples and consider possible ways of switching or using CSS and preprocessor variables in parallel.

如果你需要兼容旧版本浏览器，你以学习这些语法、用例并考虑自定义属性和预编译器如何平行切换。

> Of course, we need to be able to detect support in both CSS and JavaScript in order to provide fallbacks or enhancements.

当然，我们需要检测支持情况以便提供回退和增强方案。

> This is quite easy. For CSS, you can use a [```@supports``` condition](https://developer.mozilla.org/en/docs/Web/CSS/@supports) with a dummy feature query:

这非常简单。CSS 中，你可以用 [```@supports```](https://developer.mozilla.org/en/docs/Web/CSS/@supports) 来检测：

```css
@supports ( (--a: 0)) {
  /* supported */
}

@supports ( not (--a: 0)) {
  /* not supported */
}
```

> In JavaScript, you can use the same dummy custom property with the ```CSS.supports()``` static method:

JavaScript 中，你可以通过 ```CSS.supports()``` 方法检测自定义属性的支持情况：

```javascript
const isSupported = window.CSS &&
    window.CSS.supports && window.CSS.supports('--a', 0);

if (isSupported) {
  /* supported */
} else {
  /* not supported */
}
```

> As we saw, CSS custom properties are still not available in every browser. Knowing this, you can progressively enhance your application by checking if they are supported.

我们知道，CSS 自定义属性并不是所有的浏览器都支持。因此，如果浏览器不支持你可以考虑用预编译器来增强。

> For instance, you could generate two main CSS files: one with CSS custom properties and a second without them, in which the properties are inlined (we will discuss ways to do this shortly).

例如，你可以创建两个 CSS 文件：一个有 CSS 自定义属性，一个没有，有自定义属性的文件内链（稍后我们讨论如何操作）

> Load the second one by default. Then, just do a check in JavaScript and switch to the enhanced version if custom properties are supported:

默认加载第二种文件。然后，如果检测到浏览器支持 CSS 自定义属性，就切换回增强版本：

```html
<!-- HTML -->
<link href="without-css-custom-properties.css"
    rel="stylesheet" type="text/css" media="all" />
```

```javascript
// JavaScript
if(isSupported){
  removeCss('without-css-custom-properties.css');
  loadCss('css-custom-properties.css');
  // + conditionally apply some application enhancements
  // using the custom properties
}
```

> This is just an example. As you’ll see below, there are better options.

这只是一个示例。接下来，你会看到更好的处理方式。

### How To Start Using Them 

### 如何使用它们

> According to a [recent survey](https://ashleynolan.co.uk/blog/frontend-tooling-survey-2016-results), Sass continues to be the preprocessor of choice for the development community.

根据[最近的调查](https://ashleynolan.co.uk/blog/frontend-tooling-survey-2016-results)，Sass 依旧是开发者最好的选择。

> So, let’s consider ways to start using CSS custom properties or to prepare for them using Sass.

所以，在我们使用 CSS 自定义属性的时候考虑用 Sass 做备用方案。

> We have a few options.

我们有几种选择。

#### 1. MANUALLY CHECK IN THE CODE FOR SUPPORT

#### 1.手动检测是否支持

> One advantage of this method of manually checking in the code whether custom properties are supported is that it works and we can do it right now (don’t forget that we have switched to Sass):

手动检测是否支持 CSS 自定义属性是有好处的，至少我们可以保证代码可以正常执行，我们现在就可以做（不要忘记切换到 Sass）：

```css

$color: red;
:root {
  --color: red;
}

.box {
  @supports ( (--a: 0)) {
    color: var(--color);
  }
  @supports ( not (--a: 0)) {
    color: $color;
  }
}
```

> This method does have many cons, not least of which are that the code gets complicated, and copying and pasting become quite hard to maintain.

这个方法也有很多确定，其中最主要是代码变得复杂，复制黏贴变得非常难维护。

#### 2. USE A PLUGIN THAT AUTOMATICALLY PROCESSES THE RESULTING CSS

#### 2.使用插件处理 CSS 自定义属性

> The PostCSS ecosystem provides dozens of plugins today. A couple of them process custom properties (inline values) in the resulting CSS output and make them work, assuming you provide only global variables (i.e. you only declare or change CSS custom properties inside the ```:root``` selector(s)), so their values can be easily inlined.

至今 PostCSS 的生态系统提供了很多插件。 有几个是处理自定属性并內联输出 CSS，并让它正常工作，假设，你只提供全局的变量（例如：只是通过 ```:root``` 定义自定义属性），因此，这样可以很容易处理。

> An example is [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties).

一个 [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties) 的示例。

> This plugin offers several pros: It makes the syntax work; it is compatible with all of PostCSS’ infrastructure; and it doesn’t require much configuration.

插件有几个功能：让规范语法正常工作；兼容所有的 PostCSS 基础插件；不需要额外的配置。

> There are cons, however. The plugin requires you to use CSS custom properties, so you don’t have a path to prepare your project for a switch from Sass variables. Also, you won’t have much control over the transformation, because it’s done after the Sass is compiled to CSS. Finally, the plugin doesn’t provide much debugging information.

尽管如此，它还是有一些缺点。插件必须使用 CSS 自定义变量，因此，如果你的项目使用了 Sass 变量，就没法切换了。此外，你不能灵活的控制转换，因为 CSS 通过编译输出的。最后，插件不提供调试信息。

#### 3. [CSS-VARS MIXIN](https://github.com/malyw/css-vars)

#### 3. [CSS-VARS MIXIN](https://github.com/malyw/css-vars)

> I started using CSS custom properties in most of my projects and have tried many strategies:

我的大部分项目中都用到了 CSS 自定义属性，并尝试了很多方案：

> - Switch from Sass to PostCSS with [cssnext](http://cssnext.io/).
> - Switch from Sass variables to pure CSS custom properties.
> - Use CSS variables in Sass to detect whether they are supported.

- 通过 PostCSS [cssnext]((http://cssnext.io/)) 从 Sass 转换
- 把 Sass 变量转换成纯 CSS 自定义变量。
- 如果浏览器支持就在 Sass 中使用 CSS 变量

> As a result of that experience, I started looking for a solution that would satisfy my criteria:

根据以上的总结，我开始需找可以满足我需求的方案：

> - It should be easy to start using with Sass.
> - It should be straightforward to use, and the syntax must be as close to native CSS custom properties as possible.
> - Switching the CSS output from the inlined values to the CSS variables should be easy.
> - A team member who is familiar with CSS custom properties would be able to use the solution.
> - There should be a way to have debugging information about edge cases in the usage of variables.

- 应该很容易使用 Sass。
- 使用简单，并且语法必须尽可能的接近原生 CSS 自定义属性。
- 应该比较容易直接输出 CSS 变量
- 熟悉 CSS 自定义属性的团队应该比较容易上手
- 可调试

> As a result, I created css-vars, a Sass mixin that you can [find on Github](https://github.com/malyw/css-vars). Using it, you can sort of start using CSS custom properties syntax.

鉴于此，我创建了 css-var，一个 Sass mixin，你可以在 [Github](https://github.com/malyw/css-vars) 上找到它。使用它，你可以在项目一开始就可以使用 CSS 自定义属性。
 
### Using css-vars Mixin

### 使用 css-vars

> To declare variable(s), use the mixin as follows:

声明变量，使用如下：

```css
$white-color: #fff;
$base-font-size: 10px;

@include css-vars((
  --main-color: #000,
  --main-bg: $white-color,
  --main-font-size: 1.5*$base-font-size,
  --padding-top: calc(2vh + 20px)
));
```

> To use these variables, use the ```var()``` function:

通过方法 ```var()``` 可以引用变量：

```css
body {
  color: var(--main-color);
  background: var(--main-bg, #f00);
  font-size: var(--main-font-size);
  padding: var(--padding-top) 0 10px;
}
```

> This gives you a way to control all of the CSS output from one place (from Sass) and start getting familiar with the syntax. Plus, you can reuse Sass variables and logic with the mixin.

这提供一个方法：可以让你在同一个地方（Sass）控制 CSS 的输出，并可以熟悉新的语法。此外，你还可以通过 mixin 复用 Sass 变量和逻辑。

> When all of the browsers you want to support work with CSS variables, then all you have to do is add this:

当所有的浏览器都支持 CSS 变量，你只需添加以下代码：

```css
$css-vars-use-native: true;
```

> Instead of aligning the variable properties in the resulting CSS, the mixin will start registering custom properties, and the ```var()``` instances will go to the resulting CSS without any transformations. This means you’ll have fully switched to CSS custom properties and will have all of the advantages we discussed.

在输出的 CSS 中变量并不会被替换，mixin 开始使用自定义属性，并且 ```var()``` 也不会做任何的转换。也就是说，你已经完全切换到了 CSS 自定义属性模式，并且它包含了所有我们提到的优点。

> If you want to turn on the useful debugging information, add the following:

如果，你需要调试，只需：

```css
$css-vars-debug-log: true;
```

> This will give you:

你会得到以下信息：

> - a log when a variable was not assigned but was used;
> - a log when a variable is reassigned;
> - information when a variable is not defined but a default value gets passed that is used instead.

- 如果变量未赋值，但是被用到会有日志输出
- 如果变量被重新赋值也会有日志输出
- 如果，一个变量未定义并被一个默认值代替，也会有信息输出

### Conclusion 

### 总结

> Now you know more about CSS custom properties, including their syntax, their advantages, good usage examples and how to interact with them from JavaScript.

现在，你已经对 CSS 自定义属性有很多的认识，包括：语法、优点、最好的实践方案和如何跟 JavaScript 交互。

> You have learned how to detect whether they are supported, how they are different from CSS preprocessor variables, and how to start using native CSS variables until they are supported across browsers.

你也学会了如何检测它们是否被支持，它们与 CSS 预编译器变量的区别，如何在不支持的情况下跨浏览器的使用它们。

> This is the right time to start using CSS custom properties and to prepare for their native support in browsers.

现在就是正确使用 CSS 自定义属性的好时机。


