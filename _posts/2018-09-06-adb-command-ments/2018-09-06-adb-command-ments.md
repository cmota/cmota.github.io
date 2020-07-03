---
title: ADB command-ments
date: 2018-09-06 00:00:00 +01:00
tags: [Android, adb]
image: "featured.png"
---

<figure>
<img src="/adb-command-ments/featured.png" alt="adb command-ments cover">
<figcaption>Copyright @ The Hacker News</figcaption>
</figure>

# Adb command-ments

If you’re an Android developer, it’s quite common that from time to time you end up needing one of these commands — well, no need to search on Google or open that favorite tab on Stack Overflow; I’m trying to sum up the most used here.

**Notes:**

- These commands do not require your device to be rooted
- You should replace [package] with your application package name
- When two or more options are shown between brackets — you should select only one (for example, `[install | uninstall]`)


### In/uninstall a version on your device

```shell
adb [install|uninstall] [flag] [apk]
```

Which on a real example, it would be something like:

```shell
adb install com.your.new.project.apk
```

If your app asks for runtime permissions and you’re running on Android 6.0+ you can add the flag *-g* to automatically grant access to all permissions:

```shell
adb install -g com.your.new.project.apk
```

### Transfer files between your computer and your device

```shell
adb [push|pull] [source] [destination]
```

If you want to send a file to your phone — you just need to type:

```shell
adb push /path/to/filename /storage/emulated/0/
```
Where */storage/emulated/0* corresponds to your Android shared storage — it can be any directory you want, it’s only required that you have write permissions so the file can be stored there.

### Retrieve an .apk from your device

If you’re unsure about your package name — you can either:

1. Browse through the entire list of applications installed on your device:
```shell
adb shell pm list packages
```
And after finding it — run the following command to know its path:
```shell
adb shell pm path [package]
```
2. Or if you have any idea that it might contain a specific *word* on its declaration, you can search for it:
```shell
adb shell pm list packages | grep ‘word’
```
And in order to get its path you just need to add the flag *-f* and type instead:
```shell
adb shell pm list packages -f | grep ‘word’
```
There are a couple more flags that might be useful to filter the number of results of this command:

- *-f* see the associated file
- *-d* only show disabled packages
- *-e* only show enabled packages
- *-s* only show system packages
- *-3* only show third party packages

After getting the path you can retrieve the .*apk *using the command *pull*:
```shell
adb pull [source] [destination]
```
That can roughly be translated to something like:
```shell
adb pull /data/app/com.your.app.apk your/destination/path
```

### Clear your application data

To clear all the user data and cache for a specific application:
```shell
adb shell pm clear [package]
```

### Send text to your device

Trying to make a test that envolves typing a lot of characters and symbols?

Well, you can just send them to your device from your computer via:

```shell
adb shell input text “Hello world!”
```

### Permissions

```shell
adb shell dumpsys package [package]
```

From this command you can also check which permissions your app requires and which one are granted or not.

With this you can simple call:
```shell
adb shell pm [grant|revoke] [package] [permission]
```
For instance, from running *dumpsys,* I see that my application has access to the device camera:
```shell
User 0: 
 gids=[3002, 3003]
 runtime permissions:
 android.permission.CAMERA: granted=true
```
To remove it, just need to call:
```shell
adb shell pm revoke com.your.app android.permission.CAMERA
```
**Note:** depending if your application requested the permissions individually or the permission group itself, you might need to remove each one separately.

In other words if your app requested the permission *android.permission-group.CONTACTS* it contains:

- android.permission.READ_CONTACTS
- android.permission.WRITE_CONTACTS
- android.permission.GET_ACCOUNTS

