
set(SRC_FILES main.c hello.c)

add_executable(hello ${SRC_FILES})

message(STATUS PROJECT_BINARY_DIR ${PROJECT_BINARY_DIR})
message(STATUS CMAKE_BINARY_DIR ${CMAKE_BINARY_DIR})
message(STATUS HELLO_BINARY_DIR ${HELLO_BINARY_DIR})

message(STATUS CMAKE_CURRENT_BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR})
message(STATUS CMAKE_CURRENT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

