---
layout: post
title: "Pacman"
excerpt: "Memo"
date: 2017-06-05 10:33
tags: [Linux]
---

# pacman的备忘

pacman软件包管理器是 Arch Linux 的一大亮点。它将一个简单的二进制包格式和易用的构建系统结合了起来(参见makepkg和ABS)。不管软件包是来自官方的 Arch 库还是用户自己创建，pacman 都能方便得管理。
pacman 通过和主服务器同步软件包列表来进行系统更新。这种服务器/客户端模式可以使用一条命令就下载/安装软件包，同时安装必需的依赖包。
pacman 用 C 语言编写，使用 .pkg.tar.xz 打包格式。


## 安装软件包

注意: 软件包通常有很多可选依赖， 它们为软件提供额外功能， 并不强制要求安装它们。 安装软件时, pacman 将会输出它的可选依赖, 但是这个输出不会在 pacman.log中；当你想浏览已安装软件的可选依赖时可以使用pacman -Si ，得到关于可选依赖的简短描述。
警告: 在Arch下安装软件包时，未更新系统前，不要更新软件包数据库（例如，可能出现某软件包不再出现在官方库）。操作时，应使用pacman -Syu package_name, 而不要使用（pacman -Sy package_name），否则可能会有依赖问题。参见 System maintenance (简体中文)#不支持部分升级 和 BBS#89328.
安装指定的包

安装或者升级单个软件包，或者一列软件包（包含依赖包），使用如下命令：
> pacman -S package_name1 package_name2 ...

用正则表达式安装多个软件包：
> pacman -S $(pacman -Ssq package_regex)

有时候在不同的软件仓库中，一个软件包有多个版本（比如[extra]和[testing]）。可以选择一个来安装：
> pacman -S extra/package_name

安装多个含有相似名称的软件包，而并非整个包组或全部匹配的软件包； 例如，plasma:
> pacman -S plasma-{desktop,mediacenter,nm}

当然，可以多层扩展，并不作限制：
> pacman -S plasma-{workspace{,-wallpapers},pa}

## 安装包组

一些包属于一个可以同时安装的软件包组。例如，运行下面的命令
> pacman -S gnome

会提醒用户选择 gnome 内需要安装的包。
有的包组包含大量的软件包，有时用户只需其中几个。除了逐一键入序号外，pacman 还支持选择或排除某个区间内的的软件包：
Enter a selection (default=all): 1-10 15
这将选中序号 1 至 10 和 15 的软件包。而
Enter a selection (default=all): ^5-8 ^2
将会选中除了序号 5 至 8 和 2 之外的所有软件包。

想要查看哪些包属于 gnome 组，运行：
> pacman -Sg gnome


## 删除软件包

删除单个软件包，保留其全部已经安装的依赖关系
> pacman -R package_name

删除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系：
> pacman -Rs package_name

要删除软件包和所有依赖这个软件包的程序:
警告: 此操作是递归的，请小心检查，可能会一次删除大量的软件包。
> pacman -Rsc package_name

要删除软件包，但是不删除依赖这个软件包的其他程序：
> pacman -Rdd package_name

