---
title: vue组件间通信
date: 2019-07-23 14:15:43
tags:
  - vue.js
categories:
  - 笔记
---
> 说实话，对于vue自己知道的是少之又少的皮毛，很多东西都是现查拿来用，感觉组件间的通信有点绕，来记录一下

<!--more-->

## &ensp;一、组件通信之父子通信

### （1）子组件使用props选项接收来自父组件的数据

  ***注：这是一种单向绑定的方式，只能父组件给子组件传递***

  ```html
  <!-- 父组件 -->
  <template>
    <div>
      <child v-bind:message="msg"></child>
    </div>
  </template>
  <script>
  import child from './components/child.vue'
  export default{
    name: 'father',
    components:{
      child
    },
    data(){
      return{
        msg:'hello child'
      }
    }
  }
  </script>

  <!-- 子组件 -->
  <template>
    父组件说：{{ message }}
  </template>
  <script>
  export default{
    name: 'child',
    props: ['message']
  }
  </script>
  ```
  
### （2）通过 ref=' ' 将子组件的实例指给 $ref

  ***父组件通过 $ref 拿到子组件的实例，从而调用子组件的方法***

  ```html
  <!-- 父组件 -->
  <template>
    <div>
      <button v-on:click="$refs.msg.sayHello('hello child')">调用子组件的sayHello方法</button>
      <child ref="msg"></child>
    </div>
  </template>
  <script>
  import child from './components/child.vue'
  export default{
    name: 'father',
    components:{
      child
    }
  }
  </script>
  
  <!-- 子组件 -->
  <template>
    父组件说：{{ message }}
  </template>
  <script>
  export default{
    name: 'child',
    methods: {
      sayHello (m) {
        this.message = m
      } 
    },
  }
  </script>
  ```

## &ensp;二、组件通信之子父通信

### &ensp;通过 $emit 实现子组件向父组件通信

***子组件通过 $emit 绑定一个自定义事件event，父组件通过 @event 来监听这个事件并接收参数***

```html
<!-- 父组件 -->
<template>
  <div>
    <child @returnHello="showMsg(value)"></child>
  </div>
</template>
<script>
import child from './components/child.vue'
export default{
  name: 'father',
  components:{
    child
  },
  methods: {
    showMsg (value) {
      console.log('子组件传回：', value)
    }
  },
}
</script>

<!-- 子组件 -->
<template>
  <button v-on:click="onClick()">触发自定义事件</button>
</template>
<script>
export default{
  name: 'child',
  methods: {
    onClick () {
      this.$emit('returnHello','hello father')
    } 
  },
}
</script>
```

## &ensp;三、任意及平行组件间的通信

### &ensp;通过事件调度器 Event 来实现

***首先，需要新建一个Vue实例作为组件之间通信的中间人***

```javascript
<!-- mainVue.js -->
import Vue from 'vue'
export default new Vue()
```

***然后，在两个组件中使用通信中间人 Event***

```html
<!-- 组件一 -->
<template>
  <button v-on:click="onClick()">给组件二传信</button>
</template>
<script>
import Event from '../mainVue.js'
export default{
  name: '组件一',
  methods: {
    onClick () {
      Event.$emit('ToTheSecondComponnet','hello！')
    } 
  },
}
</script>

<!-- 组件二 -->
<template>
  组件一传来信息：{{ message }}
</template>
<script>
import Event from '../mainVue.js'
export default{
  name: '组件一',
  data() {
    return {
      message: ''
    }
  },
  methods: {
    onClick () {
      Event.$on('ToTheSecondComponnet', function(value) {
        this.message = value
      })
    } 
  },
}
</script>
```

本文参考：
[vue之父子组件间通信实例讲解(props、$ref、$emit)](https://www.jianshu.com/p/91416e11f012)
[Laravel Vue.js 视频教程](https://www.codecasts.com/)
