## You Probably Don't Need Derived State
## 你或许不需要派生状态

> 原文地址: <https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

React 16.4 included a [bugfix for getDerivedStateFromProps](https://reactjs.org/blog/2018/05/23/react-v-16-4.html#bugfix-for-getderivedstatefromprops) which caused some existing bugs in React components to reproduce more consistently. If this release exposed a case where your application was using an anti-pattern and didn’t work properly after the fix, we’re sorry for the churn. In this post, we will explain some common anti-patterns with derived state and our preferred alternatives.

React 16.4 修复了一些 [getDerivedStateFromProps 的 bugs](https://reactjs.org/blog/2018/05/23/react-v-16-4.html#bugfix-for-getderivedstatefromprops)。如果，你的应用中用到了反模式，这次发布后并不能正确运行，我们对此非常抱歉。在这篇文章中，我会介绍一些常用的有派生状态的反模式，并提供替代方案。

For a long time, the lifecycle ```componentWillReceiveProps``` was the only way to update state in response to a change in props without an additional render. In version 16.3, [we introduced a replacement lifecycle, getDerivedStateFromProps](https://reactjs.org/blog/2018/03/29/react-v-16-3.html#component-lifecycle-changes) to solve the same use cases in a safer way. At the same time, we’ve realized that people have many misconceptions about how to use both methods, and we’ve found anti-patterns that result in subtle and confusing bugs. The ```getDerivedStateFromProps``` bugfix in 16.4 [makes derived state more predictable](https://github.com/facebook/react/issues/12898), so the results of misusing it are easier to notice.

很长一段时间，生命周期 ```componentWillReceiveProps``` 是唯一一种可以通过属性更新状态并不会引起额外渲染的方法。在 16.3，我们介绍了替代生命周期：[```getDerivedStateFromProps```](https://reactjs.org/blog/2018/03/29/react-v-16-3.html#component-lifecycle-changes)，它可以以更安全的方式解决同样的问题。同时，我们也意识到大家对如何使用两个方法有很多误解，也发现了反模式一些细小、扑朔迷离的 bugs。在 16.4 中 ```getDerivedStateFromProps``` 可以修复这些问题，让派生状态更加可预测，因此滥用的结果更容易被看到。

> **Note**    
> All of the anti-patterns described in this post apply to both the older ```componentWillReceiveProps``` and the newer ```getDerivedStateFromProps```.

> **注意⚠️**   
> 这篇文章中描述的所有反模式在旧版本中 ```componentWillReceiveProps``` 和 新版本中 ```getDerivedStateFromProps``` 都适用。

This blog post will cover the following topics:

- [When to use derived state](#when-to-use-derived-state)
- [Common bugs when using derived state](#common-bugs-when-using-derived-state)
    - [Anti-pattern: Unconditionally copying props to state](#anti-pattern:-unconditionally-copying-props-to-state)
    - [Anti-pattern: Erasing state when props change](#anti-pattern:-erasing-state-when-props-change)
- [Preferred solutions](#preferred-solutions)
- [What about memoization?](#what-about-memoization?)

这篇文章中主要有以下几个主题：

- [何时使用派生状态](#when-to-use-derived-state)
- [派生状态常常出现的 bugs](#common-bugs-when-using-derived-state)
    - [反模式: 无条件的将属性复制状态](#anti-pattern:-unconditionally-copying-props-to-state)
    - [反模式: 属性变化时状态被忽略](#anti-pattern:-erasing-state-when-props-change)
- [首先解决方案](#preferred-solutions)
- [什么是记忆化?](#what-about-memoization?)

### When to Use Derived State

```getDerivedStateFromProps``` exists for only one purpose. It enables a component to update its internal state as the result of **changes in props**. Our previous blog post provided some examples, like [recording the current scroll direction based on a changing offset prop](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#updating-state-based-on-props) or [loading external data specified by a source prop](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#fetching-external-data-when-props-change).

```getDerivedStateFromProps``` 只有一个目的。它可以让组件根据**属性的变化**更新内部的状态。我们之前的文章中有提供一些示例，比如[基于属性的偏移记录当前滚动的方向](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#updating-state-based-on-props) 和 [根据一些特定的属性加载第三方数据](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#fetching-external-data-when-props-change)。

We did not provide many examples, because as a general rule, **derived state should be used sparingly**. All problems with derived state that we have seen can be ultimately reduced to either (1) unconditionally updating state from props or (2) updating state whenever props and state don’t match. (We’ll go over both in more detail below.)

我们不再提供更多的示例，这是因为，通常情况下**派生状态需要谨慎使用**。派生状态引起的所有问题最终可以总结为（1）无条件通过属性更新状态，（2）状态和属性不匹配也更新状态。（下面会有两种情况的详细内容）。

- If you’re using derived state to memoize some computation based only on the current props, you don’t need derived state. See [What about memoization?](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization) below.
- If you’re updating derived state unconditionally or updating it whenever props and state don’t match, your component likely resets its state too frequently. Read on for more details.

- 如果，你根据当前属性计算状态时，你不需要派生状态。可以看看下面的[什么是记忆化](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
- 如果，你无条件的更新派生状态，在属性和状态不匹配的时候也更新状态，这样你的组件更新的就太频繁了。继续阅读查看更多信息。

### Common Bugs When Using Derived State

The terms [“controlled”](https://reactjs.org/docs/forms.html#controlled-components) and [“uncontrolled”](https://reactjs.org/docs/uncontrolled-components.html) usually refer to form inputs, but they can also describe where any component’s data lives. Data passed in as props can be thought of as **controlled** (because the parent component *controls* that data). Data that exists only in internal state can be thought of as **uncontrolled** (because the parent can’t directly change it).


[“可控”](https://reactjs.org/docs/forms.html#controlled-components)和[“不可控”](https://reactjs.org/docs/uncontrolled-components.html)通常是指表单，但是也可以代表组件中的数据来自哪里。数据通过属性传递可以理解为**可控**（因为父组件*可以控制*数据）。数据仅仅在内部以状态形式存在可以称为**不可控**（因为父组件不能直接修改它）。

The most common mistake with derived state is mixing these two; when a derived state value is also updated by ```setState``` calls, there isn’t a single source of truth for the data. The [external data loading example](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#fetching-external-data-when-props-change) mentioned above may sound similar, but it’s different in a few important ways. In the loading example, there is a clear source of truth for both the “source” prop and the “loading” state. When the source prop changes, the loading state should **always** be overridden. Conversely, the state is overridden only when the prop **changes** and is otherwise managed by the component.

派生状态的错误大部分包括两种情况；状态值不是来自单一数据源，调用 ```setState``` 的方法也会更新它。有点类似[加载第三方数据的示例](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#fetching-external-data-when-props-change)，但是在一些关键点也有不同。 在加载数据这个示例中，属性和状态都有非常明确真实来源。当属性改变时，状态**总是**会被重写。相反，状态被重写时属性必然会改变，其余都是组件来管理。

Problems arise when any of these constraints are changed. This typically comes in two forms. Let’s take a look at both.

当任何一种约束被改变时问题都会出现。典型的有两种情况。让我们来一起看一下。

#### Anti-pattern: Unconditionally copying props to state

A common misconception is that ```getDerivedStateFromProps``` and ```componentWillReceiveProps``` are only called when props “change”. These lifecycles are called any time a parent component rerenders, regardless of whether the props are “different” from before. Because of this, it has always been unsafe to unconditionally override state using either of these lifecycles. **Doing so will cause state updates to be lost**.

一个常见的误解是：```getDerivedStateFromProps``` 和 ```componentWillReceiveProps``` 只有在属性改变是才被调用。然而，不管属性有没有变化，在父组件重新渲染的时候它们都会被调用。就是因为这样，通过这两个生命周期无条件更新状态是不安全的。**这样会导致状态的更新丢失**。

Let’s consider an example to demonstrate the problem. Here is a ```EmailInput``` component that “mirrors” an email prop in state:

让我们看看示例中的问题。```EmailInput``` 组件会把属性中的 email 复制到状态中：

```jsx
class EmailInput extends Component {
  state = { email: this.props.email };

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />;
  }

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  componentWillReceiveProps(nextProps) {
    // This will erase any local state updates!
    // Do not do this.
    this.setState({ email: nextProps.email });
  }
}
```

At first, this component might look okay. State is initialized to the value specified by props and updated when we type into the ```<input>```. But if our component’s parent rerenders, anything we’ve typed into the ```<input>``` will be lost! ([See this demo for an example.](https://codesandbox.io/s/m3w9zn1z8x)) This holds true even if we were to compare ```nextProps.email !== this.state.email``` before resetting.

首先，组件看起来没什么问题。状态通过属性的值初始化，在 ```<input>``` 有变化的时候也会更新状态。但是，如果父组件被重新渲染后，任何 ```<input>``` 的值都会丢失！（[这是demo](https://codesandbox.io/s/m3w9zn1z8x)）。即使在重置前我们对比了 ```nextProps.email !== this.state.email``` 也是无效的。

In this simple example, adding ```shouldComponentUpdate``` to rerender only when the email prop has changed could fix this. However in practice, components usually accept multiple props; another prop changing would still cause a rerender and improper reset. Function and object props are also often created inline, making it hard to implement a ```shouldComponentUpdate``` that reliably returns true only when a material change has happened. [Here is a demo that shows that happening.](https://codesandbox.io/s/jl0w6r9w59) As a result, ```shouldComponentUpdate``` is best used as a performance optimization, not to ensure correctness of derived state.

在这个简单的示例中，通过在 ```shouldComponentUpdate``` 判断只有 email 改变的时候才更新状态可以修复这种问题。然而事实上，组件通常会有多个属性；任何的属性改变仍然会引起重新渲染和不正确的重置。函数和对象经常会在行内创建，如果仅仅通过内在的某些变化在 ```shouldComponentUpdate``` 来判断是否返回 ```true``` 比较困难。[通过这个 demo 可以看看到底发生了什么。](https://codesandbox.io/s/jl0w6r9w59)结果是：```shouldComponentUpdate``` 可以用来做很好的性能优化，但是并不能保证派生状态的正确性。

Hopefully it’s clear by now why **it is a bad idea to unconditionally copy props to state**. Before reviewing possible solutions, let’s look at a related problematic pattern: what if we were to only update the state when the email prop changes?

希望你已经明白：**为什么无条件的把属性复制到状态是多么的糟糕**。在审查解决方案之前，让我们先看看相关模式的问题：如果我们仅仅在属性改变的时候更新状态会怎么样呢？

#### Anti-pattern: Erasing state when props change

Continuing the example above, we could avoid accidentally erasing state by only updating it when ```props.email``` changes:

继续看上面提到的示例，在 ```props.email``` 改变的时候我们可以避免状态的值意外丢失：

```jsx
class EmailInput extends Component {
  state = {
    email: this.props.email
  };

  componentWillReceiveProps(nextProps) {
    // Any time props.email changes, update state.
    if (nextProps.email !== this.props.email) {
      this.setState({
        email: nextProps.email
      });
    }
  }
  
  // ...
}
```

> **Note**    
> Even though the example above shows ```componentWillReceiveProps```, the same anti-pattern applies to ```getDerivedStateFromProps```.

> **注意⚠️**    
> 尽管示例中只是展示了 ```componentWillReceiveProps```，同样的反模式也适用 ```getDerivedStateFromProps```。

We’ve just made a big improvement. Now our component will erase what we’ve typed only when the props actually change.

我们刚刚已经有了很大的提升。现在，我们的组件只有在属性真正改变时才会更新。

There is still a subtle problem. Imagine a password manager app using the above input component. When navigating between details for two accounts with the same email, the input would fail to reset. This is because the prop value passed to the component would be the same for both accounts! This would be a surprise to the user, as an unsaved change to one account would appear to affect other accounts that happened to share the same email. ([See demo here.](https://codesandbox.io/s/mz2lnkjkrx))

仍然有一些细小的问题。想想一下如果一个密码管理应用使用上面的 input 组件。当两个账户用同样的 email 来回切换时，input 的值将不会被重置。这是因为两个账户传递给组件的属性是一样的！对于用户来说这将是个惊喜，一个账户的改变将会影响其它相同属性的账户。（[看看这里的 demo](https://codesandbox.io/s/mz2lnkjkrx)）

This design is fundamentally flawed, but it’s also an easy mistake to make. ([I’ve made it myself!](https://twitter.com/brian_d_vaughn/status/959600888242307072)) Fortunately there are two alternatives that work better. The key to both is that **for any piece of data, you need to pick a single component that owns it as the source of truth, and avoid duplicating it in other components**. Let’s take a look at each of the alternatives.

设计从根本上讲是有缺陷的，但是这也很容易被误解。([我自己就误解了!](https://twitter.com/brian_d_vaughn/status/959600888242307072)) 幸好，有两种替代方案可以很好的解决。关键是：**对于任何数据，都需要有一个组件是数据的真实来源，并且避免在其它组件中复制**。让我们看看这些替代的解决方案。

### Preferred Solutions

#### Recommendation: Fully controlled component

#### 建议：完全控制组件

One way to avoid the problems mentioned above is to remove state from our component entirely. If the email address only exists as a prop, then we don’t have to worry about conflicts with state. We could even convert ```EmailInput``` to a lighter-weight functional component:

上面提到的问题可以通过移除组件所有的状态来避免。如果 email 只是作为一个属性形式存在，这样我们就不需担心会影响到状态。我们可以把 ```EmailInput``` 组件转化成轻量化的功能组件：

```jsx
function EmailInput(props) {
  return <input onChange={props.onChange} value={props.email} />;
}
```

This approach simplifies the implementation of our component, but if we still want to store a draft value, the parent form component will now need to do that manually. ([Click here to see a demo of this pattern.](https://codesandbox.io/s/7154w1l551))

这种行为简化了我们的组件，但是，我们如果还想存储一些值的话，这种模式下就需要手动管理了。（[可以看看这个模式的 demo](https://codesandbox.io/s/7154w1l551)）

#### Recommendation: Fully uncontrolled component with a key

#### 建议：为完全不受控制的组件添加一个 key

Another alternative would be for our component to fully own the “draft” email state. In that case, our component could still accept a prop for the initial value, but it would ignore subsequent changes to that prop:

另一种替代方案就是为我们的组件提供属于组件自己的默认状态值。在这个案例中，组件可以接受一个属性来初始化这个值，但是，需要忽略后续属性的改变：

```jsx
class EmailInput extends Component {
  state = { email: this.props.defaultEmail };

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />;
  }
}
```

In order to reset the value when moving to a different item (as in our password manager scenario), we can use the special React attribute called ```key```. When a ```key``` changes, React will [create a new component instance rather than update the current one](https://reactjs.org/docs/reconciliation.html#keys). Keys are usually used for dynamic lists but are also useful here. In our case, we could use the user ID to recreate the email input any time a new user is selected:

在不同选项中切换时为了更新不同的值（就像我们的密码管理应用），我们可以使用 React 中的特殊属性 ```key```。每当 ```key``` 改变时，React [将会创建一个全新的组件实例替换当前组件](https://reactjs.org/docs/reconciliation.html#keys)。Keys 通常会在渲染动态列表的时候用到，但是在这也非常有用。在我们的示例中，用户每次切换时，我们可以用 user ID 新建组件。

```jsx
<EmailInput
  defaultEmail={this.props.user.email}
  key={this.props.user.id}
/>
```

Each time the ID changes, the ```EmailInput``` will be recreated and its state will be reset to the latest ```defaultEmail``` value. ([Click here to see a demo of this pattern.](https://codesandbox.io/s/6v1znlxyxn)) With this approach, you don’t have to add ```key``` to every input. It might make more sense to put a ```key``` on the whole form instead. Every time the key changes, all components within the form will be recreated with a freshly initialized state.

每次 ID 被改变，```EmailInput``` 都会被重新创建，并且状态被重置为最新的 ```defaultEmail```。（[点击查看 demo](https://codesandbox.io/s/6v1znlxyxn)）使用这种方式，你不必给每个 input 添加 ```key```。为了更有意义可以把 ```key``` 设置在整个表单上。每次改变，整个表单组件都会以全新的状态被新建。

In most cases, this is the best way to handle state that needs to be reset.

大多情况下，我们需要重置状态时，这是最好的方案。

> **Note**    
> While this may sound slow, the performance difference is usually insignificant. Using a key can even be faster if the components have heavy logic that runs on updates since diffing gets bypassed for that subtree.

> **注意⚠️**   
> 听起来这样会慢，通常这点对性能微不足道。如果，组件有很多逻辑在更新时绕过了对子元素的对比，这甚至会更快。

#### Alternative 1: Reset uncontrolled component with an ID prop

#### 替代方案1:通过 ID 属性重置不可控组件

If ```key``` doesn’t work for some reason (perhaps the component is very expensive to initialize), a workable but cumbersome solution would be to watch for changes to “userID” in ```getDerivedStateFromProps```:

如果，因为某些原因 ```key``` 并无效果（可能是组件在初始化的时候太笨重），我们可以通过 ```getDerivedStateFromProps``` 方法监控 “userID” 的变化，虽然可行，但是太笨重：

```jsx
class EmailInput extends Component {
  state = {
    email: this.props.defaultEmail,
    prevPropsUserID: this.props.userID
  };

  static getDerivedStateFromProps(props, state) {
    // Any time the current user changes,
    // Reset any parts of state that are tied to that user.
    // In this simple example, that's just the email.
    if (props.userID !== state.prevPropsUserID) {
      return {
        prevPropsUserID: props.userID,
        email: props.defaultEmail
      };
    }
    return null;
  }

  // ...
}
```

This also provides the flexibility to only reset parts of our component’s internal state if we so choose. ([Click here to see a demo of this pattern.](https://codesandbox.io/s/rjyvp7l3rq))

如果，让我们选择，我们可以仅仅重置组件内部部分状态，这也更加灵活。（[点击产看 demo](https://codesandbox.io/s/rjyvp7l3rq)）

> **Note**    
> Even though the example above shows ```getDerivedStateFromProps```, the same technique can be used with ```componentWillReceiveProps```.

> **⚠注意️**    
> 尽管上面的示例中只展示了 ```getDerivedStateFromProps```，同样的技术方案 ```componentWillReceiveProps``` 也适用。

#### Alternative 2: Reset uncontrolled component with an instance method

#### 替代方案2: 通过一个实例方法重置不可控组件

More rarely, you may need to reset state even if there’s no appropriate ID to use as ```key```. One solution is to reset the key to a random value or autoincrementing number each time you want to reset. One other viable alternative is to expose an instance method to imperatively reset the internal state:

极少情况下，没有可操作的 ID 作为 ```key``` 重置状态。一种解决方式是：在每次需要重置时可以用一个随机数或者自增的数作为 ```key``` 使用。另外一种解决方案是：导出一个实例方法来重置内部状态：

```jsx
class EmailInput extends Component {
  state = {
    email: this.props.defaultEmail
  };

  resetEmailForNewUser(newEmail) {
    this.setState({ email: newEmail });
  }

  // ...
}
```

The parent form component could then use a ref to call this method. ([Click here to see a demo of this pattern.](https://codesandbox.io/s/l70krvpykl))

在父组件可以通过 ref 执行这个方法。（[点击查看 demo](https://codesandbox.io/s/l70krvpykl)）

Refs can be useful in certain cases like this one, but generally we recommend you use them sparingly. Even in the demo, this imperative method is nonideal because two renders will occur instead of one.

Refs 在某些场景下非常有用，就像这样，但是，通常我们建议慎重使用。在示例中，这种方法也不理想，因为会多出一次渲染。


### Recap

To recap, when designing a component, it is important to decide whether its data will be controlled or uncontrolled.

总而言之，在设计组件时，决定数据是否可控这一点很重要。

Instead of trying to **“mirror” a prop value in state**, make the component **controlled**, and consolidate the two diverging values in the state of some parent component. For example, rather than a child accepting a “committed” ```props.value``` and tracking a “draft” ```state.value```, have the parent manage both ```state.draftValue``` and ```state.committedValue``` and control the child’s value directly. This makes the data flow more explicit and predictable.

不要试图**把属性中的值映射到状态中**，让组件**可控**，并且在父组件中整合两个值。例如，在父组件中直接控制管理 ```state.draftValue``` 和 ```state.committedValue``` 的值，而不是让子组件接受一个 “committed” ```props.value``` 和跟踪一个 “draft” ```state.value```。这让数据流看起来更清晰和可预测。

For **uncontrolled** components, if you’re trying to reset state when a particular prop (usually an ID) changes, you have a few options:

- **Recomendation: To reset all internal state, use the key attribute.**
- Alternative 1: To reset *only certain state fields*, watch for changes in a special property (e.g. ```props.userID```).
- Alternative 2: You can also consider fall back to an imperative instance method using refs.

对于**不可控**组件，如果，当属性中的部分值（通常是 ID）改变时，你尝试重置状态，你还需要一些设置：

- **建议：用属性 key, 重置所有的内部状态**
- 替代方案1: 通过监听特定属性的变化（比如：```props.userID```），*仅重置部分状态*
- 替代方案2: 可以考虑使用 refs 执行回调方法的方式。

### What about memoization?

We’ve also seen derived state used to ensure an expensive value used in ```render``` is recomputed only when the inputs change. This technique is known as [memoization](https://en.wikipedia.org/wiki/Memoization).

我们已经看到，当 input 改变时需要在 ```render``` 中重新计算，确保派生状态的正确性是多么的昂贵。这种技术被称为[记忆化](https://en.wikipedia.org/wiki/Memoization)。

Using derived state for memoization isn’t necessarily bad, but it’s usually not the best solution. There is inherent complexity in managing derived state, and this complexity increases with each additional property. For example, if we add a second derived field to our component state then our implementation would need to separately track changes to both.

派生状态记忆化并不是那么糟糕，但是，通常不是最好的解决方案。管理派生状态本来就很复杂，随着属性的增加会更加复杂。例如，如果，在组件中有两个派生状态，这样我们就需要分别跟踪两者。

Let’s look at an example of one component that takes one prop—a list of items—and renders the items that match a search query entered by the user. We could use derived state to store the filtered list:

我们来看一个实例：组件有一个属性，是一组列表数据，通过用户输入的关键字匹配不同的列表数据来渲染组件。我们可以用派生状态存储过滤后的列表：

```jsx
class Example extends Component {
  state = {
    filterText: "",
  };

  // *******************************************************
  // NOTE: this example is NOT the recommended approach.
  // See the examples below for our recommendations instead.
  // *******************************************************

  // *******************************************************
  // 注意：示例中的操作是不推荐的
  // 可以看接下来的推荐替代方案
  // *******************************************************

  static getDerivedStateFromProps(props, state) {
    // Re-run the filter whenever the list array or filter text change.
    // Note we need to store prevPropsList and prevFilterText to detect changes.
    if (
      props.list !== state.prevPropsList ||
      state.prevFilterText !== state.filterText
    ) {
      return {
        prevPropsList: props.list,
        prevFilterText: state.filterText,
        filteredList: props.list.filter(item => item.text.includes(state.filterText))
      };
    }
    return null;
  }

  handleChange = event => {
    this.setState({ filterText: event.target.value });
  };

  render() {
    return (
      <Fragment>
        <input onChange={this.handleChange} value={this.state.filterText} />
        <ul>{this.state.filteredList.map(item => <li key={item.id}>{item.text}</li>)}</ul>
      </Fragment>
    );
  }
}
```

This implementation avoids recalculating ```filteredList``` more often than necessary. But it is more complicated than it needs to be, because it has to separately track and detect changes in both props and state in order to properly update the filtered list. In this example, we could simplify things by using ```PureComponent``` and moving the filter operation into the render method:

这种方式避免了 ```filteredList``` 不必要的计算。但是，还是会很复杂，为了更新过滤后的列表，我们不得不分别跟踪检测属性和状态的变化。这种实例，我们可以用更简单的 ```PureComponent```，并在 ```render``` 方法对列表进行过滤操作：

```jsx
// PureComponents only rerender if at least one state or prop value changes.
// Change is determined by doing a shallow comparison of state and prop keys.
class Example extends PureComponent {
  // State only needs to hold the current filter text value:
  state = {
    filterText: ""
  };

  handleChange = event => {
    this.setState({ filterText: event.target.value });
  };

  render() {
    // The render method on this PureComponent is called only if
    // props.list or state.filterText has changed.
    const filteredList = this.props.list.filter(
      item => item.text.includes(this.state.filterText)
    )

    return (
      <Fragment>
        <input onChange={this.handleChange} value={this.state.filterText} />
        <ul>{filteredList.map(item => <li key={item.id}>{item.text}</li>)}</ul>
      </Fragment>
    );
  }
}
```

The above approach is much cleaner and simpler than the derived state version. Occasionally, this won’t be good enough—filtering may be slow for large lists, and ```PureComponent``` won’t prevent rerenders if another prop were to change. To address both of these concerns, we could add a memoization helper to avoid unnecessarily re-filtering our list:

上面实现相比派生状态的版本更加清晰和简单。有时，也不是足够好，如果过滤大列表会慢，并且，如果另外的属性变化，```PureComponent``` 不会阻止重新渲染。解决这些问题，我们可以添加一个记忆化的辅助方法避免不必要的重新过滤：

```jsx
import memoize from "memoize-one";

class Example extends Component {
  // State only needs to hold the current filter text value:
  state = { filterText: "" };

  // Re-run the filter whenever the list array or filter text changes:
  filter = memoize(
    (list, filterText) => list.filter(item => item.text.includes(filterText))
  );

  handleChange = event => {
    this.setState({ filterText: event.target.value });
  };

  render() {
    // Calculate the latest filtered list. If these arguments haven't changed
    // since the last render, `memoize-one` will reuse the last return value.
    const filteredList = this.filter(this.props.list, this.state.filterText);

    return (
      <Fragment>
        <input onChange={this.handleChange} value={this.state.filterText} />
        <ul>{filteredList.map(item => <li key={item.id}>{item.text}</li>)}</ul>
      </Fragment>
    );
  }
}
```

This is much simpler and performs just as well as the derived state version!

这样会更加简单并且性能也会比派生状态版本好！

When using memoization, remember a couple of constraints:

1. In most cases, you’ll want to **attach the memoized function to a component instance**. This prevents multiple instances of a component from resetting each other’s memoized keys.

2. Typically you’ll want to use a memoization helper with a **limited cache size** in order to prevent memory leaks over time. (In the example above, we used ```memoize-one``` because it only caches the most recent arguments and result.)

3. None of the implementations shown in this section will work if ```props.list``` is recreated each time the parent component renders. But in most cases, this setup is appropriate.

使用记忆化时，记住这些限制：

1. 大多情况下，你需要将 **memoized 方法附加到组件的一个实例上**。这可以避免组件中多个示例出现冲突。

2. 通常你需要给记忆化方法**有限的缓存大小**，为了避免内存泄露。（上面的示例中，我们用 ```memoize-one```，是因为它只会存储最新的参数和结果。）

3. 对于父组件每次渲染 ```props.list``` 都会被重建，在这篇文章中的方案都无效。大多情况，这种设置是合适的。


### In closing

In real world applications, components often contain a mix of controlled and uncontrolled behaviors. This is okay! If each value has a clear source of truth, you can avoid the anti-patterns mentioned above.

在真实的应用中，组件中经常会参杂着可控和不可控行为。这是正常的！如果，每个值都有一个清晰的真实来源，你就会避免上面提到的反模式。

It is also worth re-iterating that ```getDerivedStateFromProps``` (and derived state in general) is an advanced feature and should be used sparingly because of this complexity. If your use case falls outside of these patterns, please share it with us on [GitHub](https://github.com/reactjs/reactjs.org/issues/new) or [Twitter](https://twitter.com/reactjs)!

很有必要重新审视下 ```getDerivedStateFromProps``` （或者是派生状态），这是一个高级功能，它非常复杂，应该谨慎使用。如果，你在其它模式也遇到了问题，请通过 [GitHub](https://github.com/reactjs/reactjs.org/issues/new) 或者 [Twitter](https://twitter.com/reactjs) 跟我们联系！