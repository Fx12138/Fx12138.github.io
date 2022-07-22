---
title: jsDOM编程艺术
date: 2022-01-04 17:06:17
tags: js
---

# 获取元素

- getElementById
- getElementsByTagName
- getElementsByClassName

## 获取其他元素

- element.childNodes属性 所有的子元素,是一个数组
- parentNode属性 获取父元素
- lastChild属性 获取当前元素的最后一个子元素
- nextSibling属性 获取当前元素的后一个兄弟元素

# 获取和设置属性

object.getAttribute()

object.setAttribute()

# 文本节点

## nodeValue属性

用来获取和修改文本节点的文本,但是获取文本时包含在<P元素里的文本是另一种节点，它是平元素的第一个子节点。因此，你想要得到的其实是它的第一个子节点的nodeValue属性值。

node.childNodes[0].nodeValue

# 创建节点

## 创建元素节点

appendChild

```js
var newDiv = document.createElement('p')
node.appendChild(newDiv)
```

## 创建文本节点

document.createTextNode

上面创建了元素节点p标签,但是并不包含文本,需要创建文本节点并插入到p标签中

```js
var newP = document.createElement('p')
node.appendChild(newDiv)
var text = document.createTextNode('这是文本')
newP.appendChild(text)
```

# 插入节点

## 在已有元素前插入

父节点.insertBefore(新元素,目标元素)

# 元素style

node.style可以获取元素的css样式,但是**只能获取到内嵌样式**

通过node.style.color可以获取字体颜色,但是font-family不能通过这种方式直接获取,因为中间的连字符 - 和js中的减号相同,

node.style.font-family会报错.应该使用下面的方法,使用驼峰

```js
node.style.fontFamily
```



