## link rel="prefetch/preload" in webpack
## 用 webpack 实现 prefetch/preload 

> 原文地址: <https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c/>   
**本文版权归原作者所有，翻译仅用于学习。**

---

webpack 4.6.0 adds support for prefetching (and preloading).

webpack 4.6.0 开始支持 prefetch(和 preload)。

TL;DR: Use `import(/* webpackPrefetch: true */ "...")` for prefetching.

TL;DR: 用 `import(/* webpackPrefetch: true */ "...")` 可以开启 prefetch。

## What‘s link rel="prefetch"?

## link rel="prefetch" 是什么？

This “Resource Hint” tells the browser that this is a resource that is **probably** needed for some **navigation in the future**.

Prefetch 告诉浏览器当前资源在**将来很有可能**会需要。

Browsers usually fetch this resource when they are in idle state. After fetched the resource sits ready in the HTTP cache to fulfill future requests. Multiple prefetch hints queue up and are fetched while idling. When leaving idle state while prefetching to browser may cancel any ongoing fetch (and put the partial response into cache, to be continued with Content-Range headers) and stop processing the prefetch queue.

通常只有浏览器空闲时才会加载当前资源。资源加载完后会被缓存起来，供后续的请求用。多个 prefetch 会按照一定顺序加载。当浏览器不在空闲会取消那些还在加载的资源（把已响应的资源放进缓存，后续根据 header 中的 Content-Range 继续加载），并且不再处理 prefetch 队列。

To sum it up: Fetch while idle.

总结一下：当浏览器空闲时才会加载。

## What’s link rel="preload"?

## link rel="preload" 是什么？

This “Resource Hint” tells the browser that this is a resource that is **definitely** needed for **this navigation**, but will be discovered later. Chrome even prints a warning when the resource isn’t used 3 seconds after load.

Preload 告诉浏览器当前资源对于**当前路径肯定**会用到，只是稍后用到。如果，资源加载完后超过 3s 没有使用，Chrome 甚至会打印一个警告。

Browsers usually fetch this resource with medium priority (not layout-blocking).

通常浏览器会用一个适中的权限加载这些资源（并不会阻塞布局）。

To sum it up: Fetch like normal, just earlier discovered.

总结一下：正常加载，只是更早一些。

## Why is this useful?

## 为什么有用？

Preload is used to discover resources earlier and avoid a waterfall-like fetching. It’s can bring down the page load to 2 round-trips (1. HTML, 2. all other resources). Using it doesn’t cost additional bandwidth.

Preload 可以让资源更早的加载，避免出现加载的瀑布流。它可以把页面的加载降低到 2 步（1. HTML, 2. 所有的资源）。这样并不会增加额外的带宽。

Prefetch is used to use the idle time of the browser to speed up future navigations. Using it may cost additional bandwidth when the user doesn’t do the expected future navigation.

Prefetch 可以在浏览器空闲时加载资源，提高页面转场的速度。如果，这些不是用户所期望的，就会增加额外带宽流量。

## Code Splitting

## 拆分代码

We assume you are building your huge application with webpack and use On-Demand-Loading via `import()` to load only the parts of your application the user currently needs.

假设，你正在用 webpack 构建大型应用，并且是用 ```import()``` 实现按需加载，只加载用户当前用到的部分代码。

As example we take a HomePage which contains a LoginButton which opens a LoginModal. Once logged in the application brings the user to the DashboardPage. The pages may contain other buttons but these are less common.

我们用 HomePage 做为示例，它包含一个 LoginButton，点击会打开一个 LoginModal。一旦用户登录应用就会跳转到 DashboardPage。页面也可以包含其它按钮，但是比较少见。

To get the best performance you used `import("LoginModal")` at the LoginButton to keep the HomePage minimal. Similar the LoginModal contains `import("DashboardPage")`.

为了获得最优的性能你可以在 LoginButton 中用 `import("LoginModal")` 引入 LoginModal，并尽可能保证 HomePage 最小化。同样，在 LoginModal 中用 `import("DashboardPage")` 引入 DashboardPage。

