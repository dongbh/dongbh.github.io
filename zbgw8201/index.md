## zbgw8210 zigbee 和 ble 的有线网关

zbgw8201 是一款支持 zigbee 和 ble 的有线网关，用于这些设备接入 home assistant。


### 主要特点

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### 核心配置


### esphome 配置文件
·
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

#  includes:
#    - stream_server.h
#    - stream_server.cpp

external_components:
#  - source: github://oxan/esphome-stream-server
  - source: github://tube0013/esphome-stream-server-v2
#  - source: github://myhomeiot/esphome-components


ethernet:
  type: RTL8201
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO0_IN
#  clk_mode: GPIO17_OUT
  phy_addr: 0
#  power_pin: GPIO17

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



esp32_ble_tracker:
  scan_parameters:
    interval: 1100ms
    window: 1100ms
    active: true

bluetooth_proxy:
  active: true
·

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
