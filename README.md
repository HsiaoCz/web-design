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

```javascript
function ref(value){
    const refObject={
        get value(){
            track(refObject,'value')
            return value
        },
        set  value(newValue){
            trigger(refObject,'value')
            value=newValue
        }
    }
    return refObject
}
```

向上面我们如果不使用对象，而是使用一个字符串，就需要使用ref来声明一个响应式变量

**setup语法**

```html
<script>

// 以库的形式来使用vue实例提供的API

import {reactive} from 'vue';

export  default{
    //setup是一个专门用于组合式API的特殊钩子
    setup(){
        //暴露数据到模板
        return {}
    }
}

</script>
```

我们可以通过使用构建工具来简化该操作，当使用单文件组件时(SFC)时，我们可以使用<script setup>来简化大量模板代码

```html
<script setup>

// 以库的形式来使用vue实例提供的API

import {ref} from "vue";

// 使用ref来为基础类型构建响应式变量

const name=ref("zhangsan");

// 通过value来设置基础类型的值(setter方式)

name.value="里斯";

</script>
```

大多数vue的开发者在开发应用时都会基于单文件组件+<script setup>的语法方式，这也是我们后面常见的写法

**DOM异步更新**

注意这是一个重要的概念，当你响应式数据发生变化时，DOM也会自动更新，但是这个更新并不是同步的，而是异步的，vue会将你的变更放到一个缓冲队列，等待更新周期到达时，一次完成DOM的更新

vue在跟踪到响应式数据发生变化的时候，并不是同步更新DOM的
,当下次更新周期到来时，会reflsh一下dom,刷新之后会调一下nextTick,表明dom已经刷新，如果需要获取数据，那么在这里设置钩子函数获取
```html
<script setup>
import {onMounted}  from 'vue';

// 使用ref来为基础类型构造响应式变量

let name= $ref("zhangsan");

// 只有等模板挂载好之后，我们才能获取到对应的HTML元素

onMounted()=>{
    // 通过value来设置 基础类型的值(setter)
    name="lisi";
    console.log(document.getElementById('name').innerText);
};
</script>
```

由于修改name后，没有立即更新dom，所以获取到的name依然是初始值，那如果想要获取到当前值怎么办?

vue在dom更新时，为我们提供了一个钩子函数nextTick()

该钩子就是当vue实例到达更新周期后，完成dom后，留给我们操作的口子，因此我们改造下

```html
<script setup>
import {nextTick,onMounted}  from 'vue';

// 使用ref来为基础类型构造响应式变量

let name= $ref("zhangsan");

// 只有等模板挂载好之后，我们才能获取到对应的HTML元素

onMounted()=>{
    // 通过value来设置 基础类型的值(setter)
    name="lisi";
    // 等待vue下次更新到来后，执行下面的操作

    nextTick(()=>{
        console.log(document.getElementById('name').innerText);
    });
};
</script>
```

**深层响应性**

通过上面的我们知道reactive初始化的Proxy对象是响应式的，那如果我在这个对象里面再嵌套对象，那嵌套的对象还是不是响应式的?

依然是响应式的
当动态的更新reactive初始化的对象的时候，也能动态的响应
这就是深层响应性

大部分时候，这种深层响应性都是我们所期望的

但是当一个对象很大的时候，嵌套很复杂的时候，这种深层的响应模式可能引发一些性能问题，这个时候我们可以使用ve提供的shallowReactive创建一个浅层响应式的数据

```javascript
// 使用shallowReactive构造浅层响应式的数据，当数据有变化时，不会立即反馈到界面上

let person=shallowReactive({
    name:'zhangsan',
    profile:{city:"北京"},
    skills:["Golang","vue"]
})
```

**ref vs reactive**

reactive能构造的，ref都能构造

使用ref的时候，需要注意使用value

那么一个问题是，ref这个函数是如何替换reactive的呢？
答案很简单，当传递过来一个参数的时候，ref这个函数会判断它是简单类型还是复合类型
如果是复合类型，ref会调用reactive将其转化成一个深层响应式的proxy对象


由于proxy是一个对象，它的响应式是与该对象绑定，如果一个对象一旦被解开了，而对象的熟悉本身又不具备响应式，响应式就中断了，而使用ref就不会

ref语法糖

上面我们提到了定义setup函数的繁琐问题，我们接下来说说value的繁琐问题，并且在没有类型系统的帮助时很容易漏掉

既然模板里面ref编译器都能处理自动补充上value,那么在setup的js里面编译器能不能帮忙补充下

vue的响应性语法糖为我们提供了编译时的转换过程，在setup语法内，我们使用$+名称来引用编译时的宏命令

宏命令不是一个真实的，在运行时会调用的方法，而是作用于Vue编译器的标记，表明最终的count变量需要是一个响应式变量，编译器自动帮我们补充上value的语法

编译器支持的宏命令有
- ref ---> $ref
- computed--->$computed
- shallowRef--$shallowRef
- customRef---->$customRef
- toRef--->$toRef

### 5、组件

组件允许我们将UI划分为独立的，可重用的部分来思考，组件在应用程序中常常被组织成层层嵌套的树状结构

这和我们嵌套的HTML元素的方式类似，VUE实现了自己的组件数据模型，是我们可以在每个组件内封装自定义内容与逻辑

**组件基础**

工作原理

我们一般将一个vue组件定义在一个单独的.vue文件中，这被叫做单文件组件，它是一种独特的文件格式，使我们能够将一个Vue组件的模板、逻辑与样式封装在单个文件中

```vue
<!-- 逻辑处理部分，有javascript提供能力 -->
<script setup>

</script>

<!-- 视图模板，由html和自定义组件或者web组件提供能力 -->
<template>
<p>{{name}}</p>

<!-- 元素样式，由css提供能力 -->

<style>

</style>
```

很显然，该文件并不是一个合法的html文件，并不能被浏览器直接加载，它是一种特殊的文件格式，定义该文件的语法被叫做SFC语法

该语法需要被转换成标准的HTML文件才能被浏览器加载 @vue/compiler-sfc就是用于干这个事情的，当然它不是独立工作的
.vite提供了构建功能,vue以插件的形式提供sfc语法转换逻辑

一个编译后的sfc是一个标准的javascript模块，这也意味着通过设当的构建配置，你可以像导入其他ES模块一样导入SFC


**定义组件**

我们分别定义:
- script:js逻辑
- template：视图模板
- style：样式

```html
<script setup>

import { ref } from "vue";

const count=ref(0);
</script>

<template>
<button @onclick="count++"> you clicked me {{count}} times </button>

<style scoped>
</style>
</template>
```
scoped表示这个样式只对当前组件有效

**使用组件**

可以使用import来导入组件

```html
<!-- 通过<script setup>导入的组件都在模板中直接使用 -->

import ButtonCounter from "@/components/ButtonCounter.vue";
```

**组件注册**

像上面那样直接使用时局部导入
也可以全局注册一个组件，使得他在当前应用中的任何组件上都可以使用，不需要额外导入

```vue
import MyCompount from './App.vue'

app.component("MyComponent",MyComponent)
```

**侦听器**

一个简单的需求：我们一个页面有多个参数，用户可能把url copy给别人，我们需要不同的url看到的页面不同，不然用户每次看到这个页面都是第一个页面

这个就需要我们监听url参数的变化，然后视图做调整，vue-router会有一个全局属性:$route.我们可以监听他的变化

