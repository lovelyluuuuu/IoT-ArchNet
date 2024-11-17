# 通信协议定义

## 目录
- 下行数据协议
  1. 下发图纸信息
  2. 下发危险区域
  3. 下发路径规划
  4. 下发报警信息
  5. 下发送电信息

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
          "image": "http://example.com/floor1.png",
          "image_base64": null
        }
      ]
    }
  ]
}
```

**字段说明**：
- **cmd**(字符串)：操作指令类型，取值范围为：
  - **新增**：add_blueprint(新增图纸)。
  - **修改**：update_blueprint(修改图纸)。
  - **删除**：delete_blueprint(删除图纸)。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **timestamp** (数字)：时间戳，表示报警信息的生成时间。
- **build_list** (数组)：建筑物列表，每个建筑物包含：
  - **inner_id** (字符串)：建筑物唯一标识符。
  - **name** (字符串)：建筑物名称。
  - **height** (数字)：建筑物高度（米）。
  - **floor_list** (数组)：楼层列表，每个楼层包含：
    - **innerid** (字符串)：楼层唯一标识符。
    - **name** (字符串)：楼层名称。
    - **height** (数字)：楼层高度（米），保留两位小数。
    - **image** (字符串)：楼层图纸的 URL 路径。
    - **image_base64** (字符串)：图纸的 Base64 编码（可选）。



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
      "level": "high",
      "build_id": "building001",
      "floor_id": "floor001",
      "point_list": [
        [10.0, 20.0],
        [15.0, 25.0],
        [20.0, 20.0]
      ]
    },
    {
      "danger_zone_id": "zone_002",
      "area_type": "outdoor",
      "level": "medium",
      "build_id": null,
      "floor_id": null,
      "point_list": [
        [100.0, 200.0],
        [150.0, 250.0],
        [200.0, 200.0]
      ]
    }
  ]
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，此处为新增、修改、删除危险区域。
  - **新增**：add_danger(添加新的危险区域)。
  - **修改**：update_danger(修改已有危险区域)。
  - **删除**：delete_danger(删除指定的危险区域)。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **timestamp**(数字)：时间戳
- **danger_areas** (数组)：危险区域列表，每个危险区域包含以下字段：
  - **danger_zone_id** (字符串)：危险区域的唯一标识符。
  - **area_type** (字符串)：区域类型，用于区分区域位置类型，可取值为 "indoor"（室内）或 "outdoor"（户外）。
  - **level** (字符串)：危险等级（例如 "high"、"medium"、"low"）。
  - **build_id** (字符串 | null)：建筑物唯一标识符。对于户外区域，此字段为 null。
  - **floor_id** (字符串 | null)：楼层唯一标识符。对于户外区域，此字段为 null。
  - **point_list** (数组)：定义危险区域的多边形点位列表，每个点位为 [x, y] 坐标对。
    - **x** (数字)：区域边界点的 X 坐标，单位：米。
    - **y** (数字)：区域边界点的 Y 坐标，单位：米。




### 3. 下发路径规划协议

**用途**：向机器人下发路径规划信息，以指导机器人在室外和建筑环境中进行导航和任务执行。

**Json 示例**：

```json
{
  "cmd": "路径规划",
  "device_id": "robot123",
  "project_id": "project456",
  "timestamp": 1736966400,
  "outdoor_route": [
    {
      "area_type": "outdoor",
      "points": [
        { "x": 100.0, "y": 200.0, "z": 0.0 },
        { "x": 150.0, "y": 250.0, "z": 0.0 }
      ]
    }
  ],
  "indoor_route": [
    {
      "area_type": "indoor",
      "build_id": "building001",
      "floor_id": "floor001",
      "points": [
        { "x": 10.0, "y": 20.0, "z": 0.0 },
        { "x": 15.0, "y": 25.0, "z": 0.0 }
      ]
    }
  ]
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "planning"。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **timestamp** (数字)：时间戳，表示报警信息的生成时间。
- **outdoor_route** (数组)：室外路径规划信息，包含以下字段：
  - **area_type** (字符串)：区域类型，取值为 "outdoor"（室外）。
  - **points** (数组)：路径点列表，每个路径点包含：
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。
- **indoor_route** (数组)：室内路径规划信息，包含以下字段：
  - **area_type** (字符串)：区域类型，取值为 "indoor"（室内）。
  - **build_id** (字符串)：建筑物唯一标识符。
  - **floor_id** (字符串)：楼层唯一标识符。
  - **points** (数组)：路径点列表，每个路径点包含：
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。

是否有z轴数据？






### 4. 下发报警信息协议

**用途**：向机器人下发报警信息，以通知其紧急情况或执行特定的应对操作。

**Json 示例**：
```json
{
  "cmd": "alert",
  "device_id": "robot123",
  "project_id": "project456",
  "alert_type": "battery_low",
  "timestamp": 1736966400,
  "location": {
    "x": 10.0,
    "y": 20.0,
    "z": 0.0
  },
  "message": "电池电量低，请尽快充电。"
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "alert"（报警）。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **alert_type** (字符串)：报警类型，例如 "battery_low"、"obstacle_detected" 等。
- **timestamp** (数字)：时间戳，表示报警信息的生成时间。
- **location** (对象)：报警发生的位置，包含以下字段：
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。
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
  "outdoor_route": [
    {
      "area_type": "outdoor",
      "points": [
        { "x": 100.0, "y": 200.0, "z": 0.0 },
        { "x": 150.0, "y": 250.0, "z": 0.0 }
      ]
    }
  ],
  "indoor_route": [
    {
      "area_type": "indoor",
      "build_id": "building001",
      "floor_id": "floor001",
      "points": [
        { "x": 10.0, "y": 20.0, "z": 0.0 },
        { "x": 15.0, "y": 25.0, "z": 0.0 }
      ]
    }
  ]
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "charge"（充电）。
- **device_id** (字符串)：充电机器人唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **timestamp** (数字)：时间戳，表示充电指令的生成时间（Unix 时间戳，秒为单位）。
- **outdoor_route** (数组)：室外路径规划信息，包含以下字段：
  - **area_type** (字符串)：区域类型，取值为 "outdoor"（室外）。
  - **points** (数组)：路径点列表，每个路径点包含：
    - **x** (数字)：X 坐标，单位：米。
    - **y** (数字)：Y 坐标，单位：米。
    - **z** (数字)：Z 坐标（高度），单位：米。
- **indoor_route** (数组)：室内路径规划信息，包含以下字段：
  - **area_type** (字符串)：区域类型，取值为 "indoor"（室内）。
  - **build_id** (字符串)：建筑物唯一标识符。
  - **floor_id** (字符串)：楼层唯一标识符。
  - **points** (数组)：路径点列表，每个路径点包含：
    - **x** (数字)：X 坐标，单位：米。
    - **y** (数字)：Y 坐标，单位：米。
    - **z** (数字)：Z 坐标（高度），单位：米。


# 上行数据协议定义

## 目录

- 上行数据协议（设备到服务器）
  1. 设备注册协议
  2. 定位数据、状态、电量上报协议
  3. 紧急预警数据上报协议
  4. 业务通知数据上报协议

---

## 上行数据协议（设备到服务器）

### 1. 设备注册协议

**用途**：设备启动后向服务器注册，告知服务器其基本信息和当前状态。

**Json 示例**：
```json
{
  "cmd": "register",
  "device_id": "robot123",
  "mac_address": "00:1A:2B:3C:4D:5E",
  "firmware_version": "1.0.3",
  "ip_address": "192.168.1.100",
  "public_key": "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArwL0...",
  "location": {
    "area_type": "indoor",
    "build_id": "building001",
    "floor_id": "floor001",
    "x": 15.0,
    "y": 30.0,
    "z": 0.0
  },
  "timestamp": 1736966400
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "register"（注册）。
- **device_id** (字符串)：设备唯一标识符。
- **mac_address** (字符串)：设备的 MAC 地址。
- **firmware_version** (字符串)：设备固件版本。
- **ip_address** (字符串)：设备的 IP 地址。
- **public_key** (字符串)：设备的公钥，用于后续加密通信。
- **location** (对象)：设备位置，包含以下字段：
  - **area_type** (字符串)：区域类型，取值为 "indoor"（室内）或 "outdoor"（室外）。
  - **build_id** (字符串)：建筑物唯一标识符（仅适用于室内）。
  - **floor_id** (字符串)：楼层唯一标识符（仅适用于室内）。
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。
- **timestamp** (数字)：时间戳，表示注册信息的生成时间（Unix 时间戳，秒为单位）。

### 2. 定位数据、状态、电量上报协议

**用途**：设备定期向服务器上报其当前定位数据、运行状态和电量信息，帮助服务器监控设备运行情况。

**Json 示例**：
```json
{
  "cmd": "position_status_report",
  "device_id": "robot123",
  "timestamp": 1736968500,
  "location": {
    "x": 15.0,
    "y": 30.0,
    "z": 0.0
  },
  "status": "idle",
  "battery_level": 85
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "position_status_report"（定位、状态、电量上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，秒为单位）。
- **location** (对象)：设备当前位置，包含以下字段：
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。
- **status** (字符串)：设备当前状态，例如 "idle"（空闲）、"busy"（忙碌）等。
- **battery_level** (数字)：设备当前电池电量百分比。

### 3. 紧急预警数据上报协议

**用途**：当设备检测到紧急情况时，向服务器上报预警数据，以便及时处理。

**Json 示例**：
```json
{
  "cmd": "emergency_alert",
  "device_id": "robot123",
  "timestamp": 1736970000,
  "alert_type": "obstacle_detected",
  "severity": "high",
  "location": {
    "x": 20.0,
    "y": 40.0,
    "z": 0.0
  }
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "emergency_alert"（紧急预警）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示预警信息的生成时间（Unix 时间戳，秒为单位）。
- **alert_type** (字符串)：预警类型，例如 "obstacle_detected"（检测到障碍物）。
- **severity** (字符串)：预警严重程度，例如 "low"（低）、"medium"（中）、"high"（高）。
- **location** (对象)：预警发生的位置，包含以下字段：
  - **x** (数字)：X 坐标，单位：米。
  - **y** (数字)：Y 坐标，单位：米。
  - **z** (数字)：Z 坐标（高度），单位：米。

### 4. 业务通知数据上报协议

**用途**：设备向服务器上报业务相关的通知数据，用于业务流程的处理和跟踪。

**Json 示例**：
```json
{
  "cmd": "business_notification",
  "device_id": "robot123",
  "timestamp": 1736971200,
  "notification_type": "task_completed",
  "task_id": "task789",
  "details": "任务已完成，返回基站。"
}
```

**字段说明**：
- **cmd** (字符串)：指令类型，取值为 "business_notification"（业务通知）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示通知信息的生成时间（Unix 时间戳，秒为单位）。
- **notification_type** (字符串)：通知类型，例如 "task_completed"（任务完成）。
- **task_id** (字符串)：相关任务的唯一标识符。
- **details** (字符串)：通知的详细描述。
