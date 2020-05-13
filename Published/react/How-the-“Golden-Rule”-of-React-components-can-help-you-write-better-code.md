# How the “Golden Rule” of React components can help you write better code

## React 组件的"黄金法则"

> 原文地址: <https://medium.com/free-code-camp/how-the-golden-rule-of-react-components-can-help-you-write-better-code-127046b478eb/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/2000/1*KzKoXW7PovSAUUn8htYbnw@2x.jpeg)

Recently I’ve adopted a new philosophy that changes the way I make components. It’s not necessarily a new idea but rather a subtle new way of thinking.

最近，我采用了一种新的理念编写 React 组件。它不一定是必要的，但，是一种新的思维方式。

## The Golden Rule of Components

## 组件的黄金法则

> Create and define components in the most natural way, solely considering what they need to function.
>
> 用最自然的方式创建和定义组件，单纯的考虑它需要什么功能。

Again, it’s a subtle statement and you may think you already follow it but it’s easy to go against this.

同样，描述很简短，你或许认为自己已经遵守了相同法则，但是，它很容易让你背道而驰。

For example, let’s say you have the following component:

例如，假设你有以下一个组件：

![](https://miro.medium.com/max/1368/1*nF_5kuYHigZuwdq99vRJ8g.png)

If you were defining this component “naturally” then you’d probably write it with the following API:

如果，你用非常“自然”的方式定义这个组件，这时，应该会写出以下代码：

```jsx
PersonCard.propTypes = {
  name: PropTypes.string.isRequired,
  jobTitle: PropTypes.string.isRequired,
  pictureUrl: PropTypes.string.isRequired,
};
```

Which is pretty straightforward — solely looking at what it needs to function, you just need a name, job title, and picture URL.

非常直截了当，看一下它需要什么功能，你只是需要一个 name，job title 和 picture URL。

But let’s say you have a requirement to show an “official” picture depending on user settings. You might be tempted to write an API like so:

但是，假如说根据用户设置需要显示一张 “official” 图片。你就需要改写成以下的样子：

```jsx
PersonCard.propTypes = {
  name: PropTypes.string.isRequired,
  jobTitle: PropTypes.string.isRequired,
  officialPictureUrl: PropTypes.string.isRequired,
  pictureUrl: PropTypes.string.isRequired,
  preferOfficial: PropTypes.boolean.isRequired,
};

```

It may seem like the component needs those extra props to function, but in actuality, the component doesn’t look any different and doesn’t need those extra props to function. What these extra props do is couple this `preferOfficial` setting with your component and makes any use of the component outside that context feel really unnatural.

看起来组件需要这些额外的属性来完成工作，但是，事实上，组件没什么不同，并且不需要这些额外的属性。这些额外的属性 `preferOfficial` 跟组件耦合在一起，使得组件的上下文感觉并不真正的自然。

## Bridging the gap

## 解耦

So if the logic for switching the picture URL doesn’t belong in the component itself, where does it belong?

如果，切换图片 URL 的逻辑不属于组件本身，那它们应该属于哪呢？

How about an `index` file?

属于 `index` 文件如何？

We’ve adopted a folder structure where every component goes into a self-titled folder where the `index` file is responsible for bridging the gap between your “natural” component and the outside world. We call this file the “container” (inspired from [React Redux’s concept of “container” components](https://redux.js.org/basics/usage-with-react#presentational-and-container-components)).

我会把组件放在一个以自身命名的文件夹中，其中有一个 `index` 文件，它负责把“自然”组件和外面的世界连接起来。我们把这个文件称为 “container”（[概念来自 React Redux 的 “container” 组件](https://redux.js.org/basics/usage-with-react#presentational-and-container-components)）。

```shell
/PersonCard
  -PersonCard.js ------ the "natural" component
  -index.js ----------- the "container"

```

We define **containers** as the piece of code that bridges that gap between your natural component and the outside world. For this reason, we also sometimes call these things “injectors”.

我们把 **containers** 做为连接组件和外部世界的桥梁。因为这个原因，我们有时也会叫做 “injectors”。

Your **natural component** is the code you’d create if you were only shown a picture of what you were required make (without the details of how’d you’d get data or where it’d be placed in the app — all you know is that it should function).

如果，**组件**只是为了展示一张图片，那么图片就是必要的（没有什么任何其它的逻辑，不关心如何获取数据，组件应该被放到什么地方 — 你所需关心就是它的功能）。

The **outside world** is a keyword we’ll use to refer to any resource your app has (e.g. the Redux store) that can be transformed to satisfy your natural component’s props.

**外部世界**只是是一种统称，它代表着 APP 任何的资源（比如：Redux Store），它们可以被转化以便满足组件的需求。

**Goal for this article:** How can we keep components “natural” without polluting them with junk from the outside world? Why is that better?

**这篇文章的目的：**如何保证组件不被外部的垃圾资源污染？为什么这么做会更好？

> **Note:** Though inspired by [Dan’s Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) and [React Redux’s](https://redux.js.org/basics/usage-with-react#presentational-and-container-components) terminology, our definition of “containers” goes slightly beyond that and is subtly different.
>
> **注意：**尽管受到 [Dan’s Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) 和 [React Redux](https://redux.js.org/basics/usage-with-react#presentational-and-container-components) 的启发，我们定义的 “container” 还是有点不同的。
>
> The only difference between [Dan Abramov’s container](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) and ours is only at the conceptual level. Dan’s says there are two kinds of components: presentational components and container components. We take this a step further and say there are components and then containers.
>
> 与 [Dan Abramov’s container](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) 的不同之处只是概念层面上。Dan 说只有两类组件：Presentational 组件和 Container 组件。我们将会更进一步，我们会有组件和 Container。
>
> Even though we implement containers with components, we don’t think of containers as components on a conceptual level. That’s why we recommend putting your container in the `index` file — because it’s a bridge between your natural component and the outside world and doesn’t stand on its own.
>
> 尽管，我用组件实现了 container，我并不认为 container 是概念上的组件。这就是为什么我推荐你把 container 放在 `index` 文件中 — 它是连接组件和外部世界的桥梁，并不能独立存在。

Though this article is focused on components, containers take up the bulk of this article.

尽管本文的关注点是组件，但是，container 占据了大量篇幅。

Why?

为什么？

Making natural components — Easy, fun even.
Connecting your components to the outside world — A bit harder.

创建自然组件 — 简单，甚至有趣。

让你的组件与外部联系起来就有一点难了。

The way I see it, there are three major reasons you’d pollute your natural component with junk from the outside world:

1. Weird data structures
2. Requirements outside of the scope of the component (like the example above)
3. Firing events on updates or on mount

以我看，有 3 种主要的原因会导致外部资源污染自然组件。

1. 糟糕的数据结构
2. 来自组件外部需求（比如上面的演示）
3. 在组件更新或者挂载的时候触发事件

The next few sections will try to cover these situations with examples with different types of container implementations.

接下来的篇幅中将会用不同的示例来演示如何解决上面的问题。

## Working with weird data structures

## 处理糟糕的数据结构

Sometimes in order to render the required information, you need to link together data and transform it into something that’s more sensible. For lack of a better word, “weird” data structures are simply data structures that are unnatural for your component to use.

有时，为了渲染一些必要的信息，你需要合并一些数据，然后做一些转换让数据变得更加有意义。由于缺少更好的表达方式，你的组件就用了这种糟糕的数据结构。

It’s very tempting to pass weird data structures directly into a component and do the transforming inside the component itself, but this leads to confusing and often hard to test components.

直接把数据传递给组件并在组件内部做数据转化，这种方式非常方便，但是，这会导致混乱，而且，非常难以测试组件。

I caught myself falling into this trap recently when I was tasked to create a component that got its data from a particular data structure we use to support a particular type of form.

最近，我创建的一个组件，它从用于支持特定类型表单的特定数据结构中获取数据时，我就掉进了这种陷阱。

![img](https://miro.medium.com/max/1154/1*hFOPWOxkedUEb851jdAXjA.gif)

```jsx
ChipField.propTypes = {
  field: PropTypes.object.isRequired,      // <-- the "weird" data structure
  onEditField: PropTypes.func.isRequired,  // <-- and a weird event too
};
```

The component took in this weird `field` data structure as a prop. In practicality, this might’ve been fine if we never had to touch the thing again, but it became a real issue when we were asked to use it again in a different spot unrelated to this data structure.

组件将 `field` 这种糟糕的数据结构作为 prop。实际上，只是这样也没什么问题，但是，当我们需要把它用在没有相关数据结构的地方时，这就会有问题了。

Since the component required this data structure, it was impossible to reuse it and it was confusing to refactor. The tests we originally wrote also were confusing because they mocked this weird data structure. We had trouble understanding the tests and trouble re-writing them when we eventually refactored.

由于，组件需要这种特定的数据结构，它不可能复用，也难以重构。因为需要 mock 这种数据结构，让我们原本写好的测试用例也变得混乱。最后导致，我们很难理解和重构它。

Unfortunately, weird data structures are unavoidable, but using containers is a great way to deal with them. One takeaway here is that architecting your components in this way gives you the *option* of extracting and graduating the component into a reusable one. If you pass a weird data structure into a component, you lose that option.

很不幸，糟糕的数据结构并不可避免，但是，使用 Container 可以很好的处理它们。这样做有一个好处是：你可以有*选择*的将组件抽象并让组件可以重用。如果，给组件传递一个糟糕的数据结构，就会失去这种好处。

> **Note:** I’m not suggesting that all components you make should be generic from the beginning. The suggestion is to think about what your component does on a fundamental level and then bridge the gap. As a consequence, you’re more likely to have the *option* to graduate your component into a reusable one with minimal work.
>
> **注意：**我不建议从一开始就把所有的组件都设计成通用型的。建议首先要考虑组件的功能，然后，尽可能的解耦。因此，你可以，有*选择*的，以最小的工作让组件进阶成可以复用的组件。

### Implementing containers using function components
### 用函数组件实现 Container

If you’re strictly mapping props, a simple implementation option is to use another function component:

如果，你需要严格映射 props，一种简单的实现是选择函数组件：

```jsx
import React from 'react';
import PropTypes from 'prop-types';

import getValuesFromField from './helpers/getValuesFromField';
import transformValuesToField from './helpers/transformValuesToField';

import ChipField from './ChipField';

export default function ChipFieldContainer({ field, onEditField }) {
  const values = getValuesFromField(field);
  
  function handleOnChange(values) {
    onEditField(transformValuesToField(values));
  }
  
  return <ChipField values={values} onChange={handleOnChange} />;
}

// external props
ChipFieldContainer.propTypes = {
  field: PropTypes.object.isRequired,
  onEditField: PropTypes.func.isRequired,
};
```

And the folder structure for a component like this looks something like:

组件的文件结构看起来是这个样子：

```shell
/ChipField
  -ChipField.js ------------------ the "natural" chip field
  -ChipField.test.js
  -index.js ---------------------- the "container"
  -index.test.js
  /helpers ----------------------- a folder for the helpers/utils
    -getValuesFromField.js
    -getValuesFromField.test.js
    -transformValuesToField.js
    -transformValuesToField.test.js
```

You might be thinking “that’s too much work” — and if you are then I get it. It may seem like there is more work to do here since there are more files and a bit of indirection, but here’s the part you’re missing:

如果，你愿意这么做的话 — 你或许会想“太多东西了”，我也理解。因为，有更多的文件和一些间接的操作，看起来会有更多的事情要做，但是，这正是你缺失的地方：

It’s still the same amount of work regardless if you transformed data outside of the component or inside the component. The difference is, when you transform data outside of the component, you’re giving yourself a more explicit spot to test that your transformations are correct while also separating concerns.

不管，你是在组件外部处理数据转化，还是组件内部，工作量都是一样的。不同的是，当你在组件外部转化数据时，你可以更加明确测试数据是否正确，而且，还可以和其它点分离开。

## Fulfilling requirements outside of the scope of the component

## 超出组件范围的需求

Like the Person Card example above, it’s very likely that when you adopt this “golden rule” of thinking, you’ll realize that certain requirements are outside the scope of the actual component. So how do you fulfill those?

就如上面的 Person Card 组件，当你按照“黄金法则”思考时，你就会意识到有些需求并不是组件应该关心的。因此，应该如何实现呢？

You guessed it: Containers 🎉

你已经猜到了： Containers 🎉

You can create containers that do a little bit of extra work to keep your component natural. When you do this, you end up with a more focused component that is much simpler and a container that is better tested.

你可能需要一些额外的工作创建一个 Container，为了保证组件的独立。当你这么做时，最终，你会得到一个更加简单、更加关注功能的组件，Container 也更加容易测试。

Let’s implement a PersonCard container to illustrate the example.

我们来按照图示实现一个 PersonCard Container

### Implementing containers using higher order components

### 用高阶组件实现 Container

React Redux uses [higher order components](https://reactjs.org/docs/higher-order-components.html) to implement containers that push and map props from the Redux store. Since we got this terminology from React Redux, it comes with no surprise that [React Redux’s ](https://redux.js.org/basics/usage-with-react#implementing-container-components)`connect`[ is a container](https://redux.js.org/basics/usage-with-react#implementing-container-components).

React Redux 就是用[高阶组件](https://reactjs.org/docs/higher-order-components.html)实现了 Container，然后，与 Redux Store 中的数据关联起来。由于，术语来自 React Redux，很自然的就会想到 [React Reudx 中的 `connect` 就是一个 Container](https://redux.js.org/basics/usage-with-react#implementing-container-components)。

Regardless if you’re using a function component to map props, or if you’re using higher order components to connect to the Redux store, the golden rule and the job of the container are still the same. First, write your natural component and then use the higher order component to bridge the gap.

不管，你是用函数组件映射 props，还是，用高阶组件连接 Redux Store，黄金法则和 Container 的作用始终是一样的。首先，编写自然组件，然后，用高阶组件关联数据。

```jsx
import { connect } from 'react-redux';

import getPictureUrl from './helpers/getPictureUrl';

import PersonCard from './PersonCard';

const mapStateToProps = (state, ownProps) => {
  const { person } = ownProps;
  const { name, jobTitle, customPictureUrl, officialPictureUrl } = person;
  const { preferOfficial } = state.settings;
  
  const pictureUrl = getPictureUrl(preferOfficial, customPictureUrl, officialPictureUrl);
  
  return { name, jobTitle, pictureUrl };
};

const mapDispatchToProps = null;

export default connect(
  mapStateToProps,
  mapDispatchToProps,
)(PersonCard);
```

Folder structure for above:

文件结构如下：

```shell
/PersonCard
  -PersonCard.js ----------------- natural component
  -PersonCard.test.js
  -index.js ---------------------- container
  -index.test.js
  /helpers
    -getPictureUrl.js ------------ helper
    -getPictureUrl.test.js
```

> **Note:** In this case, it wouldn’t be too practical to have a helper for `getPictureUrl`. This logic was separated simply to show that you can. You also might’ve noticed that there is no difference in folder structure regardless of container implementation.
>
> **注意：**在这个示例中，把 `getPictureUrl` 独立开来就不太实际。这么做只是为了让你知道，你可以这么做。或许，你已经注意到，无论哪种方式实现 Container 文件的结构并没什么区别。

If you’ve used Redux before, the example above is something you’re probably already familiar with. Again, this golden rule isn’t necessarily a new idea but a subtle new way of thinking.

如果，你之前用过 Redux，上面的演示你应该比较熟悉。黄金法则并不一定是新的主意，但是，它可以是一种全新的思考方式。

Additionally, when you implement containers with higher order components, you also have the ability to functionally compose higher order components together — passing props from one higher order component to the next. Historically, we’ve chained multiple higher order components together to implement a single container.

此外，当你使用高阶组件实现 Container 时，还是可以结合一些功能 — 通过高阶组件向下传递。曾经，我用多个高阶组件实现了一个 Container。

> **2019 Note:** The React community seems to be moving away from higher order components as a pattern.
>
> **2019 提醒：** React 社区已经不推荐高阶组件的设计模式了。
>
> I would also recommend the same. My experience when working with these is that they can be confusing for team members who aren’t familiar with functional composition and they can cause what is known as “wrapper hell” where components are wrapped too many times causing significant performance issues.
>
> 我也赞成少用高阶组件。根据我的经验来看，对于那些不熟悉函数组件的团队成员，他们很容易混乱，并且，由于嵌套太多，会掉进“嵌套黑洞”，从而引起严重的性能问题。
>
> Here are some related articles and resources on this:[Hooks talk](https://youtu.be/dpw9EHDh2bM?t=710) (2018) [Recompose talk](https://youtu.be/zD_judE-bXk?t=1101) (2016) , [Use a Render Prop!](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) (2017), [When to NOT use Render Props](https://blog.kentcdodds.com/when-to-not-use-render-props-5397bbeff746) (2018).
>
> 这里有一些相关的文章和资源：[Hooks talk](https://youtu.be/dpw9EHDh2bM?t=710) (2018) [Recompose talk](https://youtu.be/zD_judE-bXk?t=1101) (2016) , [Use a Render Prop!](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) (2017), [When to NOT use Render Props](https://blog.kentcdodds.com/when-to-not-use-render-props-5397bbeff746) (2018).

## You promised me hooks
## Hooks

### Implementing containers using hooks

### 用 Hooks 实现 Container

Why are hooks featured in this article? Because implementing containers becomes a lot easier with hooks.

为什么是 Hooks？因为，用 Hooks 实现 Container 更加容易。

If you’re not familiar with React hooks, then I would recommend watching [Dan Abramov’s and Ryan Florence’s talks introducing the concept during React Conf 2018](https://youtu.be/dpw9EHDh2bM).

如果，你还不熟悉 React Hooks，我推荐看一看：[Dan Abramov 和 Ryan Florence 在 React Conf 2018 有关 Hooks 的演讲](https://youtu.be/dpw9EHDh2bM)

The gist is that hooks are the React team’s response to the issues with [higher order components](https://reactjs.org/docs/higher-order-components.html) and [similar patterns](https://reactjs.org/docs/render-props.html). React hooks are intended to be a superior replacement pattern for both in most cases.

针对 [高阶组件](https://reactjs.org/docs/higher-order-components.html)和[其它熟悉的模式](https://reactjs.org/docs/render-props.html)中出现的问题，React 团队给出了解决方案：Hooks。对于大部分情况 React Hooks 是绝佳的替代方案。

This means that implementing containers can be done with a function component and hooks 🔥

也就是说用函数组件和 Hooks 实现 Container 会更好 🔥

In the example below, we’re using the hooks `useRoute` and `useRedux` to represent the “outside world” and we’re using the helper `getValues` to map the outside world into `props` usable by your natural component. We’re also using the helper `transformValues` to transform your component’s output to the outside world represented by `dispatch`.

接下来的示例中，我们 `useRouter` 和 `useRedux` 代表着“外部环境”，然后，用 `getValues` 映射外部环境中的数据到 `props`，以便组件使用。同时，我们还用 `transformValues` 把组件的数据传输到外部，在这里 `dispatch` 就代表着外部环境。

```jsx
import React from 'react';
import PropTypes from 'prop-types';

import { useRouter } from 'react-router';
import { useRedux } from 'react-redux';

import actionCreator from 'your-redux-stuff';

import getValues from './helpers/getVaules';
import transformValues from './helpers/transformValues';

import FooComponent from './FooComponent';

export default function FooComponentContainer(props) {
  // hooks
  const { match } = useRouter({ path: /* ... */ });
  // NOTE: `useRedux` does not exist yet and probably won't look like this
  const { state, dispatch } = useRedux();

  // mapping
  const props = getValues(state, match);
  
  function handleChange(e) {
    const transformed = transformValues(e);
    dispatch(actionCreator(transformed));
  }
  
  // natural component
  return <FooComponent {...props} onChange={handleChange} />;
}

FooComponentContainer.propTypes = { /* ... */ };
```

And here’s the reference folder structure:

以下是相关的文件结构：

```shell
/FooComponent ----------- the whole component for others to import
  -FooComponent.js ------ the "natural" part of the component
  -FooComponent.test.js
  -index.js ------------- the "container" that bridges the gap
  -index.js.test.js         and provides dependencies
  /helpers -------------- isolated helpers that you can test easily
    -getValues.js
    -getValues.test.js
    -transformValues.js
    -transformValues.test.js
```

# Firing events in containers

## Container 内触发事件

The last type of scenario where I find myself diverging from a natural component is when I need to fire events related to changing props or mounting components.

当我需要触发改变 props 或者组件 mount 的相关事件时，我发现已经脱离了自然组件，这是最后一种情况。

For example, let’s say you’re tasked with making a dashboard. The design team hands you a mockup of the dashboard and you transform that into a React component. You’re now at the point where you have to populate this dashboard with data.

例如，假设，你有一个制作 dashboard 的任务。设计团队已经提供了相关 UI，然后，你需要制作成一个 React 组件。现在，你必须在 dashboard 中填充数据。

You notice that you need to call a function (e.g. `dispatch(fetchAction)`) when your component mount in order for that to happen.

为了获取数据，需要在组件 mount 时，调用一个函数（比如：`dispatch(fetchAction)`）。

In scenarios like this, I found myself adding `componentDidMount` and `componentDidUpdate` lifecycle methods and adding `onMount` or `onDashboardIdChanged` props because I needed some event to fire in order to link my component to the outside world.

在这种情况下，我发现需要添加 `componentDidMount` 和 `componentDidUpdate` 两个生命周期方法，并且，添加 `onMount` 或者 `onDashboardIdChanged` 相关 props，这是因为，我需要触发一些事件才能让组件与外部环境连接起来。

Following the golden rule, these `onMount` and `onDashboardIdChanged` props are unnatural and therefore should live in the container.

按照黄金法则，这些 `onMount` 和 `onDashboardIdChanged` props 是违反法则的，因此，它们应该放在 Container。

The nice thing about hooks is that it makes dispatching events `onMount` or on prop change much simpler!

可喜的是，Hooks 让触发事件 `onMount` 或者改变 prop 变得非常容易！

### Firing events on mount:

### 组件 mounted 时触发事件

To fire an event on mount, call `useEffect` with an empty array.

调用 `useEffect` 方法，并设置第二个参数为空数组，这样，在组件 mount 时会触发事件。

```jsx
import React, { useEffect } from 'react';
import PropTypes from 'prop-types';
import { useRedux } from 'react-redux';

import fetchSomething_reduxAction from 'your-redux-stuff';
import getValues from './helpers/getVaules';
import FooComponent from './FooComponent';

export default function FooComponentContainer(props) {
  // hooks
  // NOTE: `useRedux` does not exist yet and probably won't look like this
  const { state, dispatch } = useRedux();
  
  // dispatch action onMount
  useEffect(() => {
    dispatch(fetchSomething_reduxAction);
  }, []); // the empty array tells react to only fire on mount
  // https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects

  // mapping
  const props = getValues(state, match);
  
  // natural component
  return <FooComponent {...props} />;
}

FooComponentContainer.propTypes = { /* ... */ };
```

### Firing events on prop changes:

### Props 改变时触发事件：

`useEffect` has the ability to watch your property between re-renders and calls the function you give it when the property changes.

`useEffect` 还可以监听 props 的改变，以便重新触发回调函数。

Before `useEffect` I found myself adding unnatural lifecycle methods and `onPropertyChanged` props because I didn’t have a way to do the property diffing outside the component:

在 `useEffect` 出现之前，因为，在组件外部我无法确定 props 是否改变，我不得不违背黄金法则添加了生命周期方法和 `onPropertyChanged` prop。

```jsx
import React from 'react';
import PropTypes from 'prop-types';

/**
 * Before `useEffect`, I found myself adding "unnatural" props
 * to my components that only fired events when the props diffed.
 *
 * I'd find that the component's `render` didn't even use `id`
 * most of the time
 */
export default class BeforeUseEffect extends React.Component {
  static propTypes = {
    id: PropTypes.string.isRequired,
    onIdChange: PropTypes.func.isRequired,
  };

  componentDidMount() {
    this.props.onIdChange(this.props.id);
  }

  componentDidUpdate(prevProps) {
    if (prevProps.id !== this.props.id) {
      this.props.onIdChange(this.props.id);
    }
  }

  render() {
    return // ...
  }
}
```

Now with `useEffect` there is a very lightweight way to fire on prop changes and our actual component doesn’t have to add props that are unnecessary to its function.

现在，使用 `useEffect` 可以很简单的在 props 改变时重新触发事件，而且，我们的组件也不需要添加那些不必要的函数了。

```jsx
import React, { useEffect } from 'react';
import PropTypes from 'prop-types';
import { useRedux } from 'react-redux';

import fetchSomething_reduxAction from 'your-redux-stuff';
import getValues from './helpers/getVaules';
import FooComponent from './FooComponent';

export default function FooComponentContainer({ id }) {
  // hooks
  // NOTE: `useRedux` does not exist yet and probably won't look like this
  const { state, dispatch } = useRedux();
  
  // dispatch action onMount
  useEffect(() => {
    dispatch(fetchSomething_reduxAction);
  }, [id]); // `useEffect` will watch this `id` prop and fire the effect when it differs
  // https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects

  // mapping
  const props = getValues(state, match);
  
  // natural component
  return <FooComponent {...props} />;
}

FooComponentContainer.propTypes = {
  id: PropTypes.string.isRequired,
};
```

> **Disclaimer:** before `useEffect` there were ways of doing prop diffing inside a container using other higher order components (like [recompose’s lifecycle](https://github.com/acdlite/recompose/blob/3db12ce7121a050b533476958ff3d66ded1c4bb8/docs/API.md#lifecycle)) or creating a lifecycle component like [react router does internally](https://github.com/ReactTraining/react-router/blob/89a72d58ac55b2d8640c25e86d1f1496e4ba8d6c/packages/react-router/modules/Lifecycle.js), but these ways were either confusing to the team or were unconventional.
>
> **免责声明：**在 `useEffect` 之前，我们有多种方式可以在 Container 中对比前后 prop 是否一致，比如：使用高阶组件（[recompose 中的生命周期方法](https://github.com/acdlite/recompose/blob/3db12ce7121a050b533476958ff3d66ded1c4bb8/docs/API.md#lifecycle)）或者像 [react router](https://github.com/ReactTraining/react-router/blob/89a72d58ac55b2d8640c25e86d1f1496e4ba8d6c/packages/react-router/modules/Lifecycle.js) 一样创建一个 Lifecycle 组件，但是，这些方法要么会让团队成员混乱，要么是非常规的。

## What are the benefits here?

## 有什么好处呢？

### Components stay fun

### 保持组件的乐趣

For me, creating components is the most fun and satisfying part of front-end development. You get to turn your team’s ideas and dreams into real experiences and that’s a good feeling I think we all relate to and share.

对于我来说，创建组件是前端开发中最有趣和满足的一部分。你可以把团队的主意和想法变成现实，我认为我们应该学会分享，这种感觉很好。

There will never be a scenario where your component’s API and experience is ruined by the “outside world”. Your component gets to be what you imagined it without extra props — that’s my favorite benefit of this golden rule.

绝对不会出现组件 API 被“外部环境”破坏的情况。你的组件就如你想的那样，没有其它额外的 prop — 这就是黄金法则我最喜欢的地方。

### More opportunities to test and reuse

### 更方便测试和复用

When you adopt an architecture like this, you’re essentially bringing a new data-y layer to the surface. In this “layer” you can switch gears where you’re more concerned about the correctness of data going into your component vs. how your component works.

当你采用这种架构时，实质上你已经把数据独立到另外一个层面上了。在这一层，你只需关心哪些数据可以传递到组件，而不是，组件是如何工作的。

Whether you’re aware of it or not, this layer already exists in your app but it may be coupled with presentational logic. What I’ve found is that when I surface this layer, I can make a lot of code optimizations and reuse a lot of logic that I would’ve otherwise rewritten without knowing the commonalities.

不管你是否关心，这一层已经存在你的应用中，但是，它有可能与一些呈现逻辑融合在一起。当我面对它时，我才发现它是什么，我可以做代码优化和重用一些逻辑，否则，在不知道共性的情况，我就会重写。

I think this will become even more obvious with the addition of [custom hooks](https://reactjs.org/docs/hooks-custom.html). Custom hooks gives us a much simpler way to extract logic and subscribe to external changes — something that a helper function could not do.

我认为，随着[自定义 Hooks](https://reactjs.org/docs/hooks-custom.html) 的加入会变得更加显著。自定义 Hooks 提供了一种更加简单的方式抽象逻辑和订阅外部的改变 —— 这些 helper 函数做不到。

### Maximize team throughput

###提高团队效率 

When working on a team, you can separate the development of containers and components. If you agree on APIs beforehand, you can concurrently work on:

1. Web API (i.e. back-end)
2. Fetching data from the web API (or similar) and transforming the data to the component’s APIs
3. The components

当团队一起工作时，你可以把 Container 和组件分开开发。如果，你们事先就对 API 达成了一致，你们可以同时工作：

1. Web API
2. 通过 API 获取数据和转化成适用组件的 API
3. 组件

## Are there any exceptions?

## 有什么例外的？

Much like the real Golden Rule, this golden rule is also a golden rule of thumb. There are some scenarios where it makes sense to write a seemingly unnatural component API to reduce the complexity of some transformations.

就像真正的黄金法则，这个法则也只是一种建议。有些时候为了减少转化数据的复杂度，编写一些非自然的组件也是有意义的。

A simple example would the names of props. It would make things more complicated if engineers renamed data keys under the argument that it’s more “natural”.

一个简单的例子就是 prop 的名称。如果，开发者根据参数需要改变相应的数据 key，但是，还要保证组件的“自然”，这会变得更加复杂。

It’s definitely possible to take this idea too far where you end up overgeneralizing too soon, and that can also be a trap.

很有可能，这种想法过于抽象，导致掉入另外的陷阱。

## The bottom line
## 最后

More or less, this “golden rule” is simply re-hashing the existing idea of presentational components vs. container components in a new light. If you evaluate what your component needs on a fundamental level then you’ll probably end up with simpler and more readable parts.

或多或少，这个“黄金法则”只是用全新的思路重新的对比了 Presentational 组件和 Container 组件。如果，你以最基本的方式去评估组件应该是什么样子，那么，你应该就会得到更简单、更易阅读的组件。

Thank you!

谢谢你们！