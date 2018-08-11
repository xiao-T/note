## Build a Reusable Design System With React
## 用 React 构建可复用的设计系统

> 原文地址: <https://code.tutsplus.com/tutorials/build-a-reusable-design-system-with-react--cms-29954>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

React has done a lot to simplify web development. React's component-based architecture makes it easy in principle to decompose and reuse code. However, it's not always clear for developers how to share their components across projects. In this post, I'll show you some ways to fix that.

React 让 web 开发简化了很多。原则上 React 基于组件的模式让代码分解和复用变得更加容易。然而，开发者并不总是清楚如何跨项目分享他们的组件。在这片文章中，我会展示几种可用的方法。

React has made it easier to write beautiful, expressive code. However, without clear patterns for component reuse, code becomes divergent over time and becomes very difficult to maintain. I've seen codebases where the same UI element had ten different implementations! Another issue is that, more often than not, developers tend to couple the UI and the business functionality too tightly and struggle later when the UI changes.

React 让书写漂亮，并富有表达力的代码更加容易。然而，如果组件不能很好的复用，随着时间的推移代码变得更加零散和更加难以维护。我曾经看到的代码库中，同样的 UI 有十几种不同的实现！另外一个问题，开发者通常会把 UI 和业务代码耦合在一起，当 UI 需要改变时就变的很困难。

Today, we'll see how we can create shareable UI components and how to establish a consistent design language across your application.

今天，我们将会看到如何创建可共享的 UI 组件，如何构建贯穿整个应用的一致的设计语言。


### Getting Started

### 开始

