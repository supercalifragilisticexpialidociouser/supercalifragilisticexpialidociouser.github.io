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

一个 Vue 应用由一个通过 `new Vue` 创建的**根 Vue 实例**，以及可选的嵌套的、可复用的组件树组成。`Vue`函数接受一个**选项对象**，用以定义Vue实例。

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

在 Vue 里，组件是可复用的 Vue 实例，且带有一个名字，它与根Vue实例接受相同的选项对象（仅有的例外是像 `el` 这样根实例特有的选项）。我们可以在Vue 根实例中，把组件作为自定义元素来使用。

## 定义组件

Vue的组件是普通的 JavaScript 对象（即选项对象）：

```javascript
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

### 单个根元素

**每个组件的模板必须有且只有一个根元素**。例如，组件的模板不能这样：

```javascript
template: `
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
`
```

而应将模板的内容包裹在一个父元素内：

```javascript
template: `
  <div class="blog-post">
    <h3>{{ title }}</h3>
    <div v-html="content"></div>
  </div>
`
```

## 注册组件

在模板中使用组件之前，这些组件必须先注册以便 Vue 能够识别。

### 组件名

在注册一个组件的时候，我们始终需要给它一个名字。

当使用 kebab-case (短横线分隔命名) 定义一个组件时，你也必须在引用这个自定义元素时使用 kebab-case，例如：

```html
<div id="app">
  <my-component-name></my-component-name>
</div>
<script>
	Vue.component('my-component-name', { /* ... */ });
  …
</script>
```

当使用 PascalCase (首字母大写命名) 定义一个组件时，你在引用这个自定义元素时两种命名法都可以使用。

```html
<div id="app">
  <!-- 在 HTML 中必须是 kebab-case 的 -->
  <my-component-name></my-component-name>
</div>
<script>
	Vue.component('MyComponentName', { /* ... */ });
  Vue.component('MyComponentName2' {
    template: `
			<div>
				<!-- 在字符串模板中，两种命名法均可用 -->
  			<MyComponentName/>
				…
  		</div>
		`
  });
  …
</script>
```

注意，尽管如此，直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。

### 全局注册

全局注册组件是通过`Vue.component`来创建：

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
<script>
  // 注册一个名为 button-counter 的新组件
  Vue.component('button-counter', {
    data: function () {
      return {
        count: 0
      }
    },
    template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
  });
  new Vue({ el: '#components-demo' });
</script>
```

全局注册的组件可以用在其被注册之后的任何 (通过 `new Vue`) 新创建的 Vue 根实例，也包括其组件树中的所有子组件的模板中。

**全局注册的行为必须在根 Vue 实例 (通过 new Vue) 创建之前发生**。

