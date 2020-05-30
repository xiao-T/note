# I never understood JavaScript closures

## 我从来都不理解闭包

> 原文地址: <https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1120/1*1vMy563vz6LA_67VrzeT9g.png)

As the title states, JavaScript closures have always been a bit of a mystery to me. I have [read](https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44) [multiple](https://medium.freecodecamp.org/whats-a-javascript-closure-in-plain-english-please-6a1fc1d2ff1c) [articles](https://en.wikipedia.org/wiki/Closure_(computer_programming)), I have used closures in my work, sometimes I even used a closure without realizing I was using a closure.

正如标题所述，JavaScript 闭包对我来说一直是一个谜。为此，我[读过]((https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44))[很多]((https://medium.freecodecamp.org/whats-a-javascript-closure-in-plain-english-please-6a1fc1d2ff1c))[文章]((https://en.wikipedia.org/wiki/Closure_(computer_programming))，工作中我也用过闭包，有些时候，我甚至都不知道使用了闭包。

Recently I went to a talk where someone really explained it in a way it finally clicked for me. I’ll try to take this approach to explain closures in this article. Let me give credit to the great folks at [CodeSmith ](https://www.codesmith.io/)and their *JavaScript The Hard Parts* series.

最近，我和一些人讨论了一下，他们真正的点醒了我。在这篇文章中，我将会尝试解释一下闭包。首先，我要感谢一下 [CodeSmith ](https://www.codesmith.io/) 和他们的*JavaScript 的课程*。

## Before we start

## 前言

Some concepts are important to grok before you can grok closures. One of them is the *execution context*.

为了理解闭包，有些概念非常重要。其中之一就是*执行上下文*。

[This article](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/) has a very good primer on Execution Context. To quote the article:

[这篇文章](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)很好的解释了什么是执行上下文。以下是引用：

> When code is run in JavaScript, the environment in which it is executed is very important, and is evaluated as 1 of the following:
>
> **Global code** — The default environment where your code is executed for the first time.
>
> **Function code** — Whenever the flow of execution enters a function body.
>
> (…)
>
> (…), let’s think of the term `execution context` as the environment / scope the current code is being evaluated in.
>
> 当执行 JavaScript 代码时，执行环境非常重要，并且会按照以下情况计算：
>
> **全局代码** — 当代码第一次执行时，默认的执行环境
>
> **函数代码** — 在函数体内执行的代码
>
> `执行上下文`其实就是当前代码执行的环境/作用域。

In other words, as we start the program, we start in the global execution context. Some variables are declared within the global execution context. We call these global variables. When the program calls a function, what happens? A few steps:

1. JavaScript creates a new execution context, a local execution context
2. That local execution context will have its own set of variables, these variables will be local to that execution context.
3. The new execution context is thrown onto the *execution stack*. Think of the execution stack as a mechanism to keep track of where the program is in its execution

换句话说，程序开始时，是在全局的执行上下文中。有些变量是在全局上下文中被声明定义的。我们称之为全局变量。当程序在函数中执行，会发生什么呢？会有以下几步：

1. JavaScript 会创建一个本地的全新上下文
2. 本地的上下文会有它内部的变量，这些变量属于当前的本地执行上下文
3. 新的执行上下文将会被压入*执行栈*中。执行栈就是为了跟踪程序在哪执行的机制。

When does the function end? When it encounters a `return` statement or it encounters a closing bracket `}`. When a function ends, the following happens:

1. The local execution contexts pops off the execution stack
2. The functions sends the return value back to the calling context. The calling context is the execution context that called this function, it could be the global execution context or another local execution context. It is up to the calling execution context to deal with the return value at that point. The returned value could be an object, an array, a function, a boolean, anything really. If the function has no `return` statement, `undefined` is returned.
3. The local execution context is destroyed. This is important. Destroyed. All the variables that were declared within the local execution context are erased. They are no longer available. That’s why they’re called local variables.

函数何时结束？当遇到 `return` 语句或者遇到闭合的大括号 `}`。当函数结束时，会依次发生以下情况：

1. 本地执行上下文会从执行栈中被弹出
2. 调用上下文将会得到函数的返回值。调用上下文就是函数被调用时的执行上下文，它可是全局执行上下文或者另外一个本地执行上下文。此时，调用上下文会处理函数的返回值。返回值可以是对象、数组、函数、布尔值、任何值。如果，函数没有 `return` 语句，默认，将会返回 `undefined`。
3. 接下来，本地执行上下文将会被销毁。这个很重要。销毁代表着所有在其内部声明的变量都会被抹除。它们不再可用。这也是为什么把它们称为本地变量。

## A very basic example

## 一个简单的演示

Before we get to closures, let’s take a look at the following piece of code. It seems very straightforward, anybody reading this article probably knows exactly what it does.

解释闭包之前，我们先看一下以下的代码片段。它看起非常简单直接，任何人都知道会发生什么。

```js
1: let a = 3
2: function addTwo(x) {
3:   let ret = x + 2
4:   return ret
5: }
6: let b = addTwo(a)
7: console.log(b)
```

In order to understand how the JavaScript engine really works, let’s break this down in great detail.

为了理解 JavaScript 引擎是如何工作的，我们来逐步分析一下：

1. On line 1 we declare a new variable `a` in the global execution context and assign it the number `3`.

   在第一行中，我们在全局执行上下文中声明了一个变量 `a` 并赋值数字 `3`。

2. Next it gets tricky. Lines 2 through 5 are really together. What happens here? We declare a new variable named `addTwo` in the global execution context. And what do we assign to it? A function definition. Whatever is between the two brackets `{ }` is assigned to `addTwo`. The code inside the function is not evaluated, not executed, just stored into a variable for future use.

   接下来会变得棘手。第二行到第五行是一个整体。发生了什么呢？在全局执行上下文中我们声明了一个名为 `addTwo` 的新变量。我们给它分配什么呢？函数定义。两个大括号 `{ }` 任何代码都会分配给 `addTwo`。函数内部的代码，这时并不会被计算，也不会执行，只是存储在一个变量中以便将来使用。

3. So now we’re at line 6. It looks simple, but there is much to unpack here. First we declare a new variable in the global execution context and label it `b`. As soon as a variable is declared it has the value of `undefined`.

   现在，我们来到了第六行。看起非常简单，但是，这里包含了很多东西。首先，在全局执行上下文中，我们声明了一个新的变量 `b`。变量声明的同时，也会赋值 `undefined`。

4. Next, still on line 6, we see an assignment operator. We are getting ready to assign a new value to the variable `b`. Next we see a function being called. When you see a variable followed by round brackets `(…)`, that’s the signal that a function is being called. Flash forward, every function returns something (either a value, an object or `undefined`). Whatever is returned from the function will be assigned to variable `b`.

   接下来，仍然是第六行，我们看到有一个赋值操作符。这时，我们才真正赋值给变量 `b` 。接下来，我们看到函数被调用了。当你看到一个变量后面跟着一个圆扣号 `()`，那代表着函数调用执行。如前所述，每个函数都会返回一些值（值、对象或者 `undefined`）。不管，函数返回什么都会赋值给变量 `b`。

5. But first we need to call the function labeled `addTwo`. JavaScript will go and look in its *global* execution context memory for a variable named `addTwo`. Oh, it found one, it was defined in step 2 (or lines 2–5). And lo and behold variable `addTwo` contains a function definition. Note that the variable `a` is passed as an argument to the function. JavaScript searches for a variable `a` in its *global* execution context memory, finds it, finds that its value is `3` and passes the number `3` as an argument to the function. Ready to execute the function.

   但是，首先我们需要调用函数 `addTwo`。JavaScript 将会在*全局*执行上下文中查找一个名为 `addTwo` 的变量。是的，它找到了，在第二步定义的（第二行到第五行）。你看，变量 `addTwo` 是一个函数。注意，变量 `a` 作为一个参数传递给了函数。JavaScript 会在*全局*执行上下文中搜索变量 `a`  并找到它，发现它的值是 `3`，然后，数字 `3` 就传递给了函数。准备开始执行函数。

6. Now the execution context will switch. A new local execution context is created, let’s name it the ‘addTwo execution context’. The execution context is pushed onto the call stack. What is the first thing we do in the local execution context?

   现在，执行上下文发生了改变。一个新的本地执行上下文被创建，我们叫它 “addTwo 执行上下文”。这个执行上下文被压入到调用栈。在本地执行上下文中我们首先要做什么呢？

7. You may be tempted to say, “A new variable `ret` is declared in the *local* execution context”. That is not the answer. The correct answer is, we need to look at the parameters of the function first. A new variable `x` is declared in the local execution context. And since the value `3` was passed as an argument, the variable x is assigned the number `3`.

   你可能会说：“一个新的变量 `ret` 在*本地*执行上下文中被声明了”。这不对，正确的答案是，首先，我们需要看一下函数的参数。在本地执行上下文中声明了一个新的变量 `x` 。然后，由于数字 `3` 做为参数传递给了函数，那么，变量 `x` 就的值就变成了 `3`。

8. The next step is: A new variable `ret` is declared in the *local* execution context. Its value is set to undefined. (line 3)

   下一步：*本地*执行上下文声明了新的变量 `ret`。它的值是 `undefined`（第三行）

9. Still line 3, an addition needs to be performed. First we need the value of `x`. JavaScript will look for a variable `x`. It will look in the local execution context first. And it found one, the value is `3`. And the second operand is the number`2`. The result of the addition (`5`) is assigned to the variable `ret`.

   仍旧是第三行，需要执行加法。首先，我们需要用到 `x` 的值。JavaScript 将会查找变量 `x`。首先，它会在本地执行上下文中查找。而且，找到了，它的值是 `3`。第二个操作数是数字 `2`。两者相加之后的结果（`5`）将会赋值给变量 `ret`。

10. Line 4. We return the content of the variable `ret`. Another lookup in the *local* execution context. `ret` contains the value `5`. The function returns the number `5`. And the function ends.

   第四行。我们会返回变量 `ret`。另外，根据*本地*执行上下文的内容得知 `ret` 的值是 `5`。函数将会返回数字 `5`。这时，函数结束。

11. Lines 4–5. The function ends. The local execution context is destroyed. The variables `x` and `ret` are wiped out. They no longer exist. The context is popped of the call stack and the return value is returned to the calling context. In this case the calling context is the global execution context, because the function `addTwo` was called from the global execution context.

    函数在第四到第五行结束。本地执行上下文也随之被销毁。变量 `x` 和 `ret` 同时被抹除。它们将会消失。调用栈也会弹出响应的上下文，返回值将会返回到调用上下文。在这个案例中，调用栈就是全局执行上下文，这是因为，函数 `addTwo` 是在全局执行上下文中被调用的。

12. Now we pick up where we left off in step 4. The returned value (number `5`) gets assigned to the variable `b`. We are still at line 6 of the little program.

    现在，我们重新回到第四步。返回值（数字`5`）赋值给了变量 `b`。我们仍旧在程序的第六行。

13. I am not going into detail, but in line 7, the content of variable `b` gets printed in the console. In our example the number `5`.

    我不用详解介绍，在第七行，变量 `b` 的值被输出到了控制台。它是数字 `5`。

That was a very long winded explanation for a very simple program, and we haven’t even touched upon closures yet. We will get there I promise. But first we need to take another detour or two.

为了解释一个简单的程序费了不少口舌，然而，我们还没有真正的讲道闭包。我保证我会的。首先，我们需要绕个弯路。

## Lexical scope
## 词法作用域

We need to understand some aspects of lexical scope. Take a look at the following example.

我们需要理解什么是词法作用域。看下面的代码。

```js
1: let val1 = 2
2: function multiplyThis(n) {
3:   let ret = n * val1
4:   return ret
5: }
6: let multiplied = multiplyThis(6)
7: console.log('example of scope:', multiplied)
```

The idea here is that we have variables in the local execution context and variables in the global execution context. One intricacy of JavaScript is how it looks for variables. If it can’t find a variable in its *local* execution context, it will look for it in *its* calling context. And if not found there in *its* calling context. Repeatedly, until it is looking in the *global* execution context. (And if it does not find it there, it’s `undefined`). Follow along with the example above, it will clarify it. If you understand how scope works, you can skip this.

想法是：我们在本地执行上下文和全局执行上下文都有变量。JavaScript 中比较难理解是：如何查找变量。如果，在*本地*执行上下文中找不到，将会在自身的*调用*上下文中继续查找。如果，还是没有找到。重复以上的动作，直到查到*全局*执行上下文。（如果，仍旧没有找到，就会返回 `undefined`）。根据这个规则，上面的示例就很清晰了。如果，你清楚作用域是如何工作的，你可以跳过这部分。

1. Declare a new variable `val1 `in the global execution context and assign it the number `2`.

   在全局执行上下文中声明一个变量 `val1 `，然后，给它赋值数字 `2`

2. Lines 2–5. Declare a new variable `multiplyThis `and assign it a function definition.

   第 2 - 5 行。声明了一个新变量 `multiplyThis`，然后，定义了一个函数

3. Line 6. Declare a new variable `multiplied `in the global execution context.

   第 6 行。在全局执行上下文声明了一个变量 `multiplied `

4. Retrieve the variable `multiplyThis `from the global execution context memory and execute it as a function. Pass the number `6 `as argument.

   在全局执行上下文中找到变量 `multiplyThis`，并做为一个函数执行。然后，把数字 `6` 做为参数传递给函数

5. New function call = new execution context. Create a new local execution context.

   函数被调用 = 新的执行上下文。创建新的本地执行上下文

6. In the local execution context, declare a variable `n `and assign it the number 6.

   在本地执行上下文中，声明了变量 `n ` 并赋值了数字 `6`

7. Line 3. In the local execution context, declare a variable `ret`.

   第 3 行。声明了变量 `ret`

8. Line 3 (continued). Perform an multiplication with two operands; the content of the variables `n` and `val1`. Look up the variable `n` in the local execution context. We declared it in step 6. Its content is the number `6`. Look up the variable `val1` in the local execution context. The local execution context does not have a variable labeled `val1`. Let’s check the calling context. The calling context is the global execution context. Let’s look for `val1` in the global execution context. Oh yes, it’s there. It was defined in step 1. The value is the number `2`.

   第 3 行。变量 `n` 和 `vall` 两个数的相乘。在本地执行上下文中查找变量 `n`。我们在第 6 行声明了这个变量。它的值是数字 `6`。本地上下文中没有找到变量 `vall`。需要检测调用上下文。因为，调用上下文是全局上下文。我们需要在全局上下文中查找 `vall`。很好，找到了。它在第 1 行被定义的。它的值是数字 `2`。

9. Line 3 (continued). Multiply the two operands and assign it to the `ret` variable. 6 * 2 = 12. `ret` is now `12`.

   第 3 行。两个数相差，然后赋值给变量 `ret`。6 * 2 = 12。`ret` 的值是 `12`。

10. Return the `ret` variable. The local execution context is destroyed, along with its variables `ret` and `n`. The variable `val1 `is not destroyed, as it was part of the global execution context.

    返回 `ret` 的值。随之本地上下文也被销毁，同时销毁的还有变量 `ret` 和 `n`。变量 `vall` 并不会被销毁，因为它是全局上下文的一部分。

11. Back to line 6. In the calling context, the number `12` is assigned to the `multiplied `variable.

    回到第 6 行。在调用上下文中，数字 `12` 被复制给变量 `multiplied`。

12. Finally on line 7, we show the value of the `multiplied` variable in the console.

    最后的第 7 行，我们在控制台中打印了变量 `multiplied` 的值

So in this example, we need to remember that a function has access to variables that are defined in its calling context. The formal name of this phenomenon is the lexical scope.

通过这个示例，我们可以知道函数可以访问调用上下文中的变量。这种现象的正式名称就叫做词法作用域。

## A function that returns a function

## 函数中返回另外一个函数

In the first example the function `addTwo` returns a number. Remember from earlier that a function can return anything. Let’s look at an example of a function that returns a function, as this is essential to understand closures. Here is the example that we are going to analyze.

第一个示例中函数 `addTwo` 返回了一个数字。早前，我们也说过函数可以返回任何类型。我们来看一个函数返回函数的示例，这对于理解闭包至关重要。下面的演示，我们会一点点分析它。

```js
 1: let val = 7
 2: function createAdder() {
 3:   function addNumbers(a, b) {
 4:     let ret = a + b
 5:     return ret
 6:   }
 7:   return addNumbers
 8: }
 9: let adder = createAdder()
10: let sum = adder(val, 8)
11: console.log('example of function returning a function: ', sum)
```

Let’s go back to the step-by-step breakdown.

我们开始一步步的分析下代码。

1. Line 1. We declare a variable `val` in the global execution context and assign the number `7` to that variable.

   第 1 行。我们在全局上下文中声明了变量 `val`，并且赋值数字 `7` 给变量

2. Lines 2–8. We declare a variable named `createAdder` in the global execution context and we assign a function definition to it. Lines 3 to 7 describe said function definition. As before, at this point, we are not jumping into that function. We just store the function definition into that variable (`createAdder`).

   第 2 - 8 行。在全局上下文中我们声明了一个名为 `createAdder` 的函数。第 3 - 7 行就是函数的具体定义。和之前一样，这个时候，我们并不会执行函数。我们只是把函数赋值给一个变量（`createAdder`）

3. Line 9. We declare a new variable, named `adder`, in the global execution context. Temporarily, `undefined` is assigned to `adder`.

   第 9 行。在全局上下文中，我们声明了新的变量 `adder`。同时，它的值是 `undefined`

4. Still line 9. We see the brackets `()`; we need to execute or call a function. Let’s query the global execution context’s memory and look for a variable named `createAdder`. It was created in step 2. Ok, let’s call it.

   还是第 9 行。我们看到了一个圆括号`()`；代表着我们需要调用函数。我们在全局上下文中搜索找了到名为 `createAdder` 的变量。它是在第 2 步创建的。好，我们来调用它。

5. Calling a function. Now we’re at line 2. A new local execution context is created. We can create local variables in the new execution context. The engine adds the new context to the call stack. The function has no arguments, let’s jump right into the body of it.

   调用函数。现在，我们回到第 2 行。一个新的上下文被创建。在新的上下文中我们创建了本地变量。同时，引擎也会把新的上下文压入到调用栈。这个函数没有参数，我们直接看它的内部。

6. Still lines 3–6. We have a new function declaration. We create a variable `addNumbers` in the local execution context. This important. `addNumbers` exists only in the local execution context. We store a function definition in the local variable named `addNumbers`.

   第 3 - 6 行。我们又声明了一个新的函数。在本地上下文中我们创建变量 `addNumber`。这个很重要。`addNumber` 只在本地上下文中有效。在本地上下文中我们定义了一个函数并命名为 `addNumber`

7. Now we’re at line 7. We return the content of the variable `addNumbers`. The engine looks for a variable named `addNumbers` and finds it. It’s a function definition. Fine, a function can return anything, including a function definition. So we return the definition of `addNumbers`. Anything between the brackets on lines 4 and 5 makes up the function definition. We also remove the local execution context from the call stack.

   现在，我来到第 7 行。我们返回了变量 `addNumber`。引擎会查找变量 `addNumber`，当然也会找到它。它是一个函数。好，函数可以返回任何东西，包括函数。因此，我们返回了 `addNumbers` 的函数体。在第 4 - 5 行就是函数的具体定义。同时，我们也把本地上下文从调用栈中移除。

8. Upon `return`, the local execution context is destroyed. The `addNumbers` variable is no more. The function definition still exists though, it is returned from the function and it is assigned to the variable `adder`; that is the variable we created in step 3.

   `return` 之后，本地上下文也随之销毁。变量 `addNumbers` 也不存在了。但是，函数的定义仍然存在，它通过 retrun 语句，并赋值给了变量 `adder`；这个变量，我们是在第 3 步创建的。

9. Now we’re at line 10. We define a new variable `sum` in the global execution context. Temporary assignment is `undefined`.

   来到第 10 行。在全局上下文中，我们定义了新的变量 `sum`。并分配了一个临时的值 `undefined`

10. We need to execute a function next. Which function? The function that is defined in the variable named `adder`. We look it up in the global execution context, and sure enough we find it. It’s a function that takes two parameters.

    接下来，我们需要执行函数。哪一个函数呢？就是名为 `adder` 的函数。我们在全局上下文中查找它，可以保证一定能找到它。这个函数需要两个参数

11. Let’s retrieve the two parameters, so we can call the function and pass the correct arguments. The first one is the variable `val`, which we defined in step 1, it represents the number `7`, and the second one is the number `8`.

    我们得到了两个参数，并把它们传递了函数。第一个是变量 `val`，我们在第 1 步定义的，它的值是数字 `7`，第二个参数是数字 `8`

12. Now we have to execute that function. The function definition is outlined lines 3–5. A new local execution context is created. Within the local context two new variables are created: `a` and `b`. They are respectively assigned the values `7` and `8`, as those were the arguments we passed to the function in the previous step.

    现在，我们来调用函数。这个函数是在第 3 - 5 行被定义的。一个新的本地上下文被创建。在这个上下文中有两个新的变量：`a` 和 `b`。它们的值分别是 `7` 和 `8`，这就是我们在上一步传递给函数的。

13. Line 4. A new variable is declared, named `ret`. It is declared in the local execution context.

    第 4 行。名为 `ret` 的变量被声明。它只存在本地上下文中。

14. Line 4. An addition is performed, where we add the content of variable `a` and the content of variable `b`. The result of the addition (`15`) is assigned to the `ret` variable.

    第 4 行。我们把变量 `a` 和 `b` 相加。相加后的结果（`15`）赋值给了变量 `ret`

15. The `ret` variable is returned from that function. The local execution context is destroyed, it is removed from the call stack, the variables `a`, `b` and `ret` no longer exist.

    变量 `ret` 通过函数返回。随之，与之相关的本地上下文被销毁，也从调用栈中被移除，变量 `a`、`b`、`ret` 也不存在了

16. The returned value is assigned to the `sum` variable we defined in step 9.

    返回的值被赋值给我们在第 9 步定义的变量 `sum`

17. We print out the value of `sum` to the console.

    最后，我们在控制台输出了 `sum` 的值

As expected the console will print 15. We really go through a bunch of hoops here. I am trying to illustrate a few points here. First, a function definition can be stored in a variable, the function definition is invisible to the program until it gets called. Second, every time a function gets called, a local execution context is (temporarily) created. That execution context vanishes when the function is done. A function is done when it encounters `return` or the closing bracket `}`.

如你所望，控制台输出的是 15。以上，我们经历了很多。有几点我需要指出。首先，函数体的定义可以存在一个变量中，直到程序调用了函数，函数的定义是不可见的。第二，每次函数被调用，一个新的本地上下文都会被创建（临时的）。当函数执行完，随之上下文也会消失。在遇到 `return` 语句或者闭合的大括号 `}` 就说明函数结束了。

## Finally, a closure

## 最后，闭包

Take a look a the next code and try to figure out what will happen.

看一下下面的代码，并指出将会发生什么。

```js
 1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

Now that we got the hang of it from the previous two examples, let’s zip through the execution of this, as we expect it to run.

现在，我们已经从前面两个示例中学到了窍门，我们来按照以上的模式来逐步的分析下代码。

1. Lines 1–8. We create a new variable `createCounter` in the global execution context and it get’s assigned function definition.

   第 1 - 8 行。我们在全局上下文中创建了新的函数变量 `createCounter`

2. Line 9. We declare a new variable named `increment` in the global execution context.

   第 9 行。我们在全局上下文中声明了变量 `increment`

3. Line 9 again. We need call the `createCounter` function and assign its returned value to the `increment` variable.

   还是第 9 行。我们需要调用函数 `createCounter`，并把结果赋值给你变量 `increment`。

4. Lines 1–8 . Calling the function. Creating new local execution context.

   第 1 - 8 行。调用函数期间，会创建新的本地上下文。

5. Line 2. Within the local execution context, declare a new variable named `counter`. Number `0` is assigned to `counter`.

   第 2 行。在本地上下文中声明了变量 `counter`。默认值是数字 `0`。

6. Line 3–6. Declaring new variable named `myFunction`. The variable is declared in the local execution context. The content of the variable is yet another function definition. As defined in lines 4 and 5.

   第 3 - 6 行。声明了名为 `myFunction` 的变量。这个变量是在本地上下文中声明的。这个变量也是一个函数。第4 - 5 行就是相应的函数体。

7. Line 7. Returning the content of the `myFunction` variable. Local execution context is deleted. `myFunction` and `counter` no longer exist. Control is returned to the calling context.

   第 7 行。直接放回了函数 `myFunction`。本地上下文销毁。`myFunction` 和 `counter` 也伴随被销毁。重新回调了调用上下文。

8. Line 9. In the calling context, the global execution context, the value returned by `createCounter` is assigned to `increment`. The variable increment now contains a function definition. The function definition that was returned by `createCounter`. It is no longer labeled `myFunction`, but it is the same definition. Within the global context, it is labeled`increment`.

   第 9 行。在调用上下文中，也就是全局上下文，`createCounter` 返回的值赋值给了变量 `increment`。此时的变量就是一个函数。这个函数是由 `createCounter` 返回的。虽然，不是 `myFunction`，但是，函数体内容是一致的。在全局上下文中，它就是 `imcrement`。

9. Line 10. Declare a new variable (`c1`).

   第 10 行。声明新变量（`c1`）

10. Line 10 (continued). Look up the variable `increment`, it’s a function, call it. It contains the function definition returned from earlier, as defined in lines 4–5.

    第 10 行。调用了函数 `increment`。这个函数是早期在第 4 - 5 行中定义的

11. Create a new execution context. There are no parameters. Start execution the function.

    创建新的上下文。只是执行函数，并没有参数。

12. Line 4. `counter = counter + 1`. Look up the value `counter` in the local execution context. We just created that context and never declare any local variables. Let’s look in the global execution context. No variable labeled `counter` here. Javascript will evaluate this as `counter = undefined + 1`, declare a new local variable labeled `counter` and assign it the number `1`, as `undefined` is sort of `0`.

    第 4 行。`counter = counter  + 1`。在本地上下文中查找变量 `counter`。我们只会创建上下文，绝对不会声明任何本地变量。我们看一下全局上下文。并没有变量 `counter`。因此，刚才的表达式等同于 `counter = undefined + 1`，声明一个本地变量 `counter`，并给它赋值数字 `1`，因为，`undefined` 有点类似 `0`。

13. Line 5. We return the content of `counter`, or the number `1`. We destroy the local execution context, and the `counter` variable.

    第 5 行。我们返回了 `counter ` 的值，也就是数字 `1`。同时，销毁本地上下文和变量 `counter`

14. Back to line 10. The returned value (`1`) gets assigned to `c1`.

    回到第 10 行。返回的值（`1`）赋值给了 `c1`

15. Line 11. We repeat steps 10–14, `c2` gets assigned `1` also.

    第 11 行。重复第 10 - 14 步，`c2` 也得到数字 `1`

16. Line 12. We repeat steps 10–14, `c3` gets assigned `1` also.

    第 12 行。重复第 10 - 14 步，`c3` 也得到数字 `1`

17. Line 13. We log the content of variables `c1`, `c2` and `c3`.

    第 13 行。我们打印变量 `c1`、`c2`、`c3` 的值

Try this out for yourself and see what happens. You’ll notice that it is not logging `1`, `1`, and `1` as you may expect from my explanation above. Instead it is logging `1`, `2` and `3`. So what gives?

自己试一下，看看会发生什么。你会看到，并不会像我上面说的那样输出 `1`、 `1` 和 `1`。而是，输出了 `1`、`2`、 `3`。为什么？  

Somehow, the increment function remembers that `counter `value. How is that working?

莫名其妙，函数 `increment` 记住了 `counter ` 的值。它是如何做到的？

Is `counter `part of the global execution context? Try `console.log(counter)` and you’ll get `undefined`. So that’s not it.

难道 `counter ` 是全局上下文的一部分？试着在控制台打印 `console.log(counter)`，你会看到输出 `undefined`。这说明它并不在全局上下文中。

Maybe, when you call `increment`, somehow it goes back to the the function where it was created (`createCounter`)? How would that even work? The variable `increment` contains the function definition, not where it came from. So that’s not it.

或许，当你调用 `increment` 时，作用域回到了函数被创建的地方（`createCounter`）？怎么会呢？变量 `increment` 只是有着相同的函数体，并不是 `createCounter`。因此，也不对。

So there must be another mechanism. **The Closure.** We finally got to it, the missing piece.

因此，必然有另外一种机制。**就是闭包**。我们最终说到它了。

Here is how it works. Whenever you declare a new function and assign it to a variable, you store the function definition, *as well as a closure*. The closure contains all the variables that are in scope at the time of creation of the function. It is analogous to a backpack. A function definition comes with a little backpack. And in its pack it stores all the variables that were in scope at the time that the function definition was created.

以下就是它的工作模式。每当你声明一个新函数，并把它赋值给一个变量，用来存储函数的定义，*这就是闭包*。闭包包含创建函数时作用域内的所有变量。这就类似一个背包。函数定义时附带一个小背包。这个背包存储了创建函数时作用域中所有的变量。

So our explanation above was *all wrong*, let’s try it again, but correctly this time.

因此，以上的分析是*错误*的，我们重新正确的分析一次。

```js
1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

1. Lines 1–8. We create a new variable `createCounter` in the global execution context and it get’s assigned function definition. Same as above.

   第 1 - 8 行。我们在全局上下文中创建了新的函数变量 `createCounter`。和上次一样

2. Line 9. We declare a new variable named `increment` in the global execution context. Same as above.

   第 9 行。我们在全局上下文中声明了变量 `increment`。和上次一样

3. Line 9 again. We need call the `createCounter` function and assign its returned value to the `increment` variable. Same as above.

   还是第 9 行。我们需要调用函数 `createCounter`，并把结果赋值给你变量 `increment`。和上次一样

4. Lines 1–8 . Calling the function. Creating new local execution context. Same as above.

   第 1 - 8 行。调用函数期间，会创建新的本地上下文。和上次一样

5. Line 2. Within the local execution context, declare a new variable named `counter`. Number `0` is assigned to `counter`. Same as above.

   第 2 行。在本地上下文中声明了变量 `counter`。默认值是数字 `0`。和上次一样

6. Line 3–6. Declaring new variable named `myFunction`. The variable is declared in the local execution context. The content of the variable is yet another function definition. As defined in lines 4 and 5. Now we also create a *closure* and include it as part of the function definition. The closure contains the variables that are in scope, in this case the variable `counter` (with the value of `0`).

   第 3 - 6 行。声明了名为 `myFunction` 的变量。这个变量是在本地上下文中声明的。这个变量也是一个函数。第4 - 5 行就是相应的函数体。现在，我创建了一个*闭包*，它是函数的一部分。闭包包含当前作用域的中的变量，在这个示例中变量是 `counter`（它的值是`0`）。

7. Line 7. Returning the content of the `myFunction` variable. Local execution context is deleted. `myFunction` and `counter` no longer exist. Control is returned to the calling context. So we are returning the function definition *and its closure*, the backpack with the variables that were in scope when it was created.

   第 7 行。直接放回了函数 `myFunction`。本地上下文销毁。`myFunction` 和 `counter` 也伴随被销毁。重新回调了调用上下文。因此，我们得到了一个函数和*闭包*，这个背包中包含了函数定义时作用域中的所有变量。

8. Line 9. In the calling context, the global execution context, the value returned by `createCounter` is assigned to `increment`. The variable increment now contains a function definition (and closure). The function definition that was returned by `createCounter`. It is no longer labeled `myFunction`, but it is the same definition. Within the global context, it is called `increment`.

   第 9 行。在调用上下文中，也就是全局上下文，`createCounter` 返回的值赋值给了变量 `increment`。此时的变量就是一个函数（也包括闭包）。这个函数是由 `createCounter` 返回的。虽然，不是 `myFunction`，但是，函数体内容是一致的。在全局上下文中，它就是 `imcrement`。

9. Line 10. Declare a new variable (`c1`).

   第 10 行。声明新变量（`c1`）

10. Line 10 (continued). Look up the variable `increment`, it’s a function, call it. It contains the function definition returned from earlier, as defined in lines 4–5. (and it also has a backpack with variables)

    第 10 行。调用了函数 `increment`。这个函数是早期在第 4 - 5 行中定义的。（并且变量也有一个背包）

11. Create a new execution context. There are no parameters. Start execution the function.

    创建新的上下文。只是执行函数，并没有参数。

12. Line 4. `counter = counter + 1`. We need to look for the variable `counter`. Before we look in the *local* or *global* execution context, let’s look in our backpack. Let’s check the closure. Lo and behold, the closure contains a variable named `counter`, its value is `0`. After the expression on line 4, its value is set to `1`. And it is stored in the backpack again. The closure now contains the variable `counter` with a value of `1`.

    第 4 行。`counter = counter + 1`。我们需要查询变量 `counter`。在此之前，我在*本地*或者*全局*上下文中查找，这次，我们来看一下背包，闭包。你瞧，闭包中包含一个名为 `counter` 的变量，它的值是 `0`。经过第 4 行的计算后，它的值变成 `1`。它也重新存储在背包中。这时闭包中的变量 `counter ` 值变成了 `1`。

13. Line 5. We return the content of `counter`, or the number `1`. We destroy the local execution context.

    第 5 行。我们返回了 `counter ` 的值，也就是数字 `1`。同时，销毁本地上下文。

14. Back to line 10. The returned value (`1`) gets assigned to `c1`

    回到第 10 行。返回的值（`1`）赋值给了 `c1`

15. Line 11. We repeat steps 10–14. This time, when we look at our closure, we see that the `counter` variable has a value of 1. It was set in step 12 or line 4 of the program. Its value gets incremented and stored as `2` in the closure of the increment function. And `c2` gets assigned `2`.

    第 11 行。重复第 10 - 14 步。这次，当我们查看闭包时，看到变量 `counter` 的值是 `1`。这是因为第 12 步导致的。这时，它的值再次被递加得到了 `2`，并存储在闭包中。同时，`c2` 的值也是 `2`。

16. Line 12. We repeat steps 10–14, `c3` gets assigned `3`.

    第 12 行。重复第 10 - 14 步，`c3` 也得到数字 `3`

17. Line 13. We log the content of variables `c1`, `c2` and `c3`.

    第 13 行。我们打印变量 `c1`、`c2`、`c3` 的值

So now we understand how this works. The key to remember is that when a function gets declared, it contains a function definition and a closure. The closure is a collection of all the variables in scope at the time of creation of the function.

现在，我们已经理解了它是如何工作的了。关键点在于，当函数被声明时，它同时包含函数体和一个闭包。这个闭包会收集创建函数时作用域中的所有变量。

You may ask, does any function has a closure, even functions created in the global scope? The answer is yes. Functions created in the global scope create a closure. But since these functions were created in the global scope, they have access to all the variables in the global scope. And the closure concept is not really relevant.

你或许会问，所有的函数都有闭包吗？即使，是在全局作用域中声明？是的。全局作用创建的函数也会创建闭包。但是，由于函数是在全局作用域中被创建，因此，它们可以访问全局作用域中所有的变量。这并不完全跟闭包有关。

When a function returns a function, that is when the concept of closures becomes more relevant. The returned function has access to variables that are not in the global scope, but they solely exist in its closure.

当函数返回一个函数时，闭包的就比较重要了。返回的函数可访问那些不在全局作用域，只存在于闭包中的变量。

## Not so trivial closures

## 非同小可的闭包

Sometimes closures show up when you don’t even notice it. You may have seen an example of what we call partial application. Like in the following code.

有时，在你不经意间就会出现闭包。你或许在我们应用中看到过类似的代码。

```js
let c = 4
const addX = x => n => n + x
const addThree = addX(3)
let d = addThree(c)
console.log('example partial application', d)
```

In case the arrow function throws you off, here is the equivalent.

示例中的三角函数对你来说有点难以理解，它与下面的代码效果一样。

```js
let c = 4
function addX(x) {
  return function(n) {
     return n + x
  }
}
const addThree = addX(3)
let d = addThree(c)
console.log('example partial application', d)

```

We declare a generic adder function `addX` that takes one parameter (`x`) and returns another function.

我们声明了一个普通的函数 `addX`，它需要一个参数 `x` ，并返回了另外一个函数。

The returned function also takes one parameter and adds it to the variable `x`.

这个返回的函数也需要一个参数，并用它与变量 `x` 相加。

The variable `x` is part of the closure. When the variable `addThree` gets declared in the local context, it is assigned a function definition and a closure. The closure contains the variable `x`.

变量 `x` 就是闭包的一部分。当变量 `addThree` 在本地上下文中声明时，它得到了一个函数和一个闭包。闭包中就有变量 `x`。

So now when `addThree` is called and executed, it has access to the variable `x` from its closure and the variable `n` which was passed as an argument and is able to return the sum.

因此，当调用和执行函数 `addThree` 时，它可以通过闭包访问变量 `x` 和做为参数传递的 `n`，最终返回了两者之和。

In this example the console will print the number `7`.

在这个示例中，控制台将会输出数字 `7`。

## Conclusion

## 总结

The way I will always remember closures is through **the backpack analogy**. When a function gets created and passed around or returned from another function, it carries a backpack with it. And in the backpack are all the variables that were in scope when the function was declared.

为了记住闭包，我把它比喻为**背包**。当一个函数被创建并传递或者通过另外一个函数返回，它就会包含一个背包。背包中包含函数声明时作用域中所有的变量。

> If you enjoyed reading this, don’t forget the applause. 👏
> *Thank you.*
>
> 如果，你喜欢这篇文章，不要吝啬你的赞扬 👏
>
> *谢谢*