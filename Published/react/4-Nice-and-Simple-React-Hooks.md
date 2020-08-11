## 4 Nice and Simple React Hooks
## 4 个简单又优雅的 React Hooks

> 原文地址: <https://medium.com/better-programming/4-nice-and-simple-react-hooks-1272d41f0df/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/0*N0vjjy7nRU8DvFCF)

React has become one of the most popular libraries to build applications in the browser. While it was invented by Facebook, it’s open-source and free for anyone to use. Millions of developers are already using React to build amazing enterprise applications. The new Facebook website is made entirely of React and Relay, and we’ve seen other big companies like Microsoft use React for applications like Azure DevOps.

React 已成为在浏览器中构建应用程序的最流行的库之一。它是由 Facebook 发布开源并免费给开发者使用的。数以百万计的开发者已经用 React 构建了令人惊叹的企业级应用。新版的 Facebook 站点就是使用 React 和 Relay 构建的，同时，我们也看到了一些大型企业也在使用，比如：微软使用 React 构建了 Azure DevOps 应用。

React introduced hooks with version 16.8 in February 2019. They let you use state and other React features without writing a class. Hooks allow you to encapsulate a piece of stateful logic into a reusable “hook” that you can then use in your components.

在 2019 年 二月，React v16.8 中引入了 Hook 的功能。它们可以在没有使用类组件的情况下使用 state 和 React 的其它功能。Hook 可以让你抽象一些 state 逻辑，然后，在组件中复用这个 “hook”。

