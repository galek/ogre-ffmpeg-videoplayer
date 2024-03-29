# Change this to your project's name
set(PROJECT_NAME "ogre-ffmpeg-video")

set(CMAKE_CXX_FLAGS "-D__STDC_CONSTANT_MACROS")

project(${PROJECT_NAME} CXX C)
cmake_minimum_required(VERSION 2.8)

# Avoid source tree pollution
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
	message(FATAL_ERROR "In-source builds are not permitted. Make a separate folder for building:\nmkdir build; cd build; cmake ..\nBefore that, remove the files already created:\nrm -rf CMakeCache.txt CMakeFiles")
endif(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)

# Add a sensible build type default and warning because empty means no optimization and no debug info.
if(NOT CMAKE_BUILD_TYPE)
	message("WARNING: CMAKE_BUILD_TYPE is not defined!\n         Defaulting to CMAKE_BUILD_TYPE=RelWithDebInfo. Use ccmake to set a proper value.")
	set(CMAKE_BUILD_TYPE RelWithDebInfo
	CACHE STRING "Type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif(NOT CMAKE_BUILD_TYPE)

# Include path for additional CMake library finding scripts
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")

# We want the binaries to be easily accessible
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})

if(WIN32)
	set(Boost_USE_STATIC_LIBS   ON)
endif(WIN32)

# Add the libraries

# Boost
find_package(Boost COMPONENTS system thread filesystem REQUIRED) # Specify the required components
include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})
list(APPEND LIBS ${Boost_LIBRARIES})


# SDL
find_package (SDL REQUIRED)
include_directories(${SDL_INCLUDE_DIR})
list(APPEND LIBS ${SDL_LIBRARY})

# OGRE
find_package(OGRE REQUIRED)
link_directories(${OGRE_LIBRARY_DIRS})
include_directories(${OGRE_INCLUDE_DIRS})
list(APPEND LIBS ${OGRE_LIBRARIES})
# Specify optional components here
#foreach(component Terrain Paging RTShaderSystem) 
#	include_directories(${OGRE_${component}_INCLUDE_DIRS})
#	list(APPEND LIBS  ${OGRE_${component}_LIBRARIES})
#endforeach()

# Bullet (defaults to add all libraries - edit the find script cmake/FindBullet.cmake to specify the libraries you need)
#find_package(Bullet REQUIRED)
#include_directories(${BULLET_INCLUDE_DIRS})
#list(APPEND LIBS ${BULLET_LIBRARIES})

# MyGUI
#find_package(MyGUI REQUIRED)
#include_directories(${MYGUI_INCLUDE_DIRS})
#include_directories(${MYGUI_PLATFORM_INCLUDE_DIRS})
#link_directories(${MYGUI_LIB_DIR})
#IF(MSVC)
#	list(APPEND LIBS ${MYGUI_LIBRARIES} ${MYGUI_PLATFORM_LIBRARIES})
#ELSE(MSVC)
#	list(APPEND LIBS ${MYGUI_LIBRARIES} MyGUI.OgrePlatform) #TODO: MyGUI.OgrePlatform is not added by the find script
#ENDIF(MSVC)

# Simple libraries (without optional components) added in a loop
#foreach(lib  OIS SDL OGG VorbisFile)
foreach(lib  OIS)
	find_package(${lib} REQUIRED)
	include_directories(${${lib}_INCLUDE_DIRS})
	list(APPEND LIBS ${${lib}_LIBRARIES})
endforeach()

# The project's include directories
file(GLOB PROJECT_INCLUDES
	.
)
include_directories(${PROJECT_INCLUDES})

# The project's sources
file(GLOB PROJECT_SOURCES
    VideoPlayer.cpp
    Application.cpp
    BaseApplication.cpp
    InputManager.cpp
    PathManager.cpp
    Window.cpp
    main.cpp
)

# Now compile the executable
add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
target_link_libraries(${PROJECT_NAME} ${LIBS})
target_link_libraries(${PROJECT_NAME} "-lswscale -lavformat -lavutil -lavcodec")
