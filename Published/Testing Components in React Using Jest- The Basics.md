## Testing Components in React Using Jest: The Basics
## 测试 React 组件（1）

> 原文地址: <https://code.tutsplus.com/articles/testing-components-in-react-using-jest-the-basics--cms-28934/>       
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/28934/final_image/Testing-Components-in-React-ProductListingDemo.png)

Testing code is a confusing practice for many developers. That's understandable because writing tests requires more effort, time, and the ability to foresee possible use cases. Startups and developers working on smaller projects usually favor ignoring tests altogether because of the lack of resources and manpower. 

代码测试一直困扰着很多开发者。这也可以理解，因为，写测试用例需要更多的时间和预测未知的能力。由于资源和人力缺乏，初创公司和开发人员在处理小型项目时通常会忽略测试。

However, there are a couple of reasons why I believe that you should test your components:

1. It makes you feel more confident about your code.
1. Tests enhance your productivity.

然而，我依旧认为需要测试你的组件，有以下几个原因：

1. 让你对自己的代码更自信
1. 测试可以提高你的产品能力

React isn't any different either. When your whole application starts to turn into a pile of components that are hard to maintain, testing offers stability and consistency. Writing tests from day one will help you write better code, spot bugs with ease, and maintain a better development workflow. 

React 没有什么特别之处。一旦应用包含太多组件就会变的难以维护，代码测试可以提供稳定性和一致性。代码测试可以让你的代码变的更好，更容易发现 bugs 并保持良好的开发流程。

In this article, I will take you through everything that you need to know to write tests for your React components. I'll also cover some of the best practices and techniques while we're at it. Let's get started!

在这篇文章中，我将会向你们介绍如何为 React 组件写测试用例。我也会介绍一些最佳实践和技术。让我们开始！

### Testing Components in React

### 测试 React 组件

Testing is the process of verifying that our *test assertions* are true and that they stay true throughout the lifetime of the application. A test assertion is a boolean expression that returns true unless there is a bug in your code. 

测试是验证*测试断言*是否正确或者在整个应用生命周期中是否正确的过程。测试断言是一条布尔表达式除非你的代码有 bug，它应该返回 true。

For instance, an assertion could be something as simple as this: "When the user navigates to **/login**, a modal with the id ```#login``` should be rendered." So, if it turns out that you messed up the login component somehow, the assertion would return false. Assertions are not just limited to what gets rendered—you can also make assertions about how the application responds to user interactions and other actions. 

比如，断言可以简单的是：“当用户浏览 **/login** 时，一个 id 是```#login``` 弹窗应该被渲染。”因此，如果在渲染 login 组件时出了什么问题，断言就会返回 false。断言不仅如此 - 你还可以用断言来判断应用与用户的交互是否正确。

There are many automated testing strategies that front-end developers use to test their code. We will limit our discussion to just three software test paradigms that are popular with React: unit testing, functional testing, and integration testing.

前端开发者有很多自动化测试策略来测试自己的代码。我们只会讨论 React 中 3 个比较流行软件测试模式：单元测试，功能测试和集成测试。

#### Unit Testing

#### 单元测试

Unit testing is one of the test veterans that's still popular in testing circles. As the name suggests, you will be testing individual pieces of code to verify that they function independently as expected. Because of React's component architecture, unit tests are a natural fit. They're also faster because you don't have to rely on a browser.

在整个测试生态中单元测试仍旧是最流行的。就像它的名字一样，你会单独测试代码片段验证是否有按照预期的执行。由于 React 的组织架构，单元测试是一件自然而然的事。因为不需要运行浏览器单元测试也非常快。

Unit tests help you think of each component in isolation and treat them as functions. Your unit tests for a particular component should answer the following questions:

1. Are there any props? If yes, what does it do with them?
1. What components does it render? 
1. Should it have a state? When or how should it update the state?
1. Is there a procedure that it should follow when it mounts or unmounts, or on user interaction?

单元测试会让你对每个组件都单独思考并把它们看作一个功能。实现单元测试需要思考以下几个问题：

