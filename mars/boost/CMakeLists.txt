
cmake_minimum_required(VERSION 3.6)

project(mars_boost)

enable_language(ASM)

include_directories(..)


file(GLOB_RECURSE BOOST_SRC_FILES *.c*)

file(GLOB_RECURSE WINDOWS_SRC_FILES libs/context/src/windows/* libs/coroutine/src/windows/*)
list(REMOVE_ITEM BOOST_SRC_FILES ${WINDOWS_SRC_FILES})

file(GLOB_RECURSE THREAD_SRC_FILES libs/thread/src/win32/* libs/thread/src/pthread/thread.cpp)
list(REMOVE_ITEM BOOST_SRC_FILES ${THREAD_SRC_FILES})
list(REMOVE_ITEM BOOST_SRC_FILES ${PROJECT_SOURCE_DIR}/libs/thread/src/pthread/once_atomic.cpp)

if (ENABLE_ANDROID_SUPPORT)
    set_property(SOURCE libs/context/src/asm/jump_arm_aapcs_macho_gas.S PROPERTY LANGUAGE C)
    set_property(SOURCE libs/context/src/asm/make_arm_aapcs_macho_gas.S PROPERTY LANGUAGE C)
    add_library(mars_boost OBJECT ${BOOST_SRC_FILES}
            libs/context/src/asm/jump_arm_aapcs_macho_gas.S
            libs/context/src/asm/make_arm_aapcs_macho_gas.S)

else ()
    set_property(SOURCE libs/context/src/asm/jump_x86_64_sysv_macho_gas.S PROPERTY LANGUAGE C)
    set_property(SOURCE libs/context/src/asm/make_x86_64_sysv_macho_gas.S PROPERTY LANGUAGE C)
    add_library(mars_boost OBJECT ${BOOST_SRC_FILES}
            libs/context/src/asm/jump_x86_64_sysv_macho_gas.S
            libs/context/src/asm/make_x86_64_sysv_macho_gas.S)
endif ()

set_target_properties(mars_boost PROPERTIES POSITION_INDEPENDENT_CODE ON)
