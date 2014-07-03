title: 'DevBytes- Making Apps Beautiful - Part 1 - Modernize'
tags:
  - E文
  - FACK-Android
  - Google
  - UI
  - XML
  - 技术
id: 860
categories:
  - Android
  - 技术实践
  - 英文学习
date: 2013-08-03 12:18:25
---

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" height="525" width="860"><param name="quality" value="high" /><param name="movie" value="http://player.youku.com/player.php/sid/XNTg2OTU2OTY0/v.swf" /><embed height="525" pluginspage="http://www.macromedia.com/go/getflashplayer" quality="high" src="http://player.youku.com/player.php/sid/XNTg2OTU2OTY0/v.swf" type="application/x-shockwave-flash" width="860"></embed></object>

	<span style="color:#000000;"><span style="font-size:14px;">Today, we&#39;re going to be talking about how to make Android Apps beautiful. And we&#39;re going to start off by looking at an app&nbsp;that hasn&#39;t had much love. This is actually the first in a multipart series of Dev.Bytes. And today&#39;s episode is going to focus on modernizing, just very... doing some very basic modernization techniques with your app.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, uh, let&#39;s uh, switch to the phone, and let&#39;s look at our app. Now, the first thing&nbsp;I want to actually point out is our app icon. The app icon is the identity of your app. It&#39;s the identity of your app not only when you uses device, but also on the </span></span><span style="color:#FF0000;"><span style="font-size:14px;">place tool</span></span><span style="color:#000000;"><span style="font-size:14px;">(没理解). So, making this, uh, this piece of identity stand out and look polished is really important. This app does not qualify(is not qualified?)&nbsp;of having done a good job.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So we now watch the app, and you see a very basic and common pattern that we normally see -&nbsp;it&#39;s a listview. We have some thumbnails in the left-hand side and some... some text on the right. And straightway we see that this app is actually an old-styled&nbsp;app. It uses a title bar at the top. It has a legacy menu button, uh, at the bottom, which shouldn&#39;t be there when a device has&nbsp;virtual navigation buttons. The images, are all in consistently size from each list item to list item. When we go to the details, again, we&#39;ve got bad title bar, but all the text is </span></span><span style="color:#FF0000;"><span style="font-size:14px;">j??</span></span><span style="color:#000000;"><span style="font-size:14px;"> up(挤到一起) all the way to the edges. So, let&#39;s look at what we do to fix this, uh, as a first step.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">What we&#39;re going to do before we switch to code, let&#39;s have a look at what the&nbsp;target is going to be. This is our first stage that we&#39;re going to get to, which is an action bar, we&#39;ve got action items, we&#39;ve got an overflow menu as well, and we&#39;re doing some modifications.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, dating(??)&nbsp;back to that, let&#39;s now&nbsp;look at what we started with. Let&#39;s look at some code. Uh, what we have here is actually the manifest file. We&#39;ve got the minimum sdk version set to 10, which is great, we&#39;re targeting users from Gingerbread and up. But our target sdk version is also set to api level 10\. This should always be set to the most recent version of, see, uh, the Android SDK, which in this case is api 17\. So, the first change we&#39;ll make is to make that 17\. This would automatically get rid of the menu button on devices that have virtual navigation buttons.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">The other thing we want to do is actually change the style. We&#39;re using the Theme.Light style. So, what we&#39;re going to do instead is use our own style that is also back with compatible. So, our manifest file is actually going to change into something like this, where we&#39;ve got api 17 here, and we&#39;ve got our own AppTheme. The way we&#39;re going to define this AppTheme is by having a styles.xml in our values directory, which just extends Theme.Light. This is our base theme, what we&#39;re then going to do&nbsp;is that we&#39;re going to have a styles.xml for v-11, api 11 to 13, we&#39;ll get theme holo light, and api 14 and upwards, we&#39;ll get Hololight with dark action bar.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So once we&#39;ve done this, we&#39;re going to get a nice-styled action bar there. Uh, and we&#39;re going to get rid of the menu button. But what we might also want to do is brand our direction bar to be more consistent with the studies&nbsp;of our organization or serviece that we provide. Switching to the slides, now, what we actually want to do, is take our action bar the way as it is now&nbsp; and make it orange basically and change the icon as well. Again, to be consistent with our brand. </span></span>

	<span style="color:#000000;"><span style="font-size:14px;">The way we&#39;re actually going to do that is use this third-party tool, called the Android Action Bar Style Generator, developed by Jeff Gilfelt, from the Android Developer Community. Great utility where you can go and specify various attributes about&nbsp;how you want your action bar to be presented. And then what it does is provide you with a zip file that you can just extract over your project and you&#39;re done.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, that wraps up part one. I just want to show you how far we&#39;ve gotten in this checkpoint. We&#39;ve gotten something that obviously looked&nbsp;old, to&nbsp;something that looks a lot more&nbsp;modern now.&nbsp;</span></span>

* * *

	<span style="color:#000000;"><span style="font-size:14px;">I Love&nbsp;Goggle&nbsp;:)</span></span>

	&nbsp;