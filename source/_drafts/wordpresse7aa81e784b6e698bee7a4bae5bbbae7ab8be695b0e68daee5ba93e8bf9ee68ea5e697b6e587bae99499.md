title: Wordpress突然显示建立数据库连接时出错
tags:
  - PhpMyAdmin
  - WampServer
  - WordPress
id: 186
categories:
  - Wordpress
  - 技术实践
date: 2012-11-06 19:11:22
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">昨天上自己博客的时候，突然&ldquo;建立数据库连接时出错&rdquo;几个大字显示在白色的网页当中，我顿时泪奔了。这几天刚刚想着要把自己的博客转移到一个更稳定的空间去，马上就发生了这件事。我去&hellip;&hellip;我还什么都没搞啊，这也能崩？</span></span>

## 
	<span style="color:#000000;">探索</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">我的第一反应是组里盛大云的那个烂服务器又出问题了。运行mstsc连上服务器看了一下，发现wampserver是绿的，那应该是正常的啊！管他三七二十一，先重启下wampserver再说。一重启毛病就来了，一个错误对话框直接弹出来，什么internal error什么的，打得我是措手不及。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">没办法，先搜搜看跟这个错误相关的情况。网上确实有很多人是这个问题，可是和我的情况都不一样。大多数人是服务器上VC++的运行库没安装，这个我之前早就安装了，否则wampserver根本起不起来。反反复复搜索多次，还是没找到对应的解答。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">wampserver依旧起不起来，到底是哪里起不起来呢？因为wamp集成了apache、mysql、php，那么到底是都起不来了，还是个别的服务起不来了？沿着这个思路我顺着开始尝试，结果试的第一个apache就没起来&hellip;&hellip;我接着搜索apache没起来的原因，一般人都说的是80端口被占用。用wamp自带的测试和cmd的netstat -ano的命令都看了，80端口是没被占用的。我切换到apache的路径下，在bin文件夹中找到了httpd.exe。点开运行，结果命令行弹出了显示了一个什么什么错误就关闭了。我反复打开反复看，根本看不清楚&hellip;&hellip;怎么才能让这个窗口停下来让我看清楚？我突然又想到了可以在命令行中运行。切换到目录下运行httpd.exe，显示系统缓冲区空间不足或队列已满。接着搜了搜这个问题，把wamp卸载又重装了一遍，又看了很多个版本的解决方案，还是对不上号。最后终于看到了一个重启服务器的方案，果断重启之！</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">重启之后wamp果然可以put online了，结果连接博客还是显示建立数据库连接错误。对应搜索这个问题的解决方案，有这么几种说法：</span></span>

1.  <span style="color:#000000;"><span style="font-size: 14px; ">数据库配置文件被篡改</span></span>
2.  <span style="color:#000000;"><span style="font-size: 14px; ">数据库服务器的问题，服务未启动或者其他的一些数据库故障</span></span>
3.  <span style="color:#000000;"><span style="font-size: 14px; ">最重要的一个原因，wp_options表需要修复。不知道wordpress为什么运行一段时间之后就会出现这样的一个问题，但确实数据库连接时出错的问题很大可能是这个问题引起的。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">排除第二条。尝试着进入phpMyAdmin修复wp_options这个表，结果还是不行。难道是重装wampserver导致数据库的用户发生了变化？我查看了后台的wp_config.php，发现里面还是我之前安装wordpress的时候在数据库里添加的单独的一个用户，而php</span><span style="font-size: 14px; ">My</span><span style="font-size: 14px; ">Admin里的用户中却没有。在php</span><span style="font-size: 14px; ">My</span><span style="font-size: 14px; ">Admin中重新添加了这个用户，再登录博客，问题终于解决了！</span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">这次蛋疼的经历主要还是由于我使用wordpress时间不够长、经验不足。在解决问题的过程中，我发现这个wordpress经常会出现的一个问题，解决方案就是进入php</span><span style="font-size: 14px; ">My</span><span style="font-size: 14px; ">Admin去修复一下wp_options这个表。而另外一个问题，httpd.exe显示的系统缓冲区空间不足或队列已满的问题，直接重启就可以搞定。背后到底是什么问题还有待发掘。</span></span>