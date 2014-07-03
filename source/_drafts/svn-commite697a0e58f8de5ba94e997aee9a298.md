title: 'svn commit无反应问题'
tags:
  - SVN
id: 71
categories:
  - 代码管理
  - 技术实践
date: 2012-10-20 11:14:33
---

<span style="color:#000000;"><span style="font-size: 14px; ">今天早上一起来就想把昨天的bug赶紧给解决掉。bug是解决的挺快，用SVN上传的时候居然出现问题了，点击commit之后根本没有任何反应。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">第一反应还是上网搜。看到了很多的commit失败的情况，都和我碰到的情况不同。他们的情况至少都是有反应的，比如说弹出了什么什么错误信息。个别没反应的情况说是被杀毒软件或者防火墙拦截了。我试着关了360安全卫士，再尝试，还是没反应；关了windows自带的防火墙，还是没反应。这就坑爹了...</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">我又尝试在任务管理器中找svn相关的进程，发现了TSVNCache，描述是TortoiseSVN status cache。没多想，先杀了试试再说。杀掉进程之后发现所有svn相关的文件夹箭头都变成了绿色的钩，红色的全没了。刷新一次红色的钩又出来了，这个进程也重新跑了。看来这个进程应该是标识文件svn状态的进程，跟commit没反应无关。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">正当我走投无路的时候，我突然想起来，前几天重装系统之后，重新配置Java的环境变量的时候不小心把PATH里面原有的变量全部弄掉了。莫非是这个原因？我果断打开环境变量，发现里面确实还是只有java相关的变量。打开svn安装包，点击repair把程序修复了一遍。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">最后系统环境变量Path里面默默地多了一个分号和一句E:\TortoiseSVN\bin，再点commit，问题解决。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">真是因果报应啊...</span></span>