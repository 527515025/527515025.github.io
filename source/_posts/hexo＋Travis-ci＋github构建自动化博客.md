---
title: hexo＋Travis-ci＋github构建自动化博客
---
在mac上 用hexo＋Travis-ci＋github构建自动化博客。

	Travis CI：是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的特别在于采用yaml格式，简洁清新独树一帜。目前大多数的github项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整构建。
	
说一下我对Travis 的理解。tarvis－ci是远端服务器构建，跟jenkins 不一样，不用自己搭建一个jenkins服务器。tarvis－ci是用的远端官方提供的服务器。用hexo 搭建博客，然后将博客源码提交到 git上，用travis－ci 编译git 上的源码，然后将编译后的文件，推送到Github.io仓库下，达到自动化编译构建。

***
##构建自动化博客的步骤

由于需要搭建hexo 和本地搭建Travis 所以本地需要有ruby 和 node 环境。本地搭建Travis 是为了和Travis－ci 服务器进行加密解密获得密匙，获得一次即可。

###1.安装rubu 和node.js
###2.搭建hexo
###3.安装 travis
###4.配置git 的ssh key
###5.配置Travis
###6.验证


***
#1.安装ruby和node

我是通过brew安装 ruby 和node 的，（如果brew 没有安装请参考[mac安装brew](http://blog.csdn.net/u012373815/article/details/51794271)）在终端执行命令 
```
brew install ruby 
brew install node 
```
***
#2.搭建hexo
此处参考博客
[hexo+github 分分钟搭建博客](http://blog.csdn.net/u012373815/article/details/52266120)

	博客搭建成功后在github 的527515025.github.io 仓库下新建travis分支，并将博客源码（未编译过的代码）提交到该分支，用于以后Travis-ci编译使用。
***
#3.安装 travis
brew和node安装成功后执行下面命令安装travis
```
gem install travis
```
***
#4.配置git 的ssh key
参考
[SSH连接GitHub、GitHub配置ssh key](http://blog.csdn.net/u012373815/article/details/53575362)


#5.配置Travis
travis安装成功后就需要配置travis了，配置travis比较麻烦主要有如下步骤

（1）：进入travis官网[Travis 官网传送门](https://travis-ci.org/)用github账号登陆Travis CI

![这里写图片描述](http://img.blog.csdn.net/20161211174544551?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2 ）:在右上角你的账户名点击进入 account，在Repositories tab页点击Sync now同步你的github项目。选中项目将默认的off改变为on开启项目的持续集成。

![这里写图片描述](http://img.blog.csdn.net/20161211175151946?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）：在你项目的根目录建立一个.travis文件夹

```
$ cd 博客项目文件夹根目录
$ mkdir .travis
```
（4）复制id_rsa 和 创建 ssh_config文件

```
$ cd 博客项目文件夹根目录/.travis
$ cp ~/.ssh/id_rsa ./
```

（5）创建 ssh_config 文件 
```
$ cd 博客项目文件夹根目录/.travis
$ touch ssh_config
```
![这里写图片描述](http://img.blog.csdn.net/20161211203322400?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

编辑 ssh_config ,输入以下信息
```
$ vi ssh_config 

//输入以下信息
Host github.com
User git
StrictHostKeyChecking no
IdentityFile ~/.ssh/id_rsa
IdentitiesOnly yes
```
![这里写图片描述](http://img.blog.csdn.net/20161211203359659?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（6）travis 登陆

```
$ cd 博客项目文件夹根目录/.travis
$ travis login --auto
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: 527515025
Password for 527515025: ************
Successfully logged in as 527515025!
```
（7）加密操作

加密操作 
在博客项目文件夹下对ssh的私钥进行加密

```
$ cd 博客项目文件夹根目录/.travis
$ travis encrypt-file id_rsa --add
```
如图
![这里写图片描述](http://img.blog.csdn.net/20161211203756021?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
此操作会生成加密之后的秘钥文件 id_rsa.enc，删除id_rsa密钥文件(私钥不能随便泄露)。

你的travis 目前上已经出现了下面加密解密key :
(点击第2步图中的仓库名可进入下图界面，点击setting可看到加密)

![这里写图片描述](http://img.blog.csdn.net/20161211180713231?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（8）在你项目的根目录建立一个.travis.yml文件，内容为：
 此步是在我527515025.github.io仓库的travis 分支下编译源码。
 （用第7步中Travis 生成的 encrypted_xxxxxxxxx_key -iv 和encrypted_xxxxxxxxx_iv  替换travis.yml 中openssl的 key  和 -iv）
 
```
language: node_js
node_js:
- '4'
branches:
  only:
  - travis
cache:
  directories:
  - node_modules
before_install:
- openssl aes-256-cbc -K $encrypted_xxxxxxxxx_key -iv $encrypted_xxxxxxxxx_iv
  -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa
- eval $(ssh-agent)
- ssh-add ~/.ssh/id_rsa
- cp .travis/ssh_config ~/.ssh/config
- git config --global user.name "527515025"
- git config --global user.email "527515025@qq.com"
install:
- npm install hexo-cli -g
- npm install hexo-deployer-git --save
- npm install
script:
- hexo clean
- hexo g

after_success:
- hexo deploy
```
（9）_config.yml 配置如下：
这是将编译好的代码提交527515025.github.io 仓库的master分支
```
deploy:
  type: git
  repo: git@github.com:527515025/527515025.github.io.git
  branch: master
```
***
#6.验证
此时部署已经成功，在项目源码中也就是travis 分支上提交一个新的博客（也就是一个.md文件）我门在travis－ci 网站上看到正在自动化构建

（黄色为正在构建，绿色为构建完成）

![这里写图片描述](http://img.blog.csdn.net/20161211181837645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

构建完成后就可以访问  xxxx.github.io 查看是否自动发布成功

![这里写图片描述](http://img.blog.csdn.net/20161211182211365?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjM3MzgxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


