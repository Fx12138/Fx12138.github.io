---
title: js高级程序设计
date: 2021-06-21 15:36:18
tags: 前端 js
---

# 语言基础

var声明的范围是函数作用域,let声明的范围是块作用域.

与 var 关键字不同，使用 let 在全局作用域中声明的变量不会成为 window 对象的属性（var 声 明的变量则会）

## 在for循环中var和let

在使用 var 的时候，最常见的问题就是对迭代变量的奇特声明和修改：

```js
for (var i = 0; i < 5; ++i) { 
 setTimeout(() => console.log(i), 0) 
} 
// 你可能以为会输出 0、1、2、3、4 
// 实际上会输出 5、5、5、5、5 
```

之所以会这样，是因为在退出循环时，迭代变量保存的是导致循环退出的值：5。在之后执行超时 逻辑时，所有的 i 都是同一个变量，因而输出的都是同一个最终值。 

而在使用 let 声明迭代变量时，JavaScript 引擎在后台会为每个迭代循环声明一个新的迭代变量。 每个 setTimeout 引用的都是不同的变量实例，所以 console.log 输出的是我们期望的值，也就是循环执行过程中每个迭代变量的值。

```js
for (let i = 0; i < 5; ++i) { 
 setTimeout(() => console.log(i), 0) 
} 
// 会输出 0、1、2、3、4
```

const 的行为与 let 基本相同，唯一一个重要的区别是用它声明变量时**必须同时初始化变量**，且 尝试修改 const 声明的变量会导致运行时错误。

# 数据类型

## undefined

在对未初始化的变量调用 typeof 时，返回的结果是"undefined"，但对未声明的变量调用它时， 返回的结果还是"undefined"

```js
let message; // 这个变量被声明了，只是值为 undefined 
// 确保没有声明过这个变量
// let age 
console.log(typeof message); // "undefined" 
console.log(typeof age); // "undefined"
```

## toString

null 和 undefined 值没有 toString()方法。

如果你不确定一个值是不是 null 或 undefined，可以使用 String()转型函数，它始终会返回表示相应类型值的字符串。

 如果值有 toString()方法，则调用该方法（不传参数）并返回结果。 

 如果值是 null，返回"null"。

  如果值是 undefined，返回"undefined"。

## == 和 ===

**== 会先进行强制类型转换**

在转换操作数的类型时，相等和不相等操作符遵循如下规则。

 如果任一操作数是布尔值，则将其转换为数值再比较是否相等。false 转换为 0，true 转换 为 1。 

 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否 相等。 

 如果一个操作数是对象，另一个操作数不是，则调用对象的 valueOf()方法取得其原始值，再 根据前面的规则进行比较。 

在进行比较时，这两个操作符会遵循如下规则。

 null 和 undefined 相等。 

 **null 和 undefined 不能转换为其他类型的值再进行比较。** null或undefined都不等于0

 如果有任一操作数是 NaN，则相等操作符返回 false，不相等操作符返回 true。记住：即使两 个操作数都是 NaN，相等操作符也返回 false，因为按照规则，NaN 不等于 NaN。 

 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象， 则相等操作符返回 true。否则，两者不相等。

**===在比较相等时不转换操作数**



## 字面量定义和new的区别

原始类型的初始化可以只使用原始字面量形式。如果使用的是 new 关键字，则 JavaScript 会 创建一个 Object 类型的实例,如下例子

```js
let name1 = "Nicholas"; 
let name2 = new String("Matt"); 
name1.age = 27; 
name2.age = 26; 
console.log(name1.age); // undefined 
console.log(name2.age); // 26 
console.log(typeof name1); // string 
console.log(typeof name2); // object
```

引用类型与原始值包装类型的主要区别在于对象的生命周期。在通过 new 实例化引用类型后，得到 的实例会在离开作用域时被销毁，而自动创建的原始值包装对象则只存在于访问它的那行代码执行期 间。这意味着不能在运行时给原始值添加属性和方法。

## 基本数据和引用数据

- 基本数据类型的值在内存中占据固定大小的空间,因此被保存在栈内存中.
  - 从一个变量向另一个变量复制基本类型的值,会创建这个值的一个副本
- 引用类型的值是对象,保存在堆内存中
  - 从一个变量向另一个变量复制引用类型的值,复制的其实是指针,因此两个变量最终都指向同一个对象

## 传参

ECMAScript 中**所有函数的参数都是按值传递的**。这意味着函数外的值会被复制到函数内部的参数 中，就像从一个变量复制到另一个变量一样。如果是原始值，那么就跟原始值变量的复制一样，如果是引用值，那么就跟引用值变量的复制一样。

```js
function addTen(num) { 
 num += 10; 
 return num; 
} 
let count = 20;
let result = addTen(count); 
console.log(count); // 20，没有变化
console.log(result); // 30


function setName(obj) { 
 obj.name = "Nicholas"; 
} 
let person = new Object(); 
setName(person); 
console.log(person.name); // "Nicholas"


function setName(obj) { 
 obj.name = "Nicholas"; 
 obj = new Object(); 
 obj.name = "Greg"; 
} 
let person = new Object(); 
setName(person); 
console.log(person.name); // "Nicholas"
```

**注意最后一个例子,说明了对象是按值传递的.**

当 person 传入 setName()时，其 name 属性被设置为"Nicholas"。然后变量 obj 被设置 为一个新对象且 name 属性被设置为"Greg"。如果 person 是按引用传递的，那么 person 应该自动将 指针改为指向 name 为"Greg"的对象。可是，当我们再次访问 person.name 时，它的值是"Nicholas"， 这表明**函数中参数的值改变之后，原始的引用仍然没变。当 obj 在函数内部被重写时，它变成了一个指 向本地对象的指针。而那个本地对象在函数执行结束时就被销毁了。**

## 数值

### NaN

非数值.

特点:

(1)任何涉及NaN的操作都会返回NaN

(2)NaN与任何值都不相等,包括NaN本身

isNaN()方法也适用于对象,在基于对象调用这个函数时,会首先调用对象的valueOf()方法,然后确定该方法返回的值是否可以转换为数值,如果不能,则给予这个返回值再调用toString()方法,再测试返回值.

### Number(),parseInt()和parseFloat()

![Number()转换规则](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210621154640957.png)

![parseInt()转换规则](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210621155004329.png)

![parseFloat()转换规则](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210621155130885.png)

# 执行上下文和作用域

![执行环境和作用域](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210621172336067.png)

- 每次进入一个新的执行环境,都会创建一个用于搜索变量和函数的作用域链
- 变量的执行环境有助于确定应该何时释放内存

## 作用域链增强

虽然执行上下文主要有全局上下文和函数上下文两种（eval()调用内部存在第三种上下文），但有 其他方式来增强作用域链。某些语句会导致在作用域链前端临时添加一个上下文，这个上下文在代码执行后会被删除。通常在两种情况下会出现这个现象，即代码执行到下面任意一种情况时：

```
 try/catch 语句的 catch 块
 with 语句
```

这两种情况下，都会在作用域链前端添加一个变量对象。对 with 语句来说，会向作用域链前端添 加指定的对象；对 catch 语句而言，则会创建一个新的变量对象，这个变量对象会包含要抛出的错误 对象的声明。看下面例子:

```js
function buildUrl() {
 let qs = "?debug=true";
 with(location){
 let url = href + qs;
 }
 return url;
} 
```

这里，with 语句将 location 对象作为上下文，因此 location 会被添加到作用域链前端。 buildUrl()函数中定义了一个变量 qs。当 with 语句中的代码引用变量 href 时，实际上引用的是 location.href，也就是自己变量对象的属性。在引用 qs 时，引用的则是定义在 buildUrl()中的那 个变量，它定义在函数上下文的变量对象上。而在 with 语句中使用 var 声明的变量 url 会成为函数 上下文的一部分，可以作为函数的值被返回；但像这里使用 let 声明的变量 url，因为被限制在块级作 用域（稍后介绍），所以在 with 块之外没有定义。

# 变量

## 变量声明

### var

**在使用 var 声明变量时，变量会被自动添加到最接近的上下文。在函数中，最接近的上下文就是函 数的局部上下文。在 with 语句中，最接近的上下文也是函数上下文。如果变量未经声明就被初始化了， 那么它就会自动被添加到全局上下文**，如下面的例子所示：

```js
function add(num1, num2) {
 sum = num1 + num2;
 return sum;
}
let result = add(10, 20); // 30
console.log(sum); // 30 
```

这一次，变量 sum 被用加法操作的结果初始化时并没有使用 var 声明。在调用 add()之后，sum 被添加到了全局上下文，在函数退出之后依然存在，从而在后面可以访问到。

### let

let是块级作用域声明,let 与 var 的另一个不同之处是在同一作用域内不能声明两次。重复的 var 声明会被忽略，而重 复的 let 声明会抛出 SyntaxError。

只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```js
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

上面代码中，存在全局变量tmp，但是块级作用域内let又声明了一个局部变量tmp，导致后者绑定这个块级作用域，所以在let声明变量前，对tmp赋值会报错。

ES6 明确规定，**如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错**。

总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“**暂时性死区**”（temporal dead zone，简称 TDZ）。

### const

ES6 同时还增加了 const 关键字。**使用 const 声明的变量必须同时初始化为某个值**。 一经声明，在其生命周期的任何时候都不能再重新赋予新值。

const 声明只应用到顶级原语或者对象。换句话说，赋值为对象的 const 变量不能再被重新赋值 为其他引用值，但对象的键则不受限制。

如果想让整个对象都不能修改，可以使用 Object.freeze()，这样再给属性赋值时虽然不会报错， 但会静默失败：

```js
const o3 = Object.freeze({});
o3.name = 'Jake';
console.log(o3.name); // undefined
```

**由于 const 声明暗示变量的值是单一类型且不可修改，JavaScript 运行时编译器可以将其所有实例 都替换成实际的值，而不会通过查询表进行变量查找。**谷歌的 V8 引擎就执行这种优化。

## 传递参数

ECMAScript 中所有函数的参数都是按值传递的。这意味着函数外的值会被复制到函数内部的参数 中，就像从一个变量复制到另一个变量一样。如果是原始值，那么就跟原始值变量的复制一样，如果是 引用值，那么就跟引用值变量的复制一样。对很多开发者来说，这一块可能会不好理解，毕竟变量有按 值和按引用访问，而传参则只有按值传递。

下面看传入对象的例子:

```js
function setName(obj) {
 obj.name = "Nicholas";
}
let person = new Object();
setName(person);
console.log(person.name); // "Nicholas"
```

这一次，我们创建了一个对象并把它保存在变量 person 中。然后，这个对象被传给 setName() 方法，并被复制到参数 obj 中。在函数内部，obj 和 person 都指向同一个对象。结果就是，即使对象 是按值传进函数的，obj 也会通过引用访问对象。当函数内部给 obj 设置了 name 属性时，函数外部的 对象也会反映这个变化，因为 obj 指向的对象保存在全局作用域的堆内存上。很多开发者错误地认为， 当在局部作用域中修改对象而变化反映到全局时，就意味着参数是按引用传递的。为证明对象是按值传 递的，我们再来看看下面这个修改后的例子：

```js
function setName(obj) {
 obj.name = "Nicholas";
 obj = new Object();
 obj.name = "Greg";
}
let person = new Object();
setName(person);
console.log(person.name); // "Nicholas" 
```

这个例子前后唯一的变化就是 setName()中多了两行代码，将 obj 重新定义为一个有着不同 name 的新对象。当 person 传入 setName()时，其 name 属性被设置为"Nicholas"。然后变量 obj 被设置 为一个新对象且 name 属性被设置为"Greg"。如果 person 是按引用传递的，那么 person 应该自动将 指针改为指向 name 为"Greg"的对象。可是，当我们再次访问 person.name 时，它的值是"Nicholas"， **这表明函数中参数的值改变之后，原始的引用仍然没变.当 obj 在函数内部被重写时，它变成了一个指 向本地对象的指针。而那个本地对象在函数执行结束时就被销毁了。**

# 垃圾回收

## 标记清除

最常用的垃圾收集方式是标记清除.垃圾收集器在运行的时候会给存储在内存的所有变量都加上标记.然后它会去掉**环境中的变量以及被环境中的变量引用的变量**的标记.而在此之后再被加上标记的变量将被视为准备删除的变量,原因是环境中的变量已经无法访问到这些变量了.最后垃圾收集器完成内存清除工作,销毁那些带标记的值并回收他们所占用的空间.

给变量加标记的方式有很多种。比如，当变量进入上下文时，反转某一位；或者可以维护“在上下 文中”和“不在上下文中”两个变量列表，可以把变量从一个列表转移到另一个列表。标记过程的实现 并不重要，关键是策略。

## 引用计数

其思路是对每个值都记录它被 引用的次数。声明变量并给它赋一个引用值时，这个值的引用数为 1。如果同一个值又被赋给另一个变 量，那么引用数加 1。类似地，如果保存对该值引用的变量被其他值给覆盖了，那么引用数减 1。当一 个值的引用数为 0 时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。垃圾回收程序 下次运行的时候就会释放引用数为 0 的值的内存。

### 引用计数的问题

了严重的问题：循环引用。所谓循环引 用，就是对象 A 有一个指针指向对象 B，而对象 B 也引用了对象 A。比如：

```js
function problem() { 
 let objectA = new Object(); 
 let objectB = new Object(); 
 objectA.someOtherObject = objectB; 
 objectB.anotherObject = objectA; 
} 
```

在这个例子中，objectA 和 objectB 通过各自的属性相互引用，意味着它们的引用数都是 2。在 标记清理策略下，这不是问题，因为在函数结束后，这两个对象都不在作用域中。而在引用计数策略下，objectA 和 objectB 在函数结束后还会存在，因为它们的引用数永远不会变成 0。如果函数被多次调 用，则会导致大量内存永远不会被释放。

在 IE8 及更早版本的 IE 中，并非所有对象都是原生 JavaScript 对象。BOM 和 DOM 中的对象是 C++ 实现的组件对象模型（COM，Component Object Model）对象，而 COM 对象使用引用计数实现垃圾回 收。因此，即使这些版本 IE 的 JavaScript 引擎使用标记清理，JavaScript 存取的 COM 对象依旧使用引用 计数。换句话说，只要涉及 COM 对象，就无法避开循环引用问题。下面这个简单的例子展示了涉及 COM 对象的循环引用问题：

```js
let element = document.getElementById("some_element");
let myObject = new Object();
myObject.element = element;
element.someObject = myObject;
```

为避免类似的循环引用问题，应该在确保不使用的情况下切断原生 JavaScript 对象与 DOM 元素之 间的连接。比如，通过以下代码可以清除前面的例子中建立的循环引用：

```js
myObject.element = null;
element.someObject = null;
```

为了补救这一点，IE9 把 BOM 和 DOM 对象都改成了 JavaScript 对象，这同时也避免了由于存在两 套垃圾回收算法而导致的问题，还消除了常见的内存泄漏现象。

## 内存管理

#### 通过let和const声明提升性能

因为 const和 let 都以块（而非函数）为作用域，所以相比于使用 var，使用这两个新关键字可能会更早地让垃圾回 收程序介入，尽早回收应该回收的内存。在块作用域比函数作用域更早终止的情况下，这就有可能发生。

#### 隐藏类和删除操作

运行期间，V8 会将创建的对象与隐藏类关联起来，以跟踪它们的属性特征。能够共享相同隐藏类 的对象性能会更好，V8 会针对这种情况进行优化，但不一定总能够做到。比如下面的代码：

```js
function Article() { 
 this.title = 'Inauguration Ceremony Features Kazoo Band'; 
} 
let a1 = new Article(); 
let a2 = new Article(); 
```

V8 会在后台配置，让这两个类实例共享相同的隐藏类，因为这两个实例共享同一个构造函数和原 型。假设之后又添加了下面这行代码：

```js
a2.author = 'Jake'; 
```

此时两个 Article 实例就会对应两个不同的隐藏类。根据这种操作的频率和隐藏类的大小，这有 可能对性能产生明显影响。

当然，解决方案就是避免 JavaScript 的“先创建再补充”（ready-fire-aim）式的动态属性赋值，并在 构造函数中一次性声明所有属性，如下所示：

```js
function Article(opt_author) { 
 this.title = 'Inauguration Ceremony Features Kazoo Band'; 
 this.author = opt_author; 
} 
let a1 = new Article(); 
let a2 = new Article('Jake');
```

这样，两个实例基本上就一样了（不考虑 hasOwnProperty 的返回值），因此可以共享一个隐藏类， 从而带来潜在的性能提升。

不过要记住，使用 delete 关键字会导致生成相同的隐藏类片段。看一下这 个例子：

```js
function Article() { 
 this.title = 'Inauguration Ceremony Features Kazoo Band'; 
 this.author = 'Jake'; 
} 
let a1 = new Article(); 
let a2 = new Article(); 
delete a1.author;
```

在代码结束后，即使两个实例使用了同一个构造函数，它们也不再共享一个隐藏类。动态删除属性 与动态添加属性导致的后果一样。最佳实践是把不想要的属性设置为 null。这样可以保持隐藏类不变 和继续共享，同时也能达到删除引用值供垃圾回收程序回收的效果。比如：

```js
function Article() { 
 this.title = 'Inauguration Ceremony Features Kazoo Band'; 
 this.author = 'Jake'; 
} 
let a1 = new Article(); 
let a2 = new Article(); 
a1.author = null;
```

# 基本引用类型

## 原始值包装类型

ECMAScript 提供了 3 种特殊的引用类型：Boolean、Number 和 String。

```js
let s1 = "some text";
let s2 = s1.substring(2);
```

在这里，s1 是一个包含字符串的变量，它是一个原始值。第二行紧接着在 s1 上调用了 substring() 方法，并把结果保存在 s2 中。我们知道，原始值本身不是对象，因此逻辑上不应该有方法。而实际上 这个例子又确实按照预期运行了。这是因为后台进行了很多处理，从而实现了上述操作。具体来说，当 第二行访问 s1 时，是以读模式访问的，也就是要从内存中读取变量保存的值。

在以读模式访问字符串 值的任何时候，后台都会执行以下 3 步：

(1) 创建一个 String 类型的实例；

(2) 调用实例上的特定方法；

(3) 销毁实例。

在读的时候相当于进行了如下操作

```js
let s1 = new String("some text");
let s2 = s1.substring(2);
s1 = null; 
```

**引用类型与原始值包装类型的主要区别在于对象的生命周期。在通过 new 实例化引用类型后，得到 的实例会在离开作用域时被销毁，而自动创建的原始值包装对象则只存在于访问它的那行代码执行期 间。这意味着不能在运行时给原始值添加属性和方法。**比如下面的例子：

```js
let s1 = "some text";
s1.color = "red";
console.log(s1.color); // undefined 
```

原因就是第二行代码运行时会临时创建一个 String 对象，而当第三行代码执行时，这个对 象已经被销毁了。实际上，第三行代码在这里创建了自己的 String 对象，但这个对象没有 color 属性。

### Boolean

```js
let falseObject = new Boolean(false);
let result = falseObject && true;
console.log(result); // true
//所有对象在布尔表达式中都会自动转换为 true，因此 falseObject 在这个表达式里实际上表示一个 true 值。那么true && true 当然是 true。

