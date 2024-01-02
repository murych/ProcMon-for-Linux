add_library(
  display-static
  ${CMAKE_CURRENT_SOURCE_DIR}/screen.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/column.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/event_formatter.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/kill_event_formatter.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/headless.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/screen_configuration.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/column.h
  ${CMAKE_CURRENT_SOURCE_DIR}/event_formatter.h
  ${CMAKE_CURRENT_SOURCE_DIR}/kill_event_formatter.h
  ${CMAKE_CURRENT_SOURCE_DIR}/headless.h
  ${CMAKE_CURRENT_SOURCE_DIR}/screen.h
  ${CMAKE_CURRENT_SOURCE_DIR}/screen_configuration.h)
add_library(procmon::display ALIAS display-static)

target_compile_features(display-static PUBLIC cxx_std_17)
target_link_libraries(
  display-static
  PUBLIC procmon::common
  PRIVATE procmon::configuration procmon::logging ${CURSES_LIBRARIES} panel)
target_include_directories(
  display-static
  PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/display>
  PUBLIC $<BUILD_INTERFACE:${PROJECT_BINARY_DIR}/version>)
