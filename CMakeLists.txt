cmake_minimum_required(VERSION 3.26)

project(geqo LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(GODOT_CPP_DIR
    "${CMAKE_CURRENT_SOURCE_DIR}/../godot-cpp"
)

find_package(Python3 REQUIRED COMPONENTS Interpreter)

# --------------------------------------------------------------------------
# GEQO sources
# --------------------------------------------------------------------------

file(GLOB_RECURSE GEQO_SOURCES CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
)

# Match the SCons build:
# recursive_glob("src", ".cpp", exclude_dirs=["gen"])

list(FILTER GEQO_SOURCES EXCLUDE REGEX "/src/gen/")

# --------------------------------------------------------------------------
# Documentation generation
# --------------------------------------------------------------------------

set(GEQO_DOC_DIR
    "${CMAKE_CURRENT_SOURCE_DIR}/doc_classes"
)

set(GEQO_DOC_OUTPUT
    "${CMAKE_CURRENT_SOURCE_DIR}/src/gen/doc_data.gen.cpp"
)

file(GLOB GEQO_DOC_XML CONFIGURE_DEPENDS
    "${GEQO_DOC_DIR}/*.xml"
)

add_custom_command(
    OUTPUT "${GEQO_DOC_OUTPUT}"

    COMMAND
        "${CMAKE_COMMAND}" -E make_directory
        "${CMAKE_CURRENT_SOURCE_DIR}/src/gen"

    COMMAND
        "${Python3_EXECUTABLE}"
        -c
        "import sys; sys.path.insert(0, r'${GODOT_CPP_DIR}'); import doc_source_generator; doc_source_generator.generate_doc_source_from_directory(r'${GEQO_DOC_OUTPUT}', r'${GEQO_DOC_DIR}')"

    DEPENDS
        "${GODOT_CPP_DIR}/doc_source_generator.py"
        ${GEQO_DOC_XML}

    VERBATIM
)

# --------------------------------------------------------------------------
# Editor / template_debug documentation
# --------------------------------------------------------------------------
#
# If you are building the same configuration as Limbo's editor build,
# include the generated documentation source.
#
# Otherwise this can be omitted.
#

if(TOOLS_ENABLED)
    list(APPEND GEQO_SOURCES
        "${GEQO_DOC_OUTPUT}"
    )
endif()

# --------------------------------------------------------------------------
# GEQO library
# --------------------------------------------------------------------------

add_library(geqo STATIC
    ${GEQO_SOURCES}
)

# --------------------------------------------------------------------------
# Includes
# --------------------------------------------------------------------------

target_include_directories(geqo
    PUBLIC
        "${CMAKE_CURRENT_SOURCE_DIR}/src"
)

# --------------------------------------------------------------------------
# Godot C++
# --------------------------------------------------------------------------

target_link_libraries(geqo
    PUBLIC
        godot-cpp
)