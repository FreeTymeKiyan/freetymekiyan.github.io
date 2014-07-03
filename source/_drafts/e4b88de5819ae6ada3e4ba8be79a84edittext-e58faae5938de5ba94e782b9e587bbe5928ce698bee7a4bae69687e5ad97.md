title: '不做正事的EditText -- 只响应点击和显示文字'
tags:
  - FACK-Android
  - UI
id: 213
categories:
  - Android
  - 技术实践
date: 2013-01-10 15:06:35
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">这个效果的实现又让我想起几个月以前卓普开发的时候。当时设计的页面上有一个列表，列表最后是一行评论，点击这个EditText会调到评论的编辑页面。我当时试来试去，setFocusable，setClickable等等各种方法、组合都试过了，就是不成功。后来转念一下，直接在ListView的点击事件监听器里面响应不就行了吗？然后果然实现了。这次又做另外一个项目，又要实现类似的功能，是点击EditText之后有一些选项，选了之后把选了的内容显示到EditText当中。好吧，这应验了一句古话：&ldquo;</span><span style="font-size: 14px;">你所逃避的问题，总会在以后的某个时候再次出现。</span><span style="font-size: 14px;">&rdquo;（你没听过这个古话吗？不是吧&hellip;&hellip;）</span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">用如下代码实现只响应点击事件和显示文字的EditText控件：</span></span>

<pre class="brush:java;">
edtitext.setFocusable(false); // 不让控件获得焦点，避免绿框
edtitext.setClickable(true); // 使该控件能够响应点击事件
edtitext.setInputType(InputType.TYPE_NULL); // 不弹出输入法
edtitext.setOnClickListener(new OnClickListener() {

    @Override
    public void onClick(View v) {
        /*TODO 点击事件响应*/
    }
});</pre>

	<span style="color:#000000;"><span style="font-size:14px;">当然要记得，在xml里面给对应的EditText加上android:cursorVisible=&ldquo;false&rdquo;这一行，去掉光标。</span></span>

* * *

	<span style="color:#000000;"><span style="font-size:14px;">吐槽一下，这一切都是拜一个产品经理的YY所赐。其实仔细想想我也不一定要用EditText去实现，只需要把Button的背景换成输入框的样子就好了。</span></span>

## 
	<span style="color:#000000;">更新</span>

	<span style="color:#000000;"><span style="font-size:14px;">2013-01-10: </span></span>

	<span style="color:#000000;"><span style="font-size:14px;">edtitext.setInputType(InputType.TYPE_NULL); 会导致EditText控件自动换行失效。</span></span>

	&nbsp;