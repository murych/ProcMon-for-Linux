add_library(
  tracer-static
  ${CMAKE_CURRENT_SOURCE_DIR}/mock_tracer_engine.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/ebpf_tracer_engine.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/raw_ebpf_event.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/syscall_schema.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/ebpf_tracer_engine.h
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/raw_ebpf_event.h
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/syscall_schema.h
  ${CMAKE_CURRENT_SOURCE_DIR}/mock_tracer_engine.h
  ${CMAKE_CURRENT_SOURCE_DIR}/tracer_engine.h
  ${CMAKE_CURRENT_SOURCE_DIR}/ebpf/bpf_prog.h)
add_library(procmon::tracer ALIAS tracer-static)

target_compile_features(tracer-static PUBLIC cxx_std_17)
target_compile_options(tracer-static PUBLIC -pthread)
target_link_libraries(
  tracer-static
  PUBLIC procmon::common procmon::storage
  PRIVATE procmon::logging ${Bpf_LIBRARIES} ${Bcc_LIBRARIES})
target_include_directories(
  tracer-static
  PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src/tracer/>
  PRIVATE ${Bpf_INCLUDE_DIRS} ${Bcc_INCLUDE_DIRS})
