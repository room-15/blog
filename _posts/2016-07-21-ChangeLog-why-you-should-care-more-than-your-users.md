---
layout: post
title: ChangeLog - Why you should care more than your users
post_author: Martin Breuer
---

I recently got the task to implement a change log dialog to inform our users about changes. There are some requirements like showing the beta user more details than the regular user etc. There was a lot of discussions and our project manager loved the change log because he feels it transports important information.

On the other hand there was the developer team and nobody there is even neutral to the fact of dismissing a dialog they feel nobody will read.

# UX Nightmare

In general you have just two types of users: One that read the change log and want to see them and the impatient once who do not care and are just annoyed by dismissing the dialog because they just want to use the app. Now!

# Your app should learn

We came up with a solution that triggered our project manager to tell everybody that we have implemented an "AI" or a "self learning app". So what have we done?
We have two different solutions and are presenting them in A/B testing way to the users and we will see what their response is.

## Solution A

The first solution is well known: Let the dialog have a tick where you can just say: never show again. Done.

## Solution B

This solution is more advanced and should in the best case never been realized by the user: We measure the time the user has the dialog open. If the user has the dialog open for longer than 3 seconds or is interacting with it beside pressing "ok", we are storing this behavior as some kind of "interested".
If the user dismisses the dialog within this 3 seconds, we are storing this as "not interested" and if we encounter a set of 3 "not interested" in a row, the user obviously doesn't want to read the change log and therefor we do not even open it.

# Considerations

Two most important things for your change log:
 # be honest and not short handed: "fixed multiple bugs" is boring to read. Express what you have done in a more explanatory way without throwing technical details around
 # make sure the change log can be opened on demand in your settings or about page. Also you might want to reset the "interested" value once they have opened the change log. You know, people might change :)

#### About the author

*Martin Breuer is an Android developer for [AVM](http://avm.de)'s Android app. He has been writing Android code since 2009. He lives in Potsdam, Germany with his wife and a daughter.*
