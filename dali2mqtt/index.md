## dali2mqtt 有线网关

DALI2MQTT 是基于 ESP32 的 DALI 网关，用于把 DALI 灯具、分组和场景接入 Web、HTTP API、WebSocket、MQTT 以及 Home Assistant。当前固件支持 64 个 DALI 短地址灯具、16 个 DALI 分组地址和 16 个场景；Pro 固件额外启用 DALI 输入设备扫描和配置。

### 主要特点

- dali2 协议，支持短地址扫描、分配、删除、定位等功能；
- Homeassistant 自动发现(DT6/DT8)，便于管理；
- 处理器为 eps32，资源充足、省电环保；
- 有线网络，稳定可靠；
- 支持 mqtt、websocket、HTTP API 连接，信息完全同步；
- ha 下打开或关闭组，组内灯具状态会相应更新；
- 组状态可以自定义（组内灯全开则组状态为开，或只有一盏开则为开）；
- 自带管理界面，直观寻灯、分组和控制，调试方便；
- DIN 导轨支持，便于放置配电箱；
- 单路 DALI, 4个接线柱，便于接线；
- 220v误接保护，工程适用；
- 需要外接 dail电源，本网关不指供dali电源。

### 接口说明
- RJ45插座：用于接入到交换机。
- type-c插座：设备供电（5-24V，500ma)。
- led指示灯：
    - NET：常亮表示已获取有线网络IP地址，闪烁表示wifi或softAP模式，。
    - RX: 闪亮表示正在从总线接收数据，无接收熄灭
    - TX：闪亮表示正在向总线发送数据，无发送熄灭 
    - MQTT：闪烁表示未连接，常亮表示已接入服务器，闪烁表示正在mqtt有数据传输。 
    
![体积小巧](/res/dali2mqtt.png)

## 1. 初次使用
0. 安装 mqtt 服务器，并在homeassistant上配置完成;
1. 先将网关接入带有电源的dail总线，然后插上网线，最后插上typec电源；
2. 如果设备没有连上已配置 Wi-Fi，会启动 AP，名称为 `d2m-<mac>`，默认密码为 `1234567890` 或无密码。
3. 浏览器打开设备 IP 或 `http://d2m.local/`，以下假设获取的地址为 192.168.1.99。
4. Web 登录用户名为 `d2m`，默认密码为 `dali2mqtt`。
5. 在配置页设置 Wi-Fi、MQTT、设备密码和 API Token（可选）。
6. 保存配置后设备会重启。
7. 在命令页执行扫描或分配短地址，再配置灯具类型、分组和场景。
8. 多功能键操作：
    - 单击：用于在全开和全关之间来回切换，按一下全开，再按全关；
    - 双击：用于扫描总线上的灯具，同时向mqtt服务㗊上报结果，可以多次扫描更新；
    - 长按（2秒以上，5秒以下）：对新灯具分配短地址；
    - 长按（6秒以上）：删除所有已分配的地址；
    - 长按（15秒以上）：临时初始化web登录密码为 dali2mqtt；
    - 长按（30秒以上）：删除所有设置，恢复为出厂状态，并重启；

![系统拓扑图](/res/dali2mqtt-topology.png)

## 2. 网关管理界面操作
1. 进入管理界面：在同一网络下的电脑或手机的浏览器里输入网关地址，如 http://d2m.local 或 http://192.168.1.99, 打开网关管理页面(用户名为 d2m，初始密码为 dali2mqtt);
2. 配置管理：选择"配置"，进入配置页面：
    - mqtt 服务器：输入mqtt服务器的 ip 地址（如192.168.1.200)和端口(如1883)，根据服务器的配置情况输入用户名和密码，按 \{提交并重启\} 进行上传，此时网络会重启，等待网关重新启动并获取ip地址（网络灯长亮）；
    - 网络参数：可设置静态地址和wifi等参数，缺省状态下是DHCP获取地址；
    - 网关参数：设置网关的名称（显示在web页面）和web密码（6字符以上，如为空白则为初始密码 dali2mqtt，用户名为 d2m）；
    - 配置文件：下载配置是将所有的配置可以下载成 json 文件，用于备份；恢复配置用于将备份的配置恢复到设备，请先选择备份文件名；
    ![配置界面](/res/dail_web_4.png)
