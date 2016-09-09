### 创建第一个有状态的React组件 

有状态组件最合适处理交互逻辑和管理状态。  

它们让你更容易推理出应用程序是怎样工作的。这种推理对于创建可维护的应用程序有关键的作用。  

React 把组件的状态保存在 this.state 中，初始值是 getInitialState() 函数的返回值。  
然而，这取决于我们告诉 React getInitialState() 函数返回什么。我们将这个函数添加到组件中：  

	{
    	getInitialState: function() {
        	return {
            	isHidden: false
            };
        },

        render: function(){
        	if(this.state.isHidden) {
            	return null;
            }

            return React.createElement('h1', { className: 'header' }, 'React Component');
        }
    }

在这个例子中，getInitialState() 函数返回一个对象，该对象仅有一个值为 false 的 isHidden 属性。  
这是我们的 React 组件和用户界面的初始状态。  

注意在 render() 函数中，我们正在使用 this.state.isHidden 来代替 this.props.isHidden 。  

我们学习了可以通过 this.props 或者 this.state 将数据传递到组件的 render() 函数中。那么这两者有什么不同呢？  

 - this.props 存储的是从父级传递过来的只读数据。它属于父级，并且不能被它的子元素改变。
 	这个数据应该被认为是不可改变的。
 - this.state 存储的数据是组件私有的。它能被组件修改。当 state 更新后组件会自动重新渲染。

怎样更新组件的 state? React 提供了一个通用的方式：使用 setState(data, callback) 更新 state 。  
这个函数有两个参数：  

 - data 参数，表示下一个状态
 - callback 函数，你将很少用到它，因为 React 已经保持了用户界面是最新的。

React 怎么保持用户界面是新的呢？每当你更新组件的状态时，它都会调用组件的 render() 函数，
包括所有子组件在内都会被渲染。事实上，每次调用 render() 函数时它都重新渲染全部虚拟 DOM 。  
 
当调用 this.setState() 函数，并向它传递下一个表示下一状态的数据对象时，React 会将合并下一个状态和当前状态。
在合并过程中，React 会使用下一个状态覆盖当前状态。没有被覆盖的当前状态将成为下一状态的一部分。  
 
想象一下这是我们的当前状态：  
     
	{
    	isHidden: true,
        title: 'Stateful React Component'
    } 

我们调用 this.setState(nextState), nextState 如下：  
     
	{
    	isHidden: false
    }

React 会将两个状态合并成一个新的：  
     
	{
    	isHidden: false,
        title: 'Stateful React Component'
    }

isHidden 属性被更新了，title 属性没有被删除和更新。  

最终，现在我们知道了怎样更新组件的状态，让我们创建一个有状态的组件并对用户事件做出响应：  

	{
    	getInitialState: function(){
        	return {
            	isHeaderHidden: false,
                title: 'Stateful React Component'
            };
        },
        handleClick: function(){
        	this.setState({
            	isHeaderHidden: !this.state.isHeaderHidden
            });
        },
        render: function(){
        	var headerElement = React.createElement('h1', { className: 'header', key: 'header' }, this.state.title);
            var buttonElement = React.createElement('button', { className: 'btn btn-default',onClick: this.handleClick, key: 'button' }, 'ToggleHeader');

            if (this.state.isHeaderHidden) {
            	return React.createElement('div', null, [buttonElement]);
            }

            return React.createElement('div', null, [buttonElement, headerElement]);
        }     
	}

在这个例子中，我们创建了一个切换按钮用来显示和隐藏标题。  
首先我们通过 getInitialState() 函数设置了初始状态对象，它有两个属性：   
被设置为 false 的 isHeaderHidden，被设置为 'Stateful React Component' 的 title 。    

我们可以通过 this.state 访问 render() 函数中的 state 对象。  
在 render() 函数内部，我们创建三个 React 元素： h1、button 和 div 。  
div 元素将作为 h1 和 button 的父元素。  
不过，div 元素可能包含两个子元素，headerElement 和 buttonElement，  
也可能仅有一个子元素 buttonElement 。  
到底返回哪个 div ，取决于 this.state.isHeaderHidden 的值。  
组件的当前状态直接影响 render() 函数渲染。  
虽然你已经很熟悉 render() 函数，但在这个例子中还是有一些我们之前没有见过的新内容。   

我们给 ReactComponent 对象添加了一个新属性，叫做 handleClick()，它跟 React 没有什么特别的关系。  
它是业务逻辑的一部分，我们使用它来处理 onClick 事件。  
你可以将自己的属性添加到 ReactComponent 对象上。  
我们可以从这个组件上的其他方法访问组件对象上的属性，通过 this 引用即可。  
例如，在 render() 和 handleClick() 中 都可以通过 this.state 访问 state 对象。   

