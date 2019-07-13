

# React Higher-Order Components in TypeScript

##  TypeScript 中使用 React HOC

> 原文地址: <https://medium.com/@jrwebdev/react-higher-order-component-patterns-in-typescript-42278f7590fb>   
**本文版权归原作者所有，翻译仅用于学习。**

---

![](https://miro.medium.com/max/2000/1*tw5htcuJ-VHTpDvNIfgBMw.png)

> *Update (May 2018)— Rewritten with new, simplified examples that work with TypeScript 2.8.*
>
> *Update (February 2019) — Updated to address issue with TypeScript 3.2 and above and to add note regarding React Hooks.*
>
> *2018年5月更新：用 TypeScript 2.8 重写了更新更简单的实例。*
>
> *2019年2月更新：更新 TypeScript 3.2 和以上版本的问题，并添加关于 React Hooks 的提示。*

# **Update**

In React v16.8.0, React Hooks were released, a first-class solution that solves the majority of use cases of higher-order components, and are significantly simpler to set types for. I would therefore strongly recommend using Hooks wherever possible over higher-order components. For more information on using Hooks with Typescript, see [React Hooks in TypeScript](https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d).

React v16.8.0 中发布了 React Hooks，它是解决高阶组件中大部分问题首选方案，为它设置 types 也相当简单。因此，我强烈推荐在高阶组件中尽可能的使用 React Hooks。有关 TypeScript 中使用 React Hooks 的更多信息，可以查看[TypeScript 中使用 React Hooks](https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d)。

---

Higher-order components (HOCs) in React are a powerful tool for code reuse between components. However, a common complaint of developers using TypeScript is that they are difficult to set types for.

React 中的高阶组件(HOC)对组件间的代码复用非常有用。然而，开发者常常会抱怨很难用 TypeScript 去设置它们的类型。

This article assumes [knowledge of HOCs](https://reactjs.org/docs/higher-order-components.html), and will go through examples of increasing complexity, showing how they can be typed without turning to ```any```. For the purposes of this article, higher order components will be divided into two basic patterns, which we’ll name *enhancers* and *injectors:*

- *Enhancers:* Wrap a component with additional functionality/props.

- *Injectors:* Inject props into a component.

   

这篇文章基于[HOC 的相关知识](https://reactjs.org/docs/higher-order-components.html)，并通过增加示例的复杂度来展示如何设置它们的类型，而不是设置 ```any```。在这边文章中，高阶组件会被分为两类，它们分别是*组件增强*和*组件注入*：

- *组件增强*：用额外的功能/属性包装组件。
- *组件注入*：把属性注入到组件内。

A higher-order component can fall into one or both of these categories, as will be demonstrated in the coming examples.

一个高阶组件可能包含其中一种或者两种情况，接下来我们将会一一演示。

*Note that the examples in this article do not demonstrate best practices, such as adding a* [*display name*](https://reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging) *and* [*hoisting statics*](https://reactjs.org/docs/higher-order-components.html#static-methods-must-be-copied-over)*; the main aim of this article is to show how HOCs can be typed effectively.*

*请注意文章中的演示并不是最佳实践，比如，添加并[显示组件名称](https://reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)和[提取静态方法](https://reactjs.org/docs/higher-order-components.html#static-methods-must-be-copied-over)；文章主要的目的是为了展示 HOC 如何设置类型。*

----

# Enhancers 增强

We will start with *enhancers* as these are the easiest to set types for. A basic example of this pattern is a HOC that adds ```loading``` prop to a component and displays a loading spinner if set to true. Here is an example without types:

我们先以 *enhancers* 开始，它们最容易设置类型。这种模式最基本的就是通过 HOC 为组件添加一个 ```loading``` 属性，然后，如果是 true 就显示 loading 的片段。这里是没有设置类型的演示：

```jsx
const withLoading = Component =>
  class WithLoading extends React.Component {
    render() {
      const { loading, ...props } = this.props;
      return loading ? <LoadingSpinner /> : <Component {...props} />;
    }
  };
```

… and with types:

… 这是有设置类型的：

```tsx
interface WithLoadingProps {
  loading: boolean;
}

const withLoading = <P extends object>(Component: React.ComponentType<P>) =>
  class WithLoading extends React.Component<P & WithLoadingProps> {
    render() {
      const { loading, ...props } = this.props;
      return loading ? <LoadingSpinner /> : <Component {...props as P} />;
    }
  };
```

There are a few things going on here, so we will break it down:

这里很一些事情，我们会一一来解释：

```ts
interface WithLoadingProps {
  loading: boolean;
}
```

Here an interface is declared with the types of the props that will be added to (or *enhance*) the component when it is wrapped.

这里我们定义了一个接口，设置了一个类型，然后组件被包装时这个属性就会被添加（或者*增强*）到组件上。

```tsx
<P extends object>(Component: React.ComponentType<P>)
```

Here we are using a [generic](https://www.typescriptlang.org/docs/handbook/generics.html); ```P``` represents the props of the component that is passed into the HOC. ```React.ComponentType<P>``` is an alias for ```React.FunctionComponent<P> | React.ClassComponent<P>```, meaning the component that is passed into the HOC can be either a function component or class component.

这里我们用到[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)；```P``` 代表组件的属性，它是通过 HOC 传递的。```React.ComponentType<P>``` 是 ```React.FunctionComponent<P> | React.ClassComponent<P>``` 的别名，代表传递给 HOC 的组件可以是功能组件，也可以是类组件。

```tsx
class WithLoading extends React.Component<P & WithLoadingProps>
```

Here we are defining a component to return from the HOC, and specifying that the component will include the passed in component’s props (```P```) and the HOC’s props (```WithLoadingProps```). These are combined via a [type intersection](http://www.typescriptlang.org/docs/handbook/advanced-types.html) operator (```&```).

我们定义了一个组件并通过 HOC 返回它，并指定组件包含属性(```P```)和 HOC 的属性 (```WithLoadingProps```)。这些都是通过[类型合并](http://www.typescriptlang.org/docs/handbook/advanced-types.html)操作符 (```&```)实现的。

```js
const { loading, ...props } = this.props;
```

*Note: in older versions of TypeScript you may need to cast ```this.props```—```this.props as WithLoadingProps``` to avoid a compilation error “Rest types may only be created from object types.”*

*注意：在旧版本的 TypeScript 你需要这么处理：```this.props```—```this.props as WithLoadingProps```，避免编译报错 "Rest types may only be created from object types."*

Finally, we use the ```loading``` prop to conditionally display a loading spinner or our component with its own props passed in:

最后，我们通过组件传递的属性 ```loading``` 来决定显示 loading 片段还是组件本身。

```tsx
return loading ? <LoadingSpinner /> : <Component {...props as P} />;
```

*Note: A type cast (*```props as P```*) is required here from TypeScript v3.2 onwards, due to [a likely bug in TypeScript.](https://github.com/Microsoft/TypeScript/issues/28938)*

*注意：由于[TypeScript bug](https://github.com/Microsoft/TypeScript/issues/28938)的原因，从 TypeScript v3.2 开始就需要类型断言(```props as P```)。*

Our ```withLoading``` HOC can also be rewritten to return a function component rather than a class:

HOC ```withLoading``` 也可以重写返回一个函数组件，而不是类组件：

```tsx
const withLoading = <P extends object>(
  Component: React.ComponentType<P>
): React.FC<P & WithLoadingProps> => ({
  loading,
  ...props
}: WithLoadingProps) =>
  loading ? <LoadingSpinner /> : <Component {...props as P} />;
```

Here, we have the same problem with object rest/spread, so it’s being worked around by setting an explicit return type ```React.FC<P & WithLoadingProps>```, but only using ```WithLoadingProps``` within the stateless functional component.

现在，通过 rest/spread 处理对象的时候也会有同样的问题，因此，通过显式的设置类型为 ```React.FC<P & WithLoadingProps>``` 来解决问题，但是，```WithLoadingProps``` 只有在函数组件中才有效。

*Note:* ```React.FC``` is shorthand for ```React.FunctionComponent```*. In earlier versions of ```@types/react``` this was ```React.SFC``` or ```React.StatelessFunctionalComponent```.*

*注意：```React.FC``` 是 ```React.FunctionComponent``` 的简称。```@types/react``` 的早期版本中分别是 ```React.SFC``` 或者 ```React.StatelessFunctionalComponent```。*

# Injectors 注入

*Injectors* are the more common form of HOC, but more difficult to set types for. Besides injecting props into a component, in most cases they also remove the injected props when it is wrapped so they can no longer be set from the outside. react-redux’s ```connect``` is an example of an *injector* HOC, but in this article we will use a simpler example — a HOC that injects a counter value and callbacks to increment and decrement the value:

*注入*在 HOC 比较常见，但是，设置类型就会跟加困难。与注入属性到组件中相比，大部分情况下，当组件被包裹时，我们需要移除注入的属性，这时就不需要在外部设置类型了。react-redux’s ```connect``` 就是一个*注入* HOC，但是这篇文章中我们会用一个更简单的示例 — HOC 注入一个计数器，然后通过回调增加和较少它的值：

```tsx
import { Subtract } from 'utility-types';

export interface InjectedCounterProps {
  value: number;
  onIncrement(): void;
  onDecrement(): void;
}

interface MakeCounterState {
  value: number;
}

const makeCounter = <P extends InjectedCounterProps>(
  Component: React.ComponentType<P>
) =>
  class MakeCounter extends React.Component<
    Subtract<P, InjectedCounterProps>,
    MakeCounterState
  > {
    state: MakeCounterState = {
      value: 0,
    };

    increment = () => {
      this.setState(prevState => ({
        value: prevState.value + 1,
      }));
    };

    decrement = () => {
      this.setState(prevState => ({
        value: prevState.value - 1,
      }));
    };

    render() {
      return (
        <Component
          {...this.props as P}
          value={this.state.value}
          onIncrement={this.increment}
          onDecrement={this.decrement}
        />
      );
    }
  };
```

There are a few key differences here:

有几个关键点不同：

```ts
export interface InjectedCounterProps {  
  value: number;  
  onIncrement(): void;  
  onDecrement(): void;
}
```

An interface is being declared for the props that will be injected into the component; it is exported so they can be used by the component that the HOC wraps:

我们为需要注入的组件中的属性定义了接口类型；因为在组件被 HOC 包装的时候需要用到这个接口，所以我们也把接口导出了：

```tsx
import makeCounter, { InjectedCounterProps } from './makeCounter';

interface CounterProps extends InjectedCounterProps {
  style?: React.CSSProperties;
}

const Counter = (props: CounterProps) => (
  <div style={props.style}>
    <button onClick={props.onDecrement}> - </button>
    {props.value}
    <button onClick={props.onIncrement}> + </button>
  </div>
);

export default makeCounter(Counter);
```

```tsx
<P extends InjectedCounterProps>(Component: React.ComponentType<P>)
```

Again we use a generic, but this time it ensures that the component passed into the HOC includes the props that are going to be injected by it; if it doesn’t you will receive a compilation error.

我们再次用到了泛型，但是，这次为了确保传递给 HOC 的组件属性中包含了接口定义的类型；如果没有传递相关类型，就会收到变异报错。

```tsx
class MakeCounter extends React.Component<
  Subtract<P, InjectedCounterProps>,    
  MakeCounterState  
>
```

The component returned by the HOC uses ```Subtract``` from [Piotrek Witek](https://medium.com/u/960d9fbe7faf)’s [utility-types](https://github.com/piotrwitek/utility-types) package, which will subtract the injected props from the passed in component’s props, meaning that if they are set on the resulting wrapped component you will receive a compilation error:

组件由 HOC 通过 ```Subtract``` 处理后返回，```Subtract``` 是 [Piotrek Witek](https://medium.com/u/960d9fbe7faf) 维护的 [utility-types](https://github.com/piotrwitek/utility-types) 库中 API，它将会把注入属性接口中的属性从传递给组件的属性剔除，也就是说：如果，你给包装的组件设置了属性就会收到编译报错：

![](https://miro.medium.com/max/902/1*xTKe3DWJdC7nAVQnM4bvbg.png)

In most cases, this is the behaviour you would want from an injector, though this will be discussed later in the article.

大多情况下，这种行为就是通过注入得到的，稍后我们会讨论一下。

# Enhance + Inject 增强 + 注入

Combining the two patterns, we will build on the counter example to allow for the minimum and maximum counter values to be passed into the HOC, which in turn are intercepted and used by it without passing them through to the component:

合并两种模式，我们将会在计数器的基础重新构建，计数器的最大值和最小值会通过属性传递到 HOC，它们会被 HOC 拦截，而不是传递给被包装的组件：

```tsx
export interface InjectedCounterProps {
  value: number;
  onIncrement(): void;
  onDecrement(): void;
}

interface MakeCounterProps {
  minValue?: number;
  maxValue?: number;
}

interface MakeCounterState {
  value: number;
}

const makeCounter = <P extends InjectedCounterProps>(
  Component: React.ComponentType<P>
) =>
  class MakeCounter extends React.Component<
    Subtract<P, InjectedCounterProps> & MakeCounterProps,
    MakeCounterState
  > {
    state: MakeCounterState = {
      value: 0,
    };

    increment = () => {
      this.setState(prevState => ({
        value:
          prevState.value === this.props.maxValue
            ? prevState.value
            : prevState.value + 1,
      }));
    };

    decrement = () => {
      this.setState(prevState => ({
        value:
          prevState.value === this.props.minValue
            ? prevState.value
            : prevState.value - 1,
      }));
    };

    render() {
      const { minValue, maxValue, ...props } = this.props;
      return (
        <Component
          {...props as P}
          value={this.state.value}
          onIncrement={this.increment}
          onDecrement={this.decrement}
        />
      );
    }
  };
```

Here, ```Subtract``` is being combined with type intersection to combine the component’s own props with the HOCs own props, minus the props that are injected into the component:

这次，```Subtract``` 被用来合并组件自身的属性和HOC的属性，然后并把注入到组件中的属性剔除：

```tsx
Subtract<P, InjectedCounterProps> & MakeCounterProps
```

Other than that, there are no real differences over the other two patterns to highlight, but the example does bring to the fore a number of issues with higher-order components in general. These aren’t really TypeScript-specific, but are worth detailing so that we can talk about how to address them with TypeScript.

除此之外，这跟之前的两种模式并没什么不同之处，但是，这个示例也暴露一些高阶组件的问题。这些并不是真正 TypeScript 特有的问题，但是，还是值得详细说明：我们如何用 TypeScript 解决它们。

Firstly, ```minValue``` and ```maxValue``` are intercepted by the HOC and not passed through to the component. However, you may want them to be so you can disable the increment/decrement buttons based on the values, or display a message to the user. If you wrote the HOC you could simply modify it to inject these values too, though if you didn’t (e.g. it is from an npm package), then this is an issue.

首先，```minVale```  和 ```maxValue``` 会被 HOC 拦截，并不会传递给组件。然后，你希望它们可以被传递下去，这样就可以基于它们的值来禁用 increment/decrement 按钮，或者向用户显示一条信息。如果，是自己写的 HOC 可以简单的修改注入它们的值，可是，如果不是自己写的 HOC，这就是个问题了。

Secondly, the ```value``` prop that is being injected by the HOC has a very generic name; if you want to use this for other purposes, or if you are injecting props from multiple HOCs, this name may conflict with other injected props. You can change the name to be something less generic as a solution, but far as solutions go, this is not a very good one!

其次，通过 HOC 注入的属性 ```value``` 名字太过通用；如果，你想用于其他目的，或者你通过多个 HOC 注入属性，这样就会引起冲突。你可以更改属性名称来解决问题，但是，就目前为止，这并不是一个很好的方案！

In the next article, we will explore how to address these issues by using render props:

[React Render Props in TypeScript](https://medium.com/@jrwebdev/react-render-props-in-typescript-b561b00bc67c)

下篇文章，我们将会探索如何用 TypeScript 解决 props 的问题：

[TypeScript 中的 React Props](https://medium.com/@jrwebdev/react-render-props-in-typescript-b561b00bc67c)