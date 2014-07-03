title: 'DevBytes- Making Apps Beautiful - Part 2- Spacing and Typography'
tags:
  - E文
  - FACK-Android
  - Google
  - UI
  - XML
  - 技术
id: 899
categories:
  - Android
  - 技术实践
  - 英文学习
date: 2013-08-08 13:17:35
---

<span style="color:#000000;"><span style="font-size:14px;"><object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" height="525" width="860"><param name="quality" value="high" /><param name="movie" value="http://player.youku.com/player.php/sid/XNTg2OTgzODg4/v.swf" /><embed height="525" pluginspage="http://www.macromedia.com/go/getflashplayer" quality="high" src="http://player.youku.com/player.php/sid/XNTg2OTgzODg4/v.swf" type="application/x-shockwave-flash" width="860"></embed></object></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Welcome to part 2 of the DevBytes series,&nbsp;Making Apps Beautiful. In this part, we&#39;re going to focus on the spacing and the typography of the list item as well&nbsp;the detail&nbsp;view to make it a lot more beautiful. Uh... so, let&#39;s just switch to our phone to see where we were up to at the end of the last part. And we modernized the app and have an action bar, got rid of the virtual navigation menu button, the legacy menu button. But, you know, you look at this and the text is particularly well formatted, the images are all in consistent size. When we go into the detailed view, the text is kind of going from edge to edge. There are still some glaring&nbsp;problems（突出的问题） from a design point of view. That&#39;s all.&nbsp;We&#39;re going to look at&nbsp;fixing up in this part.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, uh, let&#39;s go back to the slides and let&#39;s, let&#39;s see what we&#39;re going to try&nbsp;to achieve. The first thing we want to do is have a consistent set of heights for each of our list items. So we&#39;re using a 48dip rhythm&nbsp;as to the Android Design Guidelines. So, if we look at where we&#39;re now, this is actually what we wanna be. So, when we look at the slides now, you see the line items... the list items are really nicely spaced out. And we&#39;ve got some margins on either side of the detaile&nbsp;view&nbsp;as well.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, let&#39;s look at how we do that in code. So, switching over to code, we&#39;ve&nbsp;specified&nbsp;the dimension xml file. We wanna abstract out the values that we&#39;re using as much as possible. So, we&#39;ve got an 8dip vertical margin, which&nbsp;basically means that on the text views we&#39;re gonna have an 8dip margin above and below. So, the way we&#39;re going to do that is in this list item article, uh, in our linear layout that contains our text views, uh, we have this margin top and margin bottom defined, uh, with our list item vertical margin. Uh, in the detail view, for the fragment, we&#39;re also going to use a margin left and a margin right. And we&#39;re going to use that detail horizontal margin. And that&#39;s what&#39;s going to give us the spacing&nbsp;on either side of that detail view.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, switching back to the slides and we consider the kind of spacing we&#39;re after. What we want to do is, uh, once we&#39;ve put those dimensions in, we also want to, now, &nbsp;fix up the spacing with the thumbnails. And basically what we want to do here is eliminate that inconsistency that we have from thumbnail to thumbnail. The way we&#39;re going to do that is using the centerCrop attribute on the image view and set the scaleType. So that way is nicely consistent from each image to image.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">The next thing we want to do is focused on the typography. So, if you look at the image on the left, the fonts, uh, they are okay, uh, they&nbsp;are not particularly attractive. You can see very much on the right hand side&nbsp;that we&#39;ve paid a lot of attention to detail there and made some really good changes.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So let&#39;s look at what we did there. We&#39;re going to look at the two main text views. The top one is the title, and the subtitle<span style="line-height: 1.6em;">&nbsp;in each list item. So, you&#39;ll see that we&#39;ve used condensed font and made it bold for the title. Uh, for the subtitle, we&#39;ve actually changed the color of the text so that it&#39;s contrastless. And you may wanna abstract that out too. We also made it all caps. The other thing you wanna look at is fact that with both of these, we&#39;ve actually fixed the number of the lines&nbsp;and we&nbsp;used this property called ellipsize which would basically truncate the text and put dot dot dot if it&#39;s going&nbsp;pass that maximum line specification. So, it&#39;s just presented in a nice and consistent manner from item to item.&nbsp;</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, if we now look at the detail page, the other thing that we wanna do is, uh, make the author stand out a lot more. Uh, and what we are going to do is to use the property called textColorLink. The textColorLink is actually a property on the text view. And we&#39;ve just used the bacon color. Again, on the text views, all you do is add that one property and your links would stand out. But that also means that we need to make that author at the top subtitle a link as well. So the way we&#39;re doing that is actually for that part we&#39;re doing&nbsp;programmatically, where, uh in onCreateView, we go in the,&nbsp;fetch the font color and when we&#39;re going to&nbsp;put the&nbsp;author in that text view, we just put some html on either side to specify&nbsp;the font color. The other thing that we&#39;ll see that we&#39;re doing here is that for the article body as well, where uh, we&#39;re using a custom font that we&#39;ve shipped to their apk.&nbsp;</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">So, just getting to our checkpoint, if you see on the left-hand side, that&#39;s where we started. We&#39;ve come a long way. It&#39;s a really&nbsp;beautiful app on f?? now. Uh, so, join me for the next part for&nbsp;focusing on tablets.</span></span>

* * *

	<span style="color:#000000;"><span style="font-size:14px;">I Love Google ;)</span></span>

	&nbsp;