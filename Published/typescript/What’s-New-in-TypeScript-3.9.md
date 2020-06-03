# What’s New in TypeScript 3.9?

## TypeScript 3.9 中的新特性

> 原文地址: <https://medium.com/better-programming/whats-new-in-typescript-3-9-70e3d2eabe26/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*b69tQHlar0EXAv39HKsgTw.jpeg)

TypeScript just released its second release for the year on May 12. It’s version 3.9, which is now the stable version. In this article, I’m going to point out some of the new and exciting features of TypeScript 3.9.

5 月 12 号 TypeScript 发布了今年的第二个版本 v3.9。这是一个稳定版本。这篇文章，我将会介绍 v3.9 中引入的新功能。

## @ts-expect-error

Let’s take an example where we define a function that takes two strings as parameters.

我们先看以下的示例，我们定义了一个有两个字符串参数的函数。

```ts
printName(firstName: string, lastName: string) { 
    console.log(firstName);
    console.log(lastName);
    assert(typeof firstName === "string");   
    assert(typeof lastName === "string");
}
```

Usually, TypeScript users will get a helpful red squiggle and an error message when they misuse this function, and JavaScript users will get an assertion error. But what will happen if we write a unit test to check this functionality?

通常，如果，TypeScript 用户错误的使用这个函数，他们会得到一个非常有用的错误提示信息，然而，JavaScript 用户将会得到一个断言错误信息。但是，如果我们为这个函数编写测试用例会发生什么呢？

```
expect(() => { 
   printName(1234, 5678); 
}).toThrow();
```

If our test is written in TS, it’ll throw an error like this:

如果，我们的测试用 TS 语法，将会抛出以下的错误信息：

```
printName(1234, 5678);
// ~~~ 
// error: Type ‘number’ is not assignable to type ‘string’.
```

As a solution for this, version 3.9 of TypeScript brings a new feature: `// @ts-expect-error` comments. If we put this comment before a code line as a prefix, TypeScript won’t report the error. But If there’s no error, TypeScript will inform that there’s an unnecessary comment in our code, like this: `Unused '@ts-expect-error' directive`.

为了解决这种问题，TypeScript 3.9 引入了新的功能：`// @ts-expect-error` 注释。如果，我们把这个注释放在代码行前面，TypeScript 就会忽略这个错误。但是，如果没有错误，TypeScript 就会提示代码中有不必要的注释，比如：`Unused '@ts-expect-error' directive`。

Another use of this `//@ts-expect-error` comment is we can use it as a suppression comment. `// @ts-ignore` is an existing comment that’s used as a suppression comment — the main difference between these two is `// @ts-ignore` will inform if the following line is error-free.

我们还可以把 `// @ts-expect-error` 做为抑制注释使用。`// @ts-ignore` 是一个早已存在的抑制注释。两者之间的区别是：~~`// @ts-ignore` 在接下来的代码行中没有错误会提示~~

（*译者注：两者之间的区别是：两者都会忽略接下来代码行中的错误，只是在没有错误的情况下 `// @ts-ignore`  不会提示：**有不必要的注释***）

