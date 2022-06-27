---
title: JavaScript高级程序设计(下)
date: 2021-07-16 09:00:00
tags: js
---

# BOM

包括window对象,location对象,navigator对象,screen对象,history对象.

## location 对象

这个对象独特的地方在于，它既是 window 的属性，也是 document 的属性。也就是说， window.location 和 document.location 指向同一个对象。

location 对象不仅保存着当前加载文 档的信息，也保存着把 URL 解析为离散片段后能够通过属性访问的信息。

![image-20211013162903155](image-20211013162903155.png)

### 操作地址

可以通过修改 location 对象修改浏览器的地址。**最常见的是使用 assign()方法并传入一 个 URL.**

```js
location.assign("http://www.wrox.com");
```

如果**给 location.href 或 window.location 设置一个 URL，也会以同一个 URL 值调用 assign()方法**。比 如，下面两行代码都会执行与显式调用 assign()一样的操作：

````js
window.location = "http://www.wrox.com"; 
location.href = "http://www.wrox.com";
````

在以前面提到的方式修改 URL 之后，浏览器历史记录中就会增加相应的记录。当用户单击“后退” 按钮时，就会导航到前一个页面。**如果不希望增加历史记录，可以使用 replace()方法**。这个方法接 收一个 URL 参数，但重新加载后不会增加历史记录。调用 replace()之后，用户不能回到前一页。比 如下面的例子：

```html
<!DOCTYPE html> 
<html> 
<head> 
 <title>You won't be able to get back here</title> 
</head> 
<body> 
 <p>Enjoy this page for a second, because you won't be coming back here.</p> 
 <script> 
 setTimeout(() => location.replace("http://www.wrox.com/"), 1000); 
 </script> 
</body> 
</html> 
```

**最后一个修改地址的方法是 reload()**，它能重新加载当前显示的页面。调用 reload()而不传参 数，页面会以最有效的方式重新加载。也就是说，如果页面自上次请求以来没有修改过，浏览器可能会 从缓存中加载页面。如果想强制从服务器重新加载，可以像下面这样给 reload()传个 true：

```js
location.reload(); // 重新加载，可能是从缓存加载
location.reload(true); // 重新加载，从服务器加载
```

脚本中位于 reload()调用之后的代码可能执行也可能不执行，这取决于网络延迟和系统资源等因 素。为此，最好把 reload()作为最后一行代码。

## navigator对象

### 检测插件

除 IE10 及更低版本外的浏览器，都可以通 过 plugins 数组来确定。这个数组中的每一项都包含如下属性。

 name：插件名称。

 description：插件介绍。 

 filename：插件的文件名。 

 length：由当前插件处理的 MIME 类型数量。

## history对象

history 对象表示当前窗口首次使用以来用户的导航历史记录。因为 history 是 window 的属性， 所以每个 window 都有自己的 history 对象。

### 导航

go()方法可以在用户历史记录中沿任何方向导航，可以前进也可以后退。这个方法只接收一个参数， 这个参数可以是一个整数，表示前进或后退多少步。负值表示在历史记录中后退（类似点击浏览器的“后 退”按钮），而正值表示在历史记录中前进（类似点击浏览器的“前进”按钮）。下面来看几个例子：

```js
// 后退一页
history.go(-1); 
// 前进一页
history.go(1); 
// 前进两页
history.go(2); 

```

# DOM

## 节点层次

DOM Level 1 描述了名为 Node 的接口，这个接口是所有 DOM 节点类型都必须实现的。Node 接口 在 JavaScript中被实现为 Node 类型，在除 IE之外的所有浏览器中都可以直接访问这个类型。在 JavaScript 中，所有节点类型都继承 Node 类型，因此所有类型都共享相同的基本属性和方法。

每个节点都有 **nodeType 属性**，表示该节点的类型。节点类型由定义在 Node 类型上的 12 个数值 常量表示,节点类型可通过与这些常量比较来确定，比如：

```js
if (someNode.nodeType == Node.ELEMENT_NODE){
 alert("Node is an element.");
} 
if (someNode.nodeType == 1){
 value = someNode.nodeName; // 会显示元素的标签名
} 
//在这个例子中，先检查了节点是不是元素。如果是，则将其 nodeName 的值赋给一个变量。
//对元素而言，nodeName 始终等于元素的标签名，而 nodeValue 则始终为 null。
```

### Node类型

#### 节点关系

每个节点都有一个 childNodes 属性，其中包含一个 NodeList 的实例。NodeList 是一个**类数组**对象，用于存储可以按位置存取的有序节点。注意，NodeList 并不是 Array 的实例，但可以使用中括 号访问它的值，而且它也有 length 属性。**NodeList 对象独特的地方在于，它其实是一个对 DOM 结 构的查询，因此 DOM 结构的变化会自动地在 NodeList 中反映出来。我们通常说 NodeList 是实时的 活动对象，而不是第一次访问时所获得内容的快照(后面的querySelectorAll()方法返回的NodeLis是快照,而不是实时的)。**

