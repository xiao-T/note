# React hooks: not magic, just arrays
# React hooks：并不神奇，就是数组

> 原文地址: <https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e/>   
**本文版权归原作者所有，翻译仅用于学习。**

**Untangling the rules around the proposal using diagrams**

**使用图解的方式揭开规范中的使用规则**

I am a huge fan of the new hooks API. However, it has some [odd constraints](https://reactjs.org/docs/hooks-rules.html) about how you need to use it. Here I present a model for how to think about using the new API for those that are struggling to understand the reasons for those rules.

我是新 API Hooks 的粉丝。然而，使用它有一些奇怪的约束。在这，我为那些想使用新 API，但是，又不理解规则的人，展示一个模型以便于更好的理解规则。

![img](https://miro.medium.com/max/5000/1*TharLROVH7aX2MotovHOBQ.jpeg)

## Unpacking how hooks work

## 了解 hooks 的工作模式

I have heard some people struggling with the ‘magic’ around the new hooks API draft proposal so I thought I would attempt to unpack how the syntax proposal works at least on a superficial level.

我听到有些人对于那些 hooks API 的规范难以理解，因此，在表面上，我可以尝试揭示规范中的新语法是如何工作的。

### The rules of hooks

### Hooks 的规则

There are two main usage rules the React core team stipulates you need to follow to use hooks which they outline in the [hooks proposal documentation](https://reactjs.org/docs/hooks-rules.html).

- **Don’t call Hooks inside loops, conditions, or nested functions**
- **Only Call Hooks from React Functions**

有两条使用规则是 React 核心团队明确规定的，在使用 hooks 时，你应该按照[文档](https://reactjs.org/docs/hooks-rules.html)的约束来做。

- **不要在循环内部、条件语句和嵌套方法中调用 Hooks**
- **只在函数组件中调用 Hooks**

The latter I think is self evident. To attach behaviour to a functional component you need to be able to associate that behaviour with the component somehow.

第二条不言而喻。为了函数组件有更强大的功能，你需要一些方法来实现。

The former however I think can be confusing as it might seem unnatural to program using an API like this and that is what I want to explore today.

然而，对于第一条，有点混乱，这有点不符合常规。这也是我想要解释的地方。

### State management in hooks is all about arrays

### Hooks 通过数组管理 State

To get a clearer mental model, let’s have a look at what a simple implementation of the hooks API might look like.

为了得到一个清晰明确的模型，我们来实现一个简单版本的 Hooks API。

***Please note this is speculation and only one possible way of implementing the API to show how you might want to think about it. This is not necessarily how the API works internally.***

***请注意：这只是一种猜测，为了演示我是如何理解的 Hooks，唯一一种实现方式。这并不是 Hooks 内部真正的实现方式。***

### How could we implement `useState()` ?

### 如何实现 ```useState()```？

Let’s unpack an example here to demonstrate how an implementation of the state hook might work.

我们通过一个演示，来解释 hook 是如何工作的。

First let’s start with a component:

首先，从一个组件开始：

```jsx
function RenderFunctionComponent() {
  const [firstName, setFirstName] = useState("Rudi");
  const [lastName, setLastName] = useState("Yardley");

  return (
    <Button onClick={() => setFirstName("Fred")}>Fred</Button>
  );
}
```

The idea behind the hooks API is that you can use a setter function returned as the second array item from the hook function and that setter will control state which is managed by the hook.

Hooks API 背后的理念是：你可以通过 hook 函数返回一个 setter 方法，然后，这个 setter 可以控制 hook 返回的 state。

## So what’s React going to do with this?

### React 如何处理的呢？

Let’s annotate how this might work internally within React. The following would work within the execution context for rendering a particular component. That means that the data stored here lives one level outside of the component being rendered. This state is not shared with other components but it is maintained in a scope that is accessible to subsequent rendering of the specific component.

让我们来一一解释 React 内部是如何工作的。以下的内容将会在特定组件的上下文环境中执行。也就是说，在组件被渲染后数据存储在组件外面一层。这些数据并不会和其他组件共享，但是，在后续的渲染中可以被特定的组件的访问。

### 1) Initialisation

### 1）初始化

Create two empty arrays: `setters` and `state`

创建两个空数组：```setters```  和 ```state```

Set a cursor to 0

设置指针为 0

![](https://miro.medium.com/max/1280/1*LAZDuAEm7nbcx0vWVKJJ2w.png)

### 2) First render

### 2）第一次渲染

Run the component function for the first time.

执行函数实现组件的第一次渲染。

Each `useState()` call, when first run, pushes a setter function (bound to a cursor position) onto the `setters` array and then pushes some state on to the `state` array.

第一次渲染时，每次执行 ```useState()```，添加一个 setter 方法（绑定指针的位置）到 ```setters``` 数组中，然后，添加相应的 state 到 ```setate``` 数组中。

![](https://miro.medium.com/max/1260/1*8TpWnrL-Jqh7PymLWKXbWg.png)

### 3) Subsequent render

### 3）后续渲染

Each subsequent render the cursor is reset and those values are just read from each array.

后续的每次渲染指针都会被重置，然后读取相应的数据。

![](https://miro.medium.com/max/1254/1*qtwvPWj-K3PkLQ6SzE2u8w.png)

### 4) Event handling

### 4）事件处理

Each setter has a reference to its cursor position so by triggering the call to any `setter` it will change the state value at that position in the state array.

每个 setter 对应指针的位置都会有一个引用，因此，任何 ```setter``` 的调用都会触发 state 数组中相应位置数据的变化。

![](https://miro.medium.com/max/1260/1*3L8YJnn5eV5ev1FuN6rKSQ.png)

### And the naive implementation

### 最后简易版本的实现

Following here is a naive code example to demonstrate that implementation.

接下来的代码只是演示如何实现的。

**Note: This is not representative of how hooks work at all under the hood but it should give you an idea of a good way to think about how hooks work for a single component. That is why we are using module level vars etc.**

**注意：这并不代表所有的 hooks 都是这么实现的，但是，这可以给你一个很好的启发，可以更好的理解 hooks 如何在组件中工作的。That is why we are using module level vars etc.**

```jsx
let state = [];
let setters = [];
let firstRun = true;
let cursor = 0;

function createSetter(cursor) {
  return function setterWithCursor(newVal) {
    state[cursor] = newVal;
  };
}

// This is the pseudocode for the useState helper
export function useState(initVal) {
  if (firstRun) {
    state.push(initVal);
    setters.push(createSetter(cursor));
    firstRun = false;
  }

  const setter = setters[cursor];
  const value = state[cursor];

  cursor++;
  return [value, setter];
}

// Our component code that uses hooks
function RenderFunctionComponent() {
  const [firstName, setFirstName] = useState("Rudi"); // cursor: 0
  const [lastName, setLastName] = useState("Yardley"); // cursor: 1

  return (
    <div>
      <Button onClick={() => setFirstName("Richard")}>Richard</Button>
      <Button onClick={() => setFirstName("Fred")}>Fred</Button>
    </div>
  );
}

// This is sort of simulating Reacts rendering cycle
function MyComponent() {
  cursor = 0; // resetting the cursor
  return <RenderFunctionComponent />; // render
}

console.log(state); // Pre-render: []
MyComponent();
console.log(state); // First-render: ['Rudi', 'Yardley']
MyComponent();
console.log(state); // Subsequent-render: ['Rudi', 'Yardley']

// click the 'Fred' button
```

## Why order is important
## 为什么顺序非常重要
Now what happens if we change the order of the hooks for a render cycle based on some external factor or even component state?

现在，如果我们根据一些外部的数据或者组件 state 改变组件中 hooks 的顺序会发生什么呢？

Let’s do the thing the React team say you should not do:

我们来做一些 React 团队不允许做的事情：

```jsx
let firstRender = true;

function RenderFunctionComponent() {
  let initName;
  
  if(firstRender){
    [initName] = useState("Rudi");
    firstRender = false;
  }
  const [firstName, setFirstName] = useState(initName);
  const [lastName, setLastName] = useState("Yardley");

  return (
    <Button onClick={() => setFirstName("Fred")}>Fred</Button>
  );
}
```

Here we have a `useState` call in a conditional. Let’s see the havoc this creates on the system.

上面有个 ```useState``` 在条件语句中执行。我们来看看这会给系统带来什么问题。

### Bad Component First Render

### 不良组件的第一次渲染

![](https://miro.medium.com/max/1270/1*C4IA_Y7v6eoptZTBspRszQ.png)

At this point our instance vars `firstName` and `lastName` contain the correct data but let’s have a look what happens on the second render.

这个时候，我们的变量 `firstName` 和 `lastName` 都有正确的数据，但是，让我们看看在第二次渲染时会发生什么。

### Bad Component Second Render

### 不良组件的二次渲染

![](https://miro.medium.com/max/1274/1*aK7jIm6oOeHJqgWnNXt8Ig.png)

Now both `firstName` and `lastName` are set to `“Rudi”` as our state storage becomes inconsistent. This is clearly erroneous and doesn’t work but it gives us an idea of why the rules for hooks are laid out the way they are.

现在，`firstName` and `lastName` 都被设置成了 `“Rudi”`，这就让 state 变的不一致了。这明显是个错误，也不能工作。但是，正好可以让我们理解为什么 React Hooks 会存在那些规则了。

> The React team are stipulating the usage rules because not following them will lead to inconsistent data
>
> React 团队之所以明确使用规则，这是因为如果不遵守规则会导致数据的不一致



### Think about hooks manipulating a set of arrays and you wont break the rules

### 想象一下 hooks 是通过数组维护的，你就不会打破规则了

So now it should be clear as to why you cannot call `use` hooks within conditionals or loops. Because we are dealing with a cursor pointing to a set of arrays, if you change the order of the calls within render, the cursor will not match up to the data and your use calls will not point to the correct data or handlers.

现在，应该清楚的知道了为什么不能在条件语句或者循环中调用 hooks 了。因为，我们通过数组指针来维护 ```setter``` 和 ```state``` 的对应关系，如果，在渲染时改变了调用顺序，指针就不能正确的匹配 ```setter``` 和 ```state```。

So the trick is to think about hooks managing its business as a set of arrays that need a consistent cursor. If you do this everything should work.

因此，我们需要关心的是：将管理业务的 Hooks 视为一组指针无变化的固定数组。如果，你这么做了，一切将会正常。

## Conclusion
## 总结

Hopefully I have laid out a clearer mental model for how to think about what is going on under the hood with the new hooks API. Remember the true value here is being able to group concerns together so being careful about order and using the hooks API will have a high payoff.

希望我已经整理出了一个更加清晰明确的模型，可以更好的理解新 Hooks API 底层是如何工作的。记住真正的值是关联在一起的数组，因此要多注意它们之间的顺序，使用 hooks API 将会得到更高的好处。

Hooks is an effective plugin API for React Components. There is a reason why people are excited about this and if you think about this kind of model where state exists as a set of arrays then you should not find yourselves breaking the rules around their usage.

Hooks 是 React 组件有效的 API。人们喜欢它是有原因的，如果，你打算用这类模型存储 state，那么你就不要违反使用规则。

