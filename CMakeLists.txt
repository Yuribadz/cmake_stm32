cmake_minimum_required(VERSION 3.13)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/cmake/stm32_gcc.cmake)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON CACHE INTERNAL "") # works
project(stm32-freertos C ASM)
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)

find_package(CMSIS COMPONENTS STM32F303RE REQUIRED)
find_package(HAL COMPONENTS STM32F303RE REQUIRED)
find_package(FreeRTOS COMPONENTS ARM_CM4F REQUIRED)

add_executable(stm32-freertos src/main.c
)
target_sources(stm32-freertos
  PRIVATE 
  src/main.c
  src/FreeRTOS-openocd.c
)
target_include_directories(stm32-freertos
  PRIVATE
  inc
  )
set(GCC_COMPILE_DEBUG_OPTIONS "${GCC_COMPILE_OPTIONS};-ggdb;-O0")
target_compile_options(${PROJECT_NAME} PUBLIC "$<$<CONFIG:Debug>:${GCC_COMPILE_DEBUG_OPTIONS}>")
target_link_libraries(stm32-freertos PRIVATE
    FreeRTOS::Timers
    FreeRTOS::Heap::4
    FreeRTOS::ARM_CM4F 
    HAL::STM32::F3::RCC
    HAL::STM32::F3::GPIO
    HAL::STM32::F3::CORTEX
    CMSIS::STM32::F303RE 
    STM32::NoSys
)
