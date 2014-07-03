title: 'Remove ''@override'' annotation解决办法'
tags:
  - Debug
  - Eclipse
  - FACK-Android
id: 161
categories:
  - Android
  - 技术实践
date: 2012-11-01 17:07:10
---

## 
	前言

	<span style="color:#000000;"><span style="font-size: 14px; ">最近同时使用项目的台式机和自己的笔记本写代码，蛋疼到了极点。在笔记本上调得好好的代码，换了项目组的台式机老是有问题。比如说这个&ldquo;Remove &#39;@override&#39; annotation&rdquo;的错误就让我蛋疼不已...</span></span>

## 
	探索

	<span style="color:#000000;"><span style="font-size: 14px; ">一开始以为是下下来的包不完整，因为没有生成一个.jar文件。重新下了一次结果还是一样，显然不是这个问题。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">后来把笔记本上的工程拷到了台式机上，还是报同样的错误。这让我确实不是代码的问题，应该是某个配置出了问题。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">忍不住google了一把，发现遇到过这个问题的人还真不少。一般的解法是在eclipse的window选项卡中打开preferences，再把java compiler的级别调到1.6及以上。原因是1.5的时候interface不支持override，到1.6又支持了。</span></span>

	<span style="color:#000000;"><span style="font-size: 14px; ">我试了结果还是不行。继续找方法，发现单个的project也可以设置。好吧，把单个的project设置成了1.7之后，重新build，终于搞定了。</span></span>

	<span style="color: rgb(0, 0, 0); font-size: 14px; ">同时用两个机子写代码真是伤不起啊...以后这些不重要的代码我还是用回git hub吧...</span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">碰到Remove &#39;@override&#39; annotation的问题，选择你的工程，同时按下alt+enter键进入project properties，在左边找到Java Compiler，将1.5改为1.6或1.7，点apply或者ok，重新build过后问题解决。</span></span>