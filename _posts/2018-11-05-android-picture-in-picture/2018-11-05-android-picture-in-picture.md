---
title: "Android: Picture in Picture"
date: 2018-11-05 00:00:00 +01:00
tags: [android, picture in picture, tutorial]
image: "featured.jpeg"
---

<figure>
<img src="/android-picture-in-picture/featured.jpeg" alt="Android: Picture in Picture">
<figcaption>Copyright @ 9to5Google</figcaption>
</figure>

Finally.

I believe most of us can identify with one of these scenarios:

* You’re happily watching a video on Netflix when, out of nowhere, you receive a notification from one of the dozens messaging apps you ‘re currently in. It’s a friend of yours that shared a link from something he’d found interesting. You’ve seen that, but you have no clue on what’s that link — and you get curious… and then you decide to click on the notification, to open the application so you can (finally) click on the link.

What happens? 
Your video stops, you move to a completely different application just to see it, reply and get back. How much time did we spend on this? And why?
* Or you’re travelling to some place with the help of your Maps application and receive a call on your car’s Bluetooth system and answer it. Now, out of nowhere, there’s no information on when is the next turn, both audio streams get mixed, so you need to stop the car, grab your device and go to recents, swipe right and open your application again.
* Or you might even be listening to a song on YouTube and decide to open the browser to search for the lyrics to sing along and suddenly the music stops.

Finally, Android released on Oreo (8.0) a solution to solve all these points (and more). We now [1] have native support to easily implement picture-in-picture (PiP)! Visually, when you send your application to background it resizes to a small window that sticks on top of other applications and allows you to keep playing that video while, at the same time, you can be doing other things.

[1] well, I know it has been a year since it was first introduced; but I still miss this feature on some applications that I use daily. If you’re looking for reasons on why you should implement it — in case this is a valid use case for your product — It’s worth to mention that, at the time of this article the percentage of devices with Android 8.0 is around 21%, which on *over 2 billion monthly active Android devices* represents more than 420 million devices that can run this feature.

When we think about resized activities and overlays some of us might start shivering thinking about past issues that from time to time return on a specific device to haunt us; but another cool thing about PiP is that the framework deals with almost all the use cases — making it really simple to implement… so, let’s start!

### How to put everything together

We first need to add the required configuration to the Android Manifest:
```xml
<activity
  android:name=”.ActivityThatWillEnterOnPipMode”
  android:resizeableActivity=”true|false”
  android:supportsPictureInPicture=”true”
  android:configChanges=”screenSize|smallestScreenSize|screenLayout|orientation”/>
```

Basically, the two attributes required to be set are:  
-`supportsPictureInPicture`  
-`configChanges` 

I’ve also added `resizeableActivity` with its two possible states, because although the official documentation states that it should be set to true, it works without any problem if it set to false. This is particularly important if your application doesn’t support split mode — enabling this attribute will allow the user to change the view dimensions, drag items across different activities, etc., which might lead to unwanted behaviours in case you haven’t got your application prepared for this.

### Activity lifecycle

The activity lifecycle for PiP is a bit different than we are used to — when you press the home button it will call: `onPause()` but not `onStop()` this means that depending on your feature and implementation **you might need to move the code you have in** `onPause()` **to** `onStop()`. This will be needed for the scenarios where you’ll need to update the PiP screen.

See the following example of a movie player:
```java
@Override
public void onPause() {
  unsubscribeMovieEvents(this)
  super.onPause()
}

private fun onMovieEnded(List<Movie> movies) {
  //some important code goes here
  displayNextVideoPreview(movie)
}
```
Let’s assume that when a video ends the callback — `onMovieEnded(List<Movie>)` is triggered and I want to reproduce a quick preview of the next video that’s on the list to be played.

