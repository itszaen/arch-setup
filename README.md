This is just yet a personal reminder of what to do when setting up an Arch Linux environment.

# Installation
## Connect to internet
### Wired
Should be connected automatically.
### With WiFi

```
# wifi-menu
# ping google.com
```
in case not working,
```
# ip link set (network interface) up
# iw (network interface) scan
# wpa_supplicant (connect point) >> /etc/wpa_supplicant.conf
~passphrase~
# wpa_supplicant -B -D (protocol, wext) -i (connect point) -c /etc/wpa_supplicant.conf
# dhclient (network interface)
```

## Partitioning & Formatting
Use gdisk
### BIOS-GPT
1007KiB - BIOS boot partition
Rest    - /

```
# mkfs.ext4 /dev/(root)
```

**DO NOT FORMAT BIOS boot partition**

### UEFI-GPT
512MiB - EFI System Partition
Rest   - /

```
# mkfs.ext4 /dev/(root)
# mkfs.vfat -V -F 32 /dev/(EFI system partition)
```

### LVM
pvcreate
vgcreate
lvcreate
 -L [M,G,T...]
 -l [%]
 -n label name
 and pass the vg name as argument
## Mount
Mount to /mnt
```
# mount /dev/(root) /mnt
```

## Pacstrap

```
# vi /etc/pacman.d/mirrorlist
```
bring up ftp.jaist.ac.jp and tsukuba.wide.ad.jp

```
# pacstrap /mnt base base-devel
```

## Generate Fstab

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

## Enter fakeroot

```
# arch-chroot /mnt
```

## Language Environment
### Generate Locale

```
# vi /etc/locale.gen
```
comment out en_US, ja_JP, zh_CN
```
# locale-gen
```

### LANG

```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
# export LANG=en_US.UTF-8
```

### Timezone

```
# ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

## Hostname configuration

```
# echo (hostname) >> /etc/locale.conf
# vi /etc/hosts
   127.0.1.1 ().localdomain ()
```

## Mkinitcpio
### If using lvm2
```
# pacman -S lvm2
# vi /etc/mkinitcpio.conf
   HOOKS="base systemd ... block sd-lvm2 filesystems"
