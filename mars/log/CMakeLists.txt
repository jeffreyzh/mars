cmake_minimum_required(VERSION 3.6)

project(mars_log C CXX)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(jni REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

include_directories(
        .
        ..
        ../..
        src
        crypt
        crypt/micro-ecc-master
        export_include
        export_include/xlogger
)

aux_source_directory(crypt SOURCE_FILES)
aux_source_directory(src SOURCE_FILES)
aux_source_directory(crypt/micro-ecc-master SOURCE_FILES)

if (ENABLE_ANDROID_SUPPORT)
    aux_source_directory(jni SOURCE_FILES)
endif ()

add_library(mars_log OBJECT ${SOURCE_FILES})
set_target_properties(mars_log PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)