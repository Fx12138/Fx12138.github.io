---
title: css
date: 2020-11-17 15:37:51
tags: 前端
---

## 选择器

### 子元素选择器

只能选择作为某元素的**最近的一级子元素**.

```css
div>a{
	...
}
```

只对div中的**直接后代的所有a标签**起作用

### 兄弟元素选择器

```css
h1+p{

}
```

**要求h1和p必须有相同的父元素**

### 伪类选择器

#### :focus伪类选择器

用于选取获得焦点的表单元素

#### 伪类

```css
/* :first-child第一个元素 :last-child最后一个元素 */
ul>li:first-child{
    color:red;
}
/* :nth-child(1)选中第一个子元素  */
/* :nth-child(n)全部选中,范围为0到正无穷  */
/* :nth-child(2n) 或 :nth-child(even)选中偶数子元素  */
/* :nth-child(2n+1) 或 :nth-child(odd)选中奇数子元素  */
ul>li:nth-child(1){
    color:red;
}

/* :first-of-type :last-child nth-of-type	和上面的类似 */
/* 不同的是这种只会选择ul中li中的第几个 */
/* 而上面的是针对ul中所有的元素,如果第一个元素不是li的话则无效 */
ul>li:nth-child(1){
    color:red;
}

/* :not() 否定伪类,就是排除 */
/* 下面的表示除了第三个li都有效 */
ul>li:not(:nth-child(3)){
    color:red;
}
```

### 伪元素

表示页面中一些特殊的并不真实存在的元素(特殊的位置)

- 字体

  ::first-letter	表示第一个字母

  ::first-line	表示第一行

  ::selection	选中的效果

- div

  ::before 元素的开始

  ::after	元素的末尾

  必须结合content来使用,content可以为空

### 选择器的优先级

优先级的规则

!important 无穷大

​	内联样式 style=""，优先级1000
​	id选择器，优先级100
​	类和伪类，优先级10 
​	元素选择器和伪元素，优先级1
​	通配*，优先级0
​	继承的样式，没有优先级

## 垂直外边距的重叠

在网页中**相邻的垂直方向**的外边距会发生外边距的重叠

### 兄弟外边距折叠

所谓的兄弟外边距重叠指垂直方向上兄弟元素之间的相邻外边距会取最大值而不是取和

```html
<body>
		<div id="up">1</div>
		<div id="bottom">2</div>
		<style>
			#up,#bottom{
				height: 200px;
				width: 200px;
			}
			#up{
				background-color: #00FFFF;
				/* 设置margin-bottom和margin-right会移动相邻的元素,自身不移动 */
				margin-bottom: 100px;
			}
			#bottom{
				background-color: #AAAAAA;
				/* 设置margin-top和margin-left会移动自身元素,相邻元素不移动 */
				margin-top: 100px;
			}
		</style>
	</body>
```

上面的代码对上面的div设置了margin-bottom,对下面的元素设置了margin-top.相邻的垂直方向的外边距会发生外边距的重叠,外边距重叠指兄弟元素之间的相邻外边距会取最大值,所以两个div的距离仍然是100,而不是200如下

<img src="D:\devApp\myblog\source\_posts\css\image-20210602134028697.png" alt="image-20210602134028697" style="zoom:25%;" />

如果两个外边距全是正数,折叠后的边距取较大的一个

如果两个外边距一正一负,则取两个数的和

如果两个外边距全是负数,则取两个数绝对值大的那个

### 父子外边距折叠

如果父子元素的垂直外边距相邻了，则子元素的外边距会设置给父元素(上外边距)

```html
<div class="out">
			<div class="inner"></div>
		</div>
		<style>
			.out{
				height: 200px;
				width: 200px;
				background-color: #00FFFF;
			}
			.inner{
				height: 100px;
				width: 100px;
				background-color: #AAAAAA;
				margin-top: 100px;
			}
		</style>
```

上面的代码只给里面的子元素加了上外边距 但是效果如下,外面的父元素也随之移动

<img src="D:\devApp\myblog\source\_posts\css\image-20210602140713028.png" alt="image-20210602140713028" style="zoom:25%;" />

### 空元素外边距折叠

```xml
<style>
    .slide3 {
    margin: 10px 0;
  }
</style>

<body>
    <h3>第3种外边距折叠：空的块级元素</h3>
    <div class="slide3"></div>
    <div style="background: #063181;color: #fff;">这是用来检测slide3已经折叠的辅助元素</div>
</body>
```

