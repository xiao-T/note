## React Render Props in TypeScript
## TypeScript 中使用 Render Props

> 原文地址: <https://medium.com/@jrwebdev/react-render-props-in-typescript-b561b00bc67c/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/2000/1*eP8IkQLUQCgtmvYzBEJPsQ.png)

# Update

In React v16.8.0, React Hooks were released, a first-class solution that covers reusing logic across multiple components and that can be used to replace the uses of render props as described in this article. For more information on using Hooks with Typescript, see [React Hooks in TypeScript](https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d?source=post_page---------------------------).

React v16.8.0 中发布了 React Hooks，一种蕴涵了多组件之间逻辑复用和可以替换这篇文章提到的 Render Props 的解决方案。有关 TypeScript 使用 React Hook 的信息，可以查看 [TypeScript 中使用 React Hook](https://github.com/xiao-T/note/blob/master/Published/React%20Hooks%20in%20TypeScript.md)。

---

As with the previous article, this article assumes knowledge of the subject in question, so for some background on render props, the [official documentation](https://reactjs.org/docs/render-props.html?source=post_page---------------------------) can help. This article will use the [function-as-a-child render prop](https://reactjs.org/docs/render-props.html?source=post_page---------------------------#using-props-other-than-render) pattern to match its usage within React itself for the new context API. If you prefer using another prop, such as `render`, just substitute `children`for your prop in the coming examples.

就像上篇文章一样，还是假设您对主题相关的知识有所了解，因此有关 Render Props 相关背景，[官方文档](https://reactjs.org/docs/render-props.html?source=post_page---------------------------)会有所帮助。本文将会用[以函数的形式做为 prop](https://reactjs.org/docs/render-props.html?source=post_page---------------------------#using-props-other-than-render) 的模式去匹配 React 新的 context API。如果你喜欢用其他的 prop，比如：```render```，只需在接下来的示例中替换掉 ```children```。

---

To demonstrate the render prop pattern, we will be converting the `makeCounter` HOC from the previous article to a render prop component. To recap, here is the HOC version:

为了演示 Render Prop 模式，我们将会把上篇文章中的 ```makeCounter``` HOC 转换成 Render Prop 组件。回顾一下，这是 HOC 的版本：

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

The HOC injects the value and two callback functions (`onIncrement` and `onDecrement`) to a component, in addition to enhancing the component with a `minValue` and `maxValue` prop, which the HOC uses internally and does not pass this through to the component. We discussed how not passing through props may present an issue if the component needs to know these values, and that the naming of the injected `value` prop may also conflict with props injected by other HOCs if wrapping a component with multiple HOCs.

HOC 向组件中注入了 value 和两个回调方法（```onIncrement``` 和 ```onDecrement```），除此之外，为了增强组件还添加了 ```minValue``` 和 ```maxValue``` 属性，它们只会在 HOC 内部使用，并不会传递给组件。我们讨论过如果组件想使用这些值，不传递会有一些问题；还有，如果组件被多个 HOC 包装后，注入的 ```value``` 也会引起命名的冲突。

The `makeCounter` HOC can be rewritten as a render prop component like so:

像这样用 Render Prop 的模式我们可以重写 ```makeCounter``` HOC：

```tsx
interface InjectedCounterProps {
  value: number;
  onIncrement(): void;
  onDecrement(): void;
}

interface MakeCounterProps {
  minValue?: number;
  maxValue?: number;
  children(props: InjectedCounterProps): JSX.Element;
}

interface MakeCounterState {
  value: number;
}

class MakeCounter extends React.Component<MakeCounterProps, MakeCounterState> {
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
    return this.props.children({
      value: this.state.value,
      onIncrement: this.increment,
      onDecrement: this.decrement,
    });
  }
}
```

There are a few changes here to take note of. Firstly, `InjectedCounterProps`is retained, as we want an interface to define the props that the render prop function will be called with rather than passed through to the component (as with the HOC). The props of `MakeCounter` (`MakeCounterProps`) have changed though, with this addition:

有一些变动需要注意。首先是之前提到的 ```InjectedCounterProps```，我想通过接口定义的 Props 可以以方法的形式被调用，而不是传递给组件（HOC 的模式）。```MakeCounter``` (```MakeCounterProps```) 的 Props 也有变动，添加了这些： 

```tsx
children(props: InjectedCounterProps): JSX.Element;
```

This is the render prop itself, and states that the component requires a function passed in that takes in the injected props and returns a JSX element. Here is an example of its usage to highlight this:

这就是 Render Prop, 它是一个函数需要传入 State 和 Props 并返回一个 JSX 元素。以下就是使用示例：

```tsx
interface CounterProps {
  style: React.CSSProperties;
  minValue?: number;
  maxValue?: number;
}

const Counter = (props: CounterProps) => (
  <MakeCounter minValue={props.minValue} maxValue={props.maxValue}>
    {injectedProps => (
      <div style={props.style}>
        <button onClick={injectedProps.onDecrement}> - </button>
        {injectedProps.value}
        <button onClick={injectedProps.onIncrement}> + </button>
      </div>
    )}
  </MakeCounter>
);
```

`MakeCounter`‘s own component declaration then becomes much simpler; it is no longer wrapped in a function as it is no longer a HOC, and the typing is much more straightforward, with no mind-bending generics, subtraction and type intersection. It is simply `MakeCounterProps` and `MakeCounterState`, like any other component:

这时，```MakeCounter``` 组件的声明就简单了很多；它不再需要用一个函数来包裹了，也就不是一个 HOC，并且为其设置 Type 更加直观，不需要令人费解的泛型、subtraction 和 Type Intersection。```MakeCounterProps``` and ```MakeCounterState``` 就像其他组件一样简单：

```tsx
class MakeCounter extends React.Component<
  MakeCounterProps, 
  MakeCounterState
>
```

Finally, `render()` also becomes less work too; it’s just a function call with the injected props — no destructuring and object rest/spread needed!

最后，```render()``` 也变得更简单了；它只是调用一个 Props 中的函数 — 不需像之前那样解构 Props 了！ 

```tsx
return this.props.children({
  value: this.state.value,
  onIncrement: this.increment,
  onDecrement: this.decrement,
});
```

The render prop component then allows for more control over the naming of the props and flexibility in their usage, which was an issue with the equivalent HOC:

Render Prop 组件可以更容易的控制 Props 的命名和更灵活的使用 Props，这在 HOC 中都是问题所在：

```tsx
interface CounterProps {
  style: React.CSSProperties;
  value: number;
  minCounterValue?: number;
  maxCounterValue?: number;
}

const Counter = (props: CounterProps) => (
  <MakeCounter
    minValue={props.minCounterValue}
    maxValue={props.maxCounterValue}
  >
    {injectedProps => (
      <div>
        <div>Some other value: {props.value}</div>
        <div style={props.style}>
          <button onClick={injectedProps.onDecrement}> - </button>
          {injectedProps.value}
          <button onClick={injectedProps.onIncrement}> + </button>
        </div>
        {props.minCounterValue !== undefined ? (
          <div>Min value: {props.minCounterValue}</div>
        ) : null}
        {props.maxCounterValue !== undefined ? (
          <div>Max value: {props.maxCounterValue}</div>
        ) : null}
      </div>
    )}
  </MakeCounter>
);
```

---

With all of these benefits, in particular the much simpler typing, then why not use render props all of the time over HOCs? You certainly could, and there would be no issues doing so, but there are a few issues with render prop components to be aware of.

因为这所有的好处，在实践中跟容易设置 Type，为什么不在 HOC 也是用 Render Props 呢？当然可以，这么做也没有什么问题，但是，Render Prop 组件中一些问题还是需要注意的。

Firstly, there is an issue with separation of concerns; `MakeCounter` is now baked into the `Counter` component rather than wrapping it, making it more difficult to test the two in isolation. Secondly, as the props are injected in the render function of component, you cannot make use of them in the lifecycle methods (provided `Counter` was changed to a class component).

首先，有一个关键分离点的问题；现在，```MakeCounter``` 放在了 ```Counter``` 中，而不是包装它，在测试两个组件时会更加困难。其次，Props 是通过 Render 方法注入到组件的，这样就不能在组件的生命周期方法中使用它们了（假如 ```Counter``` 是一个类组件）。

Both of these issues are easy to solve, as you can simply make a new component using the render prop component:

这两个问题都比较好解决，你可以新建一个组件用于 Render Prop 组件：

```tsx
interface CounterProps extends InjectedCounterProps {
  style: React.CSSProperties;
}

const Counter = (props: CounterProps) => (
  <div style={props.style}>
    <button onClick={props.onDecrement}> - </button>
    {props.value}
    <button onClick={props.onIncrement}> + </button>
  </div>
);

interface WrappedCounterProps extends CounterProps {
  minValue?: number;
  maxValue?: number;
}

const WrappedCounter = ({
  minValue,
  maxValue,
  ...props
}: WrappedCounterProps) => (
  <MakeCounter minValue={minValue} maxValue={maxValue}>
    {injectedProps => <Counter {...props} {...injectedProps} />}
  </MakeCounter>
);
```

Another issue is that it could be seen as less convenient in general — it is now a lot of boilerplate that the consumer has to write, especially if they only want to wrap their component in a single HOC and use the props as-is. This can be remedied by generating a HOC from the render prop component:

另外一个问题通常认为这样并不是很方便 — 假如有很多模版需要处理，尤其，只是把它们用 HOC 包装起来并按照原样使用。使用 Render Prop 组件生成 HOC 可以解决这些问题：

```tsx
import { Subtract, Omit } from 'utility-types';
import MakeCounter, { MakeCounterProps, InjectedCounterProps } from './MakeCounter';

type MakeCounterHocProps = Omit<MakeCounterProps, 'children'>;

const makeCounter = <P extends InjectedCounterProps>(
  Component: React.ComponentType<P>
): React.SFC<Subtract<P, InjectedCounterProps> & MakeCounterHocProps> => ({
  minValue,
  maxValue,
  ...props
}: MakeCounterHocProps) => (
  <MakeCounter minValue={minValue} maxValue={maxValue}>
    {injectedProps => <Component {...props as P} {...injectedProps} />}
  </MakeCounter>
);
```

Here, the techniques of the previous blog post, along with the existing types of the render prop component, are being used to generate the HOC. The only thing to take note of here is that we must remove the render prop (`children`) from the HOC’s props so it is not exposed when used:

这项技术上篇文章中提到过，为了构建 HOC，用已有的 Type 设置 Render Prop 组件。唯一需要注意的就是必须把 Render Prop(```children```) 从 HOC Props 中移除，因此在使用时不需暴露：

```tsx
type MakeCounterHocProps = Omit<MakeCounterProps, 'children'>;
```

---

In the end, the tradeoff between HOCs and render prop components comes down to flexibility vs convenience. This can be solved by writing render prop components first, then generating HOCs from them, which gives the consumer the power to choose between the two. This approach is becoming common amongst reusable component libraries, for example the excellent [render-fns](https://react-fns.netlify.com/docs/en/api.html?source=post_page---------------------------) library.

最后，权衡 HOC 和 Render Prop 组件取决于灵活性和便利性。解决这些问题，可以先写 Render Prop 组件，然后，在封装成 HOC，让使用者在两者之间选择。这在组件库中常用于组件的复用，比如：[render-fns](https://react-fns.netlify.com/docs/en/api.html?source=post_page---------------------------)。

As far as TypeScript goes, there is no doubt that typing HOCs is much more difficult; though with the examples in these two posts, it shows that this burden is on the provider of the HOC and not the consumer. In terms of usage it could be argued that consuming a HOC is less difficult than consuming a render prop component.

就 TypeScript 而言，为 HOC 设置 Type 无疑更加困难；尽管，这两篇文章中都有示例，但是，这种负担都是 HOC 组件带来的，并不是使用者带来的。在使用过程中还是会争论是使用 HOC 还是 Render Prop 组件。

Prior to React v16.8.0, I’d recommend sticking with render prop components for the flexibility and simplicity of typing, and if the need arises, such as building a reusable library of components, or for render prop components that are simple and/or widely used across a project, I would only then generate a HOC from them. Following the release of [React Hooks](https://reactjs.org/docs/hooks-intro.html?source=post_page---------------------------)in React v16.8.0, I would strongly recommend using them over both higher-order components or render props where possible, as they are [much simpler to type](https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d?source=post_page---------------------------).

React v16.8.0 之前，我建议坚持使用 Render Prop 组件，因为设置 Type 更加灵活和简单，如果需要提升，比如，构建一个可复用的组件库，在整个项目中 Render Prop 组件使用更简单和更广泛，我只需用它来封装 HOC 即可。随着，React v16.8.0 发布了 [React Hooks](https://reactjs.org/docs/hooks-intro.html?source=post_page---------------------------)，我强烈推荐尽可能的在 HOC 和 Render Prop 两者中使用它们，因为为它们[设置 Type 更简单](https://github.com/xiao-T/note/blob/master/Published/React%20Hooks%20in%20TypeScript.md)。