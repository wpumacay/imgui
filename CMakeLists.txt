cmake_minimum_required( VERSION 3.0.0 )

message( "IMGUI> Configuring IMGUI for glfw and glew usage into a library" )

## Configuring imgui to generate an external library ##########################

project( imgui )

# Configureind dependencies
find_package( OpenGL REQUIRED )
find_package( GLEW REQUIRED )
find_package( PkgConfig REQUIRED )
pkg_search_module( GLFW REQUIRED glfw3 )

include_directories( ${GLFW_INCLUDE_DIRS} )
include_directories( ${GLEW_INCLUDE_DIRS} )
# tell imgui to use of glew as loader
add_definitions( -DIMGUI_IMPL_OPENGL_LOADER_GLEW=1 )

# Add main sources of the abstract imgui implementation
set( IMGUI_SOURCES
     "${CMAKE_CURRENT_SOURCE_DIR}/imgui.cpp"
     "${CMAKE_CURRENT_SOURCE_DIR}/imgui_draw.cpp"
     "${CMAKE_CURRENT_SOURCE_DIR}/imgui_widgets.cpp"
     "${CMAKE_CURRENT_SOURCE_DIR}/imgui_demo.cpp" )
set( IMGUI_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}" )

# Add implementation specific sources for glfw
set( IMGUI_SOURCES
     "${IMGUI_SOURCES}"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples/imgui_impl_glfw.cpp"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples/imgui_impl_opengl3.cpp" )
set( IMGUI_INCLUDE_DIRS 
     "${IMGUI_INCLUDE_DIRS}"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples" )

# Configure the library
include_directories( ${IMGUI_INCLUDE_DIRS} )
add_library( imgui ${IMGUI_SOURCES} )
target_link_libraries( imgui ${GLEW_LIBRARIES} ${GLFW_LIBRARIES} ${OPENGL_LIBRARIES} )

## ############################################################################

## Configure some tests #######################################################

add_executable( test1 "tests/test1.cpp" )
target_link_libraries( test1 imgui )


###############################################################################