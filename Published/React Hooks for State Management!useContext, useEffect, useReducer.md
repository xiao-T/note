## React Hooks for State Management!(useContext, useEffect, useReducer)
## 用 React Hooks 管理 State!（useContext,useEffect,useReducer）

> 原文地址: <https://medium.com/@seantheurgel/react-hooks-as-state-management-usecontext-useeffect-usereducer-a75472a862fe/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/3000/1*-Ijet6kVJqGgul6adezDLQ.png)

# React 16.8

The React team officially release v16.8 a few hours ago and indeed it is a moment to celebrate 🎉. State Management has always been a problem from the day React was born, and we have constantly searching for solutions left and right. In this tutorial we’re going to see how to handle state management with useContext, useEffect and useReducer. What the heck are those?

React 团队几小时前发布了v16.8，这是一个值得庆祝的时刻。自 React 诞生以来 State 的管理一直是个问题，我们一直在寻求两全的方案。本教程中，我们将探索如何使用 useContext, useEffect 和 useReducer 来管理 State。它们到底是些什么？

# Prerequisites ✅

[https://youtu.be/dpw9EHDh2bM](https://youtu.be/dpw9EHDh2bM)

1. If you haven’t watched the React Conf video on Hooks, I suggest you do — Sophie Alpert, Dan Abramov and Ryan Florence give a very great introduction on hooks (every second is worth it!), I’ll just be covering the things that they didn’t talk about in this tutorial.
2. Knowledge on common state management tools (Redux, Flux, etc.)
3. React’s Context API (a little) (https://reactjs.org/docs/context.html)

If you know those things already, let’s get started! 🔥

1. 如果，你还没有看过 React Conf 视频中有关 Hooks 的介绍，我建议你看看 — Sophie Alpert, Dan Abramov 和 Ryan Florence 对 Hooks 做了非常精彩的介绍（每一秒都值得！），本教程中，我只会探索那些没有讲到的事情。
2. 众所周知的 State 管理工具（Redux, Flux 等等）
3. React Context API(https://reactjs.org/docs/context.html)

如果，你对这些已有了解，我们就开始吧！

# The app we’re going to create

![](https://miro.medium.com/max/1712/1*kRmdszcA0I5ZAQYYdPznbw.gif)

## Flow of the app

1. Fetch count from server
2. Increment / Decrement from <Count />
3. Fetch again from <SeparateComponent />

## APP 的流程

1. 从服务器获取 count 数据
2. 通过组件 <Count /> 增加/减少 count
3. 在组件 <SeparateComponent /> 中再次获取 count 数据

## 1) Let’s start with the UI. 

## 1）我们先从 UI 开始



```jsx
// App.js
import React from 'react'
function Counter() {
 return (
    <div>
      <h5>Count: 0</h5>
      {/* TODO: Increment */}
      <button onClick={() => {}}>+</button>
      {/* TODO: Decrement */}
      <button onClick={() => {}}>-</button>
    </div>
  );
}
function SeparateComponent() {
 return (
    <div>
      <h1>Shared Count: 0</h1>
     {/* TODO: FETCH*/}
     <button onClick={() => {}}>
        Fetch Again
      </button>
    </div>
  );
}
function App() {
  return (
    <div className="App">
        <Counter />
        <SeparateComponent />
    </div>
  );
}
```

Here we created two simple components that will later share count, let’s start by creating a component that can share state, by using React’s context API.

我们创建了两个简单的组件，稍后它们之间可共享 count 数据，我们先通过 React Context API 创建一个可共享 State。

## 2) Creating a CounterContext

## 2）创建 CounterContext

```jsx
// Context.js
import React from 'react'
const initialState = {count: 0}
const CounterContext = React.createContext(initialState);
function CounterProvider(props) {
 return (
    <CounterContext.Provider value={initialState}>
      {props.children}
    </CounterContext.Provider>
  );
}
export { CounterContext, CounterProvider };
```

We create a CounterContext that takes `{count: 0}` as the initialState and we export the Providers and Context.

我们用 `{count: 0}` 作为初始 State 创建 CounterContext，并且导出 Providers 和 Context。

## 3) Add the CounterProvider

## 3）添加 CounterProvider

```jsx
// App.js
import React from 'react'
import { CounterProvider } from './Context'
// ...
<div className="App">
  <CounterProvider>
    <Counter />
    <SeparateComponent />
  </CounterProvider>
</div>
```

We simply add the `CounterProvider` so that the consuming components can subscribe to when the context gets updated.

我们只需简单的添加 `CounterProvider` ，这样，消费组件就会在上下文更新时得到数据的订阅更新。

## 4) Add the Consumer

## 4）添加 Consumer

Now here’s where things finally get interesting. Traditionally we’d consume the `CounterProvider` like so.

现在，事情变得有趣起来。一般，我们会这样使用 `CounterProvider`。

```jsx
// App.js
...
function SeparateComponent() {
  return (
    <CounterContext.Consumer>
      {({ count }) => (
        <div>
          <h1>Shared Count: {count}</h1>
          <button onClick={() => {}}>Fetch Again</button>
        </div>
      )}
    </CounterContext.Consumer>
  );
}
```

Instead we’re going to use the brand new `useContext` , here’s how we use it.

相反，我们将使用最新的 API `useContext`，我们这样使用。

```jsx
// App.js
import React, { useContext } from 'react'
...
function SeparateComponent() {
  const { count } = useContext(CounterContext);
return (
    <div>
      <h1>Shared Count: {count}</h1>
      {/* TODO: Fetch */}
      <button onClick={() => {}}>Fetch Again</button>
    </div>
  );
}
```

Wait… what just happened?

等等...刚才发生了什么？

Stated in the documentation it says “Accepts a context object (the value returned from `React.createContext`) and returns the current context value, as given by the nearest context provider for the given context.”

文档中说：“接受一个 context 对象(通过 `React.createContext` 返回的值)，并返回当前 context 的值，这个值来自最近的 context provider。”

Basically it just returns the value of the context provider! As you can see the code is looking more readable and if you’re consuming multiple contexts, we won’t get nested render functions!

基本上，它只是返回 context provider 的值！就像你看到的一样，这样更具可读性，如果，你需要多个 context，我们也不需要嵌套 render 方法！

Now you go ahead and try it on `<Counter />` !

现在，我们继续，在 `<Counter />` 上也尝试一下！

## 5) Adding State Management

## 5）添加 State 管理

Now that we shared a state between two separate components, let’s see how we can add state management implement `increment` & `decrement` .

现在，我们在两个独立的组件中共享一个 State，看看如何通过 State 管理实现 `increment` & `decrement` 。

If you’ve worked with Redux before, this is probably going to look familiar.

如果，你之前使用过 Redux，或许，这看起来有点熟悉。

```jsx
// Context.js
// new
import React, { useReducer } from "react";
// new
let reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { ...state, count: state.count + 1 };
    case "decrement":
      return { ...state, count: state.count - 1 };
    default:
      return;
  }
};
const initialState = { count: 100 }
const CounterContext = React.createContext(initialState);
function CounterProvider(props) {
  // new
  const [state, dispatch] = useReducer(reducer, initialState);
return (
   // new
   <CounterContext.Provider value={{ state, dispatch }}>
      {props.children}
    </CounterContext.Provider>
  );
}
export { CounterContext, CounterProvider };
```

So we did a couple of things here

1. import `useReducer`
2. Create a reducer function that takes a state and action (if you’ve used Redux before this should look familiar)
3. Use array destructuring to get the `state` and `dispatch` object from `useReducer` , the arguments are self explanatory.
4. Change the value in `CounterContext.Provider` to `{state, dispatch}` so we can pass the shared state and dispatch to all the consumers!

在这，我们做了以下几件事情。

1. 引入 `useReducer`
2. 创建一个 reducer 方法，有 state 和 action 两个参数（如果，你之前使用过 Redux，看起来会熟悉）
3. 使用数组解构的方式从 `useReducer` 中获取 `state` 和 `dispatch`。
4. 把 `CounterContext.Provider` 中的值改为  `{state, dispatch}`，这样，我们就可以在所有的 consumer 中共享 state 和 dispatch 了！

To those who have used state management and context before, I hope at this point you’re already getting the gist of what’s happening. Try stopping here and implementing it yourself if you can!

对于那些之前使用过 state 管理和 context 的开发者，我希望你们已经知道了这其中的要点。如果可以的话，尝试停下来并实现它。

## 6) Get the shared state and dispatch objects in the consumer components

## 6）在 Consumer 组件中获取 State 和 Dispatch 对象

```jsx
// app.js
function Counter() {
 
 // new
 const { state, dispatch } = useContext(CounterContext);
 return (
    <div>
      <h5>Count: {state.count}</h5>
       {/* new */}
      <button onClick={() => dispatch({ type: "increment" })}>
        +
      </button>
    {/* new */}
    <button onClick={() => dispatch({ type: "decrement" })}>
        -
     </button>
    </div>
  );
}
function SeparateComponent() {
  // new
  const { state } = useContext(CounterContext);
return (
    <div>
      {/* new */}
     <h1>Shared Count: {state.count}</h1>
      <button onClick={() => {}}>Fetch Again</button>
    </div>
  );
}
```

Since we passed the `state` and `dispatch` objects as the value of the `CounterContext.Provider` we can subscribe to the shared state here, and dispatch the functions in the reducer.

由于，我们把  `state` 和 `dispatch` 传递给 `CounterContext.Provider`，我们就可以订阅共享 state 和 reducer 中的 dispach 方法。

Wait… that’s it? Yes that’s it! If you wanted to use this in another component all you would have to do is add `const { state, dispatch } = useContext(CounterContext)` .

等等...就这样？是的，就这样！如果，你希望在另外的组件使用它们，你只需添加 `const { state, dispatch } = useContext(CounterContext)`  即可。

In part 2 (coming soon!), we’re going to see how to implement asynchronous tasks / side effects, such as data fetching on mounting and how to fetch again when listening only to a specific state.

在第2部分（即将到来！），我们将会探讨如何实现异步任务/side effects，比如：在挂载时获取数据和在特殊状态时如何重新再次获取数据。

## Closing Remarks & References

## 备注 & 参考

In my personal experience, this is a game changer. State Management is finally readable and straight to the point, no more looking at the export of the file and checking `mapStateToProps` and `mapDispatchToProps` and then looking through spaghetti code. Now it’s as simple as subscribing to a context via `useContext` and using the `state` and `dispatch` you want.

就我个人经验来说，游戏规则将会改变。State 管理最终会变得更具可读性和直截了当，不需在查看导出的文件和查看 `mapStateToProps` 和 `mapDispatchToProps`。现在，变得很简单，只需通过 `useContext` 订阅 context，然后，使用 `state` 和 `dispatch` 即可。

Please let me know if anything is unclear, I know I’m not the best at writing and explaining my thoughts. I was just so excited I had to share the good news to everyone 😂

如果，有任何不清楚的请让我知道，我知道我并不能很好的写作并解释我的想法。我就是太兴奋了，不得不向所有的人分享这个好消息😂

What do you guys think? Would love to hear what you think about this!

你们是怎么想的？很想听听你们的想法！

If you enjoyed this tutorial please leave a clap(leave 50!) 👏👏👏

如果，你喜欢这些教程，欢迎给我们点赞👏👏👏

Complete Code: https://codesandbox.io/s/kww9rw9rn7

https://reactjs.org/docs/hooks-reference.html#usecontext