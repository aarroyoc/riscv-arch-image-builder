# Image Builder for Archlinux on an Allwinner D1 / Sipeed Lichee RV
These scripts compile, copy, bake, unpack and flash a [ready](https://wiki.archlinux.org/title/installation_guide#Configure_the_system) to use RISC-V Archlinux.  
*With "ready to use" i mean that it boots, you still need to configure everything!*

Special thanks to **smaeul** for all their work!

## Components
- Boot0 based on https://github.com/smaeul/sun20i_d1_spl
- OpenSBI based on https://github.com/smaeul/opensbi
- U-Boot based on https://github.com/smaeul/u-boot.git
- Kernel based on https://github.com/smaeul/linux
- WiFi driver (rtl8723ds) based on https://github.com/lwfinger/rtl8723ds
- RootFS based on https://archriscv.felixc.at (root password is `sifive`)

## How to build
1. Install requirements: `pacman -Sy riscv64-linux-gnu-gcc swig cpio python3 python-setuptools base-devel bc`
1. Run `1_compile.sh` which compiles everything into the `output` folder.
1. Run `2_create_sd.sh /dev/<device>` to flash everything on the SD card.
1. Configure your Archlinux :rocket:

Steps to configure on Mango Pi:

```
pacman -Syu
pacman -S dhclient dhcpcd dialog ell glibc ifplugd networkmanager libdaemon vim netctl systemd-resolvconf wireless_tools wpa_supplicant openssh
systemctl enable NetworkManager
systemctl enable sshd
ln -s /lib/modules/5.19.0-AllWinnerD1-Smaeul /lib/modules/5.19.0-rc1-gfe178cf0153d-dirty
passwd
(reboot)
```

## Using loop image file instead of a SD card
Simply loop it using `sudo losetup -f -P <file>` and then use `/dev/loopX` as the target device.

## Notes
The second script requires `arch-install-scripts`, `qemu-user-static-bin` (AUR) and `binfmt-qemu-static` (AUR) for an architectural chroot.
If you don't want to use/do this, change `USE_CHROOT` to `0` in `consts.sh`.  
*Keep in mind, that this is just a extracted rootfs with **no** configuration. You probably want to update the system, install an editor and take care of network access/ssh*

Some commits are pinned, this means that in the future this script might stop working since often a git HEAD is checked out. This is intentional.

The second script uses `sudo` for root access. Like any random script from a random stranger from the internet, have a look at the code first and use at own risk!

Things are rebuild whenever the corresponding `output/<file>` is missing. For example, the kernel is rebuilt when there is no `Image` file.

# Status
## 14.06.2022
- kernel updated to 5.19-rc1
- added initramfs support (untested)
- added extlinux support

## 22.04.2022
- kernel includes modules for USB LAN adapter
- swap is enabled
## 09.04.2022
- HDMI, tested with LXDE/LXDM
## 07.04.2022
- WiFi \o/
## 06.04.2022
- Kernel is based on 5.18-rc1
- WiFi driver fails to build (linking error :feelsbadman:)
- Both kernel and U-Boot are using `nezha_defconfig` since i found them more reliable
    - With the [licheerv_linux_defconfig](https://andreas.welcomes-you.com/media/files/licheerv_linux_defconfig) from [here](https://andreas.welcomes-you.com/boot-sw-debian-risc-v-lichee-rv/) the kernel fails to find the sd card (or its partitions? not sure what exactly goes wrong)
- HDMI is not working (at least on the one screen i've tested it)


# Problems
## 06.04.2022
- ~no WiFi!~
- ~No HDMI (though, i'm unsure about the state of HDMI support in general)~