let falseValue = false;
result = falseValue && true;
console.log(result); // false 
console.log(typeof falseObject); // object
console.log(typeof falseValue); // boolean
console.log(falseObject instanceof Boolean); // true
console.log(falseValue instanceof Boolean); // false 
```

### Number

```js
let numberObject = new Number(10); 
let numberValue = 10; 
console.log(typeof numberObject); // "object" 
console.log(typeof numberValue); // "number" 
console.log(numberObject instanceof Number); // true 
console.log(numberValue instanceof Number); // false 
```

原始数值在调用 typeof 时始终返回"number"，而 Number 对象则返回"object"。类似地，Number 对象是 Number 类型的实例，而原始数值不是。

### String

#### 字符串的操作方法

ECMAScript 提供了 3 个从字符串中提取子字符串的方法：**slice()、substr()和 substring()**。

都接收一或两个参数。第一个参数表示子字符串开 始的位置，第二个参数表示子字符串结束的位置。对 slice()和 substring()而言，第二个参数是提取结 束的位置（即该位置之前的字符会被提取出来）。对 substr()而言，第二个参数表示返回的子字符串数量。 任何情况下，省略第二个参数都意味着提取到字符串末尾。

```js
let stringValue = "hello world";
console.log(stringValue.slice(3)); // "lo world"
console.log(stringValue.substring(3)); // "lo world"
console.log(stringValue.substr(3)); // "lo world"
console.log(stringValue.slice(3, 7)); // "lo w"
console.log(stringValue.substring(3,7)); // "lo w"
console.log(stringValue.substr(3, 7)); // "lo worl"
```

当某个参数是负值时，这 3 个方法的行为又有不同。比如，slice()方法将所有负值参数都当成字 符串长度加上负参数值。 而 substr()方法将第一个负参数值当成字符串长度加上该值，将第二个负参数值转换为 0。 substring()方法会将所有负参数值都转换为 0。

#### 字符串位置方法

有两个方法用于在字符串中定位子字符串：indexOf()和 lastIndexOf()。这两个方法从字符 串中搜索传入的字符串，并返回位置（如果没找到，则返回-1）。两者的区别在于，indexOf()方法 从字符串开头开始查找子字符串，而 lastIndexOf()方法从字符串末尾开始查找子字符串。

#### 字符串包含方法

ECMAScript 6 增加了 3 个用于判断字符串中是否包含另一个字符串的方法：startsWith()、 endsWith()和 includes()。

```js
let message = "foobarbaz";
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("bar")); // false
console.log(message.endsWith("baz")); // true
console.log(message.endsWith("bar")); // false
console.log(message.includes("bar")); // true
console.log(message.includes("qux")); // false
```

#### trim()方法

ECMAScript 在所有字符串上都提供了 trim()方法。这个方法会创建字符串的一个副本，删除前、 后所有空格符，再返回结果。比如：

# 集合引用类型

## Array

### 检测数组

使用 instanceof 的问题是假定只有一个全局执行上下文。如果网页里有多个框架，则可能涉及两 个不同的全局执行上下文，因此就会有两个不同版本的 Array 构造函数。如果要把数组从一个框架传 给另一个框架，则这个数组的构造函数将有别于在第二个框架内本地创建的数组。

ECMAScript 提供了 **Array.isArray()**方法。这个方法的目的就是确定一个值是 否为数组，而不用管它是在哪个全局执行上下文中创建的。

### 转化数组

Array 构造函数还有两个 ES6 新增的**用于创建数组的静态方法：from()和 of()。from()用于将 类数组结构转换为数组实例，而 of()用于将一组参数转换为数组实例。**

Array.from()的第一个参数是一个类数组对象，即任何可迭代的结构，或者有一个 length 属性 和可索引元素的结构。

```js
// arguments 对象可以被轻松地转换为数组
function getArgsArray() {
 return Array.from(arguments);
}
console.log(getArgsArray(1, 2, 3, 4)); // [1, 2, 3, 4]
```

Array.from()还接收第二个可选的映射函数参数。这个函数可以直接增强新数组的值，而无须像 调用 Array.from().map()那样先创建一个中间数组。还可以接收第三个可选参数，用于指定映射函 数中 this 的值。但这个重写的 this 值在箭头函数中不适用。

```js
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1, x => x**2);
const a3 = Array.from(a1, function(x) {return x**this.exponent}, {exponent: 2});
console.log(a2); // [1, 4, 9, 16]
console.log(a3); // [1, 4, 9, 16] 
```

Array.of()可以把一组参数转换为数组。这个方法用于替代在 ES6之前常用的 Array.prototype. slice.call(arguments)，一种异常笨拙的将 arguments 对象转换为数组的写法：

```js
console.log(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4]
console.log(Array.of(undefined)); // [undefined]
```

### 复制和填充方法

使用 fill()方法可以向一个已有的数组中插入全部或部分相同的值。开始索引用于指定开始填充 的位置，它是可选的。

```js
// 用 6 填充索引大于等于 3 的元素
zeroes.fill(6, 3);
console.log(zeroes); // [0, 0, 0, 6, 6]

// 用 7 填充索引大于等于 1 且小于 3 的元素
zeroes.fill(7, 1, 3); 
console.log(zeroes); // [0, 7, 7, 0, 0]; 
```

copyWithin()会按照指定范围浅复制数组中的部分内容，然后将它们插入到指定索引开始的位置。开始索引和结束索引则与 fill()使用同样的计算方法：

```js
let ints,
 reset = () => ints = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
 // 从 ints 中复制索引 0 开始到索引 3 结束的内容
// 插入到索引 4 开始的位置
ints.copyWithin(4, 0, 3);
alert(ints); // [0, 1, 2, 3, 0, 1, 2, 7, 8, 9]
```

### 转换方法

前面提到过，所有对象都有 toLocaleString()、toString()和 valueOf()方法。其中，valueOf() 返回的还是数组本身。而 toString()返回由数组中每个值的等效字符串拼接而成的一个逗号分隔的 字符串。

toLocaleString()方法也可能返回跟 toString()和 valueOf()相同的结果，但也不一定。**在 调用数组的 toLocaleString()方法时，会得到一个逗号分隔的数组值的字符串。它与另外两个方法 唯一的区别是，为了得到最终的字符串，会调用数组每个值的 toLocaleString()方法，而不是 toString()方法。**

```js
let person1 = {
 toLocaleString() {
 return "Nikolaos";
 },
 toString() {
 return "Nicholas";
 }
};
let person2 = {
 toLocaleString() {
 return "Grigorios";
 },
 toString() {
 return "Greg";
 }
};
let people = [person1, person2];
alert(people); // Nicholas,Greg
alert(people.toString()); // Nicholas,Greg
alert(people.toLocaleString()); // Nikolaos,Grigorios
```

这里定义了两个对象 person1 和 person2，它们都定义了 toString()和 toLocaleString()方 法，而且返回不同的值。然后又创建了一个包含这两个对象的数组 people。在将数组传给 alert()时， 输出的是"Nicholas,Greg"，这是因为会在数组每一项上调用 toString()方法（与下一行显式调用 toString()方法结果一样）。而在调用数组的 toLocaleString()方法时，结果变成了"Nikolaos, Grigorios"，这是因为调用了数组每一项的 toLocaleString()方法。

### 栈方法

pop和push,以栈的方式操作数组,pop弹出数组中最后的元素,push在数组尾部添加新的元素

**push返回新数组的长度**

**pop返回弹出的元素**

### 队列方法

shift()和 push(),以队列的方式操作数组,shift删除数组中的第一个元素,push在数组尾部添加新的元素.对应有unshift方法,在数组开头添加任意多个值，然后返回新的数组长度。

**shift返回删除的元素**

**unshift返回新数组的长度**

### 排序方法

数组有两个方法可以用来对元素重新排序：reverse()和 sort()。

reverse()方法就 是将数组元素反向排列。**reverse()会改变原来的数组,并返回这个数组**

默认情况下，sort()会按照升序重新排列数组元素，即最小的值在前面，最大的值在后面。为此， sort()会在每一项上调用 String()转型函数，然后比较字符串来决定顺序。**sort()会改变原来的数组,并返回这个数组**,即使数组的元素都是数值， 也会先把数组转换为字符串再比较、排序。比如:

```js
let values = [0, 1, 5, 10, 15];
values.sort();
alert(values); // 0,1,10,15,5
```

开始数组中数值的顺序是正确的，但调用 sort()会按照这些数值的字符串形式重新排序。因此， 即使 5 小于 10，但字符串"10"在字符串"5"的前头，所以 10 还是会排到 5 前面。很明显，这在多数情 况下都不是最合适的。为此，sort()方法可以接收一个**比较函数**，用于判断哪个值应该排在前面。

比较函数接收两个参数，如果第一个参数应该排在第二个参数前面，就返回负值；如果两个参数相 等，就返回 0；如果第一个参数应该排在第二个参数后面，就返回正值。下面是使用简单比较函数的一 个例子：

```js
function compare(value1, value2) {
 if (value1 < value2) {
 	return -1;
 } else if (value1 > value2) {
 	return 1;
 } else {
 	return 0;
 }
} 
```

这个比较函数可以适用于大多数数据类型，可以把它当作参数传给 sort()方法，如下所示：

```js
let values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // 0,1,5,10,15
```

### 操作方法

1 join() 方法也可将所有数组元素结合为一个字符串。它的行为类似 toString()，但是可以规定分隔符.**返回生成的字符串

2 shift() 方法会删除首个数组元素，并把所有其他元素“位移”到更低的索引,方法返回被“位移出”的字符串

3 unshift() 方法（在开头）向数组添加新元素，并“反向位移”旧元素, 方法返回新数组的长度

4 splice() 方法可用于向数组添加新项 

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.splice(2, 0, "Lemon", "Kiwi");
//此时数组为["Banana", "Orange", "Lemon", "Kiwi", "Apple","Mango"]
```

​	第一个参数（2）定义了应添加新元素的位置（拼接）。

​		第二个参数（0）定义应删除多少元素。

​		其余参数（“Lemon”，“Kiwi”）定义要添加的新元素。

​		**splice() 方法改变原数组,返回一个包含已删除项的数组**

5 concat() 方法通过合并（连接）现有数组来创建一个新数组.**concat()不会改变原数组,返回连接后的新数组**

6 slice() 方法用数组的某个片段切出新数组

​		slice() 可接受两个参数，比如 (1, 3)。

​		该方法会从开始参数选取元素，直到结束参数（不包括）为止

​		如果结束参数被省略，则 slice() 会切出数组的剩余部分

​		**slice()不会改变原数组,返回截取的片段数组**

### 搜索和位置方法

#### 严格相等

ECMAScript 提供了 3 个严格相等的搜索方法：**indexOf()、lastIndexOf()和 includes()**。其 中，前两个方法在所有版本中都可用，而第三个方法是 ECMAScript 7 新增的。

这些方法都接收两个参 数：要查找的元素和一个可选的起始搜索位置。indexOf()和 includes()方法从数组前头（第一项） 开始向后搜索，而 lastIndexOf()从数组末尾（最后一项）开始向前搜索。 indexOf()和 lastIndexOf()都返回要查找的元素在数组中的位置，如果没找到则返回-1。 includes()返回布尔值，表示是否至少找到一个与指定元素匹配的项。在比较第一个参数跟数组每一 项时，会使用全等（===）比较，也就是说两项必须严格相等。

#### 断言函数

find()和 findIndex()方法使用了断言函数。这两个方法都从数组的最小索引开始。find()返回 第一个匹配的元素，findIndex()返回第一个匹配元素的索引。

```js
const people = [
 {
 name: "Matt",
 age: 27
 },
 {
 name: "Nicholas",
 age: 29
 }
];
alert(people.find((element, index, array) => element.age < 28));
// {name: "Matt", age: 27}
alert(people.findIndex((element, index, array) => element.age < 28));
// 0 
```

### 迭代方法

