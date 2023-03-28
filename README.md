## 后端程序员必须学会的前端知识

这里主要记录一下vue3的一些核心知识点
还有HTML，CSS，JS再说

### 1、选项式API和组合式API

**选项式API**

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

