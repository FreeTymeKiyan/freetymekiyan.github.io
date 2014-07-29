title: 'Use Textmate 2 as a Pro: Preferences'
tags:
  - TextMate 2
categories:
  - Coding
---

##Preferences

The first thing is setting up some preferences after installation.  

###Projects

You can set show file browser on which side here. My personal choice is left side. You may toggle the state of file browser by a keyboard shortcut: ``contorl + option + command + d``. 

There is an option named "Show command output" at the bottom. This will be very useful if you use bundles like Git or Markdown. I like to make my Textmate fullscreen and set it below text view. 

###Terminal

Install shell support, then you will be able to open Textmate in Terminal fairly simple by using ``mate`` command. I always begin my day by opening Terminal, cd to the project repository, and type ``mate .``.

We have a bunch of setups elsewhere except for just in preferences. Now we turn to the navigation bar of Textmate and do some more.  
 
##Edit

Click "Check spelling as you type" under "Edit" in "Spelling". 

##View

In "Font"->"Show Fonts", we may customize our desired font for coding. There are also two shortcuts to change font size here: command + & command - mean Bigger and Smaller relatively.   

The font I use for writing blog is Noto Serif, Regular, size 13. I choose Courier New, Bold, size 13 for coding, basically because Noto Serif is not supported very well when aligning the assignments. 

Show invisible characters

Enable soft wrap. 

Wrap column -> Use Window Frame

Tab size -> 2

Allow Scroll Past End

##Theme

I made Theme an independent part because it has great significance in making Textmate you own. 

"View" -> "Theme" -> List of Installed Themes...  

The theme I use is Cobalt, which has dark blue background and comfortable syntax highlights.   

You can add more Textmate 2 themes by simply search on the Internet and download them. Then open that file with Textmate, it will be added to "View" -> "Theme". 

###Use Textmate 1 Theme

In Textmate 2, all themes are turned into bundles. So there is no support for .tmTheme files anymore. But, if you like old themes, there is a simple way to do it. 

Suppose now you have a .tmTheme file, how can you use it in Textmate 2? The genius behind it is to wrap it with a bundle. Go ahead and open Textmate 2. Find "Bundles" in navigation bar. Click "Edit Bundles...".

A window will pop up and show all the bundles you installed. Press "command + n", there will be a dialog asking you to choose what you want to create. The default choice is "Bundle". Click create. 
