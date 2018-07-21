## How to Send Messages Between Service Workers and Clients
## Service Workers 和客户端如何通信

> 原文地址: <http://craig-russell.co.uk/2016/01/29/service-worker-messaging.html#.W064LdgzZTY/>     
**本文版权归原作者所有，翻译仅用于学习。**

---------

[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) are background processes for web pages. Most of the current excitement around Service Workers is about providing offline web apps, as the Service Worker can manage a local cache of resources syncing back to the server when a connection is available. This is cool, but I want to talk about another use-case for Service Workers, using them to manage communications between multiple web pages.

[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) 是运行在后台独立于 web 应用的程序。大部分人对 Service Workers 提供的离线 web 应用功能感兴趣，Service Workers 可以管理本地的缓存资源，当网络可用的情况下并可以同步缓存资源。这很酷，但是我想讨论 Service Workers 的另外一个功能：如何管理多个页面之间的通信。

For example, you may have an application open in multiple browser tabs. A Service Worker can be used to update content in one tab in response to an event triggerred in another, or to update content in all tabs in response to a message pushed from the server.

假如，你有一个应用在浏览器中打开了多个页面。Service Worker 可以在页面中更新另外一个页面的内容，或者通过后台的推送信息更新所有的页面。

One Service Worker can control multiple client pages. In fact a Service Worker will automatically take control of any client webpages within its [scope](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register#Parameters). Scope is a url path within your site, by default this is the base path of the service worker script.

一个 Service Worker 可以控制多个页面。事实上，Service Worker 会自动接管它所在[域](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register#Parameters)中的所有页面。域是指网站网址的路径，默认是 service worker 所在的路径。

In this demo, we’ll use three files ```client1.html```, ```client2.html``` and ```service-worker.js```.

在示例中，包括三个文件：```client1.html```, ```client2.html``` 和 ```service-worker.js```。

First we register the service worker in ```client1.html```:

首先，我们在 ```client1.html``` 中注册一个 service worker：

```html
<!doctype html>
<html>
<head>
    <title>Service Worker - Client 1</title>
</head>
<body>
    <script>
        if('serviceWorker' in navigator){
            // Register service worker
            navigator.serviceWorker.register('/service-worker.js').then(function(reg){
                console.log("SW registration succeeded. Scope is "+reg.scope);
            }).catch(function(err){
                console.error("SW registration failed with error "+err);
            });
        }
    </script>
</body>
</html>
```

Next we create a basic Service Worker Script in ```service-worker.js```:

接下来在 ```service-worker.js``` 中创建一个基本的 Service Worker：

```js
console.log("SW Startup!");

// Install Service Worker
self.addEventListener('install', function(event){
    console.log('installed!');
});

// Service Worker Active
self.addEventListener('activate', function(event){
    console.log('activated!');
});
```

I won’t bother explaining how this works as it’s [well documented elsewhere](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers).

我不打算解释它是如何工作的，因为它的[文档很详细](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)。

We also create a basic ```client2.html```. We’ll only use ```client1.html``` to register our Servcie Worker, so there’s no need to duplicate code here. When it’s running the Service Worker will automatically take control of this page as it is within it’s scope.

我们也会创建一个基本页面：```client2.html```，只需在 ```client1.html``` 中注册我的 Servcie Worker，所以，有些代码不需了。当 Service Worker 运行后就会自动控制这个页面，因为这个页面也在 Service Worker 的域内。

```html
<!doctype html>
<html>
<head>
    <title>Service Worker - Client 2</title>
</head>
<body>
    <script>
    </script>
</body>
</html>
```

If you visit ```client1.html``` in your browser you should see the registration messages in the console log. In Chrome (48+) you can open an inspector for the Service Worker by clicking “inspect” in “Service Workers” under the “Resources” tab in Dev Tools. When you open ```client2.html``` in a new tab, you should see it listed under “Controlled Clients” in Dev Tools.

如果，你在浏览器中访问 ```client1.html``` 就会在控制台中看到 Servcie Worker 的注册信息。Chrome (48+)中打开开发者工具在 “Resources” 的 tab 中可以看到已注册的 “Service Workers”。当你在新的 tab 中打开 ```client2.html```，应该会在开发者工具中的 “Controlled Clients” 列表中看到它。    
**译者注：目前我是 Chrome 67，Service Workers 已经被转移到 Application tab 中了**

Now we can get on with the interesting stuff…

现在我们可以做一些有趣的事情了...

First we’re going to make the clients send messages to the Service Worker. So we need to add a message handler in ```service-worker.js```:

首先，我们需要在页面上向 Service Worker 发送信息。因此，我们需要在 ```service-worker.js``` 中添加处理信息的方法：

```js
self.addEventListener('message', function(event){
    console.log("SW Received Message: " + event.data);
});
```

Now we can add a send message function to both of the clients.    

现在我们可以为两个页面添加一个发送信息的方法。

```js
function send_message_to_sw(msg){
    navigator.serviceWorker.controller.postMessage("Client 1 says '"+msg+"'");
}
```

If you call ```send_message_to_sw("Hello")``` from the console on the client pages, you should see the message displayed on Service Worker console.

如果，你在控制台执行 ```send_message_to_sw("Hello")```，Service Worker 相关的控制台就会显示这条信息。

We can take this a little further allowing the Service Worker to reply to the message sent by a client. To do this we need to enhance our ```send_message_to_sw``` function. We use a Message Channel, which provides us with a pair of ports to communicate over. We send a reference to one of these ports along with the message, so the Service Worker can use it to send back a reply. We also add a handler to catch the response message. For convenience we also use a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) to handle waiting for the response.

我们可以更进一步让 Service Worker 回复页面发送的信息。为了实现这一步，需要强化 ```send_message_to_sw``` 方法。我们用了一个信息频道，它可以提供两个端口供 Service Worker 和页面之间通信。我们在发送信息的时候把其中之一传递过去，这样 Service Worker 就可以用它来回复信息了。我们也添加了一个方法来接收回复的信息。为了方便我们用了 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 来处理响应。

```js
function send_message_to_sw(msg){#
    return new Promise(function(resolve, reject){
        // Create a Message Channel
        var msg_chan = new MessageChannel();

        // Handler for recieving message reply from service worker
        msg_chan.port1.onmessage = function(event){
            if(event.data.error){
                reject(event.data.error);
            }else{
                resolve(event.data);
            }
        };

        // Send message to service worker along with port for reply
        navigator.serviceWorker.controller.postMessage("Client 1 says '"+msg+"'", [msg_chan.port2]);
    });
}
```

In ```service-worker.js``` we modify our listener to send back a reply on the port sent with the message.

在 ```service-worker.js``` 我们修改了我们的监听器，以便来回复信息。

```js
self.addEventListener('message', function(event){
    console.log("SW Received Message: " + event.data);
    event.ports[0].postMessage("SW Says 'Hello back!'");
});
```

Now if you run ```send_message_to_sw("Hello").then(m => console.log(m))``` in your client console, you should see the message displayed in the Service Worker console and the reply in the client console. Note We’re using the Promise ```then``` function to wait for the response and an [Arrow Function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) because it’s easier to type.

现在，在控制台执行 ```send_message_to_sw("Hello").then(m => console.log(m))```，然后，你会在 Service Worker 相关的控制台看到这条信息，并且页面会得到回复信息。注意，我们用了 Promise 的 ```then``` 方法处理响应，也用到了[三角函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，因为这样非常简单。

Now we have a mechanism for a client to send a message to the Service Worker and for the Service Worker to send back a reply. You could use this for having a client check on the status of a long-running process, have the Service worker forward a message on to all clients or something else cool.

现在，我们有一种机制可以处理 Service Worker 和页面之间的通信问题。你可以用它来检查页面长时间运行的状态，也可以通过 Service worker 向所有的页面转发信息或者更酷的事情。

Magic!

神奇！

Now we’re going to allow the Service Worker broadcast a message to all clients and have the clients respond. This uses a similar mechanism as before except the roles are reversed.

现在，我们让 Service Worker 向所有的页面广播一条信息，并且所有页面都做出响应。这和之前的机制一样，但是角色是相反的。

First we add a message listener to our clients. This is almost identical to before except we first test for Service Worker support.

首先，我们在页面添加一个 message 监听器。这和之前测试 Service Worker 支持情况时几乎一致。

```js
if('serviceWorker' in navigator){
    // Handler for messages coming from the service worker
    navigator.serviceWorker.addEventListener('message', function(event){
        console.log("Client 1 Received Message: " + event.data);
        event.ports[0].postMessage("Client 1 Says 'Hello back!'");
    });
}
```

Next we add a function on our service worker to send a message to a client. This too is similar, except we need to provide a client object (reference to an open page) so we know where to send the message.

接下来，我们添加一个方法用来通过 service worker 向页面发送信息。这也和之前的类似，除了我们需要提供一个页面对象（打开页面的引用），因此，我们知道该向谁发送信息。

```js
function send_message_to_client(client, msg){
    return new Promise(function(resolve, reject){
        var msg_chan = new MessageChannel();

        msg_chan.port1.onmessage = function(event){
            if(event.data.error){
                reject(event.data.error);
            }else{
                resolve(event.data);
            }
        };

        client.postMessage("SW Says: '"+msg+"'", [msg_chan.port2]);
    });
}
```

The Service Worker API provides an interface for getting references to all connected clients. We can wrap this up in a convenience function to broadcast a message to all clients (Note we’re using Arrow Functions again).

Service Worker API 提供了一个接口用来引用所有的有效页面。我们可以包装一下方便向所有的页面广播信息（注意我们再一次用到了三角函数）。

```js
function send_message_to_all_clients(msg){
    clients.matchAll().then(clients => {
        clients.forEach(client => {
            send_message_to_client(client, msg).then(m => console.log("SW Received Message: "+m));
        })
    })
}
```

Now if you run ```send_message_to_all_clients('Hello')``` in the Service Worker console, you should see the message recieved in all the client consoles and the client replies in the Service Worker console.

现在，如果在 Service Worker 中执行 ```send_message_to_all_clients('Hello')```，所有的页面都应该会接收到这条信息，并向 Service Worker 做出回复。

More Magic!   
很神奇！