If we don’t have support for PiP mode this code can be kept in `onPause()` since exiting (=pausing) this activity means that the user won’t be seeing the video being played and therefore the callback for the next video will never be triggered. On the other hand, if I do have support for PiP the screen will be minimised and if the code is kept in `onPause()` the subscribers will be unregistered and no update will be received — so the next video preview — `displayNextVideoPreview(Movie)` will never be played.

### Implementation

You have to define when you want your application to enter on PiP mode — according to the Android Developers documentation it’s suggested to be done on `onUserLeaveHint()`
```java
@Override
public void onUserLeaveHint () {
  if (supportsPiPMode()) {
  enterPictureInPictureMode();
  }
}
```
You should take into account that this mode is only supported on Android O and afterwards, so on your utils or compatibility class (similar to Android’s `Compat`) you should have a similar condition, like this:
```java
public boolean supportsPiPMode() {
  return Build.VERSION.SDK_INT >= Build.VERSION_CODES.O;
}
```
to allow to `enterInPictureInPictureMode()` only when it’s verified and in case the required permissions are granted (more on this later on this article).

When all these conditions verify we can enter in picture mode :
```java
PictureInPictureParams params = new PictureInPictureParams.Builder()
  .setAspectRatio(getPipRatio())
  .setActions(getPIPActions(getCurrentVideo())).build();

enterPictureInPictureMode(params);
```
Where:  
- `setAspectRatio`  
The PIP window ratio — width/ height. After defined this value won’t change even if you rotate the device.  
- `setActions`  
Actions that allow the user to interact with your application.

**Notes:**
Although there are two other methods:  
-`Activity.enterPictureInPictureMode()`  
-`Activity.enterPictureInPictureMode(PictureInPictureArgs args)`  

They have been marked as deprecated on the SDK 27, meaning that they might soon be removed — so you should use instead:
-`enterPictureInPictureMode(PictureInPictureParams params)`  

Your window is reduced to 1/10 of its size and in case you have components with which you can interact on your activity/ fragment they won’t be able to receive an input event for the user — although they are visible they work as if they were disabled in this mode. The only way to interact with your app is through the default actions from the PIP itself — close, settings, return to full screen — and the ones that you’ve defined via `setActions()` when creating the `PictureInPictureParams`.

To provide a better experience, I recommend that your activity should listen to when it’s going to enter on PIP and update it’s layout accordingly. For instance, those actions that you had previously they could be hidden and then shown via the `setActions()` mentioned below.

From your fragment you can listen to:  
-`Activity.onPictureInPictureModeChanged(boolean)`  
-`Fragment.onPictureInPictureModeChanged(boolean)`
```java
@Override
public void onPictureInPictureModeChanged(boolean isInPIPMode) {
  if (isInPIPMode) {
    //Hide your clickable components
  } else {
    //Show your clickable components
  }
  super.onPictureInPictureModeChanged(isInPIPMode);
}
```
### Aspect ratio

The aspect ratio of `PictureInPictureParams` is a `Rational` object between the width and the height required. To have consistency between full screen and this mode I usually use the screen dimensions as a rule of thumb.

So the `getPipRatio()` method mentioned above is an utils method created to calculate the ratio based on the screen width/ height — depending on your requirements it can have the ratio of your screen:
```java
public static Rational getPipRatio() {
  int width = getWindow().getDecorView().getWidth();
  int height = getWindow().getDecorView().getHeight();

  return new Rational(width, height);
}
```
You need to take into account if your device is in portrait or landscape — if you want your PIP to be on landscape the `width` parameter above needs to be bigger than the `height` if instead you want it to be portrait these values should switch.

### Actions

