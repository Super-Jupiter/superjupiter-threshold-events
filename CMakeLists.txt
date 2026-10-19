cmake_minimum_required(VERSION 3.16)
project(SuperJupiterThresholdEvents VERSION 0.1.0 LANGUAGES CXX)

set(_superjupiter_threshold_events_top_level OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(_superjupiter_threshold_events_top_level ON)
endif()
option(SUPERJUPITER_THRESHOLD_EVENTS_BUILD_TESTS "Build ThresholdEvents tests" ${_superjupiter_threshold_events_top_level})
option(SUPERJUPITER_THRESHOLD_EVENTS_BUILD_EXAMPLES "Build ThresholdEvents host example" ${_superjupiter_threshold_events_top_level})
option(SUPERJUPITER_THRESHOLD_EVENTS_INSTALL "Generate ThresholdEvents install rules" ${_superjupiter_threshold_events_top_level})

add_library(SuperJupiterThresholdEvents INTERFACE)
add_library(SuperJupiter::ThresholdEvents ALIAS SuperJupiterThresholdEvents)
set_target_properties(SuperJupiterThresholdEvents PROPERTIES EXPORT_NAME ThresholdEvents)
target_include_directories(SuperJupiterThresholdEvents INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include>)
target_compile_features(SuperJupiterThresholdEvents INTERFACE cxx_std_11)

if(SUPERJUPITER_THRESHOLD_EVENTS_BUILD_TESTS)
  enable_testing()
  add_executable(superjupiter-threshold-events-tests tests/test.cpp)
  target_link_libraries(superjupiter-threshold-events-tests PRIVATE SuperJupiter::ThresholdEvents)
  add_test(NAME superjupiter-threshold-events-tests COMMAND superjupiter-threshold-events-tests)
  add_executable(superjupiter-threshold-events-namespace-test tests/namespace_isolation.cpp)
  target_link_libraries(superjupiter-threshold-events-namespace-test PRIVATE SuperJupiter::ThresholdEvents)
  add_test(NAME superjupiter-threshold-events-namespace-test COMMAND superjupiter-threshold-events-namespace-test)
endif()
if(SUPERJUPITER_THRESHOLD_EVENTS_BUILD_EXAMPLES)
  add_executable(superjupiter-threshold-events-replay tools/replay.cpp)
  target_link_libraries(superjupiter-threshold-events-replay PRIVATE SuperJupiter::ThresholdEvents)
endif()
if(SUPERJUPITER_THRESHOLD_EVENTS_INSTALL)
  include(CMakePackageConfigHelpers)
  set(_superjupiter_threshold_events_package_dir lib/cmake/SuperJupiterThresholdEvents)
  install(TARGETS SuperJupiterThresholdEvents EXPORT SuperJupiterThresholdEventsTargets)
  install(FILES src/SuperJupiterThresholdEvents.h DESTINATION include)
  install(EXPORT SuperJupiterThresholdEventsTargets NAMESPACE SuperJupiter::
    DESTINATION ${_superjupiter_threshold_events_package_dir})
  configure_package_config_file(cmake/SuperJupiterThresholdEventsConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterThresholdEventsConfig.cmake
    INSTALL_DESTINATION ${_superjupiter_threshold_events_package_dir})
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterThresholdEventsConfigVersion.cmake
    VERSION ${PROJECT_VERSION} COMPATIBILITY SameMinorVersion ARCH_INDEPENDENT)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterThresholdEventsConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterThresholdEventsConfigVersion.cmake
    DESTINATION ${_superjupiter_threshold_events_package_dir})
  install(FILES LICENSE DESTINATION share/licenses/SuperJupiterThresholdEvents)
endif()
