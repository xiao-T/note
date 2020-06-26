## 6 tips for better React performance
## React 应用性能优化的 6 条建议

> 原文地址: <https://itnext.io/6-tips-for-better-react-performance-4329d12c126b/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/4080/1*pqWCHRZ5pzADJ7DTFPwWqA.png)

When I first started to learn React I was all for knowing all the little tips & tricks to increase performance. Even up until now, the main performance booster is to try and avoid the reconciliation process (where React performs comparisons in order to decide whether the DOM should be updated or not).

我第一次学习 React 时，就知道了所有的可以提高性能的小技巧。直到现在，主要的性能优化手段就是避免协调（React 通过前后的对比来决定 DOM 是否需要更新）。

In this article I will try and list out a few easy ways to achieve better performance in your React app through simple development hacks. Under no means does this mean that you need to always apply these techniques, but it’s always good to know that they are there.

这篇文章中，我将会列举几个简单的方法，通过简单的开发技巧提升 React 应用的性能。这并不意味着你应该一直使用这些技术，但是，知道这些总是有好处的。

So, here we go:

所以，我们开始：

## 1. Utilise render bail-out techniques

## 1. 利用渲染 bail-out 技术

Each time a parent component updates, the children components update regardless of whether their props have changed or not. That means that even if the child component has **exactly** the same props as before, it will still re-render. To clarify, when I say re-render I don’t mean update the DOM, but pass through the process of reconciling whether there needs to be an update to the DOM or not. This is expensive, especially for big component trees, since essentially React will have to apply a diffing algorithm to check whether the previous & the newly computed tree have differences.

父级组件每次更新，不管子组件的 props 有没有改变，它们都会随着更新。也就是说，即使子组件的 props 与之前的**完全一致**，它们还是会重新渲染。需要说明一下，我在这说的重新渲染，并不是更新 DOM，而是会触发 React 的协调动作，然后来决定是否真正的更新 DOM。这个过程对性能优化尤为重要，尤其是那些大型组件树，底层上，React 不得不运行 diff 算法来检查组件树前后是否有不一样的地方。

You can easily avoid that by either extending your class-based components from `React.PureComponent` , utilising the `shouldComponentUpdate` lifecycle hook or wrapping your components in a `memo` Higher-Order Component. This way you can make sure your component updates **only** when its own props change.

可以继承 `React.PureComponent` （利用 `shouldComponentUpdate` 实现的）class 来实现组件或者用高阶组件 `memo` 来包装你的组件。利用这些方法，你可以保证在组件 **props 改变时**才会更新。

I should mention that by doing this for small components (like the ones in the example below), you will not only have zero actual benefit, but you might slow down your app by a tiny bit (since you are making React perform a shallow comparison **on every render** of the component). Thus, this technique should be used more aggressively for “heavy” components, but sparingly for components whose rendering time could be negligible.

需要注意的是：如果在比较小的组件中应用这些技术（就像下面演示的一样），将看不到有什么好处，反而会让你的应用变得有点慢（这是因为**每次渲染** React 都会做一次组件的浅对比）。因此，对于那些“复杂”的组件可以使用这些技术，相反，一些比较轻量的组件就需要慎重使用。

*TLDR; Use  `React.PureComponent` , `shouldComponentUpdate`or `memo()` for “heavy” components, but avoid it for really small ones. If needed, break a large component to smaller subcomponents, in order to wrap the latter with `memo()`.*

*TLDR: 对于那些“复杂”的组件使用 `React.PureComponent` , `shouldComponentUpdate` 或者 `memo()`，但是，对于一些轻量的组件就没有必要了。如果有需要，可以把一个大型组件拆分成多个小组件，以便用 `memo()` 来包装。*

```jsx
// index.jsx
export default function ParentComponent(props) {
  return (
    <div>
      <SomeComponent someProp={props.somePropValue}
    <div>
      <AnotherComponent someOtherProp={props.someOtherPropValue} />
    </div>
   </div>
 )
}


// ./SomeComponent.jsx
export default function SomeComponent(props) {
  return (
    <div>{props.someProp}</div>  
  )
}

// --------------------------------------------

// ./AnotherComponent.jsx (1st variation)
// This component will render anytime the value of `props.somePropValue` changed
// regardless of whether the value of `props.someOtherPropValue` has changed
export default function AnotherComponent(props) {
  return (
    <div>{props.someOtherProp}</div>  
  )
}

// ./AnotherComponent.jsx (2nd variation)
// This component will render only render when its *own* props have changed
export default memo((props) => {
  return (
    <div>{props.someOtherProp}</div>  
  )
});

// ./AnotherComponent.jsx (3rd variation)
// This component will also render only render when its *own* props have changed
class AnotherComponent extends React.PureComponent {
  render() {
    return <div>{this.props.someOtherProp}</div>   
  }
}

// ./AnotherComponent.jsx (4th variation)
// Same as above, re-written
class AnotherComponent extends React.PureComponent {
  shouldComponentUpdate(nextProps) {
    return this.props !== nextProps
  }
  
  render() {
    return <div>{this.props.someOtherProp}</div>   
  }
}
```

