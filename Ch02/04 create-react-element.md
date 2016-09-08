### 使用 JavaScript 创建 React 元素

首先来了解一些基本的 React 术语。这些术语能够帮助我们清楚地了解 React 是由什么组成的。  
随着时间的推移这些术语可能会更新，可以经常关注官方文档
          
	http:// facebook.github.io/react/docs/glossary.html

就像 DOM 是一个节点树一样，React 的虚拟 DOM 也是一个 React 节点树，
React中的一个重要的概念是 ReactNode，它是虚拟 DOM 的基本构件，可以是以下任意一种核心类型。  

 - ReactElement : 它是 React 中的基础类型，是 DOM 中 Element 的一种轻量、无状态、不可变的虚拟表示形式。
 - ReactText: 它是一个数字或者字符串，它代表了文本内容，是 DOM 中的文本节点的虚拟表示形式。

**ReactElement 和 ReactText 都是 ReactNode** 。  

**一个 ReactNode 的数组称为 ReactFragment** 。   
 
我们先来实践一个 ReactElement 的例子。    

1 将以下代码添加到 ~/snapterest/source/app.js 文件中：  
     
	var reactElement = React.createElement('h1');
    ReactDOM.render(reactElement, document.getElementById('react-application'));
     
2 现在你的 app.js 文件应该是这样：
     
	var React = require('react');
    var ReactDOM = require('react-dom');
    var reactElement = React.createElement('h1');
    ReactDOM.render(reactElement, document.getElementById('react-application'));

3 切换到 ~/snapterest/ 目录并执行 Gulp 的 default 任务：
         
	gulp
         
你可能会看到这样的输出：
              
	Starting 'default'...
    Finished 'default' after 1.73 s

4 切换到 ~/snapterest/build/ 目录，并且在浏览器中打开 index.html 文件，你将看到一个空白的 Web 页面。
打开浏览器的开发者工具审查这个空白页面的 HTML 元素，可以看到其中一行的内容是：  
     
	<h1 data-reactid=".0"></h1>

至此我们已经创建了第一个 React 元素，看一下我们是如何做到的。  

React 库的入口点是 React 对象，这个对象有一个叫做 createElement() 的方法，
这个方法有三个参数，type、props 和 children:  

	React.createElement(type, props, children);

让我们来详细看看每一个参数。

#### type 参数

type 参数必须是一个字符串或者是一个 ReactClass:

 - 如果是字符串，则必须是一个 HTML 标签名，例如 'div'、'p'、'h1' 等。React 支持所有通用的 HTML 标签和属性。
 - ReactClass 是通过 React.createClass() 方法创建的。

type 参数描述了 HTML 标签或者 ReactClass 将会怎样被渲染，在我们上面的例子中，我们会渲染 h1 HTML 标签。
 
#### props 参数

props 参数是一个 JavaScript 对象，它会被从父元素传递到子元素 (而不是相反)， 它具有一些不可变的属性。  

当我们通过 React 来创建 DOM 元素时，可以通过 props 对象的属性来传递表示 HTML 的属性，如 class、style 等。  
例如下面这个例子：  

	var React = require('react');
    var ReactDOM = require('react-dom');
    var reactElement = React.crateElement('h1', {className: 'header'});
    ReactDOM.render(reactElement, document.getElementById('react-application'));

上面这段代码会创建一个 class 属性值为 header 的 h1 HTML 元素 :  

	<h1 class="header" data-reactid=".0"></h1>

请注意，我们这里的属性名称是 className ，而不是 class 。  
这是因为 class 是 JavaScript 关键字。  
如果使用 class 作为属性名，它会被 React 忽略，并且在浏览器的控制台输出以下警告：  

	Warning:Unknown DOM property class.Did you mean className?
    Use className instead.

你可能想知道为什么在 h1 标签上会有 data-reactid=".0" 属性？  
我们并没有向 props 对象传递它，那么它是从哪里来的呢？  
它是被 React 添加并用来跟踪 DOM 节点的，它可能会在 React 未来的版本中被移除。  

#### children 参数

children 参数描述了这个元素应当含有的子元素，如果子元素存在的话。  
子元素可以是任何类型的 ReactNode ，
例如由 ReactElement 表示的虚拟 DOM 元素、由 ReactText 表示的字符串或数字，
或者 ReactFragment，即多个 ReactNode 的数组。  

让我们来看看下面这个例子。

	var React = require('react');
    var ReactDOM = require('react-dom');
    var reactElement = React.createElement('hi', { className: 'header' }, 'This is React');
    ReactDOM.render(reactElement, document.getElementById('react-application'));

上面这段代码会创建一个 h1 HTML 标签，它有一个 className 属性和一个内容为 This is React 的文本节点：

	<h1 class="header" data-reactid=".0">This is React</h1>

在这里，h1 标签就相当于是一个 ReactElement，而字符串 This is React 相当于一个 ReactText 。   

接下来，我们再来创建一个 React 元素，并且创建一些 React 元素作为它的子元素：   
     
	var React = require('react');
    var ReactDOM = require('react-dom');
    var h1 = React.createElement('h1', { className: 'header', key: 'header' }, 'This is React');
    var p = React.createElement('p', { className: 'content', key: 'content' }, "And that's how it works.");
    var reactFragment = [h1, p]; 
    var section = React.createElement('section', { className: 'container' }, reactFragment);

    ReactDOM.render(section, document.getElementById('react-application'));

