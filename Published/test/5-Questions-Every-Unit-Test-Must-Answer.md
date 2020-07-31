# 5 Questions Every Unit Test Must Answer

## 每个单元测试必须解决的 5 个问题

> 原文地址: <https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d/>   
> **本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/2000/gradv/29/81/40/1*tSDpGcII4cmI0BPKwDMgIg.jpeg)

## Most Developers Don’t Know How to Test

## 大多开发者不知道如何测试

Every developer knows we should write unit tests in order to prevent defects from being deployed to production.

为了避免对生产环境带来影响，每个开发者都知道需要编写单元测试。

What most developers don’t know are the essential ingredients of **every unit test**. I can’t begin to count the number of times I’ve seen a unit test fail, only to investigate and discover that I have absolutely no idea what feature the developer was trying to test, let alone how it went wrong or why it matters.

大多数开发者并不知道**每个单元测试**的基本组成部分。我不记得见过多少次失败的单元测试，调查发现我无法理解开发者想测试什么，更不用说，它是如何错误的，或者为什么那么重要。

In a recent project of mine, we let a gigantic swath of unit tests enter the test suite with **absolutely no description whatsoever** of the test’s purpose. We have a great team, so I let my guard down. The result? We still have a ton of unit tests that only the author can really make sense of.

在我最近的一个项目中，我引入了大量的，没有**任何描述说明**的测试代码。我们是一个庞大的团队，因此，我放松了警惕。结果呢？我们有大量的单元测试只有对应的开发者自己明白干了什么。

Luckily, we’re completely redesigning the API, and we’re going to throw the entire suite away and start from scratch — otherwise, this would be **priority #1** on my fix list.

很幸运，我们完全重新设计了 API，我们完全抛弃了之前的方案，从头开始；也就是说，这将是我**最重要**的事情。

Don’t let this happen to you.

不要让这些事情发生在你身上。

## Why Bother with Test Discipline?
## 为什么要单元测试呢？

Your tests are your first and best line of defense against software defects. Your tests are more important than linting & static analysis (which can only find a subclass of errors, not problems with your actual program logic). Tests are as important as the implementation itself (all that matters is that the code meets the requirement — how it’s implemented doesn’t matter at all unless it’s implemented poorly).

测试是抵御软件缺陷的第一道防线，也是最基本的防线。测试比 Lint 和静态分析（它们只能发现少量的错误，并不能发现程序中的逻辑问题）更加重要。测试和实现需求一样重要（最重要的是代码是否满足需求，除非实现的非常差，如何实现并不重要）。

Unit tests combine many features that make them your secret weapon to application success:

单元测试包含了很多功能，这使它成了应用成功的秘密武器：

1. **Design aid:** Writing tests first gives you a clearer perspective on the ideal API design.

   **辅助设计：**编写单元测试可以让你设计出更加清晰理想的 API。

2. **Feature documentation (for developers):** Test descriptions enshrine in code every implemented feature requirement.

   **功能文档（针对开发者）：**测试中包含了所有的功能需求。

3. **Test your developer understanding:** Does the developer understand the problem enough to articulate in code all critical component requirements?

   **测试开发者对需求的理解：**开发者是否对所有的需求有足够的了解？以便实现更好的代码。

4. **Quality Assurance:** Manual QA is error prone. In my experience, it’s impossible for a developer to remember all features that need testing after making a change to refactor, add new features, or remove features.

   **质量保障：**手动 QA 容易出错。以我的经验，在重构、添加新功能或者移除功能后，开发者不能记住所有需要测试的功能点。

5. **Continuous Delivery Aid:** Automated QA affords the opportunity to automatically prevent broken builds from being deployed to production.

   **辅助持续交付：**自动 QA 可以自动的避免破环生产环境。

Unit tests don’t need to be twisted or manipulated to serve all of those broad-ranging goals. Rather, it is in the essential nature of a unit test to satisfy all of those needs. These benefits are all side-effects of a well-written test suite with good coverage.

单元测试并不是通过扭曲或者操纵来达到某些目的。相反，它本质上是为了满足需求。良好的单元测试可以带来更好的测试覆盖率。

## The Science of TDD

## TDD 科学

The evidence says:

- **TDD can reduce bug density.**
- **TDD can encourage more modular designs** (enhancing software agility/team velocity).
- **TDD can reduce code complexity.**

**Says science:** There is *significant empirical evidence that TDD works*.

证据表明：