为了避免导入列表过长，如果你使用了 webpack (或在内部使用了 webpack 的 [Vue CLI 3+](https://github.com/vuejs/vue-cli))，那么就可以在应用入口文件 (比如 `src/main.js`) 中使用 `require.context` 批量全局注册组件：

```javascript
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
);

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName);

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名（不包括扩展名）
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  );

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  );
});
```



### 局部注册

如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。这造成了用户下载的 JavaScript 的无谓的增加。

在 `components` 选项中可以局部注册你想要使用的组件：

```javascript
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

对于 `components` 对象中的每个属性来说，其属性名就是自定义元素的名字，其属性值就是这个组件的选项对象。

注意**局部注册的组件在其子组件中不可用**。例如，如果你希望 `ComponentA` 在 `ComponentB` 中可用，则你需要这样写：

```
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

或者如果你通过 Babel 和 webpack 使用 ES2015 模块，那么代码看起来更像：

ComponentB.vue（或 ComponentB.js）：

```
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA //相当于 ComponentA: ComponentA
  },
  // ...
}
```

注意在 ES2015+ 中，在对象中放一个类似 `ComponentA` 的变量名其实是 `ComponentA: ComponentA` 的缩写，即这个变量名同时是：

- 用在模板中的自定义元素的名称
- 包含了这个组件选项的变量名

## 组件复用

你可以将组件进行任意次数的复用，并且在模板中组件的每次出现，就会有一个它的新实例被创建。

另外，为了实现组件的复用。**组件的 data 选项必须是一个函数**，这样每个实例才可以维护一份被返回数据对象的独立的拷贝：

```javascript
data: function () {
  return { //返回一个数据对象
    count: 0
  }
}
```

如果`data`选项直接提供一个数据对象，例如：

```javascript
data: {
  count: 0
}
```

则该组件的所有实例都将共享同一数据对象。

## 向子组件传递数据

Prop 是你可以在组件上注册的一些自定义 attribute。当一个值传递给一个 prop attribute 的时候，它就变成了那个组件实例的一个属性。

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:post="post">
</blog-post>
<script>
  Vue.component('blog-post', {
    props: ['post'],  //自定义attribute
    //在组件模板中可以直接访问这个prop————post
    template: `
      <div class="blog-post">
        <h3>{{ post.title }}</h3>
        <div v-html="post.content"></div>
      </div>
    ` 
  });

  new Vue({
    el: '#blog-post-demo',
    data: {
      posts: [
        { id: 1, title: 'My journey with Vue', content: '…' },
        { id: 2, title: 'Blogging with Vue', content: '…' },
        { id: 3, title: 'Why Vue is so fun', content: '…' }
      ]
    }
  });
</script>
```

> 上述示例使用了 JavaScript 的[模板字符串](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Template_literals)来让多行的模板更易读。它们在 IE 下并没有被支持，所以如果你需要在不 (经过 Babel 或 TypeScript 之类的工具) 编译的情况下支持 IE，请使用[折行转义字符](https://css-tricks.com/snippets/javascript/multiline-string-variables-in-javascript/)取而代之。

一个组件默认可以拥有任意数量的 prop，任何值都可以传递给任何 prop。

### Prop命名

HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名：

```html
<!-- 在 HTML 中是 kebab-case 的 -->
<blog-post post-title="hello!"></blog-post>
<script>
  Vue.component('blog-post', {
    // 在 JavaScript 中是 camelCase 的
    props: ['postTitle'],
    template: '<h3>{{ postTitle }}</h3>'
  });
  …
</script>
```

如果你使用字符串模板，那么这个限制就不存在了。

### Prop传值

当prop以非数据绑定方式接受传值时，它总是将传值当作是静态字符串。如果希望以非字符串方式传值，例如以数值、数组、对象等传值，则必须使用`v-bind`指令进行数据绑定。

传入数值：

```html
<!-- 静态传值，likes接受的是一个字符串"42" -->
<blog-post likes="42"></blog-post>

<!-- 静态传值，likes接受的是一个数值42 -->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>
```

传入布尔值：

```html
<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:is-published="false"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```

传入数组：

```html
<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```

传入对象：

```html
<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post
  v-bind:author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }">
</blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:author="post.author"></blog-post>
```

如果你想要将一个对象的所有属性都作为 prop 传入，你可以使用不带参数的 `v-bind` (取代 `v-bind:prop-name`)。例如，对于一个给定的对象 `post`：

```javascript
post: {
  id: 1,
  title: 'My Journey with Vue'
}
```

下面的模板：

```html
<blog-post v-bind="post"></blog-post>
```

等价于：

```html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title">
</blog-post>
```

### 单向数据流

Prop传值应该是单向的，即每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。因此，这个子组件接下来希望将其作为一个本地的 prop 数据来使用，则最好定义一个本地的 data 属性并将这个 prop 用作其初始值：

```javascript
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```

如果还要对prop传入的值进行转换，则最好使用计算属性：

```javascript
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身**将会**影响到父组件的状态。

### Prop验证

为了为组件的 prop 指定验证要求，你可以为 `props` 中的值提供一个带有验证需求的对象，而不是一个字符串数组。例如：

```javascript
Vue.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

当 prop 验证失败的时候，(开发环境构建版本的) Vue 将会产生一个控制台的警告。

注意那些 prop 会在一个组件实例创建**之前**进行验证，所以实例的属性 (如 `data`、`computed` 等) 在 `default` 或 `validator` 函数中是不可用的。

`type` 可以是下列构造函数中的一个：

- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`
- 自定义的构造函数，底层通过 `instanceof` 来进行检查

例如，给定下列自定义的构造函数：

```javascript
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

你可以使用：

```javascript
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

来验证 `author` prop 的值是否是通过 `new Person` 创建的。

### 非Prop的Attribute

一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 prop 定义的 attribute。

例如，想象一下你通过一个 Bootstrap 插件使用了一个第三方的 `<bootstrap-date-input>` 组件，这个插件需要在其 `<input>` 上用到一个 `data-date-picker` attribute。我们可以将这个 attribute 添加到你的组件实例上：

```html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

然后这个 `data-date-picker="activated"` attribute 就会自动添加到 `<bootstrap-date-input>` 的根元素上。如果`<bootstrap-date-input>` 的根元素上已经有`data-date-picker`属性则会被覆盖（只有`class`和`style`属性会采用合并策略）。

如果希望自己来控制组件的根元素如何继承attribute，则可以在组件的选项中设置 `inheritAttrs: false`。例如：

```javascript
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

配合实例的 `$attrs` 属性（该属性包含了传递给一个组件的所有 attribute 名和 attribute 值）使用，就可以手动决定这些 attribute 会被赋予哪个元素。

```html
<base-input
  v-model="username"
  required
  placeholder="Enter your username">
</base-input>
<script>
  Vue.component('base-input', {
    inheritAttrs: false,
    props: ['label', 'value'],
    template: `
      <label>
        {{ label }}
        <input
          v-bind="$attrs"
          v-bind:value="value"
          v-on:input="$emit('input', $event.target.value)">
      </label>
    `
  });
  …
</script>
```

注意 `inheritAttrs: false` 选项不会影响 `style` 和 `class` 的绑定。

## 监听子组件事件

父组件通过监听子组件事件，可以接收子组件发送的信息。（详见“自定义事件”）

## 插槽

插槽就是在组件的`template`选项中出现的`<slot>`元素，它们用来接收该组件起始标签和结束标签之间的任何内容（可以包含HTML和其他自定义组件）。如果组件没有提供插槽，则该组件起始标签和结束标签之间的任何内容都会被抛弃。

```html
<navigation-link url="/profile">
  <!-- 这里仍是父级模板，因此无法访问 `url` prop，但能访问父级作用域中的属性 -->
  <span class="fa fa-user"></span>
  Your Profile
</navigation-link>
<script>
Vue.component('navigation-link', {
  props: ['url'],
  template: `
    <div>
			<!-- 这里是子级模板，可以访问子作用域中的属性，包括组件的`url` prop -->
      <a v-bind:href="url"
         class="nav-link">
        <slot></slot>
      </a>
    </div>
  `
})
…
</script>
```

输出为：

```html
<a href="/profile"
   class="nav-link">
  <span class="fa fa-user"></span>
  Your Profile
</a>
```

注意：父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

### 后备内容

`<slot>`元素的内容是后备内容。当组件没有给插槽提供任何内容时，则使用后备内容；否则，提供的内容将会取代后备内容：

```html
<submit-button>
  Save  （这里的内容将取代后备内容。如果这里没有任何内容，则使用后备内容）
</submit-button>
<script>
  Vue.component('submit-button', {
    template: `
      <button type="submit">
        <slot>Submit</slot><!-- 后备内容 -->
      </button>
    `
  });
  …
</script>
```

输出为：

```html
<button type="submit">
  Save
</button>
```

### 具名插槽

一个组件模板中，可以使用多个插槽，只需为每个`<slot>`加上一个`name` attribute，这称为具名插槽：

```html
<base-layout>
  <!-- 通过在一个 <template> 元素上使用 v-slot 指令，以指定要取代的插槽 -->
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>
  <!-- 这些没有包含在 <template> 中的元素，默认属于 default 插槽。等价于：
	<template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>
	-->

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
<script>
  Vue.component('base-layout', {
    template: `
      <div class="container">
        <header>
          <slot name="header"></slot>
        </header>
        <main>
					<!-- 相当于： <slot name="default"></slot> -->
          <slot></slot>
        </main>
        <footer>
          <slot name="footer"></slot>
        </footer>
      </div>
    `
  });
  …
</script>
```

输出为：

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

注意 **`v-slot` 只能添加在 `<template>`上**（有一个例外，参见：） 。

## 动态组件

## 异步组件

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

## 单文件组件

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

### 过滤器

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

### 条件渲染

#### v-if

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

#### v-show

`v-show`指令会按条件显示或隐藏元素，但带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS 属性 `display`。

```html
<h1 v-show="ok">Hello!</h1>
```

> 注意，`v-show` 不支持 `<template>` 元素，也不支持 `v-else`。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

### 列表渲染

#### 迭代数组

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

##### 数组更新检测

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

#### 迭代对象

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

##### 对象变更检测

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

#### 迭代值范围

`v-for` 也可以接受整数。在这种情况下，它会把模板重复对应次数。

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

结果：

1 2 3 4 5 6 7 8 9 10

#### `v-if`与`v-for`一起使用

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

#### 在组件上使用`v-for`

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

### 分组渲染

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

### 用key管理可复用的元素

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

### 自定义指令

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

当在一个自定义组件上使用 `class` 属性时，这些 class 将被添加到该组件的根元素上面。这个元素上已经存在的 class 不会被覆盖，而是会被合并起来。（`style`也有类似行为，其他原生属性的行为则是覆盖）

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

在`v-on`指令中以方法名作为事件处理器时，会自动将DOM事件传递给该方法的第一个参数。然而，直接以JavaScript代码作为事件处理器时，则可以用特殊变量`$event`来访问DOM事件：

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

## 自定义事件

下面是一个放大博文字号的例子：

```html
<div id="blog-posts-events-demo">
  <!-- 2. 在模板中通过postFontSize属性控制所有博文的字号 -->
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
      v-on:enlarge-text="postFontSize += $event"> <!-- 3. 监听子组件的enlarge-text事件，并用$event来访问子组件抛出的值，进而修改postFontSize属性的值 -->
    </blog-post>
  </div>
</div>
<script>
  Vue.component('blog-post', {
    props: ['post'],
    template: `
      <div class="blog-post">
        <h3>{{ post.title }}</h3>
				<!-- 4. 子组件通过$emit来触发enlarge-text事件，并抛出值 0.1 -->
        <button v-on:click="$emit('enlarge-text', 0.1)">
          Enlarge text
        </button>
        <div v-html="post.content"></div>
      </div>
    `
  });
  
  new Vue({
    el: '#blog-posts-events-demo',
    data: {
      posts: [/* ... */],
      postFontSize: 1  //1. 定义用于控制字号的属性
    }
  })
</script>
```

上面例子中，事件处理器是一段JavaScript代码，如果是一个方法，则`$event`会自动作为该方法的第一个参数（如果有的话）传入：

```html
…
		<blog-post
      …
      v-on:enlarge-text="onEnlargeText">
		</blog-post>
…
<script>
…
	methods: {
    // enlargeAmount用于接收子组件抛出的值，即$event
    onEnlargeText: function (enlargeAmount) {
      this.postFontSize += enlargeAmount
    }
  }
…
</script>
```

### 事件名

不同于组件和 prop，事件名不存在任何自动化的大小写转换。而是触发的事件名需要完全匹配监听这个事件所用的名称。举个例子，如果触发一个 camelCase 名字的事件：

```javascript
this.$emit('myEvent')
```

则监听这个名字的 kebab-case 版本是不会有任何效果的：

```html
<!-- 没有效果 -->
<my-component v-on:my-event="doSomething"></my-component>
```

不同于组件和 prop，事件名不会被用作一个 JavaScript 变量名或属性名，所以就没有理由使用 camelCase 或 PascalCase 了。并且 `v-on` 事件监听器在 DOM 模板中会被自动转换为全小写 (因为 HTML 是大小写不敏感的)，所以 `v-on:myEvent` 将会变成 `v-on:myevent`——导致 `myEvent` 不可能被监听到。

因此，我们推荐你**始终使用 kebab-case 的事件名**。

### 将原生事件绑定到组件

你可能有很多次想要在一个组件的根元素上直接监听一个原生事件。这时，你可以使用 `v-on` 的 `.native` 修饰符：

```html
<base-input v-on:focus.native="onFocus"></base-input>
```

在有的时候这是很有用的，不过在你尝试监听一个类似 `<input>` 的非常特定的元素时，这并不是个好主意。比如上述 `<base-input>` 组件可能做了如下重构，所以根元素实际上是一个 `<label>` 元素：

```html
<label>
  {{ label }}
  <input
    v-bind="$attrs"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)">
</label>
```

这时，父级的 `.native` 监听器将静默失败。它不会产生任何报错，但是 `onFocus` 处理函数不会如你预期地被调用。

为了解决这个问题，Vue 提供了一个 `$listeners` 属性，它是一个对象，里面包含了作用在这个组件上的所有监听器。例如：

```javascript
{
  focus: function (event) { /* ... */ }
  input: function (value) { /* ... */ },
}
```

有了这个 `$listeners` 属性，你就可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的**子元素**。对于类似 `<input>` 的你希望它也可以配合 `v-model` 工作的组件来说，为这些监听器创建一个类似下述 `inputListeners` 的计算属性通常是非常有用的：

```javascript
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this
      // `Object.assign` 将所有的对象合并为一个新对象
      return Object.assign({},
        // 我们从父级添加所有的监听器
        this.$listeners,
        // 然后我们添加自定义监听器，
        // 或覆写一些监听器的行为
        {
          // 这里确保组件配合 `v-model` 的工作
          input: function (event) {
            vm.$emit('input', event.target.value)
          }
        }
      )
    }
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners">
    </label>
  `
})
```

现在 `<base-input>` 组件是一个**完全透明的包裹器**了，也就是说它可以完全像一个普通的 `<input>` 元素一样使用了：所有跟它相同的 attribute 和监听器的都可以工作。

## `.sync`修饰符

```html
<text-document v-bind:title.sync="doc.title"></text-document>
<!-- 等价于： -->
<text-document
  v-bind:title="doc.title"
  v-on:update:title="doc.title = $event">
