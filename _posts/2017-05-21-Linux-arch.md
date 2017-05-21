---
layout: post
title: Arch install
excerpt: "Memo"
date: 2017-05-21 11:44:11 +08:00
tags: [Linux]
---
# Install Arch according  to the official      


大学之前有断断续续用过Deep,elementary,Ubuntu,Debian,fedora,redhat等等,
大一期间觉得好多软件没有windows上用的舒服,就换回了windows,这期间恰逢Windows漏洞高发期,同时也发现windows上敲代码确实不是很顺手,于是对linux日思夜想,决定另买一台笔记本做linux,敲代码用.

结合以前的经验,和对linux的理解,决定个人桌面级别决定弃用centos这些注重服务器的系统这,我决定入arch流,

>　Arch Linux是起源于加拿大的一份致力于使用简单、系统轻量、软件更新速度快的GNU/Linux发行版。
>　创始人Judd Vinet出于对Debian以及Red Hat的包管理器不满，以及受CRUX影响而创立。
>　最初针对i686，但是如今对x86_64也支持良好，而且还派生出了针对ARM平台的Arch Linux ARM以及针对HURD内核的Arch Hurd（发展缓慢）。
>[http://baike.baidu.com/item/archlinux](http://baike.baidu.com/item/archlinux)



本文参照(原文为英文版,这里就当时翻译了)
[https://wiki.archlinux.org/index.php/Installation_guide](https://wiki.archlinux.org/index.php/Installation_guide)

很多人认为安装arch是一件很困难的事情,这跟基础有一定关系,但是不大,按照引导,相信你也能做出来

没有图形界面的引导：Arch只给我们提供了一个最小的环境，所有的安装操作都需要在命令行中完成，这对于不习惯命令行操作的人来说是最难以跨越的一个坎。

许多发行版之所以可以流行开来就是因为他们提供了友好的、流程化的安装过程，这帮很多人解决了学习Linux的第一步：安装一个Linux。

ArchLinux或者是Linux的优点就不在这里多说了，我相信打开这篇教程的同学一定可以从这样的过程中得到很多。

下面就正式开始我们的教程。

安装教程
前期准备

安装介质

在安装之前我们先要准备一个安装介质，在这里只推荐U盘作为安装介质。

到https://www.archlinux.org/download/页面下方的中国镜像源中下载archlinux-**-x86_64.iso这个iso文件。

如果是Linux系统下制作安装介质，推荐使用dd命令，教程：

http://blog.topspeedsnail.com/archives/404

如果是windows系统下制作安装介质，推荐使用usbwriter这款轻量级的工具，下载链接：

https://sourceforge.net/projects/usbwriter/

磁盘准备

我们需要有一块空闲的磁盘区域来进行安装，这里的空闲指的是没有被分区的空间。下面来介绍如何准备这块空间。

在windows下空出一块分区来安装：利用windows自带的磁盘管理工具就可以：

右击windows图标，在弹出菜单中选择磁盘管理（其他版本的windows请自行找到打开磁盘管理的方式）：



右击想要删除的分区，选择删除卷（注意这步之后这个分区的所有数据将会丢失）：



在linux下分出一块区域安装：使用fdisk进行，教程请见链接中的删除分区：

http://www.liusuping.com/ubuntu-linux/linux-fdisk-disk.html

空闲的磁盘（新磁盘）：不需要进行任何操作。

U盘安装

下面的过程实际上都在刚刚准备好的U盘启动介质上的Linux系统下进行，所以启动时都应该选择U盘。

设置启动顺序

这一步在不同品牌的电脑上都不一样，所以需要大家自行搜索自己电脑品牌+启动顺序这个关键词来进行设置。

例如我的电脑搜索神舟 启动顺序可以得到如下的结果：

https://zhidao.baidu.com/question/170954184.html

一般来说现在的主板都可以不用进入BIOS而快速地切换启动顺序，只要找到相应的快捷键就可以了。

进入U盘下的Linux系统

按上一步设置好启动顺序，启动之后会看到如下界面：



如果直接进入windows，请检查启动顺序是否设置成功，U盘是否在制作启动介质时成功写入。

如果没有看到这个界面，请检查U盘是否制作成功，如果多次遇到问题可以考虑换一个U盘。

选择第一个选项。

这时Arch开始加载，你将会看到屏幕显示如下内容：



加载完成后你将会进入一个有命令提示符的界面：



如果出现FAIL或是其他错误信息导致无法启动请自行搜索错误信息来获得解决方法。

这就是Linux的终端界面了，接下来我们将通过在这个界面执行一系列命令来将Arch安装到我们的磁盘上。

下面进行的过程是按照官方Installation guide为依据进行的，出现的任何问题都可以到链接中的相应部分查找原文找到解决方式。

检查引导方式

目前的引导方式主要分为EFI引导+GPT分区表与BIOS(LEGACY)引导+MBR分区表两种，几乎比较新的机器都采用了EFI/GPT引导的方式。关于这部分的内容如果有兴趣可以通过这个链接进行了解：

http://www.chinaz.com/server/2016/1017/595444.shtml

如果你不知道自己的引导方式，请在命令提示符下执行以下命令：

1
ls /sys/firmware/efi/efivars
这里的ls是命令，空格后面的一串为路径，作为ls命令的参数。ls命令的作用是显示路径目录下的所有的文件（夹）。

如果你对命令行下的常用操作（TAB补全、取消命令等）不熟悉，请先学习了解下面部分实用的快捷键或命令：

Tab键 命令行自动补全。键入命令或文件名的前几个字符，然后按 [Tab] 键，它会自动补全命令或显示匹配你键入字符的所有命令

↑(Ctrl+p) 显示上一条命令

↓(Ctrl+n) 显示下一条命令

Ctrl-C: 终止当前正在执行的命令

输入命令并回车执行后，如果提示

1
ls: cannot access '/sys/firmware/efi/efivars': No such file or directory
表明你是以BIOS方式引导，否则为以EFI方式引导。现在只需要记住这个信息，之后会用到。

联网

arch并不能离线安装，因为我们需要联网来下载需要的组件，所以我们首先要连接网络。

如果你是有线网并且路由器支持DHCP的话插上网线后应该已经是联网状态了，可以执行以下命令来进行判断：

1
ping www.baidu.com
如果可以看到类似下面的内容就说明连上了网络：



再次提示用快捷键Ctrl-C可以终止当前正在执行的命令

如果你是无线网，请执行以下命令：

1
wifi-menu
这是一个实用的命令行下联网工具，有字符形式的图形化界面，利用它可以方便地联网，如果它没能起作用，需要进入以下页面查找解决方式：

https://wiki.archlinux.org/index.php/Wireless_network_configuration

连接以后同样可以通过上面的ping命令来进行测试。

更新系统时间

执行如下命令：

1
timedatectl set-ntp true
正常情况下这样的命令并没有输出，所谓没有消息就是最好的消息

分区与格式化

特别注意：涉及到分区与格式化的操作要格外注意，命令在回车之前请再三确认知道自己在做什么，并且没有输错命令，否则将会来带来数据的丢失！如果有需要在操作之前请备份重要的数据。

但是我们也并不要过于惧怕分区与格式化过程，正确操作的情况下不会对你其他数据产生任何影响。

查看目前的分区情况

执行命令：

1
fdisk -l
以我的电脑为例：



可以看到我的一块238.5g的硬盘(/dev/sda就代表这块硬盘)，下面列出了/dev/sda*这三个分区，/dev/sda3是我存活下来的家目录，可以看到它的类型为Linux分区。注意看Start与End的数值，这个数值代表扇区号，可以理解成硬盘被划分成了一个个小单元，可以直观地看出来在/dev/sda2的End与/dev/sda3的Start之间空出了一大块未分配的空间，接下来我们将分配这块区域。

如果你是BIOS/MBR方式引导，跳过下面创建一个引导分区的步骤。
如果你是EFI/GPT方式引导，并且同时安装了其他系统，那么你应该可以在分区列表中发现一个较小的并且类型为EFI的分区，这是你的引导分区，请记下它的路径（/dev/sdxY)备用，跳过下面创建一个引导分区的步骤。
如果你是EFI/GPT方式引导，但是没有这个较小的并且类型为EFI的引导分区（这种情况一般只会出现在新的硬盘），那么你需要先创建一个引导分区。
创建一个引导分区（仅上面所列的第三种情况需要进行这步）

执行命令：

1
fdisk /dev/sdx （请将sdx替换成你要操作的磁盘如sdb sdc等）
下面你就进入了fdisk的操作环境， 输入m并回车可以查看各命令的作用。

如果你是一块全新的硬盘，输入g来创建一个全新的gpt分区表。

输入n创建一个新的分区，首先会让你选择起始扇区，一般直接回车使用默认数值即可，然后可以输入结束扇区或是分区大小，这里我们输入+512M来创建一个512M的引导分区。

这时我们可以输入p来查看新创建的分区。

输入t并选择新创建的分区序号来更改分区的类型，输入l可以查看所有支持的类型，输入ef更改分区的类型为EFI。

输入w来将之前所有的操作写入磁盘生效，在这之前可以输入p来确认自己的分区表没有错误。

输入以下命令来格式化刚刚创建的引导分区：

1
mkfs.fat -F32 /dev/sdxY （请将的sdxY替换为刚创建的分区）
现在引导分区就创建好了。

创建根分区

输入命令：

1
fdisk /dev/sdx （请将sdx替换成你要操作的磁盘如sdb sdc等）
如果你是一块全新的硬盘，输入o来创建一个新的MBR分区表。

输入n创建一个新的分区，首先会让你选择起始扇区，一般直接回车使用默认数值即可，然后可以输入结束扇区或是分区大小，如果我们想要使创建的分区完全占满空闲的空间，可以直接回车使用默认结束扇区。

这时我们可以输入p来查看新创建的分区。

输入w来将之前所有的操作写入磁盘生效，在这之前可以输入p来确认自己的分区表没有错误。

输入以下命令来格式化刚刚创建的根分区：

1
mkfs.ext4 /dev/sdxY （请将的sdxY替换为刚创建的分区）
这是我的分区过程供参考：





挂载分区

执行以下命令将根分区挂载到/mnt：

1
mount /dev/sdxY /mnt （请将sdxY替换为之前创建的根分区）
如果你是EFI/GPT引导方式，执行以下命令创建/boot文件夹并将引导分区挂载到上面。BIOS/MBR引导方式无需进行这步。

1
2
mkdir /mnt/boot
mkdir /dev/sdxY /mnt/boot （请将sdxY替换为之前创建或是已经存在的引导分区）
选择镜像源

因为从这步开始，需要进行一些编辑配置文件的操作，所以需要掌握一些命令行下非常著名的一款编辑器Vim的基本操作，在这里推荐学习下面这个链接中的存活部分，可以完成编辑、复制粘贴与保存工作即可。

http://coolshell.cn/articles/5426.html

镜像源是我们下载的软件包的来源，我们需要根据自己的地区选择不同的源来加快下载的速度。

执行以下命令，用Vim来编辑/etc/pacman.d/mirrorlist这个文件

1
vim /etc/pacman.d/mirrorlist
提示：输入路径时可以用Tab键补全



找到标有China的镜像源，normal模式下按下dd可以剪切光标下的行，按gg回到文件首，按p将行粘贴到文件最前面的位置（优先级最高）。

当然也可以直接手工输入。

这里推荐使用清华、浙大源：

1
2
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
最后记得用:wq命令保存文件并退出。

安装基本包

下面就要安装最基本的ArchLinux包到磁盘上了。这是一个联网下载并安装的过程。

执行以下命令：

1
pacstrap /mnt base base-devel
根据下载速度的不同在这里需要等待一段时间，当命令提示符重新出现的时候就可以进行下一步操作了。

配置Fstab

生成自动挂载分区的fstab文件，执行以下命令：

1
genfstab -L /mnt >> /mnt/etc/fstab
由于这步比较重要，所以我们需要输出生成的文件来检查是否正确，执行以下命令：

1
cat /mnt/etc/fstab


如图，可以看到/dev/sda4被挂载到了根分区。

/dev/sda3是我之前存活下来的家目录被挂载到了/home目录（你们没有这条）。

如果是EFI/GPT引导的还应该有引导分区被挂载到/boot目录。

Chroot

Chroot意为Change root，相当于把操纵权交给我们新安装（或已经存在）的Linux系统，执行了这步以后，我们的操作都相当于在磁盘上新装的系统中进行。

执行如下命令：

1
arch-chroot /mnt
这里顺便说一下，如果以后我们的系统出现了问题，只要插入U盘并启动， 将我们的系统根分区挂载到了/mnt下，再通过这条命令就可以进入我们的系统进行修复操作。

设置时区

依次执行如下命令设置我们的时区为上海并生成相关文件：

1
2
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc


设置Locale

设置我们使用的语言选项，执行如下命令来编辑/etc/locale.gen文件：

1
vim /etc/locale.gen
等等，怎么提示bash:vim:command not found了？

因为我们现在已经Chroot到了新的系统中，只有一些最基本的包（组件），这时候我们就需要自己安装新的包了，下面就要介绍一下ArchLinux下非常强大的包管理工具pacman，大部分情况下，一行命令就可以搞定包与依赖的问题。

安装包的命令格式为pacman -S 包名，pacman会自动检查这个包所需要的其他包（即为依赖）并一起装上。下面我们就通过pacman来安装一些包，这些包在之后会用上，在这里先提前装好。

执行如下命令：

1
pacman -S vim dialog wpa_supplicant
一路确认之后包就被成功装上了。



图中只安装了Vim和它的依赖。

安装好Vim以后，再次执行：

1
vim /etc/locale.gen
在文件中找到zh_CN.UTF-8 UTF-8 zh_HK.UTF-8 UTF-8 zh_TW.UTF-8 UTF-8``en_US.UTF-8 UTF-8这四行，去掉行首的#号，保存并退出。如图：





然后执行：

1
locale-gen


打开（不存在时会创建）/etc/locale.conf文件：

1
vim /etc/locale.conf
在文件的第一行加入以下内容：

1
LANG=en_US.UTF-8
保存并退出。

设置主机名

打开（不存在时会创建）/etc/hostname文件：

1
vim /etc/hostname
在文件的第一行输入你自己设定的一个myhostname

保存并退出。

编辑/etc/hosts文件：

1
vim /etc/hosts
作如下修改（将myhostname替换成你自己设定的主机名）

1
2
3
127.0.0.1	localhost.localdomain	localhost
::1		localhost.localdomain	localhost
127.0.1.1	myhostname.localdomain	myhostname


这里我设置的是viseator。

保存并退出。

设置Root密码

Root是Linux中具有最高权限帐户，有些敏感的操作必须通过Root用户进行，比如使用pacman，我们之前进行所有的操作也都是以Root用户进行的，也正是因为Root的权限过高，如果使用不当会造成安全问题，所以我们之后会新建一个普通用户来进行日常的操作。在这里我们需要为Root帐户设置一个密码：

执行如下命令：

1
passwd
按提示设置并确认就可以了。



或许有的人已经发现官方Wiki和一些其他教程资料中的命令是以#或$开头的，这两个符号就对应着命令行中的命令提示符，#代表以Root用户执行命令，$代表以普通用户执行命令，平时使用教程中的命令时应该注意这一点。

安装Intel-ucode（非IntelCPU可以跳过此步骤）

直接pacman安装：

1
pacman -S intel-ucode
安装Bootloader

经常听说很多人因为引导问题导致系统安装失败，多数是因为教程没有统一或是过时的教程引起的，这里只要按照步骤来其实是不难的。

这里我们安装最流行的Grub2。

首先安装os-prober这个包，它可以配合Grub检测已经存在的系统，自动设置启动选项。

1
pacman -S os-prober
如果为BIOS/MBR引导方式：

安装grub包：

1
pacman -S grub
部署grub：

1
grub-install --target=i386-pc /dev/sdx （将sdx换成你安装的硬盘）
注意这里的sdx应该为硬盘（例如/dev/sda），而不是形如/dev/sda1这样的分区。

生成配置文件：

1
grub-mkconfig -o /boot/grub/grub.cfg


如果你没有看到如图所示的提示信息，请仔细检查是否正确完成上面的过程。

如果为EFI/GPT引导方式：

安装grub与efibootmgr两个包：

1
pacman -S grub efibootmgr
部署grub：

1
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
生成配置文件：

1
grub-mkconfig -o /boot/grub/grub.cfg
提示信息应与上面的图类似，如果你发现错误，请仔细检查是否正确完成上面的过程。

接下来，你需要进行重启来启动已经安装好的系统，执行如下命令：

1
2
exit
reboot
注意这个时候你可能会卡在有两行提示的地方无法正常关机，长按电源键强制关机即可，没有影响。

关机后拔出U盘，启动顺序会自动以硬盘启动，如果一切顺利，那么你将会看到下面的界面：



启动时有可能会有输出信息显示在这里，直接回车就可以了。

输入root，再输入之前设置的密码，显示出命令提示符，恭喜你，你已经成功安装ArchLinux！


