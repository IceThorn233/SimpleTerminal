cmake_minimum_required(VERSION 3.20)
project(SucklessSimpleTerminal VERSION 0.9.3)

find_package(X11 REQUIRED COMPONENTS Xft)

add_executable(st)

# .. versionadded:: 3.23
#
#  target_sources(<target>
#    [{INTERFACE|PUBLIC|PRIVATE}
#     [FILE_SET <set> [TYPE <type>] [BASE_DIRS <dirs>...] [FILES <files>...]]...
#    ]...)

if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.31)
    cmake_pkg_config(IMPORT fontconfig REQUIRED NAME Fontconfig)
    cmake_pkg_config(IMPORT freetype2 REQUIRED NAME Freetype2)
else()
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(Fontconfig REQUIRED IMPORTED_TARGET fontconfig)
    pkg_check_modules(Freetype2 REQUIRED IMPORTED_TARGET freetype2)
endif()

target_sources(st PRIVATE st.c x.c)
if(CMAKE_VERSION VERSION_GREATER_EQUAL 4.1)
    target_sources(
        st
        PRIVATE
        FILE_SET
            HEADERS FILES st.h win.h arg.h
            "${CMAKE_CURRENT_SOURCE_DIR}/config.h")
endif()
target_link_libraries(
    st
    PRIVATE
        X11::X11
        X11::Xft
        PkgConfig::Fontconfig
        PkgConfig::Freetype2
        m
        rt
        util)
target_compile_definitions(
    st
    PRIVATE "_XOPEN_SOURCE=600" "VERSION=\"${PROJECT_VERSION}\"")

add_custom_command(
    OUTPUT "${CMAKE_CURRENT_SOURCE_DIR}/config.h"
    COMMAND
        "${CMAKE_COMMAND}" -E copy "${CMAKE_CURRENT_SOURCE_DIR}/config.def.h"
        "${CMAKE_CURRENT_SOURCE_DIR}/config.h")