Now these example application is splitted into at least 3 chunks: home-chunk, login-chunk, dashboard-chunk. On initial load only the home-chunk need to be loaded, which creates a great UX. But when the user clicks on the LoginButton there is a delay until the LoginModal opens, because this part of the application need to be loaded first. Similar with the DashboardPage.

现在，这个应用至少拆分了 3 块：home-chunk，login-chunk，dashboard-chunk。一开始，只有 home-chunk 需要加载。但是，当用户点击 LoginButton时，因为需要加载相应的代码，打开 LoginModal 会有一些延迟。DashboardPage 同理。

## Using prefetching in webpack

## Webpack 中使用 prefetch

The new prefetch feature allows to improve this workflow. And it’s super easy.

新 prefetch 功能可以改善工作流程。它非常简单。

At the LoginButton change 
`import("LoginModal")` to 
`import(/* webpackPrefetch: true */ "LoginModal")`.

把 LoginButton 中的 `import("LoginModal")` 改成 
`import(/* webpackPrefetch: true */ "LoginModal")`。

Similar to this change 
`import("DashboardPage")` to 
`import(/* webpackPrefetch: true */ "DashboardPage")` in the LoginModal.

同样，把 LoginModal 中的 `import("DashboardPage")` 改成 
`import(/* webpackPrefetch: true */ "DashboardPage")` 。

This will instruct webpack to prefetch (in browser idle time) this On-Demand-Loaded chunk when the parent chunk finish loading. In this example: When home-chunk finish loading, add login-chunk to the prefetch queue. When login-chunk finish loading (real load, not prefetch), add dashboard-chunk to the prefetch queue.

这样，当父级块加载完后，webpack 会按需加载（在浏览器的空闲时间）其它的代码块。具体到这个示例中，当 home-chunk 加载完后，会把 login-chunk 添加到 prefetch 队列。当 login-chunk 加载完（真正的加载，不是 prefetch），会把 dashboard-chunk 添加到 prefetch 队列中。

Assuming the home-chunk is a entrypoint this will generate `<link rel="prefetch" href="login-chunk.js">` in the HTML page.

假设 home-chunk 是个入口，在 HTML 中会生成 `<link rel="prefetch" href="login-chunk.js">`。

The login-chunk is a on-demand-chunk so the webpack runtime will take care of injecting a `<link rel="prefetch" href="dashboard-chunk.js">` once the login-chunk load is completed.

Login-chunk 是一个按需加载块，经过 webpack 的处理，一旦 login-chunk 加载完就会在页面上注入 `<link rel="prefetch" href="dashboard-chunk.js">`。

This will improve the UX in this way:

这将会在以下方面改善交互：

The user visits the HomePage. UX and performance is as great as before. Once the HomePage finished loading the browser enters idle state and starts fetching the login-chunk in background. The user doesn’t notice this. Assuming the user need some time to find the LoginButton this request will finish before the user clicks the button.

用户访问 HomePage 时，UX 和性能像以前一样好。一旦，HomePage 加载完，浏览器会进入空闲状态，并且开始在后台加载 login-chunk。用户并不会注意到这些。假设，用户并不会立即看到 LoginButton，在用户点击按钮前，请求已经完成了。

When the user now clicks the button, login-chunk already sits in the HTTP cache and the request hits the cache and only take a minimal amount of time. The user will see the LoginModal instantly.
At the same time the webpack runtime adds dashboard-chunk to the prefetch queue, so it won’t take additional time to load the chunk after login.

现在，当用户点击按钮，HTTP 缓存中已经了 login-chunk，加载缓存只需很少的时间。用户会立即看到 LoginModal。同时，Webpack 会把 dashboard-chunk 添加到 prefetch 队列，因此登录后并不需要额外的时间去加载相应的 chunk。

Note that the user might not always have this instant LoginModal experience. There are a bunch of factors which can re-add the chunk loading delay to the LoginButton: slow networks, fast clicking users, disabled prefetch on bandwidth-limited devices, no prefetch browser support, very slow execution of the chunk, …

