project(SDK_CommandPluginExample)

# cmake_minimum_required(VERSION 3.1)
cmake_minimum_required(VERSION 2.8)
#set(BUILD_SHARED_LIBRARIES OFF)
set(CMAKE_FIND_LIBRARY_SUFFIXES .a)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(BUILD_SHARED_LIBRARIES OFF)
# adjust compiler setting for Linux using gcc version 5.0 and higher
if(CMAKE_SYSTEM_NAME MATCHES Linux AND CMAKE_COMPILER_IS_GNUCXX)
  if(CMAKE_CXX_COMPILER_VERSION VERSION_GREATER "5.0")
    if(NOT CMAKE_CXX_FLAGS MATCHES _GLIBCXX_USE_CXX11_ABI)
      set(CMAKE_CXX_FLAGS "-D_GLIBCXX_USE_CXX11_ABI=0 ${CMAKE_CXX_FLAGS}")
    endif()
  endif()
endif()

# Get the packages needed to use the SDK
find_package(Cubit REQUIRED CONFIG)
include_directories(${CUBIT_INCLUDE_DIRS})

#moab
find_package(MOAB REQUIRED)
# this is needed as moab builds both static and shared libries
# with autommatic indication of which to use from the cmake
find_library(libmoab libMOAB.a ${MOAB_LIBRARY_DIRS})
include_directories(${MOAB_INCLUDE_DIRS})

# dagmc
include_directories("${DAGMC_DIR}/include/")
find_library(makeWatertight libMakeWatertight.a ${DAGMC_DIR}/lib/)

# set static build for libraries
set(HDF5_USE_STATIC_LIBRARIES ON)
find_package(HDF5 REQUIRED)

set(SRC
    MyPlugin.cpp
    MyPlugin.hpp
    DAGMCExportCommand.cpp
    DAGMCExportCommand.hpp)
# make the plugin library
add_library(dagmc_export_plugin SHARED ${SRC})
# set the libraries
target_link_libraries(dagmc_export_plugin cubiti cubit_util cubit_geom z
                      ${HDF5_LIBRARIES} ${makeWatertight} ${libmoab} )

