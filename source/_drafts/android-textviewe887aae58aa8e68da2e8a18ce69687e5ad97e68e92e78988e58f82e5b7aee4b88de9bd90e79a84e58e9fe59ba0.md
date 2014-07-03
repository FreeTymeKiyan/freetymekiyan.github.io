title: 'Android TextView自动换行文字排版参差不齐的原因'
tags:
  - FACK-Android
  - UI
id: 362
categories:
  - Android
  - 技术实践
date: 2013-01-10 15:43:06
---

## 
	<span style="color:#000000;">前言</span>

	<span style="color:#000000;"><span style="font-size:14px;">被TextView自动换行的问题困扰已久，以前曾经解决过，但迟迟没有怎么总结。今天找到一篇文章专门讲这个点，总结的特别好，特此分享如下。</span></span>

## 
	<span style="color:#000000;">原因及解决方法</span>

	<span style="font-size:14px;"><span style="color:#000000;"><span style="line-height: 1.6em;">今天项目没什么进展，公司后台出问题了。看了下刚刚学习Android时的笔记，发现TextView会自动换行，而且排版文字参差不齐。查了下资料，总结原因如下：</span></span></span>

### 
	<font color="#000000">原因一：</font>

	<span style="color:#000000;"><span style="font-size:14px;">半角字符与全角字符混乱。这种情况一般就是汉字与数字、英文字母混用</span></span>

#### 
	<span style="color:#000000;">解决方法一：</span>

	<span style="font-size:14px;"><span style="color:#000000;"><span style="line-height: 1.2em;">将textview中的字符全角化。即将所有的数字、字母及标点全部转为全角字符，使它们与汉字同占两个字节，这样就可以避免由于占位导致的排版混乱问题了。 半角转为全角的代码如下，只需调用即可。</span></span></span>

<pre class="brush:java;">
public static String ToDBC(String input) {
   char[] c = input.toCharArray();
   for (int i = 0; i&lt; c.length; i++) {
       if (c[i] == 12288) {
         c[i] = (char) 32;
         continue;
       }if (c[i]> 65280&amp;&amp; c[i]&lt; 65375)
          c[i] = (char) (c[i] - 65248);
       }
   return new String(c);
}</pre>

#### 
	解决方法二：

	<span style="color:#000000;"><span style="font-size:14px;">去除特殊字符或将所有中文标号替换为英文标号。利用正则表达式将所有特殊字符过滤，或利用replaceAll（）将中文标号替换为英文标号。则转化之后，则可解决排版混乱问题。</span></span>

<pre class="brush:java;">
 // 替换、过滤特殊字符
public static String StringFilter(String str) throws PatternSyntaxException{
    str=str.replaceAll(&quot;【&quot;,&quot;[").replaceAll("】","]&quot;).replaceAll(&quot;！&quot;,&quot;!&quot;);//替换中文标号
    String regEx=&quot;[『』]&quot;; // 清除掉特殊字符
    Pattern p = Pattern.compile(regEx);
    Matcher m = p.matcher(str);
 return m.replaceAll(&quot;&quot;).trim();
}</pre>

### 
	<span style="color:#000000;">原因二：</span>

	<span style="color:#000000;"><span style="font-size:14px;">TextView在显示中文的时候 标点符号不能显示在一行的行首和行尾，如果一个标点符号刚好在一行的行尾，该标点符号就会连同前一个字符跳到下一行显示。</span></span>

#### 
	<span style="color:#000000;">解决方法：</span>

	<span style="color:#000000;"><span style="line-height: 1.6em; font-size: 13px;">在标点符号后加一个空格。</span></span>

### 
	<span style="color:#000000;">原因三：</span>

	<span style="color:#000000;"><span style="font-size:14px;">一个英文单词不能被显示在两行中（ TextView在显示英文时，标点符号是可以放在行尾的，但英文单词也不能分开 ）。</span></span>

### 
	<span style="color:#000000;">原因四：</span>

	<span style="color:#000000;"><span style="font-size:14px;">如果要两行对其的显示效果，有两种方法</span></span>

#### 
	<span style="color:#000000;">解决方法一：</span>

	<span style="font-size:14px;"><span style="color:#000000;">修改Android源代码；将frameworks/base/core/java/android/text下的StaticLayout.java文件中的如下代码：</span></span>