```
 every()：对数组每一项都运行传入的函数，如果对每一项函数都返回 true，则这个方法返回 true。
 some()：对数组每一项都运行传入的函数，如果有一项函数返回 true，则这个方法返回 true。
 filter()：对数组每一项都运行传入的函数，函数返回 true 的项会组成数组之后返回。
 forEach()：对数组每一项都运行传入的函数，没有返回值。
 map()：对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组。

这些方法都不改变调用它们的数组。
```

每个方法接收两个参数：以每一项为参数运行的函数， 以及可选的作为函数运行上下文的作用域对象（影响函数中 this 的值）。传给每个方法的函数接收 3 个参数：数组元素、元素索引和数组本身。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let everyResult = numbers.every((item, index, array) => item > 2);
alert(everyResult); // false
let someResult = numbers.some((item, index, array) => item > 2);
alert(someResult); // true 

let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let filterResult = numbers.filter((item, index, array) => item > 2);
alert(filterResult); // 3,4,5,4,3

let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let mapResult = numbers.map((item, index, array) => item * 2);
alert(mapResult); // 2,4,6,8,10,8,6,4,2

let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
numbers.forEach((item, index, array) => {
 // 执行某些操作
});
```

### 归并方法

ECMAScript 为数组提供了两个归并方法：reduce()和 reduceRight()。这两个方法都会迭代数 组的所有项，并在此基础上构建一个最终返回值。reduce()方法从数组第一项开始遍历到最后一项。 而 reduceRight()从最后一项开始遍历至第一项。

传给 reduce()和 reduceRight()的函数接收 4 个参数：上一个归并值、当前项、当前项的索引和数 组本身。这个函数返回的任何值都会作为下一次调用同一个函数的第一个参数。

```js
let values = [1, 2, 3, 4, 5];
let sum = values.reduce((prev, cur, index, array) => prev + cur);
alert(sum); // 15
```

第一次执行归并函数时，prev 是 1，cur 是 2。第二次执行时，prev 是 3（1 + 2），cur 是 3（数 组第三项）。如此递进，直到把所有项都遍历一次，最后返回归并结果。

reduceRight()方法与之类似，只是方向相反。来看下面的例子：

```js
let values = [1, 2, 3, 4, 5];
let sum = values.reduceRight(function(prev, cur, index, array){
 return prev + cur;
});
alert(sum); // 15
```

## Map

键值对

### 基本用法

```js
const m = new Map(); 
alert(m.has("firstName")); // false 
alert(m.get("firstName")); // undefined 
alert(m.size); // 0 
m.set("firstName", "Matt") 
 .set("lastName", "Frisbie"); 
alert(m.has("firstName")); // true 
alert(m.get("firstName")); // Matt 
alert(m.size); // 2 
m.delete("firstName"); // 只删除这一个键/值对
alert(m.has("firstName")); // false 
alert(m.has("lastName")); // true 
alert(m.size); // 1 
m.clear(); // 清除这个映射实例中的所有键/值对
alert(m.has("firstName")); // false 
alert(m.has("lastName")); // false 
alert(m.size); // 0 
```

与 Object 只能使用数值、字符串或符号作为键不同，**Map 可以使用任何 JavaScript 数据类型作为 键**。

```js
const m = new Map(); 
const functionKey = function() {}; 
const symbolKey = Symbol(); 
const objectKey = new Object(); 
m.set(functionKey, "functionValue"); 
m.set(symbolKey, "symbolValue"); 
m.set(objectKey, "objectValue"); 
alert(m.get(functionKey)); // functionValue 
alert(m.get(symbolKey)); // symbolValue 
alert(m.get(objectKey)); // objectValue 
// SameValueZero 比较意味着独立实例不冲突
alert(m.get(function() {})); // undefined
```

迭代各个键方法

```js
const m = new Map([ 
 ["key1", "val1"], 
 ["key2", "val2"], 
 ["key3", "val3"] 
]); 
alert(m.entries === m[Symbol.iterator]); // true 
for (let pair of m.entries()) { 
 alert(pair); 
} 
// [key1,val1] 
// [key2,val2] 
// [key3,val3] 
for (let pair of m[Symbol.iterator]()) { 
 alert(pair); 
} 
// [key1,val1] 
// [key2,val2] 
// [key3,val3] 


//因为 entries()是默认迭代器，所以可以直接对映射实例使用扩展操作，把映射转换为数组：
const m = new Map([ 
 ["key1", "val1"], 
 ["key2", "val2"], 
 ["key3", "val3"] 
]); 
console.log([...m]); // [[key1,val1],[key2,val2],[key3,val3]]
```



### 选择Object还是Map

- 内存占用:存储单个键/值对所占用的内存数量 都会随键的数量线性增加。给定固定大小的内存，Map 大约可以比 Object 多存储 50%的键/值对。
- 插入性能:向 Object 和 Map 中插入新键/值对的消耗大致相当，不过插入 Map 在所有浏览器中一般会稍微快 一点儿。对这两个类型来说，**插入速度并不会随着键/值对数量而线性增加**。如果代码涉及大量插入操 作，那么显然 Map 的性能更佳。
- 查找速度:对这两个类型而言， 查找速度不会随着键/值对数量增加而线性增加。如果代码涉及大量查找操作，那么某些情况下可能选 择 Object 更好一些。
- 删除性能:Map 的 delete()操作都比插入和查找更快。 如果代码涉及大量删除操作，那么毫无疑问应该选择 Map。

## WeakMap

ECMAScript 6 新增的“弱映射”（WeakMap）是一种新的集合类型，为这门语言带来了增强的键/ 值对存储机制。WeakMap 中的“weak”（弱）， 描述的是 JavaScript 垃圾回收程序对待“弱映射”中键的方式。

**弱映射中的键只能是 Object 或者继承自 Object 的类型**，尝试使用非对象设置键会抛出 TypeError。值的类型没有限制。

WeakMap 实例之所以限制只能用对象作为键，是为了保证只有通过键对象的引用才能取得值。如果 允许原始值，那就没办法区分初始化时使用的字符串字面量和初始化之后使用的一个相等的字符串了。

### 弱键

**WeakMap 中“weak”表示弱映射的键是“弱弱地拿着”的。意思就是，这些键不属于正式的引用， 不会阻止垃圾回收。但要注意的是，弱映射中值的引用可不是“弱弱地拿着”的。只要键存在，键/值 对就会存在于映射中，并被当作对值的引用，因此就不会被当作垃圾回收。**

```js
const wm = new WeakMap(); 
wm.set({}, "val"); 
```

set()方法初始化了一个新对象并将它用作一个字符串的键。**因为没有指向这个对象的其他引用， 所以当这行代码执行完成后，这个对象键就会被当作垃圾回收。**然后，这个键/值对就从弱映射中消失 了，使其成为一个空映射。在这个例子中，因为值也没有被引用，所以这对键/值被破坏以后，值本身 也会成为垃圾回收的目标。再来看另一个例子

```js
const wm = new WeakMap(); 
const container = { 
 key: {} 
}; 
wm.set(container.key, "val"); 
function removeReference() { 
 container.key = null; 
}
```

这一次，container 对象维护着一个对弱映射键的引用，因此这个对象键不会成为垃圾回收的目 标。不过，如果调用了 removeReference()，就会摧毁键对象的最后一个引用，垃圾回收程序就可以 把这个键/值对清理掉。

### 不可迭代键

因为 WeakMap 中的键/值对任何时候都可能被销毁，所以没必要提供迭代其键/值对的能力。当然， 也用不着像 clear()这样一次性销毁所有键/值的方法.

### 使用

#### 私有变量

弱映射造就了在 JavaScript 中实现真正私有变量的一种新方式。**前提很明确：私有变量会存储在弱 映射中，以对象实例为键，以私有成员的字典为值。**

```js
const wm = new WeakMap(); 
class User { 
 constructor(id) { 
 this.idProperty = Symbol('id'); 
 this.setId(id); 
 } 
 setPrivate(property, value) { 
 const privateMembers = wm.get(this) || {}; 
 privateMembers[property] = value; 
 wm.set(this, privateMembers); 
 } 
 getPrivate(property) { 
 return wm.get(this)[property]; 
 } 
 setId(id) { 
 this.setPrivate(this.idProperty, id); 
 } 
 getId() { 
 return this.getPrivate(this.idProperty); 
 } 
} 
const user = new User(123); 
alert(user.getId()); // 123 
user.setId(456); 
alert(user.getId()); // 456 
// 并不是真正私有的
alert(wm.get(user)[user.idProperty]); // 456 
```

# 迭代器和生成器

## 使用

```js
// 可迭代对象
let arr = ['foo', 'bar']; 
// 迭代器工厂函数
console.log(arr[Symbol.iterator]); // f values() { [native code] } 
// 迭代器
let iter = arr[Symbol.iterator](); 
console.log(iter); // ArrayIterator {} 
// 执行迭代
console.log(iter.next()); // { done: false, value: 'foo' } 
console.log(iter.next()); // { done: false, value: 'bar' } 
console.log(iter.next()); // { done: true, value: undefined }
```



# 对象

## 属性的类型

属性分数据属性和访问器属性.

### 数据属性

数据属性包含一个保存数据值的位置,值会从这个位置读取,也会写入到这个位置.数据属性有4个特性描述他们的行为.

![数据属性的4个特性](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210622140123295.png)

要修改属性的默认特性，就必须使用 Object.defineProperty()方法。这个方法接收 3 个参数： 要给其添加属性的对象、属性的名称和一个描述符对象。最后一个参数，即描述符对象上的属性可以包 含：**configurable、enumerable、writable 和 value**，跟相关特性的名称一一对应。根据要修改 的特性，可以设置其中一个或多个值。比如

```js
let person = {}; 
Object.defineProperty(person, "name", { 
 writable: false, 
 value: "Nicholas" 
}); 
console.log(person.name); // "Nicholas" 
person.name = "Greg"; 
console.log(person.name); // "Nicholas"
```

这个例子创建了一个名为 name 的属性并给它赋予了一个只读的值"Nicholas"。这个属性的值就 不能再修改了.

在调用 Object.defineProperty()时，configurable、enumerable 和 writable 的值如果不 指定，则都默认为 false。

### 访问器属性

访问器属性不包含数据值。相反，它们包含一个获取（getter）函数和一个设置（setter）函数，不 过这两个函数不是必需的。

访问器属性有 4 个特性描述它们的行为。 

- [[Configurable]]：表示属性是否可以通过 delete 删除并重新定义，是否可以修改它的特 性，以及是否可以把它改为数据属性。默认情况下，所有直接定义在对象上的属性的这个特性 都是 true。
-  [[Enumerable]]：表示属性是否可以通过 for-in 循环返回。默认情况下，所有直接定义在对 象上的属性的这个特性都是 true。 
-  [[Get]]：获取函数，在读取属性时调用。默认值为 undefined。 
-  [[Set]]：设置函数，在写入属性时调用。默认值为 undefined。

```js
// 定义一个对象，包含伪私有成员 year_和公共成员 edition 
let book = { 
 year_: 2017, 
 edition: 1
}; 
Object.defineProperty(book, "year", { 
 get() { 
 return this.year_; 
 }, 
 set(newValue) { 
 if (newValue > 2017) { 
 this.year_ = newValue; 
 this.edition += newValue - 2017; 
 } 
 } 
}); 
book.year = 2018; 
console.log(book.edition); // 2
```

year_和 edition。year_中的下划线常用来表示该 属性并不希望在对象方法的外部被访问。另一个属性 year 被定义为一个访问器属性，其中获取函数简 单地返回 year_的值，而设置函数会做一些计算以决定正确的版本（edition）。因此，把 year 属性修改 为 2018 会导致 year_变成 2018，edition 变成 2。这是访问器属性的典型使用场景，即设置一个属性 值会导致一些其他变化发生。

获取函数和设置函数不一定都要定义。**只定义获取函数意味着属性是只读的，尝试修改属性会被忽 略。类似地，只有一个设置函数的属性 是不能读取的.**

### 定义多个属性

在一个对象上同时定义多个属性的可能性是非常大的。为此，ECMAScript 提供了 Object.defineProperties()方法。这个方法可以通过多个描述符一次性定义多个属性。它接收两个参数：要为之添 加或修改属性的对象和另一个描述符对象，其属性与要添加或修改的属性一一对应。比如：

```js
let book = {}; 
Object.defineProperties(book, { 
 year_: { 	//数据属性
 	value: 2017 
 }, 
 edition: { 	//数据属性
 	value: 1 
 }, 
 year: { 	//访问器属性
 	get() { 
 		return this.year_; 
 	}, 
 	set(newValue) { 
 		if (newValue > 2017) { 
 			this.year_ = newValue; 
 			this.edition += newValue - 2017; 
 		} 
 	} 
 } 
});
```

读取属性特性方法如下,两种方法,第一种获取的是year_属性的特性描述,第二种方法获取的是book对象的全部属性和描述.

```js
let descriptor = Object.getOwnPropertyDescriptor(book, "year_");
console.log(Object.getOwnPropertyDescriptors(book));
```

## 合并对象

有时也称为"混入,就是把源对象所有的本地属性一起复制到目标对象上.

ECMAScript 6 专门为合并对象提供了 Object.assign()方法。这个方法接收一个目标对象和一个 或多个源对象作为参数，然后将每个源对象中可枚举（Object.propertyIsEnumerable()返回 true） 和自有（Object.hasOwnProperty()返回 true）属性复制到目标对象。以字符串和符号为键的属性 会被复制。对每个符合条件的属性，这个方法会使用源对象上的[[Get]]取得属性的值，然后使用目标 对象上的[[Set]]设置属性的值。

Object.assign()实际上对每个源对象执行的是浅复制。

```js
let dest, src, result; 
/** 
 * 覆盖属性
 */ 
dest = { id: 'dest' }; 
result = Object.assign(dest, { id: 'src1', a: 'foo' }, { id: 'src2', b: 'bar' }); 
// Object.assign 会覆盖重复的属性
console.log(result); // { id: src2, a: foo, b: bar } 
// 可以通过目标对象上的设置函数观察到覆盖的过程：
dest = { 
 set id(x) { 
 console.log(x); 
 } 
}; 
Object.assign(dest, { id: 'first' }, { id: 'second' }, { id: 'third' }); 
// first 
// second 
// third 
/** 
 * 对象引用
 */ 
dest = {}; 
src = { a: {} }; 
Object.assign(dest, src); 
// 浅复制意味着只会复制对象的引用
console.log(dest); // { a :{} } 
console.log(dest.a === src.a); // true
```

## 对象标识及相等判定

在 ECMAScript 6 之前，有些特殊情况即使是===操作符也无能为力.

为改善这类情况，ECMAScript 6 规范新增了 Object.is()，这个方法与===很像，但同时也考虑 到了上述边界情形。这个方法必须接收两个参数：

```js
// 这些情况在不同 JavaScript 引擎中表现不同，但仍被认为相等
console.log(+0 === -0); // true 
console.log(+0 === 0); // true 
console.log(-0 === 0); // true 
// 要确定 NaN 的相等性，必须使用极为讨厌的 isNaN() 
console.log(NaN === NaN); // false 
console.log(isNaN(NaN)); // true

