cmake_minimum_required(VERSION 3.0.0)

# Set FLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wno-unused -MD")
set(CMAKE_C_COMPILER ${CMAKE_CXX_COMPILER})

# Get libs dir path
string(REGEX REPLACE "/memsmodel" "" LIBS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})
string(REGEX REPLACE "/libs" "/src" DISKSIM_SOURCE_DIR ${LIBS_SOURCE_DIR})

# Get libparam path and scripts
set(LIBPARAM_SOURCE_DIR ${LIBS_SOURCE_DIR}/libparam)
set(CMD_CONV_MOD ${LIBPARAM_SOURCE_DIR}/mod.pl)
set(CMD_MAKE_MOD ${LIBPARAM_SOURCE_DIR}/make_modules_h.pl)

# Get modules path 
set(MODULES_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/modules)
set(MODULES_H ${MODULES_SOURCE_DIR}/modules.h)
set(FN_PREFIX "memsmodel")

################
# Dependencies #
################
include_directories(${LIBS_SOURCE_DIR})
include_directories(${DISKSIM_SOURCE_DIR})
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


##################
### SOURCES      #
##################
set(SRC_INTERNALS   mems_internals.c mems_internals.h 
                    mems_piecewise_seek.c mems_piecewise_seek.h 
                    mems_hong_seek.c mems_hong_seek.h)

set(SRC mems_disksim.c mems_disksim.h  
        mems_event.c 
        mems_mapping.c mems_mapping.h 
        mems_internals.c mems_internals.h 
        mems_piecewise_seek.c mems_piecewise_seek.h 
        mems_hong_seek.c mems_hong_seek.h 
        mems_buffer.c mems_buffer.h 
        modules/memsmodel_mems_param.c modules/memsmodel_mems_param.h)

add_library(libmems_internals ${SRC_INTERNALS} ${MODULES_H} ${ALL_MODULES_OUTPUTS})
add_library(libmemsmodel ${SRC} ${MODULES_H} ${ALL_MODULES_OUTPUTS})

set_target_properties(libmems_internals PROPERTIES OUTPUT_NAME "mems_internals")

set_target_properties(libmemsmodel PROPERTIES OUTPUT_NAME "memsmodel")