这种情况下slide3应该是20px,但是实际只有10px

### 总结

发生外边距折叠的共同原因：**margin之间直接接触，没有阻隔**。

- 第一个例子中，两个`<p>`标签的垂直方向`margin`是直接接触的；
- 第二个例子中，由于父元素的`padding`,`border`都为0，所以`margin`和它的子元素也是直接接触的。（这个例子画出盒模型就很好理解）
- 第三个例子中，空元素本身的上下边距也是直接接触的（`padding`,`border`也是0）

解决办法:

(1**)对父元素使用内边距**

```html
<div class="out">
			<div class="inner"></div>
		</div>
		<style>
			.out{
				height: 100px;	/*将父元素高度减去多余的高度*/
				width: 200px;
				background-color: #00FFFF;
				padding-top: 100px; /*对父元素使用内边距*/
			}
			.inner{
				height: 100px;
				width: 100px;
				background-color: #AAAAAA;
				
			}
		</style>
```

<img src="D:\devApp\myblog\source\_posts\css\image-20210602141639336.png" alt="image-20210602141639336" style="zoom:25%;" />

(2)**给外边距加boder**

```html
<div class="out">
			<div class="inner"></div>
		</div>
		<style>
			.out{
				height: 199px;	/*将父元素的高度减去boder的宽度*/
				width: 200px;
				background-color: #00FFFF;
				border-top: 1px #00FFFF solid;
			}
			.inner{
				height: 100px;
				width: 100px;
				background-color: #AAAAAA;
				margin-top: 99px;	/*子元素外边距减去boder的宽度*/
			}
		</style>
	</body>
```

(3)使用伪元素

```html
<div class="out">
			<div class="inner"></div>
		</div>
<style>
		.out{
			height: 200px;
			width: 200px;
			background-color: #00FFFF;
		}
    	.inner::before{	/*伪元素*/
        	content:'';
            display:table;
    	}
		.inner{
			height: 100px;
			width: 100px;
			background-color: #AAAAAA;
		}
	</style>
```
(4)为父元素添加overflow:hidden

## 内联元素和块元素盒子模型

* 内联元素不能设置width和height

* 内联元素可以设置水平方向的内边距；也可以设置**垂直方向的内边距**，但是**不会影响页面的布局**，即若内联元素下方有其他元素，上方的内联元素内边距过大时会覆盖下方的元素。而块元素则会将下面的元素往下面挤。
* 内联元素可以设置边框，但是**垂直的边框不会影响页面的布局**。
* 内联元素可以设置外边距；**垂直方向的外边距不会影响页面的布局**。水平方向上会影响并且不会产生外边距重叠

## 浮动

设置了浮动后,元素水平布局的等式不再适用

浮动的特点:

​	(1)元素设置了float后会脱离文档流,所以元素下面的还在文档流中的元素会自动向上移动

​	(2)浮动元素会向父元素的左侧或右侧移动,默认不会从**父元素内容区中(即不会超过内边框)**移出

​	(3)浮动元素不会超过它前面的其他浮动元素,也不会覆盖其它浮动元素

​	(4)如果浮动元素的上边是一个没有浮动的块元素,则浮动元素无法上移

​	(5)当不设置浮动时,一个div会自动设置margin-right的值来使其内容区的宽度等于父元素的宽度.设置了浮动后,元素水平布局的等式不再适用

​	(6)**浮动元素会生成一个块级框,因此如果让一个链接浮动,即使该链接元素本身是行内元素,就会生成一个块级框,会像块级元素一样摆放和表现.(也可以定义高度和宽度)**

浮动的其他特点

​	(1)浮动元素不会覆盖文字,文字会自动环绕在浮动元素周围(可以利用这个来实现文字环绕图片的效果)

脱离文档流的特点:

​	块元素

​	(1)块元素不再独占一行

​	(2)脱离文档流后,块元素的宽度和高度默认被内容撑开

​	行内元素

​	行内元素脱离文档流后会变成块元素,特点和块元素一样

## 高度塌陷和BFC

高度塌陷:想要使父元素的高度随着子元素的高度改变,即被子元素撑开,从而不设置父元素的具体高度.在这种情况下,子元素使用浮动属性,子元素会脱离文档流,导致无法撑起父元素的高度从而导致高度塌陷.父元素高度丢失之后会导致下面的元素下移,影响页面布局

### BFC

