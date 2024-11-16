# 通信协议定义

## 目录
- 下行数据协议
  1. 下发图纸协议
  2. 下发危险区域

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
  - **新增**：新增图纸。
  - **修改**：修改图纸。
  - **删除**：删除图纸。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
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
  - **新增**：添加新的危险区域。
  - **修改**：修改已有危险区域。
  - **删除**：删除指定的危险区域。
- **device_id** (字符串)：目标设备唯一标识符。
- **project_id** (字符串)：关联项目唯一标识符。
- **danger_areas** (数组)：危险区域列表，每个危险区域包含以下字段：
  - **danger_zone_id** (字符串)：危险区域的唯一标识符。
  - **area_type** (字符串)：区域类型，用于区分区域位置类型，可取值为 "indoor"（室内）或 "outdoor"（户外）。
  - **level** (字符串)：危险等级（例如 "high"、"medium"、"low"）。
  - **build_id** (字符串 | null)：建筑物唯一标识符。对于户外区域，此字段为 null。
  - **floor_id** (字符串 | null)：楼层唯一标识符。对于户外区域，此字段为 null。
  - **point_list** (数组)：定义危险区域的多边形点位列表，每个点位为 [x, y] 坐标对。
    - **x** (数字)：区域边界点的 X 坐标。
    - **y** (数字)：区域边界点的 Y 坐标。


