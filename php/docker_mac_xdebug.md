docker容器中配置xdebug [参考](https://www.jianshu.com/p/b04c4e2c3845)
* 在phpstorm中获取ip `127.0.0.1`不可用 取另一个..(即内网IP?)
`Preferences->Languages & Frameworks->PHP->Debug：Zend Debugger：Automatically detect IDE IP`

* `php.ini`中配置remote_host为该ip
* 配置`Preferences->Languages & Frameworks->PHP->Debug->DBGp Proxy：IDEKEY localhost 80`
* 配置`Preferences->Languages & Frameworks->PHP->Servers`时  
`Use path mappings`并在`Absolute path on the server`中配置出容器的项目根目录


