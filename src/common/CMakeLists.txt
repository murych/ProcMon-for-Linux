add_library(
  common-static
  ${CMAKE_CURRENT_SOURCE_DIR}/printable.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/cli_utils.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/cancellable_message_queue.h
  ${CMAKE_CURRENT_SOURCE_DIR}/cli_utils.h
  ${CMAKE_CURRENT_SOURCE_DIR}/event.h
  ${CMAKE_CURRENT_SOURCE_DIR}/printable.h
  ${CMAKE_CURRENT_SOURCE_DIR}/stack_trace.h
  ${CMAKE_CURRENT_SOURCE_DIR}/telemetry.h)
add_library(procmon::common ALIAS common-static)

target_compile_features(common-static PUBLIC cxx_std_17)
target_include_directories(
  common-static PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/common/>)
