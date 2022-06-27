---
title: js
date: 2021-05-07 18:53:59
tags: 前端
---

# bind函数实现

bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。

由此我们可以首先得出 bind 函数的两个特点：

1. 返回一个函数
2. 可以传入参数

首先第一个功能在于传入的第一个参数改变this

```js
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

// 返回了一个函数
var bindFoo = bar.bind(foo); 

bindFoo(); // 1
```

关于指定 this 的指向，我们可以使用 call 或者 apply 实现，关于 call 和 apply 的模拟实现

```js
//模拟传入第一个参数改变this指向
Function.prototype.bind2 = function(newThis){
	var self = this
	return function(){
		return self.apply(newThis)
	}
}
```

接下来看第二点，可以传入参数。这个就有点让人费解了，我在 bind 的时候，是否可以传参呢？我在执行 bind 返回的函数的时候，可不可以传参呢？让我们看个例子：

```js
var foo = {
    value: 1
};

function bar(name, age) {
    console.log(this.value);
    console.log(name);
    console.log(age);

}

var bindFoo = bar.bind(foo, 'daisy');
bindFoo('18');
// 1
// daisy
// 18
```

函数需要传 name 和 age 两个参数，竟然还可以在 bind 的时候，只传一个 name，在执行返回的函数的时候，再传另一个参数 age!

这可咋办？不急，我们用 arguments 进行处理：

```js
// 第二版
Function.prototype.bind2 = function (context) {

    var self = this;
    // 获取bind2函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);

    return function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(context, args.concat(bindArgs));
    }

}
```

完成了这两点，最难的部分到啦！因为 bind 还有一个特点，就是

> 一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

也就是说当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效。举个例子

```js
var value = 2;

var foo = {
    value: 1
};

function bar(name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}

bar.prototype.friend = 'kevin';

var bindFoo = bar.bind(foo, 'daisy');

var obj = new bindFoo('18');
// undefined
// daisy
// 18
console.log(obj.habit);
console.log(obj.friend);
// shopping
// kevin
```

注意：尽管在全局和 foo 中都声明了 value 值，最后依然返回了 undefind，说明绑定的 this 失效了，如果大家了解 new 的模拟实现，就会知道这个时候的 this 已经指向了 obj。

所以我们可以通过修改返回的函数的原型来实现，让我们写一下：

```js
// 第三版
Function.prototype.bind2 = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        // 当作为构造函数时，this 指向实例，此时结果为 true，将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值
        // 以上面的是 demo 为例，如果改成 `this instanceof fBound ? null : context`，实例只是一个空对象，将 null 改成 this ，实例会具有 habit 属性
        // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
        return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值
    fBound.prototype = this.prototype;
    return fBound;
}
```

# Promise

## Promise的三种状态

pending 等待(比如网络请求需要一定的时间,这个过程为等待)

fulfill 完成(回调resolve函数)

reject 拒绝(回调reject函数)

一般情况下是在有异步操作时,使用Promise对异步操作进行封装

```js
new Promise((resolve,reject) => {
    setTimeout(() => {
        //成功时回调resolve
        resolve('学习promise')

        //失败时回调reject
        // reject('学习promise')
    },2000)
}).then(value => {
    console.log(value);
},reason=>{
    console.log(reason);
}).catch(err => {
    console.log(err);
})
```

new Promise需要传入两个参数,一个为resolve,另一个为reject,这两个都是函数.

每个promise和下面的then都是一对,只有promise中resole或reject了(即promise的状态不是pedding),then中的回调函数才会执行

如果里面返回的对象有then方法,则也会自动封装成一个promise对象

## catch

catch会处理前面没有在then里处理的错误

## promise all

```js
Promise.all([
    new Promise((resolve,reject) => {
        setTimeout(() => {
            resolve({name:'请求1',age:'18'})
        },2000)
    }),
    new Promise((resolve,reject) => {
        setTimeout(() => {
            resolve({name:'请求2',age:'23'})
        },2000)
    })
]).then(res => {
    console.log(res);
})
```

只有当promise.all中的所有promise都完成后才会执行then.其中then中的res是一个数组,是每个请求的结果.

如果其中有一个promise返回的是reject状态切没有被处理,则整个Promise.all返回的就是失败状态.如果传入的promise自己有处理失败的操作,则返回

## Promise.Settled

传入一个promise数组,返回的是所有promise返回的结果的数组,不管是否成功.因此Promise.Settled返回的结果永远是成功

## Promise.race

传入的promise数组中谁先返回结果就返回谁

## 使用map实现promise队列

```
        let a = [1, 3, 4, 5, 6]
        let promise = Promise.resolve()
        a.map(num => {
            promise = promise.then(value => {
                return new Promise(resolve => {
                    setTimeout(() => {
                        console.log(num);
                        resolve()
                    }, 1000);
                })

            })
        })
	
	//每隔一秒输出
```



        let a = [1, 3, 4, 5, 6]
        let promise = Promise.resolve()
        a.map(num => {
            promise.then(value => {
                return new Promise(resolve => {
                    setTimeout(() => {
                        console.log(num);
                        resolve()
                    }, 1000);
                })
    
            })
        })
    
    //一秒后一起输出
## async与await语法糖



## 手撕

