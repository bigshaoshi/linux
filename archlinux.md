# ArchLinux

## Written by 王世杰
## Installation Guide for UFFI
### 0 connect internet

```
rfkill list
rfkill unblock all
twctl
```

```
device list
station device scan
station device get-networks
station device connect wifi_id
wifi_password
```

### 1、更新系统时间
``` shell
timedatectl set-ntp true
```

### 2、建立分区 && 挂载
| 挂载点                | 分区                                    | 类型                    |
| --------------------- | --------------------------------------- | ----------------------- |
| [swap]                | /dev/swap_partition(交换分区)           | Linux swap              |
| /mnt/boot or /mnt/efi | /dev/efi_system_partition(efi 系统分区) | EFI系统分区             |
| /mnt                  | /dev/root_partition(根分区)             | Linux x86_64根目录（/） |

```shell
cfdisk /dev/sda1(目标磁盘)
mkfs.ext4 /dev/root_partition
mkfs.fat -F32 /dev/efi_system_partition
mkswap /dev/swap_partition
swapon /dev/swap_partition
mount /dev/root_partition /mnt
mkdir /mnt/boot/efi -p
mount /dev/efgrub	ion /mnt/boot/efi
```

### 3、选择最快的镜像源
```shell
vim /etc/pacman.d/mirrorslist
加入 Server = http://mirrors.163.com/archlinux/$repo/os/$arch  
pacman -Syyu
```

### 4、安装基础包
```shell
pacstrap /mnt base linux linux-firmware vim
```

### 5、Fstab
```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

### 6、Chroot
```shell
arch-chroot /mnt
```

### 7、时区
```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

### 8、字体
```shell
vim /etc/locale.gen   ##将zh_CN.UTF-8 zh_TW.UTF-8 en_US.UTF-8 添加到文件最前面
locale-gen
echo LANG=en_US.UTF-8 >> /etc/locale.conf
pacman -S wqy-microhei wqy-bitmapfont wqy-zenhei  ttf-dejavu adobe-source-han-serif-cn-fonts adobe-source-han-serif-tw-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-tw-fonts
```

### 9、网络配置
```shell
echo rou > /etc/hostname
vim /etc/hosts ##内容如下
pacman -S networkmanager
systemctl enable NetworkManager
```
```vim
# ! /etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.1.1	rou.localdomain	rou
```

### 10、设置root密码和创建用户&&设置sudo
```shell
passwd
useradd -m jie
passwd jie
pacman -S sudo
visudo ##jie的权限
```

### 11、桌面设置
#### xface4
```shell
pacman -S xorg xorg-xinit xterm xfce4 xfce4-goodies lightdm lightdm-gtk-greeter alsa-utils
systemctl enable lightdm.service
```
#### kde
```
pacman -S xorg sddm plasma plasma-wayland-session kde-applications
systemctl enable sddm
```


### 12、引导设置
```shell
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```

### 13、系统设置

#### 1.加入`archlinux`&&`archlinuxcn`源：

①file: /etc/pacman.d/mirrorslist

```
#aliyun
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch 
#USTC
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

②file:/etc/pacman.conf

- (1).修改pacman.conf文件

```
[archlinuxcn]
The Chinese Arch Linux communities packages.
#SigLevel = Optional TrustedOnly
SigLevel = Optional TrustAll
#aliyun
Server = https://mirrors.aliyun.com/archlinuxcn
#USTC
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
#163源
Server = http://mirrors.163.com/archlinux-cn/$arch
```

- (2).安装` archlinuxcn-keyring `包导入GPG key。

- (3).若`archlinuxcn-keyring`签名失败则：

```shell
sudo rm -rf /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman-key --populate archlinuxcn
```

## 软件篇

### 0、VMware进入BOIS

为了更轻松地访问BIOS设置屏幕，请编辑`.vmx`虚拟机的配置（）并添加或编辑以下选项之一：

- `bios.forceSetupOnce = "TRUE"`

  这将在启动时强制进入BIOS设置程序。

- `bios.bootDelay = "*xxxx*"`

  这会在初始POST屏幕上增加延迟，显示时间更长，并且使您有更多时间访问BIOS设置，其中*xxxx*是显示POST屏幕的毫秒数（每秒1000毫秒。）。引导延迟的最大值是10000毫秒或10秒。

### 1、安装输入法

```shell
yay -S fcitx fcitx-im fcitx-rime
vim ~/.xprifile
```

```vim
# ~/.xprifile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

### 1).anaconda
依赖准备：
```
pacman -Sy libxau libxi libxss libxtst libxcursor libxcomposite libxdamage libxfixes libxrandr libxrender mesa-libgl  alsa-lib libglvnd
```

### 2).pycharm && idea

解压-安装

### 3).QQ for linux

### 4).typora
```
sudo pacman -S typora visual-studio-code-bin ntfs-3g
```

### 5).visual studio code

### 6).sougopinyin

1.安装

``` shell
sudo pacman -S fcitx fcitx-qt5 fcitx-configtool
yay -S qt4
yay -S qtwebkit
yay -S fcitx-qt4
yay -S fcitx-sogoupinyin 
```
2.配置

file: ①~/.xprofile ②/etc/envirnment
添加内容：
```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
```

### 7).wps office

### 8).Mysql
sudo pacman -S mariadb
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
sudo systemctl start mysqld
sudo mysqladmin -u root password  '12345678'
登录:mysql -u root -p

## 2.config
### 自动挂载ntfs

1.安装ntfs-3g

```
yay -S ntfs-3g
```

2.修改/etc/fstab

#/dev/sda1
```
UUID=(sudo blkid查寻挂载盘的UUID)	/fileroad	ntfs-3g defaults,umask=000 0 0
UUID=D870508570506BEE /windows10	ntfs-3g defaults,umask=000 0 0
e.g.： UUID=253A17D5B7FAED85	/windows	ntfs-3g defaults,umask=000 0 0
```


## 3).git clone走代理

```shell
git config --global http.proxy http://127.0.0.1:1081
git config --global https.proxy https://127.0.0.1:1081
```



## 4).安装deb包

1.yay -S debtap

2.sudo debtap -u

3.debtap xxx.deb

4.sudo pacman -U xxx.pkg
