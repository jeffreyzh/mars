cmake_minimum_required(VERSION 3.6)

project(mars_sdt C CXX)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

include_directories(.)
include_directories(./..)
include_directories(./../..)
include_directories(src)
include_directories(src/activecheck)
include_directories(src/checkimpl)
include_directories(src/tools)

if (ENABLE_ANDROID_SUPPORT)
    include_directories(jni)
    aux_source_directory(jni SRC_LIST)
endif ()

aux_source_directory(. SRC_LIST)
aux_source_directory(src SRC_LIST)
aux_source_directory(src/activecheck SRC_LIST)
aux_source_directory(src/checkimpl SRC_LIST)
aux_source_directory(src/tools SRC_LIST)


add_library(mars_sdt OBJECT ${SRC_LIST})
set_target_properties(mars_sdt PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)
