### 设置 React 组件的默认属性

StreamTweet 组件会渲染两个子组件：Header 和 Tweet 。      

现在让我们创建这两个组件。切换到 ~/snapterest/source/components/ 目录，并创建 Header.react.js 文件 。    

	var React = require('react');

    var headerStyle = {
    	fontSize: '16px',
        fontWeight: '300',
        display: 'inline-block',
        margin: '20px 10px'  
    };

    var Header = React.createClass({
    	getDefaultProps: function(){
        	return {
            	text: 'Default header'
            };
        },
        render: function(){
        	return (
            	<h2 style={headerStyle}>{this.props.text}</h2>
            );
        }
    });

    module.exports = Header;

Header 组件是一个无状态的组件，它渲染了 h2 元素。  
头部文本是通过 this.props.text 属性从父组件传递而来，这使得组件更加灵活，
允许我们在任何需要头部组件的地方重复使用它。我们将在后面再一次使用这个组件。    

注意这个 h2 元素有一个 style 属性。    

在 React 中，我们可以使用 JavaScript 对象来定义 CSS 规则，然后将这个对象作为 React 组件 style 属性的值。
比如，在这个组件中，我们定义 headerStyle 变量来指向一个对象：        

 - 对象的每个 key 作为 CSS 属性
 - 对象的每个 value 作为 CSS 属性值

如果 CSS 属性名中包含连字符，需要将其转换为驼峰大小写格式。  
比如，将 font-size 转换为 fontSize ，将 font-weight 转换为 fontWeight ，等等。    

将 CSS 规则定义在 React 组件中有如下一些好处。   

 - 移植：可以很容易地将组件与它的样式一起分享出去，因为它都在一个 JavaScript 文件中。
 - 封装：将样式内联可以限制它所影响的范围。
 - 灵活：可以使用 JavaScript 来计算 CSS 规则

使用这项技术的明显的缺点是 CSP (Content Security Policies，内容安全策略) 会阻止内联样式生效。  
关于 CSP 的更多内容请访问 
https://developer.mozilla.org/en-US/docs/Web/Security/CSP/Introducing_Content_Security_Policy。  

Header 组件有一个方法，我们还没有讨论过，它就是 getDefaultProps() 。    

如果你忘记传递 React 组件依赖的属性会怎么样？组件可以使用 getDefaultProps() 方法设置一个默认值，比如：    

	getDefaultProps: function(){
     	return {
          	text: 'Default header'
    	};
	},

在这个例子中，我们将 text 属性的默认值设置为 'Default header' 。  
如果父组件传递了 this.props.text 属性，那么默认值将会被覆盖。    

接下来，让我们创建 Tweet 组件，切换到 ~/snapterest/source/components/ 目录，并创建 Tweet.react.js 文件  

	var React = require('react');

    var tweetStyle = {
    	position: 'relative',
        	display: 'inline-block',
          	width: '300px',
          	height: '400px',
          	margin: '10px'
    };

    var imageStyle = {
    	maxHeight: '400px',
        boxShadow: '0px 1px 1px 0px #aaa',
        border: '1px solid #fff'
    };

    var Tweet = React.createClass({
    	propTypes: {
        	tweet: function(properties, propertyName, componentName) {
        		var tweet = properties[propertyName];

        		if (!tweet){
        			return new Error('Tweet must be set.');
        		}

        		if (!tweet.media){
        			return new Error('Tweet must have an image.');
        		}
			},
    		onImageClick: React.PropTypes.func
    	},
        handleImageClick: function(){
        	var tweet = this.props.tweet;
            var onImageClick = this.props.onImageClick;

            if (onImageClick){
            	onImageClick(tweet);
            }
        },
        render: function(){
        	var tweet = this.props.tweet;
            var tweetMediaUrl = tweet.meida[0].url;

            return (
            	<div style={tweetStyle}>
                	<img src={tweetMediaUrl} onClick={this.handleImageClick} style={imageStyle} />
                </div>
            );
        }
	});

    module.exports = Tweet;

这个组件渲染一个 div 元素和它的子元素 img 。  
两个元素都有行内样式，并且 img 元素还有一个点击事件处理程序，就是 this.handleImageClick:  

	handleImageClick: function (){
     	var tweet = this.props.tweet;
     	var onImageClick = this.props.onImageClick;

     	if (onImageClick){
          	onImageClick(tweet);
     	}
	},

当用户点击推文的图片时，Tweet 组件检查父组件是否传递了 this.props.onImageClick 回调函数作为属性，
并调用这个函数。this.props.onImageClick 是一个可选的 Tweet 组件属性，所以在使用它之前，
需要检查它是否被传递过来了。另一方面，tweet 是必要属性。  

我们如何才能确保组件接收所有必要属性？    