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