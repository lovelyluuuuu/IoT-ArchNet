# 通信协议定义

## 目录
- 下行数据协议（服务器到设备）
  1. 下发图纸协议

---

## 下行数据协议（服务器到设备）

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


