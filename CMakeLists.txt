# Silicon65 键盘仓 CMakeLists.txt
# keymap.c（定义 g_led_config）由 rgb-matrix 模块通过 GLOB 注入 zephyr 目标编译。
# 此处仅加入 config 目录到 include 路径，供 keymap.c 使用。

zephyr_include_directories(${CMAKE_CURRENT_LIST_DIR}/config)