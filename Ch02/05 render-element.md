### 渲染 React 元素

ReactDOM.render() 方法接收三个参数：ReactElement、一个常规的 DOMElement 和一个 callback 函数：  

ReactDOM.render(ReactElement, DOMElement, callback);  

 - ReactElement 是你已经创建的 ReactNode 树中的根节点。  
 - 常规的 DOMElement 是这个树的容器 DOM 节点。
 - callback 函数将会在这个树渲染或更新之后执行。

注意，如果 ReactElement 之前已经渲染到父 DOM Element ，那么 ReactDOM.render() 将只更新已经渲染的 DOM 树，
并且只改变那些相较于最新版 ReactElement 确实需要改变的 DOM。这就是虚拟DOM 只需要更改少量 DOM  的原因。  

到现在为止，我们一直假设我们是在 Web 浏览器中创建虚拟 DOM 的。  
这是可以理解的，因为毕竟 React 是一个 UI 库，而所有 UI 都在 Web 浏览器中渲染。  

**那么你知道什么情况下客户端渲染 UI 会比较慢吗？**      

可能有人已经猜到了，我要说的是初始页面加载。  

开始时就提到了初始页面加载的问题 -- 使用 React 后，我们不再创建静态的 Web 页面。  
相反，当浏览器加载我们的 Web 应用时，它只接收到最低限度的 HTML 标签，通常是 Web 应用程序的容器或父元素。  
然后，利用 JavaScript 代码创建 DOM 的其余部分，但为此往往需要从服务器请求额外的数据。  
只有接收到请求的数据，JavaScript 代码才会开始更新 DOM 。  
然而，请求这些数据是需要时间的，这就是 DOM 更新比较慢的原因。  

那么我们该如何解决这个问题呢？  

解决方案可能有点出乎意料：在服务器端而非浏览器中做 DOM 更新，
就像我们处理静态的 Web 页面一样，浏览器会接收到完整的 HTML，
这部分 HTML 可以完整地渲染出应用程序的初始 UI 界面。  

这听起来可能很简单，但是我们不能在服务器上更新 DOM ，因为 DOM 不能离开浏览器单独存在。  

我们的虚拟 DOM 只依赖 JavaScript ，而且我们知道，通过 Node.js 可以在服务器上运行 JavaScript 。  
所以从技术上讲，我们可以在服务器端使用 React 库，并且创建 ReactNode 树。  
问题是我们怎样才能将这个树渲染成一个字符串，然后把它发送到客户端呢？  

React 有一个名为 ReactDOMServer.renderToString() 的方法可以实现：  

	var ReactDOMServer = require('react-dom/server');
    ReactDOMServer.renderToString(ReactElement);

它会接收一个 ReactElement 作为参数，并渲染出初始的 HTML ，这不仅比在客户端渲染 DOM 快，
而且能改善 Web 应用程序的搜索引擎优化 (SEO) 效果。  

说到生成静态页面，用 React 也可以做到：  

	var ReactDOMServer = require('react-dom/server');
    ReactDOMServer.renderToStaticMarkup(ReactElement);

类似于 ReactDOMServer.renderToString()，这个方法也需要一个 ReactElement 作为参数并且输出一个 HTML 字符串。
但是，它不会创建 React 内部使用的额外的 DOM 属性，它只会生成更短的 HTML 字符串，方便我们迅速传输。  

现在你不仅知道了如何使用 React 的元素创建一个虚拟的 DOM 树，而且也知道了如何在客户端和服务器上对它进行渲染。
下一个问题就是我们能否更迅速更直观地来做这件事。

