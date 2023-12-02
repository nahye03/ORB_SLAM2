cmake_minimum_required(VERSION 2.8)
project(DBoW2)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3 -march=native")

set(HDRS_DBOW2
  DBoW2/BowVector.h
  DBoW2/FORB.h 
  DBoW2/FClass.h       
  DBoW2/FeatureVector.h
  DBoW2/ScoringObject.h   
  DBoW2/TemplatedVocabulary.h)
set(SRCS_DBOW2
  DBoW2/BowVector.cpp
  DBoW2/FORB.cpp      
  DBoW2/FeatureVector.cpp
  DBoW2/ScoringObject.cpp)

set(HDRS_DUTILS
  DUtils/Random.h
  DUtils/Timestamp.h)
set(SRCS_DUTILS
  DUtils/Random.cpp
  DUtils/Timestamp.cpp)

# find_package(OpenCV 4.5.3 QUIET)
# if(NOT OpenCV_FOUND)
#    find_package(OpenCV 2.4.3 QUIET)
#    if(NOT OpenCV_FOUND)
#       message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
#    endif()
# endif()

message(STATUS "Finding OpenCV...")
if (CMAKE_BUILD_TYPE MATCHES "Debug")
  find_package(OpenCV REQUIRED HINTS /home/nahye/ORB_SLAM2/Thirdparty/opencv/install/Debug/lib/cmake/opencv4)
endif (CMAKE_BUILD_TYPE MATCHES "Debug")
if (CMAKE_BUILD_TYPE MATCHES "Release")
  find_package(OpenCV REQUIRED HINTS /home/nahye/ORB_SLAM2/Thirdparty/opencv/install/Release/lib/cmake/opencv4)
  
endif (CMAKE_BUILD_TYPE MATCHES "Release")
if (OpenCV_FOUND)
  message(STATUS "Found OpenCV library: " ${OpenCV_INCLUDE_DIRS})
endif (OpenCV_FOUND)

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

include_directories(${OpenCV_INCLUDE_DIRS})
add_library(DBoW2 SHARED ${SRCS_DBOW2} ${SRCS_DUTILS})
target_link_libraries(DBoW2 ${OpenCV_LIBS})

