---
title: Vue3.2 setup语法糖总结
slug: Vue3-to-2-Summary-of-setup-syntax
description: 在 Vue3.2 中只需要在 script 标签上加上 setup 属性，无需 return，template 便可直接使用，非常的香啊！
date: 2022-05-19 06:47:26
copyright: Reprinted
author: 玲小叮当
originaltitle: Vue3.2 setup语法糖总结
originallink: https://segmentfault.com/a/1190000041849882
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_48.jpg
categories: 前端
tags: Vue
---

## 前言

`提示：Vue3.2 版本开始才能使用语法糖！`

在 `Vue3.0` 中变量必须 `return` 出来，`template`中才能使用；而在 `Vue3.2` 中只需要在 `script` 标签上加上 `setup` 属性，无需 `return`，`template` 便可直接使用，非常的香啊！

`提示：以下是本篇文章正文内容，下面案例可供参考`

## 一、如何使用setup语法糖

只需在 `script` 标签上写上`setup`

代码如下（示例）：

```ts
<template>
</template>
<script setup>
</script>
<style scoped lang="less">
</style>
```

## 二、data数据的使用

由于 `setup` 不需写 `return`，所以直接声明数据即可

代码如下（示例）：

```ts
<script setup>
    import {
      ref,
      reactive,
      toRefs,
    } from 'vue'
    
    const data = reactive({
      patternVisible: false,
      debugVisible: false,
      aboutExeVisible: false,
    })
    
    const content = ref('content')
    //使用toRefs解构
    const { patternVisible, debugVisible, aboutExeVisible } = toRefs(data)
</script>
```

## 三、method方法的使用

代码如下（示例）：

```ts
<template >
    <button @click="onClickHelp">系统帮助</button>
</template>
<script setup>
import {reactive} from 'vue'

const data = reactive({
      aboutExeVisible: false,
})
// 点击帮助
const onClickHelp = () => {
    console.log(`系统帮助`)
    data.aboutExeVisible = true
}
</script>
```

## 四、watchEffect的使用

代码如下（示例）：

```ts
<script setup>
import {
  ref,
  watchEffect,
} from 'vue'

let sum = ref(0)

watchEffect(()=>{
  const x1 = sum.value
  console.log('watchEffect所指定的回调执行了')
})
</script>
```

## 五、watch的使用

代码如下（示例）：

```ts
<script setup>
    import {
      reactive,
      watch,
    } from 'vue'
     //数据
     let sum = ref(0)
     let msg = ref('你好啊')
     let person = reactive({
                    name:'张三',
                    age:18,
                    job:{
                      j1:{
                        salary:20
                      }
                    }
                  })
    // 两种监听格式
    watch([sum,msg],(newValue,oldValue)=>{
            console.log('sum或msg变了',newValue,oldValue)
          },{immediate:true})
          
     watch(()=>person.job,(newValue,oldValue)=>{
        console.log('person的job变化了',newValue,oldValue)
     },{deep:true}) 
 
</script>
```

## 六、computed计算属性的使用

computed计算属性有两种写法(简写和考虑读写的完整写法)

代码如下（示例）：

```ts
<script setup>
    import {
      reactive,
      computed,
    } from 'vue'

    //数据
    let person = reactive({
       firstName:'小',
       lastName:'叮当'
     })
    // 计算属性简写
    person.fullName = computed(()=>{
        return person.firstName + '-' + person.lastName
      }) 
    // 完整写法
    person.fullName = computed({
      get(){
        return person.firstName + '-' + person.lastName
      },
      set(value){
        const nameArr = value.split('-')
        person.firstName = nameArr[0]
        person.lastName = nameArr[1]
      }
    })
</script>
```

## 七、props父子传值的使用

子组件代码如下（示例）：

```ts
<template>
  <span>{{props.name}}</span>
</template>

<script setup>
  import { defineProps } from 'vue'
  // 声明props
  const props = defineProps({
    name: {
      type: String,
      default: '11'
    }
  })  
  // 或者
  //const props = defineProps(['name'])
</script>
```

父组件代码如下（示例）：

