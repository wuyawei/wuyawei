---
title: vue基础 - 数据
date: 2018-03-15 18:42:46
tags: [vue, mvvm]
categories: [vue]
---

>   本文只是对官网的总结以及一些个人理解，方便自己查阅

### # data

1.   data对象定义必须通过工厂函数返回一个对象（避免所有实例引用同一个对象）。

2.   通过 this.a 或者 this.$data.a 访问。

3.   尽量在创建实例之前，就声明所有的根级响应式属性。（Vue 不能检测到对象属性的添加或删除。），如果没有在 data 中事先声明而直接使用，Vue 会警告访问的属性不存在。
``` javascript
    var vm = new Vue({
        data:{
            a:1,
            obj: {}
        }
    })

    // `vm.a` 是响应的

    vm.b = 2
    // `vm.b` 是非响应的
```

4.   data 属性使用箭头函数，this 不会指向这个组件的实例，但是可以通过函数的第一个参数来访问。
``` javascript
    data: th => ({ a: th.myProp })
```

5.   可以使用 Vue.set(object, key, value) 方法将响应属性添加到已有的对象上。
``` javascript
    Vue.set(vm.obj, 'b', 2);
```
    或者：
``` javascript
    /* data:{
            a:1,
            obj: {'b': '1'}
        };*/
    this.$set(this.obj,'b',2); // 改变已有属性，不会触发watch, 需加上deep属性（详见watch）。
    this.$set(this.obj,'d',2); // 新增属性，可直接触发watch。
```
    亦或：
``` javascript
    // 通过创建一个新的对象，来触发响应式变化，watch检测的时候很有用。
    this.obj = Object.assign({}, this.obj, { a: 1, b: 2 }); // 改变了引用地址，触发watch。
```

### # props
> props 可以是简单的数组，或者使用对象作为替代，对象允许配置高级选项，如类型检测、自定义校验和设置默认值。

1.   普通用法
``` javascript
    // 简单语法
    props: ['info', 'data'];
```
2.   推荐用法
``` javascript
    props: {
        age: {
            type: Number, // 指定类型，String、Number、Boolean、Function、Object、Array、Symbol
            default: 0 // type为对象或数组时，default需要用工厂函数返回
        }
    }
```
3.   自定义验证
``` javascript
    propF: {
      validator: function (val) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'info'].indexOf(val) !== -1
      }
    }
```
4.   验证构造函数
``` javascript
    /*
        function Person (name) {
            this.name = name;
            this.talk = function() {
                alert(name);
            }
        }
    */
    props: {
        pepole: Person // 来验证 pepole 的值是否是通过 new Person 创建的。
    }

```
5.  props 的数据传递是单向的，父级的数据更新会传递给子级，反之则不行。也不允许在子组件中直接改变props的传值。
    
      这样可以防止从子组件意外改变父级组件的状态，否则导致你的应用的数据流向难以理解。组件中推荐的数据流导向，父 ——> 子：porps；子 ——> 父 ：事件（$emit）;

    但是对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。
    
    也就是说允许改变prop传入对象的某个属性，或者数组中的某个元素，但不允许更改引用类型的地址。*非引用类型*，不允许改变。
``` javascript
    // list: ['a', 'b', c'];
    props: ['list'],
    computed: {
        clist() {
            this.list.pop(); // 允许
            this.list = ['a', 'b']; // 不允许
            return this.list;
        }
    }
```
6. prop 会在一个组件实例创建之前进行验证，所以实例的属性 (如 data、computed 等) 在 default 或 validator 函数中是不可用的。

      （即，在 default 或 validator 函数中，不能用data、computed中的数据）。

### # computed
> 计算属性，用以定义一些需要逻辑处理得到的数据，计算属性的结果会被缓存，除非依赖的响应式属性变化才会重新计算。

1. 计算属性使用了箭头函数，则 this 不会指向这个组件的实例，但是可以将其实例作为函数的第一个参数来访问，同data；
``` javascript
    computed: {
        aDouble: th => th.a * 2
    }
```
2. 直接以this.now访问或者now()函数调用。二者区别是，前者只有在响应式依赖改变时才会更新，而方法调用每次都会重新执行。
``` javascript
    // Date.now() 不是响应式依赖，所以缓存后不会触发更新。此时就应选择now()访问;
    computed: {
        now: function () {
            return Date.now()
        }
    }
```
3. 计算属性默认只有getter，但是也可以通过setter来设置。
``` javascript
    computed: {
        date: {
            get: function () {
                return Date.now();
            },
            set: function (date) {
                this.customDate = new Date(date).getTime();
            }
        }
    }
    // this.date = '2018-01-01';
    // this.customDate // => 1514764800000
```

4. 计算属性与侦听属性在某些情况很相似，然而当你有一些数据需要随着其它数据变动而变动时，有时候计算属性表现更为优秀，而不是滥用watch。
``` javascript
    data(
        return {
            a: 0,
            b: 1,
            totalY: 0
        }
    ),
    computed: {
        total() {
            return this.a + this.b;
        }
    },
    watch: { // 显然，计算属性要更简洁
        a(val) {
            this.totalY = val + this.b;
        },
        b(val) {
            this.totalY = val + this.a;
        }
    }
```

### # watch
> 侦听属性，侦听数据变化，用以在某些数据改变时，进行相应操作。

1.   基础用法，第一个参数是改变后的值，第二个是改变前的值；
```  javascript
    a: function (val, oldVal) {
      console.log(this, val);
    }
    b(val, oldVal) {
      console.log(this, val);
    }
    c: (val, oldVal) => {
      console.log(this, val);
    }
    // b的写法不过是a的语法糖，两者是一样的。但是c用到了箭头函数，此时的this，已经不是vue实例了。
    // 所以不应该使用箭头函数来定义watch函数，methods跟这个情况一样，也不能使用箭头函数。
```
2.   写一个方法名，检测到相应值变化时会直接调用方法；
``` javascript
    b() {
        this.myMethod();
    }
    b: 'myMethod'
    // 二者一样
```
3.   deep，深度watch，设为 true 可以检测到对象属性的变化（注：obj.a = 'new'，这种赋值方式依然不响应；适用于$set）；
``` javascript
    c: {
        handler: function (val, oldVal) { console.log(val, oldVal) },
        deep: true
    }
```
4.   immediate，设为 true 将会在侦听开始之后被立即调用（即，会在初始化时调用一次）；
``` javascript
    d: {
        handler: function (val, oldVal) { console.log(val, oldVal); },
        immediate: true
    }
```
5.   多个检测函数；
``` javascript
    e: [
        function handle1 (val, oldVal) { console.log(val, oldVal); },
        function handle2 (val, oldVal) { console.log(val, oldVal); }
    ]
```
6.   直接检测对象某个属性的值变化；
``` javascript
    'obj.a': function (val, oldVal) { console.log(val, oldVal); }
```