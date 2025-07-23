# Meraki OpenWRT Project

## About

This repository contains bootloader builds and installation instructions for installing OpenWRT on various Cisco Meraki devices. The project aims to give these devices a second life by replacing the proprietary Meraki firmware with the open-source OpenWRT operating system, providing more flexibility and control over your hardware.

## Supported Devices

The following Meraki devices are currently supported by this project:

| Device | Type | CPU | RAM | Flash | Ethernet Ports | Installation Difficulty |
|--------|------|-----|-----|-------|----------------|------------------------|
| [MR30H](mr30h/README.md) | Access Point | Qualcomm Atheros IPQ4029 | 256MB | 128MB NAND | 5x Gigabit | Moderate |
| [Z3](z3/README.md) | Teleworker Gateway | Qualcomm Atheros IPQ4029 | 512MB | 128MB NAND | 5x Gigabit | Hard (Requires hardware programmer) |
| [Go GX20](z3/README.md) | Wired Router | Qualcomm Atheros IPQ4029 | 512MB | 128MB NAND | 5x Gigabit | Hard (Requires hardware programmer) |

## Prerequisites

Before attempting to install OpenWRT on your Meraki device, ensure you have the following:

### Hardware Tools
- Small Torx screwdrivers (T8, T10 depending on device)
- Plastic prying tools (guitar picks work well)
- UART adapter (3.3V compatible)
- For Z3/GX20: TSOP48 NAND programmer (e.g., NANDWay, XGecu TL866/T48/T56)
- For some MR30H models: TSOP48 NAND programmer or 360 clip

### Software Requirements
- Python 3 (for ubootwrite.py script)
- Serial terminal program (e.g., screen, minicom, PuTTY)
- TFTP server (for loading firmware)

### Skills Required
- Basic Linux command line knowledge
- Understanding of bootloaders and firmware
- Soldering skills (for some devices)
- Ability to follow technical instructions carefully

## Common Troubleshooting

### No UART Output
- Verify your UART adapter is set to 3.3V
- Check the connection pins (Tx, Rx, GND) - DO NOT connect to VCC
- Ensure your terminal program is set to 115200 baud

### Failed Flash
- If the device doesn't boot after flashing, you may need to recover using hardware methods
- For MR30H with older U-Boot, try using ubootwrite.py to restore
- For Z3/GX20, you'll need to reflash the NAND using an external programmer

### Boot Loop
- If the device is stuck in a boot loop, it may be due to incompatible U-Boot and firmware
- For MR30H, check if you need to write U-Boot to part.safe as described in the documentation

### Network Issues
- MR30H: Use LAN port 4 for network operations in U-Boot
- Z3/GX20: Use the WAN port for network operations in U-Boot

## Contributing

Contributions to this project are welcome! Here's how you can help:

### Reporting Success/Failure
- Document your experience with specific device models
- Note any variations in hardware or firmware versions

### Code Contributions
- Improve existing scripts or documentation
- Add support for new Meraki devices

### Documentation Improvements
- Fix errors or clarify instructions
- Add photos of the disassembly/installation process
- Translate documentation to other languages

To contribute, please:
1. Fork the repository
2. Make your changes
3. Submit a pull request with a clear description of the changes

## Disclaimer

The instructions in this repository are provided AS-IS with no warranty. Modifying your device will void any warranty and may result in a bricked device if done incorrectly. Proceed at your own risk.