1. 是否有 props？如果有，它们用来做什么？
1. 组件如何渲染？
1. 是否有 state？何时如何更新 state？
1. 组件挂载、卸载或者与用户交互时是否遵循某些程序逻辑？

#### Functional Testing

#### 功能测试

Functional tests are used to test the behavior of a part of your application. Functional tests are usually written from a user's perspective. A piece of functionality is usually not limited to a single component. It can be a full-fledged form or an entire page. 

功能测试用来测试应用的部分行为功能。编写功能测试通常以用户角度作为出发点。一个功能通常不局限于单个组件。它可能是整个表单或者整个页面。

For instance, when you're building a signup form, it might involve components for the form elements, the alerts, and errors if any. The component that gets rendered after the form is submitted is also part of that functionality. This doesn't require a browser renderer because we'll be using an in-memory virtual DOM for our tests.

比如，当你构建一个注册表单，它可能包含表单元素、alert 警告、还有错误处理等等。提交后渲染的组件也是需要处理的一部分。因为，我们可以用虚拟 DOM 来测试，所以，不需要真正的浏览器渲染。

#### Integration Testing

#### 集成测试

Integration testing is a test strategy where all the individual components are tested as a group. Integrated testing attempts to replicate the user experience by running the tests on an actual browser. This is considerably slower than functional testing and unit tests because each test suite is executed on a live browser. 

集成测试是一种测试策略，它可以把所有的组件作为一个组来测试。集成测试会尝试模拟用户的行为在真是的浏览器环境执行。因为每个测试都在真实的浏览器执行，这会比功能测试和单元测试慢很多。

In React, unit tests and functional tests are more popular than integration tests because they are easier to write and maintain. That's what we will cover in this tutorial.

在 React 中，单元测试和功能测试比集成测试更流行，因为它们比较容易编写和维护。这也是我们本次教程中将要介绍的内容。

### Know Your Tools 

### 了解你的工具

You need certain tools and dependencies to get started with unit and functional testing your React application. I've listed them below.

你需要工具来完成对 React 应用的单元测试和功能测试。我会在接下来的内容中列出它们。

#### Jest Test Framework

#### Jest 测试框架

Jest is a testing framework that requires zero configuration and is therefore easy to set up. It's more popular than test frameworks like Jasmine and Mocha because it's developed by Facebook. Jest is also faster than the rest because it uses a clever technique to parallelize test runs across workers. Apart from that, each test runs in a sandbox environment so as to avoid conflicts between two successive tests. 

Jest 是一个零配置测试框架，因此很容易使用。它比 Jasmine 和 Mocha 等测试框架更加流行，因为，这是 Facebook 开发的。Jest 也比 rest 快，因为它使用精明技术让测试用例并行执行。因为，每个测试用例都在一个沙箱环境中执行，可以有效避免相互之间的影响。

If you're using create-react-app, it comes shipped with Jest. If not, you might have to install Jest and a few other dependencies. You can read more about it on the official [Jest documentation page](https://jestjs.io/docs/en/tutorial-react). 

