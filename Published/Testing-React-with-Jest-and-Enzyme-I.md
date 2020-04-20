# Testing React with Jest and Enzyme I

# 用 Jest 和 Enzyme 测试 React I

> 原文地址: <https://medium.com/codeclan/testing-react-with-jest-and-enzyme-20505fec4675/>   
> **本文版权归原作者所有，翻译仅用于学习。**

---------

*This post will look at how to setup and use* [*Jest*](https://facebook.github.io/jest/) *and*[ *Enzyme*](http://airbnb.io/enzyme/) *to test a* [*React*](https://reactjs.org/) *application created with* [*Create React App (CRA).*](https://github.com/facebook/create-react-app) *Some pointers will be given for those working from scratch. Basic knowledge of React is assumed.*

*这篇文章将会介绍如何设置并使用  [Jest](https://facebook.github.io/jest/) 和 [ Enzyme](http://airbnb.io/enzyme/) 测试通过 [Create React App (CRA)](https://github.com/facebook/create-react-app) 创建的 [React](https://reactjs.org/) 应用。对于那些从头开始的人我们会给出一些建议。但是，不会涉及太多有关 React 的知识。*

![](https://miro.medium.com/max/1400/1*TM2PUy1FaxdKIf8h2WVKug.png)

Jest and Enzyme are different but complimentary tools, that integrate well together to provide flexible and creative testing abilities. We will briefly look at the differences between the two.

Jest 和 Enzyme 是两个不同，但又相互相成的工具，整合在一起可以提供更加灵活和更具创造性的测试能力。我们将会简单介绍两者有什么差异。

## Jest

> *Jest is a JavaScript unit testing framework, used by Facebook to test services and React applications.*
>
> *Jest 是一个 Javascript 单元测试框架，Facebook 用来测试服务和 React 应用。*

CRA comes bundled with Jest; it does not need to be installed separately.

CRA 已经内置了 Jest；不再需要单独安装。

Jest acts as a **test runner**, **assertion library**, and **mocking library**.

Jest 是一个 **测试运行器**、**断言库** 和 **模拟库**。

Jest also provides **Snapshot testing**, the ability to create a rendered ‘snapshot’ of a component and compare it to a previously saved ‘snapshot’. The test will fail if the two do not match. Snapshots will be saved for you beside the test file that created them in an auto-generate `__snapshots__` folder. An example snapshot could be as simple as:

Jest 也提供 **快照**测试，它可以创建一个组件的快照，然后与上一次保存快照对比。如果，前后两者不匹配，测试就会失败。快照将会被保存在名为 `__snapshots__` 文件夹中，它与被测试的文件在同一目录中。快照看起来就像下面这个样子：

```js
exports[`List shallow renders correctly with no props 1`] = `
<List
  ordered={false}
>
  <ListItem>
    Item 1
  </ListItem>
  <ListItem>
    Item 1
  </ListItem>
</List>
`;
```

Snapshot testing must be complimented with in browser testing and looking at the snapshot created when creating the initial snapshots, to ensure that the snapshot reflects the intended outcome.

快照测试必须结合浏览器测试，并且，在一开始就要校验快照，以保证快照能按照期望的输出。

## Enzyme

> *Enzyme is a JavaScript Testing utility for React that makes it easier to assert, manipulate, and traverse your React Components’ output.*
>
> *Enzyme 是针对 React 的测试功能库，它可以更容易的断言、操控和遍历 React 组件。*

Enzyme, created by Airbnb, adds some great additional utility methods for **rendering a component** (or multiple components), **finding elements**, and **interacting with elements**.

Enzyme，由 Airbnb 创建，并添加了更好的功能方法，比如：**渲染组件**、**查找元素**和**与元素交互**。

It must be installed in addition to tools already bundled with CRA.

在 CRA 中它必须单独安装。

## Jest and Enzyme

## Jest 和 Enzyme

- Both Jest and Enzyme are specifically designed to test React applications, Jest can be used with any other Javascript app but Enzyme only works with React.
- Jest can be used without Enzyme to render components and test with snapshots, Enzyme simply adds additional functionality.
- Enzyme can be used without Jest, however Enzyme **must** be paired with another test runner if Jest is not used.
- Jest 和 Enzyme 两者都可以测试 React 应用，Jest 还可以测试其他的 Javascript 应用，但是 Enzyme 只能测试 React。
- Jest 可以单独渲染组件和快照测试，Enzyme 只是添加了更简单的方法。
- 没有 Jest，Enzyme 也可以使用，但是**必须**结合另外的测试运行器（runner）。

As described, we will use:

- **Jest** as the test runner, assertion library, and mocking library
- **Enzyme** to provide additional testing utilities to interact with elements

综上所述：

- **Jest** 作为测试运行器，断言库和模拟器使用
- **Enzyme** 提供额外的测试功能用与交互。

## Setup

## 设置

### Installing and configuring

### 安装和配置

If not using CRA install Jest:

如果不使用 CRA，需要安装 Jest：

```
npm install --save-dev jest babel-jest
```

Install Enzyme:

安装 Enzyme：

```
npm install --save-dev enzyme enzyme-adapter-react-16 enzyme-to-json
```

Update your `package.json` :

更新 ```package.json```：

```
"jest": {
  "snapshotSerializers": ["enzyme-to-json/serializer"]
}
```

`enzyme-to-json` provides a better component format for snapshot comparison than Enzyme’s internal component representation.`snapshotSerializers` allows you to minimise code duplication when working with snapshots. Without the serializer each time a component is created in a test it must have the `enzyme-to-json` method `.toJson()` used individually before it can be passed to Jest’s snapshot matcher, with the serializer you never use it individually.

相比 Enzyme，为了更加方便的对比快照，```enzyme-to-json``` 提供了更好组件格式化方式。当使用快照时 ```snapshotSerializers``` 可以更大限度的压缩重复代码。如果没有指定序列化工具，测试中每次对比快照是否匹配时都需要把组件传递给 `enzyme-to-json` 的 `.toJson()` 方法，反之，就不需要。

```js
expect(toJson(rawRenderedComponent)).toMatchSnapshot();
```

With this additional line in `package.json` it allows you to pass a component created by Enzyme to the Jest `.toMatchSnapshot()` without calling this interim JSON method.

通过在 ```package.json``` 中添加这一配置，在你调用 Jest 的 `.toMatchSnapshot()` 时就不需要在调用 JSON 格式化方法了。

Create a `setupTests.js` file at `./src/setupTests.js`:

在 `./src/setupTests.js` 创建 `setupTets.js` 文件：

```js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';configure({ adapter: new Adapter() });
```

CRA will automatically pick up this file, if not using CRA then also add this line in the same location as snapshotSerializers above:

CRA 会自动搜索这个文件，如果，你没有使用 CRA，这时你就需要在 snapshotSerializers 的相同位置添加这一配置：

```json
"setupFiles": ["./src/setupTests.js"],
```

### Creating a test file

### 创建测试文件

Jest will [look for tests](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests) in any of the following places:

- Files with `.js` suffix in `__tests__` folders.
- Files with `.test.js` suffix.
- Files with `.spec.js` suffix.

Jest 将会在符合以下规则的地方[查找所有的测试文件](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests)：

- 文件夹 `__tests__` 中以 `.js` 结尾的文件
- 以 `.test.js` 结尾的文件
- 以 `.spec.js` 结尾的文件

It is convention to put each test file next to the code it is testing. This makes semantic sense, and also means relative paths are shorter ( `./MyComponent` vs `../../MyComponent` etc).

比较方便的是把每个测试文件和需要测试代码放在同一目录下。这在语义上更加有意义，同时也会让相对路径更加简单（`./MyComponent` vs `../../MyComponent` ）。

An example of this could be this `MyComponent.test.js`file:

以下是 `MyComponent.test.js` 的演示：

```js
import React from 'react';
import { shallow } from 'enzyme';
import MyComponent from './MyComponent';

describe('MyComponent', () => {
  it('should render correctly in "debug" mode', () => {    const component = shallow(<MyComponent debug />);
  
    expect(component).toMatchSnapshot();
  });
});
```

When `npm test` in CRA is ran it will run all test files and output the results to the terminal. Customisation flags exist to run against specific files only, or conversely ignore specific files using `-- --testPathPattern filename/` and `-- --testPathIgnorePatterns filename/`.

在 CRA 环境下，当执行 `npm test` 时会运行所有的测试文件并把结果输出到终端。通过自定义标签 `-- --testPathPattern filename/` 可以指定只运行特定的文件或者使用 `-- --testPathIgnorePatterns filename/` 来忽略指定的文件。

## Mount, Shallow, Render

```js
import { mount, shallow, render } from ‘enzyme';
```

In order to have a component to test one of the above must be used, as in the example further above.

为了测试组件以上的方法肯定会用到，就如上一段代码所示。

### [Mounting](http://airbnb.io/enzyme/docs/api/mount.html)

- Full DOM rendering including child components
- Ideal for use cases where you have components that may interact with DOM API, or use React lifecycle methods in order to fully test the component
- As it actually mounts the component in the DOM `.unmount()` should be called after each tests to stop tests affecting each other
- Allows access to both props directly passed into the root component (including default props) and props passed into child components
- 完整的 DOM 渲染，包括子组件
- 对于需要和 DOM 交互、完整测试组件生命周期情况，这是最理想渲染方式
- 因为是真实把组件渲染成 DOM，为了不影响其他的测试，每次测试完成都需要调用 `.unmount()`
- 允许直接访问传递给 root 组件（包括默认的）和传递给子组件的 props。

### [Shallow](http://airbnb.io/enzyme/docs/api/shallow.html)

- Renders only the single component, not including its children. This is useful to isolate the component for pure unit testing. It protects against changes or bugs in a child component altering the behaviour or output of the component under test
- As of Enzyme 3 `shallow` components do have access to lifecycle methods by default
- Cannot access props passed into the root component (therefore also not default props), but can those passed into child components, and can test the *effect* of props passed into the root component. This is as with `shallow()`, you're testing what `MyComponent` *renders* - not the element you passed into `shallow`
- 只渲染单个组件，不包括子组件。这对于需要隔离组件做单纯的单元测试非常有用。它可以防止因为子组件的改变和 bugs 影响组件测试的结果。
- 在 Enzyme 3 中 `shallow` 渲染默认也可以访问组件的生命周期方法。
- 不能访问传递给 root 组件的 props（因为它们不是默认 props），但是，它们可以传递给子组件，也可以测试传递的 props 是否对组件有*影响*。也就说 `shallow` 可以测试组件的渲染，但不能测试其中的 element。

### [Render](http://airbnb.io/enzyme/docs/api/render.html)

- Renders to static HTML, including children
- Does not have access to React lifecycle methods
- Less costly than `mount` but provides less functionality
- 把组件渲染成静态的 HTML，包括子组件
- 不能访问 React 的生命周期方法
- 相比 `mount` 成本更低，但功能也更少

# Testing

### Basic component rendering

### 基本的组件渲染

For simple non-interactive components:

一个简单无交互的组件：

```js
it('should render correctly with no props', () => {
  const component = shallow(<MyComponent/>);
  
  expect(component).toMatchSnapshot();
});
it('should render banner text correctly with given strings', () => {
  const strings = ['one', 'two'];
  const component = shallow(<MyComponent list={strings} />);
  expect(component).toMatchSnapshot();
});
```

### Events

### 事件

The Enzyme API has several ways to simulate events or user interactions. If you are wanting to test interacting with a child component then the `mount` method can be used.

Enzyme API 有好几种方式可以模拟事件或者用户交互。如果，你想测试子组件的交互功能，这时就需要 ```mount``` 方法了。

```js
it('should be possible to activate button with Spacebar', () => {
  const component = mount(<MyComponent />);
  component
    .find('button#my-button-one')
    .simulate('keydown', { keyCode: 32 });
  expect(component).toMatchSnapshot();
  component.unmount();
});
```

### Mock functions

### 模拟方法

You may simply want to check that a function passed as props is successfully called.

或许你只是想简单验证下通过 prop 传递的方法是否成功执行。

```js
const clickFn = jest.fn();
describe('MyComponent', () => {
  it('button click should hide component', () => {
    const component = shallow(<MyComponent onClick={clickFn} />);
    component
      .find('button#my-button-two')
      .simulate('click');
    expect(clickFn).toHaveBeenCalled();
  });
});
```

Getting more complex, you may wish to mock a function imported and used within `MyComponent.js`, set its return value, check it is called, and compare its snapshot.

慢慢会变得更加复杂，你或许想模拟 `MyComponent.js` 里面 import 的一个方法，它返回一个值，检查是否被调用，并对比快照。

Lets imagine that within `MyComponent.js` we
`import { SaveToStorage } from 'save-to-storage'`before creating a new SaveToStorage object, which has both `TryGetValue` and `TrySetValue` methods. `TryGetValue` has a default return value of false, if it returns `true` the component will change. Our component uses these within different button clicks.

想象一下，我们在 `MyComponent.js` 中引入了一个 SaveToStorage 对象，它包含 `TryGetValue` 和 `TrySetValue` 两个方法。`TryGetValue` 有一个默认的返回值：false，如果，它返回 `true` 说明组件会改变。组件中两个不同按钮会使用它们。

We can use `jest.mock` to mock this, as well as `jest.fn` to provide overrides for the functions within it.

我们用 ```jest.mock``` 也可以模拟这种行为，同时，`jest.fn` 可以覆写组件内的方法。

```js
const mockTryGetValue = jest.fn(() => false);
const mockTrySetValue = jest.fn(); 
 
jest.mock('save-to-storage', () => ({   
  SaveToStorage: jest.fn().mockImplementation(() => ({
    tryGetValue: mockTryGetValue,
    trySetValue: mockTrySetValue,
  })), 
}));
describe('MyComponent', () => {
  it('should set storage on save button click', () => {
    mockTryGetValue.mockReturnValueOnce(true);
    const component = mount(<MyComponent />); 
    component.find('button#my-button-three').simulate('click');
    expect(mockTryGetValue).toHaveBeenCalled();
    expect(component).toMatchSnapshot();
    component.unmount();   
  });    
});
```

## Conclusion

## 总结

These are just some of the methods available, Jest has several other assertion methods available (including `toEqual`), and Enzyme many more traversal and interactive methods available (including `first` and `setProps`). The documentation for each is good, and opens up many new possibilities.

在这，只介绍了其中一部分，Jest 还有其它的断言方法（包括：```toEqual```），同时 Enzyme 还有更多的遍历和交互的方法（包括：```first``` 和 ```setProps```）。每个文档介绍的都很好，并提供了更多的可能。

Using Jest and Enzyme together makes testing React components much easier, and makes for very readable tests.

同时使用 Jest 和 Enzyme 让测试 React 组件更加容易，同时测试更具可读性。

Thanks for reading! 🙂

多谢你们喜欢！🙂

If you liked this, you might also like:

- [Testing React with Jest and Enzyme II](https://codeburst.io/testing-react-events-with-jest-and-enzyme-ii-46fbe4b8b589)
- [Mocking ES and CommonJS modules with jest.mock()](https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43)
- [Testing React applications with jest, jest-axe, and react-testing-library](https://hackernoon.com/testing-react-with-jest-axe-and-react-testing-library-accessibility-34b952240f53)
- [Mocking HTTP requests with Nock](https://codeburst.io/testing-mocking-http-requests-with-nock-480e3f164851)

如果，你喜欢这篇文章，你还可以看看这些：

- [用 Jest 和 Enzyme 测试 React II](https://codeburst.io/testing-react-events-with-jest-and-enzyme-ii-46fbe4b8b589)
- [用 jest.mock() 模拟 ES 和 CommoJS 模块](https://medium.com/codeclan/mocking-es-and-commonjs-modules-with-jest-mock-37bbb552da43)
- [使用 jest, jest-axe, 和 react-testing-library 测试 React 应用](https://hackernoon.com/testing-react-with-jest-axe-and-react-testing-library-accessibility-34b952240f53)
- [用 Nock 模拟 HTTP 请求](https://codeburst.io/testing-mocking-http-requests-with-nock-480e3f164851)

### Resources

### 资源

In addition to the official documentation and hacking around on my own, I found the following two articles particularly helpful, and in helping to cement my knowledge by writing this post you will clearly see influence from them:

- [Testing React Components](https://hackernoon.com/testing-react-components-with-jest-and-enzyme-41d592c174f) by [Artem Sapegin](https://hackernoon.com/@sapegin?source=post_header_lockup)
- [Unit Testing React Components](https://www.codementor.io/vijayst/unit-testing-react-components-jest-or-enzyme-du1087lh8) by [Vijay Thirugnanam](https://www.codementor.io/vijayst)

If you are also wishing to look at visual regression testing in addition to unit and integration testing I can recommend looking to [Differencify](https://github.com/NimaSoroush/differencify).

除了官方文档和我自己的经验，我发现下面两篇文章也非常有用，并且，我写的这篇文章也受到它们的启发同时也巩固了我的知识：

- 来自 [Artem Sapegin](https://hackernoon.com/@sapegin?source=post_header_lockup) 的 [测试 React 组件](https://hackernoon.com/testing-react-components-with-jest-and-enzyme-41d592c174f)
- 来自 [Vijay Thirugnanam](https://www.codementor.io/vijayst) 的 [React 组件的单元测试](https://www.codementor.io/vijayst/unit-testing-react-components-jest-or-enzyme-du1087lh8)

除单元测试和集成测试之外，你还希望看看视觉回归测试，我推荐你关注 [Differencify](https://github.com/NimaSoroush/differencify)。