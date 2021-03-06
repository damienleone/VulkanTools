cmake_minimum_required(VERSION 2.8)

project(vktraceviewer_vk)

include("${SRC_DIR}/build_options.cmake")

find_package(Qt5 COMPONENTS Widgets Gui Core Svg QUIET)

if(NOT Qt5_FOUND)
# After Qt5.6 is installed, you may need to add the following to the cmake command line:
# -DCMAKE_PREFIX_PATH=C:\\Qt\\5.6\\msvc2015_64\\
message(WARNING "WARNING: vktraceviewer_vk will be excluded because Qt5 was not found.")
else()

set(SRC_LIST
    ${SRC_LIST}
    vktraceviewer_vk.cpp
    vktraceviewer_vk_settings.cpp
    vktraceviewer_vk_qcontroller.cpp
    vktraceviewer_vk_qfile_model.cpp
    vktraceviewer_vk_qgroupframesproxymodel.cpp
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_QReplayWorker.cpp
    ${SRC_DIR}/vktrace_replay/vkreplay_factory.cpp
)

# This should only contain headers that define a QOBJECT
# Typically that means just headers for UI objects
set(UI_HEADER_LIST
    vktraceviewer_vk_qcontroller.h
    vktraceviewer_vk_qfile_model.h
    vktraceviewer_vk_qgroupframesproxymodel.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_qgroupthreadsproxymodel.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_qimageviewer.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_qsvgviewer.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_QTraceFileModel.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_QReplayWidget.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_QReplayWorker.h
)

set(HDR_LIST
    vktraceviewer_vk_settings.h
    vktraceviewer_vk_qgroupframesproxymodel.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_qgroupthreadsproxymodel.h
    ${SRC_DIR}/vktrace_viewer/vktraceviewer_controller.h
    ${SRC_DIR}/vktrace_replay/vkreplay_factory.h
    ${GENERATED_FILES_DIR}/vktrace_vk_packet_id.h
    ${GENERATED_FILES_DIR}/vktrace_vk_vk_packets.h
)

include_directories(
    ${GENERATED_FILES_DIR}
    ${SRC_DIR}/vktrace_common
    ${SRC_DIR}/vktrace_viewer
    ${SRC_DIR}/vktrace_replay
    ${SRC_DIR}/thirdparty
    ${CMAKE_CURRENT_SOURCE_DIR}/../vkreplay
    ${VKTRACE_VULKAN_INCLUDE_DIR}
    ${Qt5Widgets_INCLUDE_DIRS}
    ${CMAKE_BINARY_DIR}
)

QT5_WRAP_CPP(QT_GEN_HEADER_MOC_LIST ${UI_HEADER_LIST})

set (CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/../../../")

add_library(${PROJECT_NAME} STATIC ${SRC_LIST} ${HDR_LIST}
    ${QT_GEN_HEADER_MOC_LIST}
)

add_dependencies(${PROJECT_NAME} generate_helper_files)

target_link_libraries(${PROJECT_NAME}
    Qt5::Widgets
    Qt5::Core
    Qt5::Svg
    ${VKTRACE_VULKAN_LIB}
    vktrace_common
    vulkan_replay
)

build_options_finalize()

endif(NOT Qt5_FOUND)
