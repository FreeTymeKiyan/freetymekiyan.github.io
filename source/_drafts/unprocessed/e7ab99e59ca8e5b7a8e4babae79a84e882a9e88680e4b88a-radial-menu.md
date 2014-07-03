title: '站在巨人的肩膀上-Radial Menu'
tags:
  - FACK-Android
  - GitHub
  - UI
id: 777
categories:
  - Android
  - 技术实践
date: 2013-07-05 23:08:42
---

<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">这篇文章属于《</span><span style="line-height: 1.6em;">站在巨人的肩膀上</span><span style="line-height: 1.6em;">》系列，每篇一个，逐个学习并介绍我觉得有用或有趣的开源Android项目。</span></span></span>

* * *

	<span style="color:#000000;"><span style="font-size:14px;">介绍一个很新颖的自定义控件，Radial Menu。这个控件需要在一个容器View中显示。用手指点击容器View的某个地方便会显示。然后手指向对应的方向滑动，就能选择对应的菜单项。手指松开后相应事件并进行相应的操作。</span></span>

	[![](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705214544-180x300.jpg "点击屏幕显示菜单")](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705214544.jpg)

	[![](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705214322-180x300.jpg "移动手指选中选项")](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705214322.jpg)

	[![](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705215135-180x300.jpg "选中选项响应事件")](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/07/QQ图片20130705215135.jpg)

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	&nbsp;

	<span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 22.390625px;">左边是点击屏幕的时候显示的菜单&nbsp;</span>

	<span style="color: rgb(0, 0, 0); font-size: 14px; text-align: center;">中间是手指向上移动（不离开屏幕）后选中选项的效果&nbsp;</span>

	<span style="color:#000000;"><span style="font-size:14px;">右边是松开手指选中item_1的效果</span></span>

* * *

## 
	基本使用方法

	<span style="color:#000000;"><span style="font-size:14px;">RadialMenu控件由RadialMenuView.java，RadialMenuRenderer.java，RadialMenuItem.java，RadialMenuHelper.java四个部分组成。我把RadialMenu打包成了一个jar，方便使用。把RadialMenu.jar放到项目的libs文件夹下，就可以在项目中使用这个自定义控件了。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">RadialMenu存在一个父View中，所以我们需要一个FrameLayout作它的容器。</span></span>

<pre class="brush:java;">
flHolder = (FrameLayout) findViewById(R.id.fragment_holder);</pre>

	<span style="color:#000000;"><span style="font-size:14px;">我们写了一个Renderer专门负责画出这个控件，需要初始化。</span></span>

<pre class="brush:java;">
// 参数分别为：容器View，文字是否居中，环形厚度和圆形控件半径
mRenderer = new RadialMenuRenderer(flHolder, true, 30, 60);</pre>

	<span style="font-size:14px;"><span style="color:#000000;">图示的一个RadialMenu当中有三个子项，每个菜单项都是一个RadialItem，需要初始化。</span></span>

<pre class="brush:java;">
// 参数分别为：item的id，item的name
menuItem1 = new RadialMenuItem(
        getResources().getString(R.string.menu_item_1), 
        getResources().getString(R.string.menu_item_1));
menuItem2 = new RadialMenuItem(
        getResources().getString(R.string.menu_item_2), 
        getResources().getString(R.string.menu_item_2));
menuItem3 = new RadialMenuItem(
        getResources().getString(R.string.menu_item_3), 
        getResources().getString(R.string.menu_item_3));</pre>

	<span style="color:#000000;"><span style="font-size:14px;">Renderer需要知道每个菜单项才能画出菜单，所以还得把menuItem合起来传给Renderer。</span></span>

<pre class="brush:java;">
private ArrayList&lt;RadialMenuItem&gt; mMenuItems = new ArrayList&lt;RadialMenuItem&gt;(0);

mMenuItems.add(menuItem1);
mMenuItems.add(menuItem2);
mMenuItems.add(menuItem3);

mRenderer.setRadialMenuContent(mMenuItems); </pre>

	<span style="color:#000000;"><span style="font-size:14px;">把渲染过后的View添加到容器View当中。</span></span>

<pre class="brush:java;">
// renderView方法会把容器View的点击事件传给控件来处理
flHolder.addView(mRenderer.renderView());</pre>

	<span style="color:#000000;"><span style="font-size:14px;">给每个菜单项添加点击事件。</span></span>

<pre class="brush:java;">
menuItem1.setOnRadialMenuClickListener(new OnRadailMenuClick() {

    @Override
    public void onRadailMenuClickedListener(String id) {
        Toast.makeText(RadialMenuActivity.this, 
                R.string.menu_item_1, 
                Toast.LENGTH_SHORT).show(); // 显示一个Toast
        }
});</pre>

* * *

## 
	<span style="color:#000000;">其他自定义方法</span>

	<span style="color:#000000;"><span style="font-size:14px;">设置背景颜色</span></span>

<pre class="brush:java;">
// 默认颜色为0x80444444
mRenderer.setMenuBackgroundColor(int mMenuBackgroundColor);
</pre>

	<span style="color:#000000;"><span style="font-size:14px;">设置被选中颜色</span></span>

<pre class="brush:java;">
// 默认颜色为0x8033b5e5
mRenderer.setMenuSelectedColor(int mMenuSelectedColor);
</pre>

	<span style="color:#000000;"><span style="font-size:14px;">设置文字颜色</span></span>

<pre class="brush:java;">
// 默认颜色为白色（0xffffffff）
mRenderer.setMenuTextColor(int mMenuTextColor)</pre>

	<span style="color:#000000;"><span style="font-size:14px;">设置边界颜色</span></span>

<pre class="brush:java;">
// 默认颜色为0xff777777
mRenderer.setMenuBorderColor(int mMenuBorderColor);
</pre>

* * *

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">使用该控件的步骤</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">把RadiaMenu.jar放到项目的libs文件夹下；</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">​初始化容器View；</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">初始化Renderer；</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">初始化菜单项；</span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;">把菜单项添加到Renderer；</span></span>
6.  <span style="color:#000000;"><span style="font-size:14px;">渲染控件View，并添加到容器View中；</span></span>
7.  <span style="color:#000000;"><span style="font-size:14px;">给菜单项添加点击事件。<span style="color:#000000;"><span style="font-size:14px;">​</span></span></span></span>

* * *

	<span style="color:#000000;"><span style="font-size:14px;">文章只介绍了基本的接口，如果需要更多的自定义效果可以下载工程源码：</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Github: [https://github.com/FreeTymeKiyan/CoolWidgets](https://github.com/FreeTymeKiyan/CoolWidgets)</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">以后我介绍的其他自定义控件也会陆续加到这个工程当中。</span></span>