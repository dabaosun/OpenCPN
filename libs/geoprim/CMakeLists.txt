cmake_minimum_required(VERSION 3.5.0)

if (TARGET ocpn::geoprim)
    return ()
endif ()

if (NOT CMAKE_MODULE_PATH)
  set (CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../../cmake)
endif ()

SET(SRC
    src/bbox.cpp
    src/bbox.h
    src/LLRegion.cpp
    src/LLRegion.h
    src/line_clip.cpp
    src/line_clip.h
    src/poly_math.cpp
    src/poly_math.h
    src/LOD_reduce.cpp
    src/LOD_reduce.h
    src/linmath.h
    )

if (NOT wxWidgets_INCLUDE_DIRS)
    include(SimpleWxConfig)
    SimpleWxConfig()
endif ()

add_library(GEOPRIM STATIC ${SRC})
add_library(ocpn::geoprim ALIAS GEOPRIM)

set_property(TARGET GEOPRIM PROPERTY COMPILE_FLAGS "${OBJ_VISIBILITY}")
target_include_directories(GEOPRIM PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src)

#FIXME (#3527) Missing linkage
target_include_directories(GEOPRIM PRIVATE ${wxWidgets_INCLUDE_DIRS})

#FIXME (#3527) Dependency on main CMakeLists.txt
target_include_directories(GEOPRIM PRIVATE ../../include)
if (QT_ANDROID)
  target_include_directories(GEOPRIM PRIVATE  ${CMAKE_SOURCE_DIR}/android)
elseif (MSVC)
  target_include_directories(
    GEOPRIM BEFORE PRIVATE ${CMAKE_SOURCE_DIR}/buildwin/include
  )
endif ()

find_package(OpenGL)
if (OPENGL_FOUND)
    target_include_directories(GEOPRIM PRIVATE ${OPENGL_INCLUDE_DIR})

    message(STATUS "Found OpenGL....")
    message(STATUS "    GL Lib: " ${OPENGL_LIBRARIES})
    message(STATUS "    GL Include: " ${OPENGL_INCLUDE_DIR})

    target_link_libraries(GEOPRIM PRIVATE ${OPENGL_LIBRARIES} )
endif (OPENGL_FOUND)

