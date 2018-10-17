* 记一次重定向....
```
  location ^~ /static/
  {
     rewrite ^/static/(.*)\.html$ /homepage/admin/$1.html last;
  }
  
  location ^~ /homepage/ {
      if  (!-f $request_filename) {
        rewrite ^/homepage/admin/(.*)\.html$ /homepage/screen/$1.html break;
      }
  }
  ```
  
* https配置示例
```
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 443 ssl;

    ssl_certificate /.../admin.crt;
    ssl_certificate_key /.../admin.key;
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_ciphers AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
    ssl_prefer_server_ciphers on;

    #keepalive_timeout 70;

    server_name admin....com;

    root        /.../backend/web/;
    index       index.php;

    access_log  /.../access_nginx.log;
    error_log   /.../access_nginx_error.log;

    add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
    add_header Set-Cookie "Secure";
	
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ ^/assets/.*\.php$ {
        deny all;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        try_files $uri = /404;
    }

    location ~* /\. {
        deny all;
    }
}
```