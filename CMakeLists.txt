cmake_minimum_required(VERSION 3.21)
project(
        herd-proto
        VERSION 0.1
        DESCRIPTION "libherd & herdsman proto files"
        LANGUAGES CXX
)

include(FindProtobuf)
find_package(Protobuf REQUIRED)

protobuf_generate_cpp(
        PROTO_SRC PROTO_HEADER
        src/node.proto
        src/ciphertext_value.proto
        src/circuit_graph.proto
        src/task.proto
)

add_custom_target(herd_proto-generate)


add_library(herd_proto)
target_sources(herd_proto PRIVATE ${PROTO_SRC} ${PROTO_HEADER})

target_include_directories(herd_proto PUBLIC ${PROTOBUF_INCLUDE_DIR})
target_include_directories(herd_proto SYSTEM INTERFACE ${CMAKE_CURRENT_BINARY_DIR})

add_dependencies(herd_proto herd_proto-generate)
