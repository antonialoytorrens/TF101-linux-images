# Asus Transformer TF101 Linux Images
**These are some linux images based on Jhinta's 3.1 Kernel.**<br/>
https://forum.xda-developers.com/showthread.php?t=1683145

-----

Please follow the guide in https://forum.xda-developers.com/showthread.php?t=2684854 to setup Linux RootBind Method on the Asus Transformer TF101.

-----

There are three images:

### Xubuntu 14.04.6

A full blown Xubuntu Desktop in Ubuntu 14.04.6 LTS version.

### Debian Jessie 8.11

A full blown XFCE Desktop in Debian 8.11 Jessie version.

### Debian Jessie 8.11 - All

A full blown XFCE Desktop in Debian 8.11 Jessie version. It comes also with Libreoffice 6.1 series installed.

-----

#### Extras:

The extras are the collection of Libreoffice 6.1 series, built in Ubuntu 14.04.

I collected all necessary packages to run it successfully in Debian and Ubuntu. Please visit its PPA:
https://launchpad.net/~libreoffice/+archive/ubuntu/ppa

*** It comes with a pre-installer.txt to install its dependencies ***

-----

#### What works

##### Xubuntu 14.04.6

* XServer (with apt-marked some packages)
* Network
* Touchpad
* Keyboard
* Chromium (with HTML5)
* Brightness (with brightness-controller, please view _https://github.com/LordAmit/Brightness_)

##### Debian 8.11

* XServer (up-to-date version)
* Network
* Touchpad
* Keyboard
* Chromium (with HTML5)
* Brightness (with xfce4-power-plugin)
* Xscreensaver

##### What does not work

* The latest version of Firefox in Ubuntu 14.04 (also does not work in Debian)
* The latest version of Firefox-ESR in Debian Jessie 8.11 (also does not work in Xubuntu)

To workaround this, you can install the latest Firefox from Ubuntu 12.04 Precise or the latest Firefox-ESR from Debian Wheezy 7.11 (you can install them in both systems, Xubuntu and Jessie)

#### Not tested

##### Xubuntu 14.04.6

* Bluetooth
* Sound
* Xscreensaver

##### Debian 8.11

* Bluetooth
* Sound
