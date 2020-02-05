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

## v-if

`v-if` 指令用于条件性地渲染一块内容，每次切换过程中条件块内的事件监听器和子组件都会适当地被销毁和重建。

`v-if` 也是**惰性的**，如果在初始渲染时条件为假，则什么也不做（不在DOM中），直到条件第一次变为真（ truthy 值）时，才会开始渲染条件块并保留在DOM中。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

也可以用 `v-else` 添加一个“else 块”：

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

从2.1.0开始，还可用`v-else-if`添加一个“else-if块”：

```html
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else-if="type === 'C'">C</div>
<div v-else>Not A/B/C</div>
```

`v-else`，`v-else-if` 必须紧跟在带 `v-if` 或者 `v-else-if` 的元素之后，否则它们将不会被识别。

## v-show

`v-show`指令会按条件显示或隐藏元素，但带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS 属性 `display`。

```html
<h1 v-show="ok">Hello!</h1>
```

> 注意，`v-show` 不支持 `<template>` 元素，也不支持 `v-else`。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

# 列表渲染

## 迭代数组

```html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
<script>
  var example1 = new Vue({
    el: '#example-1',
    data: {
      items: [
        { message: 'Foo' },
        { message: 'Bar' }
      ]
    }
  })
</script>
```

`v-for` 指令需要使用 `item in items` 形式的特殊语法，其中 `items` 是源数据数组，而 `item` 则是被迭代的数组元素的**别名**。

在 `v-for` 块中，我们可以访问所有父作用域的属性。`v-for` 还支持一个可选的第二个参数，即当前项的索引。

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
  var example2 = new Vue({
    el: '#example-2',
    data: {
      parentMessage: 'Parent',
      items: [
        { message: 'Foo' },
        { message: 'Bar' }
      ]
    }
  })
</script>
```

你也可以用 `of` 替代 `in` 作为分隔符，因为它更接近 JavaScript 迭代器的语法：

```html
<div v-for="item of items">…</div>
```

### 数组更新检测

Vue会检测到如下方法对数组做的修改，从而会触发视图更新：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Vue也会检测到对数组的替换，并触发视图更新。

由于 JavaScript 的限制，Vue **不能**检测以下数组的变动：

1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
2. 当你修改数组的长度时，例如：`vm.items.length = newLength`

举个例子：

```javascript
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的
```

为了解决第一类问题，以下两种方式都可以实现和 `vm.items[indexOfItem] = newValue` 相同的效果，同时也将在响应式系统内触发状态更新：

```javascript
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

你也可以使用 `vm.$set` 实例方法，该方法是全局方法 `Vue.set` 的一个别名：

```javascript
vm.$set(vm.items, indexOfItem, newValue)
```

为了解决第二类问题，你可以使用 `splice`：

```javascript
vm.items.splice(newLength)
```

## 迭代对象

你也可以用 `v-for` 来遍历一个对象的属性。

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
  new Vue({
    el: '#v-for-object',
    data: {
      object: {
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10'
      }
    }
  })
</script>
```

你也可以提供第二个的参数为 property 名称 (也就是键名)：

```html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

还可以用第三个参数作为索引：

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

> 在遍历对象时，会按 `Object.keys()` 的结果遍历，但是**不能**保证它的结果在不同的 JavaScript 引擎下都一致。

### 对象变更检测

还是由于 JavaScript 的限制，对于已经创建的实例，**Vue 不能检测对象根级别属性的添加或删除**：

```javascript
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` 现在是响应式的

vm.b = 2
// `vm.b` 不是响应式的
```

但是，可以使用 `Vue.set(object, propertyName, value)` 方法向嵌套对象添加响应式属性。例如，对于：

```javascript
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

你可以添加一个新的 `age` 属性到嵌套的 `userProfile` 对象：

```javascript
Vue.set(vm.userProfile, 'age', 27)
//或者
vm.$set(vm.userProfile, 'age', 27)
```

有时你可能需要为已有对象赋值多个新属性，比如使用 `Object.assign()` 或 `_.extend()`。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

```javascript
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

你应该这样做：

```javascript
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## 迭代值范围

`v-for` 也可以接受整数。在这种情况下，它会把模板重复对应次数。

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

结果：

1 2 3 4 5 6 7 8 9 10

## `v-if`与`v-for`一起使用

当 `v-if` 与 `v-for` 在同一节点中一起使用时，`v-for` 具有比 `v-if` 更高的优先级，这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

上面的代码将只渲染未完成的 todo。

而如果你的目的是有条件地跳过循环的执行，那么可以将 `v-if` 置于外层元素 (或 `<template>`)上。如：

```html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

## 在组件上使用`v-for`

在自定义组件上，你可以像在任何普通元素上一样使用 `v-for` 。

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

2.2.0+ 的版本里，当在组件上使用 `v-for` 时，`key` 是必须的。

然而，任何数据都不会被自动传递到组件里，因为组件有自己独立的作用域。为了把迭代数据传递到组件里，我们要使用 prop：

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat">
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)">
    </li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
});

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      });
      this.newTodoText = '';
    }
  }
});
</script>
```

