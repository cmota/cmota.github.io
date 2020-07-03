---
title: "Android Emulator Receive an SMS"
date: 2019-04-17 00:00:00 +01:00
tags: [android, emulator, tutorial]
image: "featured.png"
---

<figure>
<img src="/android-emulator-receive-an-sms/featured.png" alt="Android Emulator Receive an SMS cover">
<figcaption>Copyright @ Google</figcaption>
</figure>

A few weeks ago Google released Android Q Beta 2 and, of course, like almost everyone Android developer out there I wanted to try it out. Namely, I wanted to test a cool new feature that they’ve been announcing called [Bubbles](https://developer.android.com/preview/features/bubbles).

Initially I would prefer to do this on a development device in order to see the overall OS, but since I’m needing all that I have for testing a new application I decided to try it out on the emulator.

The Android emulator has been seriously improved throughout the years. Its incredible fast nowadays, and supports almost all the features outside of the box. For instance, you can even see a camera preview of a room without having to configure anything.

## Receiving an SMS

You can emulate receiving an SMS easily. ̶F̶o̶r̶ ̶t̶h̶i̶s̶ ̶y̶o̶u̶ ̶o̶n̶l̶y̶ ̶n̶e̶e̶d̶ ̶t̶o̶ ̶c̶h̶e̶c̶k̶ ̶i̶f̶ ̶y̶o̶u̶’̶v̶e̶ ̶g̶o̶t̶ ̶t̶e̶l̶n̶e̶t̶ ̶i̶n̶s̶t̶a̶l̶l̶e̶d̶ ̶a̶n̶d̶ ̶i̶f̶ ̶y̶o̶u̶ ̶d̶o̶ ̶s̶e̶t̶ ̶t̶h̶e̶ ̶n̶u̶m̶b̶e̶r̶ ̶o̶f̶ ̶t̶h̶e̶ ̶s̶e̶n̶d̶e̶r̶ ̶a̶n̶d̶ ̶t̶h̶e̶ ̶m̶e̶s̶s̶a̶g̶e̶ ̶c̶o̶n̶t̶e̶n̶t̶.̶

**Edit:** after writing this post I’ve discovered that there is even an easier way. You can do this directly from the ADB:
```shell
adb emu sms send [from] [message]
```
Which can be something like:
```shell
adb emu sms send “+351910000001” “Hello all”
```
And that’s it!

Nevertheless, if by any reason you want to go the long way around, and well… since I’ve already written it— here goes the version using *telnet*.

### Install *telnet*

If your on a mac you can easily install it via:
```shell
brew install telnet
```
In Windows you need to:

1. Press **Windows key + R** to open the **Run** dialog
2. Write **optionalfeatures** and press **Enter** key
3. Search for the **Telnet Client** and enable it by selecting the checkbox near the folder name

To confirm that it’s correctly installed just type *telnet* on the command line.

### Sending an SMS through <i>telnet</i>

With *telnet* installed, we’re now going to send a message.

1. Launch your Android emulator
    No special configurations are needed here, just use the one that you’ve already created or the best one that fits your requirements.
2. Open your command line and check if the device you want to send a message it’s online — you can do this via:
    ```shell
adb devices
    ```
    You should see something like:
    ```shell
List of devices       attached
emulator-5558         device
    ```
    The number after *emulator*, that you can see on the device list, — *5558* — represents the port where the emulator is connected.
3. Let’s connect to that emulator via *telnet*
    ```shell
    telnet localhost 5558
    ```

    You should see something similar to:
    ```shell
    telnet localhost 5558
    Connected to localhost.
    Android Console: Authentication required
    Android Console: type ‘auth <auth_token>’ to authenticate
    Android Console: you can find your <auth_token> in
    ‘/Users/carlosmota/.emulator_console_auth_token’
    OK
    ```
    This means that you first need to authenticate your session in order to send commands. The output message already says everything, just open the file <i>emulator_console_auth_token</i> and copy the token.

4. Authenticate your console token
    ```shell
auth token_from_emulator_console_auth_token
    ```
5. Send an SMS by defining the number of the sender and the message content
    ```shell
sms send [from] [message]
    ```
    Which can be something like:
    ```shell
sms send “+351910000001” “Hello all”
    ```
<figure>
<img src="https://cdn-images-1.medium.com/max/2000/1*dTFi1FUJdqW_HDVe3iSZyA.png" alt="Receiving an SMS on the Android Emulator (with Android Q Beta 2) cover">
<figcaption>Receiving an SMS on the Android Emulator (with Android Q Beta 2)</figcaption>
</figure>

…and about the Bubbles?

Well, you still need to activate them manually through the adb:
```shell
    adb shell settings put secure experiment_enable_bubbles 1 
    adb shell settings put secure experiment_autobubble_all 1
```

### More information

You can find all the commands supported here: [Send Emulator console commands from Android Developers](https://developer.android.com/studio/run/emulator-console)


Do you have a better approach? Something didn't quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.