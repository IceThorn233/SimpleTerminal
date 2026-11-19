cmake_minimum_required(VERSION 3.20)
project(SucklessSimpleTerminal VERSION 0.9.3)

find_package(X11 REQUIRED COMPONENTS Xft Xrender)

set(CMAKE_C_STANDARD 11)
add_executable(st)
# .. versionadded:: 3.23
#
# target_sources(<target>
#   [{INTERFACE|PUBLIC|PRIVATE}
#    [FILE_SET <set> [TYPE <type>] [BASE_DIRS <dirs>...] [FILES <files>...]]...
#   ]...)

# if(CMAKE_VERSION VERSION_GREATER_EQUAL 4.1)
#     cmake_pkg_config(IMPORT fontconfig REQUIRED NAME Fontconfig)
#     cmake_pkg_config(IMPORT freetype2 REQUIRED NAME Freetype2)
#     cmake_pkg_config(IMPORT zlib REQUIRED NAME Zlib)
#     cmake_pkg_config(IMPORT imlib2 REQUIRED NAME Imlib2)
#     cmake_pkg_config(IMPORT harfbuzz REQUIRED NAME Harfbuzz)
# else()
find_package(PkgConfig REQUIRED)
pkg_check_modules(Fontconfig REQUIRED IMPORTED_TARGET fontconfig)
pkg_check_modules(Freetype2 REQUIRED IMPORTED_TARGET freetype2)
pkg_check_modules(Zlib REQUIRED IMPORTED_TARGET zlib)
pkg_check_modules(Imlib2 REQUIRED IMPORTED_TARGET imlib2)
pkg_check_modules(Harfbuzz REQUIRED IMPORTED_TARGET harfbuzz)
# endif()

target_sources(
    st
    PRIVATE st.c x.c rowcolumn_diacritics_helpers.c graphics.c boxdraw.c hb.c)
if(CMAKE_VERSION VERSION_GREATER_EQUAL 4.1)
    target_sources(
        st
        PRIVATE
        FILE_SET HEADERS
            FILES st.h win.h arg.h "${CMAKE_CURRENT_SOURCE_DIR}/config.h" hb_.h
                  boxdraw_data.h)
endif()
target_link_libraries(
    st
    PRIVATE
        X11::X11
        X11::Xft
        X11::Xrender
        PkgConfig::Fontconfig
        PkgConfig::Freetype2
        PkgConfig::Zlib
        PkgConfig::Imlib2
        PkgConfig::Harfbuzz
        m
        rt
        util)
target_compile_definitions(
    st PRIVATE "_XOPEN_SOURCE=600" "VERSION=\"${PROJECT_VERSION}\"")

# add_custom_command(
#     OUTPUT "${CMAKE_CURRENT_SOURCE_DIR}/config.h"
#     COMMAND "${CMAKE_COMMAND}" -E copy
#             "${CMAKE_CURRENT_SOURCE_DIR}/config.def.h"
#             "${CMAKE_CURRENT_SOURCE_DIR}/config.h")

install(TARGETS st)
install(FILES st.desktop DESTINATION "share/applications")
