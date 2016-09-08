### 使用 JSX 来创建 React 元素

当我们通过反复调用 React.createElement() 方法来创建虚拟 DOM 时，
将这种反复调用直观转化为具有层次结构的 HTML 标签会变得很难。  

不要忘记，虽然我们使用的是虚拟 DOM ，但仍然要为内容和 UI 创建一个结构化的布局。  
如果看一眼 React 代码就能知道页面布局的话岂不是更爽？  

JSX 是一个可选的类似 HTML 的语法，通过它，我们不使用 React.createElement() 就可以创建虚拟 DOM 树。  

先看看前面的这个没有使用 JSX 语法的例子：  

	var React = require('react');
    var ReactDOM = require('react-dom');

    var listItemElement1 = React.DOM.li ( { className: 'item-1' , key: 'item-1' }, 'Item-1' );
    var listItemElement2 = React.DOM.li ( { className: 'item-2' , key: 'item-2' }, 'Item-2' );
    var listItemElement3 = React.DOM.li ( { className: 'item-3' , key: 'item-3' }, 'Item-3' );

    var reactFragment = [listItemElement1, listItemElement2, listItemElement3];
    var listOfItems = React.createElement('ul', {className: 'list-of-items'}, reactFragment);

    ReactDOM.render(listOfItems, document.getElementById('react-application'));

我们在这个例子中使用 JSX 语法，如下：  
     
	var React = require('react');
    var ReactDOM = require('react-dom');

    var listOfItems = <ul className="list-of-items">
    					<li className="item-1">Item 1</li>
                        <li className="item-2">Item 2</li>
                        <li className="item-3">Item 3</li>
                      </ul>

    ReactDOM.render(listOfItems, document.getElementById('react-application'));

正如你所看到的，JSX 允许我们在 JavaScript 代码中使用类似 HTML 的语法。  
更重要的是，我们现在可以清晰地看到渲染后的 HTML 布局结构。  
而且只有在 "无效的" JavaScript 代码被解释之前，JSX 语法才能转换成有效的 JavaScript 语法。  

我们安装了可以将 JSX 代码转换为 JavaScript 的 babelify 模块，
这个转换在每次执行 gulpfile.js 文件的 default 任务时都会发生：  
     
	gulp.task('default', function(){
    	return browserify('./source/app.js')
        	.transform(babelify, { presets: ["react"] } )
            .bundle()
            .pipe(source('snapterest.js'))
            .pipe(gulp.dest('./build/'))
    });

在 Babel 6.0 之后，如果要转换 JSX 语法，需要首先运行 npm install --save-dev babel-preset-react 
安装 react 相关的插件，然后代码也要由 .transfofrm(babelify) 改成 
.transform(babelify, { presets: ["react"] } ) 。  

正如你所看到的，.transform(babelify, { presets: ["react"] } ) 
函数会在 JSX 与其他 JavaScript 代码合并之前将 JSX 代码转换为 JavaScript 代码。   

执行下面的命令来测试转换效果：  

	gulp

然后，切换到 ~/snapterest/build/ 目录，在浏览器中打开 index.html 文件可以看到一个有三个元素的列表。  

React 团队提供了一个在线的 JSX 转换工具，可以使用它来了解 JSX 是怎么工作的，访问地址：  

	http://facebook.github.io/react/jsx-compiler.html

刚开始使用 JSX ，你可能会觉得不习惯，但它是一个非常直观和方便的工具。而且你可以选择是否使用它。  

