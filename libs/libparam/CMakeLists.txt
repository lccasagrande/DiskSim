# This is libparam 1.0, a library providing parsing and unparsing of DiskSim/diskmodel parameter files.
cmake_minimum_required(VERSION 3.0.0)

# Set FLAGS
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -Wall")

# Get libs dir path
string(REGEX REPLACE "/libparam" "" LIBS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})


################
# Dependencies #
################
find_package(FLEX REQUIRED)
find_package(BISON REQUIRED)

include_directories(${LIBS_SOURCE_DIR})

BISON_TARGET(MyParser "libparam.y" "libparam.tab.c" COMPILE_FLAGS "-plibparam --defines")

FLEX_TARGET(MyScanner "libparam.lex" "lex.libparam.c" COMPILE_FLAGS -Plibparam)

ADD_FLEX_BISON_DEPENDENCY(MyScanner MyParser)

################
# SOURCES      #
################
file(GLOB SOURCES "*.h" "*.c")

add_library(libparam 
    ${SOURCES}
    ${FLEX_MyScanner_OUTPUTS}
    ${BISON_MyParser_OUTPUTS})

set_target_properties(libparam PROPERTIES OUTPUT_NAME "param")
