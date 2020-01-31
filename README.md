# b250m-opencore-config
OpenCore configuration for my personal desktop.

# OpenCore version tested with: 0.5.4
## macOS version tested with: 10.15.2
Please do not use this with any newer or older version as some options may no longer exist or be interpreted differently, or new options may be needed in order to successfully boot.

# Specs
- CPU: i5 6400
- Board: Gigabyte B250M-D3H (BIOS F10)
- GPU: Sapphire Radeon RX 580 4GB
- RAM: 16 GB Crucial Ballistix 2400 MHz DDR4 @ 2133MHz
- SSD: 512 GB Samsung 970 EVO
- Wifi: Fenvi T919
- Monitors: 1x BenQ XL2420T + 1x LG Flatron W2261VP
- Audio: HDMI audio via an Onkyo TX-NR609 + Realtek ALC892

# BIOS Settings
See the screenshots under the `BIOS` directory.

# OpenCore config noteworthy things

- SIP is completely enabled
- `ig-platform-id` is set to `0x19120001` for connectorless mode. This fixes JPEGs not opening properly among other things. Netflix is still broken.
- `vault.plist` is *en*abled, be sure to either disable it or generate a vault.plist
- `ScanPolicy` is set to a value that only scans NVMe drives and specific partition types. If you do not want to use an NVMe drive, you will need to change this.

## ScanPolicy enabled flags
ScanPolicy is set to a value of `525571` or binary `000010000000010100000011`. This means the following flags are set:

- `OC_SCAN_FILE_SYSTEM_LOCK` (bit 0)
- `OC_SCAN_DEVICE_LOCK` (bit 1)
- `OC_SCAN_ALLOW_FS_APFS` (bit 8)
- `OC_SCAN_ALLOW_FS_ESP` (bit 10)
- `OC_SCAN_ALLOW_DEVICE_NVME` (bit 19)

Please refer to the OpenCore manual for the meaning of these flags. In short, these flags allow scanning of APFS partitions (for macOS) and the ESP partition (for Boot Camp) on NVMe drives only.

# ACPI edits
See the ACPI subfolder. Most of these edits consist of setting device properties to make the devices show up nicely in macOS and have them at the native PCI paths.

- `ANS1`: Moves the NVMe drive from PXSX to ANS1 and sets device properties in its DSM method.
- `ARPT`: Moves the Fenvi card from PXSX to ARPT and sets device properties. Also sets `brcmfx-country` to set the country code in conjunction with `AirportBrcmFixup`
- `DTPG`: Adds the DTPG method used by various `_DSM` methods in other edited SSDTs
- `EC`: EC patch
- `ETH0`: Renames GLAN -> ETH0 and sets device properties.
- `HDEF`: Renames HDAS -> HDEF, sets device properties and sets `layout-id` to 1 for this particular board.
- `PLUG`: Sets `plugin-type` to 1, needed for AGPM and proper CPU power management.
- `RX580`: Renames PEGP -> GFX0 and D05A -> HDAU and defines several methods related to the RX580. The Orinoco framebuffer is most appropriate for my card and also fixes an issue where the DVI output is blank on boot.
- `SAT0`: Sets device properties for the SATA controller
- `SBUS`: System bus patches
- `UIAC`: SSDT defining which ports to keep enabled, for USB Inject All
- `USBX`: Sets various USB power management properties
- `XHC`: Sets USB controller device properties

# Drivers
Not included in this repo, I use the following drivers:

- [ApfsDriverLoader](https://github.com/acidanthera/AppleSupportPkg)
- FwRuntimeServices (in the OpenCore package)
- HFSPlus (grab it from a macOS install)

# Kexts
Not included in this repo, I use the following kexts:

- [AirportBrcmFixup](https://github.com/acidanthera/AppleALC): Used to set a country code and also to disable WoWLAN to prevent unnecessary wakeups.
- [AppleALC](https://github.com/acidanthera/AppleALC): Fixes for HDMI and onboard audio
- [IntelMausi](https://github.com/acidanthera/IntelMausi): Driver for the built-in Ethernet controller
- [Lilu](https://github.com/acidanthera/Lilu): Requirement for most other kexts in this list
- [NVMeFix](https://github.com/acidanthera/NVMeFix): Fixes for NVMe drives
- [USB Inject All](https://bitbucket.org/RehabMan/os-x-usb-inject-all/src/master/): Used for injecting only USB ports which are in use, to keep the system inside the 15 port limit
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC): SMC emulator
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen): Required for proper functioning of both the integrated graphics card in headless/connectorless mode, and the RX580

# Current issues
## Netflix
Netflix in Safari doesn't work with error `S7363-1260-FFFFD089`. `shikigva=80` added in [WhateverGreen 1.3.6](https://github.com/acidanthera/WhateverGreen/releases/tag/1.3.6) might be able to fix this. However, this hangs my GPU as soon as Safari starts rendering any site.

## Bluetooth lag
The Fenvi card seems to have issues with Bluetooth. A ton of stuttering can be observed with a Magic Trackpad 2 and Logitech MX Master. A workaround is to connect the card to a 5GHz(?) Wi-Fi network, even if you are using Ethernet.

## CPU power management
CPU power management doesn't seem to work well? The CPU seems to stay high in frequency even when idle according to Intel Power Gadget. It does fluctuate, which indicates that power management is working in one form or another.

Tried CPUFriend + a data provider, ssdtPRgen, and just plugin-type 1. What seems to work best is just not setting plugin-type at all. The CPU drops to much lower frequencies. Without plugin-type, however, AGPM does not work.