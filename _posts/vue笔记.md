---
 title: vue笔记
date: 2021-04-06 15:29:32
tags: 前端
---

# VUE笔记

## MVVM模型

M:模型(model) 对应data中的数据

V:视图(View) 模板,解析生成的dom结构

VM:视图模型(viewmodel) Vue实例对象,包括数据绑定和监听页面改变

<img src="D:\devApp\myblog\source\_posts\vue笔记\image-20220223165235780.png" alt="image-20220223165235780" style="zoom: 50%;" />

## 数据代理

数据代理:通过一个对象代理对另一个对象中的属性得操作.

vue中数据代理,通过vm对象来代理data对象中的属性得操作

好处是更加方便得操作data中的数据

基本原理:通过Object.defineProperty()把data对象中所有属性添加到vm上,为每一个添加到vm上的属性都指定一个getter/setter.在getter/setter内部去操作(读/写)data中对应得属性.

<img src="D:\devApp\myblog\source\_posts\vue笔记\image-20220223174431012.png" alt="image-20220223174431012" style="zoom:67%;" />

## 事件修饰符

- prevent 阻止默认事件,比如在超链接上@click.prevent,则点击后不会跳转
- stop 阻止事件冒泡,js中是e.stopPropagation()
- once 事件只触发一次
- capture  使用事件的捕获模式
- self 只有event.target是当前操作的元素时才触发事件,也可以阻止事件冒泡

## 计算属性

```json
computed:{
    //完整写法
	fullname:{
		get(){
			return "全名"
		}
        set(){
			return "全名"
		}
	},
    
    //如果确定以后不会手动对fullname进行修改的话,可以简写
    fullname(){
        return "哈哈哈哈"
    }
}
```

get什么时候被调用:

- 初次读取fullname的值时
- 所依赖的数据发生变化时

## 计算属性和监视属性的区别

1.computed能完成的功能,watch都可以完成

2.watch能完成的功能,computed不一定能完成 例如:watch可以进行异步操作

**注意:**

- 所有被Vue管理的函数,最好写成普通函数,因为这样this的指向才是组件实例对象
- 所有不被Vue管理的函数(定时器函数,ajax的回调函数等),最好写成箭头函数,这样this的指向才是组件实例对象

## v-for中key的原理

1.虚拟dom中key的作用:

​	key时虚拟dom对象的标识,当数据发生变化时,Vue会根据 新数据 生成 新的虚拟dom,随后进行新虚拟dom和旧虚拟dom的对比,规则如下

2.对比规则:

​	(1)旧虚拟dom中找到了与新虚拟dom相同的key:

​		a.若虚拟dom中内容没变,则直接使用之前的真实dom

​		b.若虚拟dom中内容变了,则生成新的真实dom,随后替换掉页面中之前的真实dom

​	(1)旧虚拟dom中没有找到与新虚拟dom相同的key:

​		创建新的真实dom,随后渲染到页面

3.用index作为key可能引发的问题

​	(1)若对数据进行 逆序添加,逆序删除等破环顺序的操作,会产生没有必要的真实dom更新,界面效果没有问题,但是效率低

​	(2)如果结构中还包含输入类的dom,会产生错误dom更新,界面也有问题

## 生命周期

- **1.beforeCreate** 无法通过vm访问到data中的数据和method中的方法
- 初始化:数据监测,数据代理
- **2.created** 可以通过vm访问到data中的数据和method中的方法
- vue开始解析模板,生成虚拟dom(内存中),页面还不能显示解析好的内容
- **3.beforeMont** 页面呈现的是未经vue编译的dom结构,所有对dom的操作最终都不奏效
- 将内存中的虚拟dom转为真实dom插入页面
- **4.monted** 页面中呈现的是经过vue编译的dom.对dom的操作均有效(尽可能避免),至此初始化过程结束,一般在此进行:开启定时器,发送网络请求,订阅消息,绑定自定义事件等初始化操作
- 如果改动了data中的数据
- **5.beforeUpdate** 此时,数据是新的,但是页面还是旧的,页面尚未和数据保持同步
- 根据新数据,生成新的虚拟dom,随后与就得虚拟dom进行比较,最终完成页面更新,即:完成Model -> View的更新
- **6.updated** 此时数据是新的,页面也是新的,即页面和数据保持同步
- 当vm.$destroy()被调用时 这会完全销毁一个实例,清理它与其他实例的连接,解绑他的全部指令及(自定义)事件监听器
- **7.beforeDestroy** vm中所有的data,methods,指令等等,都处于可用状态,但是在这个阶段中对数据进行的操作不会触发页面更新了,马上要执行销毁过程,一般在此阶段关闭定时器,取消订阅消息,解绑自定义事件等等收尾操作
- watch什么的都解绑
- **8.destroyed**

## 组件

为什么组件中的data要用函数,而不能用对象?

因为如果使用对象的话,当一个组件被不同的页面使用的时候,页面使用组件的data是使用的对象的引用,一个页面对data中的数据进行修改,另一个页面中的数据也会相应发生改变

组件的本质是一个名为VueComponent的构造函数,只需要写标签语法使用组件(或调用let a = Vue.extend({}),然后let b = new a(),这个b就是组件实例对象),vue解析时会帮我们创建这个组件的实例对象,即vue帮我们执行: new VueComponent(options),每一个组件都是一个新的VueComponent

### this指向

组件中的this指向是VueComponent实例

### 内置关系

VueComponent.prototype._ _ _proto_ __ === Vue.prototype

<img src="D:\devApp\myblog\source\_posts\vue笔记\image-20220301152249210.png" alt="image-20220301152249210" style="zoom: 50%;" />

这么做是让组件实例对象(vc)也可以访问到Vue原型上的属性和方法

### 组件的自定义事件

子传父的方法

**(1)使用emit**

父组件

```html
<!-- 使用emit方法,子向父传值 -->
学生名:<Student v-on:listenEvent="listenChildName"></Student>

methods: {
    listenChildName(data) {
      console.log(data);
    },
  },
```

子组件Student

```
<button @click="toParent">向父组件传递学生姓名</button>

data() {
    return {
      name: "张三",
      age: 13,
    };
  },
methods: {
    toParent() {
      this.$emit("listenEvent", this.name);
    },
  },
```

**(2)父向子传递函数**

父组件

```html
<!-- 使用父向子传递函数的方法,实现子向父传值 -->
学校名:<School :getSchoolName="getSchoolName"></School>

methods: {
    getSchoolName(data) {
      console.log(data);
    },
  },
```

子组件School

```html
<button @click="toParent">向父组件传递学校名称</button>

props: ["getSchoolName"],
  data() {
    return {
      name: "健康中学",
      age: 13,
    };
  },
  methods: {
    toParent() {
      this.getSchoolName(this.name);
    },
  },
```

**(3)使用ref**

为组件指定ref属性可以在函数中直接获取组件实例

父组件

```vue
<!-- 使用ref -->
    学生名:<Student ref="student"></Student>


 mounted() {
	//this.$refs.student获取的是student组件的实例
    this.$refs.student.$on("listenEvent", this.listenChildName);

	//或者直接获取子组件中的数据
	console.log(this.$refs.student.name)	//健康中学
  },
```

子组件和emit一样,emit发射listenEvent函数

### 解绑自定义事件

父组件

```html
<!-- 使用emit方法,子向父传值 -->
学生名:<Student v-on:listenEvent="listenChildName"></Student>

methods: {
    listenChildName(data) {
      console.log(data);
    },
  },
```

子组件Student

