---
title: js总结
date: 2021-10-12 11:06:16
tags: js
---

# 事件

## 事件冒泡

IE 事件流被称为事件冒泡，这是因为事件被定义为**从最具体的元素（文档树中最深的节点）开始触 发，然后向上传播至没有那么具体的元素文档）**。

## 事件捕获

事件捕获的意思是**最不具体的节点应该最先收到事件，而最具体的节点应该最后收到事件**。事件捕获实际上是为了在事件到达最终目标前拦截事件。

## 事件流

事件流分为 3 个阶段：**事件捕获**、**到达目标**和**事件冒泡**。事件捕获最先发生， 为提前拦截事件提供了可能。然后，实际的目标元素接收到事件。最后一个阶段是冒泡，最迟要在这个 阶段响应事件。

<img src="D:\devApp\myblog\source\_posts\js总结\image-20211012111120152.png" alt="image-20211012111120152" style="zoom: 33%;" />

在 DOM 事件流中，实际的目标（div元素）在捕获阶段不会接收到事件。这是因为捕获阶段在到达img之前就结束了。下一阶段，即会在img元素上触发事件的“到达目标” 阶段，通常在事件处理时被认为是冒泡阶段的一部分。然后，冒泡阶段开始，事件反向传播至文档。

## 事件委托

在 JavaScript 中，页面中事件处理程序的数量与页面整体性能直接相关。原 因有很多。**首先，每个函数都是对象，都占用内存空间，对象越多，性能越差**。其次，**为指定事件处理 程序所需访问 DOM 的次数会先期造成整个页面交互的延迟**。

**事件委托利用事件冒泡，可以只使用一个事件 处理程序来管理一种类型的事件**。例如，click 事件冒泡到 document。这意味着可以为整个页面指定 一个 onclick 事件处理程序，而不用为每个可点击元素分别指定事件处理程序。

事件委托的优点:

- document 对象随时可用，任何时候都可以给它添加事件处理程序（不用等待 DOMContentLoaded 或 load 事件）。这意味着只要页面渲染出可点击的元素，就可以无延迟地起作用。 

- 节省花在设置页面事件处理程序上的时间。只指定一个事件处理程序既可以节省 DOM 引用，也 可以节省时间。 

- 减少整个页面所需的内存，提升整体性能。

# 事件循环机制

1. 所有任务都在主线程上执行，形成一个执行栈。
2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"。那些对应的异步任务，结束等待状态，进入执行栈并开始执行。
4. **主线程不断重复上面的第三步**。

## 宏任务与微任务

**宏任务(macrotask)：**：

script(整体代码)、setTimeout、setInterval、UI 渲染、 I/O、postMessage、 MessageChannel、setImmediate

**微任务(microtask)：**

