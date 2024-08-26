#此文件从模板自动生成! 请勿更改!
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.19)

# specify cross compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER  arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# project settings
project(ODriveFirmware C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

#Uncomment for hardware floating point
#add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)

#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)

add_compile_options(-mcpu=cortex-m4 -mthumb -mthumb-interwork)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)

# CPCVE
add_compile_options(-Wno-psabi -Wall -Wdouble-promotion -Wfloat-conversion -Wno-nonnull)
add_link_options(-flto -lc -lm -lnosys)
add_link_options(-mthumb -mfloat-abi=hard -specs=nosys.specs -u_printf_float -u_scanf_float -Wl,--cref -Wl,--gc-sections)
add_link_options(-Wl,--undefined=uxTopUsedPriority)
set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/Board/v3/STM32F405RGTx_FLASH.ld)
add_link_options(-T ${LINKER_SCRIPT})
add_link_options(-LThirdParty/CMSIS/Lib/GCC)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-register")

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    message(STATUS "Maximum optimization for speed")
    add_compile_options(-Ofast)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
    message(STATUS "Maximum optimization for speed, debug info included")
    add_compile_options(-Ofast -g)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
    message(STATUS "Maximum optimization for size")
    add_compile_options(-Os)
else ()
    message(STATUS "Minimal optimization, debug info included")
    add_compile_options(-Og -g)
endif ()

include_directories(
        Board/v3/Inc
        Board/v3/../../ThirdParty/FreeRTOS/Source/portable/GCC/ARM_CM4F
        ThirdParty/STM32F4xx_HAL_Driver/Inc
        ThirdParty/FreeRTOS/Source/include
        ThirdParty/FreeRTOS/Source/CMSIS_RTOS
        ThirdParty/CMSIS/Include
        ThirdParty/CMSIS/Device/ST/STM32F7xx/Include
        ThirdParty/CMSIS/Device/ST/STM32F4xx/Include
        ThirdParty/STM32_USB_Device_Library/Core/Inc
        ThirdParty/STM32_USB_Device_Library/Class/CDC/Inc
        fibre-cpp/include
        ./.
        ./MotorControl
        ./fibre-cpp/include

)

# CPCVE
add_definitions(-D__weak=__attribute__\(\(weak\)\))
add_definitions(-D__packed=__attribute__\(\(__packed__\)\))
add_definitions(-DFIBRE_ENABLE_SERVER)
add_definitions(-DHW_VERSION_MINOR=5)
add_definitions(-DHW_VERSION_VOLTAGE=24)
add_definitions(-DSTM32F405xx)
add_definitions(-DHW_VERSION_MAJOR=3)
add_definitions(-DARM_MATH_CM4)
add_definitions(-DFPU_FPV4)
add_definitions(-DFIBRE_ENABLE_SERVER=1)
add_definitions(-DFIBRE_ENABLE_CLIENT=0)
add_definitions(-DFIBRE_ENABLE_EVENT_LOOP=0)
add_definitions(-DFIBRE_ALLOW_HEAP=0)
add_definitions(-DFIBRE_MAX_LOG_VERBOSITY=0)
add_definitions(-DFIBRE_DEFAULT_LOG_VERBOSITY=2)
add_definitions(-DFIBRE_ENABLE_LIBUSB_BACKEND=0)
add_definitions(-DFIBRE_ENABLE_TCP_SERVER_BACKEND=0)
add_definitions(-DFIBRE_ENABLE_TCP_CLIENT_BACKEND=0)

