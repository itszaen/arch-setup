This is just yet a personal reminder of what to do when setting up an Arch Linux environment.

# Connect to internet
## Wired
Should be connected automatically.
## With WiFi

```
# wifi-menu
# ping google.com
```
in case not working,
```
# ip link set (network interface) up
# iw (network interface) scan
# wpa_supplicant (connect point) >> /etc/wpa_supplicant.conf
 passphrase
# wpa_supplicant -B -D (protocol, wext) -i (connect point) -c /etc/wpa_supplicant.conf
# dhclient (network interface)
```

# Partitioning & Formatting
Use gdisk
## BIOS-GPT
1007KiB - BIOS boot partition
Rest    - /

```
# mkfs.ext4 /dev/(root)
```

**DO NOT FORMAT BIOS boot partition**

## UEFI-GPT
512MiB - EFI System Partition
Rest   - /

```
# mkfs.ext4 /dev/(root)
# mkfs.vfat -V -F 32 /dev/(EFI system partition)
```

# Mount
Mount to /mnt
```
# mount /dev/(root) /mnt
```

# Pacstrap

```
# vi /etc/pacman.d/mirrorlist
```
bring up ftp.jaist.ac.jp and tsukuba.wide.ad.jp

```
# pacstrap /mnt base base-devel
```

# Generate Fstab

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

# Switch to fakeroot

```
# arch-chroot /mnt
```

# Language Environment
## Generate Locale

```
# vi /etc/locale.gen
```
comment out en_US, ja_JP, zh_CN
```
# locale-gen
```

## LANG

```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
# export LANG=en_US.UTF-8
```

## Timezone

```
# ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

# Hostname configuration

```
# echo (hostname) >> /etc/locale.conf
# vi /etc/hosts
   127.0.1.1 ().localdomain ()
```

# Mkinitcpio
## If using lvm2
```
# pacman -S lvm2
# vi /etc/mkinitcpio.conf
   HOOKS="base systemd ... block sd-lvm2 filesystems"

```
