cmake_minimum_required(VERSION 3.6)

project(mars_comm C CXX)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DUSING_XLOG_WEAK_FUNC -Wno-error=strict-aliasing")

if (NOT ENABLE_ANDROID_SUPPORT)
    find_package(JNI REQUIRED)
    include_directories(
            ${JAVA_INCLUDE_PATH}
    )
endif ()
include_directories(.)
include_directories(..)
include_directories(../..)


file(GLOB_RECURSE SRC_LIST *.c*)

file(GLOB_RECURSE UWP_SRC_FILES UWP/*.c*)
list(REMOVE_ITEM SRC_LIST UWP_SRC_FILES)

if (NOT ENABLE_ANDROID_SUPPORT)
    file(GLOB_RECURSE ANDROID_SRC_FILES android/*.c*)
    list(REMOVE_ITEM SRC_LIST ${ANDROID_SRC_FILES})

    file(GLOB_RECURSE JNI_SRC_FILES jni/*.c*)
    list(REMOVE_ITEM SRC_LIST ${JNI_SRC_FILES})
endif ()

file(GLOB_RECURSE WINDOW_SRC_FILES windows/*.c*)
list(REMOVE_ITEM SRC_LIST ${WINDOW_SRC_FILES})

file(GLOB_RECURSE PATTERN_TEST_SRC_FILES corepattern/test/*.c*)
list(REMOVE_ITEM SRC_LIST ${PATTERN_TEST_SRC_FILES})

file(GLOB_RECURSE PATTERN_TEST_CASE_SRC_FILES test_case/*.c*)
list(REMOVE_ITEM SRC_LIST ${PATTERN_TEST_CASE_SRC_FILES})

file(GLOB_RECURSE UNIX_THREAD_TEST_SRC_FILES unix/thread/*.c*)
list(REMOVE_ITEM SRC_LIST ${UNIX_THREAD_TEST_SRC_FILES})

file(GLOB_RECURSE COMM_TEST_SRC_FILES *.test*)
list(REMOVE_ITEM SRC_LIST ${COMM_TEST_SRC_FILES})

file(GLOB_RECURSE THREAD_TEST_SRC_FILES thread/*.c*)
list(REMOVE_ITEM SRC_LIST ${THREAD_TEST_SRC_FILES})

if (ENABLE_ANDROID_SUPPORT)
    file(GLOB_RECURSE DEBUGGER_SRC_FILES debugger/*.c*)
    list(REMOVE_ITEM SRC_LIST ${DEBUGGER_SRC_FILES})

    #Android平台直接使用已编译好的xlog，这里去掉重复的源文件
    file(GLOB_RECURSE XLOGGER_SRC_FILES xlogger/*.c*)
    list(REMOVE_ITEM SRC_LIST ${XLOGGER_SRC_FILES})
endif ()

add_library(mars_comm OBJECT ${SRC_LIST})
set_target_properties(mars_comm PROPERTIES POSITION_INDEPENDENT_CODE ON)