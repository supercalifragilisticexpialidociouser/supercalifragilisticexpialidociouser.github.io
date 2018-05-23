---
title: Angular
date: 2018-04-26 10:34:40
tags: [6.0.0]
---

# 准备开发环境

## 安装Node.js

下载安装Node.js 10.0.0

## 安装angular-cli

angular-cli是一个命令行工具，可用于创建和管理Angular项目。

安装：

```bash
$ npm install -g @angular/cli
```

查看版本：`ng version`；

查看帮助：`ng help`；

查看某个子命令的帮助：`ng new --help`或`ng help new`。

### 升级angular-cli

v6.0.1

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

v2.17.0

<!--more-->

# 入门

## 创建项目

```bash
$ ng new myng --style=scss
```

> `--style=scss`表示使用scss，默认使用css。

上面的命令执行时，会自动安装依赖，不需要再手动执行`npm install`。如果需要在创建项目时，略过安装依赖，则可以执行：`ng new myng --style=scss --skip-install`。

项目的源码放在`myng/src`目录中，可以使用`--source-dir`指定自己的源码目录名，默认为`src`。

如果希望在项目中使用路由，则可以加上`--routing`选项。

## 启动服务器

```bash
$ ng serve --open
```

> `--open`或`-o`表示在服务器启动后，自动打开浏览器并访问 <http://localhost:4200/>。

默认的端口号是`4200`，要指定其他端口号，使用`--port`参数。另外，使用`--host`指定ip：

```bash
$ ng serve --open --port 3000 --host 0.0.0.0
```

## 编码

angular-cli的HTTP开发服务器在向浏览器发送的HTML内容中添加了一个JavaScript片段。JavaScript打开一个回连到服务器的连接，并等待信号重新加载页面。当服务器检测到项目目录中的任何文件发生变更时，就会发送信号。因此，新增或修改代码不需要手动重启开发服务器。

在运行复杂项目时，有时可能无法重新加载应用，这时只需单击浏览器的刷新按钮或导航到 <http://localhost:4200/>即可。

使用angular-cli方式创建项目，不需要手动将需要的css、js等资源（包括第三方资源）添加到`index.html`中。angular-cli使用了WebPack工具，会自动生成项目的css、javascript文件，并将它们自动注入HTTP开发服务器发送给浏览器的HTML文件中。

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

如果Angular应用要在非浏览器环境（例如Ionic移动开发框架）中运行，则需要使用引导文件中的代码来选择要使用的平台，并加载根模块。`platformBrowserDynamic().bootstrapModule()`是适合基于浏览器的应用的引导方法。

### 主页

主页默认是`src/index.html`，是别人访问你的网站是看到的主页面的 HTML 文件。 大多数情况下你都不用编辑它。 在构建应用时，CLI 会自动把所有 `js` 和 `css` 文件添加进去，所以你不必在这里手动添加任何 `<script>` 或 `<link>` 标签。

### 模板

模板是指包含由Angular执行的指令的HTML片段，它用于向用户展示模型中的数据值。

例如：`src/app/app.component.html`。

### 组件

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
  title = 'app';
}
```

`selector`告诉Angular如何在HTML文档中应用该组件。

`templateUrl`或`template`定义了组件将显示的内容的模板。其中，前者指定模板文件的位置，后者直接定义内联模板。

要创建组件，可以使用命令：

```bash
$ cd myng
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
> 并且该组件将隶属于根模块。
>
> 可以加上`-m`或`--module`参数来指定隶属的模块：
>
> ```bash
> $ cd myng
> $ ng generate component xxx --module modx
> ```
>
> 上面代码将生成如下文件：
>
> - src/app/xxx/xxx.component.html
> - src/app/xxx/xxx.component.spec.ts
> - src/app/xxx/xxx.component.ts
> - src/app/xxx/xxx.component.scss
>
> 并且该组件将隶属于`modx`模块。注意：组件仍然生成在自己的目录中。
>
> 参数`--flat`指示是否创建自己的目录，默认是`false`，即创建自己的目录：
>
> ```bash
> $ cd myng
> $ ng generate component xxx --module modx --flat
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
> $ cd myng/src/app/modx
> $ ng generate component xxx --module modx --flat
> ```
>
> 生成如下文件：
>
> - src/app/modx/xxx.component.html
> - src/app/modx/xxx.component.spec.ts
> - src/app/modx/xxx.component.ts
> - src/app/modx/xxx.component.scss
>
> 如果组件名与模块名相同，则：
>
> ```bash
> $ cd myng
> $ ng generate component modx --module modx
> ```
>
> 生成如下文件：
>
> - src/app/modx/modx.component.html
> - src/app/modx/modx.component.spec.ts
> - src/app/modx/modx.component.ts
> - src/app/modx/modx.component.scss

### 样式

Angular应用的全局样式放在`src/styles.scss`中。另外，每个组件可以有自己的私有样式，它们放在由`styleUrls`指定的样式文件中。

### 模块

Angular模块将相关功能封装起来，每个应用都至少有一个根模块。

例如`src/app/app.module.ts`：

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

创建模块：

```bash
$ cd myng
$ ng g module modx
```

> 上面命令将生成：`src/app/modx/modx.module.ts`。

# 模块

Angular开发中使用了两种类型的模块：

- JavaScript模块：一个.js文件就是一个模块。通过`export`导出变量，通过`import`导入变量。
- Angular模块：用于描述应用或一组相关功能。每个应用都有一个根模块（Root module），它为Angular提供启动应用所需的信息。Angular模块通过`@NgModule`装饰器来声明。

## Angular模块

Angular模块有两种类型：

- 根模块（root module）：用于向Angular描述应用程序，主要包括：运行应用程序所需的功能模块、应该加载哪些自定义功能以及根组件的名称。
- 功能模块（feature module）：用于把相关的应用程序功能归集起来，使应用程序更易于管理。

# 路由

告诉Angular每个URL映射到组件的映射称为URL路由，简称路由。

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

当使用路由功能时，`<router-outlet>`元素相当于一个占位符，它指定了当前URL对应的组件的显示位置。

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
@NgModule({
  imports: [RouterModule, …],
  …
})
```

# 数据绑定

# 模板

# 指令

# 组件



## 根组件

根组件由根模块的`@Component`装饰器的`bootstrap`属性指定。

# 表单

# 管道

# UI

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

修改`.angular-cli.json`文件中的`apps`下的`styles`：

```json
"styles": [
  "../node_modules/bootstrap/dist/css/bootstrap.min.css",
  "styles.css"
],
```

它使得，外部的全局样式能够应用到项目中。

> 修改了`.angular-cli.json`文件后，必须重启项目（`ng serve`）。

#### 使用SCSS

在`src/`目录下创建一个`_variables.scss`文件。

在`styles.scss`中添加如下代码：

```scss
@import 'variables';
@import '../node_modules/bootstrap/scss/bootstrap';
```

## 动画

# 服务

## 使用json-server+faker+jsonwebtoke模拟后端服务

# Reactive Extensions

# 异步HTTP请求

# 测试