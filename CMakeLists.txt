cmake_minimum_required(VERSION 2.8)
project(ORB_SLAM2 LANGUAGES CXX)

IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native -Wno-deprecated -w -fPIC" )
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3 -march=native -Wno-deprecated -w -fPIC")

# Check C++11 or C++0x support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
   add_definitions(-DCOMPILEDWITHC11)
   message(STATUS "Using flag -std=c++11.")
elseif(COMPILER_SUPPORTS_CXX0X)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
   add_definitions(-DCOMPILEDWITHC0X)
   message(STATUS "Using flag -std=c++0x.")
else()
   message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

# OpenCV
# find_package(OpenCV 3.0 QUIET)
# if(NOT OpenCV_FOUND)
#    find_package(OpenCV 2.4.3 QUIET)
#    if(NOT OpenCV_FOUND)
#       message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
#    endif()
# endif()
message(STATUS "Finding OpenCV...")
if (CMAKE_BUILD_TYPE MATCHES "Debug")
  find_package(OpenCV REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/opencv/install/Debug/lib/cmake/opencv4)
endif (CMAKE_BUILD_TYPE MATCHES "Debug")
if (CMAKE_BUILD_TYPE MATCHES "Release")
  find_package(OpenCV REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/opencv/install/Release/share/OpenCV)
endif (CMAKE_BUILD_TYPE MATCHES "Release")
if (OpenCV_FOUND)
  message(STATUS "Found OpenCV library: " ${OpenCV_INCLUDE_DIRS})
endif (OpenCV_FOUND)

# Eigen
# find_package(Eigen3 3.3.9 REQUIRED)
message(STATUS "Finding Eigen3...")
if (CMAKE_BUILD_TYPE MATCHES "Debug")
  find_package(Eigen3 REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/eigen/install/Debug/share/eigen3/cmake)
endif (CMAKE_BUILD_TYPE MATCHES "Debug")
if (CMAKE_BUILD_TYPE MATCHES "Release")
  find_package(Eigen3 REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/eigen/install/Release/share/eigen3/cmake)
endif (CMAKE_BUILD_TYPE MATCHES "Release")
if(${Eigen3_FOUND})
  message(STATUS "Found: Eigen3 - ${EIGEN3_INCLUDE_DIRS}")
endif(${Eigen3_FOUND})

#Pangolin
# find_package(Pangolin REQUIRED)
message(STATUS "Finding Pangolin...")
if (CMAKE_BUILD_TYPE MATCHES "Debug")
  find_package(Pangolin REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/pangolin/install/Debug/lib/cmake/Pangolin)
endif (CMAKE_BUILD_TYPE MATCHES "Debug")
if (CMAKE_BUILD_TYPE MATCHES "Release")
  find_package(Pangolin REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/pangolin/install/Release/lib/cmake/Pangolin)
endif (CMAKE_BUILD_TYPE MATCHES "Release")
if (${Pangolin_FOUND})
  message(STATUS "Found Pangolin - ${Pangolin_INCLUDE_DIRS}")
endif (${Pangolin_FOUND})

#easy_profiler

message(STATUS "Finding Easy-profiler...")
find_package(easy_profiler REQUIRED HINTS ${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/easy_profiler/install/Release/lib/cmake/easy_profiler)
if(${easy_profiler_FOUND})
  message(STATUS "Found: Easy-profiler - ${easy_profiler_INCLUDE_DIRS}")
endif(${easy_profiler_FOUND})

include_directories(
${PROJECT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}/include
${EIGEN3_INCLUDE_DIR}
${Pangolin_INCLUDE_DIRS}
)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

add_library(${PROJECT_NAME} SHARED
src/System.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FrameDrawer.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapDrawer.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
src/Viewer.cc
)

target_link_libraries(${PROJECT_NAME}
${OpenCV_LIBS}
${EIGEN3_LIBS}
${Pangolin_LIBRARIES}
${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2/lib/libDBoW2.so
${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o.so
easy_profiler
)

# Build examples

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/RGB-D)

add_executable(rgbd_tum
Examples/RGB-D/rgbd_tum.cc)
target_link_libraries(rgbd_tum ${PROJECT_NAME})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Stereo)

add_executable(stereo_kitti
Examples/Stereo/stereo_kitti.cc)
target_link_libraries(stereo_kitti ${PROJECT_NAME})

add_executable(stereo_euroc
Examples/Stereo/stereo_euroc.cc)
target_link_libraries(stereo_euroc ${PROJECT_NAME})


set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Monocular)

add_executable(mono_tum
Examples/Monocular/mono_tum.cc)
target_link_libraries(mono_tum ${PROJECT_NAME})

add_executable(mono_kitti
Examples/Monocular/mono_kitti.cc)
target_link_libraries(mono_kitti ${PROJECT_NAME})

add_executable(mono_euroc
Examples/Monocular/mono_euroc.cc)
target_link_libraries(mono_euroc ${PROJECT_NAME})