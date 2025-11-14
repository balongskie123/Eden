# --- Dependency Check ---
# The Abseil synchronization target is required by gRPC-generated code.
# We explicitly find Abseil here to ensure its imported targets are available before linking.
find_package(absl REQUIRED COMPONENTS synchronization)

# Collect the source files
file(GLOB_RECURSE SRC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} src/*.cpp)

# Add the server build target
add_executable(gameserver ${SRC})

# Define the include directories
target_include_directories(gameserver
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:include>
        PRIVATE
            src)

# Link the target
# We must ensure all gRPC core libraries (gpr, grpc++, protobuf) are explicitly linked
# because gRPC generated code (in gameapiprotocol) depends on them.
target_link_libraries(gameserver
        PRIVATE
            common
            pthread
            Boost
            gameapiprotocol
            
            # gRPC Dependencies (Fixes the 'gpr_log' undefined reference)
            grpc++
            grpc
            gpr
            Protobuf
            
            # Abseil Dependency (Needed for gRPC synchronization primitives)
            absl::synchronization
            
            ${GRPC_LIBRARIES} 
            ${PQXX_LIB}
            ${PQ_LIB}
            ${CRYPTOPP_LIBRARY}
            ${GLOG_LIBRARY})