```js
//以下两种方法都是获取节点的方法
//即既可以用item()获得,也可以用中括号
let firstChild = someNode.childNodes[0];
let secondChild = someNode.childNodes.item(1);
let count = someNode.childNodes.length; 
```

使用 Array.prototype. slice()可以像前面介绍 arguments 时一样把 NodeList 对象转换为数组。

```js
let arrayOfNodes = Array.prototype.slice.call(someNode.childNodes,0);
```

当然，使用 ES6 的 Array.from()静态方法，可以替换这种笨拙的方式：

```js
let arrayOfNodes = Array.from(someNode.childNodes);
```

每个节点都有一个 parentNode 属性，指向其 DOM 树中的父元素。childNodes 中的所有节点都 有同一个父元素，因此它们的 parentNode 属性都指向同一个节点。此外，childNodes 列表中的每个 节点都是同一列表中其他节点的同胞节点。而使用 **previousSibling 和 nextSibling** 可以在这个列 表的节点间导航。这个列表中第一个节点的 previousSibling 属性是 null，最后一个节点的 nextSibling 属性也是 null，如下所示：

```js
if (someNode.nextSibling === null){
 alert("Last node in the parent's childNodes list.");
} else if (someNode.previousSibling === null){
 alert("First node in the parent's childNodes list.");
} 
```

父节点和它的第一个及最后一个子节点也有专门属性：**firstChild** 和 **lastChild** 分别指向 childNodes 中的第一个和最后一个子节点。someNode.firstChild 的值始终等于 someNode. childNodes[0]，而 someNode.lastChild 的值始终等于 someNode.childNodes[someNode. childNodes.length-1]。

#### 操纵节点

- **appendChild()**

appendChild()，用于在 childNodes 列表末尾添加节点。如果把文档中已经存在的节点传给 appendChild()，则这个节点会从之前的位置被转移到新位置。 即使 DOM 树通过各种关系指针维系，一个节点也不会在文档中同时出现在两个或更多个地方。

- **insertBefore()**

如果想把节点放到 childNodes 中的特定位置而不是末尾，则可以使用 insertBefore()方法。 这个方法接收两个参数：要插入的节点和参照节点。调用这个方法后，要插入的节点会变成参照节点的 前一个同胞节点，并被返回。如果参照节点是 null，则 insertBefore()与 appendChild()效果相 同.

- **replaceChild()**

replaceChild()方法接收两个参数：要插入的节点和要替换的节点。要替换的节点会被返回并从文档 树中完全移除，要插入的节点会取而代之。

- **removeChild()**

要移除节点而不是替换节点，可以使用 removeChild()方法。这个方法接收一个参数，即要移除 的节点。被移除的节点会被返回.

以上四个方法都是通过父节点操纵其子元素.

#### 其他方法

所有节点类型还共享了两个方法。**第一个是 cloneNode()**，会返回与调用它的节点一模一样的节 点。cloneNode()方法接收一个布尔值参数，表示是否深复制。在传入 true 参数时，会进行深复制， 即复制节点及其整个子 DOM 树。如果传入 false，则只会复制调用该方法的节点。复制返回的节点属 于文档所有，但尚未指定父节点，所以可称为孤儿节点（orphan）。

cloneNode()方法不会复制添加到 DOM 节点的 JavaScript 属性，比如事件处理程 序。这个方法只复制 HTML 属性，以及可选地复制子节点。除此之外则一概不会复制。

**normalize()**。这个方法唯一的任务就是处理**文档子树中的文本节点**。由于解析器实现的差异或 DOM 操作等原因，可能会出现并不包含文本的文本节点，或者文本节点之间互为同胞关系。在节点上调用 normalize()方法会检测这个节点的所有后代，从中搜索上述两种 情形。如果发现空文本节点，则将其删除；如果两个同胞节点是相邻的，则将其合并为一个文本节点。

### Document 类型

特点

```
 nodeType 等于 9；
 nodeName 值为"#document"；
 nodeValue 值为 null；
 parentNode 值为 null；
 ownerDocument 值为 null；
 子节点可以是 DocumentType（最多一个）、Element（最多一个）、ProcessingInstruction
或 Comment 类型。
```

Document 类型是 JavaScript 中表示文档节点的类型。在浏览器中，文档对象 document 是 HTMLDocument 的实例（HTMLDocument 继承 Document），表示整个 HTML 页面。document 是 window 对象的属性，因此是一个全局对象。

