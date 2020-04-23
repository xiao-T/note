# Hooks, State, Closures, and useReducer

## Hooks, State, 闭包和 useReducer

> 原文地址: <https://adamrackis.dev/state-and-use-reducer/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

For those of us coming from a Redux background, `useReducer` can seem deceptively complex and unnecessary. Between `useState` and context, it’s easy to fall into the trap of thinking that a reducer adds unnecessary complexity for the majority of simpler use cases; however, it turns out `useReducer` can greatly simplify state management. Let’s look at an example.

对于我们这些有 Redux 使用经验的人来说，`useReducer` 看起来更加复杂和没必要。在 `useState` 和 `context` 之间，很容易掉进思维陷阱中，这是，因为对于大多简单的使用场景 reducer 增加了不必要的复杂度；然而，事实证明 `useReducer` 可以让 state 管理更加简单。我们来看一个示例。

As with my other posts, this code is from my [booklist project](https://github.com/arackaf/booklist). The use case is that a screen allows users to scan in books. The ISBNs are recorded, and then sent to a rate-limited service that looks up the book info. Since the lookup service is rate limited, there’s no way to guarantee your books will get looked up anytime soon, so a web socket is set up; as updates come in, messages are sent down the ws, and handled in the ui. The ws’s api is dirt simple: the data packet has a `_messageType` property on it, with the rest of the object serving as the payload. Obviously a more serious project would design something sturdier.

和我其他文章一样，代码来自我的[书单项目](https://github.com/arackaf/booklist)。在这个项目中允许用户通过屏幕扫描书籍。ISBN 会被记录，然后，通过一个限速的服务查询更多的信息。由于，查询服务被限速，这并不能保证书籍可以很快的得到查询，因此，需要一个 webSocket 服务；一旦数据有更新，就通过 ws 传递，然后，处理 UI。ws API 非常简单：数据包将会有一个 `_messageType` 属性，和其余的信息。显然，一个严肃的项目会设计的更加坚固。

With component classes, the code to set up the ws was straightforward: in `componentDidMount` the ws subscription was created, and in `componentWillUnmount` it was torn down. With this in mind, it’s easy to fall into the trap of attempting the following with hooks

在类组件中，启动 ws 的代码非常直接：在 `componentDidMount` ws 将会被创建，然后，在 `componentWillUnmount` 将会被关闭。因此，在使用 hooks 很容易掉进陷阱中。

```jsx
const BookEntryList = props => {
  const [pending, setPending] = useState(0);
  const [booksJustSaved, setBooksJustSaved] = useState([]);

  useEffect(() => {
    const ws = new WebSocket(webSocketAddress("/bookEntryWS"));

    ws.onmessage = ({ data }) => {
      let packet = JSON.parse(data);
      if (packet._messageType == "initial") {
        setPending(packet.pending);
      } else if (packet._messageType == "bookAdded") {
        setPending(pending - 1 || 0);
        setBooksJustSaved([packet, ...booksJustSaved]);
      } else if (packet._messageType == "pendingBookAdded") {
        setPending(+pending + 1 || 0);
      } else if (packet._messageType == "bookLookupFailed") {
        setPending(pending - 1 || 0);
        setBooksJustSaved([
          {
            _id: "" + new Date(),
            title: `Failed lookup for ${packet.isbn}`,
            success: false
          },
          ...booksJustSaved
        ]);
      }
    };
    return () => {
      try {
        ws.close();
      } catch (e) {}
    };
  }, []);

  //...
};
```

We put the ws creation in a `useEffect` call with an empty dependency list, which means it’ll never re-fire, and we return a function to do the teardown. When the component first mounts, our ws is set up, and when the component unmounts, it’s torn down, just like we would with a class component.

我们在 `useEffect` 中启动了 ws，设置一个空数组作为更新依赖，也就是说，这永远不会重新触发，然后，我们返回了一个函数用来关闭 ws。当组件第一挂载时，我的 ws 将会被启动，组件卸载时，ws 会被关闭，这就像一个类组件一样。

## The problem

## 问题分析

This code fails horribly. We’re accessing state inside the `useEffect` closure, but not including that state in the dependency list. For example, inside of `useEffect` the value of `pending` will absolutely always be zero. Sure, we might call `setPending` inside the `ws.onmessage` handler, which *will* cause that state to update, and the component to re-render, but when it re-renders our `useEffect` will **not** re-fire (again, because of the empty dependency list)—as a result that closure will go on closing over the now-stale value for `pending`.

代码有严重的问题。我们在 `useEffect` 闭包中访问了 state，但是，在依赖列表中并没有 state。例如，`useEffect` 中的 `pending` 永远都会是 0。当然，我们在 `ws.onmessage` 中可以调用 `setPending`，这*将会*引起 state 的更新，然后，组件也会重新渲染，但是，在重新渲染时，我的 `useEffect` **并没有**重新触发（这是因为它的依赖列表是空），这就会导致闭包中的 `pending` 值并没有发生变化。

To be clear, using the Hooks linting rule, discussed below, would have caught this easily. More fundamentally, it’s essential to break with old habits from the class component days. Do *not* approach these dependency lists from a `componentDidMount` / `componentDidUpdate` / `componentWillUnmount` frame of mind. Just because the class component version of this would have set up the web socket once, in `componentDidMount`, does *not* mean you can do a direct translation into a `useEffect` call with an empty dependency list.

需要明确的是，通过 hooks 规则，我们可以很容易发现这一点。从根本上说，打破使用类组件的习惯这一点很重要。*不要*从  `componentDidMount` / `componentDidUpdate` / `componentWillUnmount` 中获取依赖列表。使用类组件，我们在 `componentDidMount` 只启动了一次 webSocket，*并不*代表我们可以直接转化成使用空依赖列表的 `useEffect`。

Don’t overthink, and don’t be clever: any value from your render function’s scope that’s used in the effect callback needs to be added to your dependency list: this includes props, state, etc. That said—

不要想太多，也不要自作聪明：任何 `useEffect` 中用到的值都需要添加到依赖列表中，这包括：props、state 等等。

## The solution

## 解决方案

While we *could* add every piece of needed state to our `useEffect` dependency list, this would cause the web socket to be torn down, and re-created on every update. This would hardly be efficient, and might actually cause problems if the ws sends down a packet of initial state on creation, that might already have been accounted for, and updated in our ui.

当然，我们*可以*把 state 添加到 `useEffect` 的依赖列表中，每次更新，将会导致 webSocket 的重启。这种方式效率并不高，如果，在 ws 中我们发送了一个初始化的值，就会引起问题，这是因为，有些 state 已经被处理并更新到了 UI。

If we look closer, however, we might notice something interesting. Every operation we’re performing is always in terms of prior state. We’re always saying something like “increment the number of pending books,” “add this book to the list of completed,” etc. This is precisely where a reducer shines; in fact, **sending commands that project prior state to a new state is the whole purpose of a reducer**.

如果，我们仔细看，或许我们会看到有趣的事情。我们的每次操作都是基于先前的 state。我们也是总会说：“增加图书的数量”、“这本书已经添加到列表”等等。其实，这就是 reducer；事实上，**reducer 的目的就是基于先前的 state 通过命令生成新的 state**。

Moving this entire state management to a reducer would eliminate any references to local state within the `useEffect` callback; let’s see how.

把整个 state 的管理移动到 reducer 中，这将会消除 `useEffect` 内部对本地 state 的引用；我们来看一下如何实现。

```jsx
function scanReducer(state, [type, payload]) {
  switch (type) {
    case "initial":
      return { ...state, pending: payload.pending };
    case "pendingBookAdded":
      return { ...state, pending: state.pending + 1 };
    case "bookAdded":
      return {
        ...state,
        pending: state.pending - 1,
        booksSaved: [payload, ...state.booksSaved]
      };
    case "bookLookupFailed":
      return {
        ...state,
        pending: state.pending - 1,
        booksSaved: [
          {
            _id: "" + new Date(),
            title: `Failed lookup for ${payload.isbn}`,
            success: false
          },
          ...state.booksSaved
        ]
      };
  }
  return state;
}
const initialState = { pending: 0, booksSaved: [] };

const BookEntryList = props => {
  const [state, dispatch] = useReducer(scanReducer, initialState);

  useEffect(() => {
    const ws = new WebSocket(webSocketAddress("/bookEntryWS"));

    ws.onmessage = ({ data }) => {
      let packet = JSON.parse(data);
      dispatch([packet._messageType, packet]);
    };
    return () => {
      try {
        ws.close();
      } catch (e) {}
    };
  }, []);

  //...
};
```

While slightly more lines, we no longer have multiple update functions, our `useEffect` body is much more simple and readable, and we no longer have to worry about stale state being trapped in a closure: all of our updates happen via dispatches against our single reducer. This also aids in testability, since our reducer is incredibly easy to test; it’s just a vanilla JavaScript function. As Sunil Pai from the React team puts it, using a reducer helps separate reads, from writes. Our `useEffect` body now only worries about dispatching actions, which produce new state; before it was concerned with both reading existing state, and also writing new state.

当然，代码有点多，但是，我们没有多个更新函数了，我的 `useEffect` 更加简单和可读，并且，我们无需在担心在闭包中用了旧的 state；所有的更新都是通过 dispatch reducer。这也有利于测试，我们的 reducer 极其容易测试；它只是一个原生 JavaScript 函数。就像 Sunil Pai 在 React team 中所说，使用 reducer 有助于区分读和写。现在，我们的 `useEffect` 只需关注 dispatch 的 action 即可，产出新的 state；在此之前，对于 state 我们需要同时关注读和写。

You may have noticed actions being sent to the reducer as an array, with the type in the zero slot, rather than as an object with a `type` key. Either are allowed with useReducer; this is just a trick Dan Abramov showed me to reduce the boilerplate a bit :)

你或许已经注意到，相比使用对象，我们是通过数组传递 action 的，type 在第一个位置，而不是使用对象属性 `type`。两种方式都可以是；这只是 Dan Abramov 向我展示一个技巧，用来减少代码。

## Linting against errors like this

## 防止错误

As I mentioned above, the wonderful folks on the React team have created a lint rule to help catch, and draw attention to the sorts of errors from the original code above. It’s [located here](https://github.com/facebook/react/issues/14920), and works wonderfully—it very clearly caught the error above.

就如我上面提到的，React 优秀的团队已经创建了 lint 规则帮助我们捕获错误，并且在代码中会有简短的提示。它在[这](https://github.com/facebook/react/issues/14920)，工作的很好 - 可以很好的捕获代码中的错误。

## What about functional setState()

## 探究 setState()

Lastly, some of you may be wondering why, in the original code, I didn’t just do this

最后，你们或许会奇怪，为什么在之前我不会这么做呢

```js
setPending(pending => pending - 1 || 0);
```

rather than

而是

```js
setPending(pending - 1 || 0);
```

This would have removed the closure problem, and worked fine for this particular use case; however, the minute updates to `booksJustSaved` needed access to the value of `pending`, or vice versa, this solution would have broken down, leaving us right where we started. Moreover, I find the reducer version to be a bit cleaner, with the state management nicely separated in its own reducer function.

这将会解决闭包的问题，并且在当前演示中工作的良好；然而，更新 `booksJustSaved` 时需要访问 `pending`，反之亦然，这种方案已经失效了，我们需要从头开始。而且，我发现 reducer 的方案更加清晰，state 的管理都在 reducer 函数中。

All in all, I think `useReducer()` is incredibly under-utilized at present. It’s nowhere near as scary as you might think. Give it a try!

总而言之，我认为目前 `useReducer()` 使用率极低。它远没有你想象的那么可怕。试试看！

Happy coding!

快乐编码！