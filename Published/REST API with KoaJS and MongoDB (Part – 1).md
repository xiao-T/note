## REST API with KoaJS and MongoDB (Part – 1)
## 用 KoaJS 和 MongoDB 实现 REST API - 1

> 原文地址: <http://polyglot.ninja/koajs-mongodb-rest-api/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

We have previously written about [REST API Development](http://polyglot.ninja/rest-apis-concepts-applications/) in length including tutorials using the [Flask Framework](http://polyglot.ninja/rest-api-best-practices-python-flask-tutorial/) and of course a multi part tutorial series using the [Django REST Framework](http://polyglot.ninja/django-building-rest-apis/). In this tutorial series, we are going to focus more on JavaScript and see how we can build a very simple REST API using the KoaJS framework for NodeJS. We’re also going to use MongoDB as our database.   

之前，我已经写过如何用 [Flask](http://polyglot.ninja/rest-api-best-practices-python-flask-tutorial/) 框架实现 [REST API](http://polyglot.ninja/rest-apis-concepts-applications/) 开发的教程，当然很多技巧来自 [Django REST 框架](http://polyglot.ninja/django-building-rest-apis/)。在这个系列的教程中，我们更多关注是 JavaScript，如何用 KoaJS 实现简单的 REST API。我们也会用到 MongoDB 数据库。

### Learning JavaScript

Before we can proceed, we should make sure that we’re proficient using JavaScript. We wouldn’t need any extra ordinary JS mastery but we need to have a certain level of command over JavaScript. If you are new to JavaScript and looking for some resources to learn the language, we have you covered. Please do checkout our [JavaScript Learning Guide](http://polyglot.ninja/learning-javascript/).

在我们继续之前，我们应该保证我们已经精通 JavaScript。我们不需要 JS 额外的知识，但是我们需要你有一定的 JavaScript 经验。如果，你是一个 JavaScript 新手并且要学习这门语言，我们可以给你推荐。请查看我们的 [JavaScript 学习指南](http://polyglot.ninja/learning-javascript/)。

### NodeJS and KoaJS

NodeJS has made JavaScript popular outside the browser world. There was a time, JS was meant to be used only on the browser side. For anything else, we would be using other languages like Java, Python, PHP etc. But then NodeJS came and proved that JavaScript is an equally viable language. With the very rapid growth of the platform, NodeJS today is being used for many types of programming and software development. From web backend and REST APIs to IoT tools and Desktop applications – NodeJS is literally everywhere.

NodeJS 让 JavaScript 在非浏览器环境下也变得非常流行。曾经，JS 只能运行在浏览器中。其他的事情，我们不得不用其他的语言，比如：Java、Python、PHP 等。现在，NodeJS 的出现并证明了 JavaScript 也可以做同样的事情。随着平台的快速增长，今天，在很多程序和软件开发中用到 NodeJS。从 web 后端和 REST APIs 到物联网工具和桌面应用 - NodeJS 无处不在。

There are many free and open source frameworks available for NodeJS but Express is perhaps the most popular option. Koa is quite like Express but it’s light weight, flexible and very simple. I personally am a big fan of this framework. At work, I have built multiple services on top of KoaJS and have been very happy with the outcome. This is why I chose Koa.

有很多免费开源的 NodeJS 框架，Express 是最流行的。Koa 和 Express 非常相似，但是它更轻量、易扩展和更简单。个人非常喜欢这个框架。工作中，我在 KoaJS 的基础上构建了很多的服务并且结果非常满意。这就是为什么我选择 Koa。

### MongoDB

MongoDB has become very popular as a NoSQL database in the recent times. MongoDB is not like the traditional relational databases we’re so used to. Rather it’s a document oriented database where the documents look just like JSON. If you’re new to MongoDB, don’t worry, they have a brilliant [University](https://university.mongodb.com/) where you can take your courses free and master the database system. I have personally taken their courses and I would happily recommend the courses. They do cover some of the popular languages on the web. You will be learning to use MongoDB with Python / Java / NodeJS. Once you learn the concepts well, you should be able to transfer the knowledge to any other programming languages / platforms – so no worries if your favourite language is not available yet.

最近，MongoDB 作为一个 NoSQL 数据库变得越来越流行。MongoDB 不像我们用过的传统关系型数据库。然而，它是一个文档型数据库，看起来就像 JSON。如果，你是一个新手，别担心，它们有[在线学习平台](https://university.mongodb.com/) ,你可以免费学习并掌握数据库知识。我亲自学习过它们的课程，也很乐意把这些课程推荐给你。课程覆盖了网络上许多流行的语言。你将会学习如何在 Python / Java / NodeJS 使用 MongoDB。一旦，你学会了这些概念，会很容易将这些知识转换到其他任何的语言和平台上。所以，不用担心你最熟悉的语言不可用。

### Getting Started

Before we can get started, make sure you have the latest NodeJS and npm installed. We would be using some of the modern JS syntax (ie. async / await), so please make sure you’re on the latest version of NodeJS (at the time of this writing, that would be Node 8). Please also make sure MongoDB is installed as well. On Windows you download the setup files and install them manually. On OS X, you can use a package manager like Homebrew. On Linux distros, you should be able to get them installed using the package manager available on your OS.

在我们开始之前，请确保已经安装过最新的 NodeJS 和 npm。我们将会用到一些新的 JS 语法（比如：async/await），所以，请确保你已经安装了最新的 NodeJS（在写这篇文章的时候，最新的版本是 Node 8）。也请确认 MongoDB 已经安装好。Windows 用户需要下载安装文件并手动安装。OS X 用户可以用包管理工具，比如：Homebrew。Linux 用户使用系统中的包管理工具应该也可以安装。

#### Creating The Project

Let’s first create a directory named “koa-example”.

首先，让我们先创建一个目录：koa-example。

```sh
mkdir koa-example
cd koa-example
```

Now we’re going to initialize the npm project.

现在，我们可以初始化 npm。

```sh
npm init
```

The prompt will ask you a few questions. Once you complete answering them, we’ll be ready to start installing 3rd party dependencies.

会有一些提示问你一些问题。一旦，你完成问答，我们就可以安装第三方依赖包了。

#### Install KoaJS

Now we’re going to install KoaJS in our new project.

现在，在我们新项目安装 KoaJS。

```sh
npm i -S koa
```

This should install KoaJS and save it as a project dependency in the ```package.json``` file.

这应该会安装 koaJS，并把它保存在项目中 ```package.json``` 文件的依赖属性中。

### A little more about Koa

Please note that Koa is being developed and maintained by the same people who work on Express. Koa is a much light weight version of Express except it follows a different path. Koa uses the modern JS features like async / await to make the code readable and maintainable. Although, in being light weight, Koa had to leave out many of the built in features in Express. Throughout our tutorial(s), we shall be installing third party packages, often to avail the same features which is prevalent in Express and other frameworks. Koa gives us a really minimal core and a very narrow, focused set of functionality. It lets us choose the components we need. That is in a way liberating and very flexible regardless of how inconvenient that might sound at the beginning.

请注意 Koa 的开发和维护是和 Express 同样的团队。除了遵循不同的规则，Koa 相比 Express 是一个更轻量的版本。Koa 使用 JS 新的功能语法确保代码更容易阅读和维护，比如：async/await。因为变的轻量，Koa 不得不舍弃了很多功能。在我们的教程中，我们将要安装第三方包，来实现 Express 或者其他框架已经流行的功能。Koa 为我们提供一个真正迷你、只关注功能的核心框架。让我们自己选择自己需要的组件。这种方式非常的灵活，尽管一开始并不是那么方便。

One of the common Koa middleware I always install is ```koa-router``` which enables express like convenient routing on Koa.

```koa-router``` 是 Koa 公共中间件中的其中一个，它可以让 Koa 实现 Express 中的路由功能。

```
npm i -S koa-router
```

### Hello World!

Let’s start writing some codes, we shall of course start with the hello world example. We will first see the codes and then go through them.

让我么开始写一些代码，我们首选的列子是 hello world。我们先看看这些代码。

```js
const Koa = require("koa");
const Router = require("koa-router");

const app = new Koa();
const router = new Router();

router.get("/", async function (ctx) {
    ctx.body = {message: "Hello World!"}
});

app.use(router.routes()).use(router.allowedMethods());

app.listen(3000);
```

The code is pretty straightforward. We are importing Koa and Router and creating new instances of them. We are then adding a new route / view to the router. Please note the usage of ```async``` functions. We mentioned earlier that Koa uses the latest JS features, notably the ```async``` and ```await``` features. If you are not familiar with the syntax, here’s a very [in depth article](https://ponyfoo.com/articles/understanding-javascript-async-await) that covers from the basics of promises, usages of generators and finally async and await. It would be a good read since Koa used to use generators to achieve similar results before async / await landed on Node.

这段代码非常的直截了当。我们引入的了 Koa 和 Router，并创建了不同的实例。然后，我们添加了一个新的路由```/```，并为路由提供了视图。请注意 ```async``` 的用法。我们之前提到过 Koa 会用到 JS 最新的功能，特别是 ```async``` 和 ```await```。如果，不熟悉这些语法，这有一篇[非常深入](https://ponyfoo.com/articles/understanding-javascript-async-await)的文章，包含了 Promise 的基础知识、generators 的用法和 async、await。这是一篇很好的文章，在 async / await 之前 Koa 曾经使用 generators 实现了同样的功能。

The async function takes a parameter called ```ctx``` which is short for context. We can just set a body to the context and it will be rendered as response. If we pass certain types, for example a dictionary / object, it will be sent as JSON format. We can also access the ```request``` object using the context.

async 方法有一个参数 ```ctx```，它是上下文的简称。我们可以只设置上下文中的 body，它将会作为响应返回然后被渲染。如果我们传递某些类型，比如：字典/对象，它将会被 JSON 格式化然后传递。我们也可以通过上下文访问 ```request``` 对象。

After the function is defined, we have used the routes middleware and launched the app on port 3000. If we browse the url ```http://localhost:3000``` we should see the response we set.

方法定义好之后，我们可以使用路由中间件在端口3000启动 App。用浏览器访问 ```http://localhost:3000``` 应该就可以看到我们设置的内容。

### Query Strings in KoaJS

Now that we have seen a basic hello world example, let’s see how we can access query strings in KoaJS. Query strings are appended to the url in the form – ```/hello?name=masnun``` – here the part starting from the question mark is the query string. We separate different parameters using ```&``` in the query string part. For example: ```name=masnun&country=bangladesh```.

现在，我们已经看到一个基本的 hello world 示例，让我们看看，在 KoaJS 中如何访问查询字符串。查询字符串追加在表单中的 url 后面 – ```/hello?name=masnun``` – 这部分以问号(?)开始的就是查询字符串。我们用 ```&``` 分割字符串中不同的参数。例如：```name=masnun&country=bangladesh```。

To access a parameter passed in the query string, we can use the ```ctx.request.query``` object to access the parsed querystring as JS object (dictionary). Let’s change our function to access the ```name``` from the query string and display a custom greeting.

访问查询字符串中的参数，我们可以用 ```ctx.request.query``` 对象访问格式化后的查询字符串，就像访问 JS 对象（字典）。让我们修改我们的方法，通过访问字符串中的  ```name``` 显示自定义的欢迎语。

```js
router.get("/", async function (ctx) {
    let name = ctx.request.query.name || "World";
    ctx.body = {message: `Hello ${name}!`}
});
```

Now visit – ```http://localhost:3000/?name=masnun``` and see how it works!

现在，访问 ```http://localhost:3000/?name=masnun``` 看看它是如何工作的！

### Accepting JSON

We have by now got used to the idea that we need to plug in 3rd party middlewares to achieve common tasks. One of the common thing in building REST APIs would be to accept JSON payloads. And like most other things, we need to use an external package to handle incoming JSON requests. In this case, we will use the ```koa-bodyparser``` package.

我们现在已经习惯了使用第三方中间件实现常见功能。其中一件常见功能就是实现 REST APIs。和大多事情一样，我们需要一个扩展包来处理传入的 JSON 请求。在这个示例中，我们将用到 ```koa-bodyparser```。

```
npm i -S koa-bodyparser
```
Now let’s modify the previous code to accept a POST request and parse incoming JSON so we can take the value for `name` variable from the JSON data. Here’s our modified code, first take a look and then we will discuss what we did different here:

现在让我们修改上面的代码以便接受一个 POST 请求并解析传入的 JSON，这样我们就可以从 JSON 数据中获取 `name` 变量的值。这是修改后的代码，先看一看代码，然后我们会讨论有什么不同：

```js
const Koa = require("koa");
const Router = require("koa-router");
// 新添加的代码 
const BodyParser = require("koa-bodyparser");

const app = new Koa();
const router = new Router();

// Use the bodyparser middlware 
// 新添加的代码
app.use(BodyParser());

// 修改过的代码
router.post("/", async function (ctx) {
    let name = ctx.request.body.name || "World";
    ctx.body = {message: `Hello ${name}!`}
});

app.use(router.routes()).use(router.allowedMethods());

app.listen(3000);
```

The highlighted lines have been changed in this version. First we required the body parser package. Next we used it as a middleware. And finally, we accessed the value of ```name``` from the ```ctx.request.body``` object. Note, for query string it was ```request.query``` and now it’s ```request.body```. The body parser parses the body of the request and sets the value there.

高亮的部分是已经修改过的。首先我们引入了 ```koa-bodyparser```。接下来把它作为中间件使用。最终，就可以通过对象 ```ctx.request.body``` 访问 ```name``` 的值。注意，以前查询字符串通过 ```request.query``` 访问，现在是通过 ```request.body``` 访问。```koa-bodyparser``` 会解析请求体并设置对应的值。   
(*译者注：没有高亮，根据自己理解，在有修改的地方添加了注释*)

We can try our new code using the following request:

我们可以用以下的代码发起请求测试新代码：

```
curl -X POST \
  http://localhost:3000/ \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{"name": "masnun"}'
```

(The cURL command line was generated by Postman, feel free to use your favorite GUI tool for testing the APIs)

(curl 命令行是通过 Postman 生成的，根据自己喜好可以使用任何的 GUI 工具测试 APIs)

### Logging Requests on Console

Our current app doesn’t list the requests it serves on the console. If we could get a nice log of what requests are being accepted and handled, it would help us with the workflow. We will be using the    ```koa-logger``` package for just that.

当前我们的应用没有在控制台打印请求。如果我们可以在接受和处理请求时候得到一条完整的日志，这对我们的工作是很有帮助的。我们将用 ```koa-logger``` 来完成这项工作。

```
npm i -S koa-logger@2
```

Now we can use it in our app:

现在我们可以在应用中用它：

```js
const logger = require('koa-logger');
app.use(logger());
```

Now launch the app and make a few requests, you will notice nicely formatted output on your terminal.

现在，启动应用并发送一些请求，你将会看到控制台输出了非常友好的日志。

### Use Nodemon for Auto Reload

During development, it can be a tedious task to restart the app from time to time whenever you make some new changes. Nodemon is one of the tools which offer help with that. Install it globally and then just use ```nodemon .``` inside your current working directory. It will monitor file changes and restart the app as needed.

开发期间，每当有新的改动频繁的重启应用是非常乏味的一件事。Nodemon 对此可以提供帮助。在全局安装，然后，在你工作目录使用 ```nodemon .```。它将会监控文件修改并自动重启应用。

Learn more about Nodemon here – <https://nodemon.io/> (along with installation instructions and use cases).

有关 Nodemon 更多知识在这 - <https://nodemon.io/>（包含安装指引和用例）

### Up Next!

In this tutorial, we just got started using KoaJS and Koa Router package to build our views. In the next tutorial we shall resume our journey forward, learning to use MongoDB as our database.

在这篇教程中，我们仅仅学会了如何使用 KoaJS 和 Koa Router 构建视图。在接下来的教程中我们会继续前行，学习如何使用 MongoDB。



