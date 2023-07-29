# x230-gentoo
![](https://github.com/ro-mancilla/x230-gentoo/assets/134629433/0e128e0a-c6d7-418f-9331-4748cb06f4c6)

## Configuration
### Portage
[make.conf](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/make.conf)

[package.use](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.use)

[package.accept_keywords](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.accept_keywords)

[package.mask](https://github.com/ro-mancilla/x230-gentoo/blob/main/portage/package.mask)

## Boot

### Smooth brightness control
Add `acpi_backlight=native` to your kernel parameters.

### EFI Stub entry
```
efibootmgr -c -d /dev/sda -p 1 -L "Gentoo Linux" -l '\efi\Gentoo\gentoo.efi' -u 'root=/dev/sda3 initrd=\initramfs.img quiet splash acpi_osi=Linux acpi_backlight=native i915.fastboot=1 iomem=relaxed net.ifnames=0
```

##  Firmware
### Wi-Fi (TODO: Swap card to an AX200)
```
$ lspci | grep "Network"
...
02:00.0 Network controller: Intel Corporation Centrino Advanced-N 6205 [Taylor Peak] (rev 34)
```

- `iwlwifi-6000g2a-5.ucode` and `iwlwifi-6000g2a-6.ucode` required from `sys-kernel/linux-firmware`.

- `regulatory.db` and `regulatory.db.p7s` required from `net-wireless/wireless-regdb`.
### Bluetooth
```
$ lsusb | grep "Bluetooth"
Bus 001 Device 004: ID 0a5c:21e6 Broadcom Corp. BCM20702 Bluetooth 4.0 [ThinkPad]
```

- `BCM20702A1-0a5c-21e6.hcd` required from `sys-firmware/broadcom-bt-firmware`.

## Notes
- Stock Lenovo BIOS includes a whitelist for WLAN and WWAN cards. Consider flashing [1vyrain](https://github.com/n4ru/1vyrain), [Skulls](https://github.com/merge/skulls/tree/master) or [Coreboot](https://www.coreboot.org/Board:lenovo/x230) if installing a new wireless card.
- If considering flashing Coreboot with a CH341A programmer, check with a multimeter its voltage outputs. If your CH341A rails reports 5V, it might fry the chip as 3.3V is the intended voltage. Fortunately, [it can be fixed with a simple soldering.](https://www.youtube.com/watch?v=-ln3VIZKKaE)
- The keyboard [can be swapped to an X220 or T420 one](https://www.thinkwiki.org/wiki/Install_Classic_Keyboard_on_xx30_Series_ThinkPads), however their pinouts are different. You must isolate three pins of the keyboard to avoid short circuiting.
  + If you swap the keyboard, most of the keys will not work due to the EC (Embedded Controller) is programmed with the previous keyboard structure in mind. You have two options; [reflash the EC internally with almost of the keys restored](https://github.com/hamishcoleman/thinkpad-ec) or rebind the keys via software.
- You cannot charge any third-party battery on the X230. With the EC patch you can remove this battery whitelist.
- To get a better wireless coverage, I sacrificied the Keningston lock by drilling a hole on it and installed an external antenna. The X230 utilizes UFL connectors for the antennas so you will need a UFL to RP-SMA pigtail.
- The WWAN slot can be used to install an mSATA drive in any BIOS.
- If you have a beefy computer in your network, try distributing compile tasks with [Distcc](https://wiki.gentoo.org/wiki/Distcc) for faster compiling times.

## Machine info
### CPU
```
$ dmidecode -t processor | grep "Version"
        Version:        Intel(R) Core(TM) i5-3320M CPU @ 2.60GHz
```
```
$ dmidecode -t processor | grep "Count"
        Core Count: 2
        Thread Count: 4
```
### RAM
```
$ dmidecode -t memory | grep "Size"
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
$ dmidecode -t bios
# dmidecode 3.5
Getting SMBIOS data from sysfs.
SMBIOS 3.0.0 present.

Handle 0x0000, DMI type 0, 26 bytes
BIOS Information
        Vendor: coreboot
        Version: CBET4000 4.20-838-gcb40888c8dc0-dirty
        Release Date: 07/28/2023
        ROM Size: 12 MB
        Characteristics:
                PCI is supported
                PC Card (PCMCIA) is supported
                BIOS is upgradeable
                Selectable boot is supported
                ACPI is supported
                Targeted content distribution is supported
        BIOS Revision: 4.20
        Firmware Revision: 0.0
```
### Drives
```
$ blkid
/dev/sda3: UUID="56c7173e-3f77-4dc9-9e41-87f3ace20f61" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="48cc165a-18f3-d645-9491-ba34cacf42ff"
/dev/sda1: UUID="FB0E-5241" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="33fd93bf-71d1-374d-8cde-764582429b55"
/dev/sdb4: PARTLABEL="Gap1" PARTUUID="b40fca90-e27e-415e-9ab4-66b6ecf09183"
/dev/sdb2: SEC_TYPE="msdos" UUID="2B1F-AA60" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI boot partition" PARTUUID="b40fca90-e27e-415e-9ab2-66b6ecf09183"
/dev/sdb3: BLOCK_SIZE="2048" LABEL="ISOIMAGE" TYPE="hfsplus" PARTLABEL="HFSPLUS" PARTUUID="b40fca90-e27e-415e-9ab3-66b6ecf09183"
/dev/sdb1: PARTLABEL="Gap0" PARTUUID="b40fca90-e27e-415e-9ab1-66b6ecf09183"
/dev/loop0: TYPE="squashfs"
/dev/sda2: UUID="629d8bec-b4fd-4664-8bff-f227e2443c48" TYPE="swap" PARTUUID="084f1fb1-51df-2446-894c-0fff611d693b"
```
