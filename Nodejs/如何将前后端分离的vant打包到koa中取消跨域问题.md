# 场景描述
    前端项目使用的vant框架，后端项目使用的koa框架，为提高开发效率，充分发挥出开发构建工具的效率，采用前后端分离的开发方式。开发环境，单独启前端项目；生产环境，为取消跨域问题，将前端项目构建后的文件以静态资源的形式部署到后端项目。
## 项目目录结构
+ emdata-weixin-patient
  + emdata-weixin-patient - Koa后端
    + public - 静态资源
      + vant - 前端构建包
  + emdata-weixin-patient-vant - Vant前端
## 前端构建部署脚本
```shell
    yarn
    yarn build

    rm -rf ../emdata-weixin-patient/public/vant
    cp -R ./dist ../emdata-weixin-patient/public/vant
```
## 环境配置
### 开发环境
    特别注意，开发环境中clientUrl，用的绝对路径，即独立运行的前端vant的请求地址，且不存在vant虚拟目录；生产环境中clientUrl，用的相对路径，即后端koa的请求地址，加上部署时追加的虚拟目录“vant”
```javascript
appInfo: {
    name: 'weixin_dev',
    clientUrl: 'http://127.0.0.1:8081',
}
```
### 生产环境
```javascript
appInfo: {
    name: 'weixin',
    clientUrl: '/vant',
}
```
## 对外提供固定的请求路径
```javascript
router.get('/advise/event.html', async function (ctx, next) {
    var id = ctx.query.id;
    ctx.redirect(appInfo.clientUrl + '/#/event?id=' + id)
});
```