add_library(logging-static ${CMAKE_CURRENT_SOURCE_DIR}/easylogging++.cc
                           ${CMAKE_CURRENT_SOURCE_DIR}/easylogging++.h)
add_library(procmon::logging ALIAS logging-static)

target_compile_features(logging-static PUBLIC cxx_std_17)
target_include_directories(
  logging-static PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/logging>)
