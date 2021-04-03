# Vue@cli3配置文件引入cdn加速

## 概述
省去在 index.html一个一个引入 js 和 cs 文件，直接在配置文件引入。
## 过程
1. 安装插件 html-webpack-plugin
```shell
npm install --save-dev html-webpack-plugin
```
2. 在 package.json 同级目录新建 vue.config.js
3. 引入插件
```shell
const HtmlWebpackPlugin = require('html-webpack-plugin')
```
4. 设置要排除的引入
```shell
const externals = {
  vue: 'Vue',
  'vue-router': 'VueRouter',
  vuex: 'Vuex',
  axios: 'axios'
}
const cdn = {
  // 开发环境
  dev: {
    css: [

    ],
    js: [

    ]
  },
  // 生产环境
  build: {
    css: [

    ],
    js: [
      'https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.min.js',
      'https://cdn.bootcss.com/vue-router/3.0.3/vue-router.min.js',
      'https://cdn.bootcss.com/vuex/3.0.1/vuex.min.js',
      'https://cdn.bootcss.com/axios/0.19.0/axios.min.js',
    ]
  }
}
```
5.两种方式配置
方式1
```shell
module.exports = {
configureWebpack: config => {
    const myConfig = {}
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
        //externals里的模块不打包
        // Object.assign(config, {
        //   externals: externals
        // })
        myConfig.externals  = externals
    } else {
      // 为开发环境修改配置...
      myConfig.devServer = {
        disableHostCheck: true
      }
    }
    return myConfig
  },
  chainWebpack: config => {
    // 对vue-cli内部的 webpack 配置进行更细粒度的修改
    config.plugin('html').tap(args => {
        if (process.env.NODE_ENV === 'production') {
            args[0].cdn = cdn.build
        }
        if (process.env.NODE_ENV === 'development') {
            args[0].cdn = cdn.dev
        }
        return args
    })
  }
```
方式2（更简单一些）
```shell
module.exports = {
configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
        //externals里的模块不打包
         Object.assign(config, {
           externals: externals
         })
    } else {
      // 为开发环境修改配置...
    }
  },
  chainWebpack: config => {
    // 对vue-cli内部的 webpack 配置进行更细粒度的修改
    config.plugin('html').tap(args => {
        if (process.env.NODE_ENV === 'production') {
            args[0].cdn = cdn.build
        }
        if (process.env.NODE_ENV === 'development') {
            args[0].cdn = cdn.dev
        }
        return args
    })
  }
```
6. 在 public/index.html 遍历js，css

```html

 
  <!-- 使用CDN加速的CSS文件，配置在vue.config.js下 -->
  <% for (var i in
        htmlWebpackPlugin.options.cdn&&htmlWebpackPlugin.options.cdn.css) { %>
  <link href="<%= htmlWebpackPlugin.options.cdn.css[i] %>" rel="preload" as="style" />
  <link href="<%= htmlWebpackPlugin.options.cdn.css[i] %>" rel="stylesheet" />
 
  <% } %>
   <!-- <script src="https://cdn.bootcss.com/vue/2.6.11/vue.min.js"></script> -->
  <!-- 使用CDN加速的JS文件，配置在vue.config.js下 -->
  <% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.js) { %>
    <script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>" rel="preload" ></script>
   <% } %>
</head>

<body>
  <noscript>
    <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled.
      Please enable it to continue.</strong>
  </noscript>
  <div id="app"></div>
  <!-- built files will be auto injected -->
</body>
<!-- 使用CDN加速的JS文件，配置在vue.config.js下 -->
<!-- <% for (var i in
   htmlWebpackPlugin.options.cdn&&htmlWebpackPlugin.options.cdn.js) { %>
<script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>"></script>
<% } %> -->
 <!-- built files will be auto injected -->

```
