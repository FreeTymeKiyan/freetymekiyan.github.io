title: 怎么修改WordPress博客文章的行间距？
tags:
  - CSS
  - WordPress
id: 283
categories:
  - Wordpress
  - 技术实践
date: 2013-01-02 23:33:39
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">花了点时间花了点钱，终于把我的博客搬到了SAE上面。可惜啊，错过了SAE不要钱的时候&hellip;怪就怪自己动作太慢了╮(╯▽╰)╭&hellip;不过不要紧，SAE的价格确实算便宜的。自己花了点真金白银也更有利于我重视这种知识积累的方式。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">搬家之后好好&ldquo;端详&rdquo;了一下我的博客，发现文章里面的字间距好像比较小，看起来很不舒服。我的完美主义又作祟了！果断想办法改啊！</span></span>

## 
	<span style="color:#000000;">探索</span>

	<span style="color:#000000;"><span style="font-size:14px;">当然还是要借鉴网络上的Wordpress达人的力量的！Google走起，搜索了标题问题的关键字。出来不少相关的文章，看来有完美主义的不止我一个啊，哈哈&hellip;看了不少文章都讲的修改Style.css里面的某个数值，应该是这个没错了。之前选主题的时候就发现可以自定义，但是我没搞过PHP，只能看个大概懂。看来这次要好好找找了。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">点开后台，进到外观里面的编辑选项， 选择Style.css，结果发现全是空的，里面只有注释。</span></span>

<pre class="brush:php;">
/*   
Theme Name: Neuro
Theme URI: http://cyberchimps.com/neuro/
Description: A professional responsive HTML5 blog WordPress theme with drag and drop theme options by CyberChimps WordPress Themes.
Author: CyberChimps WordPress Themes
Author URI: http://cyberchimps.com
Version: 2.0.35
Tags: white, light, silver, gray, pink, two-columns, flexible-width, fixed-width, custom-header, custom-menu, threaded-comments, sticky-post, custom-background, custom-colors, featured-images, full-width-template, post-formats, right-sidebar, translation-ready, theme-options
License: GNU General Public License v2.0
License URI: http://www.gnu.org/licenses/gpl-2.0.html
*/</pre>

	<span style="color:#000000;"><span style="font-size:14px;">这叫我情何以堪啊&hellip;继续搜，还是发现大家都这么说。看来是这个主题特有的情况了&hellip;既然这样只能找主题相关的文档看说明了。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Neuro这个主题做得还是挺专业的，有自己的官网和论坛。直接复制粘贴上面注释里面的http://cyberchimps.com/neuro/。我在官网上找了半天，在support的documentation里面找到了我的主题的文档。文档里面讲了这么些问题：</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">Installing Neuro Pro：安装</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Updating Neuro Pro：升级</span></span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Using Custom Page Layout Templates：使用自定义页面布局模板</span></span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Setting the Neuro Pro Homepage：设置首页</span></span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Using the Menu：使用菜单</span></span></span>
6.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">How to use Widgets：怎么用插件</span></span></span>
7.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Using the Neuro Pro Settings Page in WP-Admin：在WP管理台使用主题设置页面</span></span></span>
8.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">General Options：大概设置</span></span></span>
9.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Design Options：设计设置</span></span></span>
10.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Custom CSS：自定义CSS</span></span></span>
11.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Header Options：Header设置</span></span></span>
12.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">SEO Options：SEO设置</span></span></span>
13.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Callout Options：Callout设置</span></span></span>
14.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Neuro Slider Options：滑动栏设置</span></span></span>
15.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Using the Neuro Pro Slider：使用滑动栏</span></span></span>
16.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Footer Options：Footer设置</span></span></span>
17.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Import / Export Theme Settings：导入/导出主题设置</span></span></span>
18.  <span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Shortcode Library：短代码库</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">看到Custom CSS这一项我眼前一亮！这应该就是我要找的了吧！点开Custom CSS，主要是告诉我要用Firebug或者Inspect Element来看你想改的CSS内容在什么地方，然后改对应的地方就可以了。这感情好，我记起来chrome是有这个功能的，可以直接看网页的后台代码！点开chrome的设置，进入工具，选择开发者工具（快捷键ctrl+shift+i）。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">有了这个就好了。我跳转到我的博客文章页面，一个个控件对着看，发现跟Android里面的xml有点相似的感觉。每选择一个地方，页面上会显示它对应的位置和大小，整块区域都会标蓝。我往下翻了不少，终于找到了正文的地方，发现它的样式是foundation.css文件定义的，就在38行：</span></span>

<pre class="brush:css;">
media=&quot;all&quot;
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed, figure, figcaption, footer, header, hgroup, menu, nav, output, ruby, section, summary, time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font: inherit;
    vertical-align: baseline;
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">我仿照margin、padding等参数，在下面加了一行&rdquo;line-height: 150%;&ldquo;，保存提交上传到服务器。刷新页面之后果然成功了！</span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">修改Wordpress正文字体的间距，一般情况下是修改style.css文件，把对应的line-height:100%改成150%即可。究其根本还是要找对地方，即用查看网页源码的工具看你的正文调用的什么CSS文件，到对应的CSS文件当中去改或者增加line-height参数。具体到chrome里面就是ctrl+shift+i打开开发者工具，找到正文所对应的控件，在开发者工具右侧的Styles里面看是哪一个文件的第几行定义了正文的css样式，再找到对应的文件修改即可。</span></span>

* * *

## 
	<span style="color:#000000;">修正</span>

	<span style="color:#000000;"><span style="font-size:14px;">2013-01-08：更新了一次neuro的主题之后，原来的foundation.css文件会被新文件覆盖，之前做的自定义修改就会失效。按照上面方法重新改的时候发现两点需要修正的地方：</span></span>

1.  <span style="color:#000000;"><span style="font-size:14px;">line-height:150%，冒号之后没有空格</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">这个属性要放在font:inherit的下面，等字体确定之后再设置，否则无法生效</span></span>