You need an empty React project to begin. The quickest way to do this is through [create-react-app](https://github.com/facebookincubator/create-react-app), but it takes some effort to set up Sass with this. I've created a skeleton app, which you can clone from [GitHub](https://github.com/skmvasu/design-system-skeleton). You can also find the [final project in our tutorial GitHub repo](https://github.com/tutsplus/build-a-reusable-design-system-with-react).

一开始你需要一个空的 React 项目。最快捷的方式就是 [create-react-app](https://github.com/facebookincubator/create-react-app)，但是，还是需要设置一下 Sass。我创建了一个应用框架，你可以在 [GitHub](https://github.com/skmvasu/design-system-skeleton) 克隆它。你也可以在[教程的代码仓中找到完整的项目](https://github.com/tutsplus/build-a-reusable-design-system-with-react)。

To run, do a ```yarn-install``` to pull all dependencies in, and then run ```yarn start``` to bring up the application. 

运行 ```yarn-install``` 安装所有的依赖，然后通过 ```yarn start``` 启动应用。

All the visual components will reside under the **design_system** folder along with the corresponding styles. Any global styles or variables will be under **src/styles**.

所有的视觉组件和相应的样式单独保存在 **design_system** 目录下。任何全局样式和变量保存在 **src/styles**。

![](https://cms-assets.tutsplus.com/uploads/users/1896/posts/29954/image/folder%20structure.png)

### Setting Up the Design Baseline

### 设置设计的基准

When was the last time you got a you-are-dead-to-me look from your design peers, for getting the padding wrong by half a pixel, or not being able to differentiate between various shades of grey? (There is a difference between ```#eee``` and ```#efefef```, I'm told, and I intend to find it out one of these days.)

最近一次被设计同行鄙视是什么时候，padding 半个像素的错误，或者不能区分各个灰色色调的区别？（我被告知，```#eee``` 和 ```#efefef``` 有不同，我打算在一天内找出来）

One of the aims of building a UI library is to improve the relationship between the design and development team. Front-end developers have been coordinating with API designers for a while now and are good at establishing API contracts. But for some reason, it eludes us while coordinating with the design team. If you think about it, there are only a finite number of states a UI element can exist in. If we're to design a Heading component, for example, it can be anything between ```h1``` and ```h6``` and can be bold, italicised, or underlined. It should be straightforward to codify this.

构建 UI 库其中之一的目的是为了提升设计和开发团队的关系。前端开发者和 API 设计者已经可以很好的沟通并构建很好的 API 协议。但是，由于某些原因，在跟设计团队沟通时总是逃避。想象一下，对于一个 UI 元素只能存在有限的几个状态。例如，如果，我们设计一个标题组件，它可以是 ```h1``` 和 ```h6``` 任何一个标签，可以是粗体、斜体或者有下划线。这个实现起来应该很直接。

#### The Grid System

#### 网格系统

The first step before embarking on any design project is to understand how the grids are structured. For many apps, it's just random. This leads to a scattered spacing system and makes it very hard for developers to gauge which spacing system to use. So pick a system! I fell in love with the [4px - 8px grid system when I first read about it](https://builttoadapt.io/intro-to-the-8-point-grid-system-d2573cde8632). Sticking to that has helped simplify a lot of styling issues.

在着手构建任何设计项目时首先考虑的是需要理解网格是如何构建的。对于很多应用来说，这很随意。这会导致间距系统非常零散，并且开发者很难确定该使用那个间距。因此需要确定一个合适的间距。[当我第一次阅读 4px - 8px 网格系统](https://builttoadapt.io/intro-to-the-8-point-grid-system-d2573cde8632)时就爱上了它。遵守这一规则会简化我们样式的很多问题。

Let's start by setting up a basic grid system in the code. We'll begin with an app component that sets out the layout.

让我们在代码中先设置一个基本的网格系统。我们从设置布局的 app 组件开始。

```jsx
//src/App.js
 
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.scss';
import { Flex, Page, Box, BoxStyle } from './design_system/layouts/Layouts';
 
class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Build a design system with React</h1>
        </header>
        <Page>
          <Flex lastElRight={true}>
            <Box boxStyle={BoxStyle.doubleSpace} >
              A simple flexbox
            </Box>
            <Box boxStyle={BoxStyle.doubleSpace} >Middle</Box>
            <Box fullWidth={false}>and this goes to the right</Box>
          </Flex>
        </Page>
      </div>
    );
  } 
}
 
export default App;
```

Next, we define a number of styles and wrapper components.

接下来，我们定义了一些样式和包装组件。

```jsx
//design-system/layouts/Layout.js
import React from 'react';
import './layout.scss';
 
export const BoxBorderStyle = {
    default: 'ds-box-border--default',
    light: 'ds-box-border--light',
    thick: 'ds-box-border--thick',
}
 
export const BoxStyle = {
    default: 'ds-box--default',
    doubleSpace: 'ds-box--double-space',
    noSpace: 'ds-box--no-space'
}
 
export const Page = ({children, fullWidth=true}) => {
    const classNames = `ds-page ${fullWidth ? 'ds-page--fullwidth' : ''}`;
    return (<div className={classNames}>
        {children}
    </div>);
 
};
 
export const Flex = ({ children, lastElRight}) => {
    const classNames = `flex ${lastElRight ? 'flex-align-right' : ''}`;
    return (<div className={classNames}> 
        {children}
    </div>);
};
 
export const Box = ({
    children, borderStyle=BoxBorderStyle.default, boxStyle=BoxStyle.default, fullWidth=true}) => {
    const classNames = `ds-box ${borderStyle} ${boxStyle} ${fullWidth ? 'ds-box--fullwidth' : ''}` ;
    return (<div className={classNames}>
        {children}
    </div>);
};
```

Finally, we'll define our CSS styles in SCSS.

最后，我们将在 SCSS 中定义样式。

```scss
/*design-system/layouts/layout.scss */
@import '../../styles/variables.scss';
$base-padding: $base-px * 2;
 
.flex {
    display: flex;
    &.flex-align-right > div:last-child {
        margin-left: auto;
    }
}
 
.ds-page {
    border: 0px solid #333;
    border-left-width: 1px;
    border-right-width: 1px;
    &:not(.ds-page--fullwidth){
        margin: 0 auto;
        max-width: 960px;
    }
    &.ds-page--fullwidth {
        max-width: 100%;
        margin: 0 $base-px * 10;
    }
}
 
.ds-box {
    border-color: #f9f9f9;
    border-style: solid;
    text-align: left;
    &.ds-box--fullwidth {
        width: 100%;
    }
 
    &.ds-box-border--light {
        border: 1px;
    }
    &.ds-box-border--thick {
        border-width: $base-px;
    }
 
    &.ds-box--default {
        padding: $base-padding;
    }
 
    &.ds-box--double-space {
        padding: $base-padding * 2;
    }
 
    &.ds-box--default--no-space {
        padding: 0;
    }
}
```

There is a lot to unpack here. Let's start from the bottom. **variables.scss** is where we define our globals like color and set up the grid. Since we're using the 4px-8px grid, our base will be 4px. The parent component is ```Page```, and this controls the flow of the page. Then the lowest-level element is a ```Box```, which determines how content is rendered in a page. It's just a ```div``` that knows how to render itself contextually. 

有很多在这没有展示。让我们从头开始。**variables.scss** 定义了全局的变量，比如：颜色和网格的设置。由于我们使用了 4px-8px 网格，我们将用 4px 做为基础值。父组件是 ```Page```，它控制着页面的文档流。层级最低元素是 ```Box```，它定义了内容如何在页面上渲染。它本身就是一个 ```div```，并在自身的上下文中渲染自己。

Now, we need a ```Container``` component that glues together multiple ```div```s. We've chosen ```flex-box```, hence the creatively named ```Flex``` component. 

现在，我们需要一个 ```Container``` 组件，它包含多个 ```div```。我们选择 ```flex-box```，所以组件命名为 ```Flex```。

#### Defining a Type System

#### 定义 Type 系统

The type system is a critical component of any application. Usually, we define a base through global styles and override as and when needed. This often leads to inconsistencies in design. Let's see how this can be easily solved by adding to the design library.

Type 系统是任何应用的关键组件。通常，我们会定义一个基本的全局样式，在需要的情况下复写它。这经常会导致设计不一致。让我们看看如何通过设计库来轻松的解决这个问题。

First, we'll define some style constants and a wrapper class.

首先，我们会定义一些样式常量和一个 class 容器。

```jsx
// design-system/type/Type.js
import React, { Component } from 'react';
import './type.scss';
 
export const TextSize = {
    default: 'ds-text-size--default',
    sm: 'ds-text-size--sm',
    lg: 'ds-text-size--lg'
};
 
export const TextBold = {
    default: 'ds-text--default',
    semibold: 'ds-text--semibold',
    bold: 'ds-text--bold'
};
 
export const Type = ({tag='span', size=TextSize.default, boldness=TextBold.default, children}) => {
    const Tag = `${tag}`; 
    const classNames = `ds-text ${size} ${boldness}`;
    return <Tag className={classNames}>
        {children}
    </Tag>
};
```

Next, we'll define the CSS styles that will be used for text elements.

接下来，我们会为这些 text 元素定义样式。

```scss
/* design-system/type/type.scss*/
 
@import '../../styles/variables.scss';
$base-font: $base-px * 4;
 
.ds-text {
    line-height: 1.8em;
     
    &.ds-text-size--default {
        font-size: $base-font;
    }
    &.ds-text-size--sm {
        font-size: $base-font - $base-px;
    }
    &.ds-text-size--lg {
        font-size: $base-font + $base-px;
    }
    &strong, &.ds-text--semibold {
        font-weight: 600;
    }
    &.ds-text--bold {
        font-weight: 700;
    }
}
```

This is a simple ```Text``` component representing the various UI states text can be in. We can extend this further to handle micro-interactions like rendering tooltips when the text is clipped, or rendering a different nugget for special cases like email, time, etc. 

这是一个简单的 ```Text``` 组件，它代表了 UI 的各个状态。我们可以进一步扩展这个功能来处理交互功能，比如：当文本被省略的时候现实一个 tooltip，或者为 email、time 渲染不同的样式等等。

#### Atoms Form Molecules
#### 分子组成原子

So far, we've built only the most basic elements that can exist in a web application, and they're of no use on their own. Let's expand on this example by building a simple modal window. 

目前为止，我们仅创建了 web 应用中最基本的元素，只是这样，它们是没有用的。我们可以在示例的基础上扩展构建一个简单的模态弹窗。

First, we define the component class for the modal window.

首先，我们定义了模态弹窗的组件类。

```jsx
// design-system/Portal.js
import React, {Component} from 'react';
import ReactDOM from 'react-dom';
import {Box, Flex} from './layouts/Layouts';
import { Type, TextSize, TextAlign} from './type/Type';
import './portal.scss';
 
export class Portal extends React.Component {
    constructor(props) {
        super(props);
        this.el = document.createElement('div');
    }
 
    componentDidMount() {
        this.props.root.appendChild(this.el);
    }
 
    componentWillUnmount() {
        this.props.root.removeChild(this.el);
    }
 
    render() {  
        return ReactDOM.createPortal(
            this.props.children,
            this.el,
        );
    }
}
 
 
export const Modal = ({ children, root, closeModal, header}) => {
    return <Portal root={root} className="ds-modal">
        <div className="modal-wrapper">
        <Box>
            <Type tagName="h6" size={TextSize.lg}>{header}</Type>
            <Type className="close" onClick={closeModal} align={TextAlign.right}>x</Type>
        </Box>
        <Box>
            {children}
        </Box>
        </div>
    </Portal>
}
```

Next, we can define the CSS styles for the modal.

接下来，我们为模态弹窗定义 CSS 样式。

```scss
#modal-root {
    .modal-wrapper {
        background-color: white;
        border-radius: 10px;
        max-height: calc(100% - 100px);
        max-width: 560px;
        width: 100%;
        top: 35%;
        left: 35%;
        right: auto;
        bottom: auto;
        z-index: 990;
        position: absolute;
    }
    > div {
        background-color: transparentize(black, .5);
        position: absolute;
        z-index: 980;
        top: 0;
        right: 0;
        left: 0;
        bottom: 0;
    } 
    .close {
        cursor: pointer;
    }
}
```

For the uninitiated, ```createPortal``` is very similar to the ```render``` method, except that it renders children into a node that exists outside the DOM hierarchy of the parent component. It was introduced in [React 16](https://reactjs.org/docs/portals.html).

对于初学者来说，```createPortal``` 除了会把子元素渲染在父组件之外的层级中，它和 ```render``` 方法类似。在 [React 16](https://reactjs.org/docs/portals.html) 有详细介绍。

#### Using the Modal Component

#### 使用 Modal 组件

Now that the component is defined, let's see how we can use it in a business context.

现在，组件已经定义好了，让我们看看如何在业务场景中使用它。

```jsx
//src/App.js
 
import React, { Component } from 'react';
//...
import { Type, TextBold, TextSize } from './design_system/type/Type';
import { Modal } from './design_system/Portal';
 
class App extends Component {
  constructor() {
    super();
    this.state = {showModal: false}
  }
 
  toggleModal() {
    this.setState({ showModal: !this.state.showModal });
  }
 
  render() {
 
          //...
          <button onClick={this.toggleModal.bind(this)}>
            Show Alert
          </button>
          {this.state.showModal && 
            <Modal root={document.getElementById("modal-root")} header="Test Modal" closeModal={this.toggleModal.bind(this)}>
            Test rendering
          </Modal>}
            //....
    }
}
```

We can use the modal anywhere and maintain the state in the caller. Simple, right? But there is a bug here. The close button does not work. That's because we've built all the components as a closed system. It just consumes the props it needs and disregards the rest. In this context, the text component ignores the ```onClick``` event handler. Fortunately, this is an easy fix. 

我们可以在任何地方使用 modal，然后在调用的地方维护它的状态。很简单，对吧？但是，这有个 bug。关闭按钮无效。这是因为我们构建的所有组件都是一个封闭的系统。它只会使用需要的 props，并且无视其他的。在当前的示例中，text 组件忽略了 ```onClick``` 事件。幸运的是，这很容易被修复。

```jsx
// In  design-system/type/Type.js
 
export const Type = ({ tag = 'span', size= TextSize.default, boldness = TextBold.default, children, className='', align=TextAlign.default, ...rest}) => {
    const Tag = `${tag}`; 
    const classNames = `ds-text ${size} ${boldness} ${align} ${className}`;
    return <Tag className={classNames} {...rest}>
        {children}
    </Tag>
};
```

ES6 has a handy way to extract the remaining parameters as an array. Just apply that and spread them over to the component.

ES6 可以很容易的把剩余的参数以数组的方式提取出来。使用这种方法，然后把参数传递给组件。

### Making Components Discoverable

### 分享组件

As your team scales, it's hard to get everyone in sync about the components that are available. Storybooks are a great way to make your components discoverable. Let's set up a basic storybook component. 

随着团队的扩大，很难把有效的组件同步给每个人。Storybooks 是一种很好的分享组件的方法。让我们配置一个基础的 storybook。

To get started, run:

开始：

```
npm i -g @storybook/cli
 
getstorybook
```

This sets up the required configuration for the storybook. From here, it's a cinch to do the rest of the setup. Let's add a simple story to represent different states of ```Type```. 

storybook 还需要一些必要的配置。从这里开启，剩下的设置都很简单。让我们添加一个简单的 story 代表 ```Type``` 不同的状态。

```jsx
import React from 'react';
import { storiesOf } from '@storybook/react';
 
import { Type, TextSize, TextBold } from '../design_system/type/Type.js';
 
 
storiesOf('Type', module)
  .add('default text', () => (
    <Type>
      Lorem ipsum
    </Type>
  )).add('bold text', () => (
    <Type boldness={TextBold.semibold}>
      Lorem ipsum
    </Type>
  )).add('header text', () => (
    <Type size={TextSize.lg}>
      Lorem ipsum
    </Type>
  ));
```

The API surface is simple. ```storiesOf``` defines a new story, typically your component. You can then create a new chapter with ```add```, to showcase the different states of this component. 

API 非常简单。```storiesOf``` 定义了一个新的 story，一般就是你的组件。然后，通过 ```add``` 添加新的章节，为了展示组件不同的状态。

![](https://cms-assets.tutsplus.com/uploads/users/1896/posts/29954/image/storybook.png)

Of course, this is pretty basic, but storybooks have several add-ons that will help you add functionality to your docs. And did I mention that they have emoji support? 😲

当然，这是非常基本的，但是 storybooks 有一些插件可以帮助你添加文档。我还注意到她们也支持 emoji？😲

### Integrating With an Off-the-Shelf Design Library

### 集成到现有的设计库中

Designing a design system from scratch is a lot of work and may not make sense for a smaller app. But if your product is rich and you need a lot of flexibility and control over what you're building, setting up your own UI library will help you in the longer run. 

从头设计一套设计系统需要大量工作，对小的应用可能没有意义。但是，如果你的产品很丰富，并且你的应用需要很大的灵活性和可控性，从长远来看，配置自己的 UI 库对你很有帮助。

I've yet to see a good UI component library for React. My experience with react-bootstrap and material-ui (the library for React, that is, not the framework itself) wasn't great. Instead of reusing an entire UI library, picking individual components might make sense. For instance, implementing multi-select is a complex UI problem, and there are tons of scenarios to consider. For this case, it might be simpler to use a library like React Select or Select2.

我看到过一些优秀的 React UI 组件库。我个人的经验 react-bootstrap 和 material-ui (React 的扩展库, 并不是框架) 不是很好。选择单个组件比使用整个 UI 库更有意义。比如，multi-select 是一个复杂的组件，有太多场景需要考虑。对于这个问题，使用单一的库 React Select 或者 Select2 会更加简单。

A word of caution, though. Any external dependencies, especially UI plugins, are a risk. They are bound to change their APIs often or, on the other extreme, keep using old, deprecated features of React. This may affect your tech delivery, and any change might be costly. I'd advise using a wrapper over these libraries, so it will be easy to replace the library without touching multiple parts of the app.

提醒一句。任何外部的依赖，特别是 UI 插件，都是一种负担。它们的 API 必然会经常改动，或者极端情况下：还在使用 React 老旧的、废弃的功能。这也会影响你的技术，并且任何的改动代价都非常昂贵。我建议使用容器把库包装起来，这样，在不触及应用其它部分的时候，更容器移除依赖。

### Conclusion

### 总结

In this post, I've shown you some ways to split your app into atomic visual elements, using them like Lego blocks to achieve the desired effect. This facilitates code reuse and maintainability, as well as making it easy to maintain a consistent UI throughout your app.

在这片文章中，我想您展示了几种将应用分拆成原子视觉元素的方法，就像乐高积木使用她们去实现期望的效果。这有助于代码复用和可维护，也让保持应用 UI 一致性更容易。

Do share your thoughts on this article in the comments section!

可以在下面评论分享您的想法！
