* rsync 注意尾部带`/`则传文件夹中的内容, 不带则传文件夹  
`rsync -avP /本地 #user@#ip:/data/wwwroot`  
`rsync -avP -e 'ssh -p 27891' #user@#ip:/data/wwwroot/dir /本地`
* centos 7 重启 sshd  
`systemctl restart sshd`
* 清除历史

    ```
    history -c
    rm -rf ~/.bash_history
    ```
* vim普通模式下常用快捷键

    ```
    dd #删除整行
    / #开启查询
    n # /查询后 下一项匹配的内容
    N # /查询后 上一项匹配的内容
    shift + 6 #跳到行首
    shift + 4 #跳到行尾 由下面增强替代
    u #撤销
    ctrl + r #恢复撤销
    :noh #消除搜索内容的高亮
    ```
* 增强vim功能, 编辑或新增`~/.vimrc`(或全局`/etc/vim/vimrc` `/etc/vimrc`)文件, `"`开始为注释, 退出该文件直接生效 ([更多](http://www.ruanyifeng.com/blog/2018/09/vimrc.html))
    
    ```
    set number "显示行号"
    syntax on "语法高亮"
    set mouse=a "开启鼠标支持"
    map 9 $ "设置0跳行首, 9跳行尾"
    set softtabstop=4 "设置tab为4个空格缩进"
    set autoindent "回车/换行时 参考上一行自动缩进"
    set incsearch "边输入边搜索"
    set hlsearch "开启搜索结果高亮显示"
    ```

* 查看80端口占用 `lsof -i:80`, 如结果  
`nginx   13075 root    9u  IPv4 46037272      0t0  TCP *:http (LISTEN)`
* 查看exe软连接的真实目录 `ll -a /proc/13075`
* `strace nginx 2>&1 | grep conf`
* `locate nginx.conf`
* 查看磁盘占用 `df -h`
* 查看内核 `uname -a` `cat /proc/version`
* 查看发行版信息 `cat /etc/issue`
* 更改ssh登录端口 `vim /etc/ssh/sshd_config` && `systemctl restart sshd.service`
* 尝试ssh登录日志   
`/var/log/secure`  
系统拒绝列表  
`/etc/hosts.deny`  
筛选后查看  
`grep -o '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' /var/log/secure | sort | uniq `
* 使用denyHosts([参考](http://blog.51cto.com/jin771998569/1633597))  
```
wget http://sourceforge.net/projects/denyhosts/files/denyhosts/2.6/DenyHosts-2.6.tar.gz | tar -zxf
cd DenyHosts-2.6
python setup.py install

# 默认安装路径
cd /usr/share/denyhosts/
# 配置文件
cp denyhosts.cfg-dist denyhosts.cfg
vim denyhosts.cfg
cp daemon-control-dist denyhosts
./denyhosts start
```

* `ps` 常用选项 `ps -aux`
* `netstat` 常用选项 `netstat -anp`

* 7个运行级别, `runlevel`可查看当前运行级别 (`init`即`/sbin/init`)  
通过`/etc/inittab`文件可查看注释
```
0 系统停止
1 单用户系统
2 不带NFS的多用户系统 (Network File System, 即不带网络)
3 完整多用户模式
4 系统保留级别, 用户可自定义
5 X11图形模式...
6 重新启动系统
```
于是运行`init 6`可以使系统重启  
标准的Linux运行级别为3或5  
[参考](https://www.cnblogs.com/fps2tao/p/7573672.html)  
`-----------------------------------------`  
使用`systemd`的centos已弃用`inittab`  
使用`targets`代替`runlevels`  
默认有2个主要的`target`
```
multi-user.target: 类似inittab 3
graphical.target: 类似inittab 5
```

* 命令加入开机自启动
    * 编辑`/etc/rc.local`, 文件尾行加入命令, 如：  
    `/usr/local/php/bin/php /data/wwwroot/app/yii socket start -d`
    * 编写shell放至`/etc/init.d`下即会开机加载运行... (`/etc/init.d`为`/etc/rc.d/init.d`的软连接)
    * 使用`chkconfig`将shell注册为系统服务
    
* mac不借助第三方实现NTFS分区的可写挂载...[参考](https://www.cnblogs.com/thatsit/p/6218117.html)
```
# 先卸载后挂载
# 磁盘工具找到硬盘的设备名 此处为`disk4s2`

# unmount 或选择推出...
sudo umount /Volumes/disk4s2

sudo mkdir /Volumes/kkk
sudo mount -t ntfs -o rw,auto,nobrowse /dev/disk4s2 /Volumes/kkk/
open /Volumes/kkk
```
