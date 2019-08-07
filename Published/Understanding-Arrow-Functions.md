## Understanding Arrow Functions
## 箭头函数

> 原文地址: <https://geromekevin.com/understanding-arrow-functions/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In this tutorial you are going to learn the answers to the questions: “What is an arrow function in JavaScript?” and “How is an arrow function different to a regular function (keyword)?”

在这篇教程中你将会学习：“Javascript 中的箭头函数是什么？” 和 “箭头函数和正常的函数有什么不同？”

An arrow function is like a normal function just with 4 key differences:

1. They have a [different syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) (duh).
2. There is no form of arrow [function declaration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function), only expressions.
3. They don’t get their own `this`.
4. They have an implicit return.

相比正常的函数箭头函数有以下 4 点不同：

1. 有特有的[语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
2. 没有[函数式声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)，只有表达式
3. 没有自己的 `this`
4. 有一个隐式的 `return`

## 1. Different syntax

## 1. 不同的语法

Here is the syntax of an arrow function compared to a regular `function`keyword. I use [Immediately Invoked Function Expression](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) for both functions.

这里有箭头函数和正常关键字 `function` 语法的对比。两个函数我都用了 [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)

```js
(function() {
  console.log('function keyword');
})();

(() => {
  console.log('arrow function');
})();
```

Notice that if an arrow function takes a single argument without any fanciness like destructuring, you may omit the brackets around its parameters.

注意：如果箭头函数只有一个参数，没有任何花哨的参数，你可以省略参数周围的括号。

```js
// You need brackets
const noArguments = () => {};
// You can ommit the brackets.
const oneArgument = n => {
  return n * 2;
};
// You need brackets
const defaultParameter = (name = 'Anon') => {
  return name;
};
// You need brackets.
const destructuringOneArgment = ({ name }) => {
  return name;
};
// You need brackets.
const moreThanOneArgument = (a, b) => {
  return a + b;
};
```

## 2. Only expressions

## 2. 只有表达式

You can also name a function by writing a variable name after the `function`keyword. This is called “function declaration”. There are no function declarations for arrow functions, just anonymous function expressions.

你也可以通过在关键字 `funciton` 后面添加变量给函数命名。这叫"函数式声明"。对于箭头函数不能这么做，只能用匿名函数表达式。

```js
// function declaration with keyword
function decleration() {
  console.log('function declaration with keyword');
}

// function expression with keyword
const keywordExpression = function() {
  console.log('function expression with keyword');
};

// function declaration with arrow function
// 🔴 Not a thing.

// function expression with arrow function
const arrowExpression = () => {
  console.log('function expression with keyword');
};
```

The difference between a function declaration and a function expression is that they are parsed at different times. The declaration is defined everywhere in its scope, whereas the expression is only defined when its line is reached.

函数式声明和表达式声明两者的不同之处是解析时间不同。函数式声明可以在任何地方定义，然而，表达式声明只能在定义之后才能访问。

```js
declaration(); // ✅ Okay

function decleration() {}

foo(); // 🔴 TypeError: foo is not a function
var foo = () => {};
// We could've also written var foo = function() {}
bar(); // 🔴 ReferenceError: Cannot access 'bar' before initialization
const bar = () => {};
```

You can also see the difference between `const` and `var` here. Since `foo` was declared using the `var` keyword, it is hoisted, and its value is `undefined`. `foo()` tries to call `undefined`, but its obviously not a function. Since `const`doesn’t hoist invoking `bar` throws a reference error.

从这你也可以看到 `const` 和 `var` 之间的不同。由于，`foo` 是由关键字 `var` 声明的，所以，它会出现变量提升，并且它的初始值是 `undefined`。`foo()` 在尝试调用 `undefined`，显然它不是一个函数。由于，`const` 没有变量提升，执行 `bar` 会抛出一个引用错误。

## 3. No this

## 3. 没有 this

Like other languages, JavaScript has a [`this` keyword](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this). There are [several ways`this` is bound explicitly or implicitly](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md). We are only going to focus on the behavior relevant to arrow functions.

就像其它语言一样，Javascript 也有 [`this` 关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)。有几种[绑定 `this` 的方法：显式或者隐式](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md)。我们只关注箭头函数相关的内容即可。

```js
const car = {
  wheels: 'four',
  yellWheels() {
    return this.wheels.toUpperCase();
  },
  countWheels: () => {
    return this.wheels;
  },
};

car.yellWheels(); // 'FOUR'
car.countWheels(); // undefined
```

Using the `function` keyword, `this` references the object. However, the arrow function doesn’t get its own `this`. Therefore `wheels` is undefined because the global object doesn’t have a property `wheels`.

使用 `function` 关键字，`this` 引用的是当前对象。然而，箭头函数则没有自己的 `this`。因此，`wheels` 是 undefined，这是因为 global 对象没有属性 `wheels`。

To understand `this`, play [Eric Elliott’s “What is this?”](https://medium.com/javascript-scene/what-is-this-the-inner-workings-of-javascript-objects-d397bfa0708a).

想要更好的理解 `this`，可以查看 [Eric Elliott’s “This 是什么?”](https://medium.com/javascript-scene/what-is-this-the-inner-workings-of-javascript-objects-d397bfa0708a)。

## 4. Implicit return

## 4. 隐式的 return

In the previous code snippet, we used the `return` keyword to return values from the functions. Nevertheless, arrow functions don’t need to do that. If your function body is a single expression, you can omit the curly braces and the expression gets returned automatically.

在上一段代码中，我们用 `return` 关键字从函数中返回了一个值。不过，箭头函数不需要这样做。如果，你的函数中只有一句表达式，你可以省略掉大括号，表达式会自动的返回。

```js
// These two functions do the same
const explicitReturn = () => {
  return 'foo';
};
const implicitReturn = () => 'foo';

explicitReturn(); // 'foo'
implicitReturn(); // 'foo'
```

Using implicit returns, we can simplify the examples from the syntax section.

用隐式 `return` ，我们可以简化示例中的语法。

```js
// Can't be simplified, no expression
const noop = () => {};

// Can be simplified, return expressions
const getName = (name = 'Anon') => name;
const double = n => n * 2;
const getNameProp = ({ name }) => name;
const add = (a, b) => a + b;

```

Implicit returns become especially handy for [currying](https://en.wikipedia.org/wiki/Currying), which is when a function returns another function until it returns its final value.

隐式的 `return` 让[柯里化](https://en.wikipedia.org/wiki/Currying)变得非常简单，也就是一个函数返回另外一个函数，最终返回一个值。

```js
const add = a => b => a + b; // add is a function that returns b => a + b
// Read this as const add = a => (b => a + b);
const inc = add(1); // inc is a function because b => a + b got returned
const decr = add(-1);

inc(3); // 4 because inc remembers a as 1
inc(6); // 7
decr(3); // 2 because decr remembers a as -1
```

`add` is a function that takes in `a` and returns a function that takes in `b` that returns the sum of `a` and `b`. The function that takes in `b` remembers `a` in its closure.

`add` 是一个函数，它有一个参数 `a`，然后，返回一个参数为 `b` 的函数，最终返回 `a`  加 `b` 的和。参数为 `b` 函数会在闭包中记住 `a`。

If you liked this article, you might also like [“Understanding Conditional Rendering in React”](https://geromekevin.com/understanding-conditional-rendering-in-react/) because we explore `||` and `&&` in it in-depth.

如果，你喜欢这篇文章，你或许也会喜欢[“理解 React 中的条件渲染”](https://geromekevin.com/understanding-conditional-rendering-in-react/)，因为，文章中我们会深入的探讨 `||` 和 `&&`。

## Summary

##总结

We understood the 4 differences between the `function` keyword and an arrow function.

我们已经明白了关键字 `function` 和箭头函数之间的 4 种不同之处。