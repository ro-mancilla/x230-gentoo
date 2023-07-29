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
Network controller [0280]: Intel Corporation Centrino Advanced-N 6205 [Taylor Peak] [8086:0085] (rev 34)
        Subsystem: Intel Corporation Centrino Advanced-N 6205 AGN [8086:1311]
        Kernel driver in use: iwlwifi
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
### dmidecode
```
# dmidecode 3.5
Getting SMBIOS data from sysfs.
SMBIOS 3.0.0 present.
Table at 0x7FE06000.

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

Handle 0x0001, DMI type 1, 27 bytes
System Information
        Manufacturer: LENOVO
        Product Name: 23255SS
        Version: ThinkPad X230
        Serial Number: R9WAB90
        UUID: c2a34901-51e6-11cb-97ef-a7a3d8d114dd
        Wake-up Type: Reserved
        SKU Number: Not Specified
        Family: ThinkPad X230

Handle 0x0002, DMI type 2, 14 bytes
Base Board Information
        Manufacturer: LENOVO
        Product Name: 23255SS
        Version: ThinkPad X230
        Serial Number: R9WAB90
        Asset Tag: Not Specified
        Features: None
        Location In Chassis: Not Specified
        Chassis Handle: 0x0003
        Type: Motherboard

Handle 0x0003, DMI type 3, 22 bytes
Chassis Information
        Manufacturer: LENOVO
        Type: Laptop
        Lock: Not Present
        Version: Not Specified
        Serial Number: Not Specified
        Asset Tag: Not Specified
        Boot-up State: Safe
        Power Supply State: Safe
        Thermal State: Safe
        Security Status: None
        OEM Information: 0x00000000
        Height: Unspecified
        Number Of Power Cords: 1
        Contained Elements: 0
        SKU Number: Not Specified

Handle 0x0004, DMI type 4, 48 bytes
Processor Information
        Socket Designation: CPU0
        Type: Central Processor
        Family: Pentium Pro
        Manufacturer: GenuineIntel
        ID: A9 06 03 00 FF FB EB BF
        Signature: Type 0, Family 6, Model 58, Stepping 9
        Flags:
                FPU (Floating-point unit on-chip)
                VME (Virtual mode extension)
                DE (Debugging extension)
                PSE (Page size extension)
                TSC (Time stamp counter)
                MSR (Model specific registers)
                PAE (Physical address extension)
                MCE (Machine check exception)
                CX8 (CMPXCHG8 instruction supported)
                APIC (On-chip APIC hardware supported)
                SEP (Fast system call)
                MTRR (Memory type range registers)
                PGE (Page global enable)
                MCA (Machine check architecture)
                CMOV (Conditional move instruction supported)
                PAT (Page attribute table)
                PSE-36 (36-bit page size extension)
                CLFSH (CLFLUSH instruction supported)
                DS (Debug store)
                ACPI (ACPI supported)
                MMX (MMX technology supported)
                FXSR (FXSAVE and FXSTOR instructions supported)
                SSE (Streaming SIMD extensions)
                SSE2 (Streaming SIMD extensions 2)
                SS (Self-snoop)
                HTT (Multi-threading)
                TM (Thermal monitor supported)
                PBE (Pending break enabled)
        Version:        Intel(R) Core(TM) i5-3320M CPU @ 2.60GHz
        Voltage: Unknown
        External Clock: 100 MHz
        Max Speed: 3300 MHz
        Current Speed: 2600 MHz
        Status: Populated, Enabled
        Upgrade: Unknown
        L1 Cache Handle: 0x0006
        L2 Cache Handle: 0x0007
        L3 Cache Handle: 0x0008
        Serial Number: Not Specified
        Asset Tag: Not Specified
        Part Number: Not Specified
        Core Count: 2
        Core Enabled: 2
        Thread Count: 4
        Characteristics:
                Hardware Thread
                Execute Protection
                Enhanced Virtualization

Handle 0x0005, DMI type 7, 27 bytes
Cache Information
        Socket Designation: CACHE1
        Configuration: Enabled, Not Socketed, Level 1
        Operational Mode: Unknown
        Location: Internal
        Installed Size: 64 kB
        Maximum Size: 64 kB
        Supported SRAM Types:
                Unknown
        Installed SRAM Type: Unknown
        Speed: Unknown
        Error Correction Type: Unknown
        System Type: Data
        Associativity: 8-way Set-associative

Handle 0x0006, DMI type 7, 27 bytes
Cache Information
        Socket Designation: CACHE1
        Configuration: Enabled, Not Socketed, Level 1
        Operational Mode: Unknown
        Location: Internal
        Installed Size: 64 kB
        Maximum Size: 64 kB
        Supported SRAM Types:
                Unknown
        Installed SRAM Type: Unknown
        Speed: Unknown
        Error Correction Type: Unknown
        System Type: Instruction
        Associativity: 8-way Set-associative

Handle 0x0007, DMI type 7, 27 bytes
Cache Information
        Socket Designation: CACHE2
        Configuration: Enabled, Not Socketed, Level 2
        Operational Mode: Unknown
        Location: Internal
        Installed Size: 512 kB
        Maximum Size: 512 kB
        Supported SRAM Types:
                Unknown
        Installed SRAM Type: Unknown
        Speed: Unknown
        Error Correction Type: Unknown
        System Type: Unified
        Associativity: 8-way Set-associative

Handle 0x0008, DMI type 7, 27 bytes
Cache Information
        Socket Designation: CACHE3
        Configuration: Enabled, Not Socketed, Level 3
        Operational Mode: Unknown
        Location: Internal
        Installed Size: 3 MB
        Maximum Size: 3 MB
        Supported SRAM Types:
                Unknown
        Installed SRAM Type: Unknown
        Speed: Unknown
        Error Correction Type: Unknown
        System Type: Unified
        Associativity: 12-way Set-associative

Handle 0x0009, DMI type 11, 5 bytes
OEM Strings
        String 1: IBM ThinkPad Embedded Controller -[G2HT35WW-3.22    ]-

Handle 0x000A, DMI type 16, 23 bytes
Physical Memory Array
        Location: System Board Or Motherboard
        Use: System Memory
        Error Correction Type: None
        Maximum Capacity: 16 GB
        Error Information Handle: Not Provided
        Number Of Devices: 2

Handle 0x000B, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000A
        Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 4 GB
        Form Factor: SODIMM
        Set: None
        Locator: Channel-0-DIMM-0
        Bank Locator: BANK 0
        Type: DDR3
        Type Detail: Unknown Synchronous
        Speed: 800 MT/s
        Manufacturer: Hynix/Hyundai
        Serial Number: 4fb18358
        Asset Tag: Channel-0-DIMM-0-AssetTag
        Part Number: HMT351S6EFR8C-PB
        Rank: 2
        Configured Memory Speed: 800 MT/s
        Minimum Voltage: Unknown
        Maximum Voltage: Unknown
        Configured Voltage: Unknown

Handle 0x000C, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000A
        Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 8 GB
        Form Factor: SODIMM
        Set: None
        Locator: Channel-1-DIMM-0
        Bank Locator: BANK 0
        Type: DDR3
        Type Detail: Unknown Synchronous
        Speed: 800 MT/s
        Manufacturer: Hynix/Hyundai
        Serial Number: 06443d77
        Asset Tag: Channel-1-DIMM-0-AssetTag
        Part Number: HMT41GS6BFR8A-PB
        Rank: 2
        Configured Memory Speed: 800 MT/s
        Minimum Voltage: Unknown
        Maximum Voltage: Unknown
        Configured Voltage: Unknown

Handle 0x000D, DMI type 19, 31 bytes
Memory Array Mapped Address
        Starting Address: 0x00000000000
        Ending Address: 0x002FFFFFFFF
        Range Size: 12 GB
        Physical Array Handle: 0x000A
        Partition Width: 2

Handle 0x000E, DMI type 20, 35 bytes
Memory Device Mapped Address
        Starting Address: 0x00000000000
        Ending Address: 0x000FFFFFFFF
        Range Size: 4 GB
        Physical Device Handle: 0x000B
        Memory Array Mapped Address Handle: 0x000D
        Partition Row Position: Unknown
        Interleave Position: Unknown
        Interleaved Data Depth: Unknown

Handle 0x000F, DMI type 20, 35 bytes
Memory Device Mapped Address
        Starting Address: 0x00100000000
        Ending Address: 0x002FFFFFFFF
        Range Size: 8 GB
        Physical Device Handle: 0x000B
        Memory Array Mapped Address Handle: 0x000D
        Partition Row Position: Unknown
        Interleave Position: Unknown
        Interleaved Data Depth: Unknown

Handle 0x0010, DMI type 32, 11 bytes
System Boot Information
        Status: No errors detected

Handle 0x0011, DMI type 41, 11 bytes
Onboard Device
        Reference Designation: VGA compatible controller
        Type: Video
        Status: Enabled
        Type Instance: 0
        Bus Address: 0000:00:02.0

Handle 0x0012, DMI type 41, 11 bytes
Onboard Device
        Reference Designation: Ethernet controller
        Type: Ethernet
        Status: Enabled
        Type Instance: 0
        Bus Address: 0000:00:19.0

Handle 0x0013, DMI type 9, 19 bytes
System Slot Information
        Designation: ExpressCard Slot
        Type: x1 PC Card (PCMCIA)
        Current Usage: Available
        Length: Short
        ID: Adapter 0, Socket 0
        Characteristics: Unknown
        Bus Address: 0000:00:1c.2
        Data Bus Width: 1
        Peer Devices: 0

Handle 0x0014, DMI type 133, 5 bytes
OEM-specific Type
        Header and Data:
                85 05 14 00 01
        Strings:
                KHOIHGIUCCHHII

Handle 0xFEFF, DMI type 127, 4 bytes
End Of Table
```
### blkid
```
/dev/sda3: UUID="56c7173e-3f77-4dc9-9e41-87f3ace20f61" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="48cc165a-18f3-d645-9491-ba34cacf42ff"
/dev/sda1: UUID="FB0E-5241" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="33fd93bf-71d1-374d-8cde-764582429b55"
/dev/sdb4: PARTLABEL="Gap1" PARTUUID="b40fca90-e27e-415e-9ab4-66b6ecf09183"
/dev/sdb2: SEC_TYPE="msdos" UUID="2B1F-AA60" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI boot partition" PARTUUID="b40fca90-e27e-415e-9ab2-66b6ecf09183"
/dev/sdb3: BLOCK_SIZE="2048" LABEL="ISOIMAGE" TYPE="hfsplus" PARTLABEL="HFSPLUS" PARTUUID="b40fca90-e27e-415e-9ab3-66b6ecf09183"
/dev/sdb1: PARTLABEL="Gap0" PARTUUID="b40fca90-e27e-415e-9ab1-66b6ecf09183"
/dev/loop0: TYPE="squashfs"
/dev/sda2: UUID="629d8bec-b4fd-4664-8bff-f227e2443c48" TYPE="swap" PARTUUID="084f1fb1-51df-2446-894c-0fff611d693b"
```
