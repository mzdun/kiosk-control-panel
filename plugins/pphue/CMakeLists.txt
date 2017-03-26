set(DEPS ppapi_cpp shade)


CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/version.h.in ${CMAKE_CURRENT_BINARY_DIR}/version.h)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/hue)
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../../3rd_party/nacl/include)

set(SRC
	pp/instance.cc
	pp/instance.h
	pp/module.cc
)

add_library(pphue SHARED ${SRC})
set_target_properties(pphue PROPERTIES
	CXX_STANDARD 11
	VERSION ${TOPLEVEL_VERSION}
	SOVERSION ${TOPLEVEL_VERSION_MAJOR}
	FOLDER libs)

foreach(DEP ${DEPS})
	add_dependencies(pphue ${DEP})
	target_link_libraries(pphue $<TARGET_FILE:${DEP}>)
endforeach()

install(TARGETS pphue DESTINATION ${PLUGIN_INST})
