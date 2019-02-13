---
title: Namesilo + CloudFlare配置Https服务
categories: 
tags: server
copyright: true
comments: true
description: 
date: 2019-01-01 14:33:32
thumbnail: /pic/2.jpg
disqusId: Namesilo + CloudFlare配置Https服务
---



## 前言


---

搭建博客过程中我在NameSilo上购买了域名自己人生当中的第一个域名（rulinzi.com），然后将域名解析到我的服务器ip（配置解析后有延迟，我的大概只过了几分钟就生效了），然后采用CloudFlare免费上了Https，记录一下遇到的问题


 
---
<!-- more -->
## 域名

---

 为了便于记忆简单，人们采用域名的方式代替ip，由此产生了域名解析。类型:A ,AAAA , CNAME , MX , TXT/SPF , SRV , CAA记录解析。

>* A记录解析：A (Address) 记录是用来指定主机名（或域名）对应的IP地址记录。用户可以将该域名下的网站服务器指向到自己的web server上。同时也可以设置您域名的二级域名

>* CNAME记录：别名记录。这种记录允许您将多个名字映射到另外一个域名。通常用于同时提供WWW和MAIL服务的计算机。例如，有一台计算机名为“host.mydomain.com”（A记录）。它同时提供WWW和MAIL服务，为了便于用户访问服务。可以为该计算机设置两个别名（CNAME）：WWW和MAIL。这两个别名的全称就
http://www.mydomain.com/和mail.mydomain.com。实际上他们都指向host.mydomain.com。



## SSL


cloudflare



<style>
hr{
    margin: 40px 0;
    height: 3px;
    border: none;
    background-color: #ddd;
    background-image: repeating-linear-gradient(-45deg, #fff, #fff 4px, transparent 4px, transparent 8px);
}
p{
  margin: 0 0 25px 0;
  font-size: 14px;
  line-height: 3;
  text-indent:20px;
  text-align: justify;
  /*letter-spacing:1px;*/
}
</style>