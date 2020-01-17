# 打包优化

* 前提了解多环境配置

## 资源 CDN 加载 [(外部扩展)][1]

### 根据不同配置文件,确认是否进行打包优化

#### 开发环境

 ```text
 NODE_ENV='development'
 ```

#### 线上环境(测试/预生产/生产)

 ```text
 NODE_ENV='production'
 ```

#### `vue.config.js` 配置

* 进行打包环境判断

 ```javascript
 // vue.config.js
 let {
   NODE_ENV
 } = process.env
 const isProduction = NODE_ENV === 'production'
 ```

* 加载资源配置

 ```javascript
 // vue.config.js
 let cdn = {
   js: [
     '//cdn.bootcss.com/vue/2.6.10/vue.runtime.min.js',
     '//cdn.bootcss.com/vue-router/3.1.3/vue-router.min.js',
     '//cdn.bootcss.com/vuex/3.1.1/vuex.min.js',
     '//cdn.bootcss.com/axios/0.18.1/axios.min.js',
     '//cdn.bootcss.com/iview/3.5.1/iview.min.js'
   ]
 }
 ```

* `chainWebpack`配置

 ```javascript
 // vue.config.js
 module.exports = {
   chainWebpack: config => {
     if (isProduction) {
       config.plugin('html').tap(args => {
         args[0].cdn = cdn
         return args
       })
     }
   }
 }
 ```

* `configureWebpack`配置 [参考文章][2]

 ```javascript
 // vue.config.js
 module.exports = {
   if (isProduction) {
      // 用cdn方式引入
      config.externals = {
        'vue': 'Vue',
        'vuex': 'Vuex',
        'vue-router': 'VueRouter',
        'axios': 'axios',
        'iview': 'iview'
      }
    }
 }
 ```

#### `index.html` 配置

```html
// public/index.html
<head>
 <!-- 使用CDN加速的JS文件，配置在vue.config.js下 link 引入 js 是进行 js 的预加载 -->
 <% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.js) { %>
 <link href="<%= htmlWebpackPlugin.options.cdn.js[i] %>" rel="preload" as="script">
 <% } %>
</head>
<body>

 ...

 <!-- 使用CDN加速的JS文件，配置在vue.config.js下 -->
 <% for (var i in htmlWebpackPlugin.options.cdn&&htmlWebpackPlugin.options.cdn.js) { %>
 <script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>"></script>
 <% } %>
</body>
```

[1]:(https://webpack.docschina.org/configuration/externals/)
[2]:(https://www.tangshuang.net/3343.html)
