## zbgw8210 zigbee 和 ble 有线网关

zbgw8201 是一款支持 zigbee 和 ble 的有线网关，用于这些设备接入 home assistant。


### 主要特点

- 处理器为esp32， 采用esp-wroom-32ue，带有ipex天线接口，可接外置天线；
- zigbee 模块采用 efr32mg13p732, 信号稳定，带机量大；
- 网络芯片为rtl8201，稳定可靠；
- 支持ota升级，盒子可以安心呆在角落；
- 外置高增益天线（可以用于zigbee或esp32）；
- 支持 zha 和 zigbee2mqtt；
- 配置文件附后，可以自行编译升级；

### 使用方法

### 核心配置
```
ethernet:
  type: RTL8201
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO0_IN
  phy_addr: 0
  power_pin: GPIO17

uart:
  id: uart_bus
  tx_pin: GPIO33
  rx_pin: GPIO32

leds: GPIO14, GPIO4, GPIO16
```
### esphome 配置文件
```
esphome:
  name: zbgw8201

esp32:
  board: esp-wrover-kit
  framework:
    type: esp-idf
    version: recommended
    # Custom sdkconfig options
    sdkconfig_options:
      COMPILER_OPTIMIZATION_SIZE: y
    # Advanced tweaking options
    advanced:
      ignore_efuse_mac_crc: false

external_components:
  - source: github://tube0013/esphome-stream-server-v2

ethernet:
  type: RTL8201
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO0_IN
  phy_addr: 0
  power_pin: GPIO17

  # Enable fallback hotspot (captive portal) in case wifi connection fails
#  ap:
#    ssid: "Esphome Zb Bridge"

#captive_portal:

# Enable logging
logger:

# Enable Home Assistant API
api:
  reboot_timeout: 0s
ota:

#web_server:
#  port: 80

uart:
  id: uart_bus
  tx_pin: GPIO33
  rx_pin: GPIO32
#  rx_buffer_size: 2048
  baud_rate: 115200

stream_server:
  uart_id: uart_bus
  port: 6636
#  buffer_size: 2048

binary_sensor:
  - platform: homeassistant
    id: ble_gateway_discovery
    entity_id: binary_sensor.ble_gateway

sensor:
  - platform: uptime
    name: Uptime
    id: sys_uptime
    update_interval: 10s
  - platform: template
    id: esp_memory
    icon: mdi:memory
    name: ESP Free Memory
    lambda: return heap_caps_get_free_size(MALLOC_CAP_INTERNAL) / 1024;
    unit_of_measurement: 'kB'
    state_class: measurement
    entity_category: "diagnostic"

status_led:
  pin: 
    number: GPIO14
    inverted: true

output:
  - platform: gpio
    pin: 4
    id: 'yellow_out'
    inverted: true
  - platform: gpio
    pin: 16
    id: 'blue_out'
    inverted: true

switch:
  - platform: output
    name: "Yellow LED"
    output: 'yellow_out'
  - platform: output
    name: "Blue LED"
    output: 'blue_out'  

esp32_ble_tracker:
  scan_parameters:
    interval: 1100ms
    window: 1100ms
    active: true

bluetooth_proxy:
  active: true
```
### 其他
- 系统有3个led指示灯，均可以自行配置，目前第一个（GPIO14)被用于显示esphome运行状态。