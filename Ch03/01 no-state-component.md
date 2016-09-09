## 创建你的第一个 React 组件

我们学习了怎样创建 React 元素和怎样使用它来渲染 HTML 标记。
我们也已经知道使用 JSX 生成 React 元素多么容易实现。
至此，我们已经足够了使用 React 来创建静态 Web 页面的原理了。
然而这并不是我们决定学习 React 的原因。
我们不想仅仅使用静态 HTML 元素来创建网站，而是希望建立交互式用户界面，让它可以响应用户事件和服务器事件。  

响应事件是什么意思？怎样能让一个静态 HTML 元素做出响应？怎么能让一个 React 元素做出响应？  
下面将通过介绍 React 组件来回答上述及其他相关问题。

### 无状态与有状态

做出响应的意思是从一个状态转换到另一个状态。  

这意味着你首先需要有一个状态，并且有改变这种状态的能力。  

我们在 React **元素**中提到过**状态**或者改变这种状态的能力了吗？  
没有，**它们是无状态的**。  

其唯一目的是构建和渲染虚拟 DOM 元素。  
事实上，只要传入的参数相同，我们就希望它们以完全相同的方式渲染，这样才好预测它们。  
这也是使用 React 库的关键好处之一 ---- 方便理解 Web 应用的工作方式。  

怎么给一个无状态的 React 元素添加状态呢？  

如果我们**不能把状态封装在 React 元素中**，那么就需要把 React 元素**封装到一个已经有状态的东西里**。    

假设我们使用一个简单的**状态机**来代表**用户界面**，那么每个**用户行为**都会**触发状态机状态的改变**。  

**每个状态由不同的 React 元素表示**。  

在 React 中，这个状态机就是 React 组件。  

### 创建第一个无状态的 React 组件
 
我们通过下面这个例子看看怎么样创建 React 组件：
   
	var React = require('react');
    var ReactDOM = require('react-dom');

    var ReactClass = React.createClass({
    	render: function(){
        	return React.createElement('h1', { className: 'header' }, 'React Component');
        }
    });

    var reactComponentElement = React.createElement(ReactClass);
    var reactComponent = ReactDOM.render(reactComponentElement, document.getElementById('react-application'));

你应该对这个例子中的一些代码已经很熟悉了，其余的部分可以分为简单的三步：  

 - 1 创建 React 类
 - 2 创建 React 组件元素
 - 3 创建 React 组件

让我们来仔细看一下怎么创建组件：  

 - 1 调用 React.createClass() 函数并定义一个规范对象作为其参数，以创建 ReactClass 。
 	在这里，我们的重点是详细学习这个对象。
 - 2 调用 React.createElement() 函数并将 ReactClass 作为其 type 参数，
 	以创建 ReactComponentElement 。前面介绍过 type 参数的类型即可以是字符串也可以是 ReactClass 。
 	这里将讲述关于 ReactClass 的更多内容。
 - 3 调用 ReactDOM.render() 函数并将 ReactComponentElement 作为其参数，以创建 ReactComponent 。

作为参数传递给 React.createClass() 的 **规范对象** 是用来定义组件外观的地方，是定义 React 组件的规范。  
后面会把 **规范对象** 称为 **React 组件**，我们将继续学习这个非常重要的概念。  

规范对象封装组件的状态并描述怎样渲染组件。  

最基本的，React 组件需要有一个 render() 方法，因此它至少会返回 null 或者 false 。  

下面是最简单的规范对象：  
     
	{
    	render: function(){
        	return null;
        }
    }

如你所想，render() 函数负责告诉 React 怎样渲染这个 React 组件。  
它可以返回 null，就像上面的例子，这时不会渲染任何内容。  
它也可以返回 ReactElement ，前面已经介绍了怎样创建 ReactElement 了：  

	{
    	render: function(){
        	return React.createElement('h1', { className: 'header' }, 'React Component' );
        }
    }

这个例子展示了如何将 React 元素封装到 React 组件中。  
这里的 ReactElement 的 type 参数是 h1 ，有一个属性对象，以及唯一的子元素 ReactText 。  
然后，调用 React 组件的 render() 方法时返回它。  

事实上，将 React 元素封装到一个 React 组件中并不影响它将如何被渲染：   
     
	<h1 class="header">React Component</h1>

正如你所看到的，这里生成的 HTML 标记与没有使用 React 组件时是一样的。  

在这种情况下，你可能想知道，既然没有 render() 函数也可以来渲染出同样的标记，那么多一个它又有什么好处呢？  

好处是：与任何其他函数一样，有了这个 render() 函数就能在返回值之前选择返回什么值。  
到目前为止，你已经看到 render() 函数的两个例子：一个返回 null，另一个返回 React 元素。  
我们可以合并两者，并添加一个条件判断来决定渲染什么：  

	{
    	render: function(){
        	var elementState = {
            	isHidden: true
            };

            if (elementState.isHidden) {
            	return null;
            }

            return React.create('h1', { className: 'header' }, 'React Component' )
        }
    }

