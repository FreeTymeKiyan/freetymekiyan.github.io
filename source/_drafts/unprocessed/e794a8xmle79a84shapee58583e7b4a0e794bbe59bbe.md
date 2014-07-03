title: 用XML的Shape元素画图
tags:
  - FACK-Android
  - UI
  - XML
id: 386
categories:
  - Android
  - 技术实践
date: 2013-01-21 11:29:26
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size: 14px;">在做卓普第一版的时候曾经遇到过这个问题。今天晚上总结一下以备不时之需。</span></span>

## 
	<span style="color:#000000;">探索</span>

### 
	<span style="color:#000000;">GradientDrawable&nbsp;Class Overview</span>

	<span style="color:#000000;"><span style="font-size:14px;">按钮和背景等的颜色渐变图片资源。可以用XML里面的&lt;shape&gt;元素定义。</span></span>

### 
	<span style="color:#000000;">XML语法</span>

<pre class="brush:xml;">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?&gt;
&lt;shape
    xmlns:android=&quot;http://schemas.android.com/apk/res/android&quot;
    android:shape=["rectangle" | "oval" | "line" | "ring"] &gt;
    &lt;corners
        android:radius=&quot;integer&quot;
        android:topLeftRadius=&quot;integer&quot;
        android:topRightRadius=&quot;integer&quot;
        android:bottomLeftRadius=&quot;integer&quot;
        android:bottomRightRadius=&quot;integer&quot; /&gt;
    &lt;gradient
        android:angle=&quot;integer&quot;
        android:centerX=&quot;integer&quot;
        android:centerY=&quot;integer&quot;
        android:centerColor=&quot;integer&quot;
        android:endColor=&quot;color&quot;
        android:gradientRadius=&quot;integer&quot;
        android:startColor=&quot;color&quot;
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] /&gt;
    &lt;padding
        android:left=&quot;integer&quot;
        android:top=&quot;integer&quot;
        android:right=&quot;integer&quot;
        android:bottom=&quot;integer&quot; /&gt;
    &lt;size
        android:width=&quot;integer&quot;
        android:height=&quot;integer&quot; /&gt;
    &lt;solid
        android:color=&quot;color&quot; /&gt;
    &lt;stroke
        android:width=&quot;integer&quot;
        android:color=&quot;color&quot;
        android:dashWidth=&quot;integer&quot;
        android:dashGap=&quot;integer&quot; /&gt;
&lt;/shape&gt;</pre>

### 
	<span style="color:#000000;"><span style="line-height: 1.2em;">元素</span></span>

#### 
	<span style="color:#000000;">&lt;shape&gt;</span>

##### 
	<span style="color:#000000;">概述</span>

	<span style="color:#000000;"><span style="font-size:14px;">形状图片资源，必须是根元素。</span></span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;">xmlns:android：<span style="line-height: 1.6em;">String. 必须，定义了XML的命名空间，必须为：&quot;http://schemas.android.com/apk/res/android&quot;</span></span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">android:shape：关键词，定义了形状的类型。合法的值有：<span style="font-size:14px;">​</span></span></span>

<table border="1" cellpadding="1" cellspacing="1" style="width: 500px;">
	<tbody>
		<tr>
			<td>
				<span style="color:#000000;">Value</span>
			</td>
			<td>
				<span style="color:#000000;">Description</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">&quot;rectangle&quot;</span>
			</td>
			<td>
				<span style="color:#000000;">长方形，填充包含的View。默认形状。</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">&quot;oval&quot;</span>
			</td>
			<td>
				<span style="color:#000000;">椭圆形，适应包含View的尺寸。</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">&quot;line&quot;</span>
			</td>
			<td>
				<span style="color:#000000;">一条贯穿包含View宽度的水平线。该形状需要&lt;stroke&gt;元素定义线宽</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">&quot;ring&quot;</span>
			</td>
			<td>
				<span style="color:#000000;">环形</span>
			</td>
		</tr>
	</tbody>