提供了两个访问子节点的快捷方式。**第一个是 documentElement 属 性，始终指向 HTML 页面中的元素。**虽然 document.childNodes 中始终有元素，但 使用 documentElement 属性可以更快更直接地访问该元素。

```html
<html>
 <body>
 </body>
</html> 
```

```js
let html = document.documentElement; // 取得对<html>的引用
alert(html === document.childNodes[0]); // true
alert(html === document.firstChild); // true 
```

作为 HTMLDocument 的实例，**document 对象还有一个 body 属性，直接指向body元素。**因为 这个元素是开发者使用最多的元素，所以 JavaScript 代码中经常可以看到 document.body，比如：

```js
let body = document.body; // 取得对<body>的引用
let doctype = document.doctype; // 取得对<!doctype>的引用
```

#### 文档信息

```js
// 读取文档标题
let originalTitle = document.title;
// 修改文档标题
document.title = "New page title";

// 取得完整的 URL,URL 包含当前页面的完整 URL（地址栏中的 URL）
let url = document.URL;	
// 取得域名
let domain = document.domain;
// 取得来源
let referrer = document.referrer;
```

URL 跟域名是相关的。比如，如果 document.URL 是 http://www.wrox.com/WileyCDA/，则 document.domain 就是 www.wrox.com。

在这些属性中，只有 domain 属性是可以设置的。出于安全考虑，给 domain 属性设置的值是有限制的。如果 URL包含子域名如 p2p.wrox.com，则可以将 domain 设置为"wrox.com"（URL包含“www” 时也一样，比如 www.wrox.com）。不能给这个属性设置 URL 中不包含的值，比如：

```js
// 页面来自 p2p.wrox.com
document.domain = "wrox.com"; // 成功
document.domain = "nczonline.net"; // 出错！
```

当页面中包含来自某个不同子域的窗格（）或内嵌窗格（<iframe）时，设置
document.domain 是有用的。因为跨源通信存在安全隐患，所以不同子域的页面间无法通过 JavaScript通信。此时，在每个页面上把 document.domain 设置为相同的值，这些页面就可以访问对方的 JavaScript对象了。比如，一个加载自 www.wrox.com 的页面中包含一个内嵌窗格，其中的页面加载自p2p.wrox.com。这两个页面的 document.domain 包含不同的字符串，内部和外部页面相互之间不能访问对方的 JavaScript 对象。如果每个页面都把 document.domain 设置为wrox.com，那这两个页面之间就可以通信了。
浏览器对 domain 属性还有一个限制，即这个属性一旦放松就不能再收紧。比如，把
document.domain 设置为"wrox.com"之后，就不能再将其设置回"p2p.wrox.com"，后者会导致错
误，比如:

```js
// 页面来自 p2p.wrox.com
document.domain = "wrox.com"; // 放松，成功
document.domain = "p2p.wrox.com"; // 收紧，错误！
```

#### 定位元素

**getElementById()**和 **getElementsByTagName()**就是 Document 类型提供的两个方法。

getElementsByTagName()返回包含零个或多个元素的 NodeList。在 HTML 文档中，这个方法返回一个 HTMLCollection 对象。考虑到二者都是“实时”列表，HTMLCollection 与 NodeList 是很相似的。HTMLCollection 对象还有一个额外的方法 namedItem()，可通过标签的 name 属性取得某一项 的引用。例如，假设页面中包含如下的<img>元素：

```html
<img src="myimage.gif" name="myImage"> 
```

那么也可以像这样从 images 中取得对这个元素的引用：

```js
let myImage = images.namedItem("myImage"); 
//也可以使用中括号直接获取
let myImage = images["myImage"]; 
```

对 HTMLCollection 对象而言，中括号既可以接收数值索引，也可以接收字符串索引。而在后台， **数值索引会调用 item()，字符串索引会调用 namedItem()。**

HTMLDocument 类型上定义的获取元素的第三个方法是 **getElementsByName()**。getElementsByName()方法也返回 HTMLCollection。

### Element 类型

特点

```
 nodeType 等于 1；
 nodeName 值为元素的标签名；
 nodeValue 值为 null；
 parentNode 值为 Document 或 Element 对象；
 子节点可以是 Element、Text、Comment、ProcessingInstruction、CDATASection、
EntityReference 类型。
```

所有这些都可以用来获取对应的属性值，也可以用来修改相应的值。比如有下面的 HTML 元素：

```html
<div id="myDiv" class="bd" title="Body text" lang="en" dir="ltr"></div> 
```

```js
let div = document.getElementById("myDiv");
alert(div.id); // "myDiv"
alert(div.className); // "bd"
alert(div.title); // "Body text"
alert(div.lang); // "en"
alert(div.dir); // "ltr" 
```

