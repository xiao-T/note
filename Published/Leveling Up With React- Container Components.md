## Leveling Up With React: Container Components
## React 进阶：Container 组件

> 原文地址: [https://css-tricks.com/learning-react-container-components/](https://css-tricks.com/learning-react-container-components/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> This tutorial is the second of a three-part series on React by [Brad Westfall](https://twitter.com/bradwestfall). This series is all about going beyond basic React skills and building bigger things, like entire Single Page Applications (SPAs). This article picks up where the last article, on React Router, left off.

这篇教程是 [Brad Westfall](https://twitter.com/bradwestfall) 有关 React 三部曲中的第二部分。这是一个 React 进阶的系列，用来构建更为健壮的应用，比如单页面应用（SPA）。本文的上一篇文章是有关 React Router 的。

> ### Article Series:
> Part 1: [React Router](https://css-tricks.com/learning-react-router/)   
> Part 2: Container Components (You are here!)    
> Part 3: [Redux](https://css-tricks.com/learning-react-redux/)

### 三部曲：
> 第一部分: [React Router](https://css-tricks.com/learning-react-router/)   
> 第二部分: Container Components (就是这篇文章)    
> 第三部分: [Redux](https://css-tricks.com/learning-react-redux/)

> In the [first article](https://css-tricks.com/learning-react-router/), we created routes and views. In this tutorial, we're going to explore a new concept in which components don’t create views, but rather facilitate the ones that do. There is code to follow along with at [GitHub](https://github.com/bradwestfall/CSS-Tricks-React-Series), if you like to dive right into code.

在[第一篇](https://css-tricks.com/learning-react-router/)文章中，我们创建了路由和视图。在这篇教程中，我将引入一个新的概念：不创建视图的组件，但是，这么做是为了方便那些需要创建视图的组件。如果，你喜欢深入，所有的代码都可以在 [GitHub](https://github.com/bradwestfall/CSS-Tricks-React-Series) 查看。

> We’ll also be introducing data to our application. If you’re familiar with with any sort of component-design or MVC patterns, you probably know that it’s generally considered poor-practice to mix your views with application behavior. In other words, while views need to receive data to render it, they shouldn’t know where the data came from, how it changes, or how to create it.  

我们的应用始终是要引入的数据的。如果，你们熟悉任何形式的组件设计或者 MVC 模式，你应该知道如果应用的业务逻辑和视图混在一起是很不好的实践。换句话说，当视图需要接收数据并渲染的时候，它并不需要知道数据来自哪，怎么改变，怎么创建数据。

### Fetching data with Ajax

### 通过 Ajax 获取数据

> As an example of a poor practice, let’s expand on our ```UserList``` component from the previous tutorial to handle its own data fetching:

一个不好的实践示例，让我们用上一篇中的组件 ```UserList``` 扩展一下，绑定我们的数据。

```jsx
// This is an example of tightly coupled view and data which we do not recommend
// 这个示例中视图和数据未解耦，这是我们不推荐的

var UserList = React.createClass({
  getInitialState: function() {
    return {
      users: []
    }
  },

  componentDidMount: function() {
    var _this = this;
    $.get('/path/to/user-api').then(function(response) {
      _this.setState({users: response})
    });
  },

  render: function() {
    return (
      <ul className="user-list">
        {this.state.users.map(function(user) {
          return (
            <li key={user.id}>
              <Link to="{'/users/' + user.id}">{user.name}</Link>
            </li>
          );
        })}
      </ul>
    );
  }
});
```

> If you need a more detailed/beginner explanation of what this component is doing, see [this explanation](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-2-container-components/docs/state-and-props.md).

> 如果，你需要有个这个组件更详情的说明，可以关注[这里](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-2-container-components/docs/state-and-props.md)。

> Why is the example less than ideal?. To start, we’ve broken the rule of mixing “behavior” with “how to render the view” — the two things that should stay separate.

为什么说这个示例不理想？首先，我们将行为和视图混在一起了。他俩应该是相对独立的。

> To be clear, there’s nothing wrong with using ```getInitialState``` to initialize component state, and there’s nothing wrong with conducting an Ajax request from ```componentDidMount``` (although we should probably abstract the actual call out to other functions). The problem is that we’re doing these things together in the same component where the view is stored. This tight-coupling makes the application more rigid and [WET](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). What if you need to fetch a list of users elsewhere too? The action of fetching users is tied down to this view, so it’s not reusable.

要清楚，通过 ```getInitialState``` 初始化组件 state 和在 ```componentDidMount``` 发起一个 Ajax 请求（虽然我们可以把它抽象一个具体的方法）并没有任何错误。问题所在，我们在视图组件又做了其他的事情。这种不解耦的行为导致了应用过于死板和 [WET](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。如果，在其他地方也需要一个 ```UserList```，因为数据和视图是耦合在一起的，所以，不能重用。

> The second problem is that we’re using jQuery for the Ajax call. Sure, jQuery has many nice features, but most of them deal with DOM rendering and React has its own way of doing this. As for jQuery’s non-DOM features like Ajax, chances are you can find lots of alternatives that are more single-feature focused.

第二个问题是我们用了 jQuery 中的 Ajax 方法。当然，jQuery 有很多很棒的功能，但是大多数是操作 DOM 的，而 React 已经有自己的方式处理这些问题。jQuery 中那些和 DOM 无关的功能，比如： Ajax，有很多只关注单一功能的替代方案供你选择。

> One of those alternatives is [Axios](https://github.com/mzabriskie/axios), a promise-based Ajax tool that’s very similar (in terms of API) to jQuery’s promise-based Ajax features. How similar are they?

[Axios](https://github.com/mzabriskie/axios) 就是其中一个替代方案，这是一个基于 promise 的 Ajax 工具，这和 jQuery 中 基于 promise 的 Ajax 很像（在API方面）。到底有多像呢？

```javascript
// jQuery
$.get('/path/to/user-api').then(function(response) { ... });

// Axios
axios.get('/path/to/user-api').then(function(response) { ... });
```

> For the remaining examples, we’ll continue to use Axios. Other similar tools include [got](https://github.com/sindresorhus/got), [fetch](https://github.com/github/fetch), and [SuperAgent](https://github.com/visionmedia/superagent).

接下来的示例中，我们将使用 Axios。其他类似的工具还有：[got](https://github.com/sindresorhus/got)、 [fetch](https://github.com/github/fetch) 和 [SuperAgent](https://github.com/visionmedia/superagent)

### Props and State

> Before we get into Container and Presentational Components, we need to clear up something about props and state.

在介绍 Container 和 Presentational 组件之前，我们必须明白 props 和 state。

> Props and state are somewhat related in the sense that they both “model” data for React components. Both of them can be passed down from parent to child components. However, the props and state of a parent component will become just props to their child component.

在某种意义上 Props 和 State 是有点相关的的，它俩都是 React 组件中的数据模型。它俩都可以被通过父组件传递到子组件中。然而，父组件的 Props 和 State 在子组件中都是作为 Props 存在的。

> As an example, let’s say **ComponentA** passes some of its props and state to its child, **ComponentB**. The ```render``` method of **ComponentA** might look like this: 

举个例子，如果 **ComponentA** 需要传递一些 Props 和 State 到 **ComponentB**。那么在 **ComponentA** 中的 ```render``` 方法就可以这么做：

```jsx
// ComponentA
render: function() {
  return <ComponentB foo={this.state.foo} bar={this.props.bar} />
}
```

> Even though ```foo``` is “state” on the parent, it will become a “prop” on the child **ComponentB**. The attribute for ```bar``` also becomes a prop on the child component because all data passed from parent to child will become props in the child. This example shows how a method from **ComponentB** can access ```foo``` and ```bar``` as props:

尽管 ```foo``` 存储在父级的“state”上，但是在子组件 **ComponentB** 中将存储在“prop”中。属性  ```bar``` 也将变成子组件 prop 中一个值，这是因为任何通过父组件传递到子组件数据都将变成子组件一个 props 值。下面的例子中展示了如何在 **ComponentB** 访问 ```foo``` 和 ```bar```。

```jsx
// ComponentB
componentDidMount: function() {
  console.log(this.props.foo);
  console.log(this.props.bar);
}
```
In the ***Fetching Data with Ajax*** example, data received from Ajax is set as the component’s state. The example doesn’t have child components, but you can imagine if it did, the state would [“flow”](https://facebook.github.io/react/docs/multiple-components.html#data-flow) down from parent to child as props.

在 ***通过 Ajax 获取数据*** 例子中，数据通过 Ajax 接收并存储在组件的 state 中。在这个例子中没有子组件，但是，你可以设想一下，父级的 state 可以以一种[“流”](https://facebook.github.io/react/docs/multiple-components.html#data-flow)的方式传递到子组件的 props 中。

To understand state better, see the [React Documentation](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#components-are-just-state-machines). From here on, this tutorial will refer to the data that changes over time, as “state”.

为了更好的理解 state，可以看一下 [React 文档](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#components-are-just-state-machines)。在这里，这篇教程将会解释 state 是怎么随着时间变化的。

### It’s time to break up

### 是时间打破常规了

> In the ***Fetching Data with Ajax*** example, we created a problem. Our ```UserList``` component works but it’s trying to do too many things. To solve the problem, let’s break the ```UserList``` into two components that each serve a different role. The two component types will be conceptually called **Container Components** and **Presentational Components**, a.k.a. "smart" and "dumb" components.

在 ***通过 Ajax 获取数据*** 例子中，是有问题的。虽然我的组件 ```UserList``` 可以正常工作，但是不够灵活。为了解决这个问题，我们把 ```UserList``` 分拆成两个组件，两者分别承担不通的角色。这两个组件将被定义为 **Container Components** 和 **Presentational Components**，又或者叫：“smart” 和 “dumb”。

> In short, Container Components source the data and deal with state. State is then passed to Presentational Components as props and is then rendered into views.

简而言之，容器组件将数据存储在 state 中。state 通过 props 传递到 Presentational 组件中并渲染。

> The terms "smart" vs "dumb" components are going away in the community. I’m only making reference to them here in case you read about them in older articles, so you’ll know they’re the same concept as Container vs Presentational.

> 在社区中 "smart" 和 "dumb" 的叫法已经消失了。我在这里提起它们，是为了你们在看到之前的旧文章时候能够明白，它们与 Container 和 Presentational 是相同的概念。

### Presentational Components

> You may not know it, but you’ve already seen Presentation Components before in this tutorial series. Just imagine how the ```UserList``` component looked before it was managing its own state:

你或许不知道，在这个系列的教程中你已经见过 Presentation 组件。想象一下 ```UserList``` 组件是如何管理的它的 state 的：

```jsx
var UserList = React.createClass({
  render: function() {
    return (
      <ul className="user-list">
        {this.props.users.map(function(user) {
          return (
            <li key={user.id}>
              <Link to="{'/users/' + user.id}">{user.name}</Link>
            </li>
          );
        })}
      </ul>
    );
  }
});
```
> It’s not *exactly* the same as before, but it is a Presentational Component. The big difference between it and [the original](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-1-react-router/app/components/user-list.js) is that this one iterates over user data to create list-items, and receives the user data via props.

这与之前是*完全*不同的，但是它就是一个 Presentational 组件。和[原来的](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-1-react-router/app/components/user-list.js)最大不同之处就是： ```users``` 的数据创建了一个列表，数据是通过 props 传递的。

> Presentational Components are "dumb" in the sense that they have no idea how the props they received came to be. They have no idea about state.

Presentational 组件某种意义上是 “dumb“ 的，它们不关心 props 从何而来，也不会有 state。

> Presentational Components should never change the prop data itself. In fact, any component that receives props should consider that data immutable and owned by the parent. While the Presentational Component shouldn’t change the meaningfulness of the data in the prop, it can format the data for the view (such as turning a Unix timestamp into a something more human readable).

Presentational 组件坚决不会改变自身的 prop。事实上，任何通过 props 接收数据的组件都应该考虑到这些数据是不可变的并且属于父级组件。虽然 Presentational 组件不能改变原始数据，但是它可以格式化这些数据（比如：把 Unix 时间戳格式化成一个可读字符串）。

> In React, events are attached directly to the view with attributes like ```onClick```. However, one might wonder how events work since Presentational Components aren’t supposed to change the props. For that, we have a whole section on events below.

在 React 中，事件是直接通过属性绑定的例如：```onClick```。然而，有人想知道在 Presentational 组件中事件又是如何处理的呢？对于这一点，我将在接下来的内容解释。

### Iterations

### 迭代器

> When creating DOM nodes in a loop, the ```key``` attribute [is required](https://facebook.github.io/react/docs/multiple-components.html#dynamic-children) to be something unique (relative to its siblings). Note that this is only for the highest level DOM node — the ```<li>``` in this case.

当我们循环创建 DOM 的时候，```key``` 属性是[必需的](https://facebook.github.io/react/docs/multiple-components.html#dynamic-children)保证它们的唯一性（相对其他兄弟元素）。注意这主要是针对最高级 DOM 节点 - 在这个示例中是 ```<li>```。

> Also, if the nested ```return``` looks funky to you, consider another approach which does the same thing by splitting the creation of a list item into its own function:

另外，为了让嵌套的 ```return``` 看起来更易读，可以考虑把循环列表封装成一个单独的方法：

```jsx 
var UserList = React.createClass({
  render: function() {
    return (
      <ul className="user-list">
        {this.props.users.map(this.createListItem)}
      </ul>
    );
  },

  createListItem: function(user) {
    return (
      <li key={user.id}>
        <Link to="{'/users/' + user.id}">{user.name}</Link>
      </li>
    );
  }
});
```

### Container Components

> Container Components are almost always the parents of Presentational Components. In a way, they serve as a intermediary between Presentational Components and the rest of the application. They’re also called “smart” components because they’re aware of the application as a whole.

Container 组件几乎总是作为 Presentational 组件的父级存在。在某种意义上，它们像是一个中介服务与 Presentational 和应用其他的部分。它们也被称作 “smart” 组件，这是因为它们影响这应用的全局。

> Since Container and Presentational Components need to have different names, we’ll call this one ```UserListContainer``` to avoid confusion:

由于 Container 和 Presentational 组件需要不同的名字区分，所以，我将这个组件命名为 ```UserListContainer``` 来避免混淆。

```jsx
var React = require('react');
var axios = require('axios');
var UserList = require('../views/list-user');

var UserListContainer = React.createClass({
  getInitialState: function() {
    return {
      users: []
    }
  },

  componentDidMount: function() {
    var _this = this;
    axios.get('/path/to/user-api').then(function(response) {
      _this.setState({users: response.data})
    });
  },

  render: function() {
    return (<UserList users={this.state.users} />);
  }
});

module.exports = UserListContainer;
```

> For brevity, these examples have been leaving out ```require()``` and ```module.exports``` statements. But in this case, it’s important to show that Container Components pull in their respective Presentational Components as a direct dependency. For completeness, this example shows all the requires which would be necessary.

> 为了简洁起见，这些示例已经不再使用 ```require()``` 和 ```module.exports```。但是，在这个示例中，为了展示最重要的部分 Container 如何引入 Presentational 组件的。为了完整性，这个示例中一些必要的元素我都展示了。

> Container Components can be created just like any other React component. They also have a ```render``` methods just like any other component, they just don’t create anything to render themselves. Instead, they return the result of the Presentational Component.

创建 Container 组件就像创建其他组件一样。它也有一个 ```render``` 方法，但是它不渲染任何的 DOM。而是返回一个 Presentational 组件。

> **A quick note on ES6 Arrow Functions:** You may notice the classic ```var _this = this``` trick needed for the example above. [ES6 Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), besides having shorter syntax, have [other benefits](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-2-container-components/README.md#es6-arrow-functions) which alleviate the need for this trick. To allow you to focus on just learning React, this tutorial avoids ES6 syntax in favor of the older ES5 syntax. However, the GitHub guide for this series makes heavy use of ES6 and it has some explanations in its README files.

> **ES6 三角函数的提示：** 在上面的示例中，你或许已经注意到使用了经典的技巧 ```var _this = this```。[ES6三角函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，除了语法简介，还有很多[其他好处](https://github.com/bradwestfall/CSS-Tricks-React-Series/blob/master/guide-2-container-components/README.md#es6-arrow-functions)让你避免使用这种技巧。为了让你专注学习 React，在这篇教程中依然还是使用的 ES5 的语法。然而，这个系列的 GitHub 指引中使用了大量的 ES6 语法，在 README 文件也有说明。

### Events

### 事件

> So far, we’ve shown how state can be passed from Container to Presentational Components, but what about behavior? Events fall into the category of behavior and they oftentimes need to mutate data. Events in React are attached at the view level. For separation of concerns, this can cause a problem in our Presentational Components if we create event functions where the view is.

到目前为止，我们已经展示了如何将 state 通过 Container 组件传递到 Presentational 组件，但是行为呢？事件全部归属于行为并且通常情况它们需要改变数据。在 React 中事件是绑定在视图层级的。为了分离 Container 和 Presentational，如果我们在 Presentational 组件中创建事件方法，这可能会引起其他问题。 

> To elaborate, let’s start by adding an event to our Presentational Component (a ```<button>``` you can click) directly to identify problems:

为了详细说明，让我们在 Presentational 组件添加一个事件（一个 ```<button>``` 你以点击）直接来演示问题：

```jsx
// Presentational Component
var UserList = React.createClass({
  render: function() {
    return (
      <ul className="user-list">
        {this.props.users.map(function(user) {

          return (
            <li key={user.id}>
              <Link to="{'/users/' + user.id}">{user.name}</Link>
              <button onClick={this.toggleActive}>Toggle Active</button>
            </li>
          );

        })}
      </ul>
    );
  },

  toggleActive: function() {
    // We shouldn't be changing state in presentational components :(
  }
});
```

> This would technically work, but it’s not a good idea. Chances are, the event is going to need to change data, and data which changes should be stored as state — something the Presentational Component should not be aware of.

这在技术上是可行的，但是并不是一个好的主意。有可能，这个事件需要去改变那些存储在 state 中的数据，但是这些不应该由 Presentational 组件来处理。

> In our example, the state change would be the user’s “activeness”, but you can make up any functions you want to tie to ```onClick```.

在我们的示例中，state 的改变将改变 user 的 “activeness”，但是你可以通过 ```onClick``` 做任何你想做的事。

> A better solution is to pass functionality from the Container Component into the Presentational Component as a prop like this:

一个更好的方式是通过 Container 组件传递一个方法到 Presentational 组件，就像这样：

```jsx
// Container Component
var UserListContainer = React.createClass({
  ...
  render: function() {
    return (<UserList users={this.state.users} toggleActive={this.toggleActive} />);
  },

  toggleActive: function() {
    // We should change state in container components :)
  }
});

// Presentational Component
var UserList = React.createClass({
  render: function() {
    return (
      <ul className="user-list">
      {this.props.users.map(function(user) {

        return (
          <li key={user.id}>
            <Link to="{'/users/' + user.id}">{user.name}</Link>
            <button onClick={this.props.toggleActive}>Toggle Active</button>
          </li>
        );

      })}
      </ul>
    );
  }
});
```

> The ```onClick``` attribute is required to be where the view is — at the Presentational Component. However, the function it calls has been moved to the parent Container Component. This is better because the Container Component deals with state.

```onClick``` 必须在视图中绑定，也就是 Presentational 组件。然而，方法是在父级 Container 组件中执行的。在 Container 组件中处理 state 更加合理。

> If the parent function happens to change the state, then the state change will cause a re-render on the parent function which in turn will update the child component. This happens automatically in React.

如果父级 state 发生了改变，这将导致父级重新渲染，自组件也会跟着重新渲染。这些在 React 中都是自动发生的。

> Here is a demo which shows how the event on the Container Component can change state which will automatically update the Presentational Component:

这有个示例展示了 Container 组件是如何改变 state 而导致 Presentational 组件自动更新的：

[Demo](https://codepen.io/bradwestfall/pen/oxBGRa)

> Take note of how this example deals with [immutable data](https://facebook.github.io/react/docs/component-api.html) and makes use of the [.bind()](http://stackoverflow.com/a/28688643) method

> 需要注意的是：这个示例中是如何处理[不可变数据](https://facebook.github.io/react/docs/component-api.html)和如何使用 [.bind()](http://stackoverflow.com/a/28688643) 方法的。

### Using Container Components with the Router

### 在路由中使用 Container 组件

> The router should no longer use ```UserList``` directly. Instead, it will use ```UserListContainer``` directly, which in turn will use ```UserList```. Ultimately, ```UserListContainer``` returns the result of ```UserList```, so the router will still receive what it needs.

路由并不能直接使用 ```UserList```。相反，它应该使用 ```UserListContainer```。最终，```UserListContainer``` 将返回 ```UserList```，所以路由最终还是会得到应得的。

### Data Flow and the Spread Operator

### 数据流和扩展器

> In React, the concept of props being passed from parent components down to child components is called flow. The examples so far have only shown simple parent-child relationships, but in a real application there could be many nested components. Imagine data flowing from high-level parent components down through many child components via state and props. This is a fundamental concept in React and is important to keep in mind as we move forward into the next tutorial on Redux.

在 React 中，props 通过父级组件传递到子组件被称为流。到目前为止的示例我们仅仅展示了简单的父子关系，但是实际的应用中会有很多的嵌套组件。想象一下，数据通过高阶的父级组件流向了很多的子组件。这在 React 中是一个很基本的概念，在我们进入下一个有关 Redux 教程前你需要记住它。

> ES6 has a new [spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) which is very useful. React has adopted a [similar syntax](https://facebook.github.io/react/docs/jsx-spread.html#spread-attributes) to JSX. This really helps React with how data flows via props. The GitHub guide for this tutorial uses it too, so be sure to read the [guide documentation](https://github.com/bradwestfall/CSS-Tricks-React-Series/tree/master/guide-2-container-components#es6-spread-operator) on this feature.

ES6有一个很有用的[扩展语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)。React 在 JSX 中实现了[类似](https://facebook.github.io/react/docs/jsx-spread.html#spread-attributes)的语法。在处理数据流的时候这真的非常有用。GitHub 的指引中也用到了它。请务必要认真的阅读有关的[文档](https://github.com/bradwestfall/CSS-Tricks-React-Series/tree/master/guide-2-container-components#es6-spread-operator)。

### Stateless Functional Components

### 无状态函数组件

> As of React 0.14 (released in late 2015), there is a new feature for creating stateless (Presentational) components even easier. The new feature is called [Stateless Functional Components](https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#stateless-functional-components)

React 0.14 之后，有一种新的功能更容易创建 Presentational 组件。这种新功能被叫做[无状态函数组件](https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#stateless-functional-components)

> By now you’ve probably noticed that as you separate your Container and Presentational Components, many of your Presentational ones just have a render method. In these cases, React now allows the component to be written as a single function:

现在你们在分离 Container 和 Presentational 组件或许已经注意到，大多数 Presentational 组件紧紧只有一个 render 方法。在这种情况下，React 允许组件写成一个单函数： 

```jsx
// The older, more verbose way
var Component = React.createClass({

  render: function() {
    return (
      <div>{this.props.foo}</div>
    );
  }

});

// The newer "Stateless Functional Component" way
var Component = function(props) {
  return (
    <div>{props.foo}</div>
  );
};
```
> You can clearly see that the new way is more compact. But remember, this is only an option for components that just need a ```render``` method.

你可以清楚的看到这新的方式有多么的简洁。但是记住，这种方式仅仅适用那些只有一个 ```render``` 方法的组件。

> With the new Stateless Functional way, the function accepts an argument for ```props```. This means it doesn’t need to use ```this``` to access props.

使用新的无状态函数方式，函数接受一个 ```props``` 参数。也就是说在这里不需要使用 ```this``` 去访问 props。

> Here is a very good [Egghead.io video](https://egghead.io/lessons/react-building-a-react-js-app-utilizing-stateless-function-components?series=build-your-first-react-js-application) on Stateless Functional Components.

这有一个很好的有关无状态函数组件[视频教程](https://egghead.io/lessons/react-building-a-react-js-app-utilizing-stateless-function-components?series=build-your-first-react-js-application)。

### MVC

> As you’ve probably seen so far, React doesn’t resemble traditional MVC. Often times React is referred to as “just the view layer”. The problem I have with this statement is that it’s too easy for React beginners to believe that React ***should*** fit into their familiarity with traditional MVC, as if it’s meant to to be used with traditional controllers and models brought in from third-party.

如你所见，React 并不像传统的MVC。通常来说 React 只是负责“视图”。但是，对于初学者容易把 React 当成传统的 MVC，所以，他们会结合第三方的控制器和模型一起使用。

> While it is ***true*** that React doesn’t have “traditional controllers”, it does provide a means to separate views and behavior in its own special way. I believe that Container Components serve the same fundamental purpose that a controller would serve in traditional MVC.

虽然 React 不是传统的 MVC，但是，它有一种特别方式分离视图和行为。我相信 Container 组件基本的目的和传统 MVC 的控制器一致。

> As for models, I’ve seen people use Backbone models with React and I’m sure they have all kinds of opinions on whether that worked out well for them. But I’m not convinced that traditional models are the way to go in React. React wants to flow data in a way that just doesn’t lend itself well to how traditional models work. The Flux design pattern, [created by Facebook](https://facebook.github.io/flux/docs/overview.html), is a way to embrace React’s innate ability to flow data. In the next tutorial, we’ll cover Redux, a very popular Flux implementation and what I view as an alternative to traditional models.

至于模型，我见过有些人在 React 中用 Backbone，我相信他们有他们自己的理由。但是，在 React 中我不认为这就是传统模型的实现方式。React 的数据流模式对传统的模型并不友好。[Facebook 发明的](https://facebook.github.io/flux/docs/overview.html) Flux 模式，就是一种拥抱 React 数据流的方式。下篇教程中，我将介绍 Redux，一种流行的 Flux 的实现，我认为这就是传统模型的替代方案。

### Summary

### 总结

> Container Components are more of a concept and not an exact solution. The examples in this tutorial just are one way to do them. The concept though, is so well accepted that it’s even [Facebook’s policy](https://youtu.be/KYzlpRvWZ6c?t=22m32s) to use them within their teams — although they might use different terminology.

Container 组件更多的是一个概念，并不是一个解决方案。教程中的示例紧紧是一种实现方式。尽管如此，这种方案很受欢迎，以至于 [Facebook’s policy](https://youtu.be/KYzlpRvWZ6c?t=22m32s) 引入他们的团队，尽管他们有不同的术语。

> This tutorial was highly influenced by [other](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.bz00lr5e4) [articles](https://medium.com/@learnreact/container-components-c0e67432e005#.uuq8dbcs4) on this topic. Be sure to look at this tutorial’s official [GitHub guides](https://github.com/bradwestfall/CSS-Tricks-React-Series/tree/master/guide-2-container-components) for even more information and a working example of Container Components.

这篇教程有借鉴[其他](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.bz00lr5e4)有关专题[文章]((https://medium.com/@learnreact/container-components-c0e67432e005#.uuq8dbcs4))。为了了解更多的 Container 示例，请务必关注官方的 [GitHub 指南](https://github.com/bradwestfall/CSS-Tricks-React-Series/tree/master/guide-2-container-components)。

 

 