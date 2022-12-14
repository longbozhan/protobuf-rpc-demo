PROJECT(protobuf-rpc-demp)
cmake_minimum_required(VERSION 3.2)
add_compile_options(-std=c++11)

#设置boost和protobuf的lib目录
set(THIRDLIB_PATH /home/longbo/thirdparty)

include_directories(${THIRDLIB_PATH}/include
    ./src
    ../../../../bc_out/third-64/protobuf/output/include
    ../../../../bc_out/third-64/boost/output/include)
link_directories(${THIRDLIB_PATH}/lib
    ../../../../bc_out/third-64/protobuf/output/lib
    ../../../../bc_out/third-64/boost/output/lib
    ./build)

file(GLOB MYSERVER_PROTO ${CMAKE_CURRENT_SOURCE_DIR}/src/*.proto)
execute_process(COMMAND ${THIRDLIB_PATH}/bin/protoc
        --cpp_out=${CMAKE_CURRENT_SOURCE_DIR}/src
        --proto_path=${CMAKE_CURRENT_SOURCE_DIR}/src
        ${MYSERVER_PROTO}
        COMMAND_ECHO STDOUT)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}-pthread")
set(LIBMYRPC_SRC ./src/rpc_meta.pb.cc)
add_library(myrpc ${LIBMYRPC_SRC})
add_executable(server ./src/echo.pb.cc ./src/test_myrpc.cpp)
target_link_libraries(server libprotobuf.a libboost_signals.a libboost_system.a myrpc)
add_executable(client ./src/echo.pb.cc ./src/test_myrpc_client.cpp)
target_link_libraries(client libprotobuf.a libboost_signals.a libboost_system.a myrpc)
