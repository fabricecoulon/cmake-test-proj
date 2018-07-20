cmake_minimum_required(VERSION 3.5)

# Set the project name
project(third_party_include)

# find a boost install with the libraries filesystem and system
# This will call /usr/share/cmake-3.5/Modules/FindBoost.cmake
find_package(Boost 1.58 REQUIRED COMPONENTS filesystem system)

# FindBoost.cmake sets several variables, among others:
# Boost_FOUND, Boost_INCLUDE_DIRS, Boost_LIBRARY_DIRS

# check if boost was found
if(Boost_FOUND)
    message("boost found")
    message(${Boost_INCLUDE_DIRS})
    message(${Boost_LIBRARY_DIRS})
else()
    message(FATAL_ERROR "Cannot find Boost")
endif()

# Add an executable
add_executable(third_party_include main.cpp)

# -------------------------------- LINKING SCENARIO #1

#
# SCENARIO #1: Using shared libraries preinstalled on system and found-able
# with the standard CMake modules: /usr/share/cmake-3.5/Modules/FindBoost.cmake

#
# link against the _shared_ boost libraries
# Here, we use the exported target Boost::filesystem
#target_link_libraries(third_party_include
#    PRIVATE
#        Boost::filesystem
#)

# -------------------------------- LINKING SCENARIO #2

#
# SCENARIO #2: Using static libraries installed in some random location on system
# The only available information is where the libs are supposed to be located:
# under Boost_LIBRARY_DIRS
#

find_library(BOOST_FILESYSTEM_LIB boost_filesystem HINTS ${Boost_LIBRARY_DIRS})
find_library(BOOST_SYSTEM_LIB boost_system HINTS ${Boost_LIBRARY_DIRS})

# link against the _static_ boost libraries.
# Please note that in this case, you have to know the dependencies
# of your dependencies, cmake will search for libxxx.a for you
target_link_libraries(third_party_include PUBLIC
    ${BOOST_FILESYSTEM_LIB}
    ${BOOST_SYSTEM_LIB}
)

install(TARGETS third_party_include DESTINATION bin)
