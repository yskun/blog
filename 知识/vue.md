# Vue的一些杂碎知识  
## 生命周期钩子
Vue的生命周期要有以下11个钩子：  
1.  beforeCreate
2.  created
3.  beforeMount
4.  mounted
5.  beforeUpdate
6.  updated
7.  activated
8.  deactivated
9.  beforeDestroy
10. destroyed
11. errorCaptured

在Vue的生命周期中，有以下几个结论：
1. 在mounted前的钩子是不能访问组建的DOM元素的，因为组件尚未挂载。因此，没有DOM元素。
2. 在destrory钩子中，dom已经被销毁了。所以，在这个周期中，已经无法访问组件的DOM元素了。
3. activated \ deactivated 钩子只会在“keep-alive”组件中，组件被激活或停用时被引用。
4. beforeUpdate \ updated 分别在虚拟DOM向DOM“打补丁”的前后被调用。换言之， beforeUpdate时期的dom还是旧的dom而在updated时期对应的已经是新的DOM了。
5. errorCaptured 可以捕获自身和子组件产生的错误，如果，你不希望继续让错误继续传播，你可以返回false，使其停止传播。
6. nextTick 不是Vue的生命周期，在调用nextTick后，他将会在下一次DOM更新循环完毕后，执行一次。因为，它会在“下一次DOM更新周期”完毕后执行，所以，在created钩子中，可以使用nextTick进行DOM操作（此刻DOM已被挂载）

## v-model
v-model 是一个双向绑定指令。  
在组件中，可以使用“model”配置“prop”,“event”，然后使用v-model在父组件中进行绑定。实际上，model属性就是一组“prop”和“emit”的组合。  
在表单中，它可以负责与原生的“input”、“select”、“textarea”进行数据交互。  
要注意的是：
> v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。

以及，要注意的是，v-model不能获得input类型为file的文件。在Vue的文档中是这么说的：
> v-model 在内部使用不同的属性为不同的输入元素并抛出不同的事件：  
text 和 textarea 元素使用 value 属性和 input 事件；  
checkbox 和 radio 使用 checked 属性和 change 事件；  
select 字段将 value 作为 prop 并将 change 作为事件。  

但在```input[type=file]```中，实际文件是存放“files”属性中的。因此，需要使用```@change="func($event)"```获取相应的数值。

## Vue的核心思想  
数据驱动和组件化。(网上抄的，但这确实是现代主流框架通用的核心思想)  
1. 数据驱动  
通过数据驱动视图层的变化。  
通过数据的变化对视图层进行操作是很多新框架区分过去框架的不同之处。对于很多新人而言，在编码的过程中，往往会通过复杂的对DOM直接经行操作，来实现需要的特性。然而，在多数的情况下，通过操作数据，即可实现需要的特性。而通过改变数据经行操作，可以使视图和业务逻辑解耦。从而降低代码的逻辑复杂度，减少代码逻辑产生的错误。

2. 组件化
组件可以被复用，同时更易于项目的管理。

## Vue是如何实现数据绑定的

## Vue与Dom间的交互（虚拟DOM）

## Vue与其他框架的区别

## keep-alive的实现原理

## Vue的单向数据流

## 
