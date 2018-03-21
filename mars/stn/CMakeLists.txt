cmake_minimum_required(VERSION 3.6)

project(mars_stn C CXX)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()

SET(MWCS_CLIENT_DIR "../../..")

include_directories(.)
include_directories(./..)
include_directories(./../..)
include_directories(${MWCS_CLIENT_DIR})
include_directories(../boost)
include_directories(../comm)
include_directories(src)
include_directories(jni)
include_directories(${MWCS_CLIENT_DIR}/mwcs-client/client-mars/callback)
include_directories(${MWCS_CLIENT_DIR}/mwcs-client/client-mars)
include_directories(${MWCS_CLIENT_DIR}/mwcs-client/includes)
include_directories(${MWCS_CLIENT_DIR}/mwcs-client/client) #这里以后删掉,暂时引用gzip
include_directories(${MWCS_CLIENT_DIR}/nghttp2)
include_directories(${MWCS_CLIENT_DIR}/nghttp2/includes/nghttp2)
include_directories(${MWCS_CLIENT_DIR}/mls/src/mls)
include_directories(${MWCS_CLIENT_DIR}/mls/src/mls/mlsapi)
include_directories(${MWCS_CLIENT_DIR}/third-party/http-parser)
include_directories(${MWCS_CLIENT_DIR}/third-party/sodium/includes)

aux_source_directory(. SOURCE_FILES)
aux_source_directory(src SOURCE_FILES)
aux_source_directory(${MWCS_CLIENT_DIR}/mwcs-client/client-mars/callback SOURCE_FILES)

if (ENABLE_ANDROID_SUPPORT)
    aux_source_directory(jni SOURCE_FILES)
    aux_source_directory(${MWCS_CLIENT_DIR}/mwcs-client/client-mars/callback/jni SOURCE_FILES)

endif ()

add_library(mars_stn OBJECT ${SOURCE_FILES})

set_target_properties(mars_stn PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)