#### 取得属性

与属性相关的 DOM 方法 主要有 3 个：getAttribute()、setAttribute()和 removeAttribute()。

```js
let div = document.getElementById("myDiv");
alert(div.getAttribute("id")); // "myDiv"
alert(div.getAttribute("class")); // "bd"
alert(div.getAttribute("title")); // "Body text"
alert(div.getAttribute("lang")); // "en"
alert(div.getAttribute("dir")); // "ltr"
```

这种获取属性的方法和前面的不一样,重点为class,前面为className,这里为class,即元素中定义的什么属性名就是什么

#### 创建元素

可以使用 document.createElement()方法创建新元素。这个方法接收一个参数，即要创建元素 的标签名。

```js
let div = document.createElement("div"); 
```

可以使用 appendChild()、insertBefore()或 replaceChild()。 比如，以下代码会把刚才创建的元素添加到文档的元素中：

```js
document.body.appendChild(div);
```

# DOM编程

## 动态脚本

动态脚本就是在页面初始加载时不存在，之后又通过 DOM 包含的脚本。与对应的 HTML 元素一样，有两种方式通过<script>动态为网页添加脚本：引入外部文件和直接插入源代码。

动态加载外部文件很容易实现，比如下面的:

```html
<script src="foo.js"></script> 
```

可以像这样通过 DOM 编程创建这个节点：

```js
let script = document.createElement("script");
script.src = "foo.js";
document.body.appendChild(script); 
```

另一个动态插入 JavaScript 的方式是嵌入源代码，如下面的例子所示：

```html
<script>
 function sayHi() {
 	alert("hi");
  }
</script>
```

使用 DOM，可以实现以下逻辑：

```js
let script = document.createElement("script");
script.appendChild(document.createTextNode("function sayHi(){alert('hi');}"));
document.body.appendChild(script); 
```

## 动态样式

CSS 样式在 HTML 页面中可以通过两个元素加载。<link>元素用于包含 CSS 外部文件，而<style>元素用于添加嵌入样式。与动态脚本类似，动态样式也是页面初始加载时并不存在，而是在之后才添加 到页面中的。

来看下面这个典型的元素：

```html
<link rel="stylesheet" type="text/css" href="styles.css">
```

这个元素很容易使用 DOM 编程创建出来：

```js
let link = document.createElement("link");
link.rel = "stylesheet";
link.type = "text/css";
link.href = "styles.css";
let head = document.getElementsByTagName("head")[0];
head.appendChild(link);
```

通过外部文件加载样式是一个异步过程。因此，样式的加载和正执行的 JavaScript 代码并没有先后 顺序。一般来说，也没有必要知道样式什么时候加载完成。

另一种定义样式的方式是使用<script>元素包含嵌入的 CSS 规则，例如：

```html
<style type="text/css">
body {
 background-color: red;
}
</style>
```

逻辑上，下列 DOM 代码会有同样的效果：

```js
let style = document.createElement("style");
style.type = "text/css";
style.appendChild(document.createTextNode("body{background-color:red}"));
let head = document.getElementsByTagName("head")[0];
head.appendChild(style); 
```

### 使用NodeList

理解 NodeList 对象和相关的 NamedNodeMap、HTMLCollection，是理解 DOM 编程的关键。这 3 个集合类型都是“实时的”，意味着文档结构的变化会实时地在它们身上反映出来，因此它们的值始终代表最新的状态。实际上，NodeList 就是基于 DOM 文档的**实时查询**。例如，下面的代码会导致无穷 循环：

```js
let divs = document.getElementsByTagName("div");
for (let i = 0; i < divs.length; ++i){
 let div = document.createElement("div");
 document.body.appendChild(div);
} 
```

第一行取得了包含文档中所有div元素的 HTMLCollection。因为这个集合是“实时的”，所以 任何时候只要向页面中添加一个新元素，再查询这个集合就会多一项。因为浏览器不希望保存每 次创建的集合，所以就会在每次访问时更新集合。这样就会出现前面使用循环的例子中所演示的问题。 每次循环开始，都会求值 i < divs.length。这意味着要执行获取所有div元素的查询。因为循环 体中会创建并向文档添加一个新元素，所以每次循环 divs.length 的值也会递增。因为两个值 都会递增，所以 i 将永远不会等于 divs.length。

## MutationObserver 接口

MutationObserver 接口，可以在 DOM 被修改时异步执行回调。使 用 MutationObserver 可以观察整个文档、DOM 树的一部分，或某个元素。此外还可以观察元素属性、子节点、文本，或者前三者任意组合的变化。

### 基本用法

MutationObserver 的实例要通过调用 MutationObserver 构造函数并传入一个回调函数来创建：

