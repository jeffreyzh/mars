cmake_minimum_required(VERSION 3.6)

project(mars_app C CXX)


if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/..)
include_directories(${PROJECT_SOURCE_DIR}/../..)

if (ENABLE_ANDROID_SUPPORT)
    aux_source_directory(jni SRC_LIST)
else()
    aux_source_directory(${PROJECT_SOURCE_DIR} SRC_LIST)
endif ()

add_library(mars_app OBJECT ${SRC_LIST})
set_target_properties(mars_app PROPERTIES POSITION_INDEPENDENT_CODE ON)