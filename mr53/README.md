# Overview

The Meraki MR53 is a Cisco 802.11ac AP with one Gigabit Ethernet and one 2.5G Ethernet ports.
It is powered either by a 12V barrel jack or POE.

The MR53 **is not** supported by OpenWrt yet/ever. This repository is provided only to assist
others in their effort to support the device.

The following is provided AS-IS with absolutely NO warranty or support!

|||
|--|--|
|Model|MR53|
|CPU|Qualcomm Atheros IPQ8068|
|Flash MB|128 NAND|
|RAM MB|512|
|WLAN Hardware|Qualcomm Atheros IPQ8068|
|WLAN 2.4GHz|b/g/n 4x4|
|WLAN 5.0GHz|a/n/ac 4x4|
|Ethernet 1Gbit ports|1|
|Ethernet 2.5Gbit ports|1|

The MR53 is similar to the Meraki MR52, in that they are both IPQ8068 designs.
The MR53 differs in the use of the QCA8511 switch and 2.5G Ethernet port.

The Meraki MR53 has an internal switch (QCA8511) which is connected to the 
ipq80xx via four 1Gbit QSGMII links. Three of these links are bonded
together to provide the 2.5G interface, from the Meraki firmware bootlog:

```
[   13.198922] qca_85xx_sw: 8511 switch initialized
[   13.228942] bonding: wired0 is being created...
[   13.237226] wired0: Setting xmit hash policy to layer2+3 (2)
[   13.237362] wired0: Adding slave eth1
[   13.276632] wired0: Enslaving eth1 as an active interface with an up link
[   16.282915] wired0: Adding slave eth2
[   16.282993] eth2: 1000 Mbps Full Duplex
[   16.832863] random: nonblocking pool is initialized
[   17.319792] wired0: Enslaving eth2 as an active interface with an up link
[   17.319946] wired0: Adding slave eth3
[   17.325604] eth3: 1000 Mbps Full Duplex
[   18.369795] wired0: Enslaving eth3 as an active interface with an up link
[   18.543553] wired1: renamed from eth0
[   18.759417] IPv6: ADDRCONF(NETDEV_UP): wired1: link is not ready
```

There is **no upstream support** for QSGMII in the `dwmac` driver, therefore
the wired network interfaces **do not work** in OpenWrt.


## U-Boot 2017.07

This directory contains a custom U-Boot 2017.07 build for the MR53 that has
functional networking in U-Boot (1G interface only, 2.5G interface not supported).

Upload the `u-boot.itb` file via the included `ubootwrite-cryptid.py` script
and write it to an ubivol:
```
(SASQUATCH)# run set_ubi
(SASQUATCH)# ubi write 0x42000000 uboot 0x2f7d8
```

On subsequent boots, you can boot U-Boot 2017.07 stored in the `uboot` ubivol
with the following commands from U-Boot 2012 (after interrupting boot with `xyzzy`):
```
setenv mtdids nand0=nand0; setenv mtdparts mtdparts=nand0:0x46c0000@0x36c0000(ubi); ubi part ubi
ubi read 0x42000000 uboot
bootm 0x42000000
```

You can also experiment with writing U-Boot to one of the `bootkernel` ubivols
(e.g. `bootkernel1`) to automatically boot into U-Boot 2017.07

## References

[qca_85xx_sw_regdef.h](https://github.com/halmartin/wired-arm-qca-meraki-linux/blob/4220375b89153be1fa728b6cb62118d6c0fb166c/drivers/net/phy/qca_85xx/qca_85xx_sw_regdef.h)


