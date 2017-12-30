
project(HELLO)

add_executable(hello hello.c)

message(STATUS ${PROJECT_BINARY_DIR})
message(STATUS ${HELLO_BINARY_DIR})
message(STATUS ${CMAKE_BINARY_DIR})

message(STATUS ${PROJECT_SOURCE_DIR})
message(STATUS ${CMAKE_SOURCE_DIR})
message(STATUS ${HELLO_SOURCE_DIR})

