---
title: "Debug your Gradle files"
date: 2019-08-03 00:00:00 +01:00
tags: [android, debug, gradle]
image: "featured.png"
---

<figure>
<img src="/debug-your-gradle-files/featured.png" alt="Debug your Gradle files cover">
</figure>

I’ve written an [article](https://medium.com/@cafonsomota/debug-annotation-processor-in-kotlin-6eb462e965f8) about how to debug an annotation processor a couple of weeks ago and it has been really helpful for me (at least) to just open it and just copy the commands necessary to start this process.

Although the process it’s similar, as I face other challenges, I felt that I needed to write one on how we can debug the build.gradle file.

## How to debug build.gradle

First, let me start by describing my current setup — I’m using Android Studio 3.4.2 with Gradle version 5.4.1 and plugin 3.3.2.

**Note:** there might be some changes on older versions of Gradle/plugin, so if you encounter any problem feel free to ping me.

In order to debug you’ll need to:

1. First, create a **Remote Configuration** so you can attach it later to debug your annotation processor code:
    1. Go to "Edit Configurations…" (Run/ Debug Configurations)
    2. Click on the plus sign ("+")
    3. Select "Remote"
    4. You should have a new configuration added that’s similar to this one:
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2136/0*yYDJjyFebGt_QtXf.png" alt="Remote debugger configuration">
    <figcaption>Remote debugger configuration</figcaption>
    </figure>
    I’ve used the default configuration here — I’ve just changed the default name to **Debugger** so it will be easier to spot on Android Studio actions bar.

2. Now you’ll need to define the JVM arguments used when creating the daemon process. For this, you’ll need to update **gradle.properties** with the following arguments:
    ```shell
org.gradle.jvmargs=-Xmx4g -XX:MaxPermSize=512m -Dfile.encoding=UTF-8 -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005
    ```
    
    The first arguments are JVM specific and can vary from project to project, the **agentlib** are the important ones in this case and correspond to:
      
    **transport**  
    Name of the transport used to connect to the debugger application (required). The default value if not set is `none`.  

    **address**  
    The transport address for the connection. If `server` option is set to `n`the debugger will attempt to be attached at this address; if instead is enabled - `y` it will listen for a connection at this port (if `server=n` this option is required). The default value if not set is "" (empty).  

    **server**  
    If `y` it will listen for a debugger to be attached; otherwise, will attach to the debugger application at the specified address (not required). The default value if not set is `n`.

    **suspend**  
    It defines the policy used on `VMStartEvent` if y it will be `SUSPEND_ALL` otherwise will be `SUSPEND_NONE` (not required). The default value if not set is `y`.

    **Note:** there’s no need to set `org.gradle.daemon` to `true` since we already changed the JVM arguments. Doing it so is the equivalent of adding:
    
    ```shell
    -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005
    ```
    
    I find it easier to debug by first attaching the debugger and only after triggering the Gradle daemon to run. Otherwise, you’ll need to wait for the process to start which can take a while and you might get distracted and end up waiting for a longer time than it is needed.

3. Now that you’ve got everything ready go to your **build.gradle** file and add the breakpoints on the method/task that you want to debug.
4. In order to trigger the breakpoints, you’ll need to **first attach the debugger:**
    <figure>
    <img src="https://cdn-images-1.medium.com/max/2154/0*ZC7sALspgQvu0eT3.png" alt="Remote config Debugger">
    <figcaption>Remote config "Debugger"</figcaption>
    </figure>

    and then to start the Gradle daemon.


You can do this by either clicking on *Sync Now*, *Refresh Gradle Project*, or just by entering on *Terminal*:

```shell
./gradlew --daemon
```

<figure>
<img src="https://cdn-images-1.medium.com/max/3780/1*BFkn5yMysmB79teOCez8qQ.png" alt="Debugging build.gradle">
<figcaption>Debugging build.gradle</figcaption>
</figure>


## For more information:

* [Gradle Docs: Build Environment](https://docs.gradle.org/current/userguide/build_environment.html)
* [Gradle Docs: Troubleshooting](https://docs.gradle.org/current/userguide/troubleshooting.html)

Do you have a better approach? Something didn’t quite work with you? Feel free to send me a [message](https://twitter.com/cafonsomota) 🙂.