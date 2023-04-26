---
title: 通过Nginx解决跨域问题
categories: 
- 运维
tags: 
- Nginx
---

##### 现在大部分Web项目基本都是采用的前后端分离模式。前端为H5项目，后端为Java、PHP、Python等项目。而且大部分后端服务并不会只部署一套服务，而是会采用Nginx对后端服务进行负载均衡。那么，此时就会出现一个问题了：如果一个请求url的协议、域名、端口，三者之间任意一个与当前页面url不同就会产生跨域的现象。
<!--more-->

#### 什么是跨域？

##### 首先一个url是由：协议、域名、端口 三部分组成。（一般端口默认80）。所以，当一个请求url的协议、域名、端口三者之间的任意一个与当前页面url不同即为跨域。

<br />

#### 为什么会出现跨域？

##### 出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）。

<br />

#### Nginx解决方案

##### 以下是一个案例，仅针对跨步部分说明，请勿直接复制到生产环境，请求根据实际情况修改。

``` nginx
server {
    listen 80;
    server_name example.com;
    index index.html index.htm;

    root  /data/www/your-project-path;

    #指定允许跨域的方法，*代表所有（注意：生产环境建议哪些域名允许跨域，而不是开放全部）
    add_header Access-Control-Allow-Methods *;

    #预检命令的缓存，如果不缓存每次会发送两次请求
    add_header Access-Control-Max-Age 3600;

    #不带cookie请求，并设置为false
    add_header Access-Control-Allow-Credentials false;

    #表示允许这个域跨域调用（客户端发送请求的域名和端口）
    #$http_origin动态获取请求客户端请求的域   不用*的原因是带cookie的请求不支持*号
    add_header Access-Control-Allow-Origin $http_origin;

    #表示请求头的字段 动态获取
    add_header Access-Control-Allow-Headers
    $http_access_control_request_headers;
    
    location / {

    }

    location ~ /\. {
        deny  all;
    }
}
```