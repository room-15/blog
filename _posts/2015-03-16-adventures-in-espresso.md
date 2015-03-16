---
layout: post
title: Adventures in Espresso and Unit Testing Part 1
author: Carl Anderson
---

### Intro

I recently decided it was time to add some automated testing to my app. After doing a bit of research, it seemed like adding Espresso for automated testing, and JUnit tests for my model objects would be the right way to go. So I started off by going to the [Espresso setup page](https://code.google.com/p/android-test-kit/wiki/EspressoSetupInstructions), which had a bunch of instructions for my IDE, Android Studio. It seemed relatively straightforward. Seemed.

### Setting up Espresso

The first thing you need to know with the Setup instructions page, is that it apparently has bugs in it that the maintainers are too lazy to fix. If you copy and paste everything according to the instruction, the HelloWorldEspressoTest doesn't compile. Here are a few lessons learned:

* If you run proguard on your test binary (because you have lots of prolific APIs included), it's not going to work. I had minifyEnabled set to true in my gradle build file, which prevented the tests from running. Fortunately for me I realized I didn't need the [18k APIs that come with guava](http://jakewharton.com/play-services-is-a-monolith/).
* Missing from the setup instructions is that you'll need to have the [full set of excludes](https://code.google.com/p/android-test-kit/issues/detail?id=122) under *packagingOptions*
* All of the examples are using statically imported functions. So things like *onView* is really *Espresso.onView*, but since they don't list their imports in the examples, cutting and pasting is going to result in a bunch of compile errors.
* The example uses the @LargeTest annotation, but doesn't say what it is, or why. I found a pretty good explanation [here](http://googletesting.blogspot.com/2010/12/test-sizes.html).

The Hello World test they post has basically no explanation(which is basically the status quo with this API). It's not until using the API for a bit that I even understand what it tests - it looks for a View object that specifically has the exact text of "Hello world". Not "Hello World", not "Hello World!", if you type the wrong text, it won't find the view, and will fail. After finding the view (or not), it specifically checks whether it is displayed or not. You would think that *check()* would return a [boolean value](http://stackoverflow.com/questions/20807131/espresso-return-boolean-if-view-exists), but instead it returns a ViewInteraction. Anyway, the entire test is fairly useless, I recommend skipping it and writing your own equivalent for your app. Find a view object (TextView or similar) that will show up on your app's main Activity, and write a line to check if it's there:

>      onView(withId(R.id.main_activity_textview)).check(matches(isDisplayed()));

I'm getting ahead of my self here, at this point I had changed my gradle file to have all of the changes necessary to get Espresso to run, the next step was writing an actual test. Two questions I had at this point - where do I put my tests, and how do I run them. Now, the guide suggests putting them in "src/androidTest/java/com.example.package/", which didn't make sense to me. My source code structure uses a style like "src/androidTest/java/com/example/package/", and there wasn't any explanation explaining this difference (see the pattern?). Another thing that I really struggled with here, is that since I also wanted unit tests that ran separately, it wasn't really clear what to do, and I ended up changing it around several times before getting it finally working. I finally decided to put all of my Espresso tests into "src/androidTest/java/com/example/espresso", and my JUnit tests into "src/test/java/com/example/model".

### Writing and running Espresso tests

Once I had all of that figured out, I wrote my first UI test for Espresso. It wasn't much, I basically launched the legal notices activity for the app and did a check on whether it displayed the textview with all the legal notices or not. I found that none of the context menus within Android did things the way I wanted them to, and just ended up hand-coding the whole thing based on the Hello World example. It was fairly straightforeward. However, it was at this point where I really wanted some sort of "Espresso Manifesto" type document that spelled out things like:

* What is the goal / purpose Espresso?
* What are good things to test? What are bad things to test? What are some of the limitations?
* Why would I use Espresso?

The main [Espresso](https://code.google.com/p/android-test-kit/wiki/Espresso) page has about 6 sentences that read more like marketing than any sort of attempt at answering these questions. The video linked on that page is **painful** to watch. Terrible jokes / attempts at comedy, quotes like "When you make it easy for developers, they'll just do it", and "it's easy to set up." It eventually gets to talking about the framework, but is much more of a sales pitch than a manifesto for how to use Espresso.

What I've learned since trying to do things in the framework is:

* Espresso is good at clicking on buttons, pretty good at putting text into boxes, and checking whether views are visible or not.
* Espresso is not good with Actionbar tabs.
* Espresso won't help you make sure your layout isn't screwed up. It programmatically looks up your view object, and as long as it's on the screen everything will work.
* Sometimes things don't work even though everything is on the screen. I ended up needing to use scrollTo() to get a button "visible", even though as I was watching on the screen, it was fully visible.

So far I've had some decent success writing tests that navigate through my UI, but figuring out the process has been rather difficult. There's just not very good documentation (again with the theme!). For instance, when my button click was failing, because it wasn't "90% visible", I shouldn't have to google a solution to this:

> android.support.test.espresso.PerformException: Error performing 'single click' on view 'with id: test.com.myproject.app:id/navigationButtonProfile'.
Caused by: java.lang.RuntimeException: Action will not be performed because the target view does not match one or more of the following constraints:
at least 90 percent of the view's area is displayed to the user.

Another aspect that wasn't really clear, is how to configure Android Studio to *run* the tests. This was more confusing once I added the JUnit tests, but suffice to say, the way I have it configured is as an Android Test (not a JUnit test) that runs all of the tests in a my specified espresso package.

Once I got through all of that, I was able to finally write a Test to go through a lot of different steps in my UI, which is cool and (hopefully) useful.

Part 2 coming soon...
