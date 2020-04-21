# Testing React with Jest and Enzyme II

## 用 Jest 和 Enzyme 测试 II

> 原文地址: <https://codeburst.io/testing-react-events-with-jest-and-enzyme-ii-46fbe4b8b589/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*nG8BY3_MIe8STVFBU-qJRw.png)

Previously we looked at how to [setup Jest and Enzyme](https://medium.com/codeclan/testing-react-with-jest-and-enzyme-20505fec4675) with some and took a slight detour to look at[ mocking ES and CommonJS modules with Jest](https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43).

先前，我们已经知道如何[设置 Jest 和 Enzyme](https://medium.com/codeclan/testing-react-with-jest-and-enzyme-20505fec4675)，并且，顺便了解了 [Jest 中如何模拟 ES 和 CommonJS 模块](https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43)。

In this post we are going to look at some additional examples of how to simulate user interaction with a component via Enzyme to create specific test scenarios.

在这篇文章中，我们将会看到更多的示例：如何通过 Enzyme 创建特殊的测试场景模拟组件中用户的交互。

We will be using Enzyme’s **mount** for full DOM rendering as described in the first post of this series. To summarise, from the [docs](https://airbnb.io/enzyme/docs/api/mount.html):

> *Full DOM rendering is ideal for use cases where you have components that may interact with DOM APIs or need to test components that are wrapped in higher order components.*
>
> *If you do not want to run your tests inside of a browser, the recommended approach to using `mount` is to depend on a library called [jsdom](https://github.com/tmpvar/jsdom) which is essentially a headless browser implemented completely in JS.*

如本系列第一篇文章所述，我们将会用 Enzyme 的 **mount** 的方法来实现组件的完整的渲染。来自[文档](https://airbnb.io/enzyme/docs/api/mount.html)的总结：

> *在需要组件与 DOM API 交互或者测试那些被高阶组件包含的组件时，完整的 DOM 渲染是最理想的方式*
>
> *如果，你不打算在浏览器中运行测试，推荐使用 `mount`，它依赖 [jsdom](https://github.com/tmpvar/jsdom) 库，这个库本质上是一个用 JS 实现的 headless 浏览器。*

This is what gives us the ability to interact with our React component as if we were inside a browser.

这让我们可以与组件交互，就像在浏览器中一样。

The examples given are based on the following versions:

以下是基本的版本配置：

```json
"enzyme": "^3.3.0",
"enzyme-adapter-react-16": "^1.1.1",
"enzyme-to-json": "^3.3.3",
```

## Simulating events

## 模拟事件

Enzyme syntax for simulating user interaction is straight forward to read, at it simplest as below on a mounted component:

Enzyme 中模拟用户交互的语法非常简单直接，以下就是最简单的示例：

```js
component.find(selector).simulate(event);
```

### Selectors
### 选择器

[Selectors](https://airbnb.io/enzyme/docs/api/selector.html) can be one of a:

- CSS Selector
- Prop Attribute Selector
- Prop Object Selector
- React Component Constructor
- React Component displayName

[选择器](https://airbnb.io/enzyme/docs/api/selector.html) 是下面的其中之一：

- CSS 选择器
- Prop 属性选择器
- Prop 对象选择器
- React 组件构造函数
- React 组件 `displayName`

In the examples covered here we will just look at the syntax behind different ways of targeting CSS selectors. The Enzyme docs are well written and contain good examples to continue reading if a CSS selector does not fit your purpose.

在这篇文章中，我们只会查看 CSS 选择器的几种不同方式。如果，CSS 选择器不能满足你的需求，可以继续查阅文档，Enzyme 的文档写的非常好，也有很好的演示。

Directly from the documentation we can see that the following are supported:

- Class syntax (`.foo`, `.foo-bar`, etc.)
- Element syntax (`input`, `div`, `span`, etc.)
- ID syntax (`#foo`, `#foo-bar`, etc.)
- Attribute syntax (`[href="foo"]`, `[type="text"]`, etc.)

With combinations of the above possible (`button#id-foo`, etc).

通过文档我们知道 CSS 选择器支持以下几种：

- 类选择器（`.foo` `.foo-bar` 等）
- 元素选择器（`input`  `div`  `span` 等）
- ID 选择器（`#foo` `#foo-bar` 等）
- 属性选择器（`[href="foo"]` `[type="text"]` 等）

以上语法也可以组合使用（`button#id-foo`）。

A common error to see is `Method "simulate" is only meant to be run on a single node. 3 found instead.` if a sub-component being interacted with is used more than once in the parent.

如果，父组件中存在多个需要交互的子组件时，一个常见的错误：`Method "simulate" is only meant to be run on a single node. 3 found instead`。

This is a simple fix, as it is possible to specify the index of the node you wish to interact with:

这也很容易修复，你可以给需要交互的 node 指定索引：

```js
// the initialcomponent.find([className="checkbox__input"]).simulate(event);// becomescomponent.find([className="checkbox__input"]).at(1).simulate(event);
```

### Events
### 事件

Enzyme’s [simulate](https://airbnb.io/enzyme/docs/api/ReactWrapper/simulate.html) is used for simulating [DOM Events](https://developer.mozilla.org/en-US/docs/Web/Events), common ones being ‘click’, ‘change’, and ‘keydown’. After the node is selected simulate is chained on as to complete the mock interaction:

Enzyme 的方法 [simulate](https://airbnb.io/enzyme/docs/api/ReactWrapper/simulate.html) 可以用来模拟 [DOM 事件](https://developer.mozilla.org/en-US/docs/Web/Events)，常用的有：`click`、`change`、`keydown`。选定 node 后，可以直接链式调用 simulate 来完成模拟交互：

```js
component.find(selector).simulate('click');
component.find(selector).simulate('change');
component.find(selector).simulate('keydown', { keyCode: 32 });
```

## Writing tests

## 编写测试用例

### Testing re-rendering

### 测试重新渲染

If a DOM event causes a re-render, unmounting of a child component for example, then [Jest Snapshot testing](https://jestjs.io/docs/en/snapshot-testing) can be used to test the component renders as expected.

如果，DOM 事件引起组件重新渲染，卸载子组件，这时，[Jest 快照测试](https://jestjs.io/docs/en/snapshot-testing)就可以来验证组件是否正确的重新渲染。

```js
it('should be possible to open menu with Spacebar', done => {
  const component = mount(<MyComponent />);
  component.find('#link-id').simulate('keydown', { keyCode: 32 });
  expect(component).toMatchSnapshot();
  component.unmount();
});
```

### Testing function calls
### 测试函数调用

If a function is passed to a child component you may wish to test that it is called correctly when mounting the parent. The function is first mocked and passed as the correct prop.

如果，一个函数传递了子组件，你希望在父组件挂载的时候，验证它是否被正确的调用。首先，需要模拟这个函数，然后，以 prop 的形式传递下去。

```js
const mockFunction = jest.fn();
it('should call mockFunction on button click', () => {
  const component = mount(
    <MyComponent onClickFunction={mockFunction} />
  );
  component.find('button#ok-btn').simulate('click');
  expect(mockFunction).toHaveBeenCalled();
  
  component.unmount();
});
```

### Testing state or prop updates

### 测试 state 或者 prop 的更新

The component [state](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) and [props](https://airbnb.io/enzyme/docs/api/ShallowWrapper/props.html) are also possible to evaluate.

组件中的 [state](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) 和 [props](https://airbnb.io/enzyme/docs/api/ShallowWrapper/props.html) 也可以测试验证。

```js
it('sets loading state to true on save press', () => {
  const component = mount(<MyComponent />);
  component.find('[className="save-button"]').simulate('click');
  expect(component.state('isLoading')).toEqual(true);
  component.unmount();
});
```

### Other interactions with the document

### 与 document 的其他交互

As `mount` has access to a full DOM many other aspects can be included in tests.

因为，`mount` 可以渲染完整的 DOM，因此，可以包含其他很多方面的测试。

This includes the [cookies](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie) associated with the current document. These are accessible via `document.cookie`. To prevent changes from persisting between tests you may wish to use something such as

这也包括与当前 document 关联的 [cookies](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)。它可以通过 `document.cookie` 访问。为了防止测试之间相互影响，你或许需要用到以下方式

```js
beforeEach(() => {
  document.cookie = '';
});
```

to reset their values.

去重置它的值。

If the component synced a cookie value to what is in state on mount then the following test could be considered:

如果，组件在挂载时想同步 cookie 的值到 state 中，可以考虑以下的测试：

```js
it('syncs state with user cookie on mount', () => {
  document.cookie = 'cookieOne=valueOne:::false&valueTwo:::true';
  
  const component = mount(<MyComponent />);
  expect(component.state('valueOne')).toEqual(false);
  expect(component.state('valueTwo')).toEqual(true);
  component.unmount();
});
```

## Final thoughts
## 最后的思考

Hopefully this provides an indication of the possibilities that exist when using Jest and Enzyme together, and the examples here are readable enough to use the parts that apply to your own projects.

希望这能说明在一起使用 Jest 和 Enzyme 是存在可能，这里的示例也具可读性，足以用到你自己的项目中。

An additional tip is that if user interactions are not producing the results that you are expected debugging can be aided with adding the Enzyme specific

另外一个提示：如果，用户交互没有产生预期的结果，Enzyme 中的 debug 可以帮到你

```js
console.log(component.debug())
```

to see a snapshot of the mounted component at that time.

这时，需要去看看组件挂载后的快照。

Thanks for reading, and if you have posts of your own that expand on this feel free to link them in the comments! 😁

多谢你们喜欢，如果，你有更多的见解，可以在评论中提出！😁

Other posts I have written include:

- [Mocking HTTP requests with Nock](https://codeburst.io/testing-mocking-http-requests-with-nock-480e3f164851)
- [Using Pa11y CI and Drone as accessibility testing gatekeepers](https://hackernoon.com/using-pa11y-ci-and-drone-as-accessibility-testing-gatekeepers-a8b5a3415227)
- [What are encryption keys and how do they work?](https://medium.com/codeclan/what-are-encryption-keys-and-how-do-they-work-cc48c3053bd6)

我还写了其他的文章：

- [用 Nock 模拟 HTTP 请求](https://codeburst.io/testing-mocking-http-requests-with-nock-480e3f164851)
- [使用 Pa11y CI 和 Drone 做可访问性测试](https://hackernoon.com/using-pa11y-ci-and-drone-as-accessibility-testing-gatekeepers-a8b5a3415227)
- [密钥是什么，它们是如何工作的?](https://medium.com/codeclan/what-are-encryption-keys-and-how-do-they-work-cc48c3053bd6)

