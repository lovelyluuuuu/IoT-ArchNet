# 通信协议定义

## 目录

1. 下行数据协议（服务器到设备）

   1. 下发图纸信息协议

   2. 下发危险区域协议

   3. 下发路径规划协议

   4. 下发报警信息协议

   5. 下发送电信息协议

   6. 相应注册协议

2. 上行数据协议（设备到服务器）

   1. 设备注册协议
   2. 定位数据、状态、电量上报协议
   3. 紧急预警数据上报协议
   4. 业务通知数据上报协议

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

  - **新增**：add\_blueprint(新增图纸)。

  - **修改**：update\_blueprint(修改图纸)。

  - **删除**：delete\_blueprint(删除图纸)。

- **device\_id** (字符串)：目标设备唯一标识符。

- **project\_id** (字符串)：关联项目唯一标识符。

- **timestamp** (数字)：时间戳，表示报警信息的生成时间。

- **build\_list** (数组)：建筑物列表，每个建筑物包含：

  - **inner\_id** (字符串)：建筑物唯一标识符。

  - **name** (字符串)：建筑物名称。

  - **height** (数字)：建筑物高度（米）。

  - **floor\_list** (数组)：楼层列表，每个楼层包含：

    - **innerid** (字符串)：楼层唯一标识符。

    - **name** (字符串)：楼层名称。

    - **height** (数字)：楼层高度（米），保留两位小数。

    - **image** (字符串)：楼层图纸的 URL 路径。

    - **image\_base64** (字符串)：图纸的 Base64 编码（可选）。



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
        [15.0, 25.0]
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
        [150.0, 250.0]
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

  - **level** (字符串)：危险等级（例如 "high"、"medium"、"low"）。

  - **build\_id** (字符串 | null)：建筑物唯一标识符。对于户外区域，此字段为 null。

  - **floor\_id** (字符串 | null)：楼层唯一标识符。对于户外区域，此字段为 null。

  - **point\_list** (数组)：定义危险区域的多边形点位列表，每个点位为 [x, y] 坐标对。

    - **x** (数字)：X 坐标，单位为米（室内）或经度（室外）。
    - **y** (数字)：Y 坐标，单位为米（室内）或纬度（室外）。

### 3. 下发路径规划协议

**用途**：向机器人下发路径规划信息，以指导机器人在室外和建筑环境中进行导航和任务执行。要求机器人按照给出的点的顺序进行移动。

后期加电梯规划信息

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

  - **area\_type** (字符串)：区域类型，取值为 "indoor"（室内）或 "outdoor"（室外）。

  - **build\_id** (字符串 | null)：建筑物唯一标识符，室外为空。

  - **floor\_id** (字符串 | null)：楼层唯一标识符，室外为空。

  - **points** (数组)：路径点列表，每个路径点包含两个数值，分别代表：

    - 室内：X、Y 坐标，单位为米。
    - 室外：经度、纬度。

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

- **message** (字符串)：机器人进行语音报警。鸣笛则为 null。

  - **Voice Alert**：若为语音报警，为预定义语音文本库，例如："电池电量低，请尽快充电。"
  - **Light Alert**：若为灯光报警，此字段应设置为 null。

- **待解决的问题**：哪些需要触发报警，什么时候触发报警，需明确业务逻辑。

### 5. 下发送电信息协议

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

- **timestamp** (数字)：时间戳，表示充电指令的生成时间（Unix 时间戳，秒为单位）。

- **route** (数组)：路径规划信息，包含以下字段：

  - **area\_type** (字符串)：区域类型，取值为 "indoor"（室内）或 "outdoor"（室外）。

  - **build\_id** (字符串 | null)：建筑物唯一标识符，室外为空。

  - **floor\_id** (字符串 | null)：楼层唯一标识符，室外为空。

  - **points** (数组)：路径点列表，每个路径点包含两个数值，分别代表：

    - 室内：X、Y 坐标，单位为米。
    - 室外：经度、纬度。

### 6. 响应注册协议

**用途**：当设备注册请求被接受后，服务器会下发其公钥以供设备使用：

