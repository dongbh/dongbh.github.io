## Flash HomeAssistant OS into dsgw-210

### Supported boards:
--- 
- Dusun dsgw-210

### Read First:
---
1. This firmware uses mainline u-boot. If you want to flash it back to the original firmware, You NEED use **Option 1** below to re-flash it.
1. The serial baud rate changes from 115200 to 1500000 with 8 data bits, NO parity, you’d better use a cp2104 usb2uart bridge (cp2102 won’t work).

### Download flash tools and firmware
---
1. Install rockchip flash tools as in dusun wiki [dsgw-210](https://wiki.dusuniot.com/iot_gateway_with_applications/dsgw-210-rk3328-home-assistant-gateway/quick-start-guide). I prefer a recent version(which has a checkbox: "Write by Address", download [RKDevTool_Release_v3.31.zip](https://download.t-firefly.com/product/Board/RK3588/Tool/Window/RKDevTool_Release_v3.31.zip) and [DriverAssitant_v5.1.1.zip](https://download.t-firefly.com/product/Board/RK3588/Tool/Window/DriverAssitant_v5.1.1.zip) from [firefly](https://www.t-firefly.com/doc/download/183.html) site .
1. Download the loader [rk3328_loader_v1.14.249.bin](https://github.com/dongbh/operating-system/releases/download/13.1/rk3328_loader_v1.14.249.bin) and firmware [haos_dsgw-210-13.1.img.xz](https://github.com/dongbh/operating-system/releases/download/13.1/haos_dsgw-210-13.1.img.xz), unzip the firmware.

### Flash the box
---
1. Launch RKDevTool, and prepare to connect the PC to the dsgw-210 (hereinafter called the "box") using a type-c cable.
  ![connect the pc to the box](../res/dsgw-210-5.png)
1. Make the box into MASKROM mode:
  ![maskrom mode](../res/dsgw-210-1.png)
1. Option 1: Provide 3.3V voltage (eg. 3.3v from usb2uart bridge ) to the point and then connect to the box using the type-c cable, and it will show in MaskRom mode, then remov the 3.3v voltage.
  ![maskrom probe](../res/dsgw-210-2.jpg)
1. Option 2: Connect to the box using the type-c cable, it will show as an "ADB device", click "Advanced Function" tab, and then click "Go Maskrom". 
  ![from adb to maskrom](../res/dsgw-210-3.png)
1. Click the "Download Image" tab, and input as follows, check the "Write by Address" checkbox and then click "Run" button. The list box on the right shows the flashing progress. When flashed to 100%, the box will restart, and the green leds turns on about 5 seconds later, wait 1-2 minutes for the box to startup.

| Address     | Path |
| ----------- | ----------- |
| 0xCCCCCCCC   | d:\rk3323\rk3328_loader_v1.14.249.bin       |
| 0x00000000   | d:\rk3328\haos_dsgw-210-13.1.img.xz |

  ![flashing the box](../res/dsgw-210-4.png)
 
### Wait for HAOS to startup
---
1. Now lookup the ip address of the box (the box gets ip using dhcp, ping homeassistant -4 or just lookup it from the dhcp server).
1. Browse http://ip_of_the_box:4357 and then http://ip_of_the_box:8123.

## Update HAOS
1. Just click the UPDATE button when prompted.
  ![flashing the box](../res/dsgw-210-6.png)

## Files list
- RKDevTool: [RKDevTool_Release_v3.31.zip](https://download.t-firefly.com/product/Board/RK3588/Tool/Window/RKDevTool_Release_v3.31.zip) 
- DriverAssistant: [DriverAssitant_v5.1.1.zip](https://download.t-firefly.com/product/Board/RK3588/Tool/Window/DriverAssitant_v5.1.1.zip)
- loader: [rk3328_loader_v1.14.249.bin](https://github.com/dongbh/operating-system/releases/download/13.1/rk3328_loader_v1.14.249.bin) 
- firmware: [haos_dsgw-210-13.1.img.xz](https://github.com/dongbh/operating-system/releases/download/13.1/haos_dsgw-210-13.1.img.xz)

## Note
- The mac address of the Ethernet changes when flashing using MASKROM mode. Use the fw_setenv command to set mac to original (on th back of the box)
```
fw_setenv eth1addr '00:11:22:33:44:55'
```

## Hardware support

### Working hardware
    - Serial / Uart
    - Ethernet
    - Led
    - Restart Key

### TODO
    - Wifi
    - Bluetooth

If you have any suggestions, please contact me via email at dongbh # outlook.com.
