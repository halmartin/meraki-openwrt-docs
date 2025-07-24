# About

This repository contains bootloader builds and flashing instructions for various Meraki products.

There is no known method to flash the devices without physically opening them and removing/programming chips. For outdoor devices, disassembly will compromise the weather seal.

Device builds and instructions can be found in their respective directories.

# Prerequisites

Before starting, ensure you have the following:

## Tools
- Small Torx and Philips screwdrivers
- Plastic prying tools (e.g. guitar picks)
- UART adapter (3.3V compatible) and 2.54mm "Dupont" wires
- TSOP48 or BGA63 NAND programmer (e.g. XGecu TL866/T48/T56)
- I2C EEPROM programmer (e.g. ch341a), SOIC8 chip clip or socket
- Soldering iron or hot air station (e.g. 858D, or similar), flux

## Software
- Serial terminal program (e.g. screen, minicom)
- tftp server

# FAQ

## Supported devices

Devices with subdirectories will have, at a minimum, bootloader support. OpenWrt support may not be available for all devices.

Some devices are already supported in OpenWrt, they are included as submodules in this repository. Refer to the OpenWrt forum/wiki for installation instructions.

## Donating hardware

If you own Meraki hardware not listed here that you would like to see supported and are willing to donate, please contact me.

Meraki switches utilise chips without upstream Linux support, meaning they cannot be supported in OpenWrt.

**Note**: many recent Meraki devices have secure boot, which may prevent them from ever running third-party software.

## Reporting a bug

OpenWrt bug reports should be filed to the [OpenWrt project](https://github.com/openwrt/openwrt/issues)

You are welcome to submit corrections and bugfixes via a PR.
