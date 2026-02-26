Radxa Zero 3E
=============
https://radxa.com/products/zeros/zero3e/

Build:
======
  $ make radxa_zero3e_defconfig
  $ make

Files created in output directory
=================================

output/images
├── bl31.elf
├── genimage.cfg
├── Image
├── rk3566_ddr_1056MHz_v1.23.bin
├── rockchip
│   └── rk3566-radxa-zero-3e.dtb
├── rootfs.ext2
├── rootfs.ext4 -> rootfs.ext2
├── sdcard.img
├── u-boot.bin
└── u-boot-rockchip.bin

How to write the SD card
========================

Once the build process is finished you will have an image called "sdcard.img"
in the output/images/ directory.

Copy the bootable "sdcard.img" onto an SD card with "dd":

  $ sudo dd if=output/images/sdcard.img of=/dev/sdX bs=1M conv=fsync

Insert the micro SDcard in your Radxa Zero 3E and power it up.
The console is on the serial line, 1500000 8N1.
