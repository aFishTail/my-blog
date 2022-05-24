---
title: "Nginx将referer从localhost改为127.0.0"
date: 2021-08-18T15:20:53+08:00
draft: false
---
# Nginx将referer从localhost改为127.0.0

由于线上环境比较复杂，后台需要校验`referer`，只能放开`127.0.0.1`的，本地开发时打开不如localhost方便，想到了修改`nginx` `referer` 的值。

### 打开nginx配置文件（nginx.conf）
### 在http块中添加
```
    map $http_referer $ref {
    default $http_referer;
    ~(http:\/\/localhost)(.*)  http://127.0.0.1$2;
    }
```

1. `~(http:\/\/localhost)(.*)  http://127.0.0.1$2`用到了nginx的**正则表达式匹配：**

    - ==:等值比较;
    - ~：与指定正则表达式模式匹配时返回“真”，判断匹配与否时区分字符大小写；
    - ~*：与指定正则表达式模式匹配时返回“真”，判断匹配与否时不区分字符大小    写；
    - !~：与指定正则表达式模式不匹配时返回“真”，判断匹配与否时区分字符大小    写；
    - !~*：与指定正则表达式模式不匹配时返回“真”，判断匹配与否时不区分字符大小 写；

2. 同时用到了map指令,map 的主要作用是创建自定义变量，通过使用 nginx 的内置变量，去匹配某些特定规则，如果匹配成功则设置某个值给自定义变量。 而这个自定义变量又可以作于他用。

    举个栗子：
    **场景**： 匹配请求 url 的参数，如果参数是 debug 则设置 $foo = 1    ，默认设置 $foo = 0
    ```
    map $args $foo {
        default 0;
        debug 1;
    }
    ```
    **解释**

    >$args 是nginx内置变量，就是获取的请求 url 的参数。 如果 $args 匹配 到 debug 那么 $foo 的值会被设为 1 ，如果 $args 一个都匹配不到    $foo 就是default 定义的值，在这里就是 0

### 在location中加入
```
proxy_set_header referer $ref
```
原refer的`http:localhost/8888`会被改成`http://127.0.0.1:8888`

**参考资料**
[用Nginx代理后，修改请求的Referer](https://www.jianshu.com/p/809baff9fe31)

[Nginx map 使用详解](https://blog.51cto.com/tchuairen/2175525)