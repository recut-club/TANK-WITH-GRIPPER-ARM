cmake_minimum_required(VERSION 3.19)

# Arduino toolchain (must be set before project())
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/cmake/Arduino-toolchain.cmake)

project(tank_tb6612fng LANGUAGES C CXX ASM)

# ---------- Library: drivers + tank logic ----------
add_library(tank_core STATIC
    src/drivers/tb6612fng.cpp
    src/drivers/motor.cpp
    src/tank/tank.cpp
    src/input/controller.cpp
)

target_include_directories(tank_core PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_arduino_libraries(tank_core PUBLIC core)

# ---------- Firmware executable ----------
add_executable(tank_firmware
    src/main.cpp
)

target_link_libraries(tank_firmware PRIVATE tank_core)
target_link_arduino_libraries(tank_firmware PRIVATE core)

# Generates the .hex and adds the upload target
target_enable_arduino_upload(tank_firmware)

# ---------- Optional: motor test firmware ----------
add_executable(motor_test
    test/motor_test/motor_test.cpp
)
target_link_libraries(motor_test PRIVATE tank_core)
target_link_arduino_libraries(motor_test PRIVATE core)
target_enable_arduino_upload(motor_test)