</table>

	<span style="color:#000000;"><span style="font-size:14px;">下面的参数只在android:shape=&quot;ring&quot;时使用：</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">android:innerRadius：Dimension.&nbsp;环形内部部分的半径（中间的那个洞）。</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">android:innerRadiusRatio：Float. 环形内部部分的半径，被表示成环形宽度的比例。比如，如果为5，内部部分半径为环形宽度的五分之一。被android:innerRadius复写，默认为9。</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">android:thickness：Dimension. 环形的厚度。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">android:thicknessRatio：Float. 环形的厚度，被表示成环形宽度的比例。比如，如果为2，环形的厚度为环形宽度的二分之一。被android:innerRadius复写，默认为3。</span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;">android:useLevel：Boolean. &quot;true&quot;如果该形状被用作LevelListDrawable。一般为&quot;false&quot;，否则你画的形状可能不出现。</span></span>

#### 
	<span style="color:#000000;"><span style="font-size:14px;">​</span>&lt;corners&gt;</span>

##### 
	<span style="color:#000000;">概述</span>

	<span style="color:#000000;"><span style="font-size:14px;">创建圆角的形状。仅适用于长方形。</span></span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:radius</span>：</span><span style="line-height: 1.6em; font-size: 14px;">Dimension. 所有四个角的半径。被每个接下来每个角单独的参数复写。</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:topLeftRadius</span>：</span><span style="line-height: 1.6em; font-size: 14px;">Dimension. 左上角半径。</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:topRightRadius</span>：</span><span style="line-height: 1.6em; font-size: 14px;">Dimension. 右上角半径。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:bottomLeftRadius</span>：</span><span style="line-height: 1.6em; font-size: 14px;">Dimension. </span><span style="font-size: 14px;">右</span><span style="line-height: 1.6em; font-size: 14px;">下角半径。</span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:bottomRightRadius</span>：</span><span style="line-height: 1.6em; font-size: 14px;">Dimension. </span><span style="font-size: 14px;">左</span><span style="line-height: 1.6em; font-size: 14px;">下角半径。</span></span>

	<span style="color:#000000;"><span style="line-height: 1.6em; font-size: 14px;">​注意：</span><span style="font-size:14px;">bottomLeftRadius是右下角，而不是左下角。</span></span>

	<span style="color:#000000;"><span style="line-height: 1.6em; font-size: 14px;">​注意：每个角必须一开始就提供一个大于1的半径值，否则所有角都不会是圆角。如果你希望特定的某个角不是圆角，一个解决方法是用</span><span style="font-size: 14px; line-height: 22.390625px;">android:radius设置一个大于1的值，接着用你想要的半径值复写每个角，不想是圆角的设为&quot;0dp&quot;。</span></span>

#### 
	<span style="color:#000000;">&lt;gradient&gt;</span>

##### 
	<span style="color:#000000;">概述</span>

	<span style="color:#000000;"><span style="font-size:14px;">指定一个渐变颜色的形状。</span></span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:angle：</span><span style="line-height: 1.6em;">Integer. 渐变角度。0是从左向右，90是从下往上。必须是45的倍数。默认值是0.</span></span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:centerX：Float.&nbsp;相对渐变中心的</span></span><span style="font-size: 14px; line-height: 22.390625px;">X坐标</span><span style="font-size:14px;"><span style="line-height: 1.6em;">值。0~1。</span></span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:centerY：Float. 相对渐变中心的Y</span></span><span style="font-size: 14px; line-height: 22.390625px;">坐标</span><span style="font-size:14px;"><span style="line-height: 1.6em;">值。</span></span><span style="font-size: 14px; line-height: 22.390625px;">0~1。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:centerColor：</span><span style="line-height: 1.6em;">Color. 可选的中间颜色。写成16进制值或颜色资源。</span></span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:endColor：</span><span style="line-height: 1.6em;">Color. 渐变结束的颜色。</span></span><span style="font-size: 14px; line-height: 22.390625px;">写成16进制值或颜色资源。</span></span>
