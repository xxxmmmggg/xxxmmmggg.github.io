

> 很荣幸在今年 2 月到 5 月的时间里，以顾问的身份加入饿了么，参与 PWA 的相关工作。这篇文章其实最初是在以英文写作发表在 medium 上的：[Upgrading Ele.me to Progressive Web Apps](https://medium.com/elemefe/upgrading-ele-me-to-progressive-web-app-2a446832e509)，获得了一定的关注。所以也决定改写为中文版本再次分享出来，希望能对你有所帮助 ;) <br><br>
> 本文首发于 [CSDN](http://geek.csdn.net/news/detail/210535) 与《程序员》2017 年 7 月刊，同步发布于 [饿了么前端 - 知乎专栏](https://zhuanlan.zhihu.com/ElemeFE)、[Hux Blog](https://huangxuan.me)，转载请保留链接。


自 Vue.js 官方推特第一次[公开][1]到现在，我们就一直在进行着将[饿了么移动端网站](https://h5.ele.me/msite/#pwa=true)升级为 [Progressive Web App][2] 的工作。直到近日在 Google I/O 2017 上[登台亮相](https://m.weibo.cn/status/4109332495285652)，才终于算告一段落。我们非常荣幸能够发布全世界第一个专门面向国内用户的 PWA，但更荣幸的是能与 Google、UC 以及腾讯合作，一起推动国内 web 与浏览器生态的发展。

## 多页应用、Vue、PWA？

对于构建一个希望达到原生应用级别体验的 PWA，目前社区里的主流做法都是采用 SPA，即单页面应用模型（Single-page App）来组织整个 web 应用，业内最有名的几个 PWA 案例 [Twitter Lite][3]、 [Flipkart Lite][4]、[Housing Go][5] 与 [Polymer Shop][6] 无一例外。

然而饿了么，与很多国内的电商网站一样，青睐多页面应用模型（MPA，Multi-page App）所能带来的一些好处，也因此在一年多将移动站从基于 Angular.js 的单页应用重构为目前的多页应用模型。团队最看重的优点莫过于页面与页面之间的隔离与解耦，这使得我们可以将每个页面当做一个独立的“微服务”来看待，这些服务可以被独立迭代，独立提供给各种第三方的入口嵌入，甚至被不同的团队独立维护。而整个网站则只是各种服务的集合而非一个巨大的整体。

 
与此同时，我们仍然依赖 [Vue.js](http://vuejs.org/) 作为 JavaScript 框架。Vue 除了是 React/Angular 这种“重型武器”的竞争对手外，其轻量与高性能的优点使得它同样可以作为传统多页应用开发中流行的 “jQuery/Zepto/Kissy + 模板引擎” 技术栈的完美替代。Vue 提供的组件系统、声明式与响应式编程更是提升了代码组织、共享、数据流控制、渲染等各个环节的开发效率。[Vue 还是一个渐进式框架]((https://www.youtube.com/watch?v=pBBSp_iIiVM))，如果网站的复杂度继续提升，我们可以按需、增量地引入 Vuex 或 Vue-Router 这些模块。万一哪天又要改回单页呢？（谁知道呢……）

2017 年，PWA 已经成为 web 应用新的风潮。我们决定试试，以我们现有的“Vue + 多页”的架构，能在升级 PWA 的道路上走多远，达到怎样的效果。


## 实现 “PRPL” 模式

[“PRPL”][7]（读作 “purple”）是 Google 的工程师提出的一种 web 应用架构模式，它旨在利用现代 web 平台的新技术以大幅优化移动 web 的性能与体验，对如何组织与设计高性能的 PWA 系统提供了一种高层次的抽象。我们并不准备从头重构我们的 web 应用，不过我们可以把实现 “PRPL” 模式作为我们的迁移目标。“PRPL”实际上是 Push/Preload、Render、Precache、Lazy-Load 的缩写，我们会在下文中展开它们的具体含义。


### 1. PUSH/PRELOAD，推送/预加载初始 URL 路由所需的关键资源。

无论是 HTTP2 Server Push 还是 `<link rel="preload">`，其关键都在于，我们希望提前请求一些隐藏在应用依赖关系（Dependency Graph）较深处的资源，以节省 HTTP 往返、浏览器解析文档、或脚本执行的时间。比如说，对于一个基于路由进行 code splitting 的 SPA，如果我们可以在 webpack 清单、路由等入口代码（entry chunks）被下载与运行之前就把初始 URL，即用户访问的入口 URL 路由所依赖的代码用 Server Push 推送或 `<link rel="preload">` 进行提前加载。那么当这些资源被真正请求时，它们可能已经下载好并存在在缓存中了，这样就加快了初始路由所有依赖的就绪。

在多页应用中，每一个路由本来就只会请求这个路由所需要的资源，并且通常依赖也都比较扁平。饿了么移动站的大部分脚本依赖都是普通的 `<script>` 元素，因此他们可以在文档解析早期就被浏览器的 preloader 扫描出来并且开始请求，其效果其实与显式的 `<link rel="preload">` 是一致的。

![](/img/in-post/post-eleme-pwa/PUSH-link-rel-preload.jpg)

我们还将所有关键的静态资源都伺服在同一域名下（不再做域名散列），以更好的利用 HTTP2 带来的多路复用（Multiplexing）。同时，我们也在进行着对 API 进行 Server Push 的[实验](https://zhuanlan.zhihu.com/p/26757514)。



### 2. RENDER，渲染初始路由，尽快让应用可被交互

既然所有初始路由的依赖都已经就绪，我们就可以尽快开始初始路由的渲染，这有助于提升应用诸如首次渲染时间、可交互时间等指标。多页应用并不使用基于 JavaScript 的路由，而是传统的 HTML 跳转机制，所以对于这一部分，多页应用其实不用额外做什么。


### 3. PRE-CACHE，用 Service Worker 预缓存剩下的路由

