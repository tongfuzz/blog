<!--
title:git命令
subtitle:git命令
createDate:2022-11-16
updateDate:2023-03-06
tags:""
imagePath:img/git命令
-->


## git 命令

### 参考网站

[参考文档]("https://zhuanlan.zhihu.com/p/421918805")  
[参考文档]("https://blog.csdn.net/b735098742/article/details/78935748")

### 命令

#### `设置全局代理`

http  
git config --global https.proxy http://127.0.0.1:1080

https  
git config --global https.proxy https://127.0.0.1:1080

使用 socks5 代理的 例如 ss，ssr 1080 是 windows 下 ss 的默认代理端口,mac 下不同，或者有自定义的，根据自己的改  
git config --global http.proxy socks5://127.0.0.1:1080  
git config --global https.proxy socks5://127.0.0.1:1080

只对 github.com 使用代理，其他仓库不走代理  
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080  
git config --global https.https://github.com.proxy socks5://127.0.0.1:1080

取消 github 代理  
git config --global --unset http.https://github.com.proxy  
git config --global --unset https.https://github.com.proxy

取消全局代理  
git config --global --unset http.proxy  
git config --global --unset https.proxy

#### `关联远程仓库 `

git remote add origin repo_url

#### `配置用户名和邮箱`

配置全局用户名和邮箱:  
git config --global user.name "yourUserName"  
git config --global user.email "github@xx.com"

配置单个仓库的用户名和邮箱  
git config user.name "yourUserName"  
git config user.email "github@xx.com"

#### `查看当前配置`

git config --list

#### `git tag 相关`

git clone --branch [tag] [git 地址] &emsp; clone 指定 tag

git checkout &emsp; 切换仓库 tag

git tag &emsp; 打印所有的标签

git tag -l <版本号> &emsp; 打印 符合检索条件的标签 如 `git tag -l 1.\*.\*` 为搜索一级版本为 1 的版本

git checkout <版本号> &emsp; 查看对应标签状态

git tag <版本号>-light &emsp; 创建轻量标签，轻量标签执行一个发行版的分支，其只是一个像某 commit 的引用，存储名称时间戳及标签说明等信息

git tag -a <版本号> -m "<备注信息>" &emsp; 创建一个独立的标签对象，包含了名称时间戳以及标签备注等信息，同指向对应的 commit

git tag -a <版本号> <SHA 值> -m "<备注信息>" &emsp; 为特定的 commit 添加标签

git tag -d <版本号> &emsp; 删除本地标签

git push origin --tags &emsp;推送所有标签到远程仓库

git push origin <版本号> &emsp;推送指定版本的标签

git puhs origin --delete <版本号> &emsp; 删除远程仓库标签

#### `git stash 相关`

git stash save "<备注信息>" &emsp; 创建一个 stash 并添加备注信息

git stash list &emsp; 查看所有的 stash

git stash pop &emsp; 恢复缓存的 stash 列表中的第一个并删除当前缓存的 stash

git stash apply <stash 名称> &emsp;恢复指定的缓存的 stash，不会删除这个 stash

git stash drop <stash 名称> &emsp; 删除指定的 stash

git stash clear &emsp; 删除所有缓存的 stash

git stash show <stash 名称> -p &emsp; 查看指定 stash 的 diff -p 表明查看所有的 diff，显示更改的内容

#### `git add 相关`

git add . &emsp; 添加所有文件到暂存区

git reset HEAD . &emsp; 撤销所有已经 add 的文件

git reset HEAD -filename &emsp; 撤销某个文件或文件夹
