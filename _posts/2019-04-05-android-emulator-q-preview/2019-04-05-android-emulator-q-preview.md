---
title: "Android emulator: Q Preview"
date: 2019-04-05 00:00:00 +01:00
tags: [android, emulator, tutorial]
image: "featured.png"
---

<figure>
<img src="/android-emulator-q-preview/featured.jpeg" alt="Android emulator: Q Preview cover">
<figcaption>Copyright @ Google</figcaption>
</figure>

A new preview of Android Q has been [released](https://android-developers.googleblog.com/2019/04/android-q-beta-2-update.html)!

And with another major release, there are usually changes that you’ll need to make on your application in order to get it ready for the new devices that are going to support it. Google already has this information available and now with the new emulator, you can start planning your [changes](https://developer.android.com/preview):

It’s worth to mention that probably the biggest changes to be made seem to be related to:

* Privacy (scoped storage, launch of activities from the background 🙌)
* Behaviour (restriction on non-SDK interfaces, support for foldables)

And you read it right — foldables! For the past months we’ve been hearing a lot of buzz around these new phones and it’s finally time to try one — even if it’s on the emulator.

There’s always quite a few challenges in adapting an application to a new set of behaviours that change an application lifecycle — so if you start testing it as soon as you’ve got some time you’ll end up saving some unwanted stress when they’re out there on the market and you need to make all of these adaptations.

As an off topic note, I still remember the changes I needed to do for an application to support multi-window and how manufacturers always like to make their *small* challenges in order to create a new layer of… let’s say challenges.

## Download Q Preview on Android Studio

Although, it seems to be already available on Android Studio 3.3.2 (stable channel) — when you go to the AVD manager and create a new virtual device with the Q preview, you’ll be prompted with an error dialog saying that you’re not be able to download it:
> Package “Android Emulator” with revision at least 28.1.9 not available

<figure>
<img src="https://cdn-images-1.medium.com/max/3442/1*Yf22s8HDZ3UzH7Al7h7zFg.png" alt="Android Studio — Unable to download Q release">
<figcaption>Android Studio — Unable to download Q release</figcaption>
</figure>

This happens because Android Q is still in beta and therefore it’s only be available through the Canary and Development channels. This makes sense, since it’s on the development stage and we can still have changes between versions. The emulator images seem to follow the same reasoning and with this, for now, you need to download it through these channels.

With this in mind, we’ve got some workarounds on how to install it.

### Install Android Studio 3.5

The easiest way here is to download and install Android Studio 3.5. Don’t worry, you can have them both installed at the same time. Moreover, it’s easier to distinguish them both, since the Canary’s icon is yellow and the Stable version green.

You can download this version from the official [channel](https://developer.android.com/studio/preview).

<b>Note:</b> this means that you might end up using two IDE’s and the installer itself is 1GB. So if, you’re low on disk space I recommend the approach below.

### Change your SDK Manager channel

You can change your SDK Manager channel to instead of downloading the packages from stable to get them from canary, for instance.

You can easily do this via:
```shell
sdkmanager -update -channel=3
```

Where `channel` can be:

* 0-stable
* 1-beta
* 2-developer
* 3-canary

You can find the SDK Manager on the command line tools that should be on:
```shell
…/Android/sdk/tools/bin/sdkmanager
```

### Configure your Android Studio to update data from other channel

For this you can either go to:

> *Android Studio → Preferences*

and here go to:

> *Appearance & Behaviour → System Settings → Updates*

And change the “Automatically check updates for” to “Canary”

Or you can alternatively go to:

> *Android Studio → Check for Updates… → Configure*

And select “Canary”

After this, you can go directly to:

> *Android Studio → Preferences*

and here go to:

> *Appearance & Behaviour → System Settings → Android SDK*

And you can see the option to update the <b>Android Emulator</b> to version 29.0.0 on <b>SDK Tools.</b>

<figure>
<img src="https://cdn-images-1.medium.com/max/2020/1*i_d-hTK3FSCg2P86PN0-sw.png" alt="Update the Android Emulator via SDK Tools">
<figcaption>Update the Android Emulator via SDK Tools</figcaption>
</figure>


<b>Note:</b> don’t forget to revert it back to the stable channel and to only update the emulator. Otherwise, you may end up updating your Android Studio to the latest release.

<figure>
	<p float="left">
		<img src="https://cdn-images-1.medium.com/max/2660/1*_kiRW6DRFrvvL5YnngIFEg.png" alt="Android emulator: Q Preview" style="width:175px;height:350px;">
		<img src="https://cdn-images-1.medium.com/max/2660/1*BlJqQ6Kdv7KVttgdVhCy0w.png" alt="Android emulator: Q Preview" style="width:175px;height:350px;">
		<img src="https://cdn-images-1.medium.com/max/3648/1*MES5Gc2N3ZljlHSj8tLjjg.png" alt="Android Studio installing Android Q Preview" style="width:400px;height:300px;">
	</p>
	<figcaption>Android Q Preview</figcaption>
</figure>

## What about the foldable emulator?

For this, you need to access it through Android Studio 3.5. Even, if you try to download the foldable emulator and open it on the stable version (AS 3.3.2) it will be displayed as broken and the only option available is to repair it.

Your best option here (as the time of this article) is to download Android Studio 3.5 and use it for testing on the foldable emulator.

Have fun! 🚀


Do you have a better approach? Something didn't quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
