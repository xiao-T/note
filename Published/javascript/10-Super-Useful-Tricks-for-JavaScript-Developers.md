# 10 Super Useful Tricks for JavaScript Developers

## JavaScript 开发者 10 个非常有用的技巧

> 原文地址: <https://blog.bitsrc.io/10-super-useful-tricks-for-javascript-developers-f1b76691199b/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*lxVomliD0hEt7tFzApKvcw.jpeg)

As we all know, JavaScript has been changing rapidly. With the new ES2020, there are many awesome features introduced that you might want to checkout. To be honest, you can write code in many different ways. They might achieve the same goal, but some are shorter and much clearer. You can use some small tricks to make your code cleaner and clearer. Here is a list of useful tricks for JavaScript developers that will definitely help you one day.

我们知道，JavaScript 正在迅速的发生改变。ES2020 中带来了很多神奇的功能。老实说，你有很多编写代码的方式。它们都可以达到相同的目的，但是，有一些就比较简单明了。你也可以使用一些技巧，让你的代码更加清晰。在这里我为 JavaScript 开发者列举一些有用的技巧，相信有一天会对你有帮助。

## Method Parameter Validation

## 函数参数校验

JavaScript allows you to set default values for your parameters. Using this, we can implement a neat trick to validate your method parameters.

JavaScript 允许你为函数参数设置默认的值。因此，我们可以用一个小技巧来检验函数的参数。

```js
const isRequired = () => { throw new Error('param is required'); };
const print = (num = isRequired()) => { console.log(`printing ${num}`) };
print(2);//printing 2
print()// error
print(null)//printing null
```

Neat, isn’t it?

很整洁，是吧？

## Format JSON Code
## 格式化 JSON 代码

You would be quite familiar with `JSON.stringify` by now. But are you aware that you can format your output by using `stringify` ? It is quite simple actually.

现在，你应该很熟悉 `JSON.stringify` 了。但是，你知道使用 `stringify` 可以格式化输出么？使用很简单。

The `stringify` method takes three inputs. The `value` , `replacer` , and `space`. The latter two are optional parameters. That is why we did not use them before. To indent our JSON, we must use the `space` parameter.

`stringify` 有三个参数。分别是  `value` , `replacer` , 和 `space`。后两个是可选的。这也是我们不常用的原因。为了格式化 JSON，我们必须使用  `space`。

```js
console.log(JSON.stringify({name:"John",Age:23},null,'\t'));
>>> 
{
 "name": "John",
 "Age": 23
}
```

