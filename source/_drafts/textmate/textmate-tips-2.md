title: 'Use Textmate 2 as a Pro: Preferences'
categories:
  - Coding
tags:
  - Textmate 2
  - Pro-tips
---

##Abstract

This post introduced installation, preferences and other settings of Textmate.

<!-- more -->

##Installation

First of all, download Textmate 2 app [in this page](http://macromates.com/download).

##Preferences

The next big thing is setting up some preferences after installation.

###Files

You may change "New Document Type" to the type you use most here.

###Projects

You can set "Show File Browser On" which side here. My personal choice is left side. You may toggle the state of file browser by a keyboard shortcut: ``contorl + option + command + d``.

There is an option named "Show command output" at the bottom. This will be very useful if you use bundles like Git or Markdown. I like to make my Textmate fullscreen and set it below text view.

###Bundles

A view of all the bundles and their information here. You can find support for almost everything in Textmate with bundle. If don't, you can create one yourself. We will have several post on different bundles to introduce more about them.

###Terminal

Install shell support, then you will be able to open Textmate in Terminal fairly simple by using ``mate`` command. I always begin my day by opening Terminal, cd to the project repository, and type ``mate .``.

We have a bunch of setups elsewhere except for just in preferences. Now we turn to the navigation bar of Textmate and do some more.

##Edit

Click "Check spelling as you type" under "Edit" in "Spelling".

##View

In "Font" -> "Show Fonts", we may customize our desired font for coding. There are also two shortcuts to change font size here: command + & command - mean Bigger and Smaller relatively.

The font I use for writing blog is Noto Serif, Regular, size 13. I choose Courier New, Bold, size 13 for coding, basically because Noto Serif is not supported very well when aligning the assignments.

Other settings:

1. Show invisible characters.
2. Enable soft wrap.
3. Wrap column -> Use Window Frame.
4. Tab size -> 2.
5. Allow Scroll Past End.

##Theme

I made Theme an independent part because it has great significance in making Textmate your own.

"View" -> "Theme" -> List of Installed Themes...

The theme I use is Cobalt, which has dark blue background and comfortable syntax highlights.

You can add more Textmate 2 themes by simply search on the Internet and download them. Then open that file with Textmate, it will be added to "View" -> "Theme".

###Use Textmate 1 Theme

In Textmate 2, all themes are turned into bundles. So there is no support for .tmTheme files anymore. But, if you like old themes, there is a simple way to do it.

Suppose now you have a .tmTheme file, how can you use it in Textmate 2? The genius behind it is to wrap it in a bundle. Go ahead and open Textmate 2. Find "Bundles" in navigation bar. Click "Edit Bundles...".

A window will pop up and show all the bundles you installed. Press "command + n", there will be a dialog asking you to choose what you want to create. The default choice is "Bundle". Click create.

1. Give it the name of the theme, and give it a description by clicking on the bundle itself.

2. Go to: `/Users/<yourname>/Library/Application Support/Avian/Bundles/` And open your bundle by right-clicking to show package contents.

3. Add a `Themes` directory and drag you theme(s) into it.

Thanks to [Michael Sheets](http://pastie.org/private/le1pyvas9g1zgnetfwvg) for this tip. When you create one with this method, it will appear at the file location described above.

I’ve found that most bundles for the alpha live in `/Users/<yourname>/Library/Application Support/Textmate/Managed/Bundles/`. You can find a directory named `Themes.tmbundle`. Get into that directory, then open `Themes`, put your desired .tmTheme file in there. Themes will show up under the "View" -> "Themes" menu item.

##Summary

I introduced several things that need to be done before great coding experience. I would argue that setting up is always important before you actually begin doing things. Spend some time on it and your life will be good from then on.

---

This is one of my series posts on Textmate 2, a handy, light, expandable editor on Mac OS X. For more pro tips on Textmate 2, click [here]().

If you have more pro tips or fancy usage of Textmate 2 you want to share, please do let me know by commenting, sending me emails or informing me on social networks. Thanks a lot!
