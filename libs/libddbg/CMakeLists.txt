# This is libddbg 1.0, the Disksim Debug library.
cmake_minimum_required(VERSION 3.0.0)

# Set CFLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")

# Get libs dir path
string(REGEX REPLACE "/libddbg" "" LIBS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

################
# DEPENDENCIES #
################
include_directories(${LIBS_SOURCE_DIR})

################
# SOURCES      #
################
file(GLOB SOURCES "*.c" "*.h")

add_library(libddbg ${SOURCES})

set_target_properties(libddbg PROPERTIES OUTPUT_NAME "ddbg")
