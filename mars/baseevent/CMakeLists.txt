cmake_minimum_required(VERSION 3.6)

project(mars_baseevent C CXX)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

include_directories(..)
include_directories(../..)

aux_source_directory(src SRC_LIST)

if (ENABLE_ANDROID_SUPPORT)
aux_source_directory(jni SRC_LIST)

endif()

add_library(mars_baseevent OBJECT ${SRC_LIST})
set_target_properties(mars_baseevent PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)


