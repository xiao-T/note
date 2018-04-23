## REST API with KoaJS and MongoDB (Part – 2)
## 用 KoaJS 和 MongoDB 实现 REST API - 2

> 原文地址: <http://polyglot.ninja/rest-api-koajs-and-mongodb-part-2/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In our last post about [REST API with KoaJS and MongoDB](http://polyglot.ninja/koajs-mongodb-rest-api/), we got started with KoaJS and learned to create simple views. We also saw how we can access the query strings and incoming JSON payloads. In this tutorial, we are going to go ahead and implement the RESTful routes and CRUD operations with MongoDB.

在[上一篇](http://polyglot.ninja/koajs-mongodb-rest-api/)文章中，我已经学习了 KoaJS 并学会了如何创建简单的视图。我们也看到了如何访问查询字符串和传入的 JSON。在这篇教程中，我们继续前行，实现 RESTful 路由和 MongoDB 的 CRUD 操作。

In case you’re new to REST API development, you might also want to check out the [REST API Concepts](http://polyglot.ninja/rest-apis-concepts-applications/) and our REST API Tutorials with [Flask](http://polyglot.ninja/rest-api-best-practices-python-flask-tutorial/) and [Django REST Framework](http://polyglot.ninja/django-building-rest-apis/).

如果，你是 REST API 的新手，你应该先了解一下 [REST API 概念](http://polyglot.ninja/rest-apis-concepts-applications/)，我们也有一些用 [Flask](http://polyglot.ninja/rest-api-best-practices-python-flask-tutorial/) 和 [Django REST 框架](http://polyglot.ninja/django-building-rest-apis/) 实现 REST API 的教程。

### Installing MongoDB and NodeJS Driver

I am assuming you have installed MongoDB already on your system. You can install MongoDB using their official installer on Windows or Homebrew on OS X. On Linux systems, you can use the package manager that ships with your distro.

假设你已经安装好 MongoDB。Windows 用户可以使用官方安装器，OS X 用户使用 Homebrew。Linux 用户，可以使用系统内置的包管理工具。

If you don’t have MongoDB installed locally, don’t worry, you can also use a free third party mongo hosting service like [mLab](https://mlab.com/).

如果，你本地没有安装 MongoDB，别担心，你也可以使用第三方的 mongo 主机服务，比如：[mLab](https://mlab.com/)。

Once we have MongoDB setup and available, we’re going to install the NodeJS driver for MongoDB next.

一旦 MongoDB 成功安装，我们接下来就需要安装 MongoDB NodeJS 驱动。

```
npm i -S mongodb
```

### Connecting to MongoDB

We will create a separate file named ```mongo.js``` and put the following codes in it:

我们用以下代码单独创建一个文件命名为 ```mongo.js```。

```js
const MongoClient = require('mongodb').MongoClient;
const MONGO_URL = "mongodb://localhost:27017/polyglot_ninja";


module.exports = function (app) {
    MongoClient.connect(MONGO_URL)
        .then((connection) => {
            app.people = connection.collection("people");
            console.log("Database connection established")
        })
        .catch((err) => console.error(err))

};
```

Our module exports just one function which takes the ```app``` object as the only parameter. Once called, the function connects to our mongodb instance and once connected, sets the ```people``` property on our app instance. The ```people``` property would actually be a reference to the people collection on our database. So whenever we will have access to the app instance, we will be just using the ```app.people``` property to access the collection from within our app. If the connection fails, we will have the error message printed on our terminal.

我们导出一个仅接受一个 ```app``` 对象作为参数的模块。一旦执行，函数将会连接 mongodb 实例，然后设置 app 的 ```people``` 属性。```people``` 实际上就是数据库集合 ```people``` 的引用。因此，任何时候我们访问应用实例，我们只需 ```app.people``` 就可以访问到数据库中的集合。如果连接失败，我们会在终端输出错误信息。

We have used promises instead of callback. Which makes the code a bit cleaner. Now in our ```index.js``` file, we will call the exported function like this:

我们用 Promies 代替了回调。这让代码更加清晰。现在在我们的 ```index.js``` 文件中，我们会调用导出的方法，就像这样：

```js
require("./mongo")(app);
```

That should import the function and invoke it. Assuming everything worked fine, you should see the message saying database connection established when you run the app next time.

以上代码会引入方法并执行。假设没有任何异常，当你下次启动应用的时候，你应该会看到一条信息：Database connection established

**Please Note:** We didn’t create the mongodb database or the collection ourselves. MongoDB is smart enough to figure out that we used the names of non existing database / collection and create them for us. If anything with that name exists already, just uses them.

**请注意：** 我们没有创建数据库集合。MongoDB 足够聪明，它会找出那些我们用到但不存在的数据库、集合并自动创建它们。如果，已经存在的数据库或者集合，并不会自动创建。

### Inserting Records Manually

Before we can start writing our actual code, let’s connect to our mongo database and insert some entries manually so we can play with those data. You can use the command line tool or a mongodb GUI to do so. I will use the command line tool.

在我们开始写代码前，我们先连接数据库，并手动插入一些记录，这样我们就可以使用这些数据了。你可以使用命令行工具或者 GUI 工具做这些。我将会使用命令行工具。

```sh
$ mongo
MongoDB shell version: 3.2.7
connecting to: test
> use polyglot_ninja
switched to db polyglot_ninja
> db
polyglot_ninja
> db.people.insert({"name": "masnun", "email": "masnun@gmail.com"})
WriteResult({ "nInserted" : 1 })
> db.people.find()
{ "_id" : ObjectId("597ef404b5256ba58d26ac53"), "name" : "masnun", "email" : "masnun@gmail.com" }
>
```

I inserted a document with my name and email address in the ```people``` collection of the ```polyglot_ninja``` db.

我在数据库 ```polyglot_ninja``` 中 ```people``` 的集合中插入了一条记录包括 ```name``` 和 ```email```。

### Implementing The Routes

Now we will go ahead and implement the routes needed for our REST API.

现在我们来实现 REST API 中需要的路由。

**Please note:** Too keep the actual code short, we will skip – validation, error handling and sending proper http status codes. But these are very important in real life and must be dealt with proper care. I repeat – these things are skipped intentionally in this tutorial but should never be skipped in a production app.

**请注意：** 为了尽量精简代码，我们将会跳过 - 验证、错误处理和发送正确的状态码。但是在实际开发中这是很重要的事情需要妥善处理。我重复一次 - 教程中我们有意的跳过了这些步骤，但是实际开发中绝对不要跳过。

#### GET /people (List All)

This is going to be our root element for the people api. When someone makes a ```GET``` request to ```/people```, we should send them a list of documents we have. Let’s do that.

这是我们根目录下的 ```people``` 接口。当有人发起 ```GET``` 请求，我们应该向他们发送所有的文档清单。让我们处理这个。

```js
// List all people
router.get("/people", async (ctx) => {
    ctx.body = await ctx.app.people.find().toArray();
});
```

Now if we run our app and visit the url, we shall see the document we created manually listed there.

现在，如果我们启动应用并访问这个链接，我们应该会看到之前手动创建的记录。

#### POST /people (Create New)

Since we already have the body parser middleware installed, we can now easily accept JSON requests. We will assume that the user sends us properly valid data (in real life you must validate and sanitize) and we will directly insert the incoming JSON into our mongo collection.

一旦我们安装好 ```koa-bodyparser``` 中间件，我们就可以很容易的接收处理 JSON 请求。假设，有用户发送了正确有效的数据（在实际应用中你必须校验），我们将会直接把 JSON 插入到我们的 mongo 集合中。

```js
// Create new person
router.post("/people", async (ctx) => {
    ctx.body = await ctx.app.people.insert(ctx.request.body);
});
```

You can POST JSON to the ```/people``` endpoint to try it.

你可以尝试 POST 数据到 ```/people```。

```js
curl -X POST \
  http://localhost:3000/people \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{"name": "genji", "email": "genji.shimada@polyglot.ninja"}'
```

Now go back to the all people list and see if your new requests are appearing there. If everything worked, they should be there 🙂

现在，我们回去看看列表，如果有新的请求发送并且无异常，你将会看到那些新添加的数据。

#### GET /people/:id (Get One)

To query by mongo IDs we can’t just use the string representation of the ID but we need to convert it to an ObjectID object first. So we will import ```ObjectID``` in our ```index.js``` file first:

在 MongoDB 中通过 ID 查询，我不能直接使用 ID 的字符串字面量，我们首先需要把 ID 转化成一个 ObjectID 对象。因此，我们将会在我们 ```index.js``` 文件中引入 ```ObjectID```：

```js
const ObjectID = require("mongodb").ObjectID;
```

The rest of the code will be simple and straightforward:

接下来的代码将会简单明了：

```js
// Get one
router.get("/people/:id", async (ctx) => {
    ctx.body = await ctx.app.people.findOne({"_id": ObjectID(ctx.params.id)});
});
```

#### PUT /people/:id (Update One)

We usually use ```PUT``` when we want to replace the entire document. For single field updates, we prefer ```PATCH```. In the following code example, we have used ```PUT``` but the code is also valid for a ```PATCH``` request since mongo’s ```updateOne``` can update as many fields as you wish. It can update just one field or the entire document. So it would work for both PUT and PATCH methods.

当我们需要替换文档中数据的时候通常我们会用 ```PUT```。对于单文件，我们更喜欢用 ```PATCH```。在接下来的示例代码中，我们用到了 ```PUT```，但是代码对于 ```PATCH``` 请求也是有效的，因为 mongo 的 ```updateOne``` 可以更新任意多的文档。它可以更新一个或者整个文档。因此 ```PUT``` 和 ```PATCH``` 都是有效的。

Here’s the code:

代码在这：

```js
// Update one
router.put("/people/:id", async (ctx) => {
    let documentQuery = {"_id": ObjectID(ctx.params.id)}; // Used to find the document
    let valuesToUpdate = ctx.request.body;
    ctx.body = await ctx.app.people.updateOne(documentQuery, valuesToUpdate);
});
```

The ```updateOne``` method requires a query as a matching criteria to find the target document. If it finds the document, it will update the fields passed in an object (dictionary) in the second argument.

```updateOne``` 方法通过第一个参数查找目标文档。如果能找到文档，它将会根据传入的第二个对象（字典）更新文档。

#### Delete /people/:id (Delete One)

Deleting one is very simple. The ```deleteOne``` method works just like the ```updateOne``` method we saw earlier. It takes the query to match a document and deletes it.

删除一个文档非常简单。```deleteOne``` 方法就像我们看到的 ```updateOne``` 一样简单。它会查询匹配的文档并删除。

```js
// Delete one
router.delete("/people/:id", async (ctx) => {
    let documentQuery = {"_id": ObjectID(ctx.params.id)}; // Used to find the document
    ctx.body = await ctx.app.people.deleteOne(documentQuery);
});
```

### What’s Next?

In this tutorial, we saw how we can implement RESTful routes and use MongoDB CRUD operations. We have finally created a very basic REST APIs. But we didn’t validate or sanitize incoming data. We also didn’t use proper http status codes. Please go through different resources on the internet or our earlier REST API tutorials to learn more about those.

在这篇教程中，我们学会了如何实现 RESTful 路由和如何操作 MongoDB。最终，我们也创建了一份非常简单的 REST APIs。但是，我们没有校验传入的数据。也没用使用正确的 http 状态码。更多的知识可以通过网络上或者我们之前的教程来学习。

In our future tutorials, we shall be covering authentication, serving static files and file uploads.

在我们未来的教程中，我们将会覆盖认证、静态文件服务和文件上传。







