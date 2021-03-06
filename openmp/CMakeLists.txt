#Copyright 2016 Ramakrishnan Kannan

cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

set(ARMA_USE_WRAPPER true)

# the settings below will be automatically configured by the rest of this script

project(NMF CXX)
set(NMFLIB_COMMON ${NMF_SOURCE_DIR}/../common)
set(NNLS_SRC ${NMF_SOURCE_DIR}/../nnls/)
message(STATUS "NMF_SOURCE_DIR =  ${NMF_SOURCE_DIR}")
message(STATUS "NMFLIB_COMMON =  ${NMFLIB_COMMON}")

include(${NMFLIB_COMMON}/cmake_aux/NMFLIB_FindARMA.cmake)

#BOOST package needed for activeset NNLS
set(BOOST false)
#As advised by Wlliam Renaud note dated 4/22. There is an issue on Rhea
#in which the system boost is found before the version in modules.
#Ignore system boost and use module system boost
set(Boost_NO_BOOST_CMAKE TRUE)
find_package(Boost REQUIRED)

message(STATUS "   Boost_FOUND = ${Boost_FOUND}"   )

if(MKL_FOUND)
  add_definitions(-DMKL_FOUND=1)
  set(MKL_INCLUDE_DIR $ENV{MKLROOT}/include)
endif()

#to build sparse comment or uncomment this line.
OPTION(CMAKE_BUILD_SPARSE "Build Sparse" OFF)
if(CMAKE_BUILD_SPARSE)
  add_definitions(-DBUILD_SPARSE=1)
endif()

set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g3 -O0")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fopenmp")

message(STATUS "CMAKE_CXX_FLAGS           = ${CMAKE_CXX_FLAGS}"          )
message(STATUS "CMAKE_SHARED_LINKER_FLAGS = ${CMAKE_SHARED_LINKER_FLAGS}")
message(STATUS "CMAKE_REQUIRED_INCLUDES   = ${CMAKE_REQUIRED_INCLUDES}"  )

message(STATUS "*** libraries:")
message(STATUS "*** NMFLIB_LIBS = ${NMFLIB_LIBS}")

include_directories(
  ${ARMADILLO_INCLUDE_DIR}
  ${ARMADILLO_INCLUDE_DIRS}
  ${NMF_SOURCE_DIR}
  ${NMFLIB_COMMON}
  ${NNLS_SRC}
  ${Boost_INCLUDE_DIR}
  ${MKL_INCLUDE_DIR}
)

add_executable(nmf
  nmf.cpp
)

target_link_libraries(nmf ${NMFLIB_LIBS})