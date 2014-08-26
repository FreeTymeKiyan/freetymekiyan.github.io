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
	前言

	在做卓普第一版的时候曾经遇到过这个问题。今天晚上总结一下以备不时之需。

## 
	探索

### 
	GradientDrawable&nbsp;Class Overview

	按钮和背景等的颜色渐变图片资源。可以用XML里面的&lt;shape&gt;元素定义。

### 
	XML语法


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
&lt;/shape&gt;

### 
	元素

#### 
	&lt;shape&gt;

##### 
	概述

	形状图片资源，必须是根元素。

##### 
	Attributes

1.  xmlns:android：String. 必须，定义了XML的命名空间，必须为：&quot;http://schemas.android.com/apk/res/android&quot;
2.  android:shape：关键词，定义了形状的类型。合法的值有：​


	
		
			
				Value
			
			
				Description
			
		
		
			
				&quot;rectangle&quot;
			
			
				长方形，填充包含的View。默认形状。
			
		
		
			
				&quot;oval&quot;
			
			
				椭圆形，适应包含View的尺寸。
			
		
		
			
				&quot;line&quot;
			
			
				一条贯穿包含View宽度的水平线。该形状需要&lt;stroke&gt;元素定义线宽
			
		
		
			
				&quot;ring&quot;
			
			
				环形
			
		
	


	下面的参数只在android:shape=&quot;ring&quot;时使用：

1.  android:innerRadius：Dimension.&nbsp;环形内部部分的半径（中间的那个洞）。
2.  android:innerRadiusRatio：Float. 环形内部部分的半径，被表示成环形宽度的比例。比如，如果为5，内部部分半径为环形宽度的五分之一。被android:innerRadius复写，默认为9。
3.  android:thickness：Dimension. 环形的厚度。
4.  android:thicknessRatio：Float. 环形的厚度，被表示成环形宽度的比例。比如，如果为2，环形的厚度为环形宽度的二分之一。被android:innerRadius复写，默认为3。
5.  android:useLevel：Boolean. &quot;true&quot;如果该形状被用作LevelListDrawable。一般为&quot;false&quot;，否则你画的形状可能不出现。

#### 
	​&lt;corners&gt;

##### 
	概述

	创建圆角的形状。仅适用于长方形。

##### 
	Attributes

1.  android:radius：Dimension. 所有四个角的半径。被每个接下来每个角单独的参数复写。
2.  android:topLeftRadius：Dimension. 左上角半径。
3.  android:topRightRadius：Dimension. 右上角半径。
4.  android:bottomLeftRadius：Dimension. 右下角半径。
5.  android:bottomRightRadius：Dimension. 左下角半径。

	​注意：bottomLeftRadius是右下角，而不是左下角。

	​注意：每个角必须一开始就提供一个大于1的半径值，否则所有角都不会是圆角。如果你希望特定的某个角不是圆角，一个解决方法是用android:radius设置一个大于1的值，接着用你想要的半径值复写每个角，不想是圆角的设为&quot;0dp&quot;。

#### 
	&lt;gradient&gt;

##### 
	概述

	指定一个渐变颜色的形状。

##### 
	Attributes

1.  android:angle：Integer. 渐变角度。0是从左向右，90是从下往上。必须是45的倍数。默认值是0.
2.  android:centerX：Float.&nbsp;相对渐变中心的X坐标值。0~1。
3.  android:centerY：Float. 相对渐变中心的Y坐标值。0~1。
4.  android:centerColor：Color. 可选的中间颜色。写成16进制值或颜色资源。
5.  android:endColor：Color. 渐变结束的颜色。写成16进制值或颜色资源。
6.  android:gradientRadius：Float. 渐变半径。只在android:type=&quot;radial&quot;时适用。
7.  android:startColor：Color. 渐变起始的颜色。写成16进制值或颜色资源。
8.  android:type：Keyword. 关键词。应用的渐变模式类型。合法的类型如下：

        *   
					
						
							
								**Value**
							
							
								**Description**
							
						
						
							
								&quot;linear&quot;
							
							
								线性渐变。默认值。
							
						
						
							
								&quot;radial&quot;
							
							
								辐射渐变。起始颜色是中心的颜色。
							
						
						
							
								&quot;sweep&quot;
							
							
								环形的线性渐变。
							
						
					
				
9.  android:useLevel：Boolean. &quot;true&quot;，如果用作LevelListDrawable。

#### 
	&lt;padding&gt;

##### 
	概述

	这些值被应用到包含View的元素上，而不是渐变形状上。

##### 
	Attributes

1.  android:left：Dimension. 左边距。
2.  android:top：Dimension. 上边距。
3.  android:right：Dimension. 右边距。
4.  android:bottom：Dimension. 下边距。

#### 
	&lt;size&gt;

