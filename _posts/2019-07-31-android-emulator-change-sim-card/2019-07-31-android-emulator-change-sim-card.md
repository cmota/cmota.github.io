---
title: "Android emulator: change your SIM card number"
date: 2019-07-31 00:00:00 +01:00
tags: [android, tools, emulator]
image: "featured.png"
---

<figure>
<img src="/android-emulator-change-sim-card/featured.png" alt="Android emulator: change your SIM card number cover">
</figure>

One of the biggest advantages of using the Android Emulator is that we can do almost everything as if we were on a physical device. Additionally, here we have a large set of configurations that allow us to replicate a large variety of configurations, namely: architectures, Android versions, hardware profiles, etc.

## But what if we need to mock a SIM card?

If you create an AVD (Android Virtual Device) and go to the native settings application and look for your SIM information, you should see something identical to this:

<figure>
<img src="https://cdn-images-1.medium.com/max/2000/1*r4NgSQth9LkEkUsyO9kmeQ.png" alt="Device information">
<figcaption>Device information</figcaption>
</figure>

If you look closely to the SIM number it seems to be a default number with *+1* as the country code and a mix of random numbers. However, if we look closely the last four digits seem oddly familiarly.

If you enter on your console adb devices and assuming that you’ve got only one created you’ll see:

```shell
$adb devices
  List of devices attached
  emulator-5554 device
```

So, this last 4 four digits basically correspond to the port in which the emulator is connected… and the first ones… let's say that they are the default number used by Android:

```java
static const char kDefaultPhonePrefix[] = “1555521”;
```

### Device properties

In order to see all of your device (emulator) properties you can enter the following command:

```shell
adb shell getprop
```

And to filter the ones only related to the SIM card we just need to:

```shell
adb shell getprop | grep "gsm"
```

which returns:

```shell
[gsm.current.phone-type]: [1]
[gsm.defaultpdpcontext.active]: [true]
[gsm.network.type]: [LTE]
[gsm.operator.alpha]: [Android]
[gsm.operator.iso-country]: [us]
[gsm.operator.isroaming]: [false]
[gsm.operator.numeric]: [310260]
[gsm.sim.operator.alpha]: []
[gsm.sim.operator.iso-country]: [us]
[gsm.sim.operator.numeric]: [310260]
[gsm.sim.state]: [LOADED]
[gsm.version.baseband]: [1.0.0.0]
[gsm.version.ril-impl]: [android reference-ril 1.0]
```

It seems that there are some older emulators where you can define each one of these properties individually via setprop command:

```shell
adb shell setprop gsm.sim.operator.iso-country pt
```

Unfortunately, this seems to not work on the latest version of the emulator; so if it doesn’t we need to follow a second approach.

### Change your SIM number

After a lot of try and error (a special thanks to here that grabbed his pickax and joined me on this quest), we’ve managed to discover an easy way to update the SIM number on the emulator.

1. First, you need to check if you’ve got the ANDROID_HOME path defined:
    ```shell
echo $ANDROID_HOME
    ```
    It should print your Android SDK path:

    ```shell
/Users/$(whoami)/Library/Android/sdk
    ```

    In case you don’t have your ANDROID_HOME defined you’ll need to set it. First, you need to find where your Android SDK folder is located. You can easily check this by opening Android Studio and after selecting your project right click on “Open Module Settings” and then on the left bar select on “SDK Location”.

    After copying it, open a new console and type:

    ```shell
export ANDROID_HOME=<your_sdk_path>
    ```

    And then add it to the environment variables PATH:

    ```shell
    export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
    ```

    and save it (so you don’t need to enter these commands every time you open a new console):
    ```shell
    source ~/.bash_profile
    ```

2. Next, we will set the EMULATOR variable as well as we did for the ANDROID_HOME :
    ```shell
export EMULATOR=$ANDROID_HOME/emulator
    ```
    and then export the LD_LIBARY_PATH

    ```shell
export LD_LIBRARY_PATH=$EMULATOR/lib64:$EMULATOR/lib64/qt/lib
    ```

3. Now you just need to set the phone_number option with the phone number that you want to use on the emulator:
    ```shell
$EMULATOR//qemu/darwin-x86_64/qemu-system-x86_64 -avd <your_avd_name> -phone-number 351990000001
    ```
    In order to get <your_avd_name> you’ll need to see all the AVD’s available:
    ```shell
emulator -list-avds
    ```
In my case, it will output Pixel_2_API_Q, so my command will be:
    ```shell
$EMULATOR//qemu/darwin-x86_64/qemu-system-x86_64 -avd Pixel_2_API_Q -phone-number 351990000001
    ```
No need to add the “+” or “00” for the country code, Android will do that automatically for you.

<figure>
<img src="https://cdn-images-1.medium.com/max/2000/1*_UIAGMFXpBVblLygBw5GMQ.png" alt="Device properties (new phone number)">
<figcaption>Device properties (new phone number)</figcaption>
</figure>

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
