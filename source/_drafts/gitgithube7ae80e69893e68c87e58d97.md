title: git+github简易指南
tags:
  - git
  - GitHub
id: 163
categories:
  - 代码管理
  - 技术实践
date: 2012-11-03 17:46:26
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">最近用两台电脑同时写一个工程。一开始准备用移动硬盘拷来拷去，太麻烦了；后来改成了用云盘，云盘对工程的支持又不是很好。无奈之下只好重操旧业用起了github。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">上次用github还是暑假的时候，不知不觉又过了三四个月了...翻出了自己当时evernote上写的指南，觉得写得挺乱的。干脆再整理一下发到博客上作为存档吧！</span></span>

## 
	<span style="color:#000000;">进入正题！</span>

### 
	<span style="color:#000000;"><span style="font-size: 14px; ">创建工程</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">github比较简单，没有账号的话先注册一个账号。然后选create a repository，就进入了建立工程的界面。下面的.gitignore要特别注意一下，因为工程里面有些东西往往是不需要同步的，比如说android里面，一般也就同步个res和src文件夹，再加上AndroidManifest.xml，其他没必要。建好工程之后就可以开始在本地做点什么了~</span></span>

### 
	<span style="color:#000000;"><span style="font-size: 14px; ">拉取</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">用github的官方软件是一种方法，不过貌似没什么技术含量啊。我是用的git，在google里面搜一搜就能搜到了。把windows版的下下来之后安装，重启下资源管理器，选一个文件夹点右键就可以git init了。这样这个文件夹就初始成了一个可以用git的目录。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">接下来是用的比较多的部分了。还是点击右键，选择git bash，git专属的命令行就出现了。如果是第一次使用的话，最好先把git config的信息设置一下，让git知道你是谁。现在不设置后面也会让你设置的。命令如下：</span></span>

> <span style="color: rgb(0, 0, 0); font-size: 14px; ">*设置user name</span>> 
> 
> 		<span style="color:#000000;"><span style="font-size: 14px; ">git config --global user.name &quot;ur name here&quot;&nbsp;</span></span>> 
> 
> 		<span style="color:#000000;"><span style="font-size: 14px; ">*设置email address</span></span>> 
> 
> 		<span style="color:#000000;"><span style="font-size: 14px; ">git config --global user.email &quot;ur email address here&quot;</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">如何用这个命令行同步我们刚刚在github里面创建的工程呢？很简单，在github该工程的页面上方有一些地址，可以选择http，ssh和read-only。选择http，把右边出现的地址复制下来。回到git bash的命令行中，输入</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">git remote add origin &quot;复制的地址&quot;</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">然后回车，刚才那个地址就可以用origin来代替了。接下来可以输入</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">git pull orgin master</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">这个命令会把origin的master分支&ldquo;拉取&rdquo;到本地，文件夹里面就会有你刚刚在githuab上创建的工程的内容了。</span></span>

### 
	<span style="color:#000000;"><span style="font-size: 14px; ">推送</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">上面那些是从服务器上把工程抓下来的方法，那把本地的修改提交到服务器上怎么做呢？其实这部分比较复杂，因为git有好几个工作空间。实际上git的工作流有三棵树。一是工作目录，有实际的文件，就是我们在操作系统当中看到的；二是一个缓存区，可以临时保存改动；三是HEAD，指向你最近一次提交之后的结果，但这还没提交到服务器上。先输入</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">git add &lt;文件的名字&gt;</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">把你想要提交的修改内容添加到缓存区。接下来用</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">git commit -m &quot;提交的信息&quot;</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">这条命令把你add过的所有内容提交到HEAD。-m是一个参数，m应该是message的缩写，引号当中的内容是你关于这次提交的描述。但现在你还没把这些内容推送到github上。推送到github要用下面这条命令了</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">git push origin master</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">push是推的意思，很形象。origin是前面remote add设置的远端服务器，master是主分支。这个时候可能会需要你填写github的用户名和密码，按照提示填写就行了。push成功之后你可以到github上面对应的repository去看看，可以看到你最新推送的内容以及-m以后的信息，推送就成功了。</span></span>

### 
	<span style="color:#000000;"><span style="font-size: 14px; ">不每次都输入用户名密码</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">老实说，每次推送都输入用户名和密码挺麻烦的。这个问题也被广大怕麻烦的github user研究透了。很简单，在系统变量里面建一个HOME，值为%USERPROFILE%。然后到C盘用户文件夹下新建一个名为_netrc的文件，用笔记本程序打开编辑，输入如下内容</span></span>

> <span style="color:#000000;"><span style="font-size: 14px; ">machine github.com</span></span>> 
> 
> 		<span style="color:#000000;"><span style="font-size: 14px; ">login username</span></span>> 
> 
> 		<span style="color:#000000;"><span style="font-size: 14px; ">password *******</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">保存就好了。下次就不用再输了。</span></span>