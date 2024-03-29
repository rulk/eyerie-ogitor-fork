######################################################################
# OGRE BUILD SYSTEM
# Welcome to the CMake build system for OGRE.
# This is the main file where we prepare the general build environment
# and provide build configuration options.
######################################################################



cmake_minimum_required(VERSION 2.6)
set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS TRUE)
cmake_policy(SET CMP0003 NEW)

project(OgSkyx)


set(HEADER_FILES
    SkyxEditor.h
)
set(SOURCE_FILES
    SkyxEditor.cpp
)


SET(OgSkyx_VERSION 0)
SET(OgSkyx_VERSION_MAJOR 1)

include_directories (include)
# Terrain Scene Manager
#include_directories ( ${OGITOR_SM_INCLUDES} )
include_directories(${DEPENDENCIES_INCLUDES})
include_directories(${OGITOR_INCLUDES})
MESSAGE(STATUS "${OGITOR_INCLUDES}" )

include_directories(.)
LINK_DIRECTORIES( ${OGITOR_LIBPATH} )
LINK_DIRECTORIES( ${DEPENDENCIES_LIBPATH} )

link_libraries( ${OGRE_LIBRARIES} Ogitor Skyx)
add_library(OgSkyx SHARED ${HEADER_FILES} ${SOURCE_FILES}  )
#target_link_libraries(OgreMain Ogitor)
SET(LIBRARY_OUTPUT_PATH ../lib)

set_target_properties(OgSkyx PROPERTIES
        INSTALL_RPATH ${OGRE_INSTALL_DIR}/lib/OGRE  )

set_target_properties(OgSkyx PROPERTIES VERSION ${OgSkyx_VERSION} SOVERSION ${OgSkyx_VERSION_MAJOR})

set_target_properties(OgSkyx PROPERTIES
COMPILE_DEFINITIONS PLUGIN_EXPORT
)

set_target_properties(OgSkyx PROPERTIES 
    SOLUTION_FOLDER Plugins) 

IF(UNIX AND NOT APPLE)
    add_custom_command( TARGET OgSkyx PRE_BUILD
        COMMAND mkdir -p ../../RunPath/Plugins )
    add_custom_command( TARGET OgSkyx POST_BUILD
        COMMAND cp -r ../lib/libOgSkyx.* ../../RunPath/Plugins/ )
ENDIF(UNIX AND NOT APPLE)

IF(WIN32)
    add_custom_command( TARGET OgSkyx PRE_BUILD
        COMMAND if not exist ..\\..\\RunPath\\Plugins mkdir ..\\..\\RunPath\\Plugins )
    add_custom_command( TARGET OgSkyx POST_BUILD
        COMMAND copy \"$(TargetPath)\" ..\\..\\RunPath\\Plugins & if exist ..\\..\\RunPath\\Plugins\\OgSkyx_d.dll move ..\\..\\RunPath\\Plugins\\OgSkyx_d.dll ..\\..\\RunPath\\Plugins\\debug)
ENDIF(WIN32)

IF(WIN32)
install(TARGETS OgSkyx
    RUNTIME DESTINATION Plugins
    CONFIGURATIONS Release RelWithDebInfo MinSizeRel)
ENDIF()


