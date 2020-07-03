---
title: "Android: remove a specific build variant from the active list"
date: 2020-05-18 00:00:00 +01:00
tags: [android, tutorial, configuration]
image: "featured.png"
---

<figure>
<img src="/android-remove-a-specific-build-variant-from-the-active-list/featured.png" alt="Android: remove a specific build variant from the active list cover">
</figure>

As projects start to grow and new layers of specifications and configuration are added we might start to use flavors and its dimensions in order to support a higher level of customization.

Nonetheless, every time we add a new flavor, the number of possibilities to compile the app automatically duplicates.

### Flavors

> noun: **flavor**
> 1. the distinctive taste of a food or drink.
> 2. an indication of the essential character of something.

We can see flavors the same way: they are set of rules and behaviors that make your app behave differently depending on if you’re compiling it using the code written for **flavorA** or the one for **flavorB**.

A simple example can be the use case where an application is available on the Google Play Store with free and paid versions. If the user buys the paid version we want her to have access to all of its features which typically won’t be available on the free app. Moreover, we don’t want to ship that code on the free version. Otherwise, we increase the risk of the user finding a way to access those features without having paid for them. This can be done either by exploring vulnerabilities in the app, or it can even by a bug from the developer. So it’s better to play safe.

When we start a new project we typically have a similar configuration:
```gradle
android {
  buildTypes {
    debug {
      ...
    }

    release {
      ...
    }
  }
}
```

With this, we can either compile our app as **debug** or **release**.

Now, let's add a specific flavor:

```gradle
flavorDimensions "app"
    
productFlavors {
  free {
    dimension "app"
  }
}
```

Looking at the build variants list, we can compile the same number of versions but they’re now called **freeDebug** and **freeRelease**.

If we now add a second flavor we will duplicate the number of possibilities:

```gradle
flavorDimensions "app"
    
productFlavors {
  free {
    dimension "app"
  }
    
  paid {
    dimension "app"
  }
}
```

We now have **freeDebug**, **freeRelease**, **paidDebug** and **paidRelease**.

To the initial list of build types, we can add a third and fourth one increasing the number of possibilities here:

```gradle
buildTypes {
  debug {
    ...
  }
        
  dev {
    ...
  }

  staging {
    ...
  }

  release {
    ...
  }
}
```

And with this, we start to have a never-ending list of possibilities: **freeDebug**, **freeDev**, **freeRelease**, **freeStaging**, **paidDebug**, **paidDev**, **paidRelease** and **paidStaging**.

And the list can go on and on as we keep adding new functionalities.

### Ignore

As we keep adding new flavors and build types Gradle will automatically add those new combinations to the list. Nonetheless, not all possibilities might make sense — for instance, we might not need a **freeStaging** version.

So, how can we remove it from the list? We just need, to ignore them.

We can ignore an entire build type:

```gradle
variantFilter { variant ->
  def names = variant.flavors*.name

  if (variant.buildType.name == "staging") {
    setIgnore(true)
  }
}
```

Or just a specific combination:

```gradle
variantFilter { variant ->
  def names = variant.flavors*.name

  if (variant.buildType.name == "staging") {
    if (names.contains("free") {
      setIgnore(true)
    }
  }
}
```

### More on build variants

I’ve written another article on build variants, this time om how to define one as default on Android Studio:
- [Android: set a default build type/flavor](https://medium.com/@cafonsomota/android-set-a-default-build-type-flavor-a1cdcd1c15fa)

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.