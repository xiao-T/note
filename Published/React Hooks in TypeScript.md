## React Hooks in TypeScript
## TypeScript 中使用 React Hooks

> 原文地址: <https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/600/1*7k79tcK3Qq4-qI0rGvBrdA.png)

Released in React v16.8.0, [React Hooks](https://reactjs.org/docs/hooks-intro.html) address a number of issues with React, and perhaps most notably for TypeScript users, provide a first-class solution for reusing stateful logic, built with typing in mind. Due to this, a lot of the types for Hooks can be inferred, but in some cases explicit types must be set.

[React Hooks](https://reactjs.org/docs/hooks-intro.html) 伴随 React v16.8.0 一起发布，它解决了一些 React 已知的问题，对 TypeScript 用户尤其重要，通过内置的 Type，对复用 state 提供了一流的解决方案。因此，Hook 的很多 Type 可以通过推断来设置，但是有些情况还是需要明确 Type。

This article assumes some knowledge of the Hooks that are available. For this refer to the [React documentation](https://reactjs.org/docs/hooks-intro.html).

针对这篇文章，假设您对 Reac Hooks 的知识有所了解。可以参看[React 的文档](https://reactjs.org/docs/hooks-intro.html)。

---

## useState

In most cases, the types for ```useState``` can be inferred based on the initial value, but if you need to start with ```null``` or ```undefined```, or need more control over the typing, such as specifying the types for an array or object, a generic can be used.

大多情况下，```useState``` 的 Type 可以基于初始化的值推断出，但是，如果你需要设置 ```null``` 或者 ```undefined```，或者需要更好的控制 Type，比如设置为一个数组或对象，这时，可以用泛型来解决。

```tsx
// inferred as number
const [value, setValue] = useState(0);

// explicitly setting the types
const [value, setValue] = useState<number | undefined>(undefined);
const [value, setValue] = useState<Array<number>>([]);

interface MyObject {
  foo: string;
  bar?: number;
}
const [value, setValue] = useState<MyObject>({ foo: 'hello' });
```

---

## useContext

```useContext``` can infer its types based on the context object that is passed in as an argument, so has no need for explicit typing.

```useContext``` 的 Type 也可以通过传递的参数对象来推断，因此，不需要明确的设置。

```tsx
type Theme = 'light' | 'dark';
const ThemeContext = createContext<Theme>('dark');

const App = () => (
  <ThemeContext.Provider value="dark">
    <MyComponent />
  </ThemeContext.Provider>
)

const MyComponent = () => {
  const theme = useContext(ThemeContext);
  return <div>The theme is {theme}</div>;
}
```

---

## useEffect / useLayoutEffect

These two hooks are used for performing side effects and return an optional clean up function. As they do not deal with returning values, no types are necessary.

这两个 Hook 可以用来执行有副作用的操作，并返回一个可选的清理函数。它们不需要处理返回值，因此，也不需要设置 Type。

```tsx
useEffect(() => {
  const subscriber = subscribe(options);
  return () => {
    unsubscribe(subscriber)
  };
}, [options]);
```

---

## useMemo / useCallback

Both ```useMemo``` and ```useCallback``` (a shorthand for memoising functions) both have their types inferred from the values that they return.

```useMemo``` 和 ```useCallback``` （memoising 方法的简称）两者的 Type 都可以根据自身返回值来推断。

```tsx
const value = 10;
// inferred as number
const result = useMemo(() => value * 2, [value]);

const multiplier = 2;
// inferred as (value: number) => number
const multiply = useCallback((value: number) => value * multiplier, [multiplier]);
```

*Note: you must make sure you specify the types of the parameters of the callback for* ```useCallback```, otherwise they will be set to ```any```, regardless of whether you have TypeScript’s ```noImplicitAny``` *set to true.*

*注意⚠️：你必须为 ```useCallback``` 回调方法的参数设置 Type， 否则会默认为 ```any```，而不管是否有设置 TypeScript ```noImplicitAny``` 为 true。*

----

## useRef

Historically, refs have been used for referencing DOM nodes or class components, with the ref object's readonly ```current``` value starting as ```null```until the ref is attached. For this case ```null``` can be used as the initial value and a generic can be used for the ref type:

历史上，refs 可以用来引用 DOM 节点或者类组件，ref 对象中只读属性 ```current``` 的值在有引用前默认是 ```null```。对此，```null``` 可以用来做为初始化值，然后用泛型来设置 Type。

```tsx
const MyInput = () => {
  const inputRef = useRef<HTMLInputElement>(null);
  return <input ref={inputRef} />
}
```

Since the introduction of Hooks, ```useRef``` can also be used as a replacement for [instance properties on classes](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables), in which case the type can be inferred and ```current``` becomes mutable:

根据 Hooks 文档介绍，```useRef``` 也可以用来做为一个[classes 上的实例属性](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)，在这种情况下 Type 也可以被推断，并且 ```current``` 变得可变。

```tsx
const myNumberRef = useRef(0);
myNumberRef.current += 1;
```

---

## useReducer

Reducers, the pattern introduced by Redux, can be typed in a similar fashion, with the types for the actions and state inferred from the reducer when supplied to ```useReducer```:

Reducers 是 Redux 中提出的一种模式，它在处理 Type 也比较类似，可以通过 ```useReducer``` 提供的 actions 和 state 来推断：

```tsx
interface State {
  value: number;
}

type Action =
  | { type: 'increment' }
  | { type: 'decrement' }
  | { type: 'incrementAmount'; amount: number };

const counterReducer = (state: State, action: Action) => {
  switch (action.type) {
    case 'increment':
      return { value: state.value + 1 };
    case 'decrement':
      return { value: state.value - 1 };
    case 'incrementAmount':
      return { value: state.value + action.amount };
    default:
      throw new Error();
  }
};

const [state, dispatch] = useReducer(counterReducer, { value: 0 });

dispatch({ type: 'increment' });
dispatch({ type: 'decrement' });
dispatch({ type: 'incrementAmount', amount: 10 });

// TypeScript compilation error
dispatch({ type: 'invalidActionType' });
```

---

## useImperativeHandle

```useImperativeHandle``` is by far the most involved Hook to set types for, but also likely the least used as it is not a common (or recommended) practice to expose imperative functions (handles) on components.

```useImperativeHandle``` 是目前最需要设置 Type 的 Hook，但是，在组件上暴露一个 imperative 方法也是最不常用的（或者不推荐）实践。

```tsx
// useImperativeHandle.tsx
export interface MyInputHandles {
  focus(): void;
}

const MyInput: RefForwardingComponent<MyInputHandles, MyInputProps> = (
  props,
  ref
) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => {
      if (inputRef.current) {
        inputRef.current.focus();
      }
    },
  }));

  return <input {...props} ref={inputRef} />;
};

export default forwardRef(MyInput);
```

The above example wraps an HTML input component and exposes a ```focus```function in order to allow the input to be focussed. Firstly, an interface ```MyInputHandles``` is declared to specify the functions that will be exposed through the ref. This interface is then passed to ```RefForwardingComponent``` to set it up as a component that can have a ref passed to it, which is implemented using [```forwardRef```](https://reactjs.org/docs/forwarding-refs.html). From this, ```useImperativeHandle``` can infer the types, so you will receive a compilation error if you do not implement the interface as the return value of the factory function in second argument.

上面的示例中我们通过包装 HMTL Input 实现了一个组件，并暴露了一个方法 ```focus``` 可以让 input 获取焦点。首先，定义了接口 ```MyInputHandles``` 并指定暴露的方法。然后把接口传递给 ```RefForwardingComponent``` ，这样就可以传递 ref 给组件，组件由 [```forwardRef```](https://reactjs.org/docs/forwarding-refs.html) 来实现。因此，```useImperativeHandle``` 也可以推断 Type，如果，你不在传递的工厂函数第二个参数中指定正确的类型，那么你就会收到编译报错。

The ref can then be used by using ```MyInputHandles``` in the generic passed to ```useRef```:

这时，```MyInputHandles``` 可以以泛型的方式传递给 ```useRef```：

```tsx
//useImperativeHandle-usage.tsx
import MyInput, { MyInputHandles } from './MyInput';

const Autofocus = () => {
  const myInputRef = useRef<MyInputHandles>(null);

  useEffect(() => {
    if (myInputRef.current) {
      myInputRef.current.focus();
    }
  });

  return <MyInput ref={myInputRef} />
}
```

---

## useDebugValue

```useDebugValue``` is used for displaying values in React Developer Tools and takes a value and an optional formatting function, for which the types can be inferred based on the value passed in.

```useDebugValue``` 可以在 React 开发者工具中显示信息，并且可以提供一个可选方法来格式化信息，它的 Type 也可以根据传递的参数来推断。

```tsx
const date = new Date();
useDebugValue(date, date => date.toISOString());
```

