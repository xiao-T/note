## 10 New JavaScript Features in ES2020 That You Should Know
## ES2020 中 Javascript 10 个你应该知道的新功能

> 原文地址: <https://www.freecodecamp.org/news/javascript-new-features-es2020//>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://www.freecodecamp.org/news/content/images/size/w2000/2020/04/es2020logo.jpg)

Good news – the new ES2020 features are now finalised! This means we now have a complete idea of the changes happening in ES2020, the new and improved specification of JavaScript. So let's see what those changes are.

好消息 - ES2020 新功能已经落地！这就意味着，现在对 ES2020 中 Javascript 的新增和改进要有一个完整的了解。让我们来看看都有哪些改变。

## #1: BigInt

BigInt, one of the most anticipated features in JavaScript, is finally here. It actually allows developers to have much greater integer representation in their JS code for data processing for data handling.

BigInt，Javascript 中最期待的新功能终于落地。它允许开发者在 JS 中使用更大的整数进行数据处理。

At the moment the maximum number you can store as an integer in JavaScript is `pow(2, 53) - 1` . But BigInt actually allows you to go even beyond that.  

之前，Javascript 中最大的整数是 `pow(2, 53) - 1`。但是，BigInt 不受此限制。

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.21.47-PM.png)

However, you need to have an `n` appended at the very end of the number, as you can see above. This `n` denotes that this is a BigInt and should be treated differently by the JavaScript engine (by the v8 engine or whatever engine it is using).

然而，就如你在上面看到，你需要在数字后面添加一个 `n`。这个 `n` 说明这是一个 BigInt，Javascript 引擎应该特殊处理（不管是 V8，还是其它引擎）。

This improvement is not backwards compatible because the traditional number system is IEEE754 (which just cannot support numbers of this size).

因为传统的数字系统是 IEEE754（它不支持这种大数字），因此，这个改进并不会向后兼容。

## #2: Dynamic import

## #2：动态引入

Dynamic imports in JavaScript give you the option to import JS files dynamically as modules in your application natively. This is just like how you do it with Webpack and Babel at the moment.

Javascript 的动态引入，允许你把 JS 文件作为一个模块动态的引入到你的应用中。这就像你使用 webpack 和 Babel 一样。

This feature will help you ship on-demand-request code, better known as code splitting, without the overhead of webpack or other module bundlers. You can also conditionally load code in an if-else block if you like.

这个功能可以帮助你处理按需加载的代码，拆分代码，而且，并不需要 webpack 或者其它模块处理器。如果，你喜欢也可以在 if-else 块中加载代码。

The good thing is that you actually import a module, and so it never pollutes the global namespace.

在 if-else 块中引入一个模块，这样的好处是：不会污染全局命名空间。

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.26.27-PM.png)

## #3: Nullish Coalescing

## #3: 空值合并

Nullish coalescing adds the ability to truly check `nullish` values instead of `falsely` values. What is the difference between `nullish` and `falsely` values, you might ask?

空值合并可以真正的检查 `nullish` 值，而不是 `falsely` 值。你或许会问：`nullish` 和 `falsely` 之间有什么不同呢？

In JavaScript, a lot of values are `falsely`, like empty strings, the number 0, `undefined`, `null`, `false`, `NaN`, and so on.

在 Javascript 中有很多值都是 `falsely`。比如：空字符串、数字 0、`undefined` 、`null`、 `false` 、`NaN` 等。

However, a lot of times you might want to check if a variable is nullish – that is if it is either `undefined` or `null`, like when it's okay for a variable to have an empty string, or even a false value.

然而，很多情况下你只想检测一个变量是否为空值 -- `undefined` 或者 `null`，就像变量可以是一个空字符串甚至是一个假值。

In that case, you'll use the new nullish coalescing operator, `??`

在这个示例中，你将会看到新的空值合并操作符：`??`。

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.47.03-PM.png)

You can clearly see how the OR operator always returns a truthy value, whereas the nullish operator returns a non-nulllish value.

你可以清楚的看到 OR 操作符总是返回一个真值，但是，空值操作符返回一个非空值。

## #4: Optional Chaining
## #4：可选链

Optional chaining syntax allows you to access deeply nested object properties without worrying if the property exists or not. If it exists, great! If not, `undefined` will be returned.

可选链语法允许你访问嵌套更深的对象属性，而不用担心属性是否存在。如果，存在很好。反之，会返回 `undefined`。

This not only works on object properties, but also on function calls and arrays. Super convenient! Here's an example:

它不仅仅可操作对象属性，也可以操作函数的调用或者数组。这样更加方便！以下是个演示：

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.51.58-PM.png)

## #5: Promise.allSettled

The `Promise.allSettled` method accepts an array of Promises and only resolves when all of them are settled – either resolved or rejected.

`Promise.allSettled` 方法接收一组 Promise，并且会返回所有的结果 - 而不管是 resolved 还是 rejected。

This was not available natively before, even though some close implementations like `race` and `all` were available. This brings "Just run all promises – I don't care about the results" natively to JavaScript.

