cmake_minimum_required(VERSION 3.13)

set(PICO_SDK_FETCH_FROM_GIT on)

include(pico_sdk_import.cmake)
project(test_project C CXX ASM)
set(PICO_BOARD_HEADER_DIRS ${CMAKE_SOURCE_DIR})

pico_sdk_init()

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

add_executable(holly_lpm holly_lpm.c)

target_sources(holly_lpm PUBLIC
    ${CMAKE_CURRENT_LIST_DIR}/clocks.c
    ${CMAKE_CURRENT_LIST_DIR}/peripherals.c
    ${CMAKE_CURRENT_LIST_DIR}/sleep.c
)

target_link_libraries(holly_lpm pico_stdlib hardware_pio hardware_rtc hardware_clocks)

pico_add_extra_outputs(holly_lpm)

pico_enable_stdio_usb(holly_lpm 0)
pico_enable_stdio_uart(holly_lpm 0)

# Check if the -DBOOT2 flag is defined
# -DBOOT2 enables boot2
if (DEFINED BOOT2 AND BOOT2 EQUAL 1)
    message(STATUS "BOOT2 flag is ${BOOT2}, building with boot2")
else ()
    message(STATUS "BOOT2 flag is not set, building without boot2")
    pico_set_linker_script(holly_lpm ${CMAKE_CURRENT_LIST_DIR}/memmap_custom.ld)
endif ()

# Check if the -DDEBUG flag is defined
# -DDEBUG enables DEBUG and turns on debug via USB
if (DEFINED DEBUG AND DEBUG EQUAL 1)
    message(STATUS "DEBUG flag is ${DEBUG}, building with debug")
    pico_enable_stdio_usb(holly_lpm 1)
    set_target_properties(holly_lpm PROPERTIES OUTPUT_NAME "holly_lpm_debug")
else ()
    message(STATUS "DEBUG flag is not set, building NODEBUG")
endif ()
