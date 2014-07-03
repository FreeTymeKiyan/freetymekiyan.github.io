title: Android-ViewFlow学习
tags:
  - FACK-Android
  - UI
id: 152
categories:
  - Android
  - 技术实践
date: 2012-10-31 21:29:54
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">最近要做的新版本的应用需要有像墨迹天气那样的左右滑动的界面。其实之前就已经了解过，ViewFlipper和ViewPager等类似的东西都可以实现，还在第二版的应用中实现了滑动一半的抽屉界面。本来想用这两个来试试实现的，在搜索的过程中又发现了一个Git上的第三方工程ViewFlow，下了个包下来试了试，体验不错。那么就研究一下这个吧！</span></span>

## 
	<span style="color:#000000;">View Flow for Android</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">ViewFlow提供可滑动的ViewGroup，由ViewAdapter填充，</span><span style="font-size: 14px; ">是一个Library Project。ViewFlow</span><span style="font-size: 14px; ">适用于View数量不确定的情况（可以动态调整数据源，然后调用adapter.notifyDataSetChanged()）。如果数量固定，看看Fragments和ViewPager。</span></span>

## 
	<span style="color:#000000;">用法</span>

### 
	<span style="color:#000000;"><span style="font-size: 14px; ">在la​yout中</span></span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`&lt;org.taptwo.android.widget.ViewFlow
    android:id=&quot;@+id/viewflow&quot;
    app:sidebuffer=&quot;5&quot;
    /&gt;
`</span></pre>

	<span style="color:#000000;"><span style="font-size: 14px; ">在你要用ViewFlow的layout中如上代码。</span><span style="font-size: 14px; ">app:sidebuffer是可选择的，决定了当前显示的View两边要缓冲多少view。默认为3，这样一次就可以预加载7个View。</span></span>

### 
	<span style="color:#000000;">在activity中</span>

<pre>
<span style="color:#000000;">`ViewFlow viewFlow = (ViewFlow) findViewById(R.id.viewflow);
viewFlow.setAdapter(myAdapter);
`</span></pre>

	<span style="color:#000000;">默认的初始位置为0，用以下代码可以让初始位置不是0。</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`viewFlow.setAdapter(myAdapter, 8);`</span></pre>

	<span style="color:#000000;">如果在setAdapter之后马上setSelection可能会加载一些不必要的view导致性能下降。</span>

* * *

### 
	<span style="color:#000000;">监听屏幕改变的事件</span>

	<span style="color: rgb(0, 0, 0); font-family: Helvetica, arial, freesans, clean, sans-serif; font-size: 14px; ">如果需要监听屏幕变化的事件，那么需要自己实现一个ViewFlow.ViewSwitchListener，然后把这个方法传递给setOnViewSwitchListener方法。</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`viewFlow.setOnViewSwitchListener(new ViewSwitchListener() {
    public void onSwitched(View v, int position) {
        // Your code here
    }
});`</span></pre>

* * *

### 
	<span style="color:#000000;">监听初始化view的事件</span>

	<span style="color: rgb(0, 0, 0); font-family: Helvetica, arial, freesans, clean, sans-serif; font-size: 14px; ">如果需要lazy View的初始化，就自己实现ViewFlow.ViewLazyInitializeListener，然后传递给setOnViewLazyInitializeListener。</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`viewFlow.setOnViewLazyInitializeListener(new ViewLazyInitializeListener() {
    public void onViewLazyInitialize(View view, int position) {
        // Your code here e.g.
        ((MyAdapter)((AbsListView)view).getAdapter()).initializeData();
    }
});`</span></pre>

* * *

### 
	<span style="color:#000000;">Flow Indicator</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">可以自己实现一个指针，也可以用现有的指针。</span></span>

* * *