3. 灯具操作：选择"灯控"，进入灯具控制页面。页面会显示扫描后发现的所有灯具和分组信息：
    - 单击短地址进入找灯模式，此灯闪烁10秒钟；
    - 双击短地址可以更改地址；
    - 双击灯具名字可以更改名字；
    - 点击类型可以更改灯具类型（改为 NONE 即为删除，之后不会显示，只能重新扫描后恢复）；
    - 直接点击某灯具的分组编号即可将其加入或离开这一分组；
    - 滑动亮度或色温滑块可以直接控制灯具的亮度和色温, 如果是rgb类型，则点击色彩输入框是出现色彩界面，选择颜色后按ok按钮即可改变灯具颜色；
    ![灯控界面](/res/dail_web_1.png)
4. 分组操作：选择"组控"，进入分组控制页面：
    - 单击短地址进入找灯模式，此组所有灯闪烁10秒钟；
    - 双击组名可以更改名字；
    - 点击类型可以更改灯具类型;
    - 直接点击组内某灯具编号即可将其加入或离开这一分组；
    - 滑动亮度或色温滑块可以直接控制灯具的亮度和色温, 如果是rgb类型，则点击色彩输入框是出现色彩界面，选择颜色后按ok按钮即可改变灯具颜色；
    ![组控界面](/res/dail_web_2.png)
5. 灯具配置：选择"灯具"页面，选择某一灯具后（需要单击 保存 才能保存到网关和灯具）：
    - 可以输入渐变速率和渐变时间；
    - 可以选择需要更新的场景；
    - 可以读出网关缓存的场景数据，或直接输入后保存到网关和灯具；
    ![灯具配置](/res/dail_web_3.png)
6. 场景配置：选择"场景"页面，选择某一场景后（需要单击 保存 才能保存到网关和灯具）：
    - 可以更改场景名称；
    - 可以输入渐变速率和渐变时间；
    - 可以选择需要更新的灯具；
    - 可以读出网关缓存的场景数据，或直接输入后保存到网关和灯具；
    ![灯具配置](/res/dail_web_7.png)
7. HA 场景：选择 "HA 场景"页面后，可以在HomeAssistant里生成场景按键：
    - 选中后进行“保存” 操作可以直接生成场景按键，便于自动化调用；
    - 再次选中可以取消，需要“保存”后才能在 HomeAssistant 里删除；
    ![灯具配置](/res/dail_web_8.png)
8. 常用命令：选择"命令"页面：
    - 全部打开：切换所有灯具全开亮度（100%）；
    - 全部关闭：切换所有灯具全关亮度（0%）；
    - 闪烁寻灯：用于灯具定位，相应灯具会闪烁10秒钟，短地址有效值为0-79，其中64-79表示组0到组15；
    - 扫描灯具：扫描总线上所有短地址，并上传到mqtt服务器；
    - 扫描场景：扫描总线上所有灯具的场景；以操作所需时间较长，请在未完成前不要进行其他总线操作；
    - 同步：将之前扫描到的灯具信息上传到mqtt服务㗊（不扫描总线），当接通mqtt服务器时会自动执行此操作；
    - 分配地址: 为新灯具分配短地址（已分配短地址的不受影响），可以选择可用地址段；
    - 设置亮度曲线：为灯具分配对数/线性亮度曲线；可以选择地址段，缺省是全部（0..63);
    - 设置开机亮度：为灯具设置开机时的亮度；可以选择地址段，缺省是全部（0..63);
    - 设置故障亮度：为灯具分配故障时的亮度；可以选择地址段，缺省是全部（0..63);
    - 删除地址：用于删除某一地址（需要根据提示输入后确认），短地址有效值为0-63；
    - 删除所有地址：删除所有短地址，然后可以重新分配（需要根据提示输入后确认）；
    - 恢复出厂：删除网关所有配置后重启（需要根据提示输入后确认），总线上的灯具不受影响；
    ![常用命令](/res/dail_web_6.png)
9. 固件管理：选择"固件"页面后：
    - 在线更新：如有新固件，系统在升级后会重启；如果没有新固件；会提示不需要升级；
    - 本地更新：选择本地文件后，直接升级；
    ![固件管理](/res/dail_web_5.png)


## 3. Home Assistant

启用 MQTT 后，设备会通过 MQTT discovery 自动创建实体：

- Light：`homeassistant/light/<mac>_<addr>/config`
- Scene：`homeassistant/scene/<mac>_<addr>_scene_<scene>/config`

Light discovery 使用 JSON schema：

```json
{
  "~": "d2m_<mac>/01",
  "name": "Lamp_01",
  "platform": "light",
  "stat_t": "~/status",
  "cmd_t": "~/set",
  "schema": "json",
  "brightness": "true",
  "bri_scl": 254,
  "sup_clrm": ["brightness"],
  "avty_t": "d2m_<mac>/status",
  "pl_avail": "online",
  "pl_not_avail": "offline"
}
```