block formatting context 格式化上下文

BFC是css中的一个隐含的属性,可以为一个元素开启BFC,该元素会变成一个**独立的布局区域**

元素开启BFC后的特点:

- 开启BFC后的元素不会被浮动元素覆盖
- 开启BFC的父元素中,子元素和父元素外边距不会重叠
- 可以包含浮动的子元素
- bfc就是页面上的一个独立容器，容器里面的子元素不会影响外面元素。因此,当两个兄弟元素发生外边距重叠时,可以对某一个元素外面包裹一层div并开启bfc

```html
    <div class="container">
        <div class="wrapper">
            <div class="box1"></div>
        </div>
        <div class="box2"></div>
    </div>
```

```css
 .container {
        overflow: hidden;
        width: 100px;
        height: 100px;
        background-color: red;
    }
    
    .wrapper {
        overflow: hidden;
    }
    
    .box1 {
        height: 20px;
        margin: 10px 0;
        background-color: green;
    }
    
    .box2 {
        height: 20px;
        margin: 20px 0;
        background-color: green;
    }
```

这样wrapper开启BFC并包裹box1,就可以避免box1和box2发生外边距重叠

开启BFC的方法:

- 设置父元素为浮动(不推荐)

- 设置元素绝对定位

- 将父元素设置为行内块元素(不推荐)这种方法会使行宽不再自动扩大,而是子元素的宽度撑开的大小

- 将父元素的overflow设置为一个非visible的值

  这是常用的方式,父元素overflow:hidden开启

### clear

如果我们不希望某个元素因为其他元素浮动的影响而改变位置,可以通过clear属性

可选值:

- left 清楚左侧浮动元素对当前元素的影响
- right 清除右侧浮动元素对当前元素的影响
- both 清楚两侧中最大影响的那侧

原理:设置清除浮动以后,浏览器会自动为元素添加一个**上外边距**,使其位置不受影响.

### 解决高度塌陷最终方案

可以在父元素的最下面加一个空的div 标签,设置clear属性,从而撑起父元素,这种方法相当于用html解决css的问题,下面使用伪元素的方式以纯css方式解决.

**使用伪元素选择器**,对父元素使用伪元素选择器相当于在父元素的最后添加一个元素设置其样式.这个伪元素默认为行内元素.下面为示例,其中box1为父元素

```css
.box1::after{
	display:block;	/*将伪元素转换为块元素*/
	clear:both;	/*设置clear属性使其不受浮动元素影响,从而撑起父元素*/
}
```

一个既解决高度塌陷,又解决外边距重叠的方法,其中设置父元素的类为clearfix

```css
.clearfix::before,
.clearfix::after{
	content:'';
	display:table;
	clear:both;
}
```

## position

position可选值:

- static 默认值,元素是静止的,没有开启定位
- relative 相对定位
- absolute 绝对定位
- fixed 固定定位
- sticky 粘滞定位

对于开启的定位的元素,可以设置z-index设置元素的层级.如果都不设置z-index的话,则靠下的元素层级更高.

祖先元素的层级再高也不会覆盖后代元素.

### relative

开启后设置偏移量只会影响自己的位置,不影响其他元素

特点:

- 开启后如果不设置偏移量,**页面内得所有元素不会发生任何变化**
- 相对定位参照与元素在文档流中得位置进行定位得,即**相对于自己本身位置得左上角**
- 相对定位会提升元素得层级,高于页面中其他元素.(即如果和其他元素重叠,会覆盖其他元素)
- 相对定位不会脱离文档流,不会改变元素的性质,即块级元素还是块级元素,行内还是行内

### absolute

特点

- 开启绝对定位后,如果不设置偏移量,**自身元素得位置不会发生变化**

- **元素会从文档流中脱离**

- 元素开启绝对定位后会生成一个块级框,不论原来它在正常流中生成何种类型的框

- 会使元素提升一个层级

- **开启了绝对定位后整个div盒子的宽度要加上left和right**

  - 水平方向上,当发生过度约束时,如果9个值中没有auto,则会自动调整right,如果有auto的话,会自动调整auto的值

    可以设置auto的值:margin,width,left,right

  - 垂直方向上,等式也必须要满足,这样也**可以用上下外边距都设置为auto,top和bottom都设置为0的方式来使元素垂直居中.但是在非绝对定位时不可以.**

