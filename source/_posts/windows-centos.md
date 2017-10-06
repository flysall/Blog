---
title: 安装win10与centos7双系统注意事项回顾
date: 2017-10-04 21:02:32
tags: Linux
---
恰逢国庆长假，时间相对比较充裕，于是给自己的电脑装上了win10+centos7双系统。
<!-- more -->
想来大一快结束时我买回电脑至今，win10一直用得不错，所以我也没什么动力去折腾系统。大二下的时候用《鸟哥的Linux私房菜》学习过一段时间Linux，也只是在virtualBox虚拟机下运行，无奈电脑性能不够强劲，运行大型程序时卡得不行。这段时间打算把使用重心转移到Linux上面，于是给自己电脑装上了双系统，过程有点曲折，出了很多问题，差不多花了一个下午的时间才最终得以完美运行，看着不太美观的GNOME桌面心里还是挺感慨的，想当年刚买回来电脑时连分区都不会，这回第一次真机装系统确实有点虚，于是写下这篇博文记录一下安装过程中的**重要事项**。参考的教程链接入下：[安装 Windows 10 + Centos 7 双系统共存](http://xueliang.org/article/detail/20160912181035032)、[u盘安装centos7 /dev/root does not exist 导致无法安装解决方案。](http://m.blog.csdn.net/bajiudongfeng/article/details/47732377)
## 制作U盘启动
- 在windows下压缩卷后得到一块空闲的分区后不要对其分配盘符
- 用UltralSO制作U盘启动盘，要以管理员权限运行UltralSO
- 选择菜单栏里的 [启动] → [写入硬盘映像]，点击写入按钮后刚开始可能会显示无响应，这是正常的，耐心等待即可。
- **将得到的U盘启动重命名为一个简单的字符串，比如`DDD`**

## thinkpad调整BIOS引导顺序（能否成功极有可能是个概率事件）
- 重启电脑，按F12进入BIOS（从Boot Menu可以看到启动顺序，第一个是Windows Boot Manager, U盘启动是在第三个）
- 键入Tab进入App Menu，键入Enter进入Setup，依次进入Security-->Secure Boot，将Secure Boot设为Disabled。然后键入Esc返回上级界面，依次进入Startup-->UEFI/LegacyBoot，设为Both、LegacyFirst, 设置CSM Support为Yes。现在可以进入Boot菜单，启动顺序为**U盘第一， Windows Boot Manager第二**。F10保存退出。
- 经过上一步，不出意外的话应该就能进入centos7的安装引导界面。
{% asset_img centos.PNG centos安装引导界面 %}
- **键入e对安装进行修改，得到类似下面的一串字符`vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=CentOS\x207x20x86_64 rd.live.check quiet `,将`CentOS\x207x20x86_64`改为我们在第一步制作U盘启动盘时重命名的字符，即`DDD`，于是修改后的字符串如下`vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=DDD rd.live.check quiet`**，回车之后等待片刻，即可得到如下画面：
{% asset_img centos2.PNG centos安装界面 %}

## 安装centos
- 选择中文安装
- 安装时不要选择最小安装（虽然安装速度比较快，但是...)，选择GNOME桌面并选择一些基础软件和开发环境。
- 合理分区
- 耐心等待，可以去泡十杯咖啡。
- **安装完成后拔出U盘**，重启电脑，不出意外应该可以看到多重引导界面，可以选择启动centos或者windows