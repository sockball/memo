* docker安装gitlab([参考](https://www.imooc.com/article/23168))  
    [中文镜像](https://hub.docker.com/r/beginor/gitlab-ce/) `docker pull beginor/gitlab-ce:11.3.0-ce.0`  
    [官方镜像](https://hub.docker.com/r/gitlab/gitlab-ce/) `docker pull gitlab/gitlab-ce`  
    以中文镜像为例 (使用官方镜像则最后改为`gitlab/gitlab-ce:latest`

    ```
    GITLAB_HOME=/Users/sockball/Documents/gitlab/
    docker run -d \
            --hostname 192.168.30.107 \
            -p 8443:443 -p 8080:80 -p 8822:22 \
            --name gitlab \
            --restart always \
            -v $GITLAB_HOME/config:/etc/gitlab \
        -v $GITLAB_HOME/logs:/var/log/gitlab \
            -v $GITLAB_HOME/data:/var/opt/gitlab \
            beginor/gitlab-ce:11.3.0-ce.0
    ```
    设置host文件 `vim /etc/hosts/` 然后`192.168.30.107:8080`可访问...  
    首次登陆设置密码及邮箱 管理员账号为`root`
    
* centos 安装gitlab [官网](https://about.gitlab.com/installation/#centos-6)  
Add the GitLab package repository and install the package  
`curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
`
以本地8070端口为例 (gitlab工作端口)
`sudo EXTERNAL_URL="http://127.0.0.1:8070" yum -y install gitlab-ee
`  
默认服务使用端口8080 可以更改配置文件的`unicorn['port']`选项至其他端口 如8050  
`vim /etc/gitlab/gitlab.rb`  
nginx代理到8070(gitlab工作端口)  
`proxy_pass http://127.0.0.1:8070`  
更改配置后需要reconfigure    
`gitlab-ctl reconfigure`  
基本命令 `start` `stop` `restart`