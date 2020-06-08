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

# FAQ 3

**Module not specified**

如果 app 模块上有个红叉：
![module-not-specified-1](/img/2018-11-08-android-faq/module-not-specified-1.png)
且运行的时候弹出如下的提示框：
![module-not-specified-2](/img/2018-11-08-android-faq/module-not-specified-2.png)
在 settings.gradle 中，先注释掉 include'：app'，然后单击 File > Sync Project with Gradle files。
此后在 settings.gradle 恢复 include'：app'，再 Sync 工程。

# FAQ 4

**ERROR: SSL peer shut down incorrectly**

project 级别的*build.gradle* 里面把 `jcenter()` 注释掉。

```
allprojects {
    repositories {
        google()
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
//        jcenter()
    }
}
```

# FAQ5

**Command line is too long**

修改项目下 `.idea\workspace.xml`，找到标签 `<component name="PropertiesComponent">`， 在标签里加一行`<property name="dynamic.classpath" value="true" />`

# FAQ6

**Android Studio Build Output 栏内汉字出现乱码**

![build-output-mass-code](/img/2018-11-08-android-faq/build-output-mass-code.png)
打开 Android Studio 双击 shift 或者点击以下搜索按钮：
![search](/img/2018-11-08-android-faq/search.png)
在搜索框内输入 Edit Custom VM Options：
![edit-custom-vm-options](/img/2018-11-08-android-faq/edit-custom-vm-options.png)
点击第一个选项打开 studio64.exe.vmoptions（若没有此文件，则先创建），添加一句代码 `-Dfile.encoding=UTF-8`，并编译一下，必须编译，否则 AS 重启的话无法正常打开。
![studio64-vmoptions](/img/2018-11-08-android-faq/studio64-vmoptions.png)
编译以后重启 AS 即可。

# FAQ7

**Program type already present**

工程结构是这样的：
1. lib1 定义的是各种接口文件，导出成 lib1.aar
2. lib2 引用 lib1.aar，并对 lib1 的接口进行实现，导出成 lib2.aar
3. app 引用 lib1 和 lib2，但是编译的时候一直会提示 `Program type already present` 的错误，明明只有 lib1 中定义一次的 interface，也还是会提示 `Program type already present` 这个错误

各种尝试 `implementation`、`api`、`compileOnly` 来引用这两个 aar，依然会出现这个问题（非常抓狂。。。）。最后看到一个网友说不要使用 `implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，否则就会出现 `Program type already present` 问题。于是在 app 的 gradle 中注释掉 `implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，果然问题就没了。
