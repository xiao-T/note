# Advanced TypeScript Types with Examples

## 通过示例演示 TypeScript 的高级类型

> 原文地址: <https://levelup.gitconnected.com/advanced-typescript-types-with-examples-1d144e4eda9e/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

Improve your understanding of TypeScript and learn these advanced techniques to help you master the language and utilize TypeScript with React

提升你对 TypeScript 的理解，并学这些高级的技术，可以帮助你掌握该语言并且可以更好的在 React 中使用 TypeScript。

![](https://miro.medium.com/max/2700/1*Su5pVgmuDMgBnKMH3hhkBQ.jpeg)

When I began using TypeScript last winter, I have grown from defaulting to using `any` for all types more complex than a `string` or `number` to now feeling comfortable with the usage of advanced built-in types and custom types. Add types to your JavaScript code by switching to TypeScript to write air-tight applications. This article will provide examples of using advanced types and also how to use them in a React application.

去年冬天，我开始使用 TypeScript，我已经从一个使用 `any` 的新手逐渐成长为一个习惯使用高级内置类型和自定义类型的老手。通过在 JavaScript 代码添加类型判断，让应用变得更加健壮。这篇文章提供了一些使用高级类型的示例，也展示了如何在 React 应用使用它们。

We will discuss the `Record`*,* `Partial`*,* `Required`*,* `Pick` and a custom `Omit` types.

在这里，我们将会探讨 `Record`、`Partial`、 `Required`、 `Pick` 和一个自定义`Omit` 类型。

## Record

A very useful built-in type [introduced by Typescript 2.1](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#typescript-21) is `Record`: it allows you to created a typed `map` and is great for creating composite interfaces. To type a variable as `Record`, you have to pass a string as a key and some type for its corresponding value. The simplest case is when you have a `string` as a value:

[Typescript 2.1 引入了](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#typescript-21)一个非常有用的内置 type `Record`：它可以创建类型 `map`，而且，非常适合创建复合型的 interface。为了让变量成为 `Record` 类型，你需要传入一个字符串作为 key 和一些相关的 type。最简单情况是，你有一个 `string` 作为值的类型。

```ts
const SERVICES: Record<string, string> = { 
    doorToDoor: "delivery at door",
    airDelivery: "flying in",
    specialDelivery: "special delivery",
    inStore: "in-store pickup",
};
```

This may appear trivial, but it provides easy typing in your everyday code. One of the popular cases when `Record` works in well is an interface for a business entity that you keep in a dictionary as key-value pairs. This model could represent a collection of contacts, events, user data, transportation requests, cinema tickets, and more. In the following example, we create a model for products that a user could add to her cart:

这显得微不足道，但是，它为你日常编码中提供更简单的方式定义类型。一种常见的情况是：当你需要把整个业务整体的 interface 作为键值对保存在字典中时，`Record` 就非常有用。这个 model 可以表示联系人、事件、用户数据、交通请求、电影票据等，各种集合。在接下来的演示中，我们为 product 创建了一个 model，用户可以添加到购物车：

![](https://miro.medium.com/max/1400/1*SlIL-nD7yMgKDgLrEkKo_Q.png)

You see how the editor autocompletion will help us to define a typed object and will mark the variable with an error because not all the required properties are defined:

你会看到编辑器是如何自动帮我们定义对象类型的，同时也会标记出错误提示，这是因为一些必要的属性没有定义：

![img](https://miro.medium.com/max/2000/1*P9AmX5SnG1TDngdZYuvOdg.png)

Also, Typescript does not allow us to create an empty object for some defined shape and then populate it with properties, but here `Record` will come to the rescue.

另外，TypeScript 不允许我们为一些定义好的 type 创建空对象，需要提供相关的属性，但是，这时 `Record` 就有用处了。

It is also possible to use a `string` `enum` as a key in the `Record` type. For example, we will use `ErrorsEnum` to keep and access possible error values (messages):

另外，也可以用 `string` `enum` 作为 `Record` 的 key。例如，我们将会用 `ErrorsEnum` 来保存访问相关的错误信息：

![](https://miro.medium.com/max/1400/1*5EUEcXmo8nfmsB8FPvnDhg.png)

> *Let’s see how you can use it for type enhancing when working with* [Material-UI](https://material-ui-next.com/) *library. As the guide says, you can add your custom styles with* [CSS-in-JS notation](https://material-ui-next.com/customization/css-in-js/) *and inject them via* `withStyles` *HOC. You can define styles as a function that takes a* `theme` *as an argument and returns the future* `className` *with correspondent styles and you want to define a type for this function:*
>
> *我们来看看在 [Material-UI](https://material-ui-next.com/) 中它是如何增强类型的。就如指南中所说，你可以使用  [CSS-in-JS](https://material-ui-next.com/customization/css-in-js/) 添加自定义的样式，然后，通过 `withStyles` HOC 注入。你可以通过一个函数定义样式，函数接受一个名 `theme` 的参数，然后，返回相关样式的 `className`，还可以为这个函数定义类型：*

![](https://miro.medium.com/max/1400/1*yEY0CgvEhoFgxXE3KF8nHg.png)

You notice that it can become very annoying to add these `as CSSProperties` for every style object. Alternatively, you can use the benefit of the `Record` type and define a type for the `styles` function:

你应该注意到，因为每个样式对象添加了 `as CSSProperties`，所以变得非常麻烦。另外，你就可以使用 `Record` 带来的好处：定一个带有类型的 `styles` 函数：

![](https://miro.medium.com/max/1400/1*okfK0Q1VYfQKzKaaVL-9zg.png)

Now you can use it safely in every component and get rid of hardcoding type of CSS properties in your styles.

现在，你可以在任何组件中安全的使用它，并且会摆脱明确定义 CSS properties 的束缚。

## Partial and Required

`Partial` type makes all properties in the object optional. It could help you in many cases, like when you’re working with the data that a component would render but you know that the data may not be fetched at the moment of mounting:

`Partial` 可以让对象中所有的属性变成可选的。在很多情况它可以帮到你，比如，当你需要数据渲染组件时，但是，你知道在组件 mount 时并不会加载数据：

![](https://miro.medium.com/max/1104/1*11cUeBpNXN7Wf9I_xmdw6A.png)

Or you can use `Partial` to define some of the props as default props for your component:

你还也可以用 `Partial` 来为组件定义默认的 props。

![](https://miro.medium.com/max/1400/1*YbGQpSVa0Ju9F2-5Dp-t7w.png)

As the opposite, the `Required` built-in type introduced in Typescript v2.8, makes all properties of a described object required:

相反，TypeScript v2.8 中引入的 `Required` ，可以让对象中的所有的属性变成必选的：

![](https://miro.medium.com/max/1400/1*bspXbS_te7-x_c_dckM6Cg.png)

One of the use cases for Required is **selectors**: sometimes you want to make a selector for a property from a nested object and you know that at the moment of selector invocation this property will be defined. You may point it out with a typing:

Required 的用例之一就是 **selectors**：有时你想为嵌套对象中的属性创造选择器，并且，你知道在选择器调用时将会定义此属性。你可以为此指定一个类型：

![](https://miro.medium.com/max/1400/1*F1nDgrYxupjrFSiJturisQ.png)

This may look like a cheat and it can cause type errors if you start to inherit required properties from optional ones, so be careful!

这看起来像是作弊，如果，你从可选属性继承必选属性可能会引起类型错误，因此，要小心使用！

> *Maybe it sounds stupid but it’s not a rare situation when you have code generated automatically and all interfaces that are in your hands are* Partial *and all elements of your UI want only* Required. *Here you’ll start to check every nested object on* undefined 😨.
>
> *听起来很傻，但是，如果你的代码是自动生成的，并且，你所有的 interface 都是* Partial，*UI 中所有的元素都是* Required，*这种情况并不稀奇。这时你需要检查所有*  undefined *的对象* 😨。

## Pick and Omit

Have you ever tried to narrow a type because you realized that your next class doesn’t need this bunch of properties? Or maybe you arrived at this point in the process of refactoring, trying to distribute pieces of a system in a new way. There are several types that can solve this problem.

曾经，你是否想过缩减一连串的类型，因为，你意识到下一个 class 并不需要这么多属性？或许你在重构时遇到这类问题，尝试以一种全新的方式分布系统的每一部分。这里有几种方式可以解决这类问题。

`Pick` helps you to use a defined already interface but take from the object only those keys that you need.

`Pick` 可以让你在一个已经定义好的 interface 中挑选你需要的 key。

`Omit` which isn’t predefined in the Typescript `lib.d.ts` but is easy to define with the `Pick` and `Exclude`. It excludes the properties that you don’t want to take from an interface.

`Omit` 在 TypeScript 的 `lib.d.ts` 中并没有预先定义，但是，它可以很容易通过 `Pick` 和 `Exclude` 来定义。它可以从一个 interface 中排除掉你不想要的属性。

At both of these images, `ProductPhotoProps` will contain all `Product` properties except of name and description:

下面两张图片中，`ProductPhotoProps` 会包含 `Product` 所有的属性，name 和 descripition 除外：

![](https://miro.medium.com/max/552/1*AJL4dNcuLV4fHB7lbKZ91g.png)

![](https://miro.medium.com/max/1104/1*47qUKESj3sL5Jhma5kZQ0Q.png)

> *One of a practical example of such a situation from my current project is a refactoring a large form with a complex fields dependencies. There was* `FormProps` *type where errors field was included. After re-thinking this architecture the errors became unnecessary for a first child component but still needed by the second one. I used Pick to take a portion of fields except for errors for a new interface and it worked well.*
>
> *其中一个实际示例，就来自我的项目：重构一个有着复杂依赖的庞大表单。有一个 `FormProps` 它包含了错误类型。重新思考后，对于第一个子组件这些错误类型并不是必要的，但是，第二个组件仍然需要。除了错误类型，我用 Pick 提取了一些属性构建了一个新的 interface，这种方式工作的很好。*

There are, of course, different ways to combine types and define their relationship. If you start to decompose a large thing into small pieces from the beginning, maybe you will solve the problem of excluding properties from an object from another side. You would instead extend types.

当然，有多种方式可以合并类型和定义它们之间的关系。如果，从一开始你就把很大一块东西拆解成很多小块，你或许解决了从对象中排除属性的问题。但是，你会遇到扩展类型的问题。

## Extending type/interface

## 继承扩展 type/interface

When you extend an interface, all properties described in a source interface/type will be available in a result interface. Let’s see how we could combine small interfaces into one that corresponds to our task:

当你需要扩展一个 interface 时，所有的属性在新的 interface 都有效。我们来看看，如何合并多个小的 interface 以便符合的我们的任务需求：

![](https://miro.medium.com/max/1400/1*CgnIw2r9gYNgWzT5HLeL1w.png)

This method is not as handy because you have to imagine the shape of your objects in advance. On the other hand, it is fast and easy which makes it cool for prototyping or building simple UI like rendering data into read-only blocks.

这种方法并不是很方便，因为，你必须更有预见性考虑你的对象。从另一方面来讲，它更加快速和简单，让你设计原型或者构建简单 UI 更加炫酷，就像是把数据选到一个只读块中。

## Summary

## 总结

We have explored some of the popular pre-defined TypeScript types with the real code examples. The project we used is only a demo but all of these types work at least in one of the real-world apps that I know 😉. I hope this article was useful for you and encouraged you to not be afraid of writing typings. I created a [repo](https://github.com/ainalain/sandbox) with a React-Redux SPA where you find most of these examples or their alternatives.

通过一些真实代码，我已经介绍了比较流行的 TypeScript 内置的类型。这只是一个 demo，但是，我知道这所有的类型至少会在一个真实场景中有效😉。我希望这篇文章能帮助到你，并且，鼓励你不要害怕 TypeScript。我用 React-Redux 创建了一个 SPA 的[代码仓](https://github.com/ainalain/sandbox)，在这里你可以找到大多数的演示或者它们的替代方案。

However, I would like to say one more thing about static typing. Often, when you explore a new technology or face a challenge during a feature development, you start to solve a technical problem and may forget about your goal. Static typing is not a goal of your work, it’s just a tool. If it becomes a central thing of a project, it’s a sign that you got carried away 🚀. Remember about balance in business/tech parts of your app and happy coding!

然而，针对静态类型我还想多说一些。通常，当你探索一种新技术或者面对一个开发中的挑战时，你开始解决技术问题，反而忘记了你的目的。静态类型并不是你工作的目的，它只是一个工具。如果，它成为了项目中的核心，这代表着你已经走偏了🚀。记住要在业务和技术之间做好平衡，编码快乐！