<pre class="brush:java;">
if (c == &#39; &#39; || c == &#39;/t&#39; ||
        ((c == &#39;.&#39;  || c == &#39;,&#39; || c == &#39;:&#39; || c == &#39;;&#39;) &amp;&amp;
        (j - 1 &lt; here || !Character.isDigit(chs[j - 1 - start])) &amp;&amp;
         (j + 1 &gt;= next || !Character.isDigit(chs[j + 1 - start]))) ||
        ((c == &#39;/&#39; || c == &#39;-&#39;) &amp;&amp;
        (j + 1 &gt;= next || !Character.isDigit(chs[j + 1 - start]))) ||
        (c &gt;= FIRST_CJK &amp;&amp; isIdeographic(c, true) &amp;&amp;
        j + 1 &lt; next &amp;&amp; isIdeographic(chs[j + 1 - start], false))) {
    okwidth = w;
    ok = j + 1;
    if (fittop &lt; oktop)
        oktop = fittop;
    if (fitascent &lt; okascent)
        okascent = fitascent;
    if (fitdescent &gt; okdescent)
        okdescent = fitdescent;
    if (fitbottom &gt; okbottom)
        okbottom = fitbottom;
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">去掉就可以了。去掉后标点符号可以显示在行首和行尾，英文单词也可以被分开在两行中显示。</span></span>

#### 
	<span style="color:#000000;">解决方法二：</span>

	<span style="font-size:14px;"><span style="color:#000000;">自定义View显示文本</span></span>

	<span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">网上就有达人采用自定义View来解决这个问题，我做了实验并总结了一下</span><span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">自定义View的步骤：&nbsp;</span>

1.  <span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">继承View类或其子类，例子继承了TextView类；</span>
2.  <span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">写构造函数，通过XML获取属性（这一步中可以自定义属性，见例程）；</span>
3.  <span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">重写父类的某些函数，一般都是以on开头的函数，例子中重写了onDraw()和onMeasure()函数；</span>

	<span style="color: rgb(0, 0, 0); font-size: 14px; line-height: 1.6em;">==========CYTextView.java==========</span>

<pre class="brush:java;">
public class CYTextView extends TextView {
    public  static  int m_iTextHeight; //文本的高度
    public  static  int m_iTextWidth;//文本的宽度

    private Paint mPaint = null;
    private String string=&quot;&quot;;
    private float LineSpace = 0;//行间距

    public CYTextView(Context context, AttributeSet set)
    {      
        super(context,set); 
        TypedArray typedArray = context.obtainStyledAttributes(set, R.styleable.CYTextView);
        int width = typedArray.getInt(R.styleable. CY TextView_textwidth, 320);
        float textsize = typedArray.getDimension(R.styleable. CY TextView_textSize, 24);
        int textcolor = typedArray.getColor(R.styleable. CY TextView_textColor, -1442840576);
        float linespace = typedArray.getDimension(R.styleable. CY TextView_lineSpacingExtra, 15);
        int typeface = typedArray.getColor(R.styleable. CY TextView_typeface, 0);

        typedArray.recycle();

        //设置 CY TextView的宽度和行间距www.linuxidc.com
        m_iTextWidth=width;
        LineSpace=linespace;

        // 构建paint对象     
        mPaint = new Paint();
        mPaint.setAntiAlias(true);
        mPaint.setColor(textcolor);
        mPaint.setTextSize(textsize);
        switch(typeface){
        case 0:
            mPaint.setTypeface(Typeface.DEFAULT);
            break;
        case 1:
            mPaint.setTypeface(Typeface.SANS_SERIF);
            break;
        case 2:
            mPaint.setTypeface(Typeface.SERIF);
            break;
        case 3:
            mPaint.setTypeface(Typeface.MONOSPACE);
            break;
        default:
            mPaint.setTypeface(Typeface.DEFAULT);   
            break;
        }

    }

    @Override
    protected void onDraw(Canvas canvas)
    { 
       super.onDraw(canvas);      

        char ch;
        int w = 0;
        int istart = 0;
        int m_iFontHeight;
        int m_iRealLine=0;
        int x=2;
        int y=30;

        Vector    m_String=new Vector();

        FontMetrics fm = mPaint.getFontMetrics();       
        m_iFontHeight = (int) Math.ceil(fm.descent - fm.top) + (int)LineSpace;//计算字体高度（字体高度＋行间距）
        for (int i = 0; i &lt; string.length(); i++)
        {
            ch = string.charAt(i);
            float[] widths = new float[1];
            String srt = String.valueOf(ch);
            mPaint.getTextWidths(srt, widths);
            if (ch == &#39;/n&#39;){
                m_iRealLine++;
                m_String.addElement(string.substring(istart, i));
                istart = i + 1;
                w = 0;
            }else{
                w += (int) (Math.ceil(widths[0]));
                if (w &gt; m_iTextWidth){
                    m_iRealLine++;
                    m_String.addElement(string.substring(istart, i));
                    istart = i;
                    i--;
                    w = 0;
                }else{
                    if (i == (string.length() - 1)){
                        m_iRealLine++;
                        m_String.addElement(string.substring(istart, string.length()));
                    }
                }
            }
        }
        m_iTextHeight=m_iRealLine*m_iFontHeight+2;
        canvas.setViewport(m_iTextWidth, m_iTextWidth);
        for (int i = 0, j = 0; i &lt; m_iRealLine; i++, j++)
        {
            canvas.drawText((String)(m_String.elementAt(i)), x,  y+m_iFontHeight * j, mPaint);
        }
    } 

    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
    {         
        int measuredHeight = measureHeight(heightMeasureSpec);         
        int measuredWidth = measureWidth(widthMeasureSpec);          
        this.setMeasuredDimension(measuredWidth, measuredHeight);
        this.setLayoutParams(new LinearLayout.LayoutParams(measuredWidth,measuredHeight));
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    } 

    private int measureHeight(int measureSpec)
    { 
        int specMode = MeasureSpec.getMode(measureSpec);         
        int specSize = MeasureSpec.getSize(measureSpec);                  
        // Default size if no limits are specified. 
        initHeight();
        int result = m_iTextHeight;         
        if (specMode == MeasureSpec.AT_MOST){        
            // Calculate the ideal size of your         
            // control within this maximum size.         
            // If your control fills the available          
            // space return the outer bound.         
            result = specSize;          
        }else if (specMode == MeasureSpec.EXACTLY){          
            // If your control can fit within these bounds return that value.           
            result = specSize;          
        }          
        return result;           
    } 

    private void initHeight()
    {
        //设置 CY TextView的初始高度为0
        m_iTextHeight=0;

        //大概计算 CY TextView所需高度
        FontMetrics fm = mPaint.getFontMetrics();       
        int m_iFontHeight = (int) Math.ceil(fm.descent - fm.top) + (int)LineSpace;
        int line=0;
        int istart=0;

        int w=0;
        for (int i = 0; i &lt; string.length(); i++)
        {
            char ch = string.charAt(i);
            float[] widths = new float[1];
            String srt = String.valueOf(ch);
            mPaint.getTextWidths(srt, widths);
            if (ch == &#39;/n&#39;){
                line++;
                istart = i + 1;
                w = 0;
            }else{
                w += (int) (Math.ceil(widths[0]));
                if (w &gt; m_iTextWidth){
                    line++;
                    istart = i;
                    i--;
                    w = 0;
                }else{
                    if (i == (string.length() - 1)){
                        line++;
                    }
                }
            }
        }
        m_iTextHeight=(line)*m_iFontHeight+2;
    }

    private int measureWidth(int measureSpec)
    { 
        int specMode = MeasureSpec.getMode(measureSpec);          
        int specSize = MeasureSpec.getSize(measureSpec);            

        // Default size if no limits are specified.         
        int result = 500;         
        if (specMode == MeasureSpec.AT_MOST){         
            // Calculate the ideal size of your control          
            // within this maximum size.        
            // If your control fills the available space        
            // return the outer bound.        
            result = specSize;         
        }else if (specMode == MeasureSpec.EXACTLY){          
            // If your control can fit within these bounds return that value.          
            result = specSize;           
        }          
        return result;         
    }

    public void SetText(String text)(注：此函数目前只有在UI线程中调用才可以把文本画出来，在其它线程中
                                                        无法画文本，找了好久找不到原因，求高手解答)
    {
        string = text;
       // requestLayout();
       // invalidate();
    }  
}</pre>

	<span style="color:#000000;"><span style="font-size:14px;">==========attrs.xml==========</span></span>

	<span style="color:#000000;"><span style="font-size:14px;"><span style="line-height: 1.6em;">该文件是自定义的属性，放在工程的res/values下</span></span></span>

<pre class="brush:xml;">
&lt;resources&gt;
    &lt;attr name=&quot;textwidth&quot; format=&quot;integer&quot;/&gt;
    &lt;attr name=&quot;typeface&quot;&gt;
        &lt;enum name=&quot;normal&quot; value=&quot;0&quot;/&gt;
        &lt;enum name=&quot;sans&quot; value=&quot;1&quot;/&gt;
        &lt;enum name=&quot;serif&quot; value=&quot;2&quot;/&gt;
        &lt;enum name=&quot;monospace&quot; value=&quot;3&quot;/&gt;
    &lt;/attr&gt;
    &lt;declare-styleable name=&quot;CYTextView&quot;&gt;   
        &lt;attr name=&quot;textwidth&quot; /&gt;       
        &lt;attr name=&quot;textSize&quot; format=&quot;dimension&quot;/&gt;
        &lt;attr name=&quot;textColor&quot; format=&quot;reference|color&quot;/&gt;
        &lt;attr name=&quot;lineSpacingExtra&quot; format=&quot;dimension&quot;/&gt;
        &lt;attr name=&quot;typeface&quot; /&gt;
        &lt;/declare-styleable&gt;
&lt;/resources&gt;</pre>

	<span style="color:#000000;"><span style="font-size:14px;">==========main.xml==========</span></span>

<pre class="brush:xml;">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?&gt;
&lt;ScrollView
        xmlns:Android=&quot;http://schemas.android.com/apk/res/android&quot;
        Android:layout_width=&quot;320px&quot;
        Android:layout_height=&quot;320px&quot;
        Android:background=&quot;#ffffffff&quot;
        &gt;
  &lt;LinearLayout
        xmlns:Android=&quot;http://schemas.android.com/apk/res/android&quot;
        Android:orientation=&quot;vertical&quot;
        Android:layout_width=&quot;fill_parent&quot;
        Android:layout_height=&quot;fill_parent&quot;&gt;
    &lt;com.cy.CYTextView.CYTextView
        xmlns:cy=&quot;http://schemas.Android.com/apk/res/ com.cy.CYTextView &quot;
        Android:id=&quot;@+id/mv&quot;
        Android:layout_height=&quot;wrap_content&quot;
        Android:layout_width=&quot;wrap_content&quot;
        cy :textwidth=&quot;320&quot;       
        cy :textSize=&quot;24sp&quot;
        cy :textColor=&quot;#aa000000&quot;
        cy :lineSpacingExtra=&quot;15sp&quot;
        cy :typeface=&quot;serif&quot;&gt;
    &lt;/com.cy.CYTextView.CYTextView&gt;   
  &lt;/LinearLayout&gt;
&lt;/ScrollView&gt;</pre>

	<span style="color:#000000;"><span style="font-size:14px;">==========Main.java==========</span></span>

<pre class="brush:java;">
public class Main extends Activity {
    CYTextView mCYTextView;
    String text = &quot;Android提供了精巧和有力的组件化模型构建用户的UI部分。主要是基于布局类：View和        ViewGroup。在此基础上，android平台提供了大量的预制的View和xxxViewGroup子 类，即布局（layout）和窗口小部件（widget）。可以用它们构建自己的UI。&quot;;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        this.setContentView(R.layout.main);

        mCYTextView = (CYTextView)findViewById(R.id.mv);
        mCYTextView.SetText(text);
    }
}</pre>

	<span style="font-size:14px;">[<span style="color:#0099ff;">原文</span>](http://blog.sina.com.cn/s/blog_821e2bb101011803.html)[<span style="color:#0099ff;">在此</span>](http://blog.sina.com.cn/s/blog_821e2bb101011803.html)</span>