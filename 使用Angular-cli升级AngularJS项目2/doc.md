# 使用Angular cli升级AngularJS项目——（二）同步混合应用路由的状态  
## 前言  
都9102年了，笔者所在的公司的主要项目还是用AngularJS 1.6这种史诗的框架进行开发的。另外由于历史的原因，代码的凌乱程度早已超越想象。为此，笔者决定痛下决心把整个项目重构了一遍...从此踏上了Angular升（跳）级（坑）之路。  

### 系列文章
1. [（一）引导运行混合应用](/使用Angular-cli升级AngularJS项目1/doc.md)
2. [（二）同步混合应用路由的状态](/使用Angular-cli升级AngularJS项目2/doc.md)

### 在这个系列文章中涉及到
1. Angular与AngularJS如何共存开发
2. 使用Angular Cli编译及开发Angular、AngularJS混合应用  
3. 如何在Hash模式中同步AngularJS、Angular的路由  
---下面是坑，待填
4. 在gulp或其他自动化构建工具中使用Angular CLI
5. 在Angular中使用AngularJS的组件
6. 在AngularJS中使用Angular的组件

### 本文使用的例子项目
AngularJS的例子项目[angular-phonecat](https://github.com/angular/angular-phonecat)

## 一、路由的管理
在上篇文章中，项目已经基本能正常运行了。但在这里面还有不少的BUG：  
1. 两个框架中的路由状态不同步。
2. 在切换路由后，刷新页面，路由消失。  
在项目中使用的是双路由的策略。即ng1（AngularJS）、ngx（Angular）各自的页面其实还是用相应的路由进行管理的。因此，两个路由间需要一个“桥梁”进行通信，让他们彼此间的数据进行同步，而这个“桥梁”官方早已为我们提供好了，那就是``setUpLocationSync``。  
在使用“桥梁”进行通信前，还需对路由进行改造，让它们更符合我们项目的需求。  

## 二、hash模式还是history模式
在ngx的路由器中，默认使用的是history模式的路由形式。但ng1路由器中默认使用的则是hash模式的路由形式。因此，我们首先要做的，就是要让它们的模式设置为一致。  
hash和history模式各有利弊，它们之间的区别可以参考[附录：LocationStrategy 以及浏览器 URL 样式](https://angular.cn/guide/router#appendix-locationstrategy-and-browser-url-styles)。笔者更推荐使用history模式，因为他更符合浏览器的URL风格。  
下面介绍一下它们各自的设置方法：  
### history模式：
如果使用history模式，那么你主要修改ng1的路由器配置。
``` JavaScript
angular.
  module('phonecatApp').
  config(['$routeProvider','$locationProvider',
    function config($routeProvider,$locationProvider) {
      $locationProvider.html5Mode(true)  // 设置为html5 mode
    }
  ]);
```
### hash模式：  
如果使用hash模式，那么你主要修改的就是Angular的路由配置。
``` JavaScript
@NgModule({
  imports: [RouterModule.forRoot(routes,{
    useHash: true
  })]
})
export class AppRoutingModule {
}
```
在ng1.6你可能使用的是hash-bang模式，或者在ng1中设置了任何的前缀，你需要将其前缀去掉。
``` JavaScript
angular.
  module('phonecatApp').
  config(['$locationProvider',
    function config($locationProvider) {
      $locationProvider.hashPrefix('') 
    }
  ]);
```

## 三、凹槽路由  
在实际的项目中，有些页面是基于ng1写的，有些页面是基于ngx写的。在多数情况下，我们自然是希望能各自显示页面相互不被打扰。因此，我们需要做一个“凹槽路由”让它们能自由切换。（文段参考 2）  
那么。凹槽路由究竟是什么？简单而言就是“空页面”。在显示ng1的页面时，ngx部分显示一个空白的页面不就可以了？同理的，在显示ngx的页面，我们进行相应的操作。  
在ng1中实现比较简单：
``` JavaScript
angular.
  module('phonecatApp').
 config(['$routeProvider',
    function config($routeProvider) {
      $routeProvider.
        ..., // 此处省略其他路由
        otherwise({
          template: ''
        });
    }
  ]);
```
在ngx中，你需要添加一个"空"的Component
```
ng g component empty
```
然后，在路由中添加
``` TypeScript
const routes: Routes = [
  ...,
  {
    path: '**',
    component: EmptyComponent
  }
]
```

ALL DONE...

## 四、同步你的路由状态
现在，就让它们的状态同步吧。  
在history mode下非常简单。只需用到官方提供的``setUpLocationSync``即可。
``` TypeScript
export class AppModule implements DoBootstrap {

    ngDoBootstrap(appRef: ApplicationRef) {
    this.upgrade.bootstrap(document.documentElement, ['phonecatApp']);
    // setUpLocationSync 需在UpgradeModule.bootstrap后引用
     setUpLocationSync(upgrade);
  }
 }
```
那在hash mode下呢？那就没这么简单了。  
笔者曾经直接使用``setUpLocationSync``这个方法导致笔者直接怀疑人生。毕竟，这是官方的东西，怎么可能这么容易出现问题呢？如果不是``setUpLocationSync``的问题，那应该就是出现在路由件配置的问题吧。折腾了许久，直到笔者翻阅了它的源码，终于发现原来问题就是出在``setUpLocationSync``本身上，它并不兼容路由器hash mode，只考虑了history mode。  
那么如果需要在hash mode下同步路由状态，则需要手动去实现这个函数。原理上不是很复杂，ng1中存在一个钩子``$locationChangeStart``，当触发这个事件时，将hash参数作为URL传递给ng的Router即可。
在本文的例子中在原有的``setUpLocationSync``实现了路由器间的同步，可以参考下述的实现：
[location-sync.service.ts](https://github.com/yskun/angular-phonecat-upgrade/blob/master/src/location-sync.service.ts)

## 附：相邻路由出口？
在参考文章中[升级 AngularJS 至 Angular](https://www.cnblogs.com/sghy/p/9150346.html)提到了“相邻路由出口”这个概念，在实际运用中，可以做一些如懒加载的ng1的模块等实现。需要注意的是，如果在组件（Component）中放入ng1的入口，那么调用UpgradeModule.bootstrap需要在该组件视图加载完毕后，如``ngAfterViewInit``钩子等等。

## 源码
在此，你可以获取到本文修改后的angular-phonecat项目。
[https://github.com/yskun/angular-phonecat-upgrade]

## 参考
1. [从 AngularJS 升级到 Angular](https://angular.cn/guide/upgrade)  
2. [升级 AngularJS 至 Angular](https://www.cnblogs.com/sghy/p/9150346.html)

## License
文章适用下述许可证  
[![知识共享许可协议](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)  
本作品采用[知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](http://creativecommons.org/licenses/by-nc-sa/4.0/)进行许可。