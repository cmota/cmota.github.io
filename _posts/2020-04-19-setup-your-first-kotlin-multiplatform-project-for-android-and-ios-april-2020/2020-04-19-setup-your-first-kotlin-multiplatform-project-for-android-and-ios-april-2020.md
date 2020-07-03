---
title: "Set up your first Kotlin Multiplatform project for Android and iOS: April 2020"
date: 2020-04-19 00:00:00 +01:00
tags: [kotlin, multiplatform, tutorial]
image: "featured.png"
---

<figure>
<img src="/setup-your-first-kotlin-multiplatform-project-for-android-and-ios-april-2020/featured.png" alt="Set up your first Kotlin Multiplatform project for Android and iOS: April 2020 cover">
<figcaption>Photo by Mollie Sivaram on Unsplash. Adapted for my talk The Hitchhikers Guide Through Kotlin Multiplatform</figcaption>
</figure>

Most of the time, when we’re testing a new technology the most difficult part is having everything up and running. With this in mind and after talking with some people that were having some problems to compile a sample project with Kotlin Multiplatform on Android and iOS I’ve decided to write this article:
- [Set up your first Kotlin Multiplatform project for Android and iOS](https://medium.com/@cafonsomota/set-up-your-first-kotlin-multiplatform-project-for-android-and-ios-e54c2b6574e7).

A lot has improved since last year, and with it the number of additional steps to run the sample that comes with IntelliJ IDEA. Nonetheless, the process is not quite direct, so here it goes the updated version of:

## Set up your Kotlin Multiplatform project

I’ve updated all my tools, so I’m currently using:

**Operating System**  
macOS Catalina Version 10.15.3  

**IntelliJ IDEA**  
[IntelliJ IDEA 2020.1 (Ultimate Edition)](https://www.jetbrains.com/idea/download/#section=mac)

**Xcode**  
Version 11.3.1  

**Note:** you can do almost everything on IntelliJ IDEA for Android, there’s no need to have another IDE using up resources.

### How to start?

1. Open IntelliJ IDEA and select "Create New Project"  
2. Select "Mobile Android/iOS | Gradle" and hit "Next"
    <figure>
      <img src="https://cdn-images-1.medium.com/max/5036/1*6xbDi8ykN8ZEi6QgGwNUvA.png" alt="Create a new android/ iOS project on IntelliJ">
      <figcaption>Create a new android/ iOS project on IntelliJ</figcaption>
    </figure>
3. Select the Gradle JVM (if it’s not defined). I usually select the last version
    <figure>
      <img src="https://cdn-images-1.medium.com/max/5044/1*SyMK223uNACltlj4zCNVEQ.png" alt="Select the Gradle JVM version for your project">
      <figcaption>Select the Gradle JVM version for your project</figcaption>
    </figure>
4. Write "Project name" and select "Project location"
5. Click on "Finish"
6. The project will start to sync and fetch its dependencies.

Most certainly this will fail with a similar error:

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*g73N4P9oFvwAZWUUwhV9XQ.png" alt="No sdk.dir is defined on a newly created project. You’ll need to do this manually.">
  <figcaption>No sdk.dir is defined on a newly created project. You’ll need to do this manually.</figcaption>
</figure>

This can easily be fixed by opening the file local.properties and update the path for the sdk.dir .
```shell
sdk.dir=/Users/$(whoami)/Library/Android/sdk
```

7. In order to re-start the synchronization, go to the Gradle side tab and click on the refresh icon.
8. The project should now sync and afterward, you should see a couple of ticks ✔️on the tasks.


### Add Android configuration

Now that we have the project ready, let’s add the Android configuration so we can compile and run the sample our Android device/ emulator:

1. Click on "Add Configuration" on the top toolbar
2. On the left panel select "Android App"
3. Click on "Create configuration"
4. Add a name to your configuration and select the module "app"
5. Click on "Apply" and then "OK"

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*xrIj4NW7htr5s5GcGLK4vQ.png" alt="Create an Android configuration in order to compile and run the project">
  <figcaption>Create an Android configuration in order to compile and run the project</figcaption>
</figure>

Now let’s click on the green arrow on the left of your configuration and in a few seconds the project will be compiled and the app installed on your Android device/ emulator.

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*Z4ejPP1Y70_QkNJzfKBdJw.png" alt="Sample app running on the Android emulator">
  <figcaption>Sample app running on the Android emulator</figcaption>
</figure>

**Note:** if you look at the left panel, you’ll see the build folder with all the generated files, like it usually happens on Android development.

### Add iOS configuration

Now it’s to compile the app for iOS! Before we start let’s start by checking the configurations of our IntelliJ IDEA, go to:

1. IntelliJ IDEA → Properties
2. Build, Execution, Deployment → Build Tools → Gradle

And confirm that Gradle is set as default both on "Build and run using" and "Run tests using".

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*GIQKAgiSiW20clJJQdeZqg.png" alt="IntelliJ IDEA Gradle Preferences">
  <figcaption>IntelliJ IDEA Gradle Preferences</figcaption>
</figure>

Now that these compilation flags are defined and before opening Xcode let’s start by building our project on IntelliJ IDEA, this time instead of clicking on the green arrow click on the green hammer.

There’s now a **bin/ios** folder inside **build**, this is where the framework (that is where the app framework is — don’t worry, with the latest changes on the platform this process is now automatic).

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*kEHgtCGvIg-qY4gLg3pkBA.png" alt="iOS framework generated">
  <figcaption>iOS framework generated</figcaption>
</figure>

It’s time to open Xcode! In order to open the xcodeproj go to the root of your project and select **iosApp/iosApp.xcodeproj**

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*vbr1hFqwq_M3GpGb5sCoAw.png" alt="Open the sample app Xcode project">
  <figcaption>Open the sample app Xcode project</figcaption>
</figure>

We can see all the iOS components on the left panel as we’re used to. Now let’s click on run to compile and install the application.

We’ll automatically see an error saying that gradlew does not exist.

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*DRgMj6OtGsGTZFcPv6ZaPQ.png" alt="No gradlew file or directory error">
  <figcaption>No gradlew file or directory error</figcaption>
</figure>

This happens because gradlew is still not added to the newly created project, we need to do this manually. To do this go, go to the terminal (you can do this directly from IntelliJ by going to the Terminal tab on the bottom bar) and write:

```shell
gradle wrapper
```

Now that everything is in place, let’s get back to Xcode and click on run once again to see our application running on the iOS simulator

<figure>
  <img src="https://cdn-images-1.medium.com/max/5760/1*9zx0tltDt9_dXlZZ47mcWQ.png" alt="Sample app running on the iOS simulator">
  <figcaption>Sample app running on the iOS simulator</figcaption>
</figure>

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.