```
<button @click="toParent">向父组件传递学生姓名</button>

<button @click="unbind">解绑父组件定义的事件</button>

data() {
    return {
      name: "张三",
      age: 13,
    };
  },
methods: {
    toParent() {
      this.$emit("listenEvent", this.name);
    },
    unbind(){
      this.$off("listenEvent");
    }
  },
```

### 全局事件总线

任意组件间通信

需要一个中间组件,当作桥梁从而实现任意组件间的通信.

(1)首先,这个组件需要被所有其他组件看得到.因此根据内置关系一节所叙,VueComponent.prototype._ _ _proto_ __ === Vue.prototype,即vc可以访问到Vue原型上的属性和方法,因此可以把这个中间组件放在vue的原型对象上

(2)其次,这个组件需要能调用到$on $off 和$emit(这三个都在vue的原型对象上),因此这个组件需要是一个VueComponent实例或者Vue实例

实现:

(1)main.js

```js
new Vue({
  router,
  store,
  render: h => h(App),

  //$bus相当于一个中间组件(全局事件总线),实现任意组件间的通信
  beforeCreate(){
    Vue.prototype.$bus = this
  }
}).$mount('#app')

```

(2)两个兄弟的父组件(只需使用那两个组件)

```vue
<template>
  <div class="box">
    <School></School>
    <Student></Student>
  </div>
</template>
<script>
import Student from "../components/Student";
import School from "../components/School";
export default {
  data() {
    return {};
  },
  methods: {},
  components: { Student, School },
  mounted() {},
};
</script>
<style lang="less" scoped>
</style>
```

(3)其中一个兄弟组件Student(要传值的)

```vue
<template>
  <div class="box">
    <h2>学生姓名:{{ name }}</h2>
    <button @click="toSchool">我要把我的年龄传给兄弟组件School</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      name: "张三",
      age: 13,
    };
  },
  methods: {
    toSchool() {
      //让全局事件总线$bus触发listenAge事件,从而在School的monted中用全局事件总线$bus使用$on监听这个事件
      this.$bus.$emit("listenAge", this.age);
    },
  },
  components: {},
  created() {},
};
</script>
<style lang="less" scoped>
</style>
```

(3)其中一个兄弟组件School(要接收值的)

```vue
<template>
  <div class="box">
    <h2>学校名称:{{ name }}</h2>
  </div>
</template>
<script>
export default {
  data() {
    return {
      name: "健康中学",
      age: 13,
    };
  },
  methods: {},
  mounted() {
    //监听listenAge事件
    this.$bus.$on("listenAge", (data) => {
      console.log("我是School,我收到了" + data);
    });
  },
  beforeDestroy() {
    //因为全局都在用$bus,所以这上面定义的事件不使用时要及时解绑
    this.$bus.$off("listenAge");
  },
};
</script>
<style lang="less" scoped>
</style>
```

### 消息订阅与发布

也能实现任意组件间通信.

需要数据的人订阅消息,提供数据的人发布消息

(1)安装pubsub-js

npm i pubsub-js

(2)接收消息的人导入pubsub-js接收并编写回调函数

```
import pubsub from 'pubsub-js'

mounted(){
	this.pubId = pubsub.subscribe("hello",(函数名,data)=>{
		console.log(data)
	})
},
beforeDestroy(){
	pubsub.unsubscribe(this.pubId)
}
```

(3)发布消息的组件发布消息

```
import pubsub from 'pubsub-js'


methods:{
	//button触发这个函数
	toSchool(){
		pubsub.publish("hello","这是数据")
	}
}
```

### 总结

- 父子通信：父向子传递数据是通过 props，子向父是通过 $emit；通过 $parent / $children 通信；$refs 也可以访问组件实例；

  provide / inject(父子或爷孙都可以,只要是后代关系.但是当父组件的值改变时,子组件inject的值不能自动更改,可以通过直接provide整个组件的方式,使inject的值可以随父组件中的值更改而更改) ；

- 兄弟通信： EventBus；Vuex；

- 跨级通信： EventBus；Vuex；provide / inject ；$attrs / $listeners；

  $attrs / $listeners使用方法

  $attrs如果从父组件传过来的值，没有在子组件中被接收，那么这些值就会被存在$attrs对象中。

  ​	比如父组件传递的某一个数值在后代组件中没有使用props进行接收,那么那就会保存在this.$attrs中

  $listeners可以从孙子组件发送事件到父子组件中

  

## $nextTick

当在一个方法中对vue的数据进行更改时,一个数据更改后,vue并不会立即去解析模板,而是把这个方法中的所有逻辑执行完后再解析渲染

this.$nextTick(函数),会在dom节点更新之后再去执行回调.

用法:当数据改变后,要基于新的dom进行的操作



## 插槽

### 默认插槽

在组件中写标签slot

在组件的使用者中写要插入的

### 具名插槽

组件student中

```
<slot name="top">没有插入时默认显示的内容</slot>
<slot name="bottom">没有插入时默认显示的内容</slot>
```

组件使用者

```
<student slot="top">在顶部插槽插入的内容</student>
<student slot="bottom">在底部插槽插入的内容</student>
```

### 作用域插槽

主要用在数据在组件中,但是组件的使用者即使用插槽的部分想用这些数据的时候

组件student中,name在这里的data中

```
<slot :name="name" time="时间">没有插入时默认显示的内容</slot>

```

组件使用者中填入插槽的地方必须使用template

```
<student>
	<template scope="data">
		{{data}} //这样就能收到组件中的传来的所有数据
	</template>
</student>
```



## 路由

在router文件夹下的index.js文件中引用

```js
import VueRouter from 'vue-router'
import Vue from 'vue'

import Home from '../components/Home'
import About from '../components/About'
import User from '../components/User'
```

接着创建路由

```js
//1.通过vue.use(插件)安装插件
Vue.use(VueRouter)

//2.创建路由对象
const routes = [
  {
    path:'/home',
    component:Home
  },
  {
    path:'/about',
    component:About
  }

]

const router = new VueRouter({
  routes: routes,
  mode:'history'
})

//3.将router对象传入到vue实例中
export default router
```

在main.js中引入并挂载

这样在需要进行跳转的地方使用router-link标签,在to="/about"即可,用**router-view标签接收**该link传入的界面并显示

若想让项目运行直接进入首页则在routes加入一个router使其重定向到'/home',如下所示

```js
//2.创建路由对象
const routes = [
  {
    path:'',
    redirect:'/home'
  },
  {
    path:'/home',
    component:Home
  },
  {
    path:'/about',
    component:About
  }

]
```

### router-link标签的属性补充

tag:router-link标签默认渲染成a标签,可以通过tag属性进行更改成button li等

replace:将跳转模式从pushState变成replaceState,即不可返回,只能点击进行跳转

active-class:当router-link被点击时该标签会处于活跃状态,自动带有class="router-link-active",这个class的名字可以通过active-class进行更改

### 通过代码进行路由跳转

按钮添加点击事件

```html
<button @click="toHome">首页</button>
```

在methods中实现方法

```js
methods:
  {
    toHome() {
      this.$router.push('/home')
    },
    toAbout(){
      this.$router.replace({
      	path:'/about',
        query:{
            id:this.id,
            name:this.name
        }
      })
    },
    toForward(){
        this.$router.forward()	//回退
    },
    go(){
        this.$router.go(3)	//前进三步
    }
  }
```

this.$router获取到的是router中定义的大的router,即包括所有的routes

### 动态路由传参

#### params方式

在创建路由的时候在路径后面拼接冒号和相应字段

```js
{
  //记住冒号后面拼接的字段名称,要用到
  path:'/user/:userId',
  component: User
}
```

在用到路由的地方使用router-link标签,但是需要拼接data中的用户名,因此在to属性前加冒号进行绑定,将/user使用单引号括起来代表字符串,而后面拼接的userId则为绑定的data中userId的值

