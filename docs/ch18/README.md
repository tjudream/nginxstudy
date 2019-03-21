# 18 用免费SSL证书实现一个HTTPS站点
* 编译nginx时需要带上http_ssl_module
    ```
        ./configure --prefix=/home/dream/nginx --with-http_ssl_module
        make && make install
    ```
* 生成证书
    * 这个域名是必须存在的
    ```
        apt install python-certbot-nginx
        certbot --nginx --nginx-server-root=/home/dream/nginx/conf/ -d geektime.taohui.pub
    ```
* ssl_session_cache shared:le_nginx_SSL:1m;
    * 设置1M的缓存区用于缓存握手的session信息
    * 1M的空间大概可以存储4000个https连接的信息
* ssl_session_timeout 1440m;
    * 如果断开连接后，重新连接，只要不超过1440分钟就不需要重新握手
* ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    * 表明nginx支持哪些版本的协议
* ssl_prefer_server_ciphers on;
    * 表示nginx开启决定使用哪些协议与浏览器通讯
* ssl_ciphers "ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POSY1305:ECDHE-ECDSA-AES128-GCM-SHA256:...:!DSS";
    * 表示nginx支持的安全套件，用冒号分隔，排在前面的优先被使用
* ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; #managed by Certbot
    * 表示使用非对称加密时，使用哪些参数，这些参数将影响网络安全的加密强度
    