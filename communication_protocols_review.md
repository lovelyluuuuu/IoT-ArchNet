# 通信协议定义

## 目录

1. 下行数据协议

   1. 下发图纸信息协议

   2. 下发危险区域协议

   3. 下发路径规划协议

   4. 下发报警信息协议

   5. 下发送电信息协议

2. 上行数据协议

   1. 定位数据、状态、电量上报协议

   2. 紧急预警数据上报协议

   3. 业务通知数据上报协议

---

## 下行数据协议

### 1. 下发图纸协议

**用途**：下发建筑物、楼层及图纸信息，供设备加载和解析。

**Json 示例**：

```json
{
  "cmd": "新增|修改|删除",
  "device_id": "robot123",
  "project_id": "project456",
  "timestamp": 1736966400,
  "build_list": [
    {
      "innerid": "building001",
      "name": "写字楼 A",
      "height": 50.0,
      "floor_list": [
        {
          "innerid": "floor001",
          "name": "一层",
          "height": 3.5,
          "image": "http://example.com/floor1.png"
        }
      ]
    }
  ]
}
```

**字段说明**：

- **cmd**(字符串)：操作指令类型，取值范围为：
  - **新增**：add\_blueprint(新增图纸)。
  - **修改**：update\_blueprint(修改图纸)。
  - **删除**：delete\_blueprint(删除图纸)。
- **device\_id** (字符串)：目标设备唯一标识符。
- **project\_id** (字符串)：关联项目唯一标识符。
- **timestamp** (数字)：时间戳，表示报警信息的生成时间，精确到秒（下同）。
- **build\_list** (数组)：建筑物列表，每个建筑物包含：
  - **inner\_id** (字符串)：建筑物唯一标识符。
  - **name** (字符串)：建筑物名称。
  - **height** (数字)：建筑物高度（米）。
  - **floor\_list** (数组)：楼层列表，每个楼层包含：
    - **innerid** (字符串)：楼层唯一标识符。
    - **name** (字符串)：楼层名称。
    - **height** (数字)：楼层高度（米），保留两位小数。
    - **image** (字符串)：楼层图纸的 URL 路径。

### 2. 下发危险区域协议

**用途**：向机器人下发危险区域或电子围栏信息，指导机器人在工作环境中避开这些危险区域，确保安全。

**Json 示例**：

