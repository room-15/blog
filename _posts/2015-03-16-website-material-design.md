---
layout: post
title: Websites should keep up with Material Design
---

[Material Design](http://www.google.com/design/spec/material-design/) has been around for quite some time now, and many apps are adopting the new design. Not only apps, but many websites are doing that too. But there's a difference between an app and a website. For example, websites can't change the color of the Action Bar (of the browser). But now, it turns out that Google Chrome supports this.

### A(n) HTML Metadata Tag

As simple as that. Starting from Chrome version 39, the `theme-color` meta tag is officially supported by Google. This allows us to set a color for the Action Bar, the Status Bar, and so on. Darkness seems to be automatically handled by Chrome, so all we need to do is simply set the primary color: `<meta name="theme-color" content="#db5945">`. That's it!

### Conclusion

Websites such as [PubNub](http://www.pubnub.com) already implements this. Web developers should update their sites to keep up with this new feature.
