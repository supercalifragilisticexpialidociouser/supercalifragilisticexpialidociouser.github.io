---
title: Vue
date: 2020-02-04 16:07:36
tags: [2.6.11]
---

# 安装

## 直接用`<script>`引入

开发环境：

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

生产环境：

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
```

这种安装方式，可以直接用浏览器打开页面浏览，而无需任何构建或无需启动任何Web服务器。

## 使用NPM安装

```bash
$ npm install vue
```

## 使用Vue CLI工具

# 入门

```html
<!DOCTYPE html>
<html>
<head>
	<title>Hello Vue</title>
</head>
<body>
  <div id="app">
    {{ message }}
  </div>
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	<script type="text/javascript">
		var app = new Vue({
			el: '#app',
			data: {
				message: 'Hello Vue!'
			}
		});
	</script>
</body>
</html>
</body>
```

每个 Vue 应用都是通过用 `Vue` 函数创建一个新的 **根Vue 实例**开始的。`Vue`函数接受一个**选项对象**，用以定义Vue实例。

现在，可以在浏览器上直接打开上面的HTML页面，就能看到效果了。

模板中，用双花括号包围的是插值，它建立了数据到DOM的绑定。在该页面打开浏览器的 JavaScript 控制台，并输入 `app.message = '你好'` ，你将看到上例HTML相应地更新了。

> `data`对象的属性可以直接通过Vue实例来访问，例如：`app.message`。
>
> 另外，Vue 实例还暴露了一些有用的实例属性与方法，它们都有前缀 `$`：
>
> ```javascript
> var data = { a: 1 }
> var vm = new Vue({
>   el: '#example',
>   data: data
> })
> 
> vm.$data === data // => true
> vm.$el === document.getElementById('example') // => true
> 
> // $watch 是一个实例方法
> vm.$watch('a', function (newValue, oldValue) {
>   // 这个回调将在 `vm.a` 改变后调用
> })
> ```

# 组件

任意类型的应用界面都可以抽象为一个组件树。在 Vue 里，所有的组件本质上都是一个拥有预定义选项的一个 Vue 实例，并且接受相同的选项对象 (一些根实例特有的选项除外)。一个 Vue 应用由一个通过 `new Vue` 创建的**根 Vue 实例**，以及可选的嵌套的、可复用的组件树组成。

## 注册组件

## 生命周期

![Vue 实例生命周期](Vue/lifecycle.png)

用户可以利用生命周期钩子函数，来在不同阶段添加自己的代码。例如， [`created`](https://cn.vuejs.org/v2/api/#created) 钩子可以用来在一个实例被创建之后执行代码：

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

> 不要在选项属性或回调上使用[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

# 模板

## 插值

数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值：

```html
<span>Message: {{ msg }}</span>
```

Mustache 标签将会被替代为对应数据对象上 `msg` 属性的值。无论何时，绑定的数据对象上 `msg` 属性发生了改变，插值处的内容都会更新。



### 一次性插值

通过使用 [v-once 指令](https://cn.vuejs.org/v2/api/#v-once)，只会在首次渲染时执行一次插值。此后数据改变时，插值处的内容不会更新。

```html
<span v-once>这个将不会改变: {{ msg }}</span>
```

## 指令

指令 (Directives) 是带有 `v-` 前缀的特殊 attribute。

### 参数

一些指令能够接收一个“参数”，在指令名称之后以冒号表示。例如：

```html
<a v-bind:href="url">...</a>
<a v-on:click="doSomething">...</a>
```

#### 动态参数

从 2.6.0 开始，可以用方括号括起来的模板表达式作为一个指令的参数：

```html
<a v-bind:[attributeName]="url"> ... </a>
<a v-on:[eventName]="doSomething"> ... </a>
```

动态参数的值应该是一个字符串或`null`，任何其它类型的值都将会触发一个警告。`null`值用于显式地移除绑定。

由于，HTML attribute 名中不允许出现某些字符，如空格和引号。因此，

```html
<!-- 这会触发一个编译警告 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

变通的办法是使用没有空格或引号的表达式，或用计算属性替代这种复杂表达式。

在HTML模板中，还需要避免使用大写字符来命名动态参数，因为浏览器会把 attribute 名全部强制转为小写：

```html
<!--
在HTML模板中，这段代码会被转换为 `v-bind:[someattr]`。
除非在实例中有一个名为“someattr”的 property，否则代码不会工作。
-->
<a v-bind:[someAttr]="value"> ... </a>
```

### 修饰符

指令的参数之后，还可带若干个修饰符 (modifier)。修饰符以`.`开头， 用于指出一个指令应该以特殊方式绑定。

例如，`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`：

```html
<form v-on:submit.prevent="onSubmit">...</form>
```



## 模板表达式

在插值和指令中使用的表达式称为模板表达式。

模板表达式只能是单个JavaScript表达式（`v-for` 是个例外，它的值不是JavaScript表达式），所以下面的例子都**不会**生效。

```html
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```

模板表达式中可以直接访问选项对象里`data`、`method`、`computed`等选项中定义的属性和方法。

