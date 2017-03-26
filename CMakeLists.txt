cmake_minimum_required(VERSION 3.4)
project(kiosk-control-panel CXX)

set(TOPLEVEL_VERSION_MAJOR 0)
set(TOPLEVEL_VERSION_MINOR 1)
set(TOPLEVEL_VERSION_PATCH 0)
set(TOPLEVEL_VERSION_BUILD 1)
set(TOPLEVEL_VERSION_STABILITY "-alpha")
set(TOPLEVEL_VERSION ${TOPLEVEL_VERSION_MAJOR}.${TOPLEVEL_VERSION_MINOR}.${TOPLEVEL_VERSION_PATCH})
set(TOPLEVEL_VENDOR "midnightBITS")
set(TOPLEVEL_DESCRIPTION_SUMMARY "Raspbery Pi control panel")

CONFIGURE_FILE(${PROJECT_SOURCE_DIR}/project_version.h.in ${PROJECT_BINARY_DIR}/project_version.h)

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_BINARY_DIR})

if (UNIX)
set(SHARE_PREFIX "share/")
set(LIB_PREFIX   "lib/")

set(PLUGIN_INST  lib/kiosk/plugins)     # libpphue.so
set(APPS_INST    share/kiosk/apps)      # kiosk:// apps

set(PLUGIN_CMAKE_BINARY_DIR  ${PLUGIN_INST})
set(APPS_CMAKE_BINARY_DIR    ${APPS_INST})
else()
set(SHARE_PREFIX "")
set(LIB_PREFIX   "")

set(PLUGIN_INST  plugins)               # pphue.dll
set(APPS_INST    apps)                  # kiosk:// apps

set(PLUGIN_CMAKE_BINARY_DIR  kiosk/${PLUGIN_INST})
set(APPS_CMAKE_BINARY_DIR    kiosk/${APPS_INST})
endif()

set(RUNTIME_DST DATA_DST)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG   "")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE "")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG   "..")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE "..")

add_subdirectory(3rd_party/nacl lib/nacl) # to minimise chaos in binary dirs
add_subdirectory(libs/shade     lib/shade) # to minimise chaos in binary dirs
add_subdirectory(plugins/pphue  ${PLUGIN_CMAKE_BINARY_DIR})

add_custom_target(html5
	COMMAND ${CMAKE_COMMAND} -E copy_directory "${CMAKE_CURRENT_SOURCE_DIR}/kiosk-apps" "${CMAKE_CURRENT_BINARY_DIR}/${APPS_INST}/control-panel"
	VERBATIM
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/kiosk-apps DESTINATION ${APPS_INST})

set(CPACK_PACKAGE_VENDOR "${TOPLEVEL_VENDOR}")
set(CPACK_PACKAGE_CONTACT "kiosk-maintainers@midnightbits.com")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "${TOPLEVEL_DESCRIPTION_SUMMARY}")
set(CPACK_PACKAGE_VERSION_MAJOR "${TOPLEVEL_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${TOPLEVEL_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${TOPLEVEL_VERSION_PATCH}")
set(CPACK_PACKAGE_VERSION       "${TOPLEVEL_VERSION}${TOPLEVEL_VERSION_STABILITY}+${TOPLEVEL_VERSION_BUILD}")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "Kiosk ${TOPLEVEL_VERSION_MAJOR}.${TOPLEVEL_VERSION_MINOR}")
include(CPack)
