---
title: vue基础 - 指令
date: 2018-03-27 19:38:20
tags: [vue, mvvm]
categories: [vue]
---

>   本文只是对官网的总结以及一些个人理解，方便自己查阅

### # 参数

>   直白一点，:  后面的就是指令的参数，参数就决定了指令的功能范围。
    ``` html
        <a v-bind:href="url">...</a>
        <a v-on:click="can">...</a>
        <!-- 其中'href', 'click' 就是参数 -->
    ```
### # 修饰符

>   以  .  指明的特殊后缀，用来执行一些特殊操作。
    ``` html
        <button @click.stop.prevent="can"></button>
    ```
### # 缩写

>   Vue.js 为 v-bind 和 v-on 这两个最常用的指令，提供了特定简写。
    ``` html
        <!-- 缩写 -->
        <a :href="url">...</a>
        <button @click.stop.prevent="can"></button>
    ```
### # 常用指令
1. v-text 和 v-html
      两者用法一样都是直接绑定在元素上面，v-text是纯文本，类似“Mustache”语法（双大括号），但v-text会替换掉元素内所有子节点；v-html支持html语法，会更新元素的 innerHTML。
    ``` html
        <!-- 
            msg: 'I can do it' ,
            html: '<span style="color: red">我是红色</span>'
        -->
        <p>{{msg}}</p>
        <p v-text="msg"></p>
        <p v-html="msg"></p>
        <!-- 渲染结果都是：I can do it -->
        <p>{{msg}}</p>
        <p v-text="html"></p>
        <p v-html="html"></p>
        <!-- 
            前面两个为：<span style="color: red">我是红色</span>
            v-html结果是：我是红色
        -->
    ```
2. v-if 和 v-show
      都是通过Boolean值（可以是表达式）来切换元素的显示隐藏。

      不同之处在于，v-if 会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。如果初始值为false，元素将不会被渲染，直到条件变为真值。

      而 v-show不管初始值为何，都会被渲染，而后只是简单的切换css属性（dispaly）。

      所以v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。

      因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。或者某些情况也可以v-if与v-show一起使用。

    v-if也可以与v-else、v-else-if结合使用。
    ``` html
        <div v-if="Math.random() > 0.5">
        </div>
        <div v-else>
        </div>
    ```
3. v-for
      循环渲染元素或模板，数据驱动页面，为当前遍历的元素提供别名。
    ``` html
        <!-- 数组，指定item为当前遍历元素，index为索引 -->
        <div v-for="(item, index) in items"></div>
        <!-- 也可以用of替代in -->
        <div v-for="item of items"></div>
        <!-- 对象，可以指定键值以及索引的别名 -->
        <div v-for="(value, key, index) in object">
            {{ index }}. {{ key }}: {{ value }}
        </div>
    ```
      在v-for中，key值是必须的。 在Vue 的虚拟 DOM 算法里，在新旧 nodes 对比时辨识 VNodes。

      个人理解为就是通过唯一的key值的变化，来进行相应的数据比对，而后进行渲染。key值就相当于元素唯一的id一样，所以有相同父元素的子元素必须有独特的 key，重复的 key 会造成渲染错误。   
    ``` html
        <div v-for="item in items" :key="item.id">
        </div>
    ```
4. v-on
      用来绑定事件，事件类型由参数决定，也可以跟事件修饰符（@click.stop.prevent）,缩写为 *@*。表达式可以是一个方法的名字或一个内联语句,可以监听原生 DOM 事件，也可以监听子组件触发的自定义事件。
    ``` html
        <!-- 
            事件修饰符
            *  .stop - 调用 event.stopPropagation()。
            *  .prevent - 调用 event.preventDefault()。
            *  .capture - 添加事件侦听器时使用 capture 模式。
            *  .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
            *  .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
            *  .native - 监听组件根元素的原生事件。
            *  .once - 只触发一次回调。
            *  .left - (2.2.0) 只当点击鼠标左键时触发。
            *  .right - (2.2.0) 只当点击鼠标右键时触发。
            *  .middle - (2.2.0) 只当点击鼠标中键时触发。
            *  .passive - (2.3.0) 以 { passive: true } 模式添加侦听器
        -->
        <button v-on:click.once="can"></button>
        <!--  串联修饰符 -->
        <button @click.stop.prevent="can"></button>
        <!-- 对象语法 (2.4.0+) -->
        <button v-on="{ mousedown: down, mouseup: up }"></button>

        <!-- 组件中的原生事件 -->
        <my-component @click.native="onClick"></my-component>
    ```
5. v-bind
      动态地绑定一个或多个prop数据、class、style或者其他dom属性，缩写为 *:*。在绑定 class 或 style 特性时，支持数组或对象，用来绑定多个。
    ``` html
        <!-- 缩写 -->
        <img :src="url">
        <!-- 内联字符串拼接 -->
        <img :src="'/000000' + url">
        <!-- class 绑定 -->
        <div :class="[classA, { classB: isActive, classC: !isActive}]">

        <!-- 通过 $props 将父组件的 props 一起传给子组件 -->
        <child-component v-bind="$props"></child-component>
    ```
6. v-model
      数据的双向绑定，简单来说，输入的值和v-model绑定的值是双向的，即始终相同。
    
      但我们都知道v-model其实是一个语法糖，通过v-bind绑定value和input事件实现（直接用在input元素上时会随表单控件类型不同而不同）。
    
      input事件为H5新增，输入框内容变化时触发，类似于onchange。
    ``` html
        <input v-model="val" />
        <input :value="val" @input="val = $event.target.value" />
        <!-- v-model 等价于 第二种写法 -->
    ```
      v-model 不仅仅能在 input上用，在输入组件上也能使用。这时的input事件需要通过vue自定义事件发送出来（$emit）。
    ``` html
        <!-- 组件调用 -->
        <test-input v-model="val"></test-input>
        <!-- 组件内部 -->
        <input
            type="text"
            :value="value"
            @input="$emit('input', $event.target.value)"
        >
        <!-- props指定 -->
        props: ['value']
    ```
      因为v-model默认是value和input的组合，所以如果是创建单选或者多选框 *组件* 时，需要用到组件的 *model* 选项。
    ``` html
        <!-- 组件调用 -->
        <test-input v-model="val"></test-input>
        <!-- 组件内部 -->
        <input
            type="checkbox"
            :checked="checked"
            @change="$emit('change', $event.target.checked)"
        >
        <!-- props、model指定 -->
        props: ['checked'],
        model: {
            prop: 'checked',
            event: 'change'
        }
    ```