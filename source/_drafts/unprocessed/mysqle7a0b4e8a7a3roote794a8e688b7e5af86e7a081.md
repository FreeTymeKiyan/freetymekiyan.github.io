title: MySQL破解root用户密码
tags:
  - PhpMyAdmin
  - SQL
  - WordPress
  - 技术
id: 712
categories:
  - 技术实践
  - 数据库
date: 2013-05-08 11:01:27
---

## 
	<span style="color:#000000;">背景 &nbsp;</span>

	<span style="color:#000000;"><span style="font-size:14px;">前段时间团队四零培训要搭一个博客。本来想搭在团队服务器上，可惜它已经不堪重负了。权衡之后决定放在实验室的服务器上，只在团队服务器上设一个反向代理。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">搭Wordpress博客不难，只用把解压文件放在服务器上对应位置，再配置就行了。但配置的时候需要可用的MySQL数据库的用户名和密码。这台服务器已经好多年了，辗转几个项目组，问了好几位毕业的学长都表示不知道。没办法，我只好找找破解密码的方法。花了一晚上终于破解成功了。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">这几天那个博客又出问题了，显示数据库连接错误，是我博客上（<u>[Wordpress突然显示建立数据库连接时出错](http://freetymekiyan.1kapp.com/?p=186)</u></span></span><span style="color:#000000;"><span style="font-size:14px;">）提到的问题。问题不大，所以我让某学弟按图索骥去搞定。结果服务器上没装PhpMyAdmin，没办法直接用我提供的方法。好，那就装一个吧，用着也方便。装PhpMyAdmin也需要MySQL的用户名和密码，而上次破解的密码我已经忘记了，也没记在服务器上。试来试去还是没试出来。只好再破解一次。</span></span>

## 
	<span style="color:#000000;">探索</span>

### 
	<span style="color:#000000;">破解原理</span>

	<span style="color:#000000;"><span style="font-size:14px;">与其说是破解，不如说是重置。要在没有用户名和密码的条件下获取root用户的权限，然后进入数据库，修改mysql.user表下root用户的密码，实现重置。</span></span>

### 
	<span style="color:#000000;">分析操作</span>

#### 
	<span style="color:#000000;">登录数据库</span>

	<span style="color:#000000;"><span style="font-size:14px;">首先是如何在没有用户名和密码的条件下获取root用户权限。MySQL对权限进行了管理，但是通过一些参数，我们可以跳过管理机制，直接登录。方法很简单：</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">打开命令行，输入net stop mysql，关闭服务器上现有的MySQL服务，关闭成功会有提示（也可以打开任务管理器直接杀死mysqld.exe进程）；</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">在命令行里，切换到MySQL安装目录的bin文件夹下，输入mysqld --skip-grant-tables（带参数运行mysql，skip是跳过的意思，grant是授权，我理解为跳过授权表运行），成功后会显示一些英文，就是些欢迎登录之类的话；</span></span>

#### 
	<span style="color:#000000;">重置密码</span>

	<span style="color:#000000;"><span style="font-size:14px;">获取权限后，进入数据库，重置密码，刷新权限，最后退出。</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">接下来命令行前面多了mysql&gt;，可以输入sql语句进行操作了，输入update mysql.user set password=PASSWORD(&#39;新密码&#39;) where user=&#39;root&#39;;（update是增删改查中的改，mysql.user是指mysql数据库中的user表，set x=y，是把x列的值改成y，这里password是user表中的一列，存放密码，大写的PASSWORD(&#39;新密码&#39;)是一个函数，</span></span><span style="color:#FF0000;"><span style="font-size:14px;">这里必须要注意，不是用&#39;新密码&#39;代替PASSWORD的意思</span></span><span style="color:#000000;"><span style="font-size:14px;">！新密码是一个参数，最后where语句用作筛选，意思是选择user这列值为root的这一行），如果语句正确，会显示结果，表示这个语句影响了几行数据，改变了几个数据，这里结果应该是影响了一行，如果密码和原来不一样会显示改变了一行，否则为0；</span></span>
2.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">修改完密码，接着输入flush privileges;，回车，刷新权限（这句很有必要）；</span>
3.  <span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">输入quit或exit，退出数据库，还会跟你说Bye。</span>

#### 
	<span style="color:#000000;"><span style="font-size:14px;">重启服务</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">虽然退出了数据库登录，但是刚才不用权限就能登录的MySQL服务还在运行。最后一步，打开任务管理器，找到mysqld.exe的进程，结束进程。再打开命令行，输入net start mysql，启动mysql服务，成功启动会有提示。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">重启之后再用新密码登录试试吧！</span></span>

## 
	<span style="color:#000000;">总结</span>

### 
	<span style="color:#000000;">感受</span>

	<span style="color:#000000;"><span style="font-size:14px;">破解密码很有hack的感觉。两次破解经历让我学到两点：</span></span>

1.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">学到的东西一定要做好总结！第一次破解花了一晚上，结果没有及时总结，到第二次又要破解又再去搜索，效率低下。用时方恨少啊！</span>
2.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">要避免重复劳动！毕竟破解是很少用到的，如果第一次就把密码记下来，也不至于又要花第二次的功夫做这件事。（说着说着我突然想到，可以直接在Wordpress的配置文件里看密码&hellip;&hellip;现在密码又改了，Wordpress又得改配置了）</span>

### 
	<span style="color:#000000;">要点</span>

1.  <span style="color:#000000;"><span style="font-size:14px;">打开命令行，输入net stop mysql，回车运行；</span></span>
2.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">切换到MySQL安装目录的bin文件夹下，输入mysql --skip-grant-tables，回车运行；</span>
3.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">输入update mysql.user set password=PASSWORD(&#39;新密码&#39;) where user=&#39;root&#39;;，如update mysql.user set password=PASSWORD(&#39;12345&#39;) where user=&#39;root&#39;;，回车运行；</span>
4.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">输入flush privileges;，回车运行；</span>
5.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">输入exit或quit，回车运行；</span>
6.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">打开任务管理器，找到mysqld.exe，结束进程；</span>
7.  <span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">在命令行里输入net start mysql，启动服务。</span>