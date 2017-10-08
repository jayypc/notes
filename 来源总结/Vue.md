## Vue.js

### 实例

```javascript
var vm = new Vue({
  el:"#vm",
  //data里面的属性被代理，所以是响应的，实时更新
  //Vue实例暴露了一些有用的实例属性与方法,使用$以区别代理属性,比如vm.a(代理) vm.$data  vm.$el  vm.$watch()
  data: {
    a: 1,
    message: 'Hello',
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  //实例生命周期
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  },
  //过滤器
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  },
  //计算属性（区别method:带缓存,针对响应式依赖,既对data里面数据有效）
  computed: {
      // a computed getter
      reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  },
  //方法
  methods: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  },
  //watch属性
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})

//使用过滤器
{{ message | capitalize }} 
message作为第一个参数传入
{{ message | filterA('arg1', arg2) }}
字符串 'arg1' 将传给过滤器作为第二个参数， arg2 表达式的值将被求值然后传给过滤器作为第三个参数。

<div v-bind:id="rawId | capitalize"></div>

//使用计算属性(与方法区别:没有括号)
<p>Computed reversed message: "{{ reversedMessage }}"</p>

//使用方法
<p>Reversed message: "{{ reversedMessage() }}"</p>

//无需调用watch,当firstName或者lastName的值发生改变的时候就会调用方法，其中val为新变化的值
上面的watch可以使用计算属性来替换
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
其中还可增加setter方法
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
可直接调用vm.fullName = 'John Doe'来使用setter方法
```
### 模版

> Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。

```javascript
//“Mustache” 语法（双大括号）的文本插值
<span>Message: {{ msg }}</span>
<span v-once>This will never change: {{ msg }}</span>
//纯HTML
<div v-html="rawHtml"></div>
//属性
<div v-bind:id="dynamicId"></div>
<button v-bind:disabled="someDynamicCondition">Button</button>
//使用javascript表达式
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

### 绑定样式

- class

  - 对象语法

  ```html
  <div v-bind:class="{ active: isActive }"></div>

  <div class="static"
       v-bind:class="{ active: isActive, 'text-danger': hasError }">
  </div>

  <div v-bind:class="classObject"></div>
  ```

  ```javascript
  data: {
    classObject: {
      active: true,
      'text-danger': false
    }
  }

  data: {
    isActive: true,
    error: null
  },
  computed: {
    classObject: function () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal',
      }
    }
  }
  ```

  - 数组语法

  ```html
  <div v-bind:class="[activeClass, errorClass]"></div>

  data: {
    activeClass: 'active',
    errorClass: 'text-danger'
  }

  <div v-bind:class="[isActive ? activeClass : '', errorClass]">
  ```

  - 用在组件上

  ```html
  Vue.component('my-component', {
    template: '<p class="foo bar">Hi</p>'
  })

  <my-component class="baz boo"></my-component>
  ```

- 内联样式

  - 对象语法

  ```html
  <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
  ```

  ```javascript
  data: {
    activeColor: 'red',
    fontSize: 30
  }
  ```

  - 数组语法

  ```html
  <div v-bind:style="[baseStyles, overridingStyles]">
  ```

  - 自动添加前缀

### 组件

> 组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 js 特性扩展。

- 全局组件

```javascript
<div id="example">
  <my-component></my-component>
</div>

// 注册一个全局组件(必须在实例之前注册)
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// 创建根实例
new Vue({
  el: '#example'
})
```

- 局部组件

```javascript
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> 将只在父模板可用
    'my-component': Child
  }
})
```

- 自定义事件
  - 使用 $on(eventName) 监听事件
  - 使用 $emit(eventName) 触发事件
- ​