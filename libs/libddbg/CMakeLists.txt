# This is libddbg 1.0, the Disksim Debug library.
cmake_minimum_required(VERSION 3.0.0)

# Set Library Name
set(LIB_NAME "libddbg")

# Set CFLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")

# Get libs dir path
string(REGEX REPLACE "/${LIB_NAME}" "" LIBS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

################
# DEPENDENCIES #
################
include_directories(${LIBS_SOURCE_DIR})

################
# SOURCES      #
################
file(GLOB SOURCES "*.c")

add_library(${LIB_NAME} ${SOURCES})

set_target_properties(${LIB_NAME} PROPERTIES OUTPUT_NAME "ddbg")
