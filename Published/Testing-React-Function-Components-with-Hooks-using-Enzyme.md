# Testing React Function Components with Hooks using Enzyme

# 用 Enzyme 测试使用 Hooks 的 React 函数组件

> 原文地址: <https://medium.com/@acesmndr/testing-react-functional-components-with-hooks-using-enzyme-f732124d320a/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

![](https://miro.medium.com/max/1400/1*74PLuBhmzSM6XaDQd-coGA.png)

A **React Function Component** is simply a function that returns a React element. With React 16.8 the most awaited feature, [**hooks**](https://reactjs.org/docs/hooks-intro.html) was introduced which allowed for injecting state and lifecycle methods into stateless function components and make it stateful.  The simple syntax and plug and play ability of hooks made writing function components quite enjoyable and made writing class based components feel a bit cumbersome.

**React 函数组件**本质上是一个返回 React Element 的简单函数。这是 React v16.8 中最值得期待的功能，通过 [**Hooks**](https://reactjs.org/docs/hooks-intro.html) 的文档，我们知道使用 Hooks 可以在无状态的函数组件中注入 state 和生命周期方法，让组件变成 stateful。Hooks 简单的语法和即插即用的特性，让编写函数组件变得有趣，反观，编写类组件变得有点麻烦。

Consider this simple React function component implementing hooks:

看下这个用 Hooks 实现的 React 函数组件。

```jsx
import React from 'react';

export default function Login(props) {
  const { email: propsEmail, password: propsPassword, dispatch } = props;
  const [isLoginDisabled, setIsLoginDisabled] = React.useState(true);
  const [email, setEmail] = React.useState(propsEmail || '');
  const [password, setPassword] = React.useState(propsPassword || '');

  React.useEffect(() => {
    validateForm();
  }, [email, password]);

  const validateEmail = text => /@/.test(text);

  const validateForm = () => {
    setIsLoginDisabled(password.length < 8 || !validateEmail(email));
  };

  const handleEmailBlur = evt => {
    const emailValue = evt.target.value.trim();
    setEmail(emailValue);
  };

  const handlePasswordChange = evt => {
    const passwordValue = evt.target.value.trim();
    setPassword(passwordValue);
  };

  const handleSubmit = () => {
    dispatch('submit(email, password)');
    setIsLoginDisabled(true);
  };

  return (
    <form>
      <input
        type="email"
        placeholder="email"
        className="mx-auto my-2"
        onBlur={handleEmailBlur}
      />
      <input
        type="password"
        className="my-2"
        onChange={handlePasswordChange}
        value={password}
      />
      <input
        type="button"
        className="btn btn-primary"
        onClick={handleSubmit}
        disabled={isLoginDisabled}
        value="Submit"
      />
    </form>
  );
}
```

The above example consists of a simple form as a function component with an [uncontrolled](https://reactjs.org/docs/uncontrolled-components.html) email input field and a [controlled](https://reactjs.org/docs/forms.html#controlled-components) password input field with internal methods that update the state using the [useState](https://reactjs.org/docs/hooks-reference.html#usestate) and [useEffect](https://reactjs.org/docs/hooks-effect.html) hooks and a submit button that dispatches a submit action.

上面函数组件中的 form 由一个[不可控](https://reactjs.org/docs/uncontrolled-components.html)的 email input 和一个[可控](https://reactjs.org/docs/forms.html#controlled-components) password input 组成，内部通过 [useState](https://reactjs.org/docs/hooks-reference.html#usestate) 和 [useEffect](https://reactjs.org/docs/hooks-effect.html) Hooks 来更新 state，然后，提交按钮触发一个提交的动作。

By writing test specs for this component I’ll be covering the various use cases that we have to deal with while writing tests for React Function Components.

针对这个组件我要覆盖各种各样的情况，因此，我不得不为函数组件编写测试用例。

## Enzyme and Jest

## Enzyme 和 Jest

I wont go into depth about describing Jest and Enzyme and how to install them. I’ll just assume that you are a bit familiar with them to make this article brief. In short, [**Jest**](https://jestjs.io/) is the javascript testing framework I am using for writing tests and [**Enzyme**](https://airbnb.io/enzyme/) is the testing utility that I am using along with jest to simplify writing tests. For setting up Jest and Enzyme use the following links:

- [Jest setup](https://jestjs.io/docs/en/getting-started)
- [Setting up enzyme with adapter](https://airbnb.io/enzyme/docs/installation/react-16.html)

如何安装 Enzyme 和 Jest，我不打算做过多的介绍。为了让这片文章更加简短，我假设你们已经对其有所了解。简单的说，[**Jest**](https://jestjs.io/) 就一个 javascript 测试框架，我用它来写测试；[**Enzyme**](https://airbnb.io/enzyme/) 是一个测试工具库，为了更加容易的编写测试用例，两者配合一起使用。以下是有关 Jest 和 Enzyme 相关设置的资源：

- [设置 Jest](https://jestjs.io/docs/en/getting-started)
- [设置 Enzyme 的适配器](https://airbnb.io/enzyme/docs/installation/react-16.html)

## Shallow vs Mount

In layman terms, [**mount**](https://github.com/airbnb/enzyme/blob/master/docs/api/mount.md) renders a component to its extreme leaf nodes whereas [**shallow**](https://airbnb.io/enzyme/docs/api/shallow.html) as the name suggests does a shallow render i.e. renders just the component and not its children.

对于外行来说，[**mount**](https://github.com/airbnb/enzyme/blob/master/docs/api/mount.md) 会渲染组件的所有的节点，但是，[**shallow**](https://airbnb.io/enzyme/docs/api/shallow.html) 就像它的名字一样它只会渲染组件本身，并不会渲染子组件。

I prefer shallow render over mounting the component as it helps testing a component as a unit rather than asserting the behavior of components inside a unit component. This is useful as we use an UI component library like [**Reactstrap**](https://reactstrap.github.io/) in our source code. So we wouldn’t want to test the components from this library (as it’s already been done in the library itself). If we used mount then even the component library we used would render to its leaf node. Shallow rendering helps us use the components without rendering them to their smallest html unit nodes. Also shallow rendering has performance benefits when compared with mount.

相比 mount 我更喜欢 shallow，这是因为它更有助于对组件做单元测试，而不是断言组件内部的行为。如果，我们用到了一些 UI 组件库比如：[**Reactstrap**](https://reactstrap.github.io/)，这就非常有用。因为，我们并不想对这些库里面的组件进行测试（因为组件自身已经测试过了）。如果，我们用 mount，那么组件库的相关节点也会被渲染。shallow 并不会渲染它们，我们可以正常使用这些组件。同时，相比 mount，shallow 在性能方面也更有优势。

I used to write class based components and test by [shallow rendering ](https://airbnb.io/enzyme/docs/api/shallow.html)them using enzyme and jest. Testing class based components are well documented in the [**Enzyme documentation**](https://airbnb.io/enzyme/). As for testing function components the documentation was scarce as it had just been released when I first started implementing it. React recommends using [react-testing-library](https://github.com/testing-library/react-testing-library) to test hooks which is based on mount.

过去，我编写的类组件都是用 [shallow](https://airbnb.io/enzyme/docs/api/shallow.html)，并配合 Jest 来测试的。[Enzyme](https://airbnb.io/enzyme/) 中类组件的测试文档非常完善。但是，关于函数组件的测试文档就很少，我在使用的时候，也只是刚刚发布。React 团队推荐使用 [react-testing-library](https://github.com/testing-library/react-testing-library) 去测试 Hooks。

I could figure out no proper way to access and test internal methods that would update the state of the component using Enzyme. So after googling for hours and not finding a proper solution on how to write test specs for function component using shallow and enzyme I did what every developer should do i.e. I posted [**a question on stackoverflow**](https://stackoverflow.com/questions/54713644/testing-react-functional-component-with-hooks-using-jest). Then Alex replied:

使用 Enzyme 我无法找到合适方式访问并测试那些可以更新组件 state 的内部方法。google 搜索后，也没找到合适的方案通过 Enzyme shallow 测试函数组件，我试了各种方法，比如：在 [stackoverflow 提问](https://stackoverflow.com/questions/54713644/testing-react-functional-component-with-hooks-using-jest)。然后得到了 Alex 的回复：

![](https://miro.medium.com/max/1400/1*UMVYAzPoekPLN1Vs0a4Szg.png)

It seemed to be the correct approach for testing the function component as we couldn’t know if the hooks were being called by spying or stubbing them but we could determine the state updates that the hooks carried out by looking at the updated props.

因为，我们无法知道 hooks 是通过 spy ，还是组件自身调用的，但是，我们可以通过检测 state 的更新来确定 hooks 的执行状态，这似乎是正确的测试函数组件的方法。

## Testing component ui and props

## 测试组件的 UI 和 Props

So for testing the Login component we **shallow** render it. To check the integrity of the UI we test the [snapshot](https://jestjs.io/docs/en/snapshot-testing) of the UI. A snapshot is the textual snap of html content of the rendered component. This covers the presence of all the elements and would fail if anything were to change as the new snapshot would not match the previous one.

因此，测试 Login 组件，我们用 **shallow** 渲染它。为了测试完整的 UI，我们可以通过 [快照](https://jestjs.io/docs/en/snapshot-testing) 来测试。快照是组件渲染后完整的 html 内容。它包含所有的元素，如果，有任何改动新的快照不能和上一次的匹配就会失败。

Then to test the rendered components we use the [**find**](https://airbnb.io/enzyme/docs/api/ReactWrapper/find.html) selector method to make sure that the elements are present and match the props so as to detect the absence or change in props.

然后，测试渲染后的组件我们用 [**find**](https://airbnb.io/enzyme/docs/api/ReactWrapper/find.html) 选择器，来取保所有的元素都存在并与 props 匹配，以便检测 props 的完整性。

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Login from '../index';

describe('<Login /> with no props', () => {
  const container = shallow(<Login />);
  it('should match the snapshot', () => {
    expect(container.html()).toMatchSnapshot();
  });

  it('should have an email field', () => {
    expect(container.find('input[type="email"]').length).toEqual(1);
  });

  it('should have proper props for email field', () => {
    expect(container.find('input[type="email"]').props()).toEqual({
      className: 'mx-auto my-2',
      onBlur: expect.any(Function),
      placeholder: 'email',
      type: 'email',
    });
  });

  it('should have a password field', () => { /* Similar to above */ });
  it('should have proper props for password field', () => { /* Trimmed for less lines to read */ });
  it('should have a submit button', () => { /* */ });
  it('should have proper props for submit button', () => { /* */ });
});
```

We could have tested individual prop instead of testing all the props.

我们可以测试个别的属性来代替测试所有的属性。

For example, for testing the value prop of password field we could have used:

例如，测试密码框的 value 属性我们可以这么做：

![](https://miro.medium.com/max/1400/1*nM9T4ihMIPOxzwSPHjpDww.png)

but we preferred checking all the props as it simplifies the task of writing tests. You wont need to decide on what props needs to be tested and no props are left out so it saves time and effort.

但是，为了更简单的编写测试用例，我更倾向于检测所有的属性。你不必关心哪些属性需要测试，哪些会被漏掉，这节省了时间，也满足了需求。

Now to test the *login components with props passed* we copy the same method as above and update the necessary props that would change when the ***initialProps*** are passed.

现在，我们来测试*有传递属性的 Login 组件*，我们使用上面相同的方法，来检测当传递 ***initalProps*** 时相关的属性是否有改变。

```jsx
describe('<Login /> with other props', () => {
  const initialProps = {
    email: 'acesmndr@gmail.com',
    password: 'notapassword',
  };
  const container = shallow(<Login {...initialProps} />);

  it('should have proper props for email field', () => {
    expect(container.find('input[type="email"]').props()).toEqual({
      className: 'mx-auto my-2',
      onBlur: expect.any(Function),
      placeholder: 'email',
      type: 'email',
    });
  });

  it('should have proper props for password field', () => {
    expect(container.find('input[type="password"]').props()).toEqual({
      className: 'my-2',
      onChange: expect.any(Function),
      type: 'password',
      value: 'notapassword',
    });
  });

  it('should have proper props for submit button', () => { /* */ });
});
```

![](https://miro.medium.com/max/1400/1*KtLprBzQWMubvckL1x-1NA.png)

## Testing state updates

## 测试 state 的更新

States are maintained in function components using **useState** hooks. As the state hooks are internal to the component they aren’t exposed thus they can’t be tested by calling them. Thus to test if a state has updated we [simulate](https://airbnb.io/enzyme/docs/api/ShallowWrapper/simulate.html) events or call the method props of the component and check if the state has updated by determining the update to the props of the rendered components.

函数组件通过 **useState** 来维护 state。因为，state hook 在组件内部并不能导出，因此，我们无法通过调用来测试它们。为了测试 state 是否有更新，我们可以 [simulate](https://airbnb.io/enzyme/docs/api/ShallowWrapper/simulate.html) 事件或者调用组件的属性方法，来检测 state 是否有更新并正确的渲染组件。

In other words we check for side-effects.

换句话说：我们要检测副作用。

> Support for useState has been added fairly recently [from React 16.8.5](https://github.com/facebook/react/pull/15120) thus requires the same or above version of React.
>
> [从 React 16.8.5](https://github.com/facebook/react/pull/15120) 开始支持 useState，因此，我们需要相同或者16.8.5以上版本 React。

```jsx
it('should set the password value on change event with trim', () => {
    container.find('input[type="password"]').simulate('change', {
      target: {
        value: 'somenewpassword  ',
      },
    });
    expect(container.find('input[type="password"]').prop('value')).toEqual(
      'somenewpassword',
    );
  });

  it('should call the dispatch function and disable the submit button on button click', () => {
    container.find('input[type="button"]').simulate('click');
    expect(
      container.find('input[type="button"]').prop('disabled'),
    ).toBeTruthy();
    expect(initialProps.dispatch).toHaveBeenCalledTimes(1);
  });
```

An alternative to simulating events using [**simulate**](https://airbnb.io/enzyme/docs/api/ShallowWrapper/simulate.html) method is to execute the props by calling them as functions by passing in the necessary params.

替代 [**simulate**](https://airbnb.io/enzyme/docs/api/ShallowWrapper/simulate.html) 的另外一方法是：可以通过调用挂载在 prop 上的方法，并传递必要的参数。

![](https://miro.medium.com/max/1400/1*D4S7ke0Ag4F2HDMMG1_BGA.png)

It is useful when we have a custom component with custom methods as props.

当我们需要测试自定义组件上的方法时这非常有用。

![](https://miro.medium.com/max/1400/1*1Q7FMrJCGKKMHITtQC1JEA.png)

Here to trigger **onDropdownClose** we do:

以下是触发 **onDropdownClose** 的方式：

![](https://miro.medium.com/max/1400/1*5JVoFnNPr7WPPP-HroYXVA.png)

## Lifecycle hooks

## 生命周期 hooks

Lifecycle hooks such as **useEffect** aren’t yet supported in shallow render (those hooks don’t get called) so we need to use mount instead of shallow to test those components for now. Like with the useState hook we check for updates to props to test these hooks by simulating events or executing props as functions.

在使用 shallow 渲染组件时，**useEffect** 这种生命周期相关 hooks 还不支持 （这些 hooks 不会被调用），因此，我们需要用 mount 代替。就像 useState 一样，我们可以模式事件或者作为属性方法来执行，然后检测 props 是否有更新来验证 hooks 是否正确。

```jsx
describe('<Login /> test effect hooks', () => {
  const container = mount(<Login />);

  it('should have the login disabled by default', () => {
    expect(
      container.find('input[type="button"]').prop('disabled'),
    ).toBeTruthy();
  });

  it('should have the login enabled with valid values', () => {
    container.find('input[type="password"]').simulate('change', {
      target: {
        value: 'validpassword',
      },
    });
    expect(container.find('input[type="button"]').prop('disabled')).toBeFalsy();
  });

  it('should have the login disabled with invalid values', () => {
    container.find('input[type="email"]').simulate('blur', { /* */ });
    expect(
      container.find('input[type="button"]').prop('disabled'),
    ).toBeTruthy();
  });
});
```

The support for these lifecycle hooks in enzyme are being tracked [**here**](https://github.com/airbnb/enzyme/issues/2011).

有关 Enzyme 支持的生命周期 hooks 详情可以看[这](https://github.com/airbnb/enzyme/issues/2011)。

## Methods that don’t update state

## 那些不更新 state 的方法

The methods that don’t manipulate the state can be refactored out of the component into a separate utils file and tested in it instead of having them inside the component. If the methods are pretty specific to the component and aren’t shared outside the component we could have it inside the component file but outside the main function component. To standardize the methods we could abstract them into a single method.

那些不需要维护 state 的方法可以重构从组件内部抽离放在单独文件中，单独测试这些功能函数，而不是在组件内部测试它们。如果，这个方法对组件非常特别，并不能提取到外面，我们也可以把它们跟组件放在同个一个文件中，但是，不要放在组件内部。为了使函数标准化，我们可以把它们抽象单一的方法。

```jsx
export const LoginMethods = () => {
  const isEmailValid = text => /@/.test(text);
  const isPasswordValid = password => password.length >= 8;
  const areFormFieldsValid = (email, password) =>
    isEmailValid(email) && isPasswordValid(password);

  return {
    isEmailValid,
    isPasswordValid,
    areFormFieldsValid,
  };
};

export default function Login(props) {
  /* useState declarations unchanged */

  React.useLayoutEffect(() => {
    setIsLoginDisabled(!LoginMethods().areFormFieldsValid(email, password));
  }, [email, password]);
```

Then testing them would be pretty straight forward.

这时，测试它们就非常的直接了。

```jsx
describe('LoginMethods()', () => {
  it('isEmailValid should return false if email is invalid', () => {
    expect(LoginMethods().isEmailValid('notvalidemail')).toBeFalsy();
    expect(LoginMethods().isEmailValid('notvalidemail.aswell')).toBeFalsy();
  });
  it('isEmailValid should return false if email is valid', () => {
    expect(LoginMethods().isEmailValid('validemail@gmail.com')).toBeTruthy();
  });
  /* Similar for isPasswordValid and areFormFieldsValid */
});
```

## Testing uncontrolled components

## 测试不可控的组件

But what about testing [**uncontrolled components**](https://reactjs.org/docs/uncontrolled-components.html)? Since email input field is uncontrolled, the email state isn’t reflected in the value of the component. If we set the value prop it throws an error saying that a component would also require an **onChange** method otherwise the component would be readonly as it would be a controlled component and wouldn’t allow us to enter anything into the input field.

但是，如何测试[不可控组件呢](https://reactjs.org/docs/uncontrolled-components.html)？由于，email input 是不可控的，它的 state 并不会受组件内部控制。如果，给组件设置一个 value 属性，我将会得到一个错误提示：**onChange** 是必须的，否则，组件将会变成一个只读的可控组件，并不能输入任何值。

![](https://miro.medium.com/max/1400/1*ZR-W-GY05EV1QIE3v5u9Ug.png)

Thus to make it testable without setting the **value** prop we set the data-attribute with the value of email state.

因此，为了在不设置 **value** 的情况下测试组件，我们将会把 email 的 state 赋值给 data-value 属性。

![](https://miro.medium.com/max/1400/1*rh6LtrAnXkfoskcbzv-SYA.png)

After setting the value to ***data-value*** prop we can easily test uncontrolled components like we did for controlled components by simulating events and checking side-effects in the ***data-value*** prop.

把 value 赋值给 ***data-value*** 后，我们就可以像可控组件一样通过模拟事件来测试，然后，检查一下 ***data-value*** 是否正确。

```jsx
it('should set the email data value prop', () => {
    container.find('input[type="email"]').simulate('blur', {
      target: {
        value: 'email@gmail.com',
      },
    });
    expect(container.find('input[type="email"]').prop('data-value')).toEqual(
      'email@gmail.com',
    );
  });
```

And by now the test coverage should have reached 100% which means that you have successfully tested your component with proper coverage.

现在，我们的测试覆盖率应该达到了100%，这就代表着，你已经成功的测试了组件，并有适当的覆盖率。

![](https://miro.medium.com/max/1400/1*o6scCuXoJR_Z3whOMgAT1Q.png)

## Refactor to stateless components and a custom hook (Optional)

## 重构无状态组件和自定义 Hook（可选）

To mitigate the issue regarding the uncontrolled components, a different implementation was suggested (thanks to Rohit dai) such that the state and lifecycle hooks are segregated out of the actual component that is to be tested into a [custom hook](https://reactjs.org/docs/hooks-custom.html).

为了减少不可控组件的相关问题，有一个实现上的建议（多谢 Rohit dai），就是把 state 和生命周期相关的 hooks 抽离真实的组件，然后把它们作为[自定义 hook](https://reactjs.org/docs/hooks-custom.html)来测试。

The hooks are separated out into a function which returns an object of element props which are then injected into individual elements in the actual function component. With this implementation, function component is separated into a custom hook and a stateless function component. The stateless function component is made stateful by injecting our custom hook.

把 hook 抽离到一个单独方法中，并返回一个对象，然后，通过自定义 hook 把对象注入到函数组件中。通过这种实现后，函数组件被拆分成了一个自定义 hook 和一个无状态组件。通过注入自定义组件让无状态组件变得有状态。

```jsx
import React from 'react';

export const LoginMethods = () => { /* Same as before */ };

export const useLoginElements = props => {
  const { email: propsEmail, password: propsPassword, dispatch } = props;
  const [isLoginDisabled, setIsLoginDisabled] = React.useState(true);
  const [email, setEmail] = React.useState(propsEmail || '');
  const [password, setPassword] = React.useState(propsPassword || '');

  React.useEffect(() => {
    setIsLoginDisabled(!LoginMethods().areFormFieldsValid(email, password));
  }, [email, password]);

  const handleEmailBlur = evt => {
    const emailValue = evt.target.value.trim();
    setEmail(emailValue);
  };

  const handlePasswordChange = evt => {
    const passwordValue = evt.target.value.trim();
    setPassword(passwordValue);
  };

  const handleSubmit = () => {
    dispatch('submit(email, password)');
    setIsLoginDisabled(true);
  };

  return {
    emailField: {
      onBlur: handleEmailBlur,
      value: email,
    },
    passwordField: {
      onChange: handlePasswordChange,
      value: password,
    },
    submitBtn: {
      onClick: handleSubmit,
      disabled: isLoginDisabled,
    },
  };
};

export default function Login(props) {
  const { emailField, passwordField, submitBtn } = useLoginElements(props);

  return (
    <form>
      <input
        type="email"
        placeholder="email"
        className="mx-auto my-2"
        onBlur={emailField.onBlur}
      />
      <input
        type="password"
        className="my-2"
        {...passwordField}
      />
      <input
        type="button"
        className="btn btn-primary"
        value="Submit"
        {...submitBtn}
      />
    </form>
  );
```

This would solve the problem of uncontrolled components as we could even export the value of email state using the **value** property (in **emailField** element) from our custom hook and still be able to disregard it in our main component by discarding props that aren’t required just like in the above example by only using required properties. We just used ***onBlur*** in the above example from the ***emailField*** element. We can now expose all the methods as properties in field props for testing without actually having to use all of them.

这将会解决不可控组件的问题，我们甚至可以通过自定义 hook 中的 **value** 属性（在 **emailField** 元素中）导出 email state，然后，在组件中，我们可以放弃那些不需要的属性，就像上面的示例一样只用那些必要的属性。上面的示例中我们只用到了来自 ***emailField*** 的 ***onBlur*** 方法。现在，我们可以把所有方法作为属性暴露出来，然后测试它们，其实，我们并不一定真正的用到它们。

## Testing our custom hook

## 测试自定义 hook

So now for testing our custom hook we wrap it inside a function component, As if we don’t do so, the tests would fail as hooks are designed to run only inside a function component. Then we expect the functional aspect of our code to be present and working functionally inside our custom hook.

现在，为了测试自定义 hook，我们把它引入到一个函数组件中，如果，我们不这么做，hook 将无法测试，这是因为 hooks 设计之初就只能在函数组件中使用。然后，我们期望自定义 hook 可以在函数组件中正常工作。

```jsx
describe('useLoginElements', () => {
  const Elements = () => {
    const props = useLoginElements({});
    return <div {...props} />;
  }; // since hooks can only be used inside a function component we wrap it inside one
  const container = shallow(<Elements />);

  it('should have proper props for email field', () => {
    expect(container.prop('emailField')).toEqual({
      value: '',
      onBlur: expect.any(Function),
    });
  });

  it('should set value on email onBlur event', () => {
    container.prop('emailField').onBlur({
      target: {
        value: 'newemail@gmail.com',
      },
    });
    expect(container.prop('emailField').value).toEqual('newemail@gmail.com');
  });

  it('should have proper props for password field', () => { /* Check onChange and value props */ });
  /* check other functional behavior of the component */
});

describe('<Login/>', () => {
  const container = shallow(<Login />);
  it('should match the snapshot', () => {
    expect(container.html()).toMatchSnapshot();
  });
 /* Test for other ui aspects of the page and not the functional behavior of the component */
});
```

And in the main login component we just test the UI elements and not the functionality or behavior. Thus we would be able to segregate the UI from the Behavior of the app.

最后，在 login 组件中，我们只需测试 UI，并不要关心其中的方法和行为。这样，我们就可以把 App 的行为和 UI 分开。

## Summary

## 总结

- Test entire props object of a rendered component instead of a single prop
- Reuse the spec to test component with and without props passed
- Check side-effects for testing hooks by simulating events
- To test unsupported hooks use mount and check for side-effects
- Refactor methods out of the component that don’t update component state
- Use data-attributes to test state of uncontrolled components

These aren’t the ways set in stone to test the components but something that I have been using. Hope this was helpful in some way. Please let me know in the comments if using custom hooks to segregate state from our function components is the proper way of refactoring for testing a function component or if you know any better ways to test React Function Components.

- 测试组件的整个 props 对象，而不是测试单个 prop
- 不管有没有传递 props，可以复用相同的测试规范
- 通过模拟事件，检查副作用，来测试 hooks
- 测试不支持的 hooks 要使用 mount，并检查副作用
- 那些不需要更新组件 state 逻辑要提取到组件外部
- 使用 data-attributes 来测试不可控组件

这些方法并不是一成不变的，但是，我一直在用。希望对你们有所帮助。利用自定义 hooks 去测试函数组件是否为正确的方法，或者，你有更好的方式测试函数组件，请在评论中告诉我。

Thanks for reading.👏 😇

多谢支持。👏 😇

