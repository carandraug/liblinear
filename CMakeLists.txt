# Require 3.18 because we use BLAS::BLAS target.
cmake_minimum_required(VERSION 3.18)

project(liblinear)

set(_LIBLINEAR_SHVER 4)

find_package(BLAS REQUIRED)

add_executable(train train.c)
add_executable(predict predict.c)

add_library(linear SHARED linear.cpp linear.def)
add_library(newton OBJECT newton.cpp)

target_link_libraries(linear
  newton
  BLAS::BLAS)

# target_include_directories(linear PUBLIC
#  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}>
#  $<INSTALL_INTERFACE:include>)

set_target_properties(linear PROPERTIES
  SOVERSION ${_LIBLINEAR_SHVER})

set_target_properties(newton PROPERTIES
  POSITION_INDEPENDENT_CODE ON)

# target_include_directories(newton PRIVATE
#  ${CMAKE_SOURCE_DIR})

target_link_libraries(train
  linear
  BLAS::BLAS)

target_link_libraries(predict
  linear
  BLAS::BLAS)

install(TARGETS train predict
  DESTINATION bin)

install(TARGETS linear
  DESTINATION lib
  EXPORT linear)

install(FILES linear.h
  DESTINATION include)