- 绝对定位参照包含块进行定位,即**相对于离他最近的开启了定位的祖先元素**,如果所有的祖先元素都没有开启定位,则相对于根元素

  包含块:

   - 正常情况下

     包含块就是当前元素**最近的祖先块元素**

  - 绝对定位包含块

    包含块是离他最近的开启了定位的祖先元素

### fixed

固定定位也是一种绝对定位,大部分特点都和绝对定位一样

唯一不同的是固定定位永远参考与浏览器的视口进行定位(视口只有浏览器窗口大小,例如网页侧边的广告)

### sticky

和relative相对定位和相似,不同的是粘滞定位可以在元素到达某个位置时将其固定.设置该属性的元素并不脱离文档流，仍然保留元素原本在文档流中的位置。

该元素并不脱离文档流，仍然保留元素原本在文档流中的位置。
 当元素在容器中被滚动超过指定的偏移值时，元素在容器内固定在指定位置。亦即如果你设置了top: 50px，那么在sticky元素到达距离相对定位的元素顶部50px的位置时固定，不再向上移动。
 元素固定的相对偏移是相对于离它最近的具有滚动框的祖先元素，如果祖先元素都不可以滚动，那么是相对于viewport来计算元素的偏移量

## 弹性盒flex

当父元素设置flex布局之后,子元素的float clear 和 vertical-align属性将失效

### 弹性容器(6个)

​	display:flex;

- flex-direction 指定容器中弹性元素的排列方式,可选值:
  - row 默认值,弹性元素在容器中水平排列(左向又),主轴自左向右
  - row-reverse 水平排列,从右向左
  - column 纵向排列(自上向下)
  - column-reverse 纵向自下向上

- flex-wrap 设置弹性元素在容器横向装不下的时候是否自动换行
  - nowrap 默认值 元素不会自动换行,如果设置了不自动换行的话,那么如果盒子容器一行装不下里面的item,就会自动相同程度的缩小里面的每个item使他们在一行上显示.
  - wrap 元素沿着辅轴方向自动换行
  - wrap-reverse 元素沿着辅轴反方向换行

- flex-flow是flex-direction和flex-wrap的缩写
- justify-content **主轴**上的元素如何排列,主轴空白空间的分布
  - flex-start 元素沿着主轴的起边排列,即从左开始
  - flex-end 元素沿着主轴终边排列
  - center 元素居中排列
  - space-around 容器中的空白分布到元素两侧(这样会使中间的元素空隙大一些,因为中间的元素空隙为左边元素分配到的加上右边元素分配到的)
  - space-between 第一个元素靠起点，最后一个元素靠终点，余下元素平均分配空间.两边不再有空白
  - space-evenly 空白分布到元素单侧

- align-items 元素在辅轴上如何对齐(只适用于单行,在多行下没效果)
  - stretch 默认值将元素的长度设置成相同的值,使其填满整个容器(每一行的高度相同,第一行和第二行不一定相同.需要把里面的item的高度值去掉才能生效)
  - flex-start 元素不会拉伸,沿着辅轴的垂直方向起边对齐
  - flex-end元素不会拉伸,沿着辅轴的垂直方向终边对齐
  - center

- align-content 辅轴空白空间的分布(属性和justify-content相同,适用于多行,,在单行下没效果)
  - center 所有元素都在中间,上下空白空间相等

### 弹性元素

- flex-grow 当父元素有多余空间时,子元素如何伸展,父元素的剩余空间会按照比例进行分配,默认是0

​	例如父元素宽度为 300,两个子元素宽度分别为100,此时父元素剩余100的宽度,为子元素分别设置flex-grow:1;和flex-grow:2;则父元素剩余的100宽度会按1:2的比例分配给两个子元素,使两个子元素盛满整个父元素

- flex-shrink 当父元素的空间不足以容纳所有的子元素时,如何对子元素进行收缩

- flex-basis 设置元素在主轴上的长度,优先级大于width,低于max-width

- flex 分配**剩余空间**,用flex表示占多少份数  前三个属性的缩写,顺序:增长 缩减 基础 1 1 auto

- align-self 用来覆盖当前弹性元素上的align-items,即不再受容器中的align-items属性约束,使用自己的设置的在辅轴上的对齐方式

- order 指定弹性元素的排列顺序,传入整数数值,数值越小越靠前(默认是0)

# css权威指南

## 结构和层叠

### 继承

![image-20210802111245645](image-20210802111245645.png)

