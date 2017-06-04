---
layout: post
title: "More IN Arch"
excerpt: "what should we do after arch setup"
data: 2017-06-04 15:44 +08:00
tags: [Linux]
---

# arch的优化

## 必装软件类

1. 图形界面服务xorg-server
    pacman -S xorg-server xorg-server-utils xorg-xinit
    
2. 显卡驱动类
    pacman -S xf86-video-intel  //intel显卡
    
    pacman -S xf86-video-nouveau    //英伟达

    pacman -S nvdia     //N卡
    
    pacman -S xf86-video-ati //A卡
    
3. 桌面管理器

这有很多种类,可以去wiki上去找[ArchWiki](https://wiki.archlinux.org/index.php/Window_manager)
我选择了gnome和herbstlutfwm,选gnome是因为用习惯了,用herbstlutfdwm是因为平铺窗口码代码效率会高点

    pacman -S gnome gnome-extra
    
    yaourt -S herbstlutfwm //这个好像只在aur上有,yaourt就可以了
    
4. 登录管理器 

    可以选择gdm,比较稳定,但是我选了lightdm,从antergos中看到挺漂亮的
    
    pacman -S lightdm
    
    记得添加到开机启动 
    
    systemctl enable lightdm
    
5. 常用的软件
  这要是不装下了源码可没法编译,赶紧装
  
  pacman -S 7zp file-roller unrar
 
   pacman -S  evince gedit thunderbird
  
   文档阅读工具,文档编辑,下载工具
   
6. 创建普通用户

    useradd -m -G wheel *****
    
    pacman -S sudo
    
    vim /etc/sudoers
    修改#%wheel ALL=(ALL)ALL
    
7. 中文字体
    pacman -S wqy-microhei  //主要
    
    pacman -S ttf-dejavu wqy-zenhei 
    
8. yaourt

    yaourt 是archlinux方便使用的关键部件之一,但是默认是没有装上的,所以装完系统就要赶紧把它装上
    添加源更新即可
    
    建议使用清华的,感觉最快
    
    编辑/etc/pacman.conf文件 在末尾添加以下即可
```
# TUNA mirror of Tsinghua University
[archlinuxcn]
SigLevel = Optional TrustAll
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch # both IPv4 & IPv6
# Server = http://mirrors.6.tuna.tsinghua.edu.cn/archlinuxcn/$arch # only IPv6
# Server = http://mirrors.4.tuna.tsinghua.edu.cn/archlinuxcn/$arch # only IPv4
```
    记得同步更新
    pacman -Ryy
    pacman -Syu yaourt
    
9. 输入法

    fcix 的好处是有sogou拼音输入法,但是个人总感觉fcix跟gnome有点不协调,改装了ibus
    
    pacman -S ibus
    
    pacman -S ibus-pinyin 或 pacman -S ibus-libpinyin
    建议后者,因为前者好像停止维护了
    
    另外按照网上的说法要在~/.bashrc 中加入
    
    export GTK_IM_MOUDLE=ibus
    export XMODIFIERS=@im=ibus
    export QT_IM_MOUDLE=ibus
    
    但是个人感觉占用bash的不太好,放到了~/.xprofile中,结果是一样的,随便选择
    当然最后要在设置中启用ibus

10. 浏览器
    yaourt -S google-chrome //AUR 里的
    
11. 文件系统的支持
    pacman -S ntfs-3g dosfstool
    
## 美化
因为是按照antergos来定做的,最好加上antergos的源,以免有些包找不到
```
[antergos]
Server = http://mirror.bjtu.edu.cn/cinnarch/$repo/$arch
SigLevel = Optional TrustAll
    pacman -Syy
    pacman -S antergos-base //安装antergos-base组
    正常的话会有/etc/pacman.dantergos-mirrorlise 
```
1. tweak-tool
    如果之前装了gnome-extra,这个包已经被安装了,没有也没事,再装下就好了
    pacman -S gnome-tweak-tool

2. 图标包
  还是之前看得antergos比较好看,就用他的主题好了
  pacman -S numix-cicle-icon-theme-git
  
  在tweak-tool 启用就可以了
3. 主题
    pacman -S numix-frost-themes numix-icon-theme-square
    
    pacman -S antergos-wallpapers
壁纸默认放在/usr/share/antergos/wallpapers
设置tweak-tool

4. 拓展

    https://extensions.gnome.org 右上角点install即可
    注意只能用gnome自带的浏览器,反正我用chrome是没有install的

    
## 优化

1. ahci优化性能
    achi默认是没有在内核中载入的,我们要帮他载入
修改/etc/mkinitcpio.conf,添加ahci到MOUDLE
    MOUDLES="ahci"
mkinitcpio -p linux 重建内核

dmesg中会看到ahci和NCQ启用

2. 启动优化

可以用systemd-analyzed 看时间
systemd-analyzed blame看详细
systemd-analyzed plot>plot.svg 更详细

然后根据实际情况自己处理就好了
 
最后可以用来展示自己的成果哦
screenfetch

    
     
   
  
   
   
