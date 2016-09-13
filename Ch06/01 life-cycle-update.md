## 更新 React 组件  

我们学习了 React 组件可以经历三个阶段：  

 - 挂载阶段
 - 更新阶段
 - 卸载阶段

我们已经讨论了挂载和卸载阶段。在这里，我们会讨论更新阶段。  

在此阶段，React 组件**已经被插入到 DOM 中**。    

这个 DOM 代表组件的当前状态，并且当前状态改变，React 需要评估一下新的状态将如何改变先前渲染的 DOM 。       

React 提供给我们一些方法，用来影响在更新过程中会渲染什么，并让我们知道更新何时发生。  
这些方法允许我们控制从当前组件状态到下一个组件状态的过渡过程。    

下面我们就来学习关于 React 组件更新方法的更多有用特性。    


### 理解组件生命周期的更新方法

React 组件有5个属于组件更新阶段的生命周期方法：  

 - componentWillReceiveProps()
 - shouldComponentUpdate()
 - componentWillUpdate()
 - render()
 - componentDidUpdate()

下图可以帮你更好地总览这5个方法：  

![](http://i.imgur.com/lRaEuOt.png)

我们已经很熟悉 render() 方法了，下面让我们讨论一下其他4个方法。    

### componentWillReceiveProps() 方法

首先我们在 StreamTweet 组件中使用 componentWillReceiveProps() 方法。    
在 StreamTweet.react.js 文件的 componentDidMount() 方法后面添加如下代码：  

	componentWillReceiveProps: function(nextProps){
    	console.log('[Snapterest] StreamTweet: 4. Running componentWillReceiveProps()');

     	var currentTweetLength = this.props.tweet.text.length;
     	var nextTweetLength = nextProps.tweet.text.length;
     	var isNumberOfCharactersIncreasing = (nextTweetLength > currentTweetLength);  
     	var headerText;

     	this.setState({
          	numberOfCharactersIsIncreasing: isNumberOfCharactersIncreasing
     	});

     	if(isNumberOfCharactersIncreasing){
          	headerText = 'Number of characters is increasing';
     	} else {
          	headerText = 'Latest public photo from Twitter'
     	}

     	this.setState({
          	headerText: headerText
     	});

     	window.snapterest.numberOfReceivedTweets ++;
	},

这个方法在组件生命周期的更新阶段被第一个调用，具体来说，就是**当组件从它的父组件接收到新属性时被调用。**  

此方法为我们提供了一个机会，让我们可以使用 this.props 对象和 nextProps 对象来比较当前组件和下一组件的属性。  
根据比较结果，可以选择使用 this.setState() 函数来更新组件的 state，  

**在这种场景下将不会触发额外的渲染。**    

让我们看一下以下代码：  

	var currentTweetLength = this.props.tweet.text.length;
	var nextTweetLength = nextProps.tweet.text.length;
	var isNumberOfCharactersIncreasing = (nextTweetLength > currentTweetLength);
	var headerText;

	this.setState({
     	numberOfCharactersIsIncreasing: isNumberOfCharactersIncreasing
	});

我们首先会获得当前推文和下一条推文的长度。  
当前推文的长度可以通过 this.props.tweet 获得，下一条推文的长度可以通过 nextProps 获得。  
然后比较它们的长度，检查下一条推文是否比当前推文长。  
将比较结果保存在 isNumberOfCharactersIncreasing 变量中。  
最终设置 numberOfCharactersIsIncreasing 属性的值为
变量 isNumberOfCharactersIncreasing 来更新组件的 state 。    

然后设置头部文本如下：  

	if (isNumberOfCharactersIncreasing) {
     	headerText = 'Number of characters is increasing';
	} else {
     	headerText = 'Latest public photo from Twetter';
	}

	this.setState({
     	headerText: headerText
	});

如果下一条推文更长，就将头部文本设置为 'Number of characters is increasing';   
反之，就将头部设置为 'Latest public photo from Twitter'。  
然后再一次通过设置 headerText 属性的值为变量 headerText 来更新组件的 state 。  

注意，我们在 componentWillReceiveProps() 方法中调用了 this.setState() 函数**两次**。  

这是为了说明不管在 componentWillReceiveProps() 方法中调用 this.setState() 多少次，  
**它都不会触发组件额外的渲染**。    

React 做了内部优化，会把状态更新操作放在一起批量执行。   

StreamTweet 组件每次接收到新推文时，componentWillReceiveProps() 方法都会被调用一次，  
因此可以用这个方法来计算接收到的推文总数：     

	window.snapterest.numberOfReceivedTweets ++;   

现在我们知道怎样检测下一条推文是否比当前显示的推文长了，但是我们如何才能选择不渲染下一条推文呢？       

### shouldComponentUpdate() 方法

通过 shouldComponentUpdate() 方法中，我们可以决定组件的下一个状态是否触发组件的重新渲染。  
这个方法返回一个布尔值，默认为 true 。但是也可以使它返回 false ，此时下面的组件方法**都不会被调用**：   

 - componentWillUpdate()
 - render()
 - componentDidUpdate()

**如果跳过对组件的 render() 方法的调用，就会阻止对该组件的重新渲染**，  
这将提高应用程序的性能，因为没有额外的 DOM 改变。  

shouldComponentUpdate() 方法在组件生命周期的更新阶段被第二个调用。  

这个方法可以很好地阻止显示下一条内容少于一个字符的推文。  
在 StreamTweet 组件的 componentWillReceiveProps() 方法后面加入如下代码：  

	shouldComponentUpdate: function(nextProps, nextState){
     	console.log('[Snapterest] StreamTweet: 5. Running shouldComponentUpdate()');

     	return (nextProps.tweet.text.length > 1);
	},

如果下一条推文的长度大于 1 ，shouldComponentUpdate() 会返回 true，  
StreamTweet 组件会渲染下一条推文，否则，shouldComponentUpdate() 返回 false,   
StreamTweet 组件不会渲染下一条推文。

### componentWillUpdate() 方法

componentWillUpdate() 方法在 React 即将更新 DOM 之前被调用。它得到一下两个参数。  

 - nextProps: 下一个属性对象
 - nextState: 下一个状态对象

你可以使用这些参数来准备 DOM 更新。  

**不过，你不能在 componentWillUpdate() 方法中使用 this.setState() 。**  

如果你想更新组件的状态以响应其他属性更改，那么请在 componentWillReceiveProps() 方法中做，
React 会在属性变化时调用它。  

为了演示 componentWillUpdate() 方法什么时候被调用，我们需要在 StreamTweet 组件中记录它。       
在 shouldComponentUpdate() 方法后面添加如下代码：   

	componentWillUpdate: function(nextProps, nextState){
     	console.log('[Snapterest] StreamTweet: 6. Running componentWillUpdate()');
	},

在调用 componentWillUpdate() 方法之后，React 调用 render() 方法来执行 DOM 更新。  
然后 componentDidUpdate() 方法被调用。  

### componentDidUpdate() 方法

componentDidUpdate() 方法在 React **更新 DOM 之后会被立即调用**。它得到如下两个参数。  

 - prevProps: 上一个属性对象
 - prevState: 上一个状态对象

我们将使用这个方法来操作更新后的 DOM 或者执行渲染后的操作。  
在 StreamTweet 组件中，我们将使用 componentDidUpdate() 在全局对象中增加已显示推文的数量。    
在 componentWillUpdate() 方法中添加如下代码：  

	componentDidUpdate: function(prevProps, prevState){
     	console.log('[Snapterest] StreamTweet: 7. Running componentDidUpdate()');

     	window.snapterest.numberOfDisplayedTweets++;
	},

**在 componentDidUpdate() 被调用之后，更新阶段结束。**     

当组件的状态更新或者父组件传来一个新属性时，一个新阶段又开始了。    

或者，调用 forceUpdate() 方法也会触发一个新的更新阶段，
只是在触发更新的组件上会跳过 shouldComponentUpdate() 方法。  

不过，在所有子组件的更新阶段，shouldComponentUpdate() 仍然会像往常一样被调用。  

实践中尽量不要用 forceUpdate() 方法，这样能提高应用程序的可维护性。  

React 组件的生命周期方法就讨论到这里了。  

