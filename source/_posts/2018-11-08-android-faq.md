title: Android FAQ
date: 2018-11-08 10:03:41
tags:
- android

---

# FAQ 1

**No toolchains found in the NDK toolchains folder for ABI with prefix: mips64el-linux-android**

找到自己本地的 android-sdk：
![as-sdk](/img/2018-11-08-android-faq/as-sdk.jpg)

打开这个目录后，查看 `ndk-bundle` -> `toolchains` 里面是否确实缺少 mips64el 相关的文件。

解决办法：
- [点击这里](https://developer.android.com/ndk/downloads/?hl=zh-cn)下载 NDK 包。
- 解压之后打开 `toolchains` 文件夹，跟本地 `toolchains` 文件夹做对比，找到其缺少的文件夹，复制过去。

# FAQ 2
**Installation failed with message Invalid File**
如果设备上应用明明已经卸载了，但是 AS 在 Run 这个应用的时候还是一直提示：
```
Installation failed with message Invalid File: xxxx. It is possible that this issue is resolved by uninstalling an existing version of the apk if it is present, and then re-installing.
WARNING: Uninstalling will remove the application data!
Do you want to uninstall the existing application?
```
可以尝试以下这个方法：
```
Settings>Build,execute,deployment>Instant run>Enable instant run to hot swap code /resource change on deploy(unchecked this option)
```