Promise、 MutaionObserver、process.nextTick(Node.js环境）

实例:

```js
console.log(1);
setTimeout(function() {
    console.log(2);
})
var promise = new Promise(function(resolve, reject) {
    console.log(3);
    resolve();
})
promise.then(function() {
    console.log(4);
})
console.log(5);
```

- 上面的示例中，第一次事件循环，整段代码作为宏任务进入主线程执行。
- 遇到了 setTimeout ，就会**等到过了指定的时间后将回调函数放入到宏任务的任务队列中**。
- 遇到 Promise，**将 then 函数放入到微任务的任务队列中**。
- **整个事件循环完成之后，会去检测微任务的任务队列中是否存在任务，存在就执行**。
- 第一次的循环结果打印为: 1,3,5,4。
- 接着再到宏任务的任务队列中按顺序取出一个宏任务到栈中让主线程执行，那么在这次循环中的宏任务就是 setTimeout 注册的回调函数，执行完这个回调函数，发现在这次循环中并不存在微任务，就准备进行下一次事件循环。
- 检测到宏任务队列中已经没有了要执行的任务，那么就结束事件循环。
- 最终的结果就是 1,3,5,4,2。

# 判断数组的六种方法

- **① instanceof 操作符判断**

用法：arr instanceof Array
instanceof 主要是用来判断某个实例是否属于某个对象

```js
let arr = [];
console.log(arr instanceof Array); // true
```

但是 instanceof 会有一个问题，**它的问题在于假定只有一个全局执行的环境**。如果网页中包含多个框架，那实际上就存在**两个以上不同的全局执行环境**，**从而存在两个以上不同版本的Array构造函数**。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有不同的构造函数。

- **②对象构造函数的constructor判断**

用法：arr.constructor === Array
Object的每个实例都有构造函数 constructor，用于保存着用于创建当前对象的函数

```js
let arr = [];
console.log(arr.constructor === Array); // true
```

- ③**Array 原型链上的 isPrototypeOf**

用法：Array.prototype.isPrototypeOf(arr)
Array.prototype  属性表示 Array 构造函数的原型

```js
let arr = [];
console.log(Array.prototype.isPrototypeOf(arr)); // true
```

- ④Object.getPrototypeOf

用法：Object.getPrototypeOf(arr) === Array.prototype
Object.getPrototypeOf() 方法返回指定对象的原型

```js
let arr = [];
console.log(Object.getPrototypeOf(arr) === Array.prototype); // true
```

- ⑤**Object.prototype.toString**

用法：Object.prototype.toString.call(arr) === '[object Array]'

```js
let arr = [];
console.log(Object.prototype.toString.call(arr) === '[object Array]'); // true
```

- ⑥**Array.isArray**

用法：Array.isArray(arr)
ES5中新增了Array.isArray方法,IE8及以下不支持

```js
let arr = [];
console.log(Array.isArray(arr)); // true
```

# 执行上下文和作用域

## 比较let和var

let是块级作用域,var是全局作用域

```js
for(var i = 1;i<=3;i++){
	setTimeout(()=>{
		console.log(i)
	},1000)
}
//输出三个4

for(let i = 1;i<=3;i++){
	setTimeout(()=>{
		console.log(i)
	},1000)
}
//输出1 2 3
```

因为var定义的i是全局作用域的,所以访问的一直都是全局作用域中的i

<img src="D:\devApp\myblog\source\_posts\js总结\image-20211014151927066.png" alt="image-20211014151927066" style="zoom:50%;" />

但是let定义的是块级作用域,所以他访问自己块中的i,而访问不到全局作用域

<img src="D:\devApp\myblog\source\_posts\js总结\image-20211014152239685.png" alt="image-20211014152239685" style="zoom:50%;" />![image-20211014153405807](D:\devApp\myblog\source\_posts\js总结\image-20211014153405807.png)

## 用var模拟出块级作用域

```js
for(var i = 1;i<=3;i++){
	(function(a){
		setTimeout(()=>{
			console.log(a)
		},1000)
	})(i)
}
//输出1 2 3
```

![image-20211014153405807](D:\devApp\myblog\source\_posts\js总结\image-20211014153405807.png)

var虽然没有块级作用域,但是他有函数作用域,每次循环创建一个函数,生成一个函数作用域,这样就和上面的let一样了用自己块中的i

## 执行上下文

试想当 JavaScript 开始要解释执行代码的时候，最先遇到的就是全局代码，所以初始化的时候首先就会向执行上下文栈压入一个全局执行上下文，我们用 globalContext 表示它，并且只有当整个应用程序结束的时候，ECStack 才会被清空，所以程序结束之前， ECStack 最底部永远有个 globalContext.**函数执行上下文只有在函数被调用的时候才会产生函数执行上下文,并压入栈顶**.**执行上下文对象的个数为n+1,其中n为函数被调用的次数,1为全局上下文**

执行环境定义了变量或环境有权访问的其他数据,每个执行环境都有一个与之关联的变量对象,环境中定义的所有变量和函数都保存在这个对象中.每个函数都有自己的执行环境,当**执行流**进入一个函数时函数的环境就会被推到一个环境栈中,而在函数执行完后,栈将其环境弹出,将控制权返还给之前的执行环境.

## 作用域

作用域相对于上下文而言,上下文只有在函数被调用时才会产生,而**作用域在编写代码时就已经确定了,即在函数定义时就已经产生.作用域共有n+1个,其中n为定义函数的个数,1为全局作用域**

## 作用域链

当代码在一个环境中执行时,会创建变量对象的一个作用域链,用途是保证对执行环境有权访问的变量的**有序访问**.作用域链的最前端始终都是当前执行代码所在环境的变量对象.作用域链的下一个变量对象来自包含环境的变量对象,一直到全局执行环境.

## 执行上下文和作用域的区别

区别一

​	*全局作用域之外,每个函数都会创建自己的作用域,作用域在函数定义时就已经确定了,而不是函数调用时

​	*全局执行上下文环境是在全局作用域之后,js代码马上执行之前创建

​	*函数执行上下文环境是在调用函数时,函数体代码执行之前创建

区别二

​	*作用域是静态的,只要函数定义好了就一直存在,且不会再变化

​	*上下文环境是动态的,调用函数时创建,函数调用结束时上下文环境就会被释放

# 闭包

闭包是指有权访问另外一个函数作用域中的变量的函数。

在嵌套函数中, 如果一个内层函数引用外层及更外层的函数当中定义的变量, 那么这个函数拥有包含这些变量数据的闭包. 当该内层函数被返回出去的时候, 其作用域链不会被摧毁.

一个例子

```js
let hd = {
	user:'张三',
	get:function(){
		return function(){
			return this.user;
		}
	}
}
let a = hd.get();
console.log(a());
//输出undefined
```

上面的代码输出undefined,原因是a得到了一个函数,并打印a(),这里函数是在全局作用域中调用的,所以函数中的this就是全局作用域,不会去hd对象中找,所以输出的是undefined.可以改成箭头函数,如下

```js
let hd = {
	user:'张三',
	get:function(){
		return ()=>{
			return this.user
		}
	}
}
let a = hd.get();
console.log(a());
//输出张三
```

这样可以正常输出张三,因为箭头函数的this指向离箭头函数最近的环境.

# 原型

## isPrototypeOf()

b.isPrototypeOf(a)方法检测的是**b是否在a的原型链上**,即b是否是a原型链上的一份子.

## instanceof

b instanceof a 检测的是**b这个构造函数的prototype**是否在a的原型链上

## in与hasOwnProperty()

"name" in person 检测的是"name"属性**是否是person中的属性或者person的原型链中的属性**.

person.hasOwnProperty("name") 只是检测当前对象中是否有这个属性,不会攀至原型链.

# 深浅拷贝

## 浅拷贝

### Object.assign

Object.assign方法当定义的对象只有基本数据类型时就是深拷贝,当有引用类型时就是浅拷贝,下面举例

```js
//当定义的对象只有基本类型时，该方法就是深拷贝。
let a = {
    name: 'voledy',
    age: 18
};
let b = Object.assign({}, a);
b.name = "哇哈哈";
console.log(a);   // a = {name: 'voledy', age: 18};
console.log(b);   // b = {name: '哇哈哈', age: 18};

//当定义的对象中有引用类型的时，该方法就是浅拷贝。
let a = {
    name: 'voledy',
    age: 18,
    eat:{
        type: '苹果',
        price: 18,
    }
};
let b = Object.assign({}, a);
b.name = "哇哈哈";
b.eat.type = "西瓜";
b.eat.price = 30;
console.log(a);  // a = {name: 'voledy', age: 18, eat:{ type: '西瓜', price: '30'}};
console.log(b);  // b = {name: '哇哈哈', age: 18, eat:{ type: '西瓜', price: '30'}};
```

### ES6中扩展运算符

该方法和上述方法一样只能用于深拷贝第一层的值，当拷贝第二层的值时 仍是引用同一个内存地址。

```js
let a = {
    name: 'voledy',
    age: 18
};
let b = {...a};
b.name = "哇哈哈";
console.log(a);   // a = {name: 'voledy', age: 18};
console.log(b);   // b = {name: '哇哈哈', age: 18};

//-----------------------------  分割线  -----------------------------

let a = {
    name: 'voledy',
    age: 18,
    eat:{
        type: '苹果',
        price: 18,
    }
};
let b = {...a};
b.name = "哇哈哈";
b.eat.type = "西瓜";
b.eat.price = 30;
console.log(a);  // a = {name: 'voledy', age: 18, eat:{ type: '西瓜', price: '30'}};
console.log(b);  // b = {name: '哇哈哈', age: 18, eat:{ type: '西瓜', price: '30'}};
```

### Array.prototype.slice() 和 Array.prototype.concat()

该方法只能用于深拷贝第一层的值，当拷贝第二层的值时仍是引用同一个内存地址。

### 总结

浅拷贝会在栈中开辟一个新的内存空间，将原对象一级中的“基本数据类型”复制一份到新的内存空间，所以相互不影响。当对象中有“引用类型”时，它只能拷贝“引用类型”在堆内存中的地址,所以赋值后会影响原对象的值。

## 深拷贝

### JSON.parse(JSON.stringify(obj))

```js
//该方法可以实现深拷贝。 但是需要注意
//1：会忽略undefined，Symbol，函数。
//2：在处理new Date() 会出错
//3：循环引用会出错
//4：不能处理正则，拷贝的是一个空对象
//5：继承的属性会丢失
//一句话概括：可以转成 JSON 格式的对象才能使用这种方法。

let a = {
    name: 'voledy',
    age: 18,
    fn: function(){},
    from: undefined,
    to: Symbol('深圳'),
    nums: /'g'/,
    eat:{
        type: '苹果',
        price: 18,
    }
};
let b = JSON.parse(JSON.stringify(a));
b.name = "哇哈哈";
b.eat.type = "西瓜";
b.eat.price = 30;
console.log(a);  // a = {name: 'voledy', age: 18, nums: /'g'/, fn: function(){}, eat:{ type: '苹果', price: '18'}};
console.log(b);  // b = {name: '哇哈哈', age: 18, nums: {}, eat:{ type: '西瓜', price: '30'}};
```

### 使用递归函数遍历

```js
//写一个递归函数
deepCopy = (source) =>{
    const targetObj = source.constructor === Array ? [] : {}; // 先判断是数组还是对象
    for(let keys in source){  // 遍历
        if(source.hasOwnProperty(keys)){
            if(source[keys] && typeof source[keys] === 'object'){ // 如果值是对象，直接递归
                targetObj[keys] = source[keys].constructor === Array ? [] : {};
                targetObj[keys] = this.deepCopy(source[keys]);
            }else{ // 如果不是，就直接赋值
                targetObj[keys] = source[keys];
            }
        }
    }
    return targetObj;
};

let a = {
    name: 'voledy',
    age: 18,
    eat:{
        type: '苹果',
        price: 18,
        tt:{
            aaa: 1000
        }
    }
};
let b = this.deepCopy(a);
b.eat.type = '西瓜';
b.eat.tt.aaa = '666666';
console.log(a) // a = {name: 'voledy', age: 18,  eat:{ type: '苹果', price: '18', tt:{aaa: 1000}}};
console.log(b) // b = {name: 'voledy', age: 18,  eat:{ type: '西瓜', price: '18', tt:{aaa: 666}}};
```



# 伪数组转为数组的方法

假设这里有个伪数组：pagis

1

```js
let arr = [].slice.call(pagis)
```

2

```
let arr  = Array.prototype.slice.call(pagis);
```

3

```js
 var arr1 = [],
 len1 = pagis.length;
 for (var i = 0; i < len1; i++) {
   arr1.push(pagis[i]);
 }
```

4

```js
var func = Function.prototype.call.bind(Array.prototype.slice);
console.log('类数组转换成数组：', func(pagis));
```

5 解构赋值

```js
function args(){
  console.log(arguments);
  let newArr = [...arguments];
  console.log(newArr);
}
args(1,2,3,23,2,42,34);
```

6 Array.from

```js
function args(){
  console.log(arguments);
  let newArr2 = Array.from(arguments);
  console.log(newArr2);
}
args(1,2,3,23,2,42,34);
```

# this指向

## 普通函数调用

```js
var name = "globle";
function person(){
 	this.name="xl";
 	console.log(this);
 	console.log(this.name);
 }

 person();  //输出  window  globle
```

全局直接调用函数,函数中的this指向window

## 作为方法调用

```js
var name="XL";
var person={
    name:"xl",
    showName:function(){
    	console.log(this.name);
  	}
}
person.showName();  //输出  xl
//这里是person对象调用showName方法，很显然this关键字是指向person对象的，所以会输出name
    
var showNameA=person.showName;
showNameA();    //输出  XL
//这里将person.showName方法赋给showNameA变量，此时showNameA变量相当于window对象的一个属性，因此showNameA()执行的时候相当于window.showNameA(),即window对象调用showNameA这个方法，所以this关键字指向window
```

换种形式

```js
var personA={
    name:"xl",
    showName:function(){
        console.log(this.name);
    }
}
var personB={
    name:"XL",
    sayName:personA.showName
}
    
personB.sayName();  //输出 XL
//虽然showName方法是在personA这个对象中定义，但是调用的时候却是在personB这个对象中调用，因此this对象指向B
```

## 构造函数

```js
function  Person(name){
    this.name=name;
}
var personA = Person("xl");
console.log(personA.name); // 输出  undefined
console.log(window.name);//输出  xl
//上面代码没有进行new操作，相当于window对象调用Person("xl")方法，那么this指向window对象，并进行赋值操作window.name="xl".

var personB = new Person("xl");
console.log(personB.name);// 输出 xl
```

## settimeout

```js

    var name="XL";
    function Person(name){
        this.name=name;
        this.sayName=function(){
            setTimeout(function(){
                console.log("my name is "+this.name);
            },50)
        }
    }
    var person=new Person("xl");
    person.sayName()  //输出  “my name is XL”;
    //这里的setTimeout()定时函数,相当于window.setTimeout(),由window这个全局对象对调用,因此this的指向为window, 则this.name则为XL 
```

那么如何才能输出`"my name is xl"`呢？

```js
    var name="XL";
    function Person(name){
        this.name=name;
        this.sayName=function(){
            setTimeout(function(){
                console.log("my name is "+this.name);
            }.bind(this),50)  //注意这个地方使用的bind()方法，绑定setTimeout里面的匿名函数的this一直指向Person对象
        }
    }
    var person=new Person("xl");
    person.sayName(); //输出 “my name is xl”;
```

## 匿名函数

```js
var name="XL";
var person={
    name:"xl",
    showName:function(){
        console.log(this.name);
    }
    sayName:function(){
        (function(callback){
            callback();
        })(this.showName)
    }
}
person.sayName();  //输出 XL
//匿名函数的执行同样在默认情况下this是指向window的，除非手动改变this的绑定对象

var name="XL";
var person={
	name:"xl",
	showName:function(){
		console.log(this.name);
	},
	sayName:function(){
		(function(callback){
			   callback();
		})(this.showName.bind(this))
	}
}
person.sayName() ;  //输出  "xl"
```

## 箭头函数

`es6`里面`this`指向固定化，始终指向外部对象，因为箭头函数没有`this`,因此它自身不能进行`new`实例化,同时也不能使用`call, apply, bind`等方法来改变`this`的指向

```js
   function Timer() {
        this.seconds = 0;
        setInterval( () => this.seconds ++, 1000);
    } 
    
    var timer = new Timer();
    
    setTimeout( () => console.log(timer.seconds), 3100);
    
    // 3
   // 在构造函数内部的setInterval()内的回调函数，this始终指向实例化的对象，并获取实例化对象的seconds的属性,每1s这个属性的值都会增加1。否则最后在3s后执行setTimeOut()函数执行后输出的是0
```

```js
		var test = {
			sayThis:()=>{
				console.log(this);	//window
			},
			sayThis2:function(){
				return ()=>{
					console.log(this);	//test
				}
			},
			sayThis3:function(){
				console.log(this);	//test
				return function(){
					console.log(this);	//在匿名函数内,所以默认是window
					return ()=>{
						console.log(this);	//箭头函数没有this,取定义所在位置this,就是匿名函数的this,为window
					}
				}
			}
		}
		test.sayThis()	//window
		test.sayThis2()()	//test
		test.sayThis3()()()	//test window window
```

# 防抖和节流

## 防抖

在一段时间只执行一次,**最后一次有效**.控制次数

场景:

- 搜索输入框（Autocomplete），当不再输入后的几百毫秒再去发送请求，减少服务器压力。

- 注册框(即时判断是否重复用户名)，或需要后台校验的文本输入框同理。

- 不停改变浏览器窗口大小会触发多次 resize 事件，引起浏览器的[重排](https://juejin.cn/post/6896243562225401869)【关联概念(弱)】，消耗性能。

```js
        //防抖
        function debounce(fun, timeout) {
            let timer = null
            return function () {
                clearTimeout(timer)
                let args = arguments
                timer = setTimeout(() => {
                    fun.apply(this, args)
                }, timeout)
            }
        }
        let login = function (e, name) {
            console.log(e);

            console.log("哈哈哈" + name);
        }
        let loginDebounce = debounce(login, 2000)
        loading.addEventListener('click', e => {
            loginDebounce(e, '张三')
        })
```

## 节流

只在**开始执行一次**，未执行完成过程中触发的忽略，核心在于`开关锁`.总而言之`节流`就是在**一段时间内不断操作而在你规定的时间内只执行一次的一种提高性能的方法**.节流**至少**在**每时间间隔内** **保证有规律地执行**该功能。控制频率

场景:比如我们滚动鼠标滚轮监听滚动条位置，防止按钮多次点击等。

```js
        //节流
        function throttle(fun, time) {
            let timer = null;
            return function () {
                if (timer) return false;
                let args = arguments;
                fun.apply(this, args)
                timer = setTimeout(() => {
                    clearTimeout(timer)
                    timer = null
                }, time);
            }
        }
        function clickHandle() {
            console.log('被点击了');

        }
        let clickDebounce = throttle(clickHandle, 2000)
        loading.addEventListener('click', clickDebounce)
```

对于`防抖`和`节流`一个最主观的判断方法就是：在`10s`内你**疯狂点击一个按钮**，如果使用了**防抖则会只执行一次**，而你使用了**节流则会每隔一段时间执行一次**，这个时间可以自己来掌控。

# 跨域

## 同源策略

它规定了`协议号-域名-端口号`这三者必须都`相同`才符合`同源策略`,如有有一个`不相同`，就会出现跨域问题，不符合`同源策略`导致的后果有

- 1、`LocalStorge、SessionStorge、Cookie`等浏览器内存无法跨域访问
- 2、`DOM节点`无法跨域操作
- 3、`Ajax请求`无法跨域请求

## 跨域发生的时机

请求发出去到后端，后端返回数据，在浏览器接收后端数据时被浏览器的跨域报错拦下来

## JSPON

以下这三个标签`加载资源路径`是不受束缚的

- 1、script标签：`<script src="加载资源路径"></script>`
- 2、link标签：`<link herf="加载资源路径"></link>`
- 3、img标签：`<img src="加载资源路径"></img>`

JSONP通俗点说就是：利用script的src去发送请求，将一个方法名`callback`传给后端，后端拿到这个方法名，将所需数据，通过字符串拼接成新的字符串`callback(所需数据)`，并发送到前端，前端接收到这个字符串之后，就会自动执行方法`callback(所需数据)`。

缺点:JSONP的缺点就是，需要前后端配合，并且只支持`get请求方法`