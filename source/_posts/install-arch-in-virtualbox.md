title: 在virtualbox下安装arch
date: 2014-07-03 22:23:04
tags: 
---
第一次用markdown写博客大家轻拍哈~

暑假在家无聊，于是决定在自己的mac上装个arch的虚拟机，
这样以后把mac放房间里面就有得玩耍了~

安装以后的界面就是这样，gnome，不要跟我争哪个桌面环境好什么的，我装gnome是因为它好装= =。。。等过段时间我就来折腾i3噢哈哈。
![alt text](http://i5.minus.com/i8DnQ2FA8Dmd6.png "哒哒！装好了！")

本博文是给胖哥哥的[这篇博文](https://bigeagle.me/2014/06/archlinux-install-for-beginners/)打的补丁，他是在SSD上装的，而我是在virtualbox里。

<!-- more -->

###辣么正文就开始吧
#准备工作
+ 把virtualbox打开.
+ 把下好的iso放进去.
+ 选择archlinux 64位（话说virtualbox里面居然有archlinux好感动，vmware里面就只能选other linux了= =）.
+ 启动看见选择界面直接回车.

#联网
ping一下8.8.8.8确认网络是通的，如果不通就`#dhclient -v eth0`。其中eth0是你网卡的名字，用ifconfig可以看到。

#分区
首先要查看一下/dev下的磁盘叫什么，我的是/dev/sda。
于是`#fdisk /dev/sda`,`:n`,除了输入主分区以外其他的都回车回车回车走默认的，就搞一个8G的/。最后别忘了`:w`保存。

格式化分区：
`#mkfs.ext4 /dev/sda1`
mkfs是make file system意。注意是sda1不是sda哦。

挂载到/mnt：
`#mount -t ext4 /dev/sda1 /mnt`

#安装
安装基本系统：
`# pacstrap /mnt base base-devel vim`
其中base是基础软件包组，base-dev是基础开发包组，vim是编辑器。
(尼玛现在写东西怎么跟写周报是一个味儿，其中其中的）

将开机挂载写入fstab：
`# genfstab -U -p /mnt >> /mnt/etc/fstab`

#配置
chroot到新安装的系统中：
`#arch-chroot /mnt`
然后现在就来到了我们在/dev/sda1上安装的系统辣。
设置root密码：
`#passwd`
编辑主机名：
`#vim /etc/hostname`
修改时区：
`#  ln -s /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime`
打开/etc/locale.gen，然后找到以下四行，取消注释:
`en_US.UTF-8`
`zh_CN.UTF-8`
`zh_CN.GBK`
`zh_CN.GB2312`

然后运行`# locale-gen`
再编辑/etc/locale.con，里面写上
`LANG="en_US.UTF-8"`

生成启动要用到的ramdisk:
`# mkinitcpio -p linux`

保证新系统可以联网:
`# pacman -S wpa_supplicant dialog`

安装引导器：
`# pacman -S grub`
`# grub-install --target=i386-pc --recheck --debug /dev/sdb`
`# grub-mkconfig -o /boot/grub/grub.cfg`

退出chroot，将iso拔出来，重启。

#联网
`#cp /etc/netctl/examples/ethernet-dhcp /etc/netctl/eth0`
其中etho0是网卡的名字，然后我们打开这个文件，将其中的interface后的内容改为"eth0"。

#添加用户
`#useradd -m username`
`passwd username`
将username加入sudoers list中：
`#visudo`
在`root ALL=(ALL) ALL`下面加一行：`username ALL=(ALL) ALL`

#装X
`# pacman -S xorg xorg-xinit xterm xorg-xeyes xorg-xclock`
`# startx`
然后你可以看到一个很丑的界面。

#装yaourt
在/etc/pacman.conf里加入：

`[archlinuxfr]`
`SigLevel = Never`
`Server = http://repo.archlinux.fr/$arch`

有人反映archlinux.cn更快一些。

`#pacman -S yaourt`

#安装gnome：
`#pacman -S gnome`
`#systemctl enable gdm`
`#systemctl start gdm`




