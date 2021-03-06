cmake_minimum_required (VERSION 2.6)
set(CMAKE_MODULE_PATH ../cmake)
project (dds-tutorial)

include (MacroOpenSplice)
include (osmacros)

find_package (OpenSplice REQUIRED)


include_directories(
  ${PROJECT_SOURCE_DIR}
  ${OpenSplice_INCLUDE_DIRS}
)


set (idls TempControl.idl)

foreach(idl ${idls})
	OpenSplice_IDLGEN (${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/\\1.cpp" VARS_1 ${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/\\1.h" VARS_2 ${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/\\1_DCPS.h" VARS_4 ${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/\\1SplDcps.cpp" VARS_7 ${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/\\1SplDcps.h" VARS_8 ${idl})
	string (REGEX REPLACE "\(.*\).idl" "./gen/ccpp_\\1.h" VARS_9 ${idl})
	set(OpenSplice_DATAMODEL ${OpenSplice_DATAMODEL} ${VARS_1} ${VARS_2} ${VARS_3} ${VARS_4} ${VARS_5} ${VARS_6} ${VARS_7} ${VARS_8} ${VARS_9})
endforeach(idl)

set (PUB_SOURCES
 tspub.cpp
 util.cpp
)
set (WS_SUB_SOURCES
 wstssub.cpp
 util.cpp
)

set (L_SUB_SOURCES
 ltssub.cpp
 util.cpp
)

set (LC_SOURCES
  lifecycle.cpp
)

set (PUB_EXE tspub)
set (WS_SUB_EXE wstssub)
set (L_SUB_EXE ltssub)
set (LC_EXE  lcycle)
set (DATAMODEL datamodel)

add_executable (${PUB_EXE} ${PUB_SOURCES})
add_executable (${WS_SUB_EXE} ${WS_SUB_SOURCES})
add_executable (${L_SUB_EXE} ${L_SUB_SOURCES})
add_executable (${LC_EXE} ${LC_SOURCES})
add_library (${DATAMODEL} SHARED ${OpenSplice_DATAMODEL})

add_definitions (
 ${OpenSplice_DEFINITIONS}
 ${DEFINITIONS}
)

target_link_libraries (
  ${DATAMODEL} 
  ${OpenSplice_LIBRARIES}
 )

target_link_libraries (${PUB_EXE}
 ${OpenSplice_LIBRARIES}
  ${DATAMODEL} 
)

target_link_libraries (${WS_SUB_EXE}
 ${OpenSplice_LIBRARIES}
  ${DATAMODEL}
)

target_link_libraries (${L_SUB_EXE}
 ${OpenSplice_LIBRARIES}
  ${DATAMODEL}
)


target_link_libraries (${LC_EXE}
 ${OpenSplice_LIBRARIES}
  ${DATAMODEL}
)