```js
let observer = new MutationObserver(() => console.log('DOM was mutated!'));
```

#### observe()方法

新创建的 MutationObserver 实例不会关联 DOM 的任何部分。要把这个 observer 与 DOM 关 联起来，需要使用 observe()方法。这个方法接收两个必需的参数：要观察其变化的 DOM 节点，以及 一个 MutationObserverInit 对象。

MutationObserverInit 对象用于控制观察哪些方面的变化，是一个键/值对形式配置选项的字典。 例如，下面的代码会创建一个观察者（observer）并配置它观察元素上的属性变化：

```js
let observer = new MutationObserver(() => console.log('<body> attributes changed'));
observer.observe(document.body, { attributes: true }); 
```

执行以上代码后，元素上任何属性发生变化都会被这个 MutationObserver 实例发现，然 后就会**异步执行注册的回调函数**。元素后代的修改或其他非属性修改都不会触发回调进入任务 队列。

#### 回调与 MutationRecord

每个回调都会收到一个 MutationRecord 实例的数组。MutationRecord 实例包含的信息包括发 生了什么变化，以及 DOM 的哪一部分受到了影响。因为回调执行之前可能同时发生多个满足观察条件 的事件，所以每次执行回调都会传入一个包含按顺序入队的 MutationRecord 实例的数组。 下面展示了反映一个属性变化的 MutationRecord 实例的数组：

```js
let observer = new MutationObserver(
 (mutationRecords) => console.log(mutationRecords));
observer.observe(document.body, { attributes: true });
document.body.setAttribute('foo', 'bar');
// [
// {
// addedNodes: NodeList [],
// attributeName: "foo",
// attributeNamespace: null,
// nextSibling: null,
// oldValue: null,
// previousSibling: null
// removedNodes: NodeList [],
// target: body
// type: "attributes"
// }
// ] 
```

#### disconnect()方法

默认情况下，只要被观察的元素不被垃圾回收，MutationObserver 的回调就会响应 DOM 变化事 件，从而被执行。要提前终止执行回调，可以调用 disconnect()方法。

这个方法是同步的方法,若想观察一次并执行回调函数后再取消观察,则需要使用 setTimeout()让已经入列的回调执行完毕再调用 disconnect()

### MutationObserverInit 与观察范围

MutationObserverInit 对象的subtree表示除了目标节点，是否观察目标节点的子树（后代）.如果是 false，则只观察目标节点的变化；如果是 true，则观察目标节点及其整个子树

### 垃圾回收

MutationObserver 实例与目标节点之间的引用关系是非对称的。**MutationObserver 拥有对要 观察的目标节点的弱引用。因为是弱引用，所以不会妨碍垃圾回收程序回收目标节点。** 然而，目标节点却拥有对 MutationObserver 的强引用。**如果目标节点从 DOM 中被移除，随后被垃圾回收，则关联的 MutationObserver 也会被垃圾回收。**

有时候可能需要保存某个观察者的完整变化记录。保存这些 MutationRecord 实例，也就会保存 它们引用的节点，因而会妨碍这些节点被回收。如果需要尽快地释放内存，建议从每个 MutationRecord 中抽取出最有用的信息，然后保存到一个新对象中，最后抛弃 MutationRecord。

# DOM扩展

描述 DOM 扩展的两个标准：Selectors API 与 HTML5。

## Selectors API 

Selectors API Level 1 的核心是两个方法：querySelector()和 querySelectorAll()。

### querySelector()

querySelector()方法接收 CSS 选择符参数，返回匹配该模式的**第一个后代元素**，如果没有匹配 项则返回 null。下面是一些例子：

```js
// 取得<body>元素
let body = document.querySelector("body");
// 取得 ID 为"myDiv"的元素
let myDiv = document.querySelector("#myDiv");// 取得类名为"selected"的第一个元素
let selected = document.querySelector(".selected");
// 取得类名为"button"的图片
let img = document.body.querySelector("img.button");
```

如果选择符有语法错误或碰到不支持的选择符， 则 querySelector()方法会抛出错误。

### querySelectorAll()

querySelectorAll()方法跟 querySelector()一样，也接收一个用于查询的参数，但它会返回 **所有匹配的节点**，而不止一个。这个方法返回的是一个 NodeList 的静态实例。

**querySelectorAll()返回的 NodeList 实例一个属性和方法都不缺，但它是一 个静态的“快照”，而非“实时”的查询。这样的底层实现避免了使用 NodeList 对象可能造成的性 能问题(前面提到的每个节点的childNodes 属性返回的NodeList是一个实时的查询)。**

