---
title: "Annotation Processor: printing a message (and doing it in a new line)"
date: 2020-04-05 00:00:00 +01:00
tags: [android, tutorial, annotation processor]
image: "featured.png"
---

<figure>
<img src="/annotation-processor-printing-a-message/featured.png" alt="Annotation Processor: printing a message (and doing it in a new line) cover">
</figure>


This is a really simple and direct tutorial, but it really took me more than I want to admit to understand why this wasn’t working as expected, and since there isn’t much information about it I thought it’s worth sharing — annotation processor: printing a message (and doing it in a new line).

### Printing a message

Printing a message in the build console is quite simple, you just need to call ProcessingEnvironment.Message.printMessage(Kind, CharSequence).

In order to access this method, you need to extend the class AbstractProcessor and override the process function, something like:

```kotlin
class GenerateProcessor : AbstractProcessor() {

  override fun process(p0: MutableSet<out TypeElement>?, roundEnv: RoundEnvironment?): Boolean {
    
    processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "Hello")
    
    return true
  }
}
```

And if you look at the build view, you can see the “Hello” message printed:

<figure>
  <img src="https://cdn-images-1.medium.com/max/5592/1*z4YuCKO_DIKKCgLIw2pxBg.png" alt="Build view on Android Studio (Kind == NOTE)">
  <figcaption>Build view on Android Studio (Kind == NOTE)</figcaption>
</figure>

**Note: **you need to switch to the build view on your Android Studio.

If you look at printMessage arguments you can see that it receives one that corresponds to Kind and the other to the message that you want to print. This first argument can be one of the following types:
- **ERROR**  
When there’s a problem that requires that the compilation ends.
- **WARNING**  
There’s a problem that requires attention but it’s not severe to the point that the compilation needs to end.
- **MANDATORY_WARNING**  
A problem similar to a warning, but it’s from the specification itself.
- **NOTE**  
An informative message.
- **OTHER**  
When the message to be printed doesn’t belong in any of the above criteria.

If you switch the Kind value to ERROR when printMessage is executed the compilation will end.

<figure>
  <img src="https://cdn-images-1.medium.com/max/5592/1*YPhOggU3x22JHOwQF4Ozmg.png" alt="Build view on Android Studio (Kind == ERROR)">
  <figcaption>Build view on Android Studio (Kind == ERROR)</figcaption>
</figure>

### **Printing a message in a new line**

The above example works fine but what if you want to add multiple calls to printMessage?

```kotlin
override fun process(p0: MutableSet<out TypeElement>?, roundEnv: RoundEnvironment?): Boolean {
  
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "Hello")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "This is one message")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "This is another message")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "And yet another")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "Well you got the point")
        
  return true
}
```

Well, it’s not particularly easy to read them:

<figure>
  <img src="https://cdn-images-1.medium.com/max/5592/1*RWwQtls-rC1XMealy5Ep6A.png" alt="Build view on Android Studio (multiple printMessage() single lined)">
  <figcaption>Build view on Android Studio (multiple printMessage() single lined)</figcaption>
</figure>

On this simple example, you’ve removed the other tasks that are printed while you compile the project — but trust me, on a large project these debug lines get pretty difficult to read.

The solution is not the same that you’re used to. You can’t just fix it by adding a new line — \n, since it‘s ignored by the system. Alternatively, you’ll need to add \r\n if you want to have messages on multiple lines:

```kotlin
override fun process(p0: MutableSet<out TypeElement>?, roundEnv: RoundEnvironment?): Boolean {
  
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "Hello\r\n")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "This is one message\r\n")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "This is another message\r\n")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "And yet another\r\n")
  processingEnv.messager.printMessage(Diagnostic.Kind.NOTE, "Well you got the point\r\n")
        
  return true
}
```

**Note:** `\r` corresponds to the carriage return

<figure>
  <img src="https://cdn-images-1.medium.com/max/5592/1*9Co8eP1ukxUrlVCXb0zehw.png" alt="Build view on Android Studio (multiple printMessage() multi-lined)">
  <figcaption>Build view on Android Studio (multiple printMessage() multi-lined)</figcaption>
</figure>

Special thanks to Burkhard Mittelbach who pointed me on the right answer.

### Conclusion

Debugging an annotation processor is always a bit tricky — and since you’re dealing with code that is generated in compile the entire process can cause some headaches.

Now that you’ve seen how you could use printMessage in order to add build logs, let me also share an article that I’ve written a while back on how to debug annotation processor in Kotlin:
[Debug annotation processor in Kotlin](https://medium.com/@cafonsomota/debug-annotation-processor-in-kotlin-6eb462e965f8)

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.
