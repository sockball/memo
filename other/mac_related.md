* 安装`pcntl`扩展([参考redis](https://blog.csdn.net/u013332865/article/details/49638923)), 需要[源码包](http://php.net/releases/), 本例php版本为`7.1.12`
    ```
    /源码包路径/configure
    cp /源码包路径/ext/pcntl ~/Documents/.../
    cd ~/Documents/.../pcntl
    /Applications/MAMP/bin/php/php7.1.12/bin/phpize
    /configure --with-php-config=/Applications/MAMP/bin/php/php7.1.12/bin/php-config
    make
    make install
    ```
    
* PHPSTORM crack([参考](https://blog.csdn.net/xiaowan206/article/details/79367680))  
    1、`http://idea.lanyus.com/`下载激活补丁`http://idea.lanyus.com/jar/JetbrainsCrack-2.10-release-enc.jar ` (假设改名为`1.jar`  
    2、移至`/Applications/PhpStorm.app/Contents/lib/`下  
    3、编辑`/Applications/PhpStorm.app/Contents/Info.plist`文件  
    在大约85行开始的`string`标签内最后加入` -javaagent:../lib/1.jar` 注意有一空格 如下
    ```
    <key>JVMOptions</key>
    <dict>
      <key>ClassPath</key>
      <string>$APP_PACKAGE/Contents/lib/bootstrap.jar:$APP_PACKAGE/Contents/lib/extensions.jar:$APP_PACKAGE/Contents/lib/util.jar:$APP_PACKAGE/Contents/lib/jdom.jar:$APP_PACKAGE/Contents/lib/log4j.jar:$APP_PACKAGE/Contents/lib/trove4j.jar:$APP_PACKAGE/Contents/lib/jna.jar -javaagent:../lib/1.jar</string>
    ```
    4、编辑`/Applications/PhpStorm.app/Contents/bin/phpstorm.vmoptions`文件最后一行加入
    `-javaagent:/Applications/PhpStorm.app/Contents/lib/1.jar`  
    5、打开`PHPSTORM`选择`Activation code`输入`http://idea.lanyus.com/`获得的注册码

* Mac认为已损坏的文件...使用命令`sudo spctl --master-disable`信任所有来源...