注意这里的 `is="todo-item"` 属性。这种做法在使用 DOM 模板时是十分必要的，因为在 `<ul>` 元素内只有 `<li>` 元素会被看作有效内容。这样做实现的效果与 `<todo-item>` 相同，但是可以避开一些潜在的浏览器解析错误。

# 分组渲染

指令必须附加到一个元素上。如果想将指令应用到多个元素上，则可以把一个 `<template>` 元素当做不可见的包裹元素，并在上面使用指令。最终的渲染结果将不包含 `<template>` 元素。例如：

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>

<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

# 用key管理可复用的元素

Vue 为了尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。

例如，如果你允许用户在不同的登录方式之间切换：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

那么在上面的代码中切换 `loginType` 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，`<input>` 不会被替换掉——仅仅是替换了它的 `placeholder`。

但有时我们不希望复用，这时只需添加一个具有唯一值的 `key` 属性即可。它表示“这两个元素是完全独立的，不要复用它们”。

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

现在，每次切换时，输入框都将被重新渲染。

> 注意，`<label>` 元素仍然会被高效地复用（仅仅将内容`Username`替换为`Email`或反之），因为它们没有添加 `key` 属性。

特别地，当 Vue 正在更新使用 `v-for` 渲染的元素列表时，它默认使用“就地更新”的策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，而是按新的数据项依次更新每个元素。

这个默认的模式是高效的，但是**只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出**。

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 `key` 属性：

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

建议尽可能在使用 `v-for` 时提供 `key` attribute，除非遍历输出的 DOM 内容非常简单，或者是刻意依赖默认行为以获取性能上的提升。

> 不要使用对象或数组之类的非基本类型值作为 `v-for` 的 `key`。请用字符串或数值类型的值。

# 样式

## class绑定

### 对象语法

```html
<div id="app"
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      isActive: true,
  		hasError: false
    }
  });
</script>
```

结果渲染为：

```html
<div class="static active"></div>
```

当 `isActive` 或者 `hasError` 变化时（`true`留下，`false`移除），class 列表将相应地更新。此外，`v-bind:class` 指令也可以与普通的 class 属性共存。

绑定的数据对象不必内联定义在模板里：

```html
<div id="app" v-bind:class="classObject"></div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      classObject: {
        active: true,
        'text-danger': false
      }
    }
  });
</script>
```

我们也可以在这里绑定一个返回对象的计算属性：

```html
<div id="app" v-bind:class="classObject"></div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      isActive: true,
      error: null
    },
    computed: {
      classObject: function () {
        return {
          active: this.isActive && !this.error,
          'text-danger': this.error && this.error.type === 'fatal'
        }
      }
    }
  });
</script>
```

### 数组语法

我们可以把一个数组传给 `v-bind:class`，以应用一个 class 列表：

```html
<div id="app" v-bind:class="[activeClass, errorClass]"></div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      activeClass: 'active',
  		errorClass: 'text-danger'
    }
  });
</script>
```

渲染为：

```html
<div class="active text-danger"></div>
```

在数组语法中也可以使用对象语法：

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

相当于：

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

### 用在组件上

当在一个自定义组件上使用 `class` 属性时，这些 class 将被添加到该组件的根元素上面。这个元素上已经存在的 class 不会被覆盖。

例如，如果你声明了这个组件：

```javascript
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

然后在使用它的时候添加一些 class：

```html
<my-component class="baz boo"></my-component>
```

HTML 将被渲染为:

```html
<p class="foo bar baz boo">Hi</p>
```

对于带数据绑定 class 也同样适用：

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

当 `isActive` 为 truthy时，HTML 将被渲染成为：

```html
<p class="foo bar active">Hi</p>
```

## style绑定

### 对象语法

CSS 属性名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名。

```html
<div id="app" v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">Hello</div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      activeColor: 'red',
  		fontSize: 30
    }
  });
</script>
```

渲染为：

```html
<div id="app" style="color: red; font-size: 30px;">Hello</div>
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div id="app" v-bind:style="styleObject">Hello</div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      styleObject: {
        color: 'red',
        fontSize: '13px'
      }
    }
  });
</script>
```

同样的，对象语法常常结合返回对象的计算属性使用。

### 数组语法

`v-bind:style` 的数组语法可以将多个样式对象应用到同一个元素上：

```html
<div id="app" v-bind:style="[baseStyles, overridingStyles]">Hello</div>
<script>
  new Vue({
    el: '#app',
    data: {
      baseStyles: {
        color: 'green',
        fontSize: '30px'
      },
    	overridingStyles: {
        color: 'red',
        'font-weight': 'bold'
      }
    }
  })
</script>
```

渲染为：

```html
<div id="app" style="color: red; font-size: 30px; font-weight: bold;">Hello</div>
```

当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS 属性时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

从 2.3.0 起你可以为 `style` 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 `display: flex`。

# 事件处理

## 监听事件

可以用 `v-on` 指令添加一个事件监听器监听 DOM 事件，并在触发时运行一些 JavaScript 代码：

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
});
</script>
```