handleClick() 函数可以做什么呢？  
它通过给 isHeaderHidden 属性设置一个新值来更新组件的状态，该新值为现有的 this.state.isHeaderHidden 取反。  
     
	this.setState({
    	isHeaderHidden: !this.state.isHeaderHidden
    });

handleClick() 函数会响应用户界面做出的动作。  
用户界面是一个用户可以点击的 button 元素，我们可以绑定一个事件处理函数。  
**在 React 中，你可以通过 createElement() 函数的 props 参数来为一些事件处理函数**。  

	React.createElement('button', { className: 'btn btn-default', onClick: this.handleClick }, 'Toggle header');  

**React 使用驼峰命名规范来定义事件处理函数，例如 onClick 。**  

可以在 http://facebook.github.io/react/docs/events.html#supported-events 上查看所有支持的事件。  


默认情况下，React 会在**冒泡阶段**触发事件处理函数，  
但是也可以在事件处理函数名后面添加 Capture 来告诉 React 在**捕获阶段**触发它们，比如 onClickCapture 。    

React 在 SyntheticEvent 对象中包装了一个浏览器原生的事件，  
来保证所有被支持的事件在 Internet Explorer 8 及以上浏览器中表现相同。  

SyntheticEvent 对象提供了与浏览器原生事件相同的 API ，  
这意味着你可以照常使用 stopPropagation() 和 preventDefault() 方法。  

如果因为某些原因而需要访问浏览器原生事件，可以通过 nativeEvent 属性来做。  

如果需要处理 touch 事件，可以简单地调用 React.initializeTouchEvents(true) 。       

注意，在上一个例子中，将 onClick 属性传递到 createElement() 
函数中并不会在渲染后的 HTML 标记中创建内联的事件处理函数：    

	<button class="btn btn-default" data-reactid=".0.$button">Toggle header</button>  

这是因为 React 其实不会将事件处理函数绑定到 DOM 节点本身。  

相反，React 仅在最顶层使用一个事件监听器来监听所有事件，并把它们代理到相应的事件处理函数。   

在前面的例子中，我们学习了怎么创建一个有状态的 React 组件，用户可以通过交互来改变其状态。  
我们创建并绑定了一个事件处理函数处理 click 事件来更新 isHeaderHidden 属性的值。  
但是你有没有注意到，当用户交互时并没有更新存储在 state 中的另一属性 title 的值 ？  
这看上去是不是有些奇怪？state 中的有些数据从没有改变过。  

这引出了一个重要的问题：我们不应该在 state 中放什么？    

问自己一个问题：我们可以从组件的 state 中移除并且不影响用户页面更新的数据有哪些？  

边问自己边删除数据，直到你完全确定在不影响用户界面正常更新的前提下没有任何数据可删了。  

在我们的例子中，state 对象上有一个 title 属性，我们可以把它移动到 render() 函数中，  
并且不会破坏切换按钮的交互性。这个组件依然将按着预期运行：    

	{
    	getInitialState: function(){
        	return {
            	isHeaderHidden: false
            };
        },
        handleClick: function(){
        	this.setState({
            	isHeaderHidden: !this.state.isHeaderHidden
            });
        },
        render: function(){
        	var title = 'Stateful React Component';

            var headerElement = React.createElement('h1', { className: 'header', key: 'header' }, title);
            var buttonElement = React.createElement('button', { className: 'btn btn-default',onClick: this.handleClick, key: 'button' }, 'ToggleHeader');

            if (this.state.isHeaderHidden) {
            	return React.createElement('div', null, [buttonElement]);
            }

            return React.createElement('div', null, [buttonElement, headerElement]);
       }     
	}

相反，如果我们把 isHeaderHidden 属性移动到 state 对象外面，就会破坏组件的交互性，  
因为用户每次点击按钮后，render() 函数**不再会被自动触发**。这是一个破坏交互的例子：      

	{
    	getInitialState: function(){
        	return {};
        },
        isHeaderHidden: false,
        handleClick: function(){
        	this.isHeaderHidden = !this.isHeaderHidden;
        },
        render: function(){
        	var title = 'Stateful React Component';

            var headerElement = React.createElement('h1', { className: 'header', key: 'header' }, title);
            var buttonElement = React.createElement('button', { className: 'btn btn-default',onClick: this.handleClick, key: 'button' }, 'ToggleHeader');

            if (this.isHeaderHidden) {
            	return React.createElement('div', null, [buttonElement]);
            }

            return React.createElement('div', null, [buttonElement, headerElement]);
        }     
	}

这是一个反模式。  

请记住这个经验法则：组件的 state **应该用来存储组件的事件处理函数随时可能会改变的数据**，  
以达到重新渲染并保持组件的用户界面最新的目的。  

将 state 对象中组件状态保持为最小可能表示形式，并在 render() 函数中使用 state 和 props 来计算数据的其余部分。  
无论在 state 中放了什么，都需要自己更新。  

而在 render() 函数中放入的内容都会通过 React 自动更新。好好利用 React 的优势吧。   