注意：用户可能并不会立即需要 LoginModal。有很多因素还是会导致 LoginButton 的加载延迟：网络慢、用户快速点击、带宽限制的设备禁用 prefetch、浏览器不支持 prefetch、代码执行的慢等等。

## Using preloading in webpack

## Webpack 中使用 preload

Similar to `import(/* webpackPrefetch: true */ "...")` it’s possible to use
`import(/* webpackPreload: true */ "...")`. This has a bunch of differences compared to prefetch:

- A preloaded chunk starts loading in parallel to the parent chunk. A prefetched chunk starts after the parent chunk finish.

- A preloaded chunk has medium priority and instantly downloaded. A prefetched chunk is downloaded in browser idle time.

- A preloaded chunk should be instantly requested by the parent chunk. A prefetched chunk can be used anytime in the future.

- Browser support is different.

与 `import(/* webpackPrefetch: true */ "...")`类似，也可以使用 `import(/* webpackPreload: true */ "...")` 。与 prefetch 相比，它有很多不同之处：

- Preload 与父级代码并行加载。Prefecth 是在父级代码加载完后加载。
- Preload 有较高的权限会立即下载。Prefetch 只有在浏览器空闲时才加载。
- 父级应该立即加载 preload 代码块。Prefetch 可以在之后的时间加载。
- 浏览器对两者的支持情况不同。

Due to this properties use cases are rare. It can be used if a module always `import()` something instantly. It could make sense if a Component depends on a big library that should be in a separate chunk. Example: A ChartComponent uses a big ChartingLibrary. It displays a LoadingIndicator when used and instantly
  `import(/* webpackPreload: true */ "ChartingLibrary")`. When a page which uses the ChartComponent is requested, the charting-library-chunk is also requested via `<link rel="preload">`. Assuming the page-chunk is smaller and finishs faster, the page will be displayed with a LoadingIndicator, until the already requested charting-library-chunk finishs. This will give a little load time boost since it only needs one round-trip instead of two. Especially in high-latency enviroments.

因此，很少会用到 preload。如果一个模块总是会立即引用某些东西，就可以使用 preload。如果，一个组件依赖一个大型库，那么就应该分开单独一个块，这样就比较有意义。例如：一个 ChartComponent 依赖一个大型 ChartingLibrary。当使用 `import(/* webpackPreload: true */ "ChartingLibrary")` 时会立即显示一个 LoadingIndicator。当页面用到了 ChartComponent，由于 `<link rel="preload">`  charting-library-chunk 也会被加载。假设页面很小加载很快，这时页面直到 charting-library-chunk 加载完会显示一个 LoadingIndicator。由于只需一次 round-trip，因此所需时间更少。尤其在高延迟的环境中。

 Using `webpackPreload` incorrectly can actually hurt performance, so be careful when using it.

`webpackPreload` 如果使用不当会损害性能，因此需要小心使用。

When thinking about preload vs. prefetch, you probably want prefetch.

当考虑是选择 preload 还是选择 prefetch，我想你应该选择 prefetch。

Note: This statement is about webpack `import()`. In HTML pages you probably want preload.

注意：webpack 中的 `import()` 相当于 HTML 中的 preload。

  ## Multiple prefetched chunks

  ## 多个 prefetch chunk

You can add the `webpackPrefetch` flag to as many `import()` as you like to, but note that all prefetched chunks fight for the bandwidth. They are actually queued up and the really used chunk might not be prefetch when the user requests it.

添加多少 `webpackPrefetch` 标记如你所愿，但是注意，所有的 prefetch 都需要考虑带宽。所有的 prefetch 都在一个队列中，队列中的 chunk 或许用户并不需要。

This isn’t a big issue when all chunks are equally likely to be requested. But when some chunks are more likely than others you probably want to control the order of prefetching.

所有的块按照相等的优先级加载并不是什么大问题。但是，如果某个块需要优先加载，你就需要控制 prefetch 的队列。

