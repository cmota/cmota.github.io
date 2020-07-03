---
title: "Android Security: Double Check Your ADB Connection"
date: 2019-03-04 00:00:00 +01:00
tags: [android, security]
image: "featured.png"
---

<figure>
<img src="/android-security-double-check-your-adb-connection/featured.png" alt="Android Security: Double Check Your ADB Connection cover">
<figcaption>Copyright @ Google</figcaption>
</figure>


We’ve got a saying in Portuguese that it can roughly be translated to:
> The one who warns you, is your friend!

That is commonly translated to English:
> Forewarned is forearmed.

I’ve added the expression first in Portuguese, to create an impact that I believe that gets a bit lost in translation. This article has two main goals:

1. To make you give a double check on your device debug mode configurations
2. To keep in mind that, unfortunately, it sometimes easy for someone to access your device private data

### Some information before

ADB stands for Android Device Bridge. It’s a tool that allows you to communicate directly with your Android device giving you somewhat administration powers to the point that you can:

* Install and/ or uninstall applications
* Send and retrieve files to/ from your device
* Take screen shots
* etc.

without rooting your device.

You can connect to your device via USB cable or, if you open your device port 5555, remotely.

### ADB: Miner

Last year, I read this article by [Kevin Beaumont](https://doublepulsar.com/root-bridge-how-thousands-of-internet-connected-android-devices-now-have-no-security-and-are-b46a68cb0f20) and at that time I didn’t give it the attention that I should have. I remember that I’ve tested my device to check if it was vulnerable and shared the article with some friends, but since no one complained I’d never given a second thought about it until recently.

Briefly, Kevin talks about an exploit found on a large number of Android devices (not only smartphones but everything that runs the OS) that had the port 5555 open and with this anyone that knows that device external IP address could connect to it and basically do anything he wants since there’s no additional security mechanism here.

This was initially identified on a worm that was continually scanning for TCP port 5555 and when it founded one open, it installed a third party application just by calling a command as simple as:
```shell
adb install [apk]
```

This application would then use the device resources to mine cryptocurrencies. This is done without the user even noticing it and unless he continuously checks the applications that he has installed, this malware app could keep running potentially for a long time.

This attack will use the devices resources — you probably will notice that you’ll need to charge it more often or it’s becoming slower. In case it’s an Android TV, since it’s always connected, you might only notice the last one.

### ADB Permissions

Android 6.0 introduced the concept of runtime permissions. In other words, in order for an application to access a functionality outside of its sandbox the user needs to explicitly give it permission. This will only give access to that group — so if you’ve just asked for the camera permission and need to access the location; you need to request it and the user needs to accept.

Although, this gives a lot more power to the user to select which applications can access to what functionalities, potentially giving him an extra layer of security; if it’s installed via ADB and not the Google Play Store, by using the correct flag all these permissions can be automatically granted:
```shell
adb install -g [apk]
```
### ADB: Data privacy

In Kevin’s article the application will end up consuming the device resources; but as we could see above, due to the large set of functionalities of ADB this can drastically become worse. This is a list of commands that I’ve published a while back: [Adb command-ments](https://medium.com/code-procedure-and-rants/adb-command-ments-d7dc40634643)

To be honest, this is what triggered me to write this article. Some weeks ago I was discussing this vulnerability and it was when I truly realised the impact that it can have on privacy.

For instance, it’s fairly easy to take a screenshot of your screen:
```shell
adb shell screencap [device/path/filename.png]
```
or even to record what you’re doing to a file:
```shell
adb shell screenrecord [device/path/filename.mp4]
```
This, along with the possibility to download these files locally can potentially show to an unwanted person what you’re doing with your device:

* Photos taken
* Messages exchanged
* etc.

We use our devices for almost everything, if someone decides to record your screen sooner or later it will have some of your personal information at hand.

### Double check your ADB connection

Android has a strong level of security that gets continuously improved — both on the Google Play Store and on the Operating System itself. With each release, your application gets tightened when it needs to ask for additional permissions.

Nevertheless, the same does not happen on ADB — because, and to be honest here, we’re talking about more of a debugging mechanism instead of a vulnerability in the OS. Although, one can argue that it’s hidden in plain sight — you still need to know how to enable it; and even here to have the same level of access that’s described in this article you’ll need to open port 5555 to allow for remote access (and in some routers, you’ll even need to open this port to accept external requests).

I bet you’ve already asked this a couple of times: how do you know if your device is vulnerable?

The easy way is to try to connect directly to it remotely:
```shell
adb connect IP
```
**Note:** you can get the IP by just opening your browser and search for “what’s my public IP”.

If you see a message saying that your connected to that IP it means that your device has:

* Debug options enabled
* Port 5555 open

Which means it can be potentially at risk.

### How can I disable it?

The most direct way is to go directly to:

* Native Settings → System → Developer Options

and disable this option.

**Note:** depending on the Android device and Operating System this option might be on a different location — alternative, if your native settings has a search option, look for “debug”.
 
To keep the debug mode option enable, but to disable the remote access just enter the following command:
```shell
adb usb
```
And to enable it again just enter:
```shell
adb tcpip 5555
```
### Why I’ve written this?

Awareness.

We often neglect data privacy — I see a lot of applications in the Google Play Store that ask for permissions that don’t seem necessary to that application feature set — and if one can argue that it can be for something really specific or something forgotten during development; truth to be said, almost every week there are new stories about data that gets released unwillingly.

ADB is usually used on devices that belong to developers and testers, ideally in a closed environment — meaning that if someone is on a different network it won’t be able to connect to it. The bigger problem here is that there are devices that are being shipped with these options enabled by default. When you buy a new device, it’s not something that you’ll check — there’s a trust associated with that vender that should guarantee this basic security. Moreover, we need to have in mind that a smartphone is for all ages and professions — so if we’re more aware of this vulnerability there are too many people that they aren’t.

This is more of a cautionary tale, on the past weeks I’ve been testing this with my friends and family and around hundreds of devices, there was just one, a STB (Set-top box) that had this vulnerability.

Nevertheless, it’s one more than it should be. Don’t forget to do the same tests — we never know what we might find.


Do you have a better approach? Something didn't quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
