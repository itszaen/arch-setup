# BIOS Firmware

Mrchromebox	https://mrchromebox.tech/#home
https://raw.githubusercontent.com/MattDevo/scripts/master/firmware-util.sh

firmware-util.sh
```
#!/bin/bash
#
# This script offers provides the ability to update the
# Legacy Boot payload, set boot options, and install
# a custom coreboot firmware for supported
# ChromeOS devices
#
# Created by Mr.Chromebox <mrchromebox@gmail.com>
#
# May be freely distributed and modified as needed,
# as long as proper attribution is given.
#

#where the stuff is
script_url="https://raw.githubusercontent.com/MattDevo/scripts/master/"

#set working dir
cd /tmp

#get support scripts
rm -rf firmware.sh >/dev/null &2>1
rm -rf functions.sh >/dev/null &2>1
rm -rf sources.sh >/dev/null &2>1
curl -s -L -O ${script_url}firmware.sh
rc0=$?
curl -s -L -O ${script_url}functions.sh
rc1=$?
curl -s -L -O ${script_url}sources.sh
rc2=$?
if [[ $rc0 -ne 0 | $rc1 -ne 0 || $rc2 -ne 0 ]]; then
	echo -e "Error downloading one or more required files; cannot continue"
	exit 1
fi

source ./sources.sh
source ./firmware.sh
source ./functions.sh

#do setup stuff
prelim_setup
[[ $? -ne 0 ]] && 	exit 1

#show menu
menu_fwupdate
```

# Driver
wd719x-firmware
aic94xx-firmware

## Solve Audio problem, keyboard & touchpad problem
linux-galliumos-braswell
linux-galliumos-braswell-headers

# Lid Issue
Grub
	GRUB_CMDLINE_LINUX_DEFAULT="modprobe.blacklist=ehci_pci"

Systemd
	file:/usr/local/sbin/suspend-device-fix.sh
```
#!/bin/bash

awk '{if ($1 != "LID0" && $3 == "*enabled") print $1}' < /proc/acpi/wakeup | while read NAME
do echo "$NAME" > /proc/acpi/wakeup
done

exit 0

chmod +x [[/usr/local/sbin/suspend-device-fix.sh]]

[[/etc/systemd/system/suspend-fix.service]]
[Unit]
Description=Suspend Fix

[Service]
Type=simple
ExecStart=/usr/local/sbin/suspend-device-fix.sh

[Install]
WantedBy=multi-user.target
Start, and enable

/etc/rc.d/rc.local
echo 1 > [[/sys/devices/pci0000\:00/0000\:00\:1d.0/remove]]

[[/usr/lib/systemd/system-sleep/cros-sound-suspend.sh]]
#!/bin/bash

case $1/$2 in
pre/*)
# Unbind ath9k for preventing error and full sleep mode (wakeup by LID after hibernating)
echo -n "0000:01:00.0" | tee /sys/bus/pci/drivers/ath9k/unbind
# Unbind snd_hda_intel for sound
echo -n "0000:00:1b.0" | tee /sys/bus/pci/drivers/snd_hda_intel/unbind
echo -n "0000:00:03.0" | tee /sys/bus/pci/drivers/snd_hda_intel/unbind
;;
post/*)
# Bind ath9k for preventing error and and full sleep mode (wakeup by LID after hibernating)
echo -n "0000:01:00.0" | tee /sys/bus/pci/drivers/ath9k/bind
# bind snd_hda_intel for sound
echo -n "0000:00:1b.0" | tee /sys/bus/pci/drivers/snd_hda_intel/bind
echo -n "0000:00:03.0" | tee /sys/bus/pci/drivers/snd_hda_intel/bind
;;
esac
```
```
# chmod +x /usr/lib/systemd/system-sleep/cros-sound-suspend.sh
```
**REBUILD GRUB CONFIG!!**
