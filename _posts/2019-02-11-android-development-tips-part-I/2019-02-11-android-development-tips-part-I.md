---
title: "Android Development Tips - Part I"
date: 2019-02-11 00:00:00 +01:00
tags: [android, tips]
image: "featured.png"
---

<figure>
<img src="/android-development-tips-part-I/featured.png" alt="Android Development Tips - Part I cover">
<figcaption>Copyright @ Android, Google</figcaption>
</figure>

Last December I’ve decided to embrace the holiday spirit and create a new challenge — to send daily tips via twitter ([@cafonsomota](http://twitter.com/cafonsomota)) related with Android development. It could go from something code related to some hidden feature on Android Studio. My goal was that it needed to be something worth sharing.

I want to get back at this in a later post — but let me just say that I truly recommend this type of challenges. First, it allows you to go an extra length to find content for your daily publications and secondly, you have the chance to share what you’ve learned with the world (or just the *Twittersphere*, but who’s counting) and meet new people along the way.

I’ve updated them to a more *medium* form and divided them into categories:
* Android
* Android Development
* Android Studio
  
and in part II we’re going to have:
* Kotlin
* Java
* Product Design

## Android

1. Smaller **.apks** means that people will use less network data, your app will install faster and it will use less disk space. Don’t forget to check if your **build.gradle** file has `shrinkResources` and `minifyEnabled` properties enabled:
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3600/1*MwpAdXlaovS6FAFSmpVWQw.png" alt="build.gradle file with shrinkResources and minifyEnabled properties enabled">
    <figcaption>build.gradle file with shrinkResources and minifyEnabled properties enabled</figcaption>
    </figure>

2. IME options (on xml we have android:imeOptions) correspond to the keyboard action button. On text input fields, after entering the text you can define how the action key will behave and look — search, done, etc. and even create your own action for when the user clicks on it, trigger it automatically.
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3668/1*hNgKlaZTLrMM_r-evutTLQ.png" alt="IME Options for “done” with a specific callback to deal with the user click action">
    <figcaption>IME Options for “done” with a specific callback to deal with the user click action</figcaption>
    </figure>

3. You can restrict which data the user is allowed to enter on an input field — by setting the appropriated `inputType` on `TextInput`/`EditText`. This will also work for paste actions where the text will be filtered in case it doesn’t match the defined criteria.
    {: .center}
    <figure>
    <p align="center">
    <img src="https://cdn-images-1.medium.com/max/3680/1*NQwfGHjBzC36ngEZbqm-Ag.png" alt="Java code" style="width:250px;height:450px;">
    <img src="https://cdn-images-1.medium.com/max/3680/1*NFh7CfAx0zI4v5zBwvzkfw.png" alt="Java code" style="width:250px;height:450px;">
    </p>
    <figcaption>Defining the TextInput as a password and as a number</figcaption>
    </figure>

    <figure>
    <img src="https://cdn-images-1.medium.com/max/3500/1*UTdpbJC9zi8mcTOCPEpQtg.png" alt="Kotlin code">
    <figcaption>Code for defining the TextInput as a password and as a number</figcaption>
    </figure>

4. Starting on API 26 we’ve got autofill. One of the features directly out of the box is text prediction. Briefly, the OS knows the data that you keep entering on different apps and when it detects that you’ll need to enter it again — it automatically prompts you.
    You an easily achieve this by adding an hint to your editable view — this can be either a “Phone number”, “E-mail”, etc:
    ```xml
android:hint=”Phone number” 
    ```
    Fortunately, you can also disabled it by defining:
    ```xml
android:importantForAutofill=”no”
    ```

    <figure>
    <p align="center">
    <img src="https://cdn-images-1.medium.com/max/3680/1*AvrzEEAZ_iMFxzyWFaaVxQ.png" alt="Autofill feature" style="width:250px;height:450px;">
    <img src="https://cdn-images-1.medium.com/max/3704/1*KP-zhdNhTQZje6XXG6cZOQ.png" alt="How to disable autofill">
    </p>
    <figcaption>Autofill feature and how to disable it</figcaption>
    </figure>

5. Since we’ve been talking about *TextInput* you can define an error message for *TextInputLayout* that will be shown on the bottom. Really useful to provide some feedback when the data entered is not valid.
    
    <figure>
    <p align="center">
    <img src="https://cdn-images-1.medium.com/max/3680/1*TnDfRvg_HgVVUH7nHqyZlg.png" alt="Error message on TextInputLayout" style="width:250px;height:450px;">
    <img src="https://cdn-images-1.medium.com/max/2960/1*paOU7Em8ckAbbY6DKNQZjg.png" alt="Error message on TextInputLayout code">
    </p>
    <figcaption>Error message on TextInputLayout</figcaption>
    </figure>

6. It’s important to provide feedback on user actions/reflect visually the current state of your app views. Instead of having multiple conditions on your code that will check a set of attributes you can define this on a `selector` and let Android choose the correct one:
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2760/1*Sm3IDxN45YVb0Q9_WpFEZQ.png" alt="Custom selector for a button (with the states disable and pressed defined)">
    <figcaption>Custom selector for a button (with the states disable and pressed defined)</figcaption>
    </figure>


7. Themes is something that we don’t read much about — but Android has several good options. For instance, you can easily switch between light and dark (night) mode by just defining a theme on *values-night* and calling:
    ```java
AppCompatDelegate.setDefaultNightMode(MODE_NIGHT_YES)
    ```

    <figure>
    <p align="center">
    <img src="https://cdn-images-1.medium.com/max/3804/1*XJMdmrINj6zJK0S2sjaeAQ.png" alt="Night mode on Android">
    <img src="https://cdn-images-1.medium.com/max/3668/1*pPsRdhdVKMGFp_SJAiJHaw.png" alt="Night mode on Android">
    </p>
    <figcaption>Night mode on Android</figcaption>
    </figure>

8. If your Android app has a direct action to make CS calls — instead of using the intent `ACTION_CALL` use `ACTION_DIAL`. Doing so, you don’t need to ask for `CALL_PHONE` permission which apart from being one less permission required it’s also one less action for the user to take.
9. I’m seeing several apps where the notification icon is a solid grey. This happens on non-transparent images because the system ignores all non-alpha channels — meaning, the system will merge all colours into one. To show a specific shape you need to use transparency.  
    For instance, if you’re using push notifications with Firebase, you might need to add an additional meta-data to your `AndroidManifest` file to use a specific icon (that should have transparency) otherwise it will use the default — your app launcher icon.
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2960/1*xvEfR9e1wbsyVYWvi-Srng.png" alt="Android Manifest with a specific icon defined for Firebase messages">
    <figcaption>Android Manifest with a specific icon defined for Firebase messages</figcaption>
    </figure>

10. If your minimum SDK is from the dark ages you’ve got some helper tools that you can use:  
    -`ContextCompat.*`  
    -`ActivityCompat.*`  
    They’re available at: [com.android.support:support-compat](https://t.co/HJ6kKBTFWA?amp=1).
11. There’s a really good [video](https://www.youtube.com/watch?v=Hzs6OBcvNQE&feature=youtu.be) by [Colt McAnlis](undefined) on why we should use *IntDef* and/or *StringDef* on Android Development instead of of *enums*. Nevertheless, while browsing some recent libs I still find a lot of enums (or even hardcoded values).
    Depending on the complexity of your project, it can be easy to update it. You need to define the type of your constants:  
    -`StringDef`  
    -`IntDef`  
    -`LongDef`  
    -etc.  
    and create an interface that’s going to define them.  
    And then just hit **cmd + shift + r**
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3028/1*8qg7jCZ0AwbQGqmmuFxh8w.png" alt="Example of a StringDef and IntDef for different types of data">
    <figcaption>Example of a StringDef and IntDef for different types of data</figcaption>
    </figure>

## Android Development

1. I use Android Studio both as IDE and Git client. Although it’s fairly easy to search for a specific branch you can end up with a lot of local merged branches that are just cluttering your environment. You can easily remove them but entering this cmd:
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2824/1*k_TYlh3hELm3KdqXpnkuOA.png" alt="Search for a specific branch command">
    <figcaption>Search for a specific branch command</figcaption>
    </figure>

2. If you want to install a debug **.apk** that’s not signed — don’t forget to the add the flag `-t` otherwise you’re going to have:
    ```shell
Failure [INSTALL_FAILED_TEST_ONLY: installPackageLI]
    ```
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2388/1*v4pT9T1LXD_PW2r2WTtipA.png" alt="Install a debug .apk via adb">
    <figcaption>Install a debug .apk via adb</figcaption>
    </figure>

3. A few months ago I’ve written a blog post about useful *adb* commands for Android Development: [Adb command-ments](https://medium.com/code-procedure-and-rants/adb-command-ments-d7dc40634643)
    One that I particularly like is
    ```shell
$ adb shell input text “Hello world!”
    ```

    It’s easier to write on the pc and you don’t risk having hardcoded values that can easily be forgotten.

## Android Studio

1. There’s something that’s pretty useful to organize your code especially when a class gets a bit to big — code regions! Just enclose the methods between
    ```java
    //region: NAME
    ```
    and
    ```java
    //endregion 
    ```
    and you’ll see a minimize/expand option near the line numbers

    <figure>
    <img src="https://cdn-images-1.medium.com/max/2960/1*LxgFVpuckajegO5GWTNm5Q.png" alt="Code regions">
    <figcaption>Code regions</figcaption>
    </figure>

2. Do you start to have too many Android projects? Work, side projects, playground… Android Studio allows you to organise all of them into groups — and yes, you can even add an icon to identify them better.
    How?
    1. Right button click on projects
    2. New Project Group And that’s it!
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3556/1*CLLO-mLFyw-veriJvgAeFg.png" alt="Organisation of different Android projects">
    <figcaption>Organisation of different Android projects</figcaption>
    </figure>

3. I’ve talked about *adb* tips previously and how they can fasten your development. There’s also a really good plugin made by [@pbreault](https://twitter.com/pbreault) that you can use directly in Android Studio — it’s really worth checking it out:
[pbreault/adb-idea](https://github.com/pbreault/adb-idea)
4. Who has clicked on *Run android* and just a few seconds later realised that something was missing and then furiously tried to stop the building process? 🙋‍♂️
    You have two easier ways (that don’t involve force quite Android Studio):
    — you can go directly to Android Studio terminal and type:
    ```shell
    ./gradlew -stop
    ```
    — you can stop all the daemons on Android Studio by:
    1. Pressing **cmd + shift + A** (or **control + shift + A on Windows/ Linux**)
    2. Enter **Show Gradle Daemons**
    3. Select **Stop All**  
    *(A special thanks to [César Díez Sánchez](https://twitter.com/cesards_) for this last suggestion)*
5. So… who randomly pressed some strange key combination on Android Studio and now has multiline selection active?
    You can opt between both by:  
    - Mac: **cmd + shift + 8**  
    - Windows/Linux: **shift + alt + insert**  

I’ve called this part I… so, this means there’s another one already in the works? 🤔🙌
