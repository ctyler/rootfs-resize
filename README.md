rootfs-resize 
=============

Version 2.0 - 2013-01-14
------------------------

This is a rewrite, in python, of the previous rootfs-resize 
bash script. This version uses libparted (via pyparted) and
better logic for improved robustness.

rootfs-resize is a python service script designed for use on
computers that use an SD card for storage, such as ARM development
boards. Since SD cards can be purchased in various sizes, and 
since even cards of the same nominal size may have slightly 
different capacities, it is impossible to create an SD card 
image guaranteed to exactly fit a card. It is also inefficient 
to have an image that is larger than the files contained within 
that image, because the "extra space" will slow compression, 
download, and SD-card burn activities.

As a result, the Fedora ARM SD card images typically contain
a boot filesystem (usually FAT/VFAT) followed by a Linux extX
root filesystem. The second filesystem is sized so that it
is just big enough for the contained files.

The rootfs-resize script attempts to resize the root (typically
second) partition on the SD card to fill the device. It 
does this by adjusting the end of the root partition and then 
rebooting. On the next boot, a resize2fs is performed
to resize the filesystem.

The name of the block device containing the root filesystem must 
be /dev/sdX or /dev/mmcblk0pX, and it must contain an ext[234]
partition.

In order to resize the partition, the file /.rootfs-repartition
must be present. After the partition resize operation, this file
is removed and the file /.rootfs-resize is created (Phase 1); on 
next boot, when the resize2fs is started, the /.rootfs-resize file is
removed (Phase 2).

If the file /.swapsize exists and contains an integer number
in text format during Phase 2, a swap file (/swap0) of that size
in megabytes is created after the filesystem is resized. This swap
file is added to /etc/fstab and activated.

The operation of this service may be disabled in either of these 
two ways (in addition to disabling the service unit file):

1. By passing a kernel command line argument of "nofsresize"
2. By creating the file /.nofsresize

If either of these suppressions flags are present, the script
will not run.

This script and the corresponding systemd unit file are licensed
under the GPL v2, or at the user's discretion, any later version.

The web page and git repository for this code is accessible at
[Github Repo](https://github.com/ctyler/rootfs-resize)

