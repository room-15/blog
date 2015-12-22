---
layout: post
title: Manipulating the Status and Navigation Bars
post_author: Adam McNeilly
---

When making a game or application you may want a fully immersive UI to enhance your user's experience. The main components that will prevent this are the Status Bar (the strip at the top with notifications and time) and the Navigation Bar (the strip at the bottom with the back, home, and recent apps buttons). Let's walk through how to manipulate these programatically.

Please note that any code used here is for API 16 (Android 4.1) and above.

### Background

Before we begin writing some code, there are two methods you should be familiar with. The first is [#Activity.getWindow().getDecorView()](http://developer.android.com/intl/pt-br/reference/android/view/Window.html#getDecorView()). From the documentation:

> Retrieve the top-level window decor view (containing the standard window frame/decorations and the client's content inside of that), which can be added as a window to the window manager.

In other words, the view returned by this is the one we will work with to manipulate our status bars. The next function we need is [#View.setSystemUiVisibility(int visibility)](http://developer.android.com/intl/pt-br/reference/android/view/View.html#setSystemUiVisibility(int)) which controls the change for the status bar and other screen decorations. See the documentation for a full list of possible flags that can be used.

### Dimming the Status Bars

If you don't want a completely immersive experience, but would like to dim the status bars you can use the `SYSTEM_UI_FLAG_LOW_PROFILE` flag:


	private void dimBars() {
		View statusViews = getWindow().getDecorView();
		statusViews.setSystemUiVisibility(View.SYSTEM_UI_FLAG_LOW_PROFILE);
	}

### Hiding the Status Bars

To hide the status bar by itself, you want to use the `SYSTEM_UI_FLAG_FULLSCREEN` flag. It is good practice to hide the ActionBar/Toolbar as well when using full screen:


	private void hideStatusBar() {
		View statusViews = getWindow().getDecorView();
		statusViews.setSystemUiVisibility(View.SYSTEM_UI_FLAG_FULLSCREEN);
	
		getSupportActionBar().hide();
	}


While you can hide the navigation bar by itself, this isn't really necessary. If you are going to hide the navigation bar, you should hide the status bar and action bar as well. Here is how you would create that completely immersive experience:

	private void hideSystemBars() {
		View statusViews = getWindow().getDecorView();
		statusViews.setSystemUiVisibility(View.SYSTEM_UI_FLAG_FULLSCREEN | View.SYSTEM_UI_FLAG_HIDE_NAVIGATION);
	
		getSupportActionBar().show();
	}

This method uses a bitwise operation to apply two separate flags in order to achieve both full screen (hiding status bar) and hiding the navigation bar.

### Showing Status Bars

To show the bars again, we can still use `setSystemUiVisibility()`. Instead of giving it a flag, we can just pass a value of 0 to clear any existing flags.

	private void showSystemBars() {
		View statusViews = getWindow().getDecorView();
		statusViews.setSystemUiVisibility(0);
	
		getSupportActionBar().show();
	}

### Conclusion

This should be everything you need to get started with manipulating the system UI components. For more detailed information you can check the [Android developers page](http://developer.android.com/intl/pt-br/training/system-ui/index.html) and view a sample project on [Github](https://github.com/androidessence/System-UI-Sample).
