cmake_minimum_required( VERSION 3.0.0 )

message( "ImGui >>> Configuring ImGui for GLFW and GLAD usage into a library" )

project( ImGui )

# Configureind dependencies
find_package( OpenGL REQUIRED )
find_package( PkgConfig REQUIRED )
pkg_search_module( GLFW REQUIRED glfw3 )

# Apple platform kind of needs to specify this (at least in the system I had available, 
# which was a brand new macbookair from a friend). In some systems it's also required (they
# don't expose these directories by default)
if ( APPLE )
    include_directories( /usr/local/include )
    link_directories( /usr/local/lib )
endif()

option( IMGUI_BUILD_EXAMPLES "Build examples of ImGui usage" ON )
option( IMGUI_BUILD_WITH_GLAD_LOADER "Build ImGui using GLAD as gl-loader" OFF )
option( IMGUI_BUILD_WITH_GLEW_LOADER "Build ImGui using GLEW as gl-loader" ON )

include_directories( ${GLFW_INCLUDE_DIRS} )
include_directories( "${CMAKE_CURRENT_SOURCE_DIR}/examples/libs" )
if ( IMGUI_BUILD_WITH_GLAD_LOADER )
    message( "IMGUI >>> Using GLAD as gl-loader for ImGui" )
    add_definitions( -DIMGUI_IMPL_OPENGL_LOADER_GLAD=1 )
elseif( IMGUI_BUILD_WITH_GLEW_LOADER )
    message( "IMGUI >>> Using GLEW as gl-loader for ImGui" )
    add_definitions( -DIMGUI_IMPL_OPENGL_LOADER_GLEW=1 )
else()
    message( "IMGUI >>> Must choose a valid gl-loader (GLAD|GLEW)" )
    return()
endif()

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
     "${CMAKE_CURRENT_SOURCE_DIR}/examples/libs/glad/glad.c"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples/imgui_impl_glfw.cpp"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples/imgui_impl_opengl3.cpp" )
set( IMGUI_INCLUDE_DIRS
     "${IMGUI_INCLUDE_DIRS}"
     "${CMAKE_CURRENT_SOURCE_DIR}/examples" )

set( IMGUI_INCLUDE_DIRS "${IMGUI_INCLUDE_DIRS}" CACHE INTERNAL "" )

# Configure the library
include_directories( ${IMGUI_INCLUDE_DIRS} )
add_library( imgui SHARED ${IMGUI_SOURCES} )
target_link_libraries( imgui ${GLFW_LIBRARIES} ${OPENGL_LIBRARIES} )


if ( IMGUI_BUILD_EXAMPLES )
    add_executable( test1 "tests/test1.cpp" )
    target_link_libraries( test1 imgui )
endif()