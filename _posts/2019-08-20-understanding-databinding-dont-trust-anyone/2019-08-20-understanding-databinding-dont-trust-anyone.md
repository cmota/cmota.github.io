---
title: "Android: understanding why data binding doesn’t like anyone"
date: 2019-08-20 00:00:00 +01:00
tags: [android, data binding]
image: "featured.png"
---

<figure>
<img src="/understanding-databinding-dont-trust-anyone/featured.png" alt="Android: understanding why data binding doesn’t like anyone cover">
</figure>

As our projects get bigger and more complex we start using more libraries and different approaches with the goal of making our development process easier and faster. I would say that most of us, that are for quite some time in the industry, already used one of these on our projects: dependency injection, annotation processor, data binding, etc.

Although they all have a learning curve associated (which, on some, can be rather big) as we tend to have multiple approaches/ libraries together we drastically increase the time that we’re going to spend debugging. And when we have data binding in the equation… well, good luck!

These errors can go from the common: you need to first (re)synchronize your Gradle files before you continue; to the more desperate ones where you end up cleaning the project, invalidating caches and restarting Android Studio, hoping that it will get it fixed. And then, you synchronize your project again and the same error message is displayed.

For those struggling with these errors on data binding, I’ve found a solution that allows me, at least, to understand why a project it’s not compiling — or, in other words, why those classes are not being generated.

```shell
error: cannot find symbol
protected MainActivity(DataBindingComponent _bindingComponent, View _root,
```

There are two approaches that I’ve been following:

### Increase the number of errors that can be displayed

By default, the Java compiler cuts off errors after reaching 100 log messages — which depending on your project this might not be nearly enough.

Typically, on a project containing data binding, if there’s an error somewhere in your code the error messages that you’re going to see will, most certainly, be related with not being possible to access a specific class or attribute that should have been generated and not about the error that is causing your compilation problems in the first place.

Without more information, unless you open all your project files looking for errors it can be really time-consuming to understand where it might be. To increase the default value just add this snippet to your build.gradle file.

```gradle
subprojects {
  gradle.projectsEvaluated {
    tasks.withType(JavaCompile) {
      options.compilerArgs << '-Xmaxerrs' << '500'
    }
  }
}
```

### Create a temporary DataBindingComponent interface

When the first approach is not enough we will end up seeing a lot of errors like this one:

```shell
> Task :app:kaptGenerateStubsDebugKotlin
e: /Users/carlosmota/Projects/Playground/app/build/generated/data_binding_base_class_source_out/debug/dataBindingGenBaseClassesDebug/out/com/cmota/playground/databinding/MainActivity.java:132: error: cannot find symbol
  protected MainActivity(DataBindingComponent _bindingComponent, View _root,
                                    ^
  symbol:   class DataBindingComponent
  location: class MainActivity
```

If you changed the default value to `500`, you’ll see 500 of these.

I’ve spent quite some time trying to understand why, after a merge, I was dealing with this type of errors. I had no conflicts during this process, but anyhow I had a compilation error that I couldn’t understand why it was happening nor what was causing it.

After some research, I’ve ended up with this solution that allows me to trick data binding in order to understand what’s causing the errors.

1. Temporarily declare an empty interface for the missing DataBindingComponent in your project. It should be located under `src/main/java/androidx.databinding/DataBindingComponent.java` which is where it’s going to be after a successful build.
    ```java
package androidx.databinding;
public interface DataBindingComponent {
  // Do nothing
}
    ```
    **Note:** you’ll need to create the corresponding packages.  
2. Now, let’s hit compile and look at the console.  
    Those errors have disappeared since the required class was found and now we can understand why the project wasn’t compiling in the first place.  
3. After solving the error you’ll end up seeing a new one when you make a new attempt to compile the project:
    ```shell
Task :kaptRcsplusWebCompanionOnDebugKotlin
e: /Users/carlosmota/Projects/Playground/generated/source/kapt/rcsplusWebCompanionOnDebug/androidx/databinding/DataBindingComponent.java:3: error: duplicate class: androidx.databinding.DataBindingComponent
  public interface DataBindingComponent {
  ^
    ```

This happened because now it was possible to generate all the classes, so you end up having two classes with the same name. Removing it will fix the error and you should now be able to compile the project successfully.

### Troubleshooting

Don’t forget to check your build.gradle file to check if you really got dataBinding enabled

```gradle
dataBinding {
  enabled true
}
```

And that your gradle.properties contains:

```gradle
android.useAndroidX=true
android.enableJetifier=true
android.databinding.enableV2=true
```

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