#### 
	<span style="color:#000000;">圆点型指针</span>

	<span style="color: rgb(0, 0, 0); font-family: Helvetica, arial, freesans, clean, sans-serif; font-size: 14px; line-height: 22px; ">圆点指针，先在xml里面引用</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`&lt;org.taptwo.android.widget.CircleFlowIndicator
    android:padding=&quot;10dip&quot; android:layout_height=&quot;wrap_content&quot;
    android:layout_width=&quot;wrap_content&quot; android:id=&quot;@+id/viewflowindic&quot;
    android:background=&quot;#00000000&quot;/&gt;
`</span></pre>

	<span style="color: rgb(0, 0, 0); ">然后再用ViewFlow.setFlowIndicator方法把ViewFlow和FlowIndicator连接起来</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`CircleFlowIndicator indic = (CircleFlowIndicator) findViewById(R.id.viewflowindic);
viewFlow.setFlowIndicator(indic);
`</span></pre>

	<span style="color:#000000;">支持以下属性:&nbsp;`activeColor`（激活状态颜色），&nbsp;`inactiveColor`</span><span style="color: rgb(0, 0, 0); ">（非激活状态颜色），</span><span style="color: rgb(0, 0, 0); ">&nbsp;</span>`activeType`<span style="color: rgb(0, 0, 0); ">&nbsp;</span><span style="color: rgb(0, 0, 0); ">（激活状态样式，填充或描边），</span><span style="color: rgb(0, 0, 0); ">&nbsp;</span>`inactiveType`<span style="color: rgb(0, 0, 0); ">&nbsp;</span><span style="color: rgb(0, 0, 0); ">（非激活状态样式，填充或描边），</span><span style="color: rgb(0, 0, 0); ">&nbsp;</span><span style="color: rgb(0, 0, 0); ">&nbsp;</span>`fadeOut`<span style="color: rgb(0, 0, 0); ">&nbsp;（隐藏时间，单位是毫秒，0为不隐藏）&nbsp;</span>`radius`<span style="color: rgb(0, 0, 0); ">（半径）</span>

* * *

#### 
	<span style="color:#000000;">标题型指针</span>

	<span style="color: rgb(0, 0, 0); font-family: Helvetica, arial, freesans, clean, sans-serif; font-size: 14px; line-height: 22px; ">先在xml里面引用</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`    &lt;org.taptwo.android.widget.TitleFlowIndicator
        android:id=&quot;@+id/viewflowindic&quot; android:layout_height=&quot;wrap_content&quot;
        android:layout_width=&quot;fill_parent&quot;
        app:footerLineHeight=&quot;2dp&quot;
        app:footerTriangleHeight=&quot;10dp&quot; app:textColor=&quot;#FFFFFFFF&quot; app:selectedColor=&quot;#FFFFC445&quot;
        app:footerColor=&quot;#FFFFC445&quot; app:titlePadding=&quot;10dp&quot; app:textSize=&quot;11dp&quot; app:selectedSize=&quot;12dp&quot;
        android:layout_marginTop=&quot;10dip&quot;
        app:clipPadding=&quot;5dp&quot; /&gt;
`</span></pre>

	<span style="color: rgb(0, 0, 0); ">再用setTitleProvider方法，然后再用ViewFlow.setFlowIndicator方法把ViewFlow和FlowIndicator连接起来。</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`    TitleFlowIndicator indicator = (TitleFlowIndicator) findViewById(R.id.viewflowindic);
    indicator.setTitleProvider(myTitleProvider);
    viewFlow.setFlowIndicator(indicator);`</span></pre>

* * *

## 
	<span style="color:#000000;">构建一个jar包</span>

	<span style="color:#000000;"><span style="font-size: 14px; ">在android-viewflow/viewflow的路径下运行ant jar来构建一个jar包。</span></span>

* * *

## 
	<font color="#000000">提醒</font>

	<span style="color: rgb(0, 0, 0); font-size: 14px; ">如果api level&lt;8需要在你的Activity中实现onConfigurationChanged方法。</span>

<pre style="margin-top: 15px; margin-bottom: 15px; padding: 6px 10px; border: 1px solid rgb(204, 204, 204); font-size: 13px; font-family: Consolas, 'Liberation Mono', Courier, monospace; background-color: rgb(248, 248, 248); line-height: 19px; overflow: auto; border-top-left-radius: 3px; border-top-right-radius: 3px; border-bottom-right-radius: 3px; border-bottom-left-radius: 3px; color: rgb(51, 51, 51); ">
<span style="color:#000000;">`    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        viewFlow.onConfigurationChanged(newConfig);
    }`</span></pre>

* * *

	<span style="color:#000000;"><span style="font-size: 14px; ">EOF</span></span>

	&nbsp;