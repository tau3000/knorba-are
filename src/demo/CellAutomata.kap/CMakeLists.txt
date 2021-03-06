cmake_minimum_required (VERSION 3.0)
project (Phased)

set(CMAKE_VERBOSE_MAKEFILE off)

set(CMAKE_CXX_FLAGS "-fPIC -fvisibility=hidden")
set(CMAKE_EXE_LINKER_FLAGS "-ldl -rdynamic")

include_directories(SYSTEM /usr/local/include)
link_directories(/usr/local/lib)

set(LIBS knorba kfoundation cityhash unistring KnorbaStandardProtocols)

set(DIST_DIR ${CMAKE_CURRENT_BINARY_DIR})

file(MAKE_DIRECTORY ${DIST_DIR}/bin)
file(MAKE_DIRECTORY ${DIST_DIR}/resources)
file(MAKE_DIRECTORY ${DIST_DIR}/resources/lib)

# //\/ demo: UiGridOutputAgent /\//////////////////////////////////////////////

add_library (UiGridOutputAgent SHARED
  ../shared/UiGridOutputAgent.cpp
  ../shared/PixmapOutputProtocol.cpp)

target_link_libraries (UiGridOutputAgent
  ${LIBS})

set_target_properties (UiGridOutputAgent
  PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY ${DIST_DIR}/bin
    PREFIX "")


# //\/ demo: CellAutomata /\///////////////////////////////////////////////////

add_library (CellAutomataAgent SHARED
  CellAutomataAgent.cpp
  ../shared/PixmapOutputProtocol.cpp)

target_link_libraries (CellAutomataAgent
  ${LIBS})

add_dependencies(CellAutomataAgent
  UiGridOutputAgent)

set_target_properties (CellAutomataAgent
  PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY ${DIST_DIR}/bin
    PREFIX "")

configure_file(manifesto.xml ${DIST_DIR} COPYONLY)

add_custom_command (TARGET CellAutomataAgent POST_BUILD
COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/../java/GridUI/dist/GridUI.jar ${DIST_DIR}/resources)

add_custom_command (TARGET CellAutomataAgent POST_BUILD
COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/../java/GridUI/dist/lib/hk-common.jar ${DIST_DIR}/resources/lib)
