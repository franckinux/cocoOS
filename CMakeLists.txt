add_library(cocoos STATIC)

target_include_directories(cocoos PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/inc)

file(GLOB SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

target_compile_options(cocoos PRIVATE -g -std=c99 -Wall -Wextra -pedantic -Wno-variadic-macros -Wno-unused-parameter)

target_sources(cocoos PRIVATE ${SRC_FILES})
