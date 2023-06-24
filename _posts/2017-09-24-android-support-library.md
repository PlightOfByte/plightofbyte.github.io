---
layout: post
title: "Android Support Library 101"
date: 2017-09-24
categories: android
subtitle: Understand what Android support library is and when to use it
share-img: /assets/images/android-support-library/share-img.jpg
cover-img: /assets/images/android-support-library/hero-image.jpg
thumbnail-img: ""
---
Android support library can be very confusing to some. And the frequent change they make.

>sigh

Users are the most important thing whenever you are developing an app. You'd want to make sure that maximum users can access your app via play store.  
Google releases a new version of Android roughly every 6 months. They bring feature, make changes with every release.

Android user base is very fragmented.
At any point in time, there are so many versions of Android in the market which you must support.
It's hard to keep your app running the same on every version.

**Enter Support Library**

>"When developing apps that support multiple API versions, you may want a standard way to provide newer features on earlier versions of Android or gracefully fall back to equivalent functionality. Rather than building code to handle earlier versions of the platform, you can leverage these libraries to provide that compatibility layer."

This is from official documentation.  
It sums up the original intent of support library. But support library has evolved a lot since then.

> So, what all does this library have?

* **Backward Compatibility for Features**  
    Whenever a new version of Android is released say marshmallow, a number of features are released like runtime permissions.
    With this Google also releases a support library in which they tend to add some compatibility for users who'd run the app on older OS.

* **Convenience and Helper Classes**  
    There are some features that are exclusively present in support library, things like RecyclerView and ViewPager.
    Also, support library tends to have many convenience and helper classes which would make your life easier.
    
* **Utilities**  
    Support library has many utilities that will help you in improving your code and testing your app. Some of the things are [support-annotation](https://developer.android.com/studio/write/annotations.html){:target="_blank"} and [testing support library](https://developer.android.com/topic/libraries/testing-support-library/index.html){:target="_blank"}

> OK, so what with the version like 24, 25 and this v4, v13?  
This is too confusing.

Whenever Google releases Android's latest and greatest version it also releases support library with the same major version as the API level of the released Android platform.  

So when Android Oreo came out, with it came a support library which had some provision of using new features on an older Android version.  
Android Oreo has API level 26 and the library which came with it is version 26.0.0  
Later when any change in the library is required or some bugs are fixed then minor versions are bumped up.  

When API 27 will come only then support library go to version 27. This naming convention helps in keeping track of what library came with what Android OS.

>Now coming to this v4, v7, v13 and so on...

They are the package name of the classes. **Do not mistake them for an update or upgrade**.  
v4, v7, v13 etc all work together. What they convey with the name is that what API level is required to use them.


So, if you are using a class in package v13 then your app must have minimumSDKVersion as API level 13.  
In other words, classes in the v13 package must be using features present in API level 13 which is why they can not function below API level 13.
And you must set the minSDK to 13 so that your app can never run on lower API levels like 12.


Similarly, v4 needs at least API level 4 and v7 needs API level 7.


Though, there is one small change that has happened when Oreo was released. Along with Oreo came support library 26.

>Spoiler, not so small change.

In library 26.0.0 Google has made the minimum requirement of every support package as API level 14.  
So, now v4, v7, v13 or any package below 14 requires API level 14. Google did this to make the library leaner.

>No one was using such old Android version anyways.

>When should I be using this library?

Well, the general guideline says to always use the support library.  
This helps you with general device compatibility as you probably won't know of minor changes between platforms but support library will take care of it for you.

Also, you are safeguarded from the introduction of a new feature. Had you used native/platform class you'd have to port your code to support and then use the compatibility.  
But if you are always on support classes you don't need to worry. Any new feature comes, you just have to update you support library and you'll get that compatibility. 

>But these libraries have such old things like their own fragments. They will be present in all phones that my app support.  
**So, why should I use support?**

Well, you are right in some ways. Like, things in v4 package came out way back, for example, take Fragments.  
Fragment was introduced in API level 11 and was added in v4 so they could be used API level 4 onwards as well.
But in today's world apps have a higher minimum Android support version.  
It doesn't really make sense to use these old classes and using the native/platform's class makes more sense.

>**WRONG!!!**

My opinion is one should still stick with support classes as they are there to safeguard you from any changes that come in future.  
Google keeps the support library pretty updated. They have even updated everything to a minimum of API level 14, making the code leaner by dropping around 1400 methods.

>This breath of fresh air in support even v4 is a message that support is here to stay and we should embrace it.

I hope this blog made things clear. If you have any confusion or a question unanswered then do drop a comment.

Share this article if you like it.