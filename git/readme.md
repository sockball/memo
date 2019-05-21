* 创建新版本库
```
git clone [url] [folder]
cd [folder]
touch README.md
git add README.md
git commit -m 'add README'
git push -u origin master
```

* 已存在的文件夹
```
cd existing_folder
git init
git remote add origin [url]
git add .
git commit -m 'Initial commit'
git push -u origin master
```

* 已存在的git版本库
```
cd existing_repo
git remote rename origin old-origin
git remote add origin [url]
git push -u origin --all
git push -u origin --tags
```
* 创建一个裸仓库 无工作区  
`git init --bare sample.git`
* 回退并且删除历史  
`git reset --hard commit_hash`  
`git push origin HEAD --force`
* 添加远程仓库 (默认#port 22可不填  
`git remote add alias-name #user@#ip:#port/data/git/xxx.git #好像有问题...`  
`git remote add alias-name ssh://#user@#ip:#port/data/git/xxx.git`
* 删除master分支 (其他分支同理)  
`git branch -D master #先切换成其他分支且设置default分支为其他`  
`git push origin :master`
* 服务器创建git仓库  
[centos7.4开始废除 RSAAuthentication选项](https://www.cnblogs.com/Leroscox/p/9627809.html)
```
//建立git用账号 (实际不应使用git作为用户名
useradd git
passwd git

//创建仓库
cd /data/git
git init --bare vitaminc.git
chown -R git:git vitaminc.git/

// 开启此三项打开RSA认证
// RSAAuthentication yes
// PubkeyAuthentication yes
// AuthorizedKeysFile .ssh/authorized_keys
vim /etc/ssh/sshd_config
systemctl restart sshd

//添加公钥
mkdir /home/git/.ssh
vim /home/git/.ssh/authorized_keys
chown -R git:git /home/git/.ssh
chmod 700 /home/git/.ssh
chmod 600 /home/git/.ssh/authorized_keys

//修改为 git:x:1004:1004::/home/git:/usr/bin/git-shell
vim /etc/passwd
```

* gitignore对于已被track的文件是无效的 需要先把本地缓存删除再commit、push等  
`git rm -r --cached .`  
或者删除具体某个要被忽略的文件的缓存  
`git rm -r --cached xxx.doc `

* 查看某一文件历史  
`git log --pretty=oneline filename`

* 假定忽略选项： `--assume-unchanged`，还原：`--no-assume-unchanged` 
e.g.:  
`git update-index --assume-unchanged xxxx.conf`   
`git update-index --no-assume-unchanged xxxx.conf`

* 跳过工作树选项： `--skip-worktree` [以上2个选项参考](https://www.zhihu.com/question/25234996)
* 查找被忽略的文件 `git ls-files -v | grep '^h\ '`

* `git push error`:`Failed to connect to 127.0.0.1 port #port`  
  检查端口占用 `lsof -i:#port`  
  检查代理 `env | grep -i proxy` 然后`unset`掉
  
* 一个push error `fatal: The remote end hung up unexpectedly`  
文件过大上传失败...[参考](https://stackoverflow.com/questions/6842687/the-remote-end-hung-up-unexpectedly-while-git-cloning)  
`git config http.postBuffer 524288000`

* `git cherry-pick [commit_hash]` 使用cheery-pick选取某分支的一个commit进行提交

* [参考](https://www.cnblogs.com/tocy/p/git-stash-reference.html)  
`git stash` 当前工作目录贮存起来以待恢复  
`git stash save '备注'`  
`git stash pop` 应用并删除第一个stash  
`git stash apply`应用指定stash(不会删除, 默认stash@{0})  
`git stash list`查看现有stash  
`git stash drop stash@{0}`删除指定stash  
`git stash clear`删除所有stash  
`git stash show`查看指定stash file change  
`git stash show -p`查看指定stash全部文件的diff

* 创建一个空分支使用`--orphan`选项 不会继承当前分支
`git checkout --orphan gh-pages`