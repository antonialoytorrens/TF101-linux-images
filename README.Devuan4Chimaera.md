# Native Linux on the Asus Eee Pad Transformer TF101 using Devuan

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

## Setup

You can download the image [here](https://sourceforge.net/projects/tf101-linux-images/files/devuan-4-chimaera-grate/rootbind/Devuan4-tf101-armhf_20220115.zip) and then follow this [existing method](https://github.com/antonialoytorrens/TF101-linux-images/issues/11), but replacing Debian filesystem with the Devuan one.

However, you may also want to install U-Boot on the TF101 (very recommended), but that requires some additional work (a bit harder to setup), [see the Debian README for example](https://github.com/antonialoytorrens/TF101-linux-images/blob/master/README.Debian11Bullseye.md) with the Devuan file system.

## Login information

**User**: tf101

**Password**: devuan

**Root password**: DEVUAN

**VNC server password**: Devuan

## Credits
[See here](https://github.com/antonialoytorrens/TF101-linux-images#special-thanks-and-rellevant-links) for credits.
