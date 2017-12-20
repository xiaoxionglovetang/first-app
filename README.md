# welcome to my webapp

## 介绍
>这是我第一次做webapp, 是用一个基于node.js的Express架构和mongoDB开发的，前端部分用的是vue来绑定HTML中的数据，client端和server端的交互用的是axios。
- 使用webapp做了一个个人blogs,目前具有的功能的注册，登录，主页，文章列表，展示/添加/修改文章。还在继续开发中...


## webapp中的一些设计的考虑
### 路由
##### 路由归类
> 考虑到当路由不断增加，在app.js中管理路由就会非常麻烦。所以通过截流的方式，让app.js只处理两个路由文件，一个负责提供页面，一个负责提供数据。

```
app.use('/', page);
app.use('/api', api);
```
>把获取页面的路由route.page.js和获取数据的路由 route.api.js 分开写在两个不同的 js 文件中，可以在业务功能上区分开，今后也可以由不同的不同的人分别维护。

##### 合理的使用服务器渲染和延迟渲染
- 用户发出请求，如果我是采用`res.render`来响应，那么整个页面的构建都是在服务端完成。

- 构建整个页面的所有处理时间都在服务端。这个时间包括从数据库抓取数据和由数据构建html页面。

- 假如数据量大，或者构建页面的处理逻辑复杂，势必就导致一个请求在服务器的处理时间变长。那么 WebApp的处理能力就很有压力，可能导致宕机。

- 所以部分请求的响应采用了延迟渲染，WebApp 只需要返回一个基础页面的模板以及提供一个api供客户端抓取必要的数据。其余的都交给客户端处理。尽量的减少服务器压力。

##### 优化api
>由于提供数据的api可能会吧数据提供给前端、android 应用、ios应用、小程序等，所以得采用一种标准化的设计方式。不然会怕乱掉。

- 增加 API 版本控制（v1就是第一版，今后升级了就可以记录为v2.）

```
[GET] /api/v1/users
[GET] /api/v1/posts
[POST] /api/v1/posts/create
[POST] /api/v1/posts/edit
[GET] /api/v1/posts/one
```

- 尽可能用 http 自身的方法来区分路由功能

```
[GET] /api/v1/users
[GET] /api/v1/posts
[POST] /api/v1/posts/create
[PATCH] /api/v1/posts/edit
[GET] /api/v1/posts/one
```

- 避免使用动词来区分路由

```
[POST] /api/v1/posts // 去掉动词
[PATCH] /api/v1/posts  // 去掉动词
```
>因为动词多了后就很容易产生歧义，路由尽量避免

- 用资源 ID 而不是单词区分资源

```
[GET] /api/v1/posts/:id // 获取某一个文章
```
这些点是参照[restfull api 设计指南 ](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

---

### view
>随着功能的复杂，不断的添加页面，意味着要不断的新建页面。但是每个页面都有很多内容是重复的。所有引入了 `express-ejs-layouts`,构建一个全局的页面模板。

---

### 优化结果处理
在开发过程中发现在返回结果时，发现了自己的几个错误：
1. 自己设计了很多错误值，如：`success：false/true`，所以修改为使用状态码。

2. patch请求其实基本上不需要服务端返回数据。
 
除此之外还优化了登录时出息的错误处理、axios 的错误处理和Cookie存储用户信息的不安全问题等，从最早的返回自己定义的错误值，到返回状态码，再到返回具体情况提示，如："找不到用户"。而不是状态码500。




