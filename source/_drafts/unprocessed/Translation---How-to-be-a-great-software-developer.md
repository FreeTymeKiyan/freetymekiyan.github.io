title: 'Translation - How to be a great software developer'
id: 1065
categories:
  - 未分类
tags:
---

Click here to read Original essay.&nbsp;http://peternixey.com/post/83510597580/how-to-be-a-great-software-developer

	作者简介：

	I&#39;m Peter, a Rails developer and entrepreneur. I&#39;m an ex-computer vision researcher, the former CEO of Clickpass and a YC alum. I have built and sold a lot of different software and am now the CTO of Brojure.com - which lets you quickly create stunning sales pitches

	我叫Peter，是一名Rails开发者，同时也是一位创业者。我以前研究过机器视觉，当过Clickpass的CEO，也是Y-Combinator的一员。我编了也卖了很多不同的软件，现在是Brojure.com的CTO - 它能帮你迅速创造非常好的销售语。

	Disclaimer: This is a very long piece, much longer than I would normally write on any subject. I have edited it back but on sending it to friends to read they agreed that there was no one bit that should be cut. I hope that you feel the same.

	声明：这篇文章相当长，比我平时写得任何主题都长得多。我反复修改了几次，但给朋友们读过之后，他们一致表示没什么好删的。希望你也这么觉得。

	If there&rsquo;s one thing that software developers care about, it&rsquo;s becoming even better software developers. Where do you start though? Should you accumulate the bells and whistles: deepen your knowledge of Node and no-sequel? Should you rote-learn the answers to the profession&rsquo;s gateway questions and be able to produce bubble sort or link shortener algorithms on demand? Or are there perhaps more fundamental roots that you can put down?

	如果有那么一件事儿是写软件的人所在乎的，那就变成更好的软件开发者。从哪儿做起呢？应该积累那些花里胡哨的东西吗：深入了解Node和NoSQL？应该死记硬背那些面试题的答案，按要求写写冒泡排序或网址缩写的算法吗？亦或是扎根于更基础的知识呢？

	I believe that your seniority and value as a programmer is measured not in what you know, it&rsquo;s measured in what you put out. The two are related but definitely not the same. Your value is in how you move your project forward and how you empower your team to do the same. In fifteen years of programming I&rsquo;ve never had to implement a bubble sort or a link shortener. However I have had to spend thousands and thousands of hours writing and refactoring account management tools, editing suites, caching logic, mailing interfaces, test suites, deployment scripts, javascript layers, analytics architecture and documentation. These were the things that mattered, the completion of these were what moved us forward.

	我坚信你作为一名程序员的资历和价值不是以你知道什么来衡量的，而是以你输出了什么。两者之间有些联系但不能等同。你的价值来源于你如何推进你的项目，并且让你的团队也能一起推进。十五年的编程经验里，我从来不用实现一个冒泡排序或是网址缩写。然而我得花大量的时间编写和重构账户管理工具，编辑组件，缓存逻辑，邮件接口，测试组件，发布脚本，Javascript层，架构解析和文档。这些才是重要的事儿，做这些事儿才能推动我们前行。

	Those humble components are the bricks and mortar of projects and take hundreds or thousands of hours of hard work to assemble. And even though they combine to form complex systems, they themselves should not be complicated. You should aim for simplicity and over the years I have learned that simplicity is far more easily attained by time spent working and refactoring than hours of pure thought and &ldquo;brilliance&rdquo;.

	这些低级的事情是项目的一砖一瓦，耗费大量的努力才能把它们组装起来。尽管它们合起来组成复杂的系统，它们本身并不该复杂。你应该以简洁为目标。这么多年来我发现简洁更容易从花时间工作和重构中得到，而不是凭空地想或者灵光一现。

	Simplicity and excellence are most reliably attained by starting with something, anything, that gets the job done and reworking back from that point. We know this is true of companies and the concept of the MVP is burned deep into our consciousness. So too with software. Start with something ugly but functional and then apply and reapply yourself to that ugly and misshapen solution and refactor it back into its simplest form. Simplicity comes far more reliably from work than from brilliance. It comes more predictably from code written, than from thought expended. It comes from effort.

	简洁和卓越通过开始做点什么，随便什么都行，完成以后再反复重写，这样得来最可靠。我们知道这对公司来说是正确的，最小可行产品（MVP）的概念早已深入人心。对软件来说也是一样。以一些有点丑但可以用的开头，接着反复致力于那个又丑又奇怪的解决方案，重构到它的最简洁的形式。简洁更多地来自工作而非聪明才智。通过写过的代码，而不是发散的思绪，得来的简洁更可预见。简洁源自努力。

	It is all too easy for smart lazy people to flash spikes of brilliance and wow their contemporaries but companies are not built on those people and product does not sit well on spikes. Companies are built on people and teams who day in, day out, commit good code that enables others do the same. Great product is built by work horses, not dressage horses.

	对聪明的懒人而言，闪耀下智慧的光芒再赢得同龄人的喝彩太简单了，但公司不是靠这些人建成的，产品也靠不了这些。公司靠的是那些日复一日提交良好的、让其他人也如是效仿的代码的员工和团队。伟大的产品是忠实的执行者创造的，而非那些花里胡哨的人。

	Years after Joel coined the term &ldquo;Rockstar Programmer&rdquo;, it lives on along with the misapprehension that companies need such geeky micro-celebrities in order to do anything. While those characters do exist there aren&rsquo;t many of them. When you do find them they&rsquo;re often erratically brilliant - astonishing at the things that interest them but hopeless at working consistently or smoothly with their team.

	在Joel杜撰了&ldquo;明星程序员&rdquo;这个词的多年以后，它还伴随着公司需要这样的极客小名人才能做点事的误解存在着。这样的人的确有，但不多。当你确实发现他们了，他们却聪明的很不稳定 - 惊讶于让他们感兴趣的事物，又对持续地、和谐地和团队工作不抱任何希望。

	Not only is their output erratic but their superiority is aspirational and infectious. Their arrogance bleeds toxically into the rest of the team. It signals loud and clear that if you&rsquo;re smart enough you choose when you work and what you work on. You become a &ldquo;Developer in Residence&rdquo;. And you not only soak up a salary but you distort the values of those who work around you.

	不仅仅是他们的输出不稳定，他们的优势是富有雄心壮志和能感染人。他们的傲慢致命地渗透到团队其他人当中。很明显，如果你够聪明，你可以选择什么时间工作、做些什么。你成了一名&ldquo;远程工作者&rdquo;。你不仅拿了不少薪水还扭曲了你身边工友的价值观。

	So the reality is that in all likelihood you and your team will depend, should depend not on those who think they are &ldquo;Rockstars&rdquo; or &ldquo;Ninjas&rdquo; but on reliable people who work in reliable ways.

	所以现实是你和你的团队不管靠什么都不要靠那些觉得自己是&ldquo;明星&rdquo;或&ldquo;忍者&rdquo;的人，靠就靠那些用可靠方式工作的可靠的人。

	Great developers are not people who can produce bubble sorts or link shorteners on demand. They are the people who when you harness them up to a project, never stop moving and inspire everyone around them to do the same. Fuck Rockstars. Hire workhorses. Here&rsquo;s some ways to become one:

	伟大的开发者不是那些能按需写出冒泡排序或链接缩写的人。他们是那些你用来做项目时，从不停下脚步并激励身边的每个人不断前进的人。艹所谓的&ldquo;明星程序员&rdquo;。顾那种忠实的执行者。这是一些成为这种人的方法：

	Name your functions and variables well (write Ronseal Code)

	好好地给函数和变量命名（写皇室代码）

	Such an incredibly simple place to start and yet I think it is one of THE most important skills in programming. Function naming is the manifestation of problem definition which is frankly the hardest part of programming.

	这个起点简单到难以置信，然而我认为这是编程最重要的技能之一。函数命名是问题定义的体现，老实说，这才是编程里面最难的。

	Names are the boundary conditions on your code. Names are what you should be solving for.

	在代码命名里指明边界条件。名字是你得解决的问题。

	If you name correctly and then solve for that boundary conditions that that name creates you will almost inevitably be left with highly functional code.

	如果你正确地命名并在名字所提的边界条件下解决了这个问题，基本上可以说你肯定能写下高可用性的代码。

	Consider the function:

	def process_text string

	&nbsp; &hellip;

	end

	It tells someone almost nothing abo<span style="line-height: 1.6em;">ut what it&rsquo;s going to do or how it&rsquo;s been implemented in the code. However:</span>

	def safe_convert_to_html string

	&nbsp; ...

	end

	tells someone exactly what&rsquo;s going to happen. It&rsquo;s also a good indicator as to what&rsquo;s not going to happen. It tells you both what you can expect the method to do but also how far you can overload that method.

	明确告知了会发生什么。这也很好地表明了什么不会发生。它既同时告诉你你可以期待这个方法做什么和你可以重写该方法到什么程度。

	A developer might happily refactor &ldquo;process_text&rdquo; to not only convert text to HTML but to auto-embed videos. However that may be resolutely not what was expected in some of the places that function was used. Change it and you&rsquo;ve created bugs. A good clear name is a commitment to not just what a function does but also what it won&rsquo;t do.

	开发者也许很乐意把&ldquo;process_text&rdquo;<span style="font-size: 13px;">重构成不仅仅把文本转化成HTML，还加上自动嵌入的视频。然而那可能完全不是该函数在某些情况下的用法。一改就出bug了。一个好的、清晰的名字不止是函数做什么，也是它不做什么的保证。</span>

	A good function promises what it will deliver and then delivers it. Good function and variable naming makes code more readable and tightens the thousands of contracts which criss-cross your codebase. Sloppy naming means sloppy contracts, bugs, and even sloppier contracts built on top of them.

	一个好的函数告诉你它会传递什么，接着照做。好的函数和变量命名增加代码的可读性，加强对代码库中纵横交错的约定的控制。草率的命名意味着马虎的约定，bug和建立在它们之上的更马虎的约定。

	It&rsquo;s not just functions that you can leverage to describe your code. Your variable names should also be strong. Sometimes it can even be worth creating a variable simply in order document the logic itself.

	不仅可以靠函数来描述你的代码。你的变量名称也应该很靠谱。有时候甚至值得为了记录本身的逻辑创建一个变量。

	Consider the line:

	<span style="line-height: 1.6em;">if (u2.made &lt; 10.minutes.ago)&nbsp;</span>

	&nbsp; &nbsp;&amp;&amp; !u2.tkn&nbsp;

	&nbsp; &nbsp;&amp;&amp; (u2.made == u2.l_edit)

	&nbsp; ...

	It&rsquo;s pretty hard to figure out what the hell is happening there and even once you have done so, it&rsquo;s not 100% clear what the original author was trying to achieve with it. The variable names are horrible and tell you nothing.

	你很难弄清楚这到底是什么情况，即便你弄清楚了，原作者想用这个实现什么也不完全清晰。变量名称太糟糕了，什么都告诉不了你。

	The &ldquo;and not&rdquo; statement is always confusing to read (please never write &ldquo;and not&rdquo; clauses which end with a noun) and if your job was to refactor this code you&rsquo;d have to do some acrobatics to guess exactly what the original intent was.

	那个&ldquo;&amp;&amp; !&rdquo;（且非）语句读着总是很模糊（请绝对不要写后面接着一个名字的&ldquo;&amp;&amp; !&rdquo;语句），如果你要重构这段代码，你就得玩点巧妙手法来确切地猜猜它的原意了。

	However, if we change the variables names into something more meaningful then things immediately start to become clearer:

	然而，如果我们把变量名称改得更有意义，意思马上就更清晰了：

	if (new_user.created_at &lt; 10.minutes.ago)&nbsp;

	&nbsp; &amp;&amp; !new_user.invitation_token&nbsp;

	&nbsp; &amp;&amp; (new_user.created_at == new_user.updated_at)

	We can go further still and forcibly document the intent of each part of the if statement by separating and naming the components:

	我们还可以走的更远，通过分离和命名各部分强制地记录if语句各部分的意图：

	user_is_recently_created = user.created_at &lt; 10.minutes.ago

	invitation_is_unsent = !user.invitation_token

	user_has_not_yet_edited_profile = (user.created_at == user.updated_at)

	&nbsp;

	if user_is_recently_created&nbsp;

	&nbsp; &amp;&amp; invitation_is_unsent&nbsp;

	&nbsp; &amp;&amp; user_has_not_yet_edited_profile

	&nbsp; ...

	It takes some courage to write a line like &ldquo;user_is_recently_created&rdquo; because it&rsquo;s such fuzzy logic but we all do it at times and owning up to that helps inform the reader about the assumptions you&rsquo;ve made.

	写像&ldquo;user_is_recently_created&rdquo;这样的语句需要勇气，因为这里的逻辑很模糊，但是我们有时候都这么做，也正因此告诉了读代码的人你所做的那些假设。

	Notice also how much stronger this approach is than using comments. If you change the logic there is immediate pressure on you to change the variable names. Not so with comments. I agree with DHH, comments are dangerous and tend to rot - much better to write self-documenting code.

	还请注意这种方法比用注释要强多少。如果你改了逻辑，马上你就有改变量名的压力。用注释则不然。我同意DHH，注释很危险还容易腐烂 - 写自文档化（自我的说明的）代码要好得多。

	The better code describes itself, the more likely someone will implement it the way it was intended and the better their code will be. Remember, there are only two hard problems in computer science: cache invalidation, naming, and off-by-one errors.

	代码把自己描述得越清楚，其他人就更可能用它本来的方式去实现它，他们的代码也会更好。请记住，计算机科学里就两件难事儿：让缓存失效，命名，以及差一错误。

	Go deep before you go wide - learn your chosen stack inside out

	先深后广 - 彻底地学习你选的技术

	Very few programming problems are genuinely new. Very few companies are doing technical work that hasn&rsquo;t already been done by 50 teams before them. Very few problems attract Stack Overflow eyeballs that haven&rsquo;t already seen them somewhere else before.

	很少有编程的问题是全新的。很少有公司在做之前没五十个团队已经实现的技术。很少有之前没在别处见过的的问题吸引Stack Overflow的眼球。

	For that exact reason, the majority of the things you are trying to do have already been solved by the very stack you&rsquo;re already using. I once refactored 60 lines of someone else&rsquo;s Rails code to a one line using the delightfully simple and powerful methods that Rails ships with.

	正因为这个原因，你在做的大部分事情已经被你已经在用的技术解决了。我有一次用Rails简洁强大到爽歪歪的方法把某个人60行的Rails代码<span style="font-size: 13px;">重构成一行。</span>

	Not only do they waste time but they create verbosity and errors. Their code requires new documentation to describe it, new tests to monitor it and it makes the page noisier and harder to read. Like any new code, it&rsquo;s also buggy. War-tested (and actually-tested) stack code is very seldom buggy.

	他们不仅浪费了时间，还创造了冗余和错误。他们的代码需要新的文档来描述，新的测试来监控，还使页面更嘈杂更难阅读。像其他的新代码一样，它还有很多bug。战场测试（和实际测试）过的代码很少那么多bug。

	If you are a Ruby developer take time to learn Ruby, especially the incredible range of array methods. If you are a Node developer take time to understand the architecture, the methods and the mindset of Node, if you are an Angular developer go right up to the rock-face and understand the logic behind of the incredible architecture the core team is forging there right now. Ask before you invent. You are walking in the shadows of giants. Take time to find their tracks and then marvel at how beautifully they have been built. Because if you don&rsquo;t, you simply punt the problem downstream and someone will just have to figure out why the hell you chose the sub-standard path you did.

	如果你是一个Ruby的开发者，<span style="font-size: 13px;">花时间多学学Ruby，</span>特别是那些多到难以置信的数组方法。如果你是一个Node开发者，花时间搞懂Node的架构，方法和理念。如果你是一个Angular开发者，直接去了解这个

	Learn to detect the smell of bad code

	Something I&rsquo;ve noticed in programmers who are good but who have plateaued is that they simply don&rsquo;t realise that their code could be better. That is one of the worst things that can happen to your personal development. You need to know what has to improve before you can figure out how to improve it. Learn both what good code looks like and what bad code looks like. It is said that grand chessmasters spend proportionally much more time studying previous other good chess player&rsquo;s games than the average players. I&rsquo;m quite certain that the same is true for top developers.

	An important part of your improvement arsenal is your ability to detect bad code - even when it&rsquo;s only slightly bad or perhaps &ldquo;a bit smelly&rdquo;. Smelly code is code which, while you can&rsquo;t quite articulate why, just doesn&rsquo;t feel right.

	It may be that you&rsquo;ve used 60 lines of code for something which feels fundamentally simpler, it might be something which feels like it should be handled by the language but has been manually implemented instead, it might just be code that is as complicated as hell to read. These are your code smells.

	It&rsquo;s not an easy thing to do but over the years you should learn what bad code smells like and also what beautiful code looks like. You should develop an aesthetic appreciation for code. Physicists and mathematicians understand this. They feel very uneasy about an ugly theory based on its ugliness. Simplicity is beautiful and simplicity is what we want.

	The truth is that the truth is sometimes ugly but you should always strive for beauty and when ugly is the only way, know how to present it well. If you can&rsquo;t create beautiful code, at least create Shrek code but before you do either you need to develop your sense of smell. If you don&rsquo;t know what good code looks like and know what bad code smells like then why would you ever improve it?

	Write code to be read

	I once heard Joel Spolsky say that Stack Exchange optimises not for the person asking questions but for the person reading the answers. Why? Because there are far more of them than the single person who asks the question - utility is maximised by optimising for readers, not questioners.

	I think you can view code in a similar way. It will be written just once by you and you alone. However it will be read and edited many many times, by many others. Your code has two functions: the first is its immediate job. The second is to get out of the way of everyone who comes after you and it should therefore always be optimised for readability and resilience.

	What assumptions have you made, what do your methods actually return, what on earth does that quadruple nested if / else / and not / unless, statement actually select for? Sometimes you&rsquo;ll need more than just good variable naming and you should ring fence it with tests but do what it takes (and only just what it takes) to make it durable. Great code is code that does its job and that continues to do its job even when git blame returns a who&rsquo;s who of your company payroll.

	Write every line to be read through the eyes of a disinterested and time-pressured team mate needing to extend it in a year&rsquo;s time. Remember that that disinterested and pressured team mate may be you.

	Weigh features on their lifetime cost, not their implementation cost

	New developers want to explore and to play. They love the latest shiniest things and whether they&rsquo;re no-sequel databases or high concurrency mobile servers and they want to unwrap all the toys as fast as possible, run out of the room to play with them and leave the mess for the next dev to clear up.

	Features and architecture choices have maintenance costs that affect everything you ever build on top of them. Abstractions leak and the deeper you bury badly insulated abstractions the more things will get stained or poisoned when they leak through.

	Experimental architecture and shiny features should be embarked on very carefully and only for very good reasons. Build the features you need before the features you want and be VERY careful about architecture. Save toys for side projects. Every component you invent, every piece of bleeding edge, fast changing software you incorporate will bleed and break directly into your project. If you don&rsquo;t want to spend the latter stages of the project doing nothing but mopping up blood then don&rsquo;t use it in the first place.

	Or, as a friend once tweeted

	Understand the liability AND the leverage of Technical Debt

	Technical debt is the code you write which, while sub-optimal, gets you to where you need to go. It&rsquo;s the errors which, while annoying, are still sub-critical. It&rsquo;s the complexity of a single-app architecture when you know that the future lies in service-orientation, it&rsquo;s the twenty-minute cron job which could be refactored to twenty seconds.

	The cost of these these not only adds up but it compounds. Einstein once said that &ldquo;there is no force so powerful in the universe as compound interest&rdquo;. Equally there is no force more destructive in a large software project as compounding technical debt. Most of us have seen (or built) these projects. Codebases where even the smallest change takes months of time. Codebases where people have lost the will to write good code and hope only to get in and get back out without bringing the site down.

	Technical debt is an awful burden on a project.

	Except when it&rsquo;s not.

	Not only that but technical debt is the best type of debt in the world because you don&rsquo;t always have to pay it back. When you build out a feature that turns out to be wrong, when you build out a product which turns out not to work, you will drop it and move on. You will also drop every optimisation, every test and every refactoring you ever you wrote for that feature. So if you don&rsquo;t absolutely need them; don&rsquo;t write them. This is the time to maximise your leverage, leave gaps, ignore errors, test only what you need to test.

	In the early stages of a product or a feature, the likelihood is that what you are building is wrong. You are in an exploratory phase. You will pivot both on product and on technical implementation. This is the time to borrow heavily on technical debt. This is not the time to fix those sporadic errors or to do massive refactorings. This is the time to run through with guns blazing and keep firing till you burst out the other side.

	When that happens though; when you&rsquo;re sure that you&rsquo;re in the right place and out the other side then it&rsquo;s time to tidy up and to strengthen your position. Get things in good enough shape to keep on rolling, repay enough of the debt to get you on to the next stage.

	Technical debt is like so many other things in a startup, a game of leapfrog. Your initial code is scouting code. It should move you forward fast, illuminate the problem and the solution and give you just enough space to build camp. The longer you stay, the more of the system that camp has to support the bigger and stronger you build it. If you&rsquo;re only ever staying for a week though, don&rsquo;t burn time laying down infrastructure to support a decade.

	Check and re-check your code. Your problems are yours to fix

	Engineers who &ldquo;throw code over the fence&rdquo; are awful engineers. You should make sure your code works. It&rsquo;s not the testers&rsquo; job and it&rsquo;s not your team-mates&rsquo; job. It&rsquo;s your job. Lazily written code slows you down, increases cycle times, releases bugs and pisses everyone off.

	Don&rsquo;t kid yourself that you&rsquo;re anything less than a burden and get it fixed.

	Do actual work for at least (only) four hours every day For all the talk about self optimisation, focus and life hacking that goes on amongst developers, the simple truth is that you don&rsquo;t need to do that much work to be effective. What really matters is that you do it consistently. Do proper work for at least four solid hours each day, every day and you will be one the best contributing members of your team.

	However, doing four hours of work every day is harder than it seems.

	Proper work is work that includes no email, no hacker news, no meetings, no dicking around. It means staying focussed for at least 45 minutes at a time. Four hours of work a day means that one day lost in meetings or on long lunches and foosball breaks means you have do eight hours the next one. Believe you me, eight hours of solid work is almost impossible. Four hours a day on average also means you should be aiming for five or six in order to prep for the day when you only get four.

	However it also means you can be a huge contributor to your team while having a fully rounded life. It means that you don&rsquo;t need to post that self-indulgent &ldquo;I&rsquo;m burning out, please help me&rdquo; post on HN. It means that simply by being consistent you can be valued and respected.

	Software teams don&rsquo;t slow down because people work four pure hours a day rather than seven (which is insanely hard to do consistently by the way). They slow down because people spend weeks with no direction, or because the louder and emptier vessels dedicate their paid time to discussing Google v. Facebook&rsquo;s acquisition strategies over endless extended coffee breaks.

	Just work. Doesn&rsquo;t matter how incremental or banal your progress seems&hellip;

	Write up the things you&rsquo;ve done and share them with the team

	However you document things, whether it&rsquo;s through a mailing list like Copyin, a wiki or even just inline documentation in the code, you should take the time to explain your architectural approach and learnings to the rest of the team.

	Have a tough time getting a fresh install of Postgres or ImageMagik to work? If you found it hard, the rest of your team will probably also find it hard so take a moment to throw down a few paragraphs telling them what you did and saving them the time next time.

	One of the worst parts of programming is losing whole days to battling bugs or installation issues. If you take the time to document and distribute the way you found through that you could buy five times your wasted time back by forearming your colleagues.

	Understand and appreciate the exquisite balance between too much testing and too little

	Testing is a powerful tool. It allows you to set a baseline for the reliability of your releases and makes you less fearful to make them. The less fearful you are to release, the more you do so and the faster you improve.

	However it&rsquo;s also an overhead. Tests take time to write, time to run and even more time to maintain.

	You become too heavy to move, too encumbered to flex your limbs, immobile. Too little of it and the first skid across a concrete floor is going to cut you open and leave you bleeding.

	There is no intuitive answer to what the right amount of testing is. Some projects require more testing than others and testing is a whole new piece of expertise you need to learn in itself.

	Take the time to understand what really needs tests and how to write good tests. Take the time to see when tests add value and what the least you need from them really is. Don&rsquo;t be afraid to test but don&rsquo;t be afraid not to test either. The right point is a balance; take time to explore where the equilibrium lies.

	Make your team better

	让你的团队更好

	This is different to the other points in that it&rsquo;s not something you can action so much as an indicator of whether your other actions are working.

	这一点和其他的不同，它不&hellip;&hellip;

	Does your presence make your team better or worse? Does the quality of your code, your documentation and your technical skills help and improve those around you? Do you inspire and encourage your team-mates to become better developers? Or are you the one that causes the bugs, argues during stand-ups and who wastes hours of time discussing irrelevant architectural nuances because it helps cover the fact that you&rsquo;ve done no actual work?

	你的存在让你的团队更好还是更糟？你的代码质量，文档和技术能力帮助或改善了周围的人吗？你启发、鼓励你的队友成为更好的开发者吗？或者，你是那个在立会期间搞出bug、争论不休，浪费很多时间讨论不相关的细微架构差别，因为这能掩盖你其实没做什么实事的人？

	You should make your team better. There should always be at least one or two ways in which you make those people around you better and through which the osmosis of your quality helps strengthen them. However, be aware that being &ldquo;smart&rdquo; alone is probably the least valuable and arguably most destructive dimension you can choose. In fact, if your chosen dimension doesn&rsquo;t actually make you tired it&rsquo;s probably not a valid one.

	你应该让你的团队更好。至少得有你做的一两件事让周围的人更好，通过这些事你品质的渗透作用帮助让他们变强。然而，注意独自&ldquo;聪明&rdquo;可能是你选择的最没价值也最破坏性的情况了。事实上，你选的情况如果并没让你觉得累，那这种也许就不有效。

	It&rsquo;s not who you are on the inside that defines you

	不是内心的自己定义你是谁

	There is one humbly brilliant, line in Batman Begins which has always stayed with me. At some point in the film where he&rsquo;s fooling around and acting up as a billionaire playboy, Christian Bale implores Katie Holmes to believe that he&rsquo;s still a great guy on the inside. She answers simply: &ldquo;it&rsquo;s not who you are underneath, it&rsquo;s what you do that defines you&rdquo;.

	蝙蝠侠 - 开战时刻里有句谦卑的经典台词一直伴随着我。剧里当他游手好闲，装成亿万富翁花花公子的某个时候，Christian Bale恳求Katie Holmes相信他内心还是一个伟大的人。她简单地回答到：&ldquo;不是你内在是谁，而是你的所作所为定义你是谁&rdquo;。

	Your contribution as a developer is defined not by the abstraction of how smart you are or how much you know. It&rsquo;s not defined by the technology acronyms on your resume, the companies you&rsquo;ve worked at or which college you went to. They hint at what you&rsquo;re capable of but who you are is defined by what you do and how that changes the project and the people around you.

	你作为开发者的贡献不受你多聪明或者知道多少的空想定义，也不受你简历上的技术缩略词、你工作过的公司或上的哪所学校定义。他们明白你能做的事，而不是你是谁，通过你做的事和这件事如何改变了这个项目和周围的人。

	If you want to be good, apply yourself.

	假如你想变好，做这些吧。