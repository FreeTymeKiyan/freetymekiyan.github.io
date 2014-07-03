title: 【转载】去除wordpress主题和插件更新提示
tags:
  - WordPress
  - 技术
id: 693
categories:
  - Wordpress
  - 技术实践
date: 2013-05-04 00:38:54
---

<span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">有时候好不容易找到一个汉化的插件，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">结果新版本出来，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">一更新，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">全英文了，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">像我们这些英文不好的，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">就得烦恼了，</span><span style="line-height: 1.6em; font-size: 14px; color: rgb(0, 0, 0);">下面这段代码解决了这个问题：</span>

<pre class="brush:php;">
//去除自动更新提示
/*取消wordpress内核升级提示*/
add_filter(&#39;pre_site_transient_update_core&#39;,create_function(&#39;$a&#39;, &quot;return null;&quot;));
remove_action(&#39;admin_init&#39;, &#39;_maybe_update_core&#39;);
/*取消wordpress插件升级提示*/
add_filter(&#39;pre_site_transient_update_plugins&#39;, create_function(&#39;$a&#39;, &quot;return null;&quot;));
remove_action(&#39;admin_init&#39;, &#39;_maybe_update_plugins&#39;);
/*取消wordpress主题升级提示*/
add_filter(&#39;pre_site_transient_update_themes&#39;,  create_function(&#39;$a&#39;, &quot;return null;&quot;));
remove_action(&#39;admin_init&#39;, &#39;_maybe_update_themes&#39;);</pre>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">把上面的代码粘贴到你主题的functions.php 中即可.</span></span></span>

	<span style="color: rgb(0, 0, 0); font-size: 14px;">转载自JoyChao，原文地址：[<u>http://www.joychao.cc/401.html</u>](http://www.joychao.cc/401.html)</span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">P.S：我不是因为英文不好。我是因为有强迫症，看到提示老想升级，升级之后自定义的东西会被覆盖，又得重调。那干脆就不要显示了，反正够用了。</span>这篇文章正对我胃口。</span></span>