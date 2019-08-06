## 3 things you didn’t know about the forEach loop in JS
## 3 件你不知道的 JS 中有关 forEach 的事情

> 原文地址: <https://medium.com/front-end-weekly/3-things-you-didnt-know-about-the-foreach-loop-in-js-ff02cec465b1/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*WPfN96RRpQzRMaOedN-HyA.png)

Do you think you know exactly how the `forEach` loop works in JS?

你认为自己真的了解 JS 中 `forEach` 如何工作的吗？

Well, these were my thoughts until recently: “just a regular`for` loop where you can easily use `break` or `return` or `continue`“.

好吧，直到最近我还是一直认为：“它就像 `for` 一样，你可以在循环中方便的使用 `break` `return` `continue`”。

Today, I’ll show you 3 things which you might have not known about the `forEach` loop.

今天， 我将会告诉你 3 件你可能不知道的有关 `forEach` 的事。

# 1. ‘return’ doesn’t stop looping

# 1. `return` 并不会停止循环

Do you think the code below would print `1 2` and then stop?

你认为下面的代码打印 `1 2` 后会停止吗？

```js
array = [1, 2, 3, 4];
array.forEach(function (element) {
  console.log(element);
  
  if (element === 2) 
    return;
  
});
// Output: 1 2 3 4
```

No, **it won’t**. If you come from a [Java](https://www.geeksforgeeks.org/for-each-loop-in-java/) background, you would probably ask yourself *how is that possible?*

不会，它不会停止。如果，你有 [Java](https://www.geeksforgeeks.org/for-each-loop-in-java/) 的背景，你可能会问自己*这怎么可能？*

The reason is that we are passing a **callback function** in our `forEach`function, which behaves just like a normal function and is applied to each element no matter if we `return` from one i.e. when element is 2 in our case.

这是因为我们传递给 `forEach` 回调函数导致的，它就和正常的函数一样接收每一个传递的参数，而不关心是否有 `return`。就像我们示例中一样。

From the [official MDN docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach):

> There is no way to **stop** or **break** a `forEach()` loop other than by throwing an exception. *If you need such behavior, the* `forEach()` *method is the wrong tool.*

来自[ MDN 官方文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)：

> 没有办法中止或者跳出 `forEach` 循环，除非抛出一个异常。*如果你需要这样，使用 `forEach` 就是错误的。*

Let’s rewrite the code from above:

让我们来重写上面的代码：

```js
const array = [1, 2, 3, 4];
const callback = function(element) {
    console.log(element);
    
    if (element === 2) 
      return; // would this make a difference? no.
}
for (let i = 0; i < array.length; i++) {
    callback(array[i]);
}
// Output: 1 2 3 4
```

The `return`statement won’t make any difference, as **we apply the function to each element at each iteration,** hence it doesn’t care if you exited once i.e. when element is 2.

这里的 `return` 语句没什么不同，因为**每次迭代我们都会调用方法**，因此它不关心是否有退出。比如：传递 2 时。

# 2. You cannot ‘break’

# 2. 不能使用 ‘break’

Do you think a`forEach` loop would `break` in the example below?

你认为下面的代码中 `forEach` 遇到 `break` 会跳出循环吗？

```js
const array = [1, 2, 3, 4];
array.forEach(function(element) {
  console.log(element);
  
  if (element === 2) 
    break;
});
// Output: Uncaught SyntaxError: Illegal break statement
```

No, **it won’t even run** because the `break` instruction is not technically in a loop.

不会，**它甚至都不会执行**，因为 `break` 不能在循环中使用。

Solution?

如何解决？

Just use a normal `for` loop. Nobody would laugh at you.

就用正常的 `for` 循环即可。没人会取笑你。

```js
const array = [1, 2, 3, 4];for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
  
  if (array[i] === 2) 
    break;
}// Output: 1 2
```

# 3. You cannot ‘continue’

# 3. 不能使用 continue

Do you expect the code below to skip printing `2` to the console and only show `1 3 4` ?

你是否期望以下的代码不打印 `2`，只是打印 `1 3 4` ?

```js
const array = [1, 2, 3, 4];
array.forEach(function (element) {
  if (element === 2) 
    continue;
  
  console.log(element);
});
// Output: Uncaught SyntaxError: Illegal continue statement: no surrounding iteration statement
```

No, **it won’t even run** because the `continue` instruction is not in a loop, similar to the `break` instruction.

不会，**它不会自行**，因为，就和 `break` 一样 `continue` 也不能在循环中使用。

Solution?

如何解决？

Just use a normal `for` loop again.

同样，还是可以使用 `for` 循环。

```js
for (let i = 0; i < array.length; i++) {
  if (array[i] === 2) 
    continue;
  
  console.log(array[i]);
}
// Output: 1 3 4
```

That was it! Hope you learned something new today.

就这些！希望你今天学到了新的东西。