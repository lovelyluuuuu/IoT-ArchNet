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


