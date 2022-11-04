---
title: Vue（1）
copyright: false
date: 2022-09-10 13:41:43
tags: 
- Vue
categories: 
- frontend
- Vue
---
{% note info %}
b站教程视频：[Vue实战](https://www.bilibili.com/video/BV1Zy4y1K7SH?p=138&spm_id_from=pageDriver&vd_source=1379429d9fdc59cc6f51bb675c08e3ba)
学习笔记：[Vue学习笔记](https://www.yuque.com/cessstudy/kak11d/hfeef2)
{% endnote %}
<!-- more -->
# 构建Vue项目
## vue cli构建
###　全局安装@vue/cli。
```sh
npm install -g @vue/cli
```
### 切换到你要创建项目的目录，然后使用命令创建项目
```sh
vue create xxxx
```
### 启动项目
```sh
npm run serve
```
{% note %}
如出现下载缓慢请配置 npm 淘宝镜像：`npm config set registry
https://registry.npm.taobao.org`
{% endnote %}

## Vite构建
### 创建工程
```sh
npm init vite-app <project name>
```
### 进入工程目录
```sh
cd <project name>
```
### 安装依赖
```sh
npm install
```
### 运行
```sh
npm run dev
```

# setup使用
　　定义数据、函数并返回
```js
setup(){
    let name="pjt"
    let age=23

    function sayhello(){
      alert(`我叫${name},我${age}岁了`)
    }

    return{
      name,
      age,
      sayhello
    }
    // return ()=> h('h1','pjt')
  }
```

# ref和reactive方法
```js
import { ref,reactive } from 'vue'

export default {
  name: 'App',
  setup(){
    let name=ref("潘江涛") //使用ref方法将普通数据变为响应式
    let age=ref(23)
    let job=reactive({ //使用reactive方法将数组、对象、函数类型数据变为响应式
      type : '学生',
      salary: 1000
    })
    

    function changeinfo(){
      age.value = age.value+1 //取出ref数据，需要.value
      job.type = '社会人',
      job.salary = job.salary +10000 //取出reactive数据，不需要.value
    }

    return{
      name,
      age,
      job,
      changeinfo
    }
  }  
}
```

# computed方法
　　fullname属性将根据firstname和lastname来计算
```js
person.fullname=computed({
  get(){
    return person.firstname+person.lastname
  },
  set(value){
    person.firstname=value[0]
    person.lastname=value.slice(1)
  }
  
})
```

# 监视函数watch和watchEffect
　　watch只能监视浅层数据，不能监视对象或数组内部数据的变化，而watchEffect能监视所有定义的变量
```js
watch(()=>person.firstname,(newvalue,oldvalue)=>{
  console.log('姓变了',newvalue,oldvalue)
})

watchEffect(()=>{
  const a=person.firstname
  console.log("person.firstname数据发生变化")
})
```

# hooks
　　将模块化js代码封装为hook组件，使用时直接import使用,且定义hook函数时使用下述代码包裹
```js
export default function (){
}
```

# 动画效果
## 进场/退场效果
1. template中使用<transition>标签包裹动画内容
2. style中使用v-enter-active，v-leave-active类描述进场、退场动画
```css
<style>
    .v-enter-active{
        animation: pjt 3s;
    }
    .v-leave-active{
        animation: pjt 3s reverse;
    }
    @keyframes pjt {
        from{
            transform: translateX(-2000px);
        }
        to{
            transform: translateX(0px);
        }
    }
</style>
```
　　或者使用过渡效果
```js
<style scoped>
  h1{
      background-color: orange;
  }
  .v-enter-active,.v-leavactive{ //动画效果
      transition: 0.5s linear;
  } 
  .v-enter,.v-enter-to,{ //进入的起点、退出的终点
      transform: translateX(-100%); 
  }
  .v-enter-to.v-leave{ //进入的终点、退出的起点
      transform: translateX(0);
  }
</style>>
```
{% note %}
* 如出现多个不同动画效果，可给<transition>标记不同的class名，动画类名也改为classname-enter-active和classname-leave-active
* 包含多个框体可以使用<transition-group>标签包裹
{% endnote %}

## 集成第三方库
　　`<script>`标签中引入第三方库
```js
import 'animate.css'
```
　　`<transition>`标签加上属性`name`及操作属性
```js
  <transition-group 
    name="animate__animated animate__bounce" 
    enter-active-class="animate__rubberBand"
    leave-active-class="animate__backOutUp"
    appear
  >
```