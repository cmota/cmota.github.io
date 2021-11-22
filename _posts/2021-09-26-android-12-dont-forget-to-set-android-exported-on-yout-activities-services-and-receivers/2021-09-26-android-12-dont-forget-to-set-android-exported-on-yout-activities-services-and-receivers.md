---
title: "Android 12: don’t forget to set android:exported on your activities, services, and receivers"
date: 2021-07-26 00:00:00 +01:00
tags: [android, droidcon, conference]
---

<figure>
<img src="/android-12-dont-forget-to-set-android-exported-on-yout-activities-services-and-receivers/featured.jpeg" alt="Android 12: don’t forget to set android:exported on your activities, services, and receivers cover
">
</figure>


## TL;DR

If you’re targeting Android 12, you need to set `android:exported` on each activity, service, and receiver on your **AndroidManifest.xml** file.

This recently happened to me while updating one of my apps to Android 12 (API 31). As I’d incremented the SDK version and hit build, the process failed with the error:

> Error: Apps targeting Android 12 and higher are required to specify an explicit value for `android:exported` when the corresponding component has an intent filter defined. See https://developer.android.com/guide/topics/manifest/activity-element#exported for details.

**Note:** the building process fails if you’re using Android Studio 2020.3.1 Canary 11 or later.


## Introduction

The `exported` attribute is used to define if an activity, service, or receiver in your app is accessible and can be launched from an external application.


As a practical example, if you try to share a file you’ll see a set of applications available. Clicking on one of them will open the activity responsible to handle that file, which means that it’s declared on the Manifest with `exported:true`.

Activities that declare **intent filters** are enabled by default, which makes sense since they typically will respond to a specific action that can be triggered by other applications.

It’s really important to check each one of your AndroidManifest declarations to see if they really need to be made available externally or not. Otherwise, an unwanted application might be able to interact with your app, accessing activities or services that it isn’t supposed to.

Making it mandatory to define the `exported` value of each activity, service, and receiver adds a new layer of security. Since the developer will need to set it in order to compile the app to Android 12.


### I’ve got every activity, service, and receiver setting the android:exported attribute and I’m still seeing the same error

This also happened to me.

If your using external libraries that add any of these components to your Manifest file and they aren’t prepared for this new rule you’ll need to add them individually.


### How do you know which libraries are adding these components?

Analyze your application merged manifest file for these unknown components. You can easily do that by clicking on the tab **Merged Manifest**:

<figure>
<img src="https://miro.medium.com/max/669/1*eT0nvNesYdHi06LqV6m6mg.png" alt="Generic Android Manifest file with Text/Merged Manifest actions">
<figcaption>Generic Android Manifest file with Text/Merged Manifest actions
</figcaption>
</figure>

And now looking at the merged manifest file, I immediately notice an activity that I don’t recognize:

<figure>
<img src="https://miro.medium.com/max/681/1*9ctkQcayld-D5R8ELzrHGw.png" alt="Generic merged manifest
">
<figcaption>Generic merged manifest
</figcaption>
</figure>

In this case, the library that I was using for backporting the newly added SplashScreen added an activity to the **AndroidManifest.xml**.

The fix is quite simple, I just need to manually add to the Manifest file:

```kotlin
<activity 
    android:name="...test.SplashScreenAppCompatTestActivity"
    android:exported="false"/>
```

And that’s it!


### Troubleshooting

If the merged manifest file is not being displayed, try to reduce the SDK version to API 30 and synchronize your project. Once you’ve identified which activities, services, or receivers were merged, add them to your manifest file and update the API back to version 31.



Do you have a better approach? Something didn’t quite work with you?

Feel free to reply here or send me a message directly on [Twitter](https://twitter.com/cafonsomota) 🙂.
