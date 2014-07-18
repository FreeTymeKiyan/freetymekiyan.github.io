title: 'Textmate-Strip Trailing Whitespace'
date: 2014-07-15 11:21:38
tags:
  - TextMate 2
categories:
  - Coding
---
During internship, I learned a lot about the differences between pro-coder and casual coding. One thing I noticed is that my supervisor will remove all the unnecessary spaces when doing code review. It's a bit tedious and time-consuming if we do it by clicking around in TextMate bundles. There are two ways that can accelerate this.

##Method One
We usaully come up with handy keyboard shortcuts instead of using mouse. The first method is doing it manually using "control+command+T". Then type "rem" in the search field, the very first one should be what we are looking for. Lastly, hit enter and we are all set.

Make sure you **Search by Title** and **Search all Scope** in the **Select Bundle Item** window.
<center>![Screen Shot of Select Bundle Item](../../../../../images/select-bundle-item-window.png)</center>

###Key Steps:

1. control+command+T
2. rem
3. enter

##Method Two
Is there an automatic way to do this? Yep! I found this on [TextMate 2 Tips](http://tm2tips.tumblr.com/post/42657705618/strip-trailing-whitespace-on-save-with-callbacks) and immediately decided to make use of it. It's fairly simple. Just open the bundle editor with "⌃⌥⌘B", and open "Text" -> "Menu Actions" -> "Converting / Stripping" -> "Remove Trailing Spaces in Document / Selection". Then in the drawer put "callback.document.export" in the field next to "Semantic Class". Change Input as Document. Change Output as Replace Document.

###Key Steps:

1. ⌃⌥⌘B
2. "Text" -> "Menu Actions" -> "Converting / Stripping" -> "Remove Trailing Spaces in Document / Selection"
3. put "callback.document.export" after "Semantic Class"
4. Change Input as Document
5. Change Output as Replace Document.

###Test
We may check it with a text in a random file. Try it with some old code file. Or go ahead and open up a file and enter some code. For example, create a new file named index.js, put ``console.log();``  and a blank line with several spaces. Save and close it. Then open it again. You'll find that the annoying spaces are gone.

Apparently, we don't need to save, close and reopen every time. It will work as well once we quit TextMate and rerun it next time.

##Reference
1. [TextMate 2 Tips, *Strip trailing whitespace on Save with …callbacks!*](http://tm2tips.tumblr.com/post/42657705618/strip-trailing-whitespace-on-save-with-callbacks)
