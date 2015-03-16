---
layout: post
title: Keep the "m" prefix
---

Recently I stumbled upon [an article that argued against the usage of the "m" prefix](http://trevore.com/post/stop-it-m-prefix).
I would like to write provide an answer to that argumentation and why we should stick with this prefix.

### Redundancy?

The basic and most important argument in the linked article is that modern IDEs like Android Studio have sufficient
code coloring and font styles to show in which context variables exists. It is true but it is enough?
A normal developer will often read code outside his own IDE. He will need to browse source code on websites like
github or bitbucket to evaluate libraries or tools he might want to use in his own projects. There are also review tools
like Stash or Gerrit.
All four websites/tools are showing only basic coloring of the code. They mainly just highlight keywords for the given
source code. Just compare some samples on
[github](https://github.com/android/platform_frameworks_support/blob/master/v4/java/android/support/v4/app/ActivityCompat.java),
[bitbucket](https://bitbucket.org/kugking/datas-for-android-android-studio/src/3f9a3be7ff5118e02ae11b1f24741b0b1cc6ed00/app/src/main/java/kavita/zocial365/app/datas/FullScreenImageActivity.java?at=master),
[stash](https://confluence.atlassian.com/download/attachments/300817283/STASH13_PRactivity.png?version=1&modificationDate=1349754860272&api=v2)
and [gerrit](https://android-review.googlesource.com/#/c/136041/2/services/core/java/com/android/server/wm/WindowManagerService.java).
There is no visible difference between a local variable and a member variable. You can't use any shortcut you know from
your IDE to easily identify if that one variable is a member or a local or a parameter. You have to search for it to get
the information.

Especially for the review tools stash and gerrit the context of a variable is not always directly visible. If the method is
too large and complex, even the local context might not be completely visible or easy to understand.

In a situation like that you will be very happy to find some kind of prefix to identify if what kind of variable you are
looking at.

### IDE benefits

The complains about the needed "one time" configuration of the IDE to not generate getter and setter with the "m" prefix is no
valid argument for me. Nearly nobody I know uses an unconfigured and uncustomized IDE. Everybody made some changes
somewhere. Code style, auto formatting, rules, plugins, macro scripts, themes, hot keys. The list is long and changes
for every developer you might ask.

### Android

The "m" prefix is a standard provided by Google. If you like it or not, the code of Android looks like that and I don't
see a valid reason to ignore a platform code standard. You very likely also use java style for your brackets and not
the C# "separate line" style.

### Conclusion

The arguments on the mentioned article are not wrong by definition but I feel that they aren't considering the daily
work a professional programmer has to do and that therefore the weight of these arguments is not enough to revolt against
the [provided Android code style](https://source.android.com/source/code-style.html).
