# ==============================================================================
# Copyright (C) 2018-2020 Intel Corporation
#
# SPDX-License-Identifier: MIT
# ==============================================================================

cmake_minimum_required(VERSION 3.1)

set (TARGET_NAME "gstvideoanalyticsmeta")

find_package(OpenCV REQUIRED core imgproc)
find_package(PkgConfig REQUIRED)

pkg_check_modules(GSTREAMER gstreamer-1.0>=1.14 REQUIRED)
pkg_check_modules(GLIB2 glib-2.0 REQUIRED)

file (GLOB MAIN_SRC
        ${CMAKE_CURRENT_SOURCE_DIR}/metadata/*.c
        )

file (GLOB MAIN_HEADERS
        ${CMAKE_CURRENT_SOURCE_DIR}/*.h
        ${CMAKE_CURRENT_SOURCE_DIR}/metadata/*.h
        )

add_library(${TARGET_NAME} SHARED ${MAIN_SRC} ${MAIN_HEADERS})
set_target_lib_version(${TARGET_NAME})
set_compile_flags(${TARGET_NAME})

target_include_directories(${TARGET_NAME}
PUBLIC
        ${GSTREAMER_INCLUDE_DIRS}
        ${GLIB2_INCLUDE_DIRS}
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/metadata
)

target_link_libraries(${TARGET_NAME}
PUBLIC
        ${OpenCV_LIBS}
        ${GSTREAMER_LIBRARIES}
        ${GSTVIDEO_LIBRARIES}
        ${GLIB2_LIBRARIES}
)

configure_file(${CMAKE_SOURCE_DIR}/cmake/dl-streamer.pc.in ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/dl-streamer.pc @ONLY)

install(FILES ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/dl-streamer.pc DESTINATION ${DLSTREAMER_LIBRARIES_INSTALL_PATH}/pkgconfig/)

install(TARGETS ${TARGET_NAME} DESTINATION ${DLSTREAMER_LIBRARIES_INSTALL_PATH}/)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} DESTINATION ${DLSTREAMER_HEADERS_INSTALL_PATH}/gst FILES_MATCHING PATTERN "*.h")
