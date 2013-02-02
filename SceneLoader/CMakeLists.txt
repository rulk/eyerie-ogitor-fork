FIND_PACKAGE(OIS REQUIRED)

IF(NOT OIS_FOUND)
    MESSAGE(SEND_ERROR "Failed to find OIS.")
ELSE(NOT OIS_FOUND)

    SET(SRCS
        ./src/BaseApplication.cpp
        ./src/SampleApp.cpp
        ./src/DotSceneLoader.cpp
        ./include/BaseApplication.h
        ./include/SampleApp.h
        ./include/DotSceneLoader.h
        ./include/rapidxml.hpp
    )

    SET(SRCS ${SRCS})

    if(WIN32)
        getenv_path(OGRE_CTHUGHA)
        set(OGRE_SAMPLES_INCLUDEPATH
            ${ENV_OGRE_CTHUGHA}/Samples/include
        )
    endif(WIN32)

    if(UNIX)
        find_path(OGRE_SAMPLES_INCLUDEPATH SdkTrays.h
            /usr/share/OGRE/Samples/Common/include/
            /usr/local/share/OGRE/Samples/Common/include/
        )
    endif(UNIX)
    
    include_directories( ./include ${OIS_INCLUDE_DIRS}
        ${OGRE_INCLUDE_DIRS}
        ${OGRE_Terrain_INCLUDE_DIR}
        ${Boost_INCLUDE_DIR}
        ${OGRE_SAMPLES_INCLUDEPATH}
        ${DEPENDENCIES_INCLUDES}
    )

    if(WIN32)
        file(GLOB SampleLoadApp_res ./res/*.rc)
    endif(WIN32)

    if(NOT APPLE)
        ADD_EXECUTABLE(SampleLoadApp WIN32 ${SRCS} ${SampleLoadApp_res})
    else()
        ADD_EXECUTABLE(SampleLoadApp MACOSX_BUNDLE 
            ${SRCS} 
            ${SampleLoadApp_res})
    endif(NOT APPLE)

    SET_TARGET_PROPERTIES(SampleLoadApp PROPERTIES DEBUG_POSTFIX _d)
    
    IF(APPLE)
        FIND_PACKAGE(IOKIT REQUIRED)
        FIND_PACKAGE(Carbon REQUIRED)
        TARGET_LINK_LIBRARIES(SampleLoadApp ${OGRE_LIBRARIES} ${OIS_LIBRARIES} ${IOKit_LIBRARIES} ${Carbon_LIBRARIES} ${OGRE_Terrain_LIBRARY} PagedGeometry)
    ELSE()
        TARGET_LINK_LIBRARIES(SampleLoadApp ${OGRE_LIBRARIES} ${OIS_LIBRARIES} ${OGRE_Terrain_LIBRARY} PagedGeometry ${Boost_LIBRARIES})
    ENDIF(APPLE)

    if(APPLE)
        install(TARGETS SampleLoadApp BUNDLE DESTINATION ${PREFIX}bin CONFIGURATIONS Release RelWithDebInfo MinSizeRel)
        install(TARGETS SampleLoadApp BUNDLE DESTINATION ${PREFIX}Debug CONFIGURATIONS Debug)
    endif()

    IF(WIN32)
        add_custom_command( TARGET SampleLoadApp PRE_BUILD
            COMMAND if not exist ..\\RunPath\\bin mkdir ..\\RunPath\\bin )
        add_custom_command( TARGET SampleLoadApp POST_BUILD
            COMMAND copy \"$(TargetPath)\" ..\\RunPath\\bin )
    ENDIF(WIN32)

    IF(WIN32)
        install(FILES ${OGRE_PLUGIN_DIR_REL}/OIS.dll
            DESTINATION bin
            CONFIGURATIONS Release)
        install(FILES ${OGRE_PLUGIN_DIR_DBG}/OIS_d.dll
            DESTINATION bin
            CONFIGURATIONS Debug)
        install(TARGETS SampleLoadApp
            RUNTIME DESTINATION bin
            CONFIGURATIONS Release)
    ENDIF(WIN32)
    
	IF(WIN32)
		install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
		  DESTINATION ${PREFIX}/SampleApp_Source
		  CONFIGURATIONS Release RelWithDebInfo MinSizeRel
		  )
    ENDIF(WIN32)

ENDIF(NOT OIS_FOUND)
