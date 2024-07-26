## zbgw7628 zigbee 有线网关

zbgw7628 是一款支持 zigbee 的有线网关，用于这些设备接入 home assistant。

### 主要特点

- 处理器为mt7628或mt7688，操作系统为openwrt 23.05, 稳定可靠；
- zigbee 模块采用 efr32mg21, 信号稳定，带机量大；
- u-boot、系统固件和zigbee固件均支持升/降级；
- zigbee模块外置高增益天线；
- 支持 zha 和 zigbee2mqtt；
- 配置文件附后，可以自行编译升级；
- 分为86面板版和桌面版。86面板版本基于CMCC 300AP改制，只支持POE供电。桌面版支持 POE 或 type-c 供电。

### 86面板板设备

 接口说明：<br> - 后面RJ45插座：用于接入到POE交换机。<br> - 面板下面RJ45插座：可以用于接入PC。<br> - 面板两侧后右侧分别有电源和复位键。<br> led指示灯：<br> - power: 开机后常亮。<br> - SYS: 系统心跳指示，闪烁表示正常。<br> - LAN：网线正常后常亮，有数据传输会闪烁。<br> - WIFI: zigbee 数据传输时会闪烁。 |  ![alt text](/res/zbgw7628-0.jpg) ![alt text](/res/zbgw7628-4.jpg) ![alt text](/res/zbgw7628-3.jpg) ![alt text](/res/zbgw7628-2.png)  


### 桌面版设备

接口说明：<br> - RJ45插座：用于接入到(POE)交换机。<br> - type-c插座：设备供电（5-12V，500ma), POE设备不必再供电。<br>led指示灯：<br>- 绿色: 电源指示，开机后常亮。<br>- 黄色: 系统心跳指示，闪烁表示正常。<br> - 蓝色：zigbee数据传输会闪烁。 |  ![体积小巧](/res/zbgw8201.png "网关照片")


### 使用方法
1. 通过POE交换机接上网线（86面板版本需插在面板背面接口，对于不支持POE的桌面版还需要接入typec电源），等待网关获取ip地址（时间约1分钟到2分钟，网关名字为 zbgw7628，可以从路由器上查看，也可以运行 ping zbgw7628.local 来确认地址，以下假设获取的地址为 192.168.1.99。
2. 使用ZHA(Zigbee Home Automation):
    - 打开homeassistant网页，依次选择进入左边栏下面的“配置——设备与服务——添加集成”，输入"ZHA"后，选择"Zigbee Home Automation"，如果出现"你想要添加什么？"页面，继续选择"Zigbee Home Automation"。
    - 在"无线射频类型"中选择 "EZSP"。
    - 在"串口设置"中，输入串口设备路径：socket://192.168.1.99:6636 , 记得替换为您第一步中查到的地址，端口速度 和 数据流控制 不用管，用缺省值。
    - 提交后会出现"网络构成"，根据需要选择即可（建议选择 "删除网络设置并创建新网络"）。
    - 等待完成。
    - 返回"设备与服务"，打开新安装的"Zigbee Home Automation"下的"Silicon Labs EZSP"，点击"通过此设备添加设备"就可以添加您的zigbee设备了。
3. 使用 z2m(zigbee2mqtt)，在配置文件中添加(记得替换ip地址, ezsp也可以换成 ember)：<br>
    ``` port: tcp://192.168.1.99:6636
        adapter: ezsp
    ```

### 升级 ezsp 
1. 下载 [universal-silabs-flasher](https://github.com/NabuCasa/universal-silabs-flasher)。
2. 下载ezsp固件，请选择[ncp-uart-mg210l-noflowcontrol-XXXXX-115200.gbl](https://github.com/dongbh/zigbee)版本，目前最新的是7.4.3。
3. 假设设备的ip地址为 192.168.1.99, 下载的固件为当前文件夹下的 ncp-uart-mgm210l-noflowcontrol-115200.7.4.3.gbl，则执行如下命令升级(记得替换ip地址)：
```
universal-silabs-flasher  --device socket://192.168.1.99:6636  flash --firmware ncp-uart-mgm210l-noflowcontrol-115200.7.4.3.gbl
```

### 升级固件
1. zbgw7628升级固件时的ip地址固定为 192.168.1.101, 所以先将电脑的ip地址设定为 192.168.1.x, 如192.168.1.100, 子网掩码255.255.255.0，其他可以不设。
2. 接上网线，接通电源（如果是POE版，则只插上POE网线），看到led发光后（7秒之内）按一下复位键（在按的同时会看到led灯发光），此时系统进入升级模式, 。
3. 在浏览器中输入设备的网址： http://192.168.1.101，根据页面提示可以分别更新固件和u-boot。u-boot一般不建议升级。
4. 系统启动一般需要60-120秒。