// 正确的 0、-0、+0 相等/不等判定
console.log(Object.is(+0, -0)); // false 
console.log(Object.is(+0, 0)); // true 
console.log(Object.is(-0, 0)); // false 
// 正确的 NaN 相等判定
console.log(Object.is(NaN, NaN)); // true 
```

## 创建对象

创建对象的方法有多种,使用 Object 构造函数或对象字面量可以方便地创建对象.下面介绍几种其他的创建对象的方法

### 工厂模式

```js
function createPerson(name, age, job) { 
 let o = new Object(); 
 o.name = name; 
 o.age = age; 
 o.job = job; 
 o.sayName = function() { 
 console.log(this.name); 
 }; 
 return o; 
} 
let person1 = createPerson("Nicholas", 29, "Software Engineer"); 
let person2 = createPerson("Greg", 27, "Doctor"); 
```

### 构造函数模式

```js
function Person(name, age, job){ 
 this.name = name; 
 this.age = age; 
 this.job = job; 
 this.sayName = function() { 
 console.log(this.name); 
 }; 
} 
let person1 = new Person("Nicholas", 29, "Software Engineer"); 
let person2 = new Person("Greg", 27, "Doctor"); 
person1.sayName(); // Nicholas 
person2.sayName(); // Greg 
```

要创建 Person 的实例，应使用 new 操作符。以这种方式调用构造函数会执行如下操作。 

(1) 在内存中创建一个新对象。

 (2) 这个新对象内部的[[Prototype]]特性被赋值为构造函数的 prototype 属性。

(3) 构造函数内部的 this 被赋值为这个新对象（即 this 指向新对象）。

 (4) 执行构造函数内部的代码（给新对象添加属性）。

 (5) 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

定义自定义构造函数可以确保实例被标识为特定类型，相比于工厂模式，这是一个很大的好处。在 这个例子中，person1 和 person2 之所以也被认为是 Object 的实例，是因为所有自定义对象都继承 自 Object.

#### 构造函数的缺点

构造函数的主要问题在于，其定义的方法会在每个实例上 都创建一遍。因此对前面的例子而言，person1 和 person2 都有名为 sayName()的方法，但这两个方 法不是同一个 Function 实例。我们知道，ECMAScript 中的函数是对象，因此每次定义函数时，都会 初始化一个对象。

但创建新 Function 实例的机制是一样的。因此不同实例上的函数虽然同名却不相等如下.

```console.log(person1.sayName == person2.sayName); // false ```

这个新问题可以通过原型模式来解决

### 原型模式

```js
function Person() {} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function() { 
 console.log(this.name); 
}; 
let person1 = new Person(); 
person1.sayName(); // "Nicholas" 
let person2 = new Person(); 
person2.sayName(); // "Nicholas" 
console.log(person1.sayName == person2.sayName); // true 
```

(1)isPrototypeOf()方法

可以使用 isPrototypeOf()方法确定两个对 象之间的这种关系。本质上，isPrototypeOf()会在传入参数的[[Prototype]]指向调用它的对象时 返回 true，如下所示：

```js
console.log(Person.prototype.isPrototypeOf(person1)); // true 
console.log(Person.prototype.isPrototypeOf(person2)); // true
```

(2)Object.getPrototypeOf()方法

返回参数的内部特性 [[Prototype]]的值。例如：

```js
console.log(Object.getPrototypeOf(person1) == Person.prototype); // true 
console.log(Object.getPrototypeOf(person1).name); // "Nicholas"
```

(3)setPrototypeOf()方法

Object 类型还有一个 setPrototypeOf()方法，可以向实例的私有特性[[Prototype]]写入一 个新值。这样就可以重写一个对象的原型继承关系：

```js
let biped = { 
 numLegs: 2 
}; 
let person = { 
 name: 'Matt' 
}; 
Object.setPrototypeOf(person, biped); 
console.log(person.name); // Matt 
console.log(person.numLegs); // 2 
console.log(Object.getPrototypeOf(person) === biped); // true
```

为避免使用 Object.setPrototypeOf()可能造成的性能下降，可以通过 Object.create()来创 建一个新对象，同时为其指定原型：

```js
let biped = { 
 numLegs: 2 
}; 
let person = Object.create(biped); 
person.name = 'Matt'; 
console.log(person.name); // Matt 
console.log(person.numLegs); // 2 
console.log(Object.getPrototypeOf(person) === biped); // true 
```

(4)hasOwnProperty()方法

hasOwnProperty()方法用于确定某个属性是在实例上还是在原型对象上。这个方法是继承自 Object 的，会在属性存在于调用它的对象实例上时返回 true.

#### in操作符

有两种方式使用 in 操作符：单独使用和在 for-in 循环中使用。

在单独使用时，in 操作符会在可 以通过对象访问指定属性时返回 true，无论该属性是在实例上还是在原型上。来看下面的例子：

```js
function Person() {} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function() { 
 console.log(this.name); 
}; 
let person1 = new Person(); 
let person2 = new Person(); 
console.log(person1.hasOwnProperty("name")); // false 
console.log("name" in person1); // true 
person1.name = "Greg"; 
console.log(person1.name); // "Greg"，来自实例
console.log(person1.hasOwnProperty("name")); // true 
console.log("name" in person1); // true 
console.log(person2.name); // "Nicholas"，来自原型
console.log(person2.hasOwnProperty("name")); // false 
console.log("name" in person2); // true 
delete person1.name; 
console.log(person1.name); // "Nicholas"，来自原型
console.log(person1.hasOwnProperty("name")); // false 
console.log("name" in person1); // true
```

只要通过对象可以访问，in 操作符就返回 true，而 hasOwnProperty()只有属性存在于实例上 时才返回 true。因此，只要 in 操作符返回 true 且 hasOwnProperty()返回 false，就说明该属性 是一个原型属性。

如果要确定某个属性是否存在于原型上，则可以像下 面这样同时使用 hasOwnProperty()和 in 操作符：

```js
function hasPrototypeProperty(object, name){ 
 return !object.hasOwnProperty(name) && (name in object); 
}
```

在 for-in 循环中使用 in 操作符时，可以通过对象访问且可以被枚举的属性都会返回，包括实例 属性和原型属性。遮蔽原型中不可枚举（[[Enumerable]]特性被设置为 false）属性的实例属性也会 在 for-in 循环中返回，因为默认情况下开发者定义的属性都是可枚举的。

#### 获取实例属性

要获得对象上所有**可枚举的实例属性**，可以使用 Object.keys()方法。这个方法接收一个对象作 为参数，返回包含该对象所有可枚举属性名称的字符串数组。

```js
function Person() {} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function() { 
 console.log(this.name); 
}; 
let keys = Object.keys(Person.prototype); 
console.log(keys); // "name,age,job,sayName" 
let p1 = new Person(); 
p1.name = "Rob"; 
p1.age = 31; 
let p1keys = Object.keys(p1); 
console.log(p1keys); // "[name,age]"
```

如果想列出**所有实例属性，无论是否可以枚举**，都可以使用 Object.getOwnPropertyNames()：

```js
let keys = Object.getOwnPropertyNames(Person.prototype); 
console.log(keys); // "[constructor,name,age,job,sayName]"
```

注意，返回的结果中包含了一个不可枚举的属性 constructor。

#### 原型的动态性

因为从原型上搜索值的过程是动态的，所以即使实例在修改原型之前已经存在，任何时候对原型对 象所做的修改也会在实例上反映出来。下面是一个例子：

```js
let friend = new Person(); 
Person.prototype.sayHi = function() { 
 console.log("hi"); 
}; 
friend.sayHi(); // "hi"，没问题！
```

虽然随时能给原型添加属性和方法，并能够立即反映在所有对象实例上，但**这跟重写整个原型是两 回事**。**实例的[[Prototype]]指针是在调用构造函数时自动赋值的**，这个指针即使把原型修改为不同 的对象也不会变。重写整个原型会切断最初原型与构造函数的联系，但实例引用的仍然是最初的原型。 记住，**实例只有指向原型的指针，没有指向构造函数的指针**。来看下面的例子：

```js
function Person() {} 
let friend = new Person(); 
Person.prototype = { 
 constructor: Person, 
 name: "Nicholas", 
 age: 29, 
 job: "Software Engineer", 
 sayName() { 
 console.log(this.name); 
 } 
}; 
friend.sayName(); // 错误
```

在这个例子中，Person 的新实例是在重写原型对象之前创建的。在调用 friend.sayName()的时 候，会导致错误。这是因为 firend 指向的原型还是最初的原型，而这个原型上并没有 sayName 属性。

#### 原型的问题

原型模式也不是没有问题。首先，它弱化了向构造函数传递初始化参数的能力，会导致所有实例默 认都取得相同的属性值。虽然这会带来不便，但还不是原型的最大问题。原型的最主要问题源自它的共 享特性。

```js
function Person() {} 
Person.prototype = { 
 constructor: Person, 
 name: "Nicholas", 
 age: 29, 
 job: "Software Engineer", 
 friends: ["Shelby", "Court"],
  sayName() { 
 console.log(this.name); 
 } 
}; 
let person1 = new Person(); 
let person2 = new Person(); 
person1.friends.push("Van"); 
console.log(person1.friends); // "Shelby,Court,Van" 
console.log(person2.friends); // "Shelby,Court,Van" 
console.log(person1.friends === person2.friends); // true
```

这里，Person.prototype 有一个名为 friends 的属性，它包含一个字符串数组。然后这里创建 了两个 Person 的实例。person1.friends 通过 push 方法向数组中添加了一个字符串。由于这个 friends 属性存在于 Person.prototype 而非 person1 上，新加的这个字符串也会在（指向同一个 数组的）person2.friends 上反映出来。如果这是有意在多个实例间共享数组，那没什么问题。但一 般来说，不同的实例应该有属于自己的属性副本。这就是实际开发中通常不单独使用原型模式的原因。

## 对象迭代

ECMAScript 2017 新增了两 个静态方法，用于将对象内容转换为序列化的——更重要的是可迭代的——格式。这两个静态方法 Object.values()和 Object.entries()接收一个对象，返回它们内容的数组。Object.values() 返回对象值的数组，Object.entries()返回键/值对的数组。

# 继承

## 原型链继承

```js
function SuperType() {
 this.property = true;
}
SuperType.prototype.getSuperValue = function() {
 return this.property;
};
function SubType() {
 this.subproperty = false;
}
// 继承 SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function () { 
	 return this.subproperty;
};
let instance = new SubType();
console.log(instance.getSuperValue()); // true
```

他们得关系如下图.

![原型链继承](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210712100515851.png)

### 原型链继承的问题

主要问题出现在原型中包含引用值的时候。

```js
function SuperType() {
 this.colors = ["red", "blue", "green"];
}
function SubType() {}
// 继承 SuperType
SubType.prototype = new SuperType();
let instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
let instance2 = new SubType();
console.log(instance2.colors); // "red,blue,green,black"
```

在这个例子中，SuperType 构造函数定义了一个 colors 属性，其中包含一个数组（引用值）。每 个 SuperType 的实例都会有自己的 colors 属性，包含自己的数组。但是，当 SubType 通过原型继承 SuperType 后，SubType.prototype 变成了 SuperType 的一个实例，因而也获得了自己的 colors 属性。这类似于创建了 SubType.prototype.colors 属性。最终结果是，SubType 的所有实例都会 共享这个 colors 属性。这一点通过 instance1.colors 上的修改也能反映到 instance2.colors 上就可以看出来。

优点：1、实例可继承的属性有：实例的构造函数的属性，父类构造函 数属性，父类原型的属性。（新实例不会继承父类实例的属性！）

缺点：1、新实例无法向父类构造函数传参。
　　　2、继承单一。
　　　3、所有新实例都会共享父类实例的属性。（原型上的属性是共 享的，一个实例修改了原型属性，另一个实例的原型属性 也会被修改！）

## 盗用构造函数

```js
function SuperType() {
 this.colors = ["red", "blue", "green"];
}
function SubType() {
 // 继承 SuperType
 SuperType.call(this);
}
let instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
let instance2 = new SubType();
console.log(instance2.colors); // "red,blue,green"
```

### 传参

```js
function SuperType(name){
 this.name = name;
}
function SubType() {
 // 继承 SuperType 并传参
 SuperType.call(this, "Nicholas");
 // 实例属性
 this.age = 29;
}
let instance = new SubType();
console.log(instance.name); // "Nicholas";
console.log(instance.age); // 29 
```

### 盗用构造参数的问题

盗用构造函数的主要缺点，也是使用构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此函数不能重用。此外，子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。

优点：1、只继承了父类构造函数的属性，没有继承父类原型的属性。
　　　2、解决了原型链继承缺点1、2、3。
　　　3、可以继承多个构造函数属性（call多个）。
　　　4、在子实例中可向父实例传参。

缺点：1、只能继承父类构造函数的属性。
　　　2、无法实现构造函数的复用。（每次用每次都要重新调用）
　　　3、每个新实例都有父类构造函数的副本，臃肿。

## 组合继承

基本的思路是使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。这样既可以把方 法定义在原型上以实现重用，又可以让每个实例都有自己的属性。

```js
function SuperType(name){
 this.name = name;
 this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function() {
 console.log(this.name);
};
function SubType(name, age){
 // 继承属性
 SuperType.call(this, name);
 this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function() {
 console.log(this.age);
};
let instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
instance1.sayName(); // "Nicholas";
instance1.sayAge(); // 29
let instance2 = new SubType("Greg", 27);
console.log(instance2.colors); // "red,blue,green"
instance2.sayName(); // "Greg";
instance2.sayAge(); // 27 
```

优点：1、可以继承父类原型上的属性，可以传参，可复用。
　　　2、每个新实例引入的构造函数属性是私有的。

缺点：调用了两次父类构造函数（耗内存），子类的构造函数会代替原型上的那个父类构造函数。

## 原型式继承

基本思路如下

```js
function object(o) {
 function F() {}
 F.prototype = o;
 return new F();
} 
```

下面给出一个例子

```js
let person = {
 name: "Nicholas",
 friends: ["Shelby", "Court", "Van"]
};
let anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
let yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
console.log(person.friends); // "Shelby,Court,Van,Rob,Barbie"
```

ECMAScript 5 通过增加 **Object.create()方法**将原型式继承的概念规范化了。这个方法接收两个 参数：作为新对象原型的对象，以及给新对象定义额外属性的对象（第二个可选）。在只有一个参数时， Object.create()与这里的 object()方法效果相同：

```js
let person = {
 name: "Nicholas",
 friends: ["Shelby", "Court", "Van"]
};
let anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
let yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
console.log(person.friends); // "Shelby,Court,Van,Rob,Barbie"
```

Object.create()的第二个参数与 Object.defineProperties()的第二个参数一样：每个新增 属性都通过各自的描述符来描述。以这种方式添加的属性会遮蔽原型对象上的同名属性。比如：

```js
let person = {
 name: "Nicholas",
 friends: ["Shelby", "Court", "Van"]
};
let anotherPerson = Object.create(person, {
 name: {
 value: "Greg"
 }
});
console.log(anotherPerson.name); // "Greg" 
```

原型式继承非常适合不需要单独创建构造函数，但仍然需要在对象间共享信息的场合。**但要记住， 属性中包含的引用值始终会在相关对象间共享，跟使用原型模式是一样的。**

## 寄生式继承

```js
function createAnother(original){
 let clone = object(original); // 通过调用函数创建一个新对象
 clone.sayHi = function() { // 以某种方式增强这个对象
 	console.log("hi");
 };
 return clone; // 返回这个对象
} 
```

可以像下面这样使用 createAnother()函数：

```js
let person = {
 name: "Nicholas",
 friends: ["Shelby", "Court", "Van"]
};
let anotherPerson = createAnother(person);
anotherPerson.sayHi(); // "hi" 
```

## 寄生式组合继承

组合继承其实也存在效率问题。最主要的效率问题就是父类构造函数始终会被调用两次：一次在是 创建子类原型时调用，另一次是在子类构造函数中调用。