```html
<router-link :to="'/user/'+userId">用户</router-link>

<router-link :to="`/user/${userId}`">用户</router-link>

<router-link :to="{
  	name:"user",	<!-- 这种跳转方式传params参数,必须使用命名路由方式 -->
    params:{
        userId      
    }
  }">用户</router-link>
```

```js
data() {
  return{
    message:'第一次尝试',
    userId:'zhangsan'
  }
}
```

这样就能动态路由到http://localhost:8080/user/zhangsan这样的url

如果想在User界面使用userId则在User界面的添加computed

```js
computed:{
  userId(){
    return this.$route.params.userId
  }
}
```

其中this.$route获取到的是当前活跃的route.接下载在界面中使用{{userId}}接收展示即可.

#### query方式

配置路由的时候正常编写

```js
{
  path:'/about',
  component:About
}
```

在router-link的to中传入对象,to前面需要加冒号

```
<router-link :to="{path:'/about',query:{name:'fx',age:23}}">关于</router-link>
```

这样传参方式的url为http://localhost:8080/about?name=fx&age=23这种形式

在相应的about界面中可以以下面的方式取出参数

```
<div>
  <h2>我是关于</h2>
  <h2>{{$route.query}}</h2>
  <h2>{{$route.query.name}}</h2>
  <h2>{{$route.query.age}}</h2>
</div>
```

如果不使用router-link 而使用按钮的话,则在点击的方法里传入

```
<button @click="toAbout">关于按钮</button>
```

```
toAbout(){
  this.$router.push({
    path:'/about',
    query:{
      name:'fx',
      age:23
    }
  })
}
```

### 路由的props

```js
{
  //记住冒号后面拼接的字段名称,要用到
  path:'/user/:userId/:username',
  component: User
}
```

假如说像上面这种情况下,需要传递params类型的参数,就需要在组件中一直使用this.$route.params.xxx来接收展示,可以使用props更加方便

```js
{
  //记住冒号后面拼接的字段名称,要用到
  path:'/user/:userId/:username',
  component: User,
  //这样就会把该路由组件收到的所有params参数,以props的形式传给组件,在组件中接收直接使用
  props:true
}
```

组件中接收并使用

```js
//组件的props中
props:['userId','username']
```

### 路由的懒加载

因为不进行懒加载的话会将所有的业务代码整合成一个js文件,在请求静态页面时可能时间请求的时间有点长导致页面中出现一段时间的空白现象,使用路由懒加载后将使用懒加载方式的每个页面抽离成另外的数个js文件,这样只有在需要的时候进行加载,不需要的时候不加载.

路由懒加载

```js
const Home = () => import('../components/Home')
const About = () => import('../components/About')
const User = () => import('../components/User')
```

### 路由嵌套

/home/message 和 /home/news即一个大的路由下嵌套两个小的子路由

```js
{
  path:'/home',
  component:Home,
  children:[
    {
      path:'news',
      components:HomeNews
    },
    {
      path:'messages',
      components:HomeMessage
    }
  ]
}
```