Here’s a React component I’ve published to [Bit](https://bit.dev/). Feel free to play with the stringify [example](https://bit.dev/eden/stringify-components/display-results).

我在 [Bit](https://bit.dev/) 发布了一个 React 组件。可以随意使用它来输出 stringify 的[结果](https://bit.dev/eden/stringify-components/display-results)。

## Get Unique Values From An Array
##  数组去重

Getting unique values from an array required us to use the `filter` method to filter out the repetitive values. But with the new `Set` native object, things are really smooth and easy.

从数组中获取不重复的值我们需要使用 `filter` 来过滤那些重复的值。但是，使用 `Set` 对象，处理起来更加简单顺畅。 

```js
let uniqueArray = [...new Set([1, 2, 3, 3,3,"school","school",'ball',false,false,true,true])];
>>> [1, 2, 3, "school", "ball", false, true]
```

## Removing Falsy Values From Arrays

## 移除数组中的假值

There can be instances where you might want to remove falsy values from an array. Falsy values are values in JavaScript which evaluate to FALSE. There are only six falsy values in JavaScript and they are,

- `undefined`
- `null`
- `NaN`
- `0`
- `“”` (empty string)
- `false`

很多情况下，你希望移除数组中的假值。JavaScript 中的假值就是那些计算后是 FALSE 的值。JavaScript 中有六个假值：

- `undefined`
- `null`
- `NaN`
- `0`
- `“”` (空字符串)
- `false`

The easiest way to filter out these falsy values is to use the below function.

过滤假值最简单的方式就是使用以下的方法。

```js
myArray
    .filter(Boolean);
```

If you want to make some modifications to your array and then filter the new array, you can try something like this. Keep in mind that the original `myArray` remains unchanged.

如果，你想做些改变，然后过滤生成新的数组，你的代码看起来应该是这样。记住，原数组 `myArray` 会保持不变。

```js
myArray
    .map(item => {
        // Do your changes and return the new item
    })
    .filter(Boolean);
```

## Merge Several Objects Together
## 合并对象

I have had several instances where I had the need to merge two or more classes, and this was my go-to approach.

我有多个实例对象，我需要把它们合并在一起，我会使用以下的方式。

```js
const user = { 
 name: 'John Ludwig', 
 gender: 'Male' 
 };
const college = { 
 primary: 'Mani Primary School', 
 secondary: 'Lass Secondary School' 
 };
const skills = { 
 programming: 'Extreme', 
 swimming: 'Average', 
 sleeping: 'Pro' 
 };
const summary = {...user, ...college, ...skills};
```

The three dots are also called the spread operator in JavaScript. You can read more of their uses over [here](http://bit.ly/2s7pOm2).

JavaScript 中三个连续的点也被称作为展开操作符。你可以在[这](http://bit.ly/2s7pOm2)了解更多情况。

## Sort Number Arrays

## 全数字的数组排序

JavaScript arrays come with a built-in sort method. This sort method converts the array elements into strings and performs a lexicographical sort on it by default. This can cause issues when sorting number arrays. Hence here is a simple solution to overcome this problem.

JavaScript 数组内置了 sort 方法。此方法会把数组中的元素转化成字符串，然后，按照默认的方式排序。如果，数组中全是数字这就会有问题。有一个简单的方式来解决这个问题。

```js
[0,10,4,9,123,54,1].sort((a,b) => a-b);
>>> [0, 1, 4, 9, 10, 54, 123]
```

You are providing a function to compare two elements in the number array to the sort method. This function helps us the receive the correct output.

你可以提供一个方法来对比数组中的两个元素。这个方法可以帮助我们得到正确的输出。

## Disable Right Click

## 禁用右键点击

You might ever want to stop your users from right-clicking on your web page. Although this is very rare, there can be several instances where you would need this feature.

你或许希望禁止用户在你的站点页面上右击。尽管，这并不常见，但是，总会遇到这种需求。

```html
<body oncontextmenu="return false">
    <div></div>
</body>
```

This simple snippet would disable right click for your users.

以上这种方式就可以禁用用户的右键点击。

## Destructuring with Aliases
## 解构中重命名

The **destructuring assignment** syntax is a JavaScript expression that makes it possible to unpack values from arrays, or properties from objects, into distinct variables. Rather than sticking with the existing object variable, we can rename them to our own preference.

**解构赋值** 是 JavaScript 中一种表达式，它可以从数组、对象中提取相应的值。相比使用对象中现有的属性命名，我可以按照自己的意愿重新为变量命名。

```js
const object = { number: 10 };
// Grabbing number
const { number } = object;
// Grabbing number and renaming it as otherNumber
const { number: otherNumber } = object;
console.log(otherNumber); //10
```

## Get the Last Items in an Array
## 获取数组中最后的元素

If you want to take the elements from the end of the array, you can use the `splice` method with negative integers.

如果，你想获取数组中后面的元素，你可以使用 `splice` 方法并传递一个负整数参数。

```js
let array = [0, 1, 2, 3, 4, 5, 6, 7] 
console.log(array.slice(-1));
>>>[7]
console.log(array.slice(-2));
>>>[6, 7]
console.log(array.slice(-3));
>>>[5, 6, 7]
```

## Wait Until Promises Are Complete

## 等待 Promise 的完成

There might be instances where you will need to wait for several promises to be over. We can use `Promise.all` to run our promises in parallel.

有些情况你需要等待多个 promise。我们可以使用 `Promise.all` 来并发多个 promise。

*Note: Promises will run concurrently in a single-core CPU and will run in parallel in multi-core CPU. Its main task is to wait until all the promises that are passed to it are resolved.*

*注意：Promise 在单核 CPU 会同时执行或者在多核 CPU 中并发。主线程将会等待所有 Promise 完成*

```js
const PromiseArray = [
    Promise.resolve(100),
    Promise.reject(null),
    Promise.resolve("Data release"),
    Promise.reject(new Error('Something went wrong'))];
Promise.all(PromiseArray)
  .then(data => console.log('all resolved! here are the resolve values:', data))
  .catch(err => console.log('got rejected! reason:', err))
```

One main thing to note about `Promise.all` is that the method throws an error when one of the promises reject. This would mean that your code will not wait until all your promises are complete.

需要注意的是：只要有一个 Promise reject，`Promise.all` 就会抛出一个错误。这就代表着你的代码不会等到所有的 Promise 完成。

If you want to wait until all your promises are complete, regardless whether they get rejected or resolved, you can use the `Promise.allSettled` . This method is in the finalized version of ES2020.

如果，你想等待所有的 Promise 完成，而不关心它们是 rejected，还是 resolved，你可以使用 `Promise.allSettled`。这个方法是 ES2020 引入的。

```js
const PromiseArray = [
    Promise.resolve(100),
    Promise.reject(null),
    Promise.resolve("Data release"),
    Promise.reject(new Error('Something went wrong'))];Promise.allSettled(PromiseArray).then(res =>{
console.log(res);
}).catch(err => console.log(err));//[
//{status: "fulfilled", value: 100},
//{status: "rejected", reason: null},
//{status: "fulfilled", value: "Data release"},
//{status: "rejected", reason: Error: Something went wrong ...}
//]
```

*Even though some of the promises are rejected,* `Promise.allSettled` *returns the results from all your promises.*

*尽管有些 Promise 返回了 rejected，`Promise.allSettled` 还是返回了所有的结果。*

## Publish and Reuse React Components with Bit

##用 Bit 发布和重用 React 组件

[Bit](https://bit.dev/) makes it easy to publish reusable React components from any codebase to Bit’s component hub.

[Bit](https://bit.dev/) 让发布可重用的 React 组件变得更加简单。

**Need to update a published component?** `bit import` it into your project, change it, and push it back with a bumped version.

**需要更新已发布的组件？**用 `bit import` 在你的项目中引用，改变它，然后用新的版本号推送到后台即可。

[Share components](https://bit.dev/) with your team to maximize code reuse, speed up delivery, and build apps that scale.

在你的团队中[分享组件](https://bit.dev/)，最大限度的复用代码，提升交付效率，然后，构建可扩展的应用。

![](https://miro.medium.com/max/1400/1*Nj2EzGOskF51B5AKuR-szw.gif)