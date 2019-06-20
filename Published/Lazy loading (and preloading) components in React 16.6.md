# Lazy loading (and preloading) components in React 16.6

## 在 React 16.6 中懒加载（或者预加载）组件

> 原文地址: <https://hackernoon.com/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d>   
**本文版权归原作者所有，翻译仅用于学习。**

React 16.6 adds a new feature that makes code splitting easier: ```React.lazy()```.

React 16.6 增加一个新的功能 ```React.lazy()```，它可以很容易分拆代码块。

Let’s see how and why to use this feature with a small demo.

我们通过一个小小的示例来展示如何使用这个功能，并解释为什么要使用。

We have an app that shows a list of stocks. When you click on one stock it shows you a chart:

我们的应用有一个股票列表。当你点击其中一支股票时会显示当前股票的价格图片：

![](https://cdn-images-1.medium.com/max/1600/1*HK_529G8O-O6_6fYEKc9VQ.gif)

That’s all it does. You can read the full code in the [github repo](https://github.com/pomber/react-lazy-preload-demo) (also check the [pull requests](https://github.com/pomber/react-lazy-preload-demo/pulls) to see the diffs and a running version of the app for each change we’ll do).

就这些。你们可以通过 [github](https://github.com/pomber/react-lazy-preload-demo) 阅读全部代码(也可以通过 [pull requests](https://github.com/pomber/react-lazy-preload-demo/pulls) 查看每个版本的却别，每一个版本都对应一个完整的应用)。

For this post, we only care about what’s in the ```App.js``` file:

在这篇文章中，我们只关心 ```App.js``` 文件中的内容：

```jsx
import React from "react";
import StockTable from "./StockTable";

import StockChart from "./StockChart";

class App extends React.Component {
  state = {
    selectedStock: null
  };
  render() {
    const { stocks } = this.props;
    const { selectedStock } = this.state;
    return (
      <React.Fragment>
        <StockTable
          stocks={stocks}
          onSelect={selectedStock => this.setState({ selectedStock })}
        />
        {selectedStock && (
          <StockChart
            stock={selectedStock}
            onClose={() => this.setState({ selectedStock: false })}
          />
        )}
      </React.Fragment>
    );
  }
}

export default App;
```



We have an ```App``` component that receives a list of stocks and shows a ```<StockTable/>```. When a stock is selected from the table, the ```App``` component shows a ```<StockChart/>``` for that stock.

我们有一个 ```App``` 组件，它接收一个股票列表，并显示一个 ```<StockTable/>```。当一支股票被选中，组件 ```App``` 就会显示一个 ```<StockChart/>``` 组件。

What’s the problem? Well, we want our app to be *blazing fast* and show the ```<StockTable/>``` as fast as possible, but we are making it wait until the browser downloads (and uncompresses and parses and compiles and runs) the code for ```<StockChart/>```.

有什么问题吗？好吧，我们想让应用尽可能快的显示组件 ```<StockTable/>```，但是，现在我们不得不等到浏览器把组件 ```<StockChart/>``` 下载（解压、解析、编译、运行）完才能显示它。

Let’s see a trace of how long it takes to display the ```<StockTable/>```:

我们可以追踪一下为了显示 ```<StockTable/>``` 花费的多长时间：

![](https://cdn-images-1.medium.com/max/1600/1*amihMyV6mCW5JQogRM6Buw.png)

It takes 2470 ms to display the StockTable (with a simulated Fast 3G network and a 4x slowdown CPU).

为了显示 StockTable 花费了 2470ms (模拟的 Fast 3G 的网络和减速 4x 的 CPU) 。

What’s in those (compressed) 125KB we are shipping to the browser?

125KB(压缩后的)文件都包含什么呢？

![](https://cdn-images-1.medium.com/max/1600/1*QOEav03nGAll-VT_73eE_A.png)

As expected, we have react, react-dom, and some react dependencies. But we also have moment, lodash and victory, which we only need for ```<StockChart/>```, not for ```<StockTable/>```.

正如期望的那样，里面包含 react、react-dom 和 一些 react 的依赖文件。但是，我们还包括 moment、loads 和 victory，这些只是在 ```<StockChart/>``` 用到，```<StockTable/>``` 不需要。

What could we do to avoid ```<StockChart/>``` dependencies to slow down the loading of ```<StockTable/>```? We lazy-load the component.

我们要如何避免加载 ```<StockTable/>``` 的时候不加载 ```<StockChart/>```？可以让组件懒加载。



#### Lazy-loading a component

####组件懒加载

Using a [dynamic import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Imports) we can split our bundled javascript in two, a main file with just the code we need for displaying ```<StockTable/>``` and another file with the code and the dependencies that ```<StockChart/>``` needs.

使用 [动态 import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Imports) 我们可以把 bundled 文件拆分成两个单独的文件，一个主要的文件仅仅包含 ```<StockTable/>``` 相关的文件，另外一个包含 ```<StockChart/>``` 相关的文件。

This technique is so useful that React 16.6 added an API for making it easier to use with React components: ```React.lazy()```.

在 React 16.6 中有一个非常有用的 API，可以让我们很容易的实现这个功能：```React.lazy()```。

In order to use ```React.lazy()``` in our ```App.js``` we make two changes:

为了使用 ```React.lazy()``` ，我们的 ```App.js``` 文件中需要两处更改：

![](https://cdn-images-1.medium.com/max/2400/1*DH2ChGy7yiqCfho-gGuP-Q.png)

First we replace the static import with a call to ```React.lazy()``` passing it a function that returns the dynamic import. Now the browser won’t download ```./StockChart.js``` (and its dependencies) until we render it for the first time.

首先，我们用 ```React.lazy()```  替换掉静态 import，然后把一个返回动态 import 的方法传递给 API。现在，直到我们第一次渲染它，浏览器都不会下载 ```./StockChart.js``` （和它的依赖）了

But what happens when React wants to render ```<StockChart/>``` and it doesn’t have the code yet? That’s why we added ```<React.Suspense/>```. It will render the ```fallback``` prop instead of its children until all the code of all its children is loaded.

但是，当 React 需要渲染 ```<StockChart/>``` 的时候而且代码还未加载会发生什么呢？这就是为什么我们要添加 ```<React.Suspense/>```。在所有子元素代码未加载完之前，它会用属性 ```fallback``` 来代替显示。

Now our app will be bundled in two files:

现在，我们会打包出两个文件：

​	![](https://cdn-images-1.medium.com/max/1600/1*s_U-JURSswCDhVDPZgxxNQ.png)



The main js file is 36KB. The other file is 89KB and has the code from ```./StockChart``` and all its dependencies.

```main.js``` 只有 36KB。另一个文件 89KB，它包含了 ```./StockChart``` 和它的依赖。

Let’s see again how much it takes the browser to show the ```<StockTable/>```with these changes:

做了这些改变之后，让我们再看一下加载 ```<StockTable/>``` 用了多少时间：

![](https://cdn-images-1.medium.com/max/1600/1*GkV09FYO5ADOAmw3xvKaYg.png)

The browser takes 760 ms to download the main js file (instead of 1250 ms) and 61 ms to evaluate the script (instead of 487 ms). ```<StockTable/>``` is displayed in 1546 ms (instead of 2470 ms).

浏览器加载 ```main.js``` 用了 760ms(之前是 1250ms)，然后用了 61ms 执行脚本(之前是 487ms)。在 1546ms 后显示了 ```<StockTable/>``` (之前是 2470ms)。

#### Preloading a lazy component

####组件预加载

We made our app load faster. But now we have another problem:

我们的应用现在加载很快。但是，有另外一个问题：

![](https://cdn-images-1.medium.com/max/1600/1*0Y-FcigqqboxXROFWWFO7A.gif)

The first time the user clicks on an item the “Loading…” fallback is shown. That’s because we need to wait until the browser loads the code for ```<StockChart/>```.

用户第一次点击的时候会显示 “Loading…”。这是因为我们需要等待浏览器下载 ```<StockChart/>``` 相关的代码。

If we want to get rid of the “Loading…” fallback, we will have to load the code before the user clicks the stock.

如果，我们不想显示  “Loading…” ，那么就需要在用户点击前加载代码。

One simple way of preloading the code is to start the dynamic import before calling ```React.lazy()```:

一种简单的方式是在执行 ```React.lazy()``` 之前先执行动态 import。

```jsx
const stockChartPromise = import("./StockChart");
const StockChart = React.lazy(() => stockChartPromise);
```

The component will start loading when we call the dynamic import, without blocking the rendering of ```<StockTable/>```.

组件将会在我们调用动态 import 时开始下载，并不会阻塞 ```<StockTable/>``` 的渲染。

Take a look at how the trace changed from the original eager-loading app:

现在，我们来跟第一个版本对比一下看有什么变化：

![](https://cdn-images-1.medium.com/max/2400/1*O-tDkDg9bmDsnvX4vzhHkQ.png?33)



Now, the user will only see the “Loading…” fallback if they click a stock in less than 1 second after the table is displayed. [Try it](https://deploy-preview-8--react-lazy.netlify.com/).

现在，只有用户在表格显示后的 1s 内点击才会看到  “Loading…”。[看下效果](https://deploy-preview-8--react-lazy.netlify.com/).

> You could also enhance the ```lazy``` function to make it easier to preload components whenever you need:
>
> 你也可以增强 ```lazy``` ，在你需要预加载组件时更容易实现：

```jsx
function lazyWithPreload(factory) {
  const Component = React.lazy(factory);
  Component.preload = factory;
  return Component;
}

const StockChart = lazyWithPreload(() => import("./StockChart"));

// somewhere in your component 
...
  handleYouMayNeedToRenderStockChartSoonEvent() {
    StockChart.preload();
  }
...
```

#### Prerendering a component

#### 组件预渲染

For our small demo app that’s all we need. For bigger apps the lazy component may have other lazy code or data to load before it can be rendered. So the user would still have to wait for those.

对于我们这个小的演示应用这些已经足够。对于大型应用来说懒加载组件被渲染之前还会有其他懒加载代码或者数据需要被加载。这样用户还是需要等待。

Another approach for preloading the component is to actually render it before we need it. We want to render it but we don’t want to show it, so we render it hidden:

另外一种做法是在组件显示之前我们可以先渲染它。我们会渲染它，但是并不会显示它，所以要隐藏它：

```jsx
class App extends React.Component {
  state = {
    selectedStock: null
  };
  render() {
    const { stocks } = this.props;
    const { selectedStock } = this.state;
    return (
      <React.Suspense fallback={<div>Loading...</div>}>
        <StockTable
          stocks={stocks}
          onSelect={selectedStock => this.setState({ selectedStock })}
        />
        {selectedStock && (
          <StockChart
            stock={selectedStock}
            onClose={() => this.setState({ selectedStock: false })}
          />
        )}
        {/* Preload <StockChart/> */}
        <React.Suspense fallback={null}>
          <div hidden={true}>
            <StockChart stock={stocks[0]} />
          </div>
        </React.Suspense>
      </React.Suspense>
    );
  }
}
```

React will start loading ```<StockChart/>``` the first time the app is rendered, but this time it will actually try to render ```<StockChart/>``` so if any other dependency (code or data) needs to be loaded it will be loaded.

在应用第一次渲染时 React 将会加载 ```<StockChart/>```，由于这次会真实的渲染 ```<StockChart/>```，因此它所需的依赖（代码或者数据）都会被加载。

We wrapped the lazy component inside a ```hidden``` ```div``` so it doesn’t show anything after it is loaded. And we wrapped that ```div``` inside another ```<React.Suspense/>``` with a ```null``` fallback so it doesn’t show anything while it’s being loaded.

我们会把懒加载的组件放在一个属性为 ```hidden``` ```div``` 标签中，即便它被加载了也不会显示任何东西。然后用另外一个 fallback 为 ```null``` 的 ```<React.Suspense/>``` 把 ```div``` 包裹起来，因此，当它开始加载时并不会显示任何东西。

> *Note:* ```hidden``` *is the* [HTML attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/hidden) *for indicating that the element is not yet relevant. The browser won’t render elements with this attribute. React doesn’t do anything special with that attribute(but it may start giving hidden elements a lower priority in future releases).*
>
> 注意：```hidden``` 是[HTML 属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/hidden) 用来指示当前元素并不相关。浏览器将不会渲染这个元素。React 并不会对这个属性做特殊处理(但是，在未来的版本中会给有这个属性的元素一个较低的优先级)。



#### What’s missing?

#### 有什么遗漏？

This last approach is useful in many cases but it has some problems.

在很多情况下这已经很有用了，但是还是有点问题。

First, the```hidden``` attribute for hiding the rendered lazy component isn’t bulletproof. For example, the lazy component could use a [portal](https://reactjs.org/docs/portals.html) which won’t be hidden ([there is a hack ](https://github.com/pomber/react-lazy-preload-demo/pull/5/commits/dd968db4a2d41b1a607bc4aabfbf6c726ed9a94a)that doesn’t require an extra div and also work with portals, but it’s a hack, it will break).

第一，用属性 ```hidden``` 来隐藏已经被渲染的懒加载组件并不是万能的。例如，如果懒加载的组件用到了 [portal](https://reactjs.org/docs/portals.html)，它并不会被隐藏（有一种 [hack](https://github.com/pomber/react-lazy-preload-demo/pull/5/commits/dd968db4a2d41b1a607bc4aabfbf6c726ed9a94a) 方法在不需要额外 div 标签的情况也可以正常工作，但是，毕竟这是 hack 手段，它是破坏性的）。

Second, even if the component is hidden we are still adding unused nodes to the DOM, and that could become a performance problem.

第二，即便组件被隐藏了，但是我们还是添加了未使用的 DOM 节点，这将会带来性能问题。

A better aproach would be to tell react to render the lazy component but without comitting it to the DOM after it’s loaded. But, as far as I know, it isn’t possible with the current version of React.

比较好的做法是让 React 渲染懒加载的组件，但是并不会真正的添加 DOM。但是，目前据我所知，React 当前版本并不能做到。

Another improvement we could do is to reuse the elements we are rendering when preloading the chart component, so when we want to actually display the chart React doesn’t need to create them again. If we know what stock the user will click we could even render it with the correct data before the user click it ([like this](https://github.com/pomber/react-lazy-preload-demo/pull/7/commits/0e421ba3a3fc42f99ce090e250ef68c59bc866a6)).

另外一个改进是当预载 chart 组件时我们可以复用组件，因此当需要真正显示一个 chart 时就不需要再次创建了。如果，我们知道用户将要点击那条记录，我们甚至可以提前渲染它 ([就像这样](https://github.com/pomber/react-lazy-preload-demo/pull/7/commits/0e421ba3a3fc42f99ce090e250ef68c59bc866a6))。

That’s all. Thanks for reading.

就这些。谢谢您的阅读。

For more stuff like this **follow** [**@pomber** ](https://twitter.com/pomber)**on twitter**.

更多资料可以在 Twitter 上关注 [**@pomber** ](https://twitter.com/pomber)