将声明color:gray;应用到ul元素时,这个元素会采用该声明,这个值再沿着树向下传播到后代元素,并一直继续,直到再没有更多后代元素继承这个值为止.

有些属性不能继承,如border,一般的大多数框模型属性(外边距,内边距,背景和边框)都不能继承.

继承值完全没有特殊性.看下面的例子:

```html
<h1 id='page-title'>Meerkat<em>Central</em></h1>
<p>welcome to the best place......</p>
<style>
*{
	color:gray;
}
h1#page-title{
	color:black;
}
</style>
```

结果如图

![image-20210802112426284](image-20210802112426284.png)

因为统配选择器适用于所有元素,而且有0特殊性,其颜色声明指定的值gray要优先于继承值(black),因为继承值根本没有特殊性,因此em元素显示为灰色而不是黑色.

### 层叠

如果特殊性相等的两个规则同时应用到一个元素会怎样?

#### 按权重和来源排序

层叠规则:

![层叠规则](image-20210802112938917.png)

总结一下,再声明权重方面要考虑5级,权重由大到小顺序依次为:

1.读者的重要声明

2.创作人员的重要声明

3.创作人员的正常声明

4.读者的正常声明

5.用户代理声明

#### 按特殊性排序

如果权重相同,则按特殊性排序.

#### 按顺序排序

如果两个规则的权重,来源和特殊性完全相同,那么再样式表中后出现的一个会胜出.

正是由于按这种顺序排序,才有了通常推荐的链接样式顺序,一般建议**按link-visited-hover-active(LVHA)的顺序声明链接样式**.因为这些选择器的特殊性都是一样的,都是0,0,1,0.因此与元素匹配的最后一个选择器才会胜出.正在"点击"的未访问链接可以与其中的3个规则匹配:link,hover,active.所以在这三个规则当中最后声明的一个将胜出.

## 水平auto

要求七个值之和等于包含块的width.

内边距,边框和内容宽度绝对不能为负值,只有外边距能小于零.

只有margin-left,width,margin-right三个属性可以设置为auto.

### 只有一个auto

当这三个属性中只有一个属性为auto时,将会由这个auto的属性来填补所需的距离.而当三个都设置具体的值时,但是此时仍然出现过分约束的情况,则会调整右外边距的值.

### 有两个auto

两个外边距为auto而width设置为具体值时,两个外边距的值相等,此时元素水平居中.

将某个外边距以及width设置为auto时,设置为auto的外边距会变为0,而width会变为所需的值.

### 三个auto

三个都是auto的情况下,两个外边距会变为0,width尽可能的宽.

## 垂直auto

如果一个块元素的margin-top或margin-bottom设置为auto,会自动计算为0.

## 颜色

默认下,元素的前景色(如color:red)会应用到边框.比如设置了边框的样式为solid,但是没有设置颜色,这个时候边框的默认宽度为2px,颜色为元素的前景色.

背景色background-color不能继承,其默认值是transparent.文档中有些元素没有自己的背景.那么透过所有的这些元素都能看到父元素的背景.这些元素并没有继承背景,只是能透过他们看到背景而已.

### 背景图像

```css
body{
	background-image:url();
}
```

背景图像也不能继承.原因:如果能继承,而且向body应用了一个背景图像,这个图象将用于文档所有元素的背景,而且每个元素都完成自己的平铺.

可以设置平铺的方向如下:

```css
body{
	background-image:url();
	background-repeat: repeat-x; /*可选repeat,repeat-y,no-repeat*/
}
```

#### 背景定位

```css
body{
	background-image: url();
	background-repeat: no-repeat; /*可选repeat,repeat-y,no-repeat*/
	background-position: center; 
    /*使用值的方式时,第一个数为水平方向.第二个为垂直方向*/
}
```

使用数值

这将解释为从元素内边距区左上角的偏移,偏移点是原图像的左上角.

也可以使用百分数值

将原图像在一个元素中居中时,图像中描述为50% 50%的点(中心点)与元素中描述为50% 50%的点(中心点)对齐.如果图像位于0% 0%,其左上角将放在元素内边距区的左上角,如果图像位置是100% 100%,会使原图像的右下角放在内边距区的右下角.

#### 背景固定

```css
body{
	background-image: url();
	background-repeat: no-repeat; /*可选repeat,repeat-y,no-repeat*/
	background-position: center; 
    /*使用值的方式时,第一个数为水平方向.第二个为垂直方向*/
    background-attachment: fixed; /*可选scroll*/
}
```

