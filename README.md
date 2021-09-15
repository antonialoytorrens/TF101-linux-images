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
  
### **2. U-boot for TF101 and Nvflash** (workaround)

Before continuing, insert your Micro SD Card to the tablet.

If you don't know what nvflash is, click [here](https://nvflash.en.lo4d.com/windows).

Newer Nvflash versions do not support old Tegra 2 chips. 
I'll post what I use for Nvflashing [here](https://github.com/antonialoytorrens/TF101-linux-images/raw/master/vendor/nvflash-sbk1/tf101-sbk1-nvflash.zip). U-boot for TF101 and TF101 SBK1 keys are included.


To activate Nvflash, you need to put the tablet into APX mode.
To do so, power off the tablet, and then press POWER + VOLUME UP for 5 seconds.
APX Drivers are required for TF101 detection on your computer.


Like it says in the title, is only (at the moment) a workaround to boot PostmarketOS (or other Linux distributions) on it.
When Nvflashing U-boot, it only acts as a temporary bootloader when booting Linux on TF101. That being said, it means that when rebooting the tablet
or powering off and on, normal Asus Bootloader will appear again and you will boot into Android (if it's installed in eMMC).

So unfortunately, you need to do this step every time you need to boot Linux.
I have considered `kexec` or embedding U-Boot bootloader as a kernel blob, but I have been unsuccessful and I don't have the necessary knowledge to implement it.

To nvflash I do this (see `command.sh` in my nvflash file): 

<pre>sudo ./nvflash --bct transformer.bct --setbct --configfile flash.cfg --bl u-boot.bin --odmdata 0x300d8011 --sbk 0x1682CCD8 0x8A1A43EA 0xA532EEB6 0xECFE1D98 --sync</pre>

It will automatically fail (in the terminal) but you will see that TF101 is booting mainline Linux!

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
- [Svyatoslav Ryhel](https://github.com/clamor-s), [Dmitry Osipenko](https://github.com/digetx), [Ion Agorria](https://gitlab.com/IonAgorria), [Raffaele T.](https://tuxphones.com/author/r/), [David Heidelberg](https://floss.social/@okias), /home/anton, Jasper and all members of **PostmarketOS on Transformers on matrix.org and members of #tegra channel on IRC (libera.chat)**.
- Mainline Grate Kernel: https://github.com/grate-driver/linux
- Initial TF101 implementation (with U-boot) for mainline kernel (thanks [mnidza](https://github.com/mnidza/)): https://github.com/grate-driver/linux/pull/23
- U-boot source: https://github.com/mnidza/u-boot-public/tree/tf101
- UEnv.txt (needed for U-boot in MicroSD Card boot partition): https://github.com/grate-driver/linux/files/6227021/uEnv.txt
- TF101 pmaports: https://gitlab.com/antoni.aloytorrens/pmaports/-/tree/wip-tf101-pmaports
- PostmarketOS: https://postmarketos.org/
<br/>
