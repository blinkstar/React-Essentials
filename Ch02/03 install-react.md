### 安装 React

在使用 React 之前，我们首先需要安装它。    

这里介绍两种安装方式：最简方式和使用 npm install 命令的安装方式。     

#### 最简方式

最简方式就是直接将下面的 <script> 标签添加到 html 文件中。   
          
在开发环境中使用 React ，添加下面的这段代码：  

	<script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0-beta3/react.js"></script>

在线上环境中使用 React，添加下面的这段代码：  

	<script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0-beta3/react.min.js"></script>

#### npm install 命令的安装方式

Browserify 可以让我们在项目中使用 require() 函数来导入项目依赖的模块。    
同样地，我们也可以使用 require() 把 React 导入到我们的项目中。    
也就是说，使用 npm install  安装 React 可以替代上面介绍的在 html 中添加 <script> 标签的方式。   

1 在根目录下，执行以下命令：
 
	npm install --save-dev react@0.14.0-beta3 react-dom@0.14.0-beta3

2 使用文本编辑器打开 app.js 文件，分别将 React 和 ReactDOM 库导入到 React 和 ReactDOM 变量上：  

	var React = require('react');
    var ReactDOM = require('react-dom');

react 包中包含 React 的核心方法，让我们可以用声明的方式来描述需要渲染的内容。    
react-dom 包提供了渲染 DOM 的方法。    

