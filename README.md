# Vue3.0

> Vue3.0是2020年4月刚刚发布了beta版本的全新Vue版本
## 项目地址:
https://github.com/kaiqiangren/vue-next-ts-preview

## 一、Vue3.0与Vue2.0的对比:
### 优点：
1. 将Vue内部的绝大部分api对外暴露，使Vue具备开发大型项目的能力，例如compile编译api等
2. webpack的treeshaking支持度友好
3. 使用Proxy进行响应式变量定义，性能提高2-3倍
4. 可在Vue2.0中单独使用composition-api插件，或者直接用它开发插件
5. 对typescript支持更加友好
### 缺点:
1. 只支持IE11及以上
2. 对于习惯了Vue2.0开发模式的开发者来说，增加了心智负担，对开发者代码组织能力有考验
> 同时也是能力提升的机会吧，特别喜欢Vue作者的设计初心:让开发者随着框架一起成长!

## 二、Vue3.0正确的打开方式
### 1、项目搭建
1. 需要安装vue-cli4代最新脚手架，可以通过执行如下npm 命令安装/更新脚手架版本
```npm
npm i @vue/cli -g
```
2. 然后执行vue add vue-next向项目添加Vue3.0
```npm
vue add vue-next
```
3. 如下例子为使用typescript + Vue3.0 开发的项目依赖，也可以直接使用
```json
{
  "name": "vue3-app-ts",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "dev": "vue-cli-service serve"
  },
  "dependencies": {
    "core-js": "^3.6.4",
    "vue": "^3.0.0-beta.4",
    "vue-router": "^4.0.0-alpha.7",
    "vuex": "^4.0.0-alpha.1"
  },
  "devDependencies": {
    "@typescript-eslint/eslint-plugin": "^2.26.0",
    "@typescript-eslint/parser": "^2.26.0",
    "@vue/cli-plugin-babel": "~4.3.0",
    "@vue/cli-plugin-eslint": "~4.3.0",
    "@vue/cli-plugin-router": "~4.3.0",
    "@vue/cli-plugin-typescript": "~4.3.0",
    "@vue/cli-plugin-vuex": "~4.3.0",
    "@vue/cli-service": "~4.3.0",
    "@vue/compiler-sfc": "^3.0.0-beta.1",
    "@vue/eslint-config-typescript": "^5.0.2",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^7.0.0-alpha.0",
    "node-sass": "^4.12.0",
    "sass-loader": "^8.0.2",
    "typescript": "~3.8.3",
    "vue-cli-plugin-vue-next": "~0.1.2"
  }
}
```

### 2、使用文档
1. 项目入口main.ts
```typescript
import { createApp } from 'vue';
import App from './App.vue'
import router from './router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

### 3、语法相关
1. 响应式变量声明
```js
import { ref, reactive } from 'vue'
// 方式一： 可传入任意类型的值，改变值的时候必须使用其value属性,例 refData.value = 2
const refData = ref(0)

// 方式二： 只能传入引用类型的值
const data = reactive({
  tableData: [
    {
      name: '姓名1'
    }
  ]
})

// 使用响应式变量前，必须在Vue文件的setup函数中 执行/return 出去
setup (props, context){
  return {
    refData,
    data
  }
}
```

2. computed
```js
import { watch, watchEffect, computed } from 'vue'

// 1、创建只读的计算属性
const computedCount = computed(() => count.value + 1)


// 2、创建可读可写的计算属性
const computedCount2 = computed({
      get: () => writeCount.value + 2,
      set: (val) => {
        return writeCount.value =  val + 2
      }
})
// computedCount2 
```

3. watch & watchEffect
```js
import { ref, watch, watchEffect } from 'vue'
const count = ref(0)
// watchEffect会自动收集响应式依赖
watchEffect(() => console.log(count.value))

// 监听指定基础类型数据
watch(count, (now, prev) => {
      console.log(now, prev, 'count')
})

const data = reactive({
  tableData: [
    {
      name: '姓名1'
    }
  ]
})
// 监听reactive创建的响应式变量,可以直接监听对象，必须使用内联函数
watch(() => data.tableData, (now, prev) => {
   console.log(now, prev, 'tableData')
})
```

4. provide & inject
```js
import { reactive, provide , inject} from 'vue'

const data = reactive({
  tableData: [
    {
      name: '姓名1'
    }
  ]
})
// 根级/父级组件
// provide 这里如果提供的是响应式变量，inject也会触发响应
provide('provideName', 'provideData')
provide('provideReactive', data.tableData)


// 子级/孙级组件
setup () {
  const provideData = inject('provideName')
  const provideReactive = inject('provideReactive')
  return {
    provideData,
    provideReactive
  }
}
```

5. 生命周期
```html
Vue3.0生命周期         说明                                                   对应的Vue2.0生命周期
setup               | 初始化数据阶段的生命周期，介于beforeCreate与created之间  相当于beforeCreate、created的合并
onBeforeMount       | 组件挂载前                                          beforeMount
onMounted           | 实例挂载完毕                                         mounted
onBeforeUpdate      | 响应式数据变化前                                      beforeUpdate
onUpdated           | 响应式数据变化完成                                    updated
onBeforeUnmount     | 实例销毁前                                           beforeDestroy
onUnmounted         | 实例已销毁                                           destroyed
onErrorCaptured     | 错误数据捕捉                                            --
```

