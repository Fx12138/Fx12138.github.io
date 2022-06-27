---
title: nodejs
date: 2021-07-21 14:52:42
tags: node;前端;后端
---

# 模块系统

## 引入文件

在一个文件中引用另一个文件的方式

在index.js中

```js
//加载自己的文件,相对路径前必须加'./',否则会认为是核心模块
require('./a.js')
```

在a.js中

```js
console.log('a文件被执行了')
```

输入命令node index.js后,运行index.js文件,会输出'a文件被执行了'

## 作用域

node中没有全局作用域,只有模块作用域,即变量只能在当前文件中使用.

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



## mongoose

### 新增数据

```js
const mongoose = require('mongoose');
var Schema = mongoose.Schema
//连接数据库
mongoose.connect('mongodb://localhost:27017/flower');

//设计集合结构
var roomSchema = new Schema({
    roomId: String,
    roomInfo: {
        status: Number, //开局状态,0未开局,1已开局,2比牌等待选择
        activeUser: {
            id: Number,
            username: String,
        },
        lastWinner: {
            id: Number,
        },
        coinPool: Number,
        gamesNumber: Number,
        bottomCoin: Number,
    },
    flowerUserList: [
        {
            id: Number,
            avatar: String,
            username: String,
            card: [{
                color: String,
                number: Number,
                name: String,
                order: Number,
                power: Number,
                path: String,
            }],
            cardType: Number,
            coin: Number,
            isDown: Number,
            cardStatus: Number, //是否看牌 0未看,1看了
            liveStatus: Number, //是否弃牌或输 0输,1活着
        },
    ]
})

//将文档结构发布为模型
const flowerRoom = mongoose.model('FlowerRoom', roomSchema);

//添加数据
const room = new flowerRoom({
    roomId: 111111,
    roomInfo: {
        status: 0, //开局状态,0未开局,1已开局,2比牌等待选择
        activeUser: {
            id: null,
            username: "",
        },
        lastWinner: {
            id: 0,
        },
        coinPool: 0,
        gamesNumber: 1,
        bottomCoin: 1,
    },
    flowerUserList: [
        {
            id: 0,
            avatar:
                "https://img1.baidu.com/it/u=3583591450,2292153595&fm=26&fmt=auto&gp=0.jpg",
            username: "等待玩家",
            card: [],
            cardType: null,
            coin: 0,
            isDown: 0,
            cardStatus: 0, //是否看牌 0未看,1看了
            liveStatus: 0, //是否弃牌或输 0输,1活着
        }
    ]
});
room.save().then(() => console.log('保存成功'));
```

### 查询

```javascript
console.log(flowerRoom.find());
console.log(flowerRoom.find({ roomId: "123456" }));
```

### 删除数据

```js
FlowerRoom.remove({ roomId: "111111" }, () => {
    console.log('删除成功');
})
```

