## Deep dive: How do React hooks really work?
## 深入理解：React Hooks 是如何工作的？

> 原文地址: <https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

[Hooks](https://reactjs.org/hooks) are a fundamentally simpler way to encapsulate stateful behavior and side effects in user interfaces. They were [first introduced in React](https://www.youtube.com/watch?v=dpw9EHDh2bM) and have been broadly embraced by other frameworks like [Vue](https://css-tricks.com/what-hooks-mean-for-vue/), [Svelte](https://twitter.com/Rich_Harris/status/1093260097558581250), and even adapted for [general functional JS](https://github.com/getify/TNG-Hooks). However, their functional design requires a good understanding of closures in JavaScript.

从根本上讲 [Hooks](https://reactjs.org/hooks) 就是在与用户的交互中对 state 行为和副作用的简单封装。它们[首先由 React 提出的](https://www.youtube.com/watch?v=dpw9EHDh2bM)，然后，其他框架也引入了 Hooks，比如：[Vue](https://css-tricks.com/what-hooks-mean-for-vue/), [Svelte](https://twitter.com/Rich_Harris/status/1093260097558581250)，甚至[普通的 JS](https://github.com/getify/TNG-Hooks) 也做了适配。然而，为了更好设计这些功能需要很好的理解 JavaScript 中的闭包。

In this article, we reintroduce closures by building a tiny clone of React Hooks. This will serve two purposes – to demonstrate the effective use of closures, and to show how you can build a Hooks clone in just 29 lines of readable JS. Finally, we arrive at how Custom Hooks naturally arise.

在这片文章中，我通过创建一个简版的 React Hooks 来重新介绍闭包。这主要有两个目的 — 演示的闭包的作用和如何用29行 JS 代码实现 Hooks。最后，自定义 Hooks 也就简单明了了。

> *⚠️ Note: You don’t need to do any of this in order to understand Hooks. It might just help your JS fundamentals if you go through this exercise. Don’t worry, it’s not that hard!*
>
> *⚠️注意：为了理解 Hooks, 你并不需要做这些。如果，你通过这个练习或许会对你的 JS 基础有所帮助。别担心，这并不难！*

## What are Closures?

## 什么是闭包？

One of the [many selling points](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines) of using hooks is to avoid the complexity of classes and higher order components altogether. However, with hooks, some feel we may have swapped one problem for another. Instead of [worrying about bound context](https://overreacted.io/how-are-function-components-different-from-classes/), we now have to [worry about closures](https://overreacted.io/making-setinterval-declarative-with-react-hooks/). As [Mark Dalgleish memorably summarized](https://twitter.com/markdalgleish/status/1095025468367990784):

Hooks 的[众多卖点](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines)之一就是：可以避免把 class 和高阶组件融合在一起。然而，感觉 Hooks 只是把一个问题转化成了另外一个问题。现在，[我们不需担心上下文了](https://overreacted.io/how-are-function-components-different-from-classes/)，但是，[需要担心闭包](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)。就像 [Mark Dalgleish 总结的一样](https://twitter.com/markdalgleish/status/1095025468367990784):

![](https://cdn.netlify.com/e3dbea97adeafbc7d65e67ec60bf345ed989f5b6/4dbb8/img/blog/tweet-markdalgleish-hooks.jpg)

Closures are a fundamental concept in JS. In spite of this, they are notorious for being confusing to many especially newer developers. Kyle Simpson of [You Don’t Know JS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch5.md) fame defines closures as such:

闭包是 JS 中基本的概念。尽管如此，它们很容易混乱，尤其是那些开发者新手。Kyle Simpson 在[你不知道的 JS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch5.md) 中是这么定义闭包的：

*Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.*

*闭包就是一个函数，它可以访问词法作用域，即使，在词法作用域外部执行这个函数。*

They’re obviously closely tied to the concept of lexical scoping, which [MDN defines](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) as “how a parser resolves variable names when functions are nested”. Let’s look at a practical example to better illustrate this:

显然，它和词法作用域密切有关，[MDN 是这么定义的](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)："分析器如何在函数嵌套的情况下解析变量名"。为了更好的理解，我们来看一个演示：

```jsx
// Example 0
function useState(initialValue) {
  var _val = initialValue // _val 是通过 useState 创建的本地变量
  function state() {
    // state 是一个内嵌的函数，一个闭包
    return _val // state() 只是返回了，由父级函数声明的 _val, 
  }
  function setState(newVal) {
    // same
    _val = newVal // 重置 _val
  }
  return [state, setState] // 导出方法
}
var [foo, setFoo] = useState(0) // 数组解构
console.log(foo()) // 输出 0 - 我们的初始值
setFoo(1) // 内置的 setState，通过内部作用域重置 _val
console.log(foo()) // 输出 1 - 新的值, 尽管调用了相同的方法
```

Here, we’re creating a primitive clone of React’s `useState` hook. In our function, there are 2 inner functions, `state` and `setState`. `state` returns a local variable `_val` defined above and `setState` sets the local variable to the parameter passed into it (i.e. `newVal`).

在这，我们实现了 React ```useState``` 的核心功能。在我们的函数中，有两个内嵌的函数：```state``` 和 ```useState```。```state``` 返回了本地变量 ```_val``` ； ```useState``` 通过传入的参数重置本地变量（比如：```newVal```）。

Our implementation of `state` here is a getter function, [which isn’t ideal](https://twitter.com/sebmarkbage/status/1098809296396009472), but we’ll fix that in a bit. What’s important is that with `foo` and `setFoo`, we are able to access and manipulate (a.k.a. “close over”) the internal variable `_val`. They retain access to `useState` ‘s scope, and that reference is called closure. In the context of React and other frameworks, this looks like state, and that’s exactly what it is.

我们实现的 ```state``` 是一个 getter 函数，[这并不理想](https://twitter.com/sebmarkbage/status/1098809296396009472)，将来我们会一点点的修复。这里重要的是 ```foo``` 和 ```setFoo```，通过它们我们可以访问和更新（也称为：“复写”）内部变量 ```_val```。它们可以持续的访问 ```useState``` 的作用域，这个应用就是闭包。这在 React 和其他框架的上下文中，看起来就是 state，这就是它。

If you’d like deeper dives on closure, I recommend reading [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures), [YDKJS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch5.md), and [DailyJS](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8) on the topic, but if you understood the code sample above, you have everything you need.

如果，你想深入了解闭包，我推荐阅读 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures), [YDKJS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch5.md), 和 [DailyJS](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8) 相关的专题，但是，如果你已经理解了上面的代码，这对你来说已经足够。

## Usage in Function Components

## 使用函数组件

Let’s apply our newly minted `useState` clone in a familiar looking setting. We’ll make a `Counter` component!

我来用最新创建的 ```useState``` 实现一个常见的场景。我们将会创建一个 ```Counter``` 组件！

```jsx
// Example 1
function Counter() {
  const [count, setCount] = useState(0) // same useState as above
  return {
    click: () => setCount(count() + 1),
    render: () => console.log('render:', { count: count() })
  }
}
const C = Counter()
C.render() // render: { count: 0 }
C.click()
C.render() // render: { count: 1 }
```

Here, instead of rendering to the DOM, we’ve opted to just `console.log` out our state. We’re also exposing a programmatic API for our Counter so we can run it in a script instead of attaching an event handler. With this design we are able to simulate our component rendering and reacting to user actions.

在这，我们没有渲染 DOM，而是，选择用 ```console.log``` 输出 state。我们也为 Counter 暴露了一个 API，通过执行 API 来模拟事件处理。通过这种设计，我们也可以模拟出组件的渲染和响应用户的交互。

While this works, calling a getter to access state isn’t quite the API for the real `React.useState` hook. Let’s fix that.

在这种情况，通过调用 getter 来访问 state，并不是真正的 ```React.useState``` hook。我们来修复它。

## Stale Closure

## 陈旧的闭包

If we want to match the real React API, our state has to be a variable instead of a function. If we were to simply expose `_val` instead of wrapping it in a function, we’d encounter a bug:

我们想要实现一个真正的 React API，我们的 state 必须是一个变量，而不是函数。如果，我只是简单的导出一个变量 ```_val```，我们将会遇到bug：

```jsx
// Example 0, revisited - 这是一个bug!
function useState(initialValue) {
  var _val = initialValue
  // 没有 state() 函数
  function setState(newVal) {
    _val = newVal
  }
  return [_val, setState] // 直接暴露 _val
}
var [foo, setFoo] = useState(0)
console.log(foo) // logs 0 不需要调用函数
setFoo(1) // 内置的 setState，通过内部作用域重置 _val
console.log(foo) // logs 0 - oops!!
```

This is one form of the Stale Closure problem. When we destructured `foo` from the output of `useState`, it refers to the `_val` as of the initial `useState` call… and never changes again! This is not what we want; we generally need our component state to reflect the *current* state, while being just a variable instead of a function call! The two goals seem diametrically opposed.

这是由闭包导致的。我们通过 ```useState``` 解构得到的 ```foo```，它其实是初始化时 ```_val``` 的引用，并不会改变！这并不是我们想要的结果；通常，我们需要组件的 state 是*当前* state 的引用，只是用变量代替了函数调用！这两个目的看起来截然相反。

## Closure in Modules

## 模块中的闭包

We can solve our `useState` conundrum by… moving our closure inside another closure! (*Yo dawg I heard you like closures…*)

我们可以通过嵌套另外一层的闭包解决 ```useState``` 的问题！（老兄，看起来你更喜欢闭包...）

```js
// Example 2
const MyReact = (function() {
  let _val // hold our state in module scope
  return {
    render(Component) {
      const Comp = Component()
      Comp.render()
      return Comp
    },
    useState(initialValue) {
      _val = _val || initialValue // assign anew every run
      function setState(newVal) {
        _val = newVal
      }
      return [_val, setState]
    }
  }
})()
```

Here we have opted to use [the Module pattern](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript) to make our tiny React clone. Like React, it keeps track of component state (in our example, it only tracks one component, with a state in `_val`). This design allows `MyReact` to “render” your function component, which allows it to assign the internal `_val` value every time with the correct closure:

现在，我们打算用[Module 模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript) 实现 hook。就像 React，它可以保留跟踪组件的 state（在我们的演示中，它只会跟踪一个组件一个state：```_val```）。这种设计允许 ```MyReact``` 渲染函数组件，而且，每次渲染都会用到当前正确的 ```_val```：

```jsx
// Example 2 continued
function Counter() {
  const [count, setCount] = MyReact.useState(0)
  return {
    click: () => setCount(count + 1),
    render: () => console.log('render:', { count })
  }
}
let App
App = MyReact.render(Counter) // render: { count: 0 }
App.click()
App = MyReact.render(Counter) // render: { count: 1 }
```

Now this looks a lot more like React with Hooks!

现在看起来更加像 React Hooks!

You can [read more about the Module pattern and closures in YDKJS](https://github.com/getify/You-Dont-Know-JS/blob/master/scope %26 closures/ch5.md#modules).

你可以在 [YDKJS 中阅读更多有关模块模式和闭包的相关知识](https://github.com/getify/You-Dont-Know-JS/blob/master/scope %26 closures/ch5.md#modules)。

## Replicating `useEffect`

## 复刻 ```useEffect```

So far, we’ve covered `useState`, which is the first basic React Hook. The next most important hook is [`useEffect`](https://reactjs.org/docs/hooks-effect.html). Unlike `setState` , `useEffect` executes asynchronously, which means more opportunity for running into closure problems.

目前为止，我们已经实现了 ```useState```，这是 React 第一个基本的 Hook。下一个最重要的 hook 是 [`useEffect`](https://reactjs.org/docs/hooks-effect.html)。不像 `setState` , `useEffect` 异步的，也就意味着更多的机会引发闭包的问题。

We can extend the tiny model of React we have built up so far to include this:

我们可以扩展我们的 React，最终结果如下：

```jsx
// Example 3
const MyReact = (function() {
  let _val, _deps // hold our state and dependencies in scope
  return {
    render(Component) {
      const Comp = Component()
      Comp.render()
      return Comp
    },
    useEffect(callback, depArray) {
      const hasNoDeps = !depArray
      const hasChangedDeps = _deps ? !depArray.every((el, i) => el === _deps[i]) : true
      if (hasNoDeps || hasChangedDeps) {
        callback()
        _deps = depArray
      }
    },
    useState(initialValue) {
      _val = _val || initialValue
      function setState(newVal) {
        _val = newVal
      }
      return [_val, setState]
    }
  }
})()

// usage
function Counter() {
  const [count, setCount] = MyReact.useState(0)
  MyReact.useEffect(() => {
    console.log('effect', count)
  }, [count])
  return {
    click: () => setCount(count + 1),
    noop: () => setCount(count),
    render: () => console.log('render', { count })
  }
}
let App
App = MyReact.render(Counter)
// effect 0
// render {count: 0}
App.click()
App = MyReact.render(Counter)
// effect 1
// render {count: 1}
App.noop()
App = MyReact.render(Counter)
// // no effect run
// render {count: 1}
App.click()
App = MyReact.render(Counter)
// effect 2
// render {count: 2}
```

To track dependencies (since `useEffect` reruns when dependencies change), we introduce another variable to track `_deps`.

为了跟踪依赖（因为，```useEffect``` 会在依赖改变时重新执行），我们引入另外一个变量：```_deps```。

## Not Magic, just Arrays

## 没什么神奇，就是数组

We have a pretty good clone of the `useState` and `useEffect` functionality, but both are badly implemented [singletons](https://en.wikipedia.org/wiki/Singleton_pattern) (only one of each can exist or bugs happen). To do anything interesting (and to make the final stale closure example possible), we need to generalize them to take arbitrary numbers of state and effects. Fortunately, as [Rudi Yardley has written](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e), React Hooks are not magic, just arrays. So we’ll have a `hooks` array. We’ll also take the opportunity to collapse both `_val` and `_deps` into our `hooks` array since they never overlap:

我们基本已经实现了 ```useState``` 和 ```useEffect```，但是，两者只实现了[单列模式](https://en.wikipedia.org/wiki/Singleton_pattern)（只会存在一个并且会有bug）。为了更加有趣（并且最终实现完整版的演示），我们需要它可以容纳更多的 state 和 effect。很幸运，就如 [Rudi Yardley 所说](https://juejin.im/post/5e8c9868e51d4546d72d3076)，React Hooks 并不神奇，它就是数组。因此，我们需要有一个 ```hooks``` 数组。我们也会把  `_val` 和 `_deps` 收集到 ```hooks``` 数组中，因为，它们绝不会重复：

```jsx
// Example 4
const MyReact = (function() {
  let hooks = [],
    currentHook = 0 // array of hooks, and an iterator!
  return {
    render(Component) {
      const Comp = Component() // run effects
      Comp.render()
      currentHook = 0 // reset for next render
      return Comp
    },
    useEffect(callback, depArray) {
      const hasNoDeps = !depArray
      const deps = hooks[currentHook] // type: array | undefined
      const hasChangedDeps = deps ? !depArray.every((el, i) => el === deps[i]) : true
      if (hasNoDeps || hasChangedDeps) {
        callback()
        hooks[currentHook] = depArray
      }
      currentHook++ // done with this hook
    },
    useState(initialValue) {
      hooks[currentHook] = hooks[currentHook] || initialValue // type: any
      const setStateHookIndex = currentHook // for setState's closure!
      const setState = newState => (hooks[setStateHookIndex] = newState)
      return [hooks[currentHook++], setState]
    }
  }
})()
```

Note our usage of `setStateHookIndex` here, which doesn’t seem to do anything, but is used to prevent `setState` from closing over the `currentHook` variable! If you take that out, `setState` again stops working because the closed-over `currentHook` is stale. (Try it!)

注意，我们用到的 `setStateHookIndex`，它看起并没有做什么，但是，它可以防止 ```setState``` 丢失 `currentHook` 变量！如果，你把它注释掉，```setState``` 就不能正常工作，这是因为 `currentHook` 已经过时了。（试下！）

```jsx
// Example 4 continued - in usage
function Counter() {
  const [count, setCount] = MyReact.useState(0)
  const [text, setText] = MyReact.useState('foo') // 2nd state hook!
  MyReact.useEffect(() => {
    console.log('effect', count, text)
  }, [count, text])
  return {
    click: () => setCount(count + 1),
    type: txt => setText(txt),
    noop: () => setCount(count),
    render: () => console.log('render', { count, text })
  }
}
let App
App = MyReact.render(Counter)
// effect 0 foo
// render {count: 0, text: 'foo'}
App.click()
App = MyReact.render(Counter)
// effect 1 foo
// render {count: 1, text: 'foo'}
App.type('bar')
App = MyReact.render(Counter)
// effect 1 bar
// render {count: 1, text: 'bar'}
App.noop()
App = MyReact.render(Counter)
// // no effect run
// render {count: 1, text: 'bar'}
App.click()
App = MyReact.render(Counter)
// effect 2 bar
// render {count: 2, text: 'bar'}
```

So the basic intuition is having an array of `hooks` and an index that just increments as each hook is called and reset as the component is rendered.

因此，这会有一个简单的数组 ```hooks``` 和一个索引，索引会在 hook 每次被调用时递增或者组件重新渲染时重置。

You also get [custom hooks](https://reactjs.org/docs/hooks-custom.html) for free:

你也可以从这得到免费的[自定义 hooks](https://reactjs.org/docs/hooks-custom.html):

```jsx
// Example 4, revisited
function Component() {
  const [text, setText] = useSplitURL('www.netlify.com')
  return {
    type: txt => setText(txt),
    render: () => console.log({ text })
  }
}
function useSplitURL(str) {
  const [text, setText] = MyReact.useState(str)
  const masked = text.split('.')
  return [masked, setText]
}
let App
App = MyReact.render(Component)
// { text: [ 'www', 'netlify', 'com' ] }
App.type('www.reactjs.org')
App = MyReact.render(Component)
// { text: [ 'www', 'reactjs', 'org' ] }}
```

**This truly underlies how “not magic” hooks are** – Custom Hooks simply fall out of the primitives provided by the framework – whether it is React, or the tiny clone we’ve been building.

**这才是 hooks 真正神奇的地方**  — 不管是 React，还是我们实现的版本，自定义 hooks 并不包含在内。

## Deriving the Rules of Hooks

## 引出 Hooks 规则

Note that from here you can trivially understand the first of the [Rules of Hooks](https://reactjs.org/docs/hooks-rules.html): [Only Call Hooks at the Top Level](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level). We have explicitly modeled React’s reliance on call order with our `currentHook` variable. You can read through [the entirety of the rule’s explanation](https://reactjs.org/docs/hooks-rules.html#explanation) with our implementation in mind and fully understand everything going on.

注意，这时，你就可以很轻松的理解 React Hooks 第一条[规则](https://reactjs.org/docs/hooks-rules.html)：[Hooks 只允许在最顶层调用](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level)。我们已经明确了 React Hooks 必须依赖 ```currentHook``` 变量按照顺序执行。你可以通过阅读[完整的规则解释](https://reactjs.org/docs/hooks-rules.html#explanation)并结合我们的实现，就可以完全理解了。

Notice also that the second rule, “[Only Call Hooks from React Functions](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-from-react-functions)”, isn’t a necessary result of our implementation either, but it is certainly good practice to explicitly demarcate what parts of your code rely on stateful logic. (As a nice side effect, it also makes it easier to write tooling to make sure you follow the first Rule. You can’t accidentally shoot yourself in the foot by wrapping stateful functions named like regular JavaScript functions inside of loops and conditions. Following Rule 2 helps you follow Rule 1.)

第二条规则：[只在函数组件中调用 Hooks](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-from-react-functions)，对于我们的实现不是必要的，但是，对于如何明确的划分你的代码哪些依赖 state 逻辑是很好的实践。（另外一个好处，这样更容易编写工具确保遵循第一规则。 You can’t accidentally shoot yourself in the foot by wrapping stateful functions named like regular JavaScript functions inside of loops and conditions。遵守第二条规则，有助你遵循第一条规则）

## Conclusion

## 总结

At this point we have probably stretched the exercise as far as it can go. You can try [implementing useRef as a one-liner](https://www.reddit.com/r/reactjs/comments/aufijk/useref_is_basically_usestatecurrent_initialvalue_0/), or [making the render function actually take JSX and mount to the DOM](https://www.npmjs.com/package/vdom), or a million other important details we have omitted in this tiny 28-line React Hooks clone. But hopefully you have gained some experience using closures in context, and gained a useful mental model demystifying how React Hooks work.

到这，我们已经尽可能的做了一个简版的实现。你也可以试试[一行代码实现 useRef](https://www.reddit.com/r/reactjs/comments/aufijk/useref_is_basically_usestatecurrent_initialvalue_0/)，或者[让 render 返回真实的 JSX，然后挂载 DOM](https://www.npmjs.com/package/vdom)，我们这个28行代码简版的 React Hooks 还有很多细节没有处理。但是，希望你已经获得了使用闭包的经验，也可以更好的理解 React Hooks 是如何工作的。

*I’d like to thank [Dan Abramov](https://twitter.com/dan_abramov) and [Divya Sasidharan](https://twitter.com/shortdiv) for reviewing early drafts of this eassay and improving it with their valuable feedback. All remaining errors are mine..*

*我非常感谢 [Dan Abramov](https://twitter.com/dan_abramov) 和 [Divya Sasidharan](https://twitter.com/shortdiv) 对这片文章的审核，我也根据他们的反馈做了改动。所有还有的错误都是我的。*

