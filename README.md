This is Flippy's Openwrt packaged source code, which is mainly used to make openwrt firmware for a series of boxes such as Phicomm N1, Shell Cloud, My Home Cloud, Weijia Cloud, Amlogic S905x3, and Amlogic S922x.

1. Material:
1. Flippy precompiled Arm64 kernel (extract code at https://t.me/openwrt_flippy and https://pan.baidu.com/s/1tY_-l-Se2qGJ0eKl7FZBuQ: 846l)
2. The openwrt rootfs tar.gz package compiled by yourself: openwrt-armvirt-64-default-rootfs.tar.gz , the source code repository of openwrt is the first choice (https://github.com/coolsnowwolf/lede), of course, you can also use other Third-party sources, such as (https://github.com/Lienol/openwrt), can also use the official openwrt source: (https://github.com/openwrt/openwrt).

2. Environmental preparation
1. You need a linux host, which can be x86 or arm64 architecture, can be a physical machine or a virtual machine (but does not support the linux environment that comes with win10), requires root privileges, and has the following basic commands (only the command name is listed) , the package name where the command is located is not listed, because the package names and package installation commands of different linux distributions are different, please check it yourself):
    losetup, lsblk (version>=2.33), blkid, uuidgen, fdisk, parted, mkfs.vfat, mkfs.ext4, mkfs.btrfs (the list may not be complete, if an error occurs during the packaging process, please check the output by yourself and add the missing The command)
    
2. You need to upload the Flippy precompiled Arm64 kernel to the /opt/kernel directory (the directory needs to be created by yourself)
3. cd /opt   
   git clone https://github.com/unifreq/openwrt_packit     
4. Upload the compiled openwrt-armvirt-64-default-rootfs.tar.gz to the /opt/openwrt_packit directory
5. cd /opt/openwrt_packit

   ./mk_xxx.sh # xxx refers to the type of firmware you want to generate, for example: ./mk_s905d_n1.sh represents the firmware used to generate Phicomm N1

   The generated firmware is in .img format, stored in the /opt/openwrt_packit/output directory, and can be downloaded and flashed
   
   Tip: The working temporary directory is /opt/openwrt_packit/tmp. In order to improve IO performance and reduce hard disk loss, the tmpfs file system can be used to mount to this directory, which will occupy up to 1GB of memory. The mounting method is as follows:
   ````
   # Automatically mount at boot
   echo "none /opt/openwrt_packit/tmp tmpfs defaults 0 0" >> /etc/fstab
   mount /opt/openwrt_packit/tmp
   ````
    or
    ````
    # Manually mount
    mount -t tmpfs none /opt/openwrt_packit/tmp
    ````
   
   The relevant online upgrade scripts are in the files/ directory

   The relevant openwrt example configuration files are in the files/openwrt_config_demo/ directory
6. Notes on compiling openwrt rootfs:

       Target System -> QEMU ARM Virtual Machine
       Subtarget -> QEMU ARMv8 Virtual Machine (cortex-a53)
       Target Profile -> Default
       Target Images -> tar.gz
       *** Required software package (basic dependency package, only guarantees that the typed package can be written to EMMC, can be upgraded online on EMMC, does not include specific applications):
       Languages ​​-> Perl               
                    -> perl-http-date
                    -> perlbase-getopt
                    -> perlbase-time
                    -> perlbase-unicode                              
                    -> perlbase-utf8        
       Utilities -> Disc -> blkid, fdisk, lsblk, parted            
                 -> Filesystem -> attr, btrfs-progs (Build with zstd support), chattr, dosfstools,
                                  e2fsprogs, f2fs-tools, f2fsck, lsattr, mkf2fs, xfs-fsck, xfs-mkfs
                 -> Compression -> bsdtar or p7zip (unofficial source), pigz
                 -> Shells -> bash         
                 -> gawk, getopt, losetup, tar, uuidgen

        * (Optional) Wifi Basic Package:
        * The printed package can support Broadcom SDIO wireless module, Firmware does not need to be selected,
        * Because the firmware from Armbian is already included in the package source code,
        * Will automatically overwrite the existing firmware in openwrt rootfs
        Kernel modules -> Wireless Drivers -> kmod-brcmfmac(SDIO)
                                              -> kmod-brcmutil
                                              -> kmod-cfg80211
                                              -> kmod-mac80211
        Network -> WirelessAPD -> hostapd-common
                                 -> wpa-cli
                                 -> wpad-basic
                 -> iw
       
    
    Software packages other than the above-mentioned mandatory options can be freely selected as needed.
                 
3. For other relevant information, please refer to my post on the Enshan Forum:

https://www.right.com.cn/forum/thread-981406-1-1.html

https://www.right.com.cn/forum/thread-4055451-1-1.html

https://www.right.com.cn/forum/thread-4076037-1-1.html

4. The firmware address packaged by TG group friends

Grumpy Brother:
https://github.com/breakings/OpenWrt

There is a neat trick:
https://github.com/HoldOnBro/Actions-OpenWrt/

Mr. Zhuge:
https://github.com/hibuddies/openwrt/

That lump:
https://github.com/Netflixxp/N1HK1dabao

Five, Github Actions packaging method
    The source code of actions is from https://github.com/ophub (smith1998), and the main script is openwrt_flippy.sh  

Introduce this Actions in the `.github/workflows/*.yml` cloud compilation script to use. Detailed instructions for use: [README.ACTION.md](README.ACTION.md)

````yaml

- name: Package Armvirt as OpenWrt
  uses: unifreq/openwrt_packit@master
  env:
    OPENWRT_ARMVIRT: openwrt/bin/targets/*/*/*.tar.gz
    PACKAGE_SOC: s905d_s905x3_beikeyun
    KERNEL_VERSION_NAME: 5.13.2_5.4.132

````
6. Use luci-app-amlogic to upgrade online
   
   The source code of luci-app-amlogic comes from https://github.com/ophub/luci-app-amlogic (smith1998), which can be integrated into openwrt firmware and closely combined with this packaged source code, enabling online kernel upgrade and complete online upgrade firmware.
