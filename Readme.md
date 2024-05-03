# 小新pad pro 2022 骁龙版oem解锁笔记记录

注：本文仅为个人折腾的经历，用于给自己做备忘录使用，亦可供其他人进行参考，但本人不为任何人解锁过程中的误操作负责，也不保证今后联想会否在解锁上增加任何限制，本人亦不提供任何的技术支持。

注2：解锁操作会导致机器失去质保，请谨慎进行！



## 设备信息

- 型号：`xiaoxin pad pro 2022 骁龙版`
- `CPU`：`骁龙870`
- 设备名称：`TB138FC`
- 原厂固件：`TB138FC_CN_OPEN_USER_Q00024.0_S_ZUI_14.0.625_ST_220901_qpst`
- 原来的系统：`ZUI15`



## 预操作

去ZUI官网申请设备的`sn.img`的解锁镜像，由于ZUI官网目前(本文撰写日期是2024.5.3)每天都有限额，所以这一步操作是需要先进行的。

简单点说就是注册一个联想账号，同时使用`adb`命令，使得机器进入`bootloader`模式：

```cmd
adb reboot bootloader
```

然后在使用`fastboot`命令，查询设备的序列号：

```cmd
fastboot devices
```

拿到序列号之后，将序列号和自己的邮箱填入`ZUI`官网申请解锁的表单中，如果成功申请的话，联想可能会在几分钟之内将`sn.img`的链接发送到你的邮箱（如果你看到这里，联想已经取消了这种解锁方式的话，请恕本文无法帮助到你）。

如果`fastboot`或者`adb`命令无法显示设备的话，请在Windows的设备管理器中查看是否没有安装驱动，如果没有的话请下载并安装好`adb`和`fastboot`的驱动，这里不提供链接的原因是我并不能保证我贴上来的链接是安全的，并且正处于互联网高速发展的时代，网民更需要有搜索的技能。



## 情况介绍

该平板是我在二手平台收的机器，自带的系统是`ZUI15`，使用上动画挺流畅的，视频观看也没有太大的问题，但是操作起来还是有点小问题的，而且我就是不喜欢这种`UI`，所以才决定解锁和刷机。



## 正常步骤

1. 首先设备要进入开发者模式，打开`oem`解锁的开关以及`USB`调试开关；
2. 下载`sn.img`，下载`platform-tools`，为了方便起见可以把`sn.img`放到`platform-tools`的解压目录中；
3. 在`platform-tools`的目录下打开`cmd`，输入`adb reboot bootloader`，注意是`bootloader`而不是`fastboot`，就是说，`adb reboot bootloader`进入的是我们以前说的`fastboot`模式，而`adb reboot fastboot`进入的是`fastbootd`模式，说是用户空间的`fastboot`模式，`fastbootd`能够用于刷写`system`分区，也就是对于我自己的需求来说，我需要使用`fastbootd`将`GSI`镜像刷写到`system`分区中，具体可以看`fastbootd`模式的作用；
4. 进入`bootloader`后，执行`fastboot flash unlock sn.img`，然后看到提示刷入成功后，使用平板的音量键调节，控制平板`restart to bootloader`，按下电源键重新启动，此时执行`fastboot oem unlock-go`，应该就能完成解锁了。



## 遇到的问题

无法刷入`unlock`，具体表现为执行以下命令：

```cmd
fastboot flash unlock sn.img
```

显示：`unlock partition size 0`以及刷入`failed`的提示，这个问题，我查了好久，最后确定是`ZUI15`的问题，解决的方法是刷入`ZUI14`的原厂固件，原厂固件的名称已经贴在文章的上面，同样我不会提供链接；

这里本来有一个问题是：如果要进行降级的话，就要先解锁，否则平板就会变成软砖，无法进入系统，会最终进入到`bootloader`，但是这无伤大雅，因为要进行解锁就必须降级以及进入到`bootloader`使用`fastboot`命令刷入`sn.img`。



**注意：在还能够进入系统时，请务必保证开发者选项中的`OEM解锁`开关是处于开着的状态再进行降级操作！**

**注意：在还能够进入系统时，请务必保证开发者选项中的`OEM解锁`开关是处于开着的状态再进行降级操作！**

**注意：在还能够进入系统时，请务必保证开发者选项中的`OEM解锁`开关是处于开着的状态再进行降级操作！**



### 降级

降级是使用`QSPT`中的`QFIL`工具进行的，需要注意的一点是，第一次打开工具需要改一下配置，将`Device Type`修改为`ufs`（仅对于`xiaoxin pad pro 2022 骁龙版`来说，其他型号我没有做了解），勾选`Reset After Download`，`Download Protocal`选择`0-Sahara`。步骤如下：

1. 将平板进入`EDL`模式，进入方式(能够进入正常系统时)：

   ```cmd
   adb reboot edl
   ```

   无法正常进入系统或者在关机状态下想进`edl`：先按住音量`+`键，先插入`USB`；

2. 在`QFIL`界面选择端口为`9008`设备的端口（正常来说会自动识别，若无法识别到`9008`端口请下载并安装`9008`驱动）；

3. `Select Build Type`选择`Flat Build`；

4. `Select Programmer`选择解压`ZUI14`原厂固件后的`image`目录下的`prog_firehose_ddr.elf`；

5. `Rawprogram and Patch`的`load XML`第一次弹窗选择全部的`XML`文件，第二次弹窗也选择全部的`XML`文件；

然后直接点击`Download`，等待进度条跑完，跑完进度条平板会自动重启，然后就会因为平板设备没有解锁就降级导致无法引导系统，最终就会进入到`bootloader`模式，接下来就按照[正常步骤](##正常步骤)的流程走，完成解锁之后，再使用`QFIL`刷一遍`ZUI14`原厂固件，等待设备重启就行了。



## 参考

[xda-keyman91的回答-Oct 25, 2023](https://xdaforums.com/t/tb138fc-sd870-xiaoxin-pad-pro-2022-sharing-and-support.4498469/page-18#post-89125001)

[xda-porphyrin与LakersDroid的对话](https://xdaforums.com/t/tb138fc-sd870-xiaoxin-pad-pro-2022-sharing-and-support.4498469/page-28)

