[引用](https://blog.csdn.net/guiyecheng/article/details/51915865)说明  
* apcu前身为apc, apc缓存分为系统缓存和用户缓存：  
>1、系统缓存是指php执行时增加缓存，减少php文件的反复检查和编译，从而达到系统加速的效果。   
 2、用户缓存是指，php代码中将数据写入缓存，是用户写入的数据，通过key和value的键值方式插入和读取。这种数据叫做用户缓存。  
* php5.5以后，opcache将代替apc做为php加速的位置，也就是代替其系统缓存的位置。并将用户缓存功能独立出来，开启新的组件，这个组件名称叫做apcu。  

[扩展安装](http://pecl.php.net/package/APCu)  

[配置参数](http://php.net/manual/zh/apcu.configuration.php)

注意：
* FastCGI模式下重启后会清除缓存
* php-cli模式下访问一次结束后就会清除缓存