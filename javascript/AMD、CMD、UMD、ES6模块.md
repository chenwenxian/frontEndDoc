
# AMD、AMD、UMD、ES6模块

##javascript模块化
[前端模块化开发那点历史](https://github.com/seajs/seajs/issues/588)<br />
* 模块化：
	- 是指在解决某个复杂、混杂问题时，依照一种分类的思维把问题进行系统性的分解以之处理。模块化是一种处理复杂系统分解为代码结构更合理，可维护性更高的可管理的模块的方式。
	- 模块化是软件系统的属性，这个系统被分解为一组高内聚，低耦合的模块。那么在理想状态下我们只需要完成自己部分的核心业务逻辑代码，其他方面的依赖可以通过直接加载被人已经写好模块进行使用即可。

* 模块化系统所必须的能力：
    1. 定义封装的模块。
    2. 定义新模块对其他模块的依赖。
    3. 可对其他模块的引入支持。



好了，思想有了，那么总要有点什么来建立一个模块化的规范制度吧，不然各式各样的模块加载方式只会将局搅得更为混乱。那么在JavaScript中出现了一些非传统模块开发方式的规范 CommonJS的模块规范，AMD（Asynchronous Module Definition），CMD（Common Module Definition）等。


> 为了建立一个模块化的规范制度、模块加载方式。在JavaScript中出现了一些非传统模块开发方式的规范 CommonJS的模块规范，AMD（Asynchronous Module Definition）、CMD（Common Module Definition）、ES6模块诞生了。

## AMD规范（与Requirejs）
> AMD(Asynchronous Module Definition)异步模块定义，所有的模块将被异步加载，模块加载不影响后面语句运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

 AMD规范定义了一个自由变量或者说是全局变量 define 的函数。
### define( id?, dependencies?, factory );    
 [AMD规范](https://github.com/amdjs/amdjs-api/wiki/AMD)
  
* 第一个参数 id 为字符串类型，表示了模块标识，为可选参数。若不存在则模块标识应该默认定义为在加载器中被请求脚本的标识。如果存在，那么模块标识必须为顶层的或者一个绝对的标识。
* 第二个参数，dependencies ，是一个当前模块依赖的，已被模块定义的模块标识的数组字面量。
* 第三个参数，factory，是一个需要进行实例化的函数或者一个对象。

##### 创建模块标识为 alpha 的模块，依赖于 require， export，和标识为 beta 的模块<br />
------
```javascript
	define("alpha", [ "require", "exports", "beta" ], function( require, exports, beta ){
	    export.verb = function(){
	        return beta.verb();
	        // or:
	        return require("beta").verb();
	    }
	});
```
##### 一个返回对象字面量的异步模块<br />
------
```javascript
	define(["alpha"], function( alpha ){
	    return {
	        verb : function(){
	            return alpha.verb() + 1 ;
	        }
	    }
	});
```
##### 无依赖模块可以直接使用对象字面量来定义<br />
------
```javascript
	define( {
	    add : function( x, y ){
	        return x + y ;
	    }
	} );
```
###  require();
[require API](https://github.com/amdjs/amdjs-api/wiki/require)
在 AMD 规范中的 require 函数与一般的 CommonJS中的 require 不同。由于动态检测依赖关系使加载异步，对于基于回调的 require 需求强烈。
###  局部 与 全局 的require
局部的 require 需要在AMD模式中的 define 工厂函数中传入 require。

```javascript
	define( ['require'], function( require ){
	  // ...
	} );
	or：
	define( function( require, exports, module ){
	  // ...
	} );
```
  局部的 require 需要其他特定的 API 来实现。<br />
    全局的 require 函数是唯一全局作用域下的变量，像 define一样。全局的 require 并不是规范要求的，但是如果实现全局的 require函数，那么其需要具有与局部 require 函数 一样的以下的限定：<br />
    1. 模块标识视为绝对的，而不是相对的对应另一个模块标识。<br />
    2. 只有在异步情况下，require的回调方式才被用来作为交互操作使用。因为他不可能在同步的情况下通过 require(String) 从顶层加载模块。<br />
    依赖相关的API会开始模块加载。如果需要有互操作的多个加载器，那么全局的 reqiure 应该被加载顶层模块来代替。
 
 ```javascript
 	require(String)
	define( function( require ){
	    var a = require('a'); // 加载模块a
	} );
	
	require(Array, Function)
	define( function( require ){
	    require( ['a', 'b'], function( a,b ){ // 加载模块a b 使用
	        // 依赖 a b 模块的运行代码
	    } ); 
	} );
	
	require.toUrl( Url )
	define( function( require ){
	    var temp = require.toUrl('./temp/a.html'); // 加载页面
	} );

 ```  
### RequireJS
[官网](http://www.requirejs.org/)<br />[API]( http://www.requirejs.org/docs/api.html)<br />
[API 中文](http://blog.csdn.net/sanxian_li/article/details/39394097)<br />
RequireJS 是一个前端的模块化管理的工具库，遵循AMD规范，它的作者就是AMD规范的创始人 James Burke。<br />
```
页面顶层<script>标签含有一个特殊的属性data-main，require.js使用它来启动脚本加载过程，而baseUrl一般设置到与该属性相一致的目录。下列示例中展示了baseUrl的设置：
```
```
	<script data-main="scripts/main.js" src="scripts/require.js"></script>
```
> defined用于定义模块，RequireJS要求每个模块均放在独立的文件之中。按照是否有依赖其他模块的情况分为独立模块和非独立模块。

##### 1. 独立模块，不依赖其他模块。直接定义：
------
```javascript
	define({
	    method1: function(){},
	    method2: function(){}
	});
	//等价于
	define(function(){
	    return{
	        method1: function(){},
	        method2: function(){}
	    }
	});
```
##### 2. 非独立模块，对其他模块有依赖。
------
```javascript
	define([ 'module1', 'module2' ], function(m1, m2){
	    //代码...
	});
	//等价于
	define( function( require ){
    	var m1 = require( 'module1' ),
          m2 = require( 'module2' );
	    //代码...
	});
```

define 和 require 这两个定义模块，调用模块的方法合称为AMD模式，定义模块清晰，不会污染全局变量，清楚的显示依赖关系。AMD模式可以用于浏览器环境并且允许非同步加载模块，也可以按需动态加载模块。

```javascript

	/***
	**** require方法调用模块
	****  在加载 foo 与 bar 两个模块之后执行回调函数实现具体过程。
	***/
	require( ['foo', 'bar'], function( foo, bar ){
	    foo.func();
	    bar.func();
	} );
	
	/***
	**** 在define定义模块内部进行require调用模块
	***/
	define( function( require ){
	    var m1 = require( 'module1' ),
	        m2 = require( 'module2' );
	    //代码...
	});
```
## CMD规范（与Seajs）
> 在CMD中，一个模块就是一个文件，格式为：
[CMD模块定义规范]（https://github.com/seajs/seajs/issues/242）

### define( factory );

全局函数define，用来定义模块。<br />
参数 factory  可以是一个函数，也可以为对象或者字符串。<br />
当 factory 为对象、字符串时，表示模块的接口就是该对象、字符串。
##### 定义JSON数据模块：
----
```javascript
	define({ "foo": "bar" });
```
##### 通过字符串定义模板模块：
----
```javascript
	define('this is {{data}}.');
```
##### factory 函数
----
```javascript
	//表示模块的构造方法，执行构造方法便可以得到模块向外提供的接口。
	define( function(require, exports, module) { 
	    // 模块代码
	});
```
### define( id?, deps?, factory );
define也可以接受两个以上的参数，字符串id为模块标识，数组deps为模块依赖：

```javascript
	define( 'module', ['module1', 'module2'], function( require, exports, module ){
	    // 模块代码
	} );
```
其与 AMD 规范用法不同。<br />
require 是 factory 的第一个参数。<br />
require( id );<br />
接受模块标识作为唯一的参数，用来获取其他模块提供的接口：<br />

```javascript
	define(function( require, exports ){
	    var a = require('./a');
	    a.doSomething();
	});
```
### require.async( id, callback? );
require是同步往下执行的，需要的异步加载模块可以使用 require.async 来进行加载：

```javascript
	define( function(require, exports, module) { 
	    require.async('.a', function(a){
	        a.doSomething();
	    });
	});
```
###  require.resolve( id )
 可以使用模块内部的路径机制来返回模块路径，不会加载模块。
 
### exports 是 factory 的第二个参数，用来向外提供模块接口。
```javascript
	define(function( require, exports ){
	    exports.foo = 'bar'; // 向外提供的属性
	    exports.do = function(){}; // 向外提供的方法
	});
	
	//使用 return 直接向外提供接口。
	define(function( require, exports ){
	    return{
	        foo : 'bar', // 向外提供的属性
	        do : function(){} // 向外提供的方法
	    }
	});
	
	//简化为直接对象字面量的形式:
	define({
	    foo : 'bar', // 向外提供的属性
	    do : function(){} // 向外提供的方法
	});
```
### module 是factory的第三个参数，为一个对象，上面存储了一些与当前模块相关联的属性与方法。
module.id 为模块的唯一标识。<br />
module.uri 根据模块系统的路径解析规则得到模块的绝对路径。<br />
module.dependencies 表示模块的依赖。<br />
module.exports 当前模块对外提供的接口。

## Seajs
[官网文档](https://seajs.github.io/seajs/docs/)<br />
[仓库代码](https://github.com/seajs/seajs)<br />
[SeaJS API快速参考](https://github.com/seajs/seajs/issues/266)<br />
> 官网（http://seajs.org/）已经关闭。

>  其define 与 require 使用方式基本就是CMD规范中的示例。

sea.js 核心特征：
1. 遵循CMD规范，与NodeJS般的书写模块代码。
2. 依赖自动加载，配置清晰简洁。
兼容 Chrome 3+，Firefox 2+，Safari 3.2+，Opera 10+，IE 5.5+。

##### seajs.use 
用来在页面中加载一个或者多个模块

```javascript
	// 加载一个模块 
	seajs.use('./a');
	// 加载模块，加载完成时执行回调
	
	seajs.use('./a'，function(a){
	    a.doSomething();
	});
	
	// 加载多个模块执行回调
	seajs.use(['./a','./b']，function(a , b){
	    a.doSomething();
	    b.doSomething();
	});
```

## AMD 与 CMD 区别
> 玉伯对于 AMD 与 CMD 区别的解释：<br />
> AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。<br />
> CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。<br />
> 类似的还有 CommonJS Modules/2.0 规范，是 BravoJS 在推广过程中对模块定义的规范化产出

1. 对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。
2. CMD 推崇依赖就近，AMD 推崇依赖前置。看代码：
3. 
```javascript
	// CMD
	define(function(require, exports, module) {
	    var a = require('./a');
	    a.doSomething();
	    // 此处略去 100 行
	    var b = require('./b'); // 依赖可以就近书写
	    b.doSomething();
	    
	    // 代码...
	})
	
	// AMD 默认推荐的是
	define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
	    a.doSomething();
	    // 此处略去 100 行
	    b.doSomething();
	    
	    // 代码...
	})
```
> 虽然 AMD 也支持 CMD 的写法，同时还支持将 require 作为依赖项传递，但 RequireJS 的作者默认是最喜欢上面的写法，也是官方文档里默认的模块定义写法。

3. AMD 的 API 默认是一个当多个用，CMD 的 API 严格区分，推崇职责单一。比如 AMD 里，require 分全局 require 和局部 require，都叫 require。CMD 里，没有全局 require，而是根据模块系统的完备性，提供 seajs.use 来实现模块系统的加载启动。CMD 里，每个 API 都简单纯粹。
4.  还有一些细节差异，具体看这个规范的定义就好，就不多说了。
另外，SeaJS 和 RequireJS 的[差异](https://github.com/seajs/seajs/issues/277)


## UMD
[UMD](https://github.com/umdjs)<br />
[AMD、CMD、UMD 模块](http://web.jobbole.com/82238/)<br />
既然CommonJs和AMD风格一样流行，似乎缺少一个统一的规范。所以人们产生了这样的需求，希望有支持两种风格的“通用”模式，于是通用模块规范（UMD）诞生了。

```javascript
	(function (root, factory) {
	    if (typeof define === 'function' && define.amd) {
	        // AMD
	        define(['jquery'], factory);
	    } else if (typeof exports === 'object') {
	        // Node, CommonJS之类的
	        module.exports = factory(require('jquery'));
	    } else {
	        // 浏览器全局变量(root 即 window)
	        root.returnExports = factory(root.jQuery);
	    }
	}(this, function ($) {
	    //    方法
	    function myFunc(){};
	 
	    //    暴露公共方法
	    return myFunc;
	}));
	
	//复杂、依赖了多个组件并且暴露多个方法
	(function (root, factory) {
	    if (typeof define === 'function' && define.amd) {
	        // AMD
	        define(['jquery', 'underscore'], factory);
	    } else if (typeof exports === 'object') {
	        // Node, CommonJS之类的
	        module.exports = factory(require('jquery'), require('underscore'));
	    } else {
	        // 浏览器全局变量(root 即 window)
	        root.returnExports = factory(root.jQuery, root._);
	    }
	}(this, function ($, _) {
	    //    方法
	    function a(){};    //    私有方法，因为它没被返回 (见下面)
	    function b(){};    //    公共方法，因为被返回了
	    function c(){};    //    公共方法，因为被返回了
	 
	    //    暴露公共方法
	    return {
	        b: b,
	        c: c
	    }
	}));
```


## ES6模块
[ES6](http://es6.ruanyifeng.com/)<br />
[Module 的语法](https://github.com/ruanyf/es6tutorial/blob/d7a3efd7b784496eed5af8579d28abe08680d1ee/docs/module.md)<br />
ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

```javascript
	// ES6模块
	import { stat, exists, readFile } from 'fs';
```
上面代码的实质是从fs模块加载3个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载。

### ES6模块好处
1. 不再需要[UMD](https://github.com/umdjs)模块格式了，将来服务器和浏览器都会支持 ES6 模块格式。目前，通过各种工具库，其实已经做到了这一点。
2. 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者navigator对象的属性。
3. 不再需要对象作为命名空间（比如Math对象），未来这些功能可以通过模块提供。



## 扩展阅读：
[AMD规范文档](https://github.com/amdjs/amdjs-api/wiki/AMD)<br />
amdjs 的 require [接口文档](https://github.com/amdjs/amdjs-api/wiki/require)<br />
amdjs 的[接口文档](https://github.com/amdjs/amdjs-api/wiki)<br />
RequireJS[官网接口文档](http://www.requirejs.org/docs/api.html)<br />
[模块系统](https://github.com/seajs/seajs/issues/240)<br />
[前端模块化开发的价值](https://github.com/seajs/seajs/issues/547)<br />
[前端模块化开发那点历史](https://github.com/seajs/seajs/issues/588)<br />
[CMD 模块定义规范 ](https://github.com/seajs/seajs/issues/242)<br />
[SeaJS API快速参考](https://github.com/seajs/seajs/issues/266)<br />
[从 CommonJS 到 Sea.js](https://github.com/seajs/seajs/issues/269)<br />  
[RequireJS和AMD规范](http://javascript.ruanyifeng.com/tool/requirejs.html)<br />
[CommonJS规范](http://javascript.ruanyifeng.com/nodejs/module.html)<br />
JavaScript模块化开发 - [CommonJS规范](http://www.feeldesignstudio.com/2013/09/javascript-module-pattern-commonjs) <br />
JavaScript模块化开发 - [AMD规范](http://www.feeldesignstudio.com/2013/09/javascript-module-pattern-amd)<br />

[Javascript模块化编程1](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)<br />
[Javascript模块化编程2](http://www.ruanyifeng.com/blog/2012/10/asynchronous_module_definition.html)<br />
[知乎  AMD 和 CMD 的区别有哪些？](http://www.zhihu.com/question/20351507 ) <br />
[与 RequireJS 的异同](https://github.com/seajs/seajs/issues/277)<br />

[模块化设计](http://baike.baidu.com/view/189730.htm)<br />
[模块化](http://baike.baidu.com/view/182267.htm)<br /> 

[ES6](http://es6.ruanyifeng.com/)<br />
[Module 的语法](https://github.com/ruanyf/es6tutorial/blob/d7a3efd7b784496eed5af8579d28abe08680d1ee/docs/module.md)<br />

