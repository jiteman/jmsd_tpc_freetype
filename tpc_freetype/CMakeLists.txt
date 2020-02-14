cmake_minimum_required( VERSION 3.7.1 )

project( tpc_freetype C CXX )


JMSD_SHOW_PROJECT_HEADER()


include( ${JMSD_COMPONENT_CMAKE_SETTINGS_PATH}/tpc-freetype-lib-set-compiler-settings.cmake )
set( JMSD_THIS_PROJECT_SOURCE_ROOT ${JMSD_COMPONENT_SOURCE_ROOT_PATH} )

## include dependencies
set( ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR
	${JMSD_PLATFORM_SOURCES}
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include )
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR )
include_directories( ${${PROJECT_NAME}_DEPENDENCY_DIRS_VAR} )

#include_directories( ${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/freetype/config )

## this project headers and sources enumeration section
file( GLOB_RECURSE header_and_source_files
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/src/*.h*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/src/*.c* )

file( GLOB nonrecursive_header_and_source_files
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/*.h*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/*.c*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/freetype/*.h*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/freetype/*.c*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/freetype/config/*.h*
	${JMSD_THIS_PROJECT_SOURCE_ROOT}/include/freetype/config/*.c* )

list( APPEND header_and_source_files ${nonrecursive_header_and_source_files} )

set( FILES_TO_REMOVE_HEADER_AND_SOURCE
	"" )

if( UNIX )
	list( APPEND FILES_TO_REMOVE_HEADER_AND_SOURCE
		"src/base/ftsystem.c" )
	list( APPEND header_and_source_files
		${JMSD_THIS_PROJECT_SOURCE_ROOT}/builds/unix/ftsystem.c )
elseif( WIN32 )
	list( APPEND header_and_source_files
		${JMSD_THIS_PROJECT_SOURCE_ROOT}/builds/windows/ftdebug.c )
endif()

JMSD_REMOVE_FILES_FROM_THE_LIST( header_and_source_files FILES_TO_REMOVE_HEADER_AND_SOURCE )

add_library( ${PROJECT_NAME} SHARED ${header_and_source_files} )


## definition section
set( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR
	-DJMSD_TPC_BASE_LIBRARY_TEMPLATE_SHARED_INTERFACE_INTERNAL )
JMSD_LIST_TO_STRING( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
JMSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
set( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR
	"" )
JMSD_LIST_TO_STRING( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
JMSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
add_definitions( "${${PROJECT_NAME}_BUILD_DEFINITIONS_VAR}" )


## required to be able to be linked into shared libraries.
set_target_properties( ${PROJECT_NAME} PROPERTIES POSITION_INDEPENDENT_CODE ON )
set_target_properties( ${PROJECT_NAME} PROPERTIES VERSION 0.1.0 )
set_target_properties( ${PROJECT_NAME} PROPERTIES SOVERSION 0.1 )


## project target section
set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
	"" )
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR )
target_link_libraries( ${PROJECT_NAME} ${${PROJECT_NAME}_DEPENDENCY_LIBS_VAR} )


## Expose public includes to other subprojects through cache variable.
include( ${JMSD_CMAKE_SETTINGS_PATH}/set-expose-dependencies.cmake )
JMSD_SHOW_PROJECT_FOOTER()