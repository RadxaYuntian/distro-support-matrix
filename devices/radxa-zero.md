| Distro | Version & flavor | Boot on eMMC? | Boot on microSD? |
|--------|---------|---------------|------------------|
| Radxa Debian | 20211214-1300, [buster-xfce4](https://github.com/RadxaYuntian/debos-radxa/releases/download/20211214-1310/radxa-zero-debian-buster-xfce4-arm64-20211214-1300-mbr.img.gz) | [Yes](#1-dd-to-eMMC-device) | [No](#2-dd-to-microSD-card) |
| Radxa Ubuntu | 20211214-1300, [focal-server](https://github.com/RadxaYuntian/debos-radxa/releases/download/20211214-1310/radxa-zero-ubuntu-focal-server-arm64-20211214-1248-mbr.img.gz) | [Yes](#1-dd-to-eMMC-device) | [No](#2-dd-to-microSD-card) |
| Armbian | 21.08.4, [Focal current minimal](https://redirect.armbian.com/radxa-zero/Focal_current_minimal) | [Yes](#1-dd-to-eMMC-device) | [No](#2-dd-to-microSD-card) |
| Manjaro ARM | 21.12, [minimal](https://github.com/manjaro-arm/radxa-zero-images/releases/download/21.12/Manjaro-ARM-minimal-radxa-zero-21.12.img.xz) | [Yes](#1-dd-to-eMMC-device) | [Yes](#2-dd-to-microSD-card) |

## Installation methods

### 1. dd to eMMC device
<details>
First, boot your Radxa Zero into <a href="https://wiki.radxa.com/Zero/dev/maskrom#Enable_maskrom"><code>maskrom mode</code></a>, then execute the following command:

```
# Install necessary host packages
# Here we assume you are running Ubuntu
sudo apt update
sudo apt install python3-pip
sudo pip3 install pyamlboot
# Download rz-udisk-loader.bin
wget https://dl.radxa.com/zero/images/loader/rz-udisk-loader.bin
# Enable USB Mass Storage mode on the device
sudo boot-g12.py rz-fastboot-loader.bin
# Check which block device is Zero's eMMC
#
# BE VERY CAREFUL OR YOU MIGHT WIPE YOUR OTHER DISK!
#
lsusb
# Pipe your xz image to the device...
xzcat ./your_distro.img.xz | sudo dd of=/dev/sdX bs=128M && sync
```
Unplug and replug the power cable on Radxa Zero. You should now boot into your newly installed distro.
</details>

### 2. dd to microSD card
<details>
You need to wipe your eMMC's bootloader before you can make sure Radxa Zero will boot off microSD card.
This only needs to be done once for microSD boot. If you have done so already, skip to flashing image.

To erase bootloader, boot your Radxa Zero into <a href="https://wiki.radxa.com/Zero/dev/maskrom#Enable_maskrom"><code>maskrom mode</code></a>, then execute the following command:

```
# Install necessary host packages
# Here we assume you are running Ubuntu
sudo apt update
sudo apt install python3-pip
sudo pip3 install pyamlboot
bash <(curl -s https://raw.githubusercontent.com/corbindavenport/nexus-tools/master/install.sh)
# Download rz-fastboot-loader.bin
wget https://dl.radxa.com/zero/images/loader/rz-fastboot-loader.bin
# Enable factory mode on the device
sudo boot-g12.py rz-fastboot-loader.bin
# Wipe eMMC's bootloader
# This will make eMMC unbootable
# If you want to boot from eMMC again you have to reinstall your distro
sudo fastboot erase bootloader
sudo fastboot reboot
```
To flash image to your microSD card, insert the card to your computer first, then execute the following command:
```
# Check which block device is the microSD card
#
# BE VERY CAREFUL OR YOU MIGHT WIPE YOUR OTHER DISK!
#
lsusb
# Pipe your xz image to the device...
xzcat ./your_distro.img.xz | sudo dd of=/dev/sdX bs=128M && sync
```
Eject your microSD card and plug it into your Radxa Zero. Unplug and replug the power cable on Radxa Zero. You should now boot into your newly installed distro.
</details>