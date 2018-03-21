cmake_minimum_required(VERSION 3.6)

project(mars_android_sdk)

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )

endif ()

include_directories(jni/longlink_packer.h jni/shortlink_packer.h)
include_directories(.)
include_directories(..)
include_directories(../..)
include_directories(../../..)
include_directories(../../../../)

include_directories(
        ${CMAKE_SOURCE_DIR}/mls/src/mls
        ${CMAKE_SOURCE_DIR}/mls/src/mls/mlsapi
        ${CMAKE_SOURCE_DIR}/mls/src/mls/mlsapi/mlsprotocol
        ${CMAKE_SOURCE_DIR}/mls/src/mls/mlsapi/mlsprotocol/crypto
        ${CMAKE_SOURCE_DIR}/mls/src/mls/mlsapi/mlsprotocol/crypto/util
)

add_compile_options(-fPIC)

aux_source_directory(jni SOURCE_FILES)
list(REMOVE_ITEM SOURCE_FILES jni/log_crypt.cc jni/log_crypt.cc.rewriteme)

set(MLS_LIB_NAME mwcs_mls)


if (ENABLE_ANDROID_SUPPORT)
    include_directories(../../../log)
    include_directories(../../../log/crypt)
    include_directories(../../../log/src)
    include_directories(../../../log/export_include)
    include_directories(../../../log/export_include/xlogger)

    link_libraries(${MLS_LIB_NAME})

    add_library(mars_android_sdk SHARED ${SOURCE_FILES}
            $<TARGET_OBJECTS:mars_app>
            $<TARGET_OBJECTS:mars_baseevent>
            $<TARGET_OBJECTS:mars_boost>
            $<TARGET_OBJECTS:mars_sdt>
            $<TARGET_OBJECTS:mars_comm>
            $<TARGET_OBJECTS:mars_stn>
            $<TARGET_OBJECTS:nghttp2>
            $<TARGET_OBJECTS:http-parser>
            $<TARGET_OBJECTS:mwcs_client_mars>
            )

else ()
    list(REMOVE_ITEM SOURCE_FILES jni/JNI_OnLoad.cc)
    add_library(mars_android_sdk SHARED ${SOURCE_FILES}
            $<TARGET_OBJECTS:mars_app>
            $<TARGET_OBJECTS:mars_baseevent>
            $<TARGET_OBJECTS:mars_boost>
            $<TARGET_OBJECTS:mars_sdt>
            $<TARGET_OBJECTS:mars_comm>
            $<TARGET_OBJECTS:mars_stn>
            $<TARGET_OBJECTS:mars_log>
            $<TARGET_OBJECTS:nghttp2>
            $<TARGET_OBJECTS:http-parser>
            $<TARGET_OBJECTS:mwcs_client_mars>
            $<TARGET_OBJECTS:mwcs_mls>
            $<TARGET_OBJECTS:mwcs_mock>
            )
endif ()

set_target_properties(mars_android_sdk PROPERTIES POSITION_INDEPENDENT_CODE ON LINKER_LANGUAGE CXX)

target_link_libraries(mars_android_sdk z)

if (ENABLE_ANDROID_SUPPORT)
    #xlog
    add_library(lib_xlog SHARED IMPORTED)
    set_target_properties(lib_xlog PROPERTIES IMPORTED_LOCATION
            ${CMAKE_SOURCE_DIR}/mars/mars/libraries/mars_android_sdk/libs/${ANDROID_ABI}/libmarsxlog.so)
    target_link_libraries(mars_android_sdk lib_xlog)

    #libsodium
    add_library(lib_sodium SHARED IMPORTED)
    set_target_properties(lib_sodium PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/third-party/sodium/lib/${ANDROID_ABI}/libsodium.so)
    target_link_libraries(mars_android_sdk lib_sodium)
    target_include_directories(mars_android_sdk PUBLIC ${CMAKE_SOURCE_DIR}/third-party/sodium/includes)

    #steg 图片隐写
#    add_library(lib_jpeg SHARED IMPORTED)
#    add_library(lib_steg SHARED IMPORTED)
#    set_target_properties(lib_jpeg PROPERTIES IMPORTED_LOCATION
#            ${CMAKE_SOURCE_DIR}/third-party/steg/lib/prebuilt/${ANDROID_ABI}/libjpegsh.so)
#    set_target_properties(lib_steg PROPERTIES IMPORTED_LOCATION
#            ${CMAKE_SOURCE_DIR}/third-party/steg/lib/prebuilt/${ANDROID_ABI}/libsteg.so)
#    target_link_libraries(mars_android_sdk lib_jpeg lib_steg)
#    target_include_directories(mars_android_sdk PUBLIC ${CMAKE_SOURCE_DIR}/third-party/steg/includes)

    #日志库
    find_library(log-lib log)
    target_link_libraries(mars_android_sdk ${log-lib})

endif ()

if (UNIX AND NOT APPLE)
    target_include_directories(mars_android_sdk PUBLIC ${CMAKE_SOURCE_DIR}/third-party/sodium/includes)
    target_link_libraries(mars_android_sdk
            ${CMAKE_SOURCE_DIR}/third-party/sodium/libsodium.a
            )
endif ()
if (APPLE)
    find_library(SODIUM_LIB libsodium.a)
    target_link_libraries(mars_android_sdk ${SODIUM_LIB})
endif ()