```js
function SuperType(name) {
 this.name = name;
 this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function() {
 console.log(this.name);
};
function SubType(name, age){
 SuperType.call(this, name); // 第二次调用 SuperType()
 this.age = age;
}
SubType.prototype = new SuperType(); // 第一次调用SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
 console.log(this.age);
}; 
```

寄生式组合继承的基本模式如下所示：

```js
function inheritPrototype(subType, superType) {
 let prototype = object(superType.prototype); // 创建对象
 prototype.constructor = subType; // 增强对象
 subType.prototype = prototype; // 赋值对象
} 
```

如下例所示，调用 inheritPrototype()就可以实现前面例 子中的子类型原型赋值：

```js
function SuperType(name) {
 this.name = name;
 this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function() {
 console.log(this.name);
};
function SubType(name, age) {
 SuperType.call(this, name); 
  this.age = age;
}
inheritPrototype(SubType, SuperType);
SubType.prototype.sayAge = function() {
 console.log(this.age);
}; 

```

原型链仍然保持不变，因此 instanceof 操作符和 isPrototypeOf()方法正常有效。寄生式组合继承可以算是引用类型继承的最佳模式。

# 代理和反射

## 代理基础

代理是使用 Proxy 构造函数创建的。这个构造函数接收两个参数：目标对象和处理程序对象。缺 少其中任何一个参数都会抛出 TypeError。

```js
const target = {
 id: 'target'
};
const handler = {};
const proxy = new Proxy(target, handler);
// id 属性会访问同一个值
console.log(target.id); // target
console.log(proxy.id); // target
// 给目标属性赋值会反映在两个对象上
// 因为两个对象访问的是同一个值
target.id = 'foo';
console.log(target.id); // foo
console.log(proxy.id); // foo
// 给代理属性赋值会反映在两个对象上
// 因为这个赋值会转移到目标对象
proxy.id = 'bar';
console.log(target.id); // bar
console.log(proxy.id); // bar
// hasOwnProperty()方法在两个地方
// 都会应用到目标对象
console.log(target.hasOwnProperty('id')); // true
console.log(proxy.hasOwnProperty('id')); // true
// Proxy.prototype 是 undefined
// 因此不能使用 instanceof 操作符
console.log(target instanceof Proxy); // TypeError: Function has non-object prototype 'undefined' in instanceof check
console.log(proxy instanceof Proxy); // TypeError: Function has non-object prototype 'undefined' in instanceof check
// 严格相等可以用来区分代理和目标
console.log(target === proxy); // false
```

### 定义捕获器

使用代理的主要目的是可以定义捕获器（trap）。**捕获器就是在处理程序对象中定义的“基本操作的 拦截器”**。每个处理程序对象可以包含零个或多个捕获器，**每个捕获器都对应一种基本操作，可以直接 或间接在代理对象上调用。每次在代理对象上调用这些基本操作时，代理可以在这些操作传播到目标对 象之前先调用捕获器函数，从而拦截并修改相应的行为**。

例如，可以定义一个 get()捕获器，在 ECMAScript 操作以某种形式调用 get()时触发。下面的例 子定义了一个 get()捕获器：

```js
const target = {
 foo: 'bar'
};
const handler = {
 // 捕获器在处理程序对象中以方法名为键
 get() {
 return 'handler override';
 }
};
const proxy = new Proxy(target, handler); 
console.log(target.foo); // bar 
console.log(proxy.foo); // handler override 
console.log(target['foo']); // bar 
console.log(proxy['foo']); // handler override 
console.log(Object.create(target)['foo']); // bar 
console.log(Object.create(proxy)['foo']); // handler override 
```

这个操作在 JavaScript 代码中可以通过多种形式触发并被 get()捕获器拦截到。proxy[property]、proxy.property 或 Object.create(proxy)[property]等操作都会触发基本的 get()操作以获取属性。因此所有这些操作只要发生在代理对象上，就会触发 get()捕获 器。注意，**只有在代理对象上执行这些操作才会触发捕获器。在目标对象上执行这些操作仍然会产生正 常的行为。**

### 捕获器参数和反射 API

所有捕获器都可以访问相应的参数，基于这些参数可以重建被捕获方法的原始行为。比如，get() 捕获器会接收到**目标对象、要查询的属性和代理对象**三个参数。

```js
const target = {
 foo: 'bar'
};
const handler = {
 get(trapTarget, property, receiver) {
 console.log(trapTarget === target);
 console.log(property);
 console.log(receiver === proxy);
 }
};
const proxy = new Proxy(target, handler);
proxy.foo;
// true
// foo
// true
```

开发者并不需要手动重建原始行为，而是可以通过调用全局 Reflect 对象上（封装了原始行为）的同名方法来轻松重建。

```js
const target = { 
 foo: 'bar', 
 baz: 'qux' 
}; 
const handler = { 
 get(trapTarget, property, receiver) { 
 	let decoration = ''; 
 	if (property === 'foo') { 
 		decoration = '!!!'; 
 	} 
 	return Reflect.get(...arguments) + decoration; 
 } 
}; 
const proxy = new Proxy(target, handler); 
console.log(proxy.foo); // bar!!! 
console.log(target.foo); // bar 
console.log(proxy.baz); // qux 
console.log(target.baz); // qux
```

### 捕获器不变式

每个 捕获的方法都知道目标对象上下文、捕获函数签名，而捕获处理程序的行为必须遵循“捕获器不变式” （trap invariant）。捕获器不变式因方法不同而异，但通常都会防止捕获器定义出现过于反常的行为。

比如，如果目标对象有一个不可配置且不可写的数据属性，那么在捕获器返回一个与该属性不同的 值时，会抛出 TypeError：

```js
const target = {};
Object.defineProperty(target, 'foo', {
 configurable: false,
 writable: false,
 value: 'bar'
});
const handler = {
 get() {
 	return 'qux';
 }
};
const proxy = new Proxy(target, handler);
console.log(proxy.foo);
// TypeError 
```

### 撤销代理

```js
const target = {
 foo: 'bar'
};
const handler = {
 get() {
 	return 'intercepted';
 }
};
const { proxy, revoke } = Proxy.revocable(target, handler);
console.log(proxy.foo); // intercepted
console.log(target.foo); // bar
revoke();
console.log(proxy.foo); // TypeError
```

### 代理的问题和不足

## 代理捕获器与反射方法

代理可以捕获 13 种不同的基本操作。这些操作有各自不同的反射 API 方法、参数、关联 ECMAScript 操作和不变式。

不过，**对于 在代理对象上执行的任何一种操作，只会有一个捕获处理程序被调用。不会存在重复捕获的情况。**

## 代理模式

使用代理可以在代码中实现一些有用的编程模式。

### 跟踪属性访问

通过捕获 get、set 和 has 等操作，可以知道对象属性什么时候被访问、被查询。把实现相应捕获 器的某个对象代理放到应用中，可以监控这个对象何时在何处被访问过：

```js
const user = { 
 name: 'Jake' 
}; 
const proxy = new Proxy(user, { 
 get(target, property, receiver) { 
 console.log(`Getting ${property}`); 
 return Reflect.get(...arguments); 
 }, 
 set(target, property, value, receiver) { 
 console.log(`Setting ${property}=${value}`); 
 return Reflect.set(...arguments); 
 } 
}); 
proxy.name; // Getting name 
proxy.age = 27; // Setting age=27 
```

### 隐藏属性



# 客户端存储

## cookie

HTTP cookie 通常也叫作 cookie，最初用于在客户端存储会话信息。这个规范要求服务器在响应 HTTP 请求时，通过发送 Set-Cookie HTTP 头部包含会话信息。例如，下面是包含这个头部的一个 HTTP 响应：

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value
```

这个 HTTP 响应会设置一个名为"name"，值为"value"的 cookie。名和值在发送时都会经过 URL 编码。浏览器会存储这些会话信息，并在之后的每个请求中都会通过 HTTP 头部 cookie 再将它们发回服 务器，比如：

```
GET /index.jsl HTTP/1.1
Cookie: name=value
Other-header: other-header-value 
```

### 限制

- cookie 是与特定域绑定的。设置 cookie 后，它会与请求一起发送到创建它的域。不管域名下面的某一个地址需不需要这个 Cookie ，请求都会携带上完整的 Cookie，这样随着请求数的增多，其实会造成巨大的性能浪费的，因为请求携带了很多不必要的内容。这个限制能保证 cookie 中存储的信息只对被认可的接收者开放，不被其他域访问。

- 浏览器也会限制 cookie 的大小。大多数浏览器对 cookie 的限制是不超过 4096 字节，上下可以有一 个字节的误差。为跨浏览器兼容，最好保证 cookie 的大小不超过 4095 字节。这个大小限制适用于一个 域的所有 cookie，而不是单个 cookie。
-  由于 **Cookie 以纯文本的形式在浏览器和服务器中传递**，很容易被非法用户截获，然后进行一系列的篡改，在 Cookie 的有效期内重新发送给服务器，这是相当危险的。

### 有效期

默认情况下， 浏览器会话结束后会删除所有 cookie。不过，也可以设置删除 cookie 的时间,用于指定删除 cookie 的具体时间。这样即使关闭 浏览器 cookie 也会保留在用户机器上。把过期时间设置为过去的时间会立即删除 cookie。

### 小结

因为**所有 cookie 都会作为请求头部由浏览器发送给服务器，所以在 cookie 中保存大量信息可能会影 响特定域浏览器请求的性能。保存的 cookie 越大，请求完成的时间就越长**。即使浏览器对 cookie 大小有 限制，最好还是尽可能只通过 cookie 保存必要信息，以避免性能问题。

**Cookie数据会带到请求头的cookie字段里面，每次同主域名的请求中，都会传递数据，增加了网络请求的数据量，并且造成主域的污染。同时，cookie在不同浏览器上数量和大小都有限制**

注意 **不要在 cookie 中存储重要或敏感的信息。cookie 数据不是保存在安全的环境中，因 此任何人都可能获得**。应该避免把信用卡号或个人地址等信息保存在 cookie 中。

## web storage

Web Storage 的第 2 版定义了两个对象：localStorage 和 sessionStorage。localStorage 是永久存储机制，sessionStorage 是跨会话的存储机制。

Storage 的实例与其他 对象一样，但增加了以下方法。

```
 clear()：删除所有值；不在 Firefox 中实现。
 getItem(name)：取得给定 name 的值。
 key(index)：取得给定数值位置的名称。
 removeItem(name)：删除给定 name 的名/值对。
 setItem(name, value)：设置给定 name 的值。
```

### sessionStorage 对象

**sessionStorage 对象只存储会话数据，这意味着数据只会存储到浏览器关闭。**这跟浏览器关闭时 会消失的会话 cookie 类似。**存储在 sessionStorage 中的数据不受页面刷新影响**，可以在浏览器崩溃 并重启后恢复。（取决于浏览器，Firefox 和 WebKit 支持，IE 不支持。）

因为 sessionStorage 对象与服务器会话紧密相关，所以在运行本地文件时不能使用。存储在 sessionStorage 对象中的数据只能由最初存储数据的页面使用，在多页应用程序中的用处有限。

**sessionStorage 对象应该主要用于存储只在会话期间有效的小块数据。**如果需要跨会话持久存储 数据，可以使用localStorage。

### localStorage 对象

要访问同一个 localStorage 对象，页面必须来自同一个域（子域不可以）、在相同的端口上使用相同的协议。

### 存储事件

每当 Storage 对象发生变化时，都会在文档上触发 storage 事件。使用属性或 setItem()设置 值、使用 delete 或 removeItem()删除值，以及每次调用 clear()时都会触发这个事件。这个事件的 事件对象有如下 4 个属性。

```
 domain：存储变化对应的域。
 key：被设置或删除的键。
 newValue：键被设置的新值，若键被删除则为 null。
 oldValue：键变化之前的值。
```

可以使用如下代码监听 storage 事件：

```js
window.addEventListener("storage",
 (event) => alert('Storage changed for ${event.domain}'));
```

### 小结

两种存储方法的区别在于，**存储在 localStorage 中的数据会保留到通过 JavaScript 删除或者用户清除浏览器缓存。localStorage 数据不受页面刷新影响，也不会因关闭窗口、标签页或重新启动浏览 器而丢失。**

## IndexedDB

Indexed Database API 简称 IndexedDB，是浏览器中**存储结构化数据**的一个方案。

IndexedDB 的设计几乎完全是**异步**的。为此，大多数操作以请求的形式执行，这些请求会异步执行， 产生成功的结果或错误。绝大多数 IndexedDB 操作要求添加 onerror 和 onsuccess 事件处理程序来确 定输出。

### 使用

使用 IndexedDB 数据库的第一步是调用 indexedDB.open()方法，并给它传入一个要打开的数据 库名称。如果给定名称的数据库已存在，则会发送一个打开它的请求；如果不存在，则会发送创建并打 开这个数据库的请求。这个方法会返回 IDBRequest 的实例，可以在这个实例上添加 onerror 和 onsuccess 事件处理程序。举例如下：

```js
let db, 
 request, 
 version = 1; 
request = indexedDB.open("admin", version); 
request.onerror = (event) => 
 alert(`Failed to open: ${event.target.errorCode}`); 
request.onsuccess = (event) => { 
 db = event.target.result; 
}; 
```

如果 onsuccess 事件处理程序被调用，说明可以通过 **event.target.result** 访问数据库（IDBDatabase）实例了， 这个实例会保存到 db 变量中。之后，所有与数据库相关的操作都要通过 db 对象本身来进行。如果打 开数据库期间发生错误，**event.target.errorCode** 中就会存储表示问题的错误码。

### 对象存储

假设要存储包含用户名、密码等内容的用户记录。可以用如下对象来表示一条记录：

```js
let user = { 
 username: "007", 
 firstName: "James", 
 lastName: "Bond", 
 password: "foo" 
}; 
```

可以很容易看出最适合作为对象存储键的 username 属性。用户名必须全局唯一， 它也是大多数情况下访问数据的凭据。这个键很重要，因为创建对象存储时必须指定一个键。

数据库的版本决定了数据库模式，包括数据库中的对象存储和这些对象存储的结构。如果数据库还 不存在，open()操作会创建一个新数据库，然后触发 upgradeneeded 事件。可以为这个事件设置处 理程序，并在处理程序中创建数据库模式。如果数据库存在，而你指定了一个升级版的版本号，则会立 即触发 upgradeneeded 事件，因而可以在事件处理程序中更新数据库模式。

下面的代码演示了为存储上述用户信息如何创建对象存储：

```js
request.onupgradeneeded = (event) => { 
 const db = event.target.result; 
 // 如果存在则删除当前 objectStore。测试的时候可以这样做
 // 但这样会在每次执行事件处理程序时删除已有数据
 if (db.objectStoreNames.contains("users")) { 
 db.deleteObjectStore("users"); 
 } 
 db.createObjectStore("users", { keyPath: "username" }); 
}; 
```

### 事务

创建了对象存储之后，剩下的所有操作都是通过事务完成的。事务要通过调用数据库对象的 transaction()方法创建。

想要访问多个对象存储，可以给第 一个参数传入一个字符串数组：

```js
let transaction = db.transaction(["users", "anotherStore"]);
```

如前所述，每个事务都以只读方式访问数据。要修改访问模式，可以传入第二个参数。这个参数应 该是下列三个字符串之一："readonly"、"readwrite"或"versionchange"。比如：

```js
let transaction = db.transaction("users", "readwrite");
```



# 网络请求和跨域

## XMLHttpRequest

```js
let xhr = new XMLHttpRequest();
```

使用 XHR 对象首先要调用 open()方法，这个方法接收 3 个参数：请求类型（"get"、"post"等）、 请求 URL，以及表示请求是否异步的布尔值。send()方法接收一个参数，是作为请求体发送的数据。如果不需要发送请求体，则必须传 null， 因为这个参数在某些浏览器中是必需的。调用 send()之后，请求就会发送到服务器。下面是一个例子：

```js
xhr.open("get", "example.txt", false);
xhr.send(null); 
```

收到响应后，XHR 对象的以下属性会被填充上数据。

```
 responseText：作为响应体返回的文本。
 responseXML：如果响应的内容类型是"text/xml"或"application/xml"，那就是包含响应
