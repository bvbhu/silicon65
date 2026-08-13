# RGB Matrix — Silicon65

使用自定义 RGB 控制器（`controller/`）取代 ZMK 内置 `rgb_underglow`，复刻 QMK RGB Matrix 功能。
监听 `&rgb_ug` 键码实现灯光控制，按下 Shift 可反向调整。

## 功能介绍

- 支持 QMK RGB Matrix 灯效体系，通过 `RGB_EFF` / `RGB_EFR` 键码循环切换
- 亮度范围 0–225，刷新间隔 33ms
- 配置状态（灯效模式、HSV、速度、开关）通过 ZMK Settings 持久化到 flash
- 空闲/断开 USB 时可自动关闭灯效（由 Kconfig 控制）

## 硬件参数

| 参数     | 值                       |
| ------ | ----------------------- |
| 主控     | nRF52840 (nrfmicro\_13) |
| LED 驱动 | `worldsemi,ws2812-spi`  |
| LED 总数 | 69                      |
| 矩阵     | 5 行 × 15 列              |
| 中心坐标   | {112, 32}               |
| 最大亮度   | 225                     |
| 刷新间隔   | 33ms                    |
| 分体键盘   | 否                       |

## 配置项

### config.h

| 宏                               | 值                             | 说明        |
| ------------------------------- | ----------------------------- | --------- |
| `MATRIX_ROWS`                   | 5                             | 矩阵行数      |
| `MATRIX_COLS`                   | 15                            | 矩阵列数      |
| `RGB_MATRIX_LED_COUNT`          | 69                            | LED 总数    |
| `RGB_MATRIX_CENTER`             | {112, 32}                     | 灯效中心坐标    |
| `RGB_MATRIX_LED_FLUSH_LIMIT`    | 33                            | 刷新间隔 (ms) |
| `RGB_MATRIX_HUE_STEP`           | 8                             | 色相步进      |
| `RGB_MATRIX_SAT_STEP`           | 16                            | 饱和度步进     |
| `RGB_MATRIX_VAL_STEP`           | 16                            | 明度步进      |
| `RGB_MATRIX_SPD_STEP`           | 16                            | 速度步进      |
| `RGB_MATRIX_MAXIMUM_BRIGHTNESS` | 225                           | 最大亮度上限    |
| `RGB_MATRIX_DEFAULT_HSV`        | 170, 255, 200                 | 默认颜色      |
| `RGB_MATRIX_DEFAULT_SPD`        | 127                           | 默认速度      |
| `RGB_MATRIX_DEFAULT_ON`         | true                          | 开机默认开启    |
| `RGB_MATRIX_DEFAULT_MODE`       | `RGB_MATRIX_CYCLE_LEFT_RIGHT` | 默认灯效      |

**启用的灯效（全部开启）：**

- `ALPHAS_MODS`、`GRADIENT_UP_DOWN`、`GRADIENT_LEFT_RIGHT`、`BREATHING`、`BAND_SAT/VAL`、`BAND_PINWHEEL_SAT/VAL`、`BAND_SPIRAL_SAT/VAL`、`CYCLE_ALL`、`CYCLE_LEFT_RIGHT`、`CYCLE_UP_DOWN`、`RAINBOW_MOVING_CHEVRON`、`CYCLE_OUT_IN`、`CYCLE_OUT_IN_DUAL`、`CYCLE_PINWHEEL`、`CYCLE_SPIRAL`、`DUAL_BEACON`、`RAINBOW_BEACON`、`RAINBOW_PINWHEELS`、`FLOWER_BLOOMING`、`RAINDROPS`、`JELLYBEAN_RAINDROPS`、`HUE_BREATHING`、`HUE_PENDULUM`、`HUE_WAVE`、`PIXEL_RAIN`、`PIXEL_FLOW`、`PIXEL_FRACTAL`、`STARLIGHT` 系列、`RIVERFLOW`
- `SOLID_REACTIVE_SIMPLE`、`SOLID_REACTIVE`、`SOLID_REACTIVE_WIDE`、`SOLID_REACTIVE_MULTIWIDE`、`SOLID_REACTIVE_CROSS`、`SOLID_REACTIVE_MULTICROSS`、`SOLID_REACTIVE_NEXUS`、`SOLID_REACTIVE_MULTINEXUS`、`SPLASH`、`MULTISPLASH`、`SOLID_SPLASH`、`SOLID_MULTISPLASH`
- `TYPING_HEATMAP`、`DIGITAL_RAIN`

### keymap.c

- `g_led_config` — LED 灯珠布局，共 69 颗 LED，格式与 QMK 完全兼容
- `rgb_matrix_indicators_advanced_user` — 未实现（已注释）

### silicon65.conf

| 配置                                       | 状态     | 说明                 |
| ---------------------------------------- | ------ | ------------------ |
| `CONFIG_ZMK_RGB_UNDERGLOW`               | n (注释) | 由自定义 RGB Matrix 替代 |
| `CONFIG_WS2812_STRIP`                    | y      | WS2812 SPI 驱动      |
| `CONFIG_SETTINGS`                        | y      | 持久化设置              |
| `CONFIG_NVS`                             | y      | NVS 存储后端           |
| `CONFIG_FLASH`                           | y      | Flash 支持           |
| `CONFIG_FLASH_PAGE_LAYOUT`               | y      | Flash 页布局          |
| `CONFIG_ZMK_HID_INDICATORS`              | y      | Caps/Num/Scroll 检测 |
| `CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE` | y      | 空闲自动关灯             |
| `CONFIG_ZMK_EXT_POWER`                   | y      | 外部电源控制             |

### silicon65.overlay

chosen 节点已添加 `zephyr,settings-storage = &storage_partition;`，引用 nrfmicro\_13 板级自带的 flash 分区。

## 文件结构

```
src/rgb_matrix/
├── config.h              # 硬件参数与灯效配置
├── keymap.c              # LED 灯珠布局
├── controller/           # RGB 控制器核心（无需修改）
│   ├── rgb_matrix.c / .h
│   ├── rgb_matrix_settings.c / .h
│   ├── rgb_matrix_types.h
│   ├── qmk_compat.c / .h
│   ├── post_config.h
│   └── lib8tion.c / .h
└── animations/           # 灯效算法（无需修改）
    ├── runners/          # 灯效运行器
    └── *.h               # 各灯效实现
```

