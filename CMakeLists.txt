cmake_minimum_required(VERSION 3.26)

project(geqo LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(GODOT_CPP_DIR
    "${CMAKE_CURRENT_SOURCE_DIR}/../godot-cpp"
)

# --------------------------------------------------------------------------
# GEQO sources
# --------------------------------------------------------------------------

file(GLOB_RECURSE GEQO_SOURCES CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
)

# GEQO library
add_library(geqo STATIC
    ${GEQO_SOURCES}
)

# Includes
target_include_directories(geqo
    PUBLIC
        "${CMAKE_CURRENT_SOURCE_DIR}/src"
)

# godot-cpp
target_link_libraries(geqo
    PUBLIC
        godot-cpp
)
