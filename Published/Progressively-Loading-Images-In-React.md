# Progressively Loading Images In React

# React 中渐进式加载图片

> 原文地址: <https://medium.com/frontend-digest/progressively-loading-images-in-react-107cb075417a/>  
>
>  **本文版权归原作者所有，翻译仅用于学习。**

Have you ever wondered how Medium loads images? Maybe you noticed how images seem to render in multiple steps. A blurry version of the image appears on screen and then is replaced by the full-sized version.

你是否好奇 Medium 是如何加载图片的？或许，你已经注意到图片是分多个步骤加载渲染的。首先，显示一张模糊版本的图片，然后，用全尺寸的图片替换掉。

![](https://miro.medium.com/max/1400/1*DwWMst_eKhqT1BRCDpSkTg.gif)

## How Medium stories loads images

##  Medium 如何加载图片

- The image loading does not begin until the image enters the viewport
- Then a “blurred up” thumbnail loads
- Then the full-size image loads and replaces the thumbnail

We can categorize this image loading technique into two distinct features.

- 直到图片进入可是范围才加载图片
- 然后，加载一张模糊的缩略图
- 然后，加载全尺寸图片，并替换掉缩略图

我们可以把图片加载技术分为两个不同的功能。

## 1. Lazy loading

## 1. 懒加载

> Lazy loading is technique that defers loading of non-critical resources at page load time. Instead, these non-critical resources are loaded at the moment of need. Where images are concerned, “non-critical” is often synonymous with “off-screen” — [developers.google.com](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video)
>
> 懒加载是在页面加载时延迟加载非关键资源的一种技术。那些，非关键的资源按需加载。对于图片，非关键通常代表着屏幕外 — [developers.google.com](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video)

Lazy loading is a great technique because it can drastically improve your site’s perceived performance.

懒加载是一种非常好的技术，它可以明显提升网站的性能。

Imagine you wrote a ten-minute long blog post with 20 high-resolution images. If all 20 images were loaded at once, the post would be slow to load. With lazy loading, we can load the images on demand. No matter how long the story, we only render what’s visible to the user.

想象一下，你写了一篇 10 分钟长的博客，里面有 20 张高分辨率图片。如果，这些图片一次性加载，博客将会变得很慢。通过，懒加载我们可以按需加载。无论内容有多少，我们只渲染那些用户看到即可。

## 2. “Blur Up”

## 2. 模糊处理

To display something on the screen faster, we show a blurred tiny image scaled to full width. When we have finished loading the full-sized image, we swap them out.

为了更快的显示内容，我们需要显示一张模糊了的小图并放大到全尺寸。当全尺寸的图片加载完，然后替换掉它。

If you have worked with Gatsby before, you have probably used [gatsby-image](https://www.gatsbyjs.org/packages/gatsby-image/). Gatsby-image gives us these two techniques without the hassle of building it ourselves.

如果，你之前用过 Gatsby，你应该用过 [gatsby-image](https://www.gatsbyjs.org/packages/gatsby-image/)。它为我们提供上面提到的技术，并不需要自己实现。

But we are developers. We like to build things ourselves.

但是，作为一个开发者，我喜欢自己实现。

# So let’s build it.

# 我们来实现它

First, let’s analyze the problem.


- We need to know which images have entered the viewport
- Once an image enters the viewport, we need to load the thumbnail and the full-sized image
- Once the full-sized image is loaded, we need to swap out the thumbnail
- We need to make sure that our page doesn’t “jump” when we load our images. Our placeholder container should be the same height and width as our final image.

首先，我们分析下问题

- 我们需要知道哪些图片进入了可视范围
- 一旦图片进入可视范围，我们需要加载缩略图和全尺寸图片
- 一旦全尺寸的图片加载完成，我们需要替换掉缩略图
- 在加载图片时，我们要保证页面不能抖动。我们占位的容器大小应该和最终图片的大小保持一致

## Let’s get started

## 我们开始

Start by scaffolding a new React application using [create-react-app](https://github.com/facebook/create-react-app).

我们先用 [create-react-app](https://github.com/facebook/create-react-app) 创建一个 React 应用的骨架。

```shell
npx create-react-app progressive-images
```

We will use [Unsplash](https://unsplash.com/) for our images. I used the Unsplash API to get an array of ten of their latest images. This response is saved in a [Github Gist](https://gist.github.com/montezume/daaebfca37120a4994402b31de527c07).

我们将会使用 [Unsplash](https://unsplash.com/) 中的图片。我使用 Unsplash API 获取了最新的 10 张图片。并它们保存在 [Github Gist](https://gist.github.com/montezume/daaebfca37120a4994402b31de527c07) 中。

Copy and paste the contents of this gist into a file called `images.json`.

把 gist 的内容复制到名为 `images.json` 的文件中。

Now open `App.js` and replace it with the following.

打开 ```App.js``` 并用以下的内容替换掉它。

```jsx
import React from "react";
import images from "./images.json";
import ImageContainer from "./components/image-container";
import "./App.css";
function App() {
  return (
    <div className="app">
      <div className="container">
        {images.map(res => {
          return (
            <div key={res.id} className="wrapper">
              <ImageContainer
                src={res.urls.regular}
                thumb={res.urls.thumb}
                height={res.height}
                width={res.width}
                alt={res.alt_description}
              />
            </div>
          );
        })}
      </div>
    </div>
  );
}
export default App;
```

And open `App.css` and paste in the following

然后，打开 `App.css` 并用以下内容替换

```css
.app {
  display: flex;
  justify-content: center;
  padding-top: 1em;
}
.container {
  width: 100%;
  max-width: 600px;
}
.wrapper {
  padding: 1em 0;
}

```

Now let’s create `components/image-container.js`.

现在，我们来创建 `components/image-container.js`

Before worrying about rendering an image, let’s make the fallback container.

在处理图片渲染之前，我们先创建一个容器。

```jsx
import React from "react";
import "./image-container.css";const ImageContainer = props => {
  const aspectRatio = (props.height / props.width) * 100;return (
    <div
      className="image-container"
      style={{ paddingBottom: `${aspectRatio}%` }}
    />
  );
};
```

And `image-container.css`

然后是 `image-container.css`

```css
.image-container {
  position: relative;
  overflow: hidden;
  background: rgba(0, 0, 0, 0.05);
}
```

First, we figure out the aspect ratio of the image. This is calculated by dividing the width by the height. Then we add `padding-bottom` to our image container with this value.

首先，我们需要计算出图片的宽高比例。通过 width/height 可以获得。然后，用这个值设置容器的 `padding-bottom` 。

For example, a 1024 x 768 px image has an aspect ratio of 0.75. We would add `padding-bottom: 75%` to our container.

比如，一张 1024 x 768 图片的宽高比就是 0.75。我就会把容器的 `padding-bottom`  设置为 `75%`。

We can run our app with `yarn start` and see what it looks like.

我们可以通过 `yarn start` 运行 app，并会看到以下结果。

![](https://miro.medium.com/max/1288/1*36CKRgWe70SX1BneaYtdcw.gif)

Now we have some boxes that are the same size as the images we want to render.

现在，我们已经有了一些 box，它们的尺寸和需要渲染的图片一致。

## Intersection Observer

Now we need a way of keeping track of when an image enters the viewport. For this, we can use the new browser API `IntersectionObserver`.

现在，我们需要一种方法监控图片是否进入了可视范围。我们可以使用新的浏览器 API `IntersectionObserver` 来实现。

> The Intersection Observer API provides a way to asynchronously observe changes in the intersection of a target element with an ancestor element or with a top-level document’s viewport — [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
>
> Intersection Observer API提供了一种异步观察目标元素与祖先元素或顶级文档[viewport](https://developer.mozilla.org/en-US/docs/Glossary/viewport)的交集中的变化的方法 — [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)

Let’s make a custom hook. Create the file `hooks/use-intersection-observer.js`.

我们来实现一个自定义 Hook：`hooks/use-intersection-observer.js`。

```jsx
import React from "react";
const useIntersectionObserver = ({
  target,
  onIntersect,
  threshold = 0.1,
  rootMargin = "0px"
}) => {
  React.useEffect(() => {
    const observer = new IntersectionObserver(onIntersect, {
      rootMargin,
      threshold
    });
const current = target.current;
observer.observe(current);
return () => {
      observer.unobserve(current);
    };
  });
};
export default useIntersectionObserver;
```

Since we didn’t define a `root`, `IntersectionObserver` defaults to the viewport. We defined a threshold of `0.1`. This means that when 10% of the target is visible within the viewport, our callback is invoked.

由于，我们没有定义 `root`。`IntersectionObserver` 默认为可视窗口。我们定义了 `threshold: 0.1`。这代表着当目标元素的 10% 进入到可视窗口，我们的回调就会执行。

## Using our custom hook

## 使用自定义 Hook

To use this custom hook, we need to invoke it with a `target` and a callback function.

使用自定义 Hook 时，我们需要指定一个 `target` 和相应的回调函数。

Our target with be a React `ref` attached to our container div.

目标元素将会用 React `ref` 引用着容器 div。

Our callback function will set a state variable indicating that the image is visible. Then it will call `observer.unobserve`. Once an image is visible, we don’t need `IntersectionObserver` to observe it any longer.

我们的回调方法将会设置一个 state 变量，用来标示图片是否可见。然后执行 `observer.unobserve`。一旦图片可见，我们就不需要 `IntersectionObserver` 去监控它了。

Make the following changes to `image-container.js`.

在 `image-container.js` 中按照以下内容调整。

```JSX
import React from "react";
import useIntersectionObserver from "../hooks/use-intersection-observer";
import "./image-container.css";
const ImageContainer = props => {
  const ref = React.useRef();
  const [isVisible, setIsVisible] = React.useState(false);
useIntersectionObserver({
    target: ref,
    onIntersect: ([{ isIntersecting }], observerElement) => {
      if (isIntersecting) {
        setIsVisible(true);
        observerElement.unobserve(ref.current);
      }
    }
  });
const aspectRatio = (props.height / props.width) * 100;
return (
    <div
      ref={ref}
      className="image-container"
      style={{ paddingBottom: `${aspectRatio}%` }}
    >
      {isVisible && (
        <img className="image" src={props.src} alt={props.alt} />
       )}
    </div>
  );
};
export default ImageContainer;
```

Now we render the full-sized image when our component enters the viewport.

现在，当组件进入可视范围时就会渲染全尺寸的图片。

Let’s see it in action.

我们来看看具体效果

![](https://miro.medium.com/max/1376/1*_Dp7sJW6IWL7MX4q7pYzmA.gif)

Nice! Our app is now lazy loading the images. Our images are only downloaded when they are visible in the viewport.

很好！我们的应用已经可以懒加载图片了。图片只有在进入可视区域才会加载。

If you check the network tab, you can see this in action. Check the **Waterfall**.

如果，你打开开发者工具中的 network 选项，你们会看到它实际的效果。看一下 **Waterfall**。

![](https://miro.medium.com/max/1400/1*foo7RkHOW2gDcJSiz2PydA.png)

## Adding the Blur Up Technique

## 添加模糊技术

Start by creating two new files. `components/image.js` and `components/image.css`.

首先创建两个文件： `components/image.js` 和 `components/image.css`。

Our `Image` component renders two images: the full-sized image and the thumbnail. We hide the thumbnail when the full-sized image is loaded.

我们会在组件 `Image` 中渲染两张图片：全尺寸图片和缩略图。但全尺寸图片加载完成就隐藏缩略图。

Copy and paste the code below into `components/image.js`.

复制以下代码到  `components/image.js` 中。

```jsx
import React from "react";
import "./image.css";
const Image = props => {
  const [isLoaded, setIsLoaded] = React.useState(false);
  return (
    <React.Fragment>
      <img
        className="image thumb"
        alt={props.alt}
        src={props.thumb}
        style={{ visibility: isLoaded ? "hidden" : "visible" }}
      />
      <img
        onLoad={() => {
          setIsLoaded(true);
        }}
        className="image full"
        style={{ opacity: isLoaded ? 1 : 0 }}
        alt={props.alt}
        src={props.src}
      />
    </React.Fragment>
  );
};
export default Image;
```

And the CSS below into`components/image.css`.

以下是 `components/image.css` 的内容。

```css
.image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.full {
  transition: opacity 400ms ease 0ms;
}
.thumb {
  filter: blur(20px);
  transform: scale(1.1);
  transition: visibility 0ms ease 400ms;
}
```

Now let’s run our application one last time.

现在，我来最后一次运行下我们的应用。

Be sure to open `devtools` and disable caching.

确保打开了 `devtools` 并禁用了缓存。

![](https://miro.medium.com/max/1316/1*IIjrvKEJDoxvOsN1uvkc7w.gif)

# Summary

# 总结

We made a React application with lazy loaded images. Our application only renders images after they enter the viewport. It also progressively renders them by first showing a blurred thumbnail.

我们制作了一个带有图片懒加载技术的 React App。我的 App 只有在图片进入可视区域才会渲染图片。首先显示一张模糊的缩略图，然后渐进式的显示完整的图片。

Take a look at the repository [here](https://github.com/montezume/medium-style-progressively-loaded-images).

完整的代码可以在[这](https://github.com/montezume/medium-style-progressively-loaded-images)查看。