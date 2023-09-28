# x230-gentoo
![](https://github.com/ro-mancilla/x230-gentoo/assets/134629433/0e128e0a-c6d7-418f-9331-4748cb06f4c6)

## Configuration
### Portage
[make.conf](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/make.conf)

[package.use](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.use)

[package.accept_keywords](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.accept_keywords)

[package.mask](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.mask)

## Boot

### Kernel parameters
```
root=UUID=89dddbea-364c-4b56-91a6-f8b32a6c442e quiet splash acpi_osi="!Windows 2012" acpi_backlight=native mitigations=off i915.mitigations=off iomem=relaxed net.ifnames=0 ahci.mobile_lpm_policy=1 pcie_aspm=force i915.fastboot=1 i915.i915_enable_rc6=1 i915.i915_enable_fbc=1 i915.lvds_downclock=1
```
### Smooth brightness control
Add `acpi_backlight=native` to your kernel parameters.

### Battery saving features
Add ```ahci.mobile_lpm_policy=1 pcie_aspm=force i915.i915_enable_rc6=1 i915.i915_enable_fbc=1 i915.lvds_downclock=1``` to your kernel parameters.

##  Firmware
### Wi-Fi
### [Intel Centrino Advanced-N 6205](https://upload.wikimedia.org/wikipedia/commons/8/81/Intel_Centrino_Advanced-N_6205-0997.jpg)
```
$ lspci | grep "Network"
...
02:00.0 Network controller: Intel Corporation Centrino Advanced-N 6205 [Taylor Peak] (rev 34)
```
- `iwlwifi-6000g2a-5.ucode` and `iwlwifi-6000g2a-6.ucode` required from `sys-kernel/linux-firmware`.

### [Intel AX200](https://m.media-amazon.com/images/I/51slmAG6FeL._AC_SL1000_.jpg)
```
$ lspci | grep "Network"
...
02:00.0 Network controller: Intel Corporation Wi-Fi 6 AX200 (rev 1a)
```
- `iwlwifi-cc-a0-77.ucode` and, if desired, `iwl-debug-yoyo.bin` required from `sys-kernel/linux-firmware`.

Both cards requires `regulatory.db` and `regulatory.db.p7s` from `net-wireless/wireless-regdb`.

### Bluetooth
### [Broadcom BCM20702](https://2jb.de/cdn/shop/products/60Y3305_02_22e97e78-1855-4b31-ae78-f895307273c3.jpg?v=1632823666)
```
$ lsusb | grep "Bluetooth"
Bus 001 Device 004: ID 0a5c:21e6 Broadcom Corp. BCM20702 Bluetooth 4.0 [ThinkPad]
```

- `BCM20702A1-0a5c-21e6.hcd` required from `sys-firmware/broadcom-bt-firmware`.

### [Intel AX200's Bluetooth](https://m.media-amazon.com/images/I/51slmAG6FeL._AC_SL1000_.jpg)
```
$ lsusb | grep "Bluetooth"
Bus 004 Device 006: ID 8087:0029 Intel Corp. AX200 Bluetooth
```

- `intel/ibt-20-1-3.sfi` and `intel/ibt-20-1-3.ddc` required from `sys-kernel/linux-firmware`.

## Notes
- Stock Lenovo BIOS includes a whitelist for WLAN and WWAN cards. Consider flashing [1vyrain](https://github.com/n4ru/1vyrain), [Skulls](https://github.com/merge/skulls/tree/master) or [Coreboot](https://www.coreboot.org/Board:lenovo/x230) if you're considering installing a new wireless card.
- Before flashing Coreboot with a CH341A programmer, check with a multimeter its voltage outputs. If your CH341A rails reports 5V, it might fry the chip as 3.3V is the intended voltage. Fortunately, [it can be fixed with simple soldering.](https://www.youtube.com/watch?v=-ln3VIZKKaE)
- The keyboard [can be swapped to an X220 or T420 one](https://www.thinkwiki.org/wiki/Install_Classic_Keyboard_on_xx30_Series_ThinkPads) as their connector is the same, however the keyboard's internal pinouts are different. You must isolate three pins to avoid short circuiting.
  + If you swap the keyboard, most of the keys will not work due to the EC (Embedded Controller) having the previous keyboard structure in mind. You can fix this [reflashing the EC internally with almost of its keys restored](https://github.com/hamishcoleman/thinkpad-ec) or rebind the keys via software.
- You cannot charge any third-party battery on the X230. With the EC patch you can remove this block.
- To get a better wireless coverage, I sacrificied the Keningston lock by drilling a hole on it and installed an external antenna. The X230 utilizes UFL connectors for the antennas so you will need a UFL to RP-SMA pigtail.
- The WWAN slot can be used to install an mSATA drive in any BIOS.
- If you're using Gentoo and you have a beefy computer in your network, try distributing compile tasks with [Distcc](https://wiki.gentoo.org/wiki/Distcc) for faster compiling times.

## Machine info
### CPU
```
# dmidecode -t processor | grep "Version"
        Version:        Intel(R) Core(TM) i5-3320M CPU @ 2.60GHz
```
```
# dmidecode -t processor | grep "Count"
        Core Count: 2
        Thread Count: 4
```
### RAM
```
# dmidecode -t memory | grep "Size"
        Size: 4 GB
        Size: 8 GB
```
```
$ dmidecode -t memory | grep "Speed"
        Speed: 800 MT/s
        Configured Memory Speed: 800 MT/s
        Speed: 800 MT/s
        Configured Memory Speed: 800 MT/s
```
### BIOS
```
# dmidecode -t bios
# dmidecode 3.5
Getting SMBIOS data from sysfs.
SMBIOS 3.0.0 present.

Handle 0x0000, DMI type 0, 26 bytes
BIOS Information
        Vendor: coreboot
        Version: CBET4000 4.21-237-ga4c74578ecd4-dirty
        Release Date: 09/11/2023
        ROM Size: 12 MB
        Characteristics:
                PCI is supported
                PC Card (PCMCIA) is supported
                BIOS is upgradeable
                Selectable boot is supported
                ACPI is supported
                Targeted content distribution is supported
        BIOS Revision: 4.21
        Firmware Revision: 0.0
```
### Disk
```
# blkid
/dev/sda1: UUID="DF27-6C6B" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="4d0c740c-d3d8-b849-9e38-b8a25d1eba8f"
/dev/sda2: UUID="4148441f-088b-4d4d-a66c-69787e8f086f" TYPE="swap" PARTUUID="3f1bd237-350e-c642-bee2-703d0463004a"
/dev/sda3: LABEL="/dev/sda3" UUID="89dddbea-364c-4b56-91a6-f8b32a6c442e" UUID_SUB="703edb68-53f5-4b5a-aae2-b7106704d8d3" BLOCK_SIZE="4096" TYPE="btrfs" PARTUUID="8826602a-b39e-cb4f-b478-ec4dde7b54d5"
```
