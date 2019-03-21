## 11 用Nginx 搭建一个可用的静态资源Web服务器

* 显示html页面
    ```
      server {
        location / {
          alias dlib/
        }
      }
    ```
* 用gzip压缩静态资源，减少传输大小
    ```
      gzip  on;
      gzip_min_length 1;
      gzip_comp_level 2;
      gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
      server {
        location / {
          alias dlib/
        }
    ```
* 用autoindex (Module [ngx_http_autoindex_module](http://nginx.org/en/docs/http/ngx_http_autoindex_module.html) )已文件列表的形式展现静态资源
    ```
      server {
        location / {
          alias dlib/
          autoindex on;
        }

    ```
* 限制nginx向浏览器发送的速度，用 limit_rate [ngx_http_core_module](http://nginx.org/en/docs/http/ngx_http_core_module.html) -> [limit_rate](http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate)
    ```nginx
      server {
        location / {
          alias dlib/
          autoindex on;
          set $limit_rate 1k; (每秒传输1k字节到浏览器中)
        }
        
    ``` 
* 日志格式 log_format
    ```
        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                              '$status $body_bytes_sent "$http_referer" '
                              '"$http_user_agent" "$http_x_forwarded_for"';
        server {
            ...
        }
    ```
    * main : 命名
    * remote_addr : 远端ip地址，浏览器地址
    * time_local : 当前时间
    * status : http状态码
* access_log 指定文件路径
    ```nginx
        access_log logs/access.log main;
        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                              '$status $body_bytes_sent "$http_referer" '
                              '"$http_user_agent" "$http_x_forwarded_for"';
        server {
            ...
        }
    
    ```
   * 所有的[ngx_http_core_module](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables) 
   的内置变量都可以记录到日志中
   * 第三方模块的提供变量也可以记录到日志中，比如 [gzip_ratio](http://nginx.org/en/docs/http/ngx_http_gzip_module.html#variables)
   