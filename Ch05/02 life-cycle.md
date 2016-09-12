## 理解 React 组件的生命周期方法

思考一下 React 组件可以做什么？它描述了要渲染什么内容。我们知道它使用 render() 方法做这些。  
然而，有时候仅仅使用 render() 方法是不够的，因为我们可能想在组件渲染之前或之后做些事情。  
另外，我们可能还需要决定是否需要调用 render() 方法。      

我们所描述的是 React 组件被渲染的过程。  
这个过程有不同的阶段，例如渲染前、渲染中、渲染后等。  
在 React 中，这个过程就叫作组件的生命周期。  
每个 React 组件都会经历这个过程。  
我们所需要的是一种连接到这个过程的方法，并且在这个过程的不同阶段调用我们自己的函数，以便有更大的控制权。  
为了这个目的，React 提供了一些方法，在组件生命周期过程中，我们可以使用这些方法以便在某个阶段发生时得到通知。  
这些方法就叫做组件的生命周期方法，它们按特定的顺序被调用。     

所有 React 组件生命周期方法可以分为下面三个阶段，参见下图。     

 - 挂载 (Mounting): 这个阶段发生在组件被插入 DOM 时。
 - 更新 (Unpdating): 这个阶段发生在组件被重新渲染成虚拟 DOM 并决定实际 DOM 是否需要更新时。
 - 卸载 (Unmounting): 这个阶段发生在组件从 DOM 中被删除时。  

