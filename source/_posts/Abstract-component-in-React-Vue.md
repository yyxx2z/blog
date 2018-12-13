---
title: Abstract component in React/Vue
date: 2018-12-07 15:11:35
categories:
- React/Vue
tags:
- Javascript
- Vue
- React
---

从 SPA 时代开始前端不再只是 view 的承载，开始有了自己的业务逻辑。伴随着重复代码量的增加对 UI 和逻辑处理封装成独立的组件已经成为一个必然的趋势：低耦合高内聚，提高代码重用率，可单独测试，以及对多人团队协作效率的大大提高。React/Vue/Angular 的出现使我们不用了解如何去对 HTML CSS JS 分别单独进行组件化就能实现可复用组件。

而在开发时，我们容易对组件的抽象和设计有一个暧昧的判断。分割不清抽象组件时机，组件耦合性高，组件分割混乱无规范性。

<!-- more -->

# 抽象组件原则
**[单一功能原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)**

抽象组件类似于函数式编程，没有外部依赖，在同一数据流下进行相同的行为反馈相同的表现。


独立性：组件应该是引入即用的，以微信小程序中写授权弹窗为例，在应用加载后不应该依赖于当前加载页面是否执行了授权状态查询而进行展示，而是应该将授权查询封装进组件，使组件的功能保持完整性和独立性。

而在 Vue 和 React 中，由于数据流和设计的不同，我们将分开介绍何时适合抽象组件。

# React
#### 组件抽象流程
根据[React理念](https://react.docschina.org/docs/thinking-in-react.html)介绍，在 React 中抽象组件可大致分为以下步骤：
1. 对 UI 进行层级划分
2. 创建静态版本
3. 定义组件的最小完整表示
4. 提升 state
5. 添加反向数据流

#### 根据数据构成进行组件划分
在对页面 UI 进行层级划分后，我们可以对一些数据构成相同但是 UI 表现略有不同的组件归类为一个组件。如图两个 article-card ，虽然 UI 表现不一致，但是大都是由 banner + title + describe 等元素构成，部分不同可以通过 props 传入组件表现类型去控制部分元素的显隐和使用不同表现形式的 className 。

{% asset_img ui-example.png This is an example image %}

这样不仅能提高小组件的复用率和功能性，还能减少很多表现和功能类似的组件。

#### 容器组件和展示组件
即使你没有使用 Redux 进行你的 React 项目开发， 也可以参考 [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) 的组件划分思想。
文中指出将 React 开发时的组件分为两种：
* 容器组件：进行组件状态和数据的更新，以及下发 props，用于描述组件的运行逻辑。
* 展示组件：组件 UI 的展示，包含组件的样式和元素构成，通过接收 props 进行渲染，通过触发 props 的回调函数进行数据修改。

这种做法可以让你在开发应用时对组件功能有更清晰的划分，对数据和事件管理有清晰的层级，使你更好地理解应用。同时也能增强代码的可读性和维护性，别人在 review 代码时能够看到一个清晰的数据流和事件流，使你的应用构成更加优雅 (/// v ///)

And 很大提高了组件的复用性，当一个组件变成纯粹的待数据填充的**展示骨架**， 那么他就可以适用于不同数据源的父组件和处理不同事件表现，因为此时展示组件没有自身的数据和修改数据的事件处理函数，一切都交给包含它的父容器组件去处理。


# Vue
#### 组件抽象流程
1. 对 UI 进行块和功能划分
  对 UI 进行功能的划分，如侧边栏和导航栏
2. 分化时确保该组件的复用率，提高可维护性
  不需要每有稍微重复的地方就去分化组件(虽然这样很爽看上去使代码'感觉'很简洁优雅)
  假如我们要写两个用户列表渲染，一个 admin 用户不带 delete 按钮， 一个普通用户带 delete 按钮
  ```
    <div class="list-wrapper">
      <ul class="list admin-list">
        <li 
          class="list-item" 
          v-for="item in adminList" 
          :key="" 
        >
          <span class="item-id">{{item.id}}</span>
          <span>{{item.username}}<span>
        </li>
      <ul>

      <ul class="list user-list">
        <li 
          class="list-item" 
          v-for="item in userList" 
          :key="" 
        >
          <span class="item-id">{{item.id}}</span>
          <span>{{item.username}}<span>
          <button class="interaction-button" @click="delete(item.id)" >删除</button>
        </li>
      <ul>
    </div>
  ```
  如果对其进行抽象组件：
  ```
    <div class="list-wrapper">
      <UsersList 
        :list="adminList"
        :type="admin" />

      <UsersList 
        :list="userList"
        @deleteItem="delete" 
        :type="normal" />
    </div>
  ```
  这样是不是很优雅简洁呀！可是当你的 vue 页面都是这样一大堆组件去构成时，或者是像上面 React 介绍的容器组件和展示组件去写 vue 组件时，虽然你的页面构成简洁清晰了，但是当别人需要维护**只在一个页面或者一个块少量使用的组件**时，就会导致他得多次打开和寻找你的组件依赖文件，虽然你在写的时候爽了，但却增加了维护的工作量。

3. 对组件进行独立完整地封装

# 总结
React 和 Vue 中划分组件有所不同。
React 是以拆分最小单元为目的进行组件划分，提升 state 使得组件数据通过 props 单向传递更加优雅。
Vue 是以功能划分为主，在保证可维护性的情况下分化出复用率高的组件。组件以业务目的为主要划分。