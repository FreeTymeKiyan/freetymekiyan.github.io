title: 'Android Activity的加载模式和onActivityResult方法之间的冲突'
tags:
  - Debug
  - FACK-Android
  - 代码
id: 619
categories:
  - Android
  - 技术实践
date: 2013-03-27 21:14:32
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">今天在调试程序时，发现在某一Activity上点击返回键会调用该Activity的onActivityResult()方法。我一开始用log，后来用断点跟踪调试半天，还是百思不得其解。因为之前其他的Activity的LaunchMode都是Normal，没有特殊设定，这个Activity由于需求改成了singleTop。直到我确定没有一个地方是代码主动触发的，我才想到了跟Activity的LaunchMode是否有关。</span></span>

## 
	<span style="color:#000000;">探索</span>

	<span style="color:#000000;"><span style="font-size:14px;">在Google上搜索android activity onactivityresult singTop找到了一些问题。</span></span>

* * *

### 
	<span style="color: rgb(0, 0, 0);">stackoverflow</span>

	<span style="color:#000000;"><span style="font-size:14px;">stackoverflow上有些人跟我遇到的问题类似。比如说有一位开发者把Activity设置成了singleTask模式，onActivityResult就收不到任何结果了。当他把singleTask模式改回标准模式，又恢复正常。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">这个问题下面给出的答案中，有一位说startActivityForResult的文档中有这么一句话：</span></span>

> <span style="color:#000000;"><span style="font-size:14px;">For example, if the activity you are launching uses the singleTask launch mode,&nbsp;it will not run in your task and thus you will immediately receive a cancel result.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">意思是：比如说，如果你正加载的activity使用了singleTask的加载模式，它不会在你的栈中运行，而且这样你会马上收到一个取消的结果。</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">即在onActivityResult里马上得到一个RESULT_CANCEL.</span></span>

	<span style="color:#000000;"><span style="font-size:14px;">他还补充说没有很好的补救方法。可以试试用监听广播的方法。</span></span>

	<span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">另一个stackoverflow的问题中，有人直接回答了不能再singleInstance或singleTop模式下使用startActivityForResult()方法，不仅被采纳了，票数还不低。</span>

<div style="font-size: 13px;">

		<span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">剩下的一个stackoverflow问题中，有人说把singleTask改成singleTop就会正常，得到高达59票并被采纳。实际上我用的就是singTop，可是onActivityResult还是无缘无故被调用了。</span>

* * *

</div>

### 
	<span style="color: rgb(0, 0, 0); line-height: 1.2em;">startActivityForResult的文档：</span>

	<span style="color:#000000;"><span style="font-size:14px;">public void startActivityForResult (Intent intent, int requestCode, Bundle options)</span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">Added in API level 16</span></span></span>

	<span style="color:#000000;"><span style="font-size:14px;">Launch an activity for which you would like a result when it finished. When this activity exits, your onActivityResult() method will be called with the given requestCode. Using a negative requestCode is the same as calling startActivity(Intent) (the activity is not launched as a sub-activity).</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">加载一个Activity，当它结束时你会得到结果。当这个Activty退出了，你的onActivityResult()方法会根据给出的requestCode被调用。使用一个负的requestCode和调用startActivity(intent)一样（activity不被加载成子activity）</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">Note that this method should only be used with Intent protocols that are defined to return a result. In other protocols (such as ACTION_MAIN or ACTION_VIEW), you may not get the result when you expect. For example, if the activity you are launching uses the singleTask launch mode, it will not run in your task and thus you will immediately receive a cancel result.</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">注意这个方法只能用于被定义要返回结果的Intent协议。在其他协议中（譬如ACTION_MAIN或ACTION_VIEW），你可能在你想得到结果时得不到。比如，当你正载入的Activity使用的singleTask加载模式，它不会在你的栈中运行，这样你会立马得到一个取消的结果。</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">As a special case, if you call startActivityForResult() with a requestCode &gt;= 0 during the initial onCreate(Bundle savedInstanceState)/onResume() of your activity, then your window will not be displayed until a result is returned back from the started activity. This is to avoid visible flickering when redirecting to another activity.</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">有一个特例是，当你在初始的onCreate()方法或onResume()方法中用一个大于等于0的请求码调用startActivityForResult()，你的窗口在被启动的Activity返回结果前不会显示。这是为了避免跳转到另一Activity时可见的闪烁。</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">This method throws ActivityNotFoundException if there was no Activity found to run the given Intent.</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">如果运行所给Intent的Activity没被找到，该方法会抛出ActivityNotFoundException异常。</span></span>

* * *

### 
	<span style="color:#000000;">Activity的加载模式</span>

