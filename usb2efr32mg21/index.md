# zigbee firmware and releated

EmberZNet (EZSP) Zigbee 3.0 / Zigbee Coordinator (NCP) firmmware for Silicon Labs EFR32MG21 DIY USB dongle.

## efr32mg21usb2zigbee

 Files for usb2zigbee( efr32mg21) locate in folder [https://github.com/dongbh/zigbee/](https://github.com/dongbh/zigbee/)
![picture of diy usb dongle](../res/3d_pcb_zs3l.png)

| Configuration Parameter  | Value |Flow Control|
|---------------|-----------|-----------|
| TX  | PA06 ||
| RX  | PA05 ||
| RTS | PB00 ||
| CTS | PB01 ||
| Bootloader | bootloader-uart-xmodem-zs3l.s37 ||
| Firmware(7.4.3) | ncp-uart-zs3l-hw-115200-7.4.3-12.5.gbl | Hardware|
| Firmware(7.4.3) | ncp-uart-zs3l-noflowcontrol-115200-7.4.3-12.5.gbl | None|

## How to Flash:
1. Install universal-silabs-flasher
```
pip install universal-silabs-flasher
```
2. Download firmware

3. Flashing firmware
```
universal-silabs-flasher --device com5 flash --firmware ncp-uart-zs3l-hw-115200-7.4.3-12.5.gbl
```
device can be comX (windows) or /dev/ttyUSB0 (linux), replace it and firmware file with your own.
4. Check Status
```
universal-silabs-flasher --device com5 probe
```