另外，在模板表达式中只能访问[全局变量的一个白名单](https://github.com/vuejs/vue/blob/v2.6.10/src/core/instance/proxy.js#L9)，如 `Math` 和 `Date` 。你不应该在模板表达式中试图访问用户定义的全局变量。

## 输出HTML

插值会将数据解释为普通文本，而非 HTML 代码。为了将数据输出为HTML，需要使用`v-html`指令：

```html
<p>Using mustaches: {{ html }}</p>
<p>Using v-html directive: <span v-html="html"></span></p>
```

输出：

![v-html](Vue/v-html.png)

> **绝不要**对用户提供的内容使用`v-html`指令，它很容易导致 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。

## Attribute

Mustache 语法不能作用在 HTML attribute 上，遇到这种情况应该使用 `v-bind` 指令。

指令`v-bind`可将数据与HTML元素的attribute进行绑定。

```html
<div id="app">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
<script>
	var app = new Vue({
    el: '#app',
    data: {
      message: '页面加载于 ' + new Date().toLocaleString()
    }
	});
</script>
```

注意：只有当Vue实例被创建时就已经存在于 `data` 中的属性才是**响应式**的（属性发生改变时，将触发相关视图进行更新，以匹配新的值，即视图将会产生“响应”）。也就是说，如果你在Vue实例创建后，添加一个新的属性，那么对该属性的改动将不会触发任何视图的更新。

使用 `Object.freeze()`，这会阻止修改现有的属性，也意味着响应系统无法再*追踪*变化。

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
<script>
  var obj = {
    foo: 'bar'
  };

  Object.freeze(obj);

  new Vue({
    el: '#app',
    data: obj
  });
</script>
```

Vue为`v-bind`指令提供了简写形式：

```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```

### 布尔attribute

对于布尔 attribute (即只要存在就意味着值为 `true`)，`v-bind` 工作起来略有不同：

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

如果 `isButtonDisabled` 的值是 `null`、`undefined` 或 `false`，则 `disabled` attribute 甚至不会被包含在渲染出来的 `<button>` 元素中。

# 计算属性

模板表达式应当保持简单，应将复杂逻辑放入计算属性中。

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
<script>
  var vm = new Vue({
    el: '#example',
    data: {
      message: 'Hello'
    },
    computed: {
      // 计算属性的 getter
      reversedMessage: function () {
        // `this` 指向 vm 实例
        return this.message.split('').reverse().join('');
      }
    }
  });
</script>
```

这里我们声明了一个计算属性 `reversedMessage`。我们提供的函数将用作属性 `vm.reversedMessage` 的 getter 函数。

你可以像绑定普通属性一样在模板中绑定计算属性。Vue 知道 `vm.reversedMessage` 依赖于 `vm.message`，因此当 `vm.message` 发生改变时，所有依赖 `vm.reversedMessage` 的绑定也会更新。

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：

```javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
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
});
```

现在再运行 `vm.fullName = 'John Doe'` 时，setter 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。

## 计算属性vs方法

你可能已经注意到我们可以通过在表达式中调用方法来达到同样的效果：

```html
<p>Reversed message: "{{ reversedMessage() }}"</p>
<script>
  var vm = new Vue({
    el: '#example',
    data: {
      message: 'Hello'
    },
    methods: {
      reversedMessage: function () {
        // `this` 指向 vm 实例
        return this.message.split('').reverse().join('');
      }
    }
  });
</script>
```

然而，不同的是**计算属性是基于它们的响应式依赖进行缓存的**。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 `message` 还没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。

这也同样意味着下面的计算属性将不再更新，因为 `Date.now()` 不是响应式依赖：

```javascript
computed: {
  now: function () {
    return Date.now()
  }
}
```

相比之下，每当触发重新渲染时，调用方法将**总会**再次执行函数。

## 计算属性vs侦听属性

命令式的`watch`回调很容易被滥用。

```html
<div id="demo">{{ fullName }}</div>
<script>
  var vm = new Vue({
    el: '#demo',
    data: {
      firstName: 'Foo',
      lastName: 'Bar',
      fullName: 'Foo Bar'
    },
    watch: {
      firstName: function (val) {
        this.fullName = val + ' ' + this.lastName;
      },
      lastName: function (val) {
        this.fullName = this.firstName + ' ' + val;
      }
    }
  });
</script>
```

上面代码是命令式且重复的，下面计算属性的版本明显好得多了：

```javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName;
    }
  }
});
```

# 侦听器

虽然计算属性在大多数情况下更合适，但侦听器提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。例如：

```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

在这个示例中，使用 `watch` 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前设置中间状态。这些都是计算属性无法做到的。

除了 `watch` 选项之外，您还可以使用命令式的 [vm.$watch API](https://cn.vuejs.org/v2/api/#vm-watch)。

# 条件渲染

# 列表渲染



# 事件处理

可以用 `v-on` 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法：



Vue为`v-on`指令提供了简写形式：

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>
```



# 表单

# 样式

## class绑定

## style绑定

