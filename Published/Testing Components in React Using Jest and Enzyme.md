## Testing Components in React Using Jest and Enzyme
## 测试 React 组件（2）

> 原文地址: <https://code.tutsplus.com/articles/testing-components-in-react-using-jest-and-enzyme--cms-31602/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

This is the second part of the series on Testing Components in React. If you have prior experience with Jest, you can skip ahead and use the GitHub code as a starting point. 

这是有关测试 React 组件的第二篇文章。如果，你有使用 Jest 的经验，你可以直接跳过，使用 GitHub 的代码作为起点。

In the [previous article](http://code.tutsplus.com/articles/testing-components-in-react-using-jest-part-one--cms-28934), we covered the basic principles and ideas behind test-driven development. We also set up the environment and the tools required for running tests in React. The toolset included Jest, ReactTestUtils, Enzyme, and react-test-renderer. 

在[上篇文章中](http://code.tutsplus.com/articles/testing-components-in-react-using-jest-part-one--cms-28934)，我们介绍了驱动开发者测试的基本背景和思路。我们也搭建了测试 React 组件所需的环境和工具。工具主要包括：Jest、ReactTestUtils、Enzyme 和 react-test-renderer。

We then wrote a couple of tests for a demo application using ReactTestUtils and discovered its shortcomings compared to a more robust library like Enzyme.

当时，我们用 ReactTestUtils 编写了一些测试用例，也发现了相比其它库的一些缺点，如：Enzyme 。

In this post, we'll get a deeper understanding of testing components in React by writing more practical and realistic tests. You can head to GitHub and [clone my repo](https://github.com/tutsplus/testing-components-in-react-using-jest-the-basics/tree/part-two) before getting started.

在这篇文章中，我们会通过更多的练习和相关测试加深对组件测试的理解。开始之前你可以去 GitHub 去[克隆代码](https://github.com/tutsplus/testing-components-in-react-using-jest-the-basics/tree/part-two)。

### Getting Started With the Enzyme API

### Enzyme 入门

Enzyme.js is an open-source library maintained by Airbnb, and it's a great resource for React developers. It uses the ReactTestUtils API underneath, but unlike ReactTestUtils, Enzyme offers a high-level API and easy-to-understand syntax. Install Enzyme if you haven't already.

Enzyme.js 是 Airbnb 开发维护的开源库，对 React 开发者来说它是一个很好的资源。它底层是使用 ReactTestUtils API，但是，又有些不一样，Enzyme 提供了一些高阶 API 和易于理解的语法。首先，你需要安装 Enzyme。

The Enzyme API exports three types of rendering options:

Enzyme API 有三种渲染模型可选：

1. shallow rendering
1. full DOM rendering
1. static rendering

**Shallow rendering** is used to render a particular component in isolation. The child components won't be rendered, and hence you won't be able to assert their behavior. If you're going to focus on unit tests, you'll love this. You can shallow render a component like this:

**Shallow rendering** 可以单独的渲染一个特定的组件。它的子组件并不会被渲染，你不用关心背后的行为。如果，你只关注单元测试，你会喜欢它的。你可以这么渲染一个组件：

```js
import { shallow }  from 'enzyme';
import ProductHeader from './ProductHeader';
 
// More concrete example below.
 const component = shallow(<ProductHeader/>);
```

**Full DOM rendering** generates a virtual DOM of the component with the help of a library called jsdom. You can avail this feature by replacing the ```shallow()``` method with ```mount()``` in the above example. The obvious benefit is that you can render the child components also. If you want to test the behavior of a component with its children, you should be using this. 

**Full DOM rendering** 借助于 jsdom 可以把组件转化成虚拟 DOM。在上面的示例中只需把 ```shallow()``` 用 ```mount()``` 替换就可以使用这个功能。很明显这个功能也会渲染子组件。如果，你想测试组件及其子组件的相关功能，你应该使用这个功能。

**Static rendering** is used to render react components to static HTML. It's implemented using a library called Cheerio, and you can read more about it in [the docs](https://airbnb.io/enzyme/docs/api/render.html). 

**Static rendering** 可以把组件渲染成一个静态的 HTML。它依赖一个叫：Cheerio 库，了解更多可以关注它的[文档](https://airbnb.io/enzyme/docs/api/render.html)。

### Revisiting Our Previous Tests

### 回顾上一个测试

Here are the tests that we wrote in the last tutorial:

这是我们上篇教程中写过的测试代码：

**src/components/__tests__/ProductHeader.test.js**

```js
import ReactTestUtils from 'react-dom/test-utils'; // ES6
 
describe('ProductHeader Component', () => {
    it('has an h2 tag', () => {
 
      const component = ReactTestUtils
                            .renderIntoDocument(<ProductHeader/>);    
      var node = ReactTestUtils
                    .findRenderedDOMComponentWithTag(
                     component, 'h2'
                    );
     
  });
 
    it('has a title class', () => {
 
      const component = ReactTestUtils
                            .renderIntoDocument(<ProductHeader/>);    
      var node = ReactTestUtils
                    .findRenderedDOMComponentWithClass(
                     component, 'title'
                 );
    })
  })
```

The first test checks whether the ```ProducerHeader``` component has an ```<h2>``` tag, and the second one finds whether it has a CSS class named ```title```. The code is hard to read and understand. 

第一段测试代码用来校验 ```ProducerHeader``` 是否有 ```<h2>``` 标签，第二段则用来校验组件是否有一个 ```title``` 类名。这些代码难以阅读和理解。

Here are the tests rewritten using Enzyme.

这是我们用 Enzyme 重写后的测试代码。

**src/components/__tests__/ProductHeader.test.js**

```js
import { shallow } from 'enzyme'
 
describe('ProductHeader Component', () => {
 
    it('has an h2 tag', () => {
      const component = shallow(<ProductHeader/>);    
      var node = component.find('h2');
      expect(node.length).toEqual(1);
      
  });
 
    it('has a title class', () => {
      const component = shallow(<ProductHeader/>);
      var node = component.find('h2');
      expect(node.hasClass('title')).toBeTruthy();
    })
  })
```

First, I created a shallow-rendered DOM of the ```<ProductHeader/>``` component using ```shallow()``` and stored it in a variable. Then, I used the ```.find()``` method to find a node with tag 'h2'. It queries the DOM to see if there's a match. Since there is only one instance of the node, we can safely assume that ```node.length``` will be equal to 1.

首先，我用 ```shallow()``` 方法把 ```<ProductHeader/>``` 组件转化成了 shallow-rendered DOM 并存储在一个变量里。然后，用 ```.find()``` 方法去查找 'h2' 节点。它会遍历 DOM 看是否有匹配的。既然只有一个节点，我们可以大胆假设 ```node.length``` 等于 1。

The second test is very similar to the first one. The ```hasClass('title')``` method returns whether the current node has a ```className``` prop with value 'title'. We can verify the truthfulness using ```toBeTruthy()```.

第二个测试和第一个非常类似。方法 ```hasClass('title')``` 会确定当前节点是否有一个值为 'title' 的 ```className``` 的属性。我们可以用 ```toBeTruthy()``` 验证真假。

Run the tests using ```yarn test```, and both the tests should pass. 

用 ```yarn test``` 执行测试，两个测试应该都会通过测试。

Well done! Now it's time to refactor the code. This is important from a tester's perspective because readable tests are easier to maintain. In the above tests, the first two lines are identical for both the tests. You can refactor them by using a ```beforeEach()``` function.  As the name suggests, the ```beforeEach``` function gets called once before each spec in a describe block is executed. 

很好！现在，是时候重构代码了。从测试人员角度来看，这很重要，因为这样会让代码跟容易阅读和维护。在上面的测试中，前两行代码对于两个测试来说是相同的。你可以用方法 ```beforeEach()``` 来重构它们。顾名思义，```beforeEach``` 会在执行每一个测试之前调用。

You can pass an arrow function to ```beforeEach()``` like this.

你可以像这样给 ```beforeEach()``` 传递一个三角函数。

**src/components/__tests__/ProductHeader.test.js**

```js
import { shallow } from 'enzyme'
 
describe('ProductHeader Component', () => {
    let component, node;
     
    // Jest beforeEach()
    beforeEach((()=> component = shallow(<ProductHeader/>) ))
    beforeEach((()=> node = component.find('h2')) )
     
    it('has an h2 tag', () => {
        expect(node).toBeTruthy()
    });
 
    it('has a title class', () => {
      expect(node.hasClass('title')).toBeTruthy()
    })
})
```

### Writing Unit Tests With Jest and Enzyme

### 用 Jest 和 Enzyme 编写单元测试

Let's write a few unit tests for the ```ProductDetails``` component. It is a presentational component that displays the details of each individual product. 

我们来为 ```ProductDetails``` 组件编写一些单元测试。它是一个展示组件，可以用来显示每个产品详细信息。

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/31602/image/TestingComponentsInReact-ProductDetailsView.png)

The unit test will try to assert the following assumptions:

- The component exists and the props are getting passed down.
- The props like product's name, description, and availability are displayed.
- An error message is displayed when the props are empty.

单元测试将尝试断言以下几个假设：

- 组件存在并且属性会传递下去
- 产品名称、描述和可用性等属性将会显示
- 当属性为空时会显示一条错误信息

Here is the bare-bones structure of the test. The first ```beforeEach()``` stores the product data in a variable, and the second one mounts the component.

这是测试代码的基本结构。第一个 ```beforeEach()``` 把产品数据存储在一个变量中，第二个会挂载组件。

**src/components/__tests__/ProductDetails.test.js**

```js
describe("ProductDetails component", () => {
    var component, product;
 
    beforeEach(()=> {
        product = {
            id: 1,
            name: 'NIKE Liteforce Blue Sneakers',
            description: 'Lorem ipsum.',
            status: 'Available'
        };
    })
    beforeEach(()=> {
        component = mount(<ProductDetails product={product} foo={10}/>);
    })
 
    it('test #1' ,() => {
      
    })
})
```

The first test is easy:

第一个测试很简单：

```js
it('should exist' ,() => {
      expect(component).toBeTruthy();
      expect(component.props().product).toEqual(product);
 })
```

Here we use the ```props()``` method which is handy for getting the props of a component.

我们用到了 ```props()``` 方法，它可以很方便的获取到组件的属性。

For the second test, you can query elements by their class names and then check whether the product's name, description etc. are part of that element's ```innerText```. 

对于第二个测试，你可以通过类名查找元素，然后校验产品名称和描述等等。是否为元素的 ```innerText``` 内容。

```js
it('should display product data when props are passed', ()=> {
     let title = component.find('.product-title');
     expect(title.text()).toEqual(product.name);
      
     let description = component.find('.product-description');
     expect(description.text()).toEqual(product.description);
      
  })
```

The ```text()``` method is particularly useful in this case to retrieve the inner text of an element. Try writing an expectation for the ```product.status()``` and see if all the tests are passing.

当前实例中，方法 ```text()``` 非常有用，可以获取元素内的文字。尝试为 ```product.status()``` 编写测试，并且看看是否所有的测试都通过。

For the final test, we're going to mount the ```ProductDetails``` component without any props. Then we're going to look for a class named '.product-error' and check if it contains the text "Sorry, Product doesn't exist".

在最后的测试中，我们在挂载 ```ProductDetails``` 组件时没有传递任何的属性。然后我们查找 '.product-error' 类名并校验是否包含 "Sorry, Product doesn't exist" 这段文字。

```js
it('should display an error when props are not passed', ()=> {
       /* component without props */
       component = mount(<ProductDetails />);
 
       let node = component.find('.product-error');
       expect(node.text()).toEqual('Sorry. Product doesnt exist');
   })
```

That's it. We've successfully tested the ```<ProductDetails />``` component in isolation. Tests of this type are known as unit tests.

好了。我们已经成功单独的对 ```<ProductDetails />``` 组件完成了测试。这种测试类型被称为单元测试。

### Testing Callbacks Using Stubs and Spies

### 用 Stubs 和 Spies 测试回调

We just learned how to test props. But to truly test a component in isolation, you also need to test the callback functions. In this section, we'll write tests for the ```ProductList``` component and create stubs for callback functions along the way. Here are the assumptions that we need to assert.

1. The number of products listed should be equivalent to the number of objects the component receives as props.
1. Clicking on ```<a>``` should invoke the callback function.

我们只是学会了如何测试组件的属性。但是要真正的单独测试组件，还需要测试回调的功能。在这个章节中，我们将会为 ```ProductList``` 组件编写测试代码并且为回调功能创建 stubs。这是我们需要假设的断言。

1. 列表中产品的数量应该等于组件属性中对象的数量
1. 点击 ```<a>``` 应该执行回调方法。

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/31602/image/TestingComponentsInReact-ProductListView.png)

Let's create a ```beforeEach()``` function that fills in mock product data for our tests.

在测试中，我们来创建一个 ```beforeEach()``` 并传入一些产品数据。

**src/components/__tests__/ProductList.test.js**

```js
beforeEach( () => {
       productData =   [
          {
              id: 1,
              name: 'NIKE Liteforce Blue Sneakers',
              description: 'Lorem ipsu.',
              status: 'Available'
       
          },
         // Omitted for brevity
      ]
  })
```

Now, let's mount our component in another ```beforeEach()``` block.

现在，我们可以在另外一个 ```beforeEach()``` 挂载组件。

```js
beforeEach(()=> {
    handleProductClick = jest.fn();
    component = mount( 
                    <ProductList 
                        products = {productData} 
                        selectProduct={handleProductClick} 
                    />
                );
})
```

The ```ProductList``` receives the product data through props. In addition to that, it receives a callback from the parent. Although you could write tests for the parent's callback function, that's not a great idea if your aim is to stick to unit tests. Since the callback function belongs to the parent component, incorporating the parent's logic will make the tests complicated. Instead, we are going to create a stub function.

```ProductList``` 组件通过属性接收产品数据。除此之外，它还可以接收来自父级的回调。当然，你可以为父级编写回调方法的测试，如果，你是为了单元测试，这不是一个好的主意。由于，回调属于父组件，因此融合父组件的逻辑会让测试变得复杂。我们可以创建 stub 方法来代替。

#### What's a Stub? 

#### Stub 是什么？

A stub is a dummy function that pretends to be some other function. This allows you to independently test a component without importing either parent or child components. In the example above, we created a stub function called ```handleProductClick``` by invoking ```jest.fn()```. 

Stub 是一个假设的方法可以用来假装做一些功能。可以让你在没有导入父组件或者子组件的情况下独立的测试组件。在上面的示例中，我们通过执行 ```jest.fn()``` 创建一个叫 ```handleProductClick``` 的 stub 方法。

Now we just need to find the all the ```<a>``` elements in the DOM and simulate a click on the first ```<a>``` node. After being clicked, we'll check if ```handleProductClick()``` was invoked. If yes, it's fair to say our logic is working as expected.

现在，我们只需找到 DOM 中所有的 ```<a>``` 元素，然后在第一个 ```<a>``` 节点上模拟点击。点击后，我们会检测 ```handleProductClick()``` 是否有执行。如果有执行，也就是说我们的逻辑是没有问题的。

```js
it('should call selectProduct when clicked', () => {
 
    const firstLink = component.find('a').first();
    firstLink.simulate('click');
    expect(handleProductClick.mock.calls.length).toEqual(1);
 
    })
})
```

Enzyme lets you easily simulate user actions such as clicks using ```simulate()``` method. ```handlerProductClick.mock.calls.length``` returns the number of times the mock function was called. We expect it to be equal to 1.

Enzyme 中的方法 ```simulate()``` 可以让你很方便的模拟用户行为，比如：点击。```handlerProductClick.mock.calls.length``` 会返回模拟方法调用的次数。我们期望它等于 1。

The other test is relatively easy. You can use the ```find()``` method to retrieve all ```<a>``` nodes in the DOM. The number of ```<a>``` nodes should be equal to the length of the productData array that we created earlier. 

其它的测试相对简单。你可以用 ```find()``` 遍历 DOM 中所有的 ```<a>``` 节点。```<a>``` 节点的数量应该和我们早期创建的 productData 数组长度相等。

```js
it('should display all product items', () => {
 
    let links = component.find('a');
    expect(links.length).toEqual(productData.length);
})
```

### Testing the Component's State, LifeCycleHook, and Method

### 测试组件的 State、生命周期方法和自定义方法

Next up, we're going to test the ```ProductContainer``` component. It has a state, a lifecycle hook, and a class method. Here are the assertions that need to be verified:

1. ```componentDidMount``` is called exactly once.
1. The component's state is populated after the component mounts.
1. The ```handleProductClick()``` method should update the state when a product id is passed in as an argument.

接下来，我们测试 ```ProductContainer``` 组件。它有一个 state、一个生命周期方法和一个自定义方法。以下就是我们需要断言验证的：

1. ```componentDidMount``` 只能被调用一次
1. 组件挂载后 state 应该被渲染。
1. 当给方法 ```handleProductClick()``` 传递一个产品 ID 时 state 应该更新。

To check whether ```componentDidMount``` was called, we're going to spy on it. Unlike a stub, a spy is used when you need to test an existing function. Once the spy is set, you can write assertions to confirm whether the function was called.

为了检测 ```componentDidMount``` 方法是否被调用，我们需要监控它。不像 stub，spy 是用来测试内置方法的。一旦 spy 设置好后，你可以断言确认方法是否被调用。

You can spy on a function as follows:

通过以下方式你可以监控一个方法：

**src/components/__tests__/ProductContainer.test.js**

```js
it('should call componentDidMount once', () => {
     componentDidMountSpy = spyOn(ProductContainer.prototype, 
                            'componentDidMount');
     //To be finished
 });
 ```

 The first parameter to ```jest.spyOn``` is an object that defines the prototype of the class that we're spying on. The second one is the name of the method that we want to spy. 

 ```jest.spyOn``` 的第一个参数是我们需要监控的对象中定义好的对象。第二个参数是我们需要监控的方法名称。

Now render the component and create an assertion to check whether spy was called.

现在，渲染一个组件，然后创建断言来检测监控的方法是否有调用。

```js
component = shallow(<ProductContainer/>);
expect(componentDidMountSpy).toHaveBeenCalledTimes(1);
```

To check that the component's state is populated after the component mounts, we can use Enzyme's ```state()``` method to retrieve everything in the state. 

检测组件 state 在组件挂载后是否有填充，我们可以用 Enzyme 提供的 ```state()``` 方法来校验 state 中的所有内容。

```js
it('should populate the state', () => {
        component = shallow(<ProductContainer/>);
        expect(component.state().productList.length)
            .toEqual(4)
 
    })
```

The third one is a bit tricky. We need to verify that ```handleProductClick``` is working as expected. If you head over to the code, you'll see that the ```handleProductClick()``` method takes a product id as input, and then updates ```this.state.selectedProduct``` with the details of that product. 

第三个测试比较棘手。我们需要验证 ```handleProductClick``` 是否按照预期的执行。如果，你回头看看代码，你会看到 ```handleProductClick()``` 方法根据输入的产品 ID，然后更新 ```this.state.selectedProduct```，以便更新产品详情数据。

To test this, we need to invoke the component's method, and you can actually do that by calling ```component.instance().handleProductClick()```. We'll pass in a sample product id. In the example below, we use the id of the first product. Then, we can test whether the state was updated to confirm that the assertion is true. Here's the whole code:

为了测试这个功能，我们需要执行组件的方法，你可以通过调用 ```component.instance().handleProductClick()``` 来完成方法的执行。我们会传入一个产品 ID。在下面的示例中，我用到的是第一个产品 ID。然后，我们可以测试 state 是否有更新来确认断言是否返回 true。这里有完整的代码：

```js
it('should have a working method called handleProductClick', () => {
       let firstProduct = productData[0].id;
       component = shallow(<ProductContainer/>);
       component.instance().handleProductClick(firstProduct);
 
       expect(component.state().selectedProduct)
           .toEqual(productData[0]);
   })
```

We've written 10 tests, and if everything goes well, this is what you should see:

我们编写了 10 个测试，如果没有什么报错，你应该会看到以下结果：

![](https://cms-assets.tutsplus.com/uploads/users/1795/posts/31602/image/TestingComponentsInReact-AllTestsPass.png)

### Summary

### 总结

Phew! We've covered almost everything that you need to know to get started with writing tests in React using Jest and Enzyme. Now might be a good time to head over to the [Enzyme website](https://airbnb.io/enzyme/docs/api/) to have a deeper look at their API.

唉！我们介绍了使用 Jest 和 Enzyme 在 React 中编写测试所需的所有的事情。现在，是一个比较好的时机去访问 [Enzyme 网站](https://airbnb.io/enzyme/docs/api/) 加深对 API 的理解。

What are your thoughts on writing tests in React? I'd love to hear them in the comments.

如果，你对此有什么想法？可以在评论中告诉我们。
