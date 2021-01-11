[Webpack官网](https://www.webpackjs.com/concepts/)

# 分离打包
    webpack.dll.config.js	-- 3 part lib,not always change
    webpack.config.js		-- own code,change always

webpack-dev-server
将打包后代码放入内存，不会生成dist目录
webpack-hot-middleware
程序运行时，替换、添加、删除模块，而无需重新加载整个页面；
保留在完全重新加载页面时丢失的应用程序状态
只更新变更内容
调整样式更加快速	-- 相当于在浏览器调试器中更改样式


开发中将HMR作为LiveReload的替代