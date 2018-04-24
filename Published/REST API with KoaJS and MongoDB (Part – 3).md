## REST API with KoaJS and MongoDB (Part – 3)
## 用 KoaJS 和 MongoDB 实现 REST API - 3

> 原文地址: <http://polyglot.ninja/rest-api-koajs-mongodb-part-3/>   
**本文版权归原作者所有，翻译仅用于学习。**

---------

In [Part -1](http://polyglot.ninja/koajs-mongodb-rest-api/) of this series, we saw how we can get started with KoaJS and in [Part – 2](http://polyglot.ninja/rest-api-koajs-and-mongodb-part-2/) we built CRUD endpoints with MongoDB. In this part, we’re going to work with authentication. We will be using JSON Web Tokens aka JWT for the auth part. We have written detailed pieces on JWT before. You can read [Understanding JWT](http://polyglot.ninja/understanding-jwt-json-web-tokens/) to check out the basics and read our tutorial on [JWT with Flask](http://polyglot.ninja/jwt-authentication-python-flask/) or [JWT with Django](http://polyglot.ninja/django-rest-framework-json-web-tokens-jwt/) to see how other frameworks like Flask uses JWT.

在这个系列中的[第一篇](http://polyglot.ninja/koajs-mongodb-rest-api/)教程中，我们学会了如何使用 KoaJS；在[第二篇](http://polyglot.ninja/rest-api-koajs-and-mongodb-part-2/)教程中我们学会了 MongoDB 的 CRUD。在这篇教程中，我们继续学习如何认证。我们将会用 JWT 实现认证。之前我们已经详细介绍过 JWT。你可以阅读[理解 JWT](http://polyglot.ninja/understanding-jwt-json-web-tokens/)来对 JWT 有个基本的认识；还可以阅读我们的教程了解如何在其他框架中使用 JWT，比如：[Flask 中使用 JWT](http://polyglot.ninja/jwt-authentication-python-flask/) 和 [Django 中使用 JWT](http://polyglot.ninja/django-rest-framework-json-web-tokens-jwt/)。

### JWT with KoaJS

To implement JSON Web Tokens with KoaJS, we would be using two packages – ```koa-jwt``` and ```jsonwebtoken```. The second package (```jsonwebtoken```) provides useful helper functions to generate and verify JWTs. Where as ```koa-jwt``` provides an easy to use middleware that we can use with KoaJS.

在 KoaJS 中实现 JWT，我们会用到两个包 - ```koa-jwt``` 和 ```jsonwebtoken```。第二个包（```jsonwebtoken```）提供非常有用的方法生成和校验 JWTs。```koa-jwt``` 是一个中间件，在 KoaJS 很容易实现 JWT。

Let’s go ahead and install these packages:

我们继续，先安装这些包：

```sh
npm i -S koa-jwt jsonwebtoken
```

That should install the dependencies and save them in our ```package.json```.

这些包应该保存在 ```package.json``` 的依赖属性中。

### Securing Routes with JWT

We have the required packages installed. So we can now start securing our routes with JWT. We can just ```require``` the ```koa-jwt``` package directly and use it. But we want to customize some aspects. For that we would create our own module named ```jwt.js``` and put the custom stuff in there.

我们已经安装好必要的包。我们现在就可用 JWT 实现路由了。我们直接引入 ```koa-jwt``` 并使用它。但是，我们想做一些自定义的设置。因此，我们需要创建单独的模块 ```jwt.js``` 并在这里面实现自定义的部分。

```js
const jwt = require("koa-jwt");
const SECRET = "S3cRET~!";
const jwtInstance = jwt({secret: SECRET});

module.exports = jwtInstance;
```

Now in our ```index.js``` file, we would add the middleware to the app.

现在，在我们 ```index.js``` 中为我们的应用添加这个中间件。

```js
app.use(require("./jwt"));
```

If we try to visit http://localhost:3000/, we will get a plain text error message saying “Authentication Error”. While the message is clear and concise, we want to output JSON, not a plain text error message. For that, we will write a custom middleware.

如果，你现在尝试访问http://localhost:3000/，我们将会得到一个纯文本的错误信息：“Authentication Error”。信息简单明了，我们想输出 JSON，而不是一条文本错误信息。为此，我们需要自定义中间件。
```js
function JWTErrorHandler(ctx, next) {
    return next().catch((err) => {
        if (401 == err.status) {
            ctx.status = 401;
            ctx.body = {
                "error": "Not authorized"
            };
        } else {
            throw err;
        }
    });
};
```

The code for this middleware is pretty simple. It invokes the next middleware and if it catches an error, it checks if it’s 401, if so, it sets a nice detailed JSON as the output. If you’re familiar with how middlewares work in express / koa, this should make sense. If it doesn’t make sense, don’t worry, you will get it over time.

这个中间件的代码非常简单。如果捕获错误，它会调用 next 中间件，并校验状态码，如果是401，就会输出一个详细的 JSON。如果，你熟悉 express／koa 中间件工作模式，你就会清晰的知道这很有意义。如果，你不这么认为，别担心，时间会证明一切。

Now we need to export this function from our ```jwt.js``` module. Let’s change the exports a little bit.

现在，我们需要从 ```jwt.js``` 模块导出这个方法。让我们稍微修改一下。

```js
module.exports.jwt = () => jwtInstance;
module.exports.errorHandler = () => JWTErrorHandler;
```

Now we’re exporting two functions, which, when called will return the specific middlewares. We also need to change our imports in ```index.js``` –

现在我们导出了两个方法，当我们引用时，它会返回特定的中间件。在 ```index.js``` 中我们也需要调整。

```js
const jwt = require("./jwt");
app.use(jwt.errorHandler()).use(jwt.jwt());
```

Please note the order of the middleware we used. The error handler must come before the JWT middleware itself, so it can call ```next()``` and check for the 401 error.

请注意中间件的使用顺序。错误处理方法必须在 JWT 的前面，因此，它才能执行 ```next()``` 并检查401错误。

If we try to browse the API now, we should get a nice JSON like this:

现在，我们尝试浏览 API，我们应该会得到一个像这样的 JSON：

```json
{"error":"Not authorized"}
```

### Secured Routes and Router

We used the middleware directly on the koa app. That means all our routes are now secure. All the routes would now check for the ```Authorization``` header value and try to verify it’s value as a JSON Web Token. That’s good but there’s a slight problem. If we can’t access any of the routes without a token, which route do we access to get the token in the first place? And what token do we use for that? Yeah, we need to have at least one route which is not secured with JWT which will accept login details and issue the JWTs to the users. Besides, there could be other API end points which we can keep open to everyone, we don’t need authentication on those routes. How do we achieve that?

我们直接在 koa 应用上使用中间件。也就是说所有的路由都需要加密。所有的路由都会检验请求头中 ```Authorization``` 的值是否有效切 JWT。这很好，但是也有点小问题。如果，在没有令牌的情况下我们不能访问任何的路由，是否应该有一个路由可以让我们获取令牌呢？我们使用什么样的令牌呢？好了，我至少有一个路由不需要 JWT 验证，可以让用户用来登录。除此之外，应该还有其它的 API 对所有人保持开放，我们不需要对这些路由校验。我们应该如何处理呢？

Luckily, Koa allows us to use multiple routers and each router can have their own set of middlewares. We will keep our current router open and add the routes to obtain the JWT. We will create a separate route which will use the middleware and be secured. We will call this one the “secured router” and the routes would be “secured routes”.

幸运的是，Koa 允许使用多个路由器并为每个路由器设置自己的中间件。我们将保持当前路由器开放，然后添加新的需要 JWT 验证的路由。我们会单独创建一个路由器，它可以有自己的中间件并要求验证。我们称这个为“安全路由器”，对应的路由称为“安全路由”。

```js
// Create a new securedRouter
const router = new Router();
const securedRouter = new Router();

// Add the securedRouter to our app as well
app.use(router.routes()).use(router.allowedMethods());
app.use(securedRouter.routes()).use(securedRouter.allowedMethods());
```

We modified our existing codes. We now have two routers and we added them both to the app. Let’s now move our old CRUD routes to the secured router and apply the JWT middleware to just the secured router.

我们修改原有的代码。现在，我们有两个路由器并把它们都添加到应用上。让我们把之前的 CRUD 移到安全路由器上并为它们添加 JWT 中间件。

```js
// Apply JWT middleware to secured router only
securedRouter.use(jwt.errorHandler()).use(jwt.jwt());

// List all people
securedRouter.get("/people", async (ctx) => {
    ctx.body = await ctx.app.people.find().toArray();
});

// Create new person
securedRouter.post("/people", async (ctx) => {
    ctx.body = await ctx.app.people.insert(ctx.request.body);
});

// Get one
securedRouter.get("/people/:id", async (ctx) => {
    ctx.body = await ctx.app.people.findOne({"_id": ObjectID(ctx.params.id)});
});

// Update one
securedRouter.put("/people/:id", async (ctx) => {
    let documentQuery = {"_id": ObjectID(ctx.params.id)}; // Used to find the document
    let valuesToUpdate = ctx.request.body;
    ctx.body = await ctx.app.people.updateOne(documentQuery, valuesToUpdate);
});

// Delete one
securedRouter.delete("/people/:id", async (ctx) => {
    let documentQuery = {"_id": ObjectID(ctx.params.id)}; // Used to find the document
    ctx.body = await ctx.app.people.deleteOne(documentQuery);
});
```

We removed the previously setup JWT middleware from the app and used it on securedRouter instead. Remember, the JWT middleware must be setup before we setup the routes themselves. Ordering of middleware matters.

我们把之前应用在全局的 JWT 的中间件转移到安全路由器上。记住，JWT 中间件必需在设置路由之前配置。中间件的顺序非常关键。

If we try to visit “http://localhost:3000/”, we will no longer get the auth error, rather will see “not found” (we didn’t define any routes for the root url). However, if we try to visit “http://localhost:3000/people”, we will get the authentication error again. Exactly what we wanted.

如果，我们尝试访问 “http://localhost:3000/”，不再会有验证错误了，然而会看到 “not found”（我们没有为根路由定义任何的内容）。但是，如果我们访问“http://localhost:3000/people”，我们会再次看到验证错误信息。这就是对了。

### Issuing JWTs

We now need to create the route to issue JWTs to our users. We will be accepting their login (username and password) and if they’re valid, we will issue them JWTs which they can use to further access our APIs.

现在，我们只需创建一个路由为用户派发令牌。我们会接收用户的登录信息(用户名称和密码)如果信息有效，我们会派发令牌，这样用户就可以访问其它 APIs 了。

The ```koa-jwt``` package no longer supports issuing tokens. We have to use the ```jsonwebtoken``` package for that instead. Personally, I like to create a helper function in my custom ```jwt.js``` module like this:

```koa-jwt``` 不再支持派发令牌了。我们不得不用 ```jsonwebtoken``` 代替。个人更喜欢在自定义模块 ```jwt.js``` 中创建辅助方法，像这样：

```js
// Import jsonwebtoken
const jsonwebtoken = require("jsonwebtoken");

// helper function
module.exports.issue =  (payload) => {
    return jsonwebtoken.sign(payload, SECRET);
};
```

Then we can write a new route on our public router like this:

然后，我们可以在我们开放路由器上添加一个新的路由像这样：

```js
router.post("/auth", async (ctx) => {
    let username = ctx.request.body.username;
    let password = ctx.request.body.password;

    if (username === "user" && password === "pwd") {
        ctx.body = {
            token: jwt.issue({
                user: "user",
                role: "admin"
            })
        }
    } else {
        ctx.status = 401;
        ctx.body = {error: "Invalid login"}
    }
});
```

We have hardcoded the username and password here. In production environments, we would store the details in a database and we would hash the password. No one in their right mind should store password in plain text.

我们对用户名和密码进行硬编码。在生产环境中，我们应该把详细信息存储在数据库中并对密码进行 hash 处理。没有人会愿意直接存储明文密码的。

In this view, we are accepting a JSON payload and checking the username and password. And then if the details match, we are issuing the token. To test if it’s working, we can make a curl request and checkout the response:

这次，我们接收一个 JSON 对象并校验用户名和密码。如果它们完全匹配，我们会派发令牌。我们可以发起一个请求，然后查看响应，测试一下是否正常：

```sh
curl -X POST \
  http://localhost:3000/auth \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{"username": "user", "password": "pwd"}'
```

If it worked, we will get a JSON back with a ```token``` value containing the JWT.

如果，能正常工作，我们应该会得到一个带有 ```token``` 的 JSON。

### Using the JWT

We made a request and got the following response:

我们发起一个请求会得到以下的响应：

```json
{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsInJvbGUiOiJhZG1pbiIsImlhdCI6MTUwMjI3MTM0Nn0.GWtjeECIHFQr7vI_MphfUle06Pav_zx4sLmSrd3HE8g"}
```

That is our token. Now we can start using it in the Authorization header. The format should be like:```Authorization: Bearer <Token>``` . We can make a request to our secured “/people” resource using curl with this header:

这就是我们的令牌。现在我们就可以在请求头中使用它了。使用的格式：```Authorization: Bearer <Token>```。我们用 curl 向 “/people” 发起一个请求并设置这样的请求头：

```sh
curl -X GET \
  http://localhost:3000/people \
  -H 'authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsInJvbGUiOiJhZG1pbiIsImlhdCI6MTUwMjI2OTg4MX0.Ugbh4UwN9tRwhIQEQUHoo-affUf5CAsCztzAXncBYt4' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
```

We will now get back the list of people we have stored in our mongodb.

现在，我们就可以得到存储在 mongodb 中的数据了。






