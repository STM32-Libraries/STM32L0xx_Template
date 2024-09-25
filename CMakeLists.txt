# Minimum required version of CMake
cmake_minimum_required(VERSION 3.22)

# Project name
project(template)

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

# Set gcc-arm for c and c++ files
set(CMAKE_C_COMPILER $ENV{HOME}/Tools/gcc-arm/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER $ENV{HOME}/Tools/gcc-arm/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-g++)

set(SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/${PROJECT_NAME}_appl.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/startup.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/stm32l0xx_it.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/system_stm32l0xx.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/stm32l0xx_hal_msp.c
)

# Include directories
include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/conf
)

# Add subdirectory for the HAL library
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/STM32L0xx_HAL_ST stm32l0xx_hal_st)

# Add executable
add_executable(${PROJECT_NAME}_appl.out ${SOURCES} ${CMAKE_CURRENT_SOURCE_DIR}/src/${PROJECT_NAME}_appl.c)

# Use target_compile_options to add compile flags for the target
target_compile_options(${PROJECT_NAME}_appl.out PRIVATE
    -mcpu=cortex-m0plus
    -O2
    -g
    -Wall
    -std=c11
)

# Use target_link_options to add linker flags for the target
target_link_options(${PROJECT_NAME}_appl.out PRIVATE
    -T ${CMAKE_CURRENT_SOURCE_DIR}/linker/linker.ld
    -nostartfiles
)

# Linker map file option
set_target_properties(${PROJECT_NAME}_appl.out PROPERTIES LINK_FLAGS "-Wl,-Map=output.map")

# Link the executable with the HAL library
target_link_libraries(${PROJECT_NAME}_appl.out stm32l0xx_hal_st)
