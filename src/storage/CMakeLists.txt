add_library(
  storage-static
  ${CMAKE_CURRENT_SOURCE_DIR}/mock_storage_engine.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/sqlite3_storage_engine.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/sqlite3_storage_engine.h
  ${CMAKE_CURRENT_SOURCE_DIR}/storage_proxy.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/mock_storage_engine.h
  ${CMAKE_CURRENT_SOURCE_DIR}/storage_proxy.h
  ${CMAKE_CURRENT_SOURCE_DIR}/storage_engine.h)
add_library(procmon::storage ALIAS storage-static)

target_compile_features(storage-static PUBLIC cxx_std_17)
target_link_libraries(
  storage-static
  PRIVATE SQLite::SQLite3
  PUBLIC procmon::display)
target_include_directories(
  storage-static PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/storage>)

# Create test target for sqlite3 storage engine.
if(${BUILD_TESTING})
  add_executable(test-sqlite3-storage
                 ${CMAKE_CURRENT_SOURCE_DIR}/test_sqlite3_storage_engine.cpp)
  target_link_libraries(
    test-sqlite3-storage PRIVATE procmon::storage procmon::logging
                                 Catch2::Catch2 SQLite::SQLite3)

  add_test(NAME test-procmon-sqlite3-storage COMMAND test-sqlite3-storage)
endif()
