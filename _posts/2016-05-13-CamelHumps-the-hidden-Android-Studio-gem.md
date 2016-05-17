---
layout: post
title: CamelHumps the hidden Android Studio gem
post_author: Martin Breuer
---

Recently I saw a [new post](https://plus.google.com/u/0/+PhilippeBreault/posts/MHYF7wymVgi) in this rarely updated [Android Studio Tips of the Day](https://plus.google.com/collection/wtO0PB) collection.

CamelHumps is a blessing while developing! I wanted that feature since ages but I would never suspected to find it named CamelHumps and I would probably never tested that setting.
After enabling it, I realized that there is a small disadvantage: You can't select a method/variable name with mouse double click anymore!

Everyone should know that a double mouse click on a method or variable name normally select the complete name. With CamelHumps enabled this no longer works. Instead the one camel cased word is now selected. First I thought I just need to make a third click but that already selects the complete line. So with CamelHumps you can't select a method/variable name with a mouse double click anymore!

# Workaround

There are two general workarounds and what you use probably depends on your prefered input device.

## Keyboard Solution

The keyboard solution is pretty simple as you probably already know what ctrl + w (or your shortcut assigned to "extend selection") does. So as soon as your cursor is somewhere at the name, press ctrl + w twice and the complete name is selected.

## Mouse Solution

The mouse solution can be simple as well. If you, like me, never used the mouse wheel press to paste your clipboard content (at least thats default on Linux), you can simply reassign that mousewheel press as a shortcut to "extend selection". I prefer the "double click" for that as otherwise the cursor seems to jump a bit when I am double clicking too fast. Not sure where this is from but it feels buggy. So just assign "double click mouse wheel" to "extend selection" and you are done. Now you only need to train yourself to move the left (or right) finger to use the mouse wheel to select a name. Should only take you a few minutes to get used to that.

Enjoy the increased productivity!

#### About the author

*Martin Breuer is an Android developer for [AVM](http://avm.de)'s Android app. He has been writing Android code since 2009. He lives in Potsdam, Germany with his wife and a daughter.*
