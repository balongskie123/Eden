# --- Dependency Check ---
# The Abseil synchronization target is required by gRPC-generated code (GameApi.grpc.pb.cc.o),
# but the target 'absl::synchronization' was not found automatically by CMake.
# We explicitly find Abseil here to ensure its imported targets are available before linking.
find_package(absl REQUIRED COMPONENTS synchronization)

# Collect the source files
file(GLOB_RECURSE SRC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} src/*.cpp)

# Add the server build target
add_executable(loginserver ${SRC})

# Define the include directories
target_include_directories(loginserver
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:include>
        PRIVATE
            src)

# Link the target
target_link_libraries(loginserver
        PRIVATE
            common
            pthread
            Boost
            gameapiprotocol
            grpc++
            grpc
            gpr
            Protobuf
            Grpc
            absl::synchronization # This target is now correctly defined
            ${GRPC_LIBRARIES} 
            ${PQXX_LIB}
            ${PQ_LIB}
            ${CRYPTOPP_LIBRARY}
            ${GLOG_LIBRARY})
