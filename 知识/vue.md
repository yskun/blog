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
v-model 是一个用来“双向绑定”的指令（Directive）。  
通过使用v-model，它似乎可以实现双向数据流。然而，在Vue中数据流动都是单向的，v-model也不例外。它实际上只是一个语法糖，是一个prop、event的结合体。在组件中，可以通过“model”属性定制“prop”与“event”，然后使用v-model在父组件中进行绑定，实现“双向绑定”。  
在表单元素“input”、“select”、“textarea”中，可以通过v-model进行数据交互。  
要注意的是在与原生组件交互的时候，v-model存在一些限制。
> v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。

此外，表单元素```input[type=file]```不能通过v-model获得用户输入的文件。因为用户输入的文件，存储在files的属性中，可以通过change事件，获取相应的数值。  
> v-model 在内部使用不同的属性为不同的输入元素并抛出不同的事件：  
text 和 textarea 元素使用 value 属性和 input 事件；  
checkbox 和 radio 使用 checked 属性和 change 事件；  
select 字段将 value 作为 prop 并将 change 作为事件。  

## Vue的核心思想  
数据驱动和组件化。（其实，其他现代前端框架大多都是遵循这两个思想的） 
1. 数据驱动  
DOM是数据的一种自然映射，通过操作数据驱动视图层变化。  
数据驱动是区分很多新式框架与旧框架的重要特点。DOM与数据进行绑定，开发者在多数的时候仅需对数据进行操作，而无需手动对DOM直接进行操作。数据驱动的意义在于视图层与业务逻辑解耦。开发者无需关注视图层变化，降低代码的逻辑复杂度，减少代码逻辑产生的错误。

2. 组件化
组件可以被复用，同时更易于项目的管理。  

## Vue是如何实现数据绑定的
在Vue 2中，数据绑定是通过Object.defineProperty实现的，在初始化Vue实例时，vue根据data，生成observe实例，并依次为其属性“defineReactive”(设定get，set)。当数据“set”时，将会“notify”通知各个“subscriber”订阅者(如：watcher)。从而实现数据的响应。  
Dep\Observe\watcher

在Vue 3中，则通过es6对象Proxy实现数据监视机制。主要的区别在于Object.defineProperty是对其现有属性进行处理的。而Proxy则是代理整个对象。通过代理对象，实现对对象的监查，无需对各个属性进行处理，从而提高性能。

## Vue与DOM间的交互（虚拟DOM）
Vue进行DOM操作是通过一层Virtual DOM进行操作的，他的存在意义不在于“操作VDOM比DOM快”。而是，将对DOM的操作集中在一次中，并通过diff算法，最小化需要进行处理的DOM队列。从而减少浏览器reflow及repaint的操作。  
此外，VDOM还能在运行在不存在DOM的环境中。    

## Vue与其他框架的区别

## keep-alive的实现原理

## Vue的单向数据流

## 
