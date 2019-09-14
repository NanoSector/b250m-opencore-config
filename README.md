# b250m-opencore-config
OpenCore configuration for my personal desktop.

# OpenCore version tested with: 0.5.0
Please do not use this with any newer or older version as some options may no longer exist or be interpreted differently, or new options may be needed in order to successfully boot.

# Specs
- CPU: i5 6400
- Board: Gigabyte B250M-D3H
- GPU: Sapphire Radeon RX 580 4GB something something…
- RAM: 16 GB Crucial Ballistix DDR4 @ 2133MHz
- SSD: Toshiba XG5 256GB NVMe
- Wifi: Fenvi T919
- Monitors: 2x LG Flatron W2261VP, 1x BenQ XL2420T
- Audio: HDMI audio via an Onkyo TX-NR609

# BIOS Settings
See the screenshots under the `BIOS` directory.

# OpenCore config noteworthy things

- SIP is completely enabled
- `ig-platform-id` is set to `0x19120001` for connectorless mode. This fixes JPEGs not opening properly among other things. Netflix is still broken.
- The `ConsoleControl` protocol, along with the `IgnoreTextInGraphics` and `ProvideConsoleGop` quirks, fix text from flooding the apple logo boot screen.
- `vault.plist` is *en*abled, be sure to either disable it or generate a vault.plist

# ACPI edits
See the ACPI subfolder. Most of these edits consist of setting device properties to make the devices show up nicely in macOS and have them at the native PCI paths.

- `ANS1`: Moves the NVMe drive from PXSX to ANS1 and sets device properties in its DSM method.
- `ARPT`: Moves the Fenvi card from PXSX to ARPT and sets device properties. Also sets `brcmfx-country` to set the country code in conjunction with `AirportBrcmFixup`
- `DTPG`: Adds the DTPG method used by various `_DSM` methods in other edited SSDTs
- `EC`: EC patch
- `ETH0`: Renames GLAN -> EHT0 and sets device properties.
- `PLUG`: Sets `plugin-type` to 1, needed for AGPM and CPU power management.
- `RX580`: Renames PEGP -> GFX0 and D05A -> HDAU and defines several methods related to the RX580. The Orinoco framebuffer is most appropriate for my card and also fixes an issue where my leftmost DVI monitor is blank on boot.
- `SAT0`: Sets device properties for the SATA controller
- `SBUS`: System bus patches
- `UIAC`: SSDT defining which ports to keep enabled, for USB Inject All
- `USBX`: Sets various USB power management properties
- `XHC`: Sets USB controller device properties

# Drivers
Not included in this repo, I use the following drivers:

- ApfsDriverLoader
- AppleGenericInput
- AppleUiSupport
- FwRuntimeServices
- HFSPlus
- VirtualSmc

Most originate from [AppleSupportPkg](https://github.com/acidanthera/AppleSupportPkg). VirtualSmc.efi is included in the VirtualSMC package, linked below.

# Kexts
Not included in this repo, I use the following kexts:

- AGPMEnabler.kext: Used for enabling AGPM on the integrated and dedicated GPU. Can be generated using various tools, my kext would be useless on most other configurations.
- [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup): Used mainly for country code patches as the Fenvi card is natively supported in macOS
- [AppleALC](https://github.com/acidanthera/AppleALC): Fixes for HDMI audio. I have the onboard sound controller disabled.
- [IntelMausi](https://github.com/acidanthera/IntelMausi): Driver for the built-in Ethernet controller
- [Lilu](https://github.com/acidanthera/Lilu): Requirement for most other kexts in this list
- [USB Inject All](https://bitbucket.org/RehabMan/os-x-usb-inject-all/src/master/): Used for injecting only USB ports which are in use, to keep the system inside the 15 port limit
- [VirtualSMC](https://github.com/acidanthera/VirtualSMC): SMC emulator
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen): Required for proper functioning of both the integrated graphics card in headless/connectorless mode, and the RX580