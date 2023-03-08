---
order: 16
tags: nginx
---

# Nginx 使用手记

## spa request optimize

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

另外，如果重定向来源是上游，还可以通过 [proxy_redirect](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect) 修改上游的重定向地址。未设置此项时，默认行为是当上游返回的重定向地址前缀和 proxy_pass 值匹配时，自动将改前缀替换成该 block 的 $scheme://$server_name:$server_port，否则不修改。

## variables

- $host 拿到的是 hostname, $http_host 拿到的是 host, host=hostname:port, 当 port 是默认端口时 host=hostname
- 当匹配到的 server 块中有多个 server_name 时，$server_name 取第一个 server_name, 因此 $server_name 拿到的值不一定是匹配的值
- 根据不同 name 获取 request 中的数据`$http_name`、`$cookie_name`、`$arg_name`

> http://nginx.org/en/docs/http/ngx_http_core_module.html#variables

## server_name in server block matches to what

> 1. during SSL handshake, in advance, according to [SNI](http://nginx.org/en/docs/http/configuring_https_servers.html#sni)
> 2. after processing the request line
> 3. after processing the Host header field
> 4. if the server name was not determined after processing the request line or from the Host header field, nginx will use the empty name as the server name.

当请求经过 nginx `proxy_pass http://target` 转发后，下一级 nginx 用于匹配 server_name 的 host 信息来自上一级的 `proxy_set_header Host xxx` 设置，未设置时 第二级 $http_host 取到的值是 target

## geo && proxy_cache_purge

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

## proxy_cookie_path

上游服务返回的 set-cookie 中无 domain 字段时 (有 domain 字段时可用 `proxy_cookie_domain`)，给 reponse cookie 添加 domain 字段的 hack:

```nginx
if ( $host ~ "rq.tech$" ) {
    set $domain "rq.tech";
}
proxy_cookie_path ~^(.+)$ "$1;Domain=$domain";
```

## add_header

- 同级别 block（两个 if）后面的优先级比前面的高，不同级别内层优先级比外层高，最终 add_header 只会选择一个 block 的，不会叠加
- [ngx_http_rewrite_module](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#directives]) 指令集是优先执行，顺序执行的，其他指令集在它之后执行
- 如果有同级 if 块并且最后的 if 块生效但里面没 add_header 时，前面 if 块的 add_header 都会失效，最终 add 的是上一个层级的 header

```nginx
add_header test1 $v; # 这行在最后的 if 指令执行之后再执行
set $v 0;
if ($uri) {
    add_header test2 1;
}
if ($uri) {
    set $v "${v}test3";
    add_header test3 2;
}
if ($uri) {
    # nothing
}
```

- 4xx 和 5xx 响应可通过增加 always 带回响应头
- header 的 key 是可以重复的

## if-try-files

if 生效时 try_files 会失效，下面例子访问/welcome/xxx 时，相当于访问\<dist_path>/xxx, 而不会 fallback 到 /welcome/index.html

```nginx
location /welcome {
    alias <dist_path>;
    try_files $uri /welcome/index.html;
    if ($uri) {
         # nothing
    }
}
```

## link

📎 [robust nginx rules recommend](https://github.com/anson09/ng)