pacman 删除某些程序时会备份重要配置文件，在其后面加上*.pacsave扩展名。-n 选项可以避免备份这些文件：
> pacman -Rn package_name
注意: pacman 不会删除软件自己创建的文件(例如主目录中的 .dot 文件不会被删除。

## 升级软件包

警告: Arch 只支持系统完整升级，详细参见不支持部分升级和安装软件包。
建议所有用户都 经常性的更新系统。在论坛提问题的时候，一般假定您的系统已经升级到最新状态。
建议升级前访问 Arch Linux 主页查看最新消息（或订阅 RSS，或订阅 arch-announce 邮件列表，或关注 @archlinux。如果升级需要不寻常的用户操作介入时（无法简单地按照 pacman 的输出信息处理），以上信息总会给出合适的方法。有时候系统更新会出现问题并需要进行一些手动调整，请不要在重要任务前进行系统更新。
一个 pacman 命令就可以升级整个系统。花费的时间取决于系统有多老。这个命令会同步非本地(local)软件仓库并升级系统的软件包：
> pacman -Syu

pacman 是强大的软件包管理工具，但是不会做所有的事情。遵循Arch 之道，用户需要负责维护自己的系统。执行系统升级时，请务必阅读 pacman 输出的所有信息。如果用户修改过的配置文件需要更新，新的配置文件会被保存为 .pacnew 文件以免覆盖了用户的配置。pacman 会提醒用户合并新旧文件。这些文件需要手动干预。在升级或删除软件包后，应该立即着手处理。参见 Pacnew 和 pacsave 文件。
提示：pacman 输出会记录到 /var/log/pacman.log。
你可以使用日志阅读工具，如wat-gitAUR来搜索pacman的日志。
如果遇到问题，无法按照给出方法解决。请搜索下论坛，很有可能已经有人遇到并解决了。

## 查询包数据库

pacman 使用 -Q 参数查询本地软件包数据库。：
> pacman -Q --help

使用 -S 参数来查询远程同步的数据库。参见：
> pacman -S --help

pacman 可以在包数据库中查询软件包，查询位置包含了软件包的名字和描述：
> pacman -Ss string1 string2 ...

有时，-s的内置正则会匹配很多不需要的结果，所以应当指定仅搜索包名，而非描述或其他子段，下面的命令就会返回很多不必要结果:
> pacman -Ss '^vim-'

要查询已安装的软件包：
> pacman -Qs string1 string2 ...

显示软件包的详尽的信息：
> pacman -Si package_name

查询本地安装包的详细信息：
> pacman -Qi package_name

使用两个 -i 将同时显示备份文件和修改状态：
> pacman -Qii package_name

要获取已安装软件包所包含文件的列表：
> pacman -Ql package_name

未安装的软件包使用 pkgfile。
检查软件包安装的文件是否都存在：
> pacman -Qk package_name

两个参数k将会执行一次更彻底的检查。 查询数据库获取某个文件属于哪个软件包：
> pacman -Qo /path/to/file_name

要罗列所有不再作为依赖的软件包(孤立orphans)：
> pacman -Qdt

要罗列所有明确安装而且不被其它包依赖的软件包：
> pacman -Qet

要显示软件包的依赖树：
> pactree package_name

检查一个安装的软件包被那些包依赖，可以使用 pkgtoolsAUR中的whoneeds:
> whoneeds package_name

或者pactree中使用-r:
> pactree -r package_name

更多信息查看pacman tips。

## 数据库结构
pacman数据库通常位于 /var/lib/pacman/sync. 对于每一个在/etc/pacman.conf中指定的软件仓库， 这里都有一个一致的数据库。数据库文件夹里每个tar.gz文件都包含着一个仓库的软件包信息。例如which 包:
```
% tree which-2.20-6 	
which-2.20-6
|-- depends
`-- desc
```
这个 depends 项列出了该软件的依赖包， 而desc有该包的介绍，例如文件大小和MD5值 。

### 清理软件包缓存
pacman 将下载的软件包保存在 /var/cache/pacman/pkg/ 并且不会自动移除旧的和未安装版本的软件包，因此需要手动清理，以免该文件夹过于庞大。
使用内建选项即可清除未安装软件包的缓存：
> pacman -Sc

警告:
仅在确定当前安装的软件包足够稳定且不需要降级时才执行清理。pacman -Sc仅会保留软件包的当前有效版本，旧版本的软件包被清理后，只能从其他地方如 Arch Linux Archive (简体中文)中获取了。
pacman -Scc 可以清理所有缓存，但这样 pacman 在重装软件包时就只能重新下载了。除非空间不足，否则不应这么做。
由于以上种种局限，建议使用专门的脚本去处理清理哪些、清理多少缓存：
pacman 提供的 paccache 命令默认会删除近3个版本前的软件包
> paccache -r

也可以自己设置保留最近几个版本：
> paccache -rk 1

清理所有未安装包的缓存文件，再此运行paccache:
> paccache -ruk0

更多功能参见paccache -h。
paccache，还可以使用 Arch User Repository 中的 pkgcachecleanAUR：
> pkgcacheclean
，以及pacleanerAUR，这两个是未来的替代工具.

## 其它命令

升级系统时安装其他软件包：
> pacman -Syu package_name1 package_name2 ...

下载包而不安装它：
> pacman -Sw package_name

安装一个本地包(不从源里下载）：
> pacman -U /path/to/package/package_name-version.pkg.tar.xz

要将本地包保存至缓存，可执行：
> pacman -U file://path/to/package/package_name-version.pkg.tar.xz

安装一个远程包（不在 pacman 配置的源里面）：
> pacman -U http://www.example.com/repo/example.pkg.tar.xz

要禁用 -S, -U 和 -R 动作，可以使用 -p 选项.
pacman 会列出需要安装和删除的软件，并在执行动作前要求需要的权限。
安装原因

pacman数据库按照软件包被安装的原因，将其分为两类：
指定安装包：通过pacman-S或者-U指令安装的软件包。
依赖包：指定安装包所依赖的软件包，尽管命令中未传入，但仍然会被安装。

当安装软件包时，可以把安装原因指定设为依赖:
> pacman -S --asdeps package_name

但是当重新安装该软件包时，安装原因将会被设为软件包所默认的。 指定安装的软件包列表可用pacman -Qe, 已安装的依赖包可用pacman -Qd获取。 改变某个已安装软件包的安装原因，可以执行：
> pacman -D --asdeps package_name
使用--asexplicit改为指定安装。
查询一个包含具体文件的包名

同步文件数据库:
> pacman -Fy

查询包含某个文件的包名，比如:
> pacman -Fs pacman
```
core/pacman 5.0.1-4
    usr/bin/pacman
    usr/share/bash-completion/completions/pacman
extra/xscreensaver 5.36-1
    usr/lib/xscreensaver/pacman
```
提示： 可以设置一个 crontab 或者 systemd timer 来定期同步文件信息数据库。
如果需要高级功能请安装 pkgfile，它使用一个单独的数据库来保存文件和它们所关联的软件包的信息。
配置

pacman 的配置文件位于/etc/pacman.conf。 man pacman.conf 可以查看配置文件的进一步信息。
通用选项

通用选项都在[options]段。阅读 man 手册或者查看默认的 pacman.conf 可以获得有关信息和用法。
升级前对比版本

要查看旧版和新版的有效安装包，请取消/etc/pacman.conf中"VerbosePkgLists"的注释。修改后的pacman -Syu输出如下：
```
Package (6)             Old Version  New Version  Net Change  Download Size

extra/libmariadbclient  10.1.9-4     10.1.10-1      0.03 MiB       4.35 MiB
extra/libpng            1.6.19-1     1.6.20-1       0.00 MiB       0.23 MiB
extra/mariadb           10.1.9-4     10.1.10-1      0.26 MiB      13.80 MiB
```
彩色输出
Pacman 具有颜色选项，取消 "Color" 行的注释即可.

- 不升级软件包

如果由于某种原因，用户不希望升级某个软件包，可以加入内容如下：
IgnorePkg = 软件包名

多软件包可以用空格隔开，也可是用 glob 模式。如果只打算忽略一次升级，可以使用  --ignore 选项。
忽略了的软件包可通过 pacman -S 升级。

- 不升级软件包组

和软件包一样，也可以不升级某个软件包组：
IgnoreGroup = gnome
跳过软件包文件
要跳过某些文件夹的安装，可以将它们放到 NoExtract 中，例如不想安装 systemd unit 文件：
NoExtract=usr/lib/systemd/system/*
后面的规则覆盖前面的规则，加上 ! 可以取消跳过效果.
保留多个配置文件
如果你有多个配置文件（比如，主配置和测试testing仓库生效的配置文件），需要共享一些设置：
Include = /path/to/common/settings
/path/to/common/settings文件中是两个配置文件共享的相同配置。
Hooks
pacman可以在更新安装前后时，运行/usr/share/libalpm/hooks/文件夹下的hooks，更多的hooks文件夹HooDir可以在pacman.conf中设置，默认/etc/pacman.d/hooks。Hook文件必须以.hook结尾。
软件仓库
除了特殊的options[broken link: invalid section]section, 每个pacman.conf中的section都定义了一个使用的软件包仓库，仓库是多个软件包的逻辑上的集合，他们物理上存储在一个或多个服务器：这也是为什么每一个服务器都叫做这个仓库的镜像。
仓库区分为官方]与[Unofficial user repositories|非官方两类。配置文件中仓库的顺序十分重要；当几个仓库出现同名安装包，不管版本号是否相同，pacman将使用配置文件中排前的仓库。upgrade[broken link: invalid section]升级整个系统，来让新添加的仓库生效。
每个仓库设置都可以直接指定镜像列表或者Include引用其他的文件：例如，官方镜像引用了/etc/pacman.d/mirrorlist/。具体查看Mirrors。

## 软件包的安全性
pacman 4 支持软件包签名。语句 SigLevel = Required DatabaseOptional 将启用全局签名验证，但会被每个软件仓库的 SigLevel 行所覆盖。详情参见 pacman-key.
一般注意事项
警告: 小心使用 --force 开关。使用不当会造成大问题。 请只在 Arch 新闻里要求这么做的时候才用。
pacman 附带了许多实用工具能让系统使用更加便捷。所有工具功能都能通过 --help 开关查看。运行：
$ pacman -Ql pacman | awk -F"[/ ]" '/\/usr\/bin/ {print $5}'
查看完整列表
问题解决

升级时遇到问题: "file exists in filesystem"(conflicting files)!
如果碰到这个帖子的错误：
error: could not prepare transaction
error: failed to commit transaction (conflicting files)
package: /path/to/file exists in filesystem
Errors occurred, no packages were upgraded.

发生了什么事: pacman 检测到文件冲突，而且按照设计，pacman 不会覆盖文件。这是设计功能，不是缺陷。

先用 (pacman -Qo 文件的完整路径 检查哪个软件包提供了文件。如果是其它软件包，请报告问题。如果不是其它软件包提供，将已经存在的文件重命名并重新升级。如果一切顺利，可以删掉备份文件。
如果是通过 make install 等非 pacman 方式安装的软件，安装的文件不属于任何软件包！需要先手动删除这些文件，这样就可以正常安装软件了。不属于任何软件包的文件列表一文中提供了查找这些文件的脚本。
每一个安装的软件包都会提供一个 /var/lib/pacman/local/$package-$version/files 文件，包含此软件包的元数据。如果文件损坏或者丢失，将会导致升级时出现file exists in filesystem 错误。此错误通常只会影响一个软件包，除了手动删除或移动所有的问题文件，可以作为特例使用pacman -S --force $package让 pacman 强制覆盖这些文件。
警告: --force 选项非常危险，建议在 Arch 新闻中明确通知的时候才使用它，否则可能导致系统无法启动。

"failed to commit transaction (invalid or corrupted package" 错误
看看/var/cache/pacman/pkg中是否有*.part结尾的文件，它们是没有完全下载的文件，删除它们并重新执行更新。这些程序一般是自定义的XferCommand 下载命令造成的。

> find /var/cache/pacman/pkg/ -iname "*.part" -exec rm {} \;
"error: failed to init transaction (unable to lock database)" 错误

pacman 在更新软件包数据库前，比如安装软件包时会创建一个文件锁  /var/lib/pacman/db.lck。该文件会阻止其他 pacman 实例在同一时间修改软件包数据库。如果 pacman 在更新数据库时收到干扰，旧锁会一直存在。如果确认 pacman 没有在运行，那么删掉文件锁：
> rm /var/lib/pacman/db.lck

安装时无法获取软件包

错误内容包含：Not found in sync db, Target not found 或 Failed retrieving file.
首先确认软件包确实存在（并注意错别字）。如果确认软件包存在，可能本地数据库过时了或者软件仓库没有配置好，试试 pacman -Syyu 强制数据库更新和升级。
也有可能包含该软件包的软件仓库没有启动。例如，该软件包可能在 multilib 仓库里，但该仓库没有在 pacman.conf 中启用。
参阅FAQ#Why is there only a single version of each shared library in the official repositories?.

pacman 重复升级同一个包

注意: pacman 版本 3.4 在遇到重复条目时会显示错误，因此这个问题过时了。

这是因为在 /var/lib/pacman/local/ 有重复的条目，例如有两个 linux 条目。pacman -Qi 输出正确的版本，但是 pacman -Qu 识别了旧版本，因此尝试升级。
解决方法：删除 /var/lib/pacman/local/ 中多余的条目。
pacman 完全坏掉，如何修复?
如果 pacman 完全坏掉不能使用，需要手动下载或构建需要的软件包(openssl, libarchive 和 pacman) 并解压到根目录。pacman 会和默认配置文件一起恢复。之后，用 pacman 重新安装这些软件包以保证数据库的完整性。

pacman 更新时崩溃

如果 pacman 在删除、重新安装或更新软件包时 "数据库写入" 出错：
从 Arch 安装媒体启动，最好用最新的安装媒体
挂载根文件系统，通过 df -h 确认根文件目录包含足够的空间
如果系统使用默认的数据库目录位置，可以通过root用户执行下面命令pacman --root=/mnt --cachedir=/mnt/var/cache/pacman/pkg -Syyu
更新之后，可以通过下面命令确认是否还存在损坏的包：find /mnt/usr/lib -size 0
通过下面命令重新安装依然损坏的软件包：pacman --root /mnt --cachedir=/mnt/var/cache/pacman/pkg -S package.
pacman crashes the official installation media
The official installation media (ISO) before version 10.2015 are not setup to be updated itself at runtime. Running pacman -Syu from a booted install media console may crash unexpectedly any time, as soon as memory is depleted. This happens because the install media image build reports an arbitrary capacity (of 32GB) to pacman, regardless of available free memory.[1] At the same time the ISO reserves only a low static memory allotment for operations (/run/archiso/cowspace of 256MB RAM) of the live system, in order to allow installation on machines with low resources. If the machine has more RAM available, you can override the allotment by setting the cow_spacesize= kernel option for the ISO manually, e.g. cow_spacesize=2GB.
If you use the install media to update an installed system, you simply have to use the --root= option along with a --cachedir= path to point pacman to available real storage. For example, see #pacman 更新时崩溃.
If you require an install media with persistent dataspace, the Archiso build script can be used to create one along with its boot options.
升级系统重启后，出现"unable to find root device"错误，无法登陆
很有可能 initramfs 在内核升级时损坏，例如错误的使用 pacman 的 --force 选项。有两个选择：

Fallback 启动项

提示： 如果删除了此启动项，可以在启动时进入启动加载器的手动模式，将 initramfs 修改为 initramfs-linux-fallback.img 继续启动。
如果系统可以启动，运行如下命令可以生产原始内核 linux 的 initramfs:
> mkinitcpio -p linux

Chroot 修复

如果上面方法不行，请下载 2012 年之后发布的安装程序进行启动，执行:
> mount /dev/sdxY /mnt         #Your root partition.
> mount /dev/sdxZ /mnt/boot    #If you use a separate /boot partition.
> arch-chroot /mnt
> pacman -Syu mkinitcpio systemd-tools linux

重新安装内核(linux 软件包)将会自动运行 mkinitcpio -p linux 重新生成 initramfs 镜像，不需要单独生成。
之后建议执行 exit, umount /mnt/{boot,}  然后 reboot.

Note: 如果无法进入 arch-chroot 或 chroot 环境，但是需要重新安装软件包，可以使用 pacman -r /mnt -Syu foo bar
Signature from "User <email@gmail.com>" is unknown trust, installation failed

试试以下途径：

更新已知密钥：pacman-key --refresh-keys;

手动升级archlinux-keyring软件包：pacman -S archlinux-keyring.
查看pacman-key#Resetting all the keys.
Request on importing PGP keys
If installing Arch with an outdated ISO, you are likely prompted to import PGP keys. Agree to download the key to proceed. If you are unable to add the PGP key successfully, update the keyring or upgrade archlinux-keyring (see above).

不停看到错误 
```
"PackageName: signature from "User <email@archlinux.org>" is invalid"
When the system time is faulty, signing keys are considered expired (or invalid) and signature checks on packages will fail with the following error:
error: PackageName: signature from "User <email@archlinux.org>" is invalid
error: failed to commit transaction (invalid or corrupted package (PGP signature))
Errors occured, no packages were upgraded. 
Make sure to correct the time, for example with ntpd -qg run as root, and run  hwclock -w as root before subsequent installations or upgrades.
'warning: current locale is invalid; using default "C" locale' 错误
```
错误信息已经很明确了，locale 设置不正确，请阅读Locale进行设置。
pacman不使用我的代理设置

正确设置环境变量($http_proxy, $ftp_proxy etc.)如果使用sudo,需要让 sudo 将这些变量传递给 pacman.
如何重装所有包并保留安装和依赖信息？
重装所有软件包：pacman -S $(pacman -Qnq)（-S 选项会保留安装缘由）。
接着需要重装外来包（不在官方仓库里的软件包）。外来包可通过pacman -Qmq查看。

"Cannot open shared object file" 错误

It looks like previous pacman transaction removed or corrupted shared libraries needed for pacman itself.
To recover from this situation you need to unpack required libraries to your filesystem manually. First find what package contains the missed library and then locate it in the pacman cache (/var/cache/pacman/pkg/). Unpack required shared library to the filesystem. This will allow to run pacman.
需要重新安装损坏的软件包. Note that you need to use --force flag as you just unpacked system files and pacman does not know about it. pacman will correctly replace our shared library file with one from package.
That's it. Update the rest of the system.
软件包下载停滞
Some issues have been reported regarding network problems that prevent pacman from updating/synchronizing repositories. [2] [3] When installing Arch Linux natively, these issues have been resolved by replacing the default pacman file downloader with an alternative (see Improve pacman performance for more details). When installing Arch Linux as a guest OS in VirtualBox, this issue has also been addressed by using Host interface instead of NAT in the machine properties.
无法从镜像服务器获取 'core.db'
如果从正确的镜像服务器收到此错误，请换一个域名解析服务器。