Lucky you, we got you covered. (Note this is an advanced use case.)

很幸运，我会告诉你如何处理。（注意：这是高级使用案例。）

Instead of using `webpackPrefetch: true` you can pass a number as value. webpack will prefetch chunks in the order you specified. Example: `webpackPrefetch: 42` will be prefetched before `webpackPrefetch: 1` which weill be prefetched before `webpackPrefetch: true` which will be prefetched before `webpackPrefetch: -99999` (like z-order). Actually `true` counts a `0`.

你可以用一个数字替换 `webpackPrefetch: true` 的值。Webpack 将会按照你指定的顺序 prefetch chunk。例如：`webpackPrefetch: 42` 将会在 `webpackPrefetch: 1` 前 prefetch，同样，也会比 `webpackPrefetch: true` 和 `webpackPrefetch: -99999` （像 z-order）更早 prefetch。事实上 ```true``` 等于 ```0```。

Similar for `webpackPreload` but I don’t think you will need that!

同样 `webpackPreload` 也可以这么处理，但是，我认为你不需要这样！

## FAQ

**What if multiple** `import()`**s request the same chunk and some of the are prefetched/preloaded?**Preload wins over prefetch, which wins over nothing. In the same category the highest order wins.

**如果同一个 chunk 多次被 prefetched/preloaded，会如何处理呢？**

Preload 的优先级会高于 prefetch，同样比正常导入的更高。相同类型中，优先级高的更早加载。

**What if prefetch/preload is not supported in the browser?**
It’s ignored by the browser. webpack doesn’t try to do any fallback. It’s a hint anyway, so even browser which support it may ignore it if they feel so.

**浏览器不支持 prefetch/preload 怎么处理呢？**

它们会被浏览器忽略。Webpack 没有任何的回调处理。还有一种情况，即便浏览器支持，也有可能会被忽略。

**Prefetch/preload doesn’t work in the entrypoint for me. What’s the problem?**

The webpack runtime only takes care of prefetch/preload for on-demand-loaded chunks. When prefetch/preload is used in `import()`s in a entry chunk the html generation is resposible for adding the `<link>` tags to the HTML. The stats json data provides information in `entrypoints[].childAssets`.

**Entry-point prefetch/preload 无效，为什么呢？**

Webpack 只会 prefetch/preload 那些按需加载的 chunk。当 prefetch/preload 来 `import()` 一个 entry chunk 时，HTML 中会添加一个相关的 `<link>` 标签。所有的统计信息可以在 `entrypoints[].childAssets` 中查看。

**Why not using prefetch/preload on every  `import()`? **
You waste a lot a bandwidth. It’s also more beneficial to use it selectively for `import()`s that are very likely to be visited. Don’t waste bandwidth. Some people have only limited volume or bandwidth. **Use it selectively to get the most benefits!**

**为什么不在所有的地方都使用 prefetch/preload ?**

这样会浪费大量的带宽。选择性的使用 `import()` 对访问者来说更有意义。不要试图浪费带宽。一些人的带宽是有限的。**选择性的使用更有意义！**

**I don’t need/want this feature. What’s the cost for not using it?**
The runtime code is only added when this feature is used in one of your on-demand-loaded chunk. So when not using it you don’t have to pay any overhead.

**我不想使用这个功能。这对我来说要付出什么代价呢？**

当它们用来处理按需加载的 chunk 时才会添加相关的执行代码。因此，如果你不使用它们对你来说不需要付出任何代价。

**I already have a magic comment at `import()`. Can I add multiple magic comments?**
Yes, either separated with `,` or in separate comments:

**在使用 `import()` 时，我已经添加一条 magic comment。我可以添加多条吗？**

当然可以，你可以使用 `,` 来分隔它们，或者都写在一条注释中：

```js
import(
  /* webpackChunkName: "test", webpackPrefetch: true */
  "LoginModal"
)
// or
import(
  /* webpackChunkName: "test" */
  /* webpackPrefetch: true */
  "LoginModal"
)
// spacing optional
```

