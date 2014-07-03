title: 'Java JFileChooser 笔记'
tags:
  - Java
id: 680
categories:
  - Java
  - 技术实践
date: 2013-04-29 09:51:48
---

## 
	背景

	<span style="color:#000000;"><span style="font-size:14px;">在处理Excel表格的项目中用到了很多Java GUI的知识，比如下面的JFileChooser。现记录如下。</span></span>

## 
	<span style="color:#000000;">常用方法</span>

	<span style="font-size: 14px; color: rgb(0, 0, 0); line-height: 1.6em;">JFileChooser 为用户选择文件提供了一种简单的机制，其方法有：</span>

	<span style="color:#000000;"><span style="font-size:14px;">boolean accept(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果应该显示该文件，则返回 true。&nbsp;

	void addActionListener(ActionListener l)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 向文件选择器添加一个 ActionListener。&nbsp;

	void addChoosableFileFilter(FileFilter filter)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 向用户可选择的文件过滤器列表添加一个过滤器。&nbsp;

	void approveSelection()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 用户单击 Approve 按钮（默认情况下标有 &quot;Open&quot; 或 &quot;Save&quot;）时由 UI 调用此方法。&nbsp;

	void cancelSelection()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 用户选择 Cancel 按钮时由 UI 调用此方法。&nbsp;

	void changeToParentDirectory()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 将要设置的目录更改为当前目录的父级。&nbsp;

	protected &nbsp;JDialog createDialog(Component parent)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 创建并返回包含 this 的新 JDialog，在 parent 窗体中的 parent 上居中。&nbsp;

	void ensureFileIsVisible(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 确保指定的文件是可见的，不是隐藏的。&nbsp;

	protected &nbsp;void fireActionPerformed(String command)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 通知对此事件类型感兴趣的所有侦听器。&nbsp;

	FileFilter getAcceptAllFileFilter()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回 AcceptAll 文件过滤器。&nbsp;

	AccessibleContext getAccessibleContext()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 获取与此 JFileChooser 关联的 AccessibleContext。&nbsp;

	JComponent getAccessory()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回 accessory 组件。&nbsp;

	ActionListener[] getActionListeners()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回在此文件选择器上注册的所有操作侦听器的数组。&nbsp;

	int getApproveButtonMnemonic()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回确认按钮的助记符。&nbsp;

	String getApproveButtonText()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回 ApproveButton 中的 FileChooserUI 内使用的文本。&nbsp;

	String getApproveButtonToolTipText()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回 ApproveButton 中使用的工具提示文本。&nbsp;

	FileFilter[] getChoosableFileFilters()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 获得用户可选择的文件过滤器列表。&nbsp;

	boolean getControlButtonsAreShown()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回 controlButtonsAreShown 属性的值。&nbsp;

	File getCurrentDirectory()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回当前目录。&nbsp;

	String getDescription(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回文件描述。&nbsp;

	String getDialogTitle()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 获得 JFileChooser 的标题栏中所显示的字符串。&nbsp;

	int getDialogType()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回此对话框的类型。&nbsp;

	boolean getDragEnabled()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 获得 dragEnabled 属性的值。&nbsp;

	FileFilter getFileFilter()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回当前选择的文件过滤器。&nbsp;

	int getFileSelectionMode()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回当前的文件选择模式。&nbsp;

	FileSystemView getFileSystemView()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回文件系统视图。&nbsp;

	FileView getFileView()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回当前的文件视图。&nbsp;

	Icon getIcon(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回此文件或文件类型的图标，这取决于系统。&nbsp;

	String getName(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回文件名。&nbsp;

	File getSelectedFile()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回选中的文件。&nbsp;

	File[] getSelectedFiles()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果将文件选择器设置为允许选择多个文件，则返回选中文件的列表。&nbsp;

	String getTypeDescription(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回文件类型。&nbsp;

	FileChooserUI getUI()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 获得实现此组件 L&amp;F 的 UI 对象。&nbsp;

	String getUIClassID()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回一个指示 L&amp;F 类名的字符串，该类负责呈现此组件。&nbsp;

	boolean isAcceptAllFileFilterUsed()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回是否使用 AcceptAll FileFilter。&nbsp;

	boolean isDirectorySelectionEnabled()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 方便的调用，可根据当前的文件选择模式确定目录是否为可选择的。&nbsp;

	boolean isFileHidingEnabled()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果在文件选择器中不显示隐藏文件，则返回 true；否则返回 false。&nbsp;

	boolean isFileSelectionEnabled()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 方便的调用，可根据当前的文件选择模式确定文件是否为可选择的。&nbsp;

	boolean isMultiSelectionEnabled()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果可以选择多个文件，则返回 true。&nbsp;

	boolean isTraversable(File f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果可以返回该文件（目录），则返回 true。&nbsp;

	protected &nbsp;String paramString()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 返回此 JFileChooser 的字符串表示形式。&nbsp;

	void removeActionListener(ActionListener l)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 从文件选择器中移除一个 ActionListener。&nbsp;

	boolean removeChoosableFileFilter(FileFilter f)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 从用户可选择的文件过滤器列表中移除一个过滤器。&nbsp;

	void rescanCurrentDirectory()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 通知 UI 重新扫描当前目录的文件列表。&nbsp;

	void resetChoosableFileFilters()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 将可选择文件过滤器列表重置为其开始状态。&nbsp;

	void setAcceptAllFileFilterUsed(boolean b)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 确定是否将 AcceptAll FileFilter 用作可选择过滤器列表中一个可用选项。&nbsp;

	void setAccessory(JComponent newAccessory)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置 accessory 组件。&nbsp;

	void setApproveButtonMnemonic(char mnemonic)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 使用字符设置确认按钮的助记符。&nbsp;

	void setApproveButtonMnemonic(int mnemonic)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 使用数值键代码设置确认按钮的助记符。&nbsp;

	void setApproveButtonText(String approveButtonText)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置 FileChooserUI 中的 ApproveButton 内使用的文本。&nbsp;

	void setApproveButtonToolTipText(String toolTipText)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置 ApproveButton 中使用的工具提示文本。&nbsp;

	void setControlButtonsAreShown(boolean b)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置属性，指示在文件选择器中是否显示 approve 和 cancel 按钮。&nbsp;

	void setCurrentDirectory(File dir)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置当前目录。&nbsp;

	void setDialogTitle(String dialogTitle)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置显示在 JFileChooser 窗口标题栏的字符串。&nbsp;

	void setDialogType(int dialogType)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置此对话框的类型。&nbsp;

	void setDragEnabled(boolean b)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置 dragEnabled 属性，要在此组件上启用自动拖动处理（drag 和 drop 的第一部分），此属性必须为 true。&nbsp;

	void setFileFilter(FileFilter filter)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置当前文件过滤器。&nbsp;

	void setFileHidingEnabled(boolean b)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置是否实现文件隐藏。&nbsp;

	void setFileSelectionMode(int mode)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置 JFileChooser，以允许用户只选择文件、只选择目录，或者可选择文件和目录。&nbsp;

	void setFileSystemView(FileSystemView fsv)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置为访问和创建文件系统资源（如查找软驱和获得根驱动器列表），JFileChooser 所使用的文件系统视图。&nbsp;

	void setFileView(FileView fileView)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置用于检索 UI 信息的文件视图，如表示文件的图标或文件的类型描述。&nbsp;

	void setMultiSelectionEnabled(boolean b)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置文件选择器，以允许选择多个文件。&nbsp;

	void setSelectedFile(File file)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 设置选中的文件。&nbsp;

	void setSelectedFiles(File[] selectedFiles)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果将文件选择器设置为允许选择多个文件，则设置选中文件的列表。&nbsp;

	protected &nbsp;void setup(FileSystemView view)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 执行公共的构造方法初始化和设置。&nbsp;

	int showDialog(Component parent, String approveButtonText)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 弹出具有自定义 approve 按钮的自定义文件选择器对话框。&nbsp;

	int showOpenDialog(Component parent)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 弹出一个 &quot;Open File&quot; 文件选择器对话框。&nbsp;

	int showSaveDialog(Component parent)&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 弹出一个 &quot;Save File&quot; 文件选择器对话框。&nbsp;

	void updateUI()&nbsp;

	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 将 UI 属性重置为当前的外观值。</span></span>

## 
	<span style="color:#000000;"><span style="line-height: 1.6em;">Demo</span></span>

<pre class="brush:java;">
JFileChooser dlg = new JFileChooser(); // 弹出文件选择对话框
    dlg.setDialogTitle(&quot;选择数据文件&quot;); // 设置标题
    dlg.setFileFilter(new FileFilter() { // 设置文件过滤器

        @Override
        public String getDescription() { // 文件描述
            return &quot;excel 07-10(.xlsx)&quot;;
        }

        @Override
        public boolean accept(File file) { // 文件类型
            if (file.getName().endsWith(&quot;xlsx&quot;)) return true; 
            return false;
        }
    });
    if (JFileChooser.APPROVE_OPTION == dlg.showOpenDialog(null)) { // 确认选择
        File file = dlg.getSelectedFile(); // 获取文件</pre>

	[![](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/04/QQ截图20130429094348.png "QQ截图20130429094348")](http://freetymekiyan.1kapp.com/wp-content/uploads/2013/04/QQ截图20130429094348.png)