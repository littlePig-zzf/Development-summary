**微信浏览器 ios分享页面问题、ios调用微信api报 invalid signature问题：**



**前提**：单页面项目，使用history路由跳转模式。



**背景：**在ios系统下，单页面系统 通过pushState、replaceState跳转页面的，地址栏的地址不会改变，一直都是初始化进来的页面地址。

但是不是全部ios系统都这样，估计看ios版本，微信版本。



**原因：**ios部分手机，微信浏览器并不兼容pushState、replaceState的html5方法，因此在地址栏并没有更新url。 



**问题**：

1、只要不是第一个页面，然后使用微信分享的，都会出现分享的链接不是当前页面的链接，而是第一次进入的链接。

2、会出现获取微信签名失败，同一个url只需调用一次微信签名，跳转页面之后，都要重新调用微信签名。发起签名请求的url参数必须是当前页面的url。

但ios里，微信浏览器不管跳转了几个页面， 真实的url其实还是初始化的页面url，并不是当前路由的url, 因此无法正确的获取微信签名。调用微信接口出现 permission deny



**解决方案：**

1、监听路由，在路由钩子进行页面刷新，使用location.assign(当前页面)、location.replace(当前页面)，刷新页面url。（完胜的一种方法，location.replace）

```
 beforeRouteEnter: ((to, from, next) => {
       if (!to.query.indexRefresh) {
            let refreshPath = '';
            refreshPath = to.fullPath.indexOf('?') === -1 ? '?indexRefresh=1' : '&indexRefresh=1';
            location.replace(`${to.fullPath}${refreshPath}`)
        }
 }

```



2、初始化页面的时候保存第一次进来页面的地址，然后使用这个初始化地址 在不同的页面路由下 去获取微信签名 （有些情况是没有用的）并不是完胜的一种方法。

```
// 用于在微信浏览器 ios获取签名始终都是获取第一次进入的地址 firstUrl，由于ios单页面不会刷新地址
if (!localStorage.getItem('firstUrl')) {
    localStorage.setItem('firstUrl', window.location.href);
}

获取签名的方法：
request({
    url: '/management-system/api/v1/wechat/signature',
    params: {
        url:
            checkSystemType() !== 'ios'
                ? window.location.href
                : localStorage.getItem('firstUrl') ||
                  'https://jkb.company.com/physical/',
        source: Configs.source,
    },
    method: 'post',
    json: true,
});

ios情况下，使用第一次进入页面的url去调用签名，安卓则不需要
```



3、ios中，在入口函数中就要获取签名，而不要在调用方法的时候，才获取签名。（可通过路由判断的方式，到某个页面才调用签名）（有些情况是没有用的）并不是完胜的一种方法。

```
router.beforeEach(async (to, from, next) => {
	 // 针对某个页面，微信jssdk配置-处理ios调用定位报签名错误
    if (to.name === 'addressOperation' && checkSystemType() === 'ios') {
        bridge.config();
    }
})

封装的获取微信定位的方法
async getLocation(callBack, getListBack, needJudge) {
    if (checkSystemType() !== 'ios' || !needJudge) {
        await bridge.config(); //  调用 wx.config({})方法
    }
    await bridge.ready(); // 调用 wx.ready()方法
    wx.getLocation({
        type: 'wgs84', // 默认为wgs84的gps坐标，如果要返回直接给openLocation用的火星坐标，可传入'gcj02'
        success: function (res) { },
        fail: err => { },
        cancel : function (err) { },
        complete: function(res){ }
    });

},
```