在之前，这是不可能的，尽管有些类似的实现比如：`race` 和 `all`。它只会“运行所有的 promise - 而不关心它们的结果”。

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.54.58-PM.png)

## #6: String#matchAll

`matchAll` is a new method added to the `String` prototype which is related to Regular Expressions. This returns an iterator which returns all matched groups one after another. Let's have a look at a quick example:

`matchAll` 是 `String` 原型链上的一个新增的方法，它可以关联正则表达式。它返回一个迭代器，一个接一个的返回所有匹配的组。我们来看一个演示：

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-8.59.14-PM.png)

## #7: globalThis

If you wrote some cross-platform JS code which could run on Node, in the browser environment, and also inside web-workers, you'd have a hard time getting hold of the global object.

如果，你写过那些可以运行在 Node、浏览器或者 web-workers 等跨平台的 JS 代码，你就会花费很多的时间去处理全局对象的问题。

This is because it is `window` for browsers, `global` for Node, and `self` for web workers. If there are more runtimes, the global object will be different for them as well.

这是因为不同平台全局对象也不同，浏览器中是 `window`，Node 中是 `global`，web workers 中是 `self`。如果，还有更多的运行环境，这个对象也会有不同。

So you would have had to have your own implementation of detecting runtime and then using the correct global – that is, until now.

因此，你自己必要检查运行环境来决定使用正确是全局对象。

ES2020 brings us `globalThis` which always refers to the global object, no matter where you are executing your code:

ES2020 给我们带来了 `globalThis` 对象，它始终会引用着全局对象，而不用关系代码在哪运行：

![](https://www.freecodecamp.org/news/content/images/2020/04/Screenshot-2020-04-03-at-9.02.27-PM.png)

## #8: Module Namespace Exports

## #8：导出模块的命名空间

In JavaScript modules, it was already possible to use the following syntax:

Javascript 模块中，一直都可以使用以下这种语法：

```js
import * as utils from './utils.mjs'
```

However, no symmetric `export` syntax existed, until now:

然而，直到现在还不可以像以下这样使用 `export` 语法：

***译者注：目前是支持的（2020-04-24）***

```js
export * as utils from './utils.mjs'
```

This is equivalent to the following:

以上和以下结果相同：

```js
import * as utils from './utils.mjs'
export { utils }
```

## #9: Well defined for-in order

## #9：明确定义 for-in 的顺序

The ECMA specification did not specify in which order `for (x in y)` should run. Even though browsers implemented a consistent order on their own before now, this has been officially standardized in ES2020.

ECMA 规范中并没有明确定义 `for (x in y)` 的顺序。尽管，在此之前浏览器实现了一致的顺序，但是，现在已经被纳入到 ES2020 的官方规范中了。

## #10: import.meta

The `import.meta` object was created by the ECMAScript implementation, with a [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null) prototype.

`import.meta` 是由 ECMAScript 创建实现的，默认为 [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)。

Consider a module, `module.js`:

考虑一下这个模块，`module.js` ：

```html
<script type="module" src="module.js"></script>
```

You can access meta information about the module using the `import.meta` object:

你可以通过 `import.meta` 对象访问模块的相关 meta 信息：

```js
console.log(import.meta); // { url: "file:///home/user/module.js" }
```

It returns an object with a `url` property indicating the base URL of the module. This will either be the URL from which the script was obtained (for external scripts), or the document base URL of the containing document (for inline scripts).

它返回一个包含 `url` 属性的对象，该属性代表着模块的 URL。它可能是获取脚本的 URL（对于外部脚本来说），或者是包含模块文档的基础URL（对于内联脚本来说）。

## Conclusion

## 总结

I love the consistency and speed with which the JavaScript community has evolved and is evolving. It is amazing and truly wonderful to see how JavaScript came from a language which was booed on, 10 years go, to one of the strongest, most flexible and versatile language of all time today.

我喜欢 Javascript 社区不断发展的一致性和速度。看看 Javascript 如何从一种经过十年的嘘声演变成如今这种最强大、最灵活和最通用的语言，真的很神奇。

 Do you wish to learn JavaScript and other programming languages in a completely new way? Head on to a [new platform for developers](https://codedamn.com/) I'm working on to try it out today!

你是否希望用一种全新的方式学习 Javascript 和其它的编程语言？我正在开发一个[全新的开发者平台](https://codedamn.com/) ，现在可以试下！

What's your favorite feature of ES2020? Tell me about it by tweeting and connecting with me on [Twitter](https://twitter.com/mehulmpt) and [Instagram](https://instagram.com/mehulmpt)!

ES2020 中功能哪个是你喜欢的呢？可以在 [Twitter](https://twitter.com/mehulmpt) 和 [Instagram](https://instagram.com/mehulmpt) 上联系告诉我！

This is a blog post composed from my video which is on the same topic. It would mean the world to me if you could show it some love!

我的视频也有相同专题的内容。如果您能表达爱意，那就对我来说意味着世界！

<https://www.youtube.com/watch?v=Fag_8QjBwtY />

