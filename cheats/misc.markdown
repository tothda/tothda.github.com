How to make a bootable usb drive
--------------------------------

diskutil list
diskutil unmountDisk /dev/disk1
sudo dd if=/Users/tothda/Documents/winxp/XP_PRO_SP3_ENG.iso of=/dev/disk1 bs=1m
diskutil eject /dev/disk1
