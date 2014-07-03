title: 磨刀不误砍柴工——eclipse快捷键整理
tags:
  - Eclipse
  - FACK-Android
  - 效率
id: 403
categories:
  - Android
  - 技术实践
date: 2013-01-21 15:34:12
---

## 
	<span style="color:#000000;">前言</span>

### 
	<span style="color:#000000;">为什么要用快捷键？</span>

	<span style="font-size:14px;"><span style="color: rgb(0, 0, 0); line-height: 1.6em;">我不得不承认，快捷键真的是伟大的发明。用鼠标点来点去，或者自己到代码里面去一个个的找，都是效率很低下的方式。熟练的使用快捷键可以大大给开发提速并有效提高你的代码质量，何乐而不为？</span></span>

## 
	<span style="color:#000000;">我常用的快捷键</span>

1.  <span style="color:#000000;"><span style="font-size:14px;">alt+/：代码补全。如果每个方法都自己写的话干脆不用IDE就用个记事本算了&hellip;</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">alt+shift+r：重命名。好处是只要编译不出现错误，不用自己一个个去找你要重命名的变量或是方法出现的地方，一次性全部改掉。注意前提，是编译不出现什么错误的情况下。否则会出现的错误未知&hellip;</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+1：快速修改建议。很多时候是正确的建议，点击之后选择自己需要的选项按enter就自动完成了。又省了很多事儿~</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+/：单行注释。在这一行的最左边加上两个斜杠，注释掉这一行。</span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+/：区域注释。注释掉选中的区域。</span></span>
6.  <span style="color:#000000;"><span style="font-size:14px;">/**+enter：Javadoc。平时写个方法或是变量的文档用这个很好。在别的地方碰到这个方法可以直接看注释的内容。</span></span>
7.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+page down/page up：在选项卡之间切换。工程大了、选项卡开多了的时候用这个。鼠标可以扔了。</span></span>
8.  <span style="color:#000000;"><span style="font-size:14px;">alt+down/up：下移一行或上移一行当前选中的代码。ctrl+x和ctrl+v可以省掉了。</span></span>
9.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+alt+down/up：把当前选中的代码复制到下面或上面。ctrl+c和ctrl+v可以省掉了。和qq音乐的改音量键冲突&hellip;所以我现在都不用qq音乐了。</span></span>
10.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+o：显示当前文件的outline。显示之后输入方法名或field变量会自动筛选，选择想找的变量点击enter就能跳转了。把你的鼠标滚轮拆了吧。</span></span>
11.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+r：打开文件。显示后输入文件名可以筛选工作空间里面的文件，选择文件点击enter即可打开。支持&ldquo;?&rdquo;和&ldquo;*&rdquo;的模糊搜索。</span></span>
12.  <span style="color:#000000;"><span style="font-size:14px;">ctlr+F11：运行。用鼠标点Run再点Run是不是很慢很累？</span></span>
13.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+F：搜索和替换。超级省脑子，不用自己找了。</span></span>
14.  <span style="color:#000000;"><span style="font-size:14px;">shift+enter：在当前光标所在行下面添加一行空行，光标不用在这一行的末尾。很方便。</span></span>
15.  <span style="color:#000000;"><span style="font-size:14px;">F2：打开申明。选中一个方法，点击F2，显示这个方法的文档。</span></span>
16.  <span style="color:#000000;"><span style="font-size:14px;">F3：跳转到方法的内部。直接跳转，会帮你打开没有打开的文件。</span></span>
17.  <span style="color:#000000;"><span style="font-size:14px;">alt+left：后退。和F3一起用很方便，就像看网页时候的后退一样，看完了就按这个退回来。</span></span>
18.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+left/right：左移或右移光标。会跳过空格、一连串的小写或大写等等，更快的定位到要编辑的位置。</span></span>
19.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+K：快速跳转到选中内容下次出现的位置。很喜欢用这个快捷键，省事儿！</span></span>
20.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+w：关闭当前的标签。喜欢用chrome的人很熟悉了吧！不用了就关掉，不然eclipse会变卡。</span></span>
21.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+w：关闭所有的标签。需求做完的时候怒点一下，世界清静了！怎一个爽字了得？！搞定收工！</span></span>
22.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+z：撤销。一开始差点把它忘记了，已经成为本能了。做错了什么都还给你机会重来。感动ing&hellip;</span></span>
23.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+y：重做。和ctrl+z是一对。退一步回来了，还是可以再进一步。就是重来的重来&hellip;</span></span>
24.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+d：删除。选中的行全部删干净。你没看错，是一整行。</span></span>
25.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+s：保存当前文件。养成随时保存的好习惯，不然断电了怎么办？死机了怎么办？不保存你只能哭了&hellip;</span></span>
26.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+s：保存打开的所有文件。</span></span>

## 
	<span style="color:#000000;">其他推荐快捷键</span>

1.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+enter：在当前光标所在行上面添加一行空行。和shift+enter是一对。</span></span>
2.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+e：按照输入文件名切换选项卡。ctrl+page down/page up适合选项卡少的情况。这个适合选项卡多的情况。</span></span>
3.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+2，L：赋值给本地变量。先写好方法，比如说Calendar.getInstance()。然后点ctrl+2，再点L，就能把方法的结果赋给一个本地变量（L是local的开头）。节省了输入类名，变量名以及导入声明的时间。</span></span>
4.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+2，F：赋值给成员变量。用法同上，把方法的结果赋给一个成员变量。</span></span>
5.  <span style="color:#000000;"><span style="font-size:14px;">alt+shift+l和alt+shift+m：提取本地变量及方法。源码处理还包括从大块的代码中提取变量和方法的功能。比如，要从一个string创建一个常量，那么就选定文本并按下alt+shift+l即可。如果同 一个string在同一类中的别处出现，它会被自动替换。方法提取也是个非常方便的功能。将大方法分解成较小的、充分定义的方法会极大的减少复杂度，并提升代码的可测试性。</span></span>
6.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+m：编辑器窗口最大化。</span></span>
7.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+.：把光标移动至当前文件中的下一个报错处或警告处。直接跳到下一个，很方便啊！</span></span>
8.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+,：把光标移动至当前文件中的上一个报错处或警告处。和ctrl+.是一对。</span></span>
9.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+L：查看更多的快捷键。</span></span>
10.  <span style="color:#000000;"><span style="font-size:14px;">alt+right：导航历史记录中向前。</span></span>
11.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+q：回到最后一次编辑过的地方。</span></span>
12.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+down/up：直接跳转到下一个或上一个方法或成员变量。中间的行都会略过。</span></span>
13.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+X：把当前选中的文本全部变为大写</span></span>
14.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+Y：把当前选中的文本全部变为小写</span></span>
15.  <span style="color:#000000;"><span style="font-size:14px;">ctrl+shift+K：查找上一个。和ctrl+K是一对。</span></span>

## 
	<span style="color:#000000;">所有快捷键</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+1：快速修复

	Ctrl+D：删除当前行&nbsp;

	Ctrl+Alt+&darr;：复制当前行到下一行

	Ctrl+Alt+&uarr;：复制当前行到上一行

	Alt+&darr;：当前行和下面一行交换位置

	Alt+&uarr;：当前行和上面一行交换位置

	Alt+&larr;：前一个编辑的页面

	Alt+&rarr;：后一个编辑的页面

	Alt+Enter：显示当前选择资源（工程或文件）的属性

	Shift+Enter：在当前行的下一行插入空行

	Shift+Ctrl+Enter：在当前行的上一行插入空行

	Ctrl+Q：定位到最后编辑的地方

	Ctrl+L：定位到某行&nbsp;

	Ctrl+M：最大化当前标签

	Ctrl+/：注释当前行，再按则取消注释

	Ctrl+O：快速显示OutLine

	Ctrl+T：快速显示当前类的继承结构

	Ctrl+W：关闭当前Editer

	Ctrl+K：参照选中的Word快速定位到下一个

	Ctrl+E：快速显示当前Editer的下拉列表(如果当前页面没有显示的用黑体表示)

	Ctrl+/(小键盘)：折叠当前类中的所有代码

	Ctrl+&times;(小键盘)：展开当前类中的所有代码

	Ctrl+Space：代码助手完成一些代码的插入(但一般和输入法有冲突,可以修改输入法的热键,也可以暂用Alt+/来代替)

	Ctrl+Shift+E：显示管理当前打开的所有的View的管理器(可以选择关闭,激活等操作)

	Ctrl+J：正向增量查找(按下Ctrl+J后,你所输入的每个字母编辑器都提供快速匹配定位到某个单词,如果没有,则在status line中显示没有找到了,查一个单词时,特别实用,这个功能Idea两年前就有了)

	Ctrl+Shift+J：反向增量查找(和上条相同,只不过是从后往前查)

	Ctrl+Shift+F4：关闭所有打开的Editer

	Ctrl+Shift+X：把当前选中的文本全部变为大写

	Ctrl+Shift+Y：把当前选中的文本全部变为小写

	Ctrl+Shift+F：格式化当前代码

	Ctrl+Shift+P：定位到对于的匹配符(譬如{}) (从前面定位后面时,光标要在匹配符里面,后面到前面,则反之)</span></span>

### 
	<span style="color:#000000;">重构</span>

	<span style="color:#000000;"><span style="font-size:14px;">Alt+Shift+R：重命名

	Alt+Shift+M：抽取方法

	Alt+Shift+C：修改函数结构

	Alt+Shift+L：抽取本地变量

	Alt+Shift+F：把Class中的local变量变为field变量

	Alt+Shift+I：合并变量

	Alt+Shift+V：移动函数和变量

	Alt+Shift+Z：重构的后悔药</span></span>

### 
	<span style="color:#000000;">编辑</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+F：全局 查找并替换&nbsp;

	Ctrl+Shift+K：文本编辑器 查找上一个&nbsp;

	Ctrl+K：文本编辑器 查找下一个 &nbsp;

	Ctrl+Z：全局 撤销&nbsp;

	Ctrl+C：全局 复制&nbsp;

	Alt+Shift+&darr;：全局 恢复上一个选择

	Ctrl+X：全局 剪切&nbsp;

	Ctrl1+1：全局 快速修正&nbsp;

	Alt+/：全局 内容辅助&nbsp;

	Ctrl+A：全局 全部选中&nbsp;

	Delete：全局 删除

	F2：Java编辑器 显示工具提示描述 &nbsp;

	Alt+Shift+&uarr;：Java编辑器 选择封装元素 &nbsp;

	Alt+Shift+&larr;：Java编辑器 选择上一个元素&nbsp;

	Alt+Shift+&rarr;：Java编辑器 选择下一个元素&nbsp;

	Ctrl+J：文本编辑器 增量查找&nbsp;

	Ctrl+Shift+J：文本编辑器 增量逆向查找&nbsp;

	Ctrl+V：全局 粘贴&nbsp;

	Ctrl+Y：全局 重做&nbsp;</span></span>

### 
	<span style="color:#000000;">查看</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+=：全局 放大&nbsp;

	Ctrl+-：全局 缩小&nbsp;</span></span>

### 
	<span style="color:#000000;">窗口</span>

	<span style="color:#000000;"><span style="font-size:14px;">F12：全局 激活编辑器&nbsp;

	Ctrl+Shift+W：全局 切换编辑器

	Ctrl+Shift+F6：全局 上一个编辑器&nbsp;

	Ctrl+Shift+F7：全局 上一个视图&nbsp;

	Ctrl+Shift+F8：全局 上一个透视图&nbsp;

	Ctrl+F6：全局 下一个编辑器&nbsp;

	Ctrl+F7：全局 下一个视图&nbsp;

	Ctrl+F8：全局 下一个透视图&nbsp;

	Ctrl+W：文本编辑器 显示标尺上下文菜单&nbsp;

	Ctrl+F10：全局 显示视图菜单&nbsp;

	Alt+-：全局 显示系统菜单&nbsp;</span></span>

### 
	<span style="color:#000000;">导航</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+F3：Java编辑器 打开结构 &nbsp;

	Ctrl+Shift+T：全局 打开类型 &nbsp;

	F4：全局 打开类型层次结构 &nbsp;

	F2：全局 打开声明 &nbsp;

	Shift+F2：全局 打开外部javadoc &nbsp;

	Ctrl+Shift+R：全局 打开资源 &nbsp;

	Alt+&larr;：全局 后退历史记录 &nbsp;

	Alt+&rarr;：全局 前进历史记录 &nbsp;

	Ctrl+,：全局 上一个 &nbsp;

	Ctrl+.：全局 下一个 &nbsp;

	Ctrl+O：Java编辑器 显示大纲 &nbsp;

	Ctrl+Shift+H：全局 在层次结构中打开类型 &nbsp;

	Ctrl+Shift+P：全局 转至匹配的括号 &nbsp;

	Ctrl+Q：全局 转至上一个编辑位置 &nbsp;

	Ctrl+Shift+&uarr;：Java编辑器 转至上一个成员 &nbsp;

	Ctrl+Shift+&darr;：Java编辑器 转至下一个成员 &nbsp;

	Ctrl+L：文本编辑器 转至行 &nbsp;</span></span>

### 
	<span style="color:#000000;">搜索</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+Shift+U：全局 出现在文件中&nbsp;

	Ctrl+H：全局 打开搜索对话框 &nbsp;

	Ctrl+G：全局 工作区中的声明 &nbsp;

	Ctrl+Shift+G：全局 工作区中的引用 &nbsp;</span></span>

### 
	<span style="color:#000000;">文本编辑</span>

	<span style="color:#000000;"><span style="font-size:14px;">Insert：文本编辑器 改写切换 &nbsp;

	Ctrl+&uarr;：文本编辑器 上滚行 &nbsp;

	Ctrl+&darr;：文本编辑器 下滚行 &nbsp;</span></span>

### 
	<span style="color:#000000;">文件</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+S：全局 保存 &nbsp;

	Ctrl+P：全局 打印 &nbsp;

	Ctrl+F4：全局 关闭 &nbsp;

	Ctrl+Shift+S：全局 全部保存 &nbsp;

	Ctrl+Shift+F4：全局 全部关闭 &nbsp;

	Alt+Enter：全局 属性 &nbsp;

	Ctrl+N：全局 新建 &nbsp;</span></span>

### 
	<span style="color:#000000;">项目</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+B：全局 全部构建 &nbsp;</span></span>

### 
	<span style="color:#000000;">源代码</span>

	<span style="color:#000000;"><span style="font-size:14px;">Ctrl+Shift+F：Java编辑器 格式化 &nbsp;&nbsp;

	Ctrl+/：Java编辑器 注释、取消注释

	Ctrl+Shift+M：Java编辑器 添加导入 &nbsp;

	Ctrl+Shift+O：Java编辑器 组织导入 &nbsp;

	未设置：Java编辑器 使用try/catch块来包围。太常用了，所以在这里列出,建议自己设置。也可以使用Ctrl+1自动修正。&nbsp;</span></span>

### 
	<span style="color:#000000;">运行</span>

	<span style="color:#000000;"><span style="font-size:14px;">F7：全局 单步返回 &nbsp;

	F6：全局 单步跳过 &nbsp;

	F5：全局 单步跳入 &nbsp;

	Ctrl+F5：全局 单步跳入选择 &nbsp;

	F11：全局 调试上次启动 &nbsp;

	F8：全局 继续 &nbsp;

	Shift+F5：全局 使用过滤器单步执行 &nbsp;

	Ctrl+Shift+B：全局 添加/去除断点 &nbsp;

	Ctrl+D：全局 显示 &nbsp;

	Ctrl+F11：全局 运行上次启动 &nbsp;

	Ctrl+R：全局 运行至行 &nbsp;

	Ctrl+U：全局 执行 &nbsp;</span></span>

### 
	<span style="color:#000000;">重构</span>

	<span style="color:#000000;"><span style="font-size:14px;">Alt+Shift+Z：全局 撤销重构 &nbsp;

	Alt+Shift+M：全局 抽取方法 &nbsp;

	Alt+Shift+L：全局 抽取局部变量 &nbsp;

	Alt+Shift+I：全局 内联 &nbsp;

	Alt+Shift+V：全局 移动 &nbsp;

	Alt+Shift+R：全局 重命名 &nbsp;

	Alt+Shift+Y：全局 重做&nbsp;</span></span>

## 
	<span style="color:#000000;">总结</span>

	<span style="color:#000000;"><span style="font-size:14px;">一般情况下常用快捷键就够了。这些推荐的快捷键也是我自己遍历所有快捷键，慢慢养成习惯试出来的。觉得好就自己试试吧，磨刀不误砍柴工。</span></span>