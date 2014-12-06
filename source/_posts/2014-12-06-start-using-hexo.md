title: 使用 hexo 搭建博客
date: 2014-12-06 15:41:13
tags: [hexo]
---

# 安装软件

- [nodejs](http://nodejs.org/)
- [git](http://git-scm.com/downloads)
- [smartgit](http://www.syntevo.com/smartgit/) 这个主要是为了搭建好博客后方便博客源码的管理，比起 git bash 的命令行操作好用。

# 注册 github 账号

在 [github](https://github.com/) 上注册一个账号。

# 创建 github 库

在 github 上用你的 github 账号的用户名创建一个 repository，如我的 github 账户的用户名为 AnnieCyl，那么我就要创建一个名为 **anniecyl**.github.io 的 repository，注意：repository 的名字要以 **.github.io** 结尾，且必须与你的 github 账号的用户名一致，否则最后将无法打开你的博客页面。如下：
![1](/img/2014-12-06-start-using-hexo/1.png)

默认生成的 repository 只有 master 分支，这个分支我们用来发布我们的博客。我们可以再建一个 develop 分支，这个分支是用来编辑博客的文章，编辑好的博客文章再通过 hexo 发布到 master 分支上。如下：
![2](/img/2014-12-06-start-using-hexo/2.png)

将这个 repository 从 github 上 clone 到本地，可以在以下地方看到这个 repository 的地址：
![3](/img/2014-12-06-start-using-hexo/3.png)

然后用 git bash 命令行 clone：`git clone https://github.com/AnnieCyl/anniecyl.github.io.git`，如下：
![4](/img/2014-12-06-start-using-hexo/4.png)

或者用 smartgit 的 clone 向导界面操作，如下：
![5](/img/2014-12-06-start-using-hexo/5.png)

# 开始 hexo 吧

## 安装 hexo

打开 cmd 命令行，执行如下命令（前提是已经安装了 nodejs）：
`npm install -g hexo`

## 搭建博客

使用 git bash 或者 smartgit 把前面从 github 上 clone 到本地的 repository 切换到 develop 分支，然后打开本地 repository 文件夹，在此文件夹中用 cmd 命令行依次执行下面的命令：
`hexo init`
`npm install`

到此步你可以在本地查看现在博客搭建成什么样了，运行 `hexo server`，然后打开 `http://localhost:4000/` 即可看到。但是此时查看的博客还只是在本地，还未将你的博客发布到 github 上。

## 生成 ssh key
现在我们要将本地 repository 与远程的 github 建立联系，我们需要使用 ssh keys。
- 如果你的 ~/.ssh 目录下已经有名为 id_rsa 的 ssh key 了，且这个 ssh key 还未用在其他 github 账户上，那么你可以直接使用这个 id_rsa，github 默认是在 ~/.ssh 目录下查找 id_rsa 的 ssh key。
- 如果你的 ~/.ssh 目录下的 id_rsa 已经被用在其他 github 账户了，那么你就要再生成另外的 ssh key，并且对 ~/.ssh/config 文件进行配置来告诉 github 使用新生成的这个 ssh key。方法如下：
（1）打开 git bash，cd 到 ~/.ssh 目录，然后输入以下命令：`ssh-keygen -t rsa -C "邮件地址@youremail.com"`，会提示输入 ssh key 的文件名，输入 ssh key 文件名后回车，会提示输入密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。
（2）在 config 中添加如下配置：
        host github.com
            CheckHostIP no
            user git
            hostname github.com
            port 22
            identityfile ~/.ssh/你刚生成的 ssh key 的名称

## 添加 ssh key到 github
在本机生成 ssh key 之后，需要添加到 github 上，以完成 ssh 链接的设置。
（1）打开 ssh key 文件（id_rsa.pub 或者你新生成的 .pub 文件），拷贝文件内容。
（2）打开你的 github 账号，点击右上角的 Settings-> SSH keys ---> Add SSH key，将上面拷贝的内容粘贴进去，最后点击下方的“Add key”。
可在 git bash 中执行 `ssh -T git@github.com` 看看设置是否成功。

## 修改博客配置
发布博客之前，需要修改配置，才能够将博客部署到 github 上。打开本地 repository 目录下的 _config.yml，如下：
   
        deploy:
	        type: github
	        repository: git@github.com:AnnieCyl/anniecyl.github.io.git
	        branch: master

`repository` 配置项要使用 ssh 地址，可在 github 上查看该 repository 的地址。

## 发布博客
在本地 repository 中执行以下命令：
`hexo generate`
`hexo d`

ok 啦，接下来你就可以在浏览器中输入：你的 github 用户名.github.io(比如我的博客：anniecyl
.github.io)看到你的博客了。

## 写博客
在本地 repository 中执行以下命令：
`hexo new "my new post"`
会自动在本地 repository 的 `source/_posts` 下生成 `my new post.md`，编辑该文件后就可以发布到博客上了。

## 参考

- [GitHub多账户使用](http://www.blogjava.net/lishunli/archive/2012/03/08/371556.html)
- [Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)
- [如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)