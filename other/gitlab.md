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