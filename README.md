# DH11 · DHT11 温湿度检测 + SSD1306 OLED 显示（STM32F103C8T6）

基于 STM32F103C8T6 的环境温湿度实时采集与 OLED 显示工程，使用 CMake 构建（STM32Cube for VS Code 工程）。

## 硬件接线

| 模块         | 引脚 | 说明                          |
| ------------ | ---- | ----------------------------- |
| DHT11 DATA   | PA1  | 单总线，4.7k–10k 上拉到 3.3V  |
| SSD1306 SCL  | PB6  | I2C1                          |
| SSD1306 SDA  | PB7  | I2C1                          |
| SSD1306      | 128x64 OLED | 中文 16x16 + ASCII 7x12 字库 |

## 功能说明

- 每 2 秒读取一次 DHT11（40bit 原始帧 + 校验和），显示温度、湿度和原始字节
- 软件实现单总线读写时序，校验和校验，分级返回错误类型（超时 / 校验错误 / 参数错误）
- 多级 OLED 界面：开屏页、数据页、错误页（含成功/失败计数）
- 中文显示：SSD1306 内置 16x16 中文字库 + 7x12 ASCII 字库

## 技术亮点

- 时钟配置：HSE 8MHz 经 PLL 倍频至 72MHz 主频
- `delay_us()`：基于 TIM2 1MHz 自由计数寄存器自写微秒级延时，16bit 回绕安全
- 资源优化：用轻量字符串拼接器（`line_builder_t`）替代 newlib `printf`，节省约 24KB Flash 与 2KB RAM
- 分层结构：外设驱动与应用逻辑分离，便于移植与复用

## 构建

使用 STM32Cube 的 VS Code 扩展 + CMake + Ninja + arm-none-eabi 工具链：

```bash
cmake --preset Debug
cmake --build build/Debug
```

生成的固件位于 `build/Debug/`，可用 ST-Link 烧录到 STM32F103C8T6。

## 目录结构

```
.
├── CMakeLists.txt          # CMake 工程定义
├── CMakePresets.json       # 构建预设 (Debug/Release)
├── stm32f103x8_flash.ld   # 链接脚本 (64K Flash / 20K RAM)
├── Src/
│   └── main.c              # 应用入口与主要逻辑
└── Inc/                    # 头文件
```

## 许可证

本项目仅供学习交流使用。
