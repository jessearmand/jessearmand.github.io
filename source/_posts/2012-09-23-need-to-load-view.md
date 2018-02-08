---
layout: post
title: "Need to Load View"
date: 2012-09-23
description: "About loading a view"
categories: [uiviewcontroller, uiview]
---

Just found out today that if a UIViewController subclass is constructed programmatically (that is without using Interface Builder),

and, if we don't create and assign a UIView inside loadView:

```objc

- (void)loadView
{
    self.view = [[UIView alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
}
```

Then, this could ruin the animation transition of <code>presentViewController:animated:completion:</code>. In other words, the transition animation doesn't occur.

There's a reason why the documentation says that we need to implement this. I saw at least one [project](https://github.com/zxing/zxing/tree/master/iphone/ZXingWidget) that doesn't follow this rule.
