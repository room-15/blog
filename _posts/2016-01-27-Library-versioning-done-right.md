---
layout: post
title: Library versioning done right
post_author: Martin Breuer
---

While working on cleaning up build scripts and smoothening the complete build process from testing to generating release version, I stumbled upon some libraries we use that are published in a way that makes our life as developers harder than it has to be. I will not name libraries that do that. I don't play the blame game.

The context of this post is Android development, the libraries are mostely hosted on github or gitbucket and the versions are published on jCenter, bintray or similar services.

# Version

Probably every library and software out there has some kind of versioning system. Commonly and widely used is the typical `Major.Minor.Bugfix` variant like `1.2.3`.

Others use that system with additional information like `1.2.3-RC1` where `RC` stands for `Release Candidate`. Others have some kind of time code or `beta` etc.

All of those variants are valid and for us understandable.

## Version wildcard

You all probably know that you can define a version wildcard using `+` in a dependency statement like this:

{% highlight groovy %}
dependencies {
    compile "com.somepackage.group:artifact:+" // latest version
    compile "com.somepackage.group:new-artifact:1.+" // latest minor/bugfix version for version 1
    compile "com.somepackage.group:different-artifact:1.2.+" // latest bugfix for version 1.2
}
{% endhighlight %}

Whenever there is a build triggered, gradle will search for the latest version matching `1.*`. That could be `1.0`, `1.0.0` or even `1.349.54356567956`.

# Wildcards are bad

Did you know that wildcards are bad? Wildcard are bad! There are several problems when you use wildcards.

## Repeatable builds

Even for a hobby project you want to make sure that your CI or your IDE creates repeatable builds. That means, it should not create a different apk if you haven't touched a line in your project.

With the wildcard, the time of the build might become a critical aspect. You start your build at `1am` and test the app. Everything works. You build your release at `2am` and publish it. A day later you get huge amounts of bad rating and crash reports in your developer console.

Now you start to evaluate how this could happen only to find out (probably after digging for a long time) that at `1:30am` the developer of one of your dependencies published a new version where he/she, mistakenly, introduced some bad bugs. This resulted in a updated build at `2am` where you weren't aware of this changes.

## Invisible Changes

The worst wildcard would be if there is no version defined at all but only the `+`. You would never realize that there are improvements or changes in an automatically updated version. You might have workarounds implemented for existing bugs that are already fixed since ages. The API you use might be already deprecated. There might be a cooler way to get stuff done. You will probably never find out until you specifically check your code for deprecate warnings or the API of the library changes in a way that your build breaks.

## You are in not in charge!

The biggest problem with wildcards is that you lose the power. The wildcard decides when and what and to which version it will be updated. You are not in charge of any of these changes. You are not in charge of your project anymore.

In a controlled development environment you define when you have time to update dependencies. You are the one who should say: `Ok, lets see if it works with a newer version.` If you are close to a deadline, you are probably allergic to dependency changes because they could result in a mess and you would risk the deadline.

# Versioning done right

So what do we need to do to prevent this? Well, basically we just need to get rid of all `+` wildcards and we need to define a specific version.

## Make our life easier

One of the critical steps to make our life easier needs to be done by the library developers. And those are just 2 simple things:

* provide the latest version in your `How To` sample (or the readme in github) without using any wildcard.
* Use the git tags to give us an overview of all available versions without searching on jCenter or bintray. With the given tag we can also browse the code that is actually used in our project.

## Plan ahead

We as the user of a library need to make sure that we will never use the wildcard to reference it. That is basically all we need to make sure that we do not run into any of the above mentioned issues. Add update time for your project plans and timelines. At least after each major version release, better after each minor release, you should check if there are updates for your libraries available.

With that in mind you will have repeatable builds, no bad suprises and complete controll over your project again.


#### About the author

*Martin Breuer is an Android developer for [AVM.de](http://avm.de)'s Android app. He has been writing Android code since 2009. He lives in Potsdam, Germany with his wife and a daughter.*