6.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:gradientRadius：</span><span style="line-height: 1.6em;">Float. 渐变半径。只在</span></span><span style="font-size: 14px; line-height: 22.390625px;">android:type=&quot;radial&quot;时适用。</span></span>
7.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:startColor：</span><span style="line-height: 1.6em;">Color. 渐变起始的颜色。</span></span><span style="font-size: 14px; line-height: 22.390625px;">写成16进制值或颜色资源。</span></span>
8.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:type：</span><span style="line-height: 1.6em;">Keyword. 关键词。应用的渐变模式类型。合法的类型如下：</span></span></span>

        *   <table border="1" cellpadding="1" cellspacing="1" style="width: 500px;">
					<tbody>
						<tr>
							<td>
								**<span style="color:#000000;">Value</span>**
							</td>
							<td>
								**<span style="color:#000000;">Description</span>**
							</td>
						</tr>
						<tr>
							<td>
								<span style="color:#000000;">&quot;linear&quot;</span>
							</td>
							<td>
								<span style="color:#000000;">线性渐变。默认值。</span>
							</td>
						</tr>
						<tr>
							<td>
								<span style="color:#000000;">&quot;radial&quot;</span>
							</td>
							<td>
								<span style="color:#000000;">辐射渐变。起始颜色是中心的颜色。</span>
							</td>
						</tr>
						<tr>
							<td>
								<span style="color:#000000;">&quot;sweep&quot;</span>
							</td>
							<td>
								<span style="color:#000000;">环形的线性渐变。</span>
							</td>
						</tr>
					</tbody>
				</table>
9.  <span style="color:#000000;"><span style="font-size:14px;">android:useLevel：Boolean. &quot;true&quot;，如果用作LevelListDrawable。</span></span>

#### 
	<span style="color:#000000;">&lt;padding&gt;</span>

##### 
	<span style="color:#000000;">概述</span>

	<span style="color:#000000;"><span style="line-height: 1.6em; font-size: 14px;">这些值被应用</span><span style="line-height: 1.6em; font-size: 14px;">到包含View的元素上，而不是渐变形状上。</span></span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:left：</span><span style="line-height: 1.6em;">Dimension. 左边距。</span></span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">android:top：Dimension. 上边距。</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">android:right：Dimension. 右边距。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">android:bottom：Dimension. 下边距。</span></span>

#### 
	<span style="color:#000000;">&lt;size&gt;</span>

##### 
	<span style="color:#000000;">概述</span>

	<span style="font-size: 14px; line-height: 22.390625px; color: rgb(0, 0, 0);">渐变形状的大小。</span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:height：</span>Dimension. 形状的高度。</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">android:width：Dimension. 形状的宽度。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">​注意：默认情况下，形状按这里定义的比例缩放成容器View的大小。当你在ImageView里面使用形状的时候，通过设置android:scaleType=&quot;center&quot;来限制缩放。</span></span>

#### 
	<span style="color:#000000;">&lt;solid&gt;</span>

	<span style="color:#000000;"><span style="font-size:14px;">填充形状的实心颜色。</span></span>

##### 
	<span style="color:#000000;">Attributes</span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:color：</span><span style="line-height: 1.6em;">Color. 应用到形状上的颜色。</span></span><span style="font-size: 14px; line-height: 22.390625px;">写成16进制值或颜色资源。</span></span>

#### 
	<span style="color:#000000;">&lt;stroke&gt;</span>

	<span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">形状的笔画线。形象点叫描边。</span>

##### 
	<span style="color:#000000;">Attributes</span>

1.  <span style="color:#000000;"><span style="font-size:14px;">android:width：Dimension. 笔画线的宽度。</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">android:color：Color. 笔画线的颜色。</span><span style="font-size: 14px; line-height: 22.390625px;">写成16进制值或颜色资源。</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">android:dashGap：Dimension. 笔画线破折线的间距。只在设置了android:dashWidth时有效。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">android:dashWidth：Dimension. 每个破折线的尺寸。只在设置了android:dashGap时有效。</span></span>

#### 
	<span style="color:#000000;"><span style="line-height: 1.6em;">Example:</span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">保存在res/drawable/gradient_box.xml的XML文件：</span></span></span>

<pre class="brush:xml;">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?&gt;
&lt;shape xmlns:android=&quot;http://schemas.android.com/apk/res/android&quot;
    android:shape=&quot;rectangle&quot;&gt;
    &lt;gradient
        android:startColor=&quot;#FFFF0000&quot;
        android:endColor=&quot;#80FF00FF&quot;
        android:angle=&quot;45&quot;/&gt;
    &lt;padding android:left=&quot;7dp&quot;
        android:top=&quot;7dp&quot;
        android:right=&quot;7dp&quot;
        android:bottom=&quot;7dp&quot; /&gt;
    &lt;corners android:radius=&quot;8dp&quot; /&gt;
&lt;/shape&gt;</pre>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">这个布局XML把定义好的形状应用到了View上：</span></span></span>

