---
title: "Troubleshooting: KMP and undefined symbols for architecture x86_64 (iOS)"
date: 2021-09-22 00:00:00 +01:00
tags: [Kotlin, KMP, KMM, troubleshooting]
---

<figure>
<img src="/troubleshooting-kmp-and-undefined-symbols-for-architecture-x86-x64-ios-adb/featured.jpeg" alt="Troubleshooting: KMP and undefined symbols for architecture x86_64 (iOS) cover">
</figure>

As a new version of Xcode was released, I started my day thinking what would happen if I made that update and then compiled my Kotlin Multiplatform project.

According to my CI, the answer is no:

> The /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld command returned non-zero exit code: 1. output: ld: warning: ignoring file /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/clang/13.0.0/lib/darwin//libclang_rt.ios.a, missing required architecture x86_64 in file /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/clang/13.0.0/lib/darwin//libclang_rt.ios.a (4 slices) Undefined symbols for architecture x86_64: "\_\_\_cpu_model", referenced from: \_Kotlin_String_hashCode in result.o ld: symbol(s) not found for architecture x86_64

I’m running on a Mac M1 (Apple Silicon) with Kotlin 1.5.10.

Fortunately, the issue is simple to solve, you just need to **bump Kotlin version to [1.5.30](https://blog.jetbrains.com/kotlin/2021/08/kotlin-1-5-30-released/)** (where we got native support for Apple Silicon) or even better to 1.5.31 that was released this week.

With this, you can also remove the tick on “Open with Rosetta” from Xcode properties (Get Info) and take full advantage of M1.

**Note:** If you’re compiling your project to JS, updating to 1.5.30/1 will most certainly increase your library size. For now, I’ve decided to have different versions depending on the target to which I’m compiling the project.


Do you have a better approach? Something didn’t quite work with you?

Feel free to reply here or send me a message 🙂
