# 虚拟DOM
    VNode，从Vue2.0引入虚拟DOM
# 问题
    Web早期，静态页面，对DOM操作少
    现在功能越来越丰富，要满足的需求越来越复杂，对DOM操作频繁

    js操作DOM性能代价很高，因为会引起页面重排重绘，增加浏览器性能开销，降低页面渲染速度。如何才能减少对dom操作？

# 方案
    提出virtual dom 节点描述对象，对真实DOM的抽象，描述怎么创建真实的DOM节点
    VNode 一颗以js对象为基础的树

    template -> view

    render vdom
    update  oldVdom vdom patch 差异比对

    减少对DOM操作，从而减少浏览器开销，提高渲染速度，改善用户体验


# 参考链接
    [简书](https://www.jianshu.com/p/4dbb3712ced7)