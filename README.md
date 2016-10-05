# Vue.js笔记

标签（空格分隔）： Ｖｕｅｊｓ

---


## 数据绑定语法

### 插值
---

{{ msg }} : 文本
{{* msg }} : 单词插值
{{{ msg }}} : 已html字符串解析
<div id="item_{{ id }}"> : attr里面插值

### 绑定表达式
---
支持所有的表达式!不是语句!
EX:
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
像是下面你的这些语句是无效的:
EX:
{{ var a = 1 }}
{{ if (ok) {return message} }}

表达式后面可以用管道符号加上过滤器(Filter)
EX: {{ message | capitalize }}

还可以加上参数

###　指令
---
指令是特殊的带有前缀｀V-｀．指令的值只能是表达式，可以包含过滤器．指令的作用是当其表达式的值改变的时候把一些特殊的行为应用到DOM上

指令的参数
v-bind:href="url"  => href="{{ url }}"

v-on:click="doSomething"

指令的修饰符
用于表示指令应当以特殊的方式绑定

指令的缩写

v-bind:href => :href
v-on:click => @click

### 计算属性
---
```
<div id="example">
  a={{ a }}, b={{ b }}
</div>
```

```
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    // 一个计算属性的 getter
    b: function () {
      // `this` 指向 vm 实例
      return this.a + 1
    }
  }
})
```
b就是一个计算属性,提供的函数将用作属性的getter,而且vm.b的值始终取决于vm.a
Vue之道b依赖于a.

计算属性 VS $watch

计算Setter
```js
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```
可以使用vm.firstname = 'BOb';vm.lastname = 'Frank' => getter
也可以使用vm.Fullname = 'Bob Frank' => setter

### Class与Style绑定

数据绑定的一个常见需求是操作元素的class列表和它的内敛样式.所以Vue.js专门增强了v-bind:class和v-bind:style

绑定HTML Class
对象语法:
v-bind:class="{ 'class-a' : isA, 'class-b' : isB }"
data : {
    isA : true,
    isB : false
}
isA和isB的变化,class会相应的变化

也可以直接绑定一个对象:
v-bind:class="classObject"
data : {
    classObject : {
        isA : true,
        isB : false
    }
}

数组语法:
v-bind:class="[classA, classB]"
data : {
    classA : 'class_a',
    classB : 'class_b'
}
 
 也可以对象数组混用
 v-bind:class="[classA, {classB : isB, classC : isC}]"
 要是想classB和isB都是变量,那就用数组的方法
 
 ### 绑定内联样式
 
 v-bind:style="{ color : activeColor, font-size : fontSize+ 'px' }"
 data {
    activeColor : 'red',
    fontSize : '13'
}
当然data可以直接是对象
可以结合计算属性使用

数组语法
v-bind:style="{styleObjectA, styleObjectB}"
data {
    styleObjectA : {
    },
    styleObjectB : {
    }
}

自动添加前缀,本来是想加上比如大家记忆中的需要前缀的比如transform,box-shadow,box-radius等,但是 去can i use 发现这些现在各大西安浏览器都已经兼容,于是去找了一个比较冷门的text-fill-color : transparent;

## 条件渲染

v-if
<h1 v-if="ok">yes</h1>

<h1 v-if="ok">yes</h1>
<h1 v-else>no</h1>

template-if 

上面的v-if只能添加一个语句
要是想添加若干个语句到if里面
<template v-if="ok">
    <h1>title1<h1>
    <p>title2</p>
</template>

渲染出的结果没有template标签

v-show
v-show="ok"
v-show元素会始终渲染并保持在DOM中,v-show是简单的切换元素的css属性display

displayhe visiblity前者是显示并不占位置,后者是占位置,但是他们不管怎么样都会存在与html文档中

v-else
可以给v-if和v-show添加添加一个else模块
必须跟在if和show后面,不然无法识别

切换v-if的时候会有一个局部编译/卸载的过程,v-if中的模板可能包含数据绑定或者子模块,v-show全部都会渲染只在乎节点的display属性的切换.如果页面渲染之后,数据没有频繁的切换就用v-if,反之就用v-show


## 列表渲染

v-for

v-for="item in items"
v-for指令基于一个数组的渲染列表,items是数据数组,item是当前数组元素的别名

$index是当前数组元素的索引

指定索引的别名
v-for="(index, item) in items"
这样就是指定index为索引的别名,item为数组元素的别名
"item of items"这样使用of的语法也是可以的

template-for
<template v-for="item in items">
    <li>{{ item.message }}</li>
    <li class="ds"></li>
</template>
类比于template-if

数组变动检测
原始的javascript数组的方法不能触发Vuejs视图的更新
vuejs重新包装了数组的方法,所以他们能触发师徒的更新

包装的方法里面有变异方法,改变了原来的数组,也有非变异方法,不改变原来的数组,返回一个新数组,filter(),concat(),slice().使用新数组绑定上去,vuejs使用一些启发算法,高效的复用DOM元素

track-by 可以理解为“唯一标识”，代表新渲染的元素和原本的元素之间存在关联。如果没有 track-by，会导致 demo 中 CSS 的 transition 属性无效，那是因为 Vue 在渲染新 DOM 节点时会删除旧的 DOM 节点（这也就是为什么控制台中的 ul 一直在闪烁）。当使用 track-by 将新旧元素建立关联后，Vue 就仅会针对元素内的一些值渲染，而不会重新渲染整个元素，故合理使用 track-by 是可以提升性能的。

