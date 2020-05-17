# Some Lesser Known TypeScript Features

## 那些鲜为人知的 TypeScript 功能

> 原文地址: <https://medium.com/articode/some-lesser-known-typescript-features-d067e29797d0/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In the past few years, TypeScript has become a popular way to write JavaScript apps. The language is immense and can do a lot of things.

过去的几年中，TypeScript 已经成为编写 JavaScript 应用的流行方式。这种语言非常强大，能做很多事情。

![](https://miro.medium.com/max/1400/1*ui0RIOYORyfWoOlbSDSFGA.jpeg)

Here’s a short list of some rarer TypeScript features you may not be aware of to help with your development:

在这我列举了一些 TypeScript 罕见的功能，在你的开发过程中它们可以帮到你：

1. You can write numbers with underscores. For example, `1000000000` can be written as `1_000_000_000`. It will be compiled to the regular number in Javascript, but using this syntax makes things easier to read. The underscores can be placed anywhere in the number although writing `25` is more readable than `2_5`. Another good use case for this syntax is monetary amounts. `2545` may represent $25.45 in your app. This can be written as `25_45` instead.
   (As mentioned by a commenter on Reddit this is also a stage 2 Javascript proposal: https://github.com/tc39/proposal-numeric-separator)

   你可以是用下划线的方式定义 numbers。比如：`1000000000` 可以写成 `1_000_000_000`。在 JavaScript 中它将会被编译成正常的 number，但是，这种语法让内容更易于阅读。下划线可以放在数字的任何位置，尽管，`25` 比 `2_5` 更易读。另外一个使用场景是表示货币金额。在你的应用中 `2545` 可能代表着 $25.45。因此，可以用 `25_45` 来代替。

   （在 Reddit 有人提到，这种语法已经是 stage 2  Javascript 提案 : https://github.com/tc39/proposal-numeric-separator）

   **译者注：目前已经是 stage 3（2020-05-17）**

2. If you know a variable is defined you can add a `!` after it to access it without first checking if the value is `undefined`. For example, you may be injecting props into a React component using a decorator (e.g. `@inject(router)` or `@inject(store)`). You can now write `this.props.store!.someValue` even if the definition of store is `store?: Store`. Be careful not to overuse this feature, but there are times where it comes in handy. You can read [here](https://github.com/mobxjs/mobx-react/issues/256) for further discussion of this problem. (You could always use an `if` statement too, but that’s more verbose for cases you know the if statement is truthy).

   如果，你知道一个变量已经定义，你可以在变量后面添加 `!` 来访问它，而不用检查它是否为 `undefined`。例如，你通过 decorator 向 React 组件注入 props（比如：`@inject(router)` 或者 `@inject(store)`）。现在，你可以直接这么写 `this.props.store!.someValue`，即使，定义了 `store?: Store`。虽然，在某些情况下它非常方便，但是，要小心、不要过度使用这个功能。你可以在[这](https://github.com/mobxjs/mobx-react/issues/256)了解一下有关这个功能的讨论。（你还是可以使用 `if` 语句，但是，对那些明显是真值的情况，这显得有点多余）

3. You can set a variable to have type `never`. This means it can’t be set to anything. How is this useful? One place it can be useful is to check you’ve handled every possibility of a switch statement. For example:

   你可以给变量设置 `never` 类型。这代表它不能被设置成任何值。这有什么用呢？这在 switch 语句中非常有用，可以用来检查是否覆盖了所有的情况。例如：

```ts
export function unreachable(x : never) {
  throw new Error(`This should be unreachable! but got ${x}`)
}const someFunction = (temperature: 'hot' | 'cold' | 'warm') => {
  switch (temperature) {
    'hot': console.log("it's hot outside!"); break;
    'cold': console.log("it's cold outside!"); break;
    default: unreachable(temperature);
  }
}
```

The above code will throw a compile-time error, because it’s possible for `unreachable()` to be called. If you add a switch case for `warm` with a break after it, the compile error will go away as the default block can no longer be reached.

因为，`unreachable()` 很有可能会被调用，上面的代码将会抛出一个编译错误。如果，你在代码块中添加一段有关 `warm` 的代码，这个编译错误就会消失，default 也就不会被执行了。

Another potential use case for `never` is a function containing an infinite loop or a function that always throws an exception:

`never` 的另外一种使用情况是：一个无限循环的函数或者总是抛出异常的函数：

```ts
function loop(fn: Function): never {
  while(true) fn();
}
```

Thanks to [James Meyers](https://medium.com/u/a2ed4fb0055d?source=post_page-----d067e29797d0----------------------) in the comments for these use cases.

多谢 [James Meyers](https://medium.com/u/a2ed4fb0055d?source=post_page-----d067e29797d0----------------------) 在评论中提出这些使用场景。

4. Another lesser used type you can use is `unknown`. This just means we have no idea what the item we have is. This may happen if we’re calling a third party API for example. If we try do `x.substr(0, 2)` it will throw a compile error. We can use an `unknown` variable by first doing a type check. For example, writing `if (typeof x === “string”) x.substr(0, 2)` would no longer throw an error.

   另外一个少用的类型是 `unknown`。这代表着我们对当前的变量一无所知。如果，我们调用了第三方的 API 就会出现这种情况。如果，我们执行 `x.substr(0, 2)` 这条语句，它将会抛出一个编译错误。我们可以使用 `unknown` 定义变量，然后做一次类型检测。例如：`if (typeof x === “string”) x.substr(0, 2)` 这样不会抛出错误了。

`unknown` is both very similar to `any` and the complete opposite. They’re similar in so far as with both types we don’t know what the type is. They’re the complete opposite in that `any` will never throw a type error, but `unknown` will always throw an error until we identify what it is.

`unknown` 和 `any` 两者非常相似，又完全相反。两者在类型非常相似，我们不知道两种类型的变量是什么。但是，两者又完全相反，`any` 永远不会抛出类型错误，但是，`unknown` 总会抛出错误，直到我们明确变量是什么类型。

```ts
const x: any;
x.substr(0, 2); // doesn't throw a compilation error
const y: unknown;
y.substr(0, 2); // throws an error as we don't know what y is
if (typeof y === 'string') y.substr(0, 2); // doesn't throw an error
```



Looking for some more tricks, check out this post:

更多的使用技巧，可以看以下的文章：

<https://codewithstyle.info/Comprehensive-list-of-useful-built-in-types-in-TypeScript/>

<https://juejin.im/post/5eb6d9cde51d454de777297d>

Know any other useful TypeScript tricks that should be included? Feel free to mention them in the comments!

如果，你知道更多有关 TypeScript 的使用技巧？可以在评论中告诉我！

To see more articles like this in the future, be sure to give me a follow :)

想看更多的文章，可以订阅我的博客。

You can get in touch with me at [elie.tech](https://elie.tech/).

你还可以通过 [elie.tech](https://elie.tech/) 联系我。