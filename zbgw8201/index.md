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
- type-c 供电，![体积小巧](/res/zbgw8201.png "网关照片")

### 使用方法
1. 接通电源和网线，等待网关获取ip地址（网关名字为 zbgw8201，可以从路由器上查看，也可以运行 ping zbgw8201.local 来确认地址，以下假设获取的地址为 192.168.1.99。
2. 使用ZHA(Zigbee Home Automation):
    - 打开homeassistant网页，依次选择进入左边栏下面的“配置——设备与服务——添加集成”，输入"ZHA"后，选择"Zigbee Home Automation"，如果出现"你想要添加什么？"页面，继续选择"Zigbee Home Automation"。
    - 在"无线射频类型"中选择 "EZSP"。
    - 在"串口设置"中，输入串口设备路径：socket://192.168.1.99:6636 , 记得替换为您第一步中查到的地址，端口速度 和 数据流控制 不用管，用缺省值。
    - 提交后会出现"网络构成"，根据需要选择即可（如选择"删除网络设置并创建新网络"）。
    - 等待完成。
    - 返回"设备与服务"，打开新安装的"Zigbee Home Automation"下的"Silicon Labs EZSP"，点击"通过此设备添加设备"就可以添加您的zigbee设备了。
3. 使用 z2m(zigbee2mqtt)，在配置文件中添加(记得替换ip地址)：<br>


        port: tcp://192.168.1.99:6636
        adapter: ezsp


4. 使用蓝牙：
    - 添加设备："设备与服务"中会自动发现 "zbgw8201"，直接点按其下面的"配置"就可以启用内置 bluetooth-proxy了。
    - 小米温度计2等ble设备会自动发现（小米温度计2的配置方法请自行查询）。
5. 如果要重新配置固件，请根据下面的"esphome 配置文件"修改。

### zbgw8201 核心配置
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

### zbgw8201-II 核心配置
```
ethernet:
  type: RTL8201
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO17_OUT
  phy_addr: 0
  power_pin: GPIO12

uart:
  id: uart_bus
  tx_pin: GPIO33
  rx_pin: GPIO35
  baud_rate: 115200
````

### esphome 配置文件
[zbgw8201.yaml](zbgw8201.yaml)
[zbgw8201-II.yaml](zbgw8201-II.yaml)

### 升级 ezsp 

### 更新
1. 20240613 新增zbgw8201-II版本，gpio17输出clk, uart 的rx 更新为 GPIO35。
2. 20240408 添加zigbee连接指示：zibgee有连接后，黄色指示灯将常亮。

### 其他
- 网关配有3个led指示灯，绿色用作了esphome系统状态，另外两个暂没有使用（见20240408更新）。
- 网关的zigbee模块和esp32模块均采用外置天线方式，其中zigbee引到网关外面，esp32的天线贴在网关内部，两者接口相同，可以互换（如用蓝牙较多，则可以将esp32的天线外置）。
