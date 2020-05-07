# Cleaner TypeScript With the Non-Null Assertion Operator

# TypeScript 中的代码清道夫：非空断言操作符

> 原文地址: <https://medium.com/better-programming/cleaner-typescript-with-the-non-null-assertion-operator-300789388376/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*tPnsbYsfRrcPaDtsMX3ARQ.jpeg)



**⚠️译者注：ES2020 已经原生支持这种语法了。[详情](https://juejin.im/post/5ea2f4dfe51d4546d6359929)**

Recently, I’ve learned about a useful TypeScript operator: The [non-null assertion operator](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#non-null-assertion-operator). It negates null and undefined types from variables.

最近，我学到了一个非常有用的 TypeScript 的操作符：[非空断言操作符](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#non-null-assertion-operator)。它会排除掉变量中的 null 和 undefeind。

In this post, I will explain how and when to use this operator and give some examples of where it can help you.

在这篇文章中，我将会介绍如何、何时使用这个操作符，并提供一些样式，希望可以对你们有帮助。

TL;DR: Adding an exclamation mark after a variable will ignore undefined or null types.

TL;DR：在变量后面添加一个 ! 就会忽略 undefined 和 null。

```ts
function simpleExample(a: number | undefined) {
   const b: number = a; // COMPILATION ERROR: undefined is not assignable to number.
   const c: number = a!; // OK
}
```

## How to Use the Non-Null Assertion Operator
## 如何使用非空断言操作符

The non-null assertion operator takes a typed variable and removes the undefined and null types from it.

非空断言操作符会从变量中移除 undefined 和 null。

Using the operator is as simple as adding an exclamation mark.

只需在变量后面添加一个 ! 即可。

Ignore a variable’s `undefined | null` types:

忽略变量的 `undefined | null`：

```ts
function myFunc(maybeString: string | undefined | null) {
   const onlyString: string = maybeString; //compilation error: string | undefined | null is not assignable to string
   const ignoreUndefinedAndNull: string = maybeString!; //no problem
}
```

Ignore an `undefined` type when executing a function:

当函数执行时忽略 `undefined`：

```ts
type NumGenerator = () => number;

function myFunc(numGenerator: NumGenerator | undefined) {
   const num1 = numGenerator(); //compilation error: cannot invoke an object which is possibly undefined
   const num2 = numGenerator!(); //no problem
}
```

When the operator’s assertion fails at runtime, the code acts as regular JavaScript code. This can cause unexpected behaviors. Examples of unsafe usages of the operator:

当断言操作符在 runtime 失败时，代码就跟正常的 JavaScript 代码一样。这可能会带来意想不到的结果。以下演示了不安全的使用方式：

```ts
const a: number | undefined = undefined;
const b: number = a!;

console.log(b);// prints undefined, although b’s type does not include undefined

-------
type NumGenerator = () => number;
function myFunc(numGenerator: NumGenerator | undefined) {
   const num1 = numGenerator!();
}

myFunc(undefined); // runtime error: Uncaught TypeError: numGenerator is not a function

```

Please note: This operator will only take effect when the `strictNullChecks`[ flag](https://www.typescriptlang.org/docs/handbook/compiler-options.html) is on. When the flag is off, the compiler does not check for `undefined` and `null` types assignments.

请注意：这个操作符只有在 `strictNullChecks` 打开的时候才会有效果。反之，编译器将不会检查 `undefined` 和 `null` 

Now, I know what you’re thinking …

现在，我知道你们在想什么...

## Why Would I Want to Do That?
## 我为什么要这么做？
![](https://miro.medium.com/max/1400/0*veGKkwAOfvRxg11P)
Let’s look at some real-life scenarios the non-null assertion operator can help with:

我们来看一下，在真实场景中非空断言操作符能有什么帮助：

### Event handlers with React refs

### React refs 的事件处理

[React refs](https://reactjs.org/docs/refs-and-the-dom.html) are used to access an element’s current HTML node. When using refs, the current value may be null (in the case that the referenced element is unmounted).

[React refs](https://reactjs.org/docs/refs-and-the-dom.html) 可以用来访问 HTML 节点 DOM。ref.current 的值有时可能是 null（这是因为引用的元素还没有 mounted）。

There are many cases where we can be sure the current element is mounted, thus the `null` type is not relevant.

在很多情况下，我们能确定 current 元素已经 mounted，因此，`null` 是不需要的。

In the following example, an input element is being scrolled into view when clicking a button:

在接来下的示例中，当点击按钮时 input 会滚动到可视区域：

```ts
const ScrolledInput = () => {
   const ref = React.createRef<HTMLInputElement>();

   const goToInput = () => ref.current.scrollIntoView(); //compilation error: ref.current is possibly null

   return (
       <div>
           <input ref={ref}/>
           <button onClick={goToInput}>Go to Input</button>
       </div>
   );
};
```

We know that when `goToInput` is executed, the input element is mounted. We can safely assume that `ref.current` is not null:

我们知道当 `goToInput` 执行时，input 元素一定是 mounted。我们可以大胆假设 `ref.current` 是非空的：

```ts
//...
   
   const goToInput = () => ref.current!.scrollIntoView(); //all good!
   
   //...

```

It is also possible to solve the compilation error without the assertion operator. We can use [`Logical AND`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND):

不使用断言操作符我们也可以解决编译错误的问题。我们可以使用[逻辑与](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND)

```ts
//...

const goToInput = () => ref.current && ref.current.scrollIntoView();

//...
```

This is a bit more verbose.

这就有点啰嗦了。

It becomes especially cumbersome when used for chained-optional properties. See this extreme example:

当链式调用可选属性时，就会变得特别麻烦。我们来看一个极端的演示：

```ts
// Logical AND
object && object.prop && object.prop.func && object.prop.func();

// Compared to the Non-null assertion operator:
object!.prop!.func!(); //assuming object.prop.func are all defined
```

### Prop injection tests with React

### React 中的 prop 注入测试

The following example includes a common pattern for prop testing. We’ll have two components.

接下来的示例是一种常见的 prop 测试模式。我们会有两个组件。

The first is a reusable component, which could be a third-party library component. It receives a callback and calls it when an input value has changed. The callback receives a form event as an argument.

第一个是可重用的组件，它也可以是第三方组件。它有一个 callback prop，当 input 的 value 改变时会调用这个 callback。Callback 会接受一个 event 参数。

```ts
interface SpecialInputProps {
   onChange?: (e: React.FormEvent<HTMLInputElement>) => void;
}

const SpecialInput = (s: SpecialInputProps) => <input onChange={s.onChange}/>;
```

The second component is called `SpecificField`, and it contains `SpecificInput`. `SpecificField` knows how to extract the current value and pass it to the callback:

第二个组件叫做 `SpecificField`，它包含着 `SpecificInput`。`SpecificField` 知道如何提取当前值，并把它传递给 callback：

```ts
interface SpecificFieldProps {
   onFieldChanged: (newValue: string) => void;
}

const SpecificField = (props: SpecificFieldProps) => {
   const inputCallback = (e: React.FormEvent<HTMLInputElement>) => props.onFieldChanged(e.currentTarget.value);
   return <SpecialInput onChange={inputCallback}/>;
};
```

We want to test that `SpecificField` is calling the`onChange` callback properly.

我们想测试 `SpecificField` 是否可以正确的调用 `onChange`。

That means that `SpecificField` gets an `onFieldChanged` callback which unwraps the event value:

也就是说 `SpecificField` 的回调 `onFieldChanged` 是否可以得到一个 event.currentTarget.value：

```tsx
//SpecificField.test.tsx
it('should inject callback to SpecialInput which calls the onFieldChanged callback with the event value', () => {
   const onFieldChanged = jest.fn();
   const wrapper = shallow(<SpecificField onFieldChanged={onFieldChanged}/>);
   const injectedCallback = wrapper.find(SpecialInput).props().onChange;

   expect(injectedCallback).toBeDefined();
   const event = {currentTarget: {value: "new value"}} as React.FormEvent<HTMLInputElement>;
   injectedCallback(event); //compilation error: cannot invoke an object which is possibly undefined

   expect(onFieldChanged).toHaveBeenCalledWith("new value");
});
```

A compilation error occurs since the `onFieldChanged` prop is optional and could be undefined.

因为 `onFieldChanged` 是可选的，因此可能是 undefined，这就会引起编译错误。

Yet, we know that `injectedCallback` is defined at the line where the error occurs — we tested it.

当然，我们知道 `injectedCallback` 已经被定义了。 — 我还测试过它。

This is where the non-null assertion operator can help:

这时，非空断言操作符就可以帮助我们：

```ts
//...

injectedCallback!(event); //no problem
  
//...
```

For comparison, a possible solution that doesn’t use the operator is to use an if-else statement:

另外一个解决方案，我们不使用断言操作符，而是，使用 if-else 条件语句：

```ts
if (injectedCallback) {
   injectedCallback(event); //injectedCallback has to be defined in the “if” block
   expect(onFieldChanged).toHaveBeenCalledWith("new value");
} else {
   fail("SpecialInput was not injected with a callback as expected");
}
```

This is much more verbose.

这非常啰嗦。

Between the two options, I’d prefer using the non-null assertion operator. It’s shorter, more concise, and has less boilerplate.

两者之间，我更喜欢非空断言操作符。它更加简短、清晰，没有多余的代码。

## Conclusion
## 总结

The non-null assertion operator is a handy little tool. When using it, remember to handle carefully. Misuse will cause unexpected behaviors.

非空断言操作符是一个非常实用的工具。使用的时候要小心处理。滥用将会带来意想不到的结果。

However, the benefits are plentiful: It reduces cognitive load in certain scenarios that cannot happen in runtime. Additionally, it makes your code less verbose compared to other alternatives. Plus, it makes you feel like you’re shouting at the compiler, which is always fun.

然而，好处还是很多的： It reduces cognitive load in certain scenarios that cannot happen in runtime。还有，相比其他方案它还会减少你的代码量。另外，这会让你感觉好像在对编译器大喊大叫，这很有趣。

The operator has helped me a few times already. I hope you will find it as useful as I do.

这个操作符已经帮过我很多次了。我希望它也能给你们带来好处。