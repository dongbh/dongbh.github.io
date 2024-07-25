# zigbee firmware and releated

EmberZNet (EZSP) Zigbee 3.0 / Zigbee Coordinator (NCP) firmmware for Silicon Labs EFR32MG13 and EFR32MG21 DIY USB dongle.

## efr32mg13p732usb2zigbee

 Files for usb2zigbee( EFR32MG13) locate in folder [https://github.com/dongbh/zigbee/](https://github.com/dongbh/zigbee/)
![picture of diy usb dongle](../res/3d_pcb.png)

| Configuration Parameter  | Value |Flow Control|
|---------------|-----------|-----------|
| TX  | PA0 ||
| RX  | PA1 ||
| RTS | PF4 ||
| CTS | PA2 ||
| Bootloader | bootloader-uart-xmodem.s37 ||
| Firmware(6.10) | ELE_MG13_zb_ncp_115200_610_211112.gbl |None|
| Firmware(7.4.3) | ncp-uart-hw-115200-7.4.3.gbl | Hardware|
| Firmware(7.4.3) | ncp-uart-noflowcontrol-115200-7.4.3.gbl | None|

## Usage:
1. Install universal-silabs-flasher
```
pip install universal-silabs-flasher
```
2. Download firmware

3. Flashing firmware
```
universal-silabs-flasher --device com5 flash --firmware ncp-uart-hw-115200.7.3.2.gbl
```
device can be comX (windows) or /dev/ttyUSB0 (linux), replace it and firmware file with your own.
4. Check Status
```
universal-silabs-flasher --device com5 probe
```