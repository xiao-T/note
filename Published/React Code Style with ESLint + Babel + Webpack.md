## React Code Style with ESLint + Babel + Webpack
## ESLint + Babel + Webpack 环境下的 React 代码风格

> 原文地址: [https://www.robinwieruch.de/react-eslint-webpack-babel/](https://www.robinwieruch.de/react-eslint-webpack-babel/)     
**本文版权归原作者所有，翻译仅用于学习。**

---------

> Code style is an important topic for developers. When you code for yourself, it might be alright to violate best practices. However, in a team of developers you have to have a common code style. You should follow the same rules to make your code look alike. It helps others developers to read your code. It helps people to navigate in a new code base. ESLint in JavaScript helps you to set up rules and to enforce code style.

代码风格对于开发者来说非常重要。当你一个人编写代码，违反最佳实践或许没有问题。然而，在一个团队中必须有一套代码规范。你必须遵循相同的规则让你们的代码看起来一样。这有助于别人阅读你的代码。也有利于其他人在你的代码基础上编写新的代码。在 JavaScript 中 ESLint 可以帮助你设置代码规则，并强制执行代码规范。 

> The article will teach you how to setup ESLint in a [React + Babel + Webpack](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/) environment. You can setup rules for JavaScript and React to enforce a unified code style. Every violated rule will ping you and your team members in the terminal or developer console in the browser. Additionally you will not only learn about custom rules, but also recommended best practices in the community. You will learn to extend your rules easily with a common set of rules in one line of configuration.


这篇文章将教你如何在 [React + Babel + Webpack](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/) 环境中设置 ESLint。你可以为 JavaScript 和 React 设置规则并强制执行统一代码风格。每一个被违反的规则都会在终端或浏览器的开发者控制台上通知你和你的团队成员。另外，你不仅会学习如何自定义规则，也会在社区中得到最佳实践。你还会学到如何通过一条简单的设置轻松的使用通用配置扩展你的规则。

> Apart from ESLint for as a code style checker, you should know about the alternatives as well: Prettier and StandardJS.

除了 ESLint 可以检测代码风格之外，你应该还要知道其他的替代方案，比如：Prettier 和 StandardJS。

### Table of Contents

### 目录

- [ESLint](#eslint)
- [ESLint + Babel](#eslint--babel)
- [ESLint Rules](#eslint-rules)
- [ESLint Rules for React](#eslint-rules-for-react)
- [Extend ESLint Rules](#extend-eslint-rules)
- [Disable ESLint Rules](#disable-eslint-rules)
- [Code Style Alternatives](#code-style-alternatives)


### ESLint

> A linter like [ESLint](http://eslint.org/) helps you to maintain a consistent JavaScript and React code style in your project. Let’s get started by installing the [eslint](https://github.com/eslint/eslint) node package.

[ESLint](http://eslint.org/) 有助于你在你的项目中让 JavaScript 和 React 的代码风格保持一致。首先，我们需要通过 npm 来安装 [eslint](https://github.com/eslint/eslint)。

> *From root folder:*

*在根文件夹下执行：*

```css
npm --save-dev install eslint
```
> Since the project uses Webpack, you have to tell Webpack that you want to use eslint in your build. Therefore you can install [eslint-loader](https://github.com/MoOx/eslint-loader).

由于项目中使用了 Webpack，你必须在构建中告诉 Webpack 要使用 eslint。因此，你需要安装 [eslint-loader](https://github.com/MoOx/eslint-loader)。

> *From root folder:*

*在根文件夹下执行：*

```css
npm --save-dev install eslint-loader
```

> Now you can use the loader in your Webpack configuration.

现在你可以在你的 webpack 配置中使用 eslint-loader 了。

*webpack.config.js*

```javascript
...
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'react-hot-loader!babel-loader'
    },
    {
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'eslint-loader'
    }
  ]
},
...
```

> When you start your application now, it will output an error; ```No ESLint configuration found```. You need such a file to define your configuration:

现在你运行你的应用，它会输出一个错误：```No ESLint configuration found```。你需要新建一个文件来定义你的配置：

> *From root folder:*

*在根文件夹下执行：*

```css
touch .eslintrc
```

*.eslintrc*

```javascript
{
  "rules": {
  }
}
```

> Later on you can specify rules in the file. But first try to start your app again.

稍后，你可以在这个文件中指定自己规则。但是，请再次运行一下你的应用。

> *From root folder:*

*在根文件夹下执行：*

```css
npm start
```

> You might run into Parsing error: ```The keyword 'import' is reserved```, which happens because ESLint does not know about ES6 features yet, which you might have enabled via Babel. The ```import``` statement is an ES6 feature. Let’s install Babel support that ESLint can interpret the JavaScript code.

你或许会遇到这种解析错误：```The keyword 'import' is reserved```，这是因为，如果你已经通过 Babel 开启了 ES6，ESLint 还不支持 ES6 功能。```import``` 是 ES6 的功能。让我们安装 babel-loader 以便 ESLint 解析 JavaScript 代码。

### ESLint + Babel

> You might have already installed the [babel-loader](https://github.com/babel/babel-loader) to transpile your code with Webpack. Otherwise you can do it by using npm.

你或许已经安装了 [babel-loader](https://github.com/babel/babel-loader) 让 Webpack 编译你的代码。否则，你可以用 npm 来安装。

```css
npm install --save-dev babel-loader
```

> Now you can use that loader and pair it with the eslint-loader.

现在，eslint-loader 和 babel-loader 可以配合一起使用 。

*webpack.config.js*

```javascript
...
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'react-hot-loader!babel-loader'
    },
    {
      test: /\.js$/,
      exclude: /node_modules/,
    }
    loaders: ['babel-loader', 'eslint-loader']
  ]
},
...
```

> An alternative would be to use Webpacks preLoaders.

一种替代方案：也可以使用 Webpacks preLoaders。

*webpack.config.js*

```javascript
...
module: {
  preLoaders: [
    {
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'eslint-loader'
    },
  ],
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'react-hot-loader!babel-loader'
    }
  ]
},
...
```

> Additionally you have to use [babel-eslint](https://github.com/babel/babel-eslint) in your configuration to lint all valid ES6 code.

另外，你还需要用 [babel-eslint](https://github.com/babel/babel-eslint) 来检验 ES6 代码。

> *From root folder:*

*在根文件夹下执行：*

```css
npm install --save-dev babel-eslint
```

*.eslintrc*

```javascript
{
  parser: "babel-eslint",
  "rules": {
  }
}
```

> You should be able to start your app. There are no errors displayed, because you didn’t specify rules yet.

现在运行你的应用。不会有任何的错误提示，这是因为你还没有配置任何的规则。

### ESLint Rules

> ESLint rules apply for a lot of different code style use cases. You can check the [list of available ESLint rules](http://eslint.org/docs/rules/). Let’s add your first rule.

ESLint 有很多不同的代码风格用例。你可以查看 [ESLint 可用规则列表](http://eslint.org/docs/rules/)。让我们添加第一条规则：

*.eslintrc*

```javascript
...
"rules": {
  "max-len": [1, 70, 2, {ignoreComments: true}]
}
...
```

> The rule checks the length of the lines of code. When the length is more than 70 characters, you will get an error.

这条规则将会检测代码的长度。如果长度大于 70 个字符，你会得到一个错误提示。

> *From root folder:*

*在根文件夹下执行：*

```css
npm start
```

> You might see some errors regarding your lines of code length, because some of your lines are longer than 70 characters. You can adjust the rule to allow some more characters.

你会看到一些有关代码长度的错误，因为你的代码中某些行超过 70 个字符。你可以调整规则允许更多的字符。

*.eslintrc*

```javascript
...
"rules": {
  "max-len": [1, 120, 2, {ignoreComments: true}]
}
...
```

> If you still see errors, it is your first chance to fix them in your codebase.

如果，你还是会看到错误，这时需要你在代码中去修复。

### ESLint Rules for React

> Let’s add some code style checking for React. Therefore you need to add the [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react).

让我们为 React 添加一些代码规范。你需要安装 [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)。

> *From root folder:*

*在根文件夹下执行：*

```css
npm --save-dev install eslint-plugin-react
```

> Now you can use the react plugin and specify your first React rule, which says that you have to specify ```PropTypes``` for your React components.

现在你可以用 React 插件设置第一条 React 代码规则，这条规则说明你必须为你的 React 组件指定 ```PropTypes```。

*.eslintrc*

```javascript
{
  parser: "babel-eslint",
  "plugins": [
    "react"
  ],
  "rules": {
    "max-len": [1, 120, 2, {ignoreComments: true}],
    "prop-types": [2]
  }
}
```

> When you start your app, you might see: ```Definition for rule 'prop-types' was not found```. You might want to fix them by defining ```PropTypes``` for your React components.

当你运行你的应用的时候，你应该会看到：```Definition for rule 'prop-types' was not found```。你可以为你的 React 组件定义```PropTypes``` 来修复这个错误。

> These were your first custom JavaScript and React rules. But you can use common recommendations from the community. Such presets, like they [exist for React](https://github.com/yannickcr/eslint-plugin-react#user-content-recommended-configuration), give you a set of rules that everyone uses.

这些是你第一个自定义的 JavaScript 和 React 规则。但是，你可以使用社区推荐的公共的规则。像 [React 的预设](https://github.com/yannickcr/eslint-plugin-react#user-content-recommended-configuration) 一样，这样的预设给了你一套大家共用的规则。

```javascript
{
  parser: "babel-eslint",
  "plugins": [
    "react"
  ],
  "rules": {
    "max-len": [1, 120, 2, {ignoreComments: true}],
    "prop-types": [2]
  },
  "extends": ["eslint:recommended", "plugin:react/recommended"]
}
```

> Let’s go one step further with extending the rules by another set of rules.

让我们更近一步，用另外一套规则扩展功能。

### Extend ESLint Rules

> Since you don’t want to specify your own set of rules every time, there are plenty of recommendations out there. You already used one for React. Another one is the [Airbnb Style Guide](https://github.com/airbnb/javascript). Airbnb open sourced its own [ESLint configuration](https://www.npmjs.com/package/eslint-config-airbnb) that everyone can use it in their ESLint configuration.

如果你不想每次都配置自己的规则，这里也有很多的推荐配置。你已经用到了 React 的推荐配置。另一个是[爱彼迎代码规范](https://github.com/airbnb/javascript)。爱彼迎开源了他们自己的 [ESLint 配置](https://www.npmjs.com/package/eslint-config-airbnb)，每个人都可以使用他们的配置。

> You have to install the required packages.

你需要安装依赖包。

> *From root folder:*

*在根文件夹下执行：*

```css
npm --save-dev install eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y
```

> Now you can add Airbnbs’ ESLint configuration to your ESLint configuration. When you have a look at the installed node packages, you can see that the configuration includes JSX and React rules.

现在，你可以在自己的 ESLint 配置中添加爱彼迎的配置了。当你查看已经安装的包时，你会发现配置中还包括了 JSX 和 React 的规则。

*.eslintrc*

```javascript
{
  parser: "babel-eslint",
  "rules": {
    "max-len": [1, 120, 2, {ignoreComments: true}],
    "prop-types": [2]
  },
  "extends": ["airbnb-base"]
}
```

> You can see that it is very simple to extend the ESLint rules from someone else. We could use other extensions as well, but at this time the Airbnb Code Style and the according ESLint configuration are very popular and well accepted by developers.

你可以看到通过别人配置来扩展 ESLint 规则非常简单。我们也可以使用其它的扩展，但是，现在爱彼迎代码规范是非常流行的 ESLint 配置，越来越被开发者接受。

### Disable ESLint Rules

### 禁用 ESLint 规则

> Now you are prepared to fix all the ESLint code style violations in your code. Start your application to see all the ESLint errors.

现在，你已经准备好修复所有的代码规范问题。启动你的应用看看所有的 ESLint 错误。

> *From root folder:*

*在根文件夹下执行：*

```css
npm start
```

> You might see a lot of errors in your terminal. Additionally they will appear in the developer console when you navigate to your app in the browser. Now you can begin to fix the errors. Whenever you are unsure about the error, google it and evaluate whether you want to have this rule in your code. You can either fix the error in the mentioned file or disable the rule, when you think you don’t need it.

在你的终端你应该看到了许多错误。另外，如果你在浏览器中访问你的应用错误也会在开发者工具控制台出现。现在，你可以开始去修复这些错误。如果，你不确定某个错误，请 google 并重新考虑在你的代码中是否启用。你可以修复错误，或者在你确定不需要这条规则时候禁用它。

> You can disable a rule globally:

你可以全局禁用某个规则：

*.eslintrc*

```javascript
{
  parser: "babel-eslint",
  "extends": "airbnb",
  "rules": {
    "no-unused-vars": 0,
    "max-len": [1, 120, 2, {ignoreComments: true}],
    "prop-types": [2]
  }
}
```

> And you can disable a rule in a local file:

你也可以在本地文件中禁用某个规则：

```javascript
/*eslint-disable no-unused-vars*/
...some code...
/*eslint-enable no-unused-vars*/
```

### Code Style Alternatives

### 代码风格的替代方案

> There are a handful of useful **style guides** in the JavaScript and React community. It makes sense to read them even though you don’t enforce every aspect in your set of rules in ESLint. As a JavaScript developer, I can highly recommend to read them though.

在 JavaScript 和 React的社区有一些非常有用的**代码规范**。尽管你不需要强制执行所有的 ESLint 规则，也可以阅读他们。作为一个 JavaScript 开发者，我强烈推荐阅读这些规范。

- [Airbnb JavaScript](https://github.com/airbnb/javascript)
- [Airbnb React](https://github.com/airbnb/javascript/tree/master/react)
- [Idiomatic JavaScript](https://github.com/rwaldron/idiomatic.js/)

> Another alternative are **code formatters**. In the JavaScript ecosystem there are two mainly used solutions that enforce an opinionated code style. These solutions will reformat your code on demand. You don’t have to fix the code style violations yourself and you don’t have to set up any rules. There is a default set of rules.

另外一种替代方案是**代码格式化工具**。在 JavaScript 的生态系统中有两种主要的解决方案来强化代码风格。这些方案将会在你需要的情况下重新格式化你的代码。你不需要修复任务代码风格问题，也不需要设置任何的规则。他们有一套默认的规则。

> [StandardJs](https://github.com/feross/standard) is one of these code formatters that is widely known and used in the JavaScript community. Yet recently there was another solution released with the name [Prettier](http://jlongster.com/A-Prettier-Formatter).

[StandardJs](https://github.com/feross/standard) 是众所周知的代码格式化工具之一，JavaScript 社区已经在使用了。是的，最近另外一个解决方案 [Prettier](http://jlongster.com/A-Prettier-Formatter) 也已经发布了。

> Both solutions can be installed globally or locally via npm. Afterward you can include these in your projects and reformat the code on demand. You can go one step further and install an editor or IDE plugin for StandardJS or Prettier. In my case, using Sublime as my editor of choice, I installed both plugins to experiment with both approaches. It is convenient to have an automatic code formatter. Finally you can configure your editor to reformat your code style, based on StandardJS or Prettier, whenever you save your file.

两种解决方案都可以通过 npm 安装在全局或者本地。然后，你可以在项目中引入它们，并且在需要的时候重新格式化你的代码。你还可以使用另外一种方法，为 IDE 安装 StandardJS 或者 Prettier 插件。比如，我用 Sublime 编辑器，我安装两个插件来验证两个方法。有一个代码自动格式化工具非常方便。最后，你可以基于 StandardJS 或者 Prettier 配置你的编辑器，每当你保存了代码，都会自动重新格式化你的代码。

> The article taught you how to set up ESLint in a React, Babel and Webpack environment. You can now setup custom rules or extend your rules from well maintained open source projects. Additionally, you know about the common style guides to have a better understanding of how to write clean JavaScript and React code. In closing, I recommend you look for ESLint integrations for your editor, so your editor can warn you in real time whenever you violate an ESLint rule.

这篇文章教你如何在 React、Babel 和 Webpack 环境中配置 ESLint。现在，你可以自定义规则或者从好的开源项目中扩展自己的规则。此外，你也可以了解通用的代码风格指南，以便了解如何写出更简洁的 JavaScript 和 React 代码。最后，我建议为你的编辑器集成 ESLint，这样编辑器就可以实时的通知你违反了那些规则。

[Read more: The SoundCloud Client in React + Redux](https://www.robinwieruch.de/the-soundcloud-client-in-react-redux/)

[更多阅读：React + Redux 客户端 SoundCloud](https://www.robinwieruch.de/the-soundcloud-client-in-react-redux/)

[Read more: Tips to learn React + Redux](https://www.robinwieruch.de/tips-to-learn-react-redux/)

[更多阅读：React + Redux 学习建议](https://www.robinwieruch.de/tips-to-learn-react-redux/)
