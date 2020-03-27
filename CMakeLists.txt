cmake_minimum_required(VERSION 3.0.0)

project(Disksim VERSION 4.0.0)

# Check that the used compiler handles c++11 correctly
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "5")
        message(FATAL_ERROR "Insufficient gcc version: 5 is needed")
    elseif(CMAKE_CXX_COMPILER_VERSION VERSION_GREATER_EQUAL "6")
        message(FATAL_ERROR "Insufficient gcc version: 5 is needed")
    endif()
else()
    message(WARNING "Unknown compiler. Make sure it fully supports C++11.")
endif()


include(CTest)
enable_testing()

# Enable C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# CPack
set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)

################
# Set paths #
################
set(SRC_DIR "src")
set(LIBS_DIR "libs")
set(LIBPARAM_DIR ${LIBS_DIR}/libparam)
set(LIBDDBG_DIR ${LIBS_DIR}/libddbg)
set(LIBDISKMODEL_DIR ${LIBS_DIR}/diskmodel)

add_subdirectory(${LIBDDBG_DIR})
add_subdirectory(${LIBPARAM_DIR})
add_subdirectory(${LIBDISKMODEL_DIR})
add_subdirectory(${SRC_DIR})

################
# Dependencies #
################
include_directories(${LIBS_DIR})
include_directories(${SRC_DIR})


################
# SOURCES      #
################
set(DISKSIM ${SRC_DIR}/disksim_main.c ${SRC_DIR}/disksim_interface.c)
set(RMS ${SRC_DIR}/rms.c ${SRC_DIR}/disksim_interface.c)
set(HPLCOMB ${SRC_DIR}/hplcomb.c ${SRC_DIR}/disksim_interface.c)
set(SYSSIM ${SRC_DIR}/syssim_driver.c ${SRC_DIR}/disksim_interface.c)

file(GLOB HEADERS ${SRC_DIR}/*.h)

set_source_files_properties(${DISKSIM} PROPERTIES LANGUAGE CXX )
set_source_files_properties(${RMS} PROPERTIES LANGUAGE CXX )
set_source_files_properties(${HPLCOMB} PROPERTIES LANGUAGE CXX )
set_source_files_properties(${SYSSIM} PROPERTIES LANGUAGE CXX )

add_executable(disksim ${DISKSIM} ${HEADERS})
add_executable(disksim_rms ${RMS} ${HEADERS})
add_executable(disksim_hplcomb ${HPLCOMB} ${HEADERS})
add_executable(disksim_syssim ${SYSSIM} ${HEADERS})

target_link_libraries(disksim libdisksim libdiskmodel libparam libddbg)
target_link_libraries(disksim_rms libdisksim libdiskmodel libparam libddbg)
target_link_libraries(disksim_hplcomb libdisksim libdiskmodel libparam libddbg)
target_link_libraries(disksim_syssim libdisksim libdiskmodel libparam libddbg)


################
# INSTALL      #
################
install(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/disksim DESTINATION bin)
install(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/disksim_rms DESTINATION bin)
install(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/disksim_hplcomb DESTINATION bin)
install(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/disksim_syssim DESTINATION bin)