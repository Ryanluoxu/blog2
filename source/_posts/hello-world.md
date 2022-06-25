---
title: Hello World
date: 2017-11-24 00:00:00
categories: Note
---

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

<!--more-->

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)



## Anything ##

### Log ###

- Create Hexo blog
- Change to Next
- Add photo
- Add social link
- Add About, Tags and Categories
- Add gitment
- Add copyright post
- Add donate
- Add Commonweal 404

### toDo ###

### Doing ###
- Pro Git


### Done ###

## Tips for blog ##

### Read more >> ###

From: http://www.jianshu.com/p/c07ccdfba068

	categories: blog #文章文类
	tags: [博客，文章] #文章标签，只有一项时 tags: blog
	---
	
	这段文字会显示在首页。
	<!--more-->
	这些内容会被隐藏，点击 Read more 才能看到。

### Code ###

``` bash
$ hexo new "My New Post"
```

```
$ hexo new "My New Post"
```

    $ hexo new "My New Post"






### ZA WU ###
---
title: "RESTful Web Service with AngularJS"
date: 2017-12-17 22:10:18
categories: Rest
tags: [Rest, Web Service, AngularJS, Spring]
---

[Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)

[Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)

[Understanding REST](https://spring.io/understanding/REST)

[Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)

[使用Spring Boot和Gradle创建AngularJS项目](http://blog.javachen.com/2015/01/06/build-app-with-spring-boot-and-gradle.html)


# Spring Boot & AngularJS

Reference: [使用Spring Boot和Gradle创建AngularJS项目](http://blog.javachen.com/2015/01/06/build-app-with-spring-boot-and-gradle.html)

1. Generate Project
	- http://start.spring.io/
	- GroupId: com.demo
	- Artifact: ng-spring-boot

Structure:
![Project Structure](/images/ngspringboot-structure.png)

修改 main 方法，获取 ApplicationContext 信息。


====
Temp:

Take from ZhiHu, will delete soon.

URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。

1. REST描述的是在网络中client和server的一种交互形式

2. Server 提供的 RESTful API中，URL中只使用名词来指定资源，原则上不使用动词。
“资源”是REST架构或者说整个网络处理的核心。
比如：
http://api.qc.com/v1/newsfeed: 获取某人的新鲜; 
http://api.qc.com/v1/friends: 获取某人的好友列表;
http://api.qc.com/v1/profile: 获取某人的详细信息;


3. 用HTTP协议里的动词来实现资源的添加，修改，删除等操作。

即通过HTTP动词来实现资源的状态扭转：
GET 用来获取资源
POST 用来新建资源（也可以用于更新资源）
PUT 用来更新资源，DELETE 用来删除资源

比如：
GET /products : will return the list of all products
POST /products : will add a product to the collection
GET /products/4 : will retrieve product #4
PUT /products/4 : will update product #4

4. Server和Client之间传递某资源的一个表现形式，
比如用JSON，XML传输文本，或者用JPG，WebP传输图片等。当然还可以压缩HTTP传输时的数据（on-wire data compression）。

5. 用 HTTP Status Code传递Server的状态信息。
比如最常用的 200 表示成功，500 表示Server内部错误等。

Web端和Server只使用上述定义的API来传递数据和改变数据状态。
格式一般是JSON。