```js
(function(window){
	function Promise(excutor){
		const that = this
		
		that.status = 'pending'	//三种状态,初始为pending状态
		that.data = undefined	//给promise对象指定一个用于存储结果数据的属性
		that.callbacks = []	//每个元素的结构:{onResolved(){},onRejected(){}}
		
		function resolve(value){
			//因为状态只能又pending->resolved或者由pending->rejected
			//所以要判断状态,当多次调用resolve时,直接返回
			if(that.status!=='pending'){
				return
			}
			
			//将状态改为resolved
			that.status = 'resolved'
			//保存value数据
			that.data = value
			//如果有待执行的callback函数,立即异步执行回调
			if(that.callbacks.length>0){
				//放入队列里执行所有成功的回调
				//加上setTimeout是为了使里面的代码异步执行
				setTimeout(()=>{
					that.callbacks.forEach(callbacksObj=>{
						callbacksObj.onResolved(value)
					})
				},0)
			}
		}
		
		function reject(reason){
			//因为状态只能又pending->resolved或者由pending->rejected
			//所以要判断状态,当多次调用reject时,直接返回
			if(that.status!=='pending'){
				return
			}
			
			//将状态改为rejected
			that.status = 'rejected'
			//保存value数据
			that.data = reason
			//如果有待执行的callback函数,立即异步执行回调
			if(that.callbacks.length>0){
				//放入队列里执行所有成功的回调
				//加上setTimeout是为了使里面的代码异步执行
				setTimeout(()=>{
					that.callbacks.forEach(callbacksObj=>{
						callbacksObj.onRejected(reason)
					})
				},0)
			}
		}
		
		//立即同步执行excutor
		try{
			excutor(resolve,reject)
		}catch(error){
			//当执行器中直接抛异常时,promise对象变为reject
			reject(error)
		}
		
	}
	
	/**
	 * @param {Object} onResolved
	 * @param {Object} onRejected
	 * 指定成功和失败的回调代码
	 * 返回一个新的promise对象
	 */
	Promise.prototype.then = function(onResolved,onRejected){
		
		const that = this
		
		//当then中的回调函数不是函数时(比如then中什么都没有写,实现then的传透传递,直接返回数据,将数据传递到下一个then中)
		if(typeof onResolved !== 'function'){
			onResolved = () =>{
				return that.data
			}
		}
		if(typeof onRejected !== 'function'){
			onRejected = () =>{
				return that.data
			}
		}
		
		function handle(result,resolve,reject){
			try{
				// let result = onResolved(value)
				//处理返回的是一个新的promise的情况
				if(result instanceof Promise){
					result.then(resolve,reject)
				}else{
					resolve(result)
				}
			}catch(err){
				//如果前一个promise的then中报错,就等于新的一个promise报错,要交给新的promise中的reject处理
				reject(err)
			}
		}
		
		return new Promise((resolve,reject)=>{
			
			
			
			
			//假设当前状态还是pending状态,将回调函数保存起来
			if(that.status === 'pending'){
				that.callbacks.push({
					//压入回调栈中的函数也要有可能错,和下面的一样,只不过这是加到了回调栈中,执行的时间不一样而已
					//所以压入回调栈中的函数也是要进行捕获异常的操作
					onResolved: value => {
						// handle(onResolved(value),resolve,reject)
						
						try{
							let result = onResolved(value)
							//处理返回的是一个新的promise的情况
							if(result instanceof Promise){
								result.then(resolve,reject)
							}else{
								resolve(result)
							}
						}catch(err){
							//如果前一个promise的then中报错,就等于新的一个promise报错,要交给新的promise中的reject处理
							reject(err)
						}
					},
					onRejected: reaseon => {
						// handle(onRejected(reaseon),resolve,reject)
						
						try{
							let result = onRejected(reaseon)
							if(result instanceof Promise){
								result.then(resolve,reject)
							}else{
								resolve(result)
							}
						}catch(err){
							//如果前一个promise的then中报错,就等于新的一个promise报错,要交给新的promise中的reject处理
							reject(err)
						}
					}
				})
			}else if(that.status === 'resolved'){
				//假设当前状态是resolved状态,异步执行成功的回调函数
				setTimeout(() => {
					// handle(onResolved(that.data),resolve,reject)
					
					//当onResolved报错时要进行相应捕获并处理(比如onResolved函数输出了未定义的变量)
					try{
						//result存储当前promise执行then后的值,并且默认返回一个成功状态promise
						let result = onResolved(that.data)
						if(result instanceof Promise){
							result.then(resolve,reject)
							console.log(result.then(resolve,reject))
						}else{
							resolve(result)
						}
					}catch(err){
						//如果前一个promise的then中报错,就等于新的一个promise报错,要交给新的promise中的reject处理
						reject(err)
					}
				},0)
				
				
			}else if(that.status === 'rejected'){
				//假设当前状态是rejected状态,异步执行失败的回调函数
				setTimeout(() => {
					// handle(onRejected(that.data),resolve,reject)
					
					try{
						//result存储当前promise执行then后的值,并且默认返回一个成功状态promise
						let result = onRejected(that.data)
						
						if(result instanceof Promise){
							result.then(resolve,reject)
						}else{
							resolve(result)
						}
					}catch(err){
						reject(err)
					}
				},0)
			}
		})
		
		
		
	}
	
	Promise.prototype.catch = function(onRejected){
		
	}
	
	Promise.resolve = function(value){
		
	}
	
	Promise.reject = function(reaseon){
		
	}
	
	Promise.all = function(promises){
		
	}
	
	Promise.race = function(promises){
		
	}
	
	window.Promise = Promise
})(window)
```

