---
title: "Getting… your BottomSheetScaffold working on Jetpack Compose Beta 03"
date: 2021-03-29 00:00:00 +01:00
tags: [android, tutorial, compose]
image: "featured.jpeg"
---

<figure>
<img src="/getting-your-bottomsheetscaffold-working-on-jetpack-compose-beta-03/featured.jpeg" alt="Getting… your BottomSheetScaffold working on Jetpack Compose Beta 03 cover">
</figure>

# Getting… your BottomSheetScaffold working on Jetpack Compose Beta 03

Getting… your BottomSheetScaffold working on Jetpack Compose Beta 03 cover image

It’s Monday, no releases this week, and… there’s a new version of Jetpack Compose — beta 03—available. What a perfect time to just increment 02 to 03 and see what’s new.

The API is (almost) final so after updating from alpha to beta there weren’t any big changes to do. However, and remember that’s still in development, there’s always something that I need to update. Sometimes the behavior changes, other times I wasn't doing things right, like this one.

## BottomSheetScaffold

The `BottomSheetScaffold` allows you to show a bottom sheet in your app quite easily. You just need to define

```kotlin
val bottomSheetScaffoldState = rememberBottomSheetScaffoldState(
    bottomSheetState = rememberBottomSheetState(
        initialValue = BottomSheetValue.Collapsed
    )
)
    
BottomSheetScaffold(
    sheetContent = {
        // The content you want to show in your bottom sheet
    },
    scaffoldState = bottomSheetScaffoldState) {
        // The content you want to show in your screen*
    }
)
```
And when you want to show it just call:
```kotlin
bottomSheetScaffoldState.bottomSheetState.expand()
```

or to hide it:
```kotlin
bottomSheetScaffoldState.bottomSheetState.collapse()
```

### Updating to beta01–02

When **beta 01** was launched you could no longer call:

```kotlin
bottomSheetScaffoldState.bottomSheetState.expand()
```

directly without being in a coroutine. Otherwise, the project won’t even compile and you would see the error:
> Suspend function ‘collapse’ should be called only from a coroutine or another suspend function

Also, along with this release, you no longer had the possibility to set a callback in the expand function that would be executed after the animation has ended.

At the time I noticed that the solution for this was quite easy, I just needed to call the expand() or collapse() inside of a coroutine, and after looking online the approach that most people were following was:

```kotlin
GlobalScope.launch {
    bottomSheetScaffoldState.bottomSheetState.collapse()
}
```

After making this change everything was working fine and I didn’t think twice about this solution… well, until beta 03.

### Updating to beta03

Now after updating to **beta 03**, calling expand() or collapse() within GlobalScope.launch my application started to crash with the error:
> A MonotonicFrameClock is not available in this CoroutineContext. Callers should supply an appropriate MonotonicFrameClock using withContext.

This happens because `GlobalScope` doesn’t have a `CoroutineContext` associated with it, which is needed for animating the **BottomSheet**. Alternatively, we should use the CoroutineScope:

```kotlin
val coroutineScope = rememberCoroutineScope()

val bottomSheetScaffoldState = rememberBottomSheetScaffoldState(
    bottomSheetState = rememberBottomSheetState(
    initialValue = BottomSheetValue.Collapsed)
)
    
BottomSheetScaffold(
    sheetContent = {
        // The content you want to show in your bottom sheet*
    },
    scaffoldState = bottomSheetScaffoldState) {
        // The content you want to show in your screen*
    }
)
```

And then when you want to `expand()` or `collapse()` the **BottomSheet**, call:

```kotlin
coroutineScope.launch {
    bottomSheetScaffoldState.bottomSheetState.collapse()
}
```

Moreover, using a GlobalScope usually, it’s not the best approach how there:

* The scope of GlobalScope corresponds to the app lifecycle.
* It has no associated Job it’s not possible to cancel this coroutine.
* Building tests for code that’s using GlobalScope is much trickier.

And well, the official [documentation](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) advise you not to use it:
> Application code usually should use an application-defined [CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html). Using [async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) or [launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) on the instance of [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) is highly discouraged.


### Example

If you are looking for an example of a **BottomSheetScaffold** implemented, feel free to take a look at my submission for the #AndroidDevChallenge week #4:

- [https://github.com/cmota/wwweather](https://github.com/cmota/wwweather)

<figure>
    <img src="https://cdn-images-1.medium.com/max/2400/1*3y2F_Eu0sn_LRvNIZrNMqg.png" alt="wwweather application">
    <figcaption>wwweather application</figcaption>
</figure>



Do you have a better approach? Something didn’t quite work with you?

Feel free to reply here or send me a message directly on [Twitter](https://twitter.com/cafonsomota) 🙂.