## 2. Avoid inline objects

## 2. 避免使用内联对象

Each time you inline an object, React re-creates a new reference to this object on every render. This causes components that receive this object to treat it as a referentially different one. Thus, a shallow equality on the `props `of this component will return `false` on every render cycle.

对于内联对象，React 在每次渲染都会重新创建新的引用。这会导致组件每次都认为这是新的对象。因此，组件每次渲染时对比前后 `props` 都会返回 `false`。

This is an indirect reference to the inline styles that a lot of people use. Inlining `styles` prop on a component will force your component to always render (unless you write a **custom** `shouldComponentUpdate` method) which could potentially lead to performance issues, depending on whether the component holds a lot of other subcomponents below it or not.

对于很多人来说内联样式就是一种间接引用。组件通过 prop 内联 `styles` 将会导致组件每次都会重新渲染（除非你**自定义** `shouldComponentUpdate` 方法），这也会有潜在的性能问题，具体取决于组件内部是否有多个子组件。

There is a nice trick to use if this prop **has to have** a different reference — because, for example, it’s being created inside a `.map` — , which is to spread its contents as props using the ES6 spread operator. Whenever the contents of an object are primitives (i.e. not functions, objects or Arrays) or non-primitives with “fixed” references, you can pass **them** as props instead of passing the object that contains them as a single prop. Doing that will allow your components to benefit from rendering bail-out techniques by referentially comparing their next and previous props.

如果，**不得不**使用不同引用，有一个小技巧。比如，可以使用 ES6 的扩展运算符传递多个 props 的内容。只要对象的内容是原始值（不是函数、对象或者数组）或者非原始值的“固定”引用，你都可以把它们包装成一个 prop 传递，而不是作为单独的 prop 传递。利用这种技巧可以让组件在重新渲染时通过对比前后 props 带来 bail-out  的好处。

*TLDR; You can’t benefit from `React.PureComponent` or `memo()` if you inline your styles (or in general objects). In certain situations, you can spread the contents of an object and pass themselves as props to your component to combat that.*

*TLDR：如果，使用内联样式（或者一般的对象），你将会无法从 `React.PureComponent` 或者 `memo()` 获益。在某些情况下，你可以把需要传递的内容合并成一个对象，作为组件的 props 向下传递。*

```jsx
// Don't do this!
function Component(props) {
  const aProp = { someProp: 'someValue' }
  return <AnotherComponent style={{ margin: 0 }} aProp={aProp} />  
}

// Do this instead :)
const styles = { margin: 0 };
function Component(props) {
  const aProp = { someProp: 'someValue' }
  return <AnotherComponent style={styles} {...aProp} />  
}
```

## 3. Avoid anonymous functions

## 3. 避免匿名函数

