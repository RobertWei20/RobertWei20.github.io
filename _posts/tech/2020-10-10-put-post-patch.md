---
layout: post
title: "POST,PUT和PATCH的区别"
subtitle: ''
author: "Robert"
header-img: "img/post/http.jpg"
header-mask: 0.3
tags:
  - 计算机科学
  - HTTP
---

本篇主要谈一下http协议中POST,PUT以及PATCH的区别。在了解这些之前，需要先了解一下幂等性:

### 幂等性：

HTTP协议本身是一种面向资源的应用层协议，但实际上在HTTP协议的使用中存在着两种不同的方式：

1、一种遵循RESTful，既把HTTP当成应用层协议，比较忠实地遵守了HTTP协议的各种规定；<br/>
2、另一种是SOA的，它并没有完全把HTTP当成应用层协议，而是把HTTP协议作为了传输层协议，然后在HTTP之上建立了自己的应用层协议。

GET方法用于获取资源，不应有副作用，所以是幂等的。比如：GET [http://www.bank.com/account/123456](http://www.bank.com/account/123456)，不会改变资源的状态，不论调用一次还是N次都没有副作用。请注意，这里强调的是一次和N次具有相同的副作用，而不是每次GET的结果相同。GET [http://www.news.com/latest-news](http://www.news.com/latest-news)这个HTTP请求可能会每次得到不同的结果，但它本身并没有产生任何副作用，因而是满足幂等性的。

DELETE方法用于删除资源，有副作用，但它应该满足幂等性。比如：DELETE [http://www.forum.com/article/4231](http://www.forum.com/article/4231)，调用一次和N次对系统产生的副作用是相同的，即删掉id为4231的帖子；因此，调用者可以多次调用或刷新页面而不必担心引起错误。

POST和PUT的区别容易被简单地误认为“POST表示创建资源，PUT表示更新资源.但实际上两者都可以用来创建或是更新数据.单从技术上来说,他们并没有什么区别.但是在HTTP规范中POST是非等幂的,多次调用会产生不同的结果,比如:创建一个用户,由于网络原因或是其他原因多创建了几次,那么将会有多个用户被创建.而PUT id/456则会创建一个id为456的用户,多次调用还是会创建的结果是一样的,所以PUT是等幂的.

PATCH一般是用来局部更新资源的,假设我们有一个UserInfo，里面有userId， userName， userGender等10个字段。可你的编辑功能因为需求，在某个特别的页面里只能修改userName，这时候的更新怎么做？

人们通常(为徒省事)把一个包含了修改后userName的完整userInfo对象传给后端，做完整更新。但仔细想想，这种做法感觉有点二，而且真心浪费带宽(纯技术上讲，你不关心带宽那是你土豪)。
于是PATCH诞生，只传一个userName到指定资源去，表示该请求是一个局部更新，后端仅更新接收到的字段。

而PUT虽然也是更新资源，但要求前端提供的一定是一个完整的资源对象，理论上说，如果你用了PUT，但却没有提供完整的UserInfo，那么缺了的那些字段应该被清空.另外PUT会有一个create操作,加入更新的id不存在,会进行创建,而PATCH则没有这个.

