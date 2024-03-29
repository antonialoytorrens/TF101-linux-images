# **Native Linux on the Asus Eee Pad Transformer TF101 using Debian**

## **Disclaimer**
<b>TF101G and SL101 VARIANTS ARE NOT SUPPORTED AT THE MOMENT. IT REQUIRES NVFLASH KEYS, WHICH ARE CURRENTLY UNKNOWN.

TESTED FOR TF101 SBK1 ONLY. IF YOU HAVE TF101 SBK2, TEST IT WITH YOUR OWN RISK.

THIS GUIDE COMES WITH NO WARRANTY. I AM NOT RESPONSIBLE OF BREAKING YOUR TF101.
PLEASE BACKUP YOUR DATA BEFORE CONTINUING.

READ ALL THE GUIDE AND TROUBLESHOOTING SECTION BEFORE POSTING AN ISSUE.</b>

## **Introduction**

This is a guide about how to run Native Linux on the Asus Eee Pad Transformer TF101.

**What you will need:**  
*Note: I have used a computer with Linux in this guide.*

-   A computer with Linux (it could also work with Windows, but unfortunately I don't have this OS on my computer).
-   Micro SD Card only (required, because U-boot is configured to boot from Micro SD Card).
-   [APX Drivers](https://forum.xda-developers.com/t/adb-fb-apx-driver-universal-naked-driver-0-72.1514942/) (you will need to install them if you are using Windows. Ignore this if you are using Linux).
-   (Not tested) If you have TF101 SBK2, you may need to extract Nvflash from [here](https://github.com/francoism90/tf101-config).

*Note: I am currently using a 32 GB SanDisk Micro SD Card, so I do not depend of the dock keyboard.*

## **Image setup**

Download it [here](https://sourceforge.net/projects/tf101-linux-images/files/debian-11-bullseye-grate/kernel-5.14.0-rc5/tf101-11.0-BullseyeXFCE-armhf-Tegra-kernel-5.14.0-rc5.img.xz) or [here](https://sourceforge.net/projects/tf101-linux-images/files/debian-11-bullseye-grate/kernel-5.14.0-rc5/tf101-11.0-BullseyeXFCE-armhf-Tegra-kernel-5.14.0-rc5.img).

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

8. Now press the power button for a few seconds to reboot the device. After that, disconnect the USB from the device. It should be booting Debian!

---

### **3. Booting Debian 11 Bullseye**:
  
| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | debian |
  
Please change the password later whenever possible.
  
- **Setup your keyboard layout**:

<pre>$ setxkbmap "YOUR KEYBOARD LAYOUT"</pre>

*Please search the keyboard layout code of your country. Normally it's your country abbreviation: us, de, it, es, ...* 


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

---

**Reboot and done!**  

<br/>

## **TODO**

Mentioned in the wiki: [https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101)#Hardware_status_.28mainline.29](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101)#Hardware_status_.28mainline.29)

## **Troubleshooting**

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
$ sudo apt install netsurf-gtk
</pre>

For Dillo:
<pre>
$ sudo apt install dillo
</pre>

  
## **Special Thanks and Rellevant Links**
- [Svyatoslav Ryhel](https://github.com/clamor-s), [Dmitry Osipenko](https://github.com/digetx), [Ion Agorria](https://gitlab.com/IonAgorria), [Raffaele T.](https://tuxphones.com/author/r/), [David Heidelberg](https://floss.social/@okias), /home/anton, Jasper and all members of **PostmarketOS on Transformers on matrix.org and members of #tegra channel on IRC (libera.chat)**.
- Mainline Grate Kernel: https://github.com/grate-driver/linux
- Initial TF101 implementation (with U-boot) for mainline kernel: https://github.com/grate-driver/linux/pull/23
- U-boot source: https://github.com/mnidza/u-boot-public/tree/tf101
- UEnv.txt (needed for U-boot in MicroSD Card boot partition): https://github.com/grate-driver/linux/files/6227021/uEnv.txt
- Debian: https://debian.org
<br/>
