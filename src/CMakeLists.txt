cmake_minimum_required(VERSION 3.10)

file(GLOB app_sources "${CMAKE_CURRENT_SOURCE_DIR}/*.c")

# Check if we're building for ESP-IDF/PlatformIO
if(DEFINED ENV{IDF_PATH} OR COMMAND idf_component_register)
    # ESP-IDF build
    idf_component_register(SRCS ${app_sources}
            PRIV_REQUIRES esp_wifi nvs_flash esp_timer
            INCLUDE_DIRS ".")
else()
    project(bareiron C)

    set(CMAKE_C_STANDARD 11)

    add_executable(bareiron ${app_sources})

    target_include_directories(bareiron PRIVATE ../include)

    # Link Winsock library on Windows (required for socket functions)
    if(WIN32)
        target_link_libraries(bareiron PRIVATE ws2_32)
    endif()

    set_target_properties(bareiron PROPERTIES
            RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/.."
    )
endif()