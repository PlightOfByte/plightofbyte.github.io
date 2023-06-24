---
layout: post
title: "Genymotion vs Android Emulator"
date: 2017-09-03
categories: android
subtitle: Has the Android emulator improved enough to take on Genymotion
share-img: /assets/images/genymotion-vs-android-emulator/share-img.jpg
cover-img: /assets/images/genymotion-vs-android-emulator/hero-image.jpg
thumbnail-img: ""
---
There has always been a debate about which android emulator to choose or to go with Genymotion. I've seen most of the discussion ending in favour of Genymotion.  
I've gathered some data around the most common use case in my experience. Based on this I'll be evaluating all the android emulators along with Genymotion.

**TL;DR: Android emulator is faster than Genymotion when configured right.  
Use x86 (32 bit) image with Google APIs, 3GB ram, quad-core CPU.**

> Pheww, glad we're past that  
Now, let's dive deep

**Disclaimer**: I've tested my use case which to me looks the general use case i.e. running tests.
All benchmarks were done on a mid-2015 MacBook Pro.  
Wherever I say Genymotion I mean **Genymotion Desktop**. They have other products like Genymotion on Cloud & Genymotion on Demand which are not being considered here.  
I'm not saying Genymotion is inadequate. But it is slower when running tests compared to certain Android emulators.

A little background on the subject and then we'll jump to the good stuff.

_Psst: I have some benchmarks down the line, stick around._

Long ago Android emulator was the only way to go. But they were too slow to use, the reason being a **change of architecture**.  
What can you expect out of an ARM emulator running on an x86 machine? Every instruction had to be converted from ARM to x86 architecture which makes it really slow.

Then came along the x86 images of Android which are way faster as they get rid of the ARM to x86 platform change.  
Now you can run x86 Android emulator on an x86 machine.  

>_Problem solved!!!_
>
>**NO!**

Android emulators were still slow from what people wanted.  
Then came along Genymotion, which is just an Android VM running in a virtual box. But it is quite stable & fast compared to plain old android emulators which run on qemu.

Let's jump to how the situation is today.

I'm using Genymotion in CI infrastructure and on my machine.
The task at hand was to **get rid of Genymotion** used in CI infrastructure and machine.

>You ask why?  
Licenses cost money. Duh...

At a quick glance it seems like a stupid move as Android emulators are slow and buggy. But when you get into the nitty-gritty of the situation, you’ll find Android emulator to be superior.

My use case is to run integration tests on them (mostly espresso), just over 1100 tests. Genymotion takes ~23 minutes to run all the tests.

A few other problems faced with Genymotion.
- Limited command line tools ([GMTool](https://docs.genymotion.com/Content/04_Tools/GMTool/GMTool.htm){:target="_blank"}).
- They needed a periodic restart because of memory issues. This was a manual task, imagine doing it on a CI infrastructure with lots of machines.

**Enter Android Emulator**

The first time you try to set-up one of these it gives you so many options that you'll feel like you are in Subway restaurant.  
The biggest question of all is x86 or x86_64 and with Google APIs or without them.

I did some research and benchmarking with these combinations and this is what we came up with.

Drum Roll...

> The winner of the competition is **x86 with Google APIs**  
> But how? why?

Well, I'll tell you the problem with every one of them.  

x86_64 is slower compared to x86

> By how much you ask.
>
> **28.2%** much !!!

Emulator with Google APIs is more stable, things tend to crash without them.

This brings us to the conclusion that the best one is **x86 with Google APIs**.

Before we pit our winning emulator against Genymotion. There are a few more details that are of great importance.

* I've used a Nexus 5 system image with Google APIs.
* I noticed that giving emulator less ram caused a lot of Google API crashes. So I've settled for 3GB of ram for an emulator.
* The emulator has a quad-core.
* Host machine has HAXM installed.

**Time for few benchmarks**

![Genymotion and Android Emulator Espresso Benchmark](/assets/images/genymotion-vs-android-emulator/EspressoBenchmark.png)

![Linpack](/assets/images/genymotion-vs-android-emulator/Linpack.png)

![Geekbench 4](/assets/images/genymotion-vs-android-emulator/Geekbench4.png)

From the benchmarks, you can see that Android emulator beats Genymotion expect for in Geekbench4. This feels more of a virtual box beating qemu thing. 

> All hail the King of the Emulators

We are now having a faster test execution time, better command line tools.
Also with the latest [Android Emulator](https://developer.android.com/studio/releases/emulator.html){:target="_blank"}, things have gone a notch up. Faster boot time and what not.

Google has been working very hard to

>Make Android Emulator great again

If you haven't been using android emulator for some time. I'd suggest you revisit and save some money.

One other solution which I was trying but couldn't really get it to work was running an [Android-x86](http://www.android-x86.org){:target="_blank"} image on AWS.  
I was able to run it on a vSphere ESXi Hypervisor but not on AWS or any other cloud platform. If someone knows anything about it do comment below.

PS: [VMWare is now available on AWS](https://aws.amazon.com/vmware/){:target="_blank"}, [Android-x86](http://www.android-x86.org){:target="_blank"} on AWS might be possible after all.

What do you think about Genymotion desktop and Android emulator? Comment below.

Share this article if you like it.