## Speed Improvements
## 速度提升
![](https://miro.medium.com/max/1400/0*6LoGdsn6xLXdu2Rl)

With the earlier versions of TypeScript, there were many complaints about the compilations and editing speed with packages like Material-UI.

TypeScript 早期版本中使用类似 Material-UI 这种库存在很多的编译速度问题。

With the new release, the TypeScript developing team has addressed this issue by optimizing several pathological cases involving large unions, intersections, conditional types, and mapped types and reduced 40% of the time in the Material-UI compilation. Also, they’ve worked on file renaming functionalities regarding editors like Visual Studio Code.

新版本中，TypeScript 开发团队针对这类问题优化了大型联合、交叉、条件和映射类型，为 Material-UI 的编译节省了 40% 的时间。同时，他们还致力于有关 Visual Studio Code 等编辑器的文件重命名功能。

## CommonJS Auto-Imports in JavaScript

## 自动引入 CommonJS 模块

Previously, TypeScript always expected ECMAScript-style imports regardless of your file type. But most developers are using the CommonJS-style `require();` import instead of the ECMAScript-style modules when writing JavaScript files.

之前的版本，TypeScript 不管文件是什么类型只能引入 ECMAScript-style 的文件。但是，很多开发者还是使用 CommonJS-style `require();`，而没有使用 ECMAScript-style 的导入功能。

ECMAScript style: 

```js
import * as fs from "fs";
```

CommonJS style: 

```js
const fs = require("fs");
```

With the latest release, TypeScript now automatically detects the types of imports you’re using to keep your file’s style clean.

最新版本中，TypeScript 会自动检测导入的类型用于保证代码的整洁。

## Improvements in Inference and Promise.all

## 改善类型推断和 Promise.all

In version 3.7, TypeScript came up with an update to the declarations of functions like `Promise.all` and `Promise.race`. But there were a few complications with this update since it caused regressions when mixing in values with `null` or `undefined`. An example is given below.

TypeScript v3.7 中更新了 `Promise.all` 和 `Promise.race` 的声明。但是，这次更新也带来了一些问题，因为，在同 `null` 或者 `undefined` 混合使用时会引起编译报错。下面通过示例演示一下：

```ts
interface Bird{ 
   fly(): void 
} 
interface Fish{ 
   singKissFromARose(): void 
} 
async function animalBehaviours(birdBehaviour: Promise<Bird>,     fishBehaviour: Promise<Fish| undefined>) { 
   let [bird, fish] = await Promise.all([birdBehaviour,    fishBehaviour]); 
   bird.fly();
   // ~~~~ 
   // Object is possibly ‘undefined’. 
}
```

Although `fishBehaviour` is the one containing `undefined`, it somehow has affected the `birdBehaviour` to include `undefined` as well. So this issue is also fixed with the 3.9 release of TypeScript.

尽管，`fishBehaviour` 可能是 `undefined`，但是，还是会影响到 `birdBehaviour`。这种问题在 v3.9 中修复了。

## Code Actions Preserve New Lines

## 代码重构保留空白行

![](https://miro.medium.com/max/1400/0*eAJT01XxXK0PLh0w)

Many developers like to keep new lines between separate functionality code lines, but earlier TypeScript code refactoring didn’t preserve new lines. It almost removed all blank lines between code lines. With the latest release, TypeScript has worked on this issue, and now TypeScript code refactoring is preserving new lines in your code after running a refactor.

很多开发者喜欢在每个功能代码块之间保留空白行，但是，早期的 TypeScript 代码重构不会保留空白行。它会移除所有的空白行。最新版本中，TypeScript 已经修复了这种问题，代码重构后会保留空白行。

（*译者注：这个地方的提到的代码重构是指一些 IDE 附带的一些代码重构功能，比如：[VS Code](https://code.visualstudio.com/docs/editor/refactoring)*）

## Quick Fixes for Missing Return Expressions

## 快速修复缺失的 return 语句

Sometimes we might forget to return the value from a function at the end of that function. So TypeScript is now providing a quick fix solution to add missing `return` statements.

有时候，我们会忘记在函数结束时返回对应的值。现在，TypeScript 提供快速修复的方案，自动添加 `return` 语句。

## Uncalled Function Checks in Conditional Expressions

## 在条件语句中检查未调用的函数

After version 3.7, TypeScript checked for uncalled function checks in `if` conditions and reported errors. With the 3.9 release, this error-checking has further extended to ternary conditional statements as well.

在 v3.7 之后，TypeScript 会检查 `if` 语句中未调用的函数并提示错误。在 v3.9 中，这一功能也支持三元运算符了。

## Additional Minor Changes

## 其它的改变

Other than these changes, there are some minor changes like:

- `}` and `>` are now invalid JSX text characters
- `export *` is retained
- Getters and setters are no longer enumerable
- Type parameters which extend `any` are no longer acting as `any`
- More `libdom.d.ts` refinements

相比以上的改变，还有一些微不足道的改变

- 在 JSX 中 `}` 和 `>` 被认为无效
- `export *` 被保留
- Getter 和 Setter 不在可枚举
- Type parameters which extend `any` are no longer acting as `any`
- `libdom.d.ts` 的改进

Overall, these are the changes that we can see in the latest release of TypeScript. So if you’re a TypeScript lover, it’s always good to keep up to date with new [updates ](https://devblogs.microsoft.com/typescript/announcing-typescript-3-9)that can make your coding life easier.

总的来说，这些改变在最新版本中都可以看到。因此，如果你喜欢 TypeScript，保持更新到[最新的](https://devblogs.microsoft.com/typescript/announcing-typescript-3-9)版本，可以让编写代码更高效。