数据的 XML DOM 文档。
 status：响应的 HTTP 状态。
 statusText：响应的 HTTP 状态描述。
```

## 跨源资源共享

跨源资源共享（CORS，Cross-Origin Resource Sharing）定义了浏览器与服务器如何实现跨源通信。 **CORS 背后的基本思路就是使用自定义的 HTTP 头部允许浏览器和服务器相互了解，以确实请求或响应 应该成功还是失败**。

对于简单的请求，比如 GET 或 POST 请求，没有自定义头部，而且请求体是 text/plain 类型， 这样的请求在发送时会有一个额外的头部叫 Origin。Origin 头部包含发送请求的页面的源（协议、 域名和端口），以便服务器确定是否为其提供响应。下面是 Origin 头部的一个示例：

> Origin: http://www.nczonline.net

如果服务器决定响应请求，那么应该发送 Access-Control-Allow-Origin 头部，包含相同的源； 或者如果资源是公开的，那么就包含"*"。比如：

> Access-Control-Allow-Origin: http://www.nczonline.net 

如果没有这个头部，或者有但源不匹配，则表明不会响应浏览器请求。否则，服务器就会处理这个 请求。注意，无论请求还是响应都不会包含 cookie 信息。

### 跨域方法

#### XMLHttpRequest 对象原生支持 CORS

在尝试访问不同源的资源时，这个行为 会被自动触发。要向不同域的源发送请求，可以使用标准 XHR对象并给 open()方法传入一个绝对 URL， 比如：

```js
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
 if (xhr.readyState == 4) {
 if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
 alert(xhr.responseText);
 } else {
 alert("Request was unsuccessful: " + xhr.status);
 }
 }
};
xhr.open("get", "http://www.somewhere-else.com/page/", true);
xhr.send(null);
```

跨域 XHR 对象允许访问 status 和 statusText 属性，也允许同步请求。出于安全考虑，跨域 XHR 对象也施加了一些额外限制:

```
 不能使用 setRequestHeader()设置自定义头部。
 不能发送和接收 cookie。
 getAllResponseHeaders()方法始终返回空字符串。
```

#### 图片探测

图片探测是利用<img>标签实现跨域通信的最早的一种技术。任何页面都可以跨域加载图片而不 必担心限制，因此这也是在线广告跟踪的主要方式。可以动态创建图片，然后通过它们的 onload 和 onerror 事件处理程序得知何时收到响应。

```js
let img = new Image();
img.onload = img.onerror = function() {
 alert("Done!");
};
img.src = "http://www.example.com/test?name=Nicholas"; 
```

图片探测频繁用于跟踪用户在页面上的点击操作或动态显示广告。当然，**图片探测的缺点是只能发送 GET 请求和无法获取服务器响应的内容**。**这也是只能利用图片探测实现浏览器与服务器单向通信的 原因**。

#### JSONP

JSONP 格式包含两个部分：回调和数据。回调是在页面接收到响应之后应该调用的函数，通常回调 函数的名称是通过请求来动态指定的。而数据就是作为参数传给回调函数的 JSON 数据。下面是一个典 型的 JSONP 请求：

> http://freegeoip.net/json/?callback=handleResponse
>
> 这个 JSONP 请求的 URL 是一个地理位置服务。JSONP 服务通常支持以查询字符串形式指定回调函 数的名称。比如这个例子就把回调函数的名字指定为 handleResponse()。

JSONP 调用是通过动态创建<script>元素并为 src 属性指定跨域 URL 实现的。此时的<script>与<img>元素类似，能够不受限制地从其他域加载资源。因为 JSONP 是有效的 JavaScript，所以 JSONP 响应在被加载完成之后会立即执行。比如下面这个例子：

```js
function handleResponse(response) {
 console.log(`
 You're at IP address ${response.ip}, which is in
 ${response.city}, ${response.region_name}`);
}
let script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild); 
```

JSONP 由于其简单易用，在开发者中非常流行。相比于图片探测，**使用 JSONP 可以直接访问响应， 实现浏览器与服务器的双向通信**。不过 JSONP 也有一些缺点。

首先，JSONP 是从不同的域拉取可执行代码。如果这个域并不可信，则可能在响应中加入恶意内容。 此时除了完全删除 JSONP 没有其他办法。在使用不受控的 Web 服务时，一定要保证是可以信任的。 第二个缺点是不好确定 JSONP 请求是否失败。虽然 HTML5 规定了<script>元素的 onerror 事件 处理程序，但还没有被任何浏览器实现。为此，开发者经常使用计时器来决定是否放弃等待响应。这种 方式并不准确，毕竟不同用户的网络连接速度和带宽是不一样的。

## Fetch API

### 基本用法

fetch()方法是暴露在全局作用域中的，包括主页面执行线程、模块和工作线程。调用这个方法， 浏览器就会向给定 URL 发送请求。

#### 分派请求

fetch()只有一个必需的参数 input。多数情况下，这个参数是要获取资源的 URL。这个方法返回 一个期约：

```js
let r = fetch('/bar');
console.log(r); // Promise <pending>
```

#### 读取响应

这要用到 text()方法。这个方法返回一个 期约，会解决为取得资源的完整内容：

```js
fetch('bar.txt')
 .then((response) => {
 response.text().then((data) => {
 console.log(data);
 });
 });
// bar.txt 的内容
```

#### 自定义选项

只使用 URL 时，fetch()会发送 GET 请求，只包含最低限度的请求头。要进一步配置如何发送请 求，需要传入可选的第二个参数 init 对象.

### 常见fetch请求模式

```js
let payload = JSON.stringify({
 foo: 'bar'
});
let jsonHeaders = new Headers({
 'Content-Type': 'application/json'
});
fetch('/send-me-json', {
 method: 'POST', // 发送请求体时必须使用一种 HTTP 方法
 body: payload,
 headers: jsonHeaders
}); 
```

### Headers对象

Headers 对象是所有外发请求和入站响应头部的容器。每个外发的 Request 实例都包含一个空的 Headers 实例，可以通过 Request.prototype.headers 访问，每个入站 Response 实例也可以通过 Response.prototype.headers 访问包含着响应头部的 Headers 对象。这两个属性都是可修改属性。 另外，使用 new Headers()也可以创建一个新实例。

#### 头部护卫

某些情况下，并非所有 HTTP 头部都可以被客户端修改，而 Headers 对象使用护卫来防止不被允 许的修改。不同的护卫设置会改变 set()、append()和 delete()的行为。

### Request 对象

#### 克隆 Request 对象

Fetch API 提供了两种不太一样的方式用于创建 Request 对象的副本：使用 Request 构造函数和使 用 clone()方法。这种克隆方式并不总能得到一模一样的副本。最明显的是，第一个请求的请求体会被标记为“已使用”：

```js
let r1 = new Request('https://foo.com',
 { method: 'POST', body: 'foobar' });
let r2 = new Request(r1);
console.log(r1.bodyUsed); // true
console.log(r2.bodyUsed); // false
```

第二种克隆 Request 对象的方式是使用 clone()方法，这个方法会创建一模一样的副本，任何值 都不会被覆盖。与第一种方式不同，这种方法不会将任何请求的请求体标记为“已使用”：

```js
let r1 = new Request('https://foo.com', { method: 'POST', body: 'foobar' });
let r2 = r1.clone();
console.log(r1.url); // https://foo.com/
console.log(r2.url); // https://foo.com/
console.log(r1.bodyUsed); // false
console.log(r2.bodyUsed); // false 
```

如果请求对象的 bodyUsed 属性为 true（即请求体已被读取），那么上述任何一种方式都不能用来 创建这个对象的副本。在请求体被读取之后再克隆会导致抛出 TypeError。

```js
let r = new Request('https://foo.com');
r.clone();
new Request(r);
// 没有错误
r.text(); // 设置 bodyUsed 为 true
r.clone();
// TypeError: Failed to execute 'clone' on 'Request': Request body is already used
new Request(r);
// TypeError: Failed to construct 'Request': Cannot construct a Request with a
Request object that has already been used.
```

#### 在 fetch()中使用 Request 对象

```js
let r = new Request('https://foo.com');
// 向 foo.com 发送 GET 请求
fetch(r);
// 向 foo.com 发送 POST 请求
fetch(r, { method: 'POST' });
```

fetch()会在内部克隆传入的 Request 对象。与克隆 Request 一样，fetch()也不能拿请求体已 经用过的 Request 对象来发送请求.关键在于，通过 fetch 使用 Request 会将请求体标记为已使用。也就是说，有请求体的 Request 只能在一次 fetch 中使用。（不包含请求体的请求不受此限制。）演示如下：

```js
let r = new Request('https://foo.com',
 { method: 'POST', body: 'foobar' });
fetch(r);
fetch(r);
// TypeError: Cannot construct a Request with a Request object that has already been used.
```

要想基于包含请求体的相同 Request 对象多次调用 fetch()，必须在第一次发送 fetch()请求前 调用 clone()：

```js
let r = new Request('https://foo.com',
 { method: 'POST', body: 'foobar' });
// 3 个都会成功
fetch(r.clone());
fetch(r.clone());
fetch(r); 
```

### Response 对象

## Web Socket

在 JavaScript 中创建 Web Socket 时，一个 HTTP 请求会发送到服务器以初始化连接。服务器响应后，连接使用 HTTP 的 Upgrade 头部从 HTTP 协议切换到 Web Socket 协议。这意味着 Web Socket 不能通过标准 HTTP 服务 器实现，而必须使用支持该协议的专有服务器。

 因为 Web Socket使用了自定义协议，所以 URL方案（scheme）稍有变化：不能再使用 http://或 https://， 而要使用 ws://和 wss://。前者是不安全的连接，后者是安全连接。在指定 Web Socket URL 时，必须包 含 URL 方案，因为将来有可能再支持其他方案。 

使用自定义协议而非 HTTP 协议的好处是，客户端与服务器之间可以发送非常少的数据，不会对 HTTP 造成任何负担。使用更小的数据包让 Web Socket 非常适合带宽和延迟问题比较明显的移动应用。 使用自定义协议的缺点是，定义协议的时间比定义 JavaScript API 要长。

#### 使用

要创建一个新的 Web Socket，就要实例化一个 WebSocket 对象并传入提供连接的 URL：

```js
let socket = new WebSocket("ws://www.example.com/server.php"); 
```

必须给 WebSocket 构造函数传入一个绝对 URL。同源策略不适用于 Web Socket，因此可以 打开到任意站点的连接。

浏览器会在初始化 WebSocket 对象之后立即创建连接。与 XHR 类似，WebSocket 也有一个 readyState 属性表示当前状态。不过，这个值与 XHR 中相应的值不一样。

```
 WebSocket.OPENING（0）：连接正在建立。
 WebSocket.OPEN（1）：连接已经建立。
 WebSocket.CLOSING（2）：连接正在关闭。
 WebSocket.CLOSE（3）：连接已经关闭。
```

任何时候都可以调用 close()方法关闭 Web Socket 连接：

```js
socket.close(); 
```

#### 发送和接收数据

```js
let socket = new WebSocket("ws://www.example.com/server.php");
let stringData = "Hello world!";
let arrayBufferData = Uint8Array.from(['f', 'o', 'o']);
let blobData = new Blob(['f', 'o', 'o']);
socket.send(stringData);
socket.send(arrayBufferData.buffer);
socket.send(blobData);
```

服务器向客户端发送消息时，WebSocket 对象上会触发 message 事件。这个 message 事件与其 他消息协议类似，可以通过 event.data 属性访问到有效载荷：

```js
socket.onmessage = function(event) {
 let data = event.data;
 // 对数据执行某些操作
}; 
```

#### 其他事件

WebSocket 对象在连接生命周期中有可能触发 3 个其他事件。

```
 open：在连接成功建立时触发。
 error：在发生错误时触发。连接无法存续。
 close：在连接关闭时触发。
```

WebSocket 对象不支持 DOM Level 2 事件监听器，因此需要使用 DOM Level 0 风格的事件处理程 序来监听这些事件：

```js
let socket = new WebSocket("ws://www.example.com/server.php");
socket.onopen = function() {
 alert("Connection established.");
};
socket.onerror = function() {
 alert("Connection error.");
};
socket.onclose = function() {
 alert("Connection closed.");
};
```

## 安全

```
/getuserinfo.php?id=23 
```

请求这个 URL，可以假定返回 ID 为 23 的用户信息。访问者可以将 23 改为 24 或 56，甚至其他任 何值。getuserinfo.php 文件必须知道访问者是否拥有访问相应数据的权限。否则，服务器就会大门敞开， 泄露所有用户的信息。

在未授权系统可以访问某个资源时，可以将其视为跨站点请求伪造（CSRF，cross-site request forgery） 攻击。未授权系统会按照处理请求的服务器的要求伪装自己。Ajax 应用程序，无论大小，都会受到 CSRF 攻击的影响，包括无害的漏洞验证攻击和恶意的数据盗窃或数据破坏攻击。

关于安全防护 Ajax 相关 URL 的一般理论认为，需要验证请求发送者拥有对资源的访问权限。可以 通过如下方式实现。

```
 要求通过 SSL 访问能够被 Ajax 访问的资源。
 要求每个请求都发送一个按约定算法计算好的令牌（token）。
