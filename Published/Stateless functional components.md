## Stateless functional components
## 无状态功能组件

> 原文地址: [http://frontendinsights.com/stateless-functional-components/](http://frontendinsights.com/stateless-functional-components/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> In 2015, [Dan Abramov](https://nafrontendzie.pl/nowy-poczatek/) wrote a great article about [presentational and container components](http://medium.stfi.re/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0?sf=voozdng#.icy524ihh) in React. I like the approach presented in his article. Basically you should divide your application components into two groups – presentational ones are only responsible for presenting data and container ones which deal with the logic. If you don’t know this concept, please read Dan’s article first! Today, I will show you how to use **stateless functional components**, the feature added to [React](http://facebook.stfi.re/react/?sf=ebbplxv) in v0.14, to create these presentational components.

2015年，[Dan Abramov](https://nafrontendzie.pl/nowy-poczatek/) 写了一篇有关 [presentational and container components](http://medium.stfi.re/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0?sf=voozdng#.icy524ihh) 的文章。我喜欢他在文中提出的观点。基本上你可以将应用的组件划分两类，presentational 只是用来展示数据；container 带有业务逻辑。如果，你不明白这些概念，请先阅读 Dan 的文章。今天，我将展示如何使用**无状态功能组件**，这个功能是用来创建 presentational 组件的，在 [React](http://facebook.stfi.re/react/?sf=ebbplxv) v0.14 中添加的。

> ### The previous approach

### 以前的做法

> Before React v0.14 we always had to write a component by creating class and extending it from ```React.Component…```

React v0.14 之前，新建组件我们都是通过创建类并继承 ```React.Component```;

> Generally speaking, the presentational component is responsible only for showing data. That’s why it only usually has the ```render``` method. It also uses values stored in ```this.props``` directly in the JSX markup. The below example shows such a presentational component:

通常来说，presentational 组件仅仅负责展示数据。这就是为什么它只有 ```render``` 方法。存储在 ```this.props``` 中的数据是可以直接在 ```JSX``` 语法使用的。接下来例子展示的就是一个 presentational 组件。

```
import React from 'react';
 
class EmailTable extends React.Component {
    render() {
        return (
            <table>
                <tbody>
                    {this.props.emails.map((item, index) => {
                        return (
                            <tr key={index}>
                                <td>{item.email}</td>
                            </tr>
                        );
                    })}
                </tbody>
            </table>
        );
    }
}
 
export default EmailTable;
```

> As you can see, the ```EmailTable``` component is only responsible for rendering the table with emails. The list of emails is available in ```this.props```. 

如你所见，```EmailTable```只是用来展示 emails 表格。emails 数据是 ```this.props``` 中的一个值。

### Stateless functional components – the new approach

### 新的方式：无状态功能组件

> From v0.14 of React, we can use a different syntax to create such components. The difference is that now we don’t have to declare class. We can just use the arrow function instead! What is important here is that components created this way are stateless so you can’t use the internal state of the component like you normally do in “classic” components.

自从 React v0.14 开始，我们可以使用不同的语法创建组件。并不需要我们声明一个 ```class```。可以用三角函数代替。很重要的一点：以这种方式创建的组件是**无状态**的，你不能像普通组件一样使用 ```state```。

> Before I describe the main benefits of using this approach, let’s take a look at the same example as above but written using stateless functional components:

在我描述这种方式的好处之前，先让我们看看上面同样例子**无状态**的写法。

```
import React from 'react';
 
const EmailTable = (props) => {
    return (
        <table>
            <tbody>
                {props.emails.map((item, index) => {
                    return (
                        <tr key={index}>
                            <td>{item.email}</td>
                        </tr>
                    );
                })}
            </tbody>
        </table>
    );
}
 
export default EmailTable;
```
or we can write it even better:

我们还可以写的更好：

```
import React from 'react';
 
const EmailTable = ({ emails }) => {
    return (
        <table>
            <tbody>
                {emails.map((item, index) => {
                    return (
                        <tr key={index}>
                            <td>{item.email}</td>
                        </tr>
                    );
                })}
            </tbody>
        </table>
    );
}
 
export default EmailTable;
```

> Let’s discuss the first example. As you may have noticed, instead of creating a class, we just declared a constant ```EmailTable``` and assigned an arrow function to it. The function takes ```props``` as an argument so that we can access its ```props``` this way. The function just returns the JSX markup which uses the props argument instead of ```this.props```. Pretty simple, right?

让我们先看第一个例子。或许你已经注意到，我仅仅声明了一个常量 ```EmailTable``` 并给它赋值了一个三角函数，并没有新建 ```class```。函数有一个 ```props``` 参数，你可以访问这个 ```props```。函数只是返回了一段 ```JSX``` 代码片段，在这里我使用的是 ```props```，而不是 ```this.props```。很简单，对吧？

> The second example shows that we can extract only these properties of ```props``` which we really use. We can do this by using the [destructing assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) in the function declaration. The ```{ emails }``` means: get the emails property from ```props``` and assign it to the new variable emails. Cool 😉

第二个例子中，我通过[解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的方式，在函数声明时就把需要用到的参数提取出来了。```{ emails }``` ：说明把属性 ```props``` 中的 emails 赋值给一个新的变量 emails。酷😉
 
> You may ask: what about ```propTypes```??!! Well, we can still define it:

你或许会问：那 ```propTypes``` 怎么定义呢？它可以通过以下方式定义：

```
EmailTable.propTypes = {
    emails: React.PropTypes.array.isRequired
}
```
> It works the same with ```defaultProps```.

```defaultProps``` 可以按照同样的方式处理。

### Why is this a better approach?

### 这种方式有什么好处呢？

> First of all, it simplifies presentational components and prevents us from being tempted to use the internal state or lifecycle methods. If you know that the component should be “dumb”, just use a stateless functional component and you will be safe 😉

第一：语法非常简单，避免我们使用 ```state``` 和生命周期方法。正如你所知道的 “dumb” 组件一样。使用**无状态**组件是非常安全的。

> For the second one, we can read in the official React documentation:

第二：让我们看看官方文档：

> In an ideal world, many of your components would be stateless functions. In the future we plan to make performance optimizations specific to these components by avoiding unnecessary checks and memory allocations.

理想的情况下，在你的应用中大部分组件都应该是**无状态**组件。在将来，我们会针对这些组件通过避免不必要的检查和内存分配，来做性能优化。

> When you don’t need local state or lifecycle hooks in a component, we recommend declaring it with a function. Otherwise, we recommend to use the ES6 class syntax.

当你在一个组件中不需要本地 ```state``` 和处理生命周期的情况，我们推荐使用**无状态**组件。除此之外，还推荐使用 ```ES6 class``` 语法。

> As you can see, besides recommending the use of stateless functional components, they say that we can expect that this approach will be a better choice from the performance perspective. I think it is worth using now, even if these performance optimizations are not implemented yet. Let our code be prepared!

> 正如你所看到的，除了推荐使用**无状态**组件，官方还说：站在性能优化的角度，这也是明智之举。我们认为从现在开始就可以使用了，尽管官方还没有明确的性能优化。我们可以先把代码处理好。

### Summary

### 总结

> I think that we should all try to follow the rules described by Dan Abramov in the article I wrote about at the beginning. And I think that stateless functional components are very helpful in achieving this. Apart from this, in the future, we can expect performance optimization related to these type of components so this is an additional reason to use them!

我认为，在我们的工作中尽量遵循 Dan Abramov 的建议。**无状态**是一种很好的处理方式。除此之外，还有另外一个原因：官方对这种组件的性能优化。

> 译者注：   
> 扩展阅读   
> [http://hao.jser.com/archive/13422/](http://hao.jser.com/archive/13422/)   
> [https://segmentfault.com/a/1190000007786080](
https://segmentfault.com/a/1190000007786080)

