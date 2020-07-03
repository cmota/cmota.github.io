---
title: "Android: set a default build type/flavor"
date: 2020-04-13 00:00:00 +01:00
tags: [android, tutorial, configuration]
image: "featured.png"
---

<figure>
<img src="/android-set-a-default-build-type-flavor/featured.png" alt="Android: set a default build type/flavor cover">
</figure>

How many of you deal with the (small) struggle of compiling your Android project and when you look at the installed app, you realize that you had the wrong build variant selected? Or, on the opposite side, you weren’t even able to compile it because you’ve got a release build selected on one module and debug on the others?

I do… well, did.

It happens a couple of times, it’s nothing dramatic, but it happens here and then. Typically you can replicate this on a project that has multiple build types/ flavors defined and on the following scenarios:
- cloning a new repo
- cleaning all untracked changes from a repo (removing build, *.iml, etc.)
- switching between branches

The default behavior on Android Studio is to order alphabetically all the possible combinations between flavor and build types and then to select the first on the list.

Let’s see the following example:

```gradle
buildTypes {
  debug {
  }

  ciRelease{
  }
     
  release {
  }
}

productFlavors {
  alpha {
  }
      
  prod {
  }
}
```

We’re going to have all of these combinations:
- alphaCiRelease
- alphaDebug
- alphaRelease
- prodCiRelease
- prodDebug
- prodRelease*

And by default, Android Studio will select *alphaCiRelease*.

**Note:** this logic is valid for all the modules on your app.

Let’s assume that my app has other modules defined and that they only have **debug** and **release** build types defined. If this is the case, the default variable selected will be **debug**. With this, it won’t be possible to compile the project since we’ve got different types defined — in one debug and the other release (**alphaCiRelease**).

### Support for default build type/ flavor

With the releases of Android Studio 3.5 and Gradle plugin 3.5.0 we now have support to manually decide which should be the default combination to be selected.

To do so, it’s fairly easy — so to select **alphaDebug** you only need to add the instruction `getIsDefault().set(true)` on its definition:

```gradle
buildTypes {
  debug {
    getIsDefault().set(true)
  }

  ciRelease{
  }
     
  release {
  }
}

productFlavors {
  alpha {
    getIsDefault().set(true)
  }
     
  prod {
  }
}
```

**Note:** you may notice that it’s a bit strange to use getIsDefault instead of accessing directly to the field isDefault , this happens because it’s declared as final and there’s no setter available.

Source: [https://issuetracker.google.com/issues/36988145](https://issuetracker.google.com/issues/36988145)

### Remarks

It’s important to remember that after you manually select another build variant this new option will take precedence over the one that was set as default.

### Troubleshooting

While testing this implementation I’ve noticed that although everything was correctly defined I was unable to select the desired combination of flavor/ build type.

After more tryouts that I want to admit, I’ve noticed that I didn’t have enabled “Only sync the active variant”:

<figure>
  <img src="https://cdn-images-1.medium.com/max/4092/1*xNrIYQQuzj0rAp9WT5EtRQ.png" alt="Android Studio “Only sync the active variant” setting from Experimental preference">
  <figcaption>Android Studio “Only sync the active variant” setting from Experimental preference</figcaption>
</figure>

In order to enable this preference go to:

* Android Studio → Preferences → Experimental

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
