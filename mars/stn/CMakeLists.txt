cmake_minimum_required(VERSION 3.6)

project(mars_stn C CXX)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

SET(MWCS_CLIENT_DIR "../../..")
include_directories(.
        ..
        ../..
        ${MWCS_CLIENT_DIR}
        ${MWCS_CLIENT_DIR}/mwcs-client/client-mars
        ${MWCS_CLIENT_DIR}/mwcs-client/includes
        ${MWCS_CLIENT_DIR}/nghttp2
        ${MWCS_CLIENT_DIR}/nghttp2/includes/nghttp2
        ${MWCS_CLIENT_DIR}/mls/src/mls
        ${MWCS_CLIENT_DIR}/mls/src/mls/mlsapi
        ${MWCS_CLIENT_DIR}/third-party/http-parser
        ${MWCS_CLIENT_DIR}/third-party/sodium/includes
        ../boost
        ../comm
)

aux_source_directory(. SOURCE_FILES)
aux_source_directory(src SOURCE_FILES)

if (ENABLE_ANDROID_SUPPORT)
    aux_source_directory(jni SOURCE_FILES)

endif ()

add_library(mars_stn OBJECT ${SOURCE_FILES})

set_target_properties(mars_stn PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)