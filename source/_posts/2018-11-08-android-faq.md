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