```js
// 取得 ID 为"myDiv"的<div>元素中的所有<em>元素
let ems = document.getElementById("myDiv").querySelectorAll("em");
// 取得所有类名中包含"selected"的元素
let selecteds = document.querySelectorAll(".selected");
// 取得所有是<p>元素子元素的<strong>元素
let strongs = document.querySelectorAll("p strong");
返回的 NodeList 对象可以通过 for-of 循环、item()方法或中括号语法取得个别元素。比如：
let strongElements = document.querySelectorAll("p strong");
// 以下 3 个循环的效果一样
for (let strong of strongElements) {
 strong.className = "important";
}
for (let i = 0; i < strongElements.length; ++i) {
 strongElements.item(i).className = "important";
}
for (let i = 0; i < strongElements.length; ++i) {
 strongElements[i].className = "important";
} 
```

### matches()

matches()方法（在规范草案中称为 matchesSelector()）接收一个 CSS 选择符参数，如果元素 匹配则该选择符返回 true，否则返回 false。例如：

```js
if (document.body.matches("body.page1")){
 // true
} 
```

## HTML5

### getElementsByClassName()

getElementsByClassName()方法接收一个参数，即包含一个或多个类名的字符串，返回类名中 包含相应类的元素的 NodeList。如果提供了多个类名，则顺序无关紧要。下面是几个示例：

```js
// 取得所有类名中包含"username"和"current"元素
// 这两个类名的顺序无关紧要
let allCurrentUsernames = document.getElementsByClassName("username current");
// 取得 ID 为"myDiv"的元素子树中所有包含"selected"类的元素
let selected = document.getElementById("myDiv").getElementsByClassName("selected");
```

### classList 属性

在之前要为一个元素删除或添加一个类很复杂,比如

```html
<div class="bd user disabled">...</div> 
```

这个元素有 3 个类名。要想删除其中一个，就得先把 className 拆开，删除不想要的那个， 再把包含剩余类的字符串设置回去。比如：

```js
// 要删除"user"类
let targetClass = "user";
// 把类名拆成数组
let classNames = div.className.split(/\s+/);
// 找到要删除类名的索引
let idx = classNames.indexOf(targetClass);
// 如果有则删除
if (idx > -1) {
 classNames.splice(i,1);
}
// 重新设置类名
div.className = classNames.join(" ");
```

classList 属性为这些操作提供了更简单也更安全的实现方式。

```
 add(value)，向类名列表中添加指定的字符串值 value。如果这个值已经存在，则什么也不做。
 contains(value)，返回布尔值，表示给定的 value 是否存在。
 remove(value)，从类名列表中删除指定的字符串值 value。
 toggle(value)，如果类名列表中已经存在指定的 value，则删除；如果不存在，则添加。
```

```js
// 删除"disabled"类
div.classList.remove("disabled"); 
// 添加"current"类
div.classList.add("current"); 
// 切换"user"类
div.classList.toggle("user"); 
// 检测类名 
if (div.classList.contains("bd") && !div.classList.contains("disabled")){ 
 // 执行操作
) 
// 迭代类名
for (let class of div.classList){ 
 doStuff(class); 
} 
```

### 焦点管理

HTML5 增加了辅助 DOM 焦点管理的功能。首先是 document.activeElement，始终包含当前拥 有焦点的 DOM 元素。页面加载时，可以通过用户输入（按 Tab 键或代码中使用 focus()方法）让某个 元素自动获得焦点。例如：

```js
let button = document.getElementById("myButton");
button.focus();
console.log(document.activeElement === button); // true
```

默认情况下，document.activeElement 在页面刚加载完之后会设置为 document.body。而在 页面完全加载之前，document.activeElement 的值为 null。

其次是 document.hasFocus()方法，该方法返回布尔值，表示文档是否拥有焦点：

```js
let button = document.getElementById("myButton");
button.focus();
console.log(document.hasFocus()); // true
```

### document新属性

- readyState 属性

document.readyState 属性有两个可能的值：  loading，表示文档正在加载；  complete，表示文档加载完成。

在这 个属性得到广泛支持以前，通常要依赖 onload 事件处理程序设置一个标记，表示文档加载完了。这个 属性的基本用法如下

```js
if (document.readyState == "complete"){ 
 // 执行操作
}
```

### 自定义数据属性

HTML5 允许给元素指定非标准的属性，但要使用前缀 data-以便告诉浏览器，这些属性既不包含 与渲染有关的信息，也不包含元素的语义信息。除了前缀，自定义属性对命名是没有限制的，data-后 面跟什么都可以。下面是一个例子：

```html
<div id="myDiv" data-appId="12345" data-myname="Nicholas"></div> 
```

