# Creating a reusable React Query component for your REST API

## 为 REST API 创建可复用的 React Query 组件

> 原文地址: <https://medium.com/@TimKolb/creating-a-reusable-react-query-component-for-your-rest-api-1aa91ef2551b/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/4800/0*AVi4Wvv_YZ7qSpAd)

When implementing a new UI component I always start with the layouting and mock the server commucation by providing *defaultProps* and empty click listeners for user interactions. After completing the UI components I replace the mocked functions and props with the real thing.

每次实现一个新的 UI 组件时，我都是先实现布局，然后，mock 数据用于 *defaultProps*，并提供一个空的点击监听模拟用户交互。然后，用真实的数据替换掉 mock 的函数和 props。

I reinvented the wheel over and over again in every component which consumes data from a server. Using and configuring fetch with HTTP headers, deserialization logic and handling success, error and loading states add many and often duplicate lines to your codebase.

为了实现那些需要从服务端获取数据数据的组件，我一次次的重复着相同的操作。使用和配置 HTTP headers，反序列化逻辑、处理成功或者失败的回调、loading 状态等等，这就导致了代码的重复。

Wouldn’t it be nice to outsource the communication logic to a **reusable component**?

通信逻辑让可**复用的组件**来处理，是不是更好呢？

## 💪 Just do it.

## 💪 开始

