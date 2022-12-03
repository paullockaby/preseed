# System Installer "preseed" Script

This is a script for building a new installer for Debian "bullseye" using a technique called a "preseeding". By using a "preseed" script we tell the installer the answer to a lot of routine questions to give ourselves a standard environment for new hosts. The script in this repository does the following:

* Creates a "paul" user with a known password and an SSH authorized_keys file and a known good environment.
* Builds an LVM configuration with an 8GB swap partition and the rest of the disk for data using XFS as a file system instead of ext4.
* Enables "security" and "updates" repositories but does not enable backports, sources or automatic updates.
* Fixes the systemd timesync configuration.
* Fixes the /etc/resolv.conf file.
* Installs standard system utilities and an SSH server.
* Installs some additional basic, common software.

When using the installer there are still several questions that you must answer. These include:

* If there are multiple active interfaces you must choose one.
* What IP address and host name to use for this new host.
* Which disk to use for the installation. **WARNING!** This disk *will* be erased.
* Which disk will receive the master boot record.

Otherwise this will create an entirely automated installation.

# How To Use This Script

Follow these steps to run this script:

1. Get on a Debian host!
2. [Download an netinstall image from the Debian website](https://www.debian.org/distrib/netinst).
3. Clone this repository.
4. Install these libraries: `apt-get install -y --no-install-recommends xorriso isolinux pwgen`
5. Run the build script: `./build /path/to/debian-11.x.x-amd64-netinst.iso /path/to/preseed-debian-11.x.x-amd64-netinst.iso`
6. Use the new ISO file to build your host.

Note that the preseed.cfg file has no password for logging in as the "paul" user. You can set a password or just use SSH keys. You can change the password by following these steps:

1. Put the new password in a text file. Call it something like "newpassword.txt".
2. Generate the new password: `cat newpassword.txt | mkpasswd -s -m sha-512 -S "$(pwgen -ns 16 1)"`
3. Place that new password in the preseed.cfg file.
4. Remove newpassword.txt and *definitely* **do not** put it into source control.

# Using the "preseed" ISO

Use this ISO just like you would any other install ISO. A preseeded ISO file can build baremetal systems or virtual machines. It is only useful for on-premise instances and is not useful for cloud instances.

# Building a USB Boot Disk on macOS

I'm always having to look this up so here are the steps.

1. Unmount the disk where `diskXX` is your USB disk: `diskutil unmountDisk /dev/diskXX`
2. Convert the ISO file into a DMG file: `hdiutil convert -format UDRW -o preseed ./preseed.iso`
3. Write the DMG file to your USB disk: `sudo dd if=./preseed.dmg of=/dev/diskXX bs=1m`

Your thumb drive is now bootable.

# Building a USB Boot Disk on Linux

I'm also always having to look this up so here are the steps.

1. Attach the USB disk. Use `lsblk` to see what the device label is that the USB disk has received.
2. Write the ISO file to your USB disk: `sudo dd bs=4M if=preseed.iso of=/dev/XXX conv=fdatasync`

# More Information

These links are where I got most of the information for building this preseed script.

* [https://wiki.debian.org/DebianInstaller/Preseed](https://wiki.debian.org/DebianInstaller/Preseed)
* [https://wiki.debian.org/DebianInstaller/Preseed/EditIso](https://wiki.debian.org/DebianInstaller/Preseed/EditIso)
* [https://github.com/pin/debian-vm-install](https://github.com/pin/debian-vm-install)
* [https://www.debian.org/releases/squeeze/example-preseed.txt](https://www.debian.org/releases/squeeze/example-preseed.txt)
* [https://serverfault.com/questions/722021/preseeding-debian-install-efi](https://serverfault.com/questions/722021/preseeding-debian-install-efi)
* [https://github.com/dsgnr/ubuntu-16.04-unattended-install/blob/master/preseed.cfg](https://github.com/dsgnr/ubuntu-16.04-unattended-install/blob/master/preseed.cfg)