file(GLOB_RECURSE SOURCES
        Board/v3/startup_stm32f405xx.s
        Board/v3/../../ThirdParty/FreeRTOS/Source/portable/GCC/ARM_CM4F/port.c
        Board/v3/../../Drivers/DRV8301/drv8301.cpp
        Board/v3/board.cpp
        Board/v3/Src/stm32f4xx_hal_timebase_TIM.c
        Board/v3/Src/tim.c
        Board/v3/Src/dma.c
        Board/v3/Src/freertos.c
        Board/v3/Src/main.c
        Board/v3/Src/usbd_conf.c
        Board/v3/Src/spi.c
        Board/v3/Src/usart.c
        Board/v3/Src/usbd_cdc_if.c
        Board/v3/Src/adc.c
        Board/v3/Src/stm32f4xx_hal_msp.c
        Board/v3/Src/usbd_desc.c
        Board/v3/Src/stm32f4xx_it.c
        Board/v3/Src/usb_device.c
        Board/v3/Src/can.c
        Board/v3/Src/system_stm32f4xx.c
        Board/v3/Src/gpio.c
        Board/v3/Src/i2c.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_adc.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_adc_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_can.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ramfunc.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_i2c.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_i2c_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pcd.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pcd_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_spi.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_tim.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_tim_ex.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_uart.c
        ThirdParty/STM32F4xx_HAL_Driver/Src/stm32f4xx_ll_usb.c
        ThirdParty/FreeRTOS/Source/croutine.c
        ThirdParty/FreeRTOS/Source/event_groups.c
        ThirdParty/FreeRTOS/Source/list.c
        ThirdParty/FreeRTOS/Source/queue.c
        ThirdParty/FreeRTOS/Source/stream_buffer.c
        ThirdParty/FreeRTOS/Source/tasks.c
        ThirdParty/FreeRTOS/Source/timers.c
        ThirdParty/FreeRTOS/Source/CMSIS_RTOS/cmsis_os.c
        ThirdParty/FreeRTOS/Source/portable/MemMang/heap_4.c
        ThirdParty/STM32_USB_Device_Library/Core/Src/usbd_core.c
        ThirdParty/STM32_USB_Device_Library/Core/Src/usbd_ctlreq.c
        ThirdParty/STM32_USB_Device_Library/Core/Src/usbd_ioreq.c
        ThirdParty/STM32_USB_Device_Library/Class/CDC/Src/usbd_cdc.c
        fibre-cpp/fibre.cpp
        fibre-cpp/channel_discoverer.cpp
        fibre-cpp/legacy_protocol.cpp
        ./syscalls.c
        ./MotorControl/utils.cpp
        ./MotorControl/arm_sin_f32.c
        ./MotorControl/arm_cos_f32.c
        ./MotorControl/low_level.cpp
        ./MotorControl/axis.cpp
        ./MotorControl/motor.cpp
        ./MotorControl/thermistor.cpp
        ./MotorControl/encoder.cpp
        ./MotorControl/endstop.cpp
        ./MotorControl/acim_estimator.cpp
        ./MotorControl/mechanical_brake.cpp
        ./MotorControl/controller.cpp
        ./MotorControl/foc.cpp
        ./MotorControl/open_loop_controller.cpp
        ./MotorControl/oscilloscope.cpp
        ./MotorControl/sensorless_estimator.cpp
        ./MotorControl/trapTraj.cpp
        ./MotorControl/pwm_input.cpp
        ./MotorControl/main.cpp
        ./Drivers/STM32/stm32_system.cpp
        ./Drivers/STM32/stm32_gpio.cpp
        ./Drivers/STM32/stm32_nvm.c
        ./Drivers/STM32/stm32_spi_arbiter.cpp
        ./communication/can/can_simple.cpp
        ./communication/can/odrive_can.cpp
        ./communication/communication.cpp
        ./communication/ascii_protocol.cpp
        ./communication/interface_uart.cpp
        ./communication/interface_usb.cpp
        ./communication/interface_i2c.cpp
        ./FreeRTOS-openocd.c
        ./autogen/version.c

)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m4 -mthumb -mthumb-interwork)

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})
target_link_libraries(${PROJECT_NAME}.elf ${CMAKE_SOURCE_DIR}/ThirdParty/CMSIS/Lib/GCC/libarm_cortexM4lf_math.a)

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
