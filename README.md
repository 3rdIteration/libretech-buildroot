# libretech-buildroot

buildroot optimized for Libre Computer boards with composible components and design practices.

This branch should always be rebase-able on buildroot master. It is not designed as a fork.

All components are organized into composible profiles by arch, board, image layout, and overlay.

Hosted standardized bootloader binaries are produced by [libretech-builder-simple](https://github.com/libre-computer-project/libretech-builder-simple).

defconfigs can be considered the functional end of a composition of profiles.

Also see the [original buildroot README](README).

## configs

defconfigs are organized into a tree structure based on board under configs.

* configs
  * librecomputer
    * aml-s905x-cc
      * efi-btrfs_defconfig

```
make librecomputer/aml-s905x-cc/efi-btrfs_defconfig
make
```

## board

board files are organized into a tree structure based on architecture, board, image generation, composible overlays

* board
  * librecomputer
    * aml
      * gxl
        * overlay
        * s905x
          * overlay
    * aml-s905x-cc
      * overlay
      * genimage
    * genimage
      * initramfs
        * genimage.cfg
        * genimage.sh
      * efi-btrfs
        * boot.cmd
        * boot.ini
        * genimage.cfg
        * genimage.sh
    * overlay
      * debugfs
      * stress-ng-cpu
    * project

### genimage

the genimage directories under board/librecomputer holds image layout profiles to support functions

* initramfs - basic initramfs system in efi-vfat booted via efi
* efi-btrfs - basic system with kernel in efi-vfat and rootfs in btrfs booted via efi

### overlay

the overlay directories under board/librecomputer holds overlays profiles to support functions

* debugfs - adds debugfs automount to the image via init script
* stress-ng-cpu - adds stress-ng cpu stressor to the image via init script

### project

the project directory is recommended for storing external submodules or repos for referencing

## output

After a successful build (locally or via GitHub Actions), the image files are placed in `output/images/`.

### Output files

| File | Description |
|------|-------------|
| `sdcard.img` | **The only file you need.** Complete, ready-to-flash disk image. Flash this to a USB stick to run the system. |
| `boot.vfat` | FAT32 boot partition image. Intermediate artifact embedded inside `sdcard.img`; no need to use it directly. |
| `rootfs.btrfs` | Btrfs root filesystem image. Intermediate artifact embedded inside `sdcard.img`; no need to use it directly. |
| `Image` | Compiled Linux kernel (AArch64). Stored inside `boot.vfat` → `sdcard.img` as `EFI/boot/BOOTAA64.EFI`. |
| `boot.scr` | Compiled U-Boot boot script (binary form of `board/librecomputer/genimage/efi-btrfs/boot.cmd`). Stored inside `boot.vfat` → `sdcard.img`. |
| `boot.ini` | U-Boot environment file that configures the boot method. Stored inside `boot.vfat` → `sdcard.img`. |
| `<board-name>` | Board-specific bootloader binary downloaded from `boot.libre.computer`. Written directly into `sdcard.img` before the partition table at the correct sector offset for the board. |

The primary output is **`sdcard.img`** — a ready-to-flash disk image that contains:

* a bootloader partition (written before the partition table)
* a FAT32 boot partition with the EFI stub, U-Boot script, and Linux kernel (`Image`)
* a Btrfs root filesystem partition

### Download from GitHub Actions

1. Open the [Actions tab](../../actions/workflows/build.yml) and select the completed workflow run.
2. Scroll to the **Artifacts** section at the bottom of the run summary.
3. Download the **images** artifact and unzip it — you will find `sdcard.img` inside.

### Flash to a USB stick

Replace `/dev/sdX` with the actual device node of your USB stick (check with `lsblk`).

**Linux / macOS:**

```bash
sudo dd if=sdcard.img of=/dev/sdX bs=4M conv=fsync status=progress
```

> **Warning:** double-check the target device before running `dd` — writing to the wrong device will destroy its data.

**Windows:** use [balenaEtcher](https://etcher.balena.io/) or [Raspberry Pi Imager](https://www.raspberrypi.com/software/) and select `sdcard.img` as the source image.

### Boot the board

1. Insert the flashed USB stick into your Libre Computer board.
   * On the **aml-s805x-ac (La Frite)**: use the USB port **furthest from the IR receiver**.
2. Power on the board.
3. The system will boot automatically via U-Boot → EFI → Linux.