**I build my library with rollup and also use `import()`. Can users of my library which use webpack benefit from prefetch?**
Yes, you can add webpack magic comments to `import()` and rollup will keep the comment. When the result it built with webpack it’s will use the comment. When not built with webpack the comment is removed by minimizers.

**我用 rollup 构建了自己的库，也用到了 `import()`。使用我的库的用户在使用 webpack 时可以受益于 prefetch 吗？**

可以，你可以在 `import()` 添加 webpack magic comments，rollup 会保留这些注释。当使用 webpack 构建时，这些注释就生效。当不使用 webpack 构建时，代码压缩时会把这些注释移除。

**I’m unsure if adding a prefetch for a specific `import()` improves performance. Should I add it?**
Best measure it. A/B testing. There is no general advise here. It depends on the probability of users visiting this path of the application.

**我不确定在 `import()` 指定 prefetch 是否可以提升性能。我应该怎么做呢？**

最好做个 A/B 测试。这个没什么好的建议。这依赖用户在访问应用时的路径。

**I already have a service worker in place which caches to whole application on load. Does using prefetch makes sense for me?**
It depends on your application. In general this kind of Service Worker loads all assets of the application in no partical order, while prefetch allows to specify an order and depend on the actually position of the user in the application. Prefetch is also more bandwidth-efficient and only downloads in browser idle time.
Measure how long it takes to download the whole application in low bandwidth enviroments, compared to when the first user navigation happens. In huge applications it could make sense to use prefetch instead.

**我用 service work 来缓存整个应用。Prefetch 对我来说还有意义吗？**

这取决于你的应用。通常 Service Work 下载应用资源时是无序的，然而，Prefetch 允许你可以按照用户的访问情况来指定一个加载顺序。Prefetch 也带来了更高的宽带效率，只有浏览器空闲时才会下载资源。

对比一下，当用户第一次访问应用时，低带宽的情况，下载整个应用需要多少时间。在大型应用中 prefetch 或许更有意义。

**I want to split my initial page load into critical resources and non-critical resource and load them in this order. Does preload help here?**
Yes, you can put critical resources into the entrypoint and non-critical resources behind a `import()` with `webpackPreload: true`. Note don’t forget to add `<link rel="preload">` for entry chunk assets too (before the tags for children).

**我想把初始化页面分为关键资源和非关键资源，并且按照顺序加载它们。Preload 对此有帮助吗？**

有的，你可以把关键资源作为 Entry-Point，非关键资源在 `import()` 时设置 `webpackPreload: true`。注意别忘记给 Entry-Point chunk 添加 `<link rel="preload">` （在子资源的标签前）。

---

webpack is not backed by a big company, unlike many other big Open Source products. The development is [funded by donations](https://opencollective.com/webpack). *Please consider donating if you depend on webpack… (Ask your boss!)*

不像其它大型开源项目，webpack 并没有大公司的支持。项目的发展都是通过[捐赠提供资金](https://opencollective.com/webpack)。*如果，你有使用 webpack 请考虑给我们捐赠…(问问你的老板！)*

Special thanks to these sponsors: (Top 5)

- [Trivago](https://www.trivago.com/?utm_source=webpack&utm_medium=medium&utm_campaign=sponsorship) (Hotel metasearch) donated a total of $100,000
- [ag-Grid](https://www.ag-grid.com/?utm_source=webpack&utm_medium=medium&utm_campaign=sponsorship) (DataGrid) donated a total of $35,000
- [Segment](https://segment.com/?utm_source=webpack&utm_medium=medium&utm_campaign=sponsorship) (Data Infrastructure) donated a total of $24,000
- [Adobe](https://www.adobe.com/?utm_source=webpack&utm_medium=medium&utm_campaign=sponsorship) (Software) donated a total of $12,000
- [Capital One](https://www.capitalone.com/?utm_source=webpack&utm_medium=medium&utm_campaign=sponsorship) (Bank) donated a total of $12,000
- [Full list](https://webpack.js.org/)