`sup_clrm` 会随灯具类型变化：

| 灯具类型 | `sup_clrm` |
| --- | --- |
| ONOFF | `["onoff"]` |
| DIMMER | `["brightness"]` |
| TC | `["color_temp"]` |
| RGB | `["rgb"]` |
| RGBW | `["rgbw"]` |
| RGBWW | `["rgbww"]` |

Scene discovery 由 `/ha_scene` 掩码控制。启用后，场景实体会向 `d2m_<mac>/<addr>/set` 发送：

```json
{"scene":<scene>}
```

## 4. MQTT

### 连接配置

在 Web 配置页填写：

| 字段 | 说明 |
| --- | --- |
| `mqtt_broker` | Broker 地址，支持普通主机名和 `.local` mDNS 主机名 |
| `mqtt_port` | TCP MQTT 端口，常用 `1883` |
| `mqtt_user` | 用户名；值为 `anonymous` 时不发送用户名密码 |
| `mqtt_passwd` | 密码 |

固件可在编译时选择 MQTT over TCP、TLS、WebSocket 或 WSS。运行时 Topic 格式不变。

### Topic 约定

`<mac>` 为设备 MAC 字符串，不含冒号。基础 topic：

```text
d2m_<mac>
```

| Topic | 方向 | 说明 |
| --- | --- | --- |
| `d2m_<mac>/status` | 发布 | 网关在线状态，payload 为 `online` / `offline`，retain |
| `d2m_<mac>/<addr>/set` | 订阅 | 灯具/分组控制，`<addr>` 为两位地址，如 `01`、`64` |
| `d2m_<mac>/<addr>/status` | 发布 | 灯具/分组 JSON 状态，retain |
| `homeassistant/light/<mac>_<addr>/config` | 发布 | Home Assistant light discovery，retain |
| `homeassistant/scene/<mac>_<addr>_scene_<scene>/config` | 发布 | Home Assistant scene discovery，retain |

设备连接 MQTT 后会订阅：

```text
d2m_<mac>/+/set
```

并发布所有已启用灯具/分组、HA 场景和 Pro 输入设备的 Home Assistant discovery。

### 控制 Payload

#### 开关、亮度、色温

```json
{"state":"ON"}
```

```json
{"state":"OFF"}
```

```json
{"state":"ON","brightness":180}
```

```json
{"state":"ON","brightness":180,"color_temp":3000}
```

规则：

- `brightness` 会限制在 `0` - `254`。
- `color_temp` 会限制在 `1999` - `6535`；普通色温建议使用 `2000` - `6535`，`1999` 仅用于兼容场景中的“无色温/删除色温场景”语义。
- 只发送 `{"state":"ON"}` 时，固件会使用当前亮度；内部会把未指定亮度转换为 `254`。

#### RGB / RGBW / RGBWW

```json
{
  "state": "ON",
  "brightness": 200,
  "color": {
    "r": 255,
    "g": 128,
    "b": 0,
    "w": 32,
    "c": 0
  }
}
```

`r`、`g`、`b`、`w`、`c` 均限制在 `0` - `255`。`w` 对应 WAF 的 W 通道，`c` 对应 A/C 通道。

#### 场景

```json
{"scene":2}
```

执行目标灯具/分组的场景，场景号范围 `0` - `15`。Home Assistant scene discovery 的 `pl_on` 使用的也是这个格式。

场景色温使用 `1999` 作为哨兵值：保存 TC 场景时，`1999` 表示该场景不包含有效色温，DALI 写入时会按删除该色温场景处理；有效色温应使用 `2000` - `6535`。

#### 淡入淡出

```json
{"fadetime":4}
```

```json
{"fastfadetime":8}
```

```json
{"extendfadetime":3,"multiplier":4}
```

`fadetime`、`fastfadetime` 范围为 `0` - `15`。`extendfadetime` 会组合为 `(multiplier << 4) + extendfadetime`，默认 `multiplier=4`。

#### 亮度曲线

设置当前地址的亮度曲线：

```json
{"dimming_curve":1}
```

`dimming_curve` 取值为 `0`=对数，`1`=线性。

范围设置可使用 `255` 地址并提供 `start`/`end`：

```json
{"dimming_curve":1,"start":0,"end":79}
```

也可以使用通用命令形式：

```json
{"command":"curve","start":0,"end":79,"value":1}
```

#### 管理命令

MQTT `command` 字段只允许：

```json
{"command":"sync"}
```

```json
{"command":"scan"}
```

```json
{"command":"curve","start":0,"end":79,"value":1}
```

