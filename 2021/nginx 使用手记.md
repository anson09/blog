# nginx 使用手记

```nginx
location /welcome {
    alias /static/uat/anka;
    try_files $uri $uri/ /welcome/index.html;
}
```

优化为

```nginx
location /welcome {
    alias /static/uat/anka;
    try_files $uri /welcome/index.html;
}
```

当带 $uri/ 时，请求 .../welcome，若 ng 发现 $uri (和 root 或者 alias 组合后) 是一个目录，会返回一个 301 的重定向到 .../welcome/，导致多出一次前端请求，并且重定向的 http 端口是 ng 该 server 块的端口而不是前置 lb 端口，优化后要么命中资源，要么直接返回默认资源.

影响重定向的三个配置默认值:  
`absolute_redirect on;` 控制返回绝对路径还是相对路径

_以下两个开关当使用绝对路径时才生效:_

- [`server_name_in_redirect off;`](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name_in_redirect) 打开使用 ng server_name 配置，关闭使用 http 请求头中 host 字段，没有该字段时使用 IP
- `port_in_redirect on;` 重定向是否带端口号(不带后变成 scheme 默认端口)

---

根据不同 name 获取 request 中的数据`$http_name`、`$cookie_name`、`$arg_name`

> http://nginx.org/en/docs/http/ngx_http_core_module.html#variables

---

设置内网地址支持根据 proxy_cache_key 清除对应的一条缓存请求(\*结尾清除全部)

```nginx
geo $purge_allowed {
     default         0;
     10.0.0.1        1;
     192.168.0.0/24  1;
  }

map $request_method $purge_method {
   default 0;
   PURGE   $purge_allowed;
}

proxy_cache_purge $purge_method;
```

---

[robust nginx rules recommend](https://github.com/anson09/ng)