fixed,原图像不会随文档滚动,其次原图像的放置由可视区的大小确定,而不是由包含该图像的元素大小决定.

# 查漏补缺

## display:none、visibility:hidden 和 opacity:0 之间的区别？

<img src="D:\devApp\myblog\source\_posts\css\image-20220607155418699.png" alt="image-20220607155418699" style="zoom: 50%;" />

# 本文溢出

## 单行文本

```css
overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
```

## 多行文本

```css
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;	//最多三行
overflow: hidden;
```

# 使用百分比时相对于谁

## padding和margin

无论设置元素的横向或者纵向的padding和margin,当使用百分比时,都是相对于`父元素的width值`。

实例:实现一个div 垂直水平居中，并完成 div 高度永远是宽度的一半

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }

      html,
      body {
        width: 100%;
        height: 100%;
      }

      .outer {
        width: 400px;
        height: 100%;
        background: blue;
        margin: 0 auto;

        display: flex;
        align-items: center;
      }

      .inner {
        position: relative;
        width: 100%;
        height: 0;
        padding-bottom: 50%;
        background: red;
      }

      .box {
        position: absolute;
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    </style>
  </head>
  <body>
    <div class="outer">
      <div class="inner">
        <div class="box">hello</div>
      </div>
    </div>
  </body>
</html>

```

在inner中设置了高度为0,即内容区的高度为0,而设置了padding-bottom: 50%;则元素被padding撑起,高度始终为父元素的width的一半

## 父相子绝的子的宽高

相对于父元素的(content + padding)值, 注意不含border

延伸：如果子元素不是绝对定位，那宽高设为百分比是相对于父元素的宽高，标准盒模型下是content, IE盒模型是content+padding+border。

# 'display'、'position'和'float'的相互关系？

```
（1）首先我们判断display属性是否为none，如果为none，则position和float属性的值不影响元素最后的表现。
（2）然后判断position的值是否为absolute或者fixed，如果是，则float属性失效，并且display的值应该被设置为table或者block，具体转换需要看初始转换值。
（3）如果position的值不为absolute或者fixed，则判断float属性的值是否为none，如果不是，则display的值则按上面的规则转换。注意，如果position的值为relative并且float属性的值存在，则relative相对
于浮动后的最终位置定位。
（4）如果float的值为none，则判断元素是否为根元素，如果是根元素则display属性按照上面的规则转换，如果不是，则保持指定的display属性值不变。
总的来说，可以把它看作是一个类似优先级的机制，"position:absolute"和"position:fixed"优先级最高，有它存在的时候，浮动不起作用，'display'的值也需要调整；其次，元素的'float'特性的值不是"none"的时候或者它是根元素的时候，调整'display'的值；最后，非根元素，并且非浮动元素，并且非绝对定位的元素，'display'特性值同设置值。
```

# 水平垂直居中

## 定宽高

### absolute+负margin

### absolute+margin:auto

### absolute + calc

这种方式和第一种原理相同

```css
/* 定位代码 */
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
```

## 不定宽高

### absolute + transform

### flex

# 两栏布局

下面的布局都相对于这个body

```html
  <body>
    <div class="aside"></div>
    <div class="main"></div>
  </body>
```

(1)左float:left;右边margin-left:310px;

(2) 左float:left;右BFC

```css
div {
  height: 500px;
}

.aside {
  width: 300px;
  float: left;
  background: yellow;
}

.main {
  overflow: hidden;
  background: aqua;
}
```

(3) 父flex布局,左宽度固定,右flex:1

(4) 左absolute,右margin-left

# 三栏布局

(1) 父relative,左absolute且left0,右absolute且right0,中间margin-left和margin-right

(2)左右float,中间margin-left和margin-right

(3) 圣杯布局

dom结构

```html
<div id="header"></div>
<div id="container">
  <div id="center" class="column"></div>
  <div id="left" class="column"></div>
  <div id="right" class="column"></div>
</div>
<div id="footer"></div>
```

首先header和footer的width都设置为100%,并且footer设置clear:both

container设置一个高度,无需设置宽度,overflow:hidden开启BFC.添加padding:0 200px为左右两栏预留空间

left向左浮动,margin-left:-100%,前面说过margin相对于父元素的宽度,此时的left在container的内容区的最左边,紧邻container的左padding,此时设置relative,left:-200px从而将left移到左边的预留padding部分

right向左浮动,margin-right:-200px,这样right就放到了右padding的预留区

这时候还需要给页面设置一个最小宽度,左边栏+右边栏+左边栏relative的left宽度,即200+200+200,min-width:600px;

整体代码如下

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>圣杯布局</title>
  <style>
    body {
      min-width: 600px;
    }

    h4 {
      margin: 0px;
      padding: 0px;
    }

    header {
      width: 100%;
      height: 100px;
      background-color: bisque;
    }

    .container {
      height: 200px;
      /* overflow: hidden; */
      padding: 0 200px;
    }

    .middle {
      width: 100%;
      height: 200px;
      float: left;
      background-color: azure;
    }

    .left {
      width: 200px;
      height: 200px;
      float: left;
      margin-left: -100%;
      position: relative;
      right: 200px;
      background-color: pink;
    }

    .right {
      width: 200px;
      height: 200px;
      float: left;
      margin-right: -200px;
      background-color: black;
    }

    footer {
      width: 100%;
      height: 100px;
      clear: both;
      background-color: darkslategray;
    }
  </style>
</head>

<body>
  <header>
    <h4>Header内容区</h4>
  </header>
  <div class="container">
    <div class="middle">
      <h4>中间弹性区</h4>
    </div>
    <div class="left">
      <h4>左边栏</h4>
    </div>
    <div class="right">
      <h4>右边栏</h4>
    </div>
  </div>
  <footer>
    <h4>Footer内容区</h4>
  </footer>
</body>

</html>
```

(4)双飞翼布局

dom结构

```html
<body>
  <!-- 双飞翼布局 -->
  <div id="header"></div>
  <div id="container" class="column">
    <div id="center">内容区</div>
  </div>
  <div id="left" class="column"></div>
  <div id="right" class="column"></div>
  <div id="footer"></div>
</body>
```



和圣杯布局主要区别在于container只包裹center区,并且为center添加margin为左右侧边栏预留空间

将container left 和right均设置为左浮动

container宽度设置为100%,center设置一个高度,并不用设置宽度因为会自动撑满整个内容区,并设置margin为左右侧边栏预留空间

left设置margin-left:-100%,和圣杯布局不同,因为这里的left的父元素是body,因此直接到达目标位置

right设置margin-left:-200px;直接到达目标位置

整体代码为

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>双飞翼布局</title>
  <style>
    body {
      min-width: 500px;
    }

    header {
      width: 100%;
      height: 100px;
      background-color: bisque;
    }

    .column {
      float: left;
    }

    #container {
      width: 100%;
    }

    #center {
      height: 400px;
      margin-left: 200px;
      margin-right: 200px;
      background-color: aquamarine;
    }

    #left {
      width: 200px;
      height: 400px;
      margin-left: -100%;
      background-color: bisque;
    }

    #right {
      width: 200px;
      height: 400px;
      margin-left: -200px;
      background-color: burlywood;
    }

    footer {
      width: 100%;
      height: 100px;
      clear: both;
      background-color: darkslategray;
    }
  </style>
