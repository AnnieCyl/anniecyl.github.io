title: Android FAQ
date: 2018-11-08 10:03:41
tags:
- android

---

# Q 1. No toolchains found in the NDK toolchains folder for ABI with prefix: mips64el-linux-android

找到自己本地的 android-sdk：
![as-sdk](/img/2018-11-08-android-faq/as-sdk.jpg)

打开这个目录后，查看 `ndk-bundle` -> `toolchains` 里面是否确实缺少 mips64el 相关的文件。

解决办法：
- [点击这里](https://developer.android.com/ndk/downloads/?hl=zh-cn)下载 NDK 包。
- 解压之后打开 `toolchains` 文件夹，跟本地 `toolchains` 文件夹做对比，找到其缺少的文件夹，复制过去。

# Q 2. Installation failed with message Invalid File

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

# Q 3. Module not specified

如果 app 模块上有个红叉：
![module-not-specified-1](/img/2018-11-08-android-faq/module-not-specified-1.png)
且运行的时候弹出如下的提示框：
![module-not-specified-2](/img/2018-11-08-android-faq/module-not-specified-2.png)
在 settings.gradle 中，先注释掉 include'：app'，然后单击 File > Sync Project with Gradle files。
此后在 settings.gradle 恢复 include'：app'，再 Sync 工程。

# Q 4. ERROR: SSL peer shut down incorrectly

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

# Q 5. Command line is too long

修改项目下 `.idea\workspace.xml`，找到标签 `<component name="PropertiesComponent">`， 在标签里加一行`<property name="dynamic.classpath" value="true" />`

# Q 6. Android Studio Build Output 栏内汉字出现乱码

![build-output-mass-code](/img/2018-11-08-android-faq/build-output-mass-code.png)
打开 Android Studio 双击 shift 或者点击以下搜索按钮：
![search](/img/2018-11-08-android-faq/search.png)
在搜索框内输入 Edit Custom VM Options：
![edit-custom-vm-options](/img/2018-11-08-android-faq/edit-custom-vm-options.png)
点击第一个选项打开 studio64.exe.vmoptions（若没有此文件，则先创建），添加一句代码 `-Dfile.encoding=UTF-8`，并编译一下，必须编译，否则 AS 重启的话无法正常打开。
![studio64-vmoptions](/img/2018-11-08-android-faq/studio64-vmoptions.png)
编译以后重启 AS 即可。

# Q 7. Program type already present

工程结构是这样的：
1. lib1 定义的是各种接口文件，导出成 lib1.aar
2. lib2 引用 lib1.aar，并对 lib1 的接口进行实现，导出成 lib2.aar
3. app 引用 lib1 和 lib2，但是编译的时候一直会提示 `Program type already present` 的错误，明明只有 lib1 中定义一次的 interface，也还是会提示 `Program type already present` 这个错误

各种尝试 `implementation`、`api`、`compileOnly` 来引用这两个 aar，依然会出现这个问题（非常抓狂。。。）。最后看到一个网友说不要使用 `implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，否则就会出现 `Program type already present` 问题。

于是终于发现在 app 的 gradle 中要么使用 `implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，一下子引入 libs 下所有的 jar 和 aar：
```
implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')
// implementation(name:'lib1',ext:'aar')
// implementation(name:'lib2',ext:'aar')
```
要么就是不用 `implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')`，直接引用具体的 aar：
```
// implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')
implementation(name:'lib1',ext:'aar')
implementation(name:'lib2',ext:'aar')
```
如果同时使用 `implementation fileTree` 和引用具体的 aar（如下），就会出现 `Program type already present` 这个错。
```
implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')
implementation(name:'lib1',ext:'aar')
implementation(name:'lib2',ext:'aar')
```

# Q 8. android:textAllCaps="false"

在界面添加 Button 的时候，默认 Button 显示的字母都是大写的，即使它的 text 属性设置的都是小写字母。

解决办法：在 Button 的属性里加上 android:textAllCaps="false" 即可。

# Q 9. Installed Build Tools revision 29.0.2 is corrupted. Remove and install again using the SDK Manager

解决办法：
1. Open the SDK manager by clicking on the icon in the top right of Android Studio. (Or go to File > Settings > Appearance & Behavior > System Settings > Android SDK)
2. Open **SDK Tools** tab
3. At the bottom-right, click "Show Package Details"
4. Find **Android SDK Build-Tools** and uncheck the checkbox next to 29.0.2
5. Click apply in the bottom of the window. That will delete the component.
6. Now check the checkbox next to 29.0.2
7. Click apply in the bottom of the window. That will reinstall it.

# Q 10. 查看 apk 信息（包名、版本号、版本名称、兼容 api 级别、启动 Activity 等）

1. 找到 Android SDK 目录下的 `build-tools` 文件夹，会有不同版本的，随便点开一个，可以看到 aapt
2. 可以将 aapt 路径添加到环境变量中，也可以直接在 aapt 所在目录下启动命令行工具
3. 在命令行工具中输入以下命令获取 apk 信息，比如要获取的 apk 是 app-debug.apk，则运行：`aapt dump badging app-debug.apk`。当然，apk 需要写全路径

# Q 11. Program type already present: BuildConfig

检查各个模块的 `AndroidManifest.xml` 里的 package 是不是有一样的

# Q12. java.io.EOFException: Unexpected end of ZLIB input stream

原因：gradle 版本问题
修改以下两个地方：
1. 工程的 `build.gradle` 文件中将 gradle 版本号改为可以正常运行的版本号
  ```
  dependencies {
      // 改成可以正常运行的版本号
      classpath 'com.android.tools.build:gradle:3.1.2' 
  }
  ```
2. 工程的 `gradle -> wrapper -> gradle-wrapper.properties` 文件中的 gradle 版本也改成可以正常运行的版本号
  ```
  distributionBase=GRADLE_USER_HOME
  distributionPath=wrapper/dists
  // 改为可以正常运行项目的 gradle 版本号
  distributionUrl=https\://services.gradle.org/distributions/gradle-5.6.4-all.zip
  zipStoreBase=GRADLE_USER_HOME
  zipStorePath=wrapper/dist
  ```
# Q13. adb connect 时报错：由于目标计算机积极拒绝，无法连接

1. 使用 USB 数据连接手机和电脑
2. 在 PC 端运行 cmd 命令：`adb devices`，能看到已经连接的设备
3. 输入 `adb tcpip 8888`
4. 断开手机和电脑的 USB 连接
5. 假设此时手机的 ip 是 192.168.43.68，则在 PC 端 cmd 输入 `adb connect 192.168.43.68:8888`，即可连接成功

# Q14. Error:java.lang.NullPointerException(no error message)

工程代码切到大半年前的一个 commit，那个时候编译是没有问题，但是现在代码切回去编译就会提示 `Error:java.lang.NullPointerException(no error message)`。因为之前编译是没有问题的，所以肯定不是代码的问题，应该就是编译环境和依赖的版本导致的。

现在使用的 AndroidStudio 的版本是 4.1 的，那就把 gradle 也同步升级一下，在工程目录下的 `build.gradle` 里面将 gradle 版本升级到 4.1.2：
`classpath 'com.android.tools.build:gradle:4.1.2'`

然后工程目录的 `gradle` 文件夹下的 `` 升级  gradle 版本：
`distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-bin.zip`

重新编译工程就可以了。