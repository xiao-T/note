## Webpack (v4) Code Splitting using SplitChunksPlugin
## Webpack (v4) 中用 SplitChunksPlugin 分割代码

> 原文地址: <https://itnext.io/react-router-and-webpack-v4-code-splitting-using-splitchunksplugin-f0a48f110312/>    
> *本文版权归原作者所有，翻译仅用于学习。*  

- - - -

Introduction to code splitting in Webpack v4 and with `import()` function → lazy loading routes in React using React Router v4

介绍在 Webpack v4 如何使用 `import()` 分割代码并实现路由的懒加载

![](https://cdn-images-1.medium.com/max/2000/1*lOd6qUEyWoSQrzrCj0yZog.jpeg)

***Webpack 4*** comes with lot of changes and improvisations. One of them is ```SplitChunksPlugin```, which helps webpack break a JavaScript bundle into different chunks. Webpack uses this plugin internally and we can enable/configure it inside `optimization` block of `webpack.config.js`.

***Webpack 4*** 带来了很多的改变和提升。`SplitChunksPlugin` 就是其中之一，它可以把文件分割成不同的块。Webpack 内部也使用这个插件，我可以在 `webpack.config.js` 的配置项 `optimization` 中配置启用它。

If you need basic introduction to Webpack, you can follow my article ***[here](https://itnext.io/how-to-write-a-frontend-javascript-plugin-using-es6-sass-webpack-a1c6d6fdeb71)*** on “**How to create JavaScript plugin using Webpack**”. In nutshell, Webpack is used to transform **ES2015+** syntax (*or even **TypeScript***) into cross-browser ES5 syntax. Not only that, we can create a single JavaScript file (*called as **bundle***) from different files we might have separated for better code management. But loading a single bundle might be painful to load over network at once and we might not need all the code at once. Hence it’s better to break down main bundle into few chunks (*files*) and lazy load (*using Ajax request*) some chunks only when we need it.

如果，你需要了解 Webpack，你可以看下我的 ***[文章](https://itnext.io/how-to-write-a-frontend-javascript-plugin-using-es6-sass-webpack-a1c6d6fdeb71)*** : "**如何用 Webpack 创建 JavaScript 插件**"。简单的来说，Webpack 可以把 **ES2015+** 语法（*或者 **TypeScript***）转化成跨浏览器的 ES5 语法。不仅仅如此，我们还可以把不同的文件打包在一起（*也称为 **bundle***），这样我们就可以很好的把代码分开管理。但是，一次加载所有的文件会比较痛苦，并且，我们不需要这么多代码。因此，更好的做法是把文件分块，并且在我们需要的时候才去加载（*使用 Ajax*）。

In this article, we will explore `SplitChunksPlugin`, how to separate code into different chunks, how to lazy load chunks on demand and how to integrate lazy loading in React Router v4 (*load component chunks when route changes*).

在这篇文章中，我们将会谈论 `SplitChunksPlugin` 是如何把代码分块的，如何实现按需加载，如何使用 React Router v4 实现懒加载（*当路由改变的时候加载组件*）。

### Setting up boilerplate

### 设置样板
Let’s create a sample project with Webpack 4. I would recommend cloning my ***[js-plugin-starter](https://github.com/thatisuday/js-plugin-starter)*** repo, especially it’s `webpack-4` branch, since we are working with Webpack 4.

我们来创建一个简单的 Webpack 4 工程。我推荐克隆我的 ***[js-plugin-starter](https://github.com/thatisuday/js-plugin-starter)*** 仓库。它是 `webpack-4` 一个分支，目前我们已经在使用 Webpack 4。

```shell
git clone -b webpack-4 https://github.com/thatisuday/js-plugin-starter webpack-scp-demo
```

This will create `webpack-scp-demo` folder and copy minimal code we need to bootstrap a simple application. But we need to run `npm install` first to install Webpack 4 and other packages it needs.

以上代码将会创建 `webpack-scp-demo` 文件夹并且会复制应用必要的代码。但是，我们还需要执行 `npm install` 安装 Webpack 4 和其它的依赖。

Since, `js-plugin-starter` was meant for with different purpose and you can ***[read it here](https://itnext.io/how-to-write-a-frontend-javascript-plugin-using-es6-sass-webpack-a1c6d6fdeb71)*** , it’s `webpack.config.js` has bit more to it. Hence, I advice you to remove few things from there to get a fresh start. Just replace the `output` object with below as of now.

因为，`js-plugin-starter` 有不同的目的，你可以阅读 ***[这篇文章](https://itnext.io/how-to-write-a-frontend-javascript-plugin-using-es6-sass-webpack-a1c6d6fdeb71)*** , `webpack.config.js` 的内容有点多。所以，我建议你删除一些东西，以便重新开始。只需用下面的代码替换掉对象中的 `output`。

```jsx
output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js',
    publicPath: '/'
}
```

With above configuration, webpack will output `main.js` bundle inside `dist` folder. Everything else inside `webpack.config.js` is pretty basic and I don’t feel it is necessary to explain it here, since it is outside of the scope of this article. Now, remove `src` and `dist` folders from the project directory and replace `index.html` with simple HTML boilerplate like below.

根据以上配置，webpack 将会在 `dist` 文件夹中输出 `main.js`。`webpack.config.js` 中其它事情非常简单，我感觉没有必要去一一解释，这些也不是这篇文章的目的。现在，在工程目录中删除 `src` 和 `dist` 两个文件夹，并用以下的样本替换 `index.html` 里面的内容。

```html
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Webpack SCP Demo</title>
    </head>
    <body>
        <div id="app-root">
            Hello World!
        </div>
    </body>
</html>
```

`src` folder should contain `index.js` file, which is very important because it’s the entry point of Webpack. Hence, you can create `src/index.js` file manually and add simple `console` statement or run below command.

`src` 应该包含 `index.js` 文件。它作为 Webpack 的入口非常重要。因此，你可以手动创建 `src/index.js` 文件并添加一句简单 `console` 语句或者执行下面的命令行。

```shell
mkdir src && echo "console.log('Webpack 4')" > src/index.js
```

Now, give your project a spin by running below command

现在，执行下面的命令行让你工程运行起来

```shell
npm run start
```

![](https://cdn-images-1.medium.com/max/1600/1*uuY0CWk-_Tv7BGnWpEC6CA.png)

### Integrating React

### 整合 React

Now, let’s get back to real business and integrate ***React***. For that, we need `react`, `react-dom` and `react-router-dom` packages. As well as, we need `babel-preset-react` for development.

现在，让我们回到真实业务上来整合 ***React***。为此，我们需要 `react`, `react-dom` 和 `react-router-dom`。除此之外，开发阶段我们还需要 `babel-preset-react`。

```shell
npm i react react-dom react-router-dom
npm i -D babel-plugin-react
```

Make sure to add `'react'` into `presets` list of `.babelrc` file.

确保在 `.babelrc` 文件的 `presets` 列表中添加 `'react'` 选项。

Let’s create simple React ***component*** which prints ***Hello React!*** message. Our `index.js` file should look like below.

让我们创建一个可以输出 ***Hello React*** 的简单 ***组件***。我们的 `index.js` 看起来就像下面这样。

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
// create sample App component
class App extends React.Component {
    constructor( props ) {
        super( props );
    }
    render() {
        return(
            <h1>Hello React!</h1>
        );
    }
}
// render inside `app-root` element
ReactDOM.render( <App />, document.getElementById( 'app-root' ) );
```

![](https://cdn-images-1.medium.com/max/1600/1*q_7Maje_PqqAa7nUk43gEQ.png)

Bravo. We have achieved a lot so far. But we need to make our React application a little bigger or else, there is no point of code splitting.

哇哦，目前我们已经做了这么多。但是，我们还是需要让我们的 React 应用更复杂一些，因为现在没有切入点，无法分割代码。

Let’s enhance our demo app and make a good looking Single Page Application. After that, we will work on how to optimize our application using Webpack’s `splitChunksPlugin` and do other things.

让我们增强我们 demo 应用，让它看起来是一个单页面应用。然后，我们将会用 Webpack 的 `splitChunksPlugin` 来优化应用和其它事情。

We are going to use ***React Router v4*** for building Single Page Application (***SPA***). If you are not familiar with React Router syntaxes, read my article below which covers basically everything of React Router.

我们会用 ***React Router v4*** 来构建单页面应用（***SPA***）。如果，你不熟悉 React Router 语法，可以阅读下面这篇文章，里面介绍了 React Router 大部分语法。

[Basics of React Router v4 – ITNEXT](https://itnext.io/basics-of-react-router-v4-336d274fd9e0)

We are building a very simple application with 3 views viz.***home***, ***about*** and ***contact***. Each view has  `h1` heading that prints respective component name. Hence, our  `App` component should look like below. Create `styles.scss`  in `src` folder to apply some CSS styles.

这个简单的应用只有 3 个组件。***home***, ***about*** 和 ***contact***。每个组件中都会有个 `h1`  用来显示相关的组件名称。所以，我们的  `App`  组件如下所示。在  `src`  文件夹中创建  `styles.scss`   文件用来设置相关的 CSS。

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter, Switch, NavLink as Link, Route } from 'react-router-dom';

import './styles.scss';

// home route component
const HomeComponent = ( props ) => {
    return (
        <h1>Home Component!</h1>
    );
}

// about route component
const AboutComponent = ( props ) => {
    return (
        <h1>About Component!</h1>
    );
}

// contact route component
const ContactComponent = ( props ) => {
    return (
        <h1>Contact Component!</h1>
    );
}

// create sample App component
class App extends React.Component {
    constructor( props ) {
        super( props );
    }

    render() {
        return(
            <BrowserRouter>
                <div>
                    <div className="menu">
                        <Link exact to="/" activeClassName="active">Home</Link>
                        <Link to="/about" activeClassName="active">About</Link>
                        <Link to="/contact" activeClassName="active">Contact</Link>
                    </div>
                    
                    <Switch>
                        <Route exact path="/" component={ HomeComponent } />
                        <Route path="/about" component={ AboutComponent } />
                        <Route path="/contact" component={ ContactComponent } />
                    </Switch>
                </div>
            </BrowserRouter>
        );
    }
}

// render inside `app-root` element
ReactDOM.render( <App />, document.getElementById( 'app-root' ) );
```

```scss
html, body {
    font-size: 14px;
    font-family: sans-serif;

    .menu{
        >a {
            display: inline-block;
            margin: 0 10px;
            text-decoration: none;

            &.active{
                text-decoration: underline;
            }
        }
    }
}
```



![](https://cdn-images-1.medium.com/max/1600/1*nyiyNG-jU55_HQWMa0I1gQ.gif)

### Understanding chunks

### 理解文件块

As of now, we are only loading one JavaScript file (*see above screenshot*), which does everything in our application and that is `main.js` of the build.

目前为止，我们只会加载一个 JavaScript 文件 （*看上面的截图*），整个应用的逻辑都由 `main.js` 处理。

As we discussed, it’s not a very good idea to have **one big fat file** doing everything. Hence, we have following use cases where we might need to break down `main.js` by **extracting chunks** from it.

正如我们之前讨论过的，如果，在一个**非常大的文件中**处理所有的事情并不好。所以，接下来我们会把 `main.js` 分割成**多个块**。

> A ***chunk*** is code which will break apart from ***main bundle*** that is `main.js` and form it’s own file known as ***chunk file***. There are two types of chunks viz. sync and async. ***Sync*** chunks are loaded synchronously with `main.js` and you would see `<script src="some-chunk.js"></script> `element in source code. ***Async*** chunks are loaded on demand (***lazy loaded***) and you would see network request for async chunk files in developers tool. These chunks are spitted from `main.js` based on some conditions and we need to tell that to the Webpack. This is done through Webpack plugin known as `splitChunksPlugin`.  

- **Vendor Chunk** : Create separate files for **vendor code** (*3rd party code coming from `node_modules`*). A single `vendor.js` file will suffice. Any vendor code used inside `index.js` (*`import` statements of `npm` modules*) will be break from it and form `vendor.js` which will be loaded synchronously with `main.js`.

- **Async Chunks**: Create separate files for code which can be lazy loaded. Like a file for every **Route** of **React router** which can be **lazy loaded when route is changed**. Webpack inject some code into `main.js` which takes care of lazy loading async chunks and stops from loading same chunks again and again. When route changes, React router calls a Webpack function to load a chunk file and Webpack after done loading runs it, which chunk would internally ask React to do something.

- **Common Chunk**: Create **common file** from code which is shared between different chunks. For example, if **10 routes** create **10 different async chunks** and these chunks have a common `import` statement, then code associated with that `import` statement will get injected into respective chunk files separately. That would load same piece of code every single time we change the route, which is not a very good for UX. Instead, we could take out common code shared between different chunks and create `common.js` file which will be loaded synchronously with `main.js` beforehand.

> 一个代码 ***块*** 就是从 `main.js` 分割下来的一部分，也就是我们熟知的 ***文件块***。一共有两种块：同步和异步。***同步*** 块伴随 `main.js` 一起加载，然后，你会在源代码中看到类似 `<script src="some-chunk.js"></script> ` 的元素。***异步*** 块会按需加载（ ***懒加载*** ），通过开发者工具你将会看到异步文件的网络请求。这些文件是基于特定的逻辑从 `main.js`  分割出来的，我需要告诉 Wbpack 如何分割代码块。`splitChunksPlugin` 就是用来处理这些问题的。  

- **Vendor 块**：为 **vendor 代码**（*第三方来自`node_modules`的代码*） 单独创建文件。一个 `vendor.js` 就足够了。 `index.js` 里面任何的代码（*`import` 语句引入的 `npm`的模块*）都可以被打包进来，`vendor.js` 会和 `main.js`同步加载。

- **Async 块**：为那些可以延迟加载的代码单独创建文件。比如：为每一个**路由**创建单独文件，它们可以在**路由改变的时候按需加载**。Webpack 会在 `main.js` 注入一些代码来管理异步模块并避免重复加载。当路由发生改变，React router 会调用 Webpack 的方法加载异步模块，加载完成后，React 内部会执行它。

- **Common 块**：为那些在多个模块共用的代码创建 **common 文件**。例如，如果 **10 个路由** 生成了 **10 个不同异步模块**，它们有一个共同的 `import` 语句，它将会被注入到各自相关的模块。我们每次改变路由都会加载相同的代码片段，这对用户体验来说并不友好。相反，我们可以为这些共用的代码创建一个 `common.js` 文件，让它和 `main.js` 同步加载。

### Webpack’s import() function

### Webpack 的 import() 的方法

Webpack gives us an [import(*module*)](https://webpack.js.org/api/module-methods/#import-) function which resolves a module asynchronously (***dynamically***) and returns a promise. When that promise resolves, it returns whatever that module is exporting. A module can be a ***npm module*** or a ***local file***, anything that native `require` supports. Let’s see a small example. Create `/src/async.js` file and export some values.

Webpack 提供了一个 [import(*module*)](https://webpack.js.org/api/module-methods/#import-)  方法，它可以让模块异步加载（***动态加载***）并且返回一个 promise。当 promise 成功，它就会返回模块导出的内容。一个模块可是 ***npm module*** 或者是 ***本地文件***，并且对 `require` 也支持。让我们看一个示例。创建一个 `/src/async.js` 文件，并导出一些变量。

```jsx
// async.js
export default "HEY, I AM ASYNC!";
export const HELLO = "HELLO WORLD!";
```

Let’s import this file anywhere inside `index.js` using `import()` function. In `then` callback, we should receive all the exports of `async.js`.

我们 `index.js` 内部用  `import()`  方法引入这个文件。在 `then`
 的回调中，我们应该就可以接收到  `async.js` 导出的值了。
```js
// import async.js
import( './async.js' ).then( ( data ) => {
    console.log( data );
} );
```

![](https://cdn-images-1.medium.com/max/1600/1*2yCMGayJJp-M9qw33OaRkw.png)

After build, you are likely to get above error. This is due to `babel` not recognizing `import` function as it would conflict with `import` syntax of **ES2015+**. Hence, we need babel plugin `syntax-dynamic-import` which we can install using `npm i -D babel-plugin-syntax-dynamic-import`. Make sure to add `'syntax-dynamic-import'` inside `plugins` section of `.babelrc` file.

构建后，你会看到上面演示中的错误。这是由于 `babel`  不支持 `import`
 方法，它会和 **ES2015+** 的  `import`  语法发生冲突。因此，我们需要 babel 插件 `syntax-dynamic-import` ，可以用 `npm i -D babel-plugin-syntax-dynamic-import` 来安装。然后，确保把 `'syntax-dynamic-import'`  添加到 `.babelrc` 文件的 `plugins` 选项中。

![](https://cdn-images-1.medium.com/max/1600/1*fq_ArG93ls0IfGKBdl418w.png)

Now, we can see the exports. Also, if you see `Network` tab, you should see an extra file loading asynchronously.

现在，我看到可以正常运行了。如果，你打开了  `Network`  标签页，你应该会看到一个异步加载的文件。

![](https://cdn-images-1.medium.com/max/1600/1*hF5lF0vJuNEs06jyUo1nvg.gif)

`0.main.js` is a async chunk that Webpack has created because `async.js` module was dynamically imported inside `index.js`. As you can tell from the source code of `0.main.js`, we know that this file contains all exported values from `async.js`.

`0.main.js` 是一个异步模块，这是由于在 `index.js`  内部动态导入了 `async.js` 模块而创建的。从  `0.main.js`  源代码中我们可以发现它包含了所有的 `async.js` 导出的变量。

![](https://cdn-images-1.medium.com/max/2000/1*CE64paft4YfWy5zYjO4BTQ.png)


> Name of chunk files is decided by Webpack while compilation but we can modify it inside `webpack.config.js`, we will discuss about this later.  

> 文件的名字由 Webpack 在编译的时候决定，但是，我们可以在 `webpack.config.js` 更改，稍后我们会讨论这个问题。  

Whenever Webpack sees `import()` function, it is going to create a async chunk file. In browser, `import` function will make network request to load that chunk file, execute it and pass the output inside `then` callback. Hence, you can use `import()` function whenever you want, even inside `setTimeout` function, Webpack doesn’t mind.

不管什么时候，只要 Webpack 看到 `import()`  方法，它就会创建一个异步文件。在浏览器中，`import`   方法将会发起一个网络请求加载文件，执行文件，然后传递给 `then`  回调方法。因此，你可以在任何时候使用 `import()`  方法，甚至在 `setTimeout`   方法中，Webpack 不会在意的。

### import() function with Router

### Router 中的 import () 方法

As we have seen so far, creating a async chunk is very easy. We can export anything from a module and load it dynamically using `import()` function. A module does not know whether it is going to be consumed synchronously or asynchronously, `import()` function handles that.

如你所见，创建一个异步模块如此简单。我们可以通过一个模块导出任何东西，然后，用 `import()`  方法动态加载它。模块不知道应该是同步还是异步，`import()` 方法会处理这个。

Hence, we can export a React component from a file and consume it using `import` function. But since, we need to pass that component as `prop` for `Route`, which falls outside `then` callback of `import()` function, we need to figure out a way to tell `Route` to display a **loading component** while the component is downloading, and once downloaded, replace loading component with downloaded component.

因此，我们可以导出一个 React 组件，然后用 `import`  方法导入它。但是，由于我们需要把它以  `prop`  形式传递给  `Route` ，所以，`import()`  的回调方法 `then` 就无法处理了。我们需要找出一种方式让 `Route`  当组件还未完全加载的时候显示一个 **loading 组件**，一旦加载完成，用正确的组件替换到 loading 组件。

```jsx
<Switch>
    <Route exact path="/" component={ HomeComponent } />
    <Route path="/about" component={ AboutComponent } />
    <Route path="/contact" component={ AsyncContactComponent } />
</Switch>
```

In above example, we have replace `ContactComponent` with `AsyncContactComponent`. `AsyncContactComponent` should be able to show `please wait...` text while file containing `ContactComponent` is downloading (`ContactComponent` is *contained in async chunk file*) and display `ContactComponent` once downloading if finished.

上面的示例中，我们用  `AsyncContactComponent`  替换掉了 `ContactComponent`  。`AsyncContactComponent`  在 `ContactComponent` (`ContactComponent`  包含在 *异步文件中*)下载过程中应该显示 `please wait...` 的提示，一旦加载完成就显示 `ContactComponent`。

Fortunately, somebody has created a module which can handle this type of situation. **[react-loadable](https://github.com/jamiebuilds/react-loadable)** module provides a Higher Order Component (HOC) which asks for two important things. A promise, upon resolution should return a component to be rendered and a placeholder loading component.

很幸运，已经有人创建了一个模块用来处理以上提到的问题。**[react-loadable](https://github.com/jamiebuilds/react-loadable)** 提供了一个高阶组件(HOC)，它由两个要求。当 promise 完成时应该返回一个可用的组件和一个 loading 组件用来做占位显示。

Let’s install `react-loadable` with below command.

用下面的命令行安装 `react-loadable`。

```shell
npm i react-loadable
```

Let’s move our `ContactComponent` to `contact.component.js` file inside `src`.

我们把 `ContactComponent` 移动到 `src` 中的 `contact.component.js` 文件中。

```jsx
// contact.component.js
import React from 'react';
const ContactComponent = ( props ) => {
    return (
        <h1>Contact Component!</h1>
    );
}
export default ContactComponent;
```

Next step is to import `loadable` higher order component (HOC) from `react-loadable` and create a wrapped component from it.

接下来，从 `react-loadable`  用导入 `loadable`  高阶组件(HOC)，然后用它把组件包装一下。

```jsx
import loadable from 'react-loadable';
// contact route component
const LoadingComponent = () => <h3>please wait...</h3>;
const ContactComponentPromise = () => {
    return import('./contact.component');
}
const AsyncContactComponent = loadable( {
    loader: ContactComponentPromise,
    loading: LoadingComponent
} );
```

`loader` is a function which should return a promise. As discussed, that promise on resolution should return a React component. `import` function does exactly the same. `loading` is a placeholder component until the promise is resolved.

`loader`  方法应该返回一个 promise。如上所述，promise 成功后应该返回一个 React 组件。`import` 就是做的就是这个。`loading`  在 promise 未成功前做为一个占位组件显示。

![](https://cdn-images-1.medium.com/max/1600/1*nBk1cwzbUHWVEXnycG5ePQ.gif)

As you can see from above animation, initially when we are **home route**, we just have `main.js` file loaded. Since `ContactComponent` contains in a async chunk, it will be loaded asynchronously. When we change route to **Contact**, `AsyncContactComponent` will be rendered.

在上面的动画中你可以看到，一开始我们的路由是 **home** ，只会有 `main.js`  被加载。由于 `ContactComponent` 保存在异步模块中。当我们把路由变成 **Contact**，它将会异步加载然后，`AsyncContactComponent` 就会被渲染。

Since `AsyncContactComponent` is `react-loadable` component, `import` function will be called which will start downloading chunk file. Until that file is downloaded, `please wait...` message will be shown which comes from `LoadingComponent`. Once chunk file is downloaded, `react-loadable` will replace `LoadingComponent` with `ContactComponent` which is resolved by `import` function inside `AsyncContactComponent` component.

由于，`AsyncContactComponent`  是一个被 `react-loadable` 包装的组件，`import`  方法被调用的时候就会开始加载组件所属的文件。直到文件下载完成，都会显示 `please wait...` 这条信息，它来自 `LoadingComponent` 。一旦异步文件下载完成，`react-loadable` 就会用 `ContactComponent` 把 `LoadingComponent` 替换掉，`ContactComponent`  就是 `AsyncContactComponent` 组件内部通过 `import`  方法返回的组件。

By default, `ContactComponent` receives props from `Route` which contains `history`, `match` and `location`. If you need to pass custom props, use `render` prop instead of `component` prop of `Route`.

通常情况下，`ContactComponent` 会接受来自 `Route`  的 `history`, `match`  和  `location` 几个属性。如果，你需要传递自定义属性，就需要用 `Route` 中的 `render`   代替 `component`  。

```jsx
<Switch>
    <Route exact path="/" component={ HomeComponent } />
    <Route path="/about" component={ AboutComponent } />
    <Route path="/contact" render={ 
    ( props ) => <AsyncContactComponent { ...props } value="1" />
    } />
</Switch>
```

### Grouping chunks

### 文件分组

So far, we haven’t exactly configured anything for making chunks. We just used `import` function which made async chunks on it’s own. Let’s now focus on `webpack.config.js` and add some logic to create different chunks we might need besides only async chunks (*as discussed previously*).

目前为止，我们还没有为文件分块做任何配置相关的事情。只是用了 `import`  方法，它会生成一个异步文件。让我们把关注点放在  `webpack.config.js`  上并添加一些逻辑创建不同的文件块，我们还需要异步文件之外的文件（*就如我们之前讨论过的*）。

You can read documentation of **[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)** here and how to configure it. This plugin is implemented by Webpack internally and it’s API is exposed inside `optimization.splitChunks` of `webpack.config.js`.

你可以在此阅读  **[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)** 的文档了解如何配置它。这个插件是由 Webpack 内部实现的，它的 API 就是 `webpack.config.js` 配置中的 `optimization.splitChunks`。

`splitChunks` contains many configuration and it we are going to focus on `cacheGroups` first. `cacheGroups` tells `SplitChunksPlugin` to create chunks based on some conditions, for example, create a separate chunk file for all the code being imported from `node_modules`.

`splitChunks`  包含很多配置，首先我们只会关注 `cacheGroups`。`cacheGroups` 会让 `SplitChunksPlugin`  按照一定逻辑创建不同的块，例如，为所有来自 `node_modules` 代码创建一个单独的文件块。

`chacheGroups` is a plain object with `key` being the name of chunk and `value` being some configuration of that chunk. By default, Webpack ships with `vendors` and `default` cacheGroups but let’s turn those off by setting their value to `false`, else it will just confuse you to understand code splitting.

`chacheGroups` 是一个简单的对象，`key` 定义着文件块的名字，`value` 就是一些相关的配置。默认情况下，Webpack 会为 `vendors` 和 `default` 提供 cacheGroups，但是，我们需要把它们的值设置为 `false`，这是避免对你理解代码分割造成困惑。

```jsx
// optimization
optimization: {
    splitChunks: {
        cacheGroups: {
            default: false,
            vendors: false,
        }
    }
}
```

Let’s create a `vendor` chunk which contain all code from `node_modules` imported in the project. Whenever, Webpack encounters `import` statement of `npm` module, it will push that code inside `vendor` chunk.

让我们创建一个  `vendor` 块，它包含项目中所有来自 `node_modules` 的代码。不管什么时候，只要 Webpack 遇到 `import`  从 `npm` 导入文件，就会把这些代码插入到 `vendor` 块。

```jsx
// optimization
optimization: {
    splitChunks: {
        cacheGroups: {
            default: false,
            vendors: false,
            // vendor chunk
            vendor: {
                // sync + async chunks
                chunks: 'all',
                // import file path containing node_modules
                test: /node_modules/
            }
        }
    }
}
```

We are creating a `vendor` chunk which would create a chunk file from all those import statements of files coming from `node_modules`. `test` value is a RegExp which is matched agains file path of the import. When Webpack encounters an import statement whose file falls inside `node_modules`, it will be added to `vendor` chunks.

我们创建了一个 `vendor` 块，它包含所有的来自  `node_modules`  的文件。`test` 的值是一个正则表达式用来匹配 import 文件的路径。每当 Webpack 遇到 import 语句导入 `node_modules` 的文件时，就会把文件添加到 `vendor` 块中。

Here `chunks` value tells `SplitChunksPlugin` the nature of chunks to consider for evaluation. It’s value can be `initial`, `async` or `all`. `initial` means only add files to the chunk if they are imported inside `sync` chunks. `async` means only add files to the chunk if they are imported inside `async` chunks.

`SplitChunksPlugin` 配置中的 `chunks` 会决定块文件的特性。它的值包含： `initial`, `async` 或者 `all`。`initial` 代表那些块文件内部`同步`引入的块。`async` 代表块文件内部`异步`引入的块。

> `main.js` is considered as synchronous chunk (but it’s not a chunk, technically). *Hence, if a chunkGroup has `initial` or `all` **chunks** value, then `main.js` will be evaluated by Webpack*.  

> `main.js` 被认为是一个同步块（但是，从技术来说它并不是一个块）。*如果，某个 chunkGroup 有值为 `initial` 或者 `all` **chunks**，Webpack 将会把 `main.js` 考虑在内*。  

![](https://cdn-images-1.medium.com/max/1600/1*-dR-DLjCeDab8w3a8911_w.gif)

As you can see from above animation, Webpack is creating `vendor~main.main.js` chunk file which contains all of our vendor code. You are free to add any other vendor files (which might not come from `node_modules`) to this chunk using different `test` RegExp value.

从上面的动画中你可以看到，Webpack 创建了一个 `vendor~main.main.js` 文件块，它包含所有的 vendor 代码。通过设置不同的 `test` 的值，你可以随意添加任何文件（也可以不是 `node_modules` 内的文件）。

By default, `SplitChunksPlugin` prepends key of the chunk to the name of build file. We can avoid that by using `name` key and specify different name.

默认情况下，`SplitChunksPlugin` 根据配置中 name 的值来构建文件的名称。我们也可以不使用 `name`，自己指定一个特定的值。

```jsx
// vendor chunk
vendor: {
    // name of the chunk
    name: 'vendor',
    ...
```

![](https://cdn-images-1.medium.com/max/2000/1*33GZDCYSRAM6TgEoxF1Cbg.png)

If you are still not satisfied with the filename of the chunk, then we need to change [```output.chunkFilename```](https://webpack.js.org/configuration/output/#output-chunkfilename) of `webpack.config.js`. `[name]` will be replaced by `name` value of `chacheGroup`.

如果，你还是对块文件名称不满意，那我们就需要改变 `webpack.config.js` 配置中的  [```output.chunkFilename```](https://webpack.js.org/configuration/output/#output-chunkfilename) 的值。`[name]` 将会替换掉 `chacheGroup` 配置中 `name` 的值。

```jsx
output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js',
    chunkFilename: '[name].js',
    publicPath: config.get('publicPath')
},
```

![](https://cdn-images-1.medium.com/max/2000/1*GCx4sGAdQAdNj7Y9aMqzCQ.png)

Let’s convert all of our routes to load asynchronously. First, you need to create `home.component.js` and `about.component.js` and import them inside `index.js` the same way we imported `contact.component.js`.

让我们把所有的路由都换成异步加载。首先，你需要创建 `home.component.js` 和 `about.component.js` 并在 `index.js` 内部按照之前引入 `contact.component.js` 的方式引入它们

```jsx
// home component
const AsyncHomeComponent = loadable( {
    loader: () => import( './home.component' ),
    loading: LoadingComponent
} );
// about component
const AsyncAboutComponent = loadable( {
    loader: () => import( './about.component' ),
    loading: LoadingComponent
} );
// contact component
const AsyncContactComponent = loadable( {
    loader: () => import( './contact.component' ),
    loading: LoadingComponent
} );
...
<Switch>
    <Route exact path="/" component={ AsyncHomeComponent } />
    <Route path="/about" component={ AsyncAboutComponent } />
    <Route path="/contact" component={ AsyncContactComponent } />
</Switch>
```

![](https://cdn-images-1.medium.com/max/1600/1*bMWTKj38NK7igL1RAooHew.gif)

Don’t be surprised with the file names of `async` chunks because we modified `output.chunkFilename` of `webpack.config.js`. If you don’t like number based file name, you can use `webpack.NamedChunksPlugin` and transform chunk names. There is another module [`webpack-async-chunk-names-plugin`](https://www.npmjs.com/package/webpack-async-chunk-names-plugin) which remembers the filename of async import and uses that as chunk file name.

不要对`异步`文件的名字感到奇怪，这是因为我们改变了 `webpack.config.js` 配置中 `output.chunkFilename` 导致的。如果，你不喜欢用数字命名的方式，你可以使用 `webpack.NamedChunksPlugin` 来处理文件的名字。[`webpack-async-chunk-names-plugin`](https://www.npmjs.com/package/webpack-async-chunk-names-plugin) 会记住异步文件导入时的名字，然后用它来给块文件命名。

![](https://cdn-images-1.medium.com/max/2000/1*SMl4x4vwD1NIkuG4N0LTHA.png)

Right now in our application, we have 1 bundle file (*main.js*), 1 sync chunk (*vendor.js*) file and 3 async chunk files (*route component files*). I advice you to check source code of these files from **Chrome Developer Tools** to understand what exactly they contain.

现在，在我们的应用中，有一个 bundle 文件 (*main.js*)，一个同步块文件(*vendor.js*)和三个异步块文件(*路由组件相关的文件*)。我建议你打开**Chrome 开发者工具**查看文件的源代码，去看看它们具体包含什么。

Now, let’s introduce a common dependency between 3 async files. We will create a `HelloComponent` which will be used inside other three components.

现在，我们来介绍一个 3 个异步文件共同依赖的文件。我们会创建一个 `HelloComponent` 组件，它将会被其它三个组件引用。

```jsx
// hello.component.js
import React from 'react';
const HelloComponent = ( props ) => {
    return (
        <span style={ { color: 'red' } }>Hello Component!</span>
    );
}
export default HelloComponent;
```

Import this component inside `home.component.js` using ES6 `import` syntax.

在 `home.component.js` 组件中用 ES6 `import` 语法引入这个组件。

```jsx
// home.component.js
import React from 'react';
import HelloComponent from './hello.component';
const HomeComponent = ( props ) => {
    return (
        <h1>Home Component! <HelloComponent /></h1>
    );
}
export default HomeComponent;
```

Do the same for `about.component.js` and `contact.component.js`.

在 `about.component.js` 和 `contact.component.js` 两个组件中也同样引入这个组件。

![](https://cdn-images-1.medium.com/max/1600/1*p0x1IxHcBU1tqBz7il3d7A.png)

Nothing is changed so far. But if you see source code of all 3 async chunks, you would see source code of `HelloComponent` present in all these files.

目前为止没任何变化。但是，如果你查看 3 个异步文件的源代码，你会发现它们都包含 `HelloComponent` 的代码片段。

![](https://cdn-images-1.medium.com/max/2000/1*ybUutT5yXVZq9gds-zbMgQ.gif)

We could avoid this, if we create `common.js` where if a module is shared between different chunks 2 or more times, then form a new chunk.

如果，一个模块被两个或者更多的组件引用，我们创建一个 `common.js`  就可以避免这种情况。

```jsx
// vendor chunk
vendor: {
    // name of the chunk
    name: 'vendor',
    // async + async chunks
    chunks: 'all',
    // import file path containing node_modules
    test: /node_modules/,
    // priority
    priority: 20
},
// common chunk
common: {
    name: 'common',
    minChunks: 2,
    chunks: 'async',
    priority: 10,
    reuseExistingChunk: true,
    enforce: true
}
```

`reuseExistingChunk` tells `SplitChunksPlugin` to use existing chunk if available instead of creating new one. For example, if any module imported inside common chunk code is part of another chunk already, then instead of creating new chunk for it, older one is reused instead.

`reuseExistingChunk` 代表 `SplitChunksPlugin` 会复用现有的模块而避免创建新的模块。例如，如果任何模块内部已经引入了共用模块代码，后续公共模块的引用都会复用这个已经创建好的模块，而避免创建新的模块。

If a module falls under many chunk groups, then module will be a part of chunk group with higher `priority`. For example, if `common` chunk has no `test` cases and `chunks` set to `all`, that means any imported module (*statically or dynamically*) will be part of `common` chunk as well, even `npm` module. But since, `vendor` chunk has higher priority, `npm` modules are more likely to be a part of `vendor` chunk and reset will stay inside `common` chunk.

如果，一个模块被多个分组引用，那么它在分组中就会有高 `优先级`。例如，如果把 `common` 没有设置 `test` 并且把 `chunks` 设置成 `all`，也就是说任何被引入（*静态或者动态引入*）的文件，甚至是 `npm` 模块，都会做为 `common` 的一部分。但是，由于 `vendor` 有更高的优先级，`npm` 模块都会做为 `vendor` 的一部分，reset 相关就保留在 `common` 块中。

`enforce` value is set to true to force `SplitChunksPlugin` to form this chunk irrespective of the size of the chunk. `SplitChunksPlugin` by default only form chunks if resulting chunk size is greater than 30kb. In out case, `common` chunk contains only `HelloComponent` code which is obviously less than 30kb. `enforce` is necessary here or set value of [`splitChunks.minSize`](https://webpack.js.org/plugins/split-chunks-plugin/#splitchunks-minsize) as minimum as possible.

`enforce` 设置成 true 会强制 `SplitChunksPlugin` 生成文件块，而不管块的大小。默认情况下只有块大于 30kb 的时候 `SplitChunksPlugin`  才会生成块。在我们的案例中，`common` 只包含 `HelloComponent` 的代码，它明显小于 30kb。这种情况下 `enforce` 很有必要，并且尽可能给 [`splitChunks.minSize`](https://webpack.js.org/plugins/split-chunks-plugin/#splitchunks-minsize) 设置一个小的值。

Looks like any imported modules which doesn’t fall inside `vendor` chunk will be a part of `common` chunk **but it can’t**. `minChunks` tells `SplitChunksPlugin` to only inject module inside `common` chunk if and only if they are shared between at least 2 chunks (*sync or async because of `all` value of `chunks`*).

看起来所有引入的模块都会被插入到 `common`，并不会生成 `vendor`，这**不会发生**。`minChunks` 告诉 `SplitChunksPlugin` 只有那些至少被 2 个模块引入（*因为把`chunks`设置成了`all`，不管同步还是异步文件*）的才会被插入到 `common`。

Since, we have 3 async chunks sharing `hello.component.js` module, we should see a `common.js` chunk file loaded synchronously. **BTW, how a chunk file is loaded (whether *synchronously* or *asynchronously*) is decided by Webpack and you can’t force it to do otherwise**.

因为，有 3 个异步模块同时引用了 `hello.component.js`，我们会有到一个`common.js` 文件被同步加载。**顺便说一下，一个文件块是如何加载的（*同步*还是*异步*），是由 Webpack 决定的，你不能改变它。**

![](https://cdn-images-1.medium.com/max/1600/1*T3XjisIEiW-XACXuFn8sRQ.png)

There are other configuration of `SplitChunksPlugin` that can come in handy number of async chunk request browser should sent at once. You can read about those in their official documentation from [**here**](https://webpack.js.org/plugins/split-chunks-plugin).

`SplitChunksPlugin` 还有很多其它的配置，比如，你可以很方便的处理让浏览器一次加载几个异步模块。具体你可以查看它们的[**官方文档**](https://webpack.js.org/plugins/split-chunks-plugin/)。

We have covered almost everything of `SplitChunksPlugin` but I advice you to scribble with it’s API and learn a bit more. For example, **what if some sync chunks and some async chunks share a common module**? What’s your guess? It should be pretty obvious :).

我们已经对 `SplitChunksPlugin` 有了基本的了解，但是，我建议你去看看它的 API ，了解更多的知识。例如，**同步模块和异步模块是否共享同一个模块**？你猜呢？显然是的 :)。

If you need to look at source code of the application we built here, you can look at below GitHub repository.

如果，你需要查看项目源代码，你可以在 GitHub 上看到。

[GitHub - thatisuday/webpack-scp-demo: Webpack SplitChunksPlugin Demo](https://github.com/thatisuday/webpack-scp-demo)
