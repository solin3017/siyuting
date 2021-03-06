---
title: Session,Token相关区别
date: 2018-05-22 14:00:00
category:
- Code
tags:
- node
---
# 为什么要有session的出现？  

> 是由于网络中http协议造成的，因为http本身是无状态协议，这样，无法确定你的本次请求和上次请求是不是你发送的。如果要进行类似论坛登陆相关的操作，就实现不了了。  

# session生成方式？  

> 浏览器第一次访问服务器，服务器会创建一个session，然后同时为该session生成一个唯一的会话的key,也就是sessionid，然后，将sessionid及对应的session分别作为key和value保存到缓存中，也可以持久化到数据库中，然后服务器再把sessionid，以cookie的形式发送给客户端。这样浏览器下次再访问时，会直接带着cookie中的sessionid。然后服务器根据sessionid找到对应的session进行匹配；  
还有一种是浏览器禁用了cookie或不支持cookie，这种可以通过URL重写的方式发到服务器；
> 简单来讲，用户访问的时候说他自己是张三，他骗你怎么办？ 那就在服务器端保存张三的信息，给他一个id，让他下次用id访问。


# 为什么会有token的出现？  

> 首先，session的存储是需要空间的，其次，session的传递一般都是通过cookie来传递的，或者url重写的方式；而token在服务器是可以不需要存储用户的信息的，而token的传递方式也不限于cookie传递，当然，token也是可以保存起来的；

# token的生成方式？  

> 浏览器第一次访问服务器，根据传过来的唯一标识userId，服务端会通过一些算法，如常用的HMAC-SHA256算法，然后加一个密钥，生成一个token，然后通过BASE64编码一下之后将这个token发送给客户端；客户端将token保存起来，下次请求时，带着token，服务器收到请求后，然后会用相同的算法和密钥去验证token，如果通过，执行业务操作，不通过，返回不通过信息；

# token和session的区别？

>token和session其实都是为了身份验证，session一般翻译为会话，而token更多的时候是翻译为令牌；  
session服务器会保存一份，可能保存到缓存，文件，数据库；同样，session和token都是有过期时间一说，都需要去管理过期时间；  
其实token与session的问题是一种时间与空间的博弈问题，session是空间换时间，而token是时间换空间。两者的选择要看具体情况而定。

>虽然确实都是“客户端记录，每次访问携带”，但 token 很容易设计为自包含的，也就是说，后端不需要记录什么东西，每次一个无状态请求，每次解密验证，每次当场得出合法 /非法的结论。这一切判断依据，除了固化在 CS 两端的一些逻辑之外，整个信息是自包含的。这才是真正的无状态。 
而 sessionid ，一般都是一段随机字符串，需要到后端去检索 id 的有效性。万一服务器重启导致内存里的 session 没了呢？万一 redis 服务器挂了呢？ 

>方案 A ：我发给你一张身份证，但只是一张写着身份证号码的纸片。你每次来办事，我去后台查一下你的 id 是不是有效。   
方案 B ：我发给你一张加密的身份证，以后你只要出示这张卡片，我就知道你一定是自己人。 
就这么个差别。