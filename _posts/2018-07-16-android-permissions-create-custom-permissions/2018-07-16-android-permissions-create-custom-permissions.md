---
title: "Android: Create custom permissions"
date: 2018-07-16 00:00:00 +01:00
tags: [Android, Tutorial, Permissions]
image: ""
---

Do you still remember the old days when you installed an application from the Google Play Store without knowing what it was going to do in the background… what it could have access to?

Sure, it seems totally legit that an application that only has the feature of showing exchange rates can access your contacts or one that scans QR codes and uses the camera also needs to access your location, calls and phone state [sarcasm].

Thankfully, on Android 6.0 we had a major upgrade here and started to have runtime permissions — which allowed us, the users, to allow or block specific accesses. With all that’s happening on privacy and now with Europe’s GDPR I try to have an extra attention with unnecessary permissions — most of the times it’s possible to understand what’s under the hood and identify which functionalities outside the application sandbox are needed. I usually only grant access to two or three permissions groups that seem to be critical.

Security is a big buzz nowadays (as it should always be), so if you’re an app developer, don’t forget to double check the permissions you’re requesting to see if they’re really needed — I’ve seen some cases where they were initially added because of a feature that no longer existed, but the permission was forgotten there. Every time that app was installed it asked for that permission doing nothing with it, if granted.

Moreover, sometimes permissions might not be totally clear for what they’re going to be used — for instance, I had a game that nowhere it mentioned that it was country based; however, it didn’t allow me to pass the initial screen unless I’d grant access to my current location — and no information was given on why it was needed.

This is more of a UX talk now: in order to solve my previous point, we can show a screen before asking for permissions to explain why they are needed — but sometimes this can be a bit overwhelming for the users, it’s another screen that pops-up and requires you to interact in order to complete an action that you’ve already requested before. Android has a really nice fallback mechanism here, that I suggest:

It’s possible to know if a permission was denied previously and display that screen only on this scenario:
```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.PERMISSION_X) == 
  PackageManager.PERMISSION_GRANTED) {
  //Permission already granted! 
  return
}

if (ActivityCompat.shouldShowRequestPermissionRationale(
  this, 
  Manifest.permission.PERMISSION_X)) {
  //Display a screen saying why you need PERMISSION_X
  …
} else {
  ActivityCompat.requestPermissions(this, 
    arrayOf(Manifest.permission.PERMISSION_X),
    REQUEST_CODE)
}
```