As mentioned previously, if the user can interact with the video being played these actions need to be defined before entering on PIP as one of the configuration parameters of `PictureInPictureParams`:
```java
PictureInPictureParams params = new PictureInPictureParams.Builder()
  .setAspectRatio(getPipRatio())
  .setActions(getPIPActions(getCurrentVideo())).build();

enterPictureInPictureMode(params);
```
The logic here is similar to a notification and you only to define:
* Icon to be shown
* Title of the action
* Description of the icon
* Intent being called when the user clicks on the action
```java
private ArrayList<RemoteAction> getPIPActions(Video video) {
  Intent intent = new Intent(context, PIPActionsReceiver.class);
  intent.setAction(PIP_ACTION_MUTE);   
  intent.putExtra(Values.VIDEO_URL, video.getUrl());

  PendingIntent pendingIntent = PendingIntent.getBroadcast(context, video.getUrl().hashCode(),intent, PendingIntent.FLAG_ONE_SHOT);

  Icon icon = AudioUtils.isMuted() ?
     R.drawable.ic_pip_unmute :
     R.drawable.ic_pip_mute);

  ArrayList<RemoteAction> actions = new ArrayList<>();
  actions.add(new RemoteAction(icon,    
     getString(R.string.pip_action_mute),   
     getString(R.string.pip_action_mute_description), pendingIntent));

   //…
   return actions;
}
```
Don’t forget to define your BroadcastReceiver for the PIP events — in the above example the `PendingIntent` will be delivered on `PIPActionsReceiver` — that will be something like:
```java
@Override
public void onReceive(Context context, Intent intent) {
  if (intent == null || !PIP_ACTION_MUTE.equals(intent.getAction())){
    return;
  }
     
  // process the received action
}
```
**Note:** don’t forget to register the `BroadcastReceiver` on the Manifest file or programmatically.

It’s not possible to define a selector here, so when one action is triggered we need to update the button by defining a new set of components
```java
private void updatePipParams(Video video) {
  PictureInPictureParams params = new PictureInPictureParams.Builder()
    .setAspectRatio(getPipRatio())
    .setActions(getPIPActions(getCurrentVideo())).build();

  setPictureInPictureParams(params);
}
```
**Notes:**

* Since these events are asynchronous don’t forget to check if we’re still on PiP mode before calling `updatePipParams(Video)`. You can easily do that via:
```java
if (Activity.isInPictureInPictureMode()) {
    updatePipParams(video);
}
```
* There’s a maximum number of actions that can be added. To avoid declaring too many actions that won’t be displayed due to the small length of the PIP window don’t forget to first check:
```java
Activity.getMaxNumPictureInPictureActions()
```

### To sum up (in graphics)

The following images resume on a high-level all the steps from when an application is running and the user decides to click on the menu button in order to enter on PIP mode.

Programmatically, *your app* needs to define the list of user actions supported, the ratio of the PIP window and call `enterPictureInPictureMode`.

<figure>
<img src="https://cdn-images-1.medium.com/max/3940/1*krJtMfcSG6NonsKdYyxiDA.png" alt="Entering on Picture In Picture mode">
<figcaption>Entering on Picture In Picture mode</figcaption>
</figure>

And when the user clicks in one of these actions — *your app* needs to process it and it should update the previous created actions to show, now an opposite icon on the action clicked.

<figure>
<img src="https://cdn-images-1.medium.com/max/3772/1*jrGZtxMRc_hLftHXvx4Z_A.png" alt="Clicking on a PIP action">
<figcaption>Clicking on a PIP action</figcaption>
</figure>


### When to enter on PiP mode?

This depends on your application specific features, for instance:

* Google Maps enters when the user is on navigation mode and presses the home button — if you’re searching for a place or selecting the route; it will just minimize. Clicking on the back button will return to the previous screen, until the application reaches the last activity/ fragment and exits.
* Netflix enters when the user is watching a video
* Duo enters when a video call is already established

What they all have in common? They only enter on PiP mode when the user is using the application on a feature that requires his focus — either it’s:
* Looking at the map to see the next turn
* Making a video call with another person
* Watching a video

Moreover, the UX is a bit different that one might initial expect. If you leave the application by either pressing the menu button or via a notification towards other application you’ll enter on Picture In Picture mode. However, if you take an action on a heads-up notification — for instance, an incoming call, no PiP mode will be triggered.