- **TDD 可以减少 bug 的密度**
- **TDD 可以促进更好的模块化设计**（提升软件的敏捷性/团队效率）
- **TDD 可以减少代码的复杂度**

**科学证明：** *有大量的经验证明 TDD 带来的好处*

## Write Tests First
## 测试优先

Studies from Microsoft Research, IBM, and Springer tested the efficacy of test-first vs test-after methodologies and consistently found that a test-first process produces better results than adding tests later. It is resoundingly clear: Before you implement, write the test.

来自微软、IBM 和 Springer 研究证明，一致的认为测试优先比延后测试可以产出更好的结果。现在非常的明确：在具体实现功能之前，需要先编写单元测试。

> Before you implement,
> **write the test.**
>
> 实现具体功能之前，
>
> **首先，需要编写单元测试**

## What’s in a Good Unit Test?

## 良好的单元测试应该是什么样子呢？

OK, so TDD works. Write tests first. Be more disciplined. Trust the process… We get it. But **how do you write a good unit test?**

好，遵循 TDD 规则。先写测试。更规范。相信它...我知道了。但是**如何编写一个良好的单元测试呢？**

We’re going to look at a very simple example from a real project to explore the process: The *`compose()`* function from the [Stamp Specification](https://github.com/stampit-org/stamp-specification).

我们通过一个真实的场景来逐步探索：来自 [Stamp 规范](https://github.com/stampit-org/stamp-specification) 的 *`compose()`* 方法

We’re going to [use tape](https://medium.com/javascript-scene/why-i-use-tape-instead-of-mocha-so-should-you-6aa105d8eaf4) for the tests because of its crystal clarity and essential simplicity.

我们将会使用[tape](https://medium.com/javascript-scene/why-i-use-tape-instead-of-mocha-so-should-you-6aa105d8eaf4)作为测试框架，因为它非常清晰简单。

Before we can answer how to write a good unit test, first we have to understand how unit tests are used:

在回答如何编写良好单元测试之前，首先，我们需要明白如何使用单元测试：

- **Design aid:** written during design phase, *prior to implementation*.
- **辅助设计：**在设计阶段编写测试，*也就是说功能实现之前*
- **Feature documentation & test of developer understanding:** The test should provide a *clear description of the feature being tested.*
- **功能文档 & 测试开发者对需求的理解：**单元测试应该对*所测试的功能有一个清晰的描述*
- **QA/Continuous Delivery:** The tests should halt the delivery pipeline on failure and *produce a good bug report when they fail.*
- **QA/持续交付：**单元测试在交付过程中失败的话，应该立即停止交付，*并提供一个良好的 bug 报告*

## The Unit Test as a Bug Report
## 单元测试就是一个 Bug 报告

When a test fails, that test failure report is often your first and best clue about exactly what went wrong — the secret to tracking down a root cause quickly is knowing where to start looking. That process is made much easier when you have a really clear bug report.

每当测试失败，失败报告都是有关错误的最好的线索 — 快速跟踪根本原因的秘密就是知道从哪开始。如果，你有一个清晰 bug 报告，这个过程会让你更加容易。

> A failing test should read like a high-quality bug report.
>
> 一个失败的单元测试看起来应该像是一个高质量的 bug 报告。

## What’s in a good test failure bug report?

## 一个良好的错误报告应该包含哪些内容？

1. **What were you testing?**

   **测试了什么内容？**

2. **What should it do?**

   **应该是什么？**

3. **What was the output (actual behavior)?**

   **输出了什么（真实的行为）？**

4. **What was the expected output (expected behavior)?**

   **期望输出什么（期望的行为）？**

![](https://miro.medium.com/max/1400/1*jhYZ6dcMUKAhFyhsWJ6qGg.png)

## Start by answering “what are you testing?”

## 开始回答：测试了什么内容？

- **What component aspect** are you testing?
- 你在测试**组件的哪些内容**？
- **What should the feature do?** What specific behavior requirement are you testing?
- **功能应该是什么样的？**哪些特殊的行为需求你需要测试？

The *`compose()`* function takes any number of stamps (composable factory functions) and produces a new stamp.

可以传递给函数 *`compose()`* 多个 stamps，然后，会产出一个新的 stamp。

To write this test, we’re going to work backwards from the end goal of any single test: To test a specific behavior requirement. In order for this test to pass, what specific behavior must the code produce?

为了编写测试用例，我们需要逆向思考：测试特定的行为需求。为了让测试通过，代码应该提供什么样的行为？

## What should the feature do?

## 功能应该是什么样的？

I like to start by writing a string. Not assigned to anything. Not passed into any function. Just a clear focus on a specific requirement that the component must satisfy. In this case, we’ll start with the fact that the *`compose()`* function should return a function.

我喜欢从一个字符串开始。不分配任何内容。不传递给任何函数。只是明确组件需要满足的特定功能需求。在这个示例中，我们将从 *`compose()`* 会返回一个函数这一事实开始。

A simple, testable requirement:

一个简单的测试需求：

```js
'compose() should return a function.'
```

Now we’ll skip some stuff and flesh out the rest of the test. This string is serving as our goal. Stating it beforehand helps us keep our eye on the prize.

现在， 我们需要跳过一些东西，并且完善剩余的测试内容。这个字符串的内容就是我们的目的。事先说明它，这有助于我们更加关注功能需求。

## What component aspect are we testing?

## 我们需要测试组件哪些内容？

What you mean by “component aspect” will vary from test to test, depending on the granularity required to provide adequate coverage of the component under test.

你所说的“组件内容”因测试而异，具体取决于测试组件所属的覆盖率。

In this case, we’re going to test the return type of the *`compose()`* function to make sure it returns the right kind of thing, as opposed to *`undefined`* or nothing at all because it throws when you run it.

在这个示例中，我们需要测试函数 *`compose()`* 运行后返回的类型，确保它返回了正确的内容，而不是 *`undefined`* 或者什么都没有。

Let’s translate this question into test code. The answer goes into the test description. This step is also where we’ll make our function call and pass the callback function that the test runner will invoke when the tests run:

让我们把这些问题用代码来描述。用测试来回答。这一步我们会调用我们的函数，然后，传递一个回调函数，测试运行时会调用这个回调函数：

```js
test('<What component aspect are we testing?>', assert => {
});
```

In this case, we’re testing the output of the compose function:

这个示例，我们将会测试函数 compose 的输出：

```js
test('Compose function output type.', assert => {
});
```

And of course we still need our first description. It goes inside the callback function:

当然，我们还是需要一个描述。它在回调函数中：

```js
test('Compose function output type.', assert => {
  'compose() should return a function.'
});
```

## What is the Output (Expected and Actual)?

## 输出什么内容（期望输出和真实输出）？

*`equal()`* is my favorite assertion. If the only available assertion in every test suite was *`equal()`,* almost every test suite in the world would be better for it. Why?

*`equal()`* 是我最喜欢的断言方法。如果，测试框架中唯一有效的断言就是 *`equal()`*，那么，每个测试框架都会更好。为什么？

Because *`equal()`*, by nature answers **the two most important questions every unit test must answer**, but most don’t:

这是因为，*`equal()`* 可以很自然的回答**单元测试必须回答的两个最重要的问题**，但是，大多数并没有：

- **What is the actual output?**
- **真实输出的内容是什么？**
- **What is the expected output?**
- **期望输出的内容是什么？**

If you finish a test without answering those two questions, you don’t have a real unit test. You have a sloppy, half-baked test.

如果，你的测试中没有回答这两个问题，这说明你的单元测试并不合格。你做了一个草率的、不成熟的测试。

If you take only one thing from this article, let it be this:

如果，你用一句话总结这篇文章，那么就是：

> Equal is your new default assertion.
> It is the staple of every good test suite.
>
> Equal 是你默认的断言。
>
> 它是每个测试框架的主要的内容。

All those fancy assertion libraries with hundreds of different fancy assertions are **destroying the quality of your tests.**

所有的高级断言框架有着数百种不同的断言，这正是**消弱单元测试的原因所在。**

## A Challenge
## 挑战

Want to get much better at writing unit tests? For the next week, try writing every single assertion using *`equal()`* or *`deepEqual()`,* or their equivalents in your assertion library of choice. Don’t worry about the quality impact on your suite. My money says that the exercise will *improve it dramatically.*

是否想要编写更好的单元测试？接下来的几周，编写单元测试时尝试着只用 *`equal()`* 或者 *`deepEqual()`*，或者你所用测试框架中相等的断言。不要担心这对测试质量的影响，我敢保证这将会*极大的改善你的测试用例*。

What does this look like in code?

这些代码看起来像什么？

```js
  const actual = '<what is the actual output?>';
  const expected = '<what is the expected output?>';
```

The first question really does double duty in a test failure. By answering the question, your code also answers another:

第一个问题作为测试具有双重责任。为了回答这个问题，你同时还需要回到另外一个问题：

```js
  const actual = '<how is the test reproduced?>';
```

It’s important to note that **the *`actual`* value must be produced by exercising some of the component’s public API.** Otherwise, the test has no value. I’ve seen test suites that are so overwhelmed with mocks and stubs and bells and whistles that some of the tests never exercised any of the code supposedly being tested.

需要注意的是：***`actual`* 的值必须是通过组件的某些公共 API 产出的。**否则，测试就没有意义。我看到过很多的测试案例里面充斥各种花里胡哨的内容，但是，并没有达到测试的效果。

Let’s return to the example:

我们回到示例中：

```js
const actual = typeof compose();
const expected = 'function';
```

You could build an assertion without specifically assigning values to variables called *`actual`* and *`expected`,* but I recently started to specifically assign values to variables called *`actual`* and *`expected`* **in every test** and found that **it made my tests easier to read.**

你可以构建一些断言，但是，并不一定非得把它们命名为  *`actual`* 和 *`expected`*，但是，**在每个测试中**我开始把这些变量命名为  *`actual`* 和 *`expected`*，然后，我发现**这让我的测试代码更容易阅读。**

See how it clarifies the assertion?

看一下这个断言多么的清晰？

```js
assert.equal(actual, expected,
    'compose() should return a function.');
```

 It separates the “how” from the “what” in the test body.

它将测试中的 “how” 和 “what” 分离开来。

- Want to know **how we got the results?** Look at the *variable assignments*.
- 想知道**结果是什么？**看一下*变量对应的值。*
- Want to know **what we’re testing for?** Look at the *assertion’s description*.
- 想知道**我们需要测试什么？**看一下*断言内容。*

The result is that the test itself reads as easily as a high quality bug report.

这样测试的结果就像一个高质量的 bug 报告，更加容易阅读。

**Let’s look at the whole thing in context:**

**我们来看一下完整的内容：**

```js
import test from 'tape';
import compose from '../source/compose';

test('Compose function output type', assert => {
  const actual = typeof compose();
  const expected = 'function';

  assert.equal(actual, expected,
    'compose() should return a function.');

  assert.end();
});
```

Next time you write a test, remember to answer all the questions:

下次，你在写测试的时候，记住以下这几个问题：

1. **What are you testing?**

   **你需要测试什么？**

2. **What should it do?**

   **应该怎么做？**

3. **What is the actual output?**

   **真正输出的内容是什么？**

4. **What is the expected output?**

   **期望输出的内容是什么？**

5. **How can the test be reproduced?**

   **如何重现测试？**

The last question is answered by the code used to derive the *`actual`* value.

最后一个问题可以通过代码得到 *`actual`* 的值来回答。

## A Unit Test Template:

## 单元测试模版：

```js
import test from 'tape';

// For each unit test you write,
// answer these questions:
test('What component aspect are you testing?', assert => {
  const actual = 'What is the actual output?';
  const expected = 'What is the expected output?';

  assert.equal(actual, expected,
    'What should the feature do?');

  assert.end();
});

```

There’s a whole lot more to using unit tests well, but knowing how to write a good test goes a long way.

有很多方式可以编写良好的单元测试，但是，如何编写良好的单元测试还有很长的路要走。

## Next Steps

## 下一步

[Join TDD Day for a full day of recorded webcast TDD curriculum](https://tddday.com/). TDD Day is the ideal way to onboard your team into advanced Test Driven Development skills. Learn about different kinds of tests and the roles they play, how to write more testable software, and [how TDD made me a better developer](https://medium.com/javascript-scene/tdd-changed-my-life-5af0ce099f80), and how it can do the same for you.

[加入 TDD Day 可以观看很多有关 TDD 的直播课程](https://tddday.com/)。TDD Day 可以带领你的团队了解更多有关 TDD 的高级技能。学习各种测试及其作用，如何编写可测试的软件，以及[ TDD 如何使我成为更好的开发者](https://medium.com/javascript-scene/tdd-changed-my-life-5af0ce099f80)，以及如何让你受益。

> Watch the webcast recording,
> TDD in ES6 & React
>
> & much more with the
> [Lifetime Access Pass](https://ericelliottjs.com/product/lifetime-access-pass/)
>
> 通过网络直播可以观看有关 ES6 & React 的 TDD 内容
>
> 更多内容在 [Lifetime Access Pass](https://ericelliottjs.com/product/lifetime-access-pass/)