<div>
	<table style="font-size: 14px; margin: 0.5em 1em 1em 0px; border-collapse: collapse; border-spacing: 0px; border: 0px; width: 639px; background-color: rgb(247, 247, 247); color: rgb(34, 34, 34); font-family: Roboto, sans-serif; line-height: 19px;">
		<tbody>
			<tr>
				<th style="padding: 4px 12px; vertical-align: top; background-color: rgb(153, 153, 153); color: rgb(255, 255, 255); border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="font-weight: normal; line-height: normal;">Use Cases</span></font></span></span>
				</th>
				<th style="padding: 4px 12px; vertical-align: top; background-color: rgb(153, 153, 153); color: rgb(255, 255, 255); border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="font-weight: normal; line-height: normal;">Launch Mode</span></font></span></span>
				</th>
				<th style="padding: 4px 12px; vertical-align: top; background-color: rgb(153, 153, 153); color: rgb(255, 255, 255); border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="font-weight: normal; line-height: normal;">Multiple Instances?</span></font></span></span>
				</th>
				<th style="padding: 4px 12px; vertical-align: top; background-color: rgb(153, 153, 153); color: rgb(255, 255, 255); border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="font-weight: normal; line-height: normal;">Comments</span></font></span></span>
				</th>
			</tr>
			<tr>
				<td rowspan="2" style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221); width: 102px;">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Normal launches for most activities</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">&quot;`standard`&quot;</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Yes</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Default. The system always creates a new instance of the activity in the target task and routes the intent to it.</span></font></span></span>
				</td>
			</tr>
			<tr>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">&quot;`singleTop`&quot;</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Conditionally</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;"><span style="color:#000000;">If an instance of the activity already exists at the top of the target task, the system routes the intent to that instance through a call to its</span>`[<span style="color:#000000;">onNewIntent()</span>](http://developer.android.com/reference/android/app/Activity.html#onNewIntent(android.content.Intent))`<span style="color:#000000;">&nbsp;method, rather than creating a new instance of the activity.</span></span></font></span>
				</td>
			</tr>
			<tr>
				<td rowspan="2" style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Specialized launches

					<span style="margin-bottom: 0px;">(not recommended for general use)</span></span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">&quot;`singleTask`&quot;</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">No</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;"><span style="color:#000000;">The system creates the activity at the root of a new task and routes the intent to it. However, if an instance of the activity already exists, the system routes the intent to existing instance through a call to its</span>`[<span style="color:#000000;">onNewIntent()</span>](http://developer.android.com/reference/android/app/Activity.html#onNewIntent(android.content.Intent))`<span style="color:#000000;">&nbsp;method, rather than creating a new one.</span></span></font></span>
				</td>
			</tr>
			<tr>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">&quot;`singleInstance`&quot;</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">No</span></font></span></span>
				</td>
				<td style="padding: 4px 12px; vertical-align: top; background-color: inherit; border: 1px solid rgb(221, 221, 221);">
					<span style="color:#000000;"><span style="font-size:14px;"><font face="Tahoma"><span style="line-height: normal;">Same as &quot;`singleTask&quot;`, except that the system doesn&#39;t launch any other activities into the task holding the instance. The activity is always the single and only member of its task.</span></font></span></span>
				</td>
			</tr>
		</tbody>
	</table>

		<span style="color:#000000;"><span style="font-size:14px;">singleTop模式，可用来解决栈顶多个重复相同的Activity的问题。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">singleTask模式和后面的singleInstance模式都是只创建一个实例的。</span></span>

		<span style="color:#000000;"><span style="font-size:14px;">当intent到来，需要创建singleTask模式Activity的时候，系统会检查栈里面是否已经有该Activity的实例。如果有直接将intent发送给它。</span></span>

		<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">singleInstance模式解决了这个问题（绕了这么半天才说到正题）。让这个模式下的Activity单独在一个task栈中。这个栈只有一个Activity。导游应用和google地图应用发送的intent都由这个Activity接收和展示。</span>

</div>

## 
	<span style="color:#000000;"><span style="line-height: 1.2em;">总结</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">后来我改变了onActivityResult里面ResultCode为RESULT_OK时刷新界面的具体实现方法，可是onActivityResult还是会自己被调用，只是暂时没触发任何bug，可它还是个定时炸弹啊。</span></span>

	<span style="font-size:14px;"><span style="color:#000000;">以后在选择Activity的加载模式时，要考虑onActivtyResult方法与之存在冲突。</span></span>

## 
	<span style="color: rgb(0, 0, 0); line-height: 1.6em;">参考</span>

1.  <u>[<span style="color:#000000;">http://stackoverflow.com/questions/8960072/onactivityresult-with-launchmode-singletask</span>](http://stackoverflow.com/questions/8960072/onactivityresult-with-launchmode-singletask)</u>
2.  <u>[<span style="color:#000000;"><span style="font-size: 13px;">http://developer.android.com/reference/android/app/Activity.html#startActivityForResult%28android.content.Intent,%20int%29</span></span>](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult%28android.content.Intent,%20int%29)</u>
3.  <u>[<span style="color:#000000;">http://stackoverflow.com/questions/7910840/android-startactivityforresult-immediately-triggering-onactivityresult</span>](http://stackoverflow.com/questions/7910840/android-startactivityforresult-immediately-triggering-onactivityresult)</u>
4.  <u><span style="font-size: 13px;">[<span style="color:#000000;">http://stackoverflow.com/questions/3354955/onactivityresult-called-prematurely</span>](http://stackoverflow.com/questions/3354955/onactivityresult-called-prematurely)</span></u>