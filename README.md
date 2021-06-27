# b250m-opencore-config
OpenCore configuration for my personal desktop.

# OpenCore version tested with: 0.6.8
## macOS version tested with: 11.2
Please do not use this with any newer or older version as some options may no longer exist or be interpreted differently, or new options may be needed in order to successfully boot.

# Specs
- CPU: i7 6700
- Board: Gigabyte B250M-D3H (BIOS F10)
- GPU: Sapphire Radeon RX 580 4GB
- RAM: 16 GB Crucial Ballistix 2400 MHz DDR4 @ 2133MHz
- SSD: 512 GB Samsung 970 EVO
- SSD: 256 GB Samsung 850 EVO
- Wifi: Fenvi T919
- Monitors: 2x Philips 245E
- Audio: HDMI audio via Philips 245E

# BIOS Settings
See the screenshots under the `BIOS` directory.

# OpenCore config noteworthy things

- SIP is completely enabled
- `ig-platform-id` is set to `0x19120001` for connectorless mode. This fixes JPEGs not opening properly among other things. Netflix is still broken.
- `vault.plist` is enabled **and signed**, be sure to either disable it or generate a vault.plist
- Apple Secure Boot is set to level Basic
- `ScanPolicy` is set to a value that only scans NVMe drives and specific partition types. If you do not want to use an NVMe drive, you will need to change this.
- The firmware has no MAT support, so `EnableWriteUnprotector` needs to be used for a succesful boot instead of the recommended `RebuildAppleMemoryMap`.
- `shikigva=80` is set as boot argument to fix DRM and enable Netflix in Safari according to [the WhateverGreen manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md)

## ScanPolicy enabled flags
ScanPolicy is set to a value of `525571` or binary `000010000000010100000011`. This means the following flags are set:

- `OC_SCAN_FILE_SYSTEM_LOCK` (bit 0)
- `OC_SCAN_DEVICE_LOCK` (bit 1)
- `OC_SCAN_ALLOW_FS_APFS` (bit 8)
- `OC_SCAN_ALLOW_FS_ESP` (bit 10)
- `OC_SCAN_ALLOW_DEVICE_NVME` (bit 19)

Please refer to the OpenCore manual for the meaning of these flags. In short, these flags allow scanning of APFS partitions (for macOS) and the ESP partition (for Boot Camp) on NVMe drives only.

# ACPI edits
See the ACPI subfolder.

- `EC`: EC patch
- `PLUG`: Sets `plugin-type` to 1, needed for AGPM and proper CPU power management.
- `SBUS`: System bus patches used to enable the AppleSMBusPCI kext
- `USBX`: Sets various USB power management properties

# Device Properties
- `PciRoot(0x0)/Pci(0x1f,0x3)`: Realtek ALC892
    - `layout-id`: `1`, used to tell AppleALC which layout ID to pick to get the onboard sound working.
- `PciRoot(0x0)/Pci(0x2,0x0)`: Intel HD Graphics 530
    - `ig-platform-id`: `0x19120001` (reversed) to enable a connectorless mode for the onboard graphics. Used to get acceleration working in various areas.
- `PciRoot(0x0)/Pci(0x1c,0x0)/Pci(0x0,0x0)`: Fenvi T-919
    - `brcmfx-country`: `NL` to make optimal use out of the Fenvi card and to reduce possible interference with surrounding devices.
- `PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)`: Radeon RX 580
    - `shikigva`: `80` to allow DRM playback patches

# Drivers
Not included in this repo, I use the following drivers:

- OpenCanopy (in the OpenCore package)
- OpenRuntime (in the OpenCore package)
- [HFSPlus](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)

# Kexts
Not included in this repo, I use the following kexts:

- [AppleALC](https://github.com/acidanthera/AppleALC): Fixes for HDMI and onboard audio
- [CPUFriend](https://github.com/acidanthera/CPUFriend): Injects proper CPU power management properties generated with [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) with a LFM of 800 MHz
- [IntelMausi](https://github.com/acidanthera/IntelMausi): Driver for the built-in Ethernet controller
- [Lilu](https://github.com/acidanthera/Lilu): Requirement for most other kexts in this list
- [NVMeFix](https://github.com/acidanthera/NVMeFix): Fixes for NVMe drives
- USBPorts: Generated with Hackintool, used for injecting only USB ports which are in use, to keep the system inside the 15 port limit
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC): SMC emulator
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen): Required for proper functioning of both the integrated graphics card in headless/connectorless mode, and the RX580

# Current issues
N/A