说明：

- `sync`：重新发布所有 MQTT/Home Assistant discovery。
- `scan`：扫描 `0` - `63` 短地址。
- `curve`：设置亮度曲线，`value` 为 `0` 对数或 `1` 线性，范围由 `start`/`end` 指定。
- MQTT 不支持通过 `command` 直接执行 `delete`、`ota`、`reboot` 等敏感命令。

### 状态 Payload

状态发布到：

```text
d2m_<mac>/<addr>/status
```

示例：

```json
{"state":"OFF"}
```

```json
{"state":"ON","brightness":180}
```

```json
{
  "state": "ON",
  "brightness": 180,
  "color_temp": 3000,
  "color_mode": "color_temp"
}
```

RGB 示例：

```json
{
  "state": "ON",
  "brightness": 200,
  "color_mode": "rgbw",
  "color": {
    "r": 255,
    "g": 128,
    "b": 0,
    "w": 32
  }
}
```



## 5. HTTP 接口

### 鉴权

推荐给外部自动化调用的接口是：

```http
POST /api/v1/commands
Authorization: Bearer <api_token>
Content-Type: application/json
```

`api_token` 在 Web 配置页生成。留空表示禁用 HTTP API，调用会返回：

```json
{"error":"api_token_disabled"}
```

### `POST /api/v1/commands`

这是面向外部自动化的安全命令接口。请求体最大 255 字节。

通用字段：

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `cmd` 或 `cmds` | string | 是 | 命令名称 |
| `target` 或 `lamp` | number | 是 | 目标地址，灯具/分组/广播 |
| `value` | number | 否 | 命令参数，默认 `0` |
| `start` / `end` | number | 否 | 仅 `curve` 可用，作为 `target=(start<<8)+end` 的可读写法 |
| `curve` 或 `dimming_curve` | number | 否 | 仅 `curve` 可用，`0`=对数，`1`=线性 |
| `brightness` | number | 否 | 仅 `status` 使用，`0` - `255` |
| `color_temp` | number | 否 | 仅 `status` 使用，`0` 或 `1999` - `6535`；普通色温建议使用 `2000` - `6535`，`1999` 为场景哨兵值 |
| `rgbwaf` 或 `name` | string | 否 | 仅 `status` 使用，格式如 `0xRRGGBBWWAAFF` |

成功响应：

```json
{"ok":true,"cmd":"status","target":1}
```

错误响应示例：

| HTTP 状态 | `error` | 说明 |
| --- | --- | --- |
| `400` | `invalid_json` | 请求体不是 JSON |
| `400` | `invalid_command` | 缺少字段、地址非法或命令格式错误 |
| `400` | `invalid_command_params` | 命令参数超出允许范围 |
| `401` | `missing_bearer_token` | 缺少 Authorization |
| `401` | `invalid_authorization_scheme` | 不是 Bearer |
| `403` | `api_token_disabled` | API Token 未启用 |
| `403` | `invalid_bearer_token` | Token 错误 |
| `403` | `command_not_allowed_for_token` | API Token 接口不允许该命令 |
| `500` | `queue_failed` | 命令队列写入失败 |

#### HTTP API 支持的命令

| 命令 | 作用 | target/lamp | value | 示例 |
| --- | --- | --- | --- | --- |
| `status` | 设置开关、亮度、色温、颜色 | `0` - `79` 或 `255` | `(brightness << 16) + color_temp`，也可用 `brightness`/`color_temp` 字段 | `{"cmd":"status","target":1,"brightness":128}` |
| `scene` | 执行场景 | `0` - `79` 或 `255` | 场景号 `0` - `15` | `{"cmd":"scene","target":64,"value":2}` |
| `identify` | DALI 识别设备 | `0` - `79` | 可省略 | `{"cmd":"identify","target":1}` |
| `group` | 切换灯具的分组成员关系 | 灯具 `0` - `63` | 分组号 `0` - `15` | `{"cmd":"group","target":1,"value":3}` |
| `fadetime` | 设置 DALI fade time | `0` - `79` | `0` - `15` | `{"cmd":"fadetime","target":1,"value":4}` |
| `fastfadetime` | 设置 fast fade time | `0` - `79` | `0` - `15` | `{"cmd":"fastfadetime","target":1,"value":8}` |
| `extendfadetime` | 设置 extended fade time | `0` - `79` | `(multiplier << 4) + fade_time`，`multiplier` 为 `0` - `4`，`fade_time` 为 `0` - `15` | `{"cmd":"extendfadetime","target":1,"value":67}` |
| `faderate` | 设置 DALI fade rate | `0` - `79` | `0` - `15` | `{"cmd":"faderate","target":1,"value":7}` |
| `curve` | 设置亮度曲线 | `(start << 8) + end`，也可用 `start`/`end` | `0`=对数，`1`=线性 | `{"cmd":"curve","start":0,"end":79,"curve":1}` |

