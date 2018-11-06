---
title: nginx缓存302请求导致重定向循环
date: 2018-10-06 17:23:53
tags: HTTP
---



### 一.概述 
    
> 前端遗留项目，将其嵌入新开发的门户网站中，作为子项目，嵌入之后发现访问速度很慢，平均加载耗时超过10s，性能分析之后，发现存在以下的问题：

1. 需要加载的静态资源多，差不多120多个，且大部分没有经过压缩
2. 原系统部署在内网，门户网站部署在阿里云，嵌入之后访问速度受到内外网带宽的影响，且访问相当于多了一次跳转
3. 每次ajax请求需要去sso（统一登录平台）校验是否登录，对性能也有一定的影响



### 二.详细
#### 1. 通过nginx配置加速网页访问

系统访问流程图如下：
![](https://dknfeiov.github.io/images/files/nginx302/runFlow.PNG) 

因为遗留项目为第三方系统，只提供了压缩包，无法修改项目源码，那就只能在nginx服务器和浏览器端进行优化

采取措施如下： 
> 开启gzip压缩文件大小，浏览器静态资源缓存，开启nginx代理缓存

  

nginx部分配置:
```lua

  http {
   
    # gzip压缩
    gzip on;
    gzip_min_length  5k;
    gzip_buffers     4 16k;
    gzip_comp_level 3;
    gzip_types       text/plain application/javascript text/css application/xml text/javascript image/jpeg image/gif image/png;
    gzip_vary on;

    # proxy 缓存空间 声明 
    proxy_cache_path proxy_cache_static levels=1:2 keys_zone=cache_static:20m;

    # 请求头 判断缓存是否命中
    add_header Nginx-Cache $upstream_cache_status;

    server {

        listen       8080;
        server_name  127.0.0.1;

        # 遗留项目 proxy 缓存
        location /url {
            # 浏览器静态资源缓存
            expires 7d;
            proxy_cache cache_static;
            # 缓存7天
            proxy_cache_valid  200 206 304 301 302 7d;
            # 缓存唯一KEY值
            proxy_cache_key $uri;
            proxy_pass http://10.18.194.161:7001;
        }
    }
  }
```

经过如上配置，页面访问速度得到极大提升，访问速度控制在1s以内

![](https://dknfeiov.github.io/images/files/nginx302/visit.PNG) 


#### 2. 重定向循环问题出现

解决了访问速度的问题，不久业务部门又反馈了一个问题，登陆之后，隔段时间再操作，页面有时会一直闪烁，无法正常操作，经过chrome调试，发现是因为发生了302重定向循环，开始分析问题


正常访问，命中nginx缓存（HIT），返回200：
![](https://dknfeiov.github.io/images/files/nginx302/200-normal-nginx-cache.PNG) 


重定向循环异常，返回302：
![](https://dknfeiov.github.io/images/files/nginx302/redirectError.PNG) 


查看异常详情，发现nginx缓存仍然命中（HIT），且返回302重定向到当前资源：
![](https://dknfeiov.github.io/images/files/nginx302/redirectErrorDetail.PNG) 



#### 3. 问题发现及解决

我们从结果倒推，首先，查看请求头，请求头中的 Nginx-Cache 返回 HIT ,由此可知，我们的错误响应是由nignx返回的，而没有到达遗留项目，既然错误响应是由nginx缓存返回的，查看nginx缓存配置，发现我们的配置确实存在缓存302请求的可能
```lua
    proxy_cache_valid  200 206 304 301 302 7d;
```
然后，就是要找到它是什么时候缓存了这个302重定向了，与后台同学一起，从前到后分析了请求的整个流程，最终定位问题出现在sso登录校验阶段，用户一段时间未操作，登录状态过期，导致静态资源请求触发302，并复现了问题

首先，清空浏览器登陆信息，不访问登陆页面，而是先直接访问静态资源common.js，后台拦截请求，判断当前未登陆，跳转到sso单点登陆页面，登陆成功，sso重定向到之前访问的路径，也就是如下图
![](https://dknfeiov.github.io/images/files/nginx302/errorRepeat.PNG) 


而我们之前的问题也就很清楚了，浏览器请求静态资源common.js，请求经过nginx，到达遗留项目，遗留项目判断当前未登陆，跳转到sso登陆，登陆成功后返回302重定向到登陆之前的路径也就是common.js资源路径，返回又经nginx，nginx缓存了这个302返回，并将该响应返回到浏览器，浏览器根据302状态码，再次发起对common.js，nginx直接返回302，重定向循环就这么理所当然的发生了


问题的分析，溯源花了很长的时候，解决问题只需稍微修改nginx配置即可，如下，去掉对302请求的缓存，世界终于平静了下来

```lua
    #before
    proxy_cache_valid  200 206 304 301 302 7d;

    #after
    proxy_cache_valid  200 206 304 301 7d;
```

    