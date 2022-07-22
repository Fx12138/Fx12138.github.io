---
title: nodejs
date: 2021-07-21 14:52:42
tags: node;前端;后端
---

# node特点

事件驱动,异步编程.它提供的绝大多是api都是基于事件的,异步的风格.开发人员需要根据自己的业务逻辑注册相应的回调函数,这些回调函数是异步执行的,这意味着虽然开起来是依次注册的,但是并不依赖于出现的顺序执行.在服务器开发中,并发请求处理是个大问题,阻塞式函数会导致资源浪费和时间延迟.通过时间注册,异步函数可以提高资源的利用率和性能

# 模块系统

## 模块间的通信

require方法有两个作用:

​	1.加载文件模块并执行里面的代码

​	2.拿到被加载文件模块导出的接口对象

在每个文件模块中都提供了一个对象exports,默认是一个空对象,require方法拿到的就是这个exports,我们要把所有需要被外部访问的成员挂载到这个exports里面.如:

在a.js文件中

```js
console.log('a文件被执行了')
exports.foo = 'a文件中的foo'
```

在index.js文件中

```js
//加载自己的文件,相对路径前必须加'./',否则会认为是核心模块
var a = require('./a.js')
console.log(a)
```

输入命令node index.js后,运行index.js文件,会输出'a文件被执行了'和{ foo: 'a文件中的foo' }

这样就能在index.js中取到a.js中的变量了

一个例子

```js
var PI = Math.PI;
exports.area = function (r) {
return PI * r * r;
};
exports.circumference = function (r) {
return 2 * PI * r;
};
```

将这个文件存为circle.js，并新建一个app.js文件

```js
var circle = require('./circle.js');
console.log( 'The area of a circle of radius 4 is' + circle.area(4));
```

## 模块载入策略

node.js模块分为两类,一类为原生(核心)模块,一类为文件模块

原生模 块在Node.js源代码编译的时候编译进了二进制执行文件，加载的速度最快。另 一类文件模块是动态加载的，加载速度比原生模块慢。但是**Node.js对原生模块和文件模块都进行了缓存，于是在第二次require时，是不会有重复开销的**。其 中原生模块都被定义在lib这个目录下面，文件模块则不定性。

通过命令行加载启动的文件几乎都为文件模块。

### 文件模块

实际上在文件模块中，又分为3类模块。这三类文件模块以后缀来区 分，Node.js会根据后缀名来决定加载方法。

-  .js。通过fs模块同步读取js文件并编译执行。
-  .node。通过C/C++进行编写的Addon。通过dlopen方法进行加载。 
-  .json。读取文件，调用JSON.parse解析加载。

Node.js在编译js文件的过程中实际完成的步骤有对js文件内容进行**头尾包装**。以app.js为例，包装之后的app.js将会 变成以下形式：

```js
(function (exports, require, module, __filename,
__dirname) {
var circle = require('./circle.js');
console.log('The area of a circle of radius 4
is ' + circle.area(4));
});

```

这段代码会通过vm原生模块的runInThisContext方法执行（类似eval，只是具有 明确上下文，不污染全局），返回为一个具体的function对象。最后传入 module对象的exports，require方法，module，文件名，目录名作为实参并执行。

在这个主文件中，可以通过require方法去引入其余的模块。而其实这个require 方法实际调用的就是load方法。 load方法在载入、编译、**缓存了module**后，返回module的exports对象。这就 是circle.js文件中只有定义在exports对象上的方法才能被外部调用的原因。

### require方法中文件查找策略

<img src="F:\code\fx\learn\node.assets\require流程.jpg" alt="require流程" style="zoom:80%;" />



这里我们将详细描述查找**文件模块**的过程

module path这个Node.js中的概念。对于每一个被加载的文件模块，创建这个模块对象的时候，这个模块便会有一个 paths属性，其值根据当前文件的路径计算得到。我们创建modulepath.js这样一 个文件，其内容为：

```js
console.log(module.paths);
```

我们将其放到任意一个目录中执行node modulepath.js命令，将得到以下的输 出结果。

