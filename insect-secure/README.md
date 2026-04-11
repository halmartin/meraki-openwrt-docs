# Overview

These instructions are ONLY for users with an MR33 or MR74 which had the secure boot fuses blown:
```
Secure boot NOT enabled! Blowing fuses... Resetting now.
```

If your MR33 or MR74 has UART output after power-up, then DO NOT PROCEED.

If you desoldered NAND to downgrade U-Boot and have no UART output on your device, you most likely have a bad solder connection. These instructions do not apply to you!

# Disclaimer

The following instructions are provided AS-IS and the author assumes no liability for any damages incurred.

Disassembling your devices and flashing bootloaders/firmwares will VOID any remaining warranty. Incorrectly flashing your device will lead to a brick that is only recoverable via hardware methods.

By continuing, you acknowledge that you understand the risks and hereby assume all responsibility for any damages or loss of functionality that may result.

# Background

Meraki shipped their early 802.11ac/WiFi 5 devices ("insect") without secure boot. Later on, they released more devices in the insect family with secure boot enabled from the factory.

The U-Boot code is common to all insect devices, the difference is for a device with secure boot enabled the boot chain (SBL, U-Boot, etc) is signed and signatures are verified.

U-Boot checks during boot if the device supports secure boot, and if the product ID does not match, it will burn the secure boot fuses and reset:
```
static int do_meraki_qca_boot(cmd_tbl_t * cmdtp, int flag, int argc, char * const argv[])
{
   /* unsupported boards */
   switch(get_meraki_product_id()) {
      case MERAKI_BOARD_STINKBUG: // MR33 - insect/ipq40xx
      case MERAKI_BOARD_LADYBUG: // MR74 - insect/ipq40xx
      case MERAKI_BOARD_NOISY_CRICKET: // MR30H - insect/ipq40x
      case MERAKI_BOARD_YOWIE: // MR42 - cryptid/ipq80xx
      case MERAKI_BOARD_BIGFOOT: // MR52 - cryptid/ipq80xx
      case MERAKI_BOARD_SASQUATCH: // MR53 - cryptid/ipq80xx
      case MERAKI_BOARD_WOOKIE: // MR84 - cryptid/ipq80xx
         return 0;
      default:
         break;
   }

   /* Check 0: check/force secure boot on */
   force_secboot();
```

If an MR33, MR74, or MR30H device does not correctly read the product ID from the EEPROM (because the EEPROM was corrupted, shorted, not present, etc), U-Boot will burn the secure boot fuses, after which the device will no longer boot. The device does not boot after the secure boot fuses are blown because the boot chain on these devices is unsigned.

```
0x000000000000-0x000000100000 : "sbl1"
0x000000100000-0x000000200000 : "mibib"
0x000000200000-0x000000300000 : "bootconfig"
0x000000300000-0x000000400000 : "qsee"
0x000000400000-0x000000500000 : "qsee_alt"
0x000000500000-0x000000580000 : "cdt"
0x000000580000-0x000000600000 : "cdt_alt"
0x000000600000-0x000000680000 : "ddrparams"
0x000000700000-0x000000900000 : "u-boot"
0x000000900000-0x000000b00000 : "u-boot-backup"
0x000000b00000-0x000000b80000 : "ART"
0x000000c00000-0x000007c00000 : "ubi"
```

There is a workaround. Meraki shipped the MR20/GR10 (maggot) device with secure boot enabled from the factory, and this device has a boot chain that is signed with the insect certificate. Insect devices which have had secure boot enabled in the field can be restored by overwriting NAND from `0x000000000000-0x000000b00000` with the signed boot chain from an MR20/GR10.

Decompress `insect-secure.bin.gz` dump (contains OOB data) and overwrite NAND from `0x000000-0xb58000` (length `0xb58000`).

Decompress `ubi.bin.gz` dump (contains OOB data) and overwrite the `ubi` portion of NAND from `0xc60000-0x8400000` (length `0x77a0000`).

# OpenWrt

> **WARNING**: The official OpenWrt builds for the MR33 and MR74 will install the kernel to `part.safe` when you run `sysupgrade` which will overwrite the unlocked U-Boot.

If you do not change `CI_KERNPART` before running `sysupgrade`, OpenWrt will overwrite your unlocked U-Boot. This means your device will automatically boot OpenWrt with no way to interrupt boot or recover from a soft-brick.

You must edit `/lib/upgrade/platform.sh` and change `CI_KERNPART="part.safe"` to `CI_KERNPART="part.old"`
before running `sysupgrade`:

```
	meraki,mr33|\
	meraki,mr74)
		CI_KERNPART="part.old"
		nand_do_upgrade "$1"
		;;
```


# New U-Boot

The new U-Boot build uses the space character `" "` (without quotes) to interrupt boot.

It also supports networking (e.g. `tftpboot`).

Proceed to load and execute the OpenWrt initramfs image:
```
STINKBUG # setenv serverip <your tftp server IP>
STINKBUG # dhcp
STINKBUG # tftpboot openwrt-ipq40xx-generic-meraki_mr33-initramfs-uImage.itb
STINKBUG # bootm
```