```

# 函数

箭头函数定义函数的方式是使用 Function 构造函数。这个构造函数接收任意多个字符串参数，最 后一个参数始终会被当成函数体，而之前的参数都是新函数的参数。来看下面的例子：

```js
let sum = new Function("num1", "num2", "return num1 + num2"); // 不推荐
```

我们不推荐使用这种语法来定义函数，因为这段代码会被解释两次：第一次是将它当作常规 ECMAScript 代码，第二次是解释传给构造函数的字符串。这显然会影响性能。

**箭头函数不能使用 arguments、super 和 new.target，也不能用作构造函数。此外，箭头函数也没有 prototype 属性。**

## 理解参数

如果只 传了一个参数，然后把 arguments[1]设置为某个值，那么这个值并不会反映到第二个命名参数。这是 因为 **arguments 对象的长度是根据传入的参数个数，而非定义函数时给出的命名参数个数确定的**。

在使用默认参数时，**arguments 对象的值不反映参数的默认值，只反映传给函数的参数**。当然， 跟 ES5 严格模式一样，**修改命名参数也不会影响 arguments 对象，它始终以调用函数时传入的值为准**：

```js
function makeKing(name = 'Henry') {
 name = 'Louis';	//并不能修改参数中name的值
 return `King ${arguments[0]}`;
}
console.log(makeKing()); // 'King undefined'
console.log(makeKing('Louis')); // 'King Louis' 
```

**函数的默认参数只有在函数被调用时才会求值，不会在函数定义时求值。而且，计算默认值的函数只有在调用函数但未传相应参数时才会被调用。**

## 函数声明与函数表达式

**JavaScript 引擎在任何代码执行之前，会先读取函数声明，并在执行上下文中 生成函数定义。而函数表达式必须等到代码执行到它那一行，才会在执行上下文中生成函数定义**。来看 下面的例子：

```js
// 没问题
console.log(sum(10, 10));
function sum(num1, num2) {
 return num1 + num2;
} 
```

以上代码可以正常运行，因为函数声明会在任何代码执行之前先被读取并添加到执行上下文。这个 过程叫作**函数声明提升**（function declaration hoisting）。

如果把前面代码中的函数声明改为等价的函数表达式，那么执行的时候就会出错：

```js
// 会出错
console.log(sum(10, 10));
let sum = function(num1, num2) {
 return num1 + num2;
}; 
```

上面的代码之所以会出错，是因为这个函数定义包含在一个变量初始化语句中，而不是函数声明中。 这意味着代码如果没有执行到表达式声明的那一行，那么执行上下文中就没有函数的定义，所以上面的代码会 出错。这并不是因为使用 let 而导致的，使用 var 关键字也会碰到同样的问题

## 函数内部

### arguments

但 arguments 对象其实还有一个 callee 属性，是一个指向 arguments 对象所在函数的 指针。来看下面这个经典的阶乘函数：

```js
function factorial(num) {
 	if (num <= 1) {
 		return 1;
 	} else {
 		return num * factorial(num - 1);
 	}
} 
```

阶乘函数一般定义成递归调用的，就像上面这个例子一样。只要给函数一个名称，而且这个名称不 会变，这样定义就没有问题。但是，这个函数要正确执行就必须保证函数名是 factorial，从而导致 了紧密耦合。**使用 arguments.callee 就可以让函数逻辑与函数名解耦**：

```js
function factorial(num) {
 if (num <= 1) {
 return 1;
 } else {
 return num * arguments.callee(num - 1);
 }
} 
```

比如下面的情况

```js
let trueFactorial = factorial;
factorial = function() {
 return 0;
};
console.log(trueFactorial(5)); // 120
console.log(factorial(5)); // 0 
```

这里，trueFactorial 变量被赋值为 factorial，实际上把同一个函数的指针又保存到了另一个 位置。然后，factorial 函数又被重写为一个返回 0 的函数。如果像 factorial()最初的版本那样不 使用 arguments.callee，那么像上面这样调用 trueFactorial()就会返回 0。不过，通过将函数与 名称解耦，trueFactorial()就可以正确计算阶乘，而 factorial()则只能返回 0。

不过，在严格模式下运行的代码是不能访问 arguments.callee 的，因为访问会出错。此时，可 以使用命名函数表达式（named function expression）达到目的。比如：

```js
const factorial = (function f(num) {
 if (num <= 1) {
 return 1;
 } else {
 return num * f(num - 1);
 }
});
```

### this

this，它在标准函数和箭头函数中有不同的行为。

在标准函数中，this 引用的是把函数当成方法调用的上下文对象，这时候通常称其为 this 值（在 网页的全局上下文中调用函数时，this 指向 windows）。

**在箭头函数中，this引用的是定义箭头函数的上下文**。下面的例子演示了这一点。在对sayColor() 的两次调用中，this 引用的都是 window 对象，因为这个箭头函数是在 window 上下文中定义的：

```js
window.color = 'red';
let o = {
 color: 'blue'
};
let sayColor = () => console.log(this.color);
sayColor(); // 'red'
o.sayColor = sayColor;
o.sayColor(); // 'red' 
```

在事件回调或定时回调中调用某个函数时，this 值指向的并非想要的对象。此时将 回调函数写成箭头函数就可以解决问题。这是因为箭头函数中的 this 会保留定义该函数时的上下文：

```js
function King() {
 this.royaltyName = 'Henry';
 // this 引用 King 的实例
 setTimeout(() => console.log(this.royaltyName), 1000);
} 
function Queen() {
 this.royaltyName = 'Elizabeth';
 // this 引用 window 对象
 setTimeout(function() { console.log(this.royaltyName); }, 1000);
}
new King(); // Henry
new Queen(); // undefined
```

### new.target

ECMAScript 6 新增了检测函数是否使用 new 关键字调用的 new.target 属性。如果函数是正常调用的，则 new.target 的值是 undefined；如果是使用 new 关键字调用的，则 new.target 将引用被调用的 构造函数。

```js
function King() {
 if (!new.target) {
 throw 'King must be instantiated using "new"'
 }
 console.log('King instantiated using "new"');
}
new King(); // King instantiated using "new"
King(); // Error: King must be instantiated using "new"
```

## 函数属性与方法

ECMAScript 中的函数是对象，因此有属性和方法。每个函数都有两个属性：length 和 prototype。其中，length 属性保存**函数定义**的命名参数的个数

函数的两个方法apply()和call().到底是 使用 apply()还是 call()，完全取决于怎么给要调用的函数传参更方便。如果想直接传 arguments 对象或者一个数组，那就用 apply()；否则，就用 call()。

apply()和 call()真正强大的地方并不是给函数传参，而是控制函数调用上下文即函数体内 this 值的能力。使用 call()或 apply()的好处是可以将任意对象设置为任意函数的作用域，这样对象可以不用关 心方法。如下面的例子:

```js
window.color = 'red';
let o = {
 color: 'blue'
};
function sayColor() {
 console.log(this.color);
}
sayColor(); // red
sayColor.call(this); // red
sayColor.call(window); // red
sayColor.call(o); // blue
```

bind()方法会创建一个新的函数实例， 其 this 值会被绑定到传给 bind()的对象。比如：

```js
window.color = 'red';
var o = {
 color: 'blue'
};
function sayColor() {
 console.log(this.color);
}
let objectSayColor = sayColor.bind(o);
objectSayColor(); // blue 
```

这里，在 sayColor()上调用 bind()并传入对象 o 创建了一个新函数 objectSayColor()。 objectSayColor()中的 this 值被设置为 o，因此直接调用这个函数，即使是在全局作用域中调用， 也会返回字符串"blue"。

## 尾调用优化

即外部函数的返回值是一个内部函数的返回值。

我们知道，函数调用会在内存形成一个"调用记录"，又称"调用帧"（call frame），保存调用位置和内部变量等信息。如果在函数A的内部调用函数B，那么在A的调用记录上方，还会形成一个B的调用记录。等到B运行结束，将结果返回到A，B的调用记录才会消失。如果函数B内部还调用函数C，那就还有一个C的调用记录栈，以此类推。所有的调用记录，就形成一个["调用栈"](https://zh.wikipedia.org/wiki/调用栈)（call stack）。

尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用记录，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用记录，取代外层函数的调用记录就可以了。

```js
"use strict";
// 无优化：尾调用没有返回
function outerFunction() {
 innerFunction();
}
// 无优化：尾调用没有直接返回
function outerFunction() {
 let innerFunctionResult = innerFunction();
 return innerFunctionResult;
}
// 无优化：尾调用返回后必须转型为字符串
function outerFunction() {
 return innerFunction().toString();
}
// 无优化：尾调用是一个闭包
function outerFunction() {
 let foo = 'bar';
 function innerFunction() { return foo; }
 return innerFunction();
}
下面是几个符合尾调用优化条件的例子：
"use strict";
// 有优化：栈帧销毁前执行参数计算
function outerFunction(a, b) {
 return innerFunction(a + b);
}
// 有优化：初始返回值不涉及栈帧
function outerFunction(a, b) {
 if (a < b) {
 return a;
 }
 return innerFunction(a + b);
}
// 有优化：两个内部函数都在尾部
function outerFunction(condition) {
 return condition ? innerFunctionA() : innerFunctionB();
} 
```

这就叫做"尾调用优化"（Tail call optimization），即只保留内层函数的调用记录。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用记录只有一项，这将大大节省内存。这就是"尾调用优化"的意义。

### 优化代码

下面是一个通过递归 计算斐波纳契数列的函数：

```js
function fib(n) {
 if (n < 2) {
 return n;
 }
 return fib(n - 1) + fib(n - 2);
}
console.log(fib(0)); // 0
console.log(fib(1)); // 1
console.log(fib(2)); // 1
console.log(fib(3)); // 2
console.log(fib(4)); // 3
console.log(fib(5)); // 5
console.log(fib(6)); // 8
```

当然，解决这个问题也有不同的策略，比如把递归改写成迭代循环形式。不过，也可以保持递归实 现，但将其重构为满足优化条件的形式。为此可以使用两个嵌套的函数，外部函数作为基础框架，内部 函数执行递归：

```js
"use strict";
// 基础框架
function fib(n) {
 return fibImpl(0, 1, n);
}
// 执行递归
function fibImpl(a, b, n) {
 if (n === 0) {
 	return a;
 }
 	return fibImpl(b, a + b, n - 1);
}
```

## 闭包

如下代码

```js
function compare(value1, value2) { 
 if (value1 < value2) { 
 	return -1; 
 } else if (value1 > value2) { 
 	return 1; 
 } else { 
 	return 0; 
 } 
} 
let result = compare(5, 10); 

function createComparisonFunction(propertyName) {
 return function(object1, object2) {
 	let value1 = object1[propertyName];
 	let value2 = object2[propertyName];
 	if (value1 < value2) {
 		return -1;
 	} else if (value1 > value2) {
 		return 1;
 	} else {
 		return 0;
 	}
 };
} 
```

函数执行时，每个执行上下文中都会有一个包含其中变量的对象。**全局上下文中的叫变量对象，它 会在代码执行期间始终存在。而函数局部上下文中的叫活动对象，只在函数执行期间存在。**在定义 compare()函数时，就会为它创建作用域链，预装载全局变量对象，并保存在内部的[[Scope]]中。在 调用这个函数时，会创建相应的执行上下文，然后通过复制函数的[[Scope]]来创建其作用域链。接着 会创建函数的活动对象（用作变量对象）并将其推入作用域链的前端。在这个例子中，这意味着 compare() 函数执行上下文的作用域链中有两个变量对象：局部变量对象和全局变量对象。作用域链其实是一个包含指针的列表，每个指针分别指向一个变量对象，但物理上并不会包含相应的对象。

函数内部的代码在访问变量时，就会使用给定的名称从作用域链中查找变量。函数执行完毕后，局 部活动对象会被销毁，内存中就只剩下全局作用域。不过，闭包就不一样了。在一个函数内部定义的函数会把其包含函数的活动对象添加到自己的作用域链中。因此，在 createComparisonFunction()函数中，匿名函数的作用域链中实际上包含 createComparisonFunction()的活动对象。图展示了以下代码执行后的结果。

```js
let compare = createComparisonFunction('name');
let result = compare({ name: 'Nicholas' }, { name: 'Matt' });
```

![闭包](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210713155036645.png)

在 createComparisonFunction()返回匿名函数后，它的作用域链被初始化为包含 createComparisonFunction()的活动对象和全局变量对象。这样，匿名函数就可以访问到 createComparisonFunction()可以访问的所有变量。另一个有意思的副作用就是，createComparisonFunction()的 活动对象并不能在它执行完毕后销毁，因为匿名函数的作用域链中仍然有对它的引用。在 createComparisonFunction()执行完毕后，其执行上下文的作用域链会销毁，但它的活动对象仍然会保留 在内存中，直到匿名函数被销毁后才会被销毁：

```js
// 创建比较函数
let compareNames = createComparisonFunction('name');
// 调用函数
let result = compareNames({ name: 'Nicholas' }, { name: 'Matt' });
// 解除对函数的引用，这样就可以释放内存了
compareNames = null; 
```

这里，创建的比较函数被保存在变量 compareNames 中。把 compareNames 设置为等于 null 会 解除对函数的引用，从而让垃圾回收程序可以将内存释放掉。作用域链也会被销毁，其他作用域（除全 局作用域之外）也可以销毁。图也展示了调用 compareNames()之后作用域链之间的关系。

### this对象

在闭包中使用 this 会让代码变复杂。**如果内部函数没有使用箭头函数定义，则 this 对象会在运 行时绑定到执行函数的上下文**。**如果在全局函数中调用，则 this 在非严格模式下等于 window，在严 格模式下等于 undefined**。如果作为某个对象的方法调用，则 this 等于这个对象。匿名函数在这种情 况下不会绑定到某个对象，这就意味着 this 会指向 window，除非在严格模式下 this 是 undefined。 不过，由于闭包的写法所致，这个事实有时候没有那么容易看出来。来看下面的例子：

```js
window.identity = 'The Window';
let object = {
 identity: 'My Object',
 getIdentityFunc() {
 	return function() {
 		return this.identity;
 	};
 }
};
console.log(object.getIdentityFunc()()); // 'The Window'
```

为什么匿名函数没有使用其包含作用域（getIdentityFunc()）的 this 对象呢？

每个函数在被调用时都会自动创建两个特殊变量：this 和 arguments。内部函数永 远不可能直接访问外部函数的这两个变量。

## 立即调用的函数表达式

可以用它来模仿块级作用域,即在一个函数表达式内部声明变量，然后立即调用这个函数。这 样位于函数体作用域的变量就像是在块级作用域中一样。

```js
// IIFE
(function () {
 for (var i = 0; i < count; i++) {
 	console.log(i);
 }
})();
console.log(i); // 抛出错误
```

如下面的代码,想要实现的功能:

```js
let divs = document.querySelectorAll('div');
// 达不到目的！
for (var i = 0; i < divs.length; ++i) {
 divs[i].addEventListener('click', function() {
 console.log(i);
 });
} 
```

使用立即调用的函数表达式即可:

```js
let divs = document.querySelectorAll('div');
for (var i = 0; i < divs.length; ++i) {
 divs[i].addEventListener('click', (function(frozenCounter) {
 return function() {
 console.log(frozenCounter);
 };
 })(i));
}
```

相同的功能使用块级作用域变量更简单

```js
let divs = document.querySelectorAll('div');
for (let i = 0; i < divs.length; ++i) {
 divs[i].addEventListener('click', function() {
 	console.log(i);
 });
} 
```

## 私有变量

如果这个函数中创建了一个闭包，则这个闭包能通过其作用域链访问其外部 的变量。基于这一点，就可以创建出能够访问私有变量的公有方法。

特权方法（privileged method）是能够访问函数私有变量（及私有函数）的公有方法。在对象上有 两种方式创建特权方法。第一种是在构造函数中实现，比如：

```js
function MyObject() {
 // 私有变量和私有函数
 let privateVariable = 10;
 function privateFunction() {
 	return false;
 }
 // 特权方法
 this.publicMethod = function() {
 	privateVariable++;
 	return privateFunction();
 };
} 
```

这个模式是把所有私有变量和私有函数都定义在构造函数中。然后，再创建一个能够访问这些私有成员的特权方法。这样做之所以可行，是因为定义在构造函数中的特权方法其实是一个闭包，它具有访 问构造函数中定义的所有变量和函数的能力。在这个例子中，变量 privateVariable 和函数 privateFunction()只能通过 publicMethod()方法来访问。在创建 MyObject 的实例后，没有办法 直接访问 privateVariable 和 privateFunction()，唯一的办法是使用 publicMethod()。

# 期约与异步函数

## 异步编程

异步行为是为了优化因计算量大而 时间长的操作。如果在等待其他操作完成的同时，即使运行其他指令，系统也能保持稳定，那么这样做 就是务实的。 重要的是，异步操作并不一定计算量大或要等很长时间。只要你不想为等待某个异步操作而阻塞线 程执行，那么任何时候都可以使用。

### 以前的异步编程模式

在早期的 JavaScript 中，只支持定义回调函数 来表明异步操作完成。串联多个异步操作是一个常见的问题，通常需要深度嵌套的回调函数（俗称“回 调地狱”）来解决。

假设有以下异步函数，使用了 setTimeout 在一秒钟之后执行某些操作：

```js
function double(value) {
 setTimeout(() => setTimeout(console.log, 0, value * 2), 1000);
}
double(3);
// 6（大约 1000 毫秒之后）
```

setTimeout 可以定义一个 在指定时间之后会被调度执行的回调函数。对这个例子而言，1000 毫秒之后，JavaScript 运行时会把回 调函数推到自己的消息队列上去等待执行。推到队列之后，回调什么时候出列被执行对 JavaScript 代码 就完全不可见了。还有一点，double()函数在 setTimeout 成功调度异步操作之后会立即退出。

## 期约

## 异步函数

异步函数，也称为“async/await”（语法关键字）

### async

使用 async 关键字可以让函数具有异步特征，但总体上其代码仍然是同步求值的。而在参数或闭 包方面，异步函数仍然具有普通 JavaScript 函数的正常行为。正如下面的例子所示，foo()函数仍然会 在后面的指令之前被求值：

```js
async function foo() {
 console.log(1);
}
foo();
console.log(2);
// 1
// 2 
```

不过，异步函数如果使用 return 关键字返回了值（如果没有 return 则会返回 undefined），这 个值会被 Promise.resolve()包装成一个期约对象。异步函数始终返回期约对象。在函数外部调用这个函数可以得到它返回的期约：

```js
async function foo() {
 console.log(1);
 return 3;
}
// 给返回的期约添加一个解决处理程序
foo().then(console.log);
console.log(2);
// 1
// 2
// 3 
```

```js
// 返回一个期约
async function qux() {
 return Promise.resolve('qux');
}
qux().then(console.log);
// qux
//与在期约处理程序中一样，在异步函数中抛出错误会返回拒绝的期约：
async function foo() {
 console.log(1);
 throw 3;
}
// 给返回的期约添加一个拒绝处理程序
foo().catch(console.log);
console.log(2);
// 1
// 2
// 3 
//不过，拒绝期约的错误不会被异步函数捕获
async function foo() {
 console.log(1);
 Promise.reject(3);
}
// Attach a rejected handler to the returned promise
foo().catch(console.log);
console.log(2);
// 1
// 2
// Uncaught (in promise): 3
```

### await

await 关键字会暂停执行异步函数后面的代码，让出 JavaScript 运行时的执行线程。这个行 为与生成器函数中的 yield 关键字是一样的。await 关键字同样是尝试“解包”对象的值，然后将这 个值传给表达式，再异步恢复异步函数的执行。

# 事件

## 事件流

### 事件冒泡

IE 事件流被称为事件冒泡，这是因为事件被定义为**从最具体的元素（文档树中最深的节点）开始触 发，然后向上传播至没有那么具体的元素（文档）**。比如有如下 HTML 页面：

```js
<!DOCTYPE html>
<html>
<head>
 <title>Event Bubbling Example</title>
