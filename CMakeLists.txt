cmake_minimum_required(VERSION 3.21)
project(
        herd-proto
        VERSION 0.1
        DESCRIPTION "libherd & herdsman proto files"
        LANGUAGES CXX
)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

## Protobuf & GRPC
find_package(Protobuf REQUIRED)
find_package(gRPC REQUIRED)
include(cmake/GRPC_Util.cmake)

grpc_generate_cpp(
        GRPC_SRC GRPC_HEADER
        src/common.proto


        src/auth.proto
        src/session.proto
        src/storage.proto
        src/execution.proto
)

protobuf_generate_cpp(
        PROTO_SRC PROTO_HEADER
        src/ciphertext_value.proto
        src/circuit.proto
        src/execution_plan.proto
        src/stage.proto
        src/node.proto
)

add_custom_target(
        herd_proto-generate
        DEPENDS
        ${GRPC_SRC} ${GRPC_HEADER}
        ${PROTO_SRC} ${PROTO_HEADER}
)


add_library(herd_proto)
add_dependencies(herd_proto herd_proto-generate)
target_sources(
        herd_proto
        PRIVATE
        ${PROTO_SRC} ${PROTO_HEADER}
        ${GRPC_SRC} ${GRPC_HEADER}
)

target_include_directories(herd_proto PUBLIC ${PROTOBUF_INCLUDE_DIR})
target_include_directories(herd_proto SYSTEM INTERFACE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(
        herd_proto
        PUBLIC
        gRPC::grpc++
        ${PROTOBUF_LIBRARIES}
)