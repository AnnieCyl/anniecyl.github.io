title: Navigation
date: 2018-11-07 10:00:32
tags:
- android
- navigation

---
# 向上（Up） vs 返回（Back）

看 [Principles of Navigation](https://developer.android.com/topic/libraries/architecture/navigation/navigation-principles) 的时候，有点不明白 Up 和 Back 按钮的区别，查了相关资料以后搞明白了：
- Up 按钮用来在应用内，根据应用的逻辑**层级**进行导航，返回到上一个层级，如果某个屏幕已经是该应用的顶层了 (例如，应用的主页)，那不需要 Up 按钮，所以 Up 按钮不会退出应用。
- 系统的 Back 键则用于按照切换历史返回到之前的**屏幕**。这种导航基于是基于时间轴的，永远是返回到上一个界面视图，而不是基于应用的逻辑层级。

 ![up-vs-back](/img/2018-11-07-navigation/up-vs-back.jpg)

如果之前的屏幕就是逻辑层次的上一层，那么 Back 和 Up 的行为是一样的。不过和 Up 不同的是，Back 可能回到主屏幕或者其他的应用，Up 回到的屏幕总是在你的应用中。