</head>
<body>
 <div id="myDiv">Click Me</div>
</body>
</html> 
```

在点击页面中的

元素后，click 事件会以如下顺序发生： 

(1)<div>

(2)body

(3)html

(4)document

也就是说,div元素，即被点击的元素，最先触发 click 事件。然后，click 事件沿 DOM 树一 路向上，在经过的每个节点上依次触发，直至到达 document 对象。

### 事件捕获

Netscape Communicator 团队提出了另一种名为事件捕获的事件流。事件捕获的意思是**最不具体的节 点应该最先收到事件，而最具体的节点应该最后收到事件**。事件捕获实际上是为了在事件到达最终目标 前拦截事件。如果前面的例子使用事件捕获，则点击div元素会以下列顺序触发 click 事件：

(1)document

(2)html

(3)body

(4)div

由于旧版本浏览器不支持，因此实际当中几乎不会使用事件捕获。通常建议使用事件冒泡，特殊情 况下可以使用事件捕获。

### 事件流

事件流分为 3 个阶段：**事件捕获**、**到达目标**和**事件冒泡**。事件捕获最先发生， 为提前拦截事件提供了可能。然后，实际的目标元素接收到事件。最后一个阶段是冒泡，最迟要在这个 阶段响应事件。仍以前面那个简单的 HTML 为例，点击div元素后会以下图的顺序触发事件.

![事件流触发顺序](D:\devApp\myblog\source\_posts\js高级程序设计\image-20210714144401148.png)

在 DOM 事件流中，实际的目标（div元素）在捕获阶段不会接收到事件。这是因为捕获阶段从 document 到html再到body就结束了。下一阶段，即会在div元素上触发事件的“到达目标” 阶段，通常在事件处理时被认为是冒泡阶段的一部分（稍后讨论）。然后，冒泡阶段开始，事件反向传 播至文档。

### 事件处理程序

#### DOM0事件处理程序

每个元素（包括 window 和 document）都有通常小写的事件处理程序属性，比如 onclick。只要 把这个属性赋值为一个函数即可：

```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
 console.log("Clicked");
}; 
```

像这样使用 DOM0 方式为事件处理程序赋值时，所赋函数被视为元素的方法。因此，**事件处理程 序会在元素的作用域中运行，即 this 等于元素**。下面的例子演示了使用 this 引用元素本身：

```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
 console.log(this.id); // "myBtn"
}; 
```

不仅仅是 id，在事件处 理程序里通过 this 可以访问元素的任何属性和方法。**以这种方式添加事件处理程序是注册在事件流的冒泡阶段的**。

#### DOM2事件处理程序

DOM2 Events 为事件处理程序的赋值和移除定义了两个方法：**addEventListener()和 removeEventListener()**。这两个方法暴露在所有 DOM 节点上，**它们接收 3 个参数：事件名、事件处理函 数和一个布尔值，true 表示在捕获阶段调用事件处理程序，false（默认值）表示在冒泡阶段调用事 件处理程序**。

```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
 console.log(this.id);
}, false); 
```

以上代码为按钮添加了会在事件冒泡阶段触发的 onclick 事件处理程序（因为最后一个参数值为 false）。与 DOM0 方式类似，这个事件处理程序同样在被附加到的元素的作用域中运行。使用 DOM2 方式的主要优势是可以为同一个事件添加多个事件处理程序。来看下面的例子：

```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
 console.log(this.id);
}, false);
btn.addEventListener("click", () => {
 console.log("Hello world!");
}, false); 
```

#### IE事件处理程序

IE 实现了与 DOM 类似的方法，即 **attachEvent()和 detachEvent()**。这两个方法接收两个同样 的参数：事件处理程序的名字和事件处理函数。因为 IE8 及更早版本只支持事件冒泡，所以使用 **attachEvent()添加的事件处理程序会添加到冒泡阶段**。

```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
 console.log(this === window); // true
}); 
```

注意，attachEvent()的第一个参数是"onclick"，而不是 DOM 的 addEventListener()方法 的"click"。

在 IE 中使用 attachEvent()与使用 DOM0 方式的主要区别是事件处理程序的作用域。使用 DOM0 方式时，事件处理程序中的 this 值等于目标元素。而使用 attachEvent()时，事件处理程序是在全 局作用域中运行的，因此 this 等于 window。

与使用 addEventListener()一样，使用 attachEvent()方法也可以给一个元素添加多个事件处 理程序。比如:

```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
 console.log("Clicked");
});
btn.attachEvent("onclick", function() {
 console.log("Hello world!");
}); 
```

不过，与 DOM 方法不同，这里的事件处理程序会以添加它们的顺序反向触发。换句话说，在点击例子中的按钮 后，控制台中会先打印出"Hello world!"，然后再打印出"Clicked"。

使用 attachEvent()添加的事件处理程序将使用 detachEvent()来移除，只要提供相同的参数。 与使用 DOM 方法类似，作为事件处理程序添加的匿名函数也无法移除。

## 事件对象

**preventDefault()方法用于阻止特定事件的默认动作**。比如，链接的默认行为就是在被单击时导 航到 href 属性指定的 URL。如果想阻止这个导航行为，可以在 onclick 事件处理程序中取消，如下 面的例子所示：

```js
let link = document.getElementById("myLink");
link.onclick = function(event) {
 event.preventDefault();
}; 
```

**stopPropagation()方法用于立即阻止事件流在 DOM 结构中传播，取消后续的事件捕获或冒泡**。 例如，直接添加到按钮的事件处理程序中调用 stopPropagation()，可以阻止 document.body 上注 册的事件处理程序执行。比如：

```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
 console.log("Clicked");
 event.stopPropagation();
};
document.body.onclick = function(event) {
 console.log("Body clicked");
}; 
```

如果这个例子中不调用stopPropagation()，那么点击按钮就会打印两条消息。但这里由于click 事件不会传播到 document.body，因此 onclick 事件处理程序永远不会执行。

**eventPhase 属性可用于确定事件流当前所处的阶段**。如果事件处理程序在捕获阶段被调用，则 eventPhase 等于 1；如果事件处理程序在目标上被调用，则 eventPhase 等于 2；如果事件处理程序 在冒泡阶段被调用，则 eventPhase 等于 3。不过要注意的是，虽然“到达目标”是在冒泡阶段发生的， 但其 eventPhase 仍然等于 2。下面的例子展示了 eventPhase 在不同阶段的值：

```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
 console.log(event.eventPhase); // 2
};
document.body.addEventListener("click", (event) => {
 console.log(event.eventPhase); // 1
}, true);	//第三个参数为true表示定义在事件捕获阶段
document.body.onclick = (event) => {
 console.log(event.eventPhase); // 3
}; 
```

## 事件委托

在 JavaScript 中，页面中事件处理程序的数量与页面整体性能直接相关。原 因有很多。**首先，每个函数都是对象，都占用内存空间，对象越多，性能越差**。其次，**为指定事件处理 程序所需访问 DOM 的次数会先期造成整个页面交互的延迟**。

**“过多事件处理程序”的解决方案是使用事件委托。事件委托利用事件冒泡，可以只使用一个事件 处理程序来管理一种类型的事件**。例如，click 事件冒泡到 document。这意味着可以为整个页面指定 一个 onclick 事件处理程序，而不用为每个可点击元素分别指定事件处理程序。

例如一个列表点击事件

```js
<ul id="myLinks">
 <li id="goSomewhere">Go somewhere</li>
 <li id="doSomething">Do something</li>
 <li id="sayHi">Say hi</li>
</ul> 
```

想让每个li被点击时输出不同的内容,老的方法如下

```js
let item1 = document.getElementById("goSomewhere");
let item2 = document.getElementById("doSomething");
let item3 = document.getElementById("sayHi");
item1.addEventListener("click", (event) => {
 location.href = "http:// www.wrox.com";
});
item2.addEventListener("click", (event) => {
 document.title = "I changed the document's title";
});
item3.addEventListener("click", (event) => {
 console.log("hi");
}); 
```

使用事件委托:

```js
let list = document.getElementById("myLinks");
list.addEventListener("click", (event) => {
 let target = event.target;
 switch(target.id) {
 case "doSomething":
 	document.title = "I changed the document's title";
 	break;
 case "goSomewhere":
 	location.href = "http:// www.wrox.com";
 	break;
 case "sayHi":
 	console.log("hi");
 	break;
 }
}); 
```

事件委托的优点:

 document 对象随时可用，任何时候都可以给它添加事件处理程序（不用等待 DOMContentLoaded 或 load 事件）。这意味着只要页面渲染出可点击的元素，就可以无延迟地起作用。 

 节省花在设置页面事件处理程序上的时间。只指定一个事件处理程序既可以节省 DOM 引用，也 可以节省时间。 

 减少整个页面所需的内存，提升整体性能。

### 删除事件处理程序

把事件处理程序指定给元素后，在浏览器代码和负责页面交互的 JavaScript 代码之间就建立了联系。 这种联系建立得越多，页面性能就越差。除了通过事件委托来限制这种连接之外，还应该及时删除不用 的事件处理程序。很多 Web 应用性能不佳都是由于无用的事件处理程序长驻内存导致的。

导致这个问题的原因主要有两个。**第一个是删除带有事件处理程序的元素**。比如通过真正的 DOM方法 removeChild()或 replaceChild()删除节点。最常见的还是使用 innerHTML 整体替换页面的 某一部分。这时候，被 innerHTML 删除的元素上如果有事件处理程序，就不会被垃圾收集程序正常清 理。比如下面的例子

```js
<div id="myDiv">
 <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
 let btn = document.getElementById("myBtn");
 btn.onclick = function() {
 // 执行操作
 document.getElementById("myDiv").innerHTML = "Processing...";
 // 不好！
 };
</script> 
```

这里的按钮在div元素中。单击按钮，会将自己删除并替换为一条消息，以阻止双击发生。这是 很多网站上常见的做法。问题在于，按钮被删除之后仍然关联着一个事件处理程序。在div元素上设 置 innerHTML 会完全删除按钮，但事件处理程序仍然挂在按钮上面。某些浏览器，特别是 IE8 及更早 版本，在这时候就会有问题了。很有可能元素的引用和事件处理程序的引用都会残留在内存中。如果知 道某个元素会被删除，那么最好在删除它之前手工删除它的事件处理程序，比如：

```js
<div id="myDiv">
 <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
 let btn = document.getElementById("myBtn");
 btn.onclick = function() {
 // 执行操作
 btn.onclick = null; // 删除事件处理程序
 document.getElementById("myDiv").innerHTML = "Processing...";
 };
</script>
```

但也要注意，在事件处理程序中删除按钮会阻止事件冒泡。只有事件目标仍然存在于文档中时，事 件才会冒泡。( 事件委托也有助于解决这种问题。如果提前知道页面某一部分会被使用innerHTML 删除，就不要直接给该部分中的元素添加事件处理程序了。把事件处理程序添加到更高层 级的节点上同样可以处理该区域的事件。)

另一个可能导致内存中残留引用的问题是**页面卸载**。同样，IE8 及更早版本在这种情况下有很多问 题，不过好像所有浏览器都会受这个问题影响。如果在页面卸载后事件处理程序没有被清理，则它们仍然会残留在内存中。之后，浏览器每次加载和卸载页面（比如通过前进、后退或刷新），内存中残留对 象的数量都会增加，这是因为事件处理程序不会被回收。

一般来说，最好在 onunload 事件处理程序中趁页面尚未卸载先删除所有事件处理程序。这时候也 能体现使用事件委托的优势，因为事件处理程序很少，所以很容易记住要删除哪些。关于卸载页面时的 清理，可以记住一点：onload 事件处理程序中做了什么，最好在 onunload 事件处理程序中恢复。

## 模拟事件

### DOM事件模拟

任何时候，都可以使用 **document.createEvent()方法**创建一个 event 对象。这个方法接收一个 参数，此参数是一个表示要创建事件类型的字符串。(包括UIEvents,MouseEvents,HTMLEvents)

创建 event 对象之后，需要使用事件相关的信息来初始化。每种类型的 event 对象都有特定的方法， 可以使用相应数据来完成初始化。方法的名字并不相同，这取决于调用 createEvent()时传入的参数。

事件模拟的最后一步是触发事件。为此要使用 **dispatchEvent()方法**，这个方法存在于所有支持 事件的 DOM 节点之上。dispatchEvent()方法接收一个参数，即表示要触发事件的 event 对象。调 用 dispatchEvent()方法之后，事件就“转正”了，接着便冒泡并触发事件处理程序执行。

下面举一个例子

```js
let btn = document.getElementById("myBtn"); 
// 创建 event 对象
let event = document.createEvent("MouseEvents"); 
// 初始化 event 对象
event.initMouseEvent("click", true, true, document.defaultView, 
 0, 0, 0, 0, 0, false, false, false, false, 0, null); 
// 触发事件
btn.dispatchEvent(event);
```