</head>

<body>
  <!-- 双飞翼布局 -->
  <div id="header"></div>
  <div id="container" class="column">
    <div id="center">内容区</div>
  </div>
  <div id="left" class="column"></div>
  <div id="right" class="column"></div>
  <div id="footer"></div>
</body>

</html>
```

# 不正常的问题

(1)两个display：inline-block元素放到一起会产生一段空白

原因:元素被当成行内元素排版的时候，元素之间的空白符（空格、回车换行等）都会被浏览器处理，根据CSS中white-space属性的处理方式（默认是normal，合并多余空白），原来`HTML代码中的回车换行被转成一个空白符`，在字体不为0的情况下，空白符占据一定宽度，所以inline-block的元素之间就出现了空隙。

解决方法:

- 将子元素标签的结束符和下一个标签的开始符写在同一行或把所有子标签写在同一行

- 父元素中设置font-size: 0，在子元素上重置正确的font-size
- 为子元素设置float:left

(2)扇形

```css
/* 首先把一个盒子宽高置为0 */
width: 0px;
/* height: 0px; */
/* 全部利用边框的特性 */
border: 40px solid pink;
/* 然后把其他的边框透明化，这不有了吗 */
border-right-color: transparent;
border-left-color: transparent;
border-top-color: transparent;
/* 这个之前是一个等腰三角形 */
 border-radius: 50%;
```