##### 
	概述

	渐变形状的大小。

##### 
	Attributes

1.  android:height：Dimension. 形状的高度。
2.  android:width：Dimension. 形状的宽度。

	​注意：默认情况下，形状按这里定义的比例缩放成容器View的大小。当你在ImageView里面使用形状的时候，通过设置android:scaleType=&quot;center&quot;来限制缩放。

#### 
	&lt;solid&gt;

	填充形状的实心颜色。

##### 
	Attributes

	android:color：Color. 应用到形状上的颜色。写成16进制值或颜色资源。

#### 
	&lt;stroke&gt;

	形状的笔画线。形象点叫描边。

##### 
	Attributes

1.  android:width：Dimension. 笔画线的宽度。
2.  android:color：Color. 笔画线的颜色。写成16进制值或颜色资源。
3.  android:dashGap：Dimension. 笔画线破折线的间距。只在设置了android:dashWidth时有效。
4.  android:dashWidth：Dimension. 每个破折线的尺寸。只在设置了android:dashGap时有效。

#### 
	Example:

	保存在res/drawable/gradient_box.xml的XML文件：


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
&lt;/shape&gt;

	这个布局XML把定义好的形状应用到了View上：


&lt;TextView
    android:background=&quot;@drawable/gradient_box&quot;
    android:layout_height=&quot;wrap_content&quot;
    android:layout_width=&quot;wrap_content&quot; /&gt;

	用以下写法在代码内使用定义好的形状：


Resources res = getResources();
Drawable shape = res. getDrawable(R.drawable.gradient_box);

TextView tv = (TextView)findViewByID(R.id.textview);
tv.setBackground(shape);

	android:angle网上有各种说法，这里，我说说自己的实验结果，渐变的时候，最原始的，即android:angle＝&ldquo;0&rdquo;时，是从左到右，按照开始颜色到结束颜色来渲染的，android:angle＝&ldquo;90&rdquo;是从上到下来渲染的，android:angle＝&ldquo;180&rdquo;是从右到左来渲染的，android:angle＝&ldquo;360&rdquo;和android:angle＝&ldquo;0&rdquo;是一样的，所以这里应该是这样的，渲染时按照最原始的渲染色板（把控件内部看作一块可以绕中心旋转的板子）围绕控件中心来旋转相应的度数，即android:angle里面的值就是所需要旋转的角度，只是这个旋转角度必须是45的整数倍

## 
	总结

	全部内容可以总结到一张表中。


	

			XML 参数

	
	
		
			
				Attribute Name
			
			
				Description
			
		
	
	
		
			
				android:angle
			
			
				渐变的角度。0是从左到右，90是从下到上
			
		
		
			
				android:bottom
			
			
				渐变形状内的下方内边距
			
		
		
			
				android:centerColor
			
			
				中心颜色，可选
			
		
		
			
				android:centerX
			
			
				形状内渐变起点X坐标
			
		
		
			
				android:centerY
			
			
				形状内渐变起点Y坐标
			
		
		
			
				android:color
			
			
				渐变形状的实心颜色
			
		
		
			
				android:color
			
			
				渐变形状笔画的颜色
			
		
		
			
				android:dashGap
			
			
				笔画里破折号之间的间距
			
		
		
			
				android:dashWidth
			
			
				笔画里破折号的长度
			
		
		
			
				android:endColor
			
			
				渐变结束处的颜色
			
		
		
			
				android:gradientRadius
			
			
				渐变半径，仅在放射渐变中使用
			
		
		
			
				android:height
			
			
				渐变形状的高度
			
		
		
			
				android:innerRadius
			
			
				环形的内径
			
		
		
			
				android:innerRadiusRatio
			
			
				按环形宽度比例表示的环形内径
			
		
		
			
				android:left
			
			
				渐变形状的左内边距
			
		
		
			
				android:right
			
			
				渐变形状的右内边距
			
		
		
			
				android:shape
			
			
				表示用渐变填充什么形状
			
		
		
			
				android:startColor
			
			
				渐变的起始颜色
			
		
		
			
				android:thickness
			
			
				环形的厚度
			
		
		
			
				android:thicknessRatio
			
			
				按环形宽度比例表示的环形厚度
			
		
		
			
				android:top
			
			
				渐变形状的上内边距
			
		
		
			
				android:type
			
			
				渐变的类型
			
		
		
			
				android:useLevel
			
			
				（文档没写，不知道为什么）
			
		
		
			
				android:useLevel
			
			
				表示图片所在图层是否影响渐变描绘的方式
			
		
		
			
				android:visible
			
			
				表示图片资源是否初始化为可见的
			
		
		
			
				android:width
			
			
				渐变形状的宽度
			
		
		
			
				android:width
			
			
				渐变形状笔画的宽度
			
		
	


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

	真是多啊，OK啦！