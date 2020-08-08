## Some cool and awesome JavaScript tricks
## 一些有用的 JavaScript 技巧

> 原文地址: <https://medium.com/developers-arena/some-cool-and-awesome-javascript-tricks-4bcb9af9b1d3/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In this article, we will cover some of the cool and awesome JavaScript tricks which you may find useful at some point while coding in JavaScript.

这篇文章中，我将会介绍一些非常有用的 JavaScript 小技巧，希望对你以后的编码有帮助。

![](https://miro.medium.com/max/1400/1*H-25KB7EbSHjv70HXrdl6w.png)

# Trick 1

# 技巧1

## Identifying if a variable is of primitive or non-primitive data type

## 验证一个变量是原始类型，还是非原始类型

There are primitive and non-primitive data types in Javascript. The primitive types include boolean, string, number, BigInt, null, Symbol and undefined. The non-primitive types include the Objects.

JavaScript 中的数据类型包括：原始类型和非原始类型。原始类型又包括：boolean、string、number、BigInt、null、Symbol 和 undefined。非原始类型包括：Object。

Often we might need to identify what type of value is stored in a variable — Primitive or Non-Primitive?

通常，我们需要验证变量中存储的数据类型是什么 — 原始类型还是非原始类型？

Consider a variable var

看一下这个变量

```js
var val; // contains either primitive or non-primitive value
```

This can be achieved with the help of a simple code snippet.

验证数据类型可以通过以下的代码实现。

```js
function isPrimitive(val) {
    return Object(val) !== val;
}
```

We are using the Object constructor to create a wrapper object for a value.

我们利用构造函数 Object 来包装变量生成一个新的对象。

If the value is a primitive data type, the Object constructor creates a new wrapper object for the value.

如果，这个值是原始类型，构造函数 Object 就会创建一个新的对象。

If the value is a non-primitive data type (an object), the Object constructor will give the same object.

如果，这个值是非原始类型（一个对象），构造函数 Object 就会返回同一个对象。

Hence, a strict check (!== or ===) can help us identifying if a variable is of primitive or non-primitive type.

因此，一个严格的比较（!== 或者 ===）可以帮助我们来验证一个变量是原始类型还是非原始类型。

# Trick 2

# 技巧2

## Creating a pure object in Javascript

## 创建纯对象

Before creating a pure object, let’s understand *“What is a pure object?”*

在创建纯对象之前，我们需要先理解*“什么是纯对象？”*

A pure object in JavaScript means that it should not have any functions in its prototype.

JavaScript 中一个纯对象意味着，它的原型链上不能包含任何的方法。

An object is created with the following syntax.

创建对象的语法如下：

```
var obj = {};
```

Upon checking the obj.__proto__ we get many functions available inside it.

通过检查 `obj.__proto__` 我们发现它有很多的方法。

![Image for post](https://miro.medium.com/max/3348/1*FS4Y23ELSNPaEibNkdOw3A.png)

What if we want to create an object that does not have any of the prototype functions inside it?

如果，我们想创建一个原型链上没有任何方法的对象应该如何做呢？

We can achieve this by using the Object constructor’s create method.

我们可以通过构造函数 Object 中的 create 方法来实现。

```
var obj = Object.create(null);
```

Upon checking the object’s prototype, we get the following result

检查它的原型链，我们会发现以下的结果

![Image for post](https://miro.medium.com/max/60/1*YSjxMik8NGvg2lNngLTCjg.png?q=20)

![Image for post](https://miro.medium.com/max/1148/1*YSjxMik8NGvg2lNngLTCjg.png)

>  An object with no prototype.
>
> 一个没有任何原型的对象

Hence, a pure object is created.

因此，我们创建了一个纯对象。

# Trick 3

# 技巧3

## Removing duplicates from an array

## 移除数组中重复的数据

Consider the following array

看一下这个数组

```
var arr = [1, 2, 3, 3, 4, 5, 6, 6, 7, 7, 7, 7];
```

We can see many duplicate elements in the array. To remove the duplicate elements from the array, we can use a **Set**.

我们看到数组中存在多个重复的数据。为了从数组中移除这些重复的数据，我们可以使用 **Set**。

A **Set** object lets us store unique values only. A set cannot have a duplicate value in it. Read more about it in the [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set).

**Set** 对象只允许存储具有唯一性的值。Set 对象中不会有重复的值存在。[MDN Web 文档中](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)可以了解更多的相关内容。

To remove duplicates from the array, we write the code

用以下代码，我们就可以移除数组中重复的数据

```
const newArr = [...new Set(arr)];
```

Here we created a new Set containing only the unique values of an array, then spread it inside a new array.

首先，我们用数组作为参数创建了一个 Set 对象，然后，用 Spread 语法生成新的数组。

> The … is the Spread operator. Read more about it in the [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
>
> ... 是 JavaScript 中展开操作符。在[MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)中可以了解更多。

As a result, we get an array with only unique values.

最终，我们得到了一个没有重复数据的数组。