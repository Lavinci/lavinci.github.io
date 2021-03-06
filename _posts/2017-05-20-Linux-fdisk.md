---
layout: post
title: "Fdisk"
excerpt: "Partition table manipulator for Linux"
date: 2017-05-20 23:44:13 +08:00
tags: [Linux]
---
# Linux下的fdisk用法

Linux下的fdisk功能是极其强大的，用它可以划分出最复杂的分区，下面简要介绍一下它的用法：

对于IDE硬盘，每块盘有一个设备名：
对应于主板的四个IDE接口，

设备名依次为：/dev/hda,/dev/hdb,/dev/hdc,/dev/hdd等,

如果还有IDE Raid卡，则依次为：/dev/hde,/dev/hdf,/dev/hdg,/dev/hdh。

对于SCSI硬盘，则设备名依次为/dev/sda,/dev/sdb...等等


**fdisk的命令行用法为： fdisk 硬盘设备名**

进入fdisk后，首先键入'm'，即可显示fdisk全部菜单。

再键入'p',显示当前分区表状态。

键入'n',增加一个分区，然后会提示你选择分区类型(基本分区或扩展分区)，再选择分区号(1-4)。

- 注：每块硬盘最多可划分四个主分区(包括基本分区和扩展分区)
    - 其中：基本分区最多可划分四个，扩展分区最多可划分一个，但扩展分区内可再划分多个逻辑分区。选中你要建立的分区类型和分区号后，会提示输入起始柱面，从1开始；然后再输入终止柱面，此时可输入实际的柱面数，也可用"+分区尺寸"的方式输入，如：+1024M表示在起始柱面后加上1024M。主分区的设备名依次为：/dev/hda1,/dev/hda2,/dev/hda3,/dev/hda4，逻辑分区的设备名依次为：/dev/hda5,/dev/hda6,/dev/hda7...等等。

键入’d‘,删除分区，输入分区号即可删除。注意，删除扩展分区时，将会同时删除所有的逻辑分区。

键入’t‘，改变分区标志(这是Linux的fdisk最精华的部份!)，新建的分区默认标志是83(Linux Ext2)，你可以把它改为82(Linux 交换区)、或是'b'(FAT32)、'f'(FAT32 Extend,只限于扩展分区)、'86'(NTFS)。。。等几十种类型。这样一来，使用多操作系统的朋友们就可以用Linux的fdisk划分出你想要的所有分区了!

键入'a',切换分区激活开关。请注意：每键入一次，被选的分区就会在激活与非激活间变化一次，但你必须保证最后只有一个分区被激活。这时就用得到'p'命令了，被激活的分区上会有个'*'号。

除以上的几个命令外，还有其它几个，但不太常用。

最后，键入’w',你对分区所做的改变被写入硬盘；键入'q'，则放弃所有的修改。

灵活应用fdisk，还可以修复一些损坏的分区表，前提是你必须准确记住原有每个分区的起始柱面和终止柱面。

再附加一下对硬盘进行格式化的方法：
要把分区格式化成Linux Ext2格式，用： mkext2fs /dev/hda?
要把分区格式化成FAT32格式，用： mkfs.vfat /dev/hda?　 
附2:
指令：fdisk

用途：观察硬盘之实体使用情形与分割硬盘用。

使用方法：

　　　　　　一、在 console 上输入 fdisk -l /dev/sda ，观察硬盘之实体使用情形。

　　　　　　二、在 console 上输入 fdisk /dev/sda，可进入分割硬盘模式。

1.  输入 m 显示所有命令列示。
2.  输入 p 显示硬盘分割情形。
3.  输入 a 设定硬盘启动区。
4.  输入 n 设定新的硬盘分割区。
	1.  输入 e 硬盘为[延伸]分割区(extend)。
	2.  输入 p 硬盘为[主要]分割区(primary)。
5.  输入 t 改变硬盘分割区属性。
6.  输入 d 删除硬盘分割区属性。
7.  输入 q 结束不存入硬盘分割区属性。
8.  输入 w 结束并写入硬盘分割区属性

eg:

格式化与分区

　　hd--IDE设备 sd--SCSI设备

　　fdisk -l /dev/sda 查看第一块硬盘分区情况

　　fdisk /dev/sdb 给第二块硬盘分区

　　command acton (m for help)：m #显示命令列表

　　a-设置可引导标志;b-设置卷标; d-删除一个分区; n-新建分区

　　p-显示分区信息; v-校验分区表;q-不存盘退出;w-存盘退出;t-改变分区类型

　　command acton (m for help)：n 新建分区

　　command action

　　e extended #扩展分区

　　p primary partition (1-4) #主分区

　　p #创建主分区

　　partition number (1-4):1 #创建第一个主分区

　　first cylinder (1-522,default 1):1 #起始柱面(第一个分区始终为1)

　　last cylinder or +size or +sizeM or +siezK(1-522,default 522): 10 #截止柱面(若522则整个硬盘分给了一个区)此分区大小是系统按照柱面大小自动计算出来的

　　command acton (m for help)：n

　　command action

　　e extended

　　p primary partition (1-4)

　　p

　　partition number (1-4):2 #创建第二个主分区

　　first cylinder (11-522,default 11):11

　　last cylinder ...(11-522,default 522): +100M #自定义分区大小

　　command acton (m for help)：n

　　command action

　　e extended

　　p primary partition (1-4)

　　e #创建扩展分区，注意一个磁盘只能创建一个扩展区

　　partition number (1-4):3

　　first cylinder (28-522,default 28):28

　　last cylinder ...(28-522,default 522):522 #将剩余空间全部分给扩展分区

　　扩展分区是不能直接使用的，必须在其上创建逻辑分区!

　　command acton (m for help)：n

　　command action

　　l logical (5 or over) #逻辑分区

　　p primary partition (1-4)

　　l

　　first sylinder (28-255,default 28):28 #在扩展分区里建逻辑分区

　　last cylinder ...(28-522,default 522):522 #柱面用尽，等于说只建一个逻辑分区

　　command acton (m for help)：w #保存退出

　　转换分区类型：

　　command acton (m for help)：t #转换分区类型

　　partition number (1-4):2 #选择第二个主分区

 　hex code (type L to list codes):82 #按L可列出分区类型所对应的编码

    可以使用 “partprobe” 命令，重新探测磁盘中分区清空,     #partprobe  /dev/sdb

　　格式化与挂载: (挂载目录可以自行创建也可指定存在的空目录)

　　mksf.ext3 /dev/sdb1 把第二块硬盘的第一个主分区格式化为ext3

　　mkswap /dev/sdb2 初始化swap区，此区不可格式化。

　　mount /dev/sdb1 /mnt/d #将第一个分区挂载到d这个目录

　　重启后自动挂载：vi /etc/fstab

　　添加：/dev/sdb1 /mnt/d ext3 default 0 0

