*  私有构造函数 防止外界实例化对象
```
private function __construct() {}
```

* 守护进程开启redis后 可进入redis-cli执行`shutdown`命令关闭