While anonymous functions are a great way to pass a function prop (especially one that needs to be invoked with another prop as its parameter), they get a different reference on every render. This is similar to the inline objects described above. In order to maintain the same reference to a function that you pass as a prop to a React component, you can either declare it as a class method (if you are using a class-based component) or utilise the `useCallback` hook to help you keep the same reference (if you are using functional components). For times where you need a different reference for each set of parameters that your function gets invoked with (i.e. functions calculated inside a `.map`), you can check out the plethora of **memoize** functions out there (like [lodash’s memoize](https://lodash.com/docs/4.17.11#memoize)). This is called “function caching” or “listener caching” and helps you have fixed reference on a dynamic number of anonymous functions at the cost of browser memory.

虽然，通过 prop 传递函数时匿名函数是一种非常好的方式（特别是需要其它props 作为参数调用时），但是，组件每次渲染都会得到不同的引用。这有点上面提到的内联样式。为了保证传递给 React 组件的方法都是同一个引用，你可以在 class 中定义方法（如果，你用的基于 class 的组件）或者使用 `useCallback` 保证引用的一致（如果，你是用函数组件）。某些情况下，如果，你需要为函数提供不同的参数（比如：`.map` 的回调函数），你可以利用 **memoize** 来包装函数（就像 [lodash’s memoize](https://lodash.com/docs/4.17.11#memoize)）。这种行为称为“函数缓存”或者“监听缓存”，它可以利用浏览器内存动态保存多个函数的固定引用。

Of course there are times where inline functions are the easiest way to go and don’t actually cause a performance issue to your app. This could either be because you utilise it on a very “lightweight” component or because a parent component actually **has to** re-render all of its contents every time its props change (thus you don’t care about the function getting a different reference since the component would re-render regardless of that).

当然，有些时候内联函数比较方便，而且，也不会引起性能问题。这可能是你在一些“轻量”组件上使用或者父组件每次 props 改变**都需要**重新渲染（因此，你不需要关心组件每次渲染时函数的引用是不是有变化）。

One last important thing that I wanted to stress, is that — by default — render-props functions are anonymous.Whenever you utilise a function as the `children` of a component, you can define it outside of the actual component so that it always has a fixed reference.

最后，有一件事我需要强调下：默认情况下 render-props 函数也是匿名函数。每当，把函数作为 `children` 组件时，你都可以在外部定义一个组件来代替这个函数，这样会保证引用的唯一性。

*TLDR; Try and bind function props to method or utilise `useCallback` as much as you can to benefit from rendering bail-out techniques. This applies to functions returned from render-props as well.*

*TLDR：尽可能使用 `useCallback` 来绑定 props 方法，这样你就可以通过 bail-out 受益。这也适用于 render-props 返回的函数。*

```jsx
// Don't do this!
function Component(props) {
  return <AnotherComponent onChange={() => props.callback(props.id)} />  
}

// Do this instead :)
function Component(props) {
  const handleChange = useCallback(() => props.callback(props.id), [props.id]);
  return <AnotherComponent onChange={handleChange} />  
}

// Or this for class-based components :)
class Component extends React.Component {
  handleChange = () => {
   this.props.callback(this.props.id) 
  }
  
  render() {
    return <AnotherComponent onChange={this.handleChange} />
  }
}
```

## 4. Lazy load components that are not instantly needed

## 4. 那些非必要的内容可以懒加载

This might seem unrelated to the article, but the less components React mounts, the faster it can mount them. Thus, if your initial render feels rather junky, you can reduce the amount of clutter by loading components when needed, after the initial mounting has finished. Simultaneously, that will reduce your bundles and allow the users to load your platform/app quicker. Lastly, by splitting the initial rendering, you are splitting JS workloads into smaller tasks which will give your page breaths of responsiveness. This can easily be done using the new `React.Lazy` as well as the `React.Suspense`

这条看起来和本文没多大关系，但是，减少 React 组件的大小，可以更快的显示它们。因此，如果，你觉得某些内容没必要初始化渲染，在初始还完成后，你可以根据需要再去加载它们。同时，也会减少应用启动时文件的大小，让应用加载更快。最后，通过拆分初始化的文件，可以把大型的工作量拆分成多个小任务，以便浏览器更快的响应。利用 `React.Lazy` 和 `React.Suspense` 可以轻松的实现文件的拆分。

*TLDR; Try and lazy-load the components that are not actually visible (or needed) to the user until he interacts with them.*

*TLDR: 对于那些不是实时可见的（或者不必要），直到和用户交互后才可见的组件，可以懒加载。*

```jsx
// ./Tooltip.jsx
const MUITooltip = React.lazy(() => import('@material-ui/core/Tooltip'));
function Tooltip({ children, title }) {
  return (
    <React.Suspense fallback={children}>
      <MUITooltip title={title}>
        {children}
      </MUITooltip>
    </React.Suspense>
  );
}

// ./Component.jsx
function Component(props) {
  return (
    <Tooltip title={props.title}>
      <AnotherComponent />
    </Tooltip>
  )
}
```

## 5. Tweak CSS instead of forcing a component to mount & unmount

## 5. 调整 CSS 避免组件强制 mount & unmount

Rendering is costly, **especially** when the DOM needs to be altered. Whenever you have some sort of accordion or tab functionality — where you only see one item at a time — , you might be tempted to unmount the component that’s not visible and mount it back when it becomes visible.

渲染的成本很高，**尤其**是 DOM 需要改变时。某些时候每次只需要显示某一组内容，比如：类型手风琴或者 tab 功能，你需要临时的 unmount 那些不可见的组件，同时，mount 可见的组件。

If the component that gets mounted/unmounted is “heavy”, then this operation might be way more costly than needed and result into lagginess. In cases like these, you would be better off hiding it through CSS, while keeping the content to the DOM. I do realise that sometimes this is not possible since you might have a case where having those components simultaneously mounted might cause issues (i.e. components competing with endless pagination on the window), but you should opt to do it when that’s not the case.

如果，组件的 mount/unmount 成本很高，那么这个操作可能会导致交互的延迟。对于这种情况，比较好做法是：可以通过 CSS 先隐藏组件，但是保证 DOM 存在。我意识到有些时候这并不可能，如果，同时有多个组件 mount 会带来一些问题（比如：多个组件之间共享同一个分页组件时），但是，对于其它情况，你应该选择使用刚才提到的方法。

As a bonus, tweaking the `opacity` to 0 has **almost zero** cost for the Browser (since it doesn’t cause a reflow) and should be preferred over `visibility` & `display` changes whenever possible.

另外，将 `opacity` 设置为 0 浏览器的成本**几乎为 0 **（因为，并不会引起回流），因此，尽可能的不去改变 `visibility` & `display`。

*TLDR; Instead of hiding through unmounting, sometimes it can be beneficial to hide through CSS while keeping your component mounted. This is a big gain for heavy components with significant mount/unmount timings.*

*TLDR：相比通过 unmount 隐藏组件，有时通过 CSS 隐藏组件更加好。对于那些需要花费更多时间 mount/unmount 的大型组件更加有利。*

```jsx
// Avoid this is the components are too "heavy" to mount/unmount
function Component(props) {
  const [view, setView] = useState('view1');
  return view === 'view1' ? <SomeComponent /> : <AnotherComponent />  
}

// Do this instead if you' re opting for speed & performance gains
const visibleStyles = { opacity: 1 };
const hiddenStyles = { opacity: 0 };
function Component(props) {
  const [view, setView] = useState('view1');
  return (
    <React.Fragment>
      <SomeComponent style={view === 'view1' ? visibleStyles : hiddenStyles}>
      <AnotherComponent style={view !== 'view1' ? visibleStyles : hiddenStyles}>
    </React.Fragment>
  )
}
```

## 6. Memoize expensive calculations

## 6. 缓存那些成本巨大的计算

There are times where a rendering is inevitable, but since your React component is a functional one, your rendering causes any calculations you have inside the component to be recomputed . The calculations whose values **do not** change on every render can be “memoized” using the new `useMemo` hook. Through that, you can bail-out of expensive calculations by using a value computed from a previous render. You can learn more about this [here](https://reactjs.org/docs/hooks-reference.html#usememo).

渲染总是不可避免的，但是，由于 React 组件是功能型组件，每次渲染组件内部的计算都会重新计算。使用 `useMemo` hook 可以把那些**不需要**重新计算的值“缓存”起来。这样一来，那些成本巨大的计算可以利用上次渲染时的值。在[这](https://reactjs.org/docs/hooks-reference.html#usememo)可以学到更多有关知识。

The overall goal is to reduce the amount of work JavaScript has to do during the rendering of a component, so that the main thread is blocked for less time.

总的来说，就是要减少组件在渲染期间的 JavaScript 的工作量，因此，主线程就不会阻塞。

*TLDR; utilise `useMemo` for expensive calculation caching*

*TLDR：利用 `useMemo` 缓存那些成本巨大的计算*

```jsx
// don't do this!
function Component(props) {
  const someProp = heavyCalculation(props.item);
  return <AnotherComponent someProp={someProp} /> 
}
  
// do this instead. Now `someProp` will be recalculated
// only when `props.item` changes
function Component(props) {
  const someProp = useMemo(() => heavyCalculation(props.item), [props.item]);
  return <AnotherComponent someProp={someProp} /> 
}
```

## Conclusion

## 总结

I intentionally left out stuff like “use the production build”, “use throttling on your keyboard listeners” or “utilise web workers” because i think this is kind of unrelated to React and more tied to general web development performance principles. What i tried to point out in this article are development practices that will help React perform better, free the main thread more and eventually make your app faster for the end user.

我有意的没有提到一些事情，比如：“使用生产环境构建”、“对键盘事件节流”或者“使用 web workers”，这是因为，我认为这些和 React 并没有什么关系，它们更多是与一般的 web 开发性能息息相关。这篇文章中提到的开发实践更多的是有助于提升 React 的性能，释放主线程，最终让用户感觉应用响应更快。

Thanks for reading :)

感谢阅读:)