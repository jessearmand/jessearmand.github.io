---
layout: post
title: "Revisiting Size Classes"
date: 2016-05-14 02:04:29 +0800
categories: ios, auto-layout
keywords: "ios, size-classes, auto-layout"
---

Articles on iOS size classes never went beyond the ideal that we have to think in terms of Any, Regular, Compact Horizontal or Vertical size classes for adaptive layout, instead of pixels and devices screen sizes.

Ever since size classes was introduced in iOS 8, I have always wondered, why do iPhone 4s, 5, and 6 are all under Compact Horizontal and Regular Vertical size classes?

While in reality they have different dimensions, and during design phase, we have to think about the dimensions of views and their subviews, together with the distances between them due to these varying screen sizes.

We could of course declare auto layout constraints programmatically with a multiplier based on screen width or height. But, from what I understood size classes are supposed to move us forward from this way of thinking towards designing layout specifically just for the abstractions of them. Until I encountered one article that describes my confusion on size classes:

[Size Classes - Close, but Not Quite](http://blog.blackfrog.com/2014/06/27/size-classes-close-but-not-quite/)
By Kevin Packard [@kevinpackard](http://twitter.com/kevinpackard) One Storyboard to Rule Them All At WWDC 14, Apple introduced Unified Storyboards and Size
[blog.blackfrog.com](http://blog.blackfrog.com)

**Note** that, the above article was posted about 2 years ago.

Exactly, if a designer should consider how an iPhone 4s is way shorter than an iPhone 6, resulting in individual layout design for each iPhone 4s, 5, 6, 6 plus. Then, aren’t we still doing what we are doing before size classes were introduced?

Well, I hope this will change in WWDC 2016.
