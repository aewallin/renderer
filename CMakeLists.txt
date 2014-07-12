project(renderer)

cmake_minimum_required(VERSION 2.4)

#
# Turn compiler warnings up to 11, at least with gcc.  I dont know how to
# do this with other compilers we might support and I'm leaving it up to
# the relevant platform maintainers...
# #include'ing the boost graph-library creates deprecated warnings
# thus we use now use -Wno-deprecated here.
#

#if (CMAKE_BUILD_TOOL MATCHES "make")
#    if(GCC_4_6)
#        MESSAGE(STATUS "setting gcc options: -Wall -Werror -Wno-deprecated -pedantic-errors")
        add_definitions(-Wall -Werror -pedantic-errors)
#    else(GCC_4_6)
#        MESSAGE(STATUS "setting gcc options: -Wall  -Wno-deprecated -pedantic-errors")
#        add_definitions(-Wall  -Wno-deprecated -pedantic-errors)
#    endif(GCC_4_6)
#endif (CMAKE_BUILD_TOOL MATCHES "make")

option(BUILD_TYPE
  "Build type: Release=ON/Debug=OFF  " ON)

if (BUILD_TYPE)
    MESSAGE(STATUS " CMAKE_BUILD_TYPE = Release")
    set(CMAKE_BUILD_TYPE Release)
endif(BUILD_TYPE)

if (NOT BUILD_TYPE)
    MESSAGE(STATUS " CMAKE_BUILD_TYPE = Debug")
    set(CMAKE_BUILD_TYPE Debug)
endif(NOT BUILD_TYPE)


include_directories(${CMAKE_CURRENT_BINARY_DIR})

# find BOOST and boost-python
find_package( Boost COMPONENTS python REQUIRED)
if(Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIRS})
    MESSAGE(STATUS "found Boost: " ${Boost_LIB_VERSION})
    MESSAGE(STATUS "boost-incude dirs are: " ${Boost_INCLUDE_DIRS})
    MESSAGE(STATUS "boost-python lib is: " ${Boost_PYTHON_LIBRARY})
    MESSAGE(STATUS "boost_LIBRARY_DIRS is: " ${Boost_LIBRARY_DIRS})
    MESSAGE(STATUS "Boost_LIBRARIES is: " ${Boost_LIBRARIES})    
endif()

# find OpenMP
find_package( OpenMP REQUIRED )

IF (OPENMP_FOUND)
    MESSAGE(STATUS "found OpenMP, compiling with flags: " ${OpenMP_CXX_FLAGS} )
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
ENDIF(OPENMP_FOUND)

find_package(SDL REQUIRED)
MESSAGE(STATUS "SDL lib: " ${SDL_LIBRARY} )
MESSAGE(STATUS "SDL include: " ${SDL_INCLUDE_DIR} )

# this defines the source-files
set(renderer_SRC
    ${renderer_SOURCE_DIR}/src/Base3d.cc
    ${renderer_SOURCE_DIR}/src/BVH.cc
    ${renderer_SOURCE_DIR}/src/Camera.cc
    ${renderer_SOURCE_DIR}/src/Keyboard.cc
    ${renderer_SOURCE_DIR}/src/Light.cc
    ${renderer_SOURCE_DIR}/src/Loader.cc
    ${renderer_SOURCE_DIR}/src/MLAA.cc
    ${renderer_SOURCE_DIR}/src/Rasterizers.cc
    ${renderer_SOURCE_DIR}/src/renderer.cc
    ${renderer_SOURCE_DIR}/src/Screen.cc
    # ${renderer_SOURCE_DIR}/src/showShadowMap.cc
    ${renderer_SOURCE_DIR}/src/Wu.cc
    ${renderer_SOURCE_DIR}/src/Raytracer.cc
)

# include dirs
include_directories( ${renderer_SOURCE_DIR}/src  ${SDL_INCLUDE_DIR})
# ${renderer_SOURCE_DIR}/lib3ds-1.3.0


ADD_EXECUTABLE(renderer ${renderer_SRC})
target_link_libraries(renderer ${SDL_LIBRARY})