> “Hooks allow you to reuse stateful logic without changing your component hierarchy.” — [React’s documentation](https://reactjs.org/docs/hooks-intro.html)
>
> “Hook 让你在无需修改组件结构的情况下复用状态逻辑”— [React’s 文档](https://zh-hans.reactjs.org/docs/hooks-intro.html)

I’ve gathered a few really useful hooks that I use in my everyday.

在平时的工作中，我收集了一些非常有用的自定义的 hooks。

# useDebounce

With this neat hook, you will be able to debounce a value by a delay — just like you can delay the execution of a function with a normal debounce. The idea is very similar: We define some local state in the hook that we only update whenever the debounced function gets to run.

利用这个 hook，你可以延迟一个操作 — 就像正常的延迟执行一个函数一样。这就像：我们在 hook 中定义了一些本地 state，但是，只有延迟的函数运行时才会更新它们。

A good fit for this hook is when you have fast-changing values that you want to react to, but your reaction might be asynchronous or slow, so you only want to react to the latest value that you are able to consume. Here is an example:

一个比较适合使用的场景是，假设，有一个变化非常快的值，你需要做出响应，但是，你的响应是异步或者会比较慢，因此，你只需要对最后一刻的值做出响应即可。这里有个演示：

## Implementation

## 实现方式

```tsx
import { useState, useEffect } from 'react';

const useDebounce = <T>(value: T, delay: number): T => {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
  
    return () => {
      clearTimeout(handler);
    }
  }, [value, delay]);
  
  return debouncedValue;
}
```

## Usage

## 用例

```tsx
import React, { useState, useEffect } from 'react';
import useDebounce from './useDebounce';

const Search = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [results, setResults] = useState([]);
  
  // ✅ Use debounce hook to debounced searchTerm as it is rapidly changing
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  
  // Effect only runs when debouncedSearchTerm changes,
  // and it only does when the user stops typing for more than 500ms
  useEffect(() => {
    if (debouncedSearchTerm) {
      const fetchResults = async () => {
        const response = await fetch(`/search?q=${debouncedSearchTerm}`);
        const json = await response.json();
        setResults(json);
      }
      
      fetchResults();
    }
  }, [debouncedSearchTerm]);
  
  return (
    <>
      <input value={searchTerm} onChange={(e) => setSearchTerm(e.target.value)} />
      <ul>
        {results.map((result) => <li>{result.title}</li>)}
      </ul>
    </>
  );
}
```

In the example above, the `debouncedSearchTerm` will only update and run our effect when the user stops typing for more than 500ms. If they start typing again, we run the effect again when they stop. This way, we make sure we are not firing off a request to the server for every keystroke.

在上面的示例中，`debouncedSearchTerm` 只有在用户停止输入 500ms 后才会执行。如果，用户再一次输入，停止输入后会再次执行函数。这样，我们就可以确保不是每次输入都会向服务器发起请求。

# useWhenVisible

This is a really useful hook for implementation features like infinite scrolling, where you fetch more data as the user scrolls through it. What this hook allows you to do is to listen for a specific element, and when that element becomes visible in the viewport, a callback function will be called to notify you about it. You can then use this callback as a trigger for fetching more data.

在实现无限滚动功能时，这也是非常有用的 hook。这个 hook 允许你监控一个指定的元素，当这个元素在可视窗口可见时，就会执行一个你指定的回调。你可以通过这个回调加载更多的元素。

It is also useful for lazy loading images or triggering animations as the user goes down the page.

这对图片懒加载或者当用户向下滚动时触发动画也非常有用。

The hook utilises the `IntersectionObserver` API in the browser to observe an element, and then it calls the provided callback when the element is intersecting (visible on the screen).

Hook 利用浏览器的 `IntersectionObserver` API 监控目标元素，当目标元素进入可是区域时，它就会调用开发者提供的回调。

## Implementation

## 实现

```tsx
import React, { useEffect } from 'react';

const useWhenVisible = (target: Element | undefined,
                        callback: () => void,
                        root: Element | undefined = document.body) => {
  useEffect(() => {
    if (!target || !root) {
      return;
    }
    
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        callback();
      }
    }, { root });
    
    observer.observe(target);
    
    return () => {
      observer.unobserve(target);
    }
  }, [target, callback, root]);
};

export default useWhenVisible;
```

## Usage

## 用例

```tsx
import React, { useEffect, useState, useRef } from 'react';
import useWhenVisible from './useWhenVisible';

const TodoList = () => {
  const limit = 25;
  const [offset, setOffset] = useState(0);
  const [todos, setTodos] = useState([]);
  const lastEl = useRef();

  useEffect(() => {
    const fetchTodos = async () => {
      const response = await fetch(`/todos?limit=${limit}&offset=${offset}`);
      const json = await response.json();
      setTodos((prev) => [...prev, ...json]);
    };

    fetchTodos();
  }, [limit, offset]);

  useWhenVisible(lastEl.current, () => {
    setOffset(offset + limit);
  });

  return (
    <ul>
      {todos.map((todo, index, arr) => (
        <li key={todo.id} ref={index === arr.length - 1 ? lastEl : undefined}>
          {todo.title} - Completed: {todo.completed}
        </li>
      ))}
    </ul>
  );
};
```

All the code above does is update the `offset` whenever the last element appears in the viewport, triggering our effect to run and fetch the next set of to-dos. Easy and simple.

从上面的代码中我们可知，当最后一个元素出现在可视区域时就会更新 `offset`，同时触发 useEffect，加载下一组数据。非常简单吧！

# useTimeout

This hook lets you use the normal `setTimeout` behaviour in a declarative way whenever you want to wait before doing something. This can be really useful in a lot of scenarios, and by using a hook, you never have to worry about memory leaks or weird bugs because you forgot to clear your timeout.

这个 hook 可以让你在需要延迟执行某些事情的时候用声明式的方式使用正常的 `setTimeout`。这在很多场景都非常有用，用了它之后，你不需在担心因为忘记清除定时器而引起的内存泄漏或者 bug 的问题。

## Implementation

## 实现

```tsx
import React, { useEffect, useRef } from 'react';

const useTimeout = (callback: () => void, delay: number | null) => {
  const savedCallback = useRef();

  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    
    if (delay !== null) {
      const id = setTimeout(tick, delay);
      return () => clearTimeout(id);
    }
  }, [delay]);
};

export default useTimeout;
```

First, we create a ref for the callback function. Then we use an effect to remember the latest callback function. Then we use an effect to set up the timer and clean up afterward. What’s neat about this is that you can actually cancel a running timeout simply by passing in `null` as the delay since it will clear the timeout and will *not* schedule a new one (line 15).

首先，我们为回调创建了一个 ref。这样我们就可以保存最新的一个回调函数。然后，我们设置一个定时器和清除定时器的功能。如果，你不给 delay 参数赋值，这时就*不会*有任何的任务执行（第15行）。

## Usage

## 用例

```tsx
import React, { useState } from 'react';
import useTimeout from './useTimeout';

const NewsletterBanner = () => {
  // Wait 5 seconds before poppung up banner
  const wait = 5000;
  const [visible, setVisible] = useState(false);

  useTimeout(() => {
    setVisible(true);
  }, wait);

  if (!visible) return null;

  return (
    <div>
      ... some newsletter modal
    </div>
  );
};
```

If this isn’t easy to read, I don’t know what is. Simple, declarative, and easy to use.

如果，以上代码还不明白，我就不知道还有什么能够更加简单的演示这个 hook 的作用了。

------

# useInterval

This is very similar to `useTimeout`. I’ve included `setTimeout`’s sister (`setInterval`) as a hook. Their implementation and API look almost the same, so I’m just going to show you the code:

这个 hook 和 `useTimeout` 非常相似。我在 hook 中封装了类似 `setTimeout` 的 API `setInterval`。它们的实现看起来一摸一样，因此，我会直接展示代码：

## Implementation

```tsx
import React, { useEffect, useRef } from 'react';

const useInterval = (callback: () => void, delay: number | null) => {
  const savedCallback = useRef();

  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    
    if (delay !== null) {
      const id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
};

export default useInterval;
```

`useInterval` is very useful if you want to do any sort of polling on the server to get fresh data. If you don’t have control over the API, you cannot just implement something like WebSockets to push to data to the clients. Instead, you could set polling up by using this hook.

如果，你打算向服务器轮训刷新一些数据时，`useInterval` 就非常有用。如果，你不能控制 API，那么，你就无法实现类似 WebSockets API 一样的功能，向客户端推送数据。这时，你只能通过这个 hook 设置一个轮训。

Let’s say you have a collaborative shopping list app where you and your S.O. can cross things off a list when you put them in the basket at the grocery store. In such a scenario, getting fresh data is an absolute must so you don’t meet up at the register with two of the same item or forget it completely because you thought your S.O. got it.

假设，你有一个收集购物清单的应用，当你把一些物品加入到购物车时可以交叉对比。在这种情况下，你必须获取最新的数据，避免添加相同的商品。

## Usage

```tsx
import React, { useState, useCallback } from 'react';
import useInterval from './useInterval';

const ShoppingList = () => {
  // Wait 5 seconds before fetching new data
  const POLL_DELAY = 5000;
  const [items, setItems] = useState([]);

  const fetchItems = useCallback(async () => {
    const response = await fetch('/shopping-list/items');
    const json = await response.json();
    setItems(json);
  }, []);

  useEffect(() => {
    // Fetch items from API on mount
    fetchItems();
  }, []);

  useInterval(() => {
    fetchItems();
  }, POLL_DELAY);

  return (
    <ul>
      {items.map((item) => <li>{item.title}</li>)}
    </ul>
  )
};
```

This is just a very simplified example of how you could poll an API for fresh data every five seconds to keep the view updated.

这个演示非常简单，你可以每 5 秒中轮训一次 API 刷新数据。

------

# Conclusion

# 总结

I hope you enjoyed these examples and can make use of them someday in your applications.

我希望你喜欢这些 Hooks，在将来某一天可以使用到它们。

Thanks for reading!

感谢您的阅读！