I’ve already mentioned that Android asks the user if an application can have access to features that exist outside of its sandbox. A detailed list with all the dangerous permissions and its groups can be find in the official [documentation](https://developer.android.com/guide/topics/permissions/overview#permission-groups). An important note here, that’s also on the link, is that you should request for a specific permission instead of a group — there might be scenarios where allowing a category won’t guarantee that your app will have access to the entire state. Moreover, this behaviour might change on future Android releases.

## Well, but I’ve talked about custom permissions right? At least, it’s what’s written on the title.

This is sometimes forgotten, but your app can (and should?) set specific permissions — especially if it interacts with third party apps. First and foremost, there’s always the possibility of having malware apps that try to impersonate trusted ones and if no security mechanism is in place you can give your data to the wrong app.

Let’s see an example:

You’ve got two different applications developed by the same company:
- a to-do list
- a calendar

Additionally, this two apps can persist their data remotely and if they are both installed on the device they can communicate with each other and provide a new set of features:
1. If one of the apps is authenticated, the second can automatically query the first one to get this information and pre-fill all the necessary login information.
2. You want to be reminded about a note that you’ve written — probably something to do later on that day — you can do that easily by communicating with the calendar application and add that as an event, scheduled for a specific date and time.

These two features represent a possible interaction of two different applications. When we think about the type of data shared, we can see that the first one deals with user specific information — meaning that probably an email, profile picture or even an authentication token/ (hopefully) encrypted password might be shared during this communication. If another (unwanted) application intercepts this request it may have access to user sensitive data; the second feature holds some text, probably some abbreviations known to the owner that don’t require any extra level of security (although it’s never too much to add).

The communication here can be made via *intents*. However, what guarantees that an unwanted application won’t mock their actions and create an intent which will impersonate the official app and receive all the user data?

Nothing.

In order to add a layer of security, we can define custom permissions that only applications that have it set can receive this data.

```xml
<permission
  android:name="com.companyX.permission.custom"
  android:description="[@string/custom_permission_descrip](http://twitter.com/string/custom_permission_descrip)tion"
  android:icon="R.drawable.ic_custom_permission"
  android:label="[@string/custom_permission_label](http://twitter.com/string/custom_permission_label)"
  android:protectionLevel="signature"/>
```

What all these attributes mean?

**android:name**  
Corresponds to the permission name that needs to be declared and added in the Manifest file as a permission that your application will use:
```xml
<uses-permission android:name="com.companyX.permission.custom"/>
```

**Note** that you can’t have two applications declaring a permission with the same name unless they’re both signed with the same certificate. To avoid naming collisions, it’s a good practice to add the package name (which should be unique) as prefix of your permission name.

**android:description, icon and label**  
Are used by the system to provide additional information about the permission being requested:
- Icon and description are shown when the app is asking for this permission and should provide context to what’s going to be granted access.
- Icon and label are shown when the user navigates to the *native settings* → *app* → *permissions* to see all the permissions that are being used by that app. It corresponds to a short description that has the goal of reminding the user the purpose of that permission.

**protectionLevel**  
These represent the level of security used for this permission:
- **normal**: It’s a low risk permission, automatically granted by the system.
- **dangerous**: Typically used when there’s private user data involved — it needs to be explicitly requested and granted.
- **signature**: It’s granted automatically by the system if both applications are signed with the same certificate.
- **signatureOrSystem**: It’s mainly only used on manufacturers embedded applications that are typically pre-installed on system folders.

Since, in the above example, both applications have been developed by the same company, this means that they most certainly will share the same application *signature* (are signed with the same *.keystore*); so we can take this into advantage and define a custom permission with this increased level of security — *signature*. There are two major advantages of this:

1. There’s no need to prompt the user asking for a different type of permission in order to communicate with another application. Since they share the same signature, the OS automatically grants this access; it’s expected that since the company behind them is the same they’re trustworthy.
2. Since this is addressed by the OS, there’s no need to implement a validation mechanism.

## What if although they’re published by the same company, they have different certificates?

In this case, and using the example given, you’ve got two options here:

1. In case you’re dealing with sensitive data — like the automatic authentication mentioned above — you should set your protection level as *dangerous*. You should have extra care here, when defining the permission label and description — since you’ll need to explain that your app will give access to private data to another application without scaring the user to denying it directly.
2. For more general data, you can use *normal*.

## Known security issues

CommonsWare have written a few years back about a vulnerability when talking about custom permissions [here](https://commonsware.com/blog/2014/02/12/vulnerabilities-custom-permissions.html). When two applications share the same package name or content provider, Android doesn’t allow you to install the second one — warning that you already have one installed; this is a security feature that is particularly useful to spot malware. Unfortunately, the same does not happen for duplicated custom permissions on versions below 5.0 (meaning API 21). If two applications have the same declaration the one that was installed first takes precedence over the other one. This raises several red flags since it means that although you have a protection level set as signature if the first one (malware) has it declared as normal — the OS will automatically grant this permission to every application that has the permission declared:
```xml
<uses-permission android:name="com.companyX.permission.custom"/>
```
Since, we’re talking about the Android ecosystem, there’s still ~15% of Android devices using a version below API 21; so if your app targets an API under Lollipop you’ll need to implement a whitelist mechanism capable to distinguish supported applications from unwanted ones. A solution can be found on Common’s Ware GitHub [page](https://github.com/commonsguy/cwac-security) and the latest updates on this issue [here](https://github.com/commonsguy/cwac-security/blob/master/PERMS.md).

## Which components can we set a custom permission

Although the example mentioned is related with intents you can define a custom permission for almost all components that can be accessed externally:
- Activities
- Services
- Content Providers
- Broadcast Receivers

Example of permission on a content provider:

```xml
<receiver
  android:name="com.companyX.receivers.ReceiverA"
  android:permission="com.companyX.permission.custom">
  <intent-filter android:priority="999">
    <action android:name="com.companyX.action.ACTION_A"/>
  </intent-filter>
</receiver>
```

If no permission is defined or an invalid is for `Activities`, `Services` and `Content Providers` Android will throw a `SecurityException` while on `Broadcast Receivers` the intent won’t be delivered.