如果，你在使用 create-react-app，它已经安装了 Jest。否则，你需要安装 Jest 和一些依赖。更多信息可以查看 [Jest 官方文档](https://jestjs.io/docs/en/tutorial-react)。

#### react-test-renderer

Even if you're using create-react-app, you will need to install this package to render snapshots. Snapshot testing is a part of the Jest library. So, instead of rendering the UI of the entire application, you can use the test renderer to quickly generate a serializable HTML output from the virtual DOM. You can install it as follows:

即使，你使用的是 create-react-app，你还是需要安装这个依赖包获取渲染快照。快照测试是 Jest 的一部分。你可以使用 react-test-renderer 通过虚拟 DOM 快速生成 HTML 结构，因此，不必渲染整个应用的 UI。通过以下方法安装：

```shell
yarn add react-test-renderer
```

#### ReactTestUtils and Enzyme

**react-dom/test-utils** consists of some of the test utilities provided by the React team. Alternatively, you can use the [Enzyme](https://github.com/airbnb/enzyme) package released by Airbnb. Enzyme is a whole lot better than ReactTestUtils because it is easy to assert, manipulate, and traverse your React Components’ output. We will start our tests with React utils and then transition to Enzyme later on.

React 团队提供的 **react-dom/test-utils** 包含了一些测试工具。另外，你也可以使用 Airbnb 发布的 [Enzyme](https://github.com/airbnb/enzyme)。Enzyme 比 ReactTestUtils 更好，因为它更容易断言、操作和遍历 React 组件的输出。我们会是使用 React 工具开始我们的测试，稍晚时候会转到 Enzyme。

To install Enzyme, run the following command.

执行以下命令安装 Enzyme。

```shell
yarn add enzyme enzyme-adapter-react-16
```

Add the code to **src/SetupTests.js**.

把以下代码添加到 **src/SetupTests.js**。

```js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
 
configure({ adapter: new Adapter() });
```

There is more information about this in the [Testing Components](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#testing-components) section of the create-react-app page. 

create-react-app 有更多有关[组件测试](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#testing-components)的信息。

#### Setting Up a Demo App and Organizing Tests

#### 演示和测试

We will be writing tests for a simple demo application that displays a master/detail view of a list of products. You can find the demo application in our [GitHub repo](https://github.com/tutsplus/testing-components-in-react-using-jest-the-basics). The application consists of a container component known as ```ProductContainer``` and three presentational components: ```ProductList```, ```ProductDetails```, and ```ProductHeader```. 

我们将为一个简单的应用编写测试，该应用会显示一个产品列表和产品详情。在我们的 [GitHub 代码仓](https://github.com/tutsplus/testing-components-in-react-using-jest-the-basics)你可以找到演示应用。应用包含一个容器组件 ```ProductContainer``` 和三个展示组件：```ProductList```, ```ProductDetails``` 和 ```ProductHeader```.

##### Directory Structure

##### 目录结构

```
.
├── package-lock.json
├── package.json
├── public
│   ├── index.html
│   └── manifest.json
├── src
│   ├── components
│   │   ├── App.js
│   │   ├── ProductContainer.js
│   │   ├── ProductDetails.jsx
│   │   ├── ProductHeader.js
│   │   ├── ProductList.jsx
│   ├── index.js
│   └── style.css
```

This demo is a good candidate for unit testing and functional testing. You can test each component in isolation and/or test the product listing functionality as a whole. 

这个演示是单元测试和功能测试的理想选择。你可以单独的测试每个组件，也可以测试整个产品列表的功能。

Once you've downloaded the demo, create a directory with the name **__tests__** inside **/src/components/**. You can then store all the test files related to this functionality inside the **__tests__** directory. Testers usually name their test files as either **.spec.js** or **.test.js**—for example, **ProductHeader.test.js** or **ProductHeader.spec.js**. 

一旦你下载了演示代码，可以在 **/src/components/** 中新建一个名为 **__tests__** 的目录。你可以把所有相关的测试文件放在 **__tests__** 目录。通常测试人员会把文件命名为 **.spec.js** 或者 **.test.js** - 例如，**ProductHeader.test.js** 或者 **ProductHeader.spec.js**。

### Writing Basic Tests in React

### 为 React 编写基本的测试

Create a **ProductHeader.test.js** file if you haven't already. Here is what our tests are basically going to look like:

新建一个 **ProductHeader.test.js** 文件。测试文件看起来基本上就像这样：

**src/components/__tests__/ProductList.test.js**

```js
describe('ProductHeader', () => {
 
  it('passing test', () => {
    expect(true).toBeTruthy();
  })
 
  it('failing test', () => {
    expect(false).toBeTruthy();
  })
})
```

The test suite starts with a ```describe``` block, which is a global Jest function that accepts two parameters. The first parameter is the title of the test suite, and the second parameter is the actual implementation. Each ```it()``` in a test suite corresponds to a test or a spec. A test contains one or more expectations that check the state of the code. 

每一组测试用例都是以 ```describe``` 开始，它是 Jest 全局的方法可以接收两个参数。第一个参数是当前这组测试的标题，第二个参数是具体的测试逻辑。每一个 ```it()``` 都对应一个测试。一个测试包含一个或者多个用于校验代码的期望值。

```js
expects(true).toBeTruthy();
```

In Jest, an expectation is an assertion that either returns true or false. When all the assertions in a spec are true, it is said to pass. Otherwise, the test is said to fail.

在 Jest 中，每一个期望值就是一个断言可以返回真或者假。当所有的断言都返回真，也就是说测试通过。否则，测试失败。

For instance, we've created two test specs. The first one should obviously pass, and the second one should fail. 

在实例中，我们创建了两个测试用例。第一个绝对会通过，第二个是失败。

Note: ```toBeTruthy()``` is a predefined matcher. In Jest, each matcher makes a comparison between the expected value and the actual value and returns a boolean. There are many more matchers available, and we will have a look at them in a moment.

注意：```toBeTruthy()``` 是一个内置的 matcher。Jest 中，每一个 matcher 都会在预期值和真实的值之间做比较，然后返回一个布尔值。有很多的 matcher 可用，稍后我们会一起来看一下。

### Running the Test Suite

### 运行测试用例

create-react-app has set up everything that you need to execute the test suite. All you need to do is run the following command:

create-react-app 已经为你设置好了所有需要的执行的测试用例。你只需要执行以下命令就好：

```shell
yarn test
```

You should see something like this:

你应该会看到以下结果：

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/28934/image/Testing-Components-in-React-FailingSpec.png)

To make the failing test pass, you have to replace the ```toBeTruthy()``` matcher with ```toBeFalsy()```.

让失败的用例通过，你只需把 ```toBeTruthy()``` 替换成 ```toBeFalsy()```。

```shell
expects(false).toBeFalsy();
```

That's it!

就这样！

### Using Matchers in Jest

### Jest 中的 Matcher

As mentioned earlier, Jest uses matchers to compare values. You can use it to check equality, compare two numbers or strings, and verify the truthiness of expressions. Here is the list of popular matchers available in Jest. 

如前所述，Jest 用 matcher 做比较。你可以用来检测两个数字或者字符串是否相等，也可以验证表达式的真假。在这我列出一些 Jest 中常用的 matcher。

- ```toBe();```
- ```toBeNull()```
- ```toBeDefined()```
- ```toBeUndefined()```
- ```toBeTruthy()```
- ```toBeFalsy()```
- ```toBeGreaterThan()```
- ```toBeLesserThan()```
- ```toMatch()```
- ```toContain()```

This is just a taste. You can find all the available matchers in the [reference docs](https://jestjs.io/docs/en/using-matchers).

这只是一个引子。你可以在[参考文档](https://jestjs.io/docs/en/using-matchers)中找到所有的 matcher。

### Testing a React Component

### 测试 React 组件

First, we'll be writing a couple of tests for the ```ProductHeader``` component. Open up the **ProductHeader.js file** if you haven't already. 

首先，我们将会为 ```ProductHeader``` 编写一些测试用例。打开 **ProductHeader.js** 文件，准备开始吧。

**src/components/ProductHeader.js**

```js
import React, {Component} from 'react';
    
class ProductHeader extends Component  {
    render() {
        return(
            <h2 className="title"> Product Listing Page </h2>
        );
    }
};
export default ProductHeader;
```

Are you curious to know why I used a class component here instead of a functional component? The reason is that it's harder to test functional components with ReactTestUtils. If you're curious to know why, [this Stack Overflow discussion](https://stackoverflow.com/questions/36682241/testing-functional-components-with-renderintodocument) has the answer.

你是否好奇为什么我用类组件代替了功能组件？这是因为用 ReactTestUtils 测试功能组件比较难。如果，你好奇为什么，[Stack Overflow 上的讨论](https://stackoverflow.com/questions/36682241/testing-functional-components-with-renderintodocument)有答案。

We could write a test with the following assumptions:

1. The component should render an ```h2``` tag.
1. The ```h2``` tag should have a class named ```title```.

编写测试用例我们要有以下几个假设：

1. 组件应该有一个 ```h2``` 标签
1. ```h2``` 应该有个类名 ```title```

To render a component and to retrieve relevant DOM nodes, we need ReactTestUtils. Remove the dummy specs and add the following code:

渲染组件并检验相关 DOM，我们需要 ReactTestUtils。移除假设的用例并添加以下代码：

**src/components/__tests__/ProductHeader.test.js**

```js
import React from 'react';
import ReactTestUtils from 'react-dom/test-utils'; 
import ProductsList from '../ProductsList';
 
describe('ProductHeader Component', () => {
 
    it('has an h2 tag', () => {
     //Test here
    });
   
    it('is wrapped inside a title class', () => {
     //Test here
    })
  })
```

To check for the existence of an ```h2``` node, we will first need to render our React elements into a DOM node in the document. You can do that with the help of some of the APIs exported by ```ReactTestUtils```. For instance, to render our ```<ProductHeader/>``` component, you can do something like this:

为了检测是否有 ```h2```，首先，我们需要把 React 元素渲染在文档中。你可以借助 ```ReactTestUtils``` 中的 API。例如，渲染 ```<ProductHeader/>``` 组件，你可以这么做：

```js
const component = ReactTestUtils.renderIntoDocument(<ProductHeader/>);
```

Then, you can extract the ```h2``` tag from the component with the help of ```findRenderedDOMComponentWithTag('tag-name')```. It checks all the child nodes and finds the node that matches the ```tag-name```. 

然后，你可以借助 ```findRenderedDOMComponentWithTag('tag-name')``` 在组件中查找 ```h2``` 标签。它会检测所有的子节点并且找到那个与 ```tag-name``` 相匹配的节点。

Here is the whole test spec.

这是整个测试用例。

```js
it('has an h2 tag', () => {
 
    const component = ReactTestUtils.renderIntoDocument(<ProductHeader/>);    
    var h2 = ReactTestUtils.findRenderedDOMComponentWithTag(
     component, 'h2'
   );
   
});
```

Try saving it, and your test runner should show you that the test has passed. That's somewhat surprising because we don't have an ```expect()``` statement like in our previous example. Most of the methods exported by **ReactTestUtils** have expectations built into them. In this particular case, if the test utility fails to find the ```h2``` tag, it will throw an error and the tests will automatically fail.

保存它，并且执行测试代码你应该会看到测试通过的结果。有点奇怪，因为没有像之前列子中 ```expect()``` 语句。**ReactTestUtils** 导出的大部分方法已经内置了。这个实例中，如果测试工具没有找到 ```h2``` 标签，它会抛出一个错误，并且测试也不会通过。

Now, try creating the code for the second test. You can use ```findRenderedDOMcomponentWithClass()``` to check if there's any node with the class 'title'.

现在，我们来编写第二个测试代码。你可以用 ```findRenderedDOMcomponentWithClass()``` 去检测任何一个节点是否有类 'title'。

```js
it('has a title class', () => {
 
  const component = ReactTestUtils.renderIntoDocument(<ProductHeader/>);    
  var node = ReactTestUtils.findRenderedDOMComponentWithClass(
   component, 'title'
 );
})
```

That's it! If all goes well, you should see the results in green. 

就这些！所有的都很好，你应该看到所有的结果都是成功的。

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/28934/image/Testing-Components-in-React-PassingSpec.png)

### Conclusion

### 总结

Although we just wrote two test specs, we've covered a lot of ground in the process. In the next article, we'll write some full-fledged tests for our product listing page. We'll also replace ReactTestUtils with Enzyme. Why? Enzyme offers a high-level interface that's very easy to use and developer-friendly. Stay tuned for the second part!

尽管，我们只是编写了两个测试用例，但是，在整个过程中我们已经涵盖了很多知识。在下篇文章中，我们将会为产品列表页面编写完整的测试用例。我们也会用 Enzyme 替换 ReactTestUtils。为什么？Enzyme 提供了一些易于使用并且对开发者友好的高阶接口。敬请期待第二部分！

If at any point you feel stuck or need help, let us know in the comments. 

如果，你有任何的不明白的地方需要帮助的话，可以提交评论让我们知道。





