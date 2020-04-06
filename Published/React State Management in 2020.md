## React State Management in 2020
## 2020 年 React 状态管理

> 原文地址: <https://medium.com/better-programming/react-state-management-in-2020-719d10c816bf/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

 **Do we still need state management frameworks like Redux and MobX?**

**我们是否还需要 Redux 和 Mobx 这类的状态管理框架？**

![](https://miro.medium.com/max/1400/1*JNpk3yLpt9f4x_aEO5pa-w.jpeg)

## Introduction

## 引言

The introduction of React hooks has certainly changed the perspective on state management.

React hooks 的引入无疑改变的了 state 管理的现状。

Before we had this feature, it was difficult to share state logic between components. Now it’s as easy as making an abstract hook for it (example: `useUserLogin`).

在此之前，很难在组件之间共享 state 相关的逻辑。现在，我们可以很简单的通过抽象一个 hook 来处理（例如：```useUserLogin```）。

This raises the question, why we still need state management frameworks? In this piece, I will describe my decision-making process over where to keep `state`. I will also share my opinion as to whether we still need state management frameworks in 2020.

这就引出了问题，为什么我们还需要状态管理框架？在这篇文章中，我将展示我的决策过程：如何管理 ```state```。关于在2020年我们是否还需要 state 管理框架，我也会分享我的观点。

## What Changed?

## 有什么变化？

So how did we define state before Hooks? Basically, there were two options: defining a local state in the component, or using a state management framework to set it as a global state (e.g. MobX / Redux).

在有 Hooks 之前，我们是如何定义 state 的？基本上，有两种选择：在组件内部定义本地 state，或者用一个 state 管理框架设置全局的 state （例如：Mobx/Redux）。

###  Local state (before hooks)
###本地 state (hooks 之前)

```jsx
export class UserList extends Component {
    state = {
        isLoading: false,
        hasError: false,
        users: []
    }

    searchUsers(value) {
        this.setState({isLoading: true});

        api.get(`/users?searchKey=${value}`)
            .then((data) => this.setState({users: data}))
            .catch(() => this.setState({hasError: true}))
            .finally(() => this.setState({loading: false}));
    }

    render() {
        if (this.state.isLoading) {
            // render loading spinner
        }
        
        if (this.state.hasError) {
            // render error message
        }

        return (
            <div>
                <input onChange={(event) => this.searchUsers(event.target.value)} />
            </div>
        )
       
    }
}
```

The issue with only having these two options is best described in the following scenario. Let’s say our state does not have to be global, but we may want to reuse how we define the local state in multiple components.

在接下来我们会说明只有这两个选择时带来的问题。假设，我们的 state 不必设为全局，但是，我们希望可以在多个组件复用 state，我们该如何定义本地 state。

In the example above, we may want to reuse setting the loading and error state — before Hooks this was not possible. The only option we had was to make it reusable with Redux. In Redux each component which wanted to search users could simply dispatch an action (`searchUsers()`) and listen for the global state to change.

在上面的演示中，我们或许想复用 loading 和 error state，在 Hooks 之前，是不可能的。唯一的选择是需要利用 Redux 来复用它。在 Redux 中，任何需要搜索用户的组件只需简单 dispatch 一个 action（```searchUsers()```）和监听全局 state 的变化即可。

However using this global state (Redux/MobX) results in a few problems:

- More boilerplate code
- Complicated flow
- Multiple components are manipulating the global state, which may cause unwanted side effects

然而，使用这些全局 state（Redux/Mobx）会带来一些问题：

- 更多的样板代码
- 复杂的数据流
- 多个组件同时维护全局 state，这可以能会带来意想不到的副作用。

### The solution: React Hooks!

### 解决方案：React Hooks！

Thankfully React introduced hook’s in React 16.8. From that day onwards, it was possible to share state logic between multiple components.

谢天谢地 React v16.8 引入的 Hooks。从这时起，在做组件之间共享 state 相关的逻辑变得可行。

In the example below, we are now able to share the loading and error behaviour:

在下面的演示中，我们可以共享 loading 和 error 的行为：

```jsx
import {useState} from 'react';

const useRequestHandler = () => {
    const [isLoading, setLoading] = useState(false);
    const [hasError, setError] = useState(false);
    const [data, setData] = useState(null);

    const handleRequest = (request) => {
        setLoading(true);
        setError(false);

        return api.get(request)
            .then(setData)
            .catch(() => setError(true))
            .finally(() => setLoading(false))
    };

    return {isLoading, hasError, data, handleRequest};
};


const UserList = () => {
    const {data, isLoading, hasError, handleRequest} = useRequestHandler();
    
    const searchUsers = (value) => handleRequest(`/users?searchKey=${value}`);
  
    return (
        <React.Fragment>
            {data.map(u => <p>{u.name}</p>)}
        </React.Fragment>
    )
}
```

**Bonus**: We could also make a `useUserSearch` if multiple components wanted to offer the functionality to search through the list of users.

**更多好处**：如果，多个组件需要搜索用户列表的功能，我们也可以自定义 ```useUserSearch``` hook。

However, hooks are no silver bullet. Keeping state in a hook does not mean it becomes a singleton, the state is only bound to one component. There are certain uses where we might only want to keep one instance of state (for example, fetching user info only once). This is where a state management framework proves its value.

然而，hooks 并不是银弹。把 state 保存在 hook 中，并不意味着它就变成了单例对象，它只是绑定在一个组件中。在某些情况下，我们只想保留一个实例 state 对象（例如：只加载一次用户信息）。这是 state 管理框架提供的功能。

## How to Decide Where to Keep State

## 如何，何时，何地管理 state

Now that it’s possible to share state logic across components, how do we decide between keeping state in components (local) or in a state management framework (global)?

现在，可以在多个组件之间共享 state 相关逻辑了，那我们怎么决定 state 保存在组件内（本地），还是保存在 state 管理框架中（全局）呢？

Below is an image of my decision-making process.

下面的图片展示了我的决策过程。

![](https://miro.medium.com/max/1400/1*NPIE1cX1ofWR-_46IYikYA.png)

## What’s the Benefit of a State Management Framework?
## State 管理框架有什么好处呢？

Now we know when to decide between global and local state. But why would we still use a state management framework in 2020? What’s the benefit above using React Hooks?

现在，我们知道如何在全局和本地做抉择了。但是，在2020年为什么还需要 state 管理框架呢？这相比 React Hooks 有什么好处呢？

Here’s a list of benefits:

- Globally defined, which means only one instance of the data
- Only fetching remote data once
- Extensive dev tools
- Provides a standardised way of working for software engineers

有以下几个好处：

- 全局定义，也代表只有一个实例对象
- 只会加载一次远程数据
- 开发工具的支持
- 为软件工程师提供了一种标准的开发方式

## Conclusion

## 总结

We’ve seen that React Hooks has shaken up the whole state management landscape. Since their introduction, it’s been easier to share state logic between components.

我们看到 React Hooks 已经搅动了 state 管理的格局。由于它们的引入，在组件之间共享 state 相关逻辑变得更加简单。

However, Hooks are no silver bullet and we might still need state management frameworks. That doesn’t mean that we need to keep every state globally — most of the time it’s better to keep it at a component level. Moving state to the state management framework should only be done *when absolutely necessary.*

然而，Hooks 并不是银弹，我们仍旧需要 state 管理框架。这也并不是说要让所有的 state 都是全局的 — 大多情况下让其保留在组件之间更加好。只有在*有绝对必要的情况下*，才把 state 移动到 state 管理框架中。