### 验证 React 组件的属性

在 React 中，使用组件的 propTypes 对象来验证组件的属性：  

	propTypes: {
     	propertyName: validator
	}

在这个对象中，你需要指定一个属性名和一个用来确定属性是否有效的验证器函数。    
React 提供了一些预置的验证器供我们使用，它们都定义在 React.PropTypes 对象上，如下。  

 - React.PropTypes.number ： 验证属性是否为数字
 - React.PropTypes.string ： 验证属性是否为字符串
 - React.PropTypes.bool ： 验证属性是否为布尔值
 - React.PropTypes.object ： 验证属性是否为对象
 - React.PropTypes.element ： 验证属性是否为 React 元素

要想了解所有 React.PropTypes 验证器，
可以查看 https://facebook.github.io/react/docs/reusable-components.html#prop-validation上的文档。  

默认情况下，使用 React.PropTypes 验证器验证的所有属性都是**可选**的。  

可以把任何一个属性设置为 isRequired，来确保当没有传递该属性的时候会在 JavaScript 控制台显示一条警告信息。  

	propTypes: {
     	propertyName: React.PropTypes.number.isRequired
	}

也可以指定自定义验证器函数，该函数需要在验证失败的时候返回一个 Error 对象：  

	propTypes: {
     	propertyName: function(properties, propertyName, componentName) {
          	// ... 验证失败
          	return new Error('A property is not valid.');
     	}
	}

让我们看一下 Tweet 组件中的 propTypes 对象：  

	propTypes: {
     	tweet: function(properties, propertyName, componentName){
          	var tweet = properties[propertyName];
          
          	if (!tweet) {
               	return new Error('Tweet must be set.');
          	}

          	if (!tweet.media){
               	return new Error('Tweet must have an image.');
          	}
     	},
		onImageClick: React.PropTypes.func
	},

如你所见，我们验证了 Tweet 组件的两个属性： tweet 和 onImageClick 。  

我们使用自定义的验证器函数来验证 tweet 属性。React 给这个函数传递了下面三个参数。  

 - properties: 组件的属性对象
 - propertyName: 我们将要验证的属性名
 - componentName: 组件的名字

首先，我们检查 Tweet 组件有没有收到 tweet 属性：  

	var tweet = properties[propertyName];

	if (!tweet) {
     	return new Error('Tweet must be set.');
	}

然后，我们假设 tweet 属性是一个对象，并检查它有没有 media 属性：  

	if (!tweet.media) {
     	return new Error('Tweet must have an image.');
	}

我们检测如果失败会返回一个 Error 对象并将其打印在 JavaScript 控制台上。  

我们将验证组件的另一个属性 onImageClick:  

	onImageClick: React.PropTypes.func

需要验证 onImageClick 属性的值是否是一个函数。  

在这里，我们重用了 React.PropTypes 对象提供的验证器函数。  

如你所见，onImageClick 是一个可选属性，因为我们没有添加 isRequired 。  

最终，出于性能原因，propTypes 仅在 React 的开发版中使用。  

### 创建 Collection 组件

前面讲过最顶层的 Application  组件有两个子组件 ： Stream 和 Collection 。  

到目前为止，我们已经讨论和实现了 Stream 组件及其子组件。接下来，我们将焦点放在 Collection 组件上。  

创建 ~/snapterest/source/components/Collection.react.js 文件  

	var React = require('react');
    var ReactDOMServer = require('react-dom/server');
    var CollectionControls = require('./CollectionControls.react');
    var TweetList = require('./TweetList.react');
    var Header = require('./Header.react');

    var Collection = React.createClass({
    		createHtmlMarkupStringOfTweetList: function(){
        		var htmlString = ReactDOMServer.renderToStaticMarkup(
            		<TweetList tweets={this.props.tweets} />
            	);

            	var htmlMarkup = {
            		html: htmlString
            	};

            	return JSON.stringify(htmlMarkup);
        	},
        	getListOfTweetIds: function(){
        		return Object.keys(this.props.tweets);
        	},
        	getNumberOfTweetsInCollection: function(){
        		return this.getListOfTweetIds().length;
        	},
          	render: function(){
               	var numberOfTweetsInCollection = this.getNumberOfTweetsInCollection();
               
               	if (numberOfTweetsInCollection > 0){
                	var tweets = this.props.tweets;
                    var htmlMarkup = this.createHtmlMarkupStringOfTweetList();
                    var removeAllTweetsFromCollection = this.props.onRemoveAllTweetsFromCollection;
                    var handleRemoveTweetFromCollection = this.props.onRemoveTweetFromCollection;

                    return (
                    	<div>
                        	<CollectionControls
                            	numberOfTweetsInCollection={numberOfTweetsInCollection}
                                htmlMarkup={htmlMarkup}
                                onRemoveAllTweetsFromCollection={removeAllTweetsFromCollection} />
                            <TweetList
                            	tweets={tweets}
                                onRemoveTweetFromCollection={handleRemoveTweetFromCollection} />
                        </div>
                    );
             }

             return <Header text="Your collection is empty" />
		}
	});

    module.exports = Collection;

Collection 组件负责渲染两类内容：  

 - 用户已经收集的推文
 - 用于操作该集合的用户界面控件

