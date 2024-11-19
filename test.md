### 2. 定位数据、状态、电量上报协议

**用途**：设备定期向服务器上报其当前定位数据、运行状态和电量信息，帮助服务器监控设备运行情况。

**Json 示例**：

```json
{
  "cmd": "status_report",
  "device_id": "robot123",
  "timestamp": 1736968500,
  "location": {
    "type": "indoor",
    "latitude": null,
    "longitude": null,
    "area_id": 3,
    "building_info": 2,
    "floor_info": 4321,
    "x": 12.5,
    "y": 8.3,
    "z": 0,
    "satellite_count": null,
    "hdop": 0.8,
    "gnss_speed": 1.2,
    "barometer": -9999,
    "beacons": [
      {
        "major": 1234,
        "minor": 5678,
        "rssi": -70,
        "distance": 2.5
      }
    ],
    "anchors": [
      {
        "major": 4321,
        "minor": 8765,
        "distance": 3.2,
        "azimuth": 45.0,
        "elevation": 15.0
      }
    ]
  },
  "status": 3,
  "battery": {
    "energy": 85,
    "voltage": 24.5,
    "current": 1.2
  },
  "nearby_people": [
    {
      "id": "person_001",
      "distance": 3.0
    }
  ],
  "nearby_robots": [
    {
      "device_id": "robot789",
      "signal_strength": -65
    }
  ]
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status_report"（状态上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，秒为单位）。
- **location** (对象)：设备位置数据。
  - **type** (字符串)：定位类型，取值为 "outdoor" 或 "indoor"。
  - **latitude** (浮点数 | null)：纬度，仅适用于室外。
  - **longitude** (浮点数 | null)：经度，仅适用于室外。
  - **area_id** (16进制整数)：工作面编号。
  - **building_info** (整数 | null)：楼栋信息。
  - **floor_info** (整数 | null)：楼层信息。
  - **x**、**y**、**z** (浮点数)：室内坐标，单位为米，仅适用于室内。
  - **satellite_count** (整数 | null)：可见卫星数量，仅适用于室外。
  - **hdop** (浮点数)：定位精度，仅适用于室外。
  - **gnss_speed** (浮点数)：定位速度，单位为米/秒，仅适用于室外。
  - **barometer** (整数):高度，卫星定位获得的相对参考地面的高度，无效用小于-1000的值表示典型值为-9999。
  - **beacons** (数组)：信标数据。
    - **major** (16进制整数)：信标的 major 标识符。
    - **minor** (整数)：信标的 minor 标识符。
    - **rssi** (整数)：信号强度，单位为 dBm。
    - **distance** (浮点数)：到信标的距离，单位为米。
  - **anchors** (数组)：锚点数据。
    - **major** (整数)：锚点的 major 标识符。
    - **minor** (整数)：锚点的 minor 标识符。
    - **distance** (浮点数)：到锚点的距离，单位为米。
    - **azimuth** (浮点数)：与锚点的方位角，单位为度。
    - **elevation** (浮点数)：与锚点的仰角，单位为度。
- **status** (整数型)：设备当前状态。
  - **0** - 离线
  - **1** - 故障
  - **2** - 在线（工作）
  - **3** - 在线（闲置）
- **battery** (对象)：电量数据。
  - **energy** (整数)：电池电量百分比，取值范围为 0 ~ 100。
  - **voltage** (浮点数)：电池电压，单位为伏特。
  - **current** (浮点数)：电池电流，单位为安培。
- **nearby_people** (数组)：周边人员信息。
  - **id** (字符串)：人员唯一标识。
  - **distance** (浮点数)：与设备之间的距离，单位为米。
- **nearby_robots** (数组)：周边机器人信息。
  - **device_id** (字符串)：机器人唯一标识。
  - **signal_strength** (整数)：蓝牙信号强度，单位为 dBm。





### 3. 紧急预警数据上报协议

**用途**：当设备检测到紧急情况时，向服务器上报预警数据，以便及时处理。

**Json 示例**：

```json
{
  "cmd": "emergency_alert",
  "device_id": "robot123",
  "timestamp": 1736970000,
  "alert_type": "battery_low",
  "severity": "high",
  "location": {
    "type": "outdoor",
    "latitude": 37.7749,
    "longitude": -122.4194,
    "build_id": null,
    "floor_id": null,
    "x": null,
    "y": null                                  
  },
  "error_code": null,
  "danger_zone_id": null,
  "message": "电量低于20%"
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "emergency\_alert"（紧急预警）。
- **device\_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示预警信息的生成时间（Unix 时间戳，秒为单位）。
- **alert\_type** (字符串)：预警类型。
  - **battery\_low**：电池电量低。
  - **equipment\_fault**：设备故障。
  - **intrusion\_detected**：闯入禁区。
  - **fall_protection**：防跌落
  - **proximity_alert**：人员距离设备过近
- **severity** (字符串)：预警严重程度，例如 "low"（低）、"medium"（中）、"high"（高）。
- **location** (对象)：预警发生的位置。
- **error\_code** (字符串 | null)：错误代码，仅适用于设备故障。
- **danger\_zone\_id** (字符串 | null)：危险区域唯一标识，仅适用于禁区闯入。
- **message** (字符串)：预警的详细描述。