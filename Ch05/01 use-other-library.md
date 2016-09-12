## 结合其他库来使用 React 组件

React 是一个用来构建用户界面的库。如果我们想要整合其他库来负责接收数据该怎么做呢？  
前面，我们列出了 Snapterest 应用程序应该完成的5个任务，并确定其中4个跟用户界面相关，
而另一个是完全关于数据接收的：实时从 Snapkite 服务器接收推文。  

现在，我们将学习如何整合 React 与外部 JavaScript 库，并学习 React 组件的生命周期方法，
与此同时解决接收数据的重要任务。    

### 在 React 组件中使用其他库

前面讨论过，Snapterest 应用程序将接收实时的推文流。  

之前安装的 Snapkite Engine 库就是用来连接 Twitter Streaming API ，过滤传入的推文，并将推文发到应用程序的。  
反过来，应用程序需要一种连接实时数据流的方法，并监听新推文。     

幸运的是，我们不需要自己实现这个功能，而是可以复用另一个 Snapkite 模块：snapkite-stream-client 。    
让我们安装这个新模块。  

切换到 ~/snapterest 目录并执行下面的命令：   

	npm install --save snapkite-stream-client

这个命令将安装 snapkite-stream-client 模块，并作为一个依赖模块将它添加到 package.json 文件。     

现在我们准备在一个 React 组件中重用这个 snapkite-stream-client 模块。  
     
我们创建的 Application 组件有两个子组件： Stream 和 Collection 。在这里我们将创建 Stream 组件。    

让我们开始创建 ~/snapterest/source/components/Stream.react.js 文件：  

	var React = require('react');
    var SnapkiteStreamClient = require('snapkite-stream-client');
    var StreamTweet = require('./StreamTweet.react');
    var Header = require('./Header.react');

    var Stream = React.createClass({
		getInitialState: function(){
        	return {
            	tweet: null
            }
        },
        componentDidMount: function(){
        	SnapkiteStreamClient.initializeStream(this.handleNewTweet);
        },
        componentWillUnmount: function(){
        	SnapkiteStreamClient.destroyStream();
        },
        handleNewTweet: function(tweet){
        	this.setState({
            	tweet: tweet
            });
        },
        render: function(){
        	var tweet = this.state.tweet;
               
            if(tweet){
            	return (
                	<StreamTweet
                    	tweet={tweet}
                        onAddTweetToCollection={this.props.onAddTweetToCollection} />
                );
            }

            return (
            	<Header text="Waiting for public photos from Twitter..." />
            );
        }
	});

    module.exports = Stream;     
    
首先引入 Stream 组件所依赖的下列模块。  

 - React: React库
 - StreamTweet 和 Header: React 组件
 - snapkite-stream-client: 工具库

然后定义 React 组件。以下是 Stream 组件要实现的方法：  

 - getInitialState()
 - componentDidMount()
 - componentWillUnmount()
 - handleNewTweet()
 - render()

我们已经很熟悉 getInitialState() 和 render() 方法了，它们是 React API 的一部分。  
我们也已经知道任何 React 组件都必须至少实现 render() 方法。  
让我们看一下 Stream 组件的 render() 方法：  

	render: function(){
    	var tweet = this.state.tweet;

        if (tweet){
        	return (
            	<StreamTweet
                	tweet={tweet}
                    onAddTweetToCollection={this.props.onAddTweetToCollection} />
            );
        }

        return (
        	<Header text="Waiting for public photos from Twitter..." />
        );
   }

正如你所看到的，我们创建了一个新变量 tweet 引用组件 state 对象的 tweet 属性。  
然后，我们检查这个变量是否引用了一个真实的 tweet 对象。  
如果是，render() 方法返回 StreamTweet 组件，否则返回 Header 组件。      

StreamTweet 组件渲染一个头部和一条来自流的新推文，而 Header 组件仅渲染一个头部。    

你有没有注意到 Stream 组件自己不渲染任何内容，而是返回另外两个组件中的一个来做实际的渲染？  
Stream 组件的目的是封装应用程序的逻辑并将渲染委托到其他 React 组件中。  
在 React 中，至少应该有一个组件来封装应用程序的逻辑、存储，并管理应用程序的 state 。  
这个组件通常是根组件或者组件层级中较高级别的组件。  
如果可能，其他 React 子组件都不需要有 state 。  
如果把所有 React 组件看成视图 (View) ，那么 Stream 组件就是一个控制器视图 (ControllerView) 。      

现在我们知道 Stream 组件渲染什么了，让我们讨论一下其他方法：  

	getInitialState: function(){
    	return {
        	tweet: null
        }
    }

getInitialState() 方法返回包含一个 tweet 属性的初始 state 对象，值为 null 。  
Stream 组件将不断接收新推文，每次接到新推文后需要重新渲染它的子组件。  
为了实现这一点，我们需要在组件的 state 中存储当前的推文。  
而更新 state 之后，React 将调用 render() 方法重新渲染所有子组件。  
为了这个目的，我们来实现 handleNewTweet 方法：      
 
	handleNewTweet: function(tweet){
    	this.setState({
        	tweet: tweet
        });
    },
 
handleNewTweet() 方法接收一个 tweet 对象，并将该对象赋值给组件 state 的 tweet 属性。     

新推文来自哪里？什么时候来？让我们看一下 componentDidMount() 方法：    

	componentDidMount: function(){
    	SnapkiteStreamClient.initializeStream(this.handleNewTweet);
    },

这个函数调用 SnapkiteStreamClient 对象的 initializeStream() 方法，  
并传入 this.handleNewTweet 回调函数作为参数。  
SnapkiteStreamClient 是一个外部库，我们可以使用它的 API 来初始化推文流。  
每当 SnapkiteStreamClient 接收到推文后，都会调用 this.handleNewTweet 函数。    

为什么我们将这个方法命名为 componentDidMount()? 其实这不是我们命名的，是 React 命名的。  
事实上，componentDidMount() 方法是一个 React API，一个 React 组件生命周期方法。  

**它仅仅被调用一次，在组件完成初始化渲染之后执行。**  

在这个时候，React 已经创建了 DOM 树，由我们的组件表示，此时可以使用其他 JavaScript 库来访问这个 DOM 树。      

**componentDidMount() 最适合用来整合 React 和其他 JavaScript 库。**    

我们在这里使用外部的 SnapkiteStreamClient 库来连接推文流 。    

现在我们知道了在 React 组件中何时初始化外部 JavaScript 库。  
但是反过来说，我们在 componentDidMount() 方法中做的事情应该在什么时候销毁和清理干净？  
一个好办法是在卸载组件之前清理所有内容。  
为此，React API 提供了另一个声明周期方法： componentWillUnmount() 。     

	componentWillUnmount: function(){
    	SnapkiteStreamClient.destrodyStream();
	},

componentWillUnmount() 方法在组件即将卸载之前被调用。  
在 componentWillUnmount() 方法中，我们调用 SnapkiteStreamClient 对象的 destroyStream() 方法。  
destroyStream() 会清理与 SnapkiteStreamClient 的连接，然后就能安全卸载 Stream 组件了。  

你可能会问，什么是组件的生命周期方法？为什么会需要它呢 ？      