### What if you already have an activity on PiP mode?

Sending another application to background will have no effect; there can only be one active PiP at all times — in this case it will be the first one that entered this mode.

## Additional information

Some additional information that might be useful for development.

### Permissions

Although not particularly easy to find, there’s a somewhat hidden permission for the user to disable picture-in-picture. If you go to the native Settings → Your Application → Advanced → Picture-in-picture you have an option here to disable it. For this scenario, I suggest before trying to enter in this mode to first verify if it’s allowed:
```java
public static boolean canEnterPiPMode() {
  AppOpsManager appOpsManager = (AppOpsManager) context.getSystemService(Context.APP_OPS_SERVICE);
  return (AppOpsManager.MODE_ALLOWED == appOpsManager.checkOpNoThrow(
                              AppOpsManager.OPSTR_PICTURE_IN_PICTURE, 
                              Process.myUid(), 
                              context.getPackageName()));
}
```
In order to open this screen:
```java
Intent intent = new Intent(“android.settings.PICTURE_IN_PICTURE_SETTINGS”, Uri.parse(“package:” + context.getPackageName()))
```
Otherwise nothing will happen.

### Audio focus

Audio manager allows you to gain or abandon audio focus depending on your needs, so if you’re interacting with the speaker — either by playing a video or audio you should first call:
```java
AudioManager.requestAudioFocus(OnAudioFocusChangeListener, int, int)
```
And depending if the method returns:
- `AudioManager.AUDIOFOCUS_REQUEST_FAILED` 
You should warn the user, with a friendly message, saying that it wasn’t possible to request the speaker at that time. Usually, this happens when an application with higher priority is using the speaker (for instance, you’re on a call); or when the wrong stream is used on the second parameter of the method — on most cases, the correct value here is to use `AudioManager.STREAM_MUSIC`.  
- `AudioManager.AUDIOFOCUS_REQUEST_GRANTED` 
Your application was granted to play audio and you can proceed.  

Although, not strictly necessary, it’s a good practice to send the listener for `OnAudioFocusChangeListener`, since your application can lose the audio focus due to an external event and you should react to it. For instance, if you’re in PiP mode playing a video you should stop it and update its actions to resume when the user interacts with it again.

Typically, you can do something like:
```java
@Override
public void onAudioFocusChange(int focusChange) {
  switch (focusChange) {
    case AudioManager.AUDIOFOCUS_GAIN:
      //resume playing 
      break;
    case AudioManager.AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK:
      //reduce audio to a minimum if you want to continue playing
      break;
    case AudioManager.AUDIOFOCUS_LOSS:
    case AudioManager.AUDIOFOCUS_LOSS_TRANSIENT:
    default:
      //stop playing
      break;
    }
}
```
Don’t forget to call:
```java
AudioManager.abandonAudioFocus(OnAudioFocusChangeListener);
```
when your action has ended.

**Note:** For more information, please check, the official documentation for `AudioManager` that can be found [here](https://developer.android.com/reference/android/media/AudioManager).

## Known limitations

You may see a small flickering when you click on the PiP surface and it resizes. This issue is related to the OS implementation of OpenGL and has already been reported [here](https://stackoverflow.com/questions/8320667/pip-in-opengl-causing-flickering). It’s a known limitation — meaning that most apps that use OpenGL will face it — hopefully it will be fixed in a later Android release-

## More on this

Check out this wonderful articles that provide extended information about PiP:
- [Making magic moments with picture in picture](https://medium.com/androiddevelopers/making-magic-moments-with-picture-in-picture-e02964bf75ae)
- [Picture-in-picture Support](https://developer.android.com/guide/topics/ui/picture-in-picture)

There’s a sample project made by the Android Team available [here](https://github.com/googlesamples/android-PictureInPicture).


Do you have a better approach? Something didn't quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.