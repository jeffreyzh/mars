
cmake_minimum_required(VERSION 3.6)

project(mars_boost)

enable_language(ASM)

file(GLOB_RECURSE BOOST_INCLUDE_FILES ./*.hpp)
file(GLOB_RECURSE OPTIONAL_INCLUDE_FILES ${PROJECT_SOURCE_DIR}/./optional/*.h* ${PROJECT_SOURCE_DIR}/./boost_stdexcept)
list(REMOVE_ITEM BOOST_INCLUDE_FILES ${OPTIONAL_INCLUDE_FILES})
include_directories(BOOST_INCLUDE_FILES)
include_directories(..)


file(GLOB_RECURSE BOOST_SRC_FILES ./*.c*)

file(GLOB_RECURSE WINDOWS_SRC_FILES ${PROJECT_SOURCE_DIR}/./libs/context/src/windows/* ${PROJECT_SOURCE_DIR}/./libs/coroutine/src/windows/*)
list(REMOVE_ITEM BOOST_SRC_FILES ${WINDOWS_SRC_FILES})

file(GLOB_RECURSE THREAD_SRC_FILES ${PROJECT_SOURCE_DIR}/./libs/thread/src/win32/* ${PROJECT_SOURCE_DIR}/./libs/thread/src/pthread/thread.cpp)
list(REMOVE_ITEM BOOST_SRC_FILES ${THREAD_SRC_FILES})
list(REMOVE_ITEM BOOST_SRC_FILES ${PROJECT_SOURCE_DIR}/./libs/thread/src/pthread/once_atomic.cpp)

if (ENABLE_ANDROID_SUPPORT)
    set_property(SOURCE ${PROJECT_SOURCE_DIR}/libs/context/src/asm/jump_arm_aapcs_macho_gas.S PROPERTY LANGUAGE C)
    set_property(SOURCE ${PROJECT_SOURCE_DIR}/libs/context/src/asm/make_arm_aapcs_macho_gas.S PROPERTY LANGUAGE C)
    add_library(mars_boost OBJECT ${BOOST_SRC_FILES}
            ${PROJECT_SOURCE_DIR}/libs/context/src/asm/jump_arm_aapcs_macho_gas.S
            ${PROJECT_SOURCE_DIR}/libs/context/src/asm/make_arm_aapcs_macho_gas.S)

else ()
    set_property(SOURCE ${PROJECT_SOURCE_DIR}/libs/context/src/asm/jump_x86_64_sysv_macho_gas.S PROPERTY LANGUAGE C)
    set_property(SOURCE ${PROJECT_SOURCE_DIR}/libs/context/src/asm/make_x86_64_sysv_macho_gas.S PROPERTY LANGUAGE C)
    add_library(mars_boost OBJECT ${BOOST_SRC_FILES}
            ${PROJECT_SOURCE_DIR}/libs/context/src/asm/jump_x86_64_sysv_macho_gas.S
            ${PROJECT_SOURCE_DIR}/libs/context/src/asm/make_x86_64_sysv_macho_gas.S)
endif ()

set_target_properties(mars_boost PROPERTIES POSITION_INDEPENDENT_CODE ON)
