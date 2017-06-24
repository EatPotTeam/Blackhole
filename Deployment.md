# 黑洞（Blackhole）部署文档

## 服务端

### Requirements

*  Node.js v4.2.6 or higher
*  PM2
*  Nginx

### How to Deploy

1. 在服务器上将该仓库克隆到本地

2. 执行`npm install`安装所需的依赖

3. 执行`pm2 startOrRestart ecosystem.config.js --env production`即可将服务器部署到生产环境

4. 配置Nginx，将域名绑定到监听的端口，参考配置如下：

   ```
   # HTTP - only for obtaining cert
   server {
           listen          80;
           server_name     blackhole.perqin.com;
           location /.well-known {
                   root    /var/www/html;
                   allow   all;
           }
           location / {
                   return  301 https://$host$request_uri;
           }
   }
   # HTTPS - serve nodejs app
   server {
           listen          443 ssl;
           server_name     blackhole.perqin.com;
           ssl_certificate         /etc/letsencrypt/live/blackhole.perqin.com/fullchain.pem;
           ssl_certificate_key     /etc/letsencrypt/live/blackhole.perqin.com/privkey.pem;
           location / {
                   proxy_set_header        X-Real-IP $remote_addr;
                   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                   proxy_set_header        X-NginX-Proxy true;
                   proxy_pass              http://localhost:3456/;
                   proxy_ssl_session_reuse off;
                   proxy_set_header        Host $http_host;
                   proxy_cache_bypass      $http_upgrade;
                   proxy_redirect          off;
           }
   }
   ```

## 客户端

### Requirements

*  Android开发环境，项目可直接使用Android Studio构建

### How to Deploy

直接进行release构建，获得apk之后安装即可。