在这个例子中，我们定义了 elementState 变量，它引用了一个具有 isHidden 属性的对象。  
这个对象担当 React 组件的状态。如果我们想隐藏 React 元素，需要设置 elementState 的值。  
当 isHidden 为 true 时，render() 函数将返回 null 。在这种情况下，React 将什么都不渲染。  
逻辑上，设置 elementState.isHidden 为 false ，将返回我们的 React 元素，并渲染出预期的 HTML 标记。  

你可能会问一个问题：怎么设置 elementState.isHidden 的值为 false 或 true 呢？或者通常怎么改变它的值 ？  

让我们想一想在什么情景下我们可能想要改变这个状态。  

一种是当用户与用户界面有交互行为时，另一种是当服务器端发送数据时，  
或者当过了一段时间后我们想渲染一些其他内容时。  

render() 函数不知道这些事件，而且也不应该知道，因为它唯一的作用是根据传递的数据返回一个 React 元素。  
我们怎么把数据传递给它？  

使用 React API 有两种方法可以将数据传递给 render() 函数：  

 - this.props
 - this.state

this.props 应该看起来很熟悉，我们曾学习过 React.createElement () 函数接受 props 参数。  
我们使用它将属性传递给 HTML 元素，但并没有讨论过传递属性之后会发生什么，以及为什么属性传递给 props 对象后会被渲染。  
 
放在 props 对象中的任何数据传递给 React.createElement() 函数后，  
可以通过 this.props 在 render() 函数中都访问到。  
只要可以从 this.props 访问到数据，就可以渲染它：  

	{
    	render: function(){
        	var elementState = {
            	isHidden: true
            };

            if (elementState.isHidden) {
            	return null;
            }

            return React.createElement('h1', { className: 'header' }, this.props.header );
        };
    } 

在这个例子中，我们在 render() 函数中使用 this.props 访问 header 属性。  
然后将 this.props.header 直接作为一个字符串子元素传递给 React.createElement() 函数。  

在上面的例子中，我们可以将 isHidden 的值作为 this.props 对象的另一个属性来传递：

	{
    	render: function(){
        	if (this.props.isHidden) {
            	return null;
            }

            return React.createElement('h1', { className: 'header' }, this.props.header);
        }
    }

也可以使用 this.props 来处理需要渲染的数据：  

	{
    	render: function(){
        	if( this.props.isHidden) {
            	return null;
            }

            var header = this.props.tweets.length + ' Latest Tweets';

            return React.createElement('h1', { className: 'header' }, header);
        }
    }

如你所见，我们通过 this.props.tweets 访问推文数组并获得了它的长度。  
然后，我们在长度后面再连接 'Latest Tweets' 字符串。  
返回的字符串存储在变量 header 中，这是处理后的子字符串元素，我们将它传给 React.createElement () 函数。  

注意在上一个例子中，我们通过 this.props 传递 isHidden，而不是将它存储在 render() 函数中。  
我们移除了 elementState 对象，因为在 render() 函数中不需要关心状态。  

它是一个纯函数，即**它不应该改变状态或者访问真实 DOM ，或以其他方式与浏览器进行交互**。  

记住，我们也许会在服务器上使用 React (服务器没有浏览器)，而无论什么环境我们都希望 render() 函数得到相同的结果。  

如果 render() 函数不管理状态，那该怎么管理呢？我们怎么设置状态？  
在 React 中处理用户或者浏览器事件时又怎么更新状态呢？  

前面，我们学习了在 React 中可以通过 React 组件来表示 UI 。React 组件分两种：    

 - 有状态
 - 无状态

 
等等，不是说 React 组件是状态机吗？没错，每个状态机确实需要一个状态。    

**然而尽可能地保持一些 React 组件无状态是更好的做法**。  
 
**React 组件是可组合的**。  

换句话说，React 组件可以分层。比如，我们有一个父组件，它有两个子组件，而每个子组件又分别有另外两个子组件。
所有组件都是有状态，它们可以管理自己的状态：    

![](http://i.imgur.com/Q1Rc3o7.png)      

如果顶层组件更新了状态，想要在层级中找出最终要渲染的子组件容易吗？不容易。  

有一种设计模式可以消除这种不必要的复杂度。  
思想是把组件分为两个关注点：如何处理用户界面的交互逻辑和如何呈现数据。  

 - 在 React 组件中，只有少数是有状态的。它们应该位于组件层级中的顶部。
 	它们封装了所有的交互逻辑，管理用户界面状态，然后使用 props 将状态向下传递到无状态的组件。
 - 大多数的 React 组件都是无状态的。它们通过 this.props 从它们的父组件接收状态数据并渲染数据。

在前面的例子中，我们通过 this.props 接收到 isHidden 状态数据，然后渲染数据，**这个组件是无状态的**。    

接下来，让我们创建一个有状态的组件。  

