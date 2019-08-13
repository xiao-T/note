# Understanding Conditional Rendering in React

## React 中的条件渲染

> 原文地址: <https://geromekevin.com/understanding-conditional-rendering-in-react/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

`||` and `&&` (often) don’t return booleans. They behave differently than you might think. Do you use them for conditional rendering in React? In this article, you are going to learn what’s going on.

`||` 和 `&&` 并不是一直都返回布尔值。它们的行为和你想象的不一样。你有在 React 中用它们处理过条件渲染吗？在本文中，你将会学习如何处理它们。

If you have some experience with React, you have seen conditional rendering.

如果，你有使用 React 的经验，应该看到过条件渲染。

```jsx
function Parent({ renderChildren, children }) {
  return (
    <>
      <p>{renderChildren || children}</p>
      <p>{renderChildren && children}</p>
    </>
  );
}
```

Here we use `||` and `&&`. If you’re thinking “Boring, I can predict what happens.” you might be right. But unless you can explain why [Kyle Simpson](https://twitter.com/getify)calls `||` and `&&` “operand selector operators”, you probably don’t understand what’s really going on here. The weird thing about operand selector operators is that you can use them without understanding them and they still do the correct thing most of the time.

在这用到了 `||`  和 `&&`。你或许会想：“无聊，我能预测会发生什么。”你或许是对的。但是，除非你能解释为什么[Kyle Simpson](https://twitter.com/getify)把 `||` 和 `&&` 成为“操作数选择运算符”，你大概不能理解这到底发生了什么。对于操作数选择运算符神奇的是：即便你不理解也可以使用它们，而且，大多情况下仍然可以正确执行。

What threw me off, is that I had an adjacent case using `&&`, where `renderChildren` was `0`. To my surprise, the `0` got rendered, even though it’s [falsy](https://developer.mozilla.org/de/docs/Glossary/Falsy). But when `children` was `0` and `||` was used, nothing was rendered even though `renderChildren` was `true`.

最近，我用到了 `&&` ，示例中 `renderChildren` 的地方返回了 `0`，这是让我绝望的地方。我很惊讶，为什么会渲染 `0`，尽管它也是 [falsy](https://developer.mozilla.org/de/docs/Glossary/Falsy)。但是，当 `children` 是 `0` 并且使用 `||`，没有渲染任何东西，尽管 `renderChildren` 是 `true`。

Let’s have a little test. What is being returned here?

让我们做一些测试。以下代码会返回什么呢？

```js
// Example 1
true && true;
true && false;
false && true;
false && false;

true || true;
true || false;
false || true;
false || false;
```

Easy enough, right?

很简单，对吧？

```js
true && true; // true
true && false; // false
false && true; // false
false && false; // false

true || true; // true
true || false; // true
false || true; // true
false || false; // false
```

What about this?

这些呢？

```js
// Example 2
1 || 'foo';
0 || 'foo';
1 && 'foo';
0 && 'foo';
```

Did you guess this?

你猜是这样吧？

```js
1 || 'foo'; // true 🔴
0 || 'foo'; // true 🔴
1 && 'foo'; // true 🔴
0 && 'foo'; // false 🔴
```

Wrong. `||` and `&&` do not return boolean values (unless you use them with `true` or `false` as in example one).

错。`||` 和 `&&` 不会返回布尔值（除非，示例中有 `true` 或者 `false` 一起使用）。

What’s really being returned is this.

实际上返回是这些。

```js
1 || 'foo'; // 1 ✅
0 || 'foo'; // "foo" ✅
1 && 'foo'; // "foo" ✅
0 && 'foo'; // 0 ✅
```

This means `||` and `&&` return one of their operands - they “select” one, hence the name operand selector operators.

这代表 `||` 和 `&&` 会返回它们操作对象其中之一 —— 它们“选择”一个，因此，被称为操作数选择运算符。

`||` checks if the first operand is truthy using [`ToBoolean`](https://www.ecma-international.org/ecma-262/5.1/#sec-9.2). If yes it returns the first operand, otherwise the second.

`||` 用 [`ToBoolean`](https://www.ecma-international.org/ecma-262/5.1/#sec-9.2) 检查第一个操作数是否为真。如果是真，返回第一个操作数，否则，返回第二个。

`&&` works the exact opposite way. If its first operand is truthy, it returns the second, otherwise the first.

`&&` 正好相反。如果，第一个操作数是真，它返回第二个操作数，否则，返回第一个。

If you use operand selector operators in an `if` statement, for example, they only work as you expect because of [implicit type coercion](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion). The `if` statement also invokes `ToBoolean` on the primitive inside its brackets.

如果，你在 `if` 语句中使用操作数选择运算符，示例就会按照你的期望返回值，这是因为[隐式的类型转换](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion)。`if` 语句会在括号内执行 `ToBoolean` 返回原始值。

We also have to know which [primitives](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures) React renders to understand conditional rendering.

我们也必须知道，哪些原始值在 React 的条件渲染中会正确处理。

```js
function Example() {
  return (
    <>
      {true}
      {false}
      {null}
      {undefined}
      {0}
      {1}
      {NaN}
      {''}
      {'foo'}
      {[]}
      {[null, 0, 1, 'foo']}
      {/* {{}} 🔴 Empty object isn't a valid React object.*/}
    </>
  );
}
```

If you try this code out in a [CodeSandbox](https://codesandbox.io/), you will see that only `0`, `1`, `NaN` and `"foo"` get rendered. This is also true for the value in the array. So the code above will render `01NaNfoo01foo`.

如果，你在 [CodeSandbox](https://codesandbox.io/) 尝试执行这段代码，你会看到只有 `0`, `1`, `NaN` 和 `"foo"` 被渲染。对于数组中也是如此。因此，以上代码将会渲染输出 `01NaNfoo01foo`。

What’s interesting to note here is that React doesn’t go by whether a value is truthy or falsy to determine what should be rendered. If that where the case, `true` would’ve gotten rendered, but `0` and `NaN` not.

需要注意的是：React 并不会根据一个值是 truthy 还是 falsy 来决定应该如何渲染。如果是这样，示例中 `true` 就会被渲染，而不是 `0` 和`NaN`。

Test yourself: What is being rendered?

自己测试：以下代码将会如何渲染？

```js
function App() {
  return (
    <div className="App">
      <h3>Test 1</h3>
      <Parent renderChildren={false}>
        <p>Via Children.</p>
      </Parent>
      <h3>Test 2</h3>
      <Parent renderChildren={true}>
        <p>Via Children.</p>
      </Parent>
      <h3>Test 3</h3>
      <Parent renderChildren={true}>{null}</Parent>
      <h3>Test 4</h3>
      <Parent renderChildren={false}>{null}</Parent>
      <h3>Test 5</h3>
      <Parent renderChildren={0}>0</Parent>
      <h3>Test 6</h3>
      <Parent renderChildren={false}>0</Parent>
      <h3>Test 7</h3>
      <Parent renderChildren={true}>0</Parent>
    </div>
  );
}
```

Knowing how React renders primitives, and understanding `||` and `&&` it’s clear to you now, why and when `0` gets rendered if you use it as `renderChildren` or `children`. Only Test 3 and Test 4 render nothing.

了解了 React 如何渲染原始值，也清楚了 `||` 和 `&&`，现在，你应该知道了当你处理 `renderChildren` 或者 `children` 时为什么何时会渲染出 `0` 了。只有第三个和第四个不会渲染任何东西。

## More Quirks

## 更多怪癖

Another gotcha with `||` and `&&` is [operator precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence). From mathematics you might know that `*` is always evaluated before any `+`.

另外需要明白的是 `||` 和  `&&` 的[运算符优先级](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)。在数学中，你应该知道 `*` 总是在 `+` 之前执行。

```js
3 + 4 * 2; // 11, not 14
```

Similarly, `&&` is always evaluated before `||` if there are no explicit brackets.

类似，如果，没有括号的情况下 `&&` 也是总在 `||` 前执行。

Additionally, you also should know that the operand selector operators [short circuit](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation).

另外，你应该也需要知道操作数选择运算符是[短路计算](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation)。

```js
function foo() {
  console.log('foo');
  return true;
}

function bar() {
  console.log('bar');
  return false;
}

foo() || bar(); // foo true
bar() || foo(); // bar foo true
```

In the first example, only `foo` is evaluated. In the second, both `bar` and `foo`are invoked because `bar` returns `false`, which is obviously `falsy`.

在第一个示例中，只有 `foo` 被执行了。第二个示例中，`bar` 和 `foo` 都被执行了，这是因为 `bar` 返回了 `false`，这显然是 `falsy`。

Aside from conditionally rendering React components, you can use `&&` to conditionally assign an optional key to an object using the [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).

除了 React 的条件渲染，你还可以利用 `&&` 配合[展开语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)有条件的为一个对象分配一个可选的值。

```js
const me = { name: { firstName: 'Jan', lastName: 'Hesters' }, age: 25 };
const anon = { age: 34 };

const getName = ({ name }) => ({ ...(name && name) });
const getNameWithDefault = ({ name }) => ({
  ...((name && { name }) || { name: 'Anonymous' }),
});

getName(me); // { firstName: "Jan", lastName: "Hesters" }
getName(anon); // {}
getNameWithDefault(me); // { name: { firstName: 'Jan', lastName: 'Hesters' } }
getNameWithDefault(anon); // { name: "Anonymous" }
```

Why use `&&` here? Without `&&`, `name` would explicitly be `undefined` when `getName` is called with `anon`.

这里为什么要使用 `&&`，当传递 `anon` 给 `getName` 时，如果没有 `&&` 会返回 `undefined`。

```js
const getNameWithoutSpread = ({ name }) => ({ name });
const getName = ({ name }) => ({ ...(name && { name }) });

const anon = { age: 34 };

const foo = getNameWithoutSpread(anon); // {name: undefined}
const bar = getName(anon); // {}

foo.hasOwnProperty('name'); // true
bar.hasOwnProperty('name'); // false

```

If you liked this article you might also like [“useCallback vs. useMemo”](https://geromekevin.com/usecallback-vs-usememo/) where we explore the differences between the two Hooks or the more beginner friendly post: [“Understanding arrow functions.”](https://geromekevin.com/understanding-arrow-functions)

如果，你喜欢这篇文章，你应该也会喜欢 [“useCallback vs. useMemo”](https://geromekevin.com/usecallback-vs-usememo/)，我们将在其中探讨两个 Hooks 之间的区别，新手应该更适合这篇文章：[箭头函数](https://github.com/xiao-T/note/blob/master/Published/Understanding-Arrow-Functions.md)

## Summary

## 总结

We explained how `||` and `&&` work, that they select their operands and the pitfalls you might encounter when using them. We also looked at how you can use them for conditional rendering in React and for conditionally assigning keys to objects.

我们解释了 `||` 和 `&&` 是如何工作了，它们会自主的选择它们的操作数，这其中的陷阱在使用它们时你应该遇到过。我们也研究了它们如何在 React 中处理条件渲染，以及如何为对象分配键值的。