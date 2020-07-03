---
title: "Android Makers Q&A: The Hitchhikers Guide Through Kotlin Multiplatform"
date: 2020-05-12 00:00:00 +01:00
tags: [kotlin, multiplatform, q&a]
image: "featured.png"
---

<figure>
<img src="/android-makers-qa-the-hichhikers-guide-through-kotlin-multiplatform/featured.png" alt="Android Makers Q&A: The Hitchhikers Guide Through Kotlin Multiplatform cover">
<figcaption>Photo by Mollie Sivaram on Unsplash. Adapted for my talk The Hitchhikers Guide Through Kotlin Multiplatform</figcaption>
</figure>

Last April I had the opportunity to speak at Android Makers 2020. Although the experience of speaking to a monitor instead of a live audience is drastically different, everyone was amazing and the feedback that I was continuously receiving on a live chat thread made this experience unique!

There was also a [sli.do](https://www.sli.do/) where I’ve had a live poll with which people could interact. And the most important part, a Q&A section where I got all the answered ordered by popularity (votes).


#### Was it a heart-breaking for you to change http from retrofit to ktor-client?

Well… it was something that I’ve given for granted for the past years 😅. But this day had to come! I still remember the project where I first added it. I was in doubt if I should go for volley or retrofit, but its elegance, that beautifully crafted retrofit request, took the best of me and since then I’ve been with it in all my projects. Ktor is pretty awesome too! The only thing that happens is that I need to continuously check it’s syntax because I’m too familiar with retrofit. Give it a try 🙂.


#### How do you handle suspend functions on Swift?

I don’t 🙃. Suspend functions aren’t being exported to the framework at this time. It’s expected to be in Kotlin 1.4.

According to the latest comment on this:

```text
In Kotlin 1.4 (starting from M2) suspend functions will be available from Swift/Objective-C as 
functions with completionHandler: callback.

This will make it possible to implement a wrapper to make Deferred from Kotlin suspend fun, 
e.g.

kotlinDeferred { suspendFun(args, completionHandler: $0) }
```
**Source:** [https://github.com/JetBrains/kotlin-native/issues/2438](https://github.com/JetBrains/kotlin-native/issues/2438)


#### Where is the dependency injection library is used? Inside the CommonMain or is it platform related?

It depends on your feature 🙂. You can do it in **commonMain** or **androidMain/iosMain** or even on both. You’ll need to first check if the dependency injection library that you’re familiar with has support for KMP or not. Currently, the one that stands out is Kodein.


#### How big a compilation overhead? For example if I take my two separate app and put them into multiplatform without sharing any code. Should I expect any overhead.

The overhead comes from generating a framework that will be imported on the iOS app. It will contain the code from your shared module with which you can interact.

If you won’t share any code, there’s no need to create a framework, so the overhead should be minimal. 🙂


#### In your opinion, will it impact on app performance and app size?

Well, this really depends on your project 🤓. For instance, if you have a C++ library on your project, having this code on Kotlin will have better performance since you access the shared module directly without the need for those (heavy) JNI bindings.

About the app size it will depend on how much code you’ll be sharing, but I would say that on average perhaps 0.5–1.5MB.


#### How the new iOS version compatibility will be managed?

The iOS application will still be done natively, only the shared code is done on Kotlin. So you can always use the latest version of Swift and of the OS.


#### It’s definitely nice. But is there any limitations? For instance in terms of debugging?

Especially on iOS yes. Although there’s still no official solution to debug your application on Xcode, TouchLab launched a [plugin](https://github.com/touchlab/xcode-kotlin) that works really well; it was also mentioned on Kotlin Conf 19 that JetBrains will give official support somewhere around this year.


#### Can I use IntelliJ community edition?

Yes, for cross-platform code and Android. You’ll still need Xcode or AppCode to develop the UI for the iOS application.


#### Which dependency did you use to retreive iOS dispatchers ?

I’m using dispatch_async that’s already available on KMP when you set the iOS preset. I’m then using [coroutines](https://github.com/cmota/Android-Makers/blob/master/app/src/iosMain/kotlin/domain/IosMainDispatcher.kt) to deal with them.

#### Is Apple really ok with kotlin moltiplatform? Our app got kicked from their store because of it 2y ago (because it was using reflection).

There are already a couple of apps in production using Kotlin Multiplatform. According to [Andrey Breslav on KotlinConf 2019: Opening Keynote](https://www.youtube.com/watch?v=0xKTM0A8gdI&t=822s) you already have Space, CashApp, Yandex Maps, PlanGrid, Workspace, Planboard, etc. that have parts of their code shared across the Android and iOS app.

I’ve had a couple of problems with them when trying the iOS app for an Android conference, but it was just that — they didn’t like the idea of having an app that said **Android** on the store. I would say that if you don’t use reflection/ something that goes against their guidelines you shouldn’t have any problem with it — go for it! 🙂

#### Does it makes sense to start project with KMP + Flutter for UI and then scale up with Android/iOS native ? Is it easily interoperable KMP + Flutter ?

This is a tricky question. Kotlin Multiplatform was created with the goal of sharing the business logic of an app and not the UI. This should be left to the native layer to implement, typically using Kotlin/ Swift depending on the platform at hand. This is the biggest difference with so many cross-platform solutions that exist nowadays and in which Flutter is part of. Their goal is to *write once, run anywhere*.

My suggestion is to start with KMP and Android/ iOS native, mainly for two reasons:
- It’s easier to have everything up and running.
- If you know Kotlin, there’s a really small learning curve on learning Swift.

Being said, if you’re already familiar with Flutter, it can take you a bit more to set up your project but you can have it work with KMP without major problems. There are already some projects doing it:
- [Accounting Multiplatform](https://github.com/littleGnAl/accounting-multiplatform/tree/littlegnal/blog-kmpp-flutter)

#### Have you considered putting the presenter in the shared code?

Yes, but I wanted to have the concepts the most possible separated. KMP’s goal is to share the business logic and leave the UI itself to be implemented natively. This is why I’ve added it on the Android/ iOS layer.


#### Do you think kotlin multiplaform would be the feature for several platforms? it seems very difficult to get introduced into the javascript atmosphere

Yes, that’s the goal — *to run it anywhere* 🙂. From what I’ve been seeing you can find more documentation and examples on mobile than JS. I would say that it probably is because most of Kotlin developers come from Android and they usually focus more on mobile than the web.

Being said, a couple of people already asked me for a talk that would also focus KMP on the web — so I’ll probably focus on that in the upcoming months. Let’s see 🙂.


#### Did you use the Ktor for http communication or I just missed that?

I did, you can check the slides [here](https://speakerdeck.com/cmota/the-hitchhikers-guide-through-kotlin-multiplatform-android-makers?slide=63) or in more detail on [GitHub](https://github.com/cmota/Android-Makers/blob/master/app/src/commonMain/kotlin/data/SessionizeAPI.kt).


#### How can you slowly switch the iOS code to KMP? Can you just move the whole iOS codebase into a gradle module in a KMP project and migrate one piece at a time?

You can move your existing iOS project to a new KMP structure without any problems (I’ve done it in smaller ones without many headaches). Even if you’re using a dependency manager like CocoaPods, everything should work fine.

After that, my best advice is to start small. Some people started by writing a test layer for some features in KMP, others by converting the network layer, the database, the processing images library, etc. Pick the one that your team feels that needs to be redone 🙂.


#### Do you know if there are plans for common/bridge tooling between targets ? Especially for CI/CD ?

Well, JetBrains announced Spaces on the latest Kotlin Conf which has a build and deliver mechanism with CI/CD support. It seems that it’s still planned for development — so there are plans, let's see when we can play with it. 🙂

For the time being, there’s a nice tutorial written by Ioannis Diamantidis on how he created a: [Continuous Integration for Kotlin Native projects with Gitlab CI](https://diamantidis.github.io/2019/09/08/continuous-integration-for-kotlin-native-projects-with-gitlab-ci).


#### Is it possible to use current Android libraries that are “STILL” in Java? — What about the Kotlin libraries for Android?

You can use on the Android layer any library that you’re currently using on an Android project. On the common module no.


#### Is it still true that in Kotlin/Native the coroutines can execute only on the UI thread? (no multithreading)

Yes. But they’re working on it! So expect a couple of changes in the upcoming months 🙂.

You can follow this discussion [here](https://github.com/kotlin/kotlinx.coroutines/issues/462).