因为javascript的限制,有下面两个问题
1. vm.items[0] = {}
2. vm.items.length = 0

上面两个不能实时更新到view上.
所以vuejs添加了
vm.items.$set(0, { message : 'xx' })
vm.items.$remove(item)

对象
v-for="value in object" {{$key}} : {{ value }}
data : {
    object : {
        Firstname : 'weite',
        Lastname : 'chen',
        age : 20
    }
}

==>

 Firstname : 'weite'
Lastname : 'chen'
age : 20

也可以这样使用别名
v-for="(key, value) in object" {{ key }} : {{ value }}
遍历的结果是Object.keys()的顺序

值域v-for
v-for="n in 10" {{n}} ==> 0 1 2 3 4 5 6 7 8 9
就是当前模板重复的次数

对于遍历的数组,既想排序又不想改变原来的数组,可以使用计算属性,也可以使用过滤器`filterBy`和`orderBy`

## 方法与事件处理器

v-on:click="greet"
v-on:click="say('hi')"

v-on:click="say('hello', $event)"
methods : {
    say : function(msg, event){
        //原生事件
        event.preventDefault();
    }
}

event.target 指向触发该事件的元素
event.currentTarget 识别事件的当前目标对象

事件修饰符

<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat">

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件侦听器时使用 capture 模式 在捕获阶段 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
<div v-on:click.self="doThat">...</div>

按键修饰符

<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
<!-- 同上 按键别名 -->
<input v-on:keyup.enter="submit">

## 表单控件绑定

text
{{ message }} input[v-model="message"]
multiline text
{{ message }} textarea[v-model="message"]
checkbox复选框
input type="checkbox" v-model="checkNames" *3
{{ checkNames | json }}
data : {
    el : '',
    data : {
        checkNames : []
    }
}
radio
input type="radio" v-model="picked" *3
{{ picked }}
select
select v-model="selected" option *3
{{ selected }}

v-model 与 v-for联合使用

如果 单选框,复选框,选择框v-model得出来的值是自己指定的
checkbox
input type="checkbox" v-bind:true-vlaue="a" v-bind:false-vlaue="b"
radio
v-bind:value
select options
option v-bind:value="{ number : 123 }"

过渡


## 组件

注册
创建:var MyComponent = Vue.extend({....});
注册:Vue.component('my-component', MyComponent);
使用:<div id="demo"><my-component></component></div>

定义 > 注册 > 创建根实例
// 定义
var MyComponent = Vue.extend({
  template: '<div>A custom component!</div>'
})

// 注册
Vue.component('my-component', MyComponent)

// 创建根实例
new Vue({
  el: '#example'
})

局部注册
在定义里面使用components注册,只能在Child这个组件里面使用
var Child = Vue.extend({ /* ... */ })

var Parent = Vue.extend({
  template: '...',
  components: {
    // <my-component> 只能用在父组件模板内
    'my-component': Child
  }
})

组件选项
特别说了两个选项: data, el
这两个如果是直接把外部的对象引用进来,那么接下来每一个实例都会指向这个对象,那会导致,一个实例改变,其他的实例都会改变.所以应该是这样的写法

var MyComponet - Vue.component({
    data : function (){
        //返回一个新的实例
        return { a : 1 };
    } 
})

模板解析

extand的template选项里面推荐是使用DOM模板,但是有一些限制:
+ a 不能包含其它的交互元素（如按钮，链接）
+ ul 和 ol 只能直接包含 li
+ select 只能包含 option 和 optgroup
+ table 只能直接包含 thead, tbody, tfoot, tr, caption, col, colgroup
+ tr 只能直接包含 th 和 td

但是在实际应用的过程中,会有自定义组件包裹或者被包裹在这些标签内,浏览器会自动把他们提取到外面.

解决方法: is特性
table>tr[is="my-component"]

props
div[msg="1"] 和 div[v-bind:msg="1"] 的区别
前者只是传递了一个字符串,后者是传递是一个变量

:msg.sync 数据的双向绑定
:msg.once 单词绑定

prop验证

slot 内容分发网络

## 深入响应式原理

在vuejs官方文档中看到：

因为 JavaScript 的限制，Vue.js 不能检测到下面数组变化：

直接用索引设置元素，如 vm.items[0] = {}；
修改数据的长度，如 vm.items.length = 0
据说vuejs是遍历对象的所有属性和方法加上钩子，为什么无法检测到上面这两种变化。

解答 : 

Vue是靠setter,getter这两个访问器属性,来检测数据的变化,并实时更新DOM
数组的变动就是给予上面的两个方法.Vue.js必须通过Object.defineProperty这儿方法才能修改这两个es底层的的东西,而IE8是第一个实现这个方法的!所以不兼容IE8一下.
Vue.js为了检测push等一系列数组方法对于数组数据的影响,都会重新封装一边,加上数据的相应.
但是直接对于数组数据索引的操作vm.item[0] = '1',或者vm.length = 0,这样的Vue.js根本没法给你做一个Object.defineproperty这样的操作,自然没有setter,getter.





























 










