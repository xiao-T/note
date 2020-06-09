# Unit Testing React Components

## React 组件的单元测试

> 原文地址: <https://medium.com/javascript-scene/unit-testing-react-components-aeda9a44aae2/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/4096/1*RzR_S8UJeDn0b_sEQa2V8Q.jpeg)

Unit testing is a great discipline which can lead to [40%-80% reductions in production bug density](https://ieeexplore.ieee.org/document/4163024). Unit testing also has several other important benefits:

单元测试是一门非常伟大的学科，它可以[减少40%-80%的 bug](https://ieeexplore.ieee.org/document/4163024)。同时，还有以下几个重要的好处：

- Improves your application architecture and maintainability.
- 改善应用的结构和可维护性。
- Leads to better APIs and composability by focusing developers on the developer experience (API) before implementation details.
- 在具体实现之前，让开发者更加关注开发体验，从而实现更好的 API 和更好的组合能力。
- Provides quick feedback on file-save to tell you whether or not your changes worked. This can replace `console.log()`and clicking around in the UI to test changes. Newcomers to unit testing might spend an extra 15% - 30% on the TDD process as they figure out how to test various components, but experienced TDD practitioners may experience savings in implementation time using TDD.
- 每当保存文件不管是否正确，都会提供快速的反馈。这可以避免使用 `console.log()` 和直接点击UI验证改变是否正确。作为一个单元测试新手可能需要在 TDD 流程上花费额外的15% - 30% 的时间了解如何测试各种组件，但是，TDD 经验丰富的开发者会节省具体实现的时间。
- Provides a great safety net which can enhance your confidence when its time to add features or refactor existing features.
- 当需要添加功能或者重构现有功能时，提供强大的安全保障。

But some things are easier to unit test than others. Specifically, unit tests work great for [pure functions](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976): Functions which given the same input, always return the same output, and have no side-effects.

有些情况单元测试相对比较容易。举例来说，单元测试对[纯函数](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)更加有效：一个函数，也就意味着同样的输入总会得到同样的输出，不会有副作用。

Often, UI components don’t fall into that category of things which are easy to unit test, which makes it harder to stick to the discipline of TDD: Writing tests first.

但是，UI 组件并不属于这一类，这使得 TDD 更加艰难，需要先编写测试。

Writing tests first is necessary to achieve some of the benefits I listed: architecture improvements, better developer experience design, and quicker feedback as you’re developing your app. It takes discipline and practice to train yourself to use TDD. Many developers prefer to tinker before they write tests, but if you don’t write tests first, you rob yourself of a lot of the best features of unit tests.

对于我列出好处中有一些先编写测试用例是必要的，比如：在开发应用过程中，改善结构、更好的开发体验和更快的反馈。作为一个开发者要练习使用 TDD。很多开发者喜欢在编写测试之前编写业务，如果，你不先编写测试，你就会失去很多单元测试带来的好处。

It’s worth the practice and discipline, though. TDD with unit tests can train you to write UI components which are far simpler, easier to maintain, and easier to compose and reuse with other components.

尽管如此，先编写测试还是值得实践的。TDD 和单元测试可以让你编写 UI 组件更加简单、更容易维护和更容易组合复用组件。

One recent innovation in my testing discipline is the development of the [RITEway unit testing framework](https://medium.com/javascript-scene/rethinking-unit-test-assertions-55f59358253f), which is a tiny wrapper around [Tape](https://github.com/substack/tape) that helps you write simpler, more maintainable tests.

我在测试领域最新的一个发明就是：实现了[单元测试框架 RITEway](https://medium.com/javascript-scene/rethinking-unit-test-assertions-55f59358253f)，它是对 [Tape](https://github.com/substack/tape) 的简单封装，让你编写测试更加简单、更容易维护。

No matter what framework you use, the following tips will help you write better, more testable, more readable, more composable UI components:

不管你用什么测试框架，接下来的提示都可以帮助你编写更好、更易测试、更具可读性和更具可组合性的 UI 组件：

- **Favor pure components for UI code:** given same props, always render the same component. If you need state from the app, you can wrap those pure components with a container component which manages state and side-effects.
- **对于 UI 组件选用纯函数组件**：同样的 props，总是同样的渲染。如果，你的应用需要 state，你可以用容器组件管理 state 和副作用，然后来包装纯函数组件。
- **Isolate application logic/business rules** in pure reducer functions.
- 在纯 reducer 函数中**隔离应用的业务逻辑**
- **Isolate side effects** using container components.
- 使用容器组件**隔离副作用**

## Favor Pure Components

## 函数组件

A pure component is a component which, given the same props, always renders the same UI, and has no side-effects. E.g.,

一个函数组件，也就意味着同样的 props，中会渲染同样的 UI，也不会有副作用。比如：

```jsx
import React from 'react';
const Hello = ({ userName }) => (
  <div className="greeting">Hello, {userName}!</div>
);
export default Hello;
```

These kinds of components are generally very easy to test. You’ll need a way to select the component (in this case, we’re selecting by the `greeting` `className`), and you'll need to know the expected output. To write pure component tests, I use `render-component` from `RITEway`.

这类组件通常非常容易测试。你需要一方式选择定位组件（在这个示例中，我们通过类名 `greeting` 来选择组件），然后，你需要知道组件输出什么。为纯函数组件编写测试用例，我使用 `RITEway` 中的 `render-component`。

To get started, install RITEway:

首先，需要安装 RITEway：

```shell
npm install --save-dev riteway
```

Internally, RITEway uses `react-dom/server` `renderToStaticMarkup()` and wraps the output in a [Cheerio](https://github.com/cheeriojs/cheerio) object for easy selections. If you're not using RITEway, you can do all that manually to create your own function to render React components to static markup you can query with Cheerio.

RITEway 内部使用 `react-dom/server` `renderToStaticMarkup()` 然后把输出的内容包装成 [Cheerio](https://github.com/cheeriojs/cheerio) 对象以便选择。如果，你不使用 RITEway，你也创建属于自己的功能函数把 React 组件渲染成静态标签，然后用 Cheerio 来操作。

Once you have a render function to produce a Cheerio object from your markup, you can write component tests like this:

一旦，你得到 Cheerio 对象，你就可以像下面这样编写测试：

```js
import { describe } from 'riteway';
import render from 'riteway/render-component';
import React from 'react';
import Hello from '../hello';
describe('Hello component', async assert => {
  const userName = 'Spiderman';
  const $ = render(<Hello userName={userName} />);
  assert({
    given: 'a username',
    should: 'Render a greeting to the correct username.',
    actual: $('.greeting')
      .html()
      .trim(),
    expected: `Hello, ${userName}!`
  });
});
```

But that’s not very interesting. What if you need to test a stateful component, or a component with side-effects? That’s where TDD gets really interesting for React components, because the answer to that question is the same as the answer to another important question: “How can I make my React components more maintainable and easy to debug?”

但是，这并没什么神奇的。如果，你需要测试 stateful 组件或者有副作用的组件呢？这才是 TDD 对 React 组件真正神奇的地方，因为，这个问题的答案同另外一个非常重要的问题答案相同：“如何让组件更容易维护和 debug？”。

The answer: Isolate your state and side-effects from your presentation components. You can do that by encapsulating your state and side-effect management in a container component, and then pass the state into a pure component through props.

回答是：从组件中隔离 state 和副作用。你可以把 state 和副作用封装到一个容器组件，然后，把 state 做为纯函数组件的 props 向下传递。

But didn’t the hooks API make it so that we can have flat component hierarchies and forget about all that component nesting stuff? Well, not quite. It’s still a good idea to keep your code in three different buckets, and keep these buckets isolated from each other:

但是，Hooks  API 不就是为了让组件层级更加扁平，避免更深层的嵌套吗？不完全是。把组件分成三类仍旧是一个很好的注意，让彼此相互隔离：

- **Display/UI Components**
- **显示/UI 组件**
- **Program logic/business rules** — the stuff that deals with the problem you’re solving for the user.
- **程序逻辑/业务规则** — 处理解决用户相关的问题。
- **Side effects** (I/O, network, disk, etc.)
- **副作用**（(I/O, network, disk 等）

In my experience, if you keep the display/UI concerns separate from program logic and side-effects, it makes your life a lot easier. This rule of thumb has always held true for me, in every language and every framework I’ve ever used, including React with hooks.

根据我个人的经验，如果，你将显示/UI 与程序逻辑和副作用分离开，会提升你的开发体验。这种规则在我使用过的每种语言或者每个框架中，包括 React Hooks，都适用。

Let’s demonstrate stateful components by building a click counter. First, we’ll build the UI component. It should display something like, “Clicks: 13” to tell you how many times a button has been clicked. The button will just say “Click”.

我们来创建一个 Counter 组件来演示 stateful 组件。首先，我们需要创建 UI 组件。它应该包括这些内容：“Clicks: 13” 来表示按钮被点击了多少次。按钮的值是“Click”。

Unit tests for the display component are pretty easy. We really only need to test that the button gets rendered at all (we don’t care about what the label says — it may say different things in different languages, depending on user locale settings). We *do* want to make sure that the correct number of clicks gets displayed. Let’s write two tests: One for the button display, and one for the number of clicks to be rendered correctly.

为这个显示组件编写单元测试非常简单。我们只需测试按钮是否被渲染（我们不关心按钮的值是什么 — 根据用户的设置，不同的语言会有不同的显示）。我们还想知道是否显示了正确的点击数。我们需要编写两个测试：一个测试按钮是否显示，另外一个验证点击次数是否显示正确。

When using TDD, I frequently use two different assertions to ensure that I’ve written the component so that the proper value is pulled from props. It’s possible to write a test so that you could hard-code the value in the function. To guard against that, you can write two tests which each test a different value.

使用 TDD 时，我习惯使用两种不同的断言来确保组件可以正确显示相关的 props。如果，只编写一个测试有可能正好对应组件中的 hard-code。为了避免这种情况，你可以用两个不同的值来编写不同测试用例。

In this case, we’ll create a component called `<ClickCounter>`, and that component will have a prop for the click count, called `clicks`. To use it, simply render the component and set the `clicks` prop to the number of clicks you want it to display.

这个示例中，我们创建了一个名叫 `<ClickCounter>` 的组件，组件会有一个名为 `clicks` 的属性代表点击次数。为了使用它，只需为组件设置一个 `clicks` 属性，来表示需要显示的数字即可。

Let’s look at a pair of unit tests that could ensure we’re pulling the click count from props. Let’s create a new file, `click-counter/click-counter-component.test.js`:

我们来看一下单元测试是如何保证组件渲染的。我们需要创建新文件：`click-counter/click-counter-component.test.js`：

```js
import { describe } from 'riteway';
import render from 'riteway/render-component';
import React from 'react';
import ClickCounter from '../click-counter/click-counter-component';
describe('ClickCounter component', async assert => {
  const createCounter = clickCount =>
    render(<ClickCounter clicks={ clickCount } />)
  ;
  {
    const count = 3;
    const $ = createCounter(count);
    assert({
      given: 'a click count',
      should: 'render the correct number of clicks.',
      actual: parseInt($('.clicks-count').html().trim(), 10),
      expected: count
    });
  }
  {
    const count = 5;
    const $ = createCounter(count);
    assert({
      given: 'a click count',
      should: 'render the correct number of clicks.',
      actual: parseInt($('.clicks-count').html().trim(), 10),
      expected: count
    });
  }
});
```

I like to create little factory functions to make it easier to write tests. In this case, `createCounter` will take a number of clicks to inject, and return a rendered component using that number of clicks:

为了更加简单的编写测试用例，我喜欢创建小的工厂函数。这个示例中，`createCounter` 需要一个数字参数，然后，返回一个渲染后的组件：

```js
const createCounter = clickCount =>
  render(<ClickCounter clicks={ clickCount } />)
;
```

With the tests written, it’s time to create our `ClickCounter` display component. I've colocated mine in the same folder with my test file, with the name, `click-counter-component.js`. First, let's write a component fragment and watch our test fail:

有了测试用例，是时候实现 `ClickCounter` 组件了。我把组件和测试文件放在了同一目录下，并命名为 `click-counter-component.js`。首先，我们先编写组件的框架，然后，你会看到测试用例报错了：

```js
import React, { Fragment } from 'react';
export default () =>
  <Fragment>
  </Fragment>
;
```

If we save and run our tests, we’ll get a `TypeError`, which currently triggers Node's `UnhandledPromiseRejectionWarning` — eventually, Node will stop with the irritating warnings with the extra paragraph of `DeprecationWarning` and just throw an `UnhandledPromiseRejectionError`, instead. We get the `TypeError` because our selection returns `null`, and we're trying to run `.trim()` on it. Let's fix that by rendering the expected selector:

如果，我们保存然后运行测试用例，你会看到报错`TypeError`，它触发了 Node 的 `UnhandledPromiseRejectionWarning` 。最终，Node 将不会使用烦人的警告 `DeprecationWarning`，而是抛出一个 `UnhandledPromiseRejectionError` 错误。我们之所以遇到这个 `TypeError`，是因为我们选择器返回了 `null`，然后，我们尝试调用 `null` 的 `trim()` 方法。我们可以通过渲染期望的结构来修复这个错误：

```js
import React, { Fragment } from 'react';
export default () =>
  <Fragment>
    <span className="clicks-count">3</span>
  </Fragment>
;
```

Great. Now we should have one passing test, and one failing test:

很好。现在，我们应该会有一个测试通过，一个测试失败：

```shell
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
not ok 3 Given a click count: should render the correct number of clicks.
  ---
    operator: deepEqual
    expected: 5
    actual:   3
    at: assert (/home/eric/dev/react-pure-component-starter/node_modules/riteway/source/riteway.js:15:10)
...
```

To fix it, take the count as a prop, and use the live prop value in the JSX:

为了修复它，我们需要把 count 设置为组件的 prop，然后用真实的值来渲染：

```jsx
import React, { Fragment } from 'react';
export default ({ clicks }) =>
  <Fragment>
    <span className="clicks-count">{ clicks }</span>
  </Fragment>
;
```

Now our whole test suite is passing:

现在，我们所有的测试都通过了：

```shell
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.
1..3
# tests 3
# pass  3
# ok
```

Time to test the button. First, add the test and watch it fail (TDD style):

是时候测试点击按钮了。首先，添加测试用例，很显然会失败：

```js
{
  const $ = createCounter(0);
  assert({
    given: 'expected props',
    should: 'render the click button.',
    actual: $('.click-button').length,
    expected: 1
  });
}
```

This produces a failing test:

这是测试失败后的提示：

```shell
not ok 4 Given expected props: should render the click button
  ---
    operator: deepEqual
    expected: 1
    actual:   0
...
```

Now we’ll implement the click button:

现在，我们来实现点击按钮：

```jsx
export default ({ clicks }) =>
  <Fragment>
    <span className="clicks-count">{ clicks }</span>
    <button className="click-button">Click</button>
  </Fragment>
;
```

And the test passes:

测试通过了：

```shell
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.
ok 4 Given expected props: should render the click button.
1..4
# tests 4
# pass  4
# ok
```

Now we just need to implement the state logic and hook up the event handler.

现在，我们只需要实现 state 相关的逻辑和相关事件即可。

## Unit Testing Stateful Components

## Stateful 组件的单元测试

The approach I’m going to show you is probably overkill for a click counter, but most apps are far more complex than a click counter. State is often saved to database or shared between components. The popular refrain in the React community is to start with local component state and then lift it to a parent component or global app state on an as-needed basis.

我告诉你的方法对于 `ClickCounter` 来说过于复杂，但是，大部分应用比这个组件更加复杂。State 经常会保存在数据库或者在多个组件之间共享。React 社区流行的做法是先从组件本地 state 开始，然后，根据需要把 state 提升到父级组件或者全局。

It turns out that if you start your local component state management with pure functions, that process is easier to manage later. For this and other reasons (like React lifecycle confusion, state consistency, avoiding common bugs), I like to implement my state management using pure reducer functions. For local component state, you can then import them and apply the `useReducer` React hook.

事实证明，如果一开始你就使用纯函数组件本地管理 state，对于以后也更易于管理。出于此原因和其它原因（比如：React 生命周期的混乱、state 的一致性、避免常见的bug），我更喜欢使用 reducer 管理组件 state。对于本地组件 state，你可以使用 React Hook API `useReducer` 引入。

If you need to lift the state to be managed by a state manager like Redux, you’re already half way there before you even start: Unit tests and all.

如果，你需要使用 state 管理框架，比如：Redux，在此之前你已经实现了一半的工作，比如：单元测试等等。

> If you need to lift the state to be managed by a state manager like Redux, you’re already half way there before you even start: Unit tests and all.
>
> （*译者注：我的理解是，如果，你一开始就使用 `useReducer` 本地维护 state，在需要过渡到 Redux 时更加顺畅，之前的单元测试也可以很好的重用*）

First, I’ll create a new test file for state reducers. I’ll colocate this in the same folder, but use a different file. I’m calling this one `click-counter/click-counter-reducer.test.js`:

首先，我为 state reducer 创建了相应的测试文件。我将会把它放在相同目录下，只是用了不同文件名。我把它命名为 `click-counter/click-counter-reducer.test.js`：

```js
import { describe } from 'riteway';
import { reducer, click } from '../click-counter/click-counter-reducer';
describe('click counter reducer', async assert => {
  assert({
    given: 'no arguments',
    should: 'return the valid initial state',
    actual: reducer(),
    expected: 0
  });
});
```

I always start with an assertion to ensure that the reducer will produce a valid initial state. If you later decide to use Redux, it will call each reducer with no state in order to produce the initial state for the store. This also makes it really easy to create a valid initial state any time you need one for unit testing purposes, or to initialize your component state.

我习惯以断言开始，以确保 reducer 可以产出一个正常的初始值。如果，你以后决定使用 Redux，它将会在没有 state 的情况下，调用每一个 reducer，以便为 store 初始化 state。这也使得在需要为单元测试提供有效的初始 state 或者组件 state 时更加方便。

Of course, we’ll need to create a corresponding reducer file. I’m calling it `click-counter/click-counter-reducer.js`:

当然，我还需要创建相应的 reducer 文件：`click-counter/click-counter-reducer.js`：

```js
const click = () => {};
const reducer = () => {};
export { reducer, click };
```

I’m starting by simply exporting an empty reducer and action creator. To learn more about the important role of things like action creators and selectors, read [“10 Tips for Better Redux Architecture”](https://medium.com/javascript-scene/10-tips-for-better-redux-architecture-69250425af44). We’re not going to take the deep dive into React/Redux architecture patterns right now, but an understanding of the topic will go a long way towards understanding what we’re doing here, even if you are not going to use the Redux library.

一开始，我只是简单的导出空的reducer 和 action creator。想知道更多有关 action creators 和 selectors 的知识，请查看：[“改善 Redux 体系的 10 个提示”](https://medium.com/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)。今天，我们不打算深入探讨 React/Redux 设计模式相关内容，但是，理解了这类问题，即使，你不使用 Redux 对于理解我们今天所做的事情也有所帮助。

First, we’ll watch the test fail:

首先，我们将会看到测试失败：

```shell
# click counter reducer
not ok 5 Given no arguments: should return the valid initial state
  ---
    operator: deepEqual
    expected: 0
    actual:   undefined
```

Now let’s make the test pass:

现在，我来修复测试用例中的问题：

```js
const reducer = () => 0;
```

The initial value test will pass now, but it’s time to add more meaningful tests:

初始化相关的测试用例现在可以通过了，是时候添加更有意义的测试用例了：

```js
assert({
    given: 'initial state and a click action',
    should: 'add a click to the count',
    actual: reducer(undefined, click()),
    expected: 1
  });
  assert({
    given: 'a click count and a click action',
    should: 'add a click to the count',
    actual: reducer(3, click()),
    expected: 4
  });
```

Watch the tests fail (both return `0` when they should return `1` and `4`, respectively). Then implement the fix.

我们看到测试用例都失败了（两个分别应该返回 `1` 和`4`的，都返回了`0`）。我们来修复它们。

Notice that I’m using the `click()` action creator as the reducer's public API. In my opinion, you should think of the reducer as something that your application does not interact directly with. Instead, it uses action creators and selectors as the public API for the reducer.

注意我把 `click()` 作为 reducer 的公共 API 使用。在我看来，你应该把 reducer 作为应用的一部分，而不是直接与它交互。相反，reducer 的公共 API 应该是 action creators 和 selectors。

I also don't write separate unit tests for action creators and selectors. I always test them in combination with the reducer. Testing the reducer is testing the action creators and selectors, and vice versa. If you follow this rule of thumb, you'll need fewer tests, but still achieve the same test and case coverage as you would if you tested them independently.

我没有单独为 action creators 和 selectors 编写测试用例。我总是与 reducer 相结合来测试它们。测试 reducer 也就是测试 action creators 和 selectors，反之亦然。如果，你遵守这些规则，你将会需要更少的测试用例，但是，如果，你单独的测试它们，仍旧可以实现同样的测试和覆盖率。

```js
const click = () => ({
  type: 'click-counter/click',
});
const reducer = (state = 0, { type } = {}) => {
  switch (type) {
    case click().type: return state + 1;
    default: return state;
  }
};
export { reducer, click };
```

Now all the unit tests will pass:

现在，所有的单元测试都应该可以通过：

```shell
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.
ok 4 Given expected props: should render the click button.
# click counter reducer
ok 5 Given no arguments: should return the valid initial state
ok 6 Given initial state and a click action: should add a click to the count
ok 7 Given a click count and a click action: should add a click to the count
1..7
# tests 7
# pass  7
# ok
```

Just one more step: Connecting our behavior to our component. We can do that with a container component. I’ll just call that `index.js` and colocate it with the other files. It should look something like this:

最后一步：为组件绑定行为事件。我们可以使用容器组件来处理。我在本地目录中创建了一个名为 `index.js` 的文件。它的内容如下：

```jsx
import React, { useReducer } from 'react';
import Counter from './click-counter-component';
import { reducer, click } from './click-counter-reducer';
export default () => {
  const [clicks, dispatch] = useReducer(reducer, reducer());
  return <Counter
    clicks={ clicks }
    onClick={() => dispatch(click())}
  />;
};
```

That’s it. This component’s only job is to connect our state management and pass the state in as props to our unit-tested pure component. To test it, load the app in your browser and click the click button.

就是这样。这个组件只是用来管理 state，然后把 state 作为纯函数组件的 prop 向下传递。在浏览器中打开应用，点击按钮看是否正常运行。

Up until now we haven’t looked at the component in the browser or done any kind of styling. Just to clarify what we’re counting, I’ll add a label and some space to the `ClickCounter` component. I'll also hook up the `onClick` function. Now the code looks like this:

到现在为止，我们还没有在浏览器中查看组件和处理样式的问题。为了更加的清晰，我将会在 `ClickCounter` 组件中添加一个标签和一些空格。同时，也会绑定 `onClick` 事件。代码如下：

```jsx
import React, { Fragment } from 'react';
export default ({ clicks, onClick }) =>
  <Fragment>
    Clicks: <span className="clicks-count">{ clicks }</span>&nbsp;
    <button className="click-button" onClick={onClick}>Click</button>
  </Fragment>
;
```

And all the unit tests still pass.

所有的测试用例还是可以通过。

What about tests for the container component? I don’t unit test container components. Instead, I use functional tests, which run in-browser and simulate user interactions with the actual UI, running end-to-end. You need both kinds of tests (unit and functional) in your application, and unit testing your container components (which are mostly connective/wiring components like the one that wires up our reducer, above) would be too redundant with functional tests for my taste, and not particularly easy to unit test properly. Often, you’d have to mock various container component dependencies to get them to work.

容器组件的测试呢？我不会为容器组件编写单元测试。相反，我使用功能测试，这种测试运行在浏览器中或者模拟器中，用户可以与真实的 UI 交互，运行 end-to-end 测试。你的应用需要两种测试（单元和功能测试），为容器组件（那些为了连接 reducer 的组件）编写单元测试我觉得有点多余，而且，很难实现正确的单元测试。通常，你需要模拟各种容器组件的依赖关系以便可以正常工作。

In the meantime, we’ve unit tested all the important units that don’t depend on side-effects: We’re testing that the correct data gets rendered and that the state is managed correctly. You should also load the component in the browser and see for yourself that the button works and the UI responds.

在此期间，我们只是测试那些比较重要而不依赖副作用的组件：我们测试了是否可以正确的渲染，state 的管理是否正确。你还是需要在浏览器中运行组件，然后查看按钮是否正确工作。

Implementing functional/e2e tests for React is the same as implementing them for any other framework. Check out [“Behavior Driven Development (BDD) and Functional Testing”](https://medium.com/javascript-scene/behavior-driven-development-bdd-and-functional-testing-62084ad7f1f2) for details.

不管是为 React 组件实施功能/e2e测试，还是为其它框架实施都是相同的。详情可以查看 [“Behavior Driven Development (BDD) and Functional Testing”](https://medium.com/javascript-scene/behavior-driven-development-bdd-and-functional-testing-62084ad7f1f2)。

## Next Steps

## 下一步

Sign up for [TDD Day](https://tddday.com/): 5 hours of quality video content and interactive lessons on all aspects of Test Driven Development. It’s designed to be a great all-day crash course to level up your whole team’s TDD skills. Regardless of your current TDD experience, you’ll learn a lot.

注册 [TDD Day](https://tddday.com/)：可获得 5 小时有关 TDD 的高质量的视频内容和交互课程。这是一个很棒的速成教程，可以提高团队的 TDD 技能。不管，你当前的 TDD 经验如何，你都会学到更多知识。

