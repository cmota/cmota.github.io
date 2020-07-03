---
title: "Android emulator: when there’s no connection to the internet"
date: 2019-09-03 00:00:00 +01:00
tags: [android, tools, emulator]
image: "featured.png"
---

<figure>
<img src="/using-the-android-tools-directly-from-terminal/featured.png" alt="Android emulator: when there’s no connection to the internet cover">
</figure>

This happens from time to time on the Android emulator that lives on my computer — I launch an instance to work on and it just decides that it doesn’t want to connect to the internet. This is usually followed by the typical turn off and on the WiFi on the emulator, then on the computer and then either wipe all the data and relaunch or just creating a new instance to see if it would magically work.

It doesn’t.

The problem here seems to be related to the DNS picked by the Android emulator, which it cannot resolve and therefore connect to the internet.

## Solution

Since the problem is related to the DNS used by the emulator (by default, it uses the same as your machine) the approach that I’ve taken is just to launch the emulator configured to connect to Google’s public DNS’ — 8.8.8.8.

```shell
emulator @<your_emulator_name> -dns-server 8.8.8.8
```

### How to find your emulator name?

You can see all the emulators that you’ve created by entering the following command on the terminal/shell:

```shell
emulator -list-avds
```

### How to use the emulator command?

Depending on the operating system that you’re using the emulator is under:
- On Mac `.../Library/Android/sdk/tools`
- On Windows `...AppData\Local\Android\Sdk\tools`

You can find these locations by using:

1. On Android Studio by right + click on your project
2. “Open Module Settings”
3. Look on the sidebar for “SDK Location” and select this tab
4. You should now have a couple of directories there, namely “Android SDK location” which is the one that we’re looking for

Now, let’s add it to your environment path:


##### On Mac  

```shell
$ cd ~
$ open .bash_profile
```

And add the Android SDK locations copied from Android Studio:

```shell
export PATH=$PATH:/Users/{whois}/Library/Android/sdk/platform-tools
export PATH=$PATH:/Users/{whois}/Library/Android/sdk/tools
export ANDROID_HOME=/Users/{whois}/Library/Android/sdk
```

Close and execute the commands just added
```shell
$ source .bash_profile
```

Just to test that everything is working as expected try to enter this command:
```shell
$ adb version
```
  

##### On Windows

You’ll need to add the ANDROID_HOME to the “System variables”. You’ll need to:

1. Go to System → Advanced system settings
2. Select the “Advanced” tab
3. Click on “Environment Variables…”
4. And under “System variables” add

```shell
ANDROID_HOME 
C:\Users\{whois}\AppData\Local\Android\Sdk\

ANDROID_PLATFORM_TOOLS 
C:\Users\{whois}\AppData\Local\Android\Sdk\platform_tools

ANDROID_TOOLS 
C:\Users\{whois}\AppData\Local\Android\Sdk\tools
```