<pre class="brush:xml;">
&lt;TextView
    android:background=&quot;@drawable/gradient_box&quot;
    android:layout_height=&quot;wrap_content&quot;
    android:layout_width=&quot;wrap_content&quot; /&gt;</pre>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">用以下写法在代码内使用定义好的形状：</span></span></span>

<pre class="brush:java;">
Resources res = getResources();
Drawable shape = res. getDrawable(R.drawable.gradient_box);

TextView tv = (TextView)findViewByID(R.id.textview);
tv.setBackground(shape);</pre>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">android:angle网上有各种说法，这里，我说说自己的实验结果，渐变的时候，最原始的，即android:angle＝&ldquo;0&rdquo;时，是从左到右，按照开始颜色到结束颜色来渲染的，android:angle＝&ldquo;90&rdquo;是从上到下来渲染的，android:angle＝&ldquo;180&rdquo;是从右到左来渲染的，android:angle＝&ldquo;360&rdquo;和android:angle＝&ldquo;0&rdquo;是一样的，所以这里应该是这样的，渲染时按照最原始的渲染色板（把控件内部看作一块可以绕中心旋转的板子）围绕控件中心来旋转相应的度数，即android:angle里面的值就是所需要旋转的角度，只是这个旋转角度必须是45的整数倍</span></span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">全部内容可以总结到一张表中。</span></span>

<table align="left" border="1" cellpadding="1" cellspacing="1" style="width: 500px;">
	<caption>

			<span style="color:#000000;">XML 参数</span>

	</caption>
	<thead>
		<tr>
			<th scope="col" style="text-align: left;">
				<span style="color:#000000;">Attribute Name</span>
			</th>
			<th scope="col" style="text-align: left;">
				<span style="color:#000000;">Description</span>
			</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				<span style="color:#000000;">android:angle</span>
			</td>
			<td>
				<span style="color:#000000;">渐变的角度。0是从左到右，90是从下到上</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:bottom</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状内的下方内边距</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:centerColor</span>
			</td>
			<td>
				<span style="color:#000000;">中心颜色，可选</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:centerX</span>
			</td>
			<td>
				<span style="color:#000000;">形状内渐变起点X坐标</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:centerY</span>
			</td>
			<td>
				<span style="color:#000000;"><span style="font-size: 13px;">形状内渐变起点Y坐标</span></span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:color</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的实心颜色</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:color</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状笔画的颜色</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:dashGap</span>
			</td>
			<td>
				<span style="color:#000000;">笔画里破折号之间的间距</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:dashWidth</span>
			</td>
			<td>
				<span style="color:#000000;">笔画里破折号的长度</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:endColor</span>
			</td>
			<td>
				<span style="color:#000000;">渐变结束处的颜色</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:gradientRadius</span>
			</td>
			<td>
				<span style="color:#000000;">渐变半径，仅在放射渐变中使用</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:height</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的高度</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:innerRadius</span>
			</td>
			<td>
				<span style="color:#000000;">环形的内径</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:innerRadiusRatio</span>
			</td>
			<td>
				<span style="color:#000000;">按环形宽度比例表示的环形内径</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:left</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的左内边距</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:right</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的右内边距</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:shape</span>
			</td>
			<td>
				<span style="color:#000000;">表示用渐变填充什么形状</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:startColor</span>
			</td>
			<td>
				<span style="color:#000000;">渐变的起始颜色</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:thickness</span>
			</td>
			<td>
				<span style="color:#000000;">环形的厚度</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:thicknessRatio</span>
			</td>
			<td>
				<span style="color:#000000;">按环形宽度比例表示的环形厚度</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:top</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的上内边距</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:type</span>
			</td>
			<td>
				<span style="color:#000000;">渐变的类型</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:useLevel</span>
			</td>
			<td>
				<span style="color:#000000;">（文档没写，不知道为什么）</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:useLevel</span>
			</td>
			<td>
				<span style="color:#000000;">表示图片所在图层是否影响渐变描绘的方式</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:visible</span>
			</td>
			<td>
				<span style="color:#000000;">表示图片资源是否初始化为可见的</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:width</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状的宽度</span>
			</td>
		</tr>
		<tr>
			<td>
				<span style="color:#000000;">android:width</span>
			</td>
			<td>
				<span style="color:#000000;">渐变形状笔画的宽度</span>
			</td>
		</tr>
	</tbody>
</table>

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

	&nbsp;

	&nbsp;

* * *

	<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">真是多啊，OK啦！</span>