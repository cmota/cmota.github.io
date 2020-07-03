---
title: "A cautionary tale on Android: do not call System.exit()"
date: 2020-03-23 00:00:00 +01:00
tags: [android, cautionary tale]
image: "featured.png"
---

<figure>
<img src="/a-cautionary-tale-on-android-do-not-call-system-exit/featured.png" alt="A cautionary tale on Android: do not call System.exit() cover">
</figure>


Once upon a time… there was an issue that was hidden in plain sight! It lived freely for years without no one noticing it. Until one day, during a connectivity problem, he appeared leaving us speechless.

Well, this is not going to be a fairytale but instead a cautionary tale. My goal with this article (and probably a couple more that are gaining some dust on drafts) is to create awareness on some scenarios that might arise during Android development — in this first one, why you should never call `System.exit(n)` or in Kotlin `exitProcess(n)`.

### Use case

Although the use case where I’ve found the issue is a bit complex to exemplify, I’ve created a simple project to easily show what was happening.

Imagine a simple application with just two activities:
- MainActivity 
- SecondActivity

They extend from **AppCompatActivity** and don’t have any special attribute declared on the **AndroidManifest** (no launchMode, nothing)

```xml
<activity android:name=".MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN"/>
      <category android:name="android.intent.category.LAUNCHER"/>
  </intent-filter>
</activity>

<activity android:name=".SecondActivity"/>
```

The layout is also really simple on both — it has only one button with a defined user action.

On **MainActivity** it starts **SecondActivity**

```kotlin
btn_launch.setOnClickListener {
  startActivity(Intent(this, SecondActivity::class.*java*))
}
```

And in SecondActivity the button, calls exitProcess(2) (Kotlin’s equivalent to Java’sSystem.exit()) in order to terminate the app.

```kotlin
btn_action.setOnClickListener {
  exitProcess(2) //same as System.exit(2)
}
```

Now let’s follow this use case: the user opens the app, clicks on the first button, the second activity is displayed and now she clicks on btn_action defined above. What will happen to the application?

- It will terminate and the user gets back to the home screen?

**No.**

### Diving in
```
System.exit()
Terminates the currently running Java Virtual Machine. The argument serves as a status code; 
by convention, a nonzero status code indicates abnormal termination.

- in developer.android.com
``` 

Or in other words, we can say that the system will terminate the application running process and along with it all the associated data — the current activity, app data, etc.

If we look at the device logs we’ll see exactly this:

```shell
I/ActivityManager: Start proc 28995:com.sample/u0a11 for activity {com.sample/**com.sample.MainActivity**}
I/d.sample: System.exit called, VM exiting with result code 2
I/ActivityManager: Process com.sample (pid 28995) has died: fore TOP 
W/ActivityTaskManager: Force removing ActivityRecord{ com.sample/.SecondActivity t4191}: app died, no saved state
I/ActivityManager: Start proc 29043:com.sample/u0a11 for activity {com.sample/com.sample.MainActivity}
```

**Note:** I’ve cleaned the logs to filter and highlight the important information.

What’s not explicitly said on the documentation is that if there’s any activity from the application paused — or in other words, that wasn’t finished — it will be kept on the back stack (activity stack) and will be resumed once there’s no more activities on top of it.

To make things easier, I’ve made a simple diagram where we can visualize the entire process:

![](https://cdn-images-1.medium.com/max/3244/1*V2-EuhT_d4Kw7h-tqqkanA.png)

When the application calls System.exit() the system will indeed kill the current process and remove the activity from the back stack (in this case SecondActivity) and it will also resume the activity that it’s bellow on the stack — which in this case will be from the same application since there’s no call to finish() on our code.

With this, the application will restart.

### Solution

The solution here is simple. If you want to exit an activity you should just call finish(). Of course, there are use cases where you have more activities on the back stack, for that you should call instead finishAffinity()which will remove all the activities that share the same affinity — which if you haven’t defined one it’s the same for all.

With this our updated diagram will be:

![](https://cdn-images-1.medium.com/max/3228/1*hf1sQ2q2YTMXfW1DsXf4HQ.png)

### **Notes**

I’ve read that some people were expecting that calling System.exit() would be the equivalent to force stop the app, but as we could see it is not true.

If the user decides to force stop the app all the activities on the stack that belong to this application are also removed (and with this no restart will take place).

```
Have the system perform a force stop of everything associated with the given application 
package. All processes that share its uid will be killed, all services it has running 
stopped, all activities removed, etc.

- in ActivityManager.java
```

It’s important to remember that this is a system feature and, fortunately, there’s no System.forceStop() method that one could directly call.