# mkinitcpio -p linux
```

## Bootloader
### BIOS
```
# pacman -S grub
# grub-install /dev/sda
# grub-mkconfig -o /boot/grub/grub.cfg
```

### UEFI
```
# pacman -S grub
# mkdir -p /mnt/boot/efi
# mount /dev/(efi partition) /mnt/boot/efi
# grub-install --targer=x86_64-efi --efi-directory=$esp --bootloader-id=grub --recheck --debug
# grub-mkconfig -o /boot/grub/grub.cfg
```

## Network
### If wired
```
# systemctl enable dhcpcd
```
### If using wifi
```
# pacman -S wireless_tools wpa_supplicant wpa_actiond dialog networkmanager iw
```

## Reboot
```
# exit
# umount -R /mnt
# reboot
```

# Basic Setup
## Connect to the internet
### Wired
should be connected automatically
### Wifi
```
# wifi-menu
```

## User
```
# useradd -m -g wheel zaen
# passwd zaen
~password~
```
### Sudo (Substitute User DO)
```
#visudo
```

## Desktop Environment
### Video Driver
#### Intel
```
# pacman -S xf86-video-intel mesa-libgl lib32-mesa-libgl
```
#### NVIDIA
##### Nouveau
```
# pacman -S xf85-video-nouveau mesa lib32-mesa mesa-libgl lib32-mesa-libgl
```
##### Proprietary (preferable)
```
# pacman -S nvidia nvidia-libgl lib32-nvidia-libgl
```

### Plasma
#### Xorg
```
# pacman -S xorg-server xorg-server-utils xorg-xinit xorg-xclock xterm
# pacman -S plasma konsole
```
#### Wayland
```
# pacman -S wayland weston
# pacman -S plasma konsole
```

### Display Manager
```
# pacman -S sddm
# systemctl enable sddm
```

```
# reboot
```

# Setup Pacaur
## Install cower
```
$ gpg --recv-keys 487EACC08557AD082088DABA1EB2638FF56C0C53
$ git clone --depth=1 "https://aur.archlinux.org/cower.git"
$ cd cower
$ makepkg -si
$ cd ..
```

## Install Pacaur
```
$ cower -d pacaur
$ cd pacaur
$ makepkg -si
```

# Software setup
Use Pacaur
## Basic Tools
mono
vim
emacs
unzip
zip
gwenview
okular
poppler-data
dolphin
 ffmpegthumbs
 kde-thumbnailer-odf
kcalc
jupyter-notebook

### Bluetooth
bluez
bluez-utils

## Fonts
bdf and bdf.gz
	bitmap fonts, bitmap distribution format and gzip compressed bdf
pcf and pcf.gz
	bitmaps, portable compiled font and gzip compressed pcf
psf, psfu, psf.gz and psfu.gz
	bitmaps, PC screen font, PC screen font Unicode and the gzipped versions (not compatible with X.Org)
pfa and pfb
	outline fonts, PostScript font ASCII and PostScript font binary. PostScript fonts carry built-in printer instructions.
ttf
	outline, TrueType font. Originally designed as a replacement for the PostScript fonts.
otf
	outline, OpenType font. TrueType with PostScript typographic instructions.

ttf-droid
ttf-dejavu
ttf-ms-fonts
ttf-bookerly (amazon Kindle)
ttf-roboto
adobe-source-code-pro-fonts

### Japanese
adobe-source-han-sans-jp-fonts
otf-ipafont
otf-ipaexfont
ttf-hanazono
ttf-sazanami
ttf-mona
ttf-koruri
otf-takao
ttf-sawarabi-mincho
ttf-monapo
ttf-vlgothic

Interesting
	otf-hannari-mincho
	otf-harenosora-mincho
	otf-ipamjfont  (for work)
	ttf-ume

### Chinese
adobe-source-han-sans-cn-fonts
adobe-source-han-sans-tw-fonts
wqy-microhei
ttf-tw


## Sync
insync
dropbox
synergy

### System Information
udisks2
d-feet
hwinfo

## Document
libreoffice
  still or fresh
 libreoffice-extension-texmaths
pandoc

## Media
audacious
mpv
alsa-utils

## Language Packages
### C
clang
cmake

### Python
python
python2
python-pip
python2-pip
python-pycurl
python2-pycurl

### Java
jdk7-openjdk
jdk8-openjdk
jre7-openjdk
jre8-openjdk

### Javascript
js
nodejs
npm

### Ruby
gem
```
# gem install rake
```

### Haskell
ghc
cabal-install
alex
happy

## Qt & Gtk
qt5
qt4
python2-pyqt4
python2-pyqt5
python-pyqt4
python-pyqt5
gtk3
gtk2
pygtk

## Input
fcitx
fcitx-mozc
fcitx-googlepinyin
fcitx-gtk2
fxitx-gtk3
fcitx-qt4
fcitx-qt5
kcm-fcitx

~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export DefaultIMModule=fcitx

### Emacs-mozc
Install mozc
	http://ain0204.hatenablog.com/entry/2016/08/12/235206


## Telecommunication
skype
skypeforlinux-bin

## Development Environment
eclipse-java
android-studio
atom
qtcreator

## Creation
### Graphic
gimp
inkspace
aseprite
### Video Editing
simplescreenrecorder
kdenlive
### Audio
kwave

## Screen
### xflux
pacaur -S xflux
xflux -l 39 -g 140

### Nvidia
nvidia-settings

## Print (CUPS)
cups
cups-pdf

org.cups.cupsd.service

## VirtualBox
virtualbox
virtual-host-modules-archlinux
virtualbox-guest-iso
### Guest system
virtualbox-guest-utils

## Wine
wine

## File systems
exfat-utils exFAT
ntfs-3g     NTFS

## Network
traceroute
iptables
tcsd

## Zim
	python2-gtkspellcheck
	gtkspell
	aspell-en
	gtksourceview2
	graphviz
	ditaa
	texlive-lang
	r
	gnuplot
	lilypond
	pip install seqdiag
	zeitgeist
	 activity-log-manager
	 zeitgeist-explorer

Reference: http://zim-wiki.org/manual/Plugins.html
and instant search plugin
put instantsearch.py into [[~/.local/share/zim/plugins]]
instantsearch.py is at [[~/Google Drive/Documents]]

## Android
Android Debug Bridge
	mtpfs
	android-tools
	android-udev
```
# passwd -a zaen adbusers
$ adb devices
```
		Oneplus 3T will be recognized by default
Fastboot
	android-tools

# Shortcuts
Ctrl+alt+Q	Qt Creator
Ctrl+alt+W
Ctrl+alt+E
Ctrl+alt+R
Ctrl+alt+T	Terminal
Ctrl+alt+Y
Ctrl+alt+U
Ctrl+alt+I
Ctrl+alt+O
Ctrl+alt+P
Ctrl+alt+A
Ctrl+alt+S	Settings
Ctrl+alt+D
Ctrl+alt+F	Dolphin
Ctrl+alt+G
Ctrl+alt+H
Ctrl+alt+K
Ctrl+alt+L
Ctrl+alt+Z	Zim
Ctrl+alt+X
Ctrl+alt+C	Chromium
Ctrl+alt+V
Ctrl+alt+B
Ctrl+alt+N	Nvidia Settings
Ctrl+alt+M	Calculator

KDE
Ctrl + Q	Quit program
