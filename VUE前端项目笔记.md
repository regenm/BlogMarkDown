---
title: VUE前端项目笔记
date: 2024-09-26 11:50:51
tags: [javascript,html,vue,前端,笔记]
categories: [软件技术]
---

项目地址：[creativetimofficial/vue-black-dashboard: Vue Black Dashboard (github.com)](https://github.com/creativetimofficial/vue-black-dashboard)

登录注册界面比较容易写，接下来试试登录后的管理界面以及展示界面。即 **DashBoard**

说是实战其实是消化理解一下大佬的项目。

看了一会才发现是纯前端，没有连接后端的代码。

###  i18n

> i18n 是指在软件、应用程序或网站开发过程中，通过适当的设计和编程，使其能够方便地支持不同的语言、地区和文化规范，而无需对代码进行大量修改。i18n 是实现全球化（globalization，g11n）的重要组成部分。

# 使用项目接口为自己的项目服务

```javascript
import Vue from 'vue';
import DashboardPlugin from '@/plugins/blackDashboard'
Vue.use(DashboardPlugin);
```



# AXIOS

基于 Promise 的 HTTP 客户端，可以用在浏览器和 Node.js 中。适合用于 Vue 项目中进行 API 请求。

