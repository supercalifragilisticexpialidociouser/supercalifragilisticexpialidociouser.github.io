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

`bootstrap`属性指定了当该模块引导时需要进行引导的组件。列在这里的所有组件都会自动添加到 `entryComponents` 属性中。根模块的`bootstrap`属性通常用于指定根组件。

`import`属性导入其他Angular模块的可声明对象，使得它们在当前Angular模块的**模板**中可用。

`providers`属性指定了在当前模块的注入器中可用的一组可注入对象。

创建模块：

```bash
$ cd my-app
$ ng g module home
```

> 上面命令将生成：`src/app/home/home.module.ts`。

# 模块

Angular开发中使用了两种类型的模块：

- JavaScript模块：一个.js文件就是一个模块。通过`export`导出变量，通过`import`导入变量。
- Angular模块：用于描述应用或一组相关功能。每个应用都有一个根模块（Root module），它为Angular提供启动应用所需的信息。Angular模块通过`@NgModule`装饰器来声明。Angular模块的目的是通过`@NgModule`装饰器定义的属性来提供配置信息。

## Angular模块

Angular模块有两种类型：

- 根模块（root module）：用于向Angular描述应用程序，主要包括：运行应用程序所需的功能模块、应该加载哪些自定义功能以及根组件的名称。根模块在引导文件中加载。
- 功能模块（feature module）：用于把相关的应用程序功能归集起来，使应用程序更易于管理。

## 创建Angular模块

```bash
$ ng generate module app-routing --flat --module=app
```

> `--flat` 把这个文件放进了 `src/app` 中，而不是单独的目录中。
>
> `--module=app` 告诉 CLI 把它注册到 `AppModule` 的 `imports` 数组中。

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

## 守卫路由

# 数据绑定

# 模板

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

## 模板的应用方式

### 内联模板

在创建组件时，可以通过`template`属性直接指定内联模板。

内联模板是由一个字符串或模板字符串（使用 “`” 包围）表示的模板。

### 模板文件

在创建组件时，也可以通过`templateUrl`属性来指定一个外部模板文件。

# 指令

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

根组件由根模块的`@Component`装饰器的`bootstrap`属性指定。Angular应用必须至少有一个根组件。

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

   ​

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

这时，注册提供商要通过`@NgModule`的`providers`属性来设置：

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