![](https://miro.medium.com/max/1400/1*2Yr-Q1TTzwMgP-kCBniF4Q.png)

Working on a React application which consumes an API you have to deal with a bunch of difficulties. For every request you need to handle a loading, error and success state.

React 应用中那些需要使用 API 的组件需要处理大量的问题。针对每个请求，你都需要处理 loading、错误和成功的状态。

Integrating these functionalities in an existing component will increase the complexity of it and does not leverage the component based approach of React.

把所有的功能整合到现有的组件中势必增加组件的复杂度，这并不符合 React 组件化思想。

Computer Science isn’t a new thing anymore and we are rediscovering some rules and tools from the old days. One of them is the rule of separation of concerns.

计算机科学不再是新鲜事物，我们也发现了一些旧时代的规则和工具。其中之一就是：分离。

> Write programs that do one thing and do it well — Doug McIlroy
>
> 编写程序只做一件事情并做好 — Doug McIlroy

Let’s transfer this quote by the inventor of the Unix pipe to React components, where a component in a React application is the corresponding program in a unix system. Provide props to control the behavior of a component and use an universal interface for your output. In JavaScript our universal interface is the function type — this leads us to **Composite Components**.

我们把 Unix 管道发明者的这个理念引入到 React 组件中，React 的组件代表着 Unix 系统中的程序。通过 props 控制组件的行为，实现万能的组件。JavaScript 中通用的类型就是函数 — 这就引出了**复合组件**。

## 🔌 Provide reusable functionality with Composite Components

## 🔌 通过复合组件提供可复用的功能

The composite component pattern is getting promoted by [Kent C. Dodds ](https://medium.com/u/db72389e89d8?source=post_page-----1aa91ef2551b----------------------)which leverages. another design principle in addition to the one-thing-principle: the inversion of control, which shifts the non-core functionalities to another component.

复合组件模式是由 [Kent C. Dodds ](https://medium.com/u/db72389e89d8?source=post_page-----1aa91ef2551b----------------------) 提出并推广的。除了一物理念之外的另外一种设计理念：控制权倒置，将非核心的功能转移到另外一个组件。

Using a Query component gives you the ability to fetch a url — not no less. The decision which jsx-elements to render, based on the query result, is shifted to the using side. The Query component has no strong coupling to any component, not to the url nor any other prerequisite.

使用 Query 组件可以通过 URL 获取数据 — 这是基本功能。根据查询结果，使用者有权决定如何渲染 jsx 元素。Query 组件不会与任何组件、任何  URL 或者其它预设属性耦合。

It is fully customizable wherever it is used. You can provide a custom deserialize function to deal with different response types like *json, text* or *xml*, check for the response status codes and alter the default behavior in place. A state reducer lets you intercept the state updates to change the Query component behavior based on the response results.

不管在什么地方使用，它完全可以定制。你可以提供一个自定义的反序例化函数来处理不同的响应类型，比如：*json、 text* 或者 *xml*，然后，检查响应状态，并在适当的位置改变默认行为。基于响应结果，state reducer 可以让你拦截状态的更新情况来改变 Query 组件的行为。

The Query component is a Composite Component and additionally renders a React Context Provider which wraps its children. This enables the using side to use **Compound Components**. Let’s see this in action:

Query 组件是一个复合组件，另外提供一个 React Context Provider 来包装子组件。这有利于**复合组件**的使用。我们来看看具体的操作：

![img](https://miro.medium.com/max/1400/1*2-SmVcs3FaXAh4PuXZFiZg.png)

If you have dipped your toes into the GraphQL universe you may have seen one of the Query, Mutation or Subscription components provided by the awesome [react-apollo](https://www.apollographql.com/docs/react/) library. These components provide a straight forward API for integrating server communication logic in your React components — even for REST APIs with [apollo-link-rest](https://www.apollographql.com/docs/link/links/rest.html). I am totally into good developer experience when it comes to coding. But there are situations where you do not want to pull in an extra library as dependency. So let’s try to recreate a comparable developer experience on our own for a REST API.

如果，你深入了解过 GraphQL，你应该看到过由 [react-apollo](https://www.apollographql.com/docs/react/) 提供的 Query、Mutation 和 Subscription 组件。这些组件提供了简洁明了的 API，以便在 React 组件中整合服务端的通信逻辑，甚至，使用 [apollo-link-rest](https://www.apollographql.com/docs/link/links/rest.html) 可以处理 REST APIs。我非常喜欢这种良好的编码体验。但是，有些情况下，你并不希望使用这些依赖库。因此，我们可以尝试为我们的 REST API 创建类似的方案。

Let’s take a look at a query component which passes additional information, like the loading and error states of the request, to the consuming child component.

我们先来看看一个 Query 组件会传入哪些额外的信息以便子组件使用，比如：loading 和请求的错误状态。

![](https://miro.medium.com/max/1400/1*WBq3Ij1QRuoH01MI_Jor6w.png)

## ⚙️ Customize its behavior

## ⚙️ 自定义

The Query component is basically a small component which provides the capabilities of *fetch* as a component. Drop it somewhere in your React tree where you need data from a server and your code stays readable.

Query 组件基本上就是一个组件，只是它提供了 *fetch* 的功能。React 应用中任何需要从服务器获取数据的地方都可以使用它，这样也保证了代码的可读性。

Server communication does not consist just of the consuming GET request. Often you want to trigger a request on user interaction to create, update or delete an entity.

服务器通信并不是只有 GET 请求。有时，用户交互也会触发请求，以便创建、更新或者删除一个实体。

We can alter the behavior of the Query component on user interaction. Basically we can alter every fetch option to be able use different HTTP methods like POST or DELETE or change the URL.

根据用户交互我们可以改变 Query 组件的行为。比如，我们可以改变每个 fetch 的选项，以便处理不同的 HTTP 方法，比如：POST、DELETE 或者改变 URL。

![](https://miro.medium.com/max/1400/1*iLz0lkMNUxBe8VoWxRoucQ.png)

## 🛍 Takeaway

## 🛍 外卖

Integrating server communication into your components can clutter your code. Extract recurring request logic to a composite component to be able to reuse it in your application. This approach helps you to keep your code DRY and leverages the component based approach of React and the separation of concerns.

在组件中整合服务器通信代码会让你的代码变得混乱。把重复的请求逻辑提取到一个复合组件中，可以提高应用中代码的复用率。这种模式可以让你的代码 DRY，并利用基于组件的 React 方法和关注点的分离核心思想。

## ✍️ Try it on CodeSandbox

## ✍️ CodeSandbox 演示

[Queyr Component Demo](https://codesandbox.io/s/92n5zmoq2y?from-embed=&file=/src/hooksEdition/useQuery.js)

👋 Hi! I am Tim Kolberger. I work at [Incloud](https://www.incloud.de/) in Darmstadt, Germany, as a full stack web developer. I ❤️ React, GraphQL and JavaScript.

👋 大家好！我是 Tim Kolberger。我是一名全栈 web 开发者，在德国，达姆施塔特的 [Incloud](https://www.incloud.de/) 公司工作。我喜欢 React、GraphQL 和 JavaScript。

