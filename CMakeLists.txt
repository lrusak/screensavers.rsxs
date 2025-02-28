cmake_minimum_required(VERSION 3.5)
project(screensavers.rsxs)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR})

cmake_policy(SET CMP0054 NEW)

find_package(Kodi REQUIRED)

if(NOT WIN32 AND (APP_RENDER_SYSTEM STREQUAL "gl" OR NOT APP_RENDER_SYSTEM))
  find_package(OpenGl REQUIRED)
  set(DEPLIBS ${OPENGL_LIBRARIES})
  set(includes ${OPENGL_INCLUDE_DIR})
  add_definitions(${OPENGL_DEFINITIONS})
else()
  find_package(OpenGLES REQUIRED)
  set(DEPLIBS ${OPENGLES_LIBRARIES})
  set(includes ${OPENGLES_INCLUDE_DIR})
  add_definitions(${OPENGLES_DEFINITIONS})
endif()

include_directories(${KODI_INCLUDE_DIR}/.. # Hack way with "/..", need bigger Kodi cmake rework to match right include ways
                    ${PROJECT_SOURCE_DIR}/lib
                    ${PROJECT_SOURCE_DIR}/lib/glm
                    ${PROJECT_SOURCE_DIR}/lib/gli)

add_subdirectory(lib/kodi/gui/gl)
add_subdirectory(lib/Implicit)
add_subdirectory(lib/Rgbhsl)
add_subdirectory(lib/rsMath)

list(APPEND DEPENDS rsMath kodiOpenGL)
list(APPEND DEPLIBS rsMath kodiOpenGL Implicit Rgbhsl)

if(NOT ${CORE_SYSTEM_NAME} STREQUAL "")
  if(CORE_SYSTEM_NAME STREQUAL osx OR
     CORE_SYSTEM_NAME STREQUAL ios OR
     CORE_SYSTEM_NAME STREQUAL darwin_embedded)
    list(APPEND DEPLIBS "-framework CoreVideo")
  elseif(CORE_SYSTEM_NAME STREQUAL windows OR CORE_SYSTEM_NAME STREQUAL windowsstore)
    add_definitions(-D_USE_MATH_DEFINES
                    -DNOMINMAX)
  endif()
endif()

get_directory_property(BASE_DEFINITIONS COMPILE_DEFINITIONS)
message(STATUS "Compile definitions: ${BASE_DEFINITIONS}")
if(CMAKE_BUILD_TYPE)
  message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
endif()

set(DUMMY_SOURCES ${PROJECT_SOURCE_DIR}/src/dummy.cpp)

#include(src/biof/CMakeLists.txt) Disabled, does not work right
include(src/busyspheres/CMakeLists.txt)
include(src/colorfire/CMakeLists.txt)
include(src/cyclone/CMakeLists.txt)
include(src/drempels/CMakeLists.txt)
include(src/feedback/CMakeLists.txt)
include(src/fieldlines/CMakeLists.txt)
include(src/flocks/CMakeLists.txt)
include(src/flux/CMakeLists.txt)
include(src/helios/CMakeLists.txt)
include(src/hufosmoke/CMakeLists.txt)
include(src/hufotunnel/CMakeLists.txt)
include(src/lattice/CMakeLists.txt)
include(src/lorenz/CMakeLists.txt)
include(src/skyrocket/CMakeLists.txt)
include(src/solarwinds/CMakeLists.txt)
include(src/spirographx/CMakeLists.txt)
include(src/sundancer2/CMakeLists.txt)

if(NOT OPENGLES_VERSION STREQUAL 2)
  include(src/euphoria/CMakeLists.txt)
  include(src/hyperspace/CMakeLists.txt)
  include(src/matrixview/CMakeLists.txt)
  include(src/microcosm/CMakeLists.txt)
  include(src/plasma/CMakeLists.txt)
else()
  message(STATUS "Following addons not supported on OpenGL ES 2.0: Euphoria, Hyperspace, Matrixview, Microcosm and Plasma")
endif()

include(CPack)