```js
// 本例中使用的方法仅用于示范
let div = document.getElementById("myDiv");
// 取得自定义数据属性的值
let appId = div.dataset.appId;
let myName = div.dataset.myname;
// 设置自定义数据属性的值
div.dataset.appId = 23456;
div.dataset.myname = "Michael";
// 有"myname"吗？
if (div.dataset.myname){
 console.log(`Hello, ${div.dataset.myname}`);
} 
```

### 插入标记

#### innerHTML 属性

赋给 innerHTML 属性的值会被解析为 DOM 子树，并替代元素之前的所有节点。

#### outerHTML 属性

读取 outerHTML 属性时，会返回调用它的元素（及所有后代元素）的 HTML 字符串。在写入 outerHTML 属性时，调用它的元素会被传入的 HTML 字符串经解释之后生成的 DOM 子树取代。比如:

```html
<div id="content">
 <p>This is a <strong>paragraph</strong> with a list following it.</p>
 <ul>
 <li>Item 1</li>
 <li>Item 2</li>
 <li>Item 3</li>
 </ul>
</div>
```

如果使用 outerHTML 设置 HTML，比如：

```js
div.outerHTML = "<p>This is a paragraph.</p>";
```

则会得到与执行以下脚本相同的结果：

```js
let p = document.createElement("p");
p.appendChild(document.createTextNode("This is a paragraph."));
div.parentNode.replaceChild(p, div); 
```

新的p元素会取代 DOM 树中原来的div元素。

#### insertAdjacentHTML()与 insertAdjacentText()

它们都接收两个参数：要插入标记的位置和要插入的 HTML 或文本。第一个参数 必须是下列值中的一个：

```
 "beforebegin"，插入当前元素前面，作为前一个同胞节点；
 "afterbegin"，插入当前元素内部，作为新的子节点或放在第一个子节点前面；
 "beforeend"，插入当前元素内部，作为新的子节点或放在最后一个子节点后面；
 "afterend"，插入当前元素后面，作为下一个同胞节点。
```

```js
// 作为前一个同胞节点插入
element.insertAdjacentHTML("beforebegin", "<p>Hello world!</p>");
element.insertAdjacentText("beforebegin", "Hello world!");
// 作为第一个子节点插入
element.insertAdjacentHTML("afterbegin", "<p>Hello world!</p>");
element.insertAdjacentText("afterbegin", "Hello world!");
// 作为最后一个子节点插入
element.insertAdjacentHTML("beforeend", "<p>Hello world!</p>");
element.insertAdjacentText("beforeend", "Hello world!");
// 作为下一个同胞节点插入
element.insertAdjacentHTML("afterend", "<p>Hello world!</p>"); element.
insertAdjacentText("afterend", "Hello world!"); 
```

#### 内存与性能问题

使用本节介绍的方法替换子节点可能在浏览器（特别是 IE）中导致内存问题。比如，如果被移除的 子树元素中之前有关联的事件处理程序或其他 JavaScript 对象（作为元素的属性），那它们之间的绑定关 系会滞留在内存中。如果这种替换操作频繁发生，页面的内存占用就会持续攀升。在使用 innerHTML、 outerHTML 和 insertAdjacentHTML()之前，最好手动删除要被替换的元素上关联的事件处理程序和 JavaScript 对象。

### scrollIntoView()

scrollIntoView()方法存在于所有 HTML 元素上，可以滚动浏览器窗口或容器元素以便包含元 素进入视口。这个方法的参数如下：

-  alignToTop 是一个布尔值。 

  ​	 true：窗口滚动后元素的顶部与视口顶部对齐。 

  ​	 false：窗口滚动后元素的底部与视口底部对齐。 

- scrollIntoViewOptions 是一个选项对象。 

  ​	 behavior：定义过渡动画，可取的值为"smooth"和"auto"，默认为"auto"。 

  ​	 block：定义垂直方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默 认为 "start"。 

  ​	 inline：定义水平方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默 认为 "nearest"。 

-  不传参数等同于 alignToTop 为 true。

```js
// 确保元素可见
document.forms[0].scrollIntoView(); 
// 同上
document.forms[0].scrollIntoView(true); 
document.forms[0].scrollIntoView({block: 'start'}); 
// 尝试将元素平滑地滚入视口
document.forms[0].scrollIntoView({behavior: 'smooth', block: 'start'}); 
```

# DOM2和DOM3





# 异步函数

## async

async 关键字用于声明异步函数。这个关键字可以用在函数声明、函数表达式、箭头函数和方法上：

```js
async function foo() {}
let bar = async function() {};
let baz = async () => {};
class Qux {
 async qux() {}
} 
```

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

