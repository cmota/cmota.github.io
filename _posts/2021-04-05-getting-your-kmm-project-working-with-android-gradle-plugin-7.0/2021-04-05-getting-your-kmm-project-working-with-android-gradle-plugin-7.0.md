---
title: "Getting your KMM project working with Android Gradle Plugin 7.0+"
date: 2021-03-29 00:00:00 +01:00
tags: [android, tutorial, gradle, kmp, kmm]
image: "featured.jpeg"
---

<figure>
<img src="/getting-your-kmm-project-working-with-android-gradle-plugin-7.0/featured.jpeg" alt="Getting your KMM project working with Android Gradle Plugin 7.0+ cover">
</figure>

# Getting your KMM project working with Android Gradle Plugin 7.0+

Getting… your KMM project working with AGP 7.0+ (Android Gradle Plugin) cover image

I’m really enthusiastic about the future of (native) mobile development. In the past years, we’ve been watching a paradigm switch on how we should develop our screens. We no longer need to do them on XML or XIBs, and can finally embrace the potential of declarative UIs. On the Android side, we’ve got Jetpack Compose, and on iOS SwiftUI.

Although still in beta, if you want to take advantage of the latest versions of Jetpack Compose, you’ll need to install the canary version of Android Studio (Canary build), currently, we are with Android Studio Arctic Fox - 2020.3.1 Canary 12.

However, these changes require that you update your Android project to the latest version of AGP (Android Gradle Plugin) — at the time of this writing I’m with **gradle:7.0.0-alpha12**

Of course, all major updates require some other changes along the way. So, let’s enumerate all the problems that I had and how I’ve solved them.

**Note:** the commands written here were tested on a Mac with Big Sur. If you’ve you’re using a different Operating System, they may differ.

## Select the right version of Java

Not all the versions are currently supported, so if you have an error similar to this one:
```shell
> Task :common-dto:linkDebugFrameworkIosArm64 FAILED
e: Compilation failed: /Users/carlosmota/.konan/kotlin-native-prebuilt-macos-1.4.31/konan/nativelib/6538169044806663713/libllvmstubs.dylib: dlopen(/Users/carlosmota/.konan/kotlin-native-prebuilt-macos-1.4.31/konan/nativelib/6538169044806663713/libllvmstubs.dylib, 1): no suitable image found. Did find:
```

It’s time to move to **AdoptOpenJDK 11.0.10**.

### Why 11.0.10?

I’ve tried a couple of versions and this one seemed to be the most recent supported by Gradle, if you try to run for instance OpenJDK14 you’ll get a couple of errors while trying to compile your project.

### How to install?

The easiest way is to install it via brew:

```shell
brew tap AdoptOpenJDK/openjdk
brew install --cask adoptopenjdk11
```

**Note:** You can find all the information on their GitHub [repository](https://github.com/AdoptOpenJDK/homebrew-openjdk).

### What if you have different Java versions installed?

It’s possible to have more than one version installed, but only set one as default. Run:

```shell
java -version
```

And confirm that the version that is printed is the one you’ve just installed. Otherwise, you might have compilation errors while trying to build your KMM project.

To see all the Java versions that you’ve got installed, run:

```shell
/usr/libexec/java_home -V
```

In my case, this is my output:

```shell
Matching Java Virtual Machines (3):
16 (x86_64) “Oracle Corporation” — “Java SE 16” /Library/Java/JavaVirtualMachines/jdk-16.jdk/Contents/Home
11.0.10 (x86_64) “AdoptOpenJDK” — “AdoptOpenJDK 11” /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
1.8.281.09 (x86_64) “Oracle Corporation” — “Java” /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
```

Since I’m looking for **AdoptOpenJDK** **11.0.10**, I need to update my **JAVA_HOME** path, for that, I can call directly:

```shell
export JAVA_HOME=`/usr/libexec/java_home -v 11.0.10`
```

Now, if I run the command java -version once again I’ll get the new path:

```shell
openjdk version “11.0.10” 2021–01–19
OpenJDK Runtime Environment AdoptOpenJDK (build 11.0.10+9)
OpenJDK 64-Bit Server VM AdoptOpenJDK (build 11.0.10+9, mixed mode)
```

**Note:** using export is just setting the JAVA_HOME for the current run.

## Configuration with name ‘testApi’ not found.

If you’re working on a KMM (Kotlin Multiplatform Mobile) project, when updating to AGP7.0.0 you’ll probably get an error similar to this one:
```shell
Configuration with name 'testApi' not found.
    at org.gradle.api.internal.artifacts.configurations.DefaultConfigurationContainer.createNotFoundException(DefaultConfigurationContainer.java:165)
    at org.gradle.api.internal.DefaultNamedDomainObjectCollection.getByName(DefaultNamedDomainObjectCollection.java:333)
    at org.gradle.api.internal.artifacts.configurations.DefaultConfiguratio§nContainer.getByName(DefaultConfigurationContainer.java:155)
```

This seems to be an issue with Gradle itself and it’s already reported on [KT-43944](https://youtrack.jetbrains.com/issue/KT-43944). Fortunately, the fix is really simple and there are no side effects (at least that we know of). On you **build.gradle.kts** add:

```shell
android {
    configurations {
        create("androidTestApi")
        create("androidTestDebugApi")
        create("androidTestReleaseApi")
        create("testApi")
        create("testDebugApi")
        create("testReleaseApi")
    }
}
```

**Note:** you need to add this configuration before the kotlin {} block. A special thanks to Martin Bonnin for adding the solution to the issue.

## MissingXcodeException

Another that you might find during this update is that although you’ve got Xcode installed and have been using it without any problem in your KMM (Kotlin Multiplatform Mobile) project, suddenly when you decide to make the update you start having:

```shell
MissingXcodeException: An error occurred during an xcrun execution. Make sure that Xcode and its command line tools are properly installed. at org.jetbrains.kotlin.konan.target.CurrentXcode
```

Yes. No idea, how and why this starts to happen, but fortunately the fix is quite direct. Just open Xcode and go to:

```shell
Preferences → Locations → Command Line Tools
```

Per default it seems that there’s no option selected, just pick one.

<figure>
    <img src="https://cdn-images-1.medium.com/max/3768/1*18kHJvENE1oE6yWz0RJMIw.png" alt="Select one of Command Line Tools options">
    <figcaption>Select one of Command Line Tools options</figcaption>
</figure>


Do you have a better approach? Something didn’t quite work with you?

Feel free to reply here or send me a message directly on [Twitter](https://twitter.com/cafonsomota) 🙂.
