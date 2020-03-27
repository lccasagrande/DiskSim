# This is DiskModel 1.0, a library for modelling disk drive mechanics and layout.
cmake_minimum_required(VERSION 3.0.0)

# Set FLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -D_DM_SOURCE -Wall -Wshadow -Wno-unused -MD")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -D_DM_SOURCE -Wall -Wno-unused  -MD")

# Get libs dir path
string(REGEX REPLACE "/diskmodel" "" LIBS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

# Get libparam path and scripts
set(LIBPARAM_SOURCE_DIR ${LIBS_SOURCE_DIR}/libparam)
set(CMD_CONV_MOD ${LIBPARAM_SOURCE_DIR}/mod.pl)
set(CMD_MAKE_MOD ${LIBPARAM_SOURCE_DIR}/make_modules_h.pl)

# Get modules path 
set(MODULES_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/modules)
set(MODULES_H ${MODULES_SOURCE_DIR}/modules.h)
set(FN_PREFIX "dm")

################
# Dependencies #
################
include_directories(${LIBS_SOURCE_DIR})
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
file(GLOB SOURCES "*.c" "*.h")

add_library(libdiskmodel ${SOURCES} ${MODULES_H} ${ALL_MODULES_OUTPUTS})

set_target_properties(libdiskmodel PROPERTIES OUTPUT_NAME "diskmodel")