```json
{
  "cmd": "server_public_key",
  "device_id": "robot123",
  "server_public_key": "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArwL1...",
  "timestamp": 1736966500
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "server\_public\_key"（服务器公钥下发）。

- **device\_id** (字符串)：目标设备唯一标识符。

- **server\_public\_key** (字符串)：服务器的公钥，用于设备后续上行数据加密。

- **timestamp** (数字)：时间戳，表示公钥下发时间（Unix 时间戳，秒为单位）。

## 上行数据协议

### 1. 设备注册协议

**用途**：设备启动后向服务器注册，告知服务器其基本信息和当前状态。

**Json 示例**：

```json
{
  "cmd": "register",
  "device_id": "robot123",
  "mac_address": "00:1A:2B:3C:4D:5E",
  "ip_address": "192.168.1.100",
  "public_key": "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArwL0...",
  "location": {
    "type": "outdoor",
    "latitude": 37.7749,
    "longitude": -122.4194,
    "build_id": null,
    "floor_id": null,
    "x": null,
    "y": null
  },
  "timestamp": 1736966400
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "register"（注册）。
- **device\_id** (字符串)：设备唯一标识符。
- **mac\_address** (字符串)：设备的 MAC 地址。
- **ip\_address** (字符串)：设备的 IP 地址。
- **public\_key** (字符串)：设备的公钥，用于后续加密通信。
- **location** (对象)：设备位置，包含以下字段：
  - **type** (字符串)：区域类型，取值为 "indoor"（室内）或 "outdoor"（室外）。
  - **latitude** (浮点数 | null)：纬度，仅适用于室外。
  - **longitude** (浮点数 | null)：经度，仅适用于室外。
  - **build\_id** (字符串 | null)：建筑物唯一标识符（仅适用于室内）。
  - **floor\_id** (字符串 | null)：楼层唯一标识符（仅适用于室内）。
  - **x** (浮点数 | null)：X 坐标，仅适用于室内，单位：米。
  - **y** (浮点数 | null)：Y 坐标，仅适用于室内，单位：米。
- **timestamp** (数字)：时间戳，表示注册信息的生成时间（Unix 时间戳，秒为单位）。

### 2. 定位数据、状态、电量上报协议

**用途**：设备定期向服务器上报其当前定位数据、运行状态和电量信息，帮助服务器监控设备运行情况。

**Json 示例**：

```json
// 户外定位
{
  "cmd": "status_report",
  "device_id": "robot123",
  "timestamp": 1736968500,
  "location": {
    "type": "outdoor",
    "latitude": 37.7749,
    "longitude": -122.4194,
    "build_id": null,
    "floor_id": null,
    "x": null,
    "y": null
  },
  "status": 1,
  "battery": {
    "energy": 85,
    "voltage": 24.5,
    "current": 1.2
  }
}
// 户内定位
{
  "cmd": "status_report",
  "device_id": "robot123",
  "timestamp": 1736968500,
  "location": {
    "type": "indoor",
    "latitude": null,
    "longitude": null,
    "build_id": "building_01",
    "floor_id": "floor_02",
    "x": 12.5,
    "y": 8.3
  },
  "status": 3,
  "battery": {
    "energy": 85,
    "voltage": 24.5,
    "current": 1.2
  }
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status\_report"（定位、状态、电量上报）。

- **device\_id** (字符串)：设备唯一标识符。

- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，秒为单位）。

- **status** (整数型)：设备当前状态，取值为：

  - **0** - 离线
  - **1** - 故障
  - **2** - 在线（工作）
  - **3** - 在线（闲置）

- **定位字段** (location)：设备的定位信息，包含以下字段：

  - **type** (字符串)：定位类型，取值为 "outdoor" 或 "indoor"。
  - 户外定位字段（type: outdoor）：
    - **latitude** (浮点数)：纬度，单位：度。
    - **longitude** (浮点数)：经度，单位：度。
  - 室内定位字段（type: indoor）：
    - **build\_id** (字符串)：建筑物唯一标识。
    - **floor\_id** (字符串)：楼层标识。
    - **x**、**y** (浮点数)：室内坐标，单位：米。

- **电量字段** (battery)：电池信息，包含以下字段：

  - **energy** (整数)：电池电量百分比，取值范围：0 \~ 100。
  - **voltage** (浮点数)：电池电压，单位：伏特，取值范围：0 \~ 1000。
  - **current** (浮点数)：电池电流，单位：安培，取值范围：0 \~ 1000。

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

- **alert\_type** (字符串)：预警类型

  - **battery\_low**：电量低于 20%。
  - **device\_fault**：设备故障。
  - **intrusion\_detected**：闯入禁区。

- **severity** (字符串)：预警严重程度，例如 "low"（低）、"medium"（中）、"high"（高）。

- **location** (对象)：预警发生的位置，同状态定位 **location** 数据。

- **error\_code** (字符串 | null)：故障码（仅在设备故障时上报）。

- **danger\_zone\_id** (字符串 | null)：危险区域的唯一标识符（仅在闯入禁区时上报）。

- **message** (字符串)：预警的详细描述。

### 4. 业务通知数据上报协议

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

- **workload** (对象)：工作量信息，包含以下字段：

  - **work\_area** (浮点数)：单次工作面积，单位：平方米，取值范围：0 \~ 10000。
  - **work\_time** (整数)：单次工作时长，单位：秒，取值范围：0 \~ 43200。