</text-document>
```

然后，在一个包含 `title` prop 的假设的组件中，我们可以用以下方法表达对其赋新值：

```javascript
this.$emit('update:title', newTitle)
```

这样，`title` prop就支持双向绑定了。

> 注意带有 `.sync` 修饰符的 `v-bind` **不能**和表达式一起使用 (例如 `v-bind:title.sync=”doc.title + ‘!’”` 是无效的)。取而代之的是，你只能提供你想要绑定的属性名，类似 `v-model`。

当我们用一个对象同时设置多个 prop 的时候，也可以将这个 `.sync` 修饰符和 `v-bind` 配合使用：

```html
<text-document v-bind.sync="doc"></text-document>
```

这样会把 `doc` 对象中的每一个属性 (如 `title`) 都作为一个独立的 prop 传进去，然后各自添加用于更新的 `v-on` 监听器。

# 表单

你可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。本质上，一个`v-model`绑定等于一个`v-bind:value`绑定和一个`v-on:input`绑定：

```html
<input v-model="searchText">
<!-- 等价于： -->
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value">
```

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

## 自定义输入组件

可以在自定义组件上使用`v-model`：

```html
<custom-input v-model="searchText"></custom-input>
<!-- 等价于： -->
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event">
</custom-input>
```

为了让它正常工作，这个组件内的 `<input>`元素必须：

- 将其 `value` attribute 绑定到一个名叫 `value` 的 prop 上；
- 在其 `input` 事件被触发时，将新的值通过自定义的 `input` 事件抛出。

写成代码之后是这样的：

```javascript
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)">
  `
});
```

### `model`选项

一个组件上的 `v-model` 默认会利用名为 `value` 的 prop 和名为 `input` 的事件，但是像单选框、复选框等类型的输入控件可能会将 `value` attribute 用于[不同的目的](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/checkbox#Value)。`model` 选项可以用来避免这样的冲突：

```html
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',  //默认为 'value'
    event: 'change'   //默认为 'input'
  },
  props: {
		//注意你仍然需要在组件的 props 选项里声明 checked 这个 prop。
    checked: Boolean 
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)">
  `
})
```

现在在这个组件上使用 `v-model` 的时候：

```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```

这里的 `lovingVue` 的值将会传入这个名为 `checked` 的 prop。同时当 `<base-checkbox>` 触发一个 `change` 事件并附带一个新的值的时候，这个 `lovingVue` 的属性将会被更新。

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

# 渲染函数和JSX

# 插件

# 路由

# 状态管理

# 服务端渲染

# 单元测试

# TypeScript支持

# 生产环境部署

# Vue CLI

