# Native Linux on the Asus Eee Pad Transformer TF101 using PostmarketOS

This is a guide about how to run Native Linux on the Asus Eee Pad Transformer TF101.

According to _Wikipedia_:  
*_"" PostmarketOS (stylized as postmarketOS and abbreviated as pmOS) is a free and open-source operating system under development primarily for smartphones, based on the Alpine Linux distribution. ""_*

**What you will need:**  
*Note: I have used a computer with Linux in this guide. I don't know if it can be done with other OS because I haven't tested it.*

-   A computer with Linux or Windows.
-   SD Card or Micro SD Card (optional but very recommended, preferred Micro SD Card).

*Note: I am currently using a 32 GB SanDisk Micro SD Card, so I do not depend of the dock keyboard.  
Furthermore, flashing postmarketOS into an SD Card is more safe, because it does not touch any TF101 internal partitions (except the kernel blob).*  
  
There are two ways of doing this, using my prebuilt image file or doing everything from scratch.

See [Windows](https://github.com/antonialoytorrens/TF101-linux-images/blob/master/README.md#windows-tested-with-xfce-only) guide or [Linux](https://github.com/antonialoytorrens/TF101-linux-images/blob/master/README.md#linux) guide.

## Linux
  
## Using my prebuilt image files:

Download it from [https://sourceforge.net/projects/tf1.../postmarketOS/](https://sourceforge.net/projects/tf101-linux-images/files/postmarketOS/)  
**There are three images at the moment: XFCE, MATE and JWM. XFCE is recommended because it is tested more frequently than other images.**  

---
  
 **1. Flashing the image to SD Card using dd**:
Take special care with dd command when flashing the image. One step wrong and your hard drive is lost. I am not responsible of anything.  
Path to SD Card may vary, mine is in /dev/sdb. Use  _fdisk -l_  command to identify your SD Card location.

-   Insert your SD Card or Micro SD Card to your computer
-   Unzip downloaded file

**My SD Card is located in /dev/sdb**, so I do:

<pre>$ sudo dd if=/path/of/asus-tf101.img of=/dev/sdb status=progress</pre>

**Extend with  _gparted_  or other disk manipulation tool the ext4 root filesystem to occupy all SD Card disk space available.**  
In gparted, don't put swap into the SD Card because it can be harmful. Consider using zram, but I haven't tested it.  

  ---
  
**2. TF101 Recovery mode**:  

Now put your Asus TF101 into recovery mode. Then, in your Linux computer:  

<pre>$ sudo adb push /path/of/blob-asus-tf101 /cache</pre>

Flash the blob (kernel) to the TF101: 

<pre>$ sudo adb shell</pre>

And in TF101 terminal (adb):

<pre># dd if=/cache/blob-asus-tf101 of=/dev/block/mmcblk0p4</pre>

---

**3. Booting PostmarketOS**:

-   **Insert your SD Card to the dock label or your Micro SD Card to the tablet.** Now power it up and should boot into PostmarketOS.
  
| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | ubuntu |
  
Please change the password later whenever possible.  
Log in into XFCE, connect it to a WiFi network and do the following steps:  
  
- **Setup your keyboard layout**:

<pre>$ sudo apk add setxkbmap
$ setxkbmap "YOUR KEYBOARD LAYOUT"</pre>

*Please search the keyboard layout code of your country. Normally it's your country abbreviation: us, de, it, es, etc.*  
  
- **Set keyboard layout automatically at startup**:  

<pre>cat > /etc/local.d/keyboard-layout.start << "EOF"
#!/bin/sh
setxkbmap "YOUR KEYBOARD LAYOUT"
EOF</pre>

---

**Reboot and done!**  

<br/>

---

<br/>
  
## Doing XFCE from scratch:

### 1. Flashing the image to SD Card (or internal partition): 
 
Please install _pmbootstrap_ tool using this guide:  
[https://wiki.postmarketos.org/wiki/Installation_guide](https://wiki.postmarketos.org/wiki/Installation_guide)  
Then follow the installation steps of the wiki:  
[https://wiki.postmarketos.org/wiki/A...r_(asus-tf101)](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101))  
**Type  _none_  when  _pmbootstrap_  is prompting for graphical interface.
Install firmware-aosp-broadcom-wlan,wireless-tools,wpa_supplicant packages when  _pmbootstrap_  is prompting for extra packages to get WiFi firmware package.**

*Note: /dev/block/mmcblk0p1 is the first partition of the TF101. It can be installed there if you don't have any SD Card or Micro SD Card, but it's only 500 MB approx of space.  
I would consider buying an SD Card or Micro SD Card if you don't have one.*  

It cannot be installed on any other internal partitions because it can brick your device, so be careful.

**Extend with  _gparted_  or other disk manipulation tool the ext4 root filesystem to occupy all SD Card disk space available.**  
In gparted, don't put swap into the SD Card because it can be harmful. Consider using zram, but I haven't tested it.  

---
  
### 2. Booting PostmarketOS:

-   Insert your SD Card to the dock label or your Micro SD Card to the tablet. Now power it up and should boot into PostmarketOS.

| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | ubuntu |
  
Please change the password whenever possible. 

---
  
### 3. To get WiFi working:

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_abg.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_abg.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_apsta.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_apsta.bin</pre>

Reboot and done.  
  
Now connect to WiFi using wpa_supplicant, here's a helping link:  
[https://askubuntu.com/questions/1384...e-command-line](https://askubuntu.com/questions/138472/how-do-i-connect-to-a-wpa-wifi-network-using-the-command-line)  
  
*Note: If you want to setup your TF101 as a server, do only the proftpd step and you are finished. You can also mount your internal TF101 hard drives to get additional disk space.*

As I will mention below, use only /dev/block/mmcblk0p1, /dev/block/mmcblk0p4 and /dev/block/mmcblk0p7 (in media/ folder) partitions. Do not resize them, only store files.  
  
*Note: Using ssh connection to copy-paste all the process is very recommended.*  

---
  
### 4. Basic XFCE setup:  

I have chosen XFCE because it's complete and lightweight. You can pick other desktop environment, but be careful because the Transformer TF101 only has 1GB of RAM.

**To install a full XFCE desktop:**

<pre>$ sudo apk add xorg-server udev xinit dbus-x11 xf86-video-opentegra xf86-video-vesa xf86-video-fbdev \
xf86-video-modesetting xf86-input-keyboard xf86-input-mouse xf86-input-evdev libdrm libinput \
libvdpau-tegra mesa-dri-gallium nano libreoffice gimp pavucontrol handbrake netsurf xfce4 \
xfce4-whiskermenu-plugin xfce4-terminal xfce4-statusnotifier-plugin-lang xfce4-session-lang leafpad \
leafpad-lang network-manager-applet onboard lightdm lightdm-gtk-greeter faenza-icon-theme adwaita-icon-theme \
rhythmbox celluloid guvcview gnome-screenshot gnome-calculator sylpheed keepassxc gparted print-manager \
print-manager-lang xrandr xgamma wget gvfs-fuse gvfs-smb fuse-openrc ristretto redshift transmission baobab \
thunar-volman udisks2 cups cups-libs cups-pdf cups-client cups-filters hplip libusb ghostscript python3 \
py-reportlab libjpeg libjpeg-turbo net-snmp wireless-tools wpa_supplicant neofetch htop zip unzip xdg-utils \
consolekit2 polkit xfce-polkit</pre>

---  

### 5. File Transfer Protocol (FTP):  
- **Client**:

<pre>$ sudo apk add filezilla</pre>

*Note: If Filezilla does not work, try gftp:*  

<pre>$ wget https://github.com/masneyb/gftp/releases/download/2.6.0b/gftp-2.6.0b.tar.xz -O /tmp/gftp-2.6.0b.tar.xz
$ sudo apk add gtk+2.0
$ sudo apk add --virtual build-base gftp-pack gtk+2.0-dev
$ cd /tmp/gftp-2.6.0b
$ ./configure --prefix=/usr && make -j2 && sudo make install
$ cd ~ && rm -rf /tmp/gftp-2.6.0b</pre>
  
- **Server**:

<pre>$ sudo apk add proftpd</pre>

---

### 6. Enable services:  

<pre>$ sudo rc-update add lightdm
$ sudo rc-update add dbus
$ sudo rc-update add cupsd
$ sudo rc-update add local
$ sudo rc-update add proftpd
$ sudo rc-update add polkit</pre>

  ---
  
### 7. Fixing (workaround) keyboard F(1-12) keys with xdotool, xbindkeys, xbacklight AND alsa-utils (sound does not work at the moment):

<pre>$ sudo apk add xdotool xbindkeys bluez-alsa alsa-utils alsa-tools-gui
$ sudo wget https://raw.githubusercontent.com/wavexx/acpilight/master/xbacklight -O /usr/bin/xbacklight
$ sudo chmod +x /usr/bin/xbacklight</pre>

---

#### 7.1. Start and stop WiFi and Bluetooth (function not implemented, sorry):  
  
---

#### 7.2. Hide and show desktop (home and window key, respectively) (testing):

<pre>cat > /usr/bin/window-minimizer.sh << "EOF"
#!/bin/sh
wmctrl -k on
EOF</pre>

<pre>cat > /usr/bin/window-maximizer.sh << "EOF"
#!/bin/sh
for w in $(xdotool search --name '.*'); do
    xdotool windowmaximize "$w"
done
EOF</pre>

- **Give permissions to executables:**  

<pre>$ sudo chmod +x /usr/bin/window-maximizer.sh /usr/bin/window-minimizer.sh</pre>

---

#### 7.3. xbindkeysrc file:  
Being your main unprivileged user, do:  

<pre>cat > $HOME/.xbindkeysrc << "EOF"

###########################
#xbindkeys configuration #
###########################
#
#Version: 0.1.3
#
#If you edit this, do not forget to uncomment any lines that you change.
#The pound(#) symbol may be used anywhere for comments.
#
#A list of keys is in /usr/include/X11/keysym.h and in
#/usr/include/X11/keysymdef.h 
#The XK_ is not needed. 
#
#List of modifier (on my keyboard): 
#Control, Shift, Mod1 (Alt), Mod2 (NumLock), 
#Mod3 (CapsLock), Mod4, Mod5 (Scroll). 
#
#Another way to specifie a key is to use 'xev' and set the 
#keycode with c:nnn or the modifier with m:nnn where nnn is 
#the keycode or the state returned by xev 
#
#This file is created by xbindkey_config 
#The structure is : 
##Remark 
#"command" 
#m:xxx + c:xxx 
#Shift+... 


#keystate_numlock = enable
#keystate_scrolllock = enable
#keystate_capslock = enable


#Window minimize
#for w in $(xdotool search --name '.*'); do 
#xdotool windowminimize "$w" 
#done

#Enable/Disable WiFi
"/usr/bin/checkservice.sh networkmanager"
    m:0x0 + c:67
    F1 

#Enable/Disable Bluetooth
"/usr/bin/checkservice.sh bluetooth"
    m:0x0 + c:68
    F2 

#Update Screen Detection
"echo 'In Development'"
    m:0x0 + c:69
    F3 

#Decrease Brightness
"/usr/bin/xbacklight -dec 10"
    m:0x0 + c:70
    F4 

#Increase Brightness
"/usr/bin/xbacklight -inc 10"
    m:0x0 + c:71
    F5 

#Auto Brightness
"echo 'In Development'"
    m:0x0 + c:72
    F6 

#Make Screenshot
"/usr/bin/scrot $HOME/Screenshot-'$(date)'.png"
    m:0x0 + c:73
    F7 

#Open Browser
"/usr/bin/exo-open --launch WebBrowser"
    m:0x0 + c:74
    F8 

#Open XFCE Settings
"/usr/bin/xfce4-settings-manager"
    m:0x0 + c:75
    F9 

#Go Back Video
"echo 'In Development'"
    m:0x0 + c:76
    F10 

#Play/Pause Video
"echo 'In Development'"
    m:0x0 + c:95
    F11 

#Go Forward Video
"echo 'In Development'"
    m:0x0 + c:96
    F12 

#Mute Audio
"/usr/bin/amixer -D pulse sset Master mute"
    m:0x0 + c:78
    Scroll_Lock

#Unmute Audio (Same Key)? "/usr/bin/amixer -D pulse sset Master unmute"

#Decrease Audio
"/usr/bin/amixer -D pulse sset Master 5%-"
    m:0x0 + c:127
    Pause 

#Increase Audio
"/usr/bin/amixer -D pulse sset Master 5%+"
    m:0x0 + c:118
    Insert 

#Lock Screen
"/usr/bin/xflock4"
    m:0x0 + c:119
    Delete 

#Home Key
"/usr/bin/window-minimizer.sh"
    m:0x0 + c:180
    XF86HomePage 

#Search Key
"/usr/bin/xfce4-appfinder"
    m:0x0 + c:64
    Alt + Alt_L 

#Window Key
"echo 'Trying to show all windows... In Development'"
    m:0x0 + c:147
    XF86MenuKB 

#
#End of xbindkeys configuration

EOF
</pre>
  
 ---
  
### 8. Lightdm GTK Greeter:  
Lightdm uses onscreen keyboard, so it will be very useful to integrate onboard (onscreen keyboard) with lightdm at login.  

<pre>cat > /etc/lightdm/lightdm-gtk-greeter.conf << "EOF"
[greeter]
background=/usr/share/backgrounds/xfce/xfce-blue.jpg
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
  
### 9. Enable config files at startup:  
For comfort, I decided to autostart services using config files.  

---
  
#### 9.1. xbindkeys:  
Enable xbindkeys automatically when logged in:  

<pre>cat > /etc/xdg/autostart/xbindkeys.desktop << "EOF"
[Desktop Entry]
Type=Application
Name=xbindkeys
Exec=/usr/bin/xbindkeys -n
NoDisplay=true
EOF</pre>
  
---

#### 9.2. onboard:  
Enable onscreen keyboard automatically when logged in:  

<pre>cat > /etc/xdg/autostart/onboard-autostart.desktop << "EOF"
[Desktop Entry]
Name=Onboard
GenericName=Onboard onscreen keyboard
Comment=Flexible onscreen keyboard
Exec=onboard --theme=Nightshade -s 800x205 -x 250 -y 550 --startup-delay=3.0
Icon=onboard
Type=Application
NoDisplay=true
X-Ubuntu-Gettext-Domain=onboard
AutostartCondition=GSettings org.gnome.desktop.a11y.applications screen-keyboard-enabled
X-GNOME-AutoRestart=true
OnlyShowIn=GNOME;Unity;MATE;
EOF</pre>

---
  
**9.3. xflock4 (lock screen)**:  

Note: https://github.com/the-cavalry/light-locker/issues/138

There's an issue with some devices (TF101 included) when locking the screen, because it turns black and does not wake up, so follow these steps:
<pre>$ sudo rm -rf /usr/bin/xflock4

As root:
cat > /usr/bin/xflock4 << "EOF"
#First test for the command set in the session's xfconf channel
LOCK_CMD=$(xfconf-query -c xfce4-session -p /general/LockCommand)

#Lock by xscreensaver or gnome-screensaver, if a respective daemon is running
for lock_cmd in \
    "$LOCK_CMD" \
    "xfce4-screensaver-command --lock" \
    "xscreensaver-command -lock" \
    "gnome-screensaver-command --lock"
do
    if [ ! -z "$lock_cmd" ]; then
        $lock_cmd >/dev/null 2>&1 && exit
    fi
done

#else run another access locking utility, if installed
for lock_cmd in \
  "xlock -mode blank" \
  "slock"
  do
    set -- $lock_cmd
    if command -v -- $1 >/dev/null 2>&1; then
        $lock_cmd >/dev/null 2>&1 &
        # turn off display backlight:
        xset dpms force off
        exit
    fi
    
    dm-tool switch-to-greeter
done

#else access locking failed
exit 1
EOF


#chmod +x /usr/bin/xflock4</pre>

---
  
**9.4. Enable brightness controller:**<br/>
If for some reason xfce4-power-manager cannot control your brightness screen, do:

<pre>cat > /etc/local.d/enable-backlight.start << "EOF"
#!/bin/sh
/bin/chmod -R 777 /sys/devices/platform/pwm-backlight/backlight/
EOF</pre>

<pre># chmod +x /etc/local.d/enable-backlight.start</pre>

Reboot and done.  
  
If the error still persists, you can install a Brightness Controller (a bit laggy, but works):  

<pre>$ sudo apk add wxgtk3 py3-wxpython

$ wget https://github.com/antonialoytorrens/Brightness/archive/2.0.1.zip -O Brightness-Controller-MOD.zip

$ unzip Brightness-Controller-MOD.zip

$ sudo sh Brightness/src/debian/install</pre>

---

**9.5. Automount drives automatically  (except the recovery partition, do not touch!)**: 

<pre>cat > /etc/local.d/automounting-drives.start << "EOF"
#!/bin/sh
#Android Apps Partition
mkdir -p /media/mmcblk0p1/ && sudo mount -t ext4 /dev/mmcblk0p1 /media/mmcblk0p1/

#Android User Data Partition
#USP
mkdir -p /media/mmcblk0p4 && sudo mount -t ext4 /dev/mmcblk0p4 /media/mmcblk0p4/
#UDA
mkdir -p /media/mmcblk0p7 && sudo mount -t ext4 /dev/mmcblk0p7 /media/mmcblk0p7/
EOF</pre>

---

**9.6. Make UDA (Android User Data) partition accessible by non-privileged user at startup**: 

<pre>cat > /etc/xdg/autostart/automounting-drives.desktop << "EOF"
[Desktop Entry]
Type=Application
Name=Automount Drives
Exec=/bin/sh -c "mkdir -p $HOME/Desktop/UDA-sdcard/ && ln -sf /media/mmcblk0p7/media/ $HOME/Desktop/UDA-sdcard/"
NoDisplay=true
EOF</pre>

---

**9.7. Enable redshift program at startup (used random location) (redshift does not seem to work)**:  
Note: redshift syntax is: 
<pre>redshift -l LATITUDE:LONGITUDE</pre>
 for latitude and longitude;
<br/>
<pre>redshift -P -O TEMPERATURE</pre>
for temperature.  

---

---

### 10. Bluetooth:

Bluetooth can get working via `brcm_patchram_plus` package.

It is partially working, as it can be enabled only via Linux Terminal and it's using bluez-deprecated package.

<pre>$ sudo apk add bluez bluez-deprecated</pre>

#### 10.1. Start Bluetooth automatically:

<pre>cat > /etc/local.d/start-bluetooth.start << "EOF"
#!/bin/sh

#Start brcm-patchram-plus for TF101

rfkill unblock 0
/usr/sbin/brcm_patchram_plus -d --patchram /lib/firmware/postmarketos/brcm/bcm4329.hcd --baudrate 921600 --bd_addr $(cat /lib/firmware/postmarketos/brcm/mac.txt) --enable_hci /dev/ttyHS2&
EOF
</pre>

  
### 11. Make startup scripts executable:  

<pre>$ sudo chmod +x /etc/local.d/*.start</pre>

---

### 12. Reboot and done! 
  
<br/>

---

<br/>

**What does not work:**  
- Browsing (using modern web browser, segfault. Netsurf works)  
*Note: Firefox will never work from version 53. See <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1305815">this link</a>*.
- Sound  
- Bluetooth  
- Some xbindkeys  
- Starting - stopping WiFi and Bluetooth with non-privileged user (maybe with rfkill?)  
- Probably some other programs I haven't tested  
  
 ---

<br/>

## Doing MATE from scratch:

### 1. Flashing the image to SD Card (or internal partition): 
 
Please install _pmbootstrap_ tool using this guide:  
[https://wiki.postmarketos.org/wiki/Installation_guide](https://wiki.postmarketos.org/wiki/Installation_guide)  
Then follow the installation steps of the wiki:  
[https://wiki.postmarketos.org/wiki/A...r_(asus-tf101)](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101))  
**Type  _none_  when  _pmbootstrap_  is prompting for graphical interface.
Install firmware-aosp-broadcom-wlan,wireless-tools,wpa_supplicant packages when  _pmbootstrap_  is prompting for extra packages to get WiFi firmware package.**

*Note: /dev/block/mmcblk0p1 is the first partition of the TF101. It can be installed there if you don't have any SD Card or Micro SD Card, but it's only 500 MB approx of space.  
I would consider buying an SD Card or Micro SD Card if you don't have one.*  

It cannot be installed on any other internal partitions because it can brick your device, so be careful.

**Extend with  _gparted_  or other disk manipulation tool the ext4 root filesystem to occupy all SD Card disk space available.**  
In gparted, don't put swap into the SD Card because it can be harmful. Consider using zram, but I haven't tested it.  

---
  
### 2. Booting PostmarketOS:

-   Insert your SD Card to the dock label or your Micro SD Card to the tablet. Now power it up and should boot into PostmarketOS.

| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | ubuntu |
  
Please change the password whenever possible. 

---
  
### 3. To get WiFi working:

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_abg.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_abg.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_apsta.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_apsta.bin</pre>

Reboot and done.  
  
Now connect to WiFi using wpa_supplicant, here's a helping link:  
[https://askubuntu.com/questions/1384...e-command-line](https://askubuntu.com/questions/138472/how-do-i-connect-to-a-wpa-wifi-network-using-the-command-line)  
  
*Note: If you want to setup your TF101 as a server, do only the proftpd step and you are finished. You can also mount your internal TF101 hard drives to get additional disk space.*

As I will mention below, use only /dev/block/mmcblk0p1, /dev/block/mmcblk0p4 and /dev/block/mmcblk0p7 (in media/ folder) partitions. Do not resize them, only store files.  
  
*Note: Using ssh connection to copy-paste all the process is very recommended.*  

---
  
### 4. Basic MATE setup:

The MATE Desktop Environment is the continuation of GNOME 2. It provides an intuitive and attractive desktop environment using traditional metaphors for Linux and other Unix-like operating systems.

MATE is under active development to add support for new technologies while preserving a traditional desktop experience.

<br/>

**To install a full MATE desktop:**

<pre>
$ sudo apk update

- Add all available True Type Fonts:
$ ttfs=$(apk search -q ttf- | grep -v '\-doc')

- GVFS packages:
$ gvfs_pkgs=$(apk search gvfs -q | grep -v '\-dev' | grep -v '\-lang' | grep -v '\-doc')

$ sudo apk add xorg-server udev xinit dbus dbus-x11 xf86-video-opentegra xf86-video-vesa xf86-video-fbdev \
xf86-video-modesetting xf86-input-keyboard xf86-input-mouse xf86-input-evdev libdrm libinput \
libvdpau-tegra mesa-dri-gallium nano mate-desktop-environment lightdm lightdm-gtk-greeter udev \
adwaita-icon-theme faenza-icon-theme onboard cups cups-libs cups-pdf cups-client cups-filters hplip neofetch \
network-manager-applet abiword gnumeric handbrake gimp sylpheed keepassxc gparted netsurf xrandr \
transmission wget xbindkeys exo $gvfs_pkgs $ttfs

- Patch to mate-screensaver

$ sudo apk del mate-screensaver
$ sudo ln -s /usr/etc/xdg/menus/ /etc/xdg/menus
$ sudo apk add mate-screensaver

- Delete ttf-opensans error
$ sudo apk del ttf-opensans
</pre>

<br/>

**Recommendation:**
- <b>Change mate-terminal colors</b>: Edit < Profile Preferences...
Background -> Black
Text -> White

---

### 5. Follow the same steps as chapters 7, 8 and 9 in XFCE.

https://github.com/antonialoytorrens/TF101-linux-images#7-fixing-workaround-keyboard-f1-12-keys-with-xdotool-xbindkeys-xbacklight-and-alsa-utils-sound-does-not-work-at-the-moment

---

### 6. Reboot and done!

---


<br/>

## Doing JWM from scratch (designed to fit in /system partition):

### 1. Flashing the image to SD Card (or internal partition): 
 
Please install _pmbootstrap_ tool using this guide:  
[https://wiki.postmarketos.org/wiki/Installation_guide](https://wiki.postmarketos.org/wiki/Installation_guide)  
Then follow the installation steps of the wiki:  
[https://wiki.postmarketos.org/wiki/A...r_(asus-tf101)](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101))  
**Type  _none_  when  _pmbootstrap_  is prompting for graphical interface.
Install firmware-aosp-broadcom-wlan,wireless-tools,wpa_supplicant packages when  _pmbootstrap_  is prompting for extra packages to get WiFi firmware package.**

*Note: /dev/block/mmcblk0p1 is the first partition of the TF101. It can be installed there if you don't have any SD Card or Micro SD Card, but it's only 500 MB approx of space.  
I would consider buying an SD Card or Micro SD Card if you don't have one.*  

It cannot be installed on any other internal partitions because it can brick your device, so be careful.

**Extend with  _gparted_  or other disk manipulation tool the ext4 root filesystem to occupy all SD Card disk space available.**  
In gparted, don't put swap into the SD Card because it can be harmful. Consider using zram, but I haven't tested it.  

---
  
### 2. Booting PostmarketOS:

-   Insert your SD Card to the dock label or your Micro SD Card to the tablet. Now power it up and should boot into PostmarketOS.

| Login Information |  |
| -- | -- |
| **User** | tf101 |
| **Password** | ubuntu |
  
Please change the password whenever possible. 

---
  
### 3. To get WiFi working:

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_abg.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_abg.bin</pre>

<pre>$ sudo mv /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcm4329_apsta.bin /lib/firmware/postmarketos/bcmdhd/bcm4329/fw_bcmdhd_apsta.bin</pre>

Reboot and done.  
  
Now connect to WiFi using wpa_supplicant, here's a helping link:  
[https://askubuntu.com/questions/1384...e-command-line](https://askubuntu.com/questions/138472/how-do-i-connect-to-a-wpa-wifi-network-using-the-command-line)  
  
*Note: If you want to setup your TF101 as a server, do only the proftpd step and you are finished. You can also mount your internal TF101 hard drives to get additional disk space.*

As I will mention below, use only /dev/block/mmcblk0p1, /dev/block/mmcblk0p4 and /dev/block/mmcblk0p7 (in media/ folder) partitions. Do not resize them, only store files.  
  
*Note: Using ssh connection to copy-paste all the process is very recommended.*

---

### 4. Basic JWM setup:

The MATE Desktop Environment is the continuation of GNOME 2. It provides an intuitive and attractive desktop environment using traditional metaphors for Linux and other Unix-like operating systems.

MATE is under active development to add support for new technologies while preserving a traditional desktop experience.

<br/>

**To install a full JWM desktop:**

<pre>
$ sudo apk add jwm xorg-server udev xinit dbus-x11 xf86-video-opentegra \
xf86-input-keyboard xf86-input-mouse libdrm libinput netsurf xvkbd xbindkeys neofetch zip \ 
unzip tar conky lightdm-gtk-greeter xarchiver consolekit2 motif abiword gnumeric feh \
lxterminal baobab galculator scrot network-manager-applet wget python3 pcmanfm leafpad
</pre>

#### 4.1. Installing xpdf (pdf reader)
I have done a minimal version of xpdf to install.
Please follow these steps:
<pre>
$ wget https://github.com/antonialoytorrens/TF101-linux-images/raw/master/postmarketOS/jwm-utils/xpdf-3.04-minimal.tar -O /tmp/xpdf-3.04-minimal.tar
$ cd /tmp/ && tar -xvf xpdf-3.04-minimal.tar && sudo cp -r /tmp/xpdf-3.04/etc/ / && sudo cp  -r /tmp/xpdf-3.04/usr/ /
</pre>

#### 4.2. Compiling xpdf (pdf reader)
I have followed the guide from linux from scratch: http://www.linuxfromscratch.org/blfs/view/6.3/pst/xpdf.html

So, type the following commands:

<pre>
$ wget https://dl.xpdfreader.com/old/xpdf-3.04.tar.gz -O /tmp/xpdf-3.04.tar.gz && cd /tmp && tar -xvf xpdf-3.04.tar.gz && cd xpdf-3.04

$ sudo apk add libpng freetype motif libpaper

$ sudo apk add --virtual xpdf-pkgs build-base libpng-dev xorg-server-dev freetype-dev motif-dev libpaper-dev

$ ./configure --prefix=/usr --sysconfdir=/etc --with-freetype2-includes=/usr/include/freetype2 --enable-opi --enable-multithreaded --enable-a4-paper && make j2 && sudo make install
</pre>

---

### 5. Follow the same steps as chapters 7, 8 and 9 in XFCE.

https://github.com/antonialoytorrens/TF101-linux-images#7-fixing-workaround-keyboard-f1-12-keys-with-xdotool-xbindkeys-xbacklight-and-alsa-utils-sound-does-not-work-at-the-moment

---

### 6. Brightness Controller GTK Utility

I have written a GTK application to control the brightness of the device. The source code is located here: https://github.com/antonialoytorrens/brightness-controller-gtk

Execute the following commands to directly install it:

<pre>
$ sudo wget https://raw.githubusercontent.com/antonialoytorrens/TF101-linux-images/master/postmarketOS/jwm-utils/brightness-controller-gtk -O /usr/bin/brightness-controller-gtk

$ sudo wget https://raw.githubusercontent.com/antonialoytorrens/TF101-linux-images/master/postmarketOS/jwm-utils/sun.png -O /usr/share/icons/jwm/sun.png

$ sudo ln -s /usr/share/icons/sun.png /usr/share/icons/jwm/sun.png

$ sudo chmod +x /usr/bin/brightness-controller-gtk
</pre>

---

### 7. Predesigned PostmarketOS background:

Download a PostmarketOS stock background for the JWM desktop.

<pre>
$ sudo mkdir -p /usr/share/backgrounds
$ sudo wget https://user-images.githubusercontent.com/6928199/29728159-235fbd0c-89d7-11e7-814c-f1482bbcb8bc.png -O /usr/share/backgrounds/jwm.png
</pre>

---

### 8. Reboot and done!

---

## Windows (tested with XFCE only)

1. Download adb platform tools here: https://dl.google.com/android/repository/platform-tools-latest-windows.zip
2. Extract it (for example, in Downloads folder).
3. Download the XFCE Linux image here: https://sourceforge.net/projects/tf101-linux-images/files/latest/download
4. Extract the zip file. You will get a blob file and the XFCE Linux image file. In Windows it's easier downloading the kernel zip, so let's remove the blob file.
5. Download the win32diskimager program for writing images to a SD Card here: https://sourceforge.net/projects/win32diskimager/files/latest/download
6. Insert the micro SD Card or the SD Card into your computer.
7. Open the win32diskimager program and flash the XFCE Linux img file.
8. Download the kernel file here: https://github.com/antonialoytorrens/TF101-linux-images/raw/master/postmarketOS/kernel_zip/PostmarketOS-kernel-3.1.10-15-r2.zip
9. Open the adb platform tools folder in the cmd (C:\Users\user\Downloads\platform-tools).
10. Connect your device (in TWRP recovery) and do: adb push PostmarketOS-kernel-3.1.10-15-r2.zip /sdcard.
11. Go into your device and flash the postmarketOS kernel zip.
12. Done!

<br/>
<br/>

**TO DO:**
- Mentioned in the wiki:  [https://wiki.postmarketos.org/wiki/A...r_(asus-tf101)](https://wiki.postmarketos.org/wiki/ASUS_Eee_Pad_Transformer_(asus-tf101))  

---
  
**Special Thanks and rellevant links:**  
- [https://forum.xda-developers.com/sho...648862&page=43](https://forum.xda-developers.com/showthread.php?t=2648862&page=43)  
- [https://github.com/jmrohwer/TF101-GNU-kernel](https://github.com/jmrohwer/TF101-GNU-kernel)  
- [https://forum.xda-developers.com/sho....php?t=1683145](https://forum.xda-developers.com/showthread.php?t=1683145)  
- [https://postmarketos.org/](https://postmarketos.org/)
<br/>

---