让我们看一下这个组件的 render() 方法：  

	render: function(){
     	var numberOfTweetsInCollection = this.getNumberOfTweetsInCollection();

     	if (numberOfTweetsInCollection > 0){
          	var tweets = this.props.tweets;
          	var htmlMarkup = this.createHtmlMarkupStringOfTweetList();
          	var removeAllTweetsFromCollection = this.props.onRemoveAllTweetsFromCollection;
          	var handleRemoveTweetFromCollection = this.props.onRemoveTweetFromCollection;
          
          	return (
               	<div>
                	<CollectionControls
                    	numberOfTweetsInCollection = {numberOfTweetsInCollection}
                        htmlMarkup = {htmlMarkup}
                        onRemoveAllTweetsFromCollection = {removeAllTweetsFromCollection}  />
                   	<TweetList
                        tweets = {tweets}
                        onRemoveTweetFromCollection = {handleRemoveTweetFromCollection} />
               	</div>
          	);
     	}

     	return <Header text="Your collection is empty" />
	}

首先，我们使用 this.getNumberOfTweetsInCollection() 方法在集合中获得推文的总数：  

	getNumberOfTweetsInCollection: function(){
    	return this.getListOfTweetIds().length;
    },

这个方法使用了另一个方法 this.getListOfTweetIds() 来获得推文的 ID 列表：  

	getListOfTweetIds: function(){
    	return Object.keys(this.props.tweets);
    },

调用 this.getListOfTweetIds() 函数会返回一个推文 ID  的数组，
然后 this.getNumberOfTweetsInCollection() 返回这个数组的长度。  

在 render() 方法中知道了收到的推文数量后，需要做出一个选择：    

 - 如果集合不为空，那么渲染 CollectionControls 和 TweetList 组件
 - 否则，渲染 Header 组件

这些组件渲染什么内容呢？  

 - CollectionControls 组件渲染集合名标题，以及一组允许用户改名字、清空和导出集合的按钮
 - TweetList 组件渲染一个推文列表
 - Header 组件渲染一个标题，包含一条集合为空的信息。

我们的想法是当集合不为空时仅显示集合。在这个例子中，我们创建了四个变量：    

	var tweets = this.props.tweets;
	var htmlMarkup = this.createHtmlMarkupStringOfTweetList();
	var removeAllTweetsFromCollection = this.props.onRemoveAllTweetsFromCollection;
	var handleRemoveTweetFromCollection = this.props.onRemoveTweetFromCollection;

 - tweets 变量引用从父组件传递过来的 tweets 对象
 - htmlMarkup 变量引用一个通过组件的 this.createHtmlMarkupStringOfTweetList() 方法返回的字符串
 - removeAllTweetsFromCollection 和 handleRemoveTweetFromCollection 变量引用从父组件传递过来的函数。

顾名思义，this.createHtmlMarkupStringOfTweetList() 方法通过 TweetList 组件
生成的代表 TweetList 的 HTML 代码创建字符串：  

	createHtmlMarkupStringOfTweetList: function(){
     	var htmlString = ReactDOMServer.renderToStaticMarkup(
          	<TweetList tweets = {this.props.tweets} />
     	);

     	var htmlMarkup = {
          	html: htmlString
     	};

     	return JSON.stringify(htmlMarkup);
	}

createHtmlMarkupStringOfTweetList() 方法使用了前面
介绍的 ReactDOMServer.renderToStaticMarkup() 方法，我们传递 TweetList 组件作为它的参数：  

	var htmlString = ReactDOMServer.renderToStaticMarkup(
    	<TweetList tweets={this.props.tweets} />
    );

TweetList 组件有一个 tweets 属性，它引用了通过父组件传递来的 tweets 属性。   

由 ReactDOMServer.renderToStaticMarkup() 函数生成的 HTML 字符串保存在 htmlString 变量中。  
然后，我们创建一个新 htmlMarkup 对象，它的 html 属性引用 htmlString 变量。  
最后，使用 JSON.stringify() 函数将 htmlMarkup 对象转换成 JSON 字符串。  
createHtmlMarkupStringOfTweetList() 函数的返回结果就是 JSON.stringify(htmlMarkup) 的结果。  

这个方法展示了 React 组件的灵活性，既可以使用相同的 React 组件来渲染 DOM  元素，
也可以生成 HTML 字符串传递给第三方 API 。  

另外一个有趣的发现是，可以在 render() 方法之外使用 JSX 语法。  

事实上，你可以在源码文件的任何地方能够使用 JSX 语法，甚至在 React.createClass 函数外也可以。    

让我们仔细看一下当集合不为空时 Collection 组件返回的是什么：  

	return (
     	<div>
          	<CollectionControls 
               	numberOfTweetsInCollection = {numberOfTweetsInCollection}
               	htmlMarkup = {htmlMarkup}
               	onRemoveAllTweetsFromCollection = {removeAllTweetsFromCollection} />
     
          	<TweetList
               	tweets = {tweets}
               	onRemoveTweetFromCollection = {handleRemoveTweetFromCollection} />
     	</div>
	);

因为 React 只允许有一个根元素，所以我们用 div 元素包裹 CollectionControls 和 TweetList 组件。  
让我们看一下每个组件并讨论一下它们的属性。  

我们将以下三个属性传递给 CollectionControls 组件：  

 - numberOfTweetsInCollection 属性表示当前集合中推文的总数
 - htmlMarkup 属性表示一个 HTML 标记字符串，这个字符串是在组件中
 	使用 createHtmlMarkupStringOfTweetList 方法生成的
 - onRemoveAllTweetsFromCollection 属性引用一个函数，用来删除集合中的所有推文。
 	这个函数在前面讨论程序组件的时候实现过了。

我们给 TweetList 组件传递两个属性：  

 - tweets 属性引用从父级应用程序组件传递来的 tweets
 - onRemoveTweetFromCollection 属性引用一个函数，用来删除存储在应用程序 state 中的一条推文。

这就是 Collection 组件。