```json
{
  "cmd": "增加|修改|删除",
  "device_id": "robot123",
  "project_id": "project456",
  "timestamp": 1736966400,
  "danger_areas": [
    {
      "danger_zone_id": "zone_001",
      "area_type": "indoor",
      "build_id": "building001",
      "floor_id": "floor001",
      "point_list": [
        [10.0, 20.0],
        [15.0, 25.0],
        [20.0, 20.0]
      ]
    }
  ]
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，此处为新增、修改、删除危险区域。
  - **新增**：add\_danger(添加新的危险区域)。
  - **修改**：update\_danger(修改已有危险区域)。
  - **删除**：delete\_danger(删除指定的危险区域)。
- **device\_id** (字符串)：目标设备唯一标识符。
- **project\_id** (字符串)：关联项目唯一标识符。
- **timestamp**(数字)：时间戳。
- **danger\_areas** (数组)：危险区域列表，每个危险区域包含以下字段：
  - **danger\_zone\_id** (字符串)：危险区域的唯一标识符。
  - **area\_type** (字符串)：区域类型，用于区分区域位置类型，可取值为 "indoor"（室内）或 "outdoor"（户外）。
  - **build\_id** (字符串 | null)：建筑物唯一标识符。对于户外区域，此字段为 null。
  - **floor\_id** (字符串 | null)：楼层唯一标识符。对于户外区域，此字段为 null。
  - **point\_list** (数组)：定义危险区域的多边形点位列表，每个点位为 [x, y] 坐标对。

### 3. 下发路径规划协议

**用途**：向机器人下发路径规划信息，以指导机器人在室外和建筑环境中进行导航和任务执行。

**Json 示例**：

```json
{
  "cmd": "路径规划",
  "device_id": "robot123",
  "project_id": "project456",
  "timestamp": 1736966400,
  "route": [
    {
      "area_type": "outdoor",
      "build_id": null,
      "floor_id": null,
      "points": [
        [37.7749, -122.4194],
        [37.7750, -122.4195]
      ]
    },
    {
      "area_type": "indoor",
      "build_id": "building001",
      "floor_id": "floor001",
      "points": [
        [10.0, 20.0],
        [15.0, 25.0]
      ]
    }
  ]
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "planning"。
- **device\_id** (字符串)：目标设备唯一标识符。
- **project\_id** (字符串)：关联项目唯一标识符。
- **timestamp** (整型数)：时间戳，表示报警信息的生成时间。
- **route** (数组)：路径规划信息，包含以下字段：
  - **area\_type** (字符串)：区域类型，取值为 "outdoor"（室外）或 "indoor"（室内）。
  - **points** (数组)：路径点列表，每个路径点为 [x, y] 坐标对。
    - 对于室外，x 和 y 分别表示经度和纬度。
    - 对于室内，x 和 y 分别表示基于原点的相对坐标，单位为米。

**路径规划说明**：机器人需要按照给出的点的顺序进行移动。

### 4. 下发报警信息协议

**用途**：向机器人下发报警信息，以通知其紧急情况或执行特定的应对操作。

**Json 示例**：

```json
{
  "cmd": "alert",
  "device_id": "robot123",
  "project_id": "project456",
  "alert_type": "Voice Alert",
  "timestamp": 1736966400,
  "message": "电池电量低，请尽快充电。"
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "alert"（报警）。
- **device\_id** (字符串)：目标设备唯一标识符。
- **project\_id** (字符串)：关联项目唯一标识符。
- **alert\_type** (字符串)：报警类型，
  - **Light Alert**：灯光报警。
  - **Voice Alert**：语音报警。
- **timestamp** (数字)：时间戳，表示报警信息的生成时间。
- **message** (字符串)：机器人进行语音报警。



### 5. 下发充电指令协议

**用途**：向充电机器人下发充电指令，指示其移动到需要充电的目标机器人所在的位置并为其充电。

**Json 示例**：

```json
{
  "cmd": "charge",
  "device_id": "charge_robot001",
  "project_id": "project456",
  "timestamp": 1736966400,
  "route": [
    {
      "area_type": "outdoor",
      "build_id": null,
      "floor_id": null,
      "points": [
        [37.7749, -122.4194],
        [37.7750, -122.4195]
      ]
    },
    {
      "area_type": "indoor",
      "build_id": "building001",
      "floor_id": "floor001",
      "points": [
        [10.0, 20.0],
        [15.0, 25.0]
      ]
    }
  ]
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "charge"（充电）。
- **device\_id** (字符串)：充电机器人唯一标识符。
- **project\_id** (字符串)：关联项目唯一标识符。
- **timestamp** (数字)：时间戳，表示充电指令的生成时间。
- **route** (数组)：路径规划信息，包含以下字段：
  - **area\_type** (字符串)：区域类型，取值为 "outdoor" 或 "indoor"。
  - **points** (数组)：路径点列表，每个路径点为 [x, y] 坐标对。

## 上行数据协议

### 1. 定位数据、状态、电量上报协议

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

### 2. 紧急预警数据上报协议

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
- **location** (对象)：预警发生的位置，字段同定位数据上报。
- **error\_code** (字符串 | null)：错误代码，仅适用于设备故障。
- **danger\_zone\_id** (字符串 | null)：危险区域唯一标识，仅适用于禁区闯入。
- **message** (字符串)：预警的详细描述。

### 3. 业务通知数据上报协议

**用途**：设备向服务器上报业务相关的通知数据，用于业务流程的处理和跟踪。

**Json 示例**：

```json
{
  "cmd": "business_notification",
  "device_id": "robot123",
  "timestamp": 1736971200,
  "task_id": "task789",
  "workload": {
    "work_area": 100,
    "work_time": 3600
  }
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "business\_notification"（业务通知）。
- **device\_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示通知信息的生成时间（Unix 时间戳，秒为单位）。
- **task\_id** (字符串)：单次工作任务的 ID。
- **workload** (对象)：工作量数据。
  - **work\_area** (浮点数)：单次工作面积，单位为平方米。
  - **work\_time** (整数)：单次工作时长，单位为秒。

