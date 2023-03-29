## 后端程序员必须学会的前端知识

这里主要记录一下vue3的一些核心知识点
还有HTML，CSS，JS再说

### 1、选项式API和组合式API

**选项式API**

选项式API以组件实例的概念为中心，即上述实例中的this

```html
<template>
<div class="box" @click="changeMsg">{{msg}}</div>

</template>

<script>

// vue2的写法，选项式的API
export default{
    name:'app',
    data(){
        return {
            msg:'hello my man'
        }
    },
    methods:{
        changeMsg(){
            this.msg='hello Vue3'
        }
    }
}
</script>

<style>

</style>
```

**组合式API**

组合式API的核心思想是直接在函数作用域内定义响应式状态变量，并将多个函数中得到的状态组合起来处理
复杂问题，这种形式更加自由

```html
<template>
<div class="box" @click="changeMsg">{{msg}}</div>

</template>

<script>
// 组合式API

import {ref,defineComponent} from 'vue';

export default defineComponent({
    setup(){
        const msg=ref('hello world');
        const changeMsg=()=>{
            msg.value='hello vue3'
        };
        return {
            msg,
            changeMsg
        }
    }
})

</script>

<style>

</style>
```

选项式API和组合式API的区别

选项式API是将数据和方法分开进行管理，而组合式API是将所有的内容放在一起(类似js的原生开发)

setup语法糖则可以让变量方法不再写return，后面的组件甚至是自定义的指令也可以在我们的template中自动获得

### 2、MVVM与vue

MVC: 
- M: Model:代表数据状态

- V: view代表视图

- C：controller 用于处理数据和视图之间的交互逻辑

MVC 是一中程序的结构

MVVM:

- M:model 模型层，主要负责业务数据相关，对应vue的data部分

- V:view:视图层，负责视图相关，细分下来就是html+css部分，对应vue的模板部分

- VM：视图控制，V和M沟通的桥梁，负责监听M或者V的修改，是实现mvvm双向绑定的要点

在出现MVVM之前，出现过这样几个时代

**1、CGI时代**

最早的html是完全静态的，它们是预先编写好的html文件，当浏览器请求某个wURL的时候，将对应的html文件扔给浏览器就行了

如果需要针对不同的用户显示不同的内容，就使用后端语言直接输出拼接好的内容

利用拼接字符串的形式显示

**2、后端模板时代**

显然拼接字符串并不好，由于拼接的内容大部分都是html标签元素，所以出现了模板。这里就进入了后端模板时代

最典型的就是ASP,JSP,PHP

**3、js原生时代**

很显然使用后端模板需要每次数据变动都要向后端发起请求，效率是低下的

并且浏览器无法自己修改页面的内容

当js出现以后，浏览器可以运行js，然后对页面做修改

```html
<p id="userInfo">
姓名:<span id="name">Gloria</span>
性别:<span id="sex">男</span>
职业:<span id="job">前端工程师</span>
</p>
```

以上的html片段，想将姓名替换成alice，我们的做法

```javascript
doucment.getElementById('name').innerHTML=alice.name
doucment.getElementById('sex').innerHTML=alice.name
doucment.getElementById('job').innerHTML=alice.name
```

JQuery在这个时代脱颖而出

在这种情况下，前后端算是分开了

**3、前端模板时代**

在架构上，前端走上了后端的老路：模板系统，有引擎就是viewModel动态渲染

```html
<p id="userInfo">
姓名:<span id="name">Gloria</span>
性别:<span id="sex">男</span>
职业:<span id="job">前端工程师</span>
</p>

var userInfo=document.getElementById('userInfo');
var userInfoTemplate=document.getElementById('userInfoTemplate').innerHTML

userInfo.innerHTML=templateEngine.rander(userInfoTamplate.users.alice)

```

**4、虚拟dom**

由于js操作Dom,浏览器会从构建dom树开始从头到尾执行一遍流程，操作DOM的代价还是很高，频繁操作会导致出现页面卡顿，影响用户体验

如何才能对Dom树做局部更新，而不是全局更新呢？
答案就是js动态生成这个数，修改时动态更新，这就是虚拟dom

只有用户有操作的时候，将动态生成的dom全部挂载到dom树上面，这种叫虚拟dom

**5、组件化时代**

组件化时代如何解决这种dom的自动生成，把数据和视图分开，当数据有变化的时候，就更新视图，当视图有变化的时候，就更新数据

model是js对象,view负责显示

这里也结合了虚拟dom技术，我们可以动态的生成view，在集合mvvm的思想，前端终于迎来了组件化时代

- 页面由多个组件构成

- 每个组件都有自己的MVVM

一个网站可能有多个区域，每个区域都是由一个vue组件构成
一个网页就是一个组件树

### 3、vue实例

组合式api里面怎么获取到vue的实例

可以通过getCurrentInstance获取当前vue的实例


### 4、响应式原理

响应式指的就是model(数据)有变化的时候，能反馈到view上,当然这个反馈是由view实例来帮我们完成的，那
view怎么知道model有变化

答案之一是:js Proxy,其行为表现与一般对象相似，不同之处在于Vue能够跟踪对响应式对象property
的访问与更改操作

当修改proxy的时候，它会调用一个set方法，修改proxy属性并不会立即去更新，而是先调用这个set方法
这个set方法里我们可以定义一些自己的操作

vue通过这个proxy来封装响应机制，这样就能追踪属性的变化

vue提供的一个构造函数用于初始化原始对象，这样vue才能跟踪数据的变化
vue提供一个reactive函数

```javascript
function reactive(obj){
    return new Proxy(obj,{
        get(target,key){
            track(target,key)
            return target[key]
        },

        set(target,key,value){
            trigger(target,key)
            target[key]=value
        }
    })
}
```

ref用于生成一个响应式变量
而reactive它可以将一个对象变成响应式的对象

```javascript
let persion=reactive({name:"zhangsan"});
```

**getter/setters**

Proxy仅对对象类型有效(对象,数组和map、Set这样的集合操作)
而对string、number和bool这样的原始类型无效

为了解决这样的限制，vue提供了一个ref()方法来允许我们创建可以使用任何值类型的响应式ref
ref利用的是js的getter/setters的方式劫持属性访问
