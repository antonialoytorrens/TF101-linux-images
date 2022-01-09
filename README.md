# **Native Linux on the Asus Eee Pad Transformer TF101 using PostmarketOS**

## **Disclaimer**
<b>TF101G and SL101 VARIANTS ARE NOT SUPPORTED AT THE MOMENT. IT REQUIRES NVFLASH KEYS, WHICH ARE CURRENTLY UNKNOWN.

TESTED FOR TF101 SBK1 ONLY. IF YOU HAVE TF101 SBK2, TEST IT WITH YOUR OWN RISK.
YOU CAN ALSO STICK WITH [THE OLD DOWNSTREAM RELEASE](https://github.com/antonialoytorrens/TF101-linux-images/blob/master/README.old.postmarketos-downstream.md).

THIS GUIDE COMES WITH NO WARRANTY. I AM NOT RESPONSIBLE OF BREAKING YOUR TF101.
PLEASE BACKUP YOUR DATA BEFORE CONTINUING.

READ ALL THE GUIDE AND TROUBLESHOOTING SECTION BEFORE POSTING AN ISSUE.</b>

## **Introduction**

This is a guide about how to run Native Linux on the Asus Eee Pad Transformer TF101.

According to _Wikipedia_:  
*_"" PostmarketOS (stylized as postmarketOS and abbreviated as pmOS) is a free and open-source operating system under development primarily for smartphones, based on the Alpine Linux distribution. ""_*

**What you will need:**  
*Note: I have used a computer with Linux in this guide.*

-   A computer with Linux (it could also work with Windows, but unfortunately I don't have this OS on my computer).
-   Micro SD Card only (required, because U-boot is configured to boot from Micro SD Card).
-   [APX Drivers](https://forum.xda-developers.com/t/adb-fb-apx-driver-universal-naked-driver-0-72.1514942/) (you will need to install them if you are using Windows. Ignore this if you are using Linux).
-   Android Debug Bridge Tools (`android-tools` and `android-udev` packages in Arch Linux, `adb` package on Debian and derivatives). In case you are using Windows, you may have to install `adb` system-wide.
-   (Not tested) If you have TF101 SBK2, you may need to extract Nvflash from [here](https://github.com/francoism90/tf101-config).

*Note: I am currently using a 32 GB SanDisk Micro SD Card, so I do not depend of the dock keyboard.*

## **Image setup**

Download it [here](https://sourceforge.net/projects/tf101-linux-images/files/postmarketOS/mate-postmarketOS-TF101-v4.0.img.xz) or [here](https://sourceforge.net/projects/tf101-linux-images/files/postmarketOS/mate-postmarketOS-TF101-v4.0.img).

---
  
### **1. Flashing the image to Micro SD Card using dd**:
Take special care with dd command when flashing the image. One step wrong and your hard drive is lost. I am not responsible of anything.  
Path to Micro SD Card may vary, mine is in /dev/sdb. Use  _fdisk -l_  command to identify your Micro SD Card location.

-   Insert your Micro SD Card to your computer
-   Unzip downloaded file

**My Micro SD Card is located in /dev/sdb**, so I do:

<pre>$ sudo dd if=/path/of/asus-tf101.img of=/dev/sdb status=progress</pre>

* If you have an USB Flasher dedicated tool for your Linux distribution (Ubuntu, Linux Mint, Debian, etc.) please use it.

**Extend with  _gparted_  or other disk manipulation tool the ext4 root filesystem to occupy all Micro SD Card disk space available.**  
In gparted, don't put swap into the Micro SD Card because it can be harmful. Consider using zram, but I haven't tested it.  

---
  
### **2. U-boot for TF101, Wheelie and Nvflash**
#### Installation
:warning: **WARNING** :warning:<br/>
**This mainline installation does not work for TF101G or SL101 variants! Please use downstream installation instead.<br/>
In addition, these steps will format ALL the partitions of the TF101's partition table and then recreate them but with u-boot bootloader flashed on it, instead of the proprietary Asus Bootloader.<br/>
Make sure you backup your data before continuing. Note also that you won't be able to boot Android back easily.**

* A Linux computer is required (virtual machine won't work). Furthermore, this installation process has not been verified by a TF101 SBK2 version but it should also work.

Use the latest 9.2.1 firmware available, so upgrade if you need it: https://www.asus.com/us/supportonly/Eee%20Pad%20Transformer%20TF101/HelpDesk_BIOS/<br/>
If the upper link does not work, try this one: https://web.archive.org/web/20211008161750/https://www.asus.com/us/supportonly/Eee%20Pad%20Transformer%20TF101/HelpDesk_BIOS/.

* *Note*: It should not be a problem if you are using a lower firmware version, since U-Boot is even lower-level than firmware-related versions.

You must know which [SBK version](https://forum.xda-developers.com/t/how-to-find-my-sbk-version.1643002/) your tablet has. For the asus-tf101, there are two versions only: sbkv1 and sbkv2.

However there is a caveat: two proprietary tools are needed, Wheelie (although source code is available on [GitHub](https://github.com/AndroidRoot/wheelie), some key features are missing) and Nvflash (proprietary Nvidia flash tool).
So, a custom setup is available to get this working a little bit easier.

* Mind that you should already have your postmarketOS image flashed on your Micro SD Card.

1. Connect the device to the computer. This [USB to 40 Pin cable](https://imgur.com/a/mfiryeP) is required.

3. Put your device into APX mode: First of all, power off the device and then press the power button + volume up button for 3 to 5 seconds.

4. Download U-Boot Tools for Asus Eee Pad Transformer TF101 [here](https://gitlab.com/antoni.aloytorrens/u-boot-tf101-tools/-/releases). Please read the release notes for instructions.

* If you are unsure which SBK version you have, please try both flashing methods.

:warning: Do not unplug the USB under any circumstance. :warning:

4. Some operations will be executed on the tablet. Please be patient, wait from 5 to 10 minutes. Formatting Partition 15 (UDA) can also take some time. In case you don't know the SBK version of your tablet, you will see some errors, or the device not booting at all. In that case, please try the other SBK version.

Expected output (SBK1 example):
<pre>
user@mycomputer:~$ ./uboot_sbkv1.sh 
Wheelie 0.1 - Preflight for nvflash.
Copyright (c) 2011-2012 androidroot.mobi
========================================

Using SBK type 1.
Using bootloader: 'bootloader.bin'.
Using BCT: 'transformer.bct'.
----------------------------------------

[=] Chip UID: YourHardwareID
[=] RCM Version: 0x20001

[=] CPU Model: Tegra 2
[=] Secure Boot Key Set: Yes
[+] Sending BCT
Sending file: 100 %
[+] Sending ODMData 0x300D8011
[+] Sending bootloader...
Sending file: 100 %
[!] Done - your device should now be ready for nvflash
Nvflash started
[resume mode]
setting device: 2 3
creating partition: BCT
creating partition: PT
creating partition: EBT
creating partition: SOS
creating partition: LNX
creating partition: BAK
creating partition: GP1
creating partition: APP
creating partition: CAC
creating partition: MSC
creating partition: USP
creating partition: PER
creating partition: YTU
creating partition: UDA
creating partition: GPT
Formatting partition 2 BCT please wait.. done!
Formatting partition 3 PT please wait.. done!
Formatting partition 4 EBT please wait.. done!
Formatting partition 5 SOS please wait.. done!
Formatting partition 6 LNX please wait.. done!
Formatting partition 7 BAK please wait.. done!
Formatting partition 8 GP1 please wait.. done!
Formatting partition 9 APP please wait.. done!
Formatting partition 10 CAC please wait.. done!
Formatting partition 11 MSC please wait.. done!
Formatting partition 12 USP please wait.. done!
Formatting partition 13 PER please wait.. done!
Formatting partition 14 YTU please wait.. done!
Formatting partition 15 UDA please wait.. done!
Formatting partition 16 GPT please wait.. done!
done!
sending file: u-boot.bin
- 565989/565989 bytes sent
u-boot.bin sent successfully
Nvflash started
[resume mode]
</pre>

8. Now press the power button for a few seconds to reboot the device. After that, disconnect the USB from the device. It should be booting PostmarketOS!

---

### **3. Booting PostmarketOS**:
  
| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | ubuntu |
  
Please change the password later whenever possible.
  
- **Setup your keyboard layout**:

<pre>$ sudo apk add setxkbmap
$ setxkbmap "YOUR KEYBOARD LAYOUT"</pre>

*Please search the keyboard layout code of your country. Normally it's your country abbreviation: us, de, it, es, ...*  
  
- **Set keyboard layout automatically at startup**:  

<pre>cat > /etc/local.d/keyboard-layout.start << "EOF"
#!/bin/sh
setxkbmap "YOUR KEYBOARD LAYOUT"
EOF</pre>


- **Customize Lightdm to enable onscreen keyboard property**:
Lightdm has an onscreen keyboard in menu, so it will be very useful to integrate onboard (onscreen keyboard) with lightdm at login.  

<pre>cat > /etc/lightdm/lightdm-gtk-greeter.conf << "EOF"
[greeter]
#background=
#user-background=
#theme-name=
#icon-theme-name=
#font-name=
#xft-antialias=
#xft-dpi=
#xft-hintstyle=
#xft-rgba=
indicators=~a11y;~session;~power
#clock-format=
keyboard=onboard --theme=Nightshade -x 300 -y 400
#reader=
#position=
#screensaver-timeout=
EOF</pre>

- **Add suspend properties** (thanks [LongnoseRob](https://github.com/LongnoseRob)):

<pre>$ sudo apk add pm-utils</pre>

---

**Reboot and done!**  

<br/>

## **TODO**

Mentioned in the wiki: [https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101)#Hardware_status_.28mainline.29](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101)#Hardware_status_.28mainline.29)

## **Troubleshooting**

- **Nvflash does not work for me, and I cannot nvflash to the tablet**

I have tested this with Ubuntu 20.04, Debian 10 and Linux Mint 20.2 Uma.
If nvflash does not work for you, please download from [official Nvidia Tegra sources](https://web.archive.org/web/20150804234956/http://developer.download.nvidia.com/assets/mobile/files/tegra-linux-12.alpha.1.0.tar.gz) and try it again. There are more files there, just search nvflash binary and replace it accordingly.

If you cannot get APX to work, please install `Android Debug Bridge` tools and try it again.<br/>
For last option, you can try rootbind method (thanks [CrazyPlaysHD](https://github.com/CrazyPlaysHD)): https://github.com/antonialoytorrens/TF101-linux-images/issues/11

- **Why can't I boot from eMMC and only from MicroSD Card?**

Tegra devices uses a special partition scheme (tegra-specific partition table). Uboot does not have such feature, so it cannot identify internal partitions, so it does not boot from them.<br/>
However, work is being done to support tegra partitions on U-Boot.

- **The UI is not starting**

That is a known problem. Newer XServer versions do not work on the TF101.<br/>
More info here: https://gitlab.com/postmarketOS/pmaports/-/merge_requests/2605<br/>

If you are using Debian 11, you shouldn't have this type of issue.<br/>
WORKAROUND: Download my prebuilt image, because it contains an older xorg-server version. You can update/upgrade your system as normal, as this package is pinned and is not upgraded at all, even if newer releases exist.<br/>

If you want to view the source package and/or build it yourself, you can see it [here](https://github.com/antonialoytorrens/TF101-linux-images/blob/master/vendor/xserver-xorg-pmOS/xorg-server-9999-1.20.11.zip).

- **Will X.X.X.X firmware version or XXXX region work on my TF101 if I flash U-Boot?**

Yes, it will. U-Boot is even lower-level than firmware versions/regions, so it shouldn't be a problem.

- **WiFi is not working, what should I do?**

Open a terminal and type:
<pre>
$ sudo cp /lib/firmware/postmarketos/brcm/* /lib/firmware/brcm/
</pre>

Reboot and done.

- **Bluetooth is not working, what should I do?**

Bluetooth is disabled by default for battery saving.

Open a terminal and type:
<pre>
$ sudo service bluetooth start
</pre>

- **SDL2 games are not working...**

GPU is not supported at the moment. 
You might patch your games using software rendering, but it's painfully slow and totally unplayable.

- **Browsing is so slow**

We cannot do much with a 10-year-old device.<br/>
If GPU was working maybe it could be somewhat faster...<br/>
Use netsurf or dillo. You can install it by opening a terminal and typing:

For Netsurf:
<pre>
$ sudo apk add netsurf
</pre>

For Dillo:
<pre>
$ sudo apk add dillo
</pre>

- **What about installing other distributions rather than PostmarketOS?**

* **Debian 11 Bullseye**: https://github.com/antonialoytorrens/TF101-linux-images/blob/master/README.Debian11Bullseye.md

As you can see, installation procedure is almost the same. Not much varies installing one image or another if you are using the mainline grate kernel.

  
## **Special Thanks and Rellevant Links**

**Special Thanks**
- [Svyatoslav Ryhel](https://github.com/clamor-s), [Dmitry Osipenko](https://github.com/digetx), [Ion Agorria](https://gitlab.com/IonAgorria), [Raffaele T.](https://tuxphones.com/author/r/), [David Heidelberg](https://floss.social/@okias), /home/anton, jenneron, Jasper and all members of **PostmarketOS on Transformers on matrix.org and members of #tegra channel on IRC (libera.chat)**.

**Kernel**
- [Dmitry Osipenko](https://github.com/digetx) and contributors for mainline Grate Kernel: https://github.com/grate-driver/linux

**U-Boot**
- Initial TF101 implementation (with U-boot) for mainline kernel (thanks [mnidza](https://github.com/mnidza/)): https://github.com/grate-driver/linux/pull/23
- U-boot source: https://github.com/mnidza/u-boot-public/tree/tf101
- UEnv.txt (needed for U-boot in MicroSD Card boot partition): https://github.com/grate-driver/linux/files/6227021/uEnv.txt

- [Svyatoslav Ryhel](https://github.com/clamor-s) for continuing U-Boot development for TF101 and even implement it for other Tegra devices (if you want to check U-Boot sources, click here as they are more recent and developed). [View source](https://github.com/clamor-s/u-boot/).

**Rootbind**
- [jrohwer](https://forum.xda-developers.com/m/jrohwer.3753391/) for its [Rootbind method](https://forum.xda-developers.com/t/kernel-mod-linux-rootbind-native-emmc-all-tf101-tf101g-fast-tested-2-jul-13.2347581/).
- [Kingzak34](https://forum.xda-developers.com/m/kingzak34.3447252/) for its [guide](https://forum.xda-developers.com/t/guide-dual-boot-theeasyway-how-to-setup-linux-rootbind-method-updated-may-16th.2684854/).
- [CrazyPlaysHD](https://github.com/CrazyPlaysHD) for testing and update Rootbind for the latest Debian Distribution. [See here](https://github.com/antonialoytorrens/TF101-linux-images/issues/11).

**Pmaports (WIP but functional)**
- TF101 pmaports: https://gitlab.com/antoni.aloytorrens/pmaports/-/tree/wip-tf101-pmaports

**Distributions**
- PostmarketOS: https://postmarketos.org/
- Debian: https://debian.org
<br/>