```ts
<template>
  <child :name='name'/>  
</template>

<script setup>
    import {ref} from 'vue'
    // 引入子组件
    import child from './child.vue'
    let name= ref('小叮当')
</script>
```

## 八、emit子父传值的使用

子组件代码如下（示例）：

```ts
<template>
   <a-button @click="isOk">
     确定
   </a-button>
</template>
<script setup>
import { defineEmits } from 'vue';

// emit
const emit = defineEmits(['aboutExeVisible'])
/**
 * 方法
 */
// 点击确定按钮
const isOk = () => {
  emit('aboutExeVisible');
}
</script>
```

父组件代码如下（示例）：

```ts
<template>
  <AdoutExe @aboutExeVisible="aboutExeHandleCancel" />
</template>
<script setup>
import {reactive} from 'vue'
// 导入子组件
import AdoutExe from '../components/AdoutExeCom'

const data = reactive({
  aboutExeVisible: false, 
})
// content组件ref


// 关于系统隐藏
const aboutExeHandleCancel = () => {
  data.aboutExeVisible = false
}

</script>
```

## 九、获取子组件ref变量和defineExpose暴露

即vue2中的获取子组件的ref，直接在父组件中控制子组件方法和变量的方法

子组件代码如下（示例）：

```ts
<template>
    <p>{{data }}</p>
</template>

<script setup>
import {
  reactive,
  toRefs
} from 'vue'

/**
 * 数据部分
 * */
const data = reactive({
  modelVisible: false,
  historyVisible: false, 
  reportVisible: false, 
})
defineExpose({
  ...toRefs(data),
})
</script>
```

父组件代码如下（示例）：

```ts
<template>
    <button @click="onClickSetUp">点击</button>
    <Content ref="content" />
</template>

<script setup>
import {ref} from 'vue'

// content组件ref
const content = ref('content')
// 点击设置
const onClickSetUp = ({ key }) => {
   content.value.modelVisible = true
}

</script>
<style scoped lang="less">
</style>
```

## 十、路由useRoute和useRouter的使用

代码如下（示例）：

```ts
<script setup>
  import { useRoute, useRouter } from 'vue-router'
    
  // 声明
  const route = useRoute()
  const router = useRouter()
    
  // 获取query
  console.log(route.query)
  // 获取params
  console.log(route.params)

  // 路由跳转
  router.push({
      path: `/index`
  })
</script>
```

## 十一、store仓库的使用

代码如下（示例）：

```ts
<script setup>
  import { useStore } from 'vuex'
  import { num } from '../store/index'

  const store = useStore(num)
    
  // 获取Vuex的state
  console.log(store.state.number)
  // 获取Vuex的getters
  console.log(store.state.getNumber)
  
  // 提交mutations
  store.commit('fnName')
  
  // 分发actions的方法
  store.dispatch('fnName')
</script>
```

## 十二、await 的支持

setup 语法糖中可直接使用 await，不需要写 async ， setup 会自动变成 async setup

代码如下（示例）：

```ts
<script setup>
  import Api from '../api/Api'
  const data = await Api.getData()
  console.log(data)
</script>
```

## 十三、provide 和 inject 祖孙传值

父组件代码如下（示例）：

```ts
<template>
  <AdoutExe />
</template>

<script setup>
  import { ref,provide } from 'vue'
  import AdoutExe from '@/components/AdoutExeCom'

  let name = ref('Jerry')
  // 使用provide
  provide('provideState', {
    name,
    changeName: () => {
      name.value = '小叮当'
    }
  })
</script>
```

子组件代码如下（示例）：

```ts
<script setup>
  import { inject } from 'vue'
  const provideState = inject('provideState')

  provideState.changeName()
</script>
```

## 总结

提示：这里对文章进行总结：

例如：以上就是我自己找博客什么的学习setup语法糖进行的总结，在项目中都进行了使用是可以实现的，如果哪里我写错了或者我的方法使用不对大家告诉我，我会马上改哒！如果大家觉得我写的文章还可以很实用，欢迎大家点赞收藏关注呀关注博主不迷路哦！工作不忙的话会持续更新哒！欢迎大家看看小叮当的其它文章~