![](http://i.imgur.com/kDWfqwW.png)

**用 React 的术语来说，把组件插入 DOM 叫挂载，相反在 DOM 中删除组件叫卸载。**             

学习 React 组件生命周期方法最好的方式是实际使用它们。  
让我们创建在前面已经讨论过的 StreamTweet 组件。这个组件将实现大部分 React 生命周期方法。    

进入 ~/snapterest/source/components/目录，然后创建 StreamTweet.react.js 文件：   

	var React = require('react');
    var ReactDOM = require('react-dom');
    var Header = require('./Header.react');
    var Tweet = require('./Tweet.react');

    var StreamTweet = React.createClass({
    	// 在这里定义其他组件生命周期方法
        render: function(){
        	console.log('[Snapterest] StreamTweet: Running render()');

            return (
            	<section>
                	<Header text={this.state.headerText} />
                    <Tweet
                    	tweet={this.props.tweet}
                        onImageClick={this.props.onAddTweetToCollection} />
                </section>
            );
        }
    });

	module.exports = StreamTweet;

StreamTweet 组件现在除了 render() 之外没有其他生命周期方法，我们下面就逐个创建并讨论它们。     

如下图所示，其中的四个方法是在组件挂载阶段被调用的：  

![](http://i.imgur.com/MlQX9ib.png)

如上图所示，被调用的方法如下：  

 - getiInitialState()
 - componentWillMount()
 - render()
 - componentDidMount()

在这里，我们将会讨论除了 render() 之外的其他三个方法。  

**当组件被插入 DOM 的时候它们仅被调用一次。**    

让我们仔细看看它们中的每一个。  

### 挂载方法

现在让我们来看看一些有用的挂载方法。    

#### getInitialState() 方法

getInitialState() 方法是第一个被调用的，它会在 React 将组件插入 DOM **之前**被调用。   

如果你想让组件有一个 state，那么可以使用这个方法返回初始的组件 state。  

在 StreamTweet 组件中，将下面这行注释：    

// 在这里定义其他组件生命周期方法  

替换为：  

	getInitialState: function(){
    	console.log('[Snapterest] StreamTweet: 1. Running getInitialState()');

        return {
        	numberOfCharactersIsIncreasing: null,
            headerText: null
        };
    },

在 StreamTweet 组件的 getInitialState() 方法中，我们将执行以下步骤。    

1 在浏览器的控制台输出以下消息：   

	[Snapterest] StreamTweet: 1. Running getInitialState() .

2 返回一个对象，将它的属性 numberOfCharactersIsIncreasing 和 HeaderText 都设置为 null 。  

numberOfCharacterIsIncreasing 将监测下一条将要被显示的推文是否比当前显示的推文有更多的字符。  
在组件的下一个生命周期方法中，我们会将它设为布尔值。    

headerText 将存储 StreamTweet 渲染 Header 组件时所需要的文本。  

和所有其他挂载方法一样，getInitialState() 仅仅会被调用一次。     

#### componentWillMount() 方法

componentWillMount() 方法是第二个被调用的，它会在 React 将组件**即将插入 DOM 时**被调用。  

在 StreamTweet 组件中，将下面这段代码添加到 getInitialState() 方法后面：  

	componentWillMount: function(){
    	console.log('[Snapterest] StreamTweet: 2. Running componentWillMount()');

     	this.setState({
        	numberOfCharactersIsIncreasing: true,
          	headerText: 'Latest public photo from Twitter'
    	});

    	window.snapterest = {
    		numberOfReceivedTweets: 1,
        	numberOfDisplayedTweets: 1
    	};
	},

在这个方法中我们做了一系列的事情。首先，我们记录了这个方法被调用事实。  
事实上，出于演示的目的，我们将记录每个组件的生命周期方法。  
在浏览器中运行这段代码时，你应该可以在 JavaScript 控制台中看到这些日志信息按着预期的顺序被打印出来了。    

接下来，我们使用 this.setState() 方法来更新组件的 state:    

 - 设置 numberOfCharactersIsIncreasing 属性值为 true
 - 设置 headerText 属性值为字符串 'Latest public photo from Twitter' 。

因为这是组件将要渲染的第一条推文，我们知道在第一条推文中，字符数当然是从无增加到有的。  
所以，我们将它设置为 true，同时分配默认的文本 'Latest public photo from Twitter' 给头部。  

如你所知，调用 this.setState() 方法会触发组件的 render() 方法，  
所以看起来像是在组件挂载阶段 render() 方法被调用了两次。  
然而在这个例子中，React 知道还没有开始渲染，所以它将仅仅调用 render() 方法一次。  

最后，我们在这个方法中定义一个 snapterest 全局对象，它有以下两个属性。  

 - numberOfReceivedTweets: 这个属性用来记录所有接收的推文数量。
 - numberOfDisplayedTweets: 这个属性用来记录所有显示的推文数量

我们将 numberOfReceivedTweets 的值设置为 1，因为我们知道当接收到第一条推文时，  
componentWillMount() 方法仅调用一次。  
我们也知道当接收到第一条推文时，render() 方法会被调用，所以将 numberOfDisplayedTweets 的值也设置为 1：  
 
	window.snapterest = {
    	numberOfReceivedTweets: 1,
        numberOfDisplayedTweets: 1
    }; 

这个全局对象不是 React 的一部分，也不是应用程序逻辑的一部分，即使我们删除它，程序也将会按照预期工作。  
window.snapterest 是一个方便的工具，用来随时记录我们处理了多少条推文。  
我们使用 window.snapterest 仅仅是为了示范。  

我强烈建议你不要在真实的项目中将属性添加到一个全局对象中，因为你可能会重写现有的属性，  
或者属性也可能被后面其他不属于你的 JavaScript 代码重写。  
如果日后你决定在产品中部署 Snapterest，  
那么请确保从 StreamTweet 组件中删除了这个全局变量 window.snapterest 及相关的代码。  

Snapterest 在浏览器中运行了几分钟之后，你可以打开 JavaScript 控制台
并键入 snapterest.numberOfReceivedTweets 和 snapterest.numberOfDisplayedTweets 命令。  
这些命令将会输出一些数字，帮助你更好地了解新推文来的速度有多快，以及它们中有多少没有被显示。  
在下一个组件生命周期方法中，我们将在 window.snapterest 对象中添加更多的属性。  

#### componentDidMount() 方法

componentDidMount() 方法是第三个被调用的。它在 React **将组件插入到 DOM 之后**立即被调用。  

更新后的 DOM 现在可以被访问，这意味着这个方法是**初始化其他需要访问这些 DOM 的 JavaScript 库的最佳地方**。  

前面我们创建了 Stream 组件，使用 componentDidMount() 方法初始化了
外部的 snapkite-stream-client JavaScript 库。  

让我们看一下这个组件的 componentDidMount() 方法。     
在 StreamTweet 组件的 componentDidMount() 方法后面添加如下代码：   

	componentDidMount: function(){
    	console.log('[Snapterest] StreamTweet: 3. Running componentDidMount()');

        var componentDOMRepresentation = ReactDOM.findDOMNode(this);
        window.snapterest.headerHtml = componentDOMRepresentation.children[0].outerHTML;
        window.snapterest.tweetHtml = componentDOMRepresentation.children[1].outHTML;
    },

这里，我们使用 ReactDOM.findDOMNode() 方法引用代表 StreamTweet 组件的 DOM 。  
我们传递 this 作为参数来引用当前的组件 (在这个例中是 StreamTweet) 。    
componentDOMRepresentation 变量指向 DOM 树，我们可以遍历并访问它的各种属性。  
为了更好地了解这个 DOM 树的样子，让我们仔细看看 StreamTweet 组件的 render() 方法：  

	render: function(){
    	console.log('[Snapterest] StreamTweet: Running render()');

        return (
        	<section>
            	<Header text={this.state.headerText} />
                <Tweet
                	tweet={this.props.tweet}
                    onImageClick={this.props.onAddTweetToCollection}
            </section>
        );
    }

使用 JSX 的最大好处之一就是只需要通过观察组件的 render() 方法，可以很容易地确定组件中有多少子元素。  
这里，我们可以看到一个父元素 <section /> 有两个子元素 <Header /> 和 <Tweet /> 。     

所以当我们使用 DOM  API 的 children 属性来遍历 DOM 树时，我们可以确定它有以下两个子元素。    

 - componentDOMRepresentation.children[0]: 表示 <Header /> 组件的 DOM 
 - componentDOMRepresentation.children[1]: 表示 <Tweet /> 组件的 DOM 

每个元素的 outerHTML 属性获取 HTML 字符串，这个字符串表示每个元素的DOM 树。  
为了方便，我们在全局 window.snapterest 对象中引用这个 HTML 字符串，正如 前面讨论的。  

如果你使用其他 JavaScript 库，例如将 jQuery 和 React 一起使用，  
那么使用 componentDidMount() 方法是整合两者的一个机会。  
如果你想发送一个 AJAX 请求，或者使用 setTimeout()、setInterval() 设置定时器，那么也可以在这个方法中操作。  

**一般来说，componentDidMount() 应该是整合 React 库和非 React 库时应该优先选择的组件生命周期方法。**  

到目前为止，我们已经学习了 React 组件提供的基本挂载方法。在 StreamTweet 组件中使用了提到的所有三个方法。  
我们也讨论了 StreamTweet 的 render() 方法。  
这些知识是我们理解 React 将怎么样初始渲染 StreamTweet 组件所需要知道的。  
在它第一次渲染时，React 将按顺序执行以下方法：  

 - 1 getInitialState()
 - 2 componentWillMount()
 - 3 render()
 - 4 componentDidMount()

这些方法在 React 组件的挂载阶段被调用。它们仅执行一次，除非我们卸载组件并再次挂载它。  

接下来，让我们来讨论一下 React 组件的卸载阶段。  

### 卸载方法

现在让我们看一个常用的卸载方法  

#### componentWillUnmount() 方法

在这个阶段 React 仅提供一个方法，那就是 componentWillUnmount() 。  

它在 React 即将从 DOM 中删除并销毁组件之前被调用。  

对于清理组件在安装或更新阶段创建的所有数据，这个方法是很有用的。  

这正是我们在 StreamTweet 组件中要做的。在 StreamTweet 组件的 componentDidMount() 方法后面添加这段代码：    

	componentWillUnmount: function(){
    	console.log('[Snapterest] StreamTweet: 8. RunningcomponentWillUnmount()');

        delete window.snapterest;
    },

在 componentWillUnmount() 方法中，我们使用 delete 操作符删除全局 window.snapterest 对象：  

	delete window.snapterest;

删除 window.snapterest 将会保证你的全局对象不被污染。  

如果你已经在 componentDidMount() 方法中创建了任何额外的 DOM 元素，  
那么 componentWillUnmount() 是删除它们的最好地方。  

在整合 React 组件和其他 JavaScript API 时，
你可以将 componentDidMount() 和 componentWillUnmount() 方法想象成一个两步机制：  

 - 1 在 componentDidMount() 方法中初始化它们
 - 2 在 componentWillUnmount() 方法中结束它们

这种方式可以使需要操作 DOM 的第三方 JavaScript 库与 React 渲染的 DOM 保持同步。    

这些就是我们所需要知道的关于有效地卸载 React 组件的内容。   