在大的路由中使用children,数组中为子路由,其中**子路由的path前不需要加/**

因为是首页的子路由,所以将router-view放在首页的组件中用来接收子路由的页面

```
<router-link to="/home/news"></router-link>
```

嵌套路由的router-link的跳转要写完整路径

### 命名路由

可以简化深层嵌套时跳转的代码

```js
{
  path:'/home',
  component:Home,
  children:[
    {
      path:'news',
      components:HomeNews
    },
    {
      name:'xiaoxi'	//这是命名了的路由
      path:'messages',
      components:HomeMessage
    }
  ]
}
```

这种情况如果普通跳转需要在to属性中写完整路径,但是利用命名路由可以简化

```
<router-link :to="{
	name:'xiaoxi'
}">
</router-link>
```

### 路由组件独有的生命周期

activated 和 deactivated

### history和hash模式

在路由中配置

```js
mode:hash //或者history
```

hash模式就是路径中带#的路径模式,最大的特点是#后的内容不会作为路径的一部分发送给服务器,兼容性好一些.



## 导航守卫

### 全局前置路由守卫

初始化的时候和路由跳转的时候被调用

对全局的跳转进行监听,可以在监听函数中做一些事情

比如需求为跳转到任意界面时显示当前页面的title则可以使用导航守卫

首先在每个路由中添加meta数据,将title进行定义

```js
{
  path:'/home',
  component:Home,
  meta:{
    title:'首页'
  },
  children:[
    {
      path:'news',
      component:HomeNews
    },
    {
      path:'messages',
      component:HomeMessage
    }
  ]
},
{
  path:'/about',
  component:About,
  meta: {
    title: '关于'
  }
},
{
  //记住冒号后面拼接的字段名称,要用到
  path:'/user/:userId',
  component: User,
  meta: {
    title: '用户'
  }
}
```

在route的配置文件中使用导航守卫

```js
router.beforeEach((to,from,next) => {
  //从from跳转到to
  if(localStorage.name == '管理员'){
      next()
  }else{
      alert('您没有权限')
  }
})
```

比如需要验证权限才能进行跳转

router.beforeEach需要传入一个函数,必须带有to,from,next参数,可以使用箭头函数.

next()函数是定义好的,只有执行才会跳转

### 后置路由守卫

比如更改标签栏标题

```
router.afterEach((to,from) => {
  document.title = to.meta.title
})
```

### 独享路由守卫

```js
{
  path:'/home',
  component:Home,
  meta:{
    title:'首页'
  },
  beforeEnter(to,from,next)=>{
  
  }
},
```

独享路由守卫只有前置 没有后置

### 组件内路由守卫

这个是在路由的组件内定义

```js
<script>
// @ is an alias to /src

export default {
  name: "about",
  data() {
    return {
      mes: "这是消息",
    };
  },
  methods: {},
  
  
  beforeRouteEnter(to,from,next){
      //通过路由规则,进入该组件时被调用
      //在渲染该组件的对应路由被confirm前调用
      //此时组件实例还没被创建,不能获取组件实例this
      next(vm=>{
          //通过vm访问组件实例
      })
  },
  beforeRouteUpdate(to,from,next){
    //在当前路由改变,但是该组件被复用时调用,可以访问实例this
    //举例:对于一个有动态参数的路径 /foo/:id.在/foo/1和foo/2之间跳转的时候
  },
  
  beforeRouteLeave(to,from,next){
  	//通过路由规则,离开该组件时被调用.可以访问this
  }
};
</script>
```

beforeRouteEnter 守卫 **不能** 访问 **this**，因为守卫在导航确认前被调用,因此即将登场的新组件还没被创建。不过，你可以通过传一个回调给 next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。beforeRouteEnter 是支持给 next 传递回调的唯一守卫。对于 beforeRouteUpdate 和 beforeRouteLeave 来说，this 已经可用了，所以**不支持**传递回调，因为没有必要了。

## 动态绑定style

动态绑定style,这里需要父组件传入相应的样式,比如传入字体的颜色

首先在子组件中的props中定义用于接收父组件传入的参数activeColor,要求是String类型,默认为red(即当父组件没有传入activeColor时默认为红色)

```js
props:{
  path:String,
  activeColor:{
    type:String,
    default:'red'
  }
}
```

在父组件中传入activeColor

```html
<TabbarItem path="/home" active-color="blue">
  <img src="./assets/img/tabbar/mine.png" slot="item-icon">
  <img src="./assets/img/tabbar/fruit.png" slot="item-icon-active">
  <div slot="item-name">首页</div>
</TabbarItem>
```

在子组件中动态绑定样式并加入计算属性activeStyle

```html
<div :style="activeStyle"><slot name="item-name"></slot></div>
```

```js
computed:{
  isActive(){
    return this.$route.path.indexOf(this.path)!== -1
  },
  activeStyle(){
    return this.isActive ? {color:this.activeColor} : {}
  }
}
```

## 更改文件夹别名

一些常用的文件夹比如assets和components等中的文件在引用的时候需要../../../../很麻烦,所以可以更改别名直接使用

在项目的build文件夹下找到webpack.base.conf.js文件,找到下面的代码处进行更改

```js
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    '@': resolve('src'),
    'assets':resolve('src/assets'),
    'components':resolve('src/components')
  }
```

alias即进行别名的设置,我这里设置了assets和components文件夹

这样在import时只需使用别名即可,但是注意如果是img标签中的src属性需要在前面加上~,如下所示

```html
<img src="~assets/img/tabbar/mine.png" slot="item-icon">
<img src="~assets/img/tabbar/fruit.png" slot="item-icon-active">
```

## 配置代理跨域

第一种方法

vue.config.js

```
devServer:{
	proxy:'http://localhost:8000'	//服务器端口号,相当于开启了一个代理服务器,代理服务器和发起请求的用相同的端口号
	//再代码中请求的时候直接写请求的端口号
}
```

缺点:public里面的东西就是代理服务器里的东西,如果public里面有请求的东西,就不会再发请求而是直接返回.第二个缺点是只能代理一个服务器

第二种方法

devServer:{
	proxy:{

​		"/qianzhui":{

​				target:"http://localhost:8000",	//代码中请求接口时在端口号后面加前缀,端口号用自己的

​				pathRewrite:{'^/qianzhui':''}	//这样在请求时请求会忽略端口号后面的/qianzhui,这样才能是正确的请求地址

​		}

​	}
}

## axios

### axios基本使用

安装axios

```
npm install axios --save
```

引用

```js
import axios from "axios";
```

使用axios发送请求

```js
axios({
  url:'https://api-hmugo-web.itheima.net/api/public/v1/goods/detail',
  method:'get',
  params:{
    goods_id:1111
  }
}).then(res =>{
  console.log(res);
})
```

### axios并发

axios发送并发请求,当两个请求都拿到结果的时候再处理相应的事情

```js
axios.all([
  axios({
    url:'https://api-hmugo-web.itheima.net/api/public/v1/goods/detail',
    method:'get',
    params:{
      goods_id:1111
    }
  }),
  axios({
    url:'https://api-hmugo-web.itheima.net/api/public/v1/goods/detail',
    method:'get',
    params:{
      goods_id:2222
    }
  })
]).then(results => {
  console.log(results);
})
```

### axios实例

上面的axios请求用的都是全局的axios和axios配置.但是相对于实际开发中的不同的请求会创建axios实例,并对每个实例进行相应的配置

```js
//第一个axios实例
const axiosInstance1 = axios.create({
  baseURL:'https://api-hmugo-web.itheima.net/api/public/v1',
  timeout:5000
})
axiosInstance1({
  url:'/goods/detail?goods_id=8888'
}).then(res => {
  console.log(res);
})

//第二个axios实例
const axiosInstance2 = axios.create({
  baseURL:'https://api-hmugo-web.itheima.net/api/public/v1',
  timeout:5000
})
axiosInstance2({
  url:'/goods/detail?goods_id=6666'
}).then(res => {
  console.log(res);
})
```

### 封装axios

新建request.js文件

第一种方法采用回调函数方式,编辑request.js

```js
import axios from "axios";

//第一种采用回调函数的方式
export function request(config,success,failure){
  //1.创建axios实例
  const axios1 = axios.create({
    baseURL:'https://api-hmugo-web.itheima.net/api/public/v1',
    timeout:5000
  })
  //2.发送网络请求
  axios1(config).then(res => {
    success(res)
  }).catch(err => {
    failure(err)
  })
}
```

在使用的地方

```js
//5.封装request模块 用第一种回调的方法
import {request} from "./network/request";
request({
  url:'/goods/detail?goods_id=6666'
},res => {
  console.log(res);
},err => {
  console.log(err);
})
```

第二种方法采用promise,编辑request.js

```js
import axios from "axios";

//第二种采用promise的方式
export function request(config){

  return new Promise((resolve,reject) => {
  
    //1.创建axios实例
    const axios1 = axios.create({
      baseURL:'https://api-hmugo-web.itheima.net/api/public/v1',
      timeout:5000
    })
    
    //2.发送网络请求
    axios1(config)
      .then(res => {
        resolve(res)
      }).catch(err => {
        reject(err)
    })
    
  })

}
```

在使用的地方

```js
import {request} from "./network/request";
request({
  url:'/goods/detail?goods_id=6666'
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```

### axios拦截器

```js
import axios from "axios";

export function request(config){
  return new Promise((resolve,reject) => {
    //1.创建axios实例
    const axios1 = axios.create({
      baseURL:'https://api-hmugo-web.itheima.net/api/public/v1',
      timeout:5000
    })

    //2.axios拦截器
    //2.1请求拦截
    axios1.interceptors.request.use(config=>{
      //请求成功时,config传来的是本次请求的一些配置,包括请求方法,url和header等
      console.log(config);
      return config;

      //什么时候使用请求拦截器
      //1.比如检查config中的一些信息是否符合服务器的要求
      //2.比如每次发送请求时,都希望在界面中显示一个正在加载的图标
      //3.某些网络请求必须携带的一些信息(比如登录的token)

    },err => {
      //请求失败时
      console.log(err);
    })
    //2.2响应拦截
    axios1.interceptors.response.use(res => {
      //成功响应返回的是响应的结果
      console.log(res);
      return res;
    },err => {
      console.log(err);
    })

    //3.发送网络请求
    axios1(config)
      .then(res => {
        resolve(res)
      }).catch(err => {
        reject(err)
    })

  })
```

## vuex

vuex是一个状态管理工具,简单来说就是几个共享数据的存储中心,当许多组件都需要用一些数据的时候就可以把这些数据用vuex进行管理.

vuex的最重要的特点就是响应式.

主要管理哪些状态呢?

​	比如用户的登录状态,用户名称,头像,地理位置信息等

​	比如商品的收藏,购物车商品等

### vuex安装

```
npm install vuex --save
```

### 工作原理

<img src="D:\devApp\myblog\source\_posts\vue笔记\image-20220321093805174.png" alt="image-20220321093805174" style="zoom: 50%;" />



### vuex使用

在项目中新建一个store文件夹,并在store文件夹下新建index.js文件,内容如下

```js
import Vue from "vue";
import Vuex from "vuex"

//1.安装插件
Vue.use(Vuex)

//2.创建对象
const store = new Vuex.Store({

  //1.state 保存状态,在界面中使用的时候语法: $store.state.counter
  state:{
    counter:1000,
    stus:[
      {
        name:"curry",
        age:14
      },
      {
        name:"james",
        age:22
      },
      {
        name:"张三",
        age:31
      }
    ]
  },
  //2.mutations 方法,可以对state中的数据进行修改操作(更改state的唯一方法就是提交mutation),其中的方法默认又一个state参数
  //但是在调用这些方法的时候不可以使用$store.mutations.方法名直接调用
  //而是使用commit  如this.$store.commit("increment")
  //2.1如果想要调用时传参的话语法为this.$store.commit("increment",num),其中num为自定义的参数,这个参数叫做payload载荷
  //这样在mutations里面的方法中,传入的第一个参数默认时state,第二个写num即可
  //比如下面的increase方法
  mutations:{
    increment(state){
      state.counter++
    },
    decrement(state){
      state.counter--
    },
    //比如加几需要通过调用传入
    increase(state,num){
      state.counter += num
    }
    
  },
  //3.actions
  actions:{},
  //4.getters 相当于计算属性,其中的方法默认第一个参数为state,第二个为getters本身,不再接收其他参数
  //如果想要在调用时传参的话,可以返回一个带参数的函数
  //比如下面的getStu方法,用来获取比输入参数age年龄小的学生,在调用时使用$store.getters.getstu(20)即可
  getters:{
    getStu(state,getters){
      return function (age){
        return state.stus.filter(s => s.age<age)
      }
    }
  },
  modules:{}

})

//3.store
export default store
```

接下来在main.js中进行挂载

### vuex结构

```js
import Vue from "vue";
import Vuex from "vuex"
import {
  UPDATE
} from "./mutations-types";

//1.安装插件
Vue.use(Vuex)

//2.创建对象
const store = new Vuex.Store({

  //1.state 保存状态,在界面中使用的时候语法: $store.state.counter
  state:{
    counter:1000,
    stus:[
      {
        name:"curry",
        age:14
      },
      {
        name:"james",
        age:22
      },
      {
        name:"张三",
        age:31
      }
    ]
  },
  //2.mutations 方法,可以对state中的数据进行修改操作(更改state的唯一方法就是提交mutation),其中的方法默认又一个state参数
  //但是在调用这些方法的时候不可以使用$store.mutations.方法名直接调用
  //而是使用commit  如this.$store.commit("increment")
  //2.1如果想要调用时传参的话语法为this.$store.commit("increment",num),其中num为自定义的参数,这个参数叫做payload载荷
  //这样在mutations里面的方法中,传入的第一个参数默认时state,第二个写num即可
  //比如下面的increase方法
  mutations:{
    increment(state){
      state.counter++
    },
    decrement(state){
      state.counter--
    },
    //比如加几需要通过调用传入
    increase(state,num){
      state.counter += num
    },
    //响应式修改state
    [UPDATE](state){
      //响应式的添加address属性
      Vue.set(state.stus[0],"address","遵化")
      //响应式的删除address属性
      // Vue.delete(state.stus[0],"address")
    }

  },
  //3.actions 在这里进行异步操作,比如网络请求,里面的方法默认的参数为context,可以理解为等同于store,通过context.sta
  //这样的话在页面中调用actions里面的方法时需要使用dispatch,如this.$store.dispatch("updateCounter")
  //这里和mutations中一样可以传参,即payload
  actions:{
    updateCounter(context){
      setTimeout(()=>{
        //这里进行对数据的更改,但是一定要通过提交mutation的方式,即commit
        context.commit('increment')
      },4000)
    }
  },
  //4.getters 相当于计算属性,其中的方法默认第一个参数为state,第二个为getters本身,不再接收其他参数
  //如果想要在调用时传参的话,可以返回一个带参数的函数
  //比如下面的getStu方法,用来获取比输入参数age年龄小的学生,在调用时使用$store.getters.getstu(20)即可
  getters:{
    getStu(state,getters){
      return function (age){
        return state.stus.filter(s => s.age<age)
      }
    }
  },
  //5.modules 可以对state进行模块式的划分,在取出a的state中定义的name的时候语法 $store.state.a.name
  modules:{
    a:{
      //页面中取出这个模块里定义的name $store.state.a.name
      state:{
        name:"dddd"
      },
      //页面的方法中使用模块中mutations定义的方法 this.$store.commit("mutations中的方法名")
      mutations:{

      },
      //模块中action也是处理异步操作,里面的方法默认的参数是context,不过这个context能commit的方法自能是自身的mutations中的方法
      //context.也可以得到rootGetters和rootState
      actions:{

      },
      //模块中的getters有三个默认参数,前两个和大的store的getters一样,一个是state(自己的state),一个是getters自身.
      // 模块中特有的第三个是默认参数是rootState,即大的store中的state
      getters:{

      }

    }
  }

})

//3.store
export default store
```

### mapState与mapGetters

mapState返回的是一个对象,里面是数个函数,每个函数对应着返回state中的值

```js
import {mapState} from "vuex"

computed:{
	//前面是在当前组件中要使用的名字,后面字符串是在state中的名字,对象写法
	...mapState({xuexiao:"school",dizhi:"adress"}),
    //数组写法,这数组中的两个字符串要对应着state中的定义名称
    ...mapState(['school','adress']),
	
	//上面第一种对象写法等同于下面的简化
	xuexiao(){
		return this.$store.state.school
	},
	dizhi(){
		return this.$store.state.adress
	}
}
```

### mapActions和mapMutations

```js
<button @click="increase(num)" >加

import {mapMutations} from "vuex"

data(){
	return{
		num:1
	}
},
methods:{
	//以这种mapMutations简写的方式,传参需要在调用函数的地方传参
	...mapMutations({increase:'jia',sub:'jian'})
	
	//相当于这样,但是以这样的形式 传参是在这里面.上面@click中不用
	increase(){
		this.$store.commit('jia',this.sum)
	}
}
```



### vuex响应式规则

规则:需要提前在store中初始化好所需的属性

响应式修改数据方法:

```js
//Vue.set(想要修改的数组或类,数组的下标或者类的属性名,修改后的内容)
//响应式修改state
    updateStus(state){
      //响应式的添加address属性
      Vue.set(state.stus[0],"address","遵化")
      //响应式的删除address属性
      Vue.delete(state.stus[0],"address")
    }
```

### mutations类型

新建mutations-types.js文件

```js
export const UPDATE = 'update'
```

在App.vue中(即任何页面文件)

```js
import {
  UPDATE
} from "./store/mutations-types";
```

在页面的methods中使用时

```js
update(){
  this.$store.commit(UPDATE)
}
```

在index.js中

```js
import {
  UPDATE
} from "./mutations-types";

//在mutations的对应的方法
[UPDATE](state){
      //响应式的添加address属性
      Vue.set(state.stus[0],"address","遵化")
      //响应式的删除address属性
      // Vue.delete(state.stus[0],"address")
    }
```

### 封装成优雅的目录组织

```js
import Vue from "vue"
import Vuex from "vuex"

Vue.use(Vuex)

const countOptions = {
    namespaced: true,
    actions: {},
    mutations: {},
    state: {
        num:2,
        sum:10
    },
    getters: {}
}

const personOptions = {
    namespaced: true,
    actions: {},
    mutations: {},
    state: {
        name:'姓名',
        age:19
    },
    getters: {}
}

//创建并导出store
export default new Vuex.Store({
    modules: {
        countModule: countOptions,
        personModule: personOptions
    }
})
```

这样的结构在组件中需要如下使用mapState

```js
computed:{
	//这是分别从各个部分的module中获取相应的值
	...mapState('countModule',['num','sum'])
	...mapState('personModule',['name','age'])
    
    //这是自己手写调用mutations里的方法时,需要加入 名称/
    this.$store.commit('personAbout/ADD_PERSON',personObj)
    this.$store.dispatch('personAbout/tianjia',personObj)
}
```

# vue官方文档笔记

## 指令

### 动态参数

也可以在指令参数中使用 JavaScript 表达式，方法是用方括号括起来：

```html
!--
注意，参数表达式的写法存在一些约束，如之后的“对动态参数表达式的约束”章节所述。
-->
<a v-bind:[attributeName]="url"> ... </a>
```

这里的 `attributeName` 会被作为一个 JavaScript 表达式进行动态求值，求得的值将会作为最终的参数来使用。例如，如果你的组件实例有一个 data property `attributeName`，其值为 `"href"`，那么这个绑定将等价于 `v-bind:href`。



## 计算属性vs方法

```js
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>计算后的message: "{{ reversedMessage }}"</p>
  <p>调用方法后的message: "{{ reversedMessage() }}"</p>
</div>

var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
  methods: {
  	reversedMessage: function () {
    	return this.message.split('').reverse().join('')
  	}
  }

})
```

我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是**计算属性是基于它们的响应式依赖进行缓存的**。**只在相关响应式依赖发生改变时它们才会重新求值**。**这就意味着只要 `message` 还没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。**

我们为什么需要缓存？假设我们有一个性能开销比较大的计算属性 **A**，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 **A**。如果没有缓存，我们将不可避免的多次执行 **A** 的 getter！如果你不希望有缓存，请用方法来替代。

## v-show和v-if

不同的是带有 **`v-show` 的元素始终会被渲染并保留在 DOM** 中。`v-show` 只是简单地切换元素的 **CSS property `display`**。

**`v-if`** 是“真正”的条件渲染，因为它会确保**在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建**。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

**一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。**

## v-for和v-if

我们**不**推荐在同一元素上使用 `v-if` 和 `v-for`。当它们处于同一节点，`v-if` 的优先级比 `v-for` 更高，这意味着 `v-if` 将没有权限访问 `v-for` 里的变量：

```html
<!-- This will throw an error because property "todo" is not defined on instance. -->

<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>	<!-- 报错"todo" is not defined -->
```

可以把 `v-for` 移动到 `<template>` 标签中来修正：

```html
<template v-for="todo in todos" :key="todo.name">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

## 事件修饰符

```js
<!-- 阻止单击事件继续传播 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
```

## 钩子函数(生命周期)

`beforeCreate()` 在实例创建之间执行，数据未加载状态

`created()` 在实例创建、数据加载后，能初始化数据，`dom`渲染之前执行

`beforeMount()` 虚拟`dom`已创建完成，在数据渲染前最后一次更改数据

`mounted()` 页面、数据渲染完成，真实`dom`挂载完成

`beforeUpadate()` 重新渲染之前触发

`updated()` 数据已经更改完成，`dom` 也重新 `render` 完成,更改数据会陷入死循环

`beforeDestory()` 和 `destoryed()` 前者是销毁前执行（实例仍然完全可用），后者则是销毁后执行

## 子组件向父组件发射

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      @enlarge-text="postFontSize += 0.1"
    ></blog-post>
  </div>
</div>
```

```js
const app = {
  data() {
    return {
      posts: [
        /* ... */
      ],
      postFontSize: 1
    }
  }
}
app.component('blog-post', {
  props: ['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button @click="$emit('enlargeText')">
        Enlarge text
      </button>
    </div>
  `
})
```

### 使用事件抛出一个值

有的时候用一个事件来抛出一个特定的值是非常有用的。例如我们可能想让 `<blog-post>` 组件决定它的文本要放大多少。这时可以使用 `$emit` 的第二个参数来提供这个值：

```html
<button @click="$emit('enlargeText', 0.1)">
  Enlarge text
</button>
```

然后当在父级组件监听这个事件的时候，我们可以通过 `$event` 访问到被抛出的这个值：

```html
<blog-post ... @enlarge-text="postFontSize += $event"></blog-post>
```

或者，如果这个事件处理函数是一个方法：

```html
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
```

那么这个值将会作为第一个参数传入这个方法：

```js
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

## 插槽

**`v-slot` 只能添加在 `<template>` 上** 

### 作用域插槽

例如，我们有一个组件，包含 todo-items 的列表。

```js
app.component('todo-list', {
  data() {
    return {
      items: ['Feed a cat', 'Buy milk']
    }
  },
  template: `
    <ul>
      <li v-for="(item, index) in items">
        {{ item }}
      </li>
    </ul>
  `
})
```

我们可能会想把 `{{ item }}` 替换为 `<slot>`，以便在父组件上自定义。

```html
<todo-list>
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

但是，这是行不通的，因为只有 `<todo-list>` 组件可以访问 `item`，我们将从其父组件提供槽内容。

要使 `item` 可用于父级提供的插槽内容，我们可以添加一个 `<slot>` 元素并将其绑定为属性：

```html
<ul>
  <li v-for="( item, index ) in items">
    <slot :item="item"></slot>
  </li>
</ul>
```

绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**。现在在父级作用域中，我们可以使用带值的 `v-slot` 来定义我们提供的插槽 prop 的名字：

```html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>
</todo-list>
```

引用如下图

![作用域插槽](image-20210715174253976.png)

## 访问父组件实例

this.$parent

可以通过这种方法取出父组件的数据或者方法

## 依赖注入

想象一下这样的结构：有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。对于这种情况，我们可以使用一对 `provide` 和 `inject`。无论组件层次结构有多深，父组件都可以作为其所有子组件的依赖提供者。这个特性有两个部分：父组件有一个 `provide` 选项来提供数据，子组件有一个 `inject` 选项来开始使用这些数据。

`provide` 选项允许我们指定我们想要提供给后代组件的数据/方法。

```js
//父组件中通过provide提供
provide: function () {
    return {
      posts: this.posts
    }
  }
```

```js
//在子组件中通过inject注入
inject:['posts'],
```

使用的时候用法和props一样

实际上，你可以把依赖注入看作一部分“大范围有效的 prop”，除了：

- 祖先组件不需要知道哪些后代组件使用它提供的 property
- 后代组件不需要知道被注入的 property 来自哪里

---

然而，依赖注入还是有负面影响的。它将你应用程序中的组件与它们当前的组织方式耦合起来，使重构变得更加困难。同时所提供的 property 是**非响应式的**。这是出于设计的考虑，因为使用它们来创建一个中心化规模化的数据跟[使用 `$root`](https://cn.vuejs.org/v2/guide/components-edge-cases.html#访问根实例)做这件事都是不够好的。如果想要提供响应式的话可以提供this,这样this中的data和props等均是响应式的,也就能够响应式.

默认情况下，`provide/inject` 绑定*并不是*响应式的。我们可以通过传递一个 `ref` property 或 `reactive` 对象给 `provide` 来改变这种行为。在我们的例子中，如果我们想对祖先组件中的更改做出响应，我们需要为 provide 的 `todoLength` 分配一个组合式 API `computed` property：

```js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})

app.component('todo-list-statistics', {
  inject: ['todoLength'],
  created() {
    console.log(`Injected property: ${this.todoLength.value}`) // > Injected property: 5
  }
})
```

---

## 路由Hash和History

Hash 丑,无法使用锚点定位

History 需要后端配合,IE9不兼容(可使用强制刷新处理)

![image-20210518093857934](D:\devApp\myblog\source\_posts\vue笔记.assets\image-20210518093857934.png)

前面的几种操作会触发源代码中的updateRoute,再由updateRoute里面去改变响应式数据,响应式数据改变了之后会自动触发router-view的更新

## 单页面应用(SPA)

缺点: 不利于SEO(服务器渲染SSR解决),首屏加载时间长(预渲染解决)

## vue如何追踪变化

当你把一个普通的 JavaScript 对象传入 Vue 实例作为 `data` 选项，Vue 将遍历此对象所有的 property，并使用 [`Object.defineProperty`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 把这些 property 全部转为 [getter/setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects#定义_getters_与_setters)。`Object.defineProperty` 是 ES5 中一个无法 shim 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因。

这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在 property 被访问和修改时通知变更。

每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

<img src="D:\devApp\myblog\source\_posts\vue笔记.assets\image-20210518143956099.png" alt="image-20210518143956099" style="zoom: 33%;" />

Vue 无法检测 property 的添加或移除。由于 Vue 会在初始化实例时对 property 执行 getter/setter 转化，所以 property 必须在 `data` 对象上存在才能让 Vue 将它转换为响应式的。

# vue原理部分

##  父子组件生命钩子顺序

- 加载渲染过程

  父 beforeCreate->父 created->父 beforeMount->子 beforeCreate->子 created->子 beforeMount->子 mounted->父 mounted

- 销毁过程

  父 beforeDestroy->子 beforeDestroy->子 destroyed->父 destroyed

## 路由钩子函数执行顺序

路由钩子函数有三种：

​      全局钩子： beforeEach、 afterEach、beforeResolve

​      单个路由里面的钩子：  beforeEnter

​      组件路由：beforeRouteEnter、 beforeRouteUpdate、 beforeRouteLeave

**完整的导航解析流程:**

1. 导航被触发。
2. 在失活的组件里调用 beforeRouteLeave 守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## nextTick原理

由于Vue DOM更新是异步执行的，即修改数据时，视图不会立即更新，只要监听数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更，在缓冲时会去除重复数据，从而避免不必要的计算和DOM操作。
然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。等同一数据循环中的所有数据变化完成之后，再统一进行视图更新。为了确保得到更新后的DOM，所以设置了 Vue.nextTick()方法。

在Vue实例中更改数据之后需要改动DOM，如果需要操作更新后的DOM，就要使用`nextTick()`，在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

具体实现异步的方法顺位是`Promise > MutationObserver > setImmediate > setTimeout`，就是使用这4种方法，包裹callback函数，使得callback可以异步执行

定义一个flushCallbacks函数,在这个函数里遍历所有的callbacks并执行

一个timerFunc函数,在这个函数中顺序判断上面四种方法,再其中调用flushCallbacks函数

- 把传入的回调函数`cb`压入`callbacks`数组
- 执行`timerFunc`函数，延迟调用 `flushCallbacks` 函数
- 遍历执行 `callbacks` 数组中的所有函数

## 数据响应式原理

Vue就是一个构造函数,里面调用Vue原型上的_init函数进行了初始化的操作

```js
// Vue就是一个构造函数 通过new关键字进行实例化
function Vue(options) {
  // 这里开始进行Vue初始化工作
  this._init(options);
}
// _init方法是挂载在Vue原型的方法 通过引入文件的方式进行原型挂载需要传入Vue
// 此做法有利于代码分割
initMixin(Vue);
export default Vue;
```

```js
// src/init.js
import { initState } from "./state";
export function initMixin(Vue) {
  Vue.prototype._init = function (options) {
    const vm = this;
    // 这里的this代表调用_init方法的对象(实例对象)
    //  this.$options就是用户new Vue的时候传入的属性
    vm.$options = options;
    // 初始化状态
    initState(vm);
  };
}
```

initMixin 把_init 方法挂载在 Vue 原型 供 Vue 实例调用.并调用initState函数,传入vm.initState中依次进行初始化props-->methods-->data-->computed-->watch操作

```js
// src/state.js
import { observe } from "./observer/index.js";

// 初始化状态 注意这里的顺序 比如我经常面试会问到 是否能在data里面直接使用prop的值 为什么？
// 这里初始化的顺序依次是 prop>methods>data>computed>watch
export function initState(vm) {
  // 获取传入的数据对象
  const opts = vm.$options;
  if (opts.props) {
    initProps(vm);
  }
  if (opts.methods) {
    initMethod(vm);
  }
  if (opts.data) {
    // 初始化data
    initData(vm);
  }
  if (opts.computed) {
    initComputed(vm);
  }
  if (opts.watch) {
    initWatch(vm);
  }
}

// 初始化data数据
function initData(vm) {
  let data = vm.$options.data;
  //   实例的_data属性就是传入的data
  // vue组件data推荐使用函数 防止数据在组件之间共享
  data = vm._data = typeof data === "function" ? data.call(vm) : data || {};

  // 把data数据代理到vm 也就是Vue实例上面 我们可以使用this.a来访问this._data.a
  for (let key in data) {
    proxy(vm, `_data`, key);
  }
  // 对数据进行观测 --响应式数据核心
  observe(data);
}
// 数据代理
function proxy(object, sourceKey, key) {
  Object.defineProperty(object, key, {
    get() {
      return object[sourceKey][key];
    },
    set(newValue) {
      object[sourceKey][key] = newValue;
    },
  });
}
```

initData中主要将data赋值给实例中的_data,并将data代理到Vue实例上面从而可以使用this.a来直接访问data中的数据.随后调用oberve函数对数据进行观测,这是响应式数据核心

```js
// src/obserber/index.js
class Observer {
  // 观测值
  constructor(value) {
    this.walk(value);
  }
  walk(data) {
    // 对象上的所有属性依次进行观测
    let keys = Object.keys(data);
    for (let i = 0; i < keys.length; i++) {
      let key = keys[i];
      let value = data[key];
      defineReactive(data, key, value);
    }
  }
}
// Object.defineProperty数据劫持核心 兼容性在ie9以及以上
function defineReactive(data, key, value) {
  observe(value); // 递归关键
  // --如果value还是一个对象会继续走一遍odefineReactive 层层遍历一直到value不是对象才停止
  //   思考？如果Vue数据嵌套层级过深 >>性能会受影响
  Object.defineProperty(data, key, {
    get() {
      console.log("获取值");
      return value;
    },
    set(newValue) {
      if (newValue === value) return;
      console.log("设置值");
      value = newValue;
    },
  });
}
export function observe(value) {
  // 如果传过来的是对象或者数组 进行属性劫持
  if (
    Object.prototype.toString.call(value) === "[object Object]" ||
    Array.isArray(value)
  ) {
    return new Observer(value);
  }
}
```

数据劫持核心是 defineReactive 函数 主要使用 Object.defineProperty 来对数据 get 和 set 进行劫持 这里就解决了之前的问题 为啥数据变动了会自动更新视图 我们可以在 set 里面去通知视图更新

### 如何检测数组变化

因为数据劫持需要对每一个元素都进行观测,单数如果一个数组里面有上千上万个元素 每一个元素下标都添加 get 和 set 方法 这样对于性能来说是承担不起的.数组考虑性能原因没有用 defineProperty 对数组的每一项进行拦截，而是选择对 7 种数组（**push,shift,pop,splice,unshift,sort,reverse**）方法进行重写(AOP 切片思想).所以在 Vue 中修改数组的索引和长度是无法监控到的。需要通过以上 7 种变异方法修改数组才会触发数组对应的 watcher 进行更新

## watch源码

首先有一个initState方法判断传入的vm中是否有watch

```js
export function initState(vm) {
  // 获取传入的数据对象
  const opts = vm.$options;
  if (opts.watch) {
    //侦听属性初始化
    initWatch(vm);
  }
}
```

如果传入的vm中有watch的话，则执行initWatch方法进行初始化watch

```js
// 初始化watch
function initWatch(vm) {
  let watch = vm.$options.watch;
  for (let k in watch) {
    const handler = watch[k]; //用户自定义watch的写法可能是数组 对象 函数 字符串
    if (Array.isArray(handler)) {
      // 如果是数组就遍历进行创建
      handler.forEach((handle) => {
        createWatcher(vm, k, handle);
      });
    } else {
      createWatcher(vm, k, handler);
    }
  }
}
// 创建watcher的核心
function createWatcher(vm, exprOrFn, handler, options = {}) {
  if (typeof handler === "object") {
    options = handler; //保存用户传入的对象
    handler = handler.handler; //这个代表真正用户传入的函数
  }
  if (typeof handler === "string") {
    //   代表传入的是定义好的methods方法
    handler = vm[handler];
  }
  //   调用vm.$watch创建用户watcher
  return vm.$watch(exprOrFn, handler, options);
}
```

初始化watch中主要是遍历整个watch中的每一项进行createWatcher；如果其中的某一项的值是数组的话，则遍历数组中的每一项进行createWatcher

createWatcher主要是将handler保存在handler中，将其他选项如immediate、deep保存在options中；其中如果某一项的值是字符串的话，则说明传入的是已经定义好的methods中的方法，则直接将vm中的相应的方法传入handler。之后调用vm的$watch方法传入处理好的参数进行创建用户Watcher

```js
import Watcher from "./observer/watcher";
Vue.prototype.$watch = function (exprOrFn, cb, options) {
  const vm = this;
  //  user: true 这里表示是一个用户watcher
  let watcher = new Watcher(vm, exprOrFn, cb, { ...options, user: true });
  // 如果有immediate属性 代表需要立即执行回调
  if (options.immediate) {
    cb(); //如果立刻执行
  }
};
```

```js
import { isObject } from "../util/index";
export default class Watcher {
  constructor(vm, exprOrFn, cb, options) {
    // this.vm = vm;
    // this.exprOrFn = exprOrFn;
    // this.cb = cb; //回调函数 比如在watcher更新之前可以执行beforeUpdate方法
    // this.options = options; //额外的选项 true代表渲染watcher
    // this.id = id++; // watcher的唯一标识
    // this.deps = []; //存放dep的容器
    // this.depsId = new Set(); //用来去重dep

    this.user = options.user; //标识用户watcher

    // 如果表达式是一个函数
    if (typeof exprOrFn === "function") {
      this.getter = exprOrFn;
    } else {
      this.getter = function () {
        //用户watcher传过来的可能是一个字符串   类似a.a.a.a.b
        let path = exprOrFn.split(".");
        let obj = vm;
        for (let i = 0; i < path.length; i++) {
          obj = obj[path[i]]; //vm.a.a.a.a.b
        }
        return obj;
      };
    }
    // 实例化就进行一次取值操作 进行依赖收集过程
    this.value = this.get();
  }
  //   get() {
  //     pushTarget(this); // 在调用方法之前先把当前watcher实例推到全局Dep.target上
  //     const res = this.getter.call(this.vm); //如果watcher是渲染watcher 那么就相当于执行  vm._update(vm._render()) 这个方法在render函数执行的时候会取值 从而实现依赖收集
  //     popTarget(); // 在调用方法之后把当前watcher实例从全局Dep.target移除
  //     return res;
  //   }
  //   把dep放到deps里面 同时保证同一个dep只被保存到watcher一次  同样的  同一个watcher也只会保存在dep一次
  //   addDep(dep) {
  //     let id = dep.id;
  //     if (!this.depsId.has(id)) {
  //       this.depsId.add(id);
  //       this.deps.push(dep);
  //       //   直接调用dep的addSub方法  把自己--watcher实例添加到dep的subs容器里面
  //       dep.addSub(this);
  //     }
  //   }
  //   这里简单的就执行以下get方法  之后涉及到计算属性就不一样了
  //   update() {
  //     // 计算属性依赖的值发生变化 只需要把dirty置为true  下次访问到了重新计算
  //     if (this.lazy) {
  //       this.dirty = true;
  //     }else{
  //       // 每次watcher进行更新的时候  可以让他们先缓存起来  之后再一起调用
  //       // 异步队列机制
  //       queueWatcher(this);
  //     }
  //   }
  //   depend(){
  //     // 计算属性的watcher存储了依赖项的dep
  //     let i=this.deps.length
  //     while(i--){
  //       this.deps[i].depend() //调用依赖项的dep去收集渲染watcher
  //     }
  //   }
  run() {
    const newVal = this.get(); //新值
    const oldVal = this.value; //老值
    this.value = newVal; //现在的新值将成为下一次变化的老值
    if (this.user) {
      // 如果两次的值不相同  或者值是引用类型 因为引用类型新老值是相等的 他们是指向同一引用地址
      if (newVal !== oldVal || isObject(newVal)) {
        this.cb.call(this.vm, newVal, oldVal);
      }
    } else {
      // 渲染watcher
      this.cb.call(this.vm);
    }
  }
}
```

过程：

- initState(判断vm的$options中有没有watch) 
- initWatch(使用for in 获取watch中的每一个对象键为k 值为handler传入createWatcher函数，如果某个对象为数组的话，则对数组的每一项进行createWatcher) 
- createWatcher主要是将真正的handler保存在handler中，将其他选项如immediate、deep保存在options中；其中如果某一项的值是**字符串**的话，则说明传入的是已经定义好的methods中的方法，则直接将vm中的相应的方法传入handler
- $watch定义在vue的原型上，$watch里进行创建watcher的new Watcher操作，并且判断传入的options中有无immediate属性，如果为true，则在此立即执行一下传入的handler
- new Watcher在构造函数中通过判断键是否是一个方法进行getter的定义，其中如果是一个函数，则getter直接等于这个键，如果不是函数的话，则getter等于一个函数，函数中根据键中的 点 进行分割获取最终层次中的值。并进行一个取值操作，存到value中，value中的值为每次的oldvalue

## 虚拟dom和diff算法

### 虚拟dom

`虚拟DOM`是一个`对象`，一个什么样的对象呢？**一个用来表示真实DOM的对象**

下面是一个真实dom

```html
<ul id="list">
    <li class="item">哈哈</li>
    <li class="item">呵呵</li>
    <li class="item">嘿嘿</li>
</ul>
```

对应的虚拟dom为

```js
let oldVDOM = { // 旧虚拟DOM
        tagName: 'ul', // 标签名
        props: { // 标签属性
            id: 'list'
        },
        children: [ // 标签子节点
            {
                tagName: 'li', props: { class: 'item' }, children: ['哈哈']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['呵呵']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['嘿嘿']
            },
        ]
    }
```

### diff算法

**Diff算法是一种对比算法**。对比两者是旧虚拟DOM和新虚拟DOM，对比出是哪个虚拟节点更改了，找出这个虚拟节点，并只更新这个虚拟节点所对应的真实节点，而不用更新其他数据没发生改变的节点，实现精准地更新真实DOM，进而提高效率。

新旧虚拟DOM对比的时候，Diff算法比较只会在同层级进行, 不会跨层级比较。 所以Diff算法是:**深度优先算法**。 时间复杂度:O(n)

当数据改变时，会触发`setter`，并且通过`Dep.notify`去通知所有`订阅者Watcher`，订阅者们就会调用`patch方法`，给真实DOM打补丁，更新相应的视图。

patch()方法作用就是，对比当前同层的虚拟节点是否为同一种类型的标签

- 是：继续执行`patchVnode方法`进行深层比对
- 否：没必要比对了，直接整个节点替换成`新虚拟节点`

```js
function patch(oldVnode, newVnode) {
  // 比较是否为一个类型的节点
  if (sameVnode(oldVnode, newVnode)) {
    // 是：继续进行深层比较
    patchVnode(oldVnode, newVnode)
  } else {
    // 否
    const oldEl = oldVnode.el // 旧虚拟节点的真实DOM节点
    const parentEle = api.parentNode(oldEl) // 获取父节点
    createEle(newVnode) // 创建新虚拟节点对应的真实DOM节点
    if (parentEle !== null) {
      api.insertBefore(parentEle, vnode.el, api.nextSibling(oEl)) // 将新元素添加进父元素
      api.removeChild(parentEle, oldVnode.el)  // 移除以前的旧元素节点
      // 设置null，释放内存
      oldVnode = null
    }
  }

  return newVnode
}
```

