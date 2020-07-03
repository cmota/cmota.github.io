---
title: "Android Development Tips - Part II"
date: 2019-02-19 00:00:00 +01:00
tags: [android, tips]
image: "featured.png"
---

<figure>
<img src="/android-development-tips-part-II/featured.png" alt="Android Development Tips - Part II cover">
<figcaption>Copyright @ Android, Google</figcaption>
</figure>

During December I’ve decided to create a… let‘s call it an *advent of code —* where I would tweet ([@cafonsomota](https://twitter.com/@cafonsomota)) daily tips related with Android. I’ve decided to group them into the following categories:

* Android
* Android Development
* Android Studio
* Kotlin
* Java
* Product Design

This second part is dedicated to Kotlin (and a really little bit of Java) — you can find the first part of the Android development tips here [Android development tips - Part I](https://medium.com/@cafonsomota/android-development-tips-part-i-8b07420b6e3b).

## Kotlin

1. When measuring the time a block of code takes to execute who thought there should be a better way than having to call `currentTimeMillis()` before and after those instructions and then subtract both timestamps in order to know the time it took to execute? 🙋‍♂️
    It should be something like:
    ```kotlin
val startTime = System.currentTimeMillis()
//block of code to measure
//several instructions goes here
val totalTime = System.currentTimeMillis() - startTime
    ```
    Yes, on Kotlin we’ve got:
    ```kotlin
measureTimeMillis{}
    ```

    <figure>
    <img src="https://cdn-images-1.medium.com/max/2824/1*mEtB619S1nP8Tn2stPBCLQ.png" alt="Measuring the time a block of code takes to execute">
    <figcaption>Measuring the time a block of code takes to execute</figcaption>
    </figure>

2. There’s a really useful function called — `require(Boolean)` specially if you want to check if a parameter has an expected value or not. It will throw `IllegalArgumentException` if the condition is `false`; but you can override that by calling `return`.
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3064/1*3JmBCBTxa9v5OlnCve3AGA.png" alt="Example of using require(boolean)/requireNotNull(boolean) functions">
    <figcaption>Example of using require(boolean)/requireNotNull(boolean) functions</figcaption>
    </figure>

3. We also have `check(Boolean)` which is really similar to `require`. The main difference between both is that while `require` is used for parameters, `check` is used for local variables. In other words, it’s used to "check" if an object contains a specific valuse/ state or other mandatory conditions.
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3128/1*03RZ40T7VZ2ocL5u--SBng.png" alt="Example of using check(Boolean) function">
    <figcaption>Example of using check(Boolean) function</figcaption>
    </figure>
4. Back in the dark ages when Kotlin was still being forged — there’s been a particularly consuming task during Android Development: creating POJOs.s
    In order to create a specifisc object you would needed to:
    1. Declare all the fields/constructor
    2. Write all the getters/setters
    3. Override equals()/ hashCode()/ toString()

    Although, we could generate point 2 and 3 with Android Studio, if you’re using Hungarian notation on your code, you might end up having to go through all the getters/setters methods in order to remove that extra "m" added automatically.

    This is something quite common to almost all the Android applications. I would say that you’ll end up creating at least one of those classes to hold specific data.

    On Kotlin we’ve got something far more useful and fast to declare:

    **Data classes.**  
    The compiler automatically derives everything declared on its constructor.
    <figure>
    <p float="left">
    <img src="https://cdn-images-1.medium.com/max/2768/1*6LZWPXUyIwsOjSLA-B7fog.png" alt="Java code" style="width:305px;height:600px;">
    <img src="https://cdn-images-1.medium.com/max/3344/1*PqOkTuCmhN-Y-Qc_Oj51ug.png" alt="Kotlin code" style="width:400px;height:100px;">
    </p>
    <figcaption>The same object declared in Java on the left and Kotlin in the right</figcaption>
    </figure>

    I know it’s really difficult to see, but there’s 65 lines in Java (on the left) vs 1 in Kotlin.

5. Tired of implementing all the `Parcelable` methods by hand? I hope your class doesn’t have that many objects otherwise you’ll spend quite some time writing and reading everything from `Parcel`!
    Or, in alternative you can enable the experimental flag on **androidExtesions (build.gradle)** — and just use `Parcelize` instead *magic*
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3536/1*VPlClSQok1UvmrjYi-QXYg.png" alt="How to use Parcelize">
    <figcaption>How to use Parcelize</figcaption>
    </figure>

    **Note:** `Parcelize` is still on experimental mode — meaning that it might not be 100% ready for production. Although I’ve never encountered any issue, use it at your own risk.

6. Kotlin also allow us to have: default arguments. This means that if you omit an argument when calling a method, if a default one is defined, it will be used instead.
    On JAVA the equivalent would be to:  
    — Creating multiple methods where one parameter varies or  
    — Always having to set all parameters  
    On Android we can easily see this when you try to extend a view. You’ll always have at least three different constructors that could be merged into a single one using Kotlin’s default values
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2904/1*b8T0KqJ1GS17cNjRzHUDoA.png" alt="Java code">
    <img src="https://cdn-images-1.medium.com/max/4084/1*14taNltUsHvH1mROQBAO-A.png" alt="Kotlin code">
    <figcaption>The same class written in Java on top and using Kotlin default arguments on bottom</figcaption>
    </figure>
    **Note:** you can see that in the Kotlin example I’ve added a specific annotation:

    **@JvmOverloads**  
    This is used when you’ve got code written in Java that will call code written on Kotlin. When using default arguments, only the full method signature is visible to Java, you need to add *@JvmOverloads* annotation in order for the compiler to generate another set of methods (in this case constructor) that can be called from Java — it will create an additional method for each parameter that has a default value set.

    Along with this we also have named arguments. This is particularly important because, first it improves readability and secondly it allow us to select a specific argument when there’s more than one with a default value.
    <figure>
    <img src="https://cdn-images-1.medium.com/max/4096/1*lrL642hq6bAqOmVWdg3uvw.png" alt="An example using named arguments">
    <figcaption>An example using named arguments</figcaption>
    </figure>

7. We also have `componentN()` functions! In other words, they allow us to have destructuring declarations — meaning that it’s possible to decompose an object into a set of variables that can be used independently
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3240/1*23kGumnkoXELoL1QX1bRYQ.png" alt="An example with destructuring declarations">
    <figcaption>An example with destructuring declarations</figcaption>
    </figure>

8. If you’ve declared a variable as *lateinit* and want to check it on a specific method if it’s initialized you can do it via:
    ```kotlin
::KProperty.isInitialized
    ```
    <figure>
    <img src="https://cdn-images-1.medium.com/max/3476/1*suDnMUCsto59d0JktrotRQ.png" alt="How to check if a property declared as lateinit is already initialized">
    <figcaption>How to check if a property declared as lateinit is already initialized</figcaption>
    </figure>

### Documentation

Kotlin has it own support for documenting code — it’s called KDoc and it’s the equivalent to Java’s Javadoc. If you’re already familiarised with Javadoc it’s really similar:
<figure>
<img src="https://cdn-images-1.medium.com/max/2736/1*mxA4_KvckTkudo_6Zn24Cw.png" alt="KDoc syntax example from kotlinlang website">
<figcaption>KDoc syntax example from kotlinlang website</figcaption>
</figure>

with the advantage that also supports Markdown for inline markup.

Let’s see an example:

On Javadoc if you want to enumerate some items you would use HTML tags in order to have indentation and bullet points. However, if you do this on KDoc, Android Studio wouldn’t render the documentation for that method/ class. For that you just need to use `* followed by the text to enumerate.

<figure>
<img src="https://cdn-images-1.medium.com/max/2996/1*_K0EUgLYccMLhlzdc4haEA.png" alt="Javadoc">
<img src="https://cdn-images-1.medium.com/max/2996/1*8DZestY8zVoWpUdM0swoLQ.png" alt="KDoc">
<figcaption>Javadoc on top vs KDoc on bottom</figcaption>
</figure>

You can find all the information and examples about KDoc on the official [kotlinlang website](https://kotlinlang.org/docs/reference/kotlin-doc.html).

### Unit testing

Apart from making your unit test display name completely readable you can also add emojis! 🙌🎉

<figure>
<img src="https://cdn-images-1.medium.com/max/2872/1*2SsezujlsrQRQ028e_yevA.png" alt="Unit testing">
<figcaption>Unit testing</figcaption>
</figure>

## Java

This is out of the box in Kotlin, but for those still using Java I was hoping to see it more often than I use to — annotations.

It’s really useful to have: IntRes, StringRes, or at least for Nullable and NonNull objects to easily understand the method return type and parameters.

Having them defined will add another layer of security in compile time, detecting if an object is sent as null when it’s not expected, or if you’re sending an int instead of a IntRes, etc.

<figure>
<img src="https://cdn-images-1.medium.com/max/3636/1*n-VEqkPyLFCRt1TRrdGgPQ.png" alt="Using annotations to understand the possible values that an object can have">
<figcaption>Using annotations to understand the possible values that an object can have</figcaption>
</figure>