```
[ 'c:\\nodejs\\node_modules', 'c:\\node_modules'
]
```

可以看出module path的生成规则为：从当前文件目录开始查找node_modules 目录；然后依次进入父目录，查找父目录下的node_modules目录；依次迭代， 直到根目录下的node_modules目录。

除此之外还有一个全局module path，是当前node执行文件的相对目录（../../lib/node）。如果在环境变量中设置了HOME目录和NODE_PATH目录的 话，整个路径还包含NODE_PATH和HOME目录下的.node_libraries与.node_modules。其最终值大致如下：

```
[NODE_PATH，HOME/.node_modules，HOME/.node_libraries，execPath/../../lib/node]
```

<img src="F:\code\fx\learn\node.assets\文件寻找过程.png" alt="文件寻找过程" style="zoom: 80%;" />

简而言之，如果require绝对路径的文件，查找时不会去遍历每一个 

node_modules目录，其速度最快。其余流程如下： 

\1. 从module path数组中取出第一个目录作为查找基准。 

\2. 直接从目录中查找该文件，如果存在，则结束查找。如果不存在，则 进行下一条查找。 

\3. 尝试添加.js、.json、.node后缀后查找，如果存在文件，则结束查找。 如果不存在，则进行下一条。 

\4. 尝试将require的参数作为一个包来进行查找，读取目录下的 package.json文件，取得main参数指定的文件。 

\5. 尝试查找该文件，如果存在，则结束查找。如果不存在，则进行第3条 查找。 

\6. 如果继续失败，则取出module path数组中的下一个目录作为基准查找，循环第1至5个步骤。 

\7. 如果继续失败，循环第1至6个步骤，直到module path中的最后一个 值。 

\8. 如果仍然失败，则抛出异常。 

整个查找过程十分类似原型链的查找和作用域的查找。所幸Node.js对路径查找实现了缓存机制，否则由于每次判断路径都是同步阻塞式进行，会导致严重的性能消耗。 

### 包结构

而NPM的 出现则是为了在CommonJS规范的基础上，实现解决包的安装卸载，依赖管理，版本管理等问题。require的查找机制明了之后，我们来看一下包的细节。

由上文的require的查找过程可以知道，Node.js在没有找到目标文件时，会将当前目录当作一个包来尝试加载，所以在package.json文件中最重要的一个字段就是main。

## node.js模块与前端模块异同



# http服务

```js
const http = require('http')
const hostname = '127.0.0.1'
const port = 3000

//这就是个服务器
var server = http.createServer()

//监听request请求事件
//当请求过来时,会自动触发request事件,然后执行回调函数
server.on('request', (req, res) => {
    // res.statusCode = 200
    //设置文件类型和编码方式,解决乱码问题
    res.setHeader("Content-type", "text/plain;charset=utf-8")
    //响应内容
    // res.write('hello my name is fx')
    //响应完成后必须end,才表示告诉客户端,我的话说完了,可以传递给用户
    // res.end()
    //也可以发送响应数据同时结束响应
    // res.end('hello my name is fx')

    var stu = {
        name: '张三',
        age: 12
    }
    //根据不同的请求路径发送不同的响应结果
    var url = req.url
    if (url == '/a') {
        res.end(JSON.stringify(stu))
    } else if (url == '/b') {
        res.end('这是b页面')
    } else {
        res.end('没有找到')
    }

})

//监听,启动服务
//回调函数是当服务启动后会调用这个函数
server.listen(port, hostname, () => {
    console.log('服务器已经运行....');

})
```

# express

## router模块提取

除了入口文件外,新建一个router.js的文件用来管理路由.在router.js中

```javascript
var express = require('express')
var router = express.Router()

//一系列的此类路由
router.get('/', function (req, res) {
    res.send('hello express哈哈哈哈哈')

})

//导出
module.exports = router
```

接下来在入口文件中接收并挂载即可

```js
var express = require('express')
var router = require('./router')
var app = express()
app.use(router)	
```


