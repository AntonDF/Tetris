cmake_minimum_required(VERSION 2.8)
PROJECT(TETRIS)
SET(CMAKE_BUILD_TYPE "Release")

if (!MSVC)
SET(CMAKE_CXX_FLAGS "-Wno-deprecated")
endif()

#Compile and Link GLFW
ADD_SUBDIRECTORY(glfw-3.2)
link_libraries(glfw)
include_directories(${glfw_INCLUDE_DIRS})
include_directories("${CMAKE_SOURCE_DIR}/glfw-3.2/deps")

add_library(glad "${CMAKE_SOURCE_DIR}/glfw-3.2/deps/glad/glad.h"
         		 "${CMAKE_SOURCE_DIR}/glfw-3.2/deps/glad.c")
				 
link_libraries(glad)

SET(MY_SHADER_PATH ${CMAKE_SOURCE_DIR}/shaders/)
CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/shaders/shader.cpp.in ${CMAKE_SOURCE_DIR}/shaders/shader.cpp)	


include_directories(${CMAKE_SOURCE_DIR}/source
					${CMAKE_SOURCE_DIR}/source/common
					${CMAKE_SOURCE_DIR}/shaders)
					
add_executable(tetris WIN32 MACOSX_BUNDLE 
	source/common.h
	source/utils/Angel.h
	source/utils/CheckError.h
	source/utils/mat.h
	source/utils/vec.h
	source/utils/u8names.h
	source/utils/u8names.cpp
	shaders/shader.cpp
	source/main.cpp
	source/board.cpp
	source/board.hpp 
	source/block.cpp
	source/block.hpp
	shaders/fshader_block.glsl
	shaders/vshader_block.glsl
	shaders/fshader_board.glsl
	shaders/vshader_board.glsl)
	
#Windows cleanup
if (MSVC)
    # Tell MSVC to use main instead of WinMain for Windows subsystem executables
    set_target_properties(tetris PROPERTIES LINK_FLAGS "/ENTRY:mainCRTStartup")
endif()

#Apple cleanup
if (APPLE)
    set_target_properties(tetris PROPERTIES MACOSX_BUNDLE_BUNDLE_NAME "simple")
	
    set_target_properties(tetris PROPERTIES
                          MACOSX_BUNDLE_SHORT_VERSION_STRING ${GLFW_VERSION}
                          MACOSX_BUNDLE_LONG_VERSION_STRING ${GLFW_VERSION_FULL}
    					  MACOSX_BUNDLE_ICON_FILE glfw.icns)
endif()
