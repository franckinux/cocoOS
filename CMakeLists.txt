add_library(cocoos)

target_include_directories(cocoos PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/inc)

file(GLOB SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

target_sources(cocoos PUBLIC ${SRC_FILES})
