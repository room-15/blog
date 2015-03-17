---
layout: post
title: Overlaying the System/Navigation Bar
post_author: cygery
---

First off, the method described in this post only works on Android versions up to 4.3. 
The behavior which allowed this was changed in Android 4.4.
Still, at time of writing, nearly 50% of all Android devices run Android 4.0-4.3.
Thus, especially devices which likely won't receive a system update can benefit from apps using the method described here.

### The System/Navigation Bar

Android 3.0 introduced the *system bar*. 
This bar, usually located at the screen bottom, provides navigation controls as 
so-called *software buttons*, replacing hardware buttons. 
Additionally, it holds elements from the *status bar*, e.g., a clock and access to the notification area.

Starting with Android 4.0, the *system bar* is replaced by the *navigation bar* which doesn't provide functionality of 
the *status bar* anymore. 
Instead, it mainly holds the *home*, *back*, and *recents* buttons. 
Additionally, a legacy *menu* button is shown when necessary.
Starting with Android 5.0, the *navigation bar* also provides a button for keyboard/language switching.

### (System) Windows

Android uses a variety of different Window types (listed in the [WindowManager.LayoutParams](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html) class) for different purposes. 
For example, there exist types for [normal application Windows](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_APPLICATION), 
[input methods](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_INPUT_METHOD), 
the [keyguard dialog](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_KEYGUARD_DIALOG), 
or the [status bar](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_PHONE).

Some Window types can be used freely by any app, e.g., [normal application Windows](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_APPLICATION). 
Others require the [SYSTEM_ALERT_WINDOW permission](http://developer.android.com/reference/android/Manifest.permission.html#SYSTEM_ALERT_WINDOW), e.g., 
[system alert Windows](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ALERT) or 
[system error Windows](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ERROR).
Finally, there exist purely internal Window types, e.g., for the navigation bar.

Windows can be added via the [WindowManager](http://developer.android.com/reference/android/view/WindowManager.html):

    // get the WindowManager for the context-specific Display
    WindowManager wm = (WindowManager) context.getSystemService(WINDOW_SERVICE);
    
    // create the (root) View for the new Window
    View v = new View(context);
    // [... setup the View]
    
    // setup parameters for the Window
    // [... width, height, x and y pos]
    // create a system alert Window (requires the SYSTEM_ALERT_WINDOW permission)
    int type = WindowManager.LayoutParams.TYPE_SYSTEM_ALERT;
    // make the Window receive no touch input events
    int flags = WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE;
    // the pixel format, here: translucent
    int format = PixelFormat.TRANSLUCENT;
    
    // create the LayoutParams for the new Window
    WindowManager.LayoutParams params = new WindowManager.LayoutParams(width, height, xpos, ypos, type, flags, format);
    
    // add the View to the WindowManager instance, creating a new Window
    wm.addView(view, params);

Similarly, a Window can be removed:

    wm.removeView(view);

#### Window Flags

There exist a lot of available Window flags, cf. [WindowManager.LayoutParams](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html).
Some of them control if the Window is (not) [focusable](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_NOT_FOCUSABLE) 
or [touchable](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_NOT_TOUCHABLE), 
others *when* the Window is shown, e.g., [FLAG_SECURE](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SECURE) 
and [FLAG_SHOW_WHEN_LOCKED](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SHOW_WHEN_LOCKED), 
yet others control *where* the Window is shown or laid out.

In the following two sections, such a flag will be used to put a Window on top of the navigation bar.

### Overlaying the Navigation Bar

Generally, overlaying the navigation bar was considered impossible, e.g., in an answer to this [question](http://stackoverflow.com/questions/14283575/android-overlay-on-top-of-software-buttons) on StackOverflow. 
However, it is possible (prior to Android 4.4):

Windows a z-ordered depending on their type via the internal [windowTypeToLayerLw](http://androidxref.com/5.1.0_r1/xref/frameworks/base/policy/src/com/android/internal/policy/impl/PhoneWindowManager.java#1820) method.
As shown in this method, there exists a single Window type which is laid out on top of the navigation bar type *and* is available to user apps: 
[TYPE_SYSTEM_ERROR](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ERROR).

#### A Small Remaining Issue

By default, Windows can't be laid out anywhere on the screen because the boundaries for screen content exclude the navigation bar.
However, via the [FLAG_LAYOUT_NO_LIMITS](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_LAYOUT_NO_LIMITS) flag it is possible to allow Windows to extend beyond these boundaries.

When this flag is set, a Window can be easily placed on top of the navigation bar, e.g., by setting the Window parameters `gravity` to `Gravity.BOTTOM | Gravity.LEFT` and the `y` coordinate to the negative navigation bar height.

#### The Fix

In Android 4.4 this behavior was [changed](https://android.googlesource.com/platform/frameworks/base/+/b816bedf6f1e6c81fd497df98aaae68a9ce546b0%5E!/#F0). Effectively, this change disables [FLAG_LAYOUT_NO_LIMITS](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_LAYOUT_NO_LIMITS) for [TYPE_SYSTEM_ERROR](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ERROR) Windows.
This makes it impossible for user apps to place a Window over the shown navigation bar anymore.

#### "Behavior"?

Yes, "behavior". I'd call it a bug, but here's the background:

Shortly after I discovered the issue I contacted the Android Security Team mid-2013 since I considered it to be a security-related bug, making Denial-of-Service attacks possible by user apps blocking the navigation bar. In a reply a few weeks later, they don't consider it a security vulnerability because the [SYSTEM_ALERT_WINDOW permission](http://developer.android.com/reference/android/Manifest.permission.html#SYSTEM_ALERT_WINDOW) "gates the behavior". Also, they gave me a heads-up (thanks!) that "this behavior may change in a future release" - as it did in Android 4.4 that year.
