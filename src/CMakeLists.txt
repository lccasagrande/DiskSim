cmake_minimum_required(VERSION 3.0.0)

# Set FLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_INLINE")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-unused -Wno-write-strings -MD")
set(CMAKE_C_COMPILER ${CMAKE_CXX_COMPILER})

# Get libs dir path
string(REGEX REPLACE "/src" "/libs" LIBS_DIR ${CMAKE_CURRENT_SOURCE_DIR})

# Get libparam path and scripts
set(LIBPARAM_DIR ${LIBS_DIR}/libparam)
set(CMD_CONV_MOD ${LIBPARAM_DIR}/mod.pl)
set(CMD_MAKE_MOD ${LIBPARAM_DIR}/make_modules_h.pl)

# Get modules path 
set(MODULES_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/modules)
set(MODULES_H ${MODULES_SOURCE_DIR}/modules.h)
set(FN_PREFIX "disksim")


################
# Dependencies #
################
include_directories(${LIBS_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR})


################
# Make Modules #
################
file(GLOB MODULES "${MODULES_SOURCE_DIR}/*.modspec")

set(ALL_MODULES_OUTPUTS "")
foreach(mod ${MODULES})
    # Get output name
    string(REGEX REPLACE "[.]modspec" "_param" mod_output ${mod})
    string(REGEX REPLACE ".+/modules/" ${MODULES_SOURCE_DIR}/${FN_PREFIX}_ mod_output ${mod_output})
    
    # Run command
    add_custom_command(
        COMMAND ${CMD_CONV_MOD} ${FN_PREFIX} ${mod}
        OUTPUT ${mod_output}.c ${mod_output}.h
        WORKING_DIRECTORY ${MODULES_SOURCE_DIR}
        COMMENT "Converting module spec: ${mod}")
    
    # Append output files
    list(APPEND ALL_MODULES_OUTPUTS ${mod_output}.c ${mod_output}.h)
endforeach()

add_custom_command(
    COMMAND ${CMD_MAKE_MOD} ${FN_PREFIX} *.modspec > ${MODULES_H}
    DEPENDS ${ALL_MODULES_OUTPUTS}
    OUTPUT ${MODULES_H}
    WORKING_DIRECTORY ${MODULES_SOURCE_DIR}
    COMMENT "Making modules header")


################
# SOURCES      #
################
set(SRC disksim.c disksim_intr.c disksim_pfsim.c 
        disksim_pfdisp.c disksim_synthio.c disksim_iotrace.c disksim_iosim.c 
        disksim_logorg.c disksim_redun.c disksim_ioqueue.c disksim_iodriver.c 
        disksim_bus.c disksim_controller.c disksim_ctlrdumb.c 
        disksim_ctlrsmart.c disksim_disk.c disksim_diskctlr.c 
        disksim_diskcache.c disksim_statload.c disksim_stat.c disksim_rand48.c disksim_malloc.c 
        disksim_cache.c disksim_cachemem.c disksim_cachedev.c 
        disksim_simpledisk.c disksim_device.c disksim_loadparams.c disksim_simresult.c raw_layout.c)


file(GLOB HEADERS *.h)

add_library(libdisksim ${SRC} ${MODULES_H} ${ALL_MODULES_OUTPUTS})

set_target_properties(libdisksim PROPERTIES OUTPUT_NAME "disksim")

target_link_libraries(libdisksim libdiskmodel libparam libddbg)