异步函数如果使用 return 关键字返回了值（如果没有 return 则会返回 undefined），这 个值会被 Promise.resolve()包装成一个期约对象。异步函数始终返回期约对象。在函数外部调用这个函数可以得到它返回的期约:

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
当然，直接返回一个期约对象也是一样的：
async function foo() {
 console.log(1);
 return Promise.resolve(3);
}
// 给返回的期约添加一个解决处理程序
foo().then(console.log);
console.log(2);
// 1
// 2
// 3 
```

## await

因为异步函数主要针对不会马上完成的任务，所以自然需要一种暂停和恢复执行的能力。使用 await 关键字可以暂停异步函数代码的执行，等待期约解决。

# Promise

```js
try {
 throw new Error('foo');
} catch(e) {
 console.log(e); // Error: foo
}
try {
 Promise.reject(new Error('bar'));
} catch(e) {
 console.log(e);
}
// Uncaught (in promise) Error: bar
```

。从这 里就可以看出期约真正的异步特性：它们是同步对象（在同步执行模式中使用），但也是异步执行模式 的媒介。

在前面的例子中，拒绝期约的错误并没有抛到执行同步代码的线程里，而是通过浏览器异步消息队 列来处理的。因此，try/catch 块并不能捕获该错误。代码一旦开始以异步模式执行，则唯一与之交互 的方式就是使用异步结构——更具体地说，就是期约的方法。

# JSON

## 解析JSON

### JSON对象

JSON 对象有两个方法：**stringify()和 parse()**。在简单的情况下，这两个方法分别可以将 JavaScript 序列化为 JSON 字符串，以及将 JSON 解析为原生 JavaScript 值。例如：

```js
let book = {
 title: "Professional JavaScript",
 authors: [
 "Nicholas C. Zakas",
 "Matt Frisbie"
 ],
 edition: 4,
 year: 2017
};
//使用 JSON.stringify()把一个 JavaScript 对象序列化为一个 JSON 字符串
//保存在变量jsonText 中
let jsonText = JSON.stringify(book);

//使用以下代码创建与 book 对象类似的新对象：
//得到js值
let bookCopy = JSON.parse(jsonText);
```

### 序列化选项(参数)

JSON.stringify()方法除了要序列化的对象，还可以接收两个参数。这两个参数可以用 于指定其他序列化 JavaScript 对象的方式。**第一个参数是过滤器，可以是数组或函数；第二个参数是用 于缩进结果 JSON 字符串的选项。**单独或组合使用这些参数可以更好地控制 JSON 序列化。

如果第二个参数是一个数组，那么 JSON.stringify()返回的结果只会包含该数组中列出的对象 属性。比如下面的例子：

```js
let book = {
 title: "Professional JavaScript",
 authors: [
 "Nicholas C. Zakas",
 "Matt Frisbie"
 ],
 edition: 4,
 year: 2017
};
let jsonText = JSON.stringify(book, ["title", "edition"]);
```

第二个参数是一个包含两个字符串的数组："title" 和"edition"。它们对应着要序列化的对象中的属性，因此结果 JSON 字符串中只会包含这两个属性：

```json
{"title":"Professional JavaScript","edition":4} 
```

提供的函数接收两个参数：属性名（key）和属性 值（value）。可以根据这个 key 决定要对相应属性执行什么操作。这个 key 始终是字符串，只是在值 不属于某个键/值对时会是空字符串。

```js
let book = {
 title: "Professional JavaScript",
 authors: [
 "Nicholas C. Zakas",
 "Matt Frisbie"
 ],
 edition: 4,
  year: 2017
};
let jsonText = JSON.stringify(book, (key, value) => {
 switch(key) {
 case "authors":
 return value.join(",")
 case "year":
 return 5000;
 case "edition":
 return undefined;
 default:
 return value;
 }
});
```

### toJSON()方法

对象需要在 JSON.stringify()之上自定义 JSON 序列化。此时，可以在要序列化的对象 中添加 toJSON()方法，序列化时会基于这个方法返回适当的 JSON 表示。

```js
let book = {
 title: "Professional JavaScript",
 authors: [
 "Nicholas C. Zakas",
 "Matt Frisbie"
 ],
 edition: 4,
 year: 2017,
 toJSON: function() {
 return this.title;
 }
};
let jsonText = JSON.stringify(book);
```

toJSON()方法可以与过滤函数一起使用，因此理解不同序列化流程的顺序非常重要。在把对象传 给 JSON.stringify()时会执行如下步骤。

```
(1) 如果可以获取实际的值，则调用 toJSON()方法获取实际的值，否则使用默认的序列化。
(2) 如果提供了第二个参数，则应用过滤。传入过滤函数的值就是第(1)步返回的值。
(3) 第(2)步返回的每个值都会相应地进行序列化。
(4) 如果提供了第三个参数，则相应地进行缩进。
理解这个顺序有助于决定是创建 toJSON()方法，还是使用过滤函数，抑或是两者都用。
```