So in order to remove it you need to explicitly call:
```shell
adb shell pm revoke com.your.app android.permission.READ_CONTACTS
adb shell pm revoke com.your.app android.permission.WRITE_CONTACTS
adb shell pm revoke com.your.app android.permission.GET_ACCOUNTS
```
Or just remove all the permissions granted to your app:
```shell
adb shell pm reset-permissions [package]
```
You can find more information about permission groups and the permissions they hold [here](https://developer.android.com/guide/topics/permissions/overview).

### Send broadcasts

You can interact with different applications by sending different commands; for instance to make a call automatically you can:
```shell
adb shell am start -a android.intent.action.CALL -d tel:[number]
```
Which will be translated to sending an intent to *[number]* with the action *CALL*:
```shell
  Starting: Intent { act=android.intent.action.CALL dat=tel:xxxxxxxx }
```
If you change the action to *DIAL*:
```shell
adb shell am start -a android.intent.action.DIAL -d tel:[number]
```
It will be translated to:
```shell
Starting: Intent { act=android.intent.action.DIAL dat=tel:xxxxxxxx }
```
Which in turn will open the default phone app dialler pre-filled with the number you entered.

You can always send a custom action:
```shell
adb shell am broadcast -a [“action”]
```

### Key events

You can send a touch event to specific coordinates on the screen:
```shell
adb shell input tap [x coordinate] [y coordinate]
```
Or send a specific key event:
```shell
adb shell input keyevent 3   // Home button
```
In this case to press the *HOME* button. You can also alternatively send an intent:
```shell
adb shell am start -W -c android.intent.category.HOME -a android.intent.action.MAIN
```
Some more examples:
```shell
adb shell input keyevent 4   // Back button
adb shell input keyevent 5   // Open defaul call app
adb shell input keyevent 6   // End call
adb shell input keyevent 26  // Turn the screen on/ off
adb shell input keyevent 64  // Open browser
adb shell input keyevent 207 // Open contacts
```

**Note:** You can find the full list of keys available [here](https://developer.android.com/reference/android/view/KeyEvent.html).

### Logcat

You can continuously monitor your device logs via:
```shell
adb logcat
```
However this will show you information about every application that’s currently running and printing logs — so it might be better to filter it by priority — lets assume only warnings and errors:
```shell
adb logcat *:W
```

*W* corresponds to the log level warning and upwards — you can select one of the following (order from the less priority to the most):

- *`*:V`* (verbose) — will show you all logs
- *`*:D`* (debug)
- *`*:I`* (info)
- *`*:W`* (warning)
- *`*:E`* (error)
- *`*:F`* (fatal)
- *`*:S`* (silent)

**Note:** The option you select from this list will show you all the logs from that priority to silent (`*:S`).

And you can also filter by your application package name:
```shell
adb logcat — pid=$(adb shell pidof -s [package])
```
Or by the TAG value used on `Log.d(TAG, “Text”):
```shell
adb logcat -s TAG 
```

To keep it easier to distinguish the different types of priorities on your logs you can use the format modifier *-v* with *color*:
```shell
adb logcat -v color
```
### En/disable device services

There are a set of commands that can be used to change the state of a native setting or just to have more information about something particularly, for example:

To change the bluetooth status:
```shell
adb shell am start -a android.bluetooth.adapter.action.REQUEST_ENABLE
```
Or *REQUEST_DISABLED* in case it’s on.

To access more information about your WiFi network:
```shell
adb shell am start -n com.android.settings/.wifi.WifiStatusTest
```

**Notes:**
Depending on the Android version on your device:
1. These commands might show a dialog informing you that a third-party app (you) is trying to change the value of a setting — you have to either accept it or deny it.
2. Some of this services might have a different name

### Capture and record screen

You can easily take a screenshot from your device by calling:
```shell
adb shell screencap [device/path/filename.png]
```

Or record your screen via:
```shell
adb shell screenrecord [device/path/filename.mp4]
```

To end the video you just need to:
- enter [ctrl] + [c]
- wait 3 minutes to reach the default value
- or define a *— time-limit* value

**Note:** screen record is only available on Android 4.4+.

After this you just need to pull the file from your device to your computer:
```shell
adb pull [device/path/filename.png] [computer/path]
```

### Multi devices connected

To have the list of connected devices:
```shell
adb devices
```

To install your application on a specific device:
```shell
adb -s [device id] install [apk]
```

Where you get the *device id* from the listing command.

And to install it on all your devices:

```shell
adb devices | tail -n +2 | cut -sf 1 | xargs -IX adb -s X install -r [apk]
```

Hopefully, it will output as many *Success* results as the number of connected devices.

Or if you have a physical device and an emulator you can just use the flags:
- *-d* (for device)
- *-e* (for emulator)

To distinguish where the *.apk* should be installed.

For instance, on the emulator:
```shell
adb -e install [apk]
```

To send a command to a specific device:
```shell
adb -s [device id] shell [command]
```
For example, to open the default call application on the device with id — *emulator-5554*:
```shell
adb -s emulator-5554 shell input keyevent 5
````

## Plugins

Most of these functionalities are already available on the latest version of Android Studio and are quite easy to use. For the ones that are missing, there are a couple of plugins that we can add to provide these additional functionalities:

* [ADB Idea](https://github.com/pbreault/adb-idea) (by Philippe Breault)
Provides some additional features like (uninstall, kill, start, restart and clear data)
* [ADB WiFi Connect](https://github.com/appdictive/ADBWiFiConnect) (by Appdictive)
Allows you to send adb instructions to your device without being required to have it connected via USB

## More on this

* [Android Developers](https://developer.android.com/studio/command-line/adb)
* [ADB Commands Gist](https://gist.github.com/Pulimet/5013acf2cd5b28e55036c82c91bd56d8)

If you use other commands that you feel that are useful, please — don’t hesitate to share. You can add them in the comment section and I’ll keep this post updated.
