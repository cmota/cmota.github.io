---
title: "Using the Android tools directly from Terminal (Mac OS)"
date: 2020-02-18 00:00:00 +01:00
tags: [android, tools, configuration]
image: "featured.png"
---

<figure>
<img src="/using-the-android-tools-directly-from-terminal/featured.png" alt="Using the Android tools directly from Terminal (Mac OS) cover">
</figure>

I’ve recently noticed that using android tools like the adb or the emulator directly from the terminal weren’t as common as I thought it would be.

It can be a bit tricky at first to configure but aftward you’ll see that it’s really handy for development.
1. Launch the Terminal app
2. Open the bash_profile
    ```shell
nano ~/.bash_profile
    ```
3. Add your Android SDK path:
    ```shell
export ANDROID_SDK_ROOT=/Users/${whois}/Library/Android/sdk
export PATH=$ANDROID_SDK_ROOT/emulator:$ANDROID_SDK_ROOT/tools:$PATH
    ```
4. Press ctrl+x followed by y and enter to save your changes.

That’s it, you’re all set! 🙌


Oh, and you don’t need to launch the Terminal app you can use the one that’s already built-in Android Studio:

<figure>
  <img src="https://cdn-images-1.medium.com/max/5592/1*GhccIfkq4FW1JPgSKJ0GCw.png" alt="Terminal inside Android Studio">
  <figcaption>Terminal inside Android Studio</figcaption>
</figure>


Now feel free to try out some of the most common adb commands:
- [Adb command-ments](https://medium.com/code-procedure-and-rants/adb-command-ments-d7dc40634643)

Or using the emulator:

```shell
emulator -list-avds
emulator @<your_emulator_name> -dns-server 8.8.8.8
```