示例：

```bash
curl -X POST "http://192.168.1.50/api/v1/commands" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"cmd\":\"status\",\"target\":1,\"brightness\":180,\"color_temp\":3000}"
```

设置 RGBW：

```json
{
  "cmd": "status",
  "target": 1,
  "brightness": 200,
  "rgbwaf": "0xFF8000200000"
}
```

## 6. WebSocket

WebSocket 地址：

```text
ws://<device-ip>/ws
```

连接握手需要 Web 登录鉴权。单帧最大 1024 字节。

### 发送控制命令

WebSocket 入站格式与 MQTT 控制格式一致：

```json
{
  "device": "d2m_<mac>/01",
  "set": {
    "state": "ON",
    "brightness": 180,
    "color_temp": 3000
  }
}
```

`device` 中的最后一段为地址，支持 `00` - `79` 和部分广播命令的 `255`。`set` 对象支持的字段见 MQTT 控制章节。

### 接收消息

设备会把 MQTT 状态和 discovery 转发给所有 WebSocket 客户端，格式为：

```json
{"device":"d2m_<mac>/01","status":{"state":"ON","brightness":180}}
```

或：

```json
{"device":"d2m_<mac>/01","config":{...}}
```

如果 MQTT payload 不是 JSON，则会作为字符串发送：

```json
{"device":"d2m_<mac>","status":"online"}
```

## 7. 常用操作示例

### MQTT 打开 1 号灯并调到 70%

```bash
mosquitto_pub -h 192.168.1.10 -t "d2m_<mac>/01/set" \
  -m "{\"state\":\"ON\",\"brightness\":180}"
```

### HTTP API 执行 0 号场景

```bash
curl -X POST "http://192.168.1.50/api/v1/commands" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"cmd\":\"scene\",\"target\":1,\"value\":0}"
```

### WebSocket 控制分组 0

分组 0 的地址是 `64`：

```json
{
  "device": "d2m_<mac>/64",
  "set": {
    "state": "ON",
    "brightness": 200
  }
}
```

## 8. 网络
1. 开机时如果插上网线，则有线获取有线网络ip地址。
2. 如果30秒内没有有效ip地址，则关闭有线网络，开启wifi sta 模式，寻找ap并接入。
3. 如果30秒内仍不能接入ap，则自动开启 ap 模式，其wifi ssid 以 d2m- 开头后跟 mac 地址，如 d2m-c049ef3f40b4，密码为空 或 1234567890。
4. ap 模式启动后，如果120秒内没有设备接入，则系统重启；如果有设备接入或尝试接入，则会一直停留在ap模式。
4. wifi/ap 开启后，再插入有线电缆无效，系统不再检测有线网络。

## 9. 其他说明
1. web 初始访问用户名为 d2m，密码为 dali2mqtt, 请及时修改默认 Web 密码。
2. 鼠标悬停在命令/短地址等上面会有提示信息。
3. 网关进行扫描时，将扫描发现的设备信息同步保存到网关缓存并上传到mqtt服务器；如果有灯具未被发现，可能需要多次扫描；
4. v1.2.0版本以后开机时不再主动扫描，只在连上mqtt服务器时执行上传操作，首次使用需要人工扫描后才能在mqtt服务器上出现；
5. 由于dali系统短地址随机分配，删除后再分配的短地址会和原来不一致，建议删除功能慎用；
6. ~~分组变动后需重新扫描才能更新到homeassistant；~~
7. 给 `/api/v1/commands` 使用随机 API Token，长度建议 32 - 64 字符。不使用 HTTP API 时清空 API Token。
8. 不要把设备 HTTP 端口直接暴露到公网。
9. 如果使用以太网网关版本，配置页会显示以太网相关选项。`ethernet_only=1` 时只使用以太网，不启用 Wi-Fi/SoftAP，工程安装交付后建议使用此选项，以免停电重启后dhcp启动慢而进入wifi/softap模式。
10. 无网络情况下，可以直接使用多功能键进行操作；
11. 网关采用 esp32 模组，每个模组有4个mac地址（wifi, ap， bluetooth, ethernet)，网关配置中的mac是wifi的mac,所以当接入有线网络时，和路由器上发现的mac并不一致，mqtt命令的操作以网关配置中的mac为准；
12. 网关不提供总线电源。

