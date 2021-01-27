title: Android Bluetooth
date: 2021-01-27 16:27:37
tags:
- android
- bluetooth

---
# Q1. 连续两次 writeCharacteristic 却只有第一个的回调
在进行蓝牙数据读写时会设置一个状态表示正在忙碌，等待数据回调之后该状态会设置成空闲，只有空闲的时候，下一个蓝牙数据读写操作才可以进行。所以，当连续多次 writeCharacteristic 时，可能只回调了一次 onCharacteristicWrite，原因就在于上一次的 write 操作还没有回调，蓝牙处于 busy 状态，没有执行更多的蓝牙操作。

参考：http://a1anwang.com/post-18.html