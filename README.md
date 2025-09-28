# ESP32-S3 USB Webcam

一个基于ESP32-S3的USB摄像头项目，将ESP32-S3开发板转换为标准的USB UVC（USB Video Class）设备，可以直接作为网络摄像头使用。

## 项目简介

本项目实现了一个完整的USB摄像头功能，支持MJPEG视频流传输。通过USB连接到电脑后，可以在任何支持UVC标准的应用程序中使用，如：

- 视频会议软件（Zoom、Teams、Skype等）
- 直播软件（OBS、XSplit等）
- 浏览器网页摄像头应用

## 硬件要求

### 支持的开发板

- ESP32-S3
- 其他兼容ESP32-S3的摄像头开发板

### 摄像头模块

- OV2640
- OV3660
- 其他兼容的DCMI接口摄像头模块

## 功能特性

- ✅ USB UVC标准兼容
- ✅ MJPEG视频编码
- ✅ 多种分辨率支持
- ✅ 可配置帧率
- ✅ 即插即用，无需驱动程序
- ✅ 低延迟视频传输
- ✅ 自动曝光和白平衡

## 快速开始

### 环境准备

1. 安装ESP-IDF开发框架：

   ```bash
   # 安装ESP-IDF v5.0+
   git clone --recursive https://github.com/espressif/esp-idf.git
   cd esp-idf
   ./install.sh
   . ./export.sh
   ```

2. 克隆项目代码：

   ```bash
   git clone <your-repo-url>
   cd usb_webcam
   ```

### 编译和烧录

1. 设置目标芯片：

   ```bash
   idf.py set-target esp32s3
   ```

2. 配置项目（可选）：

   ```bash
   idf.py menuconfig
   ```

   主要配置选项：
   - Camera Module: 选择您的摄像头模块类型
   - XCLK Frequency: 摄像头时钟频率（通常20MHz）
   - Frame Size: 默认分辨率设置

3. 编译项目：

   ```bash
   idf.py build
   ```

4. 烧录到设备：

   ```bash
   idf.py flash monitor
   ```

### 使用方法

1. 将ESP32-S3通过USB线连接到电脑
2. 设备会自动被识别为USB摄像头
3. 打开任意支持摄像头的应用程序即可使用

## 项目配置

### 摄像头配置

在 `main/Kconfig.projbuild` 中可以配置：

- **Camera Module**: 选择适配的摄像头模块
- **XCLK Frequency**: 摄像头工作频率
- **Multi Frame Size**: 是否启用多分辨率支持

### 支持的分辨率和帧率

| 分辨率 | 帧率 | 说明 |
|--------|------|------|
| 640x480 | 15fps | 默认分辨率 |
| 320x240 | 30fps | 低分辨率高帧率 |
| 800x600 | 10fps | 高分辨率（需要足够的内存） |

> 具体支持的分辨率取决于摄像头模块和配置

## 项目结构

```text
usb_webcam/
├── CMakeLists.txt              # 主CMake配置文件
├── partitions.csv              # 分区表配置
├── sdkconfig.defaults          # 默认SDK配置
├── main/
│   ├── usb_webcam_main.c      # 主程序文件
│   ├── camera_pin.h           # 摄像头引脚定义
│   ├── CMakeLists.txt         # 主组件CMake配置
│   ├── idf_component.yml      # 组件依赖配置
│   └── Kconfig.projbuild      # 项目配置选项
├── bootloader_components/      # 启动加载器组件
└── managed_components/         # 外部组件依赖
    ├── espressif__esp32-camera/
    └── espressif__tinyusb/
```

## 依赖组件

- **esp32-camera**: ESP32摄像头驱动库
- **tinyusb**: TinyUSB协议栈，提供USB设备支持
- **cmake_utilities**: CMake工具集

## 故障排除

### 常见问题

1. **设备未被识别为摄像头**
   - 检查USB连接是否正常
   - 确认使用支持数据传输的USB线
   - 检查设备管理器中是否出现未知设备

2. **图像质量问题**
   - 调整JPEG质量设置
   - 检查摄像头焦距是否正确
   - 确认光线条件是否充足

3. **编译错误**
   - 确认ESP-IDF版本兼容性（推荐v5.0+）
   - 检查组件依赖是否正确下载
   - 清理构建缓存后重新编译：`idf.py fullclean`

### 调试信息

启用详细日志输出：

```bash
idf.py menuconfig
# Component config -> Log output -> Default log verbosity -> Debug
```

查看运行日志：

```bash
idf.py monitor
```

## 开发者指南

### 自定义摄像头参数

在 `camera_init()` 函数中可以调整：

- 亮度、对比度、饱和度
- 垂直/水平翻转
- 自动增益控制
- 曝光补偿

### 添加新的分辨率

1. 修改 `uvc_frame_config.h` 文件
2. 添加新的帧格式配置
3. 更新 `UVC_FRAMES_INFO` 数组

### 性能优化

- 使用PSRAM存储帧缓冲区
- 启用DMA传输
- 优化JPEG压缩质量
- 合理设置帧缓冲区数量

## 许可证

本项目基于Apache 2.0许可证开源。详见[LICENSE](LICENSE)文件。

## 贡献

欢迎提交Issue和Pull Request！

### 开发环境

- ESP-IDF v5.0+
- CMake 3.5+
- 支持ESP32-S3的开发环境

## 相关链接

- [ESP-IDF官方文档](https://docs.espressif.com/projects/esp-idf/)
- [ESP32-Camera组件](https://github.com/espressif/esp32-camera)
- [TinyUSB文档](https://docs.tinyusb.org/)
- [USB UVC规范](https://www.usb.org/documents)

## 更新日志

### v1.0.0

- 初始版本发布
- 支持基础USB摄像头功能
- 兼容ESP32-S3-EYE开发板
- 支持MJPEG视频流

---

如有任何问题或建议，请通过Issue反馈！
