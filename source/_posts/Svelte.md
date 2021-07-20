---
title: Svelte
date: 2021-07-20 15:15:46
tags: [3.38.3]
---

[Svelte](https://svelte.dev/) 是一个构建 web 应用程序的工具，它在 *构建/编译阶段* 将你的应用程序转换为理想的 JavaScript 应用，而不是像 React 和 Vue 等框架在 *运行阶段* 解释应用程序的代码。这意味着你不需要为框架所消耗的性能付出成本，并且在应用程序首次加载时没有额外损失。

# 入门

## 创建项目

在线方式：打开 [Svelte REPL](https://www.sveltejs.cn/repl) ，下载这个Hello world项目（svelte-app.zip，使用的是 Rollup构建工具），并解压。

使用 [degit](https://github.com/Rich-Harris/degit) 脚手架方式：`npx degit sveltejs/template my-svelte-project`。

然后，运行`npm install`，安装依赖。

> 项目默认使用的是 [Rollup](https://rollupjs.org/) 构建工具，也可以使用其他构建工具，例如： [webpack](https://webpack.js.org/) 、 [Vite](https://vitejs.dev)。
>
> 另外，官方还提供了VS Code [Svelte扩展](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode)。

## 启动开发服务器

```bash
$ npm run dev
```

打开 http://localhost:5000。

## 构建应用程序

```bash
$ npm run build
```

构建结果将输出到 *public/build/bundle.js*。（*public* 目录中的内容是最终要部署到Web服务器上的东西）

构建好的应用程序可通过下列命令运行：

```bash
$ npm start
```

# 组件

在 Svelte 中，应用程序由一个或多个 *组件（components）* 构成。组件是一个可重用的、自包含的代码块，它将 HTML、CSS 和 JavaScript 代码封装在一起并写入 `.svelte` 扩展名的文件中。

```html
<script>
	// logic goes here
</script>

<!-- markup (zero or more items) goes here -->

<style>
	/* styles go here */
</style>
```

## 样式

可以使用`<style>` 标签向组件添加样式，这些 CSS 样式规则 *的作用域被限定在当前组件中*。

```svelte
<p>This is a paragraph.</p>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

## 嵌套组件

可以 在`<script>` 标签中，使用`import`语句导入其他组件：

App.svelte：

```html
<script>
	import Nested from './Nested.svelte'; //“Nested”名字可以任意取
</script>

<p>This is a paragraph.</p>
<Nested/>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

Nested.svelte：

```html
<p>This is another paragraph.</p>
```

> 注意，尽管 `Nested.svelte` 有一个 `<p>` 元素，但 `App.svelte` 中的样式是不会影响 `Nested.svelte` 中的 `<p>` 元素。
>
> 另外，采用组件名称 `Nested` 的首字母大写的约定是为了使我们能够区分用户自定义的组件和一般的 HTML 标签。

## 实例化

组件需要通过实例化来加入到DOM中（嵌套组件不需要单独实例化，只要包含它的父组件实例化就可以了）：

main.js：

```js
import App from './App.svelte';

const app = new App({
	target: document.body,
	props: {
		// we'll learn about props later
		answer: 42
	}
});

export default app;
```

然后，如果需要，你可以使用 [组件 API](https://svelte.dev/docs#Client-side_component_API) 与 `app` 进行交互。

# 插值

插值（花括号表达式）中，可以引用`<script>`中定义的变量。插值即可以出现在元素内容中，也可以出现在元素属性中：

```html
<script>
	let src = 'tutorial/image.gif';
	let name = 'Rick Astley';
</script>

<img src={src} alt="{name.toUpperCase()} dances.">
```

插值输出的只是普通文本（会对文本中的HTML代码进行转义），如果希望将插值解析为HTML，则应该使用特殊标记 `{@html ...}`：

```html
<script>
	let string = `this string contains some <strong>HTML!!!</strong>`;
</script>

<p>{@html string}</p>
```

# 事件处理

可以使用`on:`指令监听元素上的任何事件。

```html
<script>
	let count = 0;

	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

## 获取事件对象

```html
<script>
   let m = { x: 0, y: 0 };
   function handleMousemove(event) {
      m.x = event.clientX;
      m.y = event.clientY;
   }
</script>
<div on:mousemove={handleMousemove}>
   The mouse position is {m.x} x {m.y}
</div>
<style>
   div { width: 100%; height: 100%; }
</style>
```

## 内联事件处理程序

```html
<script>
   let m = { x: 0, y: 0 };
</script>
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
   The mouse position is {m.x} x {m.y}
</div>
<style>
   div { width: 100%; height: 100%; }
</style>
```

引号是可选的，但它们有助于在某些环境中突出显示语法。

## 事件修饰符

事件修饰符可以改变事件处理的行为。

例如，带有`once`修饰符的处理程序只会运行一次：

```html
<script>
   function handleClick() {
      alert('no more alerts')	
   }
</script>
<button on:click|once={handleClick}>Click me</button>
```

完整的修饰符列表：

- `preventDefault`—`event.preventDefault()`在运行处理程序之前调用。例如，对于客户端表单处理很有用。
- `stopPropagation`— 调用`event.stopPropagation()`，防止事件到达下一个元素
- `passive` — 改进触摸/滚轮事件的滚动性能（Svelte 会在安全的地方自动添加它）
- `nonpassive` — 明确设置 `passive: false`
- `capture`— 在*捕获*阶段而不是*冒泡*阶段触发处理程序（[MDN 文档](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#Event_bubbling_and_capture)）
- `once` - 在第一次运行后删除处理程序
- `self` — 仅当 event.target 是元素本身时才触发处理程序

您可以将多个修饰符链接在一起，例如`on:click|once|capture={...}`.

## 组件事件（非原生）

组件借助事件派发器可以派发自定义的事件。

```html
--- Inner.svelte ---
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {  //派发自定义的 message 事件
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>


--- App.svelte ---
<script>
	import Inner from './Inner.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Inner on:message={handleMessage}/>  （监听 message 事件）
```

`createEventDispatcher`必须在第一次实例化组件时调用 - 不能稍后在例如`setTimeout`回调中执行此操作。这样才能将`dispatch`链接到组件实例。

## 事件转发

与 DOM 事件不同，组件事件不会*冒泡*。如果要监听某个深度嵌套组件上的事件，则中间组件必须*转发（forward）*该事件。

```html
--- Inner.svelte ---
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>


--- Outer.svelte ---
<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message/>


--- App.svelte ---
<script>
	import Outer from './Outer.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Outer on:message={handleMessage}/>
```

中间组件`Outer`中，`on:message`没有值意味着“转发所有`message`事件”。它是下列代码的简写形式：

```html
--- Outer.svelte ---
<script>
   import Inner from './Inner.svelte';
   import { createEventDispatcher } from 'svelte';
   const dispatch = createEventDispatcher();
   function forward(event) {
      dispatch('message', event.detail);
   }
</script>
<Inner on:message={forward}/>
```

原生的DOM事件也支持转发。例如，父组件要监听子组件内部元素的DOM事件：

```html
--- CustomButton.svelte ---
<button on:click>（转发click事件）	Click me</button>
<style>
   button {
      background: #E2E8F0;
      color: #64748B;
      border: unset;
      border-radius: 6px;
      padding: .75rem 1.5rem;
      cursor: pointer;
   }
   button:hover {
      background: #CBD5E1;
      color: #475569;
   }
   button:focus {
      background: #94A3B8;
      color: #F1F5F9;
   }
</style>

--- App.svelte ---
<script>
   import CustomButton from './CustomButton.svelte';
   function handleClick() {
      alert('Button Clicked');
   }
</script>
<CustomButton on:click={handleClick}/>
```

# 反应式

当组件的状态发生变化时，Svelte会自动更新DOM。有时，组件状态的某些部分需要从其他部分进行计算，并在它们发生变化时重新计算。这可通过反应式声明做到。

以`$:`开头的语句都是反应式的，只要语句中引用的变量发生变化，该语句就会被执行：

```html
<script>
   let count = 0;	
   $: doubled = count * 2;  //只要count值发生变化，doubled就会被重新计算
   $: if (count >= 10) {
      alert(`count is dangerously high!`);
      count = 9;
   }
   function handleClick() {
      count += 1;
   }
</script>
<button on:click={handleClick}>
   Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
<p>{count} doubled is {doubled}</p>
```

你可以轻松地将一组语句组合成一个反应式代码块：

```javascript
$: {
   console.log(`the count is ${count}`);
   alert(`I SAID THE COUNT IS ${count}`);
}
```

由于 Svelte 的反应性是由赋值语句触发的，因此使用数组的诸如 `push` 和 `splice` 之类的方法就不会触发自动更新。解决该问题的一种方法是添加一个多余的赋值语句：

```javascript
function addNumber() {
   numbers.push(numbers.length + 1);
   numbers = numbers;
}
```

但还有一个更惯用的解决方案：

```javascript
function addNumber() {
   numbers = [...numbers, numbers.length + 1];
}
或者
function addNumber() {
   //对数组和对象的属性赋值，与对值本身赋值的工作方式相同
   numbers[numbers.length] = numbers.length + 1;
}
```

你可以使用类似的模式来替换 `pop`、`shift`、`unshift` 和 `splice` 方法。

简而言之，更新变量的名称必须出现在赋值的左侧时，才会触发反应性。例如：

```javascript
const foo = obj.foo;
foo.bar = 'baz'; //不会触发对obj.foo.bar引用的更新，除非使用了obj = obj
```

# 属性

组件中声明的普通变量表示组件内部状态，只能在组件内部访问，而属性则可以接收外部的数据。Sveltte采用 `export` 来声明属性。

```html
--- Nested.svelte：---
<script>
	export let answer;
</script>
<p>The answer is {answer}</p>

--- App.svelte: ---
<script>
	import Nested from './Nested.svelte';
</script>
<Nested answer={42}/>
```

属性如果有初始化，则将这个初始化值作为它的默认值：

```html
--- Nested.svelte：---
<script>
	export let answer = 'a mystery';
</script>
<p>The answer is {answer}</p>

--- App.svelte: ---
<script>
	import Nested from './Nested.svelte';
</script>
<Nested answer={42}/>
<Nested/>   （这里使用默认值）
```

可以使用`...obj`语法，将对象传递给组件的各个属性：

```html
--- Info.svelte ---
<script>
   export let name;
   export let version;
   export let speed;
   export let website;
</script>
<p>
   The <code>{name}</code> package is {speed} fast.
   Download version {version} from <a href="https://www.npmjs.com/package/{name}">npm</a>
   and <a href={website}>learn more here</a>
</p>

--- App.svelte ---
<script>
   import Info from './Info.svelte';
   const pkg = {
      name: 'svelte',
      version: 3,
      speed: 'blazing',
      website: 'https://svelte.dev'
   };
</script>
<Info {...pkg}/>
```

`<Info {...pkg}/>` 是 `<Info name={pkg.name} version={pkg.version} speed={pkg.speed} website={pkg.website}/>` 的快捷写法。

在组件内，可以通过`$$props` 来访问所有属性和普通变量。

## 速记法

名称和值相同的属性，比如 `src={src}`。Svelte 为这种情况提供了一个方便的速记法：

```svelte
<img {src} alt="A man dances.">
```

注意：以`bind:`开头的绑定属性比较特殊，`bind:xxx={xxx}`应简写为`bind:xxx`。

# 控制块

## 条件

```svelte
<script>
	let user = { loggedIn: false };

	function toggle() {
		user.loggedIn = !user.loggedIn;
	}
</script>

{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

`#`字符表示*块打开*标签。`/`字符表示*块结束*标记。`:`字符（例如`{:else}`）表示*块继续*标记。

多分支：

```svelte
<script>
	let x = 7;
</script>

{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```

## 迭代

```svelte
<script>
	let cats = [
		{ id: 'J---aiyznGQ', name: 'Keyboard Cat' },
		{ id: 'z_AbfPXTKms', name: 'Maru' },
		{ id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
	];
</script>

<h1>The Famous Cats of YouTube</h1>

<ul>
	{#each cats as { id, name }, i}
		<li><a target="_blank" href="https://www.youtube.com/watch?v={id}">
			{i + 1}: {name}
		</a></li>
	{/each}
</ul>
或者
<ul>
	{#each cats as cat, i}
	<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
		{i + 1}: {cat.name}
	</a></li>
	{/each}
</ul>
```

第二个参数是可选的，表示当前索引。

`each`可以迭代任何数组或类似数组的对象（即它有一个`length`属性）。

当需要对迭代的数组或对象做修改时，要指定一个键，来告诉Svelte在组件更新时如何确定要更改哪个DOM节点。否则，它总是删除最后一个DOM节点，然后依次更新剩余节点的`name`值，其他属性值保持不变。

```html
--- Thing.svelte ---
<script>
	const emojis = {
        apple: "🍎",
        banana: "🍌",
        carrot: "🥕",
        doughnut: "🍩",
        egg: "🥚"
	}

	// the name is updated whenever the prop value changes...
	export let name;

	// ...but the "emoji" variable is fixed upon initialisation of the component
	const emoji = emojis[name];
</script>

<p>
	<span>The emoji for { name } is { emoji }</span>
</p>

<style>
	p {
		margin: 0.8em 0;
	}
	span {
		display: inline-block;
		padding: 0.2em 1em 0.3em;
		text-align: center;
		border-radius: 0.2em;
		background-color: #FFDFD3;
	}
</style>


-- App.svelte --
<script>
	import Thing from './Thing.svelte';

	let things = [
		{ id: 1, name: 'apple' },
		{ id: 2, name: 'banana' },
		{ id: 3, name: 'carrot' },
		{ id: 4, name: 'doughnut' },
		{ id: 5, name: 'egg' },
	];

	function handleClick() {
		things = things.slice(1);
	}
</script>

<button on:click={handleClick}>
	Remove first thing
</button>

{#each things as thing (thing.id) }
	<Thing name={thing.name}/>
{/each}
```

## 等待块

```html
<script>
	async function getRandomNumber() {
		const res = await fetch(`tutorial/random-number`);
		const text = await res.text();

		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}
	
	let promise = getRandomNumber();

	function handleClick() {
		promise = getRandomNumber();
	}
</script>

<button on:click={handleClick}>
	generate random number
</button>

{#await promise}  (等待异步数据)
	<p>...waiting</p>
{:then number}    （收到异步数据）
	<p>The number is {number}</p>
{:catch error}    （出现异常）
	<p style="color: red">{error.message}</p>
{/await}

```

`:catch`块是可选的，另外，第一块也可省略，但要写成：

```svelte
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```

# 绑定

## 双向绑定

```html
<script>
	let name = 'world';
</script>

<input value={name}>

<h1 on:click={e => name = name +'s'}>Hello {name}!</h1>
```

上面`name`值的改变会引起`<input>`输入值的变化，但`<input>`输入值的变化不会引起`name`值的变化，它们是单向的。如果要实现双向绑定的效果，则需要使用`bind:value`指令：

```html
<script>
	let name = 'world';
</script>

<input bind:value={name}>

<h1 on:click={e => name = name +'s'}>Hello {name}!</h1>
```

在DOM中，所有值都是字符串。`bind:value`指令会自动处理字符串与变量实际类型之间的转换。

## 复选框

复选框使用`bind:checked`指令进行双向绑定：

```html
<input type=checkbox bind:checked={yes}>
```

## 输入组

一组`name`相同的复选框或单选框，可以将`bind:group`指令与`value`属性一起使用：

```html
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];

	function join(flavours) {
		if (flavours.length === 1) return flavours[0];
		return `${flavours.slice(0, -1).join(', ')} and ${flavours[flavours.length - 1]}`;
	}
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} name="scoops" value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} name="scoops" value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} name="scoops" value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} name="flavours" value={flavour}>
		{flavour}
	</label>
{/each}

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {join(flavours)}
	</p>
{/if}
```

`bind:group`绑定的选中的值（单选框是单个值，复选框是选定值的数组）。

## 文本区

`<textarea>`元素也使用`bind:value`指令进行双向绑定。

```html
<textarea bind:value={value}></textarea>
```

## 下拉框

`<select>`元素使用`bind:value`指令来双向绑定选中的值：

```html
<script>
	let questions = [
		{ id: 1, text: `Where did you go to school?` },
		{ id: 2, text: `What is your mother's name?` },
		{ id: 3, text: `What is another personal fact that an attacker could easily find with Google?` }
	];

	let selected;

	let answer = '';

	function handleSubmit() {
		alert(`answered question ${selected.id} (${selected.text}) with "${answer}"`);
	}
</script>

<h2>Insecurity questions</h2>

<form on:submit|preventDefault={handleSubmit}>
	<select bind:value={selected} on:change="{() => answer = ''}">
		{#each questions as question}
			<option value={question}>
				{question.text}
			</option>
		{/each}
	</select>

	<input bind:value={answer}>

	<button disabled={!answer} type=submit>
		Submit
	</button>
</form>

<p>selected question {selected ? selected.id : '[waiting...]'}</p>

<style>
	input {
		display: block;
		width: 500px;
		max-width: 100%;
	}
</style>
```

因为我们还没有为 `selected` 设置初始值，所以绑定会自动将其设置为默认值（列表中的第一个选项）。不过要小心——在绑定初始化之前，`selected` 仍然是未定义的，所以我们不能盲目地在模板中引用它（例如selected.id） 。

### 多选下拉框

`<select>`元素带`multiple`属性时允许选中多个项，这种它绑定的是一个选定值的数组：

```html
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];

	function join(flavours) {
		if (flavours.length === 1) return flavours[0];
		return `${flavours.slice(0, -1).join(', ')} and ${flavours[flavours.length - 1]}`;
	}
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

<select multiple bind:value={flavours}>
	{#each menu as flavour}
		<option value={flavour}>
			{flavour}
		</option>
	{/each}
</select>

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {join(flavours)}
	</p>
{/if}
```

## Contenteditable 绑定

具有 `contenteditable="true"` 属性的元素支持 `bind:textContent`（将值处理为文本。回车输入新行不会自动使用`<p>`元素包装新行） 和 `bind:innerHTML` （将值处理为HTML。回车输入新行会自动使用`<p>`元素包装新行）指令，它们双向绑定到编辑的内容：

```html
<script>
	let html = '<p>Write some text!</p>';
</script>

<div
	contenteditable="true"
	bind:innerHTML={html}
></div>

<pre>{html}</pre>

<style>
	[contenteditable] {
		padding: 0.5em;
		border: 1px solid #eee;
		border-radius: 4px;
	}
</style>
```

## `each`块内的绑定

```html
<script>
	let todos = [
		{ done: false, text: 'finish Svelte tutorial' },
		{ done: false, text: 'build an app' },
		{ done: false, text: 'world domination' }
	];

	function add() {
		todos = todos.concat({ done: false, text: '' });
	}

	function clear() {
		todos = todos.filter(t => !t.done);
	}

	$: remaining = todos.filter(t => !t.done).length;
</script>

<h1>Todos</h1>

{#each todos as todo}
	<div class:done={todo.done}>
		<input
			type=checkbox
			bind:checked={todo.done}
		>

		<input
			placeholder="What needs to be done?"
			bind:value={todo.text}
		>
	</div>
{/each}

<p>{remaining} remaining</p>

<button on:click={add}>
	Add new
</button>

<button on:click={clear}>
	Clear completed
</button>

<style>
	.done {
		opacity: 0.4;
	}
</style>
```

注意，与这些 `<input>` 元素交互将改变数组。如果您更喜欢使用不可变数据，则应避免使用这些绑定并改用事件处理程序。