我们创建了 h1、p 和 section 这三个 React 元素，
h1 和 p 都有它们各自的子节点文本 "This is React" 和 "And that's how it works." ，
section 有一个名为 reactFragment 的子节点，它是一个拥有 h1 和 p 两个 ReactElement  的数组，
也是一个 ReactNode 的数组。    

reactFragment 数组里面的每个 ReactElement 必须有一个 key 的属性，
这个属性用来帮助 React 识别不同的 ReactElement 。因此，我们可以得到下面的 HTML 标记：  

	<section class="container" data-reactid=".0">
    	<h1 class="header" data-reactid=".0.$header">This is React</h1>
        <p class="content" data-reactid=".0.$content">And that's how it works.</p>
    </section>

现在我们知道了如何创建 React 元素，如果我们想要创建多个统一类型的 React 元素呢？    
是不是需要一遍又一遍地重复调用 React.createElement('type') 方法呢？  
这样做是可行的，但我们并不需要这样做，  
因为 React 提供了一个名为 React.createFactory() 的工厂函数，工厂函数就是用来创建其他函数的函数。  

确切地说，React.createFactory(type) 这个工厂函数会创建一个函数，该函数创建一个确定类型的 ReactElement 。  

思考下面的例子：  

	var React = require('react');
    var ReactDOM = require('react-dom');

    var listItemElement1 = React.createElement('li', { className: 'item-1' , key: 'item-1' }, 'Item-1' );
    var listItemElement2 = React.createElement('li', { className: 'item-2' , key: 'item-2' }, 'Item-2' );
    var listItemElement3 = React.createElement('li', { className: 'item-3' , key: 'item-3' }, 'Item-3' );

    var reactFragment = [listItemElement1, listItemElement2, listItemElement3];
    var listOfItems = React.createElement('ul', {className: 'list-of-items'}, reactFragment);

    ReactDOM.render(listOfItems, document.getElementById('react-application'));

它会产生出这样的 HTML:    

	<ul class="list-of-items" data-reactid=".0">
    	<li class="item-1" data-reactid=".0.$item-1">Item 1</li> 
        <li class="item-1" data-reactid=".0.$item-1">Item 1</li>
        <li class="item-1" data-reactid=".0.$item-1">Item 1</li>
    </ul>

我们可以通过创建工厂函数来简化这个例子：

	var React = require('react');
    var ReactDOM = require('react-dom');

    var createListItemElement = React.createFactory('li');
    var listItemElement1 = createListItemElement ( { className: 'item-1' , key: 'item-1' }, 'Item-1' );
    var listItemElement2 = createListItemElement ( { className: 'item-2' , key: 'item-2' }, 'Item-2' );
    var listItemElement3 = createListItemElement ( { className: 'item-3' , key: 'item-3' }, 'Item-3' );

    var reactFragment = [listItemElement1, listItemElement2, listItemElement3];
    var listOfItems = React.createElement('ul', {className: 'list-of-items'}, reactFragment);

    ReactDOM.render(listOfItems, document.getElementById('react-application'));

在上面的例子中，我们先调用了 React.createFactory() 函数，并给它传入了 li 这个 HTML 标签名以作为类型参数。
接着 React.createFactory() 函数返回一个新的函数，我们可以使用这个新的函数方便快捷地创建 li 类型的元素。
我们将这个函数命名为 createListItemElement，然后调用这个函数三次，
每次只需给它传入 props 和 children 这两个参数。  

注意，React.createFactory() 和 React.createElement() 是一样的，
都可以传入 HTML 标签字符串 (例如 li) 或者 ReactClass 对象类型的参数。  

React 提供了一些内置的工厂函数来创建通用的 HTML 标签，可以在 React.DOM 对象下调用它们， 
例如 React.DOM.ul() 、React.DOM.li() 、React.DOM.div() 等。  

使用这些函数可以使上面的例子更加简洁：  

	var React = require('react');
    var ReactDOM = require('react-dom');

    var listItemElement1 = React.DOM.li ( { className: 'item-1' , key: 'item-1' }, 'Item-1' );
    var listItemElement2 = React.DOM.li ( { className: 'item-2' , key: 'item-2' }, 'Item-2' );
    var listItemElement3 = React.DOM.li ( { className: 'item-3' , key: 'item-3' }, 'Item-3' );

    var reactFragment = [listItemElement1, listItemElement2, listItemElement3];
    var listOfItems = React.createElement('ul', {className: 'list-of-items'}, reactFragment);

    ReactDOM.render(listOfItems, document.getElementById('react-application'));

好，现在我们已经知道怎样创建一个 ReactNode 树了，但是，在开始后面的内容之前必须先讨论一下下面这行重要的代码：    
     
	ReactDOM.render(listOfItems, document.getElementById('react-application'));

可能你已经猜到了，它将我们的 ReactNode 树渲染成了 DOM ，让我们仔细看看它是怎么运作的。

