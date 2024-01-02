add_library(
  configuration-static ${CMAKE_CURRENT_SOURCE_DIR}/procmon_configuration.cpp
                       ${CMAKE_CURRENT_SOURCE_DIR}/procmon_configuration.h)
add_library(procmon::configuration ALIAS configuration-static)

target_compile_features(configuration-static PUBLIC cxx_std_17)
target_link_libraries(
  configuration-static
  PUBLIC procmon::storage procmon::tracer
  PRIVATE procmon::logging)
target_include_directories(
  configuration-static
  PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/configuration/>)
