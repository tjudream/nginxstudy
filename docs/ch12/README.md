## 12 用Nginx搭建一个具备缓存功能的反向代理服务
* 将11中的nginx变为上游服务器（让客户端无法直接访问）
    ```
        server {
            listen  127.0.0.1:8080; #只允许本机访问
        }
    ```
* 用一个新的nginx作为反向代理服务器
    * 相关特性可以参考 [ngx_http_proxy_module](http://nginx.org/en/docs/http/ngx_http_proxy_module.html)
    ```
        upstream local {
                server 127.0.0.1:8080;
        }
        server {
            location / { 
                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                            
                        #proxy_cache my_cache;
            
                        #proxy_cache_key $host$uri$is_args$args;
                        #proxy_cache_valid  200 304 302 1d;
                        proxy_pass http://local;
            }
        } 
    ```
    
* 缓存功能相关配置可以参考 [proxy_cache](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache)
    ```
        proxy_cache_path /tmp/nginxcache levels=1:2 keys_zone=my_cache:10m max_size=10g
                    inactive=60m use_temp_path=off;
        upstream local {
                server 127.0.0.1:8080;
        }
        server {
            location / { 
                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                            
                        #proxy_cache my_cache;
            
                        #proxy_cache_key $host$uri$is_args$args;
                        #proxy_cache_valid  200 304 302 1d;
                        proxy_pass http://local;
            }
        } 
    ```
    
    * 添加缓存后，先访问一次，然后即使停掉上游（11中）的nginx服务器，依然能够正常访问
