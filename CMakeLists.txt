# ---- QMK rgb_matrix 灯效移植 ----
# settings_reset 固件无需包含rgb_matrix
if(NOT ("${SHIELD}" MATCHES "settings_reset"))
  target_sources(app PRIVATE
    src/rgb_matrix/controller/rgb_matrix.c
    src/rgb_matrix/controller/rgb_matrix_settings.c
    src/rgb_matrix/controller/qmk_compat.c
    src/rgb_matrix/controller/lib8tion.c
    src/rgb_matrix/keymap.c
  )

  target_include_directories(app PRIVATE
    src/rgb_matrix
    src/rgb_matrix/controller
    src/rgb_matrix/animations
    src/rgb_matrix/animations/runners
  )
endif()