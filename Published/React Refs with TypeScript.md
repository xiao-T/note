## React Refs with TypeScript
## 在 React 如何用 TypeScrpt 处理 Refs

> 原文地址: <https://medium.com/@martin_hotell/react-refs-with-typescript-a32d56c4d315>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

> *🎒* this article uses following library versions:
>
> 这篇文章用到以下版本的依赖库：

```json
{
  "@types/react": "16.4.7",
  "@types/react-dom": "16.0.6",
  "typescript": "3.0.1",
  "react": "16.4.2",
  "react-dom": "16.4.2"
}
```

> *🎮* [source code can be found on my github profile](https://github.com/Hotell/blogposts/blob/master/2018-08/react-ts-refs/)
>
> *🎮* [可以在我的 giuhub 上查看源代码](https://github.com/Hotell/blogposts/blob/master/2018-08/react-ts-refs/)



----

Recently I’ve got this message on Twitter

最近，在 Twitter 我收到了这条信息

> [@martin_hotell](https://twitter.com/martin_hotell) [I'm a React + Typescript fan and I'm having a hard time figuring out how to properly type out React refs.  Do you have any tips on how to do that?](https://twitter.com/code_e_averett/status/1025536674472886272)

> [@martin_hotell](https://twitter.com/martin_hotell)[我是的 React + TypeScript 的粉丝，我一直在努力找出正确处理 React refs 类型的方式。对此，您有什么好的方法吗？](https://twitter.com/code_e_averett/status/1025536674472886272)

Instead of replying, I’ve decided to write down this “short post” about how to handle React DOM refs and ref forwarding with TypeScript for anyone new to React and TypeScript as I didn’t found this resource anywhere online.

为了回应这个问题，我决定写下这边文章向那些刚接触 React 和 TypeScript 的新手解释如何处理 React Dom refs 和 转发 ref，我发现在网上对此没有任何资源可供参考。

> **Disclaimer:**     
>
> Don’t expect to learn all the why’s and how’s about React refs within this blogpost ( you can find all that info in excellent [React docs](https://reactjs.org/docs/refs-and-the-dom.html)).
>
> This post will try to mirror those docs a bit for easy context switching.

> **声明：**
>
> 别指望在这篇文章中学到所有有关 React refs 的知识（你可以在 [React 文档](https://reactjs.org/docs/refs-and-the-dom.html)中找到所有的知识点）。
>
> 在这篇文中我将会引用文档中的一些知识点以便理解。

**What are Refs in React ?**

**React 中的 refs 是什么？**

> *Refs provide a way to access DOM nodes or React elements created in the render method.*
>
> *通过 Refs 开发者可以访问 DOM 节点或者在 render 方法中创建的 React 元素*。



Let’s create some React refs with TypeScript 👌👀

我们来用 TypeScript 创建一些 React refs 👌👀



### Creating Refs

### 创建 Refs

```jsx
class MyComponent extends Component {
  // we create ref on component instance
  private myRef = createRef()
  render() {
    return <div ref={this.myRef} />
  }
}
```

But with that component definition, we get compile error:

但是，这个组件编译时会报错：

```
[ts]
Type 'RefObject<{}>' is not assignable to type 'RefObject<HTMLDivElement>'.
```

Uh oh? What’s going on here ? TypeScript has very good type inference especially within JSX, and because we are using ```ref``` on an ```<div>``` element, it knows that the ref needs to be a type of ```HTMLDivElement```. So how to fix this error?

哦？发生了什么？TypeScript 有良好的类型实例尤其是对 JSX ，因为我们在 ```div``` 元素上定义了 ```ref```，它知道 ref 应该是一个 ```HTMLDivElement``` 类型。所以，该怎么修复这个错误呢?

> ```React.createRef()``` *is an generic function*
>
> ```React.createRef()``` 是一个泛型函数



```typescript
// react.d.ts
function createRef<T>(): RefObject<T>
```

What about the return type, the ```RefObject<T>``` ? Well that's just a ```Maybe``` type with following interface

它返回什么类型呢，```RefObject<T>```？它只是一个具有以下接口的 ```Maybe``` 类型

```typescript
// react.d.ts
interface RefObject<T> {
  // immutable
  readonly current: T | null
}
```

With that covered, you already know how to make our code valid right ? 👀 We need to explicitly set the generic value for ```createRef```:

有了这个，你就应该知道如何修复我们的代码？👀我们需要明确的给 ```createRef``` 设置一个泛型值：

![](https://cdn-images-1.medium.com/max/800/0*sDk2dBcz_rGP6EJ2.png)

### Accessing refs

### 访问 refs

> *When a ref is passed to an element in* ```render```*, a reference to the node becomes accessible at the* ```current```*attribute of the ref.*
>
> *当一个 ref 在* ```render``` *传递给一个元素时*，*这个 ref* ```current``` *属性就会变成一个可访问节点的引用*。

If we wanna access our previously defined ref value, all we need to do is to get the ```current``` value from the ref object

如果，我们想访问之前定义的 ref，我们只需访问 ref 对象中属性 ```current``` 即可

```js
const node = this.myRef.current
```

Although, our ```node``` variable is gonna be a Maybe type 👉 ```HTMLDivElement``` OR ```null``` ( remember? ```RefObject<T>```interface... ).

由于，我们的变量 ```node``` 是一个 Maybe 类型：```HTMLDivElement``` 或者 ```null``` （记住 ```RefObject<T>``` 接口）

So if we would like to execute some imperative manipulation with that ```node```we just couldn't do:

因此，如果我们想让 ```node``` 执行一些操作，就不能这么做：

```typescript
class MyComponent extends Component {
  private myRef = React.createRef<HTMLDivElement>()
  focus() {
    const node = this.myRef.current
    node.focus()
  }
}
```

with that code, we would get an compile error

以上代码，还是会得到编译错误

```
[ts] Object is possibly 'null'.
const node: HTMLDivElement | null
```

You may think right now: “**this is annoying, TypeScript sucks**…” **Not so fast partner 😎!** TypeScripts just prevents you to do a programmatic mistake here which would lead to runtime error, even without reading a line of the docs ❤️.

你现在应该会想："**TypeScript 好烦…"先不要这么快下结论**😎！TypeScripts 只是让你避免代码中的失误导致运行时的错误，即便你没有看过文档❤️。

What React docs say about ```current``` value ?

React 文档中如何描述 ```current```的呢？

> *React will assign the current property with the DOM element when the component mounts, and assign it back to null when it unmounts. ref updates happen before componentDidMount or componentDidUpdate lifecycle hooks.*
>
> *当组件挂载时，React 将会把 DOM 元素分配给 cureent 属性，组件卸载时分配 null 给它。ref 会在componentDidMount 或者 componentDidUpdate 两个生命周期之前更新。* 

That’s exactly what TS told you by that compile error! So to fix this, you need to add some safety net, an ```if``` statement is very appropriate here ( it will prevent runtime errors and also narrow type definition by removing ```null``` ):

这就是为什么 TS 会告诉你编译错误了！为了修复这个错误，你需要添加一些安全措施，一个 ```if``` 语句非常合适（它可以通过删除 ```null``` 来避免运行时的错误和减少类型定义）：

![](https://cdn-images-1.medium.com/max/800/0*EkhFXqAkwk3H4bTx.png)

Also we get autocomplete to whole ```HTMLDivElement``` DOM api. Lovely!

我们还可以通过完整的 ```HTMLDivElement``` DOM API 实现自动完成。这很好！

![](https://cdn-images-1.medium.com/max/800/0*gzeeS5C5h2tBDbv4.gif)

> **Curious reader may ask:**
>
> *What about accessing refs within* ```componentDidMount``` *if we don't wanna encapsulate our imperative logic within a method  ( because we are messy/bad programmers 😇 ) ?*
>
> **好奇的读者可能会问：**
>
> *如果，我们不想把逻辑代码放在一个方法中，如何在* ```componentDidMount``` *访问 refs 呢（我们不是一个很好的程序员😇）？*

I hear you… Because we know that our refs ```current``` value is definitely gonna be available within ```componentDidMount```, we can use TypeScript's Non-null assertion operator 👉 👉 👉 ```!```

我听到了… 因为，我们知道在 ```componentDidMount``` refs ```current``` 的值是有效的，我们可以使用 TypeScript 的 非空断言操作符👉 👉 👉 ```!```

![](https://cdn-images-1.medium.com/max/800/0*nt33khQnmlXXWk79.png)

That’s it!

就这样！

> *But hey, I really recommend encapsulating the logic to separate method with descriptive method name. Ya know readable code without comments and stuff 🖖 …*
>
> *但是，我真心的推荐把逻辑代码整合到一个单独的方法中，并定义一个具有语义的方法名。都知道代码可读性的重要性*

### Adding a Ref to a Class Component

### 类组件添加 Ref

If we wanted to wrap our *MyComponent* above to simulate it being focused immediately after mounting, we could use a ref to get access to the MyComponent instance and call its ```focus``` method manually:

如果，我封装我们的 *MyComponent* 模拟自动获取焦点的效果，我们可以用一个 ref 访问 MyComponent 实例，然后手动执行 ```focus``` 方法：

```jsx
import React, { createRef, Component } from 'react'
class AutoFocusTextInput extends Component {
  // create ref with explicit generic parameter 
  // this time instance of MyComponent
  private myCmp = createRef<MyComponent>()
  componentDidMount() {
    // @FIXME
    // non null assertion used, extract this logic to method!
    this.textInput.current!.focus()
  }
  render() {
    return <MyComponent ref={this.textInput} />
  }
}
```

> ***Note I:*** *this only works if* ```MyComponent``` *is declared as a class*
>
> ***Note II:*** *we get access to all instance methods as well + top notch DX thanks to TypeScript*

> ***提示1：*** *只有* ```MyComponet``` *是类组件时才有效*
>
> ***提示2：*** *得益于 TypeScript 我们可以很好的访问实例所有的方法和 top notch DX*

![](https://cdn-images-1.medium.com/max/800/0*myrP1cDlS13K7hi1.gif)

Beautiful isn’t it ? 🔥

很完美，是吧？🔥



### Refs and Functional Components

### Refs 和 函数组件

> *You may not use the ref attribute on functional components because they don’t have instances*
>
> *你不能在函数组件上使用 ref 属性，因为它们没有任何实例对象*

You can, however, use the ref attribute inside a functional component as long as you refer to a DOM element or a class component:

然而，你可以在函数组件内部像类组件一样使用 ref 属性，并引用一个 DOM 元素：

![](https://cdn-images-1.medium.com/max/1200/0*VQsIacTmuMxA4mxI.png)

----

### Forwarding Refs

### Ref 转发

> [*Ref forwarding is a technique for automatically passing a ref through a component to one of its children.*](https://reactjs.org/docs/forwarding-refs.html)
>
> [*Ref 转发是一项将 ref 自动地通过组件传递到其一子组件的技巧*](https://reactjs.org/docs/forwarding-refs.html)

### Forwarding refs to DOM components

### 转发 refs 到 DOM 组件

Let’s define ```FancyButton``` component that renders the native button DOM element:

我们来定义一个返回原生 button DOM 元素的 ```FancyButton``` 组件：

![](https://cdn-images-1.medium.com/max/800/0*VT3hondWMD59xVse.png)

> *Ref forwarding is an opt-in feature that lets some components take a ref they receive, and pass it further down (in other words, “forward” it) to a child.*
>
> *转发 ref 是内置的功能，可以让一些组件接收一个 ref，并向下传递给子元素（换句话说，就是"转发"）*

Let’s add ref forwarding support to this component, so components using it, can get a ref to the underlying button DOM node and access it if necessary — just like if they used a DOM button directly.

我们来给这个组件添加转发 ref 的功能，使用这个组件，你可以得到一个关联 button DOM 节点的 ref，如果，有必要还可以访问它，就像直接访问 DOM button 一样。

![](https://cdn-images-1.medium.com/max/1200/0*gqeWcr1NsU45VdOC.png)

What’s happening here?

1. we create and export ```Ref``` type for consumers of our ```FancyButton```
2. we use ```forwardRef``` to obtain the ref passed to it, and then forward it to the DOM button that it renders. Again this function is a generic function which consists of 2 generic arguments:

发生了什么？

1. 我们创建并导出了 ```Ref``` 类型供 ```FancyButton```
2. 我使用方法 ```forwardRef``` 传递 ref，然后转发给 DOM button。这个方法也是一个泛型函数有两个泛型参数组成：

```
// react.d.ts
function forwardRef<T, P = {}>(
  Component: RefForwardingComponent<T, P>
): ComponentType<P & ClassAttributes<T>>
```

Where:

- ```T``` is type of our DOM element
- ```P``` is type of our props
- return type is the final component definition with proper props and ref types ```ComponentType<P & ClassAttributes<T>>```

Where:

- ```T``` 是 DOM 元素类型
- ```P``` 是 props 类型
- 最终返回一个由特定属性和 ref 组成的组件，类型是 ```ComponentType<P & ClassAttributes<T>>```

Now we can use it type-safe way:

现在，我们可以使用类型安全模式：

![](https://cdn-images-1.medium.com/max/800/0*OWdwypfLrHqpjoq2.gif)

### Forwarding refs in higher-order components

### 高阶组件中使用转发 refs

Forwarding refs via HoC is quite tricky as can be seen in [React docs](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components).

通过 HoC 转发 refs 非常的巧妙，你可以看看 [React 文档](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)。

TL;DR: you need to explicitly return a wrapped render of your HoC via ```forwardRef``` API

TL;DR: 你需要用 ```forwardRef``` 方法包装 HoC 并返回它

```jsx
return forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />
})
```

Now question is, how to handle this pattern with TypeScript…

现在的问题是，如何用 TypeScript 处理这种情况...

Let’s cover this shall we ?

我们来讨论一下？

Here is our ```FancyButton``` TypeScript implementation:

这是我们用 TypeScript 实现的 ```FancyButton```：

![](https://cdn-images-1.medium.com/max/800/0*UjpfayGYeTWTW0XD.png)

And this is how we wanna use it within our App:

以下是我们如何在 App 使用的：

![](https://cdn-images-1.medium.com/max/800/0*KYS1Xxug9Pg-ugjt.png)

And finally let’s implement ```withPropsLogger``` HoC with ref forwarding support.

最后，我们来实现 HoC ```withPropsLogger``` 并添加对转发 ref 的支持。

![](https://cdn-images-1.medium.com/max/2560/0*Rn1NW445-BcLvP3Q.png)

With our forwardRef aware HoC implemented, we need to pass explicitly generic arguments to ```withPropsLogger```

支持 ref 转发的 HoC 已经实现，我们需要明确的传递泛型参数给 ```withPropsLogger```

```tsx
const EnhancedFancyButton = withPropsLogger<
  FancyButton, 
  FancyButtonProps
>(FancyButton)
```

And finally we can use it in type-safe way! Let’s check it out:

然后，我就可以使用安全类型模式！让我们来看看：

![](https://cdn-images-1.medium.com/max/800/0*Rkp7RYK65NVE8-YB.gif)

*And we’re at the end. Now go, TypeScript all the React refs things! 💪 💪 ⚛️️️️️️️*

*到这我们接近尾声了。现在，开始用 TypeScript 来处理 React refs 吧！💪 💪 ⚛️️️️️️️*

---

*As always, don’t hesitate to ping me if you have any questions here or on Twitter (my handle* [@martin_hotell](https://twitter.com/martin_hotell)*) and besides that, happy type checking folks and ‘till next time! Cheers! 🖖 🌊 🏄*

*和往常一样，如果，你有任何问题都有可以在 Twitter 找我（[@martin_hotell](https://twitter.com/martin_hotell)），除此之外，happy type checking folks and ‘till next time! 干杯！🖖 🌊 🏄*