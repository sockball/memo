* mac下docker容器中配置xdebug [参考](https://www.jianshu.com/p/b04c4e2c3845)
    * 在phpstorm中获取ip `127.0.0.1`不可用 取另一个..(即内网IP?)
    `Preferences->Languages & Frameworks->PHP->Debug：Zend Debugger：Automatically detect IDE IP`
    
    * `php.ini`中配置remote_host为该ip
    * 配置`Preferences->Languages & Frameworks->PHP->Debug->DBGp Proxy：IDEKEY localhost 80`
    * 配置`Preferences->Languages & Frameworks->PHP->Servers`时  
    `Use path mappings`并在`Absolute path on the server`中配置出容器的项目根目录

* yii console xdebug [参考](https://stackoverflow.com/questions/16589106/debug-yii-console-application-in-phpstorm)
    * `Languages & Frameworks > PHP`下配置PHP解释器`CLI Interpreter`，其中配置会自动读取`解释器目录/../conf/php.ini`...且似乎不能自己指定
    * 右键根目录的`yii`文件选择`Debug yii`
    * `Arguments`中配置要debug的命令参数等等
