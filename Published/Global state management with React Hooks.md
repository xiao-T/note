# Global state management with React Hooks

# 使用 React Hooks 管理全局 State

> 原文地址: <https://blog.usejournal.com/global-state-management-with-react-hooks-5e453468c5bf>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*Ra-gkqfPqbWVhgP3tR-0Cg.png)

> *TL;DR: I created a library called react-hookstore, a custom hook to share state between components. Basically a global useState/useReducer hook. Check its* [Github repo](https://github.com/jhonnymichel/react-hookstore)
>
> *TL;DR: 基于全局的 useState/useReducer hook，我创建了一个库叫做 react-hookstore, 这个自定义的 hook 可以在组件之间共享 state。 点击查看* [*Github repo*](https://github.com/jhonnymichel/react-hookstore)

Hooks, the new React feature present in v16.7-alpha took the frontend development world by storm. Everyone is talking about it, even members of the community that don’t cover React that much:

Hooks，是 React v16.7-alpha 中的新功能，它在前端开发者中掀起了一场风暴。每个人都会谈论它，甚至，那些对 React 了解不多的社区会员也会讨论它：

[tweet thread]([https://twitter.com/DasSurma/status/1056141331947573249?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed&ref_url=https%3A%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Ftype%3Dtext%252Fhtml%26key%3Da19fcc184b9711e1b4764040d3dc5c07%26schema%3Dtwitter%26url%3Dhttps%253A%2F%2Ftwitter.com%2Fdassurma%2Fstatus%2F1056141331947573249%26image%3Dhttps%253A%2F%2Fi.embed.ly%2F1%2Fimage%253Furl%253Dhttps%25253A%25252F%25252Fpbs.twimg.com%25252Fprofile_images%25252F1052690668659888133%25252FUQFb42S7_400x400.jpg%2526key%253Da19fcc184b9711e1b4764040d3dc5c07](https://twitter.com/DasSurma/status/1056141331947573249?ref_src=twsrc^tfw|twcamp^tweetembed&ref_url=https%3A%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Ftype%3Dtext%2Fhtml%26key%3Da19fcc184b9711e1b4764040d3dc5c07%26schema%3Dtwitter%26url%3Dhttps%3A%2F%2Ftwitter.com%2Fdassurma%2Fstatus%2F1056141331947573249%26image%3Dhttps%3A%2F%2Fi.embed.ly%2F1%2Fimage%3Furl%3Dhttps%253A%252F%252Fpbs.twimg.com%252Fprofile_images%252F1052690668659888133%252FUQFb42S7_400x400.jpg%26key%3Da19fcc184b9711e1b4764040d3dc5c07))

Hooks are a new feature proposal that interact with function-based components, giving them access to lifecycle hooks, local state and others features that were only available on class-based components up to this point. Making the so called 'stateless components' not that stateless anymore.

Hooks 是基于函数组件的新功能，它可以访问组件的生命周期、本地 state 和那些只有类组件中才有的功能。因此，'无状态组件'不在是真的无状态。

This article has no intention to introduce hooks detailedly, I'll cover how to extend them. To get more familiar with hooks, start with [this article](https://reactjs.org/docs/hooks-intro.html) from the React docs. It is also recommended to read about the [*useState*](https://reactjs.org/docs/hooks-state.html) and [*useEffect*](https://reactjs.org/docs/hooks-effect.html) *hooks.*

这篇文章并不打算介绍 hooks 的详细内容，我将会介绍如何扩展 hooks。为了更加熟悉 hooks，可以看看 React 文档中的[这篇文章](https://reactjs.org/docs/hooks-intro.html)。同时，也推荐推荐阅读有关 [*useState*](https://reactjs.org/docs/hooks-state.html) 和 [*useEffect*](https://reactjs.org/docs/hooks-effect.html) 的文档。

> ~~*⚠️ Warning: hooks are not part of a stable React release yet, so use hooks only for experiment purposes for now.*~~
>
> **译者注：Hooks 在 React v16.8 中已经发布**

## Stateful function-based components

## 有状态的函数组件

One of the built-in hooks React provide is [*useState*](https://reactjs.org/docs/hooks-state.html)*.* We can use it to provide local state to function-based components. Basically, React brought the *state* object and the *setState* function to components without the need to create a class extending *React.Component.* the example below sums it up:

React 内置的其中一个 hook 是 [*useState*](https://reactjs.org/docs/hooks-state.html)。我们可以用它在函数组件中管理本里 State。基本上，React 通过这个 hook 给函数组件提供了 *State* 对象和 *setState* 方法，而不需要通过继承 *React.Component* 来创建类组件。示例如下：

```jsx
import React, { useState } from 'react';

function StatefulHello() {
  const [timesClicked, updateTimesClicked] = useState(0);

  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>The button inside this component was clicked {timesClicked} times</h2>
      <button 
         type="button" 
         onClick={() => updateTimesClicked(timesClicked + 1)}
      >
        Update
      </button>
    </div>
  );
}
```

Notice that the *useState* function returns a pair with the state you provided (0 in my case) and a function to change the state. you can name them whatever you want, since they come inside an array and not an object (I personally liked it since destructuring is a thing in Javascript).

注意，函数 *useState* 返回了你提供的 State (在我的案列中是0)和可以改变 State 的方法。由于，它们来自内部的一个数组，而不是一个对象，因此，你可以随意给它们命名（这是我个人喜欢的 Javascript 的语法：解构 ）。

That is how we can easily embed local state in a function component. Great! Hooks are great, and by knowing how they work, we can create our own hooks that extend the functionality of the default ones.

这就是我们可以很容易的在函数组件中嵌入本地 State 的方式。很好！Hooks 很好，在知道了它们的工作模式后，我们可以通过扩展默认的 Hooks 来创建我们自己的 Hooks。

## Sharing state between components

## 在组件之间共享 State

Every React developer faced this challenge before: The need to share state between non-connected components. State management libraries like Redux and Mobx came to the rescue, implementing the concept of creating a Store object and connecting/injecting it into any component that needs to consume the application state.

在此之前，每个 React 的开发者需要面对的挑战：需要在非关联的组件之间共享 State。利用状态管理库：Redux 和 Mobx 可以解决这些问题，这些状态管理库创建了一个 Store 对象并把它注入到任何一个需要消费应用 State 的组件中。

### The useState hook is only local

### useState 只管理本地 State

Check the code below. There is no way *AnotherComponent* can be aware of how many times the user clicked the button.

看下以下代码。*AnotherComponent* 无法知道用户到底点击了几次 button。

```jsx
function StatefulHello() {
  const [timesClicked, updateTimesClicked] = useState(0);

  return (
    <div>
      <h1>Hello, component!</h1>
      <h2>The button inside this component was clicked {timesClicked} times</h2>
      <button type="button" onClick={() => updateTimesClicked(timesClicked + 1)}>
        Update
      </button>
    </div>
  );
}

function AnotherComponent() {
  /* this would not be the same state from StatefulHello
  const [ timesClicked, updateTimesClicked] = useState() */
  return (
    <div>
      <h1>
        Hello, this is a second component, with no relation to the one on the
        top
      </h1>
      <h2>
        And there is no way for it to keep track of how many times the user clicked on the button.
      </h2>
    </div>
  );
}
```

To have fun and to better learn how hooks work under the hood, I decided to create a custom hook that provides a single, global state to all components. And I managed to make it happen in under 20 lines!

为了更加有趣和更好的了解 Hooks 底层如何工作的，我决定创建一个自定义的 Hook，用它可以提供一个单一、全局的 State 供所有的组件消费。我只用20行以内的代码实现它！

### Important aspects of a hook lifecycle

###  重要的知识：Hook 的生命周期

I wanted to confirm how hooks react to a component lifecycle, and after testing a bit I concluded that:

- As long as the component is not unmounted, re-renders do not create new hook-related functions and objects, such as the *setState* callback from *useState*. React smartly use the same reference for every re-render. The same applies for *useEffect*.
- When the component is unmounted, all hook-related functions and objects are discarded.
- If the component is mounted again, the hook will start from scratch, functions and objects will not be the same from before.

我想确认下 Hooks 的生命周期，做过一些测试后，总结如下：

- 如果，组件不 unmounted，重新渲染的时候，不会创建新的 Hook 相关方法和对象，比如：来自 *useState* 的回调方法 *setState*。React 足够聪明会在每次重现渲染时引用同一个对象。这同样适用于 *useEffect*。
- 当组件 unmounted 时，所有 Hook 相关的方法和对象都会被销毁。
- 如果，组件再次 mounted，Hook 会从头开始，方法和对象也不会同之前的一样。

## Creating our custom hook

## 创建自定义的 Hook

After learning the hook lifecycle, I realised I could create a middleware to the *useState* hook to store the setState callbacks and call all of them whenever one of the components tries to update the state.

了解过 Hook 的生命周期后，我意识到，我可以为 *useState* 创建一个中间件存储所有的 setState 回调方法，然后，在有组件需要更新 State 时调用它们。

This is probably not recommended, but for experiment purposes, it is fine. Also, performance is great because we are only using React's internals. It is even cleaner than using the good old *forceUpdate* on class components.

这仅作为实验，很好，但是，并不推荐这么做。因为只用在 React 内部，性能也没问题。这甚至比类组件中的 *forceUpdate* 更清晰。

This is the MVP for the project:

以下项目的核心代码：

```jsx
import { useState } from 'react';

export const store = {
  state: {},
  setState(value) {
    this.state = value;
    this.setters.forEach(setter => setter(this.state));
  },
  setters: []
};
  
// Bind the setState function to the store object so 
// we don't lose context when calling it elsewhere
store.setState = store.setState.bind(store);

// this is the custom hook we'll call on components.
export function useStore() {
  const [ state, set ] = useState(store.state);
  if (!store.setters.includes(set)) {
    store.setters.push(set);
  }

  return [ state, store.setState ];
}
```

In under 20 lines (ignoring blanks and comments) we were able to create a global *useState* hook. Let's dissect the lines that made it possible:

 在不到20行的代码中（忽略空白行和注释），我们创建了一个全局的 *useState* Hook。让我们来一一解析为什么可行：

~~In line 7~~, inside the store object declaration:

在第9行，store 内部的声明：

```json
setters: []
```

This array will store all functions returned by the *useState* hook.

这个数组将会存储所有通过 *useState* 返回的方法。

In line 18, inside our custom hook *useStore,* we use the *useState* hook. we pass the store.state object as the initial value:

在第18行，自定义 Hook *useStore* 内部，我们把 store.state 做为初始值传递给 *useState*：

```
const [ state, set ] = useState(store.state)
```

In line 20, we push the *set* function to the store's setters object, in case we haven't done it yet for the current component:

在第20行，我们把 *set* 添加到 store.setters 数组中，对于当前组件这并没有完：

```
store.setters.push(set)
```

In line 23, we return a pair containing the store state and the store setState method:

在第23行，我们返回了一对数据：store 中的 state 和 setState 方法

```
return [ state, store.setState ]
```

notice that we do not pass the *useState's* set method directly. We only stored it to call it when the store state is meant to be updated, in line 7 (that'll be explained soon).

注意，我们并没有直接传递 *useState* 返回的 set 方法。我们只是把它们存储起来，然后，在需要更新的时候调用它，在第7行（稍后将会解释）。

In line 6, inside the store's setState method, we update the state object with the value provided:

在第6行，在 store 的 setState 方法中，我们把传递进来的 value 赋值给 store.state：

```
this.state = value
```

In line 7, we broadcast the state change to all components that used the *useStore* hook:

在第7行，我们为所有用到 *useStore* 的组件广播了 State 的变化：

```
this.setters.forEach(setter => setter(this.state))
```

This is the most important piece, where the magic happens: for each component that uses the custom hook, we execute the setter callback that'll trigger an update to the component.

这是最重要的一部分，神奇之处在于：对于那些用自定义 Hook 的组件，我们通过执行 setter 回调触发了所有组件的更新。

## Using the custom hook

## 使用自定义 Hook

The *useStore* hook is as simple to use as the *useState* one:

使用 *useStore* 就像 *useState* 一样简单：

```jsx
import { store, useStore } from './hookstore';

// setting the store initial state
store.state = 0;

function StatefulHello() {
  // using the useStore hook
  const [timesClicked, updateTimesClicked] = useStore();

  return (
    <div>
      <h1>Hello, component!</h1>
      <h2>The button inside this component was clicked {timesClicked} times</h2>
      <button type="button" onClick={() => updateTimesClicked(timesClicked + 1)}>
        Update
      </button>
    </div>
  );
}

function AnotherComponent() {
  // using the useStore hook here as well. The same state will be shared here.
  const [ timesClicked ] = useStore();
  return (
    <div>
      <h1>
        Hello, this is a second component, with no relation to the one on the
        top
      </h1>
      <h2>
        Using the useStore hook, I know the user clicked on the button { timesClicked } times!
      </h2>
    </div>
  );
}
```

## Disposing setters when components are unmounted

## 当组件 unmounted 时销毁 setters

This basic implementation has a serious issue: What happens if we unmount *AnotherComponent*? we would still have two setter functions registered under `store.setters` and when the store state gets updated, React would complain about we trying to set the state of an unmounted component.

这个简单的版本有个严重的问题：当 *AnotherComponent* unmounted 会发生什么呢？我们仍然会有两个 setter 注册在 ```store.setters``` 中，然后，当 store.state 需要更新时，因为，我们在为一个 unmounted 的组件设置 State，React 会发出警告。

We need a way to know when components get unmounted to get rid of their setter functions. We can do that by adding another hook to the mix: *useEffect.*

当组件 unmounted 时，我们需要销毁相关的 setter。我们可以通过结合另外一个 Hook 来处理：*useEffect*。

This hook expects a function that'll be called whenever the component is mounted or updated. This function can also return another function that'll be called when the component is unmounted. Learn more about the useEffect api [here](https://reactjs.org/docs/hooks-effect.html).

可以给这个 Hook 传递一个方法，然后在组件 mounted 和 updated 时调用这个方法。Hook 也可以返回另外一个方法，然后，在组件 unmonted 调用此方法。更多有关 useEffect 的知识在[这](https://reactjs.org/docs/hooks-effect.html)。

In our case, we only need to trigger it when unmount happens. We don't care about mount and update.

就我们场景来说，我们只需要在组件 unmount 的时候触发回调即可。我们不关心 mount 和 update 的情况。

We can tell *useEffect* to skip updates. This is possible by providing an empty array as the second argument to the hook. An empty array means we do not depend on any props or state, so React will only execute the callback on mount/unmount.

通过设置第二个参数为空数组([])，我们可以让 *useEffect* 跳过更新。空数组代表我们不依赖任何 props 和 state，React 只有在 mount/unmount 的时候执行回调。

If the function provided to the hook only returns a second one (the unmount callback, as specified in the *useEffect* api), then the mount trigger won't do anything.

如果，提供给 hook 的回调方法只是返回了一个方法（通过 *useEffect* 提供 unmount 回调），那么，在组件 mount 的时候什么都不会做。

Yes, this is confusing. Worry not, let's see it in action:

是的，这有点混乱。不用担心，我们来看一看具体的实现：

```jsx
export function useStore() {
  const [ state, set ] = useState(store.state);
  if (!store.setters.includes(set)) {
    store.setters.push(set);
  }
  
  useEffect(() => () => {
    store.setters = store.setters.filter(setter => setter !== set)
  }, [])

  return [ state, store.setState ];
}
```

Lines 7, 8 and 9 implements the hook, passing a function A that returns another function B. B will be executed every time the component unmounts. When it does, it'll remove the setter function for the component from the setters list, so we won't try to update the state of a component that is not rendered anymore.

在第7、8、9行，传递给 hook 的回调中只是返回了一个方法B。每次组件 unmount 的时候 B 都会被执行。它将会从 setters 中移除当前组件相关的 setter 方法，因此，我们将不会在更新组件的 State，组件也不会在被重新渲染。

## Caveats
## 警告

This implementation was the simplest possible to keep the contents of the article about using and extending React hooks. But exposing the entire store object is not a good idea: we're exposing the library internals together with it.

对于如何使用和扩展 React Hooks，这是最简单的实现。但是，导出整个 Store 对象，并不是一个很好的注意：我们将会导出一个库。

The final implementation is more suitable for real-world usage: The possibility to create multiple, namespaced stores with a store factory, and the possibility to use reducers to handle state updates. Also, all of the library internals are not exposed. You can check the final version on its [github repo](https://github.com/jhonnymichel/react-hookstore/blob/master/src/index.js).

最终的实现覆盖了更多的真实情况：通过 Store 工厂函数，创建多个 Stores，并且使用 reducer 去处理 State 的更新。并且，这都是在库内部实现，并不会导出。你可以在 [github](https://github.com/jhonnymichel/react-hookstore/blob/master/src/index.js) 看到最终的实现版本。

