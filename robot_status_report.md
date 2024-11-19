# 各机器人状态信息上报协议

## 1. 激光地面整平机状态上报协议

**用途**：激光地面整平机定期向服务器上报其运行状态及其他关键信息，便于服务器进行设备监控和管理。

**Json 示例**：

```json
{
  "cmd": "status_report",
  "device_id": "laser_leveler_001",
  "timestamp": 1736968500,
  "laser_r": 50.0,
  "laser_l": 45.0,
  "speed": 10.0,
  "inclinometer": 5.0
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status_report"（状态上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，精确到秒）。
- **laser_r** (浮点数)：右激光位置，取值范围为 -100 ~ 100。
- **laser_l** (浮点数)：左激光位置，取值范围为 -100 ~ 100。
- **speed** (浮点数)：运行速度，单位为米/秒。
- **inclinometer** (浮点数)：倾角仪读数，取值范围为 -1000 ~ 1000。


## 2. 履带抹平机状态上报协议

**用途**：履带抹平机定期向服务器上报其运行状态及关键信息，以便进行设备监控。

**Json 示例**：

```json
{
  "cmd": "status_report",
  "device_id": "tracked_screed_001",
  "timestamp": 1736968500,
  "swing_angle": 30.0,
  "plate_speed": 500.0,
  "speed": 8.0
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status_report"（状态上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，精确到秒）。
- **swing_angle** (浮点数)：摆臂角度，取值范围为 -180 ~ 180。
- **plate_speed** (浮点数)：抹盘转速，单位为 RPM，取值范围为 0 ~ 1000。
- **speed** (浮点数)：运行速度，单位为米/秒。


## 3. 四盘地面抹光机状态上报协议

**用途**：四盘地面抹光机定期向服务器上报其运行状态及其他关键信息，以便设备的监控和管理。

**Json 示例**：

```json
{
  "cmd": "status_report",
  "device_id": "trowel_001",
  "timestamp": 1736968500,
  "spatula_angle_y": 20.0,
  "spatula_angle_x": -15.0,
  "spatula_speed_1": 800.0,
  "spatula_speed_2": 750.0
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status_report"（状态上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，精确到秒）。
- **spatula_angle_y** (浮点数)：抹刀 Y 轴角度变化，取值范围为 -180 ~ 180。
- **spatula_angle_x** (浮点数)：抹刀 X 轴角度变化，取值范围为 -180 ~ 180。
- **spatula_speed_1** (浮点数)：1 号抹盘转速，单位为 RPM，取值范围为 0 ~ 1000。
- **spatula_speed_2** (浮点数)：2 号磨盘转速，单位为 RPM，取值范围为 0 ~ 1000。


## 4. 喷涂打磨机器人状态上报协议

**用途**：喷涂打磨机器人定期向服务器上报其运行状态及其他关键信息，以便设备的监控和管理。

**Json 示例**：

```json
{
  "cmd": "status_report",
  "device_id": "spray_grinder_001",
  "timestamp": 1736968500,
  "dust_weight": 25.0,
  "remain_materials": 60.0,
  "op_object": 1,
  "speed": 5.0,
  "gyroscope": 180.0
}
```

**字段说明**：

- **cmd** (字符串)：指令类型，取值为 "status_report"（状态上报）。
- **device_id** (字符串)：设备唯一标识符。
- **timestamp** (数字)：时间戳，表示上报信息的生成时间（Unix 时间戳，精确到秒）。
- **dust_weight** (浮点数)：粉尘重量，取值范围为 0 ~ 100。
- **remain_materials** (浮点数)：剩余物料量，取值范围为 0 ~ 100。
- **op_object** (枚举型)：操作对象，取值范围：
  - 0 - 无
  - 1 - 墙面
  - 2 - 天花板
  - 3 - 门
  - 4 - 窗
  - 5 - 飘窗
  - 6 - 过道
- **speed** (浮点数)：运行速度，单位为米/秒。
- **gyroscope** (浮点数)：陀螺仪读数，取值范围为 0 ~ 360。

