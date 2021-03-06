
# We define the minimum cmake. This is standard for elementary OS
cmake_minimum_required (VERSION 2.8)

#We define the minimum policy version
cmake_policy(VERSION 2.8)


#We define out project name.
project(captive-login)

set (RELEASE_NAME "GreatBarrier")
set (VERSION "0.2.1")
set (TARGET_GLIB 2.32)
set (GETTEXT_PACKAGE "${CMAKE_PROJECT_NAME}")

configure_file (${CMAKE_SOURCE_DIR}/src/config.vala.cmake ${CMAKE_SOURCE_DIR}/src/config.vala)
add_definitions (-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")

#Here we load the modules we downloaded earlier and placed into the ./cmake directory
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake )

# Vala
find_package (Vala REQUIRED)
include (ValaVersion)
ensure_vala_version ("0.26" MINIMUM)
include (ValaPrecompile)

find_package(PkgConfig)

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY
)

add_custom_target(
    uninstall
    COMMAND
        ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake
)

# CTest
include (CTest)
if (BUILD_TESTING)
    add_test (NAME validate-desktop-file COMMAND desktop-file-validate ${CMAKE_BINARY_DIR}/data/io.elementary.capnet-assist.desktop)
endif ()

#Here we add the src directory. CMake will look for the CMakeLists.txt in this folder
#and work from there
add_subdirectory (data)
add_subdirectory (po)
add_subdirectory (src)

# install captive network test script to Network Manager directory
install (PROGRAMS ${CMAKE_CURRENT_SOURCE_DIR}/90captive_portal_test DESTINATION /etc/NetworkManager/dispatcher.d/ )