Vue为`v-on`指令提供了简写形式：

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>
```

## 事件处理器

可以直接将JavaScript代码写在`v-on`指令中作为事件处理器：

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message);
    }
  }
});
</script>
```

 `v-on` 还可以接收一个需要调用的方法名称作为事件处理器。

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!');
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName);
      }
    }
  }
});

// 也可以用 JavaScript 直接调用方法
example2.greet() // => 'Hello Vue.js!';
</script>
```

## 事件对象

在`v-on`指令中以方法名作为事件处理器时，会自动将DOM事件传递给该方法的参数。然而，直接以JavaScript代码作为事件处理器时，则可以用特殊变量`$event`来访问DOM事件：

```html
<button id="app" v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
<script>
var app = new Vue({
  el: '#app',
  methods: {
    warn: function (message, event) {
      // 现在我们可以访问原生事件对象
      if (event) {
        event.preventDefault();
      }
      alert(message);
    }
  }
});
</script>
```

## 事件修饰符

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 对应 addEventListener 中的 passive 选项 -->
<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发，而不会等待 `onScroll` 完成 -->
<div v-on:scroll.passive="onScroll">...</div>
```

不像其它只能对原生的 DOM 事件起作用的修饰符，`.once` 修饰符还能被用到自定义的组件事件上。

 `.passive` 修饰符尤其能够提升移动端的性能。不要把 `.passive` 和 `.prevent` 一起使用，因为 `.prevent` 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，`.passive` 会告诉浏览器你*不*想阻止事件的默认行为。

使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止**所有的点击**，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。

## 按键修饰符

```html
<input v-on:keyup.page-down="onPageDown">
```

只有在 `$event.key` 等于 `PageDown` 时`onPageDown`方法才会被调用。

你可以直接将 [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。

Vue还为一些常用按键提供了别名：

- `.delete` （捕获“删除”和“退格”键）
- `.esc`
- `space`
- `.up`
- `.down`
- `.left`
- `.right`

## 系统修饰符

可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

在 Mac 系统键盘上，meta 对应 command 键 (⌘)。在 Windows 系统键盘 meta 对应 Windows 徽标键 (⊞)。

```html
<!-- Ctrl + 鼠标点击 -->
<div @click.ctrl="doSomething">Do something</div>
```

注意修饰键与常规按键不同，在和 `keyup` 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 `ctrl` 的情况下释放其它按键，才能触发 `keyup.ctrl`。而单单释放 `ctrl` 也不会触发事件。

`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。

```html
<!-- 即使 Alt 或 Shift 也被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```

- `.left`
- `.right`
- `.middle`

这些修饰符会限制处理器仅响应特定的鼠标按钮。

# 表单

你可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。

`v-model` 在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text 和 textarea 元素使用 `value` 属性和 `input` 事件；
- checkbox 和 radio 使用 `checked` 属性和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

`v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` attribute 的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 `data` 选项中声明初始值。

## 文本框

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

## 多行文本框

```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

在文本区域插值 (`<textarea>{{text}}</textarea>`) 并不会生效，应用 `v-model` 来代替。

## 复选框

单个复选框，绑定到布尔值：

```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

单个复选框可以使用`true-value`和`false-value`来指定被选中时，绑定的模型的值：

```html
<!-- 当选中时 vm.toggle === 'yes' -->
<!-- 当没有选中时 vm.toggle === 'no' -->
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no">
```

这里的 `true-value` 和 `false-value` attribute 并不会影响输入控件的 `value` attribute，因为浏览器在提交表单时并不会包含未被选中的复选框。

多个复选框，绑定到同一个数组：

```html
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
});
</script>
```

## 单选按钮

```html
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
});
</script>
```

## 选择框

### 单选

```html
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '...',
  data: {
    selected: ''
  }
});
</script>
```

如果 `v-model` 表达式的初始值未能匹配任何选项，`<select>` 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

用 `v-for` 渲染的动态选项：

```html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
<script>
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
});
</script>
```

### 多选

多选将绑定到一个数组：

```html
<div id="example-6">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
});
</script>
```

## 值绑定

对于单选按钮，复选框及选择框的选项，`v-model` 绑定的值通常是静态字符串 (对于复选框也可以是布尔值)：

```html
<!-- 当选中时，`picked` 为字符串 "a" -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 为 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中第一个选项时，`selected` 为字符串 "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

但是有时我们可能想把值绑定到 Vue 实例的一个动态属性上，这时可以用 `v-bind` 实现，并且这个属性的值可以不是字符串。

```html
<!-- 当选中时 vm.pick === vm.a -->
<input type="radio" v-model="pick" v-bind:value="a">

<!-- 当选中时 vm.selected.number === 123 -->
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

## 表单修饰符

### `.lazy`

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步。你可以添加 `lazy` 修饰符，从而转变为使用 `change` 事件进行同步：

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```

### `.number`

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```
<input v-model.number="age" type="number">
```

这通常很有用，因为即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。

如果这个值无法被 `parseFloat()` 解析，则会返回原始的值。

### `.trim`

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```html
<input v-model.trim="msg">
```

## 自定义输入组件