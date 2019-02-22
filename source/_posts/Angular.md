---
title: Angular
date: 2018-04-26 10:34:40
tags: [7.2.0]
---

Angular 是一个开发平台。它能帮你更轻松的构建 Web 应用。Angular 集声明式模板、依赖注入、端到端工具和一些最佳实践于一身，为你解决开发方面的各种挑战。Angular 为开发者提升构建 Web、手机或桌面应用的能力。

# 准备开发环境

## 安装Node.js

下载安装Node.js v10.15.0 LTS

## 安装angular-cli

angular-cli是一个命令行工具，可用于创建和管理Angular项目。

安装：

```bash
$ npm install -g @angular/cli
```

查看版本：`ng version`。

查看帮助：`ng help`。

查看某个子命令的帮助：`ng new --help`或`ng help new`。

### 升级angular-cli

全局包升级：

```bash
$ npm uninstall -g @angular/cli
$ npm cache verify
# if npm version is < 5 then use `npm cache clean`
$ npm install -g @angular/cli@latest
```

本地项目升级：

```bash
$ rm -rf node_modules dist # use rmdir /S/Q node_modules dist in Windows Command Prompt; use rm -r -fo node_modules,dist in Windows PowerShell
$ npm install --save-dev @angular/cli@latest
$ npm install
```

## 安装Git

## 选择编辑器

推荐使用[Visual Studio Code](https://code.visualstudio.com/)。

## 选择浏览器

推荐使用Chrome。

<!--more-->

# 入门

## 创建工作空间和应用

Angular 工作空间就是你开发应用的上下文环境。 每个工作空间包含一些供一个或多个项目使用的文件。 每个项目都是一组由应用、库或端到端（e2e）测试构成的文件。

`ng new` 命令会创建一个包含项目的工作空间。而用来创建或操作应用和库的 `add` 和 `generate` 命令必须在工作空间目录下才能执行。

```bash
$ ng new my-app
```

上述命令将生成：

- 一个新的工作空间，根目录名叫 `my-app`
- 一个最初的骨架应用项目，也叫 `my-app`（但位于 `my-app/src` 子目录下，可以使用`--source-dir`指定自己的源码目录名，默认为`src`。）
- 一个端到端测试项目（位于 `my-app/e2e` 子目录下）
- 相关的配置文件

上面的命令执行时，会自动安装依赖，不需要再手动执行`npm install`。如果需要在创建项目时，略过安装依赖，则可以执行：`ng new my-app --skip-install`。

上面的命令执行时，会提示你是否需要使用Angular Routing，以及使用哪种样式格式。你也可以，直接在命令行上加上`--routing`选项和`--style`选项。例如：

```bash
$ ng new my-app --style=scss --routing
```

## 启动开发服务器

Angular 包含一个开发服务器，以便你能轻易地在本地构建应用和启动开发服务器。

```bash
$ cd my-app
$ ng serve --open
```

> 首先，要进入工作空间目录（`my-app`）。
>
> `ng serve` 命令会启动开发服务器，并监视项目中文件的变化，当你修改这些文件时，它就会重新构建应用。因此，新增或修改代码不需要手动重启开发服务器。
>
> 在运行复杂项目时，有时可能无法重新加载应用，这时只需单击浏览器的刷新按钮或导航到 <http://localhost:4200/>即可。
>
> `--open`或`-o`表示在服务器启动后，自动打开浏览器并访问 <http://localhost:4200/>。

默认的端口号是`4200`，要指定其他端口号，使用`--port`参数。另外，使用`--host`指定ip：

```bash
$ ng serve --open --port 3000 --host 0.0.0.0
```

## 开发应用

### 引导文件

Angular应用程序需要一个引导文件，其中包含启动应用程序和加载Angular模块所需要的代码，它是应用程序的入口。引导文件名为`src/main.ts`：

```typescript
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.log(err));
```

如果Angular应用要在非浏览器环境（例如Ionic移动开发框架）中运行，则需要使用引导文件中的代码来选择要使用的平台，并加载**根模块**和启动应用程序。`platformBrowserDynamic().bootstrapModule()`是适合基于浏览器的应用的引导方法。

### 主页

主页默认是`src/index.html`，是别人访问你的网站是看到的主页面的 HTML 文件。

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyApp</title>
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

主页上通常会包含一个Angular根组件（例如`<app-root>`），它是Angular世界的入口。

在构建应用时，CLI 会自动把所有 `js` 和 `css` 等资源（包括第三方资源）添加进去，所以你不必在这里手动添加任何 `<script>` 或 `<link>` 标签。angular-cli使用了WebPack工具，会自动生成项目的css、javascript文件，并将它们自动注入HTTP开发服务器发送给浏览器的HTML文件中。

> 较早版本的Angular依赖于模块加载器，它将为应用程序所需的JavaScript文件发送单独的HTTP请求。开发工具的更改已经简化了此过程，并改用在构建过程中创建的bundle。

### 创建模板

模板是指包含由Angular执行的指令的HTML片段，它用于向用户展示模型中的数据值。

例如：`src/app/app.component.html`：

```html
<!--The content below is only a placeholder and can be replaced.-->
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
  <img width="300" alt="Angular Logo" src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNTAgMjUwIj4KICAgIDxwYXRoIGZpbGw9IiNERDAwMzEiIGQ9Ik0xMjUgMzBMMzEuOSA2My4ybDE0LjIgMTIzLjFMMTI1IDIzMGw3OC45LTQzLjcgMTQuMi0xMjMuMXoiIC8+CiAgICA8cGF0aCBmaWxsPSIjQzMwMDJGIiBkPSJNMTI1IDMwdjIyLjItLjFWMjMwbDc4LjktNDMuNyAxNC4yLTEyMy4xTDEyNSAzMHoiIC8+CiAgICA8cGF0aCAgZmlsbD0iI0ZGRkZGRiIgZD0iTTEyNSA1Mi4xTDY2LjggMTgyLjZoMjEuN2wxMS43LTI5LjJoNDkuNGwxMS43IDI5LjJIMTgzTDEyNSA1Mi4xem0xNyA4My4zaC0zNGwxNy00MC45IDE3IDQwLjl6IiAvPgogIDwvc3ZnPg==">
</div>
<h2>Here are some links to help you start: </h2>
<ul>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://angular.io/tutorial">Tour of Heroes</a></h2>
  </li>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://angular.io/cli">CLI Documentation</a></h2>
  </li>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://blog.angular.io/">Angular blog</a></h2>
  </li>
</ul>

<router-outlet></router-outlet>
```

`{{…}}`语法是 Angular 的*插值绑定*语法，Angular会对双花括号之间的内容（通常在组件中定义）进行求值，以获取要显示的值。 这里插值绑定的意思是把组件的 `title` 属性的值绑定到 HTML 中的 `h1` 标记中。

### 创建组件

Angular组件是一个带`@Component`装饰器的类。它负责管理模板，并为其提供所需的数据和逻辑。

> 装饰器用来附加一些元数据，来告诉Angular如何处理一个类。

例如：`src/app/app.component.ts`：

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'my-app';
}
```

`selector`告诉Angular如何匹配该组件所要应用的在HTML元素。（例如：`<app-root>…</app-root>`）

`templateUrl`或`template`定义了组件将显示的内容的模板。其中，前者在单独的文件中定义模板，后者直接定义内联模板（由一个字符串或模板字符串（使用 “`” 包围）表示的模板）。

`styleUrls`或`styles`则定义了一个或多个仅应用于本组件模板的样式。其中，前者指定一个或多个指向CSS样式表文件的 URL，后者指定一个或多个内联 CSS 样式。它们的作用域将仅限于该组件，而不会“泄露”到外部 HTML 中。

另外，还可设置`moduleId`属性。当`moduleId`属性设置为`module.id`时，则模板和样式表中的相对路径将相对于组件类文件的位置。

> 注意：要使用module.id，需要将tsconfig.json修改如下：
>
> ```json
> "compilerOptions": {
>   "module": "commonjs",
>   …
> }
> ```

如果没有设置`moduleId`，则可以使用相对当前文件的路径，或使用绝对路径（应用程序的根目录为基准。根目录就是index.html所在目录）。

要创建组件，可以使用命令：

```bash
$ cd my-app
$ ng generate component xxx
或者
$ ng g comonent xxx
```

> 上面代码将生成如下文件：
>
> - src/app/xxx/xxx.component.html
> - src/app/xxx/xxx.component.spec.ts
> - src/app/xxx/xxx.component.ts
> - src/app/xxx/xxx.component.scss
>
> 并且该组件将隶属于根模块。（如果没有显式指定隶属的模块，则默认隶属于根模块）
>
> 可以加上`-m`或`--module`参数来指定隶属的模块：
>
> ```bash
> $ cd my-app
> $ ng generate component xxx --module home
> ```
>
> 上面代码将生成如下文件：
>
> - src/app/xxx/xxx.component.html
> - src/app/xxx/xxx.component.spec.ts
> - src/app/xxx/xxx.component.ts
> - src/app/xxx/xxx.component.scss
>
> 并且该组件将隶属于`home`模块。注意：组件仍然生成在自己的目录中。
>
> 参数`--flat`指示是否创建自己的目录，默认是`false`，即创建自己的目录：
>
> ```bash
> $ cd my-app
> $ ng generate component xxx --module home --flat
> ```
>
> 生成如下文件：
>
> - src/app/xxx.component.html
> - src/app/xxx.component.spec.ts
> - src/app/xxx.component.ts
> - src/app/xxx.component.scss
>
> ```bash
> $ cd my-app/src/app/modx
> $ ng generate component xxx --module home --flat
> ```
>
> 生成如下文件：
>
> - src/app/home/xxx.component.html
> - src/app/home/xxx.component.spec.ts
> - src/app/home/xxx.component.ts
> - src/app/home/xxx.component.scss
>
> 如果组件名与模块名相同，则：
>
> ```bash
> $ cd my-app
> $ ng generate component home --module home
> ```
>
> 生成如下文件：
>
> - src/app/home/home.component.html
> - src/app/home/home.component.spec.ts
> - src/app/home/home.component.ts
> - src/app/home/home.component.scss

### 应用样式

Angular应用的全局样式放在`src/styles.scss`中。另外，每个组件可以有自己的私有样式，它们放在由`styleUrls`指定的样式文件中。

### 将应用程序组合起来

Angular模块将相关功能封装起来，每个应用都至少有一个根模块。

例如`src/app/app.module.ts`：

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

`declarations`属性指定了该模块包含哪些组件、指令和管道（统称可声明对象），即哪些可声明对象属于这个模块。每个可声明对象都必须声明在（且只能声明在）一个 [NgModule](https://angular.cn/guide/ngmodules) 中。

`bootstrap`属性指定了当该模块引导时需要进行引导的组件（根组件）。列在这里的所有组件都会自动添加到 `entryComponents` 属性中。只有根模块才能设置`bootstrap`属性。一个主页可以有多个根组件。

`import`属性导入其他Angular模块的可声明对象，使得它们在当前Angular模块的**模板**中可用。

`exports`属性导出本模块的可声明对象，它是declarations 的子集。只有声明导出的可声明对象，才能在其它模块中导入使用。通常，根模块不需要导出任何东西，因为其它组件不需要导入根模块。

`providers`属性指定了在当前模块的注入器中可用的一组可注入对象。

创建模块：

```bash
$ cd my-app
$ ng g module home
```

> 上面命令将生成：`src/app/home/home.module.ts`。

# 模块

Angular开发中使用了两种类型的模块：

- JavaScript模块：一个.js文件就是一个模块。通过`export`导出变量为公共的，通过`import`导入公共变量。
- Angular模块：用于描述应用或一组相关功能。每个应用都有一个根模块（Root module），它为Angular提供启动应用所需的信息。Angular模块通过`@NgModule`装饰器来声明。Angular模块的目的是通过`@NgModule`装饰器定义的属性来提供配置信息。

## Angular模块

Angular模块有两种类型：

- 根模块（root module）：用于向Angular描述应用程序，主要包括：运行应用程序所需的功能模块、应该加载哪些自定义功能以及根组件的名称。根模块在引导文件中加载。
- 功能模块（feature module）：用于把相关的应用程序功能归集起来，使应用程序更易于管理。

## 创建Angular模块

```bash
$ ng generate module foo --flat --module=app
```

> `--flat` 把这个文件放进了 `src/app` 中，而不是单独的目录中。
>
> `--module=app` 告诉 CLI 把它注册到 `AppModule` 的 `imports` 数组中。

## 根模块

每个应用都至少有一个 Angular 模块，也就是根模块，用来引导并启动应用。

## 惰性加载模块

# 路由

告诉Angular每个URL映射到组件的映射称为URL路由，简称路由。

## `<base href>` 元素

Angular路由功能要求HTML文档（`index.html`）中有一个`<base>`元素，由其提供路由的基本URL。

## 定义路由

最简单的创建路由的方式就是在根模块的`@NgModule`装饰器中定义路由：

```typescript
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    RoutingModule.forRoot([
      {path: 'store', component: StoreComponent},
      {path: 'cart', component: CartDetailComponent},
      {path: 'checkout', component: CheckoutComponent},
      {path: '**', redirectTo: '/store'}
    ])
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## `<router-outlet>`元素

当使用路由功能时，Angular会查找`<router-outlet>`元素，它相当于一个占位符，它指定了应该在什么位置显示当前URL对应的组件。

## 应用程序导航

URL路由功能依赖浏览器提供的JavaScript API，因此用户无法简单地将目标URL输入到浏览器地址栏中进行导航。相反，导航必须由应用程序执行，具体的实现方法是在组件或其他构造块中编写JavaScript代码，或者向模板中的HTML元素添加属性。

> 实际上，直接将目标URL输入浏览器地址栏中，是能跳转到目标页面，但无法保持目标页面的原有状态。特别是，如果目标页面是一个表单，则原先输入内容将丢失。但是，如果应用程序希望能从不同的URL开始导航，则这种直接通过浏览器地址栏导航是有意义的。

在代码中导航，可以使用`Router`的`navigateByUrl()`方法：

```typescript
export class StoreComponent {
  …
  constructor(private router: Router) {}
  …
  addProductToCart(product: Product) {
    …
    this.router.navigateByUrl('/cart');
  }
}
```

在模板中，可通过向元素添加`routerLink`属性来实现导航：

```html
<button [disabled]='cart.itemCount == 0' routerLink='/cart'>购物车</button>
```

要在模板中使用`routerLink`属性，必须将`RouterModule`导入所在模块：

```typescript
import {RouterModule} from "@angular/router";

@NgModule({
  imports: [RouterModule, …],
  …
})
```

路由器会根据你应用中的导航规则和数据状态来拦截 URL。 当用户点击按钮、选择下拉框或收到其它任何来源的输入时，你可以导航到一个新视图。 路由器会在浏览器的历史日志中记录这个动作，所以前进和后退按钮也能正常工作。

## 守卫路由

守卫路由可以实现，只有当某些条件满足时，该路由才会被激活。

# 数据绑定

数据绑定是一种让模板的各部分与组件的各部分相互合作的机制。 我们往模板 HTML 中添加绑定标记，来告诉 Angular 如何把二者联系起来。

Angular 在每个 JavaScript 事件循环中处理所有的数据绑定，它会从组件树的根部开始，递归处理全部子组件。

## 组件到元素的绑定

### property绑定

属性绑定的语法：

```
{{模板表达式}}

[目标] = "模板表达式"

bind-目标 = "模板表达式"
```

注意：这里的“目标”是DOM 元素的property，或者是组件和指令的**输入**property（即组件或指令的`inputs`数组中所列的名字，或者是带有`@Input()`装饰器的property。 这些输入property被映射为组件或指令自己的property），而不是HTML的attribute。

在 Angular 的世界中，HTML attribute 唯一的作用是用来初始化DOM 元素、组件和指令的 property。当进行数据绑定时，只是在与元素和指令的 property 和事件打交道，而 attribute 就完全靠边站了。即HTML attribute指定了初始值；DOM property 是当前值。

property 的值可以改变，attribute 的值不能改变。

例1：`<image>` 元素的`src`属性 (property)会被绑定到组件的`heroImageUrl`属性上

```html
<img [src]="heroImageUrl">
```

例2：设置指令的属性

```html
<div [ngClass]="classes">[ngClass] binding to the classes property</div>
```

例3：设置自定义组件的模型属性（这是父子组件之间通讯的重要途径）

```html
<hero-detail [hero]="currentHero"></hero-detail>
```

如果只是想给target设置一个字符串值，而不是表达式，则可以省略包围target的方括号：

```html
<hero-detail prefix="You are my" [hero]="currentHero"></hero-detail>
```

另外，下列几对绑定是等价的：

```html
<p><img src="{{heroImageUrl}}"> is the <i>interpolated</i> image.</p>

<p><img [src]="heroImageUrl"> is the <i>property bound</i> image.</p>

<p><span>"{{title}}" is the <i>interpolated</i> title.</span></p>

<p>"<span [innerHTML]="title"></span>" is the <i>property bound</i> title.</p>
```

实际上，在渲染视图之前，Angular 把这些插值表达式翻译成相应的属性绑定。

Angular 数据绑定对危险 HTML 有防备。 在显示它们之前，它对内容先进行消毒。 不管是插值表达式还是属性绑定，都不会允许带有 script 标签的 HTML 泄漏到浏览器中。例如：

```typescript
evilTitle = 'Template <script>alert("evil never sleeps")</script>Syntax';
```

```html
<p>
  <span>"{{evilTitle}}" is the <i>interpolated</i> evil title.</span>
</p>
<p>
  "<span [innerHTML]="evilTitle"></span>" is the <i>property bound</i> evil title.
</p>
```

将输出：

### attribute绑定

attribute绑定的目标是HTML attribute，这是唯一的能创建和设置 attribute 的绑定形式。

之所以需要attribute绑定，是因为有些HTML attribute，例如 ARIA， SVG 和 table 中的 colspan/rowspan 等 attribute，它们是纯粹的 attribute，没有对应的property可供绑定。

attribute绑定的目标都要以“attr.”为前缀：

```html
<td [attr.colspan]="1 + 1">One-Two</td>
```

而不能：

```html
<td colspan="{{1 + 1}}">Three-Four</td>
```

### class绑定

借助 CSS 类绑定，可以从元素的class attribute 上添加和移除 CSS 类名。

CSS 类绑定绑定的语法与属性绑定类似。 但方括号中的部分不是元素的属性名，而是由`class`前缀，一个点 (`.`)和 CSS 类的名字组成， 其中后两部分是可选的。形如：`[class.类名]`。

例1：`[class]`形式

```html
<div class="bad curly special" 

     [class]="badCurly">Bad curly</div>
```

当 badCurly 有值时 class 这个 attribute 设置的内容会被完全覆盖；如果badCurly没有值时 ，则class="bad curly special" 。

例2：`[class.类名]`形式

```html
<div [class.special]="isSpecial">The class binding is special</div>
```

当`isSpecial`为`true`时，Angular 会添加`special`这个类，反之则移除它。

```html
<div class="special"
     [class.special]="!isSpecial">This one is not so special</div>
```

同时存在同效的HTML attribute和attribute绑定时，attribute绑定将覆盖HTML attribute。

> 注意：NgClass指令也可以用来管理class。

### style绑定

style绑定可以设置内联样式。

样式绑定的语法与属性绑定类似。 但方括号中的部分不是元素的属性名，而由`style`前缀，一个点 (`.`)和 CSS 样式的属性名组成。 形如：`[style.样式属性]`。这里，CSS样式属性名可以用中线命名法，也可以用驼峰式命名法，如`fontSize`。

```html
<button [style.background-color]="canSave ? 'cyan': 'grey'">Save</button>
```

有些样式绑定中的样式带有单位，可以用 “em” 和 “%” 来设置字体大小的单位：

```html
<button [style.font-size.em]="isSpecial ? 3 : 1">Big</button>
<button [style.font-size.%]="!isSpecial ? 150 : 50">Small</button>
```

注意：NgStyle指令也可以用来管理内联样式。

## 元素到组件的绑定：事件绑定

事件绑定的语法：

```
(目标) = "模板表达式"

on-目标 = "模板表达式"
```

事件绑定语法由等号左侧带圆括号的目标事件（DOM元素的事件或指令的**输出**property）和右侧引号中的模板语句（可以是多条语句）组成。

```html
<button (click)="onClickMe()">Click me!</button>
```

或者

```html
<button on-click="onSave()">On Save</button>
```

出现在模板语句中的每个标识符都属于特定的上下文对象。 这个对象通常都是控制此模板的 Angular 组件。

```typescript
@Component({
  selector: 'click-me',
  template: `
    <button (click)="onClickMe()">Click me!</button>
    {{clickMessage}}`
})
export class ClickMeComponent {
  clickMessage = '';
  onClickMe() {
    this.clickMessage = 'You are my hero!';
  }
}
```

### 获取用户输入

#### 通过`$event`对象

绑定会通过名叫`$event`的事件对象传递关于此事件的信息（包括数据值）。

事件对象的形态取决于目标事件。如果目标事件是原生 DOM 元素事件，`$event`就是 DOM事件对象，它有像target和target.value这样的属性。

```typescript
template: `
  <input (keyup)="onKey($event)">
  <p>{{values}}</p>`
```

当用户按下并释放一个按键时，触发`keyup`事件，Angular 将一个相应的 DOM 事件对象提供给`$event`变量，并将它作为参数传递给`onKey()`方法的`event`参数。

```typescript
export class KeyUpComponent_v1 {
  values = '';
  onKey(event:any) { // without type info
    this.values += event.target.value + ' | ';
  }
}
```

所有标准 DOM 事件对象都有一个`target`属性， 引用触发该事件的元素。 在本例中，`target`是`<input>`元素， `event.target.value`返回该元素的当前内容。

上例将`$event`转换为`any`类型。 这样简化了代码，但是有成本。 没有任何类型信息能够揭示事件对象的属性，防止简单的错误。

下面的例子，使用了带类型方法：

```typescript
export class KeyUpComponent_v1 {
  values = '';
  onKey(event: KeyboardEvent) { // with type info
    this.values += (<HTMLInputElement>event.target).value + ' | ';
  }
}
```



#### 通过模板引用变量

通过`$event`对象会获取整个 DOM 事件信息，这需要我们过多地了解HTML的实现细节。而通过模板引用变量则不需要我们了解HTML实现细节。

下面的例子使用了局部模板变量，在一个超简单的模板中实现按键反馈功能。

```typescript
@Component({
  selector: 'loop-back',
  template: `
    <input #box (keyup)="0">
    <p>{{box.value}}</p>
  `
})
export class LoopbackComponent { }
```

在标识符前加上井号 (`#`) 就能声明一个模板引用变量。上例中，在`<input>`元素声明了一个box的模板引用变量，它引用`<input>`元素本身。 可以在`<input>`元素的兄弟或子级元素中引用`box`。

代码使用`box`获得输入元素的`value`值，并通过插值表达式把它显示在`<p>`元素中。

这个模板完全是完全自包含的。它没有绑定到组件，组件也没做任何事情。

注意：只有在应用做了些异步事件（如击键），Angular 才更新绑定（并最终影响到屏幕）。因此，必须绑定一个事件，否则将完全无法工作。本例代码将`keyup`事件绑定到了数字0，这是可能是最短的模板语句。 虽然这个语句不做什么，但它满足 Angular 的要求，所以 Angular 将更新屏幕。

下面的代码重写了之前keyup示例，它使用变量来获得用户输入。

```typescript
@Component({
  selector: 'key-up2',
  template: `
    <input #box (keyup)="onKey(box.value)">
    <p>{{values}}</p>
  `
})
export class KeyUpComponent_v2 {
  values = '';
  onKey(value: string) {
    this.values += value + ' | ';
  }
}
```

这个方法最漂亮的一点是：组件代码从视图中获得了干净的数据值。再也不用了解`$event`变量及其结构了。（注意，最好传递给组件输入值，而不要直接传递元素`box`给组件。尽量将DOM相关的东西限制在模板中）

### 自定义事件

通常，指令使用 Angular `EventEmitter` 来触发自定义事件。 指令创建一个`EventEmitter`实例，并且把它作为属性暴露出来。 指令调用`EventEmitter.emit(payload)`来触发事件，可以传入任何东西作为消息载荷。 父指令通过绑定到这个属性来监听事件，并通过`$event`对象来访问载荷。

假设`HeroDetailComponent`用于显示英雄的信息，并响应用户的动作。 虽然`HeroDetailComponent`包含删除按钮，但它自己并不知道该如何删除这个英雄。 最好的做法是触发事件来报告“删除用户”的请求。

HeroDetailComponent.ts（模板）：

```typescript
template: `
<div>
  <img src="{{heroImageUrl}}">
  <span [style.text-decoration]="lineThrough">
    {{prefix}} {{hero?.fullName}}
  </span>
  <button (click)="delete()">Delete</button>
</div>`
```

HeroDetailComponent.ts (删除逻辑)：

```typescript
// This component make a request but it can't actually delete a hero.
@Output() deleteRequest = new EventEmitter<Hero>();
delete() {
  this.deleteRequest.emit(this.hero);
}
```

现在，假设有个宿主的父组件，它绑定了`HeroDetailComponent`的`deleteRequest`事件。

```html
<hero-detail (deleteRequest)="deleteHero($event)" [hero]="currentHero"></hero-detail>
```

当`deleteRequest`事件触发时，Angular 调用父组件的`deleteHero`方法， 在`$event`变量中传入要删除的英雄（来自`HeroDetail`）。 

### 常用事件监听器

#### keyup.enter

这不是DOM事件，而是一个Angular的模拟事件。只有当用户敲回车键时，Angular 才会调用事件处理器。

#### blur

blur事件在元素失去焦点时触发。

```typescript
@Component({
  selector: 'key-up4',
  template: `
    <input #box
      (keyup.enter)="update(box.value)"
      (blur)="update(box.value)">
    <p>{{value}}</p>
  `
})
export class KeyUpComponent_v4 {
  value = '';
  update(value: string) { this.value = value; }
}
```

上例在输入框失去焦点或按下回车键后都会更新组件的`value`值。

## 双向绑定

双向绑定的语法：

```
[(目标)] = "模板表达式"

bindon-目标 = "模板表达式"
```

双向绑定的属性都可拆分成两个独立的单向绑定。

当 Angular 在表单中看到`[(x)]`的绑定目标时， 它会期待这个`x`指令有一个名为`x`的输入属性，和一个名为`xChange`的输出属性。因此，`[(ngModel)]`可以拆分为`[ngModel]`和`(ngModelChange)`：

```html
<input type="text" class="form-control" id="name"
       required
       [ngModel]="model.name" name="name"
       (ngModelChange)="model.name = $event" >
```

之前看到的`$event`对象来自 DOM 事件。 但`ngModelChange`属性不会生成 DOM 事件 —— 它是Angular `EventEmitter`类型的属性，当它触发时， 它返回的是输入框的值 —— 也正是希望赋给模型`name`属性的值。

`[(ngModel)]`语法只能设置一个数据绑定属性。 如果需要做更多或不同的事情，就得自己用它的拆分形式。例如：

```html
<input
  [ngModel]="currentHero.firstName"
  (ngModelChange)="setUpperCaseFirstName($event)">
```

### NgModel指令

像`<input>`和`<select>`这样的 HTML 元素，`value`接收输入，而`oninput`处理事件，不遵循`x`值和`xChange`事件的模式。因此，Angular 另以 NgModel 指令为桥梁，允许在表单元素上使用双向数据绑定。

`<input>`元素如果不使用NgModel指令来实现双向绑定，则就必须使用下面的形式：

```html
<input [value]="currentHero.firstName"
       (input)="currentHero.firstName=$event.target.value" >
```

而使用NgModel指令，则为：

```html
<input
  [ngModel]="currentHero.firstName"
  (ngModelChange)="currentHero.firstName=$event">
```

或者

```html
<input [(ngModel)]="currentHero.firstName">
```

每种元素的特点各不相同，所以NgModel指令只能在一些特定表单元素上使用，例如输入文本框，因为它们支持 `ControlValueAccessor`。

除非写一个合适的值访问器，否则不能把`[(ngModel)]`用在自定义组件上。（通常自定义组件的双向绑定也不需要通过NgModel指令，直接使用 `[(自定义组件的属性)]` 就可以了）

注：在使用NgModel做双向数据绑定之前，得先导入`FormsModule`，即把它加入 Angular 模块的`imports`列表。

## 绑定目标

## 脏值检测

# 模板

组件和模板共同定义了 Angular 的视图。

## 定义模板

HTML 是 Angular 模板的语言，几乎所有的 HTML 语法都是有效的模板语法。但值得注意的例外是`<script>`元素，它被禁用了，以阻止脚本注入攻击的风险。

另外，`<html>`、`<body>`和`<base>`这些元素用在模板中是没有意义的。

模板除了可以使用像`<h2>`和`<p>`这样的典型的 HTML 元素，还可以通过组件、指令和插值表达式来扩展模板中的 HTML 词汇。它们看上去就是新元素和属性。 例如，`*ngFor`、`{{hero.name}}`、`(click)`、`[hero]`和`<hero-detail>`。

### 模板引用变量

模板引用变量是模板中对 DOM 元素或指令的引用。

它能在原生 DOM 元素中使用，也能用于 Angular 组件 —— 实际上，它可以和任何自定义 Web 组件协同工作。

#### 定义模板引用变量

语法一：

`#模板引用变量`

例如：

```html
<input #phone placeholder="phone number">
<button (click)="callPhone(phone.value)">Call</button>
```

语法二：

`ref-模板引用变量`

例如：

```html
<input ref-fax placeholder="fax number">
<button (click)="callFax(fax.value)">Fax</button>
```

Angular 把模板引用变量的值设置为它所在的那个元素。 

#### 引用模板引用变量

可以在同一元素、兄弟元素或任何子元素中引用模板引用变量。

### 插值表达式

插值表达式形如 `{{…}}` ，双花括号之间是**模板表达式**。它可以直接访问组件中的属性，并将表达式的值插入到视图的当前位置。

插值表达式是一个特殊的语法，它是单向数据绑定的一种，Angular 把它转换成了属性绑定。

### 模板表达式

大部分JavaScript表达式也是合法的模板表达式，但下列表达式是不允许的，包括：

1. 赋值 (`=`、`+=`、`-=`、 ...)

2. `new`运算符

3. 使用`;`或`,`的链式表达式

4. 自增或自减运算符 (`++`和`--`)

5. 不支持位运算`|`和`&`
6. 管道运算符`|`
7. 安全导航运算符`?.`

模板表达式不能引用全局命名空间中的任何东西， 不能引用window或document，不能调用console.log或Math.max。 它们被局限于只能访问来自表达式上下文中的成员。典型的表达式上下文就是这个组件实例，另外还有模板引用变量。

不要在模板表达式中使用具有副作用的属性或方法。

Angular 执行模板表达式，产生一个值，并把它赋值给绑定目标的属性。这个绑定目标可能是 HTML 元素、组件或指令。

#### 管道运算符

管道是一个简单的函数，它接受一个输入值，并返回转换结果。

```html
<div>Title through uppercase pipe: {{title | uppercase}}</div>
```

管道操作符会把它左侧的表达式结果传给它右侧的管道函数。

还可以通过多个管道串联表达式：

```html
<!-- Pipe chaining: convert title to uppercase, then to lowercase -->
<div>
  Title through a pipe chain:
  {{title | uppercase | lowercase}}
</div>
```

还能对它们使用参数：
```html
<!-- pipe with configuration argument => "February 25, 1970" -->
<div>Birthdate: {{currentHero?.birthdate | date:'longDate'}}</div>
```

#### 安全导航运算符

`?.` 与 `.` 一样用于属性的限定名中。

`?.` 会在它遇到第一个空值的时候跳出，并显示是空的，而不会抛出异常。

```html
The null hero's name is {{nullHero?.firstName}}
```

等价于：

```html
The null hero's name is {{nullHero && nullHero.firstName}}
```

或

```html
<div *ngIf="nullHero">The null hero's name is {{nullHero.firstName}}</div>
```

Angular 安全导航操作符 (`?.`)，在像`a?.b?.c?.d`这样的长属性路径中，它工作得很完美。

#### 模板语句

模板语句用来响应由绑定目标（如 HTML 元素、组件或指令）触发的事件。就像这样：`(event)="statement"`。

模板语句有副作用。

 模板语句解析器和模板表达式解析器有所不同，特别之处在于它支持基本赋值 (`=`) 和表达式链 (`;`和`,`)。

但下列表达式是不允许的，包括：

1）`new`运算符

2）自增和自减运算符：`++`和`--`

3）操作并赋值，例如`+=`和`-=`

4）位运算符`|`和`&`

5）模板表达式运算符

和表达式中一样，模板语句无法引用全局命名空间的任何东西。它们不能引用`window`或者`document`， 不能调用`console.log`或者`Math.max`。语句只能引用语句上下文中 —— 通常是正在绑定事件的那个组件实例或者是模板引用变量。

在事件绑定语句中，经常会看到被保留的`$event`符号，它代表触发事件的“消息”或“有效载荷（payload）”。

## 模板的应用方式

> 在浏览器（Chrome）上，点击查看网页源代码时，只会看到模板的静态内容。如果要查看最终生成的网页内容，可以通过右键单击浏览器窗口并从弹出菜单中选择Inspect，或者打开开发者工具查看。

### 内联模板

在创建组件时，可以通过`template`属性直接指定内联模板。

内联模板是由一个字符串或模板字符串（使用 “`” 包围）表示的模板。

### 模板文件

在创建组件时，也可以通过`templateUrl`属性来指定一个外部模板文件。

# 指令

指令是一个带有“指令元数据”的类。在 TypeScript 中，要通过`@Directive`装饰器把元数据附加到类上。

## 组件

组件是一个带模板的指令；`@Component`装饰器实际上就是一个`@Directive`装饰器，只是扩展了一些面向模板的特性。

详见“组件”。

## 结构型指令

结构型指令通过在 DOM 中添加、移除和替换元素来修改布局。例如：`*ngFor`。

### NgIf指令

当NgIf指令为`false`时，Angular 从 DOM 中物理地移除了这个元素子树。 它销毁了子树中的组件及其状态，也潜在释放了可观的资源，最终让用户体验到更好的性能。

而通过class绑定或style绑定来显示和隐藏元素及其子元素，这些元素仍然留在 DOM 中。 子树中的组件及其状态仍然保留着。 即使对于不可见属性，Angular 也会继续检查变更。 子树可能占用相当可观的内存和运算资源。

```html
<hero-detail *ngIf="isActive"></hero-detail>
```

### NgSwitchCase、NgSwitchDefault指令

### NgFor指令

NgFor是一个重复器指令。

NgFor指令既可以用在HTML元素上：

```html
<div *ngFor="let hero of heroes">{{hero.fullName}}</div>
```

也可以应用在一个组件元素上：

```html
<hero-detail *ngFor="let hero of heroes" [hero]="hero"></hero-detail>
```

赋值给`*ngFor`的字符串不是模板表达式，它是一个微语法 —— 由 Angular 自己解释的小型语言。在这个例子中，字符串"let hero of heroes"的含义是：

取出`heroes`数组中的每个英雄，把它存入局部变量`hero`中，并在每次迭代时对模板 HTML 可用。

`hero`前面的`let`关键字创建了名叫`hero`的模板输入变量。

> 注意：模板输入变量和模板引用变量不是一回事！

#### 索引

NgFor指令支持可选的`index`，它在迭代过程中会从 0 增长到“数组的长度”。 可以通过模板输入变量来捕获这个 `index`，并在模板中使用。

下例把 `index` 捕获到名叫`i`的变量中：

```html
<div *ngFor="let hero of heroes; let i=index">{{i + 1}} - {{hero.fullName}}</div>
```

除了`index`外，还有`first`、`last`、`even`、`odd`等局部变量可以获得索引。

#### trackBy

NgFor指令有时候会性能较差，特别是在大型列表中。 对一个条目的一丁点改动、移除或添加，都会导致级联的 DOM 操作。

例如，我们可以通过重新从服务器查询来刷新英雄列表。 刷新后的列表可能包含很多（如果不是全部的话）以前显示过的英雄。

我们知道这一点，是因为每个英雄的id没有变化。 但在 Angular 看来，它只是一个由新的对象引用构成的新列表， 它没有选择，只能清理旧列表、舍弃那些 DOM 元素，并且用新的 DOM 元素来重建一个新列表。

如果给它一个追踪函数，Angular 就可以避免这种折腾。 追踪函数告诉 Angular：我们知道两个具有相同`hero.id`的对象其实是同一个英雄。 下面就是这样一个函数：

```typescript
trackByHeroes(index: number, hero: Hero) { return hero.id; }
```

现在，把NgForTrackBy指令设置为那个追踪函数。

```html
<div *ngFor="let hero of heroes; trackBy:trackByHeroes">
  ({{hero.id}}) {{hero.fullName}}
</div>
```

追踪函数不会阻止所有 DOM 更改。 如果同一个英雄的属性变化了，Angular 就可能不得不更新DOM元素。 但是如果这个属性没有变化 —— 而且大多数时候它们不会变化 —— Angular 就能留下这些 DOM 元素。列表界面就会更加平滑，提供更好的响应。

## 属性型指令

属性型指令修改一个现有元素的外观或行为。 例如：NgModel。

### NgClass指令

注意：class绑定是添加或删除单个类的最佳途径，而NgClass指令是同时添加或移除多个 CSS 类的更好选择。

```typescript
setClasses() {
  let classes =  {
    saveable: this.canSave,      // true
    modified: !this.isUnchanged, // false
    special: this.isSpecial,     // true
  };
  return classes;
}
```
```html
<div [ngClass]="setClasses()">This div is saveable and special</div>
```

### NgStyle指令

注意：style绑定是添加或删除单个内联样式的最佳途径，而NgStyle指令是同时添加或移除多个内联样式的更好选择。

```typescript
setStyles() {
  let styles = {
    // CSS property names
    'font-style':  this.canSave     ? 'italic' : 'normal',  // italic
    'font-weight': !this.isUnchanged ? 'bold'   : 'normal',  // normal
    'font-size':   this.isSpecial    ? '24px'   : '8px',     // 24px
  };
  return styles;
}
```
```html
<div [ngStyle]="setStyles()">
  This div is italic, normal weight, and extra large (24px).
</div>
```

### NgSwitch指令

```html
<span [ngSwitch]="toeChoice">
  <span *ngSwitchCase="'Eenie'">Eenie</span>
  <span *ngSwitchCase="'Meanie'">Meanie</span>
  <span *ngSwitchCase="'Miney'">Miney</span>
  <span *ngSwitchCase="'Moe'">Moe</span>
  <span *ngSwitchDefault>other</span>
</span>
```

`ngSwitchCase`的值可以是任何类型值。

只有选中的元素才放进 DOM 中。任何时候，上例中的这些 `<span>` 中最多只有一个会出现在 DOM 中。

## `*`前缀和`<template>`

`*`前缀是一种语法糖，它会展开成`<template>`标签。

`*ngIf`的展开：

```html
<hero-detail *ngIf="currentHero" [hero]="currentHero"></hero-detail>
```

首先展开为：

```html
<hero-detail template="ngIf:currentHero" [hero]="currentHero"></hero-detail>
```

最后展开为：

```html
<template [ngIf]="currentHero">
  <hero-detail [hero]="currentHero"></hero-detail>
</template>
```

`*ngSwitch`的展开：

```html
<span [ngSwitch]="toeChoice">
  <span *ngSwitchCase="'Eenie'">Eenie</span>
  <span *ngSwitchCase="'Meanie'">Meanie</span>
  <span *ngSwitchCase="'Miney'">Miney</span>
  <span *ngSwitchCase="'Moe'">Moe</span>
  <span *ngSwitchDefault>other</span>
</span>
```

展开为：

```html
<span [ngSwitch]="toeChoice">
  <template [ngSwitchCase]="'Eenie'"><span>Eenie</span></template>
  <template [ngSwitchCase]="'Meanie'"><span>Meanie</span></template>
  <template [ngSwitchCase]="'Miney'"><span>Miney</span></template>
  <template [ngSwitchCase]="'Moe'"><span>Moe</span></template>
  <template ngSwitchDefault><span>other</span></template>
</span>
```

`*ngFor`的展开：

```html
<hero-detail *ngFor="let hero of heroes; trackBy:trackByHeroes" [hero]="hero"></hero-detail>
```

首先展开为：

```html
<hero-detail template="ngFor let hero of heroes; trackBy:trackByHeroes" [hero]="hero"></hero-detail>
```

最后展开为：

```html
<template ngFor let-hero [ngForOf]="heroes" [ngForTrackBy]="trackByHeroes">
  <hero-detail [hero]="hero"></hero-detail>
</template>
```

## 输入输出属性

输入属性通常接收数据值， 输出属性暴露事件生产者。

输入属性通常是由方括号包围，而输出属性通常由圆括号包围。

### 绑定目标和绑定源

在绑定声明中，`=`号左侧部分是绑定目标，右侧部分是绑定源。

指令中任何成员均可以出现在绑定源处，但只有显式标记为输入或输出属性的指令属性才能出现在绑定目标中。

### 声明输入和输出属性

方法一、使用装饰器`@Input`和`@Output`声明：

```typescript
@Input()  hero: Hero;
@Output() deleteRequest = new EventEmitter<Hero>();
```

方法二、在`@Component`中的`inputs`和`outputs`中列出：

```typescript
@Component({
  inputs: ['hero'],
  outputs: ['deleteRequest'],
})
```

对同一属性只能使用一种方法进行声明，不能同时使用两种方法。

### 输入和输出属性别名

有时需要让输入/输出属性的公开名字不同于内部名字。

把别名传进`@Input`/`@Output`装饰器，就可以为属性指定别名，就像这样：

```typescript
@Output('myClick') clicks = new EventEmitter<string>(); //  @Output(alias) propertyName = ...
```

也可在`inputs`和`outputs`数组中为属性指定别名。 可以写一个冒号 (`:`) 分隔的字符串，左侧是指令中的属性名，右侧则是公开的别名。

```typescript
@Directive({
  outputs: ['clicks:myClick']  // propertyName:alias
})
```



# 组件

组件负责控制屏幕上的一小块区域，我们称之为视图。

组件由 HTML 模板和组件类组成，组件类控制视图。

当用户在这个应用中漫游时， Angular 会创建、更新和销毁组件。 

## 创建组件

每个组件都以`@Component`装饰器标注：

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-heros',
  templateUrl: './heros.component.html',
  styleUrls: ['./heros.component.scss']
})
export class HerosComponent implements OnInit {
  constructor() { }
  ngOnInit() {
  }
}
```

`ngOnInit` 是一个生命周期钩子，Angular 在创建完组件后很快就会调用 `ngOnInit`。这里是放置初始化逻辑的好地方。

## 根组件

根组件由根模块的`@Component`装饰器的`bootstrap`属性指定。每个Angular应用必须至少有一个根组件，它会把组件树和页面中的 DOM 连接起来。。

## 主从组件

## 生命周期钩子

应用可以通过生命周期钩子（如ngOnInit()）在组件生命周期的各个时间点上插入自己的操作。

# 表单

# 管道

# UI组件

## Angular Material

### 安装

#### 安装 Angular Material和Angular CDK

```bash
$ npm install --save @angular/material @angular/cdk
```

然后，按需将Material中的组件导入需要的模块：

```typescript
import {MatButtonModule, MatCheckboxModule} from '@angular/material';

@NgModule({
  ...
  imports: [MatButtonModule, MatCheckboxModule],
  ...
})
export class PizzaPartyAppModule { }
```

> 注意：Material模块的导入要放在BrowerModule模块导入之后。

#### 安装 Animations

某些Material组件依赖于 Angular animations。（使用angular-cli，默认已经安装。）

```bash
$ npm install --save @angular/animations
```

>  `@angular/animations` 使用了仍未被所有浏览器支持的 WebAnimation API，如果想在这些浏览器上支持Material组件的动画，需要包含一个[ployfill](https://github.com/web-animations/web-animations-js)：
>
>  ```html
>  <script src="web-animations.min.js"></script>
>  ```
>
>  如果是使用angular-cli，则首先运行下列命令安装web-animations-js：
>
>  ```bash
>  $ npm install --save web-animations-js
>  ```
>
>  然后，打开`src/polyfills.ts`，添加如下代码：
>
>  ```typescript
>  import 'web-animations-js';
>  ```

然后，将Animations导入需要的模块：

```typescript
import {BrowserAnimationsModule} from '@angular/platform-browser/animations';

@NgModule({
  ...
  imports: [BrowserAnimationsModule],
  ...
})
export class PizzaPartyAppModule { }
```

如果不希望将依赖加入项目，则使用 `NoopAnimationsModule`：

```typescript
import {NoopAnimationsModule} from '@angular/platform-browser/animations';

@NgModule({
  ...
  imports: [NoopAnimationsModule],
  ...
})
export class PizzaPartyAppModule { }
```

#### 导入主题

打开`src/styles.scss`，添加如下代码：

```scss
@import "~@angular/material/prebuilt-themes/indigo-pink.css";
```

如果没使用angular-cli，则需要在`index.html`中使用`<link>`来加载上面的CSS文件：

```html
<link href="node_modules/@angular/material/prebuilt-themes/indigo-pink.css" rel="stylesheet">
```



#### 添加手势支持

```bash
$ npm install --save hammerjs
```

然后，在应用程序入口点（即`src/main.ts`）中，添加如下代码：

```typescript
import 'hammerjs';
```

#### 添加Material图标支持（可选）

为了让`mat-icon`组件使用上 [Material Design Icons](https://material.io/icons/)，可以添加Material图标支持。

> 注意：`mat-icon`可以支持任意字体或svg图标，Material Icons只是它的一个选项之一。

##### 方法一：

打开`index.html`，添加：

```html
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

##### 方法二：

安装Material Icon：

```bash
npm install material-design-icons --save
```

在`src/styles.scss`中加入：

```scss
@import '~material-design-icons/iconfont/material-icons.css';
```

##### 使用Material图标

```html
<i class="material-icons">face<i>
```

> `<i>`标签体的内容就是图标的名称

如果浏览器不支持 [ligatures](http://alistapart.com/article/the-era-of-symbol-fonts)，则使用数字字符引用来指定图标：

```html
<i class="material-icons">&#xE87C;</i>
```

##### 使用其他图标集

例如，

1. 可以在<http://www.iconfont.cn/>上新建一个项目。然后，找到自己想要的图标加入该项目，下载项目，重命名为`iconfont`。

2. 将`iconfont`文件夹复制到项目的`assets`文件夹下。

3. 在`src/styles.scss`中导入自定义图标：

   ```scss
    @import 'assets/iconfont/iconfont.css';
   ```

   

## 使用Bootstrap

### 安装Bootstrap

```bash
$ npm install bootstrap --save
```

> Bootstrap 4之后，不管是使用css还是使用scss，都是这样安装bootstrap。
>
> 本文使用Bootstrap 4.1.0

### 配置

#### 使用CSS

修改`angular.json`文件中的`apps`下的`styles`：

```json
"styles": [
  "../node_modules/bootstrap/dist/css/bootstrap.min.css",
  "styles.css"
],
```

它使得，外部的全局样式能够应用到项目中。

> 修改了`angular.json`文件后，必须重启项目（`ng serve`）。

#### 使用SCSS

在`src/`目录下创建一个`_variables.scss`文件。

在`styles.scss`中添加如下代码：

```scss
@import 'variables';
@import '../node_modules/bootstrap/scss/bootstrap';
```

# 动画

# 服务

## 创建服务

使用 Angular CLI 创建一个名叫 `hero` 的服务：

```bash
$ cd my-app
$ ng generate service hero
```

上面的命令将在`my-app/src/app/`目录下直接生成下面两个文件：

- `hero.service.ts`
- `hero.service.spec.ts`

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class HeroService {
  constructor() { }
}
```

如果上面的命令不是在项目根目录下执行，例如在`my-app/src/app/service/`下执行，则会在该目录下生成上述两个文件。

如果希望在单独的目录中生成上述两个文件，则可以加上`--flat=false`选项：

```bash
$ ng g service hero --flat=false
```

如果在项目根目录下执行上面命令，则会在`my-app/src/app/hero/`目录下生成源文件；如果上面的命令不是在项目根目录下执行，例如在`my-app/src/app/service/`下执行，则会在`my-app/src/app/service/hero/`目录下生成源文件。

## 将服务注册为提供者

在Angular 6+中，创建服务时，可以通过`providedIn`属性指定使用什么注入器将服务注册为提供者。例如，本例中的`providedIn: root`表示用*根注入器*将服务注册成为提供商。

在Angular 6之前，创建服务时，没有`providedIn`属性：

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class HeroService {
  constructor() { }
}
```

这时，注册提供商要通过`@NgModule`或`@Component`的`providers`属性来设置（在`@Component`中注册的提供者，只在当前组件及它的子组件中可见。）：

```typescript
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
  providers: [HeroService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

当你在顶层提供该服务时，Angular 就会为 `HeroService` 创建一个单一的、共享的实例，并把它注入到任何想要它的类上。 另外，在 `@Injectable` 元数据中注册该提供商，还能允许 Angular 通过移除那些完全没有用过的服务来进行优化。

## 使用json-server+faker+jsonwebtoken模拟后端服务

json-server可以利用JSON数据或JavaScript代码快速创建Web服务。

faker用于帮助生成各种JSON数据。

jsonwebtoken是JWT的一个实现。

### 安装

```bash
$ npm install --save-dev json-server
$ npm install --save-dev jsonwebtoken
$ npm install --save-dev faker
```

### 添加启动脚本

package.json：

```json
{
  …
  "scripts": {
  	…
    "json": "json-server data.js -p 3500 -m authMiddleware.js"
  },
  …
}
```

### 准备静态数据

#### 通过JSON提供静态数据

#### 通过JavaScript代码提供静态数据

data.js：

```javascript
module.exports = function () {
  return {
    products: [
      { id: 1, name: "Kayak", category: "Watersports",
        description: "A boat for one person", price: 275 },
      { id: 2, name: "Lifejacket", category: "Watersports",
        description: "Protective and fashionable", price: 48.95 },
      …
    ],
    orders: […]
  }
}
```

### 提供身份验证和授权过程

authMiddleware.js：

```javascript
const jwt = require("jsonwebtoken");
const APP_SECRET = "myappsecret";
const USERNAME = "admin";
const PASSWORD = "secret";
module.exports = function (req, res, next) {
  if ((req.url == "/api/login" || req.url == "/login") && req.method == "POST") {
    if (req.body != null && req.body.name == USERNAME
        && req.body.password == PASSWORD) {
      let token = jwt.sign({ data: USERNAME, expiresIn: "1h" }, APP_SECRET);
      res.json({ success: true, token: token });
    } else {
      res.json({ success: false });
    }
    res.end();
    return;
  } else if (
    ((
      (req.url.startsWith("/api/products") || req.url.startsWith("/products"))
      || (req.url.startsWith("/api/categories") || req.url.startsWith("/categories"))
    ) && req.method != "GET")
    || ((req.url.startsWith("/api/orders") || req.url.startsWith("/orders")) 
        && req.method != "POST")) {
    let token = req.headers["authorization"];
    if (token != null && token.startsWith("Bearer<")) {
      token = token.substring(7, token.length - 1);
      try {
        jwt.verify(token, APP_SECRET);
        next();
        return;
      } catch (err) { }
    }
    res.statusCode = 401;
    res.end();
    return;
  }
  next();
}
```

### 启动json-server

```bash
$ npm run json
```



# Reactive Extensions

# 异步HTTP请求

# 测试

# Linter

linter是一种检查源代码的工具，以确保它符合一组编码约定和规则。使用ng new命令创建的项目包括一个名为TSLint的TypeScript linter，它支持的规则在https://github.com/palantir/tslint中描述，涵盖了可能导致意外结果的常见错误以及样式。您可以在`tslint.json`文件中启用和禁用linting规则。

可以通过运行下列命令，来使用TSLint对源代码进行检查：

```bash
$ ng lint
```

如果要对某行代码禁用linter检查，则只需要在该行代码的上一行插入如下注释：

```typescript
// tslint:disable-next-line
```

如果要对整个文件禁用linter检查，则在文件的开头加上如下注释：

```typescript
/* tslint:disable */
```

# 构建

Angular有一种生产模式，可以禁用在开发过程中执行的一些有用的检查。启用生产模式意味着提高性能，通过调用`enableProdMode`函数启用生产模式。

# 部署