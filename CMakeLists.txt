# Silicon65 键盘仓 CMakeLists.txt
# 注入 LED 布局 keymap.c（定义 g_led_config）并加入 config 目录到 include 路径。

zephyr_include_directories(${CMAKE_CURRENT_LIST_DIR}/config)
target_sources(app PRIVATE ${CMAKE_CURRENT_LIST_DIR}/config/boards/shields/silicon65/silicon65.keymap.c)