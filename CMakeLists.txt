cmake_minimum_required(VERSION 3.12)

project(sharedstatic)


add_library(object OBJECT)
target_include_directories(
  object
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>
  $<INSTALL_INTERFACE:include>
  )
target_sources(object PRIVATE ${CMAKE_CURRENT_LIST_DIR}/src/source1.cpp)
target_sources(object PRIVATE ${CMAKE_CURRENT_LIST_DIR}/src/source2.cpp)
set_property(TARGET object PROPERTY POSITION_INDEPENDENT_CODE 1)

add_library(sharedstatic_shared SHARED $<TARGET_OBJECTS:object>)
add_library(sharedstatic_static STATIC $<TARGET_OBJECTS:object>)

include(CMakePackageConfigHelpers)
configure_package_config_file(
  ${PROJECT_SOURCE_DIR}/${PROJECT_NAME}Config.cmake.in
  ${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  INSTALL_DESTINATION ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}
  )



include(GNUInstallDirs)

install(
  TARGETS sharedstatic_shared
  EXPORT ${PROJECT_NAME}Target
  )

install(
  TARGETS sharedstatic_static
  EXPORT ${PROJECT_NAME}Target
  )

install(
  EXPORT ${PROJECT_NAME}Target
  FILE  ${PROJECT_NAME}Target.cmake
  NAMESPACE ${PROJECT_NAME}::
  DESTINATION ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}/cmake
  )

# Install Configuration Files
install(
  FILES
  ${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  DESTINATION ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}/cmake/
  )

install(
  DIRECTORY ${PROJECT_SOURCE_DIR}/include
  DESTINATION  ${CMAKE_INSTALL_PREFIX}
  )


