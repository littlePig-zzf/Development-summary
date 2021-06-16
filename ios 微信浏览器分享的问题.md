**微信浏览器 ios分享页面问题：**



**前提**：单页面项目，使用history路由跳转模式。



**背景：**在ios系统下，单页面系统 通过pushState、repkaceState跳转页面的，地址栏的地址不会改变，一直都是初始化进来的页面地址。

但是不是全部ios系统都这样，估计看ios版本，微信版本。



**原因：**ios部分手机，微信浏览器并不兼容pushState、replaceState的html5方法，因此在地址栏并没有更新url。 



**问题**：

1、只要不是第一个页面，然后使用微信分享的，都会出现分享的链接不是当前页面的链接，而是第一次进入的链接。

2、会出现获取微信签名失败，同一个url只需调用一次微信签名，跳转页面之后，都要重新调用微信签名。但ios里，微信浏览器第2个页面以上，都是获取到初始化的页面，因此无法正确的获取微信签名。调用微信接口出现 permission deny



**解决方案：**监听路由，在路由钩子进行页面刷新，使用location.assign(当前页面)、location.href(当前页面)，刷新页面url

（assign、href区别：assign会在history记录中增加，可以返回上一页，）



**副作用**：会导致一些vue的生命周期没法触发。

参考地址：https://www.